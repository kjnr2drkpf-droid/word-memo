# 05 — Architektura

Stav k 2026-11-07. Autor: architekt. Zdroj pravdy: `00-zadani.md` (vč. D15 iOS 26, D16 en-GB, D17 předpoklad). Vstupy: `01`, `02`, `04`, `08`, `09`. Tvrzení o platformě přebírám z `08`/`09` se stejným značením (**ověřeno** / **domněnka**); nová tvrzení, která jsem neověřil, jsou **domněnka → sonda** (seznam v kap. 11).

> **Sladěno s `07-opakovani-algoritmus.md`** (FSRS-6, karta = slovo × směr, sloveso = jedna karta, fáze se počítá a neukládá, log musí mít `mode`, `outcome`, `hintLevel`, `rating`, `responseMs`, `typedText`, fazety a výsledky po tvarech). Zdrojem pravdy je historie pokusů. Stav FSRS je z ní kdykoli přepočitatelný (ADR-03). Kdyby se FSRS nahradilo Leitnerem (`07` kap. 1), mění se jen doménová funkce a lokální cache, ne synchronizované schéma.

## Závěr

1. **Čtyři moduly:** `Domain` (čisté Swift, bez Apple frameworků), `Persistence` (SwiftData + CloudKit), `Vision`-adaptér (OCR), `Speech`-adaptér (AVSpeech) a nad nimi `App` (SwiftUI). Každou hranici ospravedlňuje konkrétní požadavek (tabulka v kap. 1).
2. **Zdroj pravdy je append-only log pokusů (`Attempt`).** Stav FSRS (`ReviewState`: stabilita, obtížnost, termín, opakování, pády, úspěchy v řadě), fáze karty, statistiky, trend a slabá místa jsou **odvozené** a přepočitatelné. `ReviewState` je **lokální cache, která se nesynchronizuje**. Tím zmizí nejhorší třída sync konfliktů a algoritmus z `07` jde měnit bez zásahu do CloudKit schématu, které je po nasazení jen aditivní (**ověřeno**, `08` b).
3. **Model je CloudKit-kompatibilní (R4):** žádné `unique`, všechno s defaultem nebo volitelné, žádné SwiftData relace, jen UUID reference. V každé entitě je `schemaVersion`, enumy se ukládají jako String, mazání je měkké (tombstone), duplicity se slučují aliasem (`mergedIntoID`), ne přepisem historie.
4. **Import:** fotka zůstává jen dočasně v zařízení. OCR běží v zařízení (`RecognizeDocumentsRequest`, iOS 26, D15). Párování řádků je čistá doménová logika testovatelná na nahraných výstupech OCR. Na konci je vždy kontrolní obrazovka, uloží se jedna dávka (`ImportBatch`) a celou dávku jde vrátit.
5. **Soukromí:** žádná síť kromě systémové synchronizace CloudKit do **soukromé** databáze synova iCloudu, žádná SDK třetích stran, žádná analytika. Smazat jde slovo, sadu (koš 30 dní) i všechna data najednou.
6. **Testy bez zařízení:** doména (vyhodnocení, plánovač, přestavba z logu, slučování konfliktů, párování OCR, CSV) jde přes `swift test` i mimo simulátor. Kvalitu OCR, hlasy, CloudKit mezi dvěma zařízeními a TestFlight s dětským účtem jde ověřit **jen na zařízení** (T1–T8).
7. **Brány před vývojem:** R1 (T1, T2) a R2 (T6) jsou **sondy, ne fakta**. Architektura je navržená tak, aby výsledek R2 změnil jen OCR adaptér a výsledek R1 nezměnil nic v kódu (jen distribuci). Pokud R1 dopadne úplně špatně, mění se D5. To rozhoduje zadavatel.

---

## 0. Architektonicky významné požadavky

| # | Požadavek | Zdroj | Co mění ve struktuře |
|---|---|---|---|
| A1 | Offline je plnohodnotný provoz, sync je jen doplněk | US-13, `08` a | Lokální úložiště je primární. Žádná funkce kromě syncu nesmí čekat na síť. |
| A2 | Sync iPhone ↔ iPad přes iCloud, bez ztrát a bez zdvojení | D6, US-11, US-12 | Tvar dat (append-only log, tombstony, aliasy), lokální odvozený stav |
| A3 | Omezení SwiftData+CloudKit, schéma po nasazení jen aditivní | R4, `08` b | Pole s defaulty, bez unique a bez relací, `schemaVersion`, enumy jako String |
| A4 | Uložení po každé odpovědi, přežití pádu | US-10 | Zápis pokusu je samostatná transakce, odvozený stav jde opravit při startu |
| A5 | Plán opakování jde přepočítat z historie | US-08, US-12 | Algoritmus je čistá funkce nad logem (`07`) |
| A6 | OCR v zařízení, nejistá kvalita (R2), fotka se neukládá | D7, R2, R5, `09` | Adaptér OCR s neutrálním DTO, párování v doméně, dočasný soubor fotky |
| A7 | Kontrola před uložením, vrácení importu | US-03, `09` 2.1 | Koncept `ImportDraft` → `ImportBatch` |
| A8 | Výslovnost en-GB / de-DE, kvalita hlasu závisí na zařízení | D9, D16, R3 | Adaptér řeči s dotazem na dostupnost |
| A9 | Dítě: žádné odesílání ven, smazání dat | R5, US-20 | Žádné síťové moduly, cesta „smazat vše" přes obě úložiště |
| A10 | Rodičovský pohled se nesmí vyloučit | D11, US-23 | Data jako log pokusů a hranice `Persistence`, za kterou jde později vyměnit mechanismus sdílení |
| A11 | Distribuce jen TestFlight, build vyprší za 90 dní | D5, `08` a | Provozní proces (kap. 10), schéma v Production před prvním buildem |
| A12 | Minimum iOS 26 | D15 | `RecognizeDocumentsRequest` je k dispozici, odpadá starší větev OCR |

---

## 1. Moduly a hranice

```
┌──────────────────────── App (SwiftUI, iOS 26) ─────────────────────────┐
│  Features: Onboarding · Sady · Import(kontrola) · Test · Souhrn ·      │
│            Historie · Slovesa · Nastavení(sync, smazání)               │
│  Composition root: skládá Domain + adaptéry, v testech fake adaptéry   │
└───────┬──────────────────┬───────────────────┬───────────────────┬─────┘
        │                  │                   │                   │
┌───────▼───────┐  ┌───────▼────────┐  ┌───────▼───────┐  ┌────────▼───────┐
│    Domain     │  │  Persistence   │  │  OCRVision    │  │ SpeechAV       │
│ (čisté Swift, │◄─┤ SwiftData,     │  │ Vision/VisionKit│ │ AVSpeech-      │
│  Foundation)  │  │ CloudKit status│  │ → RecognizedPage│ │ Synthesizer    │
└───────────────┘  └────────────────┘  └───────────────┘  └────────────────┘
   ▲ adaptéry závisí na Domain (implementují jeho protokoly), Domain na nich ne
```

| Modul | Obsah | Smí znát | Nesmí znát | Proč hranice existuje (požadavek) |
|---|---|---|---|---|
| **Domain** (Swift Package) | Hodnotové typy (`WordValue`, `AttemptValue`, `ReviewKey`…), `AnswerEvaluator` (US-07), `Scheduler` a `ReviewRebuilder` (US-08, `07`), `SessionBuilder` (US-05/06), `StatsCalculator` (US-09/14/15), `DedupKey` a `MergePlanner` (US-12), `PairExtractor` z `RecognizedPage` (US-02), `CSVParser` (US-19), `VerbLineParser` (`09` 5.2) | Foundation | SwiftData, Vision, AVFoundation, CloudKit, SwiftUI | A5: algoritmus a vyhodnocení musí jít testovat deterministicky a bez zařízení. A2: pravidla slučování musí být testovatelná simulací dvou logů. A6: párování OCR musí jít testovat na nahraných datech nezávisle na verzi Vision. |
| **Persistence** | `@Model` třídy (kap. 2), mapování na doménové hodnoty, repozitáře, dva `ModelConfiguration` (synced/local), stav iCloud účtu, „smazat vše" | SwiftData, CloudKit (jen stav účtu a mazání) | Vision, AVSpeech, UI | A3: schéma je nevratné, doména se proto nesmí měnit kvůli tvaru úložiště a naopak. A10: rodičovský pohled bude nejspíš chtít sdílení (CKShare), které SwiftData neumí (**domněnka**, `08` b). Výměna mechanismu se pak dotkne jen tohoto modulu. |
| **OCRVision** | Volání `RecognizeDocumentsRequest` (iOS 26+), případně `RecognizeTextRequest`, kamera `VNDocumentCameraViewController`, výběr `PHPicker`, převod na `RecognizedPage` | Vision, VisionKit, PhotosUI | Persistence, doménová pravidla párování | A6/R2: OCR strategie se po T6 může změnit (jazyky, korekce, tabulky vs. řádky). Změna se nesmí dotknout párování ani kontrolní obrazovky. |
| **SpeechAV** | Výběr hlasu podle locale a kvality, přehrání, dotaz na dostupnost | AVFoundation | Doména slovíček (dostane jen text + locale) | A8/R3: kvalita a dostupnost hlasů se liší podle zařízení (T5). Testy UI potřebují fake. |
| **App** | Obrazovky, navigace, view-modely, composition root | vše výše | — | — |

**Záměrně nenavrhuji:** samostatnou „use-case" vrstvu (view-model volá doménové funkce a repozitář přímo), vlastní sync engine (CloudKit sync dělá SwiftData), síťový modul (žádná síť), modul pro rozpoznání řeči (P2, D9; až přijde, bude to další adaptér vedle SpeechAV).

**Ilustrace rozhraní (jen tvar, ne kód produktu):**

```swift
// Domain – port pro OCR; adaptér OCRVision ho implementuje
protocol TextRecognizer { func recognize(imageURL: URL, hint: LanguageHint) async throws -> RecognizedPage }
struct RecognizedPage { var tables: [[[Cell]]]; var lines: [Line] }   // Cell/Line: text, confidence, bbox

// Domain – čistá funkce; vstupem je celý log pokusů klíče, výstupem odvozený stav
func rebuild(_ attempts: [AttemptValue], config: SchedulerConfig) -> ReviewStateValue
```

---

## 2. Datový model (SwiftData, CloudKit-kompatibilní)

### 2.1 Pravidla pro všechny entity (R4)
- Žádné `@Attribute(.unique)`, žádné `deny`, žádné uspořádané relace (**ověřeno**, `08` b).
- Každý atribut má **default** nebo je volitelný. Enumy jako `String` s defaultem. Neznámá hodnota z novější verze aplikace se čte jako `.unknown` a nepřepisuje se.
- `id: UUID` (default `UUID()`), `schemaVersion: Int = 1`, `createdAt`, `updatedAt` v každé synchronizované entitě.
- **Žádné SwiftData relace**, reference jsou atributy `UUID` (ADR-04).
- Pole `[String]` se ukládají jako Codable (**domněnka → sonda N2**: přenos a granularita konfliktu, viz T4).
- Měkké mazání `deletedAt: Date?` u obsahu (ADR-05). Pokusy se nemažou jednotlivě, jen s celým slovem při vysypání koše nebo při „smazat vše".

### 2.2 Úložiště

| Konfigurace | CloudKit | Entity |
|---|---|---|
| `synced` | `.private("iCloud.<bundle>")` | `WordEntry`, `WordSet`, `SetMembership`, `IrregularVerb`, `Attempt`, `StudySession`, `ImportBatch` |
| `local` | `.none` | `ReviewState`, `ImportDraft` |

Dvě konfigurace v jednom kontejneru: **domněnka → sonda N1** (že SwiftData korektně synchronizuje jen jednu z nich). Záložní varianta: dva samostatné `ModelContainer`.

### 2.3 Entity

**`WordEntry`** (slovíčko, obsah; podle `09` 1.1) — *zdroj pravdy pro obsah*

| Pole | Typ / default | Poznámka |
|---|---|---|
| `id`, `schemaVersion`, `createdAt`, `updatedAt` | | |
| `language` | String = `"en"` | `en` / `de` (D1) |
| `kind` | String = `"other"` | noun/verb/adjective/adverb/phrase/other |
| `lemma` | String = `""` | bez členu |
| `article` | String? | der/die/das, jen `de`+`noun` |
| `pluralForm` | String? | |
| `pluralKind` | String = `"unknown"` | form/same/none/unknown |
| `translations` | [String] = [] | pořadí = důležitost |
| `altLemmas` | [String] = [] | |
| `example`, `exampleTranslation`, `note`, `ipa` | String? | |
| `source` | String = `"manual"` | photo/csv/manual/builtin |
| `importBatchID` | UUID? | vrácení importu |
| `userEdited` | Bool = false | reimport nepřepíše |
| `deletedAt` | Date? | tombstone |
| `mergedIntoID` | UUID? | alias po sloučení duplicit (ADR-05) |

`dedupKey` se **nepersistuje**, je odvozený (`09` 1.4) a počítá se v doméně.

**`WordSet`** (sada) — `id, schemaVersion, createdAt, updatedAt, name = "", language = "en", unit: String?, deletedAt: Date?`. Hlas se neukládá: en → `en-GB` (D16), de → `de-DE`. Pokud zadavatel někdy dovolí výjimku, přidá se pole (aditivní změna).

**`SetMembership`** (slovo v sadě; slovo je jedno, sady jsou štítky, `09` O3 jako **předpoklad**) — `id, schemaVersion, createdAt, setID: UUID?, wordID: UUID?, deletedAt: Date?`. Samostatná entita místo pole `setIDs` ve slově. Přidání do sady na dvou zařízeních zároveň se tak neztratí (dva záznamy, ne přepis jednoho pole). Duplicitní členství se v doméně sloučí.

**`IrregularVerb`** (podle `09` 5.1) — `id, schemaVersion, createdAt, updatedAt, language, infinitive = "", present3sg: String?, past: [String] = [], participle: [String] = [], auxiliary: String?, separablePrefix: String?, verbKind: String = "other", translations: [String] = [], note: String?, setID: UUID?, source, deletedAt, mergedIntoID`.

**`Attempt`** (pokus, `ReviewLog` z `07` kap. 7) — **zdroj pravdy pro učení, append-only, po zápisu se nemění**

| Pole | Typ / default | Poznámka |
|---|---|---|
| `id`, `schemaVersion`, `createdAt` | | |
| `itemKind` | String = `"word"` | word / verb |
| `itemID` | UUID? | `WordEntry.id` nebo `IrregularVerb.id` (i po sloučení zůstává původní; řeší se aliasem) |
| `direction` | String = `"f2c"` | f2c (cizí→čeština) / c2f (D2) / `forms` u sloves (`07` kap. 4). Karta = `(itemKind, itemID, direction)`. |
| `mode` | String = `"choice"` | choice / typed (D3) |
| `outcome` | String = `"wrong"` | correct / typo / wrong / dontKnow (`07` kap. 2). „Skoro správně" z US-07 = `typo` nebo chybný člen. |
| `hintLevel` | Int = 0 | 0 / 1 / 2 |
| `articleOk`, `pluralOk` | Bool? | fazety; `nil` = netestovalo se |
| `errorTags` | [String] = [] | diacritics, case… (kategorie `07` kap. 5) |
| `formResultsJSON` | String? | jen slovesa: výsledek po tvarech (`past`, `participle`, `present3sg`, `auxiliary`). JSON v jednom poli kvůli R4. |
| `expectedText` | String = `""` | snímek očekávané odpovědi v době pokusu (slovo se může později změnit) |
| `typedText` | String? | co syn napsal (osobní údaj, viz kap. 8) |
| `responseMs` | Int? | jen log, do hodnocení nevstupuje (`07` kap. 2, kalibrace po ~500 pokusech) |
| `answeredAt` | Date = .distantPast | čas zařízení |
| `sessionID` | UUID? | |
| `rating` | Int = 0 | **snímek** hodnocení 1–4 v době pokusu (audit). Rebuild ho počítá znovu z `mode`/`outcome`/`hintLevel`/fazet aktuální funkcí `rating()`, takže změna pravidla (např. „výběr správně = Hard") se projeví zpětně. |
| `stabilityBefore`, `retrievabilityBefore`, `elapsedDays` | Double? | **snímek** pro kalibraci (`07` kap. 7), ne zdroj pravdy |
| `evaluatorVersion` | Int = 1 | verze pravidel US-07 / `rating()` |

Pole „počítá se do FSRS" se neukládá. Do FSRS jde jen první pokus karty v relaci (`07` kap. 2) a to je odvoditelné z `sessionID` + `answeredAt`. `responseMs` je výjimka z pravidla „nic do zásoby": `07` ho požaduje od první verze, protože historii nejde doplnit zpětně.

**`StudySession`** (relace) — `id, schemaVersion, startedAt, endedAt: Date?, mode: String = "words"` (words/verbs/weak/retryWrong), `setIDs: [UUID] = [], directionFilter: String = "both", plannedCount: Int = 0`. Píše ji vždy jen jedno zařízení. `endedAt == nil` = přerušená relace, souhrn se dopočítá z pokusů (US-09).

**`ImportBatch`** — `id, schemaVersion, createdAt, source: String, setID: UUID?, addedCount: Int = 0, mergedCount: Int = 0, revertedAt: Date?`. Fotka ani text OCR se neukládá.

**`ReviewState`** (lokální, **odvozený**, cache stavu FSRS-6 podle `07`)

| Pole | Poznámka |
|---|---|
| `itemKind`, `itemID` (kanonické, po aliasu), `direction` | klíč karty (`ReviewKey`). Unikátnost hlídá doména. Entita je ve stejném stylu jako synchronizované, aby šla bez úprav přesunout. |
| `stability`, `difficulty`, `dueAt`, `lastReviewedAt` | FSRS. `difficulty` se nepoužívá jako metrika obtížnosti slova (`07` kap. 2). |
| `reps`, `lapses`, `successStreak` | |
| `attemptCount`, `lastAttemptID` | kontrola, jestli cache odpovídá logu |
| `algorithmVersion` | změna algoritmu nebo parametrů = přepočet všeho |

**Fáze** (Nové / Učí se / Známé / Zvládnuté) se neukládá, počítá se z `ReviewState` při sestavení relace (`07` kap. 3). Stejně se počítá i odemknutí směru c2f. Aktuální `desired_retention` (hlídač kalibrace, `07` kap. 1) je odvozený z posledních 100 opakování. Uloží se jen lokálně a obě zařízení se po syncu shodnou.

**`ImportDraft`** (lokální) — rozpracovaná kontrolní obrazovka: JSON návrhu dvojic, `setID`, `createdAt`, cesta k dočasnému obrázku. Přežije, když iOS aplikaci na pozadí ukončí. Maže se po uložení, po zrušení a nejpozději po 7 dnech (předpoklad).

### 2.4 Zdroj pravdy vs. odvozené

| Údaj | Zdroj pravdy | Odvozené / jak se přepočítá |
|---|---|---|
| Obsah slovíčka, sady, slovesa | `WordEntry`, `WordSet`, `SetMembership`, `IrregularVerb` | `dedupKey`, počty slov v sadě |
| Co a jak se vybavilo | `Attempt` | — |
| Plán opakování (FSRS), fáze, odemknutí směru, `desired_retention` | — | `ReviewState` = `rebuild(attempts per ReviewKey)`, fáze z `ReviewState` (`07`) |
| Souhrn relace | `Attempt` (+ `StudySession`) | počítá se vždy znovu, neukládá se |
| Úspěšnost (psaní a výběr zvlášť), vybavení po odstupu, trend 28 d, zvládnutá, slabá a zaseknutá slova, slabé kategorie | `Attempt` (+ `ReviewState` pro S) | `StatsCalculator` podle `07` kap. 5, neukládá se. Při růstu objemu lze přidat lokální cache. |
| Výběr slov do relace | `ReviewState` + obsah | `SessionBuilder` |
| Mapa sloučených duplicit | `mergedIntoID` | kanonické ID = sledování aliasů až ke konci řetězu |

Objem: řádově tisíce pokusů ročně (domněnka), plný přepočet na zařízení je levný. Na sondě N7 ověřit čas přepočtu pro 50 000 pokusů.

---

## 3. Životní cyklus dat a selhání

| Situace | Chování |
|---|---|
| Odpověď potvrzena | (1) `Attempt` zapsat a hned explicitně `save()` (nečekat na autosave, **domněnka** o jeho načasování). (2) Inkrementálně aktualizovat `ReviewState` pro daný klíč. Když (2) selže, nevadí, opraví ho start. |
| Pád nebo zabití uprostřed testu | Ztratí se nejvýš rozepsaná nepotvrzená odpověď (US-10). Při startu: relace s `endedAt == nil` starší než X hodin se označí jako přerušená a souhrn jde otevřít z historie. `ReviewState` s `attemptCount`/`lastAttemptID`, které nesedí s logem, se přepočítá. |
| Pád během kontroly importu | `ImportDraft` se ukládá průběžně (lokálně). Po startu nabídka „Pokračovat v kontrole?". Dočasný obrázek zůstává jen v `tmp`/`Caches`, vyloučený ze zálohy. |
| Commit importu | Jedna transakce: `ImportBatch` + nová `WordEntry` + `SetMembership` (+ přidané překlady k existujícím). Pak smazat draft i dočasný obrázek. |
| Příchod změn ze syncu | Na notifikaci o vzdálené změně (**domněnka → sonda N3**, jak ji SwiftData zpřístupní) a při každém startu: (a) přepočet `ReviewState` u dotčených klíčů, (b) detekce duplicit, (c) uplatnění tombstonů. |
| iCloud nepřihlášen, plný, nebo vypnutý | Aplikace jede lokálně (A1). Stav účtu (`CKContainer.accountStatus`) se ukáže jednou a pak už jen v Nastavení. `QUOTA_EXCEEDED` se nesmí projevit pádem (`08` a). Data zůstanou lokálně a odešlou se po vyřešení (**domněnka → T3/T4**). |
| OCR selže nebo vrátí prázdno | Přátelská zpráva, znovu vyfotit nebo zadat ručně (US-02). Nic se neuloží. |
| Hlas pro locale chybí | Tlačítko přehrání ukáže vysvětlení a návod ke stažení. Test ani jiná funkce na tom nezávisí. |
| Smazání sady | Měkce smazat `WordSet` a jeho `SetMembership`. Slova, která nejsou v žádné jiné sadě, také měkce smazat (předpoklad). Před potvrzením ukázat počet slov a pokusů (US-04). Pokusy zůstávají až do vysypání koše. |

---

## 4. Synchronizace a konflikty

SwiftData synchronizuje přes `NSPersistentCloudKitContainer` do soukromé databáze, asynchronně a bez záruky okamžitého doručení (**ověřeno**, `08` b). Aplikace proto musí unést libovolné pořadí příchodu záznamů. Z toho plyne tvar dat:

| Konflikt | Řešení | Kde |
|---|---|---|
| Stejné slovo procvičeno na obou zařízeních offline | Nevzniká konflikt: pokusy jsou nové záznamy s vlastním UUID, výsledek je sjednocení. `ReviewState` se lokálně přepočte z logu seřazeného podle `(answeredAt, id)` (US-12). | Domain `ReviewRebuilder` |
| Různé hodiny zařízení | Pořadí podle `answeredAt` může být mírně posunuté. FSRS pracuje s odstupem ve dnech (`07`), posun v řádu minut je zanedbatelný. „Jiný den" (odemknutí c2f) se počítá v časové zóně zařízení (předpoklad: Europe/Prague). | Domain |
| Stejná karta ve dvou relacích na dvou zařízeních zároveň | Každá relace má vlastní `sessionID`, takže do FSRS jdou první pokusy z obou. Přijímám to jako vzácný případ. | Domain |
| Stejné slovo vytvořeno na obou zařízeních | Vzniknou dva záznamy (unique nejde). Po syncu `MergePlanner` najde shodný `dedupKey` a **nabídne** sloučení (US-12, ne automaticky). Přeživší záznam = starší `createdAt`, při shodě menší `id`. Překlady se sjednotí, poražený dostane `mergedIntoID` + `deletedAt`. Pokusy se **nepřepisují**, rebuild je přes alias přičte přeživšímu. | Domain + Persistence |
| Úprava téhož slova na obou zařízeních | Poslední zápis vyhrává (chování mirroringu, **domněnka → sonda N2**: na úrovni pole nebo celého záznamu). **Q7 „podívat se, co bylo přepsáno" architektura bez vlastní historie revizí nezajistí**, protože mirroring konflikt aplikaci neoznámí (**domněnka**). Návrh k rozhodnutí zadavatele: buď přijmout LWW bez náhledu, nebo přidat append-only `WordRevision` (aditivně, později). | Persistence |
| Smazání na A, úprava na B | Tombstone `deletedAt` vyhrává nad úpravou, protože to doména tak vykládá: smazané slovo se nikde nezobrazí. B uvidí slovo v koši s poznámkou „smazáno na jiném zařízení", obnovení je ruční (US-12 „neoživit bez upozornění"). Tvrdé mazání až po vysypání koše. Zda tvrdě smazaný záznam může upravující zařízení znovu vytvořit, ověří **sonda N4**. Proto se tvrdě maže až po 30 dnech, kdy se obě zařízení nejspíš srovnala. | Domain |
| Přidání do sady na obou zařízeních | Dva `SetMembership` záznamy, sjednocení. Shodná dvojice (setID, wordID) se ignoruje. | Domain |
| Vrácení importu na A, procvičování na B | Vrácení = měkké smazání slov dávky. Pokud už mají pokusy, potvrzení „slovo už má N pokusů". Pokusy z B dorazí a zůstanou v koši spolu se slovem. | Domain |
| Novější verze aplikace na jednom zařízení (TestFlight se aktualizuje nesouběžně) | Staré zařízení: neznámé enumy jako `.unknown`, záznamy s vyšším `schemaVersion`, než zná, zobrazí, ale needituje. Nová pole ignoruje. | Persistence |
| Záznam dorazí před záznamem, na který odkazuje (pokus před slovem) | UUID reference bez relace: pokus „visí" a do rebuildu se započte, až slovo dorazí. Nic se nezahazuje. | Domain |

---

## 5. Import pipeline

```
[Sada + jazyk]──►[Kamera VNDocumentCamera │ PHPicker]──► dočasný JPEG (tmp, mimo zálohu, nikdy do iCloudu)
      │                                                     │
      │                                    OCRVision: RecognizeDocumentsRequest (iOS 26)
      │                                    jazyky [cs, de|en] podle sady, korekce podle T6
      │                                                     ▼
      │                                         RecognizedPage (tabulky, řádky, confidence, bbox)
      │                                                     ▼
      │                         Domain.PairExtractor: tabulka → dvojice; jinak geometrie řádků;
      │                         detekce jazyka sloupce, člen/plurál, označení „ověřit/neúplný/duplicita"
      │                                                     ▼
      └──────────────►  ImportDraft (lokální, průběžně ukládaný)  ◄──  CSV / vložený text (stejný tvar)
                                                            ▼
                                Kontrolní obrazovka (US-03): úpravy, sloučit/rozdělit, prohodit sloupce,
                                výřez řádku z dočasného obrázku pro porovnání
                                                            ▼
                         Commit: ImportBatch + WordEntry + SetMembership (jedna transakce)
                         → smazat draft + obrázek → souhrn „N nových, M doplněno" + [Vrátit import]
```

- OCR běží v zařízení. Vision nemá síťový parametr, ale **ověření, že nic neodchází, je sonda T6** (režim Letadlo + sledování sítě).
- Díky D15 se nemusí stavět větev pro iOS < 26. Geometrické párování z řádků se ponechává, protože je potřeba i na iOS 26, pokud T6 ukáže, že tabulky bez linek se jako tabulka nerozpoznají (**domněnka**, `09` 3.2). Pokud T6 ukáže opak, větev se nepostaví.
- Seznam jazyků se bere za běhu (`supportedRecognitionLanguages`). Pokud v něm `cs` chybí, adaptér pošle jen cizí jazyk a doména ve sloupci češtiny označí víc položek k ověření (`09` 3.3). Kontrolní obrazovka se nemění.
- Vrácení importu: měkké smazání všech `WordEntry` s daným `importBatchID`, které nejsou `userEdited`, a jejich členství. U sloučených (doplněný překlad k existujícímu slovu) se odebere jen přidaný překlad. To vyžaduje uložit v dávce seznam doplnění: pole `ImportBatch.appendedTranslationsJSON: String?` (předpoklad, doplnit do modelu, pokud zadavatel chce vracet i doplnění; jinak se vrací jen nová slova).
- Původní fotku v této verzi nelze ponechat (`09` O2, Q8 = předpoklad „mazat").

---

## 6. Výslovnost

- Adaptér `SpeechAV` implementuje doménový port `SpeechOutput { availability(locale) ; speak(text, locale) }`.
- Locale: en → **`en-GB`** (D16), de → `de-DE`. Vybírá se hlas dané lokalizace s nejvyšší `quality` ze `speechVoices()` (**ověřeno**, že kvality existují a enhanced/premium si stahuje uživatel; `08` c).
- Když `en-GB` hlas chybí: přehrát jiný anglický hlas a ukázat poznámku „britský hlas není stažen" (předpoklad, alternativa je nepřehrát nic; rozhodne zadavatel/UX).
- Text: `lemma`, u DE podstatných jmen s členem (`09` 1.1). U sloves tvary oddělené pauzou.
- Audio session tak, aby respektovala tichý režim (předpoklad z US-18). Přehrání jen na ťuknutí.
- Žádné ukládání ani cache audia, žádná síť. Offline funkčnost je **domněnka → T5**.
- Rozpoznání řeči (P2, D9) není součástí architektury. `SFSpeechRecognizer` hodnocení výslovnosti neposkytuje (**ověřeno**, `08` c). Pokud přijde, bude to samostatný adaptér s `requiresOnDeviceRecognition` a jeho výsledek **nesmí vstupovat do `Attempt`/plánovače** (`08` c).

---

## 7. Testovací strategie bez zařízení

| Oblast | Kde se testuje | Jak | Co tím **neověříme** |
|---|---|---|---|
| Vyhodnocení odpovědi (překlep, diakritika, člen, velká písmena) | `swift test` (macOS/Linux, bez simulátoru) | tabulkové testy | — |
| Plánovač FSRS-6, `rating()`, `rebuild`, fáze | `swift test` | referenční vektory z py-fsrs 6.3.2 (`07` kap. 6) jako golden testy portu. Vlastnostní testy: rebuild z logu = inkrementální aktualizace, permutace pořadí příchodu dají stejný výsledek. | vhodnost parametrů pro syna (M4, `07` kap. 8) |
| Metriky (trend, slabá slova, kategorie) | `swift test` | syntetické logy s danými pravděpodobnostmi, prahy z `07` kap. 6 | — |
| Konflikty a slučování | `swift test` | simulace dvou zařízení jako dvou logů + tombstonů, sjednocení, aliasy | skutečné chování mirroringu (N2, N4) |
| Párování OCR | `swift test` | fixtures = `RecognizedPage` nahrané z reálného zařízení při T6 (vymyšlená slovíčka, `09` kap. 7) | kvalitu OCR samotnou |
| CSV, parser sloves | `swift test` | fixtures z `09` 4.3, 4.4 | — |
| Persistence (mapování, defaulty, měkké mazání, transakce commitu) | simulátor / macOS unit test | `ModelConfiguration(isStoredInMemoryOnly:)`, CloudKit `.none` | CloudKit |
| CloudKit kompatibilita schématu | simulátor přihlášený do iCloudu, Development prostředí | `initializeCloudKitSchema()` v DEBUG + kontrola v CloudKit Console (**ověřeno** jako postup, `08` b) | sync mezi zařízeními |
| UI toky (import → kontrola → uložení, test, souhrn, přerušení) | simulátor, XCUITest | composition root s fake `TextRecognizer` (vrací fixture) a fake `SpeechOutput`; launch argument | kameru, skutečné OCR |
| Přežití pádu | simulátor | ukončit proces po N odpovědích, ověřit log a rebuild | — |
| Vision v simulátoru | simulátor | jen „potrubí" (že adaptér vrací DTO). Kvalitu ne (**domněnka**, že výsledky v simulátoru odpovídají zařízení, nespoléhat). | R2 |
| **Jen na zařízení** | iPhone + iPad syna | **T1, T2** (TestFlight, dětský účet), **T3, T4** (sync, dedup, letadlo), **T5** (hlasy en-GB/de-DE offline), **T6** (OCR jazyky, kvalita, žádná síť), **T7** (TestFlight App Review), T8 (jen P2) + nové sondy N1–N7 | — |

Pravidlo: každá chyba nalezená na zařízení se, pokud to jde, převede na doménový test s nahranými daty.

---

## 8. Soukromí dítěte a retence

Platí pro všechny entity: **žádná síť kromě systémového CloudKit syncu do soukromé databáze synova iCloud účtu. Žádná SDK třetích stran, žádná analytika, žádné vlastní crash reporty.** Odeslat cokoli jinam smí jen výslovné rozhodnutí zadavatele.

| Data | Osobní? | Kde leží | Jak dlouho | Kdo má přístup | Jak smazat |
|---|---|---|---|---|---|
| Fotka stránky | ano (může obsahovat jméno, rukopis) | jen dočasný soubor v sandboxu, mimo zálohu, nikdy iCloud | do commitu/zrušení importu, uklizeno nejpozději při dalším startu, max. 7 dní u rozpracovaného draftu | jen aplikace v zařízení | automaticky |
| `ImportDraft` | nízká | lokální store, ne iCloud | do commitu/zrušení, max. 7 dní | aplikace | automaticky, „Smazat vše" |
| Slovíčka, sady, slovesa | nízká | lokální store + soukromá CloudKit DB syna + záloha zařízení | dokud je syn nesmaže. Koš 30 dní (předpoklad). | syn (jeho Apple ID). Vývojář obsah soukromé DB nevidí (**domněnka**, `08` e). | jednotlivě / sada / „Smazat vše" |
| `Attempt` (vč. `typedText`, `responseMs`), `StudySession` | **ano** (profil učení dítěte, časy používání) | jako výše | dokud existuje slovo; při vysypání koše se smažou se slovem | jako výše | se slovem, „Smazat vše". Zadavatel může rozhodnout, že `typedText` se neukládá (pak nejde zpětně kalibrovat hodnocení překlepů, `07`). |
| `ReviewState` | odvozené | jen lokálně | přepočitatelné | aplikace | „Smazat vše". Smaže se i odinstalací. |
| TestFlight telemetrie (instalace, relace, pády) | ano | Apple / App Store Connect vývojáře | dle Apple | otec jako vývojář | mimo aplikaci. Co přesně se sdílí a zda to tester vypne, je **domněnka → T7** |

**„Smazat všechna data" (US-20):** dvoustupňové potvrzení → smazat všechny záznamy obou konfigurací, uložit, nechat smazání odejít do CloudKit. Pokud jsou zařízení offline, smazání se dokončí po připojení (druhé zařízení to dostane syncem). Alternativa „smazat celou CloudKit zónu" je **domněnka → sonda N5**. Odinstalace iCloud data nemaže (**domněnka**, `02` US-20). Kudy je jde smazat ze systému, ověří N5. Export před smazáním (Q12) čeká na rozhodnutí.

**Oprávnění:** kamera (`NSCameraUsageDescription`), výběr fotek přes `PHPicker` bez plného přístupu (**ověřeno**, `08` e). Mikrofon ani rozpoznávání řeči v P0/P1 nejsou.

**D17 (předpoklad):** otec vkládá fotky na synově zařízení. SwiftData sync je jen v rámci jednoho iCloud účtu (`08` závěr 6). Architektura cestu „z otcova Apple ID" nepodporuje. Kdyby ji zadavatel chtěl, je to stejná práce jako D11 (sdílení, ADR-11).

---

## 9. ADR

### ADR-01 Doména jako čistý Swift balíček, adaptéry kolem
- **Kontext:** algoritmus (A5), vyhodnocení, slučování (A2) a párování OCR (A6) jsou jádro a musí jít testovat bez zařízení. Vision/AVSpeech/CloudKit jsou rizika R2–R4.
- **Varianty:** (a) vše v app targetu nad `@Model`; (b) doména v balíčku bez Apple frameworků + adaptéry; (c) plná „clean" vrstva use-case/repo/entity.
- **Volba:** (b), bez samostatné use-case vrstvy.
- **Důsledky:** mapování `@Model` ↔ hodnotové typy (trochu kódu navíc). Doménové testy běží `swift test` i na Linuxu. Výměna OCR/úložiště se doménou neprotáhne.
- **Přehodnotit, když:** mapování začne tvořit většinu změn a doménové testy nebudou nic chytat. Pak sloučit doménu s modely.

### ADR-02 SwiftData + CloudKit private, dvě konfigurace (synced / local)
- **Kontext:** D6, R4, offline (A1). Odvozený stav a drafty se nemají synchronizovat.
- **Varianty:** (a) SwiftData, vše synced; (b) SwiftData, synced + local; (c) Core Data + `NSPersistentCloudKitContainer` přímo; (d) vlastní CloudKit sync.
- **Volba:** (b). Záloha: dva kontejnery.
- **Důsledky:** nejméně vlastního sync kódu. Přijímáme LWW mirroringu a omezenou viditelnost konfliktů. Sdílení (CKShare) SwiftData neumí (**domněnka**).
- **Přehodnotit, když:** N1 ukáže, že dvě konfigurace nejdou, nebo zadavatel otevře D11/D17. Pak přechod Persistence na Core Data (c), doména beze změny.

### ADR-03 Zdroj pravdy = append-only log pokusů; SRS stav je lokální odvozená cache
- **Kontext:** US-08/12 („přepočítat z historie, ne LWW"), schéma jen aditivní. `07` má parametry k ladění (retence, prahy fází, Leitner jako záloha) a personalizaci po ~1000 opakováních.
- **Varianty:** (a) synchronizovaný `ReviewState` jako zdroj pravdy; (b) synchronizovaný log + synchronizovaná cache; (c) synchronizovaný log + **lokální** cache.
- **Volba:** (c).
- **Důsledky:** žádné konflikty plánu. Změna algoritmu = `algorithmVersion++` a přepočet, bez zásahu do CloudKit. Každé zařízení počítá samo (levné). Rodičovský pohled (D11) dostane log, z něhož si spočítá cokoli.
- **Přehodnotit, když:** přepočet je na zařízení pomalý (N7), nebo personalizace parametrů (`07`) bude chtít parametry sdílené mezi zařízeními. Pak synchronizovat jen parametry (malá entita), ne stav karet.

### ADR-04 UUID reference místo SwiftData relací
- **Kontext:** R4 vyžaduje volitelné relace s inverzí. CloudKit doručuje záznamy v libovolném pořadí. Slučování duplicit musí přesměrovat pokusy bez jejich přepisu.
- **Varianty:** (a) relace Word↔Attempt, Set↔Word (many-to-many); (b) UUID atributy + `SetMembership`.
- **Volba:** (b).
- **Důsledky:** dotazy přes `#Predicate` na UUID (doporučeno indexovat), mazání kaskádou řeší aplikace, „visící" reference jsou legální stav. Odpadá riziko many-to-many přes CloudKit.
- **Přehodnotit, když:** výkon dotazů na UUID bude špatný (N7) nebo SwiftData přinese řízené relace bez těchto omezení.

### ADR-05 Měkké mazání (tombstone) a slučování duplicit aliasem
- **Kontext:** bez unique vznikají duplicity. Smazání nesmí „oživit" slovo (US-12). Import jde vrátit a sadu jde smazat s náhledem.
- **Varianty:** (a) tvrdé mazání + automatický dedup; (b) `deletedAt` + `mergedIntoID`, nabídka sloučení, koš 30 dní.
- **Volba:** (b).
- **Důsledky:** dotazy musí filtrovat `deletedAt == nil`. Úklid koše je periodický. Pokusy zůstávají neměnné. „Smazat vše" je tvrdé (soukromí má přednost).
- **Přehodnotit, když:** N4 ukáže, že mirroring tvrdé smazání spolehlivě nikdy neoživí, a zadavatel nechce koš. Pak lze tombstony zkrátit.

### ADR-06 Evoluce schématu: `schemaVersion`, enumy jako String, jen aditivní změny
- **Kontext:** po nasazení do Production nejde nic mazat ani měnit (**ověřeno**, `08` b). TestFlight se na dvou zařízeních aktualizuje nesouběžně.
- **Varianty:** (a) `VersionedSchema` + migrace; (b) aditivní pole + `schemaVersion` + tolerantní čtení; (c) nový kontejner při velké změně.
- **Volba:** (b) jako pravidlo, (c) jako nouzová cesta. Lokální `local` store smí migrovat volně.
- **Důsledky:** pole se nepřejmenovávají (staré se „zapomene", přidá se nové). Starší klient needituje záznamy s vyšší verzí. Schéma se před **prvním** TestFlight buildem nasadí do Production (T3).
- **Přehodnotit, když:** nutná změna významu pole. Pak (c) s migrací dat v aplikaci.

### ADR-07 OCR adaptér na Vision v zařízení, neutrální `RecognizedPage`, párování v doméně
- **Kontext:** R2, R5, D15 (iOS 26 → `RecognizeDocumentsRequest` s tabulkami, **ověřeno**, `08` d / `09` 3.1).
- **Varianty:** (a) VisionKit Live Text / `DataScannerViewController`; (b) `RecognizeDocumentsRequest` + doménové párování; (c) `RecognizeTextRequest` + geometrie; (d) cloudové OCR.
- **Volba:** (b), s geometrickou větví z (c) jen pokud ji T6 vyžádá. (d) vyloučeno (R5).
- **Důsledky:** změna strategie OCR po T6 = jen adaptér. Párování se testuje na nahraných stránkách. Jazyky podle `supportedRecognitionLanguages` za běhu.
- **Přehodnotit, když:** T6 ukáže, že tištěné tabulky mají < 90 % správných dvojic (`09` 3.4). Pak má ruční a CSV zadání větší váhu. Rozhodne zadavatel.

### ADR-08 Import jako dávka: draft → kontrola → `ImportBatch` → vrácení
- **Kontext:** US-02/03/19, `09` 2.1 (nic se neukládá bez kontroly, vrácení importu).
- **Varianty:** (a) ukládat rovnou a opravovat v sadě; (b) jen paměťový draft; (c) lokálně perzistovaný draft + dávka s ID.
- **Volba:** (c).
- **Důsledky:** jedna kontrolní obrazovka pro foto, CSV i vložený text. Vrácení je měkké smazání dávky. Draft přežije zabití aplikace, fotka ale jen dočasně (kap. 8).
- **Přehodnotit, když:** zadavatel rozhodne ponechávat zdrojovou fotku (Q8). Pak samostatná lokální entita, nikdy ne do iCloudu bez rozhodnutí.

### ADR-09 Výslovnost přes `AVSpeechSynthesizer`, en-GB / de-DE, bez ukládání audia
- **Kontext:** D9, D16, R3, offline.
- **Varianty:** (a) systémová syntéza; (b) předem nahrané audio; (c) cloud TTS.
- **Volba:** (a), nejlepší dostupná kvalita hlasu pro locale, návod ke stažení lepšího hlasu.
- **Důsledky:** kvalita závisí na staženém hlasu (aplikace hlas stáhnout nemůže, **domněnka**, `08` c). (b) má licenční i objemový problém, (c) porušuje R5.
- **Přehodnotit, když:** T5 ukáže, že `en-GB` nebo `de-DE` offline chybí nebo je nesrozumitelný.

### ADR-10 Zápis po každé odpovědi a rekonstrukce při startu
- **Kontext:** US-10, A4.
- **Varianty:** (a) uložit relaci na konci; (b) uložit každý `Attempt` hned, odvozený stav opravit z logu.
- **Volba:** (b).
- **Důsledky:** víc malých zápisů a CloudKit operací (zanedbatelné při desítkách odpovědí na relaci, **domněnka**). Konzistence se kontroluje při startu.
- **Přehodnotit, když:** CloudKit throttling při rychlém testu (T4).

### ADR-11 Rodičovský pohled a vkládání z jiného Apple ID: odloženo, ne vyloučeno
- **Kontext:** D11, D17, SwiftData jen private DB (**domněnka**, `08` b).
- **Varianty:** (a) navrhnout teď CKShare; (b) neřešit, ale držet log pokusů a hranici Persistence.
- **Volba:** (b). Rozsah a způsob sdílení (Q13) rozhoduje zadavatel.
- **Důsledky:** dnes žádný kód. Později buď Core Data + CKShare za hranicí Persistence, nebo export agregací se souhlasem syna.
- **Přehodnotit, když:** zadavatel otevře D11 nebo vyvrátí D17.

### ADR-12 Žádná síť a žádná SDK třetích stran
- **Kontext:** R5, dítě, `01` M7.
- **Volba:** jediný síťový kanál je systémový CloudKit. Žádné analytické, crash ani reklamní SDK. Diagnostika jen lokální (export logu na vyžádání, bez osobních dat; předpoklad).
- **Důsledky:** méně informací o chybách z terénu. Spoléháme na TestFlight pády a zpětnou vazbu otce.
- **Přehodnotit, když:** výslovně rozhodne zadavatel.

### ADR-13 Minimální iOS 26 (D15)
- **Kontext:** obě zařízení mají iOS 26.
- **Důsledky:** k dispozici je `RecognizeDocumentsRequest` a aktuální SwiftData. Není potřeba větev pro starší systém. Riziko: méně zkušeností komunity s novými API, proto sondy N1–N3 cílí právě na iOS 26.
- **Přehodnotit, když:** přibude zařízení se starším iOS (rozhodne zadavatel).

---

## 10. TestFlight a provoz

- **Brána 0 (před vývojem kromě domény):** T1 (dětský účet přijme veřejný odkaz externí skupiny a nainstaluje build), T2 (Média a nákupy ≠ iCloud účet, plán B). Výsledek nemění kód, jen distribuci. Pokud neprojde ani plán B, je potřeba rozhodnutí zadavatele o D5 (`08` a).
- **Před 1. externím buildem:** `initializeCloudKitSchema()` v DEBUG, kontrola v Console, **Deploy do Production** (T3; nasazení nutné i pro TestFlight je **domněnka**). Entitlements: iCloud/CloudKit kontejner, Background Modes → Remote notifications (**ověřeno**, `08` b). URL zásad ochrany soukromí a odpovědi App Privacy („nesbíráme") připravit (T7). V metadatech nepoužívat označení „pro děti" (**ověřeno**, `08` e).
- **Build 1 bez syncu** (`04`): `synced` konfigurace bude už v buildu 1 se stejným schématem, jen s `cloudKitDatabase: .none`. V buildu 2 se přepne na `.private`. Zda data z buildu 1 při přepnutí odejdou do CloudKit, ověří **sonda N6**. Pokud ne, build 2 nabídne jednorázový export → import.
- **90 dní:** build vyprší (**ověřeno**). Připomínka v kalendáři otce, nový build nejpozději v den 80. Data jsou v lokálním store a iCloudu, vypršení je neztratí (**domněnka**, ověřit při první obnově buildu).

---

## 11. Předpoklady k ověření sondou (pro `verifier`)

Převzaté z `08`: **T1–T8** (R1: T1, T2; R4: T3, T4; R3: T5, T8; R2+R5: T6; R5/D5: T7). R1 a R2 jsou v tomto návrhu **jen předpoklady**, nic na nich nestaví jako na faktu.

Nové sondy z tohoto návrhu:

| # | Předpoklad | Jak ověřit | Dopad, když neplatí |
|---|---|---|---|
| N1 | Jeden `ModelContainer` se dvěma `ModelConfiguration` (`.private` a `.none`) synchronizuje jen první | iOS 26, dvě zařízení, CloudKit Console | dva kontejnery (ADR-02) |
| N2 | `[String]` atributy se synchronizují. Konflikt úprav je LWW (zjistit, zda na úrovni pole, nebo celého záznamu). | součást T4: souběžná offline úprava různých polí téhož slova | při LWW celého záznamu: zvážit `WordRevision` nebo rozdělit pole (Q7) |
| N3 | SwiftData na iOS 26 dá aplikaci signál o změnách přišlých ze syncu (notifikace / history) | prototyp + log | přepočet jen při startu a při návratu do popředí |
| N4 | Tvrdě smazaný záznam se úpravou z offline zařízení neoživí / oživí | součást T4 | délka koše a pravidla tombstonů (ADR-05) |
| N5 | „Smazat vše" spolehlivě odstraní data z CloudKit a lze je smazat i ze systémového Nastavení po odinstalaci | dvě zařízení, CloudKit Console, Nastavení → iCloud → Spravovat úložiště | přidat mazání zóny přes CloudKit API, upravit text v Nastavení |
| N6 | Přepnutí konfigurace z `.none` na `.private` mezi buildy nahraje existující lokální data | build 1 → build 2 na zařízení | jednorázová migrace exportem |
| N7 | Plný `rebuild` pro 50 000 pokusů a UUID dotazy jsou na iPhonu syna pod 1 s | syntetická data na zařízení | inkrementální cache statistik, indexy |

---

## 12. Otevřené body pro zadavatele (nerozhoduji)
- **Q7:** stačí „poslední úprava vyhrává" bez náhledu přepsaného? Jinak přidat `WordRevision` (kap. 4).
- **Ukládat `typedText`** (co syn napsal)? `07` ho chce pro kalibraci, ale je to osobní údaj (kap. 8).
- **Koš 30 dní** a **draft max. 7 dní**: přijatelné lhůty?
- **Vracet při vrácení importu i doplněné překlady** k existujícím slovům (kap. 5)?
- **Q12 export** před smazáním dat.
- Z `07`: má-li zůstat „první pokus karty v relaci" pravidlem i při souběžných relacích na dvou zařízeních (kap. 4).

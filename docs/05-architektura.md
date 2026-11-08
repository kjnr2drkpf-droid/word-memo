# 05 — Architektura

Stav k 2026-11-07. Autor: architekt. Zdroj pravdy: `00-zadani.md` (vč. D5 Ad Hoc, D15 iOS 26, D16 en-GB, D17 potvrzeno, R6). Vstupy: `01`, `02`, `04`, `08`, `09`, `10`. **Revize 2026-11-07:** distribuce se mění z TestFlightu na Ad Hoc (TestFlight dětský účet nespustí), viz A11, kap. 10 a ADR-14. Tvrzení o platformě přebírám z `08`/`09` se stejným značením (**ověřeno** / **domněnka**); nová tvrzení, která jsem neověřil, jsou **domněnka → sonda** (seznam v kap. 11). **Revize 2026-11-08:** zapracováno D18 (jazyky se nemíchají) a rozhodnutí D19 (jazyk sady je neměnný, slovo nejde přesunout do jiného jazyka) a D20 (žádný souhrn přes oba jazyky), D21 (možnosti výběru se doplňují z jiných sad téhož jazyka) a D22 (import nabízí jen sady aktivního jazyka): jazyk je neměnná vlastnost sady, denormalizovaná do slova, slovesa, karty, pokusu a relace; dotazy, fronta, plánovač, výběr možností, metriky, OCR a hlas jsou po jazycích; aktivní jazyk je lokální předvolba zařízení (A13, kap. 13, ADR-15).

> **Sladěno s `07-opakovani-algoritmus.md`** (FSRS-6, karta = slovo × směr, sloveso = jedna karta, fáze se počítá a neukládá, log musí mít `mode`, `outcome`, `hintLevel`, `rating`, `responseMs`, `typedText`, fazety a výsledky po tvarech). Zdrojem pravdy je historie pokusů. Stav FSRS je z ní kdykoli přepočitatelný (ADR-03). Kdyby se FSRS nahradilo Leitnerem (`07` kap. 1), mění se jen doménová funkce a lokální cache, ne synchronizované schéma.

## Závěr

1. **Čtyři moduly:** `Domain` (čisté Swift, bez Apple frameworků), `Persistence` (SwiftData + CloudKit), `Vision`-adaptér (OCR), `Speech`-adaptér (AVSpeech) a nad nimi `App` (SwiftUI). Každou hranici ospravedlňuje konkrétní požadavek (tabulka v kap. 1).
2. **Zdroj pravdy je append-only log pokusů (`Attempt`).** Stav FSRS (`ReviewState`: stabilita, obtížnost, termín, opakování, pády, úspěchy v řadě), fáze karty, statistiky, trend a slabá místa jsou **odvozené** a přepočitatelné. `ReviewState` je **lokální cache, která se nesynchronizuje**. Tím zmizí nejhorší třída sync konfliktů a algoritmus z `07` jde měnit bez zásahu do CloudKit schématu, které je po nasazení jen aditivní (**ověřeno**, `08` b).
3. **Model je CloudKit-kompatibilní (R4):** žádné `unique`, všechno s defaultem nebo volitelné, žádné SwiftData relace, jen UUID reference. V každé entitě je `schemaVersion`, enumy se ukládají jako String, mazání je měkké (tombstone), duplicity se slučují aliasem (`mergedIntoID`), ne přepisem historie.
4. **Import:** fotka zůstává jen dočasně v zařízení. OCR běží v zařízení (`RecognizeDocumentsRequest`, iOS 26, D15). Párování řádků je čistá doménová logika testovatelná na nahraných výstupech OCR. Na konci je vždy kontrolní obrazovka, uloží se jedna dávka (`ImportBatch`) a celou dávku jde vrátit.
5. **Soukromí:** žádná síť kromě systémové synchronizace CloudKit do **soukromé** databáze synova iCloudu, žádná SDK třetích stran, žádná analytika. Smazat jde slovo, sadu (koš 30 dní) i všechna data najednou.
6. **Testy bez zařízení:** doména (vyhodnocení, plánovač, přestavba z logu, slučování konfliktů, párování OCR, CSV) jde přes `swift test` i mimo simulátor. Kvalitu OCR, hlasy, CloudKit mezi dvěma zařízeními a Ad Hoc instalaci pod dětským iCloudem jde ověřit **jen na zařízení** (T3–T8, U1–U3).
7. **Brány před vývojem:** R1 je **uzavřeno** (TestFlight dětský účet nespustí, ověřeno na zařízení). Distribuce je Ad Hoc (D5) a její provoz ověří sondy U1–U3 (`10`). R2 (T6) je dál **sonda, ne fakt**: výsledek změní jen OCR adaptér. **R6 (prostředí CloudKit Development vs. Production)** je nové a plyne z distribuce: data se mezi prostředími nekopírují, takže prostředí se musí zvolit a držet (ADR-14). Doménový kód, model ani moduly se kvůli změně distribuce nemění.
8. **Jazyky se nemíchají (D18–D20, ADR-15):** jazyk je vlastnost sady a po vzniku se nemění (D19, rozhodnuto). Slovo a sloveso ho při vzniku převezmou ze sady, pokus ze slova, relace z aktivního jazyka (denormalizace kvůli `#Predicate` bez relací a kvůli CloudKit, který nic nevynutí). Invariant „sada ↔ slovo ↔ karta ↔ pokus mají stejný jazyk" hlídá doména při zápisu (jazyk nejde změnit, přesun ani sloučení mezi jazyky nejde) a při čtení (`LanguageGuard`: co invariant porušuje, třeba po syncu, se nikam nezapočte a ukáže se v kontrole dat). Jazyk je povinný parametr každého dotazu a doménové funkce, takže zapomenutý filtr je chyba při kompilaci. Souhrn přes oba jazyky neexistuje nikde, ani v budoucím rodičovském pohledu (D20). Aktivní jazyk je **lokální předvolba zařízení**, nesynchronizuje se.

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
| A11 | Distribuce Ad Hoc přes Mac a Xcode (UDID, Developer Mode), ruční obnova podpisu | D5, `10` | Provozní proces (kap. 10), volba a držení prostředí CloudKit (ADR-14, R6) |
| A12 | Minimum iOS 26 | D15 | `RecognizeDocumentsRequest` je k dispozici, odpadá starší větev OCR |
| A13 | Jazyky se nemíchají: jeden aktivní jazyk, sada má jeden jazyk, plánování, slovesa, hlas, OCR, pokrok a slabá místa po jazycích | D18, D19, D20 | Pole `language` v obsahu i logu, invariant hlídaný doménou při zápisu i čtení, jazyk jako povinný parametr dotazů a doménových funkcí, lokální předvolba aktivního jazyka (kap. 13, ADR-15) |

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
| **Domain** (Swift Package) | Hodnotové typy (`WordValue`, `AttemptValue`, `ReviewKey`…), `Language` a `LanguageGuard` (invariant D18, kap. 13), `DistractorPicker` (výběr ze 4: stejná sada, pak jiné sady téhož jazyka, D21), `AnswerEvaluator` (US-07), `Scheduler` a `ReviewRebuilder` (US-08, `07`), `SessionBuilder` (US-05/06), `StatsCalculator` (US-09/14/15), `DedupKey` a `MergePlanner` (US-12), `PairExtractor` z `RecognizedPage` (US-02), `CSVParser` (US-19), `VerbLineParser` (`09` 5.2) | Foundation | SwiftData, Vision, AVFoundation, CloudKit, SwiftUI | A5: algoritmus a vyhodnocení musí jít testovat deterministicky a bez zařízení. A2: pravidla slučování musí být testovatelná simulací dvou logů. A6: párování OCR musí jít testovat na nahraných datech nezávisle na verzi Vision. A13: invariant jazyka musí jít ověřit simulací syncu dvou zařízení bez zařízení. |
| **Persistence** | `@Model` třídy (kap. 2), mapování na doménové hodnoty, repozitáře, dva `ModelConfiguration` (synced/local), stav iCloud účtu, „smazat vše" | SwiftData, CloudKit (jen stav účtu a mazání) | Vision, AVSpeech, UI | A3: schéma je nevratné, doména se proto nesmí měnit kvůli tvaru úložiště a naopak. A10: rodičovský pohled bude nejspíš chtít sdílení (CKShare), které SwiftData neumí (**domněnka**, `08` b). Výměna mechanismu se pak dotkne jen tohoto modulu. |
| **OCRVision** | Volání `RecognizeDocumentsRequest` (iOS 26+), případně `RecognizeTextRequest`, kamera `VNDocumentCameraViewController`, výběr `PHPicker`, převod na `RecognizedPage` | Vision, VisionKit, PhotosUI | Persistence, doménová pravidla párování | A6/R2: OCR strategie se po T6 může změnit (jazyky, korekce, tabulky vs. řádky). Změna se nesmí dotknout párování ani kontrolní obrazovky. |
| **SpeechAV** | Výběr hlasu podle locale a kvality, přehrání, dotaz na dostupnost | AVFoundation | Doména slovíček (dostane jen text + locale) | A8/R3: kvalita a dostupnost hlasů se liší podle zařízení (T5). Testy UI potřebují fake. |
| **App** | Obrazovky, navigace, view-modely, composition root | vše výše | — | — |

**Záměrně nenavrhuji:** samostatnou „use-case" vrstvu (view-model volá doménové funkce a repozitář přímo), vlastní sync engine (CloudKit sync dělá SwiftData), síťový modul (žádná síť), modul pro rozpoznání řeči (P2, D9; až přijde, bude to další adaptér vedle SpeechAV), oddělené úložiště, entity nebo CloudKit zónu pro každý jazyk (D18 řeší pole `language`, invariant a filtr, ADR-15).

**Ilustrace rozhraní (jen tvar, ne kód produktu):**

```swift
// Domain – jazyk bez kódů Vision/AVSpeech; mapování na BCP 47 je tabulka řetězců
enum Language: String { case en, de, unknown }             // unknown = neplatné, nikam se nezapočte
struct LanguageHint { var foreign: Language }               // OCR dostane [cizí jazyk, čeština]

// Domain – zápis jen přes funkce, které jazyk převezmou ze zdroje, nebo selžou
func makeMembership(word: WordValue, set: SetValue) throws -> MembershipValue   // .languageMismatch
func makeAttempt(item: ItemValue, session: SessionValue, answer: AnswerValue) throws -> AttemptValue
func buildSession(language: Language, cards: [CardValue], now: Date) -> [CardValue]  // jazyk bez defaultu

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
- **Jazyk (D18, ADR-15):** `language: String = "unknown"` ve `WordSet`, `WordEntry`, `IrregularVerb`, `Attempt`, `StudySession`, `ImportBatch` a v lokálních `ReviewState`, `ImportDraft`. Default je záměrně `unknown`, ne `en`: chybějící hodnota se nesmí potichu stát angličtinou. Po vzniku záznamu se jazyk nemění. Změna defaultu z `en` je bez nákladu, schéma ještě není v Production (ADR-06).
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
| `language` | String = `"unknown"` | `en` / `de` (D1). **Neměnné** (D19), převzaté při vzniku ze sady, do které slovo vzniká (D18, ADR-15). `unknown` = slovo se nezobrazí v žádném jazyce (kap. 13). |
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

**`WordSet`** (sada) — `id, schemaVersion, createdAt, updatedAt, name = "", language = "unknown", unit: String?, deletedAt: Date?`. **`language` je zdroj pravdy jazyka (D18), zapíše se při založení (výchozí = aktivní jazyk) a nemění se** (D19, ADR-15). Chybně založenou sadu uživatel opraví novou sadou ve správném jazyce a starou smaže (D19). Migrace jazyka sady neexistuje. Hlas se neukládá: en → `en-GB` (D16), de → `de-DE`. Pokud zadavatel někdy dovolí výjimku, přidá se pole (aditivní změna).

**`SetMembership`** (slovo v sadě; slovo je jedno, sady jsou štítky, `09` O3 jako **předpoklad**) — `id, schemaVersion, createdAt, setID: UUID?, wordID: UUID?, deletedAt: Date?`. Samostatná entita místo pole `setIDs` ve slově. Přidání do sady na dvou zařízeních zároveň se tak neztratí (dva záznamy, ne přepis jednoho pole). Duplicitní členství se v doméně sloučí. Členství vzniká jen doménovou funkcí, která ověří `word.language == set.language` (D18). Členství, které invariant nesplňuje (např. dorazí ze syncu), je neplatné: nepočítá se a ukáže se v kontrole dat (kap. 13). Vlastní pole `language` nemá, jeho platnost se odvozuje.

**`IrregularVerb`** (podle `09` 5.1) — `id, schemaVersion, createdAt, updatedAt, language = "unknown"` (neměnné, D19, ze sady `setID`, invariant `verb.language == set.language`), `infinitive = "", present3sg: String?, past: [String] = [], participle: [String] = [], auxiliary: String?, separablePrefix: String?, verbKind: String = "other", translations: [String] = [], note: String?, setID: UUID?, source, deletedAt, mergedIntoID`.

**`Attempt`** (pokus, `ReviewLog` z `07` kap. 7) — **zdroj pravdy pro učení, append-only, po zápisu se nemění**

| Pole | Typ / default | Poznámka |
|---|---|---|
| `id`, `schemaVersion`, `createdAt` | | |
| `itemKind` | String = `"word"` | word / verb |
| `itemID` | UUID? | `WordEntry.id` nebo `IrregularVerb.id` (i po sloučení zůstává původní; řeší se aliasem) |
| `language` | String = `"unknown"` | **kopie** jazyka položky v okamžiku zápisu (D18). Slouží `#Predicate` statistik a fronty bez dotazu na položku. Zdroj pravdy je jazyk položky: pokus s jiným jazykem se nikam nezapočte, `unknown` se při čtení doplní z položky (kap. 13). |
| `direction` | String = `"f2c"` | f2c (cizí→čeština) / c2f (D2) / `forms` u sloves (`07` kap. 4). Karta = `(itemKind, itemID, direction)`. |
| `mode` | String = `"choice"` | choice / typed (D3) |
| `choiceCount` | Int = 4 | počet nabídnutých možností u `choice`. Méně než 4 jen tehdy, když **celý jazyk** nemá dost slov (D21, kap. 13.3). Malá sada se doplní z jiných sad téhož jazyka. `07` z pole počítá míru hádání 1/k. |
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

**`StudySession`** (relace) — `id, schemaVersion, startedAt, endedAt: Date?, mode: String = "words"` (words/verbs/weak/retryWrong), `setIDs: [UUID] = [], directionFilter: String = "both", plannedCount: Int = 0, language: String = "unknown"`. **Jedna relace = jeden jazyk** (D18): `language` = aktivní jazyk při startu, `setIDs` smí obsahovat jen sady téhož jazyka. Píše ji vždy jen jedno zařízení. `endedAt == nil` = přerušená relace, souhrn se dopočítá z pokusů (US-09).

**`ImportBatch`** — `id, schemaVersion, createdAt, source: String, setID: UUID?, language: String = "unknown"` (jazyk sady, zvolený před skenem), ` addedCount: Int = 0, mergedCount: Int = 0, revertedAt: Date?`. Fotka ani text OCR se neukládá.

**`ReviewState`** (lokální, **odvozený**, cache stavu FSRS-6 podle `07`)

| Pole | Poznámka |
|---|---|
| `language` | kopie jazyka položky pro dotaz fronty „k procvičení" (D18). Při rebuildu se bere z položky, ne z pokusů. |
| `itemKind`, `itemID` (kanonické, po aliasu), `direction` | klíč karty (`ReviewKey`). Jazyk není součástí klíče, je funkcí položky. Unikátnost hlídá doména. Entita je ve stejném stylu jako synchronizované, aby šla bez úprav přesunout. |
| `stability`, `difficulty`, `dueAt`, `lastReviewedAt` | FSRS. `difficulty` se nepoužívá jako metrika obtížnosti slova (`07` kap. 2). |
| `reps`, `lapses`, `successStreak` | |
| `attemptCount`, `lastAttemptID` | kontrola, jestli cache odpovídá logu |
| `algorithmVersion` | změna algoritmu nebo parametrů = přepočet všeho |

**Fáze** (Nové / Učí se / Známé / Zvládnuté) se neukládá, počítá se z `ReviewState` při sestavení relace (`07` kap. 3). Stejně se počítá i odemknutí směru c2f. Aktuální `desired_retention` (hlídač kalibrace, `07` kap. 1) je odvozený z posledních 100 opakování **daného jazyka** a vede se po jazycích. Uloží se jen lokálně a obě zařízení se po syncu shodnou.

**`ImportDraft`** (lokální) — rozpracovaná kontrolní obrazovka: JSON návrhu dvojic, `setID`, `language` (pevně od začátku importu), `createdAt`, cesta k dočasnému obrázku. Přežije, když iOS aplikaci na pozadí ukončí. Maže se po uložení, po zrušení a nejpozději po 7 dnech (předpoklad).

### 2.4 Zdroj pravdy vs. odvozené

| Údaj | Zdroj pravdy | Odvozené / jak se přepočítá |
|---|---|---|
| Obsah slovíčka, sady, slovesa | `WordEntry`, `WordSet`, `SetMembership`, `IrregularVerb` | `dedupKey`, počty slov v sadě |
| Jazyk | `WordSet.language` při založení; od vzniku slova/slovesa i jeho `language` (neměnné, invariant) | `Attempt.language`, `StudySession.language`, `ReviewState.language` jsou kopie. Při nesouladu platí položka a záznam se vyřadí (kap. 13). Platnost členství = shoda jazyků. |
| Aktivní jazyk | lokální předvolba zařízení (ne CloudKit, ADR-15) | výchozí na zařízení bez předvolby = jazyk nejnovějšího platného pokusu |
| Co a jak se vybavilo | `Attempt` | — |
| Plán opakování (FSRS), fáze, odemknutí směru, `desired_retention` | — | `ReviewState` = `rebuild(attempts per ReviewKey)`, fáze z `ReviewState` (`07`) |
| Souhrn relace | `Attempt` (+ `StudySession`) | počítá se vždy znovu, neukládá se |
| Úspěšnost (psaní a výběr zvlášť), vybavení po odstupu, trend 28 d, zvládnutá, slabá a zaseknutá slova, slabé kategorie | `Attempt` (+ `ReviewState` pro S) | `StatsCalculator(language:)` podle `07` kap. 5, vždy pro jeden jazyk, neukládá se. Při růstu objemu lze přidat lokální cache. |
| Výběr slov do relace, fronta „k procvičení", distraktory | `ReviewState` + obsah | `SessionBuilder`, `DistractorPicker`, vždy pro jeden jazyk (kap. 13.3) |
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
| Přepnutí aktivního jazyka | Jen mimo relaci a mimo kontrolu importu (D18). Uloží se lokálně, fronta, statistiky a seznamy se přepočtou pro nový jazyk. Přerušená relace si nese `StudySession.language` a nabídne se jen v jejím jazyce. Rozpracovaný `ImportDraft` má vlastní jazyk a přepnutím se nemění. |
| Porušený invariant jazyka (záznam ze syncu, starší build, chyba) | `LanguageGuard` při startu a po syncu: neplatné členství, pokus nebo stav s jiným jazykem než položka, položka nebo sada `unknown` → nezapočte se do fronty, testu, distraktorů ani statistik. Počet se ukáže v Nastavení → Kontrola dat (počítá se lokálně, nic se neodesílá). Pokus se nepřepisuje, neplatné členství smaže uživatel. |
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
| Přesun slova mezi sadami | Přesun = nové `SetMembership` v cílové sadě + tombstone starého v jedné transakci. **Jen do sady stejného jazyka** (D19). Do sady jiného jazyka doména odmítne (`languageMismatch`) a UI ji nenabídne. Slovo zapsané v chybném jazyce uživatel zadá znovu v sadě správného jazyka a staré smaže (D19). Žádná migrace ani převod historie mezi jazyky neexistuje. | Domain |
| Souběžný přesun (A: S1 → S2, B: S1 → S3, vše jeden jazyk) | Slovo skončí v S2 i S3 (sjednocení členství), S1 má tombstone. Jazyk ani SRS stav se nemění (O3). Uživatel případně jedno členství odebere. | Domain |
| Jiný jazyk u slova nebo sady (LWW z chybného buildu, ruční zásah) | Běžně nevzniká: jazyk se zapisuje jen při vzniku a úprava slova pole nepřenáší. Když přesto přijde, projeví se jako nesoulad se sadami a pokusy: `LanguageGuard` vyřadí dotčená členství a pokusy (kap. 3). Automaticky se neopravuje, protože bez historie revizí nejde poznat, která hodnota je původní, a změna jazyka není povolená operace (D19). | Domain |
| Duplicity napříč jazyky | `dedupKey` začíná jazykem a `MergePlanner` navíc pár různých jazyků odmítne. en „Hand" × de „Hand", en „kind" × de „Kind" nejsou duplicity. | Domain |
| Vrácení importu na A, procvičování na B | Vrácení = měkké smazání slov dávky. Pokud už mají pokusy, potvrzení „slovo už má N pokusů". Pokusy z B dorazí a zůstanou v koši spolu se slovem. | Domain |
| Novější verze aplikace na jednom zařízení (Ad Hoc buildy se instalují ručně a nesouběžně) | Staré zařízení: neznámé enumy jako `.unknown`, záznamy s vyšším `schemaVersion`, než zná, zobrazí, ale needituje. Nová pole ignoruje. | Persistence |
| Záznam dorazí před záznamem, na který odkazuje (pokus před slovem, členství před sadou) | UUID reference bez relace: pokus „visí" a do rebuildu se započte, až slovo dorazí. Shoda jazyka se ověří až s ním. Do té doby se záznam nepočítá do žádného jazyka. Nic se nezahazuje. | Domain |

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
- **Jazyk importu (D18):** je to jazyk cílové sady, pevně daný **před skenem** a uložený v `ImportDraft`/`ImportBatch`. Během kontroly se nemění. Nová sada dostane aktivní jazyk. Tok importu nabízí **jen sady aktivního jazyka** (D22, rozhodnuto). Jiný jazyk znamená přepnout aktivní jazyk mimo import. Repozitář pro výběr sady v importu proto bere `language` = aktivní jazyk, stejně jako ostatní dotazy.
- **Jazykový kontext OCR:** doména předá `LanguageHint(foreign:)`, adaptér ho převede na `[de, cs]` nebo `[en, cs]`. Konkrétní kódy (`de-DE`, `en-US`/`en-GB`, `cs-CZ`) vybere za běhu ze `supportedRecognitionLanguages` podle prefixu, ne natvrdo. Že seznam jazyků není v dokumentaci a zjišťuje se za běhu, je **ověřeno** (`08` d, `09` 3.1). Že v něm na iOS 26 jsou `de`, `en` a hlavně `cs`, je **domněnka → T6**. Pořadí v `recognitionLanguages` je priorita (**ověřeno** u `VNRecognizeTextRequest`/`RecognizeTextRequest`, `08` d). Zda jde jazyky nastavit i u `RecognizeDocumentsRequest` (dokumentace podle `08`/`09` uvádí jen vlastnost `supportedRecognitionLanguages`), je **domněnka → sonda N8**. Pokud nejde, rozhodne T6 mezi `RecognizeDocumentsRequest` bez nápovědy a `RecognizeTextRequest` s jazyky + geometrickým párováním.
- `PairExtractor` dostane očekávaný jazyk. Když heuristika najde jiný cizí jazyk (ä ö ü ß v anglické sadě), označí stránku „vypadá to na němčinu, sada je anglická" a nic nepřepne. Dedup se hledá jen v knihovně téhož jazyka. CSV řádek se sloupcem `language` odlišným od jazyka sady je chyba s důvodem a neimportuje se (`09` 4.1).
- Seznam jazyků se bere za běhu (`supportedRecognitionLanguages`). Pokud v něm `cs` chybí, adaptér pošle jen cizí jazyk a doména ve sloupci češtiny označí víc položek k ověření (`09` 3.3). Kontrolní obrazovka se nemění.
- Vrácení importu: měkké smazání všech `WordEntry` s daným `importBatchID`, které nejsou `userEdited`, a jejich členství. U sloučených (doplněný překlad k existujícímu slovu) se odebere jen přidaný překlad. To vyžaduje uložit v dávce seznam doplnění: pole `ImportBatch.appendedTranslationsJSON: String?` (předpoklad, doplnit do modelu, pokud zadavatel chce vracet i doplnění; jinak se vrací jen nová slova).
- Původní fotku v této verzi nelze ponechat (`09` O2, Q8 = předpoklad „mazat").

---

## 6. Výslovnost

- Adaptér `SpeechAV` implementuje doménový port `SpeechOutput { availability(locale) ; speak(text, locale) }`.
- Locale se bere z jazyka **položky** (= jazyk sady díky invariantu, D18), ne z aktivní předvolby. Položka `unknown` se nepřehrává. Mapování `Language` → BCP 47 je v doméně jako tabulka řetězců, Domain AVFoundation nezná.
- Locale: en → **`en-GB`** (D16), de → `de-DE`. Vybírá se hlas dané lokalizace s nejvyšší `quality` ze `speechVoices()` (**ověřeno**, že kvality existují a enhanced/premium si stahuje uživatel; `08` c).
- Když `en-GB` hlas chybí: přehrát jiný anglický hlas a ukázat poznámku „britský hlas není stažen" (předpoklad, alternativa je nepřehrát nic; rozhodne zadavatel/UX). Náhrada nikdy nepřekročí jazyk: německé slovo se nepřečte anglickým hlasem ani naopak (D18).
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
| Invariant jazyka (D18) | `swift test` | testy L1–L15 (kap. 13.4), vč. vlastnostního testu dvou simulovaných zařízení | skutečné pořadí a obsah záznamů z mirroringu (T4) |
| Párování OCR | `swift test` | fixtures = `RecognizedPage` nahrané z reálného zařízení při T6 (vymyšlená slovíčka, `09` kap. 7) | kvalitu OCR samotnou |
| CSV, parser sloves | `swift test` | fixtures z `09` 4.3, 4.4 | — |
| Persistence (mapování, defaulty, měkké mazání, transakce commitu) | simulátor / macOS unit test | `ModelConfiguration(isStoredInMemoryOnly:)`, CloudKit `.none` | CloudKit |
| CloudKit kompatibilita schématu | simulátor přihlášený do iCloudu, Development prostředí | `initializeCloudKitSchema()` v DEBUG + kontrola v CloudKit Console (**ověřeno** jako postup, `08` b) | sync mezi zařízeními |
| UI toky (import → kontrola → uložení, test, souhrn, přerušení) | simulátor, XCUITest | composition root s fake `TextRecognizer` (vrací fixture) a fake `SpeechOutput`; launch argument | kameru, skutečné OCR |
| Přežití pádu | simulátor | ukončit proces po N odpovědích, ověřit log a rebuild | — |
| Vision v simulátoru | simulátor | jen „potrubí" (že adaptér vrací DTO). Kvalitu ne (**domněnka**, že výsledky v simulátoru odpovídají zařízení, nespoléhat). | R2 |
| **Jen na zařízení** | iPhone + iPad syna | **U1–U3** (Ad Hoc/Xcode pod dětským iCloudem, prostředí CloudKit, `10`), **T3, T4** (sync, dedup, letadlo), **T5** (hlasy en-GB/de-DE offline), **T6** (OCR jazyky, kvalita, žádná síť), T8 (jen P2) + nové sondy N1–N7. T1, T2 a T7 z `08` odpadají (TestFlight se nepoužívá) | — |

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
| Aktivní jazyk (předvolba) | ne | jen lokálně v zařízení (`UserDefaults`), nesynchronizuje se (ADR-15) | do změny | aplikace | „Smazat vše", odinstalace |
| `ReviewState` | odvozené | jen lokálně | přepočitatelné | aplikace | „Smazat vše". Smaže se i odinstalací. |
| Telemetrie distribuce | ne | — | — | — | Při Ad Hoc distribuci není TestFlight, tedy ani jeho telemetrie. Pády se čtou ze zařízení přes Xcode (**domněnka**) |

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
- **Kontext:** po nasazení do Production nejde nic mazat ani měnit (**ověřeno**, `08` b). Zařízení se aktualizují nesouběžně (Ad Hoc je ruční).
- **Varianty:** (a) `VersionedSchema` + migrace; (b) aditivní pole + `schemaVersion` + tolerantní čtení; (c) nový kontejner při velké změně.
- **Volba:** (b) jako pravidlo, (c) jako nouzová cesta. Lokální `local` store smí migrovat volně.
- **Důsledky:** pole se nepřejmenovávají (staré se „zapomene", přidá se nové). Starší klient needituje záznamy s vyšší verzí. Během vývoje se používá Development prostředí (lze resetovat, **ověřeno**, `10` kap. 6). **Aditivní pravidlo je závazné od okamžiku, kdy se schéma nasadí do Production** (ADR-14), ne dřív.
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
- **Důsledky:** méně informací o chybách z terénu. Spoléháme na zpětnou vazbu otce a lokální log. Bez TestFlightu nejsou k dispozici ani jeho hlášení pádů.
- **Přehodnotit, když:** výslovně rozhodne zadavatel.

### ADR-13 Minimální iOS 26 (D15)
- **Kontext:** obě zařízení mají iOS 26.
- **Důsledky:** k dispozici je `RecognizeDocumentsRequest` a aktuální SwiftData. Není potřeba větev pro starší systém. Riziko: méně zkušeností komunity s novými API, proto sondy N1–N3 cílí právě na iOS 26.
- **Přehodnotit, když:** přibude zařízení se starším iOS (rozhodne zadavatel).

### ADR-14 Distribuce Ad Hoc a prostředí CloudKit (D5, R6)
- **Kontext:** TestFlight se v dětském účtu 13letého stáhne, ale nespustí (ověřeno uživatelem na zařízení, 2026-11-07). Zadavatel zvolil Ad Hoc přes Mac a Xcode. TestFlight a App Store používají jen Production, Ad Hoc export a Xcode Run umožňují Development i Production. Development a Production jsou oddělená úložiště, schéma se kopíruje, data ne (**ověřeno**, `10` kap. 6).
- **Varianty:** (a) vývoj i provoz ve Production; (b) vývoj ve Development, provoz ve Production po jednorázovém přechodu; (c) vše ve Development.
- **Volba:** (b) jako pravidlo. Vývoj a testy v Development (lze resetovat). Skutečná data syna se do aplikace dávají **až po přechodu na Production build**, a od té doby se Development build na tomtéž zařízení nepoužívá. (c) se nedoporučuje: provoz v Development je mimo běžnou cestu a hrozí ztráta dat při pozdějším přechodu na App Store.
- **Důsledky:** přechod Development → Production je studený start cloudu. Před ním se schéma nasadí do Production (ruční krok v CloudKit Console). Co se stane s lokálním SwiftData při změně prostředí na témže zařízení, je **domněnka → sonda U3** (příbuzná N6). Pokud lokální data nepřežijí, nabídne aplikace jednorázový export → import (kap. 5, Q12).
- **Přehodnotit, když:** U2 ukáže, že dětský iCloud nesmí používat Development, nebo se rozhodne distribuovat přes App Store (varianta C v `10`, Production beztak).

### ADR-15 Jazyk jako neměnná vlastnost sady, denormalizovaná do slova, karty a pokusu; aktivní jazyk jako lokální předvolba (D18, D19, D20)
- **Kontext:** D18: jeden aktivní jazyk, jazyky se nemíchají v relaci, frontě, testu ani statistice. Sada má jeden jazyk, slovo a sloveso ho dědí. **Rozhodnuto zadavatelem:** jazyk sady je po vytvoření neměnný a slovo nejde přesunout do sady jiného jazyka (D19); souhrn přes oba jazyky neexistuje, ani v rodičovském pohledu (D20). Neměnnost a absence souhrnu proto nejsou varianty tohoto ADR, ale vstupní podmínky. Model nemá relace (ADR-04) ani omezení (R4). CloudKit doručuje v libovolném pořadí a úpravy jsou LWW (N2). Fronta a statistiky musí filtrovat jazyk v `#Predicate` bez joinů. Slovo smí být ve více sadách (O3).
- **Varianty – kde žije jazyk:** (a) jen na sadě, slovo a pokus ho odvozují přes členství; (b) na sadě a denormalizovaně na slově, slovesu, pokusu, relaci a kartě, po vzniku neměnně, hlídané doménou; (c) jazyk v typu (`EnWord`/`DeWord`, oddělené entity); (d) oddělené úložiště nebo CloudKit zóna na jazyk.
- **Volba:** (b). (a) odpadá: slovo bez sady (po smazání sady, nebo před doručením členství) by jazyk ztratilo, `dedupKey` jazyk potřebuje a každý dotaz by potřeboval spojení přes `SetMembership`, bez relací tedy dva dotazy a slučování v paměti. (c) a (d) zdvojí schéma a kód, přitom stejné oddělení dá filtr. (d) navíc SwiftData zóny neřídí (**domněnka**).
- **Invariant:** pro každé platné členství `word.language == set.language`; `verb.language == set(verb.setID).language`; `attempt.language == item.language`; `reviewState.language == item.language`; `session.language` = jazyk všech `setIDs` a všech jejích pokusů; platný jazyk je jen `en` nebo `de`.
- **Jak ho zajistit:** (1) **Neměnnost:** jazyk sady, slova a slovesa se zapíše jednou při vzniku, v doménových hodnotách je `let` a editace pole nepřenáší. Co se nezapisuje, nemůže mít LWW konflikt. (2) **Zápis jen přes doménu:** `makeWord(in:)`, `makeMembership(word:set:)`, `makeAttempt(item:session:)` převezmou jazyk ze zdroje, nebo selžou s `languageMismatch`. Persistence nemá veřejný setter `language`. (3) **Obranné čtení:** `LanguageGuard` při startu a po syncu vyhodnotí invariant na datech, jak dorazila. Co ho porušuje, se nezapočte nikam a ukáže se jako počet v kontrole dat. Nic se automaticky nepřepisuje (pokus je append-only, u členství rozhodne uživatel). Jediná výjimka: `unknown` u pokusu nebo relace je chybějící hodnota, ne rozpor, a doplní se při čtení z položky. (4) **Zakázané operace (D19):** změna jazyka sady (i prázdné), přesun nebo přidání slova do sady jiného jazyka, sloučení duplicit napříč jazyky. Žádná migrace jazyka sady se nenavrhuje ani nestaví. (5) **Bez souhrnu (D20):** `StatsCalculator` ani repozitář nemají variantu „všechny jazyky", takže souhrn nejde postavit omylem.
- **Konflikty syncu:** souběžné operace mění jen členství (sjednocení), jazyk ne. Přesun mezi sadami stejného jazyka je dvojice záznamů (nové členství + tombstone), takže souběžné přesuny dají slovo ve dvou sadách téhož jazyka, nikdy ve dvou jazycích (kap. 4).
- **Aktivní jazyk – varianty:** (a) lokální předvolba zařízení (`UserDefaults`); (b) synchronizovaná entita `Settings` v CloudKit; (c) `NSUbiquitousKeyValueStore`.
- **Volba:** (a). Na zařízení bez předvolby se použije jazyk nejnovějšího platného pokusu (odvozené ze synchronizovaného logu), bez pokusů se zeptá úvodní obrazovka. Proti (b): přepnutí na iPhonu by syncem přepnulo iPad, i uprostřed testu (D18: uprostřed testu se nepřepíná, volba má být viditelná). Konflikt dvou zařízení by se řešil LWW bez smyslu. Šlo by o novou synchronizovanou entitu, která po nasazení do Production zůstane navždy. Syn může mít na iPadu otevřenou němčinu a na iPhonu angličtinu a nic se tím nerozbije. Proti (c): druhý synchronizační kanál vedle SwiftData s vlastním chováním, vlastním mazáním v „Smazat vše" a vlastní sondou.
- **Důsledky:** pole `language` v sedmi entitách (aditivní a zatím jen v Development, takže zdarma, ADR-06). Repozitářová a doménová API mají `language` jako povinný parametr bez výchozí hodnoty, zapomenutý filtr je chyba při kompilaci. Statistiky mají na jazyk jen část dat, takže stav „málo dat" trvá déle (`07` kap. 5). Omylem zvolený jazyk sady se opraví novou sadou ve správném jazyce a smazáním staré (D19). Historie staré sady odejde s ní do koše. Budoucí rodičovský pohled (ADR-11) dostane data také jen po jazycích (D20). Plánovač ani metriky nepotřebují nic sdíleného mezi jazyky.
- **Přehodnotit, když:** zadavatel změní D19 nebo D20 (data by se neměnila, D20 by znamenal jen dotaz bez filtru; D19 by vyžadoval nový návrh, protože neměnnost je základ invariantu); přibude třetí jazyk (nová hodnota enumu, jinak beze změny); zadavatel chce stejný aktivní jazyk na obou zařízeních (pak (b) jako malá entita); `LanguageGuard` v provozu najde porušení (pak hledat zdroj, ne zmírňovat invariant).

---

## 10. Distribuce a provoz (Ad Hoc)

- **Brána 0 (před vývojem kromě domény):** zařízení syna (iPhone, iPad, iOS 26) se zapojí k Macu s Xcode a zapne se Developer Mode. **U1:** Hello CloudKit (iCloud capability) z Xcode pod dětským iCloudem. **U2:** sync iPhone ↔ iPad v Development i v Production (Ad Hoc export), hlášky a případné chyby účtu se zapíšou. **U3:** co se stane s lokálními daty a cloudem při změně prostředí (Development → Production) na témže zařízení. Výsledek nemění doménový kód, jen provozní postup. Zdroj sond: `10` kap. 10.
- **Registrace a instalace:** UDID synových zařízení do vývojářského účtu (limit 100 zařízení na kategorii a rok, **ověřeno**, `10`), instalace z Macu (Xcode / Apple Configurator, ne OTA). Platnost podpisu Apple na ověřených stránkách neuvádí, běžně se udává 1 rok (**domněnka**), proto se obnova podpisu a přeinstalace plánují do kalendáře otce, nejpozději měsíc před koncem.
- **Prostředí CloudKit (ADR-14):** vývoj v Development. Před předáním skutečné aplikace synovi: `initializeCloudKitSchema()` v DEBUG, kontrola v Console, **Deploy do Production**, Ad Hoc export s Production. Entitlements: iCloud/CloudKit kontejner, Background Modes → Remote notifications (**ověřeno**, `08` b). Od předání se na tomtéž zařízení nepoužívají Development buildy.
- **Build 1 bez syncu** (`04`): už nemusí platit. Pokud se vývoj dělá přes Xcode v Development a U2 projde, bude sync fungovat od začátku. Konfigurace `synced` stále existuje i s `cloudKitDatabase: .none` pro případ, že U2 neprojde. Sonda **N6** (přepnutí `.none` → `.private`) zůstává pro tento případ.
- **Záložní cesty z `10`:** varianta A (TestFlight s dospělým účtem v Média a nákupy, test T-A0, oslabuje rodičovskou kontrolu) a varianta C (nevypsaná aplikace v App Storu, vyžaduje App Review). Zapojí se jen pokud Ad Hoc selže v U1/U2. Obě používají Production, takže ADR-14 platí beze změny.
- **Nové buildy:** aktualizace se do synových zařízení dostanou jen ručně přes Mac. Pro rodinu je to provozní náklad, ne změna architektury.

---

## 11. Předpoklady k ověření sondou (pro `verifier`)

Převzaté z `08`: **T3–T6, T8** (R4: T3, T4; R3: T5, T8; R2+R5: T6). **T1, T2 a T7 odpadají** (TestFlight se nepoužívá, R1 uzavřeno). Nahrazují je sondy **U1–U3** z `10`. R2 je v tomto návrhu **jen předpoklad**, nic na něm nestaví jako na faktu.

Nové sondy z tohoto návrhu:

| # | Předpoklad | Jak ověřit | Dopad, když neplatí |
|---|---|---|---|
| N1 | Jeden `ModelContainer` se dvěma `ModelConfiguration` (`.private` a `.none`) synchronizuje jen první | iOS 26, dvě zařízení, CloudKit Console | dva kontejnery (ADR-02) |
| N2 | `[String]` atributy se synchronizují. Konflikt úprav je LWW (zjistit, zda na úrovni pole, nebo celého záznamu). | součást T4: souběžná offline úprava různých polí téhož slova | při LWW celého záznamu: zvážit `WordRevision` nebo rozdělit pole (Q7) |
| N3 | SwiftData na iOS 26 dá aplikaci signál o změnách přišlých ze syncu (notifikace / history) | prototyp + log | přepočet jen při startu a při návratu do popředí |
| N4 | Tvrdě smazaný záznam se úpravou z offline zařízení neoživí / oživí | součást T4 | délka koše a pravidla tombstonů (ADR-05) |
| N5 | „Smazat vše" spolehlivě odstraní data z CloudKit a lze je smazat i ze systémového Nastavení po odinstalaci | dvě zařízení, CloudKit Console, Nastavení → iCloud → Spravovat úložiště | přidat mazání zóny přes CloudKit API, upravit text v Nastavení |
| N6 | Přepnutí konfigurace z `.none` na `.private` mezi buildy nahraje existující lokální data | build 1 → build 2 na zařízení | jednorázová migrace exportem |
| N7 | Plný `rebuild` pro 50 000 pokusů a UUID dotazy (vč. filtru `language` a `deletedAt`) jsou na iPhonu syna pod 1 s | syntetická data na zařízení, oba jazyky | inkrementální cache statistik, indexy |
| N8 | `RecognizeDocumentsRequest` (iOS 26) dovoluje nastavit jazyky rozpoznání a jejich pořadí. V `supportedRecognitionLanguages` jsou `de`, `cs` a některý anglický kód (`en-US`, `en-GB`). | součást T6: vypsat seznam, spustit stránky s `[de, cs]`, `[en, cs]`, obráceně a bez nápovědy | `RecognizeTextRequest` s jazyky + geometrické párování, nebo `RecognizeDocumentsRequest` bez jazykového kontextu (rozhodne T6) |
| U1 | Xcode Run s iCloud capability proběhne na synově iPhonu a iPadu pod dětským iCloudem (Developer Mode) | Hello CloudKit, `10` kap. 10 | záložní cesta A nebo C |
| U2 | Dětský iCloud smí používat Development i Production prostředí, sync iPhone ↔ iPad funguje | dvě zařízení, CloudKit Console | záložní cesta A nebo C (Production), jinak změna D6 |
| U3 | Při změně prostředí Development → Production na témže zařízení lokální SwiftData data přežijí (a jak se chová cloud) | zařízení, příbuzné N6 | jednorázový export → import (Q12) |

---

## 12. Otevřené body pro zadavatele (nerozhoduji)
- **Q7:** stačí „poslední úprava vyhrává" bez náhledu přepsaného? Jinak přidat `WordRevision` (kap. 4).
- **Ukládat `typedText`** (co syn napsal)? `07` ho chce pro kalibraci, ale je to osobní údaj (kap. 8).
- **Koš 30 dní** a **draft max. 7 dní**: přijatelné lhůty?
- **Vracet při vrácení importu i doplněné překlady** k existujícím slovům (kap. 5)?
- **Q12 export** před smazáním dat. Teď i před přechodem Development → Production (ADR-14).
- **Údržba Ad Hoc:** kdo a jak často obnovuje podpis a přeinstaluje build na synových zařízeních (Mac, kabel, Developer Mode). Doporučení: kalendářní připomínka otce.
- Z `07`: má-li zůstat „první pokus karty v relaci" pravidlem i při souběžných relacích na dvou zařízeních (kap. 4).
- ~~**D18, výběr ze 4 v malé sadě**~~ **Rozhodnuto (D21):** když sada nemá 3 použitelné chybné možnosti, doplní se z jiných sad téhož jazyka, nikdy z druhého jazyka. Méně možností jen tehdy, když nestačí celý jazyk. Pravidlo pro tento okrajový případ navrhuje `07` kap. 3.
- ~~Zda tok importu nabízí i sady druhého jazyka~~ **Rozhodnuto (D22):** jen sady aktivního jazyka.
- **D18, UX:** kde se volí aktivní jazyk na novém zařízení.

---

## 13. Oddělení jazyků (D18, D19, D20, ADR-15)

### 13.1 Kde je jazyk a odkud se bere

| Entita | Role jazyka | Odkud | Mění se? |
|---|---|---|---|
| `WordSet` | **zdroj pravdy** | volba při založení, výchozí = aktivní jazyk | ne (D19) |
| `WordEntry`, `IrregularVerb` | neměnná kopie | ze sady, do které položka vzniká (import, ruční zadání, CSV) | ne (D19) |
| `SetMembership` | bez pole | platné jen při `word.language == set.language` | — |
| `Attempt` | snímek | z položky při zápisu | ne (append-only) |
| `StudySession` | jazyk relace | aktivní jazyk při startu | ne |
| `ImportDraft` (lokální), `ImportBatch` | jazyk importu | ze sady před skenem | ne |
| `ReviewState` (lokální) | kopie pro dotaz | z položky při rebuildu | přepočítá se |
| Aktivní jazyk | lokální předvolba | volba uživatele, výchozí = jazyk nejnovějšího platného pokusu | jen mimo relaci |

### 13.2 Zápis
- Položka vzniká vždy „v sadě": jazyk se zkopíruje ze sady. Bez sady (ruční zadání slovesa) se nabídnou jen sady aktivního jazyka.
- Členství (přidání, přesun, import do existujícího slova) ověří shodu jazyků, jinak `languageMismatch`.
- Pokus převezme jazyk z položky a ověří, že se shoduje s `StudySession.language`. Nesoulad je chyba programu: pokus se nezapíše a chyba jde do lokálního logu.
- Sloučení duplicit (`MergePlanner`) a reimport dedup hledají jen v rámci jazyka.
- Operace „změnit jazyk sady" a „přesunout do jiného jazyka" v doméně neexistují (D19).

### 13.3 Čtení: dotazy, fronta, plánovač, výběr možností
- **Každý dotaz** na obsah, pokusy a stav má filtr `language == L` (+ `deletedAt == nil`, `mergedIntoID == nil`). Repozitář jinou signaturu nemá.
- **Fronta „k procvičení"** = karty jazyka L s `dueAt <= now`. Počty na hlavní obrazovce, odznaky a „hotovo na dnes" platí jen pro L.
- **Plánovač** (`buildSession`, `07` kap. 3) běží na jazyk: strop 30 karet, 8 nových a práh 40 po termínu se počítají v jazyce L. Cvičení jednoho jazyka neposouvá frontu druhého.
- **Slovesa:** vlastní fronta na jazyk (strop 25, 3 nová, `07` kap. 4).
- **Relace:** jazyk se zafixuje při startu, `setIDs` jen z L. Přerušená relace se nabídne jen v jazyce L.
- **Výběr ze 4 (`DistractorPicker`, D21):** potřebuje 3 chybné možnosti. Vstupem je karta a `language`. Kandidáti jsou jen platná slova (nesmazaná, nesloučená, bez porušení `LanguageGuard`) **téhož jazyka** jako karta, jiná než karta sama, a vybírají se v pořadí:
  1. stejná sada jako karta (sada karty, která je v relaci; u fronty přes celý jazyk ty sady, ve kterých slovo je), stejný slovní druh;
  2. stejná sada, jiný slovní druh;
  3. jiné sady téhož jazyka, stejný slovní druh;
  4. jiné sady téhož jazyka, jiný slovní druh.

  Uvnitř každé úrovně u DE podstatných jmen rozhoduje člen podle `07` kap. 3 (stejný, nebo záměrně odlišný, podle toho, co se testuje). Vyloučí se možnosti, které by byly také správné (shodný překlad nebo lemma). Napříč jazyky nikdy, ani jako záloha. Druhý jazyk se do množiny kandidátů vůbec nenačte, protože dotaz má filtr `language`. **Invariant platí dál:** doplnění z jiné sady jen rozšíří množinu kandidátů v rámci jazyka L. Nemění členství, sadu ani jazyk žádného záznamu a pokus dál nese jazyk karty. Když nestačí ani celý jazyk, nabídne se méně možností a zapíše se `choiceCount`. Minimum a postup pod ním navrhuje `07` kap. 3 (návrh: k ≥ 3, jinak psaní s nápovědou). Distraktory sloves (tvary „pravidelně" a „podle jiného vzoru") se tvoří jen ze sloves téhož jazyka.
- **Statistiky, trend, slabá místa, hlídač kalibrace, `desired_retention`:** `StatsCalculator(language:)`, definice a prahy platí na jazyk (`07` kap. 5, 6, 9). Souhrn přes oba jazyky se nepočítá ani nezobrazuje (D20).
- **Hlas a OCR** podle jazyka položky, resp. importu (kap. 5, 6).
- **`LanguageGuard`** (při startu a po syncu): vrátí množinu neplatných záznamů, kterou všechny dotazy vynechají, a počty pro Nastavení → Kontrola dat.

### 13.4 Doménové testy invariantu (`swift test`, bez zařízení)

| # | Test | Očekávání |
|---|---|---|
| L1 | Vytvoření slova v sadě `de`; pokus o změnu jazyka sady nebo slova | `word.language == de`; změna jazyka nemá v doméně operaci, editace pole nepřenáší (D19) |
| L2 | `makeMembership` slova `de` do sady `en` | `languageMismatch`, nic nevznikne |
| L3 | Přesun slova do sady stejného jazyka / jiného jazyka | nové členství + tombstone v jedné operaci / `languageMismatch` a beze změny |
| L4 | `makeAttempt` pro položku `en` v relaci `de` | chyba, pokus nevznikne |
| L5 | `MergePlanner` nad en „Hand", de „Hand", en „kind", de „Kind" | žádný návrh sloučení |
| L6 | `buildSession(language: de)` nad smíšenou množinou karet (vč. karet po termínu v `en`) | jen karty `de`. Strop, nová slova a práh 40 se počítají jen z `de`. |
| L7 | `buildSession` s `setIDs` z obou jazyků | odmítnuto |
| L8 | `DistractorPicker`: sada `de` se 3 slovy + jiná sada `de` s dalšími slovy | 4 možnosti, všechny `de`. Nejdřív 2 ze stejné sady, 1 doplněná z jiné sady `de`, přednostně stejný slovní druh. |
| L8a | `DistractorPicker`: jazyk `de` má celkem jen 3 platná slova (a `en` má stovky) | `choiceCount == 3`, žádná možnost z `en`. Při 2 slovech v jazyce MC nevznikne (pravidlo `07` kap. 3). |
| L8b | `DistractorPicker` nad daty, kde druhý jazyk má shodná lemmata a překlady (en „Hand" × de „Hand"), vlastnostní test přes náhodné velikosti sad a jazyků | v žádném běhu se nedoplní možnost z druhého jazyka. `choiceCount` = min(4, počet platných slov jazyka) a klesne jen tehdy, když nestačí celý jazyk. |
| L9 | `StatsCalculator`: 60 pokusů `en` + 60 `de` v každém 28denním okně | trend „málo dat" pro oba jazyky (ne 120 společně). Slabá kategorie se počítá proti chybovosti téhož jazyka. Výpočet bez jazyka (souhrn) nejde zavolat (D20). |
| L10 | `LanguageGuard`: ze „syncu" přijde členství `de` slova v `en` sadě | členství vyřazeno z fronty, testu, distraktorů i statistik. Počet v kontrole dat = 1. |
| L11 | `LanguageGuard`: pokus s `language == en` k položce `de` / pokus s `unknown` | vyřazen z rebuildu i statistik / započten jako `de` |
| L12 | Pokus a členství dorazí dřív než slovo | nezapočteno v žádném jazyce. Po příchodu slova se ověří a započte. |
| L13 | Vlastnostní test: náhodné sekvence operací (vytvoř, přidej, přesuň, smaž, sluč, odpověz) na dvou simulovaných zařízeních, sjednocení v náhodném pořadí | po sjednocení a `LanguageGuard` platí invariant pro vše viditelné. Každá fronta, relace a statistika obsahuje jeden jazyk. |
| L14 | Import: `PairExtractor` s očekávaným `en` nad stránkou s ä ö ü ß; CSV s řádky `de` při importu do `en` sady | označení „vypadá to na němčinu", nic se nepřepne / řádky `de` jsou chyba s důvodem |
| L15 | Mapování jazyka: `Language` → locale hlasu a `LanguageHint` OCR; výchozí aktivní jazyk bez předvolby | `en` → `en-GB`, `de` → `de-DE`, `unknown` → nepřehrávat; OCR `[cizí, cs]`; výchozí = jazyk nejnovějšího platného pokusu, bez pokusů `nil` (zeptat se) |

Co tím **neověříme:** jak se mirroring skutečně zachová k pořadí a obsahu záznamů (T4) a jak Vision zpracuje jazykový kontext (T6, N8).

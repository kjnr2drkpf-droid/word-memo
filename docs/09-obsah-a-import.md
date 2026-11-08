# 09 – Obsah a import slovíček

Autor: content-curator, stav k 2026-11-07. Navrhuje, nerozhoduje (viz `00-zadani.md`). Značení tvrzení o platformě: **ověřeno (zdroj)** / **domněnka**.
**Revize 2026-11-08 (architekt, D18, D19, D22 opraveno):** jazyk je vlastnost sady, neměnný po založení. Slovo a sloveso ho převezmou ze sady. Jazyk importu volí uživatel jako první krok (předvyplněný aktivním jazykem, jde zvolit i druhý), po zvolení je pevný před skenem a import aktivní jazyk zařízení nemění; OCR (kontext [jazyk, cs]), slovník kontroly, dedup a hlas se řídí jazykem importu, resp. sady. Model a invariant viz `05` ADR-15 a kap. 13.

## Závěr

1. **Záznam slovíčka** je jeden tvar pro EN i DE: `lemma` + `překlady[]` + volitelně člen, množné číslo, věta příkladu, sada. SRS stav je **zvlášť** (oba směry mají každý svůj stav, D2), takže záznam obsahu zůstane čistý a CloudKit-kompatibilní (R4).
2. **Import z fotky** vždy končí **kontrolní obrazovkou**. Nic z OCR se neukládá automaticky. Nejistá, neúplná a duplicitní místa se označí, nikdy se „potichu neopraví“.
3. **Vision umí na zařízení tabulky**, ale jen od iOS 26 (`RecognizeDocumentsRequest`, ověřeno). Starší `VNRecognizeTextRequest` (iOS 13+) vrací jen řádky s polohou, sloupce se pak skládají geometricky.
4. **Podpora češtiny ve Vision není v dokumentaci vypsána** – seznam jazyků dává až běh (`supportedRecognitionLanguages`). Češtinu, ruční písmo a diakritiku proto bereme jako **domněnku**, dokud neproběhne krátký test na reálném zařízení (kap. 3.4).
5. **CSV** (UTF-8, hlavička, `;` i `,` i tab) jde stejnou kontrolní obrazovkou jako fotka. Nepravidelná slovesa mají vlastní záznam a vlastní CSV.
6. **Jazyk patří sadě (D18, D19):** jazyk importu je jazyk cílové sady a je daný **před** snímkem. OCR dostane jazykový kontext (němčina + čeština, nebo angličtina + čeština). Slovník kontroly, dedup a hlas se řídí týmž jazykem. Řádky jiného jazyka se neimportují, jen označí. Zda Vision tyto jazyky (hlavně češtinu) podporuje a zda jde jazyky nastavit u `RecognizeDocumentsRequest`, je **domněnka** (T6, `05` N8).
7. **Licence:** nic z učebnic nebundlovat ani do fixtures. Hotové sady jen P2, jen vlastní nebo s otevřenou licencí a atribucí. Právní tvrzení jsou **domněnka**, nejde o právní radu.

---

## 1. Tvar záznamu slovíčka

Logický model (názvy jsou návrh; architekt je převede do SwiftData s ohledem na R4: bez unikátních atributů, vše volitelné nebo s defaultem, relace volitelné).

### 1.1 `Word` (obsah)

| Pole | Typ | Poznámka |
|---|---|---|
| `id` | UUID | Není `unique` (R4). Duplicity hlídá aplikace, ne databáze. |
| `language` | `en` / `de` | Překlad je vždy do češtiny (D1). **Převzato ze sady při vzniku a pak neměnné** (D18, D19, `05` ADR-15). Slovo smí být jen v sadách svého jazyka, do sady jiného jazyka ho nejde přidat ani přesunout. |
| `kind` | `noun` / `verb` / `adjective` / `adverb` / `phrase` / `other` | Řídí, která pole se zobrazí. |
| `lemma` | String | Cizí slovo **bez členu**, jak je vytištěno (`Tisch`, `to run`, `at home`). |
| `article` | `der` / `die` / `das` / nil | Jen `de` + `noun`. Povinné pro podstatná jména při ukládání (varování, ne blokace). |
| `pluralForm` | String? | Plný tvar bez členu: `Tische`, `Kinder`, `Bücher`. |
| `pluralKind` | `form` / `same` / `none` / `unknown` | `same` = beze změny (`Lehrer`), `none` = bez plurálu (`Milch`), `unknown` = zatím nevyplněno. Odlišuje „nevím“ od „neexistuje“. |
| `translations` | [String], pořadí = důležitost | První = hlavní. Cizí → čeština: správně je kterýkoli. Čeština → cizí: odpověď musí odpovídat `lemma` (+ případně `altLemmas`). |
| `altLemmas` | [String] | Přípustné varianty cizího slova (`colour`/`color`, `Foto`/`Fotografie`). Volitelné. |
| `example` | String? | Věta v cizím jazyce. |
| `exampleTranslation` | String? | Česky. |
| `note` | String? | Krátká poznámka (`sich +4`, `nepočitatelné`, `něco/někoho`). |
| `ipa` | String? | Jen pokud je v zdroji (hranaté závorky). Nepovinné; výslovnost se přehrává hlasem (D9), ne ze záznamu. |
| `setName`, `unit` | String? | Sada (např. „Project 3“) a lekce/kapitola (např. „Unit 2“, volitelně strana). Návrh: sada = štítek, ne vlastník slova (viz otázka O3). Vždy jen sady téhož jazyka (D18). |
| `source` | `photo` / `csv` / `manual` / `builtin` | Původ. |
| `importBatchID` | UUID? | Umožní „vrátit poslední import“. |
| `createdAt`, `updatedAt` | Date | |
| `userEdited` | Bool | Ať pozdější reimport nepřepíše opravy. |

Výslovnost: přehrává se `AVSpeechSynthesizer` ze `lemma` (u podstatných jmen včetně členu, aby syn slyšel i člen). Hlas se řídí jazykem sady, který je díky invariantu i jazykem slova: `de` → `de-DE`, `en` → `en-GB` (D16). Hlas se neukládá jako zvláštní nastavení sady (`05` kap. 2.3, 6). Hlas jiného jazyka se jako náhrada nepoužije (D18).

### 1.2 Co do `Word` **nepatří**

- Stav opakování, skóre, historie pokusů – patří do samostatných entit propojených přes `Word.id` a směr (`foreignToCz` / `czToForeign`). Odpovědnost learning-scientist / architect.
- Původní fotka. Výchozí chování: fotka se po potvrzení importu **smaže** (R5, dětský účet). Případné „ponechat zdroj“ by byla opt-in funkce, nyní mimo rozsah.
- OCR confidence – existuje jen na kontrolní obrazovce.

### 1.3 Příklady

```
Word{ language: de, kind: noun, article: das, lemma: "Kind",
      pluralForm: "Kinder", pluralKind: form,
      translations: ["dítě"], example: "Das Kind spielt im Garten.",
      exampleTranslation: "Dítě si hraje na zahradě.", setName: "Deutsch 1", unit: "Lektion 2" }

Word{ language: en, kind: phrase, lemma: "look forward to",
      translations: ["těšit se na", "očekávat"], note: "+ -ing" }
```

### 1.4 Normalizace a klíč pro duplicity

`dedupKey = language | kind | foldedLemma` (jazyk je první složka, takže stejné lemma ve dvou jazycích, např. en `Hand` × de `Hand` nebo en `kind` × de `Kind`, **není** duplicita a nikdy se nesloučí, D18), kde `foldedLemma` = Unicode NFC, casefold, oříznuté mezery, bez členu a bez úvodního `to ` u anglických sloves. Pro **porovnání** se sklopí diakritika, `ß`→`ss`; pro **zobrazení a ukládání** se nic neskládá. Protože CloudKit neumí unikátní klíče a dvě zařízení mohou vytvořit totéž slovo, je potřeba i pozdější úklid duplicit (sloučení překladů, přepojení SRS stavů) – předat architektovi (**domněnka**, že R4 jinak nejde obejít).

---

## 2. Tok importu z fotky

```
[Zvolit sadu (= jazyk)] → [Snímek / výběr fotky] → [Rozpoznání s jazykem sady] → [Spárování dvojic]
        → [Kontrolní obrazovka] → [Oprava] → [Uložení + smazání fotky]
```

### 2.1 Krok za krokem

| # | Krok | Co se děje | Poznámka |
|---|---|---|---|
| 0 | Volba jazyka, pak sady | Jako **první krok** syn vybere, pro který jazyk slovíčka přidává (D22). Volba je předvyplněná aktivním jazykem, ale jde zvolit i druhý. Pak vybere (nebo založí) sadu **před** snímkem: nabízejí se **sady zvoleného jazyka** a nová sada vznikne ve zvoleném jazyce (její jazyk se po založení nemění, D19). Jazyk importu = jazyk sady (D18). Zvolený jazyk je po celý import pevný a vidět na kontrolní obrazovce; dva jazyky v jednom importu nejdou. Import **nemění aktivní jazyk** zařízení (přepnutí je uprostřed importu zakázáno); po uložení lze nabídnout „Přepnout na … a procvičit“. | Jazyk importu je kontext pro OCR ([zvolený jazyk, cs]), směr sloupců, slovník kontroly a dedup. Sady druhého jazyka se v tomto importu nenabízejí, protože jazyk je pevný (D22, O7). |
| 1 | Snímek | `VNDocumentCameraViewController` (skener stránky po jedné, vrací obrázky stránek; iOS 13+; ověřeno). Alternativně výběr fotky z galerie (otec vkládá fotky, D10). | `DataScannerViewController` (živé OCR, iOS 16+, ověřeno) pro tento účel nepotřebujeme: potřebujeme celou stránku a řádky v kontextu. Může přijít později. |
| 2 | Rozpoznání | Celé na zařízení (R5). iOS 26+: `RecognizeDocumentsRequest` → `document.tables` (řádky × buňky). Starší/bez tabulky: `VNRecognizeTextRequest` (`.accurate`) → řádky s `boundingBox`. **Jazykový kontext podle zvoleného jazyka importu:** `de` → [němčina, čeština], `en` → [angličtina, čeština], cizí jazyk první (pořadí = priorita, **ověřeno** u `VNRecognizeTextRequest`, kap. 3.1). Konkrétní kódy (`de-DE`, `en-US`/`en-GB`, `cs-CZ`) se vyberou ze `supportedRecognitionLanguages` za běhu. | Že je cizí jazyk první lepší pořadí, je **domněnka** (T6 porovná obě). `automaticallyDetectsLanguage` (iOS 16+, ověřeno) zkusit vypnuté, aby Vision neodhadoval třetí jazyk (**domněnka**). Viz kap. 3. |
| 3 | Spárování | **Tabulka:** řádek = dvojice, sloupec 1 = cizí, poslední = česky (třetí sloupec s `[...]` = výslovnost → do `ipa`). **Bez tabulky:** shlukování řádků podle x-souřadnice do sloupců; případně dělení řádku oddělovači ` – `, ` - `, ` = `, tabulátorem. Jazyk sloupce se ověří heuristikou: znaky `ěščřžýůďťň` → čeština; `äöüß` → němčina; navíc `NLLanguageRecognizer` (**domněnka**, že spolehlivě dělí cs/de/en na krátkých slovech – ověřit v testu). Očekávaný cizí jazyk je jazyk sady. Když heuristika najde jiný (`äöüß` v anglické sadě), stránka dostane banner „Vypadá to na němčinu, sada je anglická“. Nic se nepřepne ani nepřesune (D19), uživatel import zruší a začne v sadě správného jazyka. | Přepínač „prohodit sloupce“ pro celou stránku jedním tapem. |
| 4 | Kontrolní obrazovka | Seznam dvojic; u každé: cizí, česky, odznak stavu, miniatura výřezu řádku z fotky (z `boundingBox`) pro porovnání s originálem. | Povinný krok; tlačítko Uložit je vedlejší, dokud existují nevyřešené označené řádky (lze „Uložit i s nevyřešenými = přeskočit“). |
| 5 | Oprava | Úprava v řádku; rozdělit / sloučit řádky; smazat; doplnit člen a plurál; rozdělit překlady `;`/`,` do `translations`. | Bez automatických oprav a s vypnutým autocorrect v polích. Lišta se znaky `ä ö ü ß` (u německé sady) a `ě š č ř ž ý á í é ů ú ď ť ň`. |
| 6 | Uložení | Jedna transakce, `importBatchID`, zobrazí se shrnutí („23 nových, 2 přidané překlady, 1 přeskočeno“) a **Vrátit import**. Fotka se smaže. Nabídka „Procvičit teď“ (v aktivním jazyce), nebo při importu do neaktivního jazyka „Přepnout na … a procvičit“ (D22). | Žádné odesílání ven (R5). |

### 2.2 Chyby OCR

Zásada: **označit, ne opravit.** Kontrolní obrazovka je jediné místo, kde se text mění.

| Problém | Typický příznak | Zacházení |
|---|---|---|
| Chybějící/špatná diakritika | `ucitel`, `uß`, `ii` místo `ü`, `a` místo `ä` | Slovo se ověří slovníkem jazyka sady (`UITextChecker` pro `en`/`de`; pro `cs` – **domněnka**, ověřit `availableLanguages`). Neznámé slovo = odznak „ověřit“. Žádná automatická oprava. |
| Záměna znaků | `l`/`I`/`1`, `0`/`O`, `rn`/`m`, `ß`/`B` | Jen odznak „ověřit“ při nízké `confidence` (práh např. 0,5 je **počáteční odhad**, nastavit po testu na reálných fotkách). |
| Spojená / rozdělená slova | `look forwardto`, `Haus aufgabe` | Označit řádek, uživatel upraví. |
| Ruční písmo | nízká `confidence`, mnoho „ověřit“ | Banner „Rukopis se čte hůř, zkontroluj pozorně“. Kvalita je **domněnka**, dokumentace ji neslibuje. |
| Chybná jazyková korekce | Vision „opraví“ cizí slovo na jiné | `usesLanguageCorrection` zkusit v testu **zapnuto i vypnuto** (u slovíček může škodit; **domněnka**, rozhodne test). |

### 2.3 Nejednoznačné řádky

| Případ | Zacházení |
|---|---|
| Jen jedna strana (zalomený řádek, chybí překlad) | Stav „neúplný“; nabídka „připojit k předchozímu / následujícímu“. |
| Zalomený překlad na dva řádky | Pokud druhý řádek nemá protějšek a je odsazený, navrhnout sloučení (jen návrh). |
| Více významů (`1. … 2. …`, `;`, `/`) | Rozdělit do `translations`, pořadí zachovat; kontrola uživatelem. |
| Člen a plurál v jednom poli (`der Tisch, -e`, `das Kind, -er`, `die Frau, -en`) | Parser vytáhne `article` a **příponu plurálu**; pokud je zadána jen přípona/značka (`-e`, `¨-e`, `-n`), spočítá se `pluralForm` jen jako **návrh** označený „ověřit“ (umlaut je nespolehlivě čitelný). |
| Zkratky `sth.`, `sb.`, `etw.`, `jdn.`, `(sich)` | Zůstávají v `lemma`/`note`; neodstraňovat. |
| Nadpisy, čísla stran, popisky obrázků, výslovnost `[...]` | Heuristika (jeden sloupec, velká písmena, číslo) → řádek označen „nejde o slovíčko?“ a výchozí stav „vyřadit“. Výslovnost → `ipa`. |
| Sloupce v opačném pořadí než čekáme | Detekce jazyka sloupců; pokud nesedí, banner „Vypadá to, že sloupce jsou prohozené“ + tlačítko. |

### 2.4 Duplicity

Hlídají se **uvnitř dávky** i **proti knihovně téhož jazyka** podle `dedupKey` (kap. 1.4, D18).

| Situace | Výchozí akce | Volby |
|---|---|---|
| Stejné slovo i stejné překlady | Přeskočit (zobrazit šedě). | Přidat jako nové / přeskočit |
| Stejné slovo, **jiný** překlad | Přidat překlad ke stávajícímu záznamu (neztratí se SRS stav). | Nový záznam / přeskočit |
| Stejné slovo v jiné sadě téhož jazyka | Slovo se nezakládá znovu; přidat do dané sady (štítek). | Samostatná kopie |
| Stejné lemma v jiném jazyce (en `Hand` × de `Hand`) | Nejde o duplicitu, nic se nehlásí (D18). | – |
| Stejný lemma, jiný `kind` (`Lauf` × `laufen`) | Nejde o duplicitu. | – |
| Ruční úprava existujícího (`userEdited`) | Nikdy nepřepisovat. | – |

---

## 3. Apple Vision / VisionKit – co je ověřeno

Zdroje: Apple Developer Documentation (strojově čitelné `.md` verze stránek) a WWDC25 session 272, čteno 2026-11-07.

### 3.1 Ověřeno

| Tvrzení | Zdroj |
|---|---|
| `VNRecognizeTextRequest`: iOS 13+; `recognitionLanguages` = pole ISO kódů v pořadí priority; `usesLanguageCorrection`; `customWords`; `recognitionLevel` (`fast`/`accurate`); `minimumTextHeight`; `automaticallyDetectsLanguage` od iOS 16. | developer.apple.com/documentation/vision/vnrecognizetextrequest |
| Dostupnost jazyků **závisí na úrovni a revizi** („language supported in one level may not be available in another“). Seznam konkrétních jazyků dokumentace **nevypisuje**, zjišťuje se za běhu (`supportedRecognitionLanguages()`). Třídní metoda `supportedRecognitionLanguages(for:revision:)` je podle metadat dokumentace jen pro iOS 13–15, použít instanční. | tamtéž |
| `RecognizeDocumentsRequest` (Swift struct): **iOS 26+**; čte řádky, odstavce, **tabulky** a seznamy; dokumentace zmiňuje „textbook pages“. Tabulka je mřížka buněk (řádek/sloupec), buňka má `content.text.transcript`, `boundingRegion`; vlastnost `supportedRecognitionLanguages` existuje. Ukázkový kód: „tables have at most one line per cell, and no cells that span multiple rows or columns“ (platí pro export v ukázce). | developer.apple.com/documentation/vision/recognizedocumentsrequest, …/recognize-tables-within-a-document |
| WWDC25/272: požadavek umí text v **26 jazycích**; přístup po slovech není u zh/ja/ko/th; Vision API běží celé na zařízení. (Shrnuto z přepisu; **seznam těch 26 jazyků jsem nezjistil**.) | developer.apple.com/videos/play/wwdc2025/272 |
| `VNDocumentCameraViewController` (VisionKit): iOS 13+, skenuje stránku po stránce, výsledek = obrázky stránek; `isSupported`. Sám OCR nedělá. | developer.apple.com/documentation/visionkit/vndocumentcameraviewcontroller |
| `DataScannerViewController`: iOS 16+, živé video, `isSupported`/`isAvailable`, `supportedTextRecognitionLanguages`, `capturePhoto()`, potřebuje `NSCameraUsageDescription`. | developer.apple.com/documentation/visionkit/datascannerviewcontroller |

### 3.2 Domněnka (neověřeno)

- **Čeština (`cs-CZ`) je v seznamu podporovaných jazyků.** Dokumentace seznam nevypisuje. Sekundární zdroje o Live Text jsou nejednotné (češtinu starší zdroj nelistuje). Němčina a angličtina jsou podle všech dostupných zdrojů podporovány (rovněž **domněnka**, dokud nejde seznam z běhu).
- Úspěšnost čtení diakritiky `ě š č ř ž ý ů ť ď ň` a `ä ö ü ß`, ručního písma a dvousloupcových tabulek bez linek.
- Detekce tabulky u fotek slovníčků bez čar (jen odsazení).
- Požadavek A12/Neural Engine pro `DataScannerViewController` (z paměti; jen pro zvážení, na import fotkou nemá vliv).
- `NLLanguageRecognizer` a `UITextChecker` pro češtinu.

### 3.3 Dopad na návrh

- Pokud `cs-CZ` v seznamu **není**: OCR spustit s `[de-DE]` / `[en-US]` bez češtiny; české sloupce budou horší, což kontrolní obrazovka musí unést (vyšší podíl „ověřit“ u českého sloupce). Případně druhý průchod bez jazykových nápověd.
- **Jazykový kontext (D18):** OCR se vždy volá s dvojicí [cizí jazyk sady, čeština]. Že jazyky jde nastavit i u `RecognizeDocumentsRequest` (ne jen přečíst `supportedRecognitionLanguages`), dokumentace podle kap. 3.1 nedokládá: **domněnka** (`05` N8). Pokud nejde, rozhodne T6 mezi `RecognizeDocumentsRequest` bez nápovědy a `VNRecognizeTextRequest`/`RecognizeTextRequest` s jazyky a geometrickým párováním.
- Minimální verze iOS: pokud lze zvolit iOS 26+, lze spoléhat na `RecognizeDocumentsRequest` (tabulky). Jinak je nutná geometrická záloha (kap. 2.1, krok 3). **Rozhodnutí o minimální verzi patří Pavlovi/architektovi** (otázka O1).

### 3.4 Navržený krátký test (spike, předat prototyperovi a test-authorovi)

1. Na reálném iPhonu vypsat `VNRecognizeTextRequest().supportedRecognitionLanguages()` a `RecognizeDocumentsRequest().supportedRecognitionLanguages` – zjistit, zda je v nich `cs`, `de`, `en` (a který anglický kód: `en-US`, `en-GB`) a zda jde u `RecognizeDocumentsRequest` jazyky nastavit (`05` N8).
   1a. Každou stránku spustit s kontextem jazyka sady ([de, cs] / [en, cs]), v obou pořadích a pro srovnání bez nápovědy; měřit jako v bodě 3.
2. 10 fotek vlastních (vymyšlených) tabulek: tištěné EN, tištěné DE, ručně psané; s linkami i bez; 2 a 3 sloupce; šikmé světlo.
3. Měřit: podíl správně přečtených dvojic, podíl označených „ověřit“, kolik chyb je neoznačených (to je nejhorší kategorie).
4. Cíl pro P0 (**návrh k odsouhlasení, ne měřený údaj**): tištěné tabulky ≥ 90 % dvojic bez ruční opravy a prakticky žádné neoznačené chyby v diakritice.

Fixtures: **vlastní vymyšlená slovíčka**, ne stránky z učebnic (viz kap. 7).

---

## 4. Import z CSV (P1)

### 4.1 Pravidla

- Kódování UTF-8 (BOM tolerovat). Oddělovač se rozpozná automaticky: `,` `;` tab (Excel v české lokalizaci ukládá `;`). Uvozovky podle RFC 4180.
- **Hlavička je povinná**, názvy sloupců anglicky, pořadí libovolné. Neznámé sloupce se ignorují (s upozorněním).
- Více překladů v jedné buňce odděluje `|`.
- Povinné jsou jen `term` a `translations`. Zbytek volitelný.
- **Jazyk (D18, D22):** jeden import = jeden jazyk. Jazyk uživatel zvolí **jako první krok importu** (předvyplněný aktivním jazykem, jde zvolit i druhý), sada se nabízí v tomto jazyce a CSV import aktivní jazyk nemění. Sloupec `language`, je-li vyplněn, se jen kontroluje: řádek s jiným jazykem je „chyba: jiný jazyk než sada“ a neimportuje se. Sloupec `set` smí jmenovat jen sady téhož jazyka (nová sada dostane jazyk importu, existující sada jiného jazyka = chyba řádku). Soubor s oběma jazyky se importuje dvakrát, jednou pro každý jazyk.
- Chybné řádky nezastaví import; jdou na kontrolní obrazovku jako „chyba“ s důvodem a číslem řádku. Ani CSV se neukládá bez kontroly.
- Duplicity jako v kap. 2.4.
- Limit rozumné velikosti (např. 2 000 řádků na soubor – **návrh**).

### 4.2 Sloupce slovíček

| Sloupec | Povinné | Popis |
|---|---|---|
| `language` | ne | `en` / `de`; jen kontrola proti jazyku importu (D18) |
| `set` | ne | název sady téhož jazyka |
| `unit` | ne | lekce |
| `kind` | ne | `noun` `verb` `adjective` `adverb` `phrase` `other` (výchozí `other`; u `de` při vyplněném `article` = `noun`) |
| `term` | **ano** | cizí slovo bez členu |
| `article` | ne | `der` / `die` / `das` |
| `plural` | ne | plný tvar bez členu, nebo `=` (beze změny), `-` (bez plurálu) |
| `translations` | **ano** | česky, více překladů přes `\|` |
| `example` | ne | věta v cizím jazyce |
| `example_cs` | ne | překlad věty |
| `note` | ne | poznámka |

### 4.3 Příklad (`;` jako oddělovač, vymyšlená data)

Jeden soubor = jeden jazyk (D18). Němčina:

```csv
language;set;unit;kind;term;article;plural;translations;example;example_cs;note
de;Deutsch 1;Lektion 2;noun;Kind;das;Kinder;dítě;Das Kind spielt im Garten.;Dítě si hraje na zahradě.;
de;Deutsch 1;Lektion 2;noun;Lehrer;der;=;učitel|učitel (muž);Mein Lehrer ist nett.;Můj učitel je milý.;
de;Deutsch 1;Lektion 2;noun;Milch;die;-;mléko;;;nepočitatelné
```

Angličtina (samostatný import):

```csv
language;set;unit;kind;term;article;plural;translations;example;example_cs;note
en;Project 3;Unit 1;verb;run;;;běhat|běžet;I run every morning.;Každé ráno běhám.;
en;Project 3;Unit 1;phrase;look forward to;;;těšit se na;I look forward to the holiday.;Těším se na prázdniny.;+ -ing
```

Minimální varianta (dvousloupcová, jazyk a sada se zvolí v dialogu):

```csv
term,translations
apple,jablko
"to take care of",starat se o|pečovat o
```

### 4.4 CSV nepravidelných sloves

Také jeden soubor = jeden jazyk (D18), pravidla sloupce `language` jako v kap. 4.1.

```csv
language,infinitive,present_3sg,past,participle,auxiliary,translations,note
en,go,,went,gone|been,,jít|jet,
en,learn,,learnt|learned,learnt|learned,,učit se,
```

```csv
language,infinitive,present_3sg,past,participle,auxiliary,translations,note
de,fahren,fährt,fuhr,gefahren,sein,jet|jezdit,
de,aufstehen,steht auf,stand auf,aufgestanden,sein,vstávat,odlučitelné: auf|stehen
```

Pro `en` je `infinitive` = základní tvar; `present_3sg` a `auxiliary` se nepoužívají. Pro `de` je `participle` bez pomocného slovesa, to je zvlášť v `auxiliary` (`haben` / `sein` / `haben|sein`). Varianty přes `|`.

---

## 5. Nepravidelná slovesa (P1, D8: zadávají se ručně)

### 5.1 Záznam `IrregularVerb`

| Pole | EN | DE |
|---|---|---|
| `id`, `language`, `translations[]` (česky), `setName`, `note`, `source`, `createdAt` | ano | ano |
| `infinitive` | základní tvar (`go`) | infinitiv (`fahren`) |
| `present3sg` | – | 3. os. sg. přítomného času (`fährt`; u odlučitelných `steht auf`) |
| `past` | [String] minulý čas (`went`; více variant `learnt`/`learned`) | [String] préteritum 3. os. sg. (`fuhr`) |
| `participle` | [String] příčestí (`gone`/`been`) | [String] Partizip II bez pomocného slovesa (`gefahren`) |
| `auxiliary` | – | `haben` / `sein` / `both` |
| `separablePrefix` | – | String? (`auf`) – tvoří se z infinitivu, slouží ke správnému zobrazení a kontrole |
| `kind` | `strong` / `modal` / `mixed` / `other` – volitelný štítek pro třídění (`modal`: `können`, `müssen`…) | |

`language` se bere ze sady slovesa a po vzniku se nemění. Sloveso je jen v sadě svého jazyka (D18, D19).

Zobrazení němčiny: `fahren – fährt – fuhr – ist gefahren`. Perfekt se skládá z `auxiliary` + `participle`; u `both` se přijme jakýkoli.

Proč pole po slotech: každý tvar (`past`, `participle`, u DE i `present3sg` a `auxiliary`) je samostatná testovatelná položka, takže plánovač vidí, že syn zná `went`, ale ne `gone`. Podrobnosti hodnocení patří do learning-scientist.

Varianty: `learned`/`learnt`, `got`/`gotten` (US), `was`/`were` (u `be` dvě platné formy minulého času: `I was`, `we were` → pole `past` = [`was`, `were`]). `read` se píše stejně, liší se výslovnost → `note` + přehrání hlasem.

### 5.2 UX ručního zadání

1. **Formulář:** jazyk je daný aktivním jazykem a zvolenou sadou a ve formuláři se jen zobrazuje, nepřepíná (D18) → první pole `infinitive` (autofokus) → další pole podle jazyka → česky (`translations`) → Uložit. Pole se pohybují klávesou Další.
2. **Rychlé zadání jednou řádkou** (nejčastější cesta, syn píše z sešitu): `go – went – gone` nebo `fahren – fährt – fuhr – ist gefahren`. Oddělovače `–`, `-`, `/`, mezera-čárka. Parser rozdělí a ukáže náhled do polí; chybějící pole zůstanou prázdná.
3. **Vložení více řádků** (otec zkopíruje seznam) → seznam náhledů na stejné kontrolní obrazovce jako u importu (kap. 2). Totéž pro CSV (kap. 4.4).
4. **Varianty:** malé „+ varianta“ u tvaru; ne víc políček najednou.
5. **Němčina:** přepínač pomocného slovesa `haben | sein | obojí`; u odlučitelných slovesa přepínač „odlučitelné“ a předpona se nabídne podle začátku (`auf|stehen`); lišta `ä ö ü ß`.
6. **Validace (varování, ne blokace):** prázdný tvar; DE perfekt bez pomocného slovesa; EN příčestí shodné s minulým (OK, např. `bought`, ale zobrazit jen jako informaci); stejný infinitiv už existuje → nabídnout úpravu.
7. Nepovinné: po uložení nabídnout „přidat další“ a zůstat ve formuláři (rychlé zadání celé stránky).
8. Bez automatického doplňování tvarů z vestavěné tabulky v první verzi (D8: ručně; viz otázka O4).

### 5.3 Seznam typických nepravidelných sloves pro 7. třídu – **pouze pomůcka pro kontrolu**

Do aplikace se **nebundluje**. Slovesné tvary jsou fakta, výběr a pořadí z konkrétní učebnice by ale chráněné být mohly, proto je seznam sestavený nezávisle a slouží kontrole správnosti dat (test-author, verifier) a případně jako vzor pro vlastní fixtures. Rozsah „typické pro 7. třídu“ je **domněnka** (ŠVP se liší učebnice od učebnice; u němčiny jako druhého jazyka bývá v 7. třídě teprve začátek, takže zde se čeká spíš `sein`, `haben`, modální a pár základních).

**Angličtina (základní – minulý – příčestí)**

| | | |
|---|---|---|
| be – was/were – been | become – became – become | begin – began – begun |
| break – broke – broken | bring – brought – brought | build – built – built |
| buy – bought – bought | catch – caught – caught | choose – chose – chosen |
| come – came – come | cost – cost – cost | cut – cut – cut |
| do – did – done | draw – drew – drawn | drink – drank – drunk |
| drive – drove – driven | eat – ate – eaten | fall – fell – fallen |
| feel – felt – felt | find – found – found | fly – flew – flown |
| forget – forgot – forgotten | get – got – got (US gotten) | give – gave – given |
| go – went – gone/been | grow – grew – grown | have – had – had |
| hear – heard – heard | hide – hid – hidden | hit – hit – hit |
| hold – held – held | keep – kept – kept | know – knew – known |
| learn – learnt/learned – learnt/learned | leave – left – left | lend – lent – lent |
| let – let – let | lose – lost – lost | make – made – made |
| mean – meant – meant | meet – met – met | pay – paid – paid |
| put – put – put | read – read – read (výsl. /red/) | ride – rode – ridden |
| ring – rang – rung | run – ran – run | say – said – said |
| see – saw – seen | sell – sold – sold | send – sent – sent |
| sing – sang – sung | sit – sat – sat | sleep – slept – slept |
| speak – spoke – spoken | spend – spent – spent | stand – stood – stood |
| swim – swam – swum | take – took – taken | teach – taught – taught |
| tell – told – told | think – thought – thought | throw – threw – thrown |
| understand – understood – understood | wake – woke – woken | wear – wore – worn |
| win – won – won | write – wrote – written | |

**Němčina (infinitiv – 3. os. přítomný – préteritum – perfekt)**

| | |
|---|---|
| sein – ist – war – ist gewesen | haben – hat – hatte – hat gehabt |
| werden – wird – wurde – ist geworden | wissen – weiß – wusste – hat gewusst |
| können – kann – konnte – hat gekonnt | müssen – muss – musste – hat gemusst |
| wollen – will – wollte – hat gewollt | dürfen – darf – durfte – hat gedurft |
| sollen – soll – sollte – hat gesollt | mögen – mag – mochte – hat gemocht |
| gehen – geht – ging – ist gegangen | kommen – kommt – kam – ist gekommen |
| fahren – fährt – fuhr – ist gefahren | laufen – läuft – lief – ist gelaufen |
| fliegen – fliegt – flog – ist geflogen | schwimmen – schwimmt – schwamm – ist geschwommen |
| bleiben – bleibt – blieb – ist geblieben | aufstehen – steht auf – stand auf – ist aufgestanden |
| essen – isst – aß – hat gegessen | trinken – trinkt – trank – hat getrunken |
| geben – gibt – gab – hat gegeben | nehmen – nimmt – nahm – hat genommen |
| sehen – sieht – sah – hat gesehen | fernsehen – sieht fern – sah fern – hat ferngesehen |
| lesen – liest – las – hat gelesen | schreiben – schreibt – schrieb – hat geschrieben |
| sprechen – spricht – sprach – hat gesprochen | helfen – hilft – half – hat geholfen |
| schlafen – schläft – schlief – hat geschlafen | finden – findet – fand – hat gefunden |
| stehen – steht – stand – hat gestanden | sitzen – sitzt – saß – hat gesessen |
| liegen – liegt – lag – hat gelegen | treffen – trifft – traf – hat getroffen |
| bringen – bringt – brachte – hat gebracht | denken – denkt – dachte – hat gedacht |
| kennen – kennt – kannte – hat gekannt | heißen – heißt – hieß – hat geheißen |
| rufen – ruft – rief – hat gerufen | singen – singt – sang – hat gesungen |
| tragen – trägt – trug – hat getragen | waschen – wäscht – wusch – hat gewaschen |
| anfangen – fängt an – fing an – hat angefangen | beginnen – beginnt – begann – hat begonnen |
| einladen – lädt ein – lud ein – hat eingeladen | vergessen – vergisst – vergaß – hat vergessen |
| verstehen – versteht – verstand – hat verstanden | gefallen – gefällt – gefiel – hat gefallen |
| lassen – lässt – ließ – hat gelassen | tun – tut – tat – hat getan |

Poznámky ke kontrole: `stehen`/`sitzen`/`liegen` mají v jižním Německu/Rakousku/Švýcarsku perfekt s `sein`, standardní učebnicový tvar je `haben`; `schwimmen` může mít `hat`/`ist` podle významu. To by měl test-author brát jako `auxiliary = both` nebo varianta. Obsah seznamu je z mé znalosti, **nebyl v této session ověřen proti slovníku** (viz kap. 8).

---

## 6. Doporučené chování aplikace k obsahu (shrnutí návrhu)

- Vše, co vstupuje (foto, CSV, vložený text, ruční zadání), prochází **jednou kontrolní obrazovkou** a **jednou dedup logikou**.
- Import lze **vrátit** jako celek; úpravy uživatele nikdy nepřepisovat.
- Žádné odesílání obrázků ani textu ven (R5). Žádná analytika.
- Hlas a přehrání: nad `lemma` (u DE podstatných jmen včetně členu), ne nad uloženým zvukem.
- Vše po jazycích (D18): sada, dedup, jazykový kontext OCR, slovník kontroly, hlas. Slovo nejde přesunout do sady jiného jazyka a jazyk sady se po založení nemění (D19).

---

## 7. Licence a doporučení k hotovým sadám

Právní stránku považujte za **domněnku**, není to právní poradenství. Rozhodnutí o rozsahu patří Pavlovi (P2).

| Zdroj | Licence / stav | Jistota | Doporučení |
|---|---|---|---|
| Fotka stránky z učebnice/sešitu, kterou si syn naskenuje pro sebe | Texty učebnic jsou chráněné autorským právem (zákon č. 121/2000 Sb.), u slovníčků i právem pořizovatele databáze. Osobní potřeba (§ 30) pravděpodobně pokrývá soukromé užití vlastní kopie. | domněnka | Povolit: data zůstávají v zařízení a v soukromé iCloud databázi vlastníka, nic se nesdílí, nepublikuje ani nepřenáší do jiné sady. |
| Sdílení slovíček z učebnice mezi spolužáky, export, veřejné sady | Rozmnožování a zpřístupňování chráněného díla | domněnka (silná) | **Nenavrhovat.** Žádný „export sady“ pro cizí osoby, žádný veřejný katalog. |
| Slovníčky/seznamy z učebnic (Oxford, Klett, Fraus, Taktik, Prodos aj.), webové zdroje typu Quizlet | Chráněné / podmínky služby zakazují hromadné přebírání | domněnka | **Nebundlovat, nescrapovat, nezahrnout do buildu ani do fixtures a testů.** |
| Tvary nepravidelných sloves | Jednotlivé tvary jsou fakta; chráněné může být konkrétní uspořádání/výběr/překlady v cizí knize | domněnka | Vlastní seznam z kap. 5.3 slouží jen ke kontrole. Pokud by se bundlovala malá kontrolní tabulka (O4), sestavit ji nezávisle a bez českých překladů z učebnic. |
| Wiktionary (text) | CC BY-SA (a GFDL) – s atribucí a share-alike | z paměti, **neověřeno** (hostitel byl v session blokován) | Kandidát pro P2. Pozor na share-alike u odvozené databáze a na kvalitu českých překladů. |
| Tatoeba (věty) | většinou CC BY 2.0 FR, část CC0 | z paměti, **neověřeno** | Kandidát na věty příkladů (P2), nutná atribuce; české překlady kontrolovat. |
| Wikidata | CC0 | z paměti, **neověřeno** | Možný zdroj lemmat, ne překladů do češtiny ve spolehlivé kvalitě. |
| Vlastní sady (psal Pavel nebo syn) | Vlastní autorství | jisté | Preferovaná cesta pro hotové sady i fixtures: CSV z kap. 4. |
| TTS hlasy (`AVSpeechSynthesizer`) | Systémová funkce, jen přehrání | domněnka | Neukládat ani neexportovat zvuk. Kvalita/offline = R3, jiný dokument. |

**Doporučení:**
1. P0/P1: žádný bundlovaný obsah. Syn si slovíčka přináší sám (foto, CSV, ručně).
2. P2 „hotové sady“: jen (a) vlastní, (b) s otevřenou licencí s atribucí na obrazovce „O aplikaci“, (c) s písemným souhlasem nakladatele. Každá sada nese metadata `licence`, `zdroj`, `atribuce`. U nejistých se neuvolňuje do buildu.
3. Fixtures a testovací fotky: **vymyšlený obsah**, ne scany učebnic (test-author, ux-verifier).
4. Distribuce Ad Hoc mimo App Store (D5) licenční riziko nesnižuje: bundlovaný obsah by se šířil dál.

---

## 8. Předpoklady a otevřené otázky

| # | Otázka / předpoklad | Kdo rozhoduje |
|---|---|---|
| O1 | Minimální verze iOS. iOS 26+ = tabulky z `RecognizeDocumentsRequest`, jinak geometrická záloha. | Pavel + architect |
| O2 | Předpoklad: fotka se po importu maže, nic se neukládá ven. Ponechání zdroje = opt-in později. | Pavel |
| O3 | Předpoklad: slovo je jedno, sady jsou štítky (jedno slovo = jeden SRS stav, i když je ve více sadách). Alternativa: kopie na sadu. | architect + learning-scientist |
| O4 | Zda aplikace vnitřně drží malou kontrolní tabulku nepravidelných sloves (varování o překlepu), nebo vše ručně (D8). | Pavel |
| O5 | Výchozí varianta angličtiny pro hlas: `en-GB` (předpoklad). | Pavel / syn |
| O6 | Výsledek testu z kap. 3.4 rozhodne o: `usesLanguageCorrection`, podpoře češtiny v OCR, prahu „ověřit“, pořadí jazyků a o tom, zda jde u `RecognizeDocumentsRequest` jazyk nastavit. | prototyper |
| O7 | ~~Výběr sady v toku importu~~ **Rozhodnuto (D22, 2026-11-08, opraveno):** jazyk importu volí uživatel jako první krok (předvyplněný aktivním jazykem, jde zvolit i druhý); nabízejí se sady zvoleného jazyka, nová sada vznikne ve zvoleném jazyce, OCR dostane [zvolený jazyk, cs]. Import nemění aktivní jazyk zařízení. Dřívější znění „jen sady aktivního jazyka“ bylo mylný výklad. | Pavel (rozhodl) |

**Co v této session nešlo ověřit:** weby `tatoeba.org`, `en.wiktionary.org` a `www.apple.com` jsou mimo povolenou síť session, proto licence těchto zdrojů a stránka Apple „feature availability“ (jazyky Live Text) nejsou ověřeny. Zdrojem pro Vision je `developer.apple.com`.

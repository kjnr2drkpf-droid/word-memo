# Zadání a rozhodnutí (zdroj pravdy pro všechny agenty)

Stav k 2026-11-07. Změny zadání dělá jen zadavatel (Pavel). Agenti navrhují, nerozhodují o rozsahu.

## Problém
Syn (7. třída ZŠ, češtinou mluvící) se učí angličtinu a němčinu. Špatně si pamatuje slovíčka. Potřebuje aplikaci, která měří, jak dobře se mu slovo vybavilo, a podle toho plánuje opakování: co se vybaví hned, se připomíná později, co se nevybaví, častěji.

## Rozhodnutí zadavatele (ověřená)
| # | Rozhodnutí |
|---|---|
| D1 | Jazyky: angličtina a němčina. Všechny překlady jsou do češtiny. |
| D2 | Testování **oběma směry** (cizí → čeština i čeština → cizí). |
| D3 | Způsob odpovídání: **mix podle znalosti** — nová slova výběrem z možností, známá psaním. |
| D4 | Po každém pokusu **souhrn**; dlouhodobě **historie pokroku** (zlepšuje se / nezlepšuje se, kde jsou slabiny). |
| D5 | Platforma iOS (iPhone **i iPad**). Distribuce **Ad Hoc přes Mac a Xcode** (UDID synových zařízení). ~~Původně jen TestFlight~~ — změněno 2026-11-07, protože TestFlight 13letý dětský účet nespustí (ověřeno uživatelem na zařízení). Vývojářský účet je k dispozici. Podrobnosti `docs/10-distribuce-bez-testflightu.md`. |
| D6 | Výsledky se **synchronizují přes iCloud** mezi zařízeními. |
| D7 | Slovíčka se do aplikace dostávají **fotkou stránky ze sešitu/učebnice** (OCR, kontrola a oprava). Doplňkově import (např. CSV). Hotové sady jsou volitelné a jen pokud je licence v pořádku. |
| D8 | Nepravidelná slovesa: samostatný způsob procvičování, zadávají se **ručně**. |
| D9 | **Výslovnost:** přehrání správné výslovnosti hlasem. Kontrola výslovnosti syna (rozpoznání řeči) je otevřená otázka, jde o samostatnou, pozdější funkci. |
| D10 | Syn má **vlastní Apple ID v rodině (Family Sharing)**, dětský účet. Slovíčka importuje z jeho zařízení, občas fotky vloží otec. |
| D11 | Rodičovský pohled na pokrok **zatím ne**, ale architektura ho nesmí vylučovat. |
| D12 | Syn nemá dyslexii ani dysgrafii. |
| D13 | Gamifikace je přání, **ne priorita**. |
| D14 | Původní prostor: nový repozitář `word-memo`. Repozitář `KnihaJizd` (kniha jízd EV) s tímto projektem nesouvisí. |
| D15 | Syn je **13 let**. iPhone i iPad mají **iOS 26** → minimální cílová verze **iOS 26**. |
| D16 | Angličtina je **britská (en-GB)** — hlas výslovnosti i slovník. |
| D17 | **(potvrzeno 2026-11-07)** Fotky vkládá otec **na synově zařízení**, ne ze svého Apple ID (SwiftData sync je jen v rámci jednoho iCloud účtu). |
| D18 | **Jazyky se nemíchají (zadavatel, 2026-11-08).** Aplikace má vždy jeden **aktivní jazyk** (angličtina nebo němčina). Uživatel procvičuje jedno, nebo druhé; nikdy ne obojí v jedné relaci, frontě, testu ani statistice. Jazyk je vlastnost sady (sada nemůže obsahovat oba jazyky), slovo a sloveso ho dědí. Plánování opakování, nepravidelná slovesa, výslovnost (hlas), import (jazyk OCR) i pokrok a slabá místa jsou **po jazycích odděleně**. Přepnutí jazyka je viditelná, jednoznačná volba na nejvyšší úrovni; uprostřed testu se nepřepíná. **Upřesnění 2026-11-08:** test (relace) se skládá výhradně ze sad aktivního jazyka; do jedné relace nejdou vybrat sady dvou jazyků. |
| D19 | **Jazyk sady je po vytvoření neměnný (zadavatel, 2026-11-08).** Chybně založenou sadu uživatel opraví novou sadou ve správném jazyce a starou smaže. Slovo nejde přesunout do sady jiného jazyka. |
| D20 | **Žádný celkový souhrn přes oba jazyky (zadavatel, 2026-11-08).** Statistiky, souhrny, trend a pravidelnost se zobrazují jen po jazycích. Platí i pro případný rodičovský pohled (US-23). |
| D21 | **Možnosti ve výběru (zadavatel, 2026-11-08).** Má-li sada méně než 3 použitelné chybné možnosti, **doplní se z jiných sad téhož jazyka**. Nikdy z druhého jazyka. Počet možností klesne jen tehdy, když celý jazyk nemá dost slov (to je okrajový případ, pravidlo navrhne `learning-scientist`).
| D22 | **Jazyk se volí při importu (zadavatel, 2026-11-08, opraveno).** Při importu (fotka i CSV) uživatel vždy **vybere, pro který jazyk slovíčka přidává**, jako první krok; předvyplněno je aktivním jazykem, ale jde zvolit i druhý. Nabízejí se sady zvoleného jazyka a nová sada vznikne ve zvoleném jazyce. Jazyk je po celý import pevný, vidět na kontrolní obrazovce a OCR dostane jazykový kontext zvoleného jazyka. Import **nemění aktivní jazyk** zařízení; po uložení se může nabídnout „Přepnout na … a procvičit“. Do jednoho importu nejde míchat dva jazyky. (Dřívější znění D22 „import nabízí jen sady aktivního jazyka“ bylo mylný výklad a neplatí.) |

## Priority (návrh k odsouhlasení)
- **P0:** import fotkou s kontrolou, test s plánováním opakování, uložení výsledků, iCloud sync, souhrn po pokusu.
- **P1:** historie pokroku a slabá místa, nepravidelná slovesa, výslovnost (přehrání), CSV import.
- **P2:** hotové sady, gamifikace, kontrola výslovnosti hlasem, rodičovský pohled, připomenutí.

## Otevřené otázky a rizika k ověření
- **R1 (uzavřeno 2026-11-07):** TestFlight se v dětském účtu 13letého stáhne, ale nespustí (ověřeno uživatelem). Řešení: Ad Hoc distribuce (D5). Nový rizikový bod **R6**: Ad Hoc a Xcode buildy mohou používat jiné prostředí CloudKit (Development) než TestFlight a App Store (Production); data se mezi prostředími nekopírují. Přepnutí způsobu distribuce proto může znamenat ztrátu dat v cloudu — prostředí se musí zvolit hned na začátku a držet. Ověřit sondami U1–U3 z `docs/10`.
- **R2:** Jak spolehlivě Apple Vision / VisionKit přečtí češtinu, němčinu a angličtinu z fotky tabulky slovíček (diakritika, ä ö ü ß, dvojsloupcové tabulky, ručně psané)?
- **R3:** Kvalita a dostupnost hlasů (AVSpeechSynthesizer) pro de-DE a en-GB/en-US, offline.
- **R4:** Sync SwiftData přes CloudKit (omezení: žádné unikátní atributy, všechny vlastnosti volitelné nebo s defaultem, relace volitelné).
- **R5:** Dětský účet a soukromí — žádná analytika ani odesílání obrázků ven. OCR musí běžet v zařízení.

## Pravidla pro agenty
- Piš česky, stručně, závěr na začátku. Datum ISO. Částky v Kč.
- Výstup piš do souboru v `docs/` (název v zadání úkolu). Shrnutí v odpovědi je jen pomocné.
- Tvrzení o platformě označ **ověřeno (zdroj)** nebo **domněnka**.
- Neměň `docs/00-zadani.md`. Chybějící rozhodnutí zapiš jako předpoklad ve svém dokumentu.

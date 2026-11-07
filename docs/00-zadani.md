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
| D5 | Platforma iOS (iPhone **i iPad**), distribuce **jen přes TestFlight**. Vývojářský účet je k dispozici. |
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

## Priority (návrh k odsouhlasení)
- **P0:** import fotkou s kontrolou, test s plánováním opakování, uložení výsledků, iCloud sync, souhrn po pokusu.
- **P1:** historie pokroku a slabá místa, nepravidelná slovesa, výslovnost (přehrání), CSV import.
- **P2:** hotové sady, gamifikace, kontrola výslovnosti hlasem, rodičovský pohled, připomenutí.

## Otevřené otázky a rizika k ověření
- **R1:** Lze dítě v dětském účtu Family Sharing přidat do TestFlightu a spustit z něj aplikaci? Věková hranice testerů, způsob pozvánky, a zda TestFlight účet (Media & Purchases) může být jiný než iCloud účet (CloudKit). Ověřit u Apple dokumentace, nehádat.
- **R2:** Jak spolehlivě Apple Vision / VisionKit přečtí češtinu, němčinu a angličtinu z fotky tabulky slovíček (diakritika, ä ö ü ß, dvojsloupcové tabulky, ručně psané)?
- **R3:** Kvalita a dostupnost hlasů (AVSpeechSynthesizer) pro de-DE a en-GB/en-US, offline.
- **R4:** Sync SwiftData přes CloudKit (omezení: žádné unikátní atributy, všechny vlastnosti volitelné nebo s defaultem, relace volitelné).
- **R5:** Dětský účet a soukromí — žádná analytika ani odesílání obrázků ven. OCR musí běžet v zařízení.

## Pravidla pro agenty
- Piš česky, stručně, závěr na začátku. Datum ISO. Částky v Kč.
- Výstup piš do souboru v `docs/` (název v zadání úkolu). Shrnutí v odpovědi je jen pomocné.
- Tvrzení o platformě označ **ověřeno (zdroj)** nebo **domněnka**.
- Neměň `docs/00-zadani.md`. Chybějící rozhodnutí zapiš jako předpoklad ve svém dokumentu.

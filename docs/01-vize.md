# 01 — Vize produktu

Stav k 2026-11-07. Zdroj pravdy: `00-zadani.md`. Čísla v měřítkách úspěchu jsou **návrh k odsouhlasení**, zadání je nestanovuje.

## Závěr
Word-memo je malá iOS aplikace (iPhone i iPad, instalace Ad Hoc přes Mac) pro jednoho uživatele, syna v 7. třídě. Přebírá slovíčka z jeho sešitu fotkou, zjišťuje, jak dobře se mu každé vybavilo, a podle toho plánuje opakování. Nejdůležitější vlastnost není chytrý algoritmus, ale to, že se k ní syn vrací dobrovolně. Proto platí: neúspěch nesmí demotivovat, relace jsou krátké a bez tlaku.

## Problém
Syn se učí angličtinu a němčinu (překlady vždy do češtiny, D1) a špatně si pamatuje slovíčka. Nemá nástroj, který by změřil, jak dobře se mu slovo vybavilo, a podle toho řídil opakování. Co se vybaví hned, se má připomínat později, co ne, častěji.

## Cílový uživatel
- Syn, 7. třída ZŠ, mluví česky, učí se angličtinu a němčinu. Bez dyslexie a dysgrafie (D12).
- Používá vlastní zařízení s vlastním Apple ID v rodině, dětský účet (D10). Slovíčka importuje sám, občas je vloží otec.
- Zná iPhone/iPad, ale není to zaměstnanec s motivací „vyplnit tabulku". Aplikaci otevře, jen když je rychlá a nic ho při ní netrápí.
- Sekundární role: otec (zadavatel). Vkládá fotky a ověřuje, že aplikace funguje. Rodičovský pohled na pokrok zatím ne, ale architektura ho nesmí vylučovat (D11).

## Principy produktu
1. **Neúspěch nedemotivuje.** Chybná odpověď je informace, ne trest. Zobrazí se správný tvar, neutrální tón, žádné červené „křížky", žádné ztráty bodů ani sérií. Souhrn začíná tím, co se povedlo.
2. **Krátké relace.** Jedna relace se dá dokončit za několik minut. Lze ji kdykoli přerušit bez ztráty výsledků.
3. **Žádný tlak.** Žádné odpočítávání, žádné povinné denní cíle, žádné trestající upozornění. Opakování je nabídka, ne povinnost.
4. **Měř vybavení, ne jen správnost.** Plán opakování vychází z toho, jak dobře se slovo vybavilo (mix výběr/psaní, oba směry, D2–D3).
5. **Slovíčka bez opisování.** Import fotkou s kontrolou a opravou je hlavní cesta (D7). Cílem je, aby syn strávil čas učením, ne zadáváním.
6. **Soukromí dítěte.** OCR běží v zařízení, žádná analytika, žádné odesílání obrázků ven (R5). Data jsou jen v zařízeních a iCloudu syna.
7. **Funguje všude, kde syn je.** Offline použitelné, výsledky se synchronizují přes iCloud mezi iPhonem a iPadem (D6).
8. **Rozšiřitelnost.** Datový model nesmí vylučovat pozdější rodičovský pohled ani gamifikaci (D11, D13).

## Měřítka úspěchu (návrh)
Měří se lokálně z uložených pokusů, bez analytiky (princip 6). Hodnocení po 4 týdnech používání.

| # | Měřítko | Cíl |
|---|---|---|
| M1 | Pravidelnost: dny s alespoň jednou dokončenou relací | ≥ 4 ze 7 dnů v týdnu, průměr za 4 týdny |
| M2 | Délka relace | medián ≤ 10 min, ≥ 80 % relací dokončeno |
| M3 | Neúspěch nedemotivuje: podíl relací zahájených do 2 dnů po relaci s úspěšností < 60 % | není nižší než po ostatních relacích |
| M4 | Vybavení: úspěšnost u slov opakovaných po ≥ 7 dnech | roste, cíl ≥ 70 % ve 4. týdnu |
| M5 | Import: stránka s ~20 slovy od fotky po uloženou sadu | ≤ 5 min; ≤ 10 % slov vyžaduje opravu u tištěného textu (závisí na R2) |
| M6 | Sync: změna z jednoho zařízení na druhém | do 5 min při připojení (domněnka, závisí na CloudKit, R4) |
| M7 | Soukromí | 0 síťových volání s obsahem uživatele mimo iCloud (kontrola kódu a síťového provozu) |
| M8 | Dobrovolnost: otec se po 4 týdnech zeptá „používáš to rád/dobrovolně?" | ano |

## Co aplikace záměrně NENÍ
- Není to slovník ani překladač. Neřeší gramatiku, výklad pravidel ani učebnici.
- Není to školní známkování ani hodnocení syna. Neodesílá výsledky učiteli.
- Není to hra. Gamifikace je přání, ne priorita (D13), nesmí zhoršit principy 1–3.
- Zatím to není rodičovský dashboard (D11), kontrola výslovnosti syna hlasem (D9) ani připomenutí.
- Není to veřejná aplikace: distribuce Ad Hoc přes Mac a Xcode, bez App Store a bez TestFlightu (D5), bez Androidu a webu, bez účtů a přihlašování mimo Apple ID.
- Nesbírá analytiku, neobsahuje reklamy a nenabízí nákupy.
- Nenahrazuje jazykové kurzy a nenabízí hotové sady, dokud není jasná licence (D7).

## Předpoklady (zadání je nestanovuje)
- Aplikace je jednouživatelská (jeden syn na zařízení). Více profilů není v rozsahu.
- Aplikace běží česky. Jazyk rozhraní jiný než čeština se neřeší.
- Číselné cíle M1–M8 jsou odhady k odsouhlasení.

## Otevřené otázky
Viz seznamy na konci `02-user-stories.md` a `04-story-mapa.md`; rizika R1–R5 jsou vedena v `00-zadani.md`.

# 06 — UX návrh

Autor: ux-designer, stav k 2026-11-08 (zapracováno rozhodnutí D18 až D22 ve znění opraveném 2026-11-08, tj. jazyk se při importu volí, a US-26). Zdroj pravdy: `00-zadani.md`; stories: `02-user-stories.md`; obsah a import: `09-obsah-a-import.md`. Navrhuji, nerozhoduji o prioritě ani rozsahu (`00-zadani.md`). Tvrzení o platformě jsou **domněnka**, dokud je neověří architekt nebo ux-verifier na zařízení; nic z platformy jsem v této session neověřoval.

## Závěr

1. **Aktivní jazyk (D18, US-26) je jeden globální stav nad všemi obrazovkami a je vždy vidět.** Na kořenech Procvičit, Sady, Slovesa a Pokrok je pod titulkem přepínač „English | Deutsch“ (C2, bez vlajek) a titulek říká jazyk („Sady · němčina“). Pod neaktivním jazykem je jen informace „k procvičení: N“, která nemíchá fronty. Na iPadu je přepínač v horní části sidebaru; když je sidebar skrytý, je v záhlaví obsahu jako na iPhonu. V testu přepínač zmizí a zůstane štítek jazyka (C3). Nastavení je společné a jazyk nemá. Jazyk sady je po vytvoření neměnný (D19): v UI není žádná akce „Změnit jazyk sady“, chybně založenou sadu opraví nabídka „Smazat a vytvořit znovu“ (S22). Import si jazyk slovíček volí zvlášť (D22: první řádek S31, předvyplněný aktivním jazykem) a aktivní jazyk nemění. Pravidla: kap. 3.8, obrazovka S02, komponenty C2 a C3. Odchylky od zadaného návrhu a jejich důvody: kap. 3.8.1.
2. **Aplikace má jednu smyčku a čtyři místa kolem ní.** Smyčka je Procvičit → vyhodnocení → souhrn. Okolo jsou Sady (slova, import fotkou), Slovesa (samostatný vstup, D8), Pokrok (historie a slabá místa) a Nastavení (iCloud, duplicity, smazání dat). Na iPhonu je to tab bar o 5 položkách, na iPadu sidebar se stejnými 5 cíli. Layout se řídí size class, ne typem zařízení. Všechno kromě Nastavení a jazyka při importu (S31, D22) je filtrované na aktivní jazyk.
3. **Test je celoobrazovkový režim bez tab baru.** Nahoře je jen „Skončit“, štítek jazyka a ukazatel postupu (bez časomíry). Zadání a tlačítko výslovnosti jsou vždy nad klávesnicí. Pole a tlačítko „Zkontrolovat“ jsou ukotvené těsně nad klávesnicí, zadání se smí zmenšit nebo rolovat, nikdy ho klávesnice nepřekryje. Na SE velikosti (667 pt výšky) to vychází na dotyk, viz S13. Ve výběru z možností se chybné možnosti dopočítají z jiných sad téhož jazyka, nikdy z druhého (D21, S12).
4. **Neúspěch nedemotivuje konkrétně takto:** v celé aplikaci není červená mimo potvrzení mazání, žádný symbol „X“, žádné body ani série. „Špatně“ je neutrální modrofialová s ikonou „znovu“, „skoro“ je jantarová, „správně“ je klidná zelená. Stav se nikdy nesděluje jen barvou (ikona + slovo + tvar). Souhrn začíná tím, co se povedlo, a srovnání s minulým pokusem ukazuje jen tehdy, když je lepší nebo stejné. Barvy jazyka (3.8) jsou od stavových barev oddělené a v testu se nepoužívají.
5. **Kontrolní obrazovka importu (S35) je nejdůležitější obrazovka aplikace.** Je jedna pro fotku i CSV. Podezřelé řádky jsou nahoře a rozbalené s výřezem z fotky, ostatní jsou kompaktní. Každé pole je přímo editovatelné jedním ťuknutím. Uloží se jen to, co je bez značky nebo co syn opravil či potvrdil. Jazyk slovíček se volí jako první řádek průvodce (S31, předvyplněný aktivním jazykem, D22), po zahájení skenu je zamčený a je vidět až do konce (S34, S35, S36).
6. **Nepsání = „Nevím“.** Každá otázka má tlačítko „Nevím“. Vyhodnotí se jako „špatně“ bez trestu a ukáže správnou odpověď. Prázdné pole nelze potvrdit, aby nevznikaly omyly. Potřebuje potvrzení learning-scientist (viz kap. 7).
7. **Mezery ve stories (kap. 6):** nenavrhl jsem nápovědu v testu, přepínání psaní → výběr, vyhledávání, „Vrátit import“, export dat, editaci množného čísla/příkladu/IPA, hromadné vkládání sloves a „O aplikaci“. Žádná z nich nemá story. Dále chybí rozhodnutí o „nic k opakování“ (US-08) a o konceptu kontroly po násilném ukončení (US-03). Nově (D18): sloveso nemá sadu, takže jeho jazyk bere z aktivního jazyka (G18). Nabídka „Smazat a vytvořit znovu“ vychází z D19 a v US-04 zatím nemá akceptační kritérium. Dříve otevřené G2 (chybné možnosti), G20 (import do druhého jazyka) a G25 (přepnutí jazyka v „Smazat a vytvořit znovu“) jsou vyřešené (D21, D22).
8. **Data, která aplikace zatím nemá odkud vzít (kap. 5):** výřezy řádků z fotky (R2), stav a čas syncu (R4), původní hodnota při konfliktu (US-12), pravidlo „zlepšuje se / drží se“ (Q1), definice „naučené slovo“ (Q1). Nově: velikost dnešního kola pro neaktivní jazyk (D-19), zjištění jazyka textu z fotky (D-21). Návrh s nimi počítá jako s požadavky na zdroj, ne jako s hotovými daty.
9. **US-18 vs. D16:** story zmiňuje en-GB/en-US, zadání (D16) rozhodlo en-GB. Q10 je tím zodpovězena, v návrhu žádná volba varianty angličtiny není.
10. **Gamifikace, rodičovský pohled a připomenutí nejsou navrženy** (P2, bez rozhodnutí). Architektura obrazovek je nevylučuje: souhrn (S16) a Pokrok (S50) jsou místa, kde by se pozdější odměny a agregace objevily. Součet ani srovnání přes oba jazyky se nenavrhuje nikde (D20); platí i pro pozdější rodičovský pohled (US-23).

---

## 0. Východiska a značení

**Zařízení pro wireframy (domněnka, rozměry v pt):** iPhone na výšku 393 × 852, kontrolní nejmenší 375 × 667 (třída SE); iPad 11" na výšku 834 × 1194, na šířku 1194 × 834. Výška klávesnice: iPhone zhruba 260–300 pt, iPad na šířku zhruba 350–400 pt (odhad, změřit na zařízení). iPhone na šířku se nenavrhuje (zadání říká iPhone na výšku); chování po otočení je otevřená otázka (kap. 7).

**Compact vs. regular:** wireframy „iPhone“ platí pro každé compact width, tedy i pro iPad v Split View / Slide Over v úzkém okně. Wireframy „iPad“ platí pro regular width (domněnka: standardní chování size class).

**Značení v ASCII:** `[ Text ]` tlačítko, `( )` přepínač nebo výběr, `[ ♪ ]` tlačítko výslovnosti, `[!]` upozornění, `[ok]` správně, `[~]` skoro správně, `[↻]` zatím ne (neutrální, ne červené), `[DE]` / `[EN]` štítek jazyka (C3), `[ English | Deutsch ]` přepínač jazyka (C2; `▐ … ▌` označuje aktivní segment).

**ID obrazovek:** S = obrazovka, C = sdílená komponenta. U každé je story, ze které vychází.

**Příklady v wireframech** (názvy sad, počty slov, čísla „k procvičení“) jsou ilustrační, nejsou to data aplikace.

---

## 1. Úkoly uživatele a toky

Úkoly jsou formulované z pohledu syna (nebo otce). Číslo za úkolem je story.

| # | Úkol | Story | Vstup | Kroky (obrazovky) | Konec |
|---|---|---|---|---|---|
| T1 | Otevřít aplikaci poprvé, zvolit jazyk a hned vědět, co dělat | US-01, US-26 | Spuštění z čisté instalace | S01 krok 1 (volba jazyka) → krok 2 → „Vyfotit slovíčka“ | S31 (kam uložit; segment „Jazyk slovíček“ je předvyplněný jazykem zvoleným v S01) |
| T2 | Nafotit stránku ze sešitu a zkontrolovat, co aplikace přečetla | US-02, US-03 | Sady → „+“ nebo první spuštění | S30 → S31 (volba jazyka slovíček + sada tohoto jazyka) → S32 (jen poprvé) → systémový skener → S34 → S35 → S36 | Slova uložená v sadě; nabídka „Procvičit teď“ (u jiného než aktivního jazyka „Přepnout na … a procvičit“) |
| T2b | Totéž z knihovny fotek (otec vkládá fotku) | US-02 | Sady → „+“ → „Vybrat z fotek“ | S30 → S31 → výběr fotky → S34 → S35 → S36 | Jako T2 |
| T3 | Přidat nebo opravit jedno slovo ručně | US-04 | S21 → „+“ nebo řádek slova | S23 | Slovo uložené |
| T4 | Mít slova v sadách a vybrat, co se bude zkoušet | US-04 | Sady; Procvičit → „Co procvičit“ | S20 → S21; S11 (jen sady aktivního jazyka) | Sada v procvičování |
| T5 | Projít krátké procvičování | US-05, US-06, US-07, US-08, US-10 | Procvičit → „Začít“ | S10 → S12 nebo S13 → S14 → … → S16 | Souhrn |
| T6 | Skončit procvičování dřív | US-05, US-09, US-10 | „Skončit“ v testu | S15 → S16 | Souhrn z dosavadních odpovědí |
| T7 | Zopakovat si jen chybná slova | US-09 | S16 | „Ještě jednou chybná slova“ → S12/S13 → S14 → S16 | Souhrn |
| T8 | Podívat se, jestli se zlepšuji | US-14 | Pokrok | S50 → souhrn jednoho pokusu (S16) | Zpět |
| T9 | Zjistit, kde mi to nejde, a zaměřit se na to | US-15 | Pokrok → „Slabá místa“ | S51 → „Procvičit slabá slova“ → S12/S13 … | Souhrn |
| T10 | Zadat nepravidelné sloveso z učebnice | US-16 | Slovesa → „+“ | S40 → S41 | Sloveso uložené |
| T11 | Procvičit nepravidelná slovesa | US-17 | Slovesa → „Procvičit“ | S40 → S42 → S43 → … → S16 (varianta) | Souhrn sloves |
| T12 | Poslechnout si slovo | US-18 | Kdekoli je [ ♪ ] | C1 | Přehráno nebo srozumitelná hláška |
| T13 | Načíst slovíčka ze souboru CSV | US-19 | Sady → „+“ → „Soubor CSV“ | S30 → S31 → výběr souboru → S35 → S36 | Slova uložená |
| T14 | Vědět, že se výsledky synchronizují (nebo proč ne) | US-11 | Nastavení → iCloud | S60 → S61 | Stav zjištěn |
| T15 | Vyřešit, co sync zdvojil nebo přepsal | US-12 | Nastavení; banner v Sady | S62, S63; banner v S20 | Duplicity sloučeny / změny potvrzeny |
| T16 | Smazat všechna data (obou jazyků) | US-20 | Nastavení → „Smazat všechna data“ | S60 → S64 | Návrat na S01 (včetně volby jazyka) |
| T17 | Používat vše bez internetu | US-13 | — | Průřezově (kap. 3.6) | — |
| T18 | Přepnout se na druhý jazyk | US-26 | Přepínač C2 na kořeni Procvičit / Sady / Slovesa / Pokrok (v sidebaru na iPadu) | C2 → (S02 při velkém písmu) → táž záložka v novém jazyce | Obrazovka záložky v novém jazyce; VoiceOver: „Aktivní jazyk: …“ |
| T19 | Začít se učit i druhý jazyk (poprvé) | US-26, US-04, US-02 | C2 → druhý jazyk, který nemá sady | C2 (přepnutí) → prázdný stav (S10 / S20) → „Vyfotit slovíčka“ nebo „Přidat sadu“ → S31 (jazyk předvyplněný aktivním, tedy druhým) → T2 | Sada v druhém jazyce; jazyk je aktivní |
| T20 | Opravit sadu založenou ve špatném jazyce | US-04 (D19, D22) | S21 → „Upravit“ | S22 → „Smazat a vytvořit znovu“ → S24 (varianta 3; aktivní jazyk nepřepíná) → S30 → S31 (předvybraný správný jazyk, původní název) → T2 | Nová sada ve správném jazyce; stará smazaná |

**Bez vlastní obrazovky:** US-08 (plánování) a US-10 (ukládání po každé odpovědi) jsou chování, ne obrazovky. Projevují se v S10 (z čeho se kolo skládá), v štítku „Znovu“ v S12/S13 a v mikrotextu S14.

---

## 2. Informační architektura

### 2.1 Navigační mapa

```
Aplikace
├─ [první spuštění] S01 Úvod (krok 1 = volba jazyka) ──► S31 Kam uložit ──► import (T2)
│
│  AKTIVNÍ JAZYK (EN | DE) = globální stav, mění se jen přes C2 / S02
│  (přepínač je na kořeni záložek 1–4; v testu a hlubších obrazovkách je jen štítek C3)
│  JAZYK SADY je po vytvoření neměnný (D19): žádná akce „Změnit jazyk sady“
│  IMPORT si jazyk slovíček volí sám (D22): S31 první řádek, předvyplněný aktivním jazykem;
│  import aktivní jazyk NEMĚNÍ (po uložení nabídka „Přepnout na … a procvičit“)
│
├─ PROCVIČIT (tab 1)
│    S10 Start ─► S11 Co procvičit (sheet)
│        └─► TEST (celá obrazovka, bez tab baru, jazyk zamčený)
│              S12 výběr / S13 psaní ─► S14 vyhodnocení ─► … ─► S16 Souhrn
│              S15 Skončit (sheet)
│
├─ SADY (tab 2)
│    S20 Seznam sad ─► S21 Detail sady ─► S23 Slovo
│        │                  └─► S22 Úprava sady (sheet) ─► „Smazat a vytvořit znovu“ ─► S24 ─► S30 ─► S31
│        └─► „+“ S30 Přidat slova ─► S31 ─► S32 ─► skener ─► S34 ─► S35 ─► S36
│                                    └──► CSV: výběr souboru ─► S35 (S37 = vstupní kontrola souboru)
│
├─ SLOVESA (tab 3)
│    S40 Seznam ─► S41 Přidat / upravit ;  S40 ─► S42 Procvičování ─► S43 ─► S16 (varianta)
│
├─ POKROK (tab 4)
│    S50 Vývoj ─► S16 (souhrn pokusu) ;  S51 Slabá místa ─► test (S12/S13) ;  ─► S23
│
└─ NASTAVENÍ (tab 5, společné pro oba jazyky, bez přepínače)
     S60 ─► S61 iCloud ;  S62 Duplicity ;  S63 Přepsané změny ;  S64 Smazat všechna data

S02 Přepnutí jazyka: tři podoby jedné akce (přepínač C2 v záhlaví, sheet „Jazyk“ při velkém písmu,
tlačítko „Přepnout na …“ v prázdných stavech a po importu do neaktivního jazyka v S36).
```

C1 (tlačítko výslovnosti) je v S12, S14, S16, S23, S42, S43. C2 (přepínač jazyka) je na kořenech S10, S20, S40, S50 (S51 je součást Pokroku), v S02 a v sidebaru iPadu. C3 (štítek jazyka) je v testu a na hlubších obrazovkách (tabulka v 3.8).

### 2.2 iPhone (compact width): tab bar

Pět položek, ikona + krátký popisek (popisky se nesmí zkrátit na „…“ při větším Dynamic Type; při AX velikostech se tab bar chová podle systému):

| Tab | Popisek | Obsah |
|---|---|---|
| 1 | Procvičit | S10, test |
| 2 | Sady | S20–S24, import |
| 3 | Slovesa | S40–S43 |
| 4 | Pokrok | S50, S51 |
| 5 | Nastavení | S60–S64 |

- Výchozí tab po spuštění: **Procvičit**, v aktivním jazyce z minula (US-26). Při prázdné knihovně S10 sám nabídne „Vyfotit slovíčka“.
- **Přepínač jazyka (C2) leží pod velkým titulkem na kořenové obrazovce tabů 1–4**, ne v tab baru (tab bar zůstává na 5 cílech, jazyk není šestý cíl). V tabu Nastavení přepínač není.
- Přepnutí jazyka zůstane na stejném tabu (z Sad zůstanu v Sadách) a vrátí ho na kořen (3.8, bod 4).
- Test (S12–S16) se otevírá jako celoobrazovkový režim bez tab baru, aby se nedalo omylem odejít a aby nic neubíralo místo nad klávesnicí. Přepínač jazyka tam není.
- Import (S31–S36) je modální průvodce s vlastním „Zrušit“ vlevo nahoře, ne push do tab baru. Přepínač aktivního jazyka (C2) v něm není; jazyk slovíček se volí v prvním řádku S31 (D22) a po zahájení skenu je zamčený.
- Které z tabů jsou v konkrétním buildu vidět (Slovesa, Pokrok jsou P1), je rozhodnutí o rozsahu releasu, ne moje. Návrh počítá s tím, že tab, jehož funkce v buildu není, se prostě nezobrazí.

### 2.3 iPad (regular width): sidebar a split view

- **Na šířku:** sidebar s týmiž 5 cíli (Procvičit, Sady, Slovesa, Pokrok, Nastavení) vlevo, vedle něj obsah. Sady a Slovesa mají dvě sloupce obsahu (seznam | detail), takže celkem tři sloupce. Sidebar jde sbalit. **Přepínač jazyka (C2) je nahoře v sidebaru nad seznamem cílů**, ne jako šestý cíl. Když je sidebar sbalený, přepínač přejde do záhlaví obsahu stejně jako na iPhonu (jazyk nesmí zmizet).
- **Na výšku:** sidebar je skrytý a 5 cílů je v horní liště (nebo se sidebar vysouvá; domněnka: iOS 26 umí obojí z jednoho prvku, **ověřit** před implementací). Sady a Slovesa jsou dva sloupce (seznam | detail). **Přepínač jazyka je v záhlaví obsahu** (nad seznamem v levém sloupci / pod titulkem), protože sidebar není vidět. Přepínač je vždy právě na jednom místě: v sidebaru, nebo v záhlaví obsahu, nikdy na obou.
- **Test, import, souhrn:** na iPadu se otevírají jako celoobrazovkový režim nebo jako centrovaný formát (max. šířka obsahu 560–640 pt), nikdy ne roztažené přes celou šířku. Řádek textu delší než ~70 znaků se špatně čte.
- **Split View / Slide Over aplikace:** v úzkém okně se použije iPhone layout (kap. 0).
- **Externí klávesnice:** Return = „Zkontrolovat“, v dalším kroku Return = „Další“ (S13, S14). Šipky a Tab fungují v S35. Žádné další zkratky nenavrhuji.
- **Plovoucí a rozdělená klávesnice na iPadu:** zadání, pole a tlačítko se řídí skutečnou výškou klávesnice, ne pevnou hodnotou.
- Umístění přepínače v sidebaru je **domněnka** (vlastní hlavička nad seznamem cílů); ověřit na iOS 26, viz kap. 7.

---

## 3. Průřezová pravidla

### 3.1 Tón a jazyk
- Tykání, krátké věty, věcně. Žádné vykřičníky kromě výjimečně v „Hotovo“ stavech, žádné „Úžasné!“, žádná maskotka, žádné emoji. Cíl: 13letý, který nechce „dětskou“ appku.
- Chybová hláška vždy říká, **co se stalo, co je v pořádku a co lze udělat**. „Nic se neztratilo“ píšu jen tam, kde to platí.
- Počty se skloňují: 1 slovo, 2–4 slova, 5 a víc slov; 1 dvojice, 2–4 dvojice, 5 dvojic; 1 tvar, 2–4 tvary, 5 tvarů; 1 sada, 2–4 sady, 5 sad. V mikrotextech níže `{n}` předpokládá správné skloňování (požadavek na implementaci).
- Název jazyka se skloňuje (tabulka v 3.8). V mikrotextech značí `{němčinu}` správný tvar podle aktivního jazyka. Texty s jazykem se nesmí skládat lepením, každý má tvary pro oba jazyky (požadavek na implementaci).

### 3.2 Role barev a tvar stavů (bez hodnot)
| Stav | Role barvy | Ikona | Slovo |
|---|---|---|---|
| Správně | klidná zelená | zaškrtnutí v kruhu | „Správně“ |
| Skoro správně | jantarová | kruh s „≈“ | „Skoro“ |
| Zatím ne | modrofialová (ne červená) | kruh se šipkou „znovu“ | „Zatím ne“ |
| Výběr (ještě nevyhodnoceno) | akcentová | — | — |
| Označeno k ověření (import) | jantarová | „!“ v kruhu | „Ověř“ |
| Mazání (jen potvrzení) | červená, nikde jinde | — | „Smazat“ |
| Jazyk (identifikace, ne stav) | dvě jemné barvy, jedna pro angličtinu, jedna pro němčinu; žádná se nesmí shodovat ani být zaměnitelná se stavovými barvami výše | štítek C3 | „EN“ / „DE“ |

- Stav nikdy jen barvou: ikona + slovo + zvýrazněné písmeno (tučně a podtržené, ne jen barevné). Kvůli barvoslepým uživatelům (červeno-zelená), VoiceOveru a svitu slunce. Totéž platí pro jazyk: vždy text „EN“ / „DE“ nebo název, barva jen podporuje.
- Kontrast textu ≥ 4,5:1, ikon a ohraničení ≥ 3:1 (WCAG AA, domněnka, že to je dostatečná laťka; ověří ux-verifier). Světlý i tmavý režim.
- Vizuální směr: klidný, hodně prostoru, velká tučná typografie pro slovo, jedna akcentová barva pro ovládání (tlačítka, výběr) a dvě jemné identifikační barvy jazyka s přesně omezeným použitím (3.8, bod 7), žádné ilustrace, žádné „odznaky“. Animace jemné, respektují Reduce Motion. Žádná animace „slavení“.

### 3.3 Cíle a čitelnost
- Dotykový cíl ≥ 44 × 44 pt (doporučení HIG, z paměti), primární tlačítka a možnosti odpovědí ≥ 56 pt na výšku. Rozestup mezi sousedními cíli ≥ 8 pt. To platí i pro segmenty přepínače jazyka (C2): výška ≥ 44 pt. Nativní segmentovaný ovladač může být nižší (**domněnka, změřit**); pak se použije vlastní se zachováním přístupnosti.
- Zadaná slova ve velkém typu (titulek), pole odpovědi ≥ 20 pt písma.
- Dynamic Type: všechny obrazovky musí fungovat do největších přístupnostních velikostí. Pravidla: možnosti odpovědí se vždy skládají do jednoho sloupce, řádky tabulky v S35 se zalamují na dva řádky, ovládací lišty se zalamují, nic se neořezává, obrazovka se smí rolovat, ale **pole a tlačítko „Zkontrolovat“ zůstávají ukotvené nad klávesnicí**. Přepínač jazyka přejde při největších velikostech na tlačítko „Jazyk: Deutsch ▾“ (C2, S02).
- VoiceOver: u každého ovládacího prvku popisek; po vyhodnocení se focus přesune na výsledek a přečte se např. „Skoro správně. Jedno písmeno se prohodilo. Správně: das Kind.“ Ikona stavu má popisek, není jen dekorace. Aktivní jazyk se čte jako „Aktivní jazyk: němčina“ (3.8, bod 8).
- Haptika: jemná pozitivní při „správně“. Žádná negativní haptika, žádný zvuk při chybě. Přepnutí jazyka: jemný výběrový tik, žádný zvuk.

### 3.4 Pole pro psaní odpovědi
- Vypnutá autokorekce, prediktivní nápověda, automatické velké písmeno a kontrola pravopisu (jinak klávesnice napoví odpověď nebo ji pokazí). **Požadavek na implementaci**, ověří ux-verifier.
- Nad klávesnicí je lišta znaků podle jazyka odpovědi: pro němčinu `ä ö ü ß`, pro češtinu `á č ď é ě í ň ó ř š ť ú ů ý ž` (vodorovně rolovatelná). Pro anglickou odpověď (en-GB) se lišta nezobrazuje a její řádek se neponechává prázdný (rozpočet výšky S13 se tím uvolní). Neukládá se předpoklad, že má syn nainstalovanou českou nebo německou klávesnici.
- Lišta nenabízí členy der/die/das (aby se člen nepřipomínal předem; viz S14, „člen chybí“).

### 3.5 Žádný tlak
- Žádný časovač, odpočet ani „tempo“. Postup je počet slov (např. „4 z 12“), ne čas.
- Žádný počet resty. S10 ukazuje velikost dnešního kola, ne „máš 87 slov po termínu“ (US-08). Totéž platí pro údaj „k procvičení: N“ u druhého jazyka (C2): je to velikost dnešního kola daného jazyka, nikdy souhrn zpoždění; bez červené, bez zvýraznění, při 0 se nezobrazuje.
- Přerušení (zavření aplikace, hovor) nic neztratí: každá odpověď je uložená okamžitě (US-10), rozepsaný text v poli přežije přepnutí do jiné aplikace.

### 3.6 Offline (US-13)
- **Žádný globální banner „jste offline“.** Aplikace nehlásí chybu sítě tam, kde síť nepotřebuje. Funguje offline: S01, S02, S10–S16, S20–S24, import fotkou (S31–S36), CSV, S40–S43, S50–S51, přehrání výslovnosti (pokud je hlas stažený). Přepnutí jazyka je lokální (US-26).
- Síť potřebuje jen: stav a odeslání syncu (S61), smazání iCloud kopie (S64), případné stažení hlasu (mimo aplikaci, v Nastavení iOS). Tam a jen tam se offline stav hlásí (texty u těchto obrazovek).

### 3.7 Sync konflikt (US-12)
- Konflikt není modální a nezastavuje práci. V Sadách se objeví nenásilný banner jen tehdy, když je potřeba rozhodnutí (smazané vs. upravené slovo). Duplicity a přepsané změny čekají v Nastavení (S62, S63) a v řádku Nastavení se ukáže tečka s počtem. Žádný odznak na ikoně aplikace.
- Plán opakování se po sloučení přepočítá z historie (US-12) bez zobrazení uživateli.
- Banner v S20 patří jazyku dotčeného slova: ukáže se v jeho jazyce. Počty v řádcích Nastavení (S60) zahrnují oba jazyky, takže rozhodnutí z druhého jazyka nezmizí.

### 3.8 Aktivní jazyk (D18 až D22, US-26)

**Zdroj:** D18 (jazyky se nemíchají, jeden aktivní jazyk, přepnutí na nejvyšší úrovni, ne uprostřed testu), D19 (jazyk sady je po vytvoření neměnný), D20 (žádný celkový souhrn přes oba jazyky), D21 (chybné možnosti jen z téhož jazyka), D22 (při importu uživatel vždy volí jazyk slovíček; import nemění aktivní jazyk) a US-26. D19 až D22 jsou **rozhodnuté**, navrhuji podle nich. Q18–Q21 v `02-user-stories.md` jsou otevřené, návrh je neuzavírá; kde se na ně opírá, je to uvedeno.

**1. Jeden stav, jedna akce.** Aktivní jazyk je jediná hodnota (angličtina, nebo němčina). Mění se přepínačem C2 / sheetem S02 a zkratkou k téže akci „Přepnout na …“ v prázdných stavech. Nikde jinde (ne v S11, ne v S31, ne v S41, ne v S50 jako filtr, ne v S24). Segment „Jazyk slovíček“ v S31 je volba jazyka importu, ne přepínač aktivního jazyka: import aktivní jazyk nemění (bod 12). Aplikace ho nikdy nepřepne sama, ani podle obsahu fotky, souboru nebo termínů (US-26, Q21).

**2. Co je vždy po jazycích.** Fronta a plán, test (včetně chybných možností, D21), souhrn, historie a trend, pravidelnost, slabá místa, slovesa, výslovnost (hlas), import (jazyk OCR). **Žádná obrazovka nesčítá ani nesrovnává jazyky (D20)**; platí i pro pozdější rodičovský pohled (US-23). Nastavení (S60–S64) je společné.

**3. Kde je jazyk vidět.**

| Místo | Přepínač C2 | Štítek C3 | Titulek / záhlaví |
|---|---|---|---|
| Kořeny S10, S20, S40, S50 (S51 jako část Pokroku) | ano | — | „Procvičit · němčina“, „Sady · němčina“, „Slovesa · němčina“, „Pokrok · němčina“ |
| iPad, sidebar viditelný | ano, nahoře v sidebaru | — | titulek obsahu stejně jako výše |
| Hlubší obrazovky (S21, S22, S23, S41) | ne | ano v záhlaví („DE · němčina“) | název sady / slova |
| Sheet S11 | ne | v nadpisu | „Co procvičit · němčina“ |
| Test a souhrn (S12–S16, S42, S43) | ne, zmizí | ano, neutrální (bez barvy) | „DE NĚMČINA → ČEŠTINA“; S16: „Souhrn · němčina“ |
| Průvodce importem (S31–S37) | ne (jazyk slovíček se volí vlastním segmentem v S31, D22; aktivní jazyk se nemění) | S31: ovládací segment, od zahájení skenu zamčený; S34, S35, S36: štítek | S31 první řádek „Jazyk slovíček“, S34 řádek „Importuji do: …“, S35 záhlaví, S36 text |
| Nastavení (S60, S61, S64) | ne | ne | S60: „Nastavení“ + věta „Platí pro angličtinu i němčinu.“ |
| S62, S63 (řádky z obou jazyků) | ne | ano na každém řádku | — |

Důvod, proč přepínač není na hlubších obrazovkách: detail sady, slova nebo slovesa patří jednomu jazyku. Přepínač pod rukama by měnil jazyk, ale ne obrazovku, a vznikl by obsah, který tam nepatří.

**4. Co se stane při přepnutí** (jedno ťuknutí, bez potvrzení; nic se nemaže ani nemění, US-26):
1. Aktivní jazyk se okamžitě změní a uloží (bod 5). Obsah přejde jemným prolnutím (Reduce Motion: bez animace). VoiceOver oznámí „Aktivní jazyk: angličtina“.
2. Zůstane stejný tab. Navigace v tabu se vrátí na kořen (detail sady nebo slova z druhého jazyka v novém jazyce neexistuje). Na iPadu se zruší výběr v seznamu: pravý panel „Vyber sadu vlevo.“
3. Zachová se to, co s jazykem nesouvisí: přepínače Vývoj / Slabá místa, Slovíčka / Slovesa, metrika grafu. Filtr „Sada“ v S50 se vrátí na „Všechny sady“ nového jazyka.
4. Každý jazyk má vlastní výběr sad „v procvičování“ (S11), vlastní kolo, vlastní trend. Nic se mezi jazyky nepřenáší.
5. Neexistuje nic, co by se muselo před přepnutím dokončit. Otevřený test přepnutí znemožňuje (bod 6); rozpracovaná kontrola importu (G6) patří svému jazyku.

**5. Pamatování (US-26, Q18).** Po spuštění je aktivní jazyk, který byl naposledy. Navrhuji ho držet **lokálně na zařízení**, ne synchronizovat (doporučení z Q18): sync by mohl změnit jazyk pod rukama, třeba uprostřed testu. Z toho plyne, že nové zařízení s daty z iCloudu se při prvním spuštění ptá na jazyk (S01 krok 1) a sady se v něm objeví, až je sync přinese.

**6. V testu je jazyk zamčený.** V S12–S16 a S42–S43 přepínač zmizí a zůstane štítek C3 (není to tlačítko). Jediná cesta ven je „Skončit“ → S15 → S16 → „Hotovo“ → S10, kde přepínač zase je. Nezobrazuji vysvětlující text („jazyk nelze přepnout“), protože nic nenabízí, co by se dalo čekat; VoiceOver štítek přečte jako „Jazyk testu: němčina“. Návrat z jiné aplikace jazyk nemění.

**7. Barva jazyka (jemný akcent).** Dvě identifikační barvy (hodnoty ne navrhuji). Smějí se použít jen: na výplň vybraného segmentu v C2, na štítek C3 mimo test, na tenkou linku pod záhlavím kořenových obrazovek. Nesmějí se použít: na tlačítka (zůstává akcent aplikace), na stavy odpovědí, na grafy, v testu (S12–S16, S42, S43; tam jsou barvy vyhrazené pro „správně / skoro / zatím ne“). Obě barvy musí být odlišitelné od sebe i od zelené, jantarové a modrofialové pro osoby s poruchou barevného vidění a musí splnit kontrast 4,5:1 pro text; ověří ux-verifier. Bez barvy to funguje díky textu „EN“ / „DE“, názvu jazyka v titulku a tvaru aktivního segmentu (zvýšený / vyplněný + tučné písmo).

**8. VoiceOver.**
- Přepínač je skupina s popiskem a hodnotou: „Aktivní jazyk: němčina.“ (vyslovuje se po českém „němčina“, ne „Deutsch“; segment má vizuální text „Deutsch“, ale přístupnostní popisek česky).
- Segment neaktivního jazyka: „Angličtina, neaktivní. K procvičení 7 slov. Dvojitým klepnutím přepnout.“ Bez čísla: „Angličtina, neaktivní. Dvojitým klepnutím přepnout.“ Pro jazyk bez sad: „Angličtina, neaktivní. Zatím žádné sady.“
- Titulek je nadpis: „Sady, němčina, nadpis.“
- Po přepnutí oznámení „Aktivní jazyk: angličtina“ a fokus na titulek obrazovky.
- Štítek C3: „němčina“, ne „D E“. Při písmu AX velikostí viz C2, varianta tlačítko.

**9. Skloňování názvu jazyka** (požadavek na texty):

| Jazyk | „Pro …“ (4. pád) | „v …“ (6. pád) | „z …“ (2. pád) | základní tvar |
|---|---|---|---|---|
| angličtina | angličtinu | angličtině | angličtiny | angličtina |
| němčina | němčinu | němčině | němčiny | němčina |

**10. Duplicity a hledání shody jen v rámci jazyka.** Totožný zápis v obou jazycích (např. „Hand“, „Bad“, „Kind“) není duplicita. Platí pro S35 (značky duplicit), S23 (varování) a S62. Stejně tak chybné možnosti ve výběru z možností se berou jen z téhož jazyka (D21, S12).

**11. Jazyk sady je neměnný (D19, rozhodnuto).** Nikde v UI není akce „Změnit jazyk sady“: ani v S22, ani v S31, ani v nabídce „⋯“, ani v S23 (sada slova se dá změnit jen na jinou sadu téhož jazyka). Slovo ani sloveso nejde přesunout ani zkopírovat do sady jiného jazyka. Chybně založenou sadu opraví nabídka **„Smazat a vytvořit znovu“** v S22: potvrzení S24 (varianta 3) → S30 → S31 s předvybraným správným jazykem (jazyk sady, která se maže) a s původním názvem. Aktivní jazyk se při tom **nepřepíná** (D22: import ho nemění); po uložení nové sady S36 případně nabídne „Přepnout na … a procvičit“. Slova se **nepřenášejí** (jsou vázaná na jazyk) a text to říká předem. Před uložením, tedy ještě v průvodci importem, je oprava levnější: do zahájení skenu stačí v S31 změnit segment „Jazyk slovíček“; po skenu S35 → „Zrušit import“ → znovu (nic se zatím nevytvořilo).

**12. Jazyk slovíček se při importu volí (D22, rozhodnuto, opravené znění).** První řádek S31 „Jazyk slovíček“ je **ovládací prvek** (segment English | Deutsch, předvyplněný aktivním jazykem, jde zvolit i druhý). Průvodce nabízí sady zvoleného jazyka a nová sada vznikne ve zvoleném jazyce. Změna jazyka přefiltruje seznam sad a smaže dosud zvolený výběr. Po zahájení skenu je jazyk pevný (S34, S35, S36 jen štítek) a nelze ho změnit, ani mísit dva jazyky v jednom importu (09, kap. 2). **Import aktivní jazyk zařízení nemění.** Důsledek: S36 řeší případ „uloženo do jiného jazyka než aktivního“ nabídkou „Přepnout na … a procvičit“ vedle „Hotovo“.

#### 3.8.1 Odchylky od zadaného návrhu a důvody

| # | Zadaný návrh | Moje úprava | Důvod |
|---|---|---|---|
| 1 | Přepínač na každé hlavní obrazovce | Jen na kořenech, hlubší obrazovky mají štítek C3 | Detail patří jednomu jazyku (3.8, bod 3). |
| 2 | iPad: nahoře v sidebaru | Navíc: když je sidebar skrytý (na výšku, sbalený), přepínač je v záhlaví obsahu | Jinak by na iPadu na výšku nebyl jazyk vidět vůbec; požadavek „vždy vidět“. |
| 3 | Segment „English | Deutsch“ | Při největších velikostech písma tlačítko „Jazyk: Deutsch ▾“ → sheet S02 | Segment s podřádkem se při AX velikostech nevejde; sheet má velké cíle. |
| 4 | „k procvičení: N“ pod neaktivním jazykem | Pořadí segmentů je pevné (English vlevo); řádek je součástí cíle ťuknutí neaktivního segmentu (US-26); N je velikost dnešního kola, ne resta; při 0 se skryje; strop podle Q19 | Stabilní poloha pro paměť ruky; nespouští tlak (3.5, US-08). |
| 5 | Barevný akcent jazyka | Omezený na přepínač, štítek a linku; v testu vypnutý | Barvy „správně / skoro / zatím ne“ nesmí mít konkurenci. |
| 6 | Prázdný stav: „Přidat sadu“ a „Přepnout na …“ | Přidána akce „Vyfotit slovíčka“ (US-26 ji uvádí) a „Přepnout na …“ se nabídne jen tehdy, když druhý jazyk něco má | Žádná slepá ulička; shoda s US-26. |
| 7 | Vytvoření sady a import: jazyk je první povinná volba a zobrazí se na kontrolní obrazovce | Po D22 (opravené znění) je jazyk v S31 první řádek a ovládací prvek (segment, předvyplněný aktivním jazykem); po zahájení skenu zamčený, v S34, S35, S36 štítek | D22: jazyk se při importu vždy volí; import aktivní jazyk nemění. |
| 8 | (nebylo řečeno) | Zrušen filtr „Vše“ v S50 a segment jazyka v S40 a S41 | Filtr „Vše“ by míchal jazyky (D18, D20); segment jazyka by duplikoval C2. |
| 9 | (nebylo řečeno) | Úvod: sloučeny původní kroky 1 a 2, aby se s volbou jazyka vešly do 3 kroků; volba jazyka nelze přeskočit | US-01 (max. 3 kroky) a D18 (aktivní jazyk musí být vždy dán). |
| 10 | (nebylo řečeno) | Nastavení: věta „Platí pro angličtinu i němčinu.“ a S64 uvádí oba jazyky | Nastavení je společné, smazání se týká obou. |
| 11 | D19: chybnou sadu smazat a vytvořit znovu | Jedna nabídka „Smazat a vytvořit znovu“ v S22 místo ruční cesty (smazat, přepnout jazyk, otevřít „+“, znovu zadat název) | Zkrátí opravu; nevytváří akci „Změnit jazyk“. Slova se nepřenášejí a text to říká předem. Aktivní jazyk se nepřepíná, průvodce se otevře s předvybraným správným jazykem. |

---

## 4. Obrazovky

U každé obrazovky: story, účel, wireframe iPhone, iPad (na výšku i na šířku), stavy. Stavový inventář má pevných šest řádků (prázdno, načítání, chyba, offline, dlouhý obsah, sync konflikt) a podle potřeby další. Čtyři kořenové obrazovky s přepínačem jazyka (S10, S20, S40, S50) mají navíc řádek „Aktivní jazyk bez dat“. „—“ znamená, že stav na obrazovce nenastává (s důvodem).

### 4.1 První spuštění a aktivní jazyk

#### S01 Úvod — US-01, US-26

**Účel:** syn hned ví, co dělat, a zvolí jazyk, ve kterém začne. Žádná registrace, přihlášení ani souhlas s analytikou (žádná se nesbírá, R5). **Krok 1 je volba jazyka (D18, US-01) a nelze ho přeskočit**, protože aktivní jazyk musí být vždy dán a aplikace ho za syna nevybere. Kroky 2 a 3 jsou nezávislé.

**Počet kroků:** US-01 dovoluje max. 3 kroky včetně volby jazyka. Proto jsem původní kroky 1 a 2 (co aplikace dělá, jak se zkouší) sloučil do jednoho (nový krok 2). Krok 3 (iCloud) se ukáže jen když iCloud není dostupný. Nejmenší podoba úvodu jsou kroky 1 a 2.

**iPhone (na výšku), krok 1 z max. 3, volba jazyka:**
```
┌────────────────────────────────┐
│                                │   žádné „Přeskočit“
│                                │
│   Slovíčka ze sešitu           │
│   rovnou do telefonu.          │   velký titulek
│                                │
│   Co se budeš učit nejdřív?    │
│                                │
│ ┌────────────────────────────┐ │
│ │ English                    │ │   tlačítko ≥ 56 pt, ťuknutí = volba + krok 2
│ │ angličtina                 │ │
│ └────────────────────────────┘ │
│ ┌────────────────────────────┐ │
│ │ Deutsch                    │ │
│ │ němčina                    │ │
│ └────────────────────────────┘ │
│                                │
│   Druhý jazyk můžeš přidat     │
│   kdykoli později. Procvičuje  │
│   se vždy jen jeden jazyk,     │
│   jazyky se nemíchají.         │
│   ● ○ ○ (jen při více krocích) │
└────────────────────────────────┘
```
- Ťuknutí na jazyk ho zvolí a rovnou přejde na krok 2 (jedno ťuknutí, bez dalšího „Pokračovat“). Žádný jazyk není předvybraný (nehádáme).
- Bez barevného akcentu jazyka: barvy se přiřadí až po volbě.
- Vlajky ne. Pod názvem v jazyce samém je česky „angličtina“ / „němčina“, ať je jasné, co je co (a pro VoiceOver).

Krok 2 (jak to funguje; sloučené původní kroky 1 a 2):
- Titulek: „Vyfoť stránku, pak krátce procvičuj.“
- Text: „Stránku ze sešitu přečtu přímo v tomhle zařízení a ty jen zkontroluješ, jestli jsem to přečetl dobře. Pak tě zkouším po malých kolech: nové slovo si vybereš z nabídky, to, co už znáš, napíšeš. Když se spleteš, ukážu ti správnou odpověď a slovo se ti brzy vrátí. Žádné časovače ani trestné body.“
- Tlačítka: „Vyfotit slovíčka“ (primární, 56 pt), „Přeskočit“ (sekundární; skočí na S10 v zvoleném jazyce), vlevo nahoře „‹ Jazyk“ (zpět na krok 1, kdyby se syn přeťukl). Nad titulkem štítek C3 „DE · němčina“.

Krok 3 (zobrazí se jen když iCloud není dostupný, US-01 + US-11):
- Titulek: „iCloud teď není zapnutý.“
- Text: „Aplikace funguje i tak, jen slovíčka a výsledky budou zatím jen v tomhle zařízení. Až se k iCloudu přihlásíš, přenesou se samy.“
- Tlačítka: „Pokračovat“ (primární), „Otevřít Nastavení“.

Když iCloud je dostupný, krok 3 se nezobrazí. Pokud by aplikace v buildu ještě nesynchronizovala (build 1 podle `04-story-mapa.md`), text kroku 2 nemluví o iCloudu vůbec.

**iPad:** stejný obsah v centrovaném bloku (max. šířka 560 pt), svisle na střed, na šířku i na výšku beze změny; žádný sidebar, dokud úvod neskončí. Tlačítka jazyka vedle sebe (2 × 1), pokud se vejdou bez zalomení, jinak pod sebou.

**Žádná žádost o fotoaparát se tu neobjevuje** (US-01): přijde až v S32 ve chvíli, kdy ji import potřebuje.

| Stav | Chování |
|---|---|
| První spuštění | To je tato obrazovka. Po „Vyfotit slovíčka“ nebo „Přeskočit“ (krok 2) už se nikdy nezobrazí (kromě po US-20, kdy začíná opět krokem 1). |
| Prázdno | — (obrazovka je sama „prázdný“ stav aplikace). |
| Načítání | Zjišťování stavu iCloudu neblokuje; krok 3 se případně přidá až po zjištění. Volba jazyka na nic nečeká. |
| Chyba | Stav iCloudu nezjištěn: chováme se jako „dostupný“, nic se nehlásí (správné hlášení je v S61). Uložení volby jazyka selže: jazyk platí pro tuto relaci, nic se nehlásí; při příštím spuštění se aplikace zeptá znovu (G19). |
| Offline | Beze změny, úvod síť nepotřebuje. |
| Dlouhý obsah | Dynamic Type velký: text se rolue, tlačítka jazyka jsou pod sebou, tlačítko „Vyfotit slovíčka“ zůstává ukotvené dole. |
| Sync konflikt | — |
| Nevybral jazyk | Nelze pokračovat: na kroku 1 není jiné tlačítko než dvě volby jazyka (žádné „Přeskočit“, žádný „Další“). |
| Nové zařízení s daty z iCloudu | Krok 1 se zobrazí stejně, protože aktivní jazyk je vlastnost zařízení (3.8, bod 5). Po volbě se v něm objeví sady, které sync přinesl; pokud dorazí později, objeví se samy. Druhý jazyk je přes přepínač. |
| VoiceOver | Titulek, otázka „Co se budeš učit nejdřív?“, pak dvě tlačítka „angličtina“ a „němčina“. Po volbě fokus na titulek kroku 2. |

---

#### S02 Přepnutí jazyka — US-26 (nová obrazovka, P0)

**Účel:** syn (nebo otec) ví, v jakém jazyce je, a přepne ho jedním ťuknutím na nejvyšší úrovni. Nic se nemíchá (D18). S02 není jedna plná stránka, ale **tři podoby téže akce**, které sdílejí pravidla z 3.8:

| Podoba | Kde | Kdy |
|---|---|---|
| A. Přepínač C2 v záhlaví | Kořeny S10, S20, S40, S50; na iPadu nahoře v sidebaru | Výchozí. Jedno ťuknutí na neaktivní segment. |
| B. Sheet „Jazyk“ (iPad: popover 360 pt) | Otevírá ho tlačítko „Jazyk: Deutsch ▾“ | Místo A při největších velikostech písma, kdy segment s podřádkem nevejde (domněnka: od AX1; práh změřit). |
| C. Tlačítko „Přepnout na …“ | Prázdné stavy S10, S20, S40, S50, S51 | Když se obrazovka v aktivním jazyce nemá čím naplnit, ale druhý jazyk něco má. Přepne přímo, bez sheetu. |

S02 přepíná **aktivní jazyk aplikace**. Nemění jazyk žádné sady (D19) a nic nepřesouvá. Aktivní jazyk se jinde nepřepíná; import ho nemění, jen v S36 nabídne zkratku „Přepnout na … a procvičit“.

**A. iPhone — přepínač pod titulkem (příklad: kořen Sady, aktivní němčina, v angličtině je 7 slov k procvičení):**
```
┌────────────────────────────────┐
│ Sady · němčina             [+] │   titulek říká jazyk (nadpis pro VoiceOver)
│ ┌──────────┬─────────────────┐ │
│ │ English  │ ▐  Deutsch   ▌  │ │   segmenty ≥ 44 pt, pořadí pevné
│ └──────────┴─────────────────┘ │   aktivní: zvýšený tvar + tučně + akcent jazyka
│   k procvičení: 7              │   pod neaktivním; zvýrazněná je jen informace
│                                │
│ Deutsch 1              34 slov │
│ v procvičování                 │
│ Deutsch 2              21 slov │
├────────────────────────────────┤
│ Procvičit Sady Slovesa Pokrok Nastavení│
└────────────────────────────────┘
```
- Ťuknutí na „English“ nebo na řádek „k procvičení: 7“ jazyk přepne. Test se nespustí (US-26).
- Řádek se nezobrazí, když v neaktivním jazyce nic nečeká. Když neaktivní jazyk nemá žádnou sadu, je tam místo čísla „zatím žádné sady“.
- Řádek je malý, v běžné barvě textu (ne červený, ne tučný); nemá ikonu ani odznak. Jak se zobrazí velký počet („20+“, nebo přesně), je otevřená otázka Q19.

**B. iPhone — velké písmo (AX), tlačítko a sheet:**
```
┌────────────────────────────────┐        ┌────────────────────────────────┐
│ Sady · němčina             [+] │        │ Jazyk                  Zavřít  │
│ [ Jazyk: Deutsch           ▾ ] │  ───►  │ Procvičuješ vždy jen jeden     │
│                                │        │ jazyk.                         │
│ Deutsch 1              34 slov │        │ ┌────────────────────────────┐ │
│ …                              │        │ │ English                    │ │
└────────────────────────────────┘        │ │ 3 sady · 54 slov           │ │
                                          │ │ k procvičení: 7            │ │
                                          │ ├────────────────────────────┤ │
                                          │ │ ◉ Deutsch          Aktivní │ │
                                          │ │ 2 sady · 55 slov           │ │
                                          │ └────────────────────────────┘ │
                                          │ Slova, plán opakování i pokrok │
                                          │ jsou pro každý jazyk zvlášť.   │
                                          └────────────────────────────────┘
```
Ťuknutí na řádek jazyka ho přepne a sheet se zavře (bez dalšího „Hotovo“). Řádky ≥ 56 pt. Počty sad a slov jsou data z modelu (jazyk dědí slovo ze sady). Počty jsou po jazycích vedle sebe, **bez součtu** (D20).

**A. iPad na šířku (sidebar viditelný, tři sloupce):**
```
┌─────────────────┬────────────────┬───────────────────────────────┐
│ ┌────────┬────┐ │ Sady · němčina │ Deutsch 1  [DE · němčina]     │
│ │English │▐De▌│ │           [+]  │ 34 slov            [Upravit]  │
│ └────────┴────┘ │ Deutsch 1      │ [ + Přidat slova ]            │
│  k procvičení: 7│ Deutsch 2      │ das Kind        dítě     [♪]  │
│ ─────────────── │                │ der Tisch       stůl     [♪]  │
│ Procvičit       │                │ …                             │
│ Sady          ● │                │                               │
│ Slovesa         │                │                               │
│ Pokrok          │                │                               │
│ Nastavení       │                │                               │
└─────────────────┴────────────────┴───────────────────────────────┘
```
Nahoře v sidebaru je jen přepínač C2 (ne šestý cíl). Titulek obsahu říká jazyk i tady, protože sidebar jde sbalit. Sbalený sidebar: přepínač je v záhlaví sloupce se seznamem jako na iPhonu.

**A. iPad na výšku (sidebar skrytý, dva sloupce):**
```
┌──────────────────────────────────────────────────────┐
│ ≡   Procvičit  Sady  Slovesa  Pokrok  Nastavení      │   horní lišta (5 cílů)
├───────────────────────┬──────────────────────────────┤
│ Sady · němčina    [+] │ Deutsch 1  [DE · němčina]    │
│ [ English | Deutsch ] │ 34 slov            [Upravit] │
│   k procvičení: 7     │ das Kind        dítě    [♪]  │
│ Deutsch 1             │ …                            │
│ Deutsch 2             │                              │
└───────────────────────┴──────────────────────────────┘
```
**Mikrotexty:**

| Kde | Text |
|---|---|
| Segmenty | „English“ / „Deutsch“ (text v jazyce samém, bez vlajek) |
| Údaj u neaktivního jazyka | „k procvičení: 7“ (strop podle Q19) |
| Neaktivní jazyk bez sad | „zatím žádné sady“ |
| Tlačítko při velkém písmu | „Jazyk: Deutsch“ (rozbalovací šipka) |
| Sheet: nadpis, podnadpis | „Jazyk“, „Procvičuješ vždy jen jeden jazyk.“ |
| Sheet: řádek jazyka | „English“; „3 sady · 54 slov“; „k procvičení: 7“; aktivní: „Aktivní“ |
| Sheet: patička | „Slova, plán opakování i pokrok jsou pro každý jazyk zvlášť. Nic se nemíchá.“ |
| VoiceOver, přepínač | „Aktivní jazyk: němčina.“ |
| VoiceOver, neaktivní segment | „Angličtina, neaktivní. K procvičení 7 slov. Dvojitým klepnutím přepnout.“ |
| VoiceOver, po přepnutí | „Aktivní jazyk: angličtina.“ |
| VoiceOver, sheet | „Jazyk. Angličtina, 3 sady, 54 slov, k procvičení 7 slov, neaktivní. Němčina, 2 sady, 55 slov, aktivní.“ |

**Pravidla přepnutí** jsou v 3.8, bod 4. Přepínač v testu není (bod 6).

| Stav | Chování a text |
|---|---|
| První spuštění | Žádný přepínač před S01 krokem 1. Po volbě jazyka je v druhém jazyce „zatím žádné sady“ a segment je ťuknutelný: vede do prázdného stavu druhého jazyka (T19). |
| Prázdno (jazyk bez sad) | Přepnutí na jazyk bez sad vede do prázdného stavu dané obrazovky (S10, S20, S40, S50). Přepínač zůstává použitelný, aby se šlo vrátit (US-26). |
| Načítání | — (lokální data; přepnutí je okamžité). |
| Chyba | Uložení zvoleného jazyka selže: jazyk platí pro tuto relaci, nic se nehlásí. Po restartu se aplikace vrátí k poslednímu uloženému nebo, pokud žádný není, zeptá se jako v S01 (G19). |
| Offline | Beze změny (US-26, US-13). |
| Dlouhý obsah | Velké písmo: tlačítko „Jazyk: Deutsch ▾“ místo segmentu; řádek „k procvičení“ je jen v sheetu. Dlouhý počet slov se zalomí. |
| Sync konflikt | Aktivní jazyk se podle návrhu nesynchronizuje (3.8, bod 5), takže nekonfliktuje. Pokud by se synchronizoval (Q18), vyhrála by pozdější volba a nepřepisovala by běžící test. |
| Uprostřed testu | Přepínač neexistuje. V záhlaví testu je štítek C3. Odchod: „Skončit“ → S15 → S16 → „Hotovo“. |
| Otevřený sheet / popover | Přepínač je pod nimi nedostupný; jazyk se přepíná až po zavření. |
| Uprostřed importu | Přepínač aktivního jazyka není dostupný (průvodce je modální). Jazyk slovíček se volí v S31 a aktivní jazyk nemění (D22). |
| Rozepsaná kontrola importu (G6) | Patří jazyku své sady. Banner „Máš rozdělanou kontrolu slov“ se ukáže v jeho jazyce. |
| Nenapsal nic / nevybral nic | — |

---

### 4.2 Procvičit a test

#### S10 Procvičit — start — US-05, US-06, US-08, US-04, US-26

**Účel:** jedno ťuknutí k procvičování v aktivním jazyce. Ukazuje jen aktuální nabídku, ne resty (US-08). Kolo se skládá jen ze slov aktivního jazyka.

**iPhone:**
```
┌────────────────────────────────┐
│ Procvičit · němčina            │   velký titulek (říká jazyk)
│ ┌──────────┬─────────────────┐ │
│ │ English  │ ▐  Deutsch   ▌  │ │   C2
│ └──────────┴─────────────────┘ │
│   k procvičení: 7              │
│ ┌────────────────────────────┐ │
│ │ Dnešní kolo                │ │
│ │ 12 slov · z toho 4 nová    │ │   data z plánovače (US-08)
│ │                            │ │
│ │ Deutsch 1, Deutsch 2     › │ │   → S11 (jen sady němčiny)
│ │ Směr: oba                  │ │
│ │                            │ │
│ │ [         Začít          ] │ │   primární, 56 pt
│ └────────────────────────────┘ │
│                                │
├────────────────────────────────┤
│ Procvičit Sady Slovesa Pokrok Nastavení│
└────────────────────────────────┘
```
**iPad na výšku:** karta „Dnešní kolo“ centrovaná, max. šířka 560 pt, nahoře ve volném prostoru; přepínač C2 nad ní v záhlaví obsahu (sidebar je skrytý); pod kartou zůstává prázdno (nepřidávat obsah bez story).
**iPad na šířku:** totéž, vedle sidebaru; přepínač je nahoře v sidebaru (viz S02), karta na střed.

**Stavy:**

| Stav | Chování a text |
|---|---|
| První spuštění / prázdná knihovna (v obou jazycích nic) | Místo karty: titulek „Pro {němčinu} tu zatím nemáš žádná slovíčka.“ Text: „Vyfoť stránku ze sešitu a za chvíli můžeš procvičovat.“ Tlačítko „Vyfotit slovíčka“ (→ S31 s jazykem předvyplněným aktivním). Sekundárně „Přidat sadu“ (→ S30) a „Přidat ručně“ (→ S23 přes S31). Bez „Přepnout na …“: druhý jazyk také nic nemá. |
| Aktivní jazyk bez sad, druhý jazyk sady má | Titulek: „Pro {němčinu} tu zatím nemáš žádná slovíčka.“ Text: „Přidej první sadu, nebo se přepni na {angličtinu}.“ Tlačítka: „Vyfotit slovíčka“ (primární), „Přidat sadu“, „Přepnout na {angličtinu}“ (textové). Bez tlačítka „Začít“, bez chybové hlášky (US-26). V C2 u druhého jazyka „k procvičení: N“, pokud tam něco čeká. |
| Slova jsou, ale není co opakovat | Titulek: „V {němčině} je na dnes hotovo.“ Text: „Žádné slovo teď nečeká na opakování. Další přijdou na řadu později.“ Tlačítko „Přidat nová slova“ (→ S30). Když v druhém jazyce něco čeká, pod tím jeden řádek „V {angličtině} je k procvičení: {n}.“ a textové tlačítko „Přepnout na {angličtinu}“ (jen nabídka, ne tlak; pokud ji learning-scientist posoudí jako tlak, odpadne, viz 3.5). **Mezera:** US-08 nedefinuje „volné procvičování navíc“ (kap. 6, G1); do té doby není tlačítko „Procvičit i tak“. |
| Žádná sada v procvičování | Karta: „Není vybraná žádná sada v {němčině}.“ Tlačítko „Vybrat sady“ (→ S11). „Začít“ nedostupné. |
| Načítání | Skeleton karty (bez spinneru, max. desetiny sekundy; plánovač běží lokálně). |
| Chyba | Plánovač nevrátil kolo: „Kolo se nepodařilo připravit. Zkus to prosím znovu.“ [ Zkusit znovu ] |
| Offline | Beze změny (US-13). |
| Dlouhý obsah | Názvy sad se zkrátí na „Deutsch 1 a další 2“; v S11 jsou celé. |
| Sync konflikt | Pokud mezitím druhé zařízení smazalo slova z plánovaného kola, kolo se připraví znovu bez hlášky. |
| Aktivní jazyk bez dat | Viz řádky „Aktivní jazyk bez sad“ a „Slova jsou, ale není co opakovat“ výše. |

---

#### S11 Co procvičit (sheet) — US-04, US-06, US-26

**Účel:** vybrat sady **aktivního jazyka** do testu a omezit směr (US-04: „Vyberu, které sady jdou do testu“; US-06: „Syn může omezit test na jeden směr“, předpoklad). Sady druhého jazyka se tu nikdy nezobrazují a nejsou k vybrání (D18). Sheet nemá přepínač jazyka; jazyk říká nadpis.

```
┌────────────────────────────────┐
│ Co procvičit · němčina    Hotovo│
│ Sady                           │
│  [x] Deutsch 1          34 slov│
│  [x] Deutsch 2          21 slov│
│  [ ] Lektion 4           9 slov│
│ Směr                           │
│  (•) Oba směry                 │
│  ( ) Jen z němčiny do češtiny  │
│  ( ) Jen z češtiny do němčiny  │
└────────────────────────────────┘
```
**iPhone:** sheet od spodu (půl až celá výška). **iPad:** popover od karty na S10 (na výšku i na šířku), šířka 360 pt. Výběr sad a směru se pamatuje zvlášť pro každý jazyk.

Výběr sad ovlivňuje, ze kterých sad se skládá kolo. Nevybrané sady téhož jazyka se ale smějí použít jako zdroj chybných možností (D21, S12); uživatel to nevidí a nemusí to nastavovat.

| Stav | Chování a text |
|---|---|
| Prázdno | Žádné sady v aktivním jazyce: „Pro {němčinu} nemáš zatím žádné sady.“ + „Vyfotit slovíčka“. |
| Načítání | — (lokální data). |
| Chyba | — |
| Offline | Beze změny. |
| Dlouhý obsah | Dlouhý seznam rolue, „Hotovo“ je ukotvené v záhlaví. Dlouhý název se zalomí na dva řádky. |
| Sync konflikt | — |

---

#### S12 Test — výběr z možností — US-05, US-06 (US-07 pro výběr: jen správně/špatně), US-26 (jazyk zamčený)

**Účel:** nová slova výběrem. Výchozí je **4 možnosti** (správná + 3 chybné); layout zvládá i méně a více (Q5 zbývá potvrdit u learning-scientist). **Chybné možnosti jsou z téže sady; má-li sada méně než 3 použitelné chybné možnosti, doplní se z jiných sad téhož jazyka, nikdy z druhého (D21, rozhodnuto).** Pořadí možností se míchá.

**iPhone:**
```
┌────────────────────────────────┐
│ Skončit            ▓▓▓▓░░░░ 4/12│   horní lišta
│ [DE] NĚMČINA → ČEŠTINA   Vyber │   štítek jazyka (C3, neutrální) + směr + způsob
│                                │
│ ┌────────────────────────────┐ │
│ │                            │ │
│ │       das Kind     [ ♪ ]   │ │   zadání, velké; ♪ ≥ 44 pt
│ │                            │ │
│ └────────────────────────────┘ │
│ ┌────────────────────────────┐ │
│ │ dítě                       │ │   možnost ≥ 56 pt
│ └────────────────────────────┘ │
│ ┌────────────────────────────┐ │
│ │ stůl                       │ │
│ └────────────────────────────┘ │
│ ┌────────────────────────────┐ │
│ │ okno                       │ │
│ └────────────────────────────┘ │
│ ┌────────────────────────────┐ │
│ │ zahrada                    │ │
│ └────────────────────────────┘ │
│ [Nevím]   [    Zkontrolovat   ]│   Zkontrolovat nedostupné bez výběru
└────────────────────────────────┘
```
- V testu **není přepínač jazyka**; štítek `[DE]` není tlačítko (VoiceOver: „Jazyk testu: němčina. Z němčiny do češtiny. Výběr z možností.“). Štítek je neutrální, bez barvy jazyka (3.8, bod 7).
- Ťuknutí na možnost ji jen **vybere** (zvýrazní); odpověď se vyhodnotí až po „Zkontrolovat“. Důvod: chybné ťuknutí se dá opravit a rytmus je stejný jako u psaní (stejné tlačítko na stejném místě). Odpovídá US-05: „Odpověď lze potvrdit kdykoli“.
- Směr česky → cizí: zadání je české slovo, možnosti jsou cizí slova (u němčiny včetně členu). [ ♪ ] se u zadání **nezobrazuje**, kdyby prozradilo odpověď; objeví se až v S14.
- Směr cizí → česky: [ ♪ ] u zadání je k dispozici (výklad: US-18 „Given slovo“; viz G7 v kap. 6).
- Štítek „Znovu“ vedle směru, pokud se slovo vrací v téže relaci po chybě. Jmenovatel postupu (12) se kvůli tomu nemění (jinak by lišta couvala).
- Dynamic Type velký: možnosti zůstávají v jednom sloupci, zadání se rolue; „Zkontrolovat“ ukotvené dole. Štítek jazyka se zalomí s ostatním záhlavím.
- Chybné možnosti z jiné sady téhož jazyka vypadají stejně jako ostatní: žádný štítek, žádná poznámka, žádný jiný vzhled (uživatel nemá důvod to řešit).

**iPad na výšku:** obsah centrovaný, max. šířka 560 pt, možnosti pod sebou (nebo mřížka 2 × 2 při krátkých textech do 2 řádků).
**iPad na šířku:** zadání vlevo (karta, šířka ~40 %), možnosti vpravo pod sebou, „Nevím“ a „Zkontrolovat“ pod možnostmi. Horní lišta přes celou šířku se štítkem jazyka. Sidebar se při testu skrývá (a s ním přepínač jazyka).

| Stav | Chování a text |
|---|---|
| Malá sada (méně než 3 použitelné chybné možnosti) | **Rozhodnuto (D21):** chybné možnosti se doplní z jiných sad téhož jazyka, nikdy z druhého. Uživatel to nepozná, žádná hláška, žádný jiný vzhled. Obrazovka se nemění. |
| Celý jazyk nemá dost slov (okrajový případ) | Počet možností klesne jen tehdy, když nestačí všechna slova aktivního jazyka. Pravidlo pro tento případ (kolik možností, kdy se místo výběru použije psaní) navrhne learning-scientist v `07` kap. 3; UI se přizpůsobí zadanému počtu možností. Žádná hláška. |
| Prázdno | — (test se nezačne bez slov, viz S10). |
| Načítání | — (lokální data); přechod mezi slovy bez spinneru. |
| Chyba | Odpověď se nepodařilo uložit: viz S14. |
| Offline | Beze změny. |
| Dlouhý obsah | Dlouhá možnost se zalomí (až 3 řádky), pak „…“ a plná délka se čte VoiceOverem i v S14. |
| Sync konflikt | Slovo mezitím smazané na druhém zařízení: kolo se bez hlášky přeskočí na další slovo. |
| Nic nevybral | „Zkontrolovat“ je nedostupné, pod ním drobný text: „Vyber odpověď, nebo ťukni na Nevím.“ |
| Jazyk | Zamčený po celou relaci; změna jen po skončení (3.8, bod 6). |

---

#### S13 Test — psaní — US-05, US-06, US-07, US-26 (jazyk zamčený)

**Účel:** známá slova psaním. Hlavní návrhová otázka je prostor nad klávesnicí.

**Rozpočet výšky, iPhone na výšku (domněnka, změřit):**
- 393 × 852: nad klávesnicí zbývá zhruba 520 pt. Lišta (44) + štítky (28) + zadání (110) + pole (56) + lišta znaků (44) + tlačítka (56) + rozestupy (~60) = ~400 pt. Vejde se s rezervou. Štítek jazyka (C3) je ve stejném řádku jako směr, takže výšku nezvyšuje.
- 375 × 667 (kontrolní): nad klávesnicí zbývá zhruba 340–380 pt. Stejný součet je těsný. Proto platí pravidlo: **zadání se smí zmenšit (min. 1 řádek) nebo rolovat, pole + lišta znaků + tlačítka jsou ukotvené nad klávesnicí a nikdy se neposouvají mimo obrazovku.** Při Dynamic Type nad „Large“ se karta zadání mění v rolovatelný blok. Pro anglickou odpověď lišta znaků odpadá (3.4), takže v těch otázkách je rozpočet volnější.

```
┌────────────────────────────────┐
│ Skončit            ▓▓▓▓▓░░░ 5/12│
│ [DE] ČESKY → NĚMČINA   Napiš   │
│ ┌────────────────────────────┐ │
│ │ dítě                       │ │   zadání (smí se zmenšit / rolovat)
│ └────────────────────────────┘ │
│ ┌────────────────────────────┐ │
│ │ Napiš německy…             │ │   pole, autofokus, ≥ 56 pt
│ └────────────────────────────┘ │
│  ä   ö   ü   ß                 │   lišta znaků (podle jazyka odpovědi)
│ [Nevím]   [    Zkontrolovat   ]│   nad klávesnicí
├────────────────────────────────┤
│ ░░░░░░ KLÁVESNICE (systém) ░░░░│   Return = „Zkontrolovat“
└────────────────────────────────┘
```
- [ ♪ ] u zadání: jen u směru cizí → česky (stejné pravidlo jako S12). U směru česky → cizí **není**, protože by prozradil odpověď.
- Zástupný text v poli: „Napiš německy…“ / „Napiš anglicky…“ / „Napiš česky…“ podle směru.
- „Zkontrolovat“ je nedostupné, dokud pole neobsahuje znak jiný než mezeru. Pod ním: „Napiš odpověď, nebo ťukni na Nevím.“ (VoiceOver přečte při fokusu i důvod nedostupnosti.)
- Nenapsal nic (dlouho nic nedělá): nic se neděje. Žádný časovač, žádná výzva (princip bez tlaku).
- Klávesnice se v následující otázce otevře sama. Pokud je následující otázka výběr, klávesnice se zavře.
- Návrh nenavrhuje nápovědu (první písmeno, počet písmen); nemá story a ovlivňovala by hodnocení (G3). Místo pro ni v layoutu je mezi zadáním a polem, ale není využité.

**iPad na výšku (834 × 1194):** centrovaný sloupec max. šířky 560 pt. Klávesnice zabírá zhruba 400 pt, nad ní zůstává ~700 pt: zadání může být výrazně větší (velký titulek), vše ostatní stejně jako na iPhonu.

**iPad na šířku (1194 × 834):** klávesnice zabírá zhruba 350–400 pt, nad ní zůstává ~430–480 pt. Layout:
```
┌───────────────────────────────────────────────────────────┐
│ Skončit                                   ▓▓▓▓▓░░░░░░ 5/12│
│                [DE] ČESKY → NĚMČINA      Napiš            │
│            ┌─────────────────────────────────┐            │
│            │ dítě                            │            │
│            └─────────────────────────────────┘            │
│            ┌─────────────────────────────────┐            │
│            │ Napiš německy…                  │            │
│            └─────────────────────────────────┘            │
│              ä  ö  ü  ß     [Nevím] [Zkontrolovat]        │
├───────────────────────────────────────────────────────────┤
│                   KLÁVESNICE (systém)                     │
└───────────────────────────────────────────────────────────┘
```
Sloupec max. 640 pt, centrovaný. Lišta znaků a tlačítka v jednom řádku.

| Stav | Chování a text |
|---|---|
| Prázdno | — |
| Načítání | — |
| Chyba | Uložení odpovědi selhalo: viz S14. |
| Offline | Beze změny. |
| Dlouhý obsah | Dlouhé zadání (fráze „to look forward to something“): zalomí se, karta zadání rolue. Dlouhá odpověď: pole se zalomí až na 3 řádky, dál rolue. |
| Sync konflikt | Jako S12. |
| Nenapsal nic | Viz výše: „Zkontrolovat“ nedostupné, „Nevím“ aktivní. |
| Nevlastní klávesnice | Syn nemá českou/německou klávesnici: lišta znaků pokryje diakritiku. Pokud někdo zadá znak mimo, vyhodnocení pravidly US-07 (diakritika = skoro správně). |
| Přepnutí do jiné aplikace | Rozepsaný text i otázka zůstanou. Jazyk testu se nemění. |

---

#### S14 Vyhodnocení odpovědi — US-07, US-08, US-10, US-18

**Účel:** říct, jak to dopadlo, ukázat správný tvar a pokračovat. Žádné srážky bodů, žádná červená. Vyhodnocení probíhá lokálně a deterministicky (US-07). Záhlaví má stejný štítek jazyka jako S12/S13; členy a hlášky o přehlásce platí jen v němčině, v angličtině se nezobrazují.

**Struktura (společná pro všechny varianty):**
1. Zadání (zmenšené, pro kontext).
2. Karta výsledku: ikona + slovo stavu, jedna věta „co přesně“.
3. Řádky „Napsal jsi“ / „Tvoje volba“ a „Správně“ (s [ ♪ ]).
4. Další uznávané významy (Q4: uznán kterýkoli), pokud existují.
5. Tlačítko „Další“ na stejném místě jako „Zkontrolovat“ (stejný rytmus). Return na externí klávesnici = „Další“.

**iPhone — skoro správně (překlep), psaní:**
```
┌────────────────────────────────┐
│ Skončit            ▓▓▓▓▓░░░ 5/12│
│ [DE] ČESKY → NĚMČINA           │
│ dítě                           │
│ ┌────────────────────────────┐ │
│ │ [~]  Skoro to máš          │ │
│ │ Dvě písmena se prohodila.  │ │
│ │                            │ │
│ │ Napsal jsi    das Kn̲i̲d      │ │   zvýrazněná písmena: tučně + podtržení
│ │ Správně       das Kind [♪] │ │
│ └────────────────────────────┘ │
│ [            Další           ] │
└────────────────────────────────┘
```
Klávesnice se při vyhodnocení buď skryje, nebo zůstane (cíl: žádné cukání mezi dvěma psanými otázkami, ověří ux-verifier na zařízení). Tlačítko „Další“ je v obou případech v dosahu palce.

**Varianty a mikrotexty (skutečné znění):**

*Správně* (střídat 3 znění, ať se neopakuje pořád totéž): „Správně.“ / „Sedí.“ / „Přesně tak.“
- Psaní, víc významů: „Správně. Dá se říct i: mládě.“
- Výběr: vybraná možnost zvýrazněná zeleně s ikonou, ostatní se ztlumí.

*Skoro správně (US-07):*
| Důvod | Věta pod nadpisem „Skoro to máš“ |
|---|---|
| Překlep, záměna znaků | „Dvě písmena se prohodila.“ |
| Překlep, vynechání | „Chybí jedno písmeno.“ |
| Překlep, přidání | „Jedno písmeno je navíc.“ |
| Chybí/špatná česká diakritika | „Pozor na diakritiku: chybí háček nebo čárka.“ + správný tvar |
| Německá přehláska (jen němčina) | „Pozor na přehlásku: tady patří ü.“ (písmeno dle případu) |
| `ß` vs. `ss` (jen němčina) | „Tady se píše ß, ne ss.“ |
| Špatný člen u správného slova (jen němčina) | „Slovo máš dobře, jen člen je jiný: der Tisch, ne die Tisch.“ (správný člen jako první, tvar „Správně“ ukáže celý) |
| Chybí velké písmeno u německého podstatného jména (jen němčina) | „Německá podstatná jména se píšou s velkým písmenem: Tisch.“ |

*Člen chybí (jen němčina; US-07: vyzvat k doplnění, nepočítat jako chybu):* karta nemá stav a odpověď se nehodnotí, dokud syn člen nedoplní:
```
│ Slovo sedí. Ještě člen:        │
│ [  der  ] [  die  ] [  das  ]  │   3 velká tlačítka ≥ 56 pt
```
Text: „Slovo sedí. Ještě člen: der, die, nebo das?“ Po volbě se vyhodnotí celá odpověď (správně / skoro: špatný člen). Tlačítko „Nevím“ zde není (slovo už je správně).

*Zatím ne (špatně):*
- Nadpis: „Tohle ještě nesedí.“ (alternativně „Zatím ne.“)
- Věta: „Správně je: das Kind.“ + [ ♪ ]
- Pod tím: „Brzy to zkusíme znovu.“ (platí pro obě větve US-08: vrátí se v téže relaci nebo další den; vědomě neslibuji kdy)
- Bez červené, bez „X“. Ikona „znovu“ v modrofialovém kruhu.

*Nevím:*
- Nadpis: „Nevadí.“
- Věta: „Správně je: das Kind.“ + [ ♪ ]; pod ním „Brzy to zkusíme znovu.“
- Vyhodnotí se jako „špatně“ (G4, potřebuje potvrzení learning-scientist).

*Výběr z možností, zvolil špatně:*
- Nadpis: „Zatím ne.“ Vybraná možnost je označena „Tvoje volba“ (neutrální obrys, ne červená), správná „Správně“ (zelená, ikona).
- Věta: „Správně je: dítě.“ + [ ♪ ] (cizí slovo, ať už je to zadání nebo odpověď).

**Ukládání (US-10):** odpověď se uloží před zobrazením vyhodnocení. Pokud uložení selže, vyhodnocení se zobrazí a pod kartou je jemný řádek: „Tuhle odpověď se zatím nepodařilo uložit. Zkusím to znovu.“ Při opakovaném selhání (3×, návrh) sheet: „Ukládání teď nefunguje. Zkontroluj, jestli je v zařízení volné místo. Dosavadní odpovědi jsou v pořádku.“ [ Zkusit znovu ] [ Skončit ].
> Pozn.: „dosavadní odpovědi jsou v pořádku“ lze tvrdit jen tehdy, když to architekt zaručí; jinak věta odpadne (požadavek, kap. 5).

**iPad:**
- Na výšku: centrovaný sloupec stejně jako S13; karta výsledku větší, „Další“ ukotvené dole.
- Na šířku: dva sloupce vedle sebe pod zadáním: vlevo „Napsal jsi“, vpravo „Správně“ (rozdíl písmen je vidět v jednom pohledu). „Další“ pod nimi na střed.

| Stav | Chování a text |
|---|---|
| Prázdno | — |
| Načítání | — (lokální deterministické vyhodnocení). |
| Chyba | Uložení selhalo: viz výše. |
| Offline | Beze změny. |
| Dlouhý obsah | Dlouhý správný tvar se zalomí, „Napsal jsi“ a „Správně“ jsou pod sebou. Víc významů: zobrazí se maximálně 3, zbytek „+ další 2“ (ťuknutím rozbalit). |
| Sync konflikt | — (vyhodnocení se dělá lokálně nad slovem z kola). |
| VoiceOver | Focus na kartu výsledku; přečte stav, důvod a správný tvar; potom „Další“. |

---

#### S15 Skončit procvičování (sheet) — US-05, US-09, US-10

```
┌────────────────────────────────┐
│ Skončit teď?                   │
│ Všechny dosavadní odpovědi     │
│ jsou uložené. Zbylá slova se   │
│ ti nabídnou příště.            │
│ [ Pokračovat ]                 │   primární
│ [ Skončit a ukázat souhrn ]    │
└────────────────────────────────┘
```
Pokud syn dosud neodpověděl na žádné slovo: sheet nevyskakuje, „Skončit“ rovnou vrátí na S10 bez souhrnu (v témže jazyce).
Formulace „Zbylá slova se ti nabídnou příště“ je pravda jen tehdy, když plánovač nepřesune termíny nezodpovězených slov; **požadavek na plánovač (kap. 5).**
Sheet o jazyce nic neříká: po souhrnu je cesta k přepnutí jazyka až na S10 (3.8, bod 6).

| Stav | Chování |
|---|---|
| Ostatní | Prázdno, načítání, chyba, offline, dlouhý obsah, sync konflikt: — (jednoduchý sheet bez dat). |

---

#### S16 Souhrn po pokusu — US-09 (varianta sloves: US-17; z historie: US-14), US-26 (jazyk v záhlaví)

**Účel:** začít tím, co se povedlo, ukázat, co procvičit, a nikam netlačit. Souhrn se týká jednoho pokusu a tedy jednoho jazyka; nikdy nesčítá oba jazyky (D20).

**iPhone:**
```
┌────────────────────────────────┐
│ Souhrn · němčina               │
│                                │
│ Hned se ti vybavilo 8 z 12     │   velký titulek, vždy začíná tím dobrým
│ slov.                          │
│ O 2 víc než minule.            │   jen když je víc nebo stejně
│                                │
│ ┌────────────────────────────┐ │
│ │ ████████ ▒▒ ░░             │ │   tři úseky + popisky
│ │ 8 hned · 2 skoro · 2 zatím ne│ │   ne jen barvy
│ └────────────────────────────┘ │
│                                │
│ Na procvičení (4)              │
│  das Kind       dítě      [♪]  │
│  der Tisch      stůl      [♪]  │   ťuknutí na řádek → S23
│  …                             │
│  Zobrazit všech 4              │
│                                │
│ [ Ještě jednou chybná slova ]  │   primární (jen když jsou chybná)
│ [          Hotovo          ]   │   sekundární, plná šířka
└────────────────────────────────┘
```
**Pravidla textu:**
- Titulek podle výsledku (poměr počítá implementace, texty jsou tyto):
  - Vše hned: „Všech {n} slov se ti vybavilo hned.“
  - Aspoň jedno hned: „Hned se ti vybavilo {k} z {n} slov.“
  - Žádné hned, ale něco skoro: „{m} slov jsi měl skoro dobře. To už je blízko.“
  - Nic ani skoro: „Prošel jsi {n} slov. Správné odpovědi jsou níž, ať si je prohlédneš.“
- Záhlaví „Souhrn · {jazyk}“ (nominativ). Jazyk pokusu se nikdy nemění; „Ještě jednou chybná slova“ zůstane ve stejném jazyce.
- Srovnání „O {d} víc než minule.“ nebo „Stejně jako minule.“ jen při ≥ minule. Při horším výsledku řádek **vůbec není**. Při prvním pokusu řádek není a nezůstává po něm prázdné místo. **Zdroj dat:** předchozí pokus stejných sad v témže jazyce (US-10).
- „Na procvičení“ obsahuje slova „skoro“ a „zatím ne“ s **správnou odpovědí** (US-09). Zda se do „Ještě jednou chybná slova“ počítají i „skoro“, je předpoklad (G5): ano, obě skupiny.
- Předčasné ukončení: nad titulkem řádek „Skončil jsi po 5 z 12 slovech. Tohle je souhrn toho, co jsi stihl.“ (US-09).
- Bez chybných slov: „Ještě jednou chybná slova“ se nezobrazí, zůstane jen „Hotovo“.
- „Hotovo“ vrací na S10; žádné „Chceš pokračovat?“, žádný nátlak (US-09). Nenabízí „teď druhý jazyk“.
- Varianta sloves (US-17): hlavička „Nepravidelná slovesa · {jazyk}“, titulek „Tvary, které sedí: {k} z {m}.“; seznam „Na procvičení“ ukazuje sloveso a tvar, který nesedí (viz S43). Souhrn a historie slovíček a sloves se nemíchají.
- Z historie (S50): stejná obrazovka, nahoře datum („Neděle 2026-11-01“), bez tlačítka „Ještě jednou chybná slova“, pokud ho US-09 pro historii nepožaduje (G5b: nerozhodnuto, návrh: tlačítko zůstane, vyrobí nové kolo ze stejných slov). Historie je filtrovaná na aktivní jazyk, takže pokus je vždy v jazyce, který je právě aktivní.

**iPad na výšku:** centrovaný sloupec max. 560 pt, stejné pořadí. **iPad na šířku:** dva sloupce: vlevo titulek, rozložení úseků a tlačítka, vpravo seznam „Na procvičení“ (rolovatelný).

| Stav | Chování a text |
|---|---|
| První pokus | Bez srovnání. |
| Prázdno | 0 zodpovězených: souhrn se nezobrazí; návrat na S10 (S15). |
| Načítání | — (lokální data). |
| Chyba | Souhrn z historie se nepodařilo načíst: „Tenhle pokus se nepodařilo otevřít.“ [ Zpět ] |
| Offline | Beze změny. |
| Dlouhý obsah | Seznam „Na procvičení“ ukáže prvních 5, zbytek „Zobrazit všech {n}“. Dlouhé slovo se zalomí. |
| Sync konflikt | Pokus z druhého zařízení se po syncu objeví v historii samostatně; souhrn se nemění zpětně. |
| Přehrání | [ ♪ ] u každého cizího slova (C1, US-18). |

---

### 4.3 Import slovíček

Je to jeden průvodce pro fotku, knihovnu fotek a CSV. Mikrotexty a stavy odpovídají `09-obsah-a-import.md`, kap. 2 a 4. Obrázek se zpracuje v zařízení; po uložení se smaže (předpoklad Q8, výchozí chování v 09). **Jazyk slovíček se při importu vždy volí (D22)**: první řádek průvodce (S31) je segment English | Deutsch předvyplněný aktivním jazykem, nabízejí se sady zvoleného jazyka a nová sada vznikne v něm. Po zahájení skenu je jazyk pevný a je vidět jako štítek na každé další obrazovce (S34, S35, S36). Import aktivní jazyk zařízení nemění; po uložení do jiného jazyka S36 nabídne „Přepnout na … a procvičit“. Text, který vypadá jako druhý jazyk, se neuloží potichu (US-02, US-03).

#### S30 Přidat slova (volba zdroje) — US-02, US-19, US-04

```
┌────────────────────────────────┐
│ Přidat slova · němčina     ✕   │
│ [ Vyfotit stránku            ] │
│ [ Vybrat z fotek             ] │
│ [ Načíst soubor CSV          ] │   (US-19, P1)
│ [ Napsat ručně               ] │   → S23
└────────────────────────────────┘
```
**iPhone:** sheet od spodu, velké řádky ≥ 56 pt. **iPad:** popover od „+“ (na výšku i na šířku).
„Načíst soubor CSV“ se zobrazí jen, pokud je US-19 v buildu. Nadpis jazyk nenese; jazyk slovíček se volí až v S31 (první řádek, předvyplněný aktivním jazykem, D22). V toku „Smazat a vytvořit znovu“ (S22 → S24) se S31 otevře s předvybraným jazykem smazané sady.

| Stav | Chování |
|---|---|
| Fotoaparát nedostupný (např. zařízení bez kamery) | „Vyfotit stránku“ je šedé, řádek pod ním: „Fotoaparát na tomhle zařízení není. Vyber fotku z knihovny.“ |
| Ostatní | Prázdno, načítání, chyba, offline, dlouhý obsah, sync konflikt: — (statický výběr; vše funguje offline). |

---

#### S31 Kam uložit — US-02, US-04 (jazyk patří k sadě, D18, D19, D22; US-26 se importu netýká)

**Účel:** nechat vybrat **jazyk slovíček jako první řádek** a pod ním sadu **tohoto jazyka**, **před** snímkem (nápověda pro OCR a směr sloupců, 09 kap. 2.1). Jazyk určuje, jak se stránka přečte (čeština + zvolený jazyk, US-02). **Jazyk je tu ovládací prvek (D22)**: předvyplněný aktivním jazykem, jde zvolit i druhý. Volba **nemění aktivní jazyk zařízení**.

```
┌────────────────────────────────┐
│ Zrušit       Kam to uložit?    │
│ Jazyk slovíček                 │
│ [ English | Deutsch ]          │   první řádek; segment (ovládací prvek),
│ Importuješ do němčiny.         │   předvyplněný aktivním jazykem
│ Sada                           │
│ (•) Deutsch 1          34 slov │   jen sady zvoleného jazyka
│ ( ) Deutsch 2          21 slov │
│ ( ) Nová sada                  │
│     Název  [ Lektion 2       ] │   při „Nová sada“
│ [         Pokračovat         ] │
└────────────────────────────────┘
```
- **Jazyk slovíček je první řádek a ovládací prvek.** Segment English | Deutsch (bez vlajek), předvyplněný aktivním jazykem. Odpovídá zadání návrhu („jazyk je první povinná volba“) a D22 (3.8.1, řádek 7). Nad ním ani pod ním není žádné „přepni jazyk“: volba jazyka importu je samostatná věc vedle aktivního jazyka.
- **Změna jazyka** přefiltruje seznam sad na sady zvoleného jazyka a **smaže dosud zvolený výběr** (vybraná existující sada se zruší; výchozí je „Nová sada“, pokud zvolený jazyk žádnou sadu nemá, jinak není vybráno nic). Nová sada vznikne ve zvoleném jazyce. Sady obou jazyků nejsou nikdy v jednom seznamu.
- **Zámek:** po zahájení skenu (fotoaparát, výběr fotky nebo souboru) je jazyk pevný. V S34 a S35 je jen štítek (ne segment), v S36 text. Změnit ho jde jen zrušením importu a začátkem znovu. Do jednoho importu nejde míchat dva jazyky.
- **Import aktivní jazyk nemění.** Syn může při aktivní angličtině naimportovat němčinu; aktivní zůstane angličtina. Zkratka na přepnutí je až v S36.
- Při první cestě (žádné sady) je výchozí „Nová sada“. Název je povinný.
- **Jazyk existující sady se nemění (D19).** Žádná akce „Změnit jazyk sady“ neexistuje.
- „Pokračovat“ nedostupné, dokud není vybraná sada nebo vyplněný název.
- Zástupný text v názvu: „Např. Project 3 — Unit 2“ pro angličtinu, „Např. Deutsch 1 — Lektion 2“ pro němčinu.
- Pod seznamem při „Nová sada“: „Sada patří vždy jednomu jazyku. Později ho už nezměníš.“
- **Vstup z „Smazat a vytvořit znovu“ (S22, S24):** segment „Jazyk slovíček“ je předvybraný na správný jazyk (ten, který nemá smazaná sada; aktivní jazyk se nepřepíná), „Nová sada“ je vybraná a název je předvyplněný původním názvem (upravitelný). Nad jazykem řádek: „Zakládáš sadu znovu ve správném jazyce.“ Jazyk jde v S31 stále změnit.

**iPad:** formát (centrovaný sheet 540 pt); na šířku i na výšku stejný.

| Stav | Chování a text |
|---|---|
| Prázdno | Bez sad ve zvoleném jazyce: „Nová sada“ rozbalená; pod ní řádek „Pro {němčinu} zatím nemáš žádnou sadu.“ |
| Načítání | — |
| Chyba | Název prázdný: pod polem „Pojmenuj sadu, ať ji najdeš.“ Název už existuje: „Sada s tímhle názvem už je. Můžeš ji vybrat výše.“ (varování; duplicitní názvy nejsou zakázané, R4). Chyba „nevybrán jazyk“ nenastává, segment je vždy předvyplněný. |
| Offline | Beze změny. |
| Dlouhý obsah | Dlouhý seznam sad rolue; dlouhý název se zalomí. Dynamic Type velký: text pod jazykem se zalomí. |
| Sync konflikt | — |
| Chce jiný jazyk | Přepne segment „Jazyk slovíček“ (do zahájení skenu). Seznam sad se přefiltruje a dosavadní výběr se smaže (3.8, bod 12). Po skenu: „Zrušit“ a znovu. |
| VoiceOver | Segment čte česky: „Jazyk slovíček: němčina.“ (hodnota), po přepnutí „Jazyk slovíček: angličtina“, nikoli „Deutsch“; k ovládacímu prvku patří nápověda „Dvojitým klepnutím změníš jazyk slovíček. Aktivní jazyk se nezmění.“ Po změně VoiceOver oznámí „Sady v němčině“ a čte první sadu; ke každé sadě její počet slov. |

---

#### S32 Fotoaparát: vysvětlení a odmítnutí — US-02 (žádost podle US-01: až když je potřeba)

Zobrazí se před prvním systémovým dialogem o fotoaparátu:
- Titulek: „Fotoaparát na focení stránky“
- Text: „Fotoaparát používám jen na to, abys vyfotil stránku se slovíčky. Fotka se přečte přímo v tomhle zařízení, nikam se neposílá a po uložení slov se smaže.“
- Tlačítka: „Pokračovat“ (→ systémový dialog), „Vybrat fotku z knihovny“.

Když je přístup odmítnutý:
- Text: „Fotoaparát je vypnutý. Zapni ho v Nastavení, nebo vyber fotku z knihovny.“
- Tlačítka: „Otevřít Nastavení“, „Vybrat z fotek“.

Text „po uložení slov se smaže“ platí jen, když zůstane předpoklad Q8; jinak se mění.
**Výběr z knihovny fotek:** systémový výběr fotek, který zpravidla nepotřebuje obecné oprávnění ke knihovně (domněnka, ověřit). Syn nebo otec může vybrat víc stránek najednou; maximální počet určí architekt.

Skener stránky je systémové UI (domněnka: `VNDocumentCameraViewController`, 09 kap. 3.1) a nenavrhuji ho.

| Stav | Chování |
|---|---|
| Ostatní | Statická obrazovka; offline beze změny; dlouhý obsah: Dynamic Type zalomí text. |

---

#### S34 Čtení stránky (zpracování) — US-02

```
┌────────────────────────────────┐
│ Zrušit                         │
│                                │
│        (   spinner   )         │
│    Čtu stránku…                │
│    Stránka 2 ze 3              │   jen při více stránkách
│  Importuji do: Němčina ·       │   zamčený jazyk (štítek) a sada (US-02)
│  Deutsch 1                     │
│  Čtení běží přímo v zařízení,  │
│  internet není potřeba.        │
└────────────────────────────────┘
```
- Po delší době (návrh: 10 s) přibude řádek: „Trvá to déle než obvykle. Čtu dál, můžeš počkat nebo zrušit.“
- „Zrušit“ zastaví čtení a vrátí do S30; nic se neuloží.
- Řádek „Importuji do: {Němčina} · {název sady}“ je vidět před i po rozpoznání (US-02: cílový jazyk viditelný před rozpoznáním). Jazyk je tu už zamčený a je to jen štítek, ne ovládací prvek (D22).

| Stav | Chování a text |
|---|---|
| Načítání | Tato obrazovka sama. Neurčitý průběh (nevíme, kolik zbývá; nevymýšlím procenta). |
| Prázdno (nic nenalezeno) | „Na fotce jsem nenašel žádná slovíčka. Zkus ji vyfotit blíž, na rovném podkladu a při dobrém světle.“ [ Vyfotit znovu ] [ Napsat ručně ] (US-02). |
| Chyba | „Čtení se nepovedlo. Fotka je v pořádku, zkus to prosím znovu.“ [ Zkusit znovu ] [ Vyfotit znovu ] [ Napsat ručně ] |
| Offline | Beze změny (OCR v zařízení, R5). |
| Dlouhý obsah | Mnoho stránek: „Stránka {k} z {n}“. |
| Sync konflikt | — |
| Aplikace přešla na pozadí | Čtení pokračuje, nebo se po návratu restartuje (rozhodne architekt). Nic se neuloží. |

---

#### S35 Kontrolní obrazovka — US-03 (vstup z US-02 fotkou, z US-19 CSV; jazyk v záhlaví, D22)

**Účel:** jediné místo, kde se text z fotky mění. OCR se bude mýlit (R2), každý řádek jde opravit. Jazyk slovíček zvolený v S31 je v záhlaví vidět jako štítek a nejde tu změnit (D19, D22).

**Struktura:**
1. **Souhrn nahoře:** kolik dvojic aplikace přečetla a kolik chce kontrolu.
2. **Bannery** (jen když platí): prohozené sloupce, rukopis, jiný jazyk než jazyk sady.
3. **K ověření:** rozbalené řádky (výřez z fotky, odznak důvodu, dvě pole, akce).
4. **Ostatní:** kompaktní řádky (dvě pole, žádný výřez). Ťuknutí do pole = úprava přímo v řádku (US-03: „jedno ťuknutí do pole“).
5. **Přidat slovo** na konci seznamu.
6. **Lišta dole** s primární akcí.

**iPhone:**
```
┌────────────────────────────────┐
│ Zrušit        Zkontroluj slova │
│ [DE] Deutsch 1 · němčina       │   jazyk sady (C3 s akcentem jazyka)
│ ┌────────────────────────────┐ │
│ │ Přečetl jsem 23 dvojic.    │ │
│ │ U 4 si nejsem jistý.       │ │
│ │ Sloupce jsou prohozené?    │ │   banner jen když platí
│ │ [ Prohodit sloupce ]       │ │
│ └────────────────────────────┘ │
│ K ověření (4)                  │
│ ┌────────────────────────────┐ │
│ │ ▭▭▭▭ výřez z fotky ▭▭▭▭    │ │   ťuknutí = zvětšit
│ │ [!] Ověř diakritiku        │ │
│ │ Cizí   [ der Lehrer      ] │ │   člen: (— der die das), jen němčina
│ │ Česky  [ ucitel          ] │ │
│ │ [ Je to správně ]  [ ⋯ ]   │ │   ⋯ Smazat / Spojit s předchozím / Rozdělit
│ └────────────────────────────┘ │
│  … další tři                   │
│ Ostatní (19)                   │
│  [ das Kind      ][ dítě    ]  │
│  [ der Tisch     ][ stůl    ]  │
│  …                             │
│ [ + Přidat slovo ]             │
├────────────────────────────────┤
│ [ Zkontrolovat další (4) ]     │   dokud jsou označené řádky
│ Uložit 19 hotových             │
└────────────────────────────────┘
```
- Ostatní řádky jsou na iPhonu dva řádky pod sebou (cizí, česky), každé pole editovatelné; v obrázku výše je zkráceno do jednoho řádku jen kvůli čitelnosti ASCII.
- Člen u německých sad: u každého řádku malý přepínač `— / der / die / das` (US-03). **V sadě angličtiny se pole pro člen vůbec nezobrazuje** (US-03, D18). Odznak „Chybí člen“ se ukáže jen v německé sadě u slov začínajících velkým písmenem bez členu; je to **heuristika** (09 neurčuje druh slova; viz kap. 5), ne chyba. Prázdný člen je platná volba (sloveso, přídavné jméno).
- Více českých významů se zapisuje do jednoho pole odděleně středníkem; pod polem při zaměření: „Víc významů odděl středníkem.“ (Q4).
- Pole mají vypnutou autokorekci a nad klávesnicí lištu `ä ö ü ß` (jen u německých polí) a české diakritiky (09 kap. 2.1, krok 5).
- Akce řádku „⋯“ je i jako přístupnostní akce VoiceOveru; stejné akce jsou přes dlouhé podržení. Smazání swipem je navíc, nesmí být jediná cesta.
  - **Smazat:** řádek zmizí do „Vyřazené“ (dole, sbalené), jde vrátit ťuknutím „Vrátit“ (US-03: nic se nesmaže bez možnosti zpět).
  - **Spojit s předchozím:** spojí texty obou polí s řádkem nad ním.
  - **Rozdělit:** nejdřív ťukni do textu, kde se má rozdělit; zbytek textu se přesune do nového řádku pod ním. Interakci by měl ověřit uživatelský test, jde o nejméně jasnou akci návrhu.
  - Žádná akce „Přesunout do druhého jazyka“ (D19, US-04: slovo nejde přesunout do sady jiného jazyka).
- **Jiný jazyk než jazyk sady (US-02, US-03, Q21).** Řádky, které vypadají jako druhý jazyk (např. anglická slova v německé sadě), se **neuloží potichu**: dostanou značku „Jiný jazyk?“ a patří mezi „K ověření“. Když je takových řádků většina, nahoře přibude banner: „Tahle stránka vypadá spíš jako {angličtina}, ale importuješ do {němčiny}. Zkontroluj, jestli jsi zvolil správný jazyk slovíček.“ Tlačítka: [ Zrušit import ] (US-02: nabídne se zrušení; zahodí kontrolu a fotku, po zrušení jde v S31 zvolit správný jazyk a stránku vyfotit znovu) a [ Pokračovat v kontrole ]. Po zahájení skenu průvodce jazyk nemění ani nenabízí jeho změnu (D22). Aplikace jazyk nikdy nepřepne a slova nepřesune sama. Zjišťování jazyka z textu je **domněnka** (spolehlivost u krátkých slov, R2), požadavek D-21.
- **Značky řádků a mikrotexty (podle 09 kap. 2.2–2.4):**
  | Značka | Text |
  |---|---|
  | Nízká jistota | „Ověř. Nejsem si jistý, jak jsem to přečetl.“ |
  | Neznámé slovo / diakritika | „Ověř diakritiku.“ |
  | Chybí překlad | „Chybí překlad.“ |
  | Chybí cizí slovo | „Chybí cizí slovo.“ |
  | Není slovíčko (nadpis, číslo strany) | „Není to slovíčko?“ Výchozí stav „vyřazeno“: „Vyřazeno. Ťukni, pokud to slovíčko je.“ |
  | Člen (jen němčina) | „Chybí člen.“ |
  | Jiný jazyk | „Jiný jazyk? Vypadá to jako {angličtina}.“ |
  | Duplicita, stejný překlad | „Tohle slovo už v sadě máš. Přeskočím ho.“ (volba: Přeskočit / Přidat znovu) |
  | Duplicita, jiný překlad | „Slovo už v sadě máš. Přidám ten nový překlad k němu.“ (volba: Přidat k existujícímu / Nové slovo / Přeskočit) |
  | Duplicita v jiné sadě (téhož jazyka) | „Slovo už máš v sadě {název}. Přidám ho i sem.“ |
  | CSV chybný řádek | „Řádek {n}: chybí překlad.“ / „Řádek {n}: neznámý jazyk.“ (viz S37) |
- Duplicity se hledají jen v sadách téhož jazyka (3.8, bod 10).
- Banner rukopisu: „Rukopis se čte hůř. Zkontroluj pozorně.“
- Banner prohozených sloupců: „Vypadá to, že sloupce jsou prohozené.“ + [ Prohodit sloupce ]
- **Pravidlo ukládání (US-03):** uloží se (a) řádky bez značky, (b) řádky, které syn opravil nebo označil „Je to správně“. Označené a nedotčené řádky se neuloží. Primární akce:
  - Zbývají označené: „Zkontrolovat další ({n})“ (posune na další) a vedle sekundární „Uložit {k} hotových“ s potvrzením: „Uložit {k} slov? {n} označených slov se přeskočí a neuloží. Po uložení se fotka smaže, přidat je pak půjde jen ručně.“ [ Uložit {k} ] [ Zpět ke kontrole ]
  - Žádné označené: „Uložit {k} slov“ (primární).
- **Zrušit:** „Zahodit kontrolu? Nic se neuloží a fotka se smaže. Slova, která už v sadě máš, zůstanou.“ [ Zpět ke kontrole ] [ Zahodit ]. (US-03: zrušení nic neuloží a nic nesmaže.)
- Lze jen opravovat, nic se automaticky „nedoopravuje“ (09: označit, ne opravit).

**iPad na výšku (834 × 1194):** jeden sloupec seznamu (max. 720 pt); výřez řádku se ukazuje větší než na iPhonu; lišta s akcemi ukotvená dole.

**iPad na šířku (1194 × 834):** dva sloupce vedle sebe (rozložení pro porovnání s předlohou):
```
┌──────────────────────────────┬────────────────────────────────────┐
│ Zrušit   Zkontroluj slova    │  Fotka stránky (aktuální řádek)    │
│ [DE] Deutsch 1 · němčina     │  ┌──────────────────────────────┐  │
│ Přečetl jsem 23 dvojic, 4 …  │  │  zvýrazněný výřez řádku       │  │
│ K ověření (4)                │  │  na stránce                  │  │
│  [!] der Lehrer · ucitel  ◄  │  └──────────────────────────────┘  │
│  [!] …                       │  Zobrazit celou fotku              │
│ Ostatní (19)                 │                                    │
│  …                           │                                    │
│ [Zkontrolovat další (4)]     │                                    │
└──────────────────────────────┴────────────────────────────────────┘
```
Pravý panel ukazuje výřez aktuálně zaměřeného řádku (z `boundingBox`, **zdroj dat: kap. 5**); „Zobrazit celou fotku“ otevře celou stránku. Když výřez není (CSV, nebo OCR polohu neposkytlo), pravý panel se nezobrazí a seznam zabere celou šířku.

| Stav | Chování a text |
|---|---|
| První spuštění | Vstup je z S01 → S31 → … → S35; nad prvním seznamem jednorázová poznámka: „Tady můžeš cokoli opravit. Nic se neuloží, dokud nepotvrdíš.“ |
| Prázdno | OCR nic nenašlo: nepřijde sem, viz S34. Všechny řádky smazané: „Nezbylo žádné slovo.“ + [ Přidat slovo ] [ Zrušit ]. Primární akce nedostupná. |
| Načítání | Zobrazení jen po dokončení čtení (S34). Výřezy se načítají průběžně; do načtení šedý placeholder, řádek je editovatelný hned. |
| Částečná data | Výřez chybí: řádek bez výřezu, bez hlášky. Jen cizí nebo jen české pole: značka „Chybí překlad“ / „Chybí cizí slovo“ a nabídka „Připojit k předchozímu / následujícímu“ v ⋯ (09 kap. 2.3). Jazyk textu nelze zjistit (krátká slova): bez banneru a bez značek „Jiný jazyk?“, nic se nehlásí. |
| Chyba | Uložení selhalo: „Nepodařilo se uložit. Kontrola zůstává otevřená, nic se neztratilo. Zkus to prosím znovu.“ [ Zkusit znovu ]. |
| Offline | Beze změny (OCR i ukládání lokálně). |
| Dlouhý obsah | Stovky řádků: seznam se načítá líně, nahoře odkaz „K ověření (n)“ vždy přeskočí na označené. Dlouhý text v poli se zalomí; kompaktní řádek při Dynamic Type přejde na dva řádky pod sebou. |
| Sync konflikt | Duplicity proti knihovně z druhého zařízení: normální značka „Duplicita“. Sada mezitím smazána na druhém zařízení: při uložení „Sada {název} už neexistuje. Uložit do nové sady?“ [ Vytvořit sadu ] [ Zrušit ] (nová sada ve stejném jazyce). |
| Přerušení (aplikace ukončena během kontroly) | **Mezera G6:** bez konceptu kontroly se rozpracovaná kontrola ztratí. Návrh: banner na S20 „Máš rozdělanou kontrolu slov. [ Pokračovat ] [ Zahodit ]“ v jazyce dané sady. Vyžaduje rozhodnutí o ukládání fotky (Q8) a o lokálním (nesyncovaném) konceptu; neřeším dál. |

---

#### S36 Uloženo — US-02, US-03 (D22)

```
┌────────────────────────────────┐
│ Hotovo                         │
│ Uloženo 23 slov do sady        │
│ Deutsch 1 (němčina).           │
│ (2 nové překlady přidané ke    │   jen když platí
│  stávajícím slovům, 1 přeskočeno)│
│ [      Procvičit teď        ]  │   → S10 / test (jazyk = aktivní)
│ [          Hotovo           ]  │
└────────────────────────────────┘

Jiný jazyk než aktivní (např. aktivní angličtina, uloženo do němčiny):
│ [ Přepnout na němčinu          │   přepne aktivní jazyk → S10 / test
│   a procvičit ]                │
│ [          Hotovo           ]  │   aktivní jazyk zůstává
```
Při jazyce shodném s aktivním je nabídka „Procvičit teď“. Při jiném jazyce než aktivním ji nahradí „Přepnout na {němčinu} a procvičit“ vedle „Hotovo“ (D22); „Hotovo“ aktivní jazyk nemění. Přepnutí je výslovná volba syna (jazyk se nikdy nepřepne samo, US-26). Bez „Vrátit import“: 09 ho navrhuje, ale žádná story ho nemá (G8). Opravy po uložení jsou přes S23 (úprava a mazání slova, US-04); sada ve špatném jazyce se opraví přes S22 „Smazat a vytvořit znovu“ (D19).
„Procvičit teď“ je odkaz do US-05, ne samostatná funkce; nová slova se zařadí podle plánovače.

| Stav | Chování |
|---|---|
| Ostatní | Prázdno, načítání, chyba, offline, dlouhý obsah, sync konflikt: — (krátká obrazovka po dokončení). Uložení selže: patří do S35. iPad: formát (centrovaný sheet 540 pt). |

---

#### S37 CSV: vstupní kontrola souboru — US-19

Po výběru souboru systémovým výběrem a po S31 (jazyk slovíček zvolený v S31 a jeho sada; jazyk ze souboru nemění aktivní jazyk a nemá přednost před volbou v S31):
- Úspěch: rovnou S35 se značkami podle 09 kap. 4.
- Obecné chyby souboru (obrazovka s textem a jednou akcí):
  | Případ | Text |
  |---|---|
  | Chybí hlavička/povinný sloupec | „V souboru chybí sloupec {term / translations}. Přidej ho do první řádky a zkus to znovu.“ |
  | Poškozená diakritika (kódování) | „Diakritika v souboru vypadá poškozená. Ulož ho prosím jako CSV v UTF-8 a zkus to znovu.“ |
  | Prázdný soubor | „V souboru nejsou žádná slovíčka.“ |
  | Příliš velký (návrh 2 000 řádků) | „Soubor má víc než 2 000 řádků. Rozděl ho prosím na menší.“ |
  | Dva jazyky v jednom souboru (D18, US-19) | „V souboru jsou slova ze dvou jazyků. Jedna sada je vždy jen jeden jazyk, rozděl je prosím na dva soubory.“ |
  | Neznámé sloupce | Banner v S35: „Sloupce {…} jsem nepoužil.“ |
- Soubor, který je v jiném jazyce, než je aktivní, ale jazyk nelze jistě poznat: řádky se označí „Jiný jazyk?“ v S35 (Q21), neuloží se potichu.
- Formát CSV je otevřená otázka Q11; texty sloupců se přizpůsobí, až se rozhodne.

| Stav | Chování |
|---|---|
| Načítání | „Čtu soubor…“ (rychlé, bez procent). |
| Offline | Beze změny. |
| Ostatní | Prázdno, dlouhý obsah, sync konflikt: viz S35; samotná obrazovka je jen zpráva. |

---

### 4.4 Sady a slova

#### S20 Seznam sad — US-04 (banner konfliktu: US-12), US-26

**Účel:** sady aktivního jazyka. Sady druhého jazyka se objeví až po přepnutí; v jednom seznamu se jazyky nemíchají (US-04).

**iPhone:**
```
┌────────────────────────────────┐
│ Sady · němčina             [+] │
│ ┌──────────┬─────────────────┐ │
│ │ English  │ ▐  Deutsch   ▌  │ │   C2
│ └──────────┴─────────────────┘ │
│   k procvičení: 7              │
│ ┌────────────────────────────┐ │
│ │ [!] Slovo „Tisch“ jsi smazal│ │   banner jen při konfliktu (S63 logika)
│ │ na iPhonu, na iPadu jsi ho  │ │
│ │ ale upravil.                │ │
│ │ [ Nechat ]  [ Smazat ]      │ │
│ └────────────────────────────┘ │
│ Deutsch 1               34 slov │
│ v procvičování                 │
│ Deutsch 2               21 slov │
│ Lektion 4                9 slov │
├────────────────────────────────┤
│ tab bar                        │
└────────────────────────────────┘
```
- Řádek sady: název, počet slov, malý štítek „v procvičování“ podle výběru v S11. Štítek jazyka v řádku není (všechny řádky mají jazyk záhlaví); jazyk je v titulku, v přepínači a v hlasu VoiceOveru („Deutsch 1, němčina, 34 slov“). US-04 požaduje „jazyk uveden v záhlaví seznamu“.
- Ťuknutí → S21. Smazání sady: swipe nebo podržení + přístupnostní akce → potvrzení S24. „+“ → S30 (nová sada vzniká až v S31, ve zvoleném jazyce slovíček, předvyplněném aktivním).

**iPad na výšku:** dva sloupce: seznam sad (320 pt) s přepínačem pod titulkem | detail sady (S21).
**iPad na šířku:** sidebar (přepínač nahoře) | seznam sad (320 pt) | detail sady (S21).
```
┌─────────────────┬────────────────┬───────────────────────────────┐
│ ┌────────┬────┐ │ Sady · němčina │ Deutsch 1 · [DE]    [Upravit] │
│ │English │▐De▌│ │           [+]  │ 34 slov                       │
│ └────────┴────┘ │ Deutsch 1      │ [ + Přidat slova ]            │
│  k procvičení: 7│ Deutsch 2      │ das Kind          dítě   [♪]  │
│ Procvičit       │ Lektion 4      │ der Tisch         stůl   [♪]  │
│ Sady          ● │                │                               │
│ Slovesa         │                │                               │
│ Pokrok          │                │                               │
│ Nastavení       │                │                               │
└─────────────────┴────────────────┴───────────────────────────────┘
```
Bez vybrané sady: pravý panel je prázdný se zprávou „Vyber sadu vlevo.“ (i po přepnutí jazyka).

| Stav | Chování a text |
|---|---|
| První spuštění / prázdno (v obou jazycích nic) | „Pro {němčinu} tu zatím nemáš žádná slovíčka.“ „Vyfoť stránku ze sešitu a sada se vytvoří.“ [ Vyfotit slovíčka ] [ Vybrat z fotek ] [ Napsat ručně ] [ Přidat sadu ] |
| Aktivní jazyk bez sad, druhý jazyk je má | „Pro {němčinu} tu zatím nemáš žádná slovíčka.“ „Přidej první sadu, nebo se přepni na {angličtinu}.“ [ Vyfotit slovíčka ] (primární) [ Přidat sadu ] [ Přepnout na {angličtinu} ] (textové). Žádná chyba. U přepínače se u jazyka bez sad píše „zatím žádné sady“. |
| Načítání | Skeleton řádků (lokální data, rychlé). |
| Chyba | „Sady se nepodařilo načíst. Zkus aplikaci zavřít a znovu otevřít.“ [ Zkusit znovu ] |
| Offline | Beze změny. |
| Dlouhý obsah | Dlouhý název se zalomí na 2 řádky, počet slov se přesune pod název; sady se řadí abecedně (návrh). Hlavičky podle jazyka nejsou potřeba, seznam je jednojazyčný. |
| Sync konflikt | Banner nahoře (viz výše): objeví se jen při rozporu smazané vs. upravené slovo v **aktivním jazyce**; po volbě zmizí. Konflikt v druhém jazyce čeká: řádek „Přepsané změny“ v S60 zahrnuje oba jazyky. Duplicity: „Máš zdvojená slova. [ Prohlédnout ]“ → S62. |

---

#### S21 Detail sady (seznam slov) — US-04, US-18, US-26

**iPhone:**
```
┌────────────────────────────────┐
│ ‹ Sady       Deutsch 1  [Upravit]│
│ [DE] němčina · 34 slov         │   štítek jazyka sady (C3)
│ [ + Přidat slova ]             │   → S30
│ das Kind           dítě   [♪]  │
│ der Tisch          stůl   [♪]  │
│ lernen             učit se[♪]  │
│ …                              │
└────────────────────────────────┘
```
- Ťuknutí na slovo → S23. Ťuknutí na [ ♪ ] přehraje (C1), nezavádí navigaci.
- „Upravit“ → S22 (přejmenování, smazání sady, „Smazat a vytvořit znovu“).
- Přepínač jazyka tu není (hlubší obrazovka patří jednomu jazyku, 3.8, bod 3). Slovo nelze přesunout ani zkopírovat do sady druhého jazyka (US-04, D19); tu možnost UI nenabízí.
- Vyhledávání slov není navrženo (G9: bez story).

| Stav | Chování a text |
|---|---|
| Prázdno | „V téhle sadě zatím nic není.“ [ Vyfotit slovíčka ] [ Napsat ručně ] |
| Načítání | Skeleton. |
| Chyba | „Slova se nepodařilo načíst.“ [ Zkusit znovu ] |
| Offline | Beze změny. [ ♪ ] viz C1. |
| Dlouhý obsah | Stovky slov: líné načítání; několik českých významů se v řádku spojí čárkou a zkrátí „…“ (plné v S23). |
| Sync konflikt | Slovo upravené jinde se jen aktualizuje v seznamu; viz S63. |

---

#### S22 Úprava sady (sheet) — US-04 (D19)

**Účel:** přejmenovat sadu, smazat ji, nebo ji **opravit smazáním a založením znovu**, pokud je ve špatném jazyce. **Jazyk sady je jen zobrazený a nejde změnit (D19, rozhodnuto).** V sheetu ani jinde není žádná akce „Změnit jazyk sady“.

```
┌────────────────────────────────┐
│ Úprava sady             Hotovo │
│ Název  [ Deutsch 1           ] │
│ Jazyk  [DE] němčina            │   jen zobrazený, bez ovládání
│ Jazyk sady se po vytvoření     │
│ nemění.                        │
│ ──────────────────────────     │
│ Je sada ve špatném jazyce?     │
│ Jazyk změnit nejde. Smaž sadu  │
│ a vytvoř ji znovu ve správném  │
│ jazyce. Slova se nepřenesou.   │
│ [ Smazat a vytvořit znovu ]    │   → S24 (varianta 3) → S30 → S31
│ ──────────────────────────     │
│ [ Smazat sadu ]                │   červeně → S24 (varianta 2)
└────────────────────────────────┘
```
- „Smazat a vytvořit znovu“ je v běžné barvě tlačítka (ne červené): červená zůstává jen na „Smazat sadu“ a v potvrzení S24 (pravidlo „mazání jen červeně a jen tam“, 3.2).
- Nabídka je sekundární: ukazuje se vždy, ne jen když aplikace „pozná“ špatný jazyk (aplikace to nepozná a jazyk nikdy sama neopravuje, Q21).
- Pro prázdnou sadu (0 slov) je text kratší: „Sada je prázdná. Smaž ji a vytvoř znovu ve správném jazyce.“

| Stav | Chování a text |
|---|---|
| Prázdno | Sada bez slov: viz výše, kratší text; potvrzení v S24 nezmiňuje slova. |
| Načítání | — |
| Chyba | Prázdný název: „Pojmenuj sadu, ať ji najdeš.“ |
| Offline | Beze změny (lokální). |
| Dlouhý obsah | Dlouhý název se zalomí; text pod „Je sada ve špatném jazyce?“ se zalomí, tlačítka plné šířky. iPad: popover/sheet 540 pt. |
| Sync konflikt | — (jednoduchý formulář, lokální). |

---

#### S23 Slovo (karta a úprava) — US-04, US-18, US-03, US-26 (jazyk dědí ze sady)

**Účel:** zobrazit, opravit, přidat a smazat jedno slovo. Stejná obrazovka slouží k ručnímu přidání (z S30 „Napsat ručně“). Jazyk slova je jazyk sady a nezadává se zvlášť (US-04).

**iPhone, německá sada:**
```
┌────────────────────────────────┐
│ Zrušit        Slovo      Uložit│
│ [DE] němčina                   │   štítek jazyka sady
│ Německé slovo                  │
│ [ Tisch                      ] │
│ Člen                           │   jen v německé sadě
│ ( — )( der )( die )( das )     │
│ Česky                          │
│ [ stůl; stolek               ] │
│ Víc významů odděl středníkem.  │
│ [ ♪ Přehrát výslovnost ]       │
│ Sada: Deutsch 1                │   jen sady téhož jazyka
│ [ Smazat slovo ]               │   červeně → S24
└────────────────────────────────┘
```
V anglické sadě je popisek „Anglické slovo“ a řádek „Člen“ se nezobrazuje.
- „Uložit“ je dostupné, když je vyplněné cizí slovo a aspoň jeden český význam.
- Varování, ne blokace: německé slovo bez členu, začínající velkým písmenem: „U německého podstatného jména chybí člen. Můžeš ho doplnit teď, nebo později.“ (jen němčina)
- Při přidávání nového slova: „Uložit“ a „Uložit a přidat další“.
- Přidání ručně z S30 bez sady ve zvoleném jazyce: nejdřív S31 (volba jazyka, nová sada), pak S23.
- Výběr „Sada“ nabízí jen sady téhož jazyka; slovo nejde přesunout do sady druhého jazyka (D19).
- Vypnutá autokorekce, lišta znaků (kap. 3.4).
- Pole množné číslo, příklad, výslovnostní přepis, poznámka z 09 **nejsou navržena** (G10: bez story).

**iPad:** na obou orientacích sheet 540 pt na výšku obrazovky (nebo v pravém panelu S21 v režimu úpravy na šířku).

| Stav | Chování a text |
|---|---|
| Prázdno | Nové slovo: prázdná pole, „Uložit“ nedostupné. |
| Načítání | — |
| Chyba | Duplicita: „Slovo Tisch už v téhle sadě máš. [ Otevřít existující ]“ (varování, uložit lze). Stejný zápis v sadě druhého jazyka se nepočítá. |
| Offline | Beze změny; [ ♪ ] viz C1. |
| Dlouhý obsah | Dlouhé pole se zalomí; více významů nad 5: scroll v poli, žádný limit nevymýšlím. |
| Sync konflikt | Slovo bylo mezitím změněno jinde: při uložení se uloží pozdější změna; viz S63 (US-12, Q7). |
| Smazání | S24. |

---

#### S24 Potvrzení smazání (alert) — US-04 (D19; D22 pro variantu 3)

1. **Slovo:** „Smazat slovo Tisch? Smaže se i historie jeho procvičování. To nejde vrátit.“ [ Smazat ] [ Nechat ]
2. **Sada:** „Smazat sadu Deutsch 1? Smaže se 34 slov a jejich historie procvičování. To nejde vrátit.“ [ Smazat sadu ] [ Nechat ]
3. **Sada a vytvořit znovu (z S22, D19):** „Smazat sadu Deutsch 1 a vytvořit ji znovu v {angličtině}? Smaže se 34 slov a jejich historie procvičování. Slova se do nové sady nepřenesou, budeš je muset znovu vyfotit nebo zadat. To nejde vrátit.“ [ Smazat a vytvořit znovu ] [ Nechat ]. U prázdné sady: „Smazat prázdnou sadu Deutsch 1 a vytvořit ji znovu v {angličtině}?“ Po potvrzení se sada smaže, **aktivní jazyk se nepřepíná** (D22: import jazyk volí sám) a otevře se S30, po zvolení zdroje S31 s předvybraným správným jazykem (segment „Jazyk slovíček“) a předvyplněným názvem. „Zrušit“ v průvodci už smazání nevrací; alert to říká („To nejde vrátit.“).

(US-04: ukáže, co se smaže, včetně počtu slov a historie.) „Nechat“ je výchozí a nejvýraznější. Cílový jazyk ve variantě 3 je vždy ten druhý (jazyky jsou dva) a je jen předvybraný, v S31 jde změnit.

| Stav | Chování |
|---|---|
| Ostatní | Prázdno, načítání, chyba, offline, dlouhý obsah, sync konflikt: — (alert); smazání probíhá lokálně a synchronizuje se později (viz S63 pro „oživení“). |

---

### 4.5 Nepravidelná slovesa

Samostatný vstup (D8, US-17). Zadávají se ručně (US-16). Datový model slovesa je v `09-obsah-a-import.md` kap. 5. **Sloveso nemá sadu, takže jeho jazyk je aktivní jazyk v okamžiku zadání** a pak se nemění (G18). Seznam, procvičování i souhrn jsou jen pro aktivní jazyk.

#### S40 Seznam sloves — US-16, US-17, US-26

**iPhone:**
```
┌────────────────────────────────┐
│ Slovesa · němčina          [+] │
│ ┌──────────┬─────────────────┐ │
│ │ English  │ ▐  Deutsch   ▌  │ │   C2 (nahrazuje dřívější přepínač jazyka v tomto seznamu)
│ └──────────┴─────────────────┘ │
│   k procvičení: 7              │   slovíčka, ne slovesa (G21)
│ [      Procvičit slovesa     ] │   primární (US-17)
│ fahren – fährt – fuhr – ist gefahren │
│ jet, jezdit                    │
│ gehen – geht – ging – ist gegangen │
│ …                              │
└────────────────────────────────┘
```
Anglická varianta: `go – went – gone` s českým významem pod ním [ ♪ ].

**iPad na výšku:** seznam | detail (úprava, S41); přepínač pod titulkem seznamu. **iPad na šířku:** sidebar (přepínač nahoře) | seznam | detail.

| Stav | Chování a text |
|---|---|
| První spuštění / prázdno | „Pro {němčinu} tu zatím nemáš žádná nepravidelná slovesa.“ „Zadej je z učebnice a pak je tu můžeš procvičovat.“ [ Přidat sloveso ] |
| Aktivní jazyk bez sloves, druhý jazyk je má | Totéž + textové tlačítko „Přepnout na {angličtinu}“. |
| Načítání | Skeleton. |
| Chyba | „Slovesa se nepodařilo načíst.“ [ Zkusit znovu ] |
| Offline | Beze změny. |
| Dlouhý obsah | Řádek se zalomí; českých významů zobrazit max. 2, zbytek „+ další“. |
| Sync konflikt | Jako u slov (S20, S62, S63). |
| Málo sloves | „Procvičit slovesa“ nedostupné bez aspoň 1 slovesa. Zda minimální počet pro kolo zadá plánovač, jinak návrh neimprovizuje. |
| Aktivní jazyk bez dat | Viz druhý řádek tabulky. |

---

#### S41 Přidat / upravit sloveso — US-16, US-26

**Cíl:** syn píše z učebnice, nejčastěji jednou řádkou. Podle 09 kap. 5.2: první cesta je **rychlé zadání celé řádky**, druhá pole po tvarech. Jazyk není ovládací prvek: je to štítek aktivního jazyka (dřívější segment „Angličtina / Němčina“ v S41 jsem zrušil, aby se jazyk neměnil na dvou místech).

**iPhone:**
```
┌────────────────────────────────┐
│ Zrušit    Nové sloveso    Uložit│
│ [DE] němčina                   │   štítek jazyka (aktivní), ne ovládací prvek
│ Celá řádka z učebnice          │
│ [ fahren – fährt – fuhr – ist gefahren ] │
│ Rozpoznáno:                    │
│ Infinitiv      [ fahren     ]  │
│ 3. os. přít.   [ fährt      ]  │   jen němčina
│ Préteritum     [ fuhr       ]  │
│ Příčestí       [ gefahren   ]  │
│ Pomocné sloveso (haben|sein|obojí)│  jen němčina
│ ( ) Odlučitelné                │
│ Česky          [ jet; jezdit ] │
│ + varianta                     │
│ [ Uložit a přidat další ]      │
└────────────────────────────────┘
```
- Oddělovače v rychlém zadání: `–`, `-`, `/`, čárka. Pole se po zápisu doplní (jsou editovatelná). Nerozpoznané zůstává prázdné.
- Pole se posouvají klávesou „Další“; poslední pole Return = „Uložit“.
- Lišta `ä ö ü ß` nad klávesnicí v němčině.
- Chce-li syn zadat sloveso druhého jazyka, nejdřív přepne jazyk (S40, C2). Pod štítkem je to jedna věta: „Sloveso se uloží do jazyka {němčina}.“
- **Validace je jen varování, neblokuje uložení (US-16):**
  | Případ | Text |
  |---|---|
  | Prázdný tvar | „Chybí příčestí. Můžeš uložit i tak, tenhle tvar se pak zkoušet nebude.“ |
  | Němčina bez pomocného slovesa | „Chybí pomocné sloveso (haben, sein).“ |
  | Infinitiv už existuje (v témže jazyce) | „Sloveso fahren už máš. [ Upravit existující ]“ |
  | Příčestí shodné s minulým (EN) | informační řádek bez varování: „Minulý čas a příčestí jsou stejné. To je v pořádku (např. bought).“ |
- Hromadné vložení víc řádků a CSV sloves jsou v 09, ale **nemají story** (G11): v návrhu nejsou.
- Věta „tenhle tvar se pak zkoušet nebude“ je předpoklad, že plánovač prázdné tvary vynechá (kap. 5).

**iPad:** sheet 540 pt (na výšku i na šířku); pole „Celá řádka“ široké přes celý sheet. Na šířku: levý sloupec rychlé zadání, pravý pole po tvarech.

| Stav | Chování a text |
|---|---|
| Prázdno | Nové: prázdná pole, „Uložit“ nedostupné bez infinitivu a českého významu. |
| Načítání | — |
| Chyba | Viz validace. |
| Offline | Beze změny. |
| Dlouhý obsah | Několik variant (`learnt`/`learned`): „+ varianta“ přidá pole; seznam variant rolue. Odlučitelné: předpona se nabídne podle začátku infinitivu (`auf|stehen`). |
| Sync konflikt | Jako S23. |

---

#### S42 Procvičování sloves — otázka — US-17, US-18, US-26 (jazyk zamčený)

**Účel:** dotaz na chybějící tvary. Zadaná část je infinitiv + český význam; hledané tvary se píšou (US-17: „zadaná část, hledané tvary psaním“). Layout je obecný (daný tvar jako štítek, ostatní jako pole), takže zadání jiného tvaru než infinitivu by šlo, ale **mezera G12:** story říká jen „zadaná část“ a nerozhoduje, který tvar je daný. Přepínač jazyka tu není (jako v testu slovíček).

**iPhone (němčina), klávesnice nahoře:**
```
┌────────────────────────────────┐
│ Skončit            ▓▓░░░░░░ 3/8│
│ [DE] NEPRAVIDELNÁ SLOVESA      │   štítek jazyka (C3, neutrální)
│ ┌────────────────────────────┐ │
│ │ fahren           [ ♪ ]     │ │   daný tvar + význam
│ │ jet, jezdit                │ │
│ └────────────────────────────┘ │
│ 3. os. přít.   Préteritum      │
│ [ fährt      ] [ fuhr       ]  │   dvě pole vedle sebe
│ Pomocné        Příčestí        │
│ (haben|sein)   [ gefahren   ]  │
│  ä   ö   ü   ß                 │
│ [Nevím]   [    Zkontrolovat   ]│
├────────────────────────────────┤
│ ░░░░░░ KLÁVESNICE ░░░░░░░░░░░░ │
└────────────────────────────────┘
```
- Angličtina: pole „Minulý čas“ a „Příčestí“ vedle sebe; lišta znaků odpadá (3.4), takže rozpočet výšky je volnější.
- Pomocné sloveso se vybírá segmentem (haben | sein), ne píše. Důvod: je to tvar, který se zkouší, a segment šetří místo i překlepy. Alternativa „jedno pole Perfekt `ist gefahren`“ je kratší pro syna, který ho píše z sešitu; **návrh k potvrzení learning-scientist** (G13).
- Return v poli = přeskočí na další, v posledním = „Zkontrolovat“.
- „Zkontrolovat“ nedostupné, dokud není vyplněno aspoň jedno pole nebo zvolené pomocné sloveso; „Nevím“ vždy.
- Rozpočet výšky (SE 667 pt): daný tvar (80) + dva řádky polí (2 × 76) + lišta (44) + tlačítka (56) + horní lišta (72) = ~404 pt. **Je to těsné; pravidlo S13 platí:** zadání se smí zmenšit, pole a tlačítka jsou ukotvená.

**iPad na výšku:** centrovaný sloupec 560 pt, stejné rozložení. **iPad na šířku:** daný tvar vlevo, pole vpravo ve dvou sloupcích 2 × 2; lišta a tlačítka pod nimi, nad klávesnicí.

| Stav | Chování a text |
|---|---|
| První spuštění | Před prvním kolem jednorázová poznámka: „Doplň chybějící tvary. Stačí napsat, co víš, zbytek přeskoč.“ |
| Prázdno | Bez sloves S40 neumožní spustit. |
| Načítání | — |
| Chyba | Uložení selhalo: jako S14. |
| Offline | Beze změny. [ ♪ ] viz C1. |
| Dlouhý obsah | Dlouhý infinitiv („fernsehen“) nebo odlučitelný tvar se zalomí; pole se rozšíří na plnou šířku, když se tvary nevejdou vedle sebe (Dynamic Type). |
| Sync konflikt | Sloveso mezitím smazané: přeskočit bez hlášky. |
| Nenapsal nic | Viz výše. |

---

#### S43 Vyhodnocení sloves — US-17 (pravidla překlepů a diakritiky jako US-07), US-26

Hodnocení po tvarech: syn vidí, který tvar seděl a který ne.

```
┌────────────────────────────────┐
│ [DE] fahren – jet, jezdit [♪]  │
│ ┌────────────────────────────┐ │
│ │ 3 ze 4 tvarů sedí.         │ │   nadpis vždy začíná tím dobrým
│ │ [ok] 3. os. přít.  fährt   │ │
│ │ [~]  Préteritum    fuhr    │ │   napsal jsi: fur  (zvýrazněné písmeno)
│ │ [ok] Pomocné       sein    │ │
│ │ [↻]  Příčestí      gefahren│ │   napsal jsi: gefarn
│ └────────────────────────────┘ │
│ [            Další           ] │
└────────────────────────────────┘
```
Texty řádků: „Sedí“ / „Skoro: chybí jedno písmeno“ / „Zatím ne“. Řádek vždy ukazuje správný tvar, u chybného i „Napsal jsi“. Nadpis podle počtu: „4 ze 4 tvarů sedí.“ / „3 ze 4 tvarů sedí.“ / „1 ze 4 tvarů sedí, zbytek si prohlédni.“ / u 0: „Tentokrát žádný tvar nesedí. Tady jsou všechny.“
Jak se z tvarů skládá výsledek pro plánovač (US-08 pro slovesa je v US-17 jen „předpoklad“), je věc learning-scientist, ne UX (G13).

**iPad:** jako S14 (na výšku centrovaný sloupec; na šířku „Napsal jsi“ vlevo, „Správně“ vpravo, po tvarech).
Souhrn po kole: S16 (varianta sloves).

| Stav | Chování |
|---|---|
| Prázdno | — |
| Načítání | — |
| Chyba | Uložení selhalo: jako S14. |
| Offline | Beze změny. |
| Dlouhý obsah | Řádky tvarů se zalamují; u „obou platných variant“ (learnt/learned): „Uznáno i: learned“. |
| Sync konflikt | — |

---

### 4.6 Pokrok

#### S50 Vývoj — US-14, US-26

**Účel:** syn vidí, jestli se zlepšuje, v aktivním jazyce. Tón bez odsuzování; mezera v čase není selhání. Dřívější filtr „Vše | Angličtina | Němčina“ jsem odstranil: součet přes oba jazyky D20 zakazuje.

**iPhone:**
```
┌────────────────────────────────┐
│ Pokrok · němčina               │
│ ┌──────────┬─────────────────┐ │
│ │ English  │ ▐  Deutsch   ▌  │ │   C2
│ └──────────┴─────────────────┘ │
│   k procvičení: 7              │
│ [ Vývoj ][ Slabá místa ]       │   přepínač S50 / S51
│ (Slovíčka)(Slovesa)       Sada▾│   Sada▾ = jen sady aktivního jazyka
│ ┌────────────────────────────┐ │
│ │ Zlepšuje se                │ │   vždy jedna ze 4 vět (viz níže)
│ │ Za poslední týdny se ti    │ │
│ │ vybavuje víc slov.         │ │
│ └────────────────────────────┘ │
│ (Naučená slova)(Úspěšnost)     │
│   ▂ ▃ ▅ ▆   · ▇ ▇              │   týdny; mezera = tečka
│  1.10 8.10 15.10 22.10 29.10   │
│ Poslední procvičování          │
│  Dnes 16:20        8 z 12   ›  │   → S16
│  Včera 18:05      10 z 12   ›  │
└────────────────────────────────┘
```
**Čtyři věty o trendu (skutečné znění):**
| Stav | Nadpis | Věta |
|---|---|---|
| Zlepšuje se | „Zlepšuje se.“ | „Za poslední týdny se ti vybavuje víc slov.“ |
| Drží se | „Drží se.“ | „Výsledky jsou stabilní. To je v pořádku.“ |
| Zatím se nezlepšuje | „Zatím se to nehýbe.“ | „To se stává. Podívej se, která slova zlobí.“ + odkaz na S51 |
| Málo dat | „Zatím málo dat.“ | „Po pár procvičováních se tu ukáže, kam to směřuje.“ |

- Graf: týdny na ose X, metrika přepínačem („Naučená slova“, „Úspěšnost“). Týdny bez procvičování se nekreslí jako nula: je tam slabá tečka a popisek VoiceOveru „Tento týden bez procvičování“ (US-14). Graf je jednojazyčný a nepoužívá barvu jazyka (3.8, bod 7).
- Graf má textovou alternativu (tabulka „Týden / hodnota“), zapnutelnou odkazem „Zobrazit jako seznam“ a čtenou VoiceOverem.
- „Naučené slovo“ musí mít v aplikaci jasnou definici (Q1). Do rozhodnutí se v UI používá štítek „Naučená slova“, ale **definice musí být vidět na ťuknutí na (i)**: „Slovo, které sis správně vybavil psaním i po delší pauze.“ Tohle znění je **příklad ze story**, finální definici dá learning-scientist.
- Slovesa: samostatný přepínač „Slovesa“ (US-17: odděleně).
- Řádek v „Poslední procvičování“ ukazuje datum a „8 z 12“ (kolik hned) bez barevného hodnocení. Seznam je jen z aktivního jazyka.
- Jazyky se nesrovnávají ani nesčítají (D20): žádná věta typu „němčina ti jde hůř než angličtina“, žádný celkový počet naučených slov, žádná celková pravidelnost.

**iPad na výšku:** filtry nahoře vodorovně v jednom řádku, přepínač jazyka pod titulkem; trend karta a graf pod sebou; seznam pokusů pod grafem; max. šířka obsahu 720 pt.
**iPad na šířku:**
```
┌────────┬───────────────────────────────────────────────────┐
│Sidebar │ Pokrok · němčina [Vývoj][Slabá místa]  (filtry)    │
│(C2     │ ┌──────────────────┐ ┌──────────────────────────┐ │
│nahoře) │ │ Zlepšuje se      │ │ Poslední procvičování     │ │
│        │ │ …                │ │ Dnes 16:20   8 z 12     › │ │
│        │ └──────────────────┘ │ Včera 18:05 10 z 12     › │ │
│        │ ┌──────────────────┐ │ …                         │ │
│        │ │  graf týdny      │ └──────────────────────────┘ │
│        │ └──────────────────┘                               │
└────────┴───────────────────────────────────────────────────┘
```
Ťuknutí na pokus otevře S16 v pravém panelu / jako sheet.

| Stav | Chování a text |
|---|---|
| První spuštění / málo dat | Místo grafu: „Zatím málo dat.“ Pod tím: „Po pár procvičováních se tu ukáže, kam to směřuje.“ (nekreslí se osa s nulami). |
| Prázdno | Bez pokusů: totéž. |
| Aktivní jazyk bez dat | Aktivní jazyk nemá žádné pokusy: „Pro {němčinu} tu zatím není co ukázat.“ „Po pár procvičováních se tu ukáže, kam to směřuje.“ Když má pokusy druhý jazyk: navíc textové tlačítko „Přepnout na {angličtinu}“. |
| Načítání | Skeleton grafu (lokální výpočet; u velké historie krátký). |
| Chyba | „Pokrok se nepodařilo spočítat. Zkus to prosím znovu.“ [ Zkusit znovu ] |
| Offline | Beze změny. |
| Dlouhý obsah | Historie za roky: graf ukazuje posledních 8 týdnů (iPad 12), starší vodorovným posunem. Seznam pokusů se načítá líně. |
| Sync konflikt | Pokusy ze dvou zařízení se po syncu sloučí do jedné historie; trend se přepočítá z historie (US-12). Nic uživatele nevyrušuje. |
| Částečná data | Sada s menším počtem pokusů: její řádek „Zatím málo dat“; celkový graf aktivního jazyka zůstává. |
| Mezera v čase | Prázdniny: týdny jsou tečky bez textu o „výpadku“. |

---

#### S51 Slabá místa — US-15, US-26

**Účel:** ukázat cestu dál, ne počet chyb. Slabá slova a vzorce jsou jen z aktivního jazyka.

**iPhone:**
```
┌────────────────────────────────┐
│ Pokrok · němčina               │
│ [ English | Deutsch ]  (C2)    │
│   k procvičení: 7              │
│ [ Vývoj ][ Slabá místa ]       │
│ Na co se zaměřit               │
│ [  Procvičit slabá slova  ]    │   primární, jedno ťuknutí (US-15)
│                                │
│ Co se opakuje                  │
│ ┌────────────────────────────┐ │
│ │ Členy der, die, das        │ │   karta vzorce (jen němčina)
│ │ U některých německých slov │ │
│ │ se členy pletou.           │ │
│ └────────────────────────────┘ │
│ ┌────────────────────────────┐ │
│ │ Z češtiny do němčiny je to │ │
│ │ zatím těžší než naopak.    │ │
│ │ U všech to tak bývá.       │ │
│ └────────────────────────────┘ │
│ Slova (10)                     │
│  der Tisch        stůl    člen │   důvod jako štítek
│  die Straße       ulice  česky → německy│
│  …                             │
└────────────────────────────────┘
```
- „Procvičit slabá slova“ spustí kolo jen ze slabých slov aktivního jazyka (US-15) → S12/S13. Konkrétní práh slabého slova je Q1.
- Seznam je krátký (max. 10, US-15). Řádek: slovo, překlad, štítek důvodu (člen, diakritika, směr). Počet chyb se nezobrazuje.
- Karty vzorců (US-15: členy, diakritika, směr). Každá se zobrazí jen, když je pro ni dost dat. **Karta „Členy“ existuje jen pro němčinu**; v angličtině se nezobrazuje. Ťuknutí na kartu zúží seznam na její slova (jen filtr seznamu, **žádné další tlačítko**; kolo jen ze slabých slov je jedno).
- Znění karet:
  - Členy: „Členy der, die, das. U některých německých slov se členy pletou.“
  - Diakritika: „Diakritika. Občas chybí háček, čárka nebo přehláska.“ (v angličtině: „Občas chybí háček nebo čárka“ jen u české odpovědi; přehláska jen němčina)
  - Směr: „Z češtiny do němčiny je to zatím těžší než naopak. U všech to tak bývá.“ (směr podle skutečných dat; text se obrátí podle toho, který směr je slabší, a jazyk se dosadí, např. „Z češtiny do angličtiny …“). Je to srovnání směrů uvnitř jednoho jazyka, ne mezi jazyky (D20).
- Ťuknutí na slovo → S23. [ ♪ ] zde není (US-18 ho pro tuto obrazovku neuvádí); přehrát lze v S23.
- Slabá místa sloves: **mezera G14**, US-15 je nezmiňuje; v návrhu nejsou.

**iPad na výšku:** karty vzorců vedle sebe (2 sloupce), pod nimi seznam. **iPad na šířku:** vlevo karty vzorců a tlačítko, vpravo seznam slov.

| Stav | Chování a text |
|---|---|
| První spuštění / málo dat | „Zatím tu nic nezlobí.“ „Slabá místa se ukážou po pár procvičováních.“ Tlačítko „Procvičit slabá slova“ skryté. |
| Prázdno | Nikde nic slabého: „Žádné slovo teď nezlobí.“ + odkaz „Zpět na Vývoj“. |
| Aktivní jazyk bez dat | „Pro {němčinu} tu zatím nic nezlobí.“ „Slabá místa se ukážou po pár procvičováních.“ + když druhý jazyk data má: textové tlačítko „Přepnout na {angličtinu}“. |
| Načítání | Skeleton. |
| Chyba | „Slabá místa se nepodařilo spočítat.“ [ Zkusit znovu ] |
| Offline | Beze změny. |
| Dlouhý obsah | Top 10 (limit), víc se nezobrazuje. Dlouhý překlad se zalomí. |
| Sync konflikt | Seznam se po syncu přepočítá z historie; žádný zásah uživatele. |

---

### 4.7 Nastavení, sync, smazání

Nastavení je **společné pro oba jazyky** (D18, zadání). Nemá přepínač jazyka ani titulek s jazykem. Kde se obsah týká dat obou jazyků (S62, S63, S64), jazyk se píše u každého řádku.

#### S60 Nastavení — US-11, US-12, US-20

```
┌────────────────────────────────┐
│ Nastavení                      │
│ Platí pro angličtinu i němčinu.│   jedna věta pod titulkem
│ iCloud a synchronizace      ›  │   → S61, vpravo stav slovem
│ Duplicity (3)               ›  │   jen když jsou; → S62
│ Přepsané změny (1)          ›  │   jen když jsou; → S63
│ ───────────────────────────    │
│ Smazat všechna data         ›  │   červeně; → S64
└────────────────────────────────┘
```
Žádný jiný obsah (hlas, verze, „O aplikaci“ nemají story; G15).
Počty u „Duplicity“ a „Přepsané změny“ zahrnují oba jazyky (jde o počty úkolů k vyřešení, ne o statistiku učení, D20). Přepínač jazyka tu záměrně není (Nastavení je jediná kořenová obrazovka bez něj, 3.8, bod 3); na iPadu je přepínač stále v sidebaru, protože patří sidebaru, ne obsahu.
**iPad:** na šířku sidebar | seznam nastavení | detail; na výšku seznam | detail.

| Stav | Chování |
|---|---|
| Prázdno | Řádky „Duplicity“ a „Přepsané změny“ se nezobrazují, dokud není co řešit. |
| Načítání | — |
| Chyba | — |
| Offline | Řádek „iCloud“ ukazuje „Čeká na připojení“ (viz S61); jinak beze změny. |
| Dlouhý obsah | — |
| Sync konflikt | Řádky s počtem; počet se nemění na odznak mimo obrazovku. |

---

#### S61 iCloud a synchronizace — US-11

**Texty stavů (skutečné znění):**
| Stav | Nadpis | Text | Akce |
|---|---|---|---|
| Vše v pořádku | „Synchronizováno“ | „Slovíčka a výsledky se mezi tvými zařízeními synchronizují přes iCloud. Naposledy: {čas}.“ | — |
| Probíhá | „Synchronizuji…“ | „Změny se právě odesílají.“ | — |
| Čeká | „Čeká na připojení“ | „Změny jsou uložené v zařízení a odešlou se, až budeš online.“ | — |
| iCloud vypnutý / nepřihlášený | „iCloud je vypnutý“ | „Slovíčka a výsledky zůstávají jen v tomhle zařízení. Až se přihlásíš, přenesou se samy.“ | [ Otevřít Nastavení ] |
| Chyba | „Synchronizace se teď nepovedla“ | „Nic se neztratilo, data jsou v zařízení. Zkusím to znovu později.“ | [ Zkusit teď ] |
| iCloud plný | „V iCloudu je plno“ | „Nové změny se zatím do iCloudu nevejdou. Uvolni místo v nastavení iCloudu.“ | [ Otevřít Nastavení ] |

- Pod stavem vždy řádek: „Fotky se nikdy neodesílají. Do iCloudu jdou jen slova, sady a výsledky.“ (US-11).
- Synchronizují se data obou jazyků; aktivní jazyk se podle návrhu nesynchronizuje (3.8, bod 5, Q18). Do textu o syncu to nepatří.
- Jazyk „před 3 minutami“ nepoužívám, dokud není jasné, zda je čas posledního syncu dostupný (kap. 5). Do té doby jen stav bez času.
- Texty „Nic se neztratilo“ platí jen, když architekt zaručí lokální uložení před syncem (US-10); předpoklad.

| Stav | Chování |
|---|---|
| První spuštění | Stav „iCloud je vypnutý“ případně dle S01 krok 3. |
| Prázdno | — |
| Načítání | „Zjišťuji stav…“ pod nadpisem (max. pár sekund; pak „Stav teď nejde zjistit.“). |
| Chyba | Viz tabulka. |
| Offline | „Čeká na připojení“ (viz tabulka); jediné místo, kde se offline nahlásí. |
| Dlouhý obsah | Dynamic Type: nadpis a text se zalomí, tlačítko plné šířky. |
| Sync konflikt | Odkaz „Duplicity (3)“ a „Přepsané změny (1)“ v tomto detailu stejně jako v S60. |

---

#### S62 Duplicity ke sloučení — US-12

```
┌────────────────────────────────┐
│ ‹ Nastavení        Duplicity   │
│ Stejné slovo je v knihovně     │
│ víckrát. Sloučením zůstane     │
│ jedno a nic z historie se      │
│ neztratí.                      │
│ [DE] der Tisch · stůl      ×2  │
│ [ Sloučit ] [ Nechat obě ]     │
│ [EN] table · stůl          ×2  │
│ [ Sloučit ] [ Nechat obě ]     │
└────────────────────────────────┘
```
- Nic se nesluční automaticky (US-12). Sloučení spojí české významy a historii procvičování obou záznamů (**požadavek na architekta**, kap. 5).
- Seznam obsahuje duplicity obou jazyků (Nastavení je společné), každý řádek má štítek jazyka C3. Duplicitou je jen stejné slovo **v témže jazyce** (3.8, bod 10); „Hand“ v angličtině a „Hand“ v němčině spolu nikdy nejsou. Sloučení nikdy nespojí záznamy různých jazyků (D19).
- iPad: seznam | detail dvojice (obě varianty vedle sebe) na šířku; na výšku sheet.

| Stav | Chování a text |
|---|---|
| Prázdno | „Žádné duplicity.“ (řádek v S60 se normálně nezobrazí). |
| Načítání | „Hledám duplicity…“ |
| Chyba | „Sloučení se nepovedlo. Slova jsou v původním stavu.“ [ Zkusit znovu ] |
| Offline | Beze změny (sloučení je lokální a po syncu se přenese). |
| Dlouhý obsah | Mnoho duplicit: seznam rolue, „Sloučit vše“ nenavrhuji (US-12 říká „nabídnout ke sloučení“, ne hromadně). |
| Sync konflikt | Tato obrazovka je jeho výsledek. |

---

#### S63 Přepsané změny — US-12 (Q7: vyhraje pozdější změna, v nastavení lze podívat, co bylo přepsáno)

```
┌────────────────────────────────┐
│ ‹ Nastavení   Přepsané změny   │
│ Tohle slovo jsi upravil na     │
│ dvou zařízeních. Platí pozdější│
│ úprava.                        │
│ [DE] der Tisch                 │
│ Platí:      stůl; stolek       │
│ Přepsáno:   stůl               │
│ [ Vrátit přepsané ] [ Nechat ] │
└────────────────────────────────┘
```
- Zobrazuje se jen u slov, u nichž k přepsání došlo, z obou jazyků, každý řádek se štítkem jazyka C3. Starší hodnota je dostupná jen, pokud ji model uchová (**požadavek na zdroj dat**, kap. 5: CloudKit tak sám neuchovává, domněnka). Pokud ji architekt nezaručí, obrazovka ztrácí smysl a story US-12 (Q7) se musí upravit; nevymýšlím ji.
- Název zařízení („na iPadu“) nepoužívám, dokud není zdroj (kap. 5); píšu „na druhém zařízení“.
- „Slovo bylo smazáno a jinde upraveno“ (oživení) řeší banner v S20 (v jazyce slova), stejná logika.

| Stav | Chování a text |
|---|---|
| Prázdno | „Nic nebylo přepsáno.“ |
| Načítání | „Načítám…“ |
| Chyba | „Změnu se nepodařilo vrátit.“ [ Zkusit znovu ] |
| Offline | Beze změny (lokální). |
| Dlouhý obsah | Dlouhý seznam rolue, položky se řadí od nejnovějších. |
| Sync konflikt | Tato obrazovka je jeho výsledek. |

---

#### S64 Smazat všechna data — US-20

Dvoustupňové potvrzení (US-20). Vstup je z S60, červený řádek. Smazání se týká **obou jazyků** a text to říká výslovně (D18, Q20: smazání jednoho jazyka se nenabízí).

**Krok 1 (obrazovka):**
- Nadpis: „Smazat všechna data?“
- Text: „Smaže se všechno v angličtině i v němčině: {n} sad, {m} slov, {p} sloves a celá historie procvičování. Smaže se v tomhle zařízení i v iCloudu, takže zmizí i na ostatních zařízeních. Tohle nejde vrátit.“
- Rozpis (pod textem, malé písmo, jazyky vedle sebe, bez dalšího součtu mimo větu výše, která jen vyjmenovává rozsah mazání): „Angličtina: {n1} sad, {m1} slov, {p1} sloves. Němčina: {n2} sad, {m2} slov, {p2} sloves.“
- Poznámka: „Když aplikaci jen odinstaluješ, data v iCloudu zůstanou.“ (US-20: v nastavení je to vysvětleno; **domněnka, ověřit**.)
- Tlačítka: „Pokračovat“, „Zrušit“ (výchozí).
- Export před smazáním (Q12): nenavrhuji, dokud se nerozhodne; pokud ano, vloží se mezi krok 1 a 2.

**Krok 2 (alert):**
- „Opravdu smazat všechno? Po smazání se aplikace vrátí na začátek, včetně volby jazyka.“
- Tlačítka: „Smazat všechno“ (červené), „Nemazat“ (výchozí).

Po smazání se zobrazí S01 od kroku 1 (volba jazyka); dříve zapamatovaný aktivní jazyk se zapomíná.

| Stav | Chování a text |
|---|---|
| Prázdno | Nic k mazání: „Není co mazat. Aplikace je prázdná.“ „Pokračovat“ nedostupné. |
| Načítání | Při mazání spinner „Mažu…“; neblokuje bezpečné přerušení (nedělat modální blokaci bez „Zrušit“, dokud to jde). |
| Chyba | „Smazání se nepovedlo. Nic se nesmazalo úplně, zkus to prosím znovu.“ [ Zkusit znovu ] (přesné chování při částečném smazání určí architekt). |
| Offline | Smazání kopie v iCloudu potřebuje internet: „Bez internetu to teď nejde. Smazání se týká i iCloudu, proto se musíš připojit. Zkus to znovu, až budeš online.“ „Pokračovat“ nedostupné. |
| Dlouhý obsah | Text se zalomí. Rozpis podle jazyků se zalomí na dva řádky. |
| Sync konflikt | Druhé zařízení ještě pracuje: „Na jiném zařízení se právě synchronizuje. Smazání může chvíli trvat.“ (domněnka, závisí na R4). |

---

### 4.8 Sdílené komponenty

#### C1 Tlačítko výslovnosti — US-18

**Kde je:** S12 a S13 (zadání cizího slova ve směru cizí → česky), S14 a S43 (u správné odpovědi), S16 (u slov k procvičení), S21 a S23 (karta a seznam), S42 (daný infinitiv). V S51 není (viz S51).
Výklad: story říká „na kartě slova, při ukázání správné odpovědi i v souhrnu“. Zadání cizího slova v testu je výklad slova „Given slovo“; vlastní reproduktor u české strany zadání nikdy není. Viz G7.

**Chování:**
- Ikona reproduktoru, velikost ≥ 44 × 44 pt, popisek VoiceOveru „Přehrát výslovnost: {slovo}“.
- Přehraje se jen na ťuknutí, nikdy automaticky (US-18).
- Mluví se cizí slovo; u německých podstatných jmen včetně členu (09 kap. 6): „das Kind“. Hlas německý (de-DE) u DE sad, britský (en-GB) u EN sad (D16). Hlas vždy odpovídá jazyku sady slova (= aktivnímu jazyku); hláška o chybějícím hlasu se týká jen aktivního jazyka a o hlasu druhého jazyka se nikdy nehlásí.
- Při přehrávání ikona změní stav (animované vlny, při Reduce Motion statická „přehrává se“), aby nevznikla nejistota, jestli se něco děje. Opětovné ťuknutí během přehrávání spustí znovu.
- Respektuje ztlumení a hlasitost zařízení. **Domněnka:** aplikace nemusí umět spolehlivě poznat, že je zařízení ztlumené; pokud ano, krátký text „Zvuk je ztlumený.“, pokud ne, nic nehlásíme.

**Hlášky (skutečné znění):**
| Situace | Text |
|---|---|
| Hlas pro jazyk není v zařízení (R3) | „Hlas pro {němčinu / angličtinu} v tomhle zařízení chybí, takže výslovnost teď nejde přehrát. Stáhneš ho v Nastavení → Zpřístupnění → Mluvený obsah → Hlasy.“ (cesta v iOS 26 **domněnka, ověřit**) |
| Hlas chybí a není internet | „Hlas pro {němčinu} v zařízení chybí a bez internetu ho nestáhneš. Zkus to, až budeš online.“ (stažení probíhá mimo aplikaci; text jen vysvětluje) |
| Přehrání selhalo z jiného důvodu | „Výslovnost se teď nepodařilo přehrát.“ |
Hláška je krátký toast / řádek u ikony; po několika sekundách mizí, neblokuje dál. Při opakovaném ťuknutí se neopakuje celý text: po druhém ťuknutí jen „Hlas chybí.“ a odkaz „Jak ho stáhnout“.

| Stav | Chování |
|---|---|
| První spuštění | Žádná předvýuka; první ťuknutí buď přehraje, nebo ukáže hlášku. |
| Prázdno | Slovo bez textu: ikona se nezobrazí. |
| Načítání | Ikona se mění na „přehrává se“; neurčitý okamžik načtení hlasu je zahrnut. |
| Chyba | Viz tabulku. |
| Offline | Funguje, pokud je hlas stažený (US-13, R3). |
| Dlouhý obsah | Dlouhá fráze se přehraje celá; ťuknutí zastaví/spustí znovu. |
| Sync konflikt | — |

---

#### C2 Přepínač jazyka — US-26

**Kde je:** kořeny S10, S20, S40, S50; nahoře v sidebaru iPadu; v S02 (podoba B). Nikde jinde (3.8, bod 3). Na každé kořenové obrazovce je stejně umístěný (pod velkým titulkem; na iPadu se sidebarem v sidebaru), aby šel nahmatat bez čtení. Mění jen aktivní jazyk aplikace, nikdy jazyk sady (D19).

**Varianty:**
1. **Segment (výchozí):** dva segmenty „English“ a „Deutsch“, pevné pořadí (English vlevo, Deutsch vpravo, nezávisle na tom, který je aktivní), text v jazyce samém, **bez vlajek**. Výška ≥ 44 pt, šířka segmentu ≥ 44 pt, rozestup od okraje ≥ 16 pt. Aktivní segment: zvýšený / vyplněný tvar + tučné písmo + jemná barva jazyka (3.8, bod 7); neaktivní: běžné písmo, kontrastní obrys.
2. **Řádek „k procvičení: N“ pod neaktivním segmentem:** malé písmo v běžné barvě textu, bez ikony. Je součástí cíle ťuknutí neaktivního segmentu (US-26: ťuknutí jazyk jen přepne, test nespustí). N je velikost dnešního kola neaktivního jazyka (stejné číslo, které by ukázalo S10 po přepnutí), **ne** zpoždění a ne počet po termínu; zdroj D-19. Při N = 0 se řádek nezobrazí. Neaktivní jazyk bez sad: „zatím žádné sady“. Velký počet: strop a tvar podle Q19 („20+“ nebo přesné číslo). Nikdy nevstupuje do fronty, testu ani statistik aktivního jazyka a nesčítá se s číslem aktivního jazyka (D20).
3. **Tlačítko při velkém písmu:** „Jazyk: Deutsch ▾“ (plná šířka, ≥ 56 pt) otevře sheet S02 (podoba B). Aktivuje se při přístupnostních velikostech, kdy se segment s řádkem nevejde (domněnka: od AX1, práh změřit na 393 pt i 375 pt).
4. **Sidebar iPadu:** segment v hlavičce sidebaru, nad cíli; při sbaleném sidebaru se přepínač objeví v záhlaví obsahu (varianta 1).
5. **V testu, v importu a mimo kořeny není** (3.8, bod 3, 6 a 12).

**Přístupnost:** viz 3.8, bod 8. Segment je jeden prvek-skupina s hodnotou „Aktivní jazyk: němčina“, každý segment má česky vyslovitelný popisek („angličtina“, „němčina“), neaktivní navíc „k procvičení 7 slov“ a „Dvojitým klepnutím přepnout.“

| Stav | Chování |
|---|---|
| První spuštění | Před S01 krokem 1 není. Po volbě jazyka: aktivní zvolený, druhý „zatím žádné sady“. |
| Prázdno | Aktivní jazyk bez sad: přepínač zůstává (US-26), prázdný stav obrazovky nabízí „Přepnout na …“, pokud druhý jazyk něco má. |
| Načítání | Číslo „k procvičení“ se doplní, až je známo; do té doby řádek chybí (žádný spinner, žádné „…“). |
| Chyba | Číslo nelze spočítat: řádek chybí. Přepnutí funguje dál. |
| Offline | Beze změny. |
| Dlouhý obsah | Velké písmo: tlačítko místo segmentu (varianta 3). |
| Sync konflikt | — |

---

#### C3 Štítek jazyka — US-26

**Kde je:** záhlaví hlubších obrazovek (S21, S22, S23, S41), test a souhrn (S12–S16, S42, S43), průvodce importem (S31 první řádek, S34, S35, S36), řádky v S62 a S63.

- **Podoba:** malý nenávratný štítek (není tlačítko): „EN“ nebo „DE“, volitelně s názvem („DE · němčina“). Krátká podoba v hustých řádcích (S62, S63), dlouhá v záhlavích.
- **Barva:** mimo test jemná barva jazyka (3.8, bod 7); v testu a v souhrnu neutrální (obrys a text běžné barvy), aby barvy zůstaly vyhrazené stavům odpovědí. Bez barvy musí fungovat samotným textem.
- **Velikost:** cíl dotyku nemá (nejde klepnout); při Dynamic Type se zvětšuje s okolním textem.
- **VoiceOver:** čte se „němčina“ (ne „D E“), ve stejném prvku jako záhlaví, ne jako samostatný prvek navíc.
- **Nikdy neslouží jako ovládací prvek.** Přepnout jazyk z něj nejde, ani jazyk sady (D19).

| Stav | Chování |
|---|---|
| Ostatní | Statický prvek; stavy prázdno, načítání, chyba, offline, dlouhý obsah, sync konflikt: —. |

---

## 5. Požadavky na zdroj dat

Návrh s těmito daty počítá, ale aplikace je zatím odnikud nemá nebo nejsou zaručená. Označuji je jako požadavky (na architekta, learning-scientist, prototypera), ne jako fakta. Číselná řada D-n jsou požadavky na data, nesouvisí s rozhodnutími D1–D22 v zadání.

| # | Co návrh potřebuje | Kde se použije | Komu / status |
|---|---|---|---|
| D-1 | Rozsah a výřez řádku z OCR (poloha textu) | S35 (výřez, pravý panel na iPadu) | Prototyper, spike R2. Pokud není, výřez se nezobrazuje (viz stavy S35). |
| D-2 | Jistota OCR (confidence) a ověření slovníkem pro cs/de/en | S35 značky | Prototyper (09 kap. 3.4). Návrh označuje, neopravuje. |
| D-3 | Plánovač vrací: velikost dnešního kola, počet nových slov, způsob odpovědi (výběr/psaní), štítek „Znovu“ | S10, S12, S13 | Learning-scientist (US-08, Q1, Q6) |
| D-4 | Pravidlo, kdy slovo patří do „skoro / špatně“ a kam spadá „Nevím“ | S14, S16 | Learning-scientist (G4) |
| D-5 | Detail chyby při vyhodnocení: překlep (záměna/vynechání/přidání), diakritika, člen, velké písmeno, které písmeno se liší | S14 (mikrotexty, zvýraznění) | Implementace vyhodnocení US-07 |
| D-6 | Definice „naučené slovo“ a pravidlo trendu „zlepšuje se / drží se / zatím se nezlepšuje“, minimální množství dat pro graf | S50 | Learning-scientist (Q1) |
| D-7 | Práh „slabé slovo“ a vzorce chyb (člen, diakritika, směr) z historie | S51 | Learning-scientist (Q1), US-07 |
| D-8 | Předchozí pokus stejných sad (pro srovnání v S16) | S16 | Z historie (US-10) |
| D-9 | Stav syncu a (volitelně) čas posledního syncu | S61 | Architekt: **domněnka, že SwiftData ho přímo neposkytuje**; bez zdroje se čas nezobrazuje. |
| D-10 | Stav iCloud účtu (dostupný/nepřihlášený/plný) | S01, S61 | Architekt; ověřit, jak ho zjistit. |
| D-11 | Původní hodnota při konfliktu úprav a informace o smazání vs. úpravě | S63, banner v S20 | Architekt: **CloudKit tuto historii sám nemusí uchovat** (domněnka); bez ní se S63 nerealizuje. |
| D-12 | Sloučení duplicit zachová historii procvičování a české významy | S62 | Architekt (09 kap. 1.4) |
| D-13 | Dostupnost hlasu a zjištění „chybí hlas“ (po jazycích: de-DE, en-GB) | C1 | Architekt, R3 (domněnka) |
| D-14 | Druh slova (podstatné jméno, sloveso, …) | S35, S23 (heuristika „Chybí člen“) | 09 pole `kind` existuje; kdo ho vyplňuje (OCR heuristika, uživatel), není ve stories určeno. Návrh: jen heuristika podle velkého písmene a členu, uživatel typ nevybírá. |
| D-15 | Prázdné tvary sloves se při procvičování vynechají | S41, S42 | Plánovač (US-16, US-17) |
| D-16 | Nezodpovězená slova při ukončeném kole nepřijdou o termín | S15 | Plánovač (US-08) |
| D-17 | Počet stránek při výběru fotek (maximum) | S32 | Architekt |
| D-18 | Aktivní jazyk jako uložený stav zařízení; chování při nezdaru uložení; zapomenutí po US-20 | S01, S02, C2, S64 | Architekt. Návrh: lokálně na zařízení, nesynchronizovat (Q18). |
| D-19 | Velikost dnešního kola (počet k procvičení) pro **neaktivní** jazyk bez spuštění kola; je to velikost kola, ne zpoždění; strop podle Q19 | C2 (řádek „k procvičení“), S02, S10 (řádek v hotovo-stavu) | Learning-scientist / plánovač (US-08, US-26, Q19) |
| D-20 | Počty sad, slov, sloves a „v procvičování“ po jazycích; jazyk slova, slovesa i pokusu dědí ze sady / z aktivního jazyka; jazyk sady je po vytvoření neměnný (D19) | S02 sheet, S20, S64 rozpis, všechny filtry | Architekt (D18, D19, US-04). Jazyk sloves: viz G18. |
| D-21 | Zjištění jazyka textu z OCR / CSV pro značky „Jiný jazyk?“ a banner; přiznaně **domněnka** (spolehlivost u krátkých slov), bez ní značky odpadnou | S35, S37 | Prototyper (R2, Q21) |
| D-22 | Smazání sady a založení nové sady (původní název, předvybraný správný jazyk, bez přepnutí aktivního jazyka) jako jeden tok; smazání sady odstraní i slova a pokusy (US-04) | S22, S24 (varianta 3), S30, S31 | Architekt (US-04, D19, D22). Žádný přesun slov se nenavrhuje. |
| D-23 | Chybné možnosti pro výběr z možností se berou z jiných sad téhož jazyka, když sada nemá 3 použitelné (D21); počet možností klesne jen když nestačí celý jazyk | S12 | Architekt (`DistractorPicker`, `05` kap. 13.3), learning-scientist (`07` kap. 3: pravidlo okrajového případu). Z UX hlediska žádný nový prvek. |

---

## 6. Scope creep a mezery ve stories

**Obrazovky, které jsem záměrně nenavrhl, protože nemají story (scope creep):**

| # | Co | Odkud se bere | Moje doporučení (rozhodnutí patří zadavateli) |
|---|---|---|---|
| G3 | Nápověda v testu (první písmeno, počet písmen) | Role UX-designera; žádná story | Bez story; navíc ovlivňuje hodnocení (US-08). Místo v layoutu S13 je volné. |
| G4' | Přepnutí z psaní na výběr ve chvíli, kdy se mi nechce psát | Zkušenost | Bez story; ovlivňuje plánování. „Nevím“ pokrývá nouzový východ. |
| G8 | „Vrátit poslední import“ | 09 kap. 2.1 | Bez story. Oprava je přes mazání slov (US-04). |
| G9 | Vyhledávání slov v sadách | — | Bez story. |
| G10 | Pole množné číslo, věta-příklad, výslovnostní přepis (IPA), poznámka | 09 kap. 1.1 | Bez story; UI je nenabízí. Pokud OCR přečte příponu množného čísla, nikde se zobrazovat nebude. |
| G11 | Hromadné vložení sloves, CSV sloves | 09 kap. 4.4, 5.2 | US-19 mluví jen o slovíčkách, US-16 jen o ručním zadání. |
| G15 | „O aplikaci“, verze, atribuce licencí | 09 kap. 7 | Atribuce jen pro US-21 (P2). Verze build: bez story. |
| G16 | Export dat | Q12 | Do rozhodnutí neřešeno. |
| G17 | Stav slova po směrech na kartě slova | — | Bez story; agregace jsou v S50/S51. |
| G22 | Správa jazyků (zapnutí / vypnutí jazyka, přidání třetího jazyka) | Odvozeno z „druhý jazyk jde přidat později“ | Bez story a mimo rozsah (01-vize: právě dva jazyky). Druhý jazyk se „přidá“ tím, že se přepne a vznikne v něm první sada (T19); žádný zvláštní stav „jazyk zapnutý“ nezavádím. |
| — | Změna jazyka sady, přesun nebo kopie slova do sady jiného jazyka | Odvozeno z chybně založené sady | **Rozhodnuto: nenavrhuje se (D19).** UI nemá žádnou takovou akci. Oprava je „Smazat a vytvořit znovu“ (S22). |
| — | Celkový souhrn, součet nebo srovnání přes oba jazyky | — | **Rozhodnuto: nenavrhuje se (D20)**, ani pro rodičovský pohled (US-23). |
| — | Import do sady druhého jazyka, volba jazyka v importu | Původní návrh S31 | **Rozhodnuto: navrhuje se (D22, opravené znění).** S31 má první řádek „Jazyk slovíček“ jako ovládací prvek; import aktivní jazyk nemění. |
| — | Gamifikace, odznaky, série, denní cíle | US-24, D13 | P2 a bez rozhodnutí. Série a denní cíle navíc odporují principům 1 a 3. |
| — | Rodičovský pohled, připomenutí | US-23, US-25 | P2, nenavrhováno. Architektura obrazovek je nevylučuje. |
| — | Hotové sady, kontrola výslovnosti hlasem | US-21, US-22 | P2. |

**Mezery ve stories a rozhodnutí, na kterých návrh visí:**

| # | Mezera | Dopad na obrazovky |
|---|---|---|
| G1 | US-08 nedefinuje, co aplikace nabídne, když není co opakovat | S10 „Na dnes je hotovo“, bez tlačítka „Procvičit navíc“ |
| G4 | „Nevím“ nemá ve stories oporu; předpokládám výsledek „špatně“ | S12, S13, S14 |
| G5 | Do „Ještě jednou chybná slova“ patří „skoro“ i „zatím ne“? Navrženo: obojí | S16 |
| G5b | Má mít souhrn otevřený z historie také „Ještě jednou chybná slova“? | S16 |
| G6 | Koncept rozpracované kontroly po násilném ukončení | S35 |
| G7 | [ ♪ ] u zadání v testu: story neříká výslovně | S12, S13, C1 |
| G12 | Který tvar slovesa je zadaný | S42 |
| G13 | Pomocné sloveso: segment vs. jedno pole „Perfekt“; jak se tvary skládají do výsledku pro plánovač | S42, S43 |
| G14 | Slabá místa sloves nejsou ve stories | S51 |
| G18 | Sloveso nemá sadu, ale D18 říká, že jazyk dědí. Navrhuji: jazyk slovesa = aktivní jazyk v okamžiku zadání, pak neměnný (obdoba D19); potřebuje potvrzení v US-16 / US-04 | S40, S41 |
| G19 | Nezdar uložení aktivního jazyka; chování na novém zařízení s daty z iCloudu (S01 krok 1 se ukáže znovu) | S01, S02 |
| G21 | Číslo „k procvičení“ u druhého jazyka je velikost kola **slovíček**; slovesa nemají v US-17 pravidlo termínů. Na tabu Slovesa může číslo působit zavádějícím dojmem | C2, S40 |
| G23 | US-26 uvádí příklad „Němčina · 12 slov k procvičení“; návrh má pro rozložení jen „k procvičení: 12“ pod segmentem (jazyk je v segmentu). Sjednotit znění v US-26 | C2 |
| G24 | US-26 uvádí v prázdném stavu „Vyfotit slovíčka“ a „Vytvořit sadu“, zadání pro návrh „Přidat sadu“ a „Přepnout na …“. Návrh obsahuje všechny tři (název tlačítka „Přidat sadu“). Sjednotit | S10, S20 |

**Dříve mezery, nyní rozhodnuto:** G2 (odkud brát chybné možnosti, když sada nestačí) je rozhodnuto D21, viz S12. G20 (import do neaktivního jazyka) je rozhodnuto D22 (jazyk se při importu volí), viz S31 a 3.8, bod 12. G25 (přepnutí aktivního jazyka v „Smazat a vytvořit znovu“) je vyřešeno D22: aktivní jazyk se nepřepíná, S31 se otevře s předvybraným správným jazykem (S24, varianta 3). Čísla G2, G20 a G25 se nepoužívají znovu.

---

## 7. Otevřené otázky pro zadavatele a ostatní agenty

1. **iPhone na šířku:** zamknout aplikaci na výšku, nebo navrhnout i šířku? Zadání říká „iPhone na výšku“; návrh šířku neřeší.
2. **Klávesnice v testu:** zůstává nahoře i při vyhodnocení, nebo se schová a otevře znovu? Navrhuji cíl „bez cukání“; vyžaduje zkoušku na zařízení.
3. **Počet možností (Q5)** a velikost kola (Q6): návrh používá 4 možnosti (správná + 3 chybné, v souladu s D21) a „dnešní kolo“ bez konkrétního čísla. Pravidlo pro okrajový případ, kdy celý jazyk nemá dost slov, navrhne learning-scientist (`07` kap. 3).
4. **Definice „naučené slovo“ (Q1)** a pravidlo trendu: bez nich je S50 jen kostra.
5. **Ukládání fotek (Q8):** texty v S32 a S35 předpokládají, že se fotka po uložení smaže. Změna rozhodnutí mění texty.
6. **Koncept kontroly (G6):** chceme ho? Dotýká se soukromí (R5), protože by musel uchovat obrázek nebo OCR text lokálně.
7. **Který z tabů je v buildu 1:** návrh je úplný, výběr je rozhodnutí o releasu. Podle `04-story-mapa.md` build 1 neobsahuje Slovesa, Pokrok, S61–S63. Přepínač jazyka (C2) se pak zobrazuje jen na tabech, které v buildu jsou (Procvičit, Sady).
8. **Zadání jazyka rozhraní (Q15):** návrh je čistě česky.
9. **Ověřit na zařízení (ux-verifier):** výška klávesnice a rozpočet S13/S42 na 375 × 667; chování tab baru/sidebaru iPadu na iOS 26; autokorekce vypnutá v polích; interakce „Rozdělit řádek“ v S35.
10. **Aktivní jazyk lokálně, nebo synchronizovaně (Q18):** návrh počítá s lokálním (3.8, bod 5). Při synchronizaci by nové zařízení nemuselo S01 krok 1 ukazovat a změna na iPhonu by mohla změnit jazyk na iPadu.
11. **Číslo „k procvičení“ u druhého jazyka (Q19):** přesné číslo, strop („20+“), nebo „je co procvičit“? Je to jediné číslo v aplikaci související s tím, co čeká; ověřit u learning-scientist, zda nepůsobí jako tlak (3.5).
12. **Vedlejší nabídka v „Na dnes je hotovo“ (S10):** řádek „V {angličtině} je k procvičení: {n}.“ + „Přepnout na …“ je volitelný, ne požadovaný story. Pokud je to tlak, odpadne.
13. **Ověřit na zařízení (ux-verifier), jazyk:** výška a šířka segmentů C2 (≥ 44 pt), práh přechodu na tlačítko „Jazyk: … ▾“ při velkém písmu, umístění přepínače v hlavičce sidebaru na iOS 26, kontrast a odlišitelnost dvou barev jazyka od stavových barev (i při poruše barevného vidění), čtení C2 a titulků ve VoiceOveru (česká výslovnost „English“, „Deutsch“).
14. **Znění segmentů:** zadání požaduje „English | Deutsch“ (jazyk sám), US-26 používá „Angličtina / Němčina“. Návrh: vizuálně „English | Deutsch“, texty a VoiceOver česky „angličtina / němčina“. Potvrdit.
15. *(Vyřešeno D22, číslo se nepoužívá znovu.)* „Smazat a vytvořit znovu“ aktivní jazyk nepřepíná, otevře průvodce s předvybraným správným jazykem.
16. **Špatně zvolený jazyk v importu po skenu (D22):** jazyk se volí před skenem a je pak zamčený. Pokud syn zjistí chybu až v S35 (např. po vyfocení 3 stránek), musí import zrušit a začít znovu (fotka se při zrušení zahazuje). Levnější cestou by bylo zachovat fotku po „Zrušit import“, což je změna Q8 (ukládání fotek). Neřeším, jen upozorňuji; banner „Jiný jazyk?“ v S35 chybu odhalí včas.

---

## 8. Mapa story → obrazovky

| Story | Priorita | Obrazovky |
|---|---|---|
| US-01 První spuštění | P0 | S01 (krok 1 = volba jazyka), S10 (prázdný stav), S32 |
| US-02 Import fotkou | P0 | S30, S31 (jazyk slovíček jako první řádek a ovládací prvek, sady zvoleného jazyka; D22), S32, S34 (řádek „Importuji do: …“), S35 (banner a značka „Jiný jazyk?“), S36 |
| US-03 Oprava OCR | P0 | S35 (a výřezy; člen jen v německé sadě), S36, banner S35 |
| US-04 Správa sad a slov | P0 | S11, S20, S21, S22 (jazyk jen zobrazený, „Smazat a vytvořit znovu“; D19), S23, S24 (varianta 3), S31 |
| US-05 Test: mix výběr/psaní | P0 | S10, S12 (chybné možnosti z téhož jazyka; D21), S13, S15 (jazyk zamčený: štítek C3) |
| US-06 Oba směry | P0 | S11 (omezení směru, „Jen z němčiny do češtiny“), štítek směru v S12/S13 |
| US-07 Vyhodnocení | P0 | S14 (výběr: S12, psaní: S13; členy a přehlásky jen němčina) |
| US-08 Plánování | P0 | bez obrazovky; projevy v S10, S12/S13 („Znovu“), S14; údaj „k procvičení“ v C2 |
| US-09 Souhrn | P0 | S15, S16 (jen jeden jazyk, D20) |
| US-10 Uložení | P0 | bez obrazovky; chyba ukládání v S14 |
| US-11 iCloud sync | P0 | S01 (krok 3), S61 |
| US-12 Konflikty | P0 | S20 (banner), S62, S63 |
| US-13 Offline | P0 | průřezově, kap. 3.6, řádek „Offline“ každé obrazovky |
| US-26 Přepnutí aktivního jazyka | P0 | **S02** (podoby A, B, C), C2, C3, kap. 3.8; prázdné stavy S10, S20, S40, S50, S51; S01 krok 1; S60 (společné); S64 (text o obou jazycích); jazyk zamčený v S12–S16, S42, S43 |
| US-14 Historie pokroku | P1 | S50 (a S16 z historie; bez filtru „Vše“, D20) |
| US-15 Slabá místa | P1 | S51 |
| US-16 Zadání sloves | P1 | S40, S41 |
| US-17 Procvičování sloves | P1 | S40, S42, S43, S16 (varianta) |
| US-18 Výslovnost | P1 | C1 (S12, S13, S14, S16, S21, S23, S42, S43) |
| US-19 CSV import | P1 | S30, S35, S37 (chyba „dva jazyky v souboru“) |
| US-20 Smazání dat | P1 | S60, S64 (oba jazyky; návrat na S01 včetně volby jazyka) |

Každá obrazovka v kap. 4 má alespoň jednu story. Obrazovky bez story jsou v kap. 6 označené jako scope creep a nenavrženy. Nová obrazovka S02 a komponenty C2, C3 vycházejí z US-26 (P0, D18). Nabídka „Smazat a vytvořit znovu“ vychází z D19 a v US-04 zatím nemá vlastní kritérium.

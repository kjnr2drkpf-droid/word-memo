# 06 — UX návrh

Autor: ux-designer, stav k 2026-11-07. Zdroj pravdy: `00-zadani.md`; stories: `02-user-stories.md`; obsah a import: `09-obsah-a-import.md`. Navrhuji, nerozhoduji o prioritě ani rozsahu (`00-zadani.md`). Tvrzení o platformě jsou **domněnka**, dokud je neověří architekt nebo ux-verifier na zařízení; nic z platformy jsem v této session neověřoval.

## Závěr

1. **Aplikace má jednu smyčku a čtyři místa kolem ní.** Smyčka je Procvičit → vyhodnocení → souhrn. Okolo jsou Sady (slova, import fotkou), Slovesa (samostatný vstup, D8), Pokrok (historie a slabá místa) a Nastavení (iCloud, duplicity, smazání dat). Na iPhonu je to tab bar o 5 položkách, na iPadu sidebar se stejnými 5 cíli. Layout se řídí size class, ne typem zařízení.
2. **Test je celoobrazovkový režim bez tab baru.** Nahoře je jen „Skončit“ a ukazatel postupu (bez časomíry). Zadání a tlačítko výslovnosti jsou vždy nad klávesnicí. Pole a tlačítko „Zkontrolovat“ jsou ukotvené těsně nad klávesnicí, zadání se smí zmenšit nebo rolovat, nikdy ho klávesnice nepřekryje. Na SE velikosti (667 pt výšky) to vychází na dotyk, viz S13.
3. **Neúspěch nedemotivuje konkrétně takto:** v celé aplikaci není červená mimo potvrzení mazání, žádný symbol „X“, žádné body ani série. „Špatně“ je neutrální modrofialová s ikonou „znovu“, „skoro“ je jantarová, „správně“ je klidná zelená. Stav se nikdy nesděluje jen barvou (ikona + slovo + tvar). Souhrn začíná tím, co se povedlo, a srovnání s minulým pokusem ukazuje jen tehdy, když je lepší nebo stejné.
4. **Kontrolní obrazovka importu (S35) je nejdůležitější obrazovka aplikace.** Je jedna pro fotku i CSV. Podezřelé řádky jsou nahoře a rozbalené s výřezem z fotky, ostatní jsou kompaktní. Každé pole je přímo editovatelné jedním ťuknutím. Uloží se jen to, co je bez značky nebo co syn opravil či potvrdil.
5. **Nepsání = „Nevím“.** Každá otázka má tlačítko „Nevím“. Vyhodnotí se jako „špatně“ bez trestu a ukáže správnou odpověď. Prázdné pole nelze potvrdit, aby nevznikaly omyly. Potřebuje potvrzení learning-scientist (viz kap. 7).
6. **Mezery ve stories (kap. 6):** nenavrhl jsem nápovědu v testu, přepínání psaní → výběr, vyhledávání, „Vrátit import“, export dat, editaci množného čísla/příkladu/IPA, hromadné vkládání sloves a „O aplikaci“. Žádná z nich nemá story. Dále chybí rozhodnutí o „nic k opakování“ (US-08) a o konceptu kontroly po násilném ukončení (US-03).
7. **Data, která aplikace zatím nemá odkud vzít (kap. 5):** výřezy řádků z fotky (R2), stav a čas syncu (R4), původní hodnota při konfliktu (US-12), pravidlo „zlepšuje se / drží se“ (Q1), definice „naučené slovo“ (Q1). Návrh s nimi počítá jako s požadavky na zdroj, ne jako s hotovými daty.
8. **US-18 vs. D16:** story zmiňuje en-GB/en-US, zadání (D16) rozhodlo en-GB. Q10 je tím zodpovězena, v návrhu žádná volba varianty angličtiny není.
9. **Gamifikace, rodičovský pohled a připomenutí nejsou navrženy** (P2, bez rozhodnutí). Architektura obrazovek je nevylučuje: souhrn (S16) a Pokrok (S50) jsou místa, kde by se pozdější odměny a agregace objevily.

---

## 0. Východiska a značení

**Zařízení pro wireframy (domněnka, rozměry v pt):** iPhone na výšku 393 × 852, kontrolní nejmenší 375 × 667 (třída SE); iPad 11" na výšku 834 × 1194, na šířku 1194 × 834. Výška klávesnice: iPhone zhruba 260–300 pt, iPad na šířku zhruba 350–400 pt (odhad, změřit na zařízení). iPhone na šířku se nenavrhuje (zadání říká iPhone na výšku); chování po otočení je otevřená otázka (kap. 7).

**Compact vs. regular:** wireframy „iPhone“ platí pro každé compact width, tedy i pro iPad v Split View / Slide Over v úzkém okně. Wireframy „iPad“ platí pro regular width (domněnka: standardní chování size class).

**Značení v ASCII:** `[ Text ]` tlačítko, `( )` přepínač nebo výběr, `[ ♪ ]` tlačítko výslovnosti, `[!]` upozornění, `[ok]` správně, `[~]` skoro správně, `[↻]` zatím ne (neutrální, ne červené).

**ID obrazovek:** S = obrazovka, C = sdílená komponenta. U každé je story, ze které vychází.

---

## 1. Úkoly uživatele a toky

Úkoly jsou formulované z pohledu syna (nebo otce). Číslo za úkolem je story.

| # | Úkol | Story | Vstup | Kroky (obrazovky) | Konec |
|---|---|---|---|---|---|
| T1 | Otevřít aplikaci poprvé a hned vědět, co dělat | US-01 | Spuštění z čisté instalace | S01 (1–3 kroky) → „Vyfotit slovíčka“ | S31 (kam uložit) |
| T2 | Nafotit stránku ze sešitu a zkontrolovat, co aplikace přečetla | US-02, US-03 | Sady → „+“ nebo první spuštění | S30 → S31 → S32 (jen poprvé) → systémový skener → S34 → S35 → S36 | Slova uložená v sadě; nabídka „Procvičit teď“ |
| T2b | Totéž z knihovny fotek (otec vkládá fotku) | US-02 | Sady → „+“ → „Vybrat z fotek“ | S30 → S31 → výběr fotky → S34 → S35 → S36 | Jako T2 |
| T3 | Přidat nebo opravit jedno slovo ručně | US-04 | S21 → „+“ nebo řádek slova | S23 | Slovo uložené |
| T4 | Mít slova v sadách a vybrat, co se bude zkoušet | US-04 | Sady; Procvičit → „Co procvičit“ | S20 → S21; S11 | Sada v procvičování |
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
| T16 | Smazat všechna data | US-20 | Nastavení → „Smazat všechna data“ | S60 → S64 | Návrat na S01 |
| T17 | Používat vše bez internetu | US-13 | — | Průřezově (kap. 3.6) | — |

**Bez vlastní obrazovky:** US-08 (plánování) a US-10 (ukládání po každé odpovědi) jsou chování, ne obrazovky. Projevují se v S10 (z čeho se kolo skládá), v štítku „Znovu“ v S12/S13 a v mikrotextu S14.

---

## 2. Informační architektura

### 2.1 Navigační mapa

```
Aplikace
├─ [první spuštění] S01 Úvod ──► S31 Kam uložit ──► import (T2)
│
├─ PROCVIČIT (tab 1)
│    S10 Start ─► S11 Co procvičit (sheet)
│        └─► TEST (celá obrazovka, bez tab baru)
│              S12 výběr / S13 psaní ─► S14 vyhodnocení ─► … ─► S16 Souhrn
│              S15 Skončit (sheet)
│
├─ SADY (tab 2)
│    S20 Seznam sad ─► S21 Detail sady ─► S23 Slovo
│        │                  └─► S22 Úprava sady (sheet)
│        └─► „+“ S30 Přidat slova ─► S31 ─► S32 ─► skener ─► S34 ─► S35 ─► S36
│                                    └──► CSV: výběr souboru ─► S35 (S37 = vstupní kontrola souboru)
│
├─ SLOVESA (tab 3)
│    S40 Seznam ─► S41 Přidat / upravit ;  S40 ─► S42 Procvičování ─► S43 ─► S16 (varianta)
│
├─ POKROK (tab 4)
│    S50 Vývoj ─► S16 (souhrn pokusu) ;  S51 Slabá místa ─► test (S12/S13) ;  ─► S23
│
└─ NASTAVENÍ (tab 5)
     S60 ─► S61 iCloud ;  S62 Duplicity ;  S63 Přepsané změny ;  S64 Smazat všechna data
```

C1 (tlačítko výslovnosti) je v S12, S14, S16, S23, S42, S43.

### 2.2 iPhone (compact width): tab bar

Pět položek, ikona + krátký popisek (popisky se nesmí zkrátit na „…“ při větším Dynamic Type; při AX velikostech se tab bar chová podle systému):

| Tab | Popisek | Obsah |
|---|---|---|
| 1 | Procvičit | S10, test |
| 2 | Sady | S20–S24, import |
| 3 | Slovesa | S40–S43 |
| 4 | Pokrok | S50, S51 |
| 5 | Nastavení | S60–S64 |

- Výchozí tab po spuštění: **Procvičit**. Při prázdné knihovně S10 sám nabídne „Vyfotit slovíčka“.
- Test (S12–S16) se otevírá jako celoobrazovkový režim bez tab baru, aby se nedalo omylem odejít a aby nic neubíralo místo nad klávesnicí.
- Import (S31–S36) je modální průvodce s vlastním „Zrušit“ vlevo nahoře, ne push do tab baru.
- Které z tabů jsou v konkrétním TestFlight buildu vidět (Slovesa, Pokrok jsou P1), je rozhodnutí o rozsahu releasu, ne moje. Návrh počítá s tím, že tab, jehož funkce v buildu není, se prostě nezobrazí.

### 2.3 iPad (regular width): sidebar a split view

- **Na šířku:** sidebar s týmiž 5 cíli (Procvičit, Sady, Slovesa, Pokrok, Nastavení) vlevo, vedle něj obsah. Sady a Slovesa mají dvě sloupce obsahu (seznam | detail), takže celkem tři sloupce. Sidebar jde sbalit.
- **Na výšku:** sidebar je skrytý a 5 cílů je v horní liště (nebo se sidebar vysouvá; domněnka: iOS 26 umí obojí z jednoho prvku, **ověřit** před implementací). Sady a Slovesa jsou dva sloupce (seznam | detail).
- **Test, import, souhrn:** na iPadu se otevírají jako celoobrazovkový režim nebo jako centrovaný formát (max. šířka obsahu 560–640 pt), nikdy ne roztažené přes celou šířku. Řádek textu delší než ~70 znaků se špatně čte.
- **Split View / Slide Over aplikace:** v úzkém okně se použije iPhone layout (kap. 0).
- **Externí klávesnice:** Return = „Zkontrolovat“, v dalším kroku Return = „Další“ (S13, S14). Šipky a Tab fungují v S35. Žádné další zkratky nenavrhuji.
- **Plovoucí a rozdělená klávesnice na iPadu:** zadání, pole a tlačítko se řídí skutečnou výškou klávesnice, ne pevnou hodnotou.

---

## 3. Průřezová pravidla

### 3.1 Tón a jazyk
- Tykání, krátké věty, věcně. Žádné vykřičníky kromě výjimečně v „Hotovo“ stavech, žádné „Úžasné!“, žádná maskotka, žádné emoji. Cíl: 13letý, který nechce „dětskou“ appku.
- Chybová hláška vždy říká, **co se stalo, co je v pořádku a co lze udělat**. „Nic se neztratilo“ píšu jen tam, kde to platí.
- Počty se skloňují: 1 slovo, 2–4 slova, 5 a víc slov; 1 dvojice, 2–4 dvojice, 5 dvojic; 1 tvar, 2–4 tvary, 5 tvarů. V mikrotextech níže `{n}` předpokládá správné skloňování (požadavek na implementaci).

### 3.2 Role barev a tvar stavů (bez hodnot)
| Stav | Role barvy | Ikona | Slovo |
|---|---|---|---|
| Správně | klidná zelená | zaškrtnutí v kruhu | „Správně“ |
| Skoro správně | jantarová | kruh s „≈“ | „Skoro“ |
| Zatím ne | modrofialová (ne červená) | kruh se šipkou „znovu“ | „Zatím ne“ |
| Výběr (ještě nevyhodnoceno) | akcentová | — | — |
| Označeno k ověření (import) | jantarová | „!“ v kruhu | „Ověř“ |
| Mazání (jen potvrzení) | červená, nikde jinde | — | „Smazat“ |

- Stav nikdy jen barvou: ikona + slovo + zvýrazněné písmeno (tučně a podtržené, ne jen barevné). Kvůli barvoslepým uživatelům (červeno-zelená), VoiceOveru a svitu slunce.
- Kontrast textu ≥ 4,5:1, ikon a ohraničení ≥ 3:1 (WCAG AA, domněnka, že to je dostatečná laťka; ověří ux-verifier). Světlý i tmavý režim.
- Vizuální směr: klidný, hodně prostoru, velká tučná typografie pro slovo, jedna akcentová barva, žádné ilustrace, žádné „odznaky“. Animace jemné, respektují Reduce Motion. Žádná animace „slavení“.

### 3.3 Cíle a čitelnost
- Dotykový cíl ≥ 44 × 44 pt (doporučení HIG, z paměti), primární tlačítka a možnosti odpovědí ≥ 56 pt na výšku. Rozestup mezi sousedními cíli ≥ 8 pt.
- Zadaná slova ve velkém typu (titulek), pole odpovědi ≥ 20 pt písma.
- Dynamic Type: všechny obrazovky musí fungovat do největších přístupnostních velikostí. Pravidla: možnosti odpovědí se vždy skládají do jednoho sloupce, řádky tabulky v S35 se zalamují na dva řádky, ovládací lišty se zalamují, nic se neořezává, obrazovka se smí rolovat, ale **pole a tlačítko „Zkontrolovat“ zůstávají ukotvené nad klávesnicí**.
- VoiceOver: u každého ovládacího prvku popisek; po vyhodnocení se focus přesune na výsledek a přečte se např. „Skoro správně. Jedno písmeno se prohodilo. Správně: das Kind.“ Ikona stavu má popisek, není jen dekorace.
- Haptika: jemná pozitivní při „správně“. Žádná negativní haptika, žádný zvuk při chybě.

### 3.4 Pole pro psaní odpovědi
- Vypnutá autokorekce, prediktivní nápověda, automatické velké písmeno a kontrola pravopisu (jinak klávesnice napoví odpověď nebo ji pokazí). **Požadavek na implementaci**, ověří ux-verifier.
- Nad klávesnicí je lišta znaků podle jazyka odpovědi: pro němčinu `ä ö ü ß`, pro češtinu `á č ď é ě í ň ó ř š ť ú ů ý ž` (vodorovně rolovatelná). Neukládá se předpoklad, že má syn nainstalovanou českou nebo německou klávesnici.
- Lišta nenabízí členy der/die/das (aby se člen nepřipomínal předem; viz S14, „člen chybí“).

### 3.5 Žádný tlak
- Žádný časovač, odpočet ani „tempo“. Postup je počet slov (např. „4 z 12“), ne čas.
- Žádný počet resty. S10 ukazuje velikost dnešního kola, ne „máš 87 slov po termínu“ (US-08).
- Přerušení (zavření aplikace, hovor) nic neztratí: každá odpověď je uložená okamžitě (US-10), rozepsaný text v poli přežije přepnutí do jiné aplikace.

### 3.6 Offline (US-13)
- **Žádný globální banner „jste offline“.** Aplikace nehlásí chybu sítě tam, kde síť nepotřebuje. Funguje offline: S10–S16, S20–S24, import fotkou (S31–S36), CSV, S40–S43, S50–S51, přehrání výslovnosti (pokud je hlas stažený).
- Síť potřebuje jen: stav a odeslání syncu (S61), smazání iCloud kopie (S64), případné stažení hlasu (mimo aplikaci, v Nastavení iOS). Tam a jen tam se offline stav hlásí (texty u těchto obrazovek).

### 3.7 Sync konflikt (US-12)
- Konflikt není modální a nezastavuje práci. V Sadách se objeví nenásilný banner jen tehdy, když je potřeba rozhodnutí (smazané vs. upravené slovo). Duplicity a přepsané změny čekají v Nastavení (S62, S63) a v řádku Nastavení se ukáže tečka s počtem. Žádný odznak na ikoně aplikace.
- Plán opakování se po sloučení přepočítá z historie (US-12) bez zobrazení uživateli.

---

## 4. Obrazovky

U každé obrazovky: story, účel, wireframe iPhone, iPad (na výšku a na šířku), stavy. Stavový inventář má pevných šest řádků (prázdno, načítání, chyba, offline, dlouhý obsah, sync konflikt) a podle potřeby další. „—“ znamená, že stav na obrazovce nenastává (s důvodem).

### 4.1 První spuštění

#### S01 Úvod — US-01

**Účel:** syn hned ví, co dělat. Žádná registrace, přihlášení ani souhlas s analytikou (žádná se nesbírá, R5). Kroky jsou nezávislé: minimální podoba je jen krok 1 s tlačítkem.

**iPhone (na výšku), krok 1 z max. 3:**
```
┌────────────────────────────────┐
│                      Přeskočit │
│                                │
│                                │
│   Slovíčka ze sešitu           │
│   rovnou do telefonu.          │   velký titulek
│                                │
│   Vyfoť stránku se slovíčky.   │
│   Přečtu ji přímo v tomhle     │
│   zařízení a ty jen zkontroluješ,│
│   jestli jsem to přečetl dobře.│
│                                │
│                                │
│   ○ ● ○ (jen při více krocích) │
│ [      Vyfotit slovíčka      ] │   primární, 56 pt
│        Další                   │   sekundární text
└────────────────────────────────┘
```
Krok 2 (jak se zkouší):
- Titulek: „Krátké procvičování, žádný stres.“
- Text: „Nové slovo si vybereš z nabídky, to, co už znáš, napíšeš. Když se spleteš, ukážu ti správnou odpověď a slovo se ti brzy vrátí. Žádné časovače ani trestné body.“
- Tlačítka: „Vyfotit slovíčka“ (primární), „Přeskočit“.

Krok 3 (zobrazí se jen když iCloud není dostupný, US-01 + US-11):
- Titulek: „iCloud teď není zapnutý.“
- Text: „Aplikace funguje i tak, jen slovíčka a výsledky budou zatím jen v tomhle zařízení. Až se k iCloudu přihlásíš, přenesou se samy.“
- Tlačítka: „Pokračovat“ (primární), „Otevřít Nastavení“.

Když iCloud je dostupný, krok 3 se nezobrazí. Pokud by aplikace v buildu ještě nesynchronizovala (build 1 podle `04-story-mapa.md`), text kroku 2 nemluví o iCloudu vůbec.

**iPad:** stejný obsah v centrovaném bloku (max. šířka 560 pt), svisle na střed, na šířku i na výšku beze změny; žádná sidebar, dokud úvod neskončí.

**Žádost o fotoaparát se tu neobjevuje** (US-01): přijde až v S32 ve chvíli, kdy ji import potřebuje.

| Stav | Chování |
|---|---|
| První spuštění | To je tato obrazovka. Po „Vyfotit slovíčka“ nebo „Přeskočit“ už se nikdy nezobrazí (kromě po US-20). |
| Prázdno | — (obrazovka je sama „prázdný“ stav aplikace). |
| Načítání | Zjišťování stavu iCloudu neblokuje; krok 3 se případně přidá až po zjištění. |
| Chyba | Stav iCloudu nezjištěn: chováme se jako „dostupný“, nic se nehlásí (správné hlášení je v S61). |
| Offline | Beze změny, úvod síť nepotřebuje. |
| Dlouhý obsah | Dynamic Type velký: text se rolue, tlačítko „Vyfotit slovíčka“ zůstává ukotvené dole. |
| Sync konflikt | — |

---

### 4.2 Procvičit a test

#### S10 Procvičit — start — US-05, US-06, US-08, US-04

**Účel:** jedno ťuknutí k procvičování. Ukazuje jen aktuální nabídku, ne resty (US-08).

**iPhone:**
```
┌────────────────────────────────┐
│ Procvičit                      │   velký titulek
│                                │
│ ┌────────────────────────────┐ │
│ │ Dnešní kolo                │ │
│ │ 12 slov · z toho 4 nová    │ │   data z plánovače (US-08)
│ │                            │ │
│ │ Deutsch 1, Project 3     › │ │   → S11
│ │ Směr: oba                  │ │
│ │                            │ │
│ │ [         Začít          ] │ │   primární, 56 pt
│ └────────────────────────────┘ │
│                                │
├────────────────────────────────┤
│ Procvičit Sady Slovesa Pokrok Nastavení│
└────────────────────────────────┘
```
**iPad na výšku:** karta „Dnešní kolo“ centrovaná, max. šířka 560 pt, nahoře ve volném prostoru; pod ní zůstává prázdno (nepřidávat obsah bez story).
**iPad na šířku:** totéž, vedle sidebaru; karta na střed.

**Stavy:**

| Stav | Chování a text |
|---|---|
| První spuštění / prázdná knihovna | Místo karty: titulek „Zatím tu nejsou žádná slovíčka.“ Text: „Vyfoť stránku ze sešitu a za chvíli můžeš procvičovat.“ Tlačítko „Vyfotit slovíčka“ (→ S31). Sekundárně „Přidat ručně“ (→ S23). |
| Slova jsou, ale není co opakovat | Titulek: „Na dnes je hotovo.“ Text: „Žádné slovo teď nečeká na opakování. Další přijdou na řadu později.“ Tlačítko „Přidat nová slova“ (→ S30). **Mezera:** US-08 nedefinuje „volné procvičování navíc“ (kap. 6, G1); do té doby není tlačítko „Procvičit i tak“. |
| Žádná sada v procvičování | Karta: „Není vybraná žádná sada.“ Tlačítko „Vybrat sady“ (→ S11). „Začít“ nedostupné. |
| Načítání | Skeleton karty (bez spinneru, max. desetiny sekundy; plánovač běží lokálně). |
| Chyba | Plánovač nevrátil kolo: „Kolo se nepodařilo připravit. Zkus to prosím znovu.“ [ Zkusit znovu ]. |
| Offline | Beze změny (US-13). |
| Dlouhý obsah | Názvy sad se zkrátí na „Deutsch 1 a další 2“; v S11 jsou celé. |
| Sync konflikt | Pokud mezitím druhé zařízení smazalo slova z plánovaného kola, kolo se připraví znovu bez hlášky. |

---

#### S11 Co procvičit (sheet) — US-04, US-06

**Účel:** vybrat sady do testu a omezit směr (US-04: „Vyberu, které sady jdou do testu“; US-06: „Syn může omezit test na jeden směr“, předpoklad).

```
┌────────────────────────────────┐
│ Co procvičit              Hotovo│
│ Sady                           │
│  [x] Deutsch 1     DE · 34 slov│
│  [x] Project 3     EN · 21 slov│
│  [ ] Unit 4        EN ·  9 slov│
│ Směr                           │
│  (•) Oba směry                 │
│  ( ) Jen cizí → česky          │
│  ( ) Jen česky → cizí          │
└────────────────────────────────┘
```
**iPhone:** sheet od spodu (půl až celá výška). **iPad:** popover od karty na S10 (na výšku i na šířku), šířka 360 pt.

| Stav | Chování a text |
|---|---|
| Prázdno | Žádné sady: „Nemáš zatím žádné sady.“ + „Vyfotit slovíčka“. |
| Načítání | — (lokální data). |
| Chyba | — |
| Offline | Beze změny. |
| Dlouhý obsah | Dlouhý seznam rolue, „Hotovo“ je ukotvené v záhlaví. Dlouhý název se zalomí na dva řádky. |
| Sync konflikt | — |

---

#### S12 Test — výběr z možností — US-05, US-06 (US-07 pro výběr: jen správně/špatně)

**Účel:** nová slova výběrem. Počet možností je otevřená otázka Q5; návrh funguje pro 3–5 možností, výchozí předpoklad **4**. Chybné možnosti jsou z téže sady (US-05). Pořadí možností se míchá.

**iPhone:**
```
┌────────────────────────────────┐
│ Skončit            ▓▓▓▓░░░░ 4/12│   horní lišta
│ NĚMČINA → ČEŠTINA    Vyber     │   směr + způsob (štítek)
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
- Ťuknutí na možnost ji jen **vybere** (zvýrazní); odpověď se vyhodnotí až po „Zkontrolovat“. Důvod: chybné ťuknutí se dá opravit a rytmus je stejný jako u psaní (stejné tlačítko na stejném místě). Odpovídá US-05: „Odpověď lze potvrdit kdykoli“.
- Směr česky → cizí: zadání je české slovo, možnosti jsou cizí slova (u němčiny včetně členu). [ ♪ ] se u zadání **nezobrazuje**, kdyby prozradilo odpověď; objeví se až v S14.
- Směr cizí → česky: [ ♪ ] u zadání je k dispozici (výklad: US-18 „Given slovo“; viz G7 v kap. 6).
- Štítek „Znovu“ vedle směru, pokud se slovo vrací v téže relaci po chybě. Jmenovatel postupu (12) se kvůli tomu nemění (jinak by lišta couvala).
- Dynamic Type velký: možnosti zůstávají v jednom sloupci, zadání se rolue; „Zkontrolovat“ ukotvené dole.

**iPad na výšku:** obsah centrovaný, max. šířka 560 pt, možnosti pod sebou (nebo mřížka 2 × 2 při krátkých textech do 2 řádků).
**iPad na šířku:** zadání vlevo (karta, šířka ~40 %), možnosti vpravo pod sebou, „Nevím“ a „Zkontrolovat“ pod možnostmi. Horní lišta přes celou šířku. Sidebar se při testu skrývá.

| Stav | Chování a text |
|---|---|
| Příliš málo chybných možností | Sada má méně než 4 slova: počet možností klesne (min. 2 podle Q5). Pokud nelze sestavit ani 2 možnosti, **mezera pro learning-scientist**: pravidlo, odkud brát chybné možnosti, když sada nestačí (G2). Návrh neimprovizuje. |
| Prázdno | — (test se nezačne bez slov, viz S10). |
| Načítání | — (lokální data); přechod mezi slovy bez spinneru. |
| Chyba | Odpověď se nepodařilo uložit: viz S14. |
| Offline | Beze změny. |
| Dlouhý obsah | Dlouhá možnost se zalomí (až 3 řádky), pak „…“ a plná délka se čte VoiceOverem i v S14. |
| Sync konflikt | Slovo mezitím smazané na druhém zařízení: kolo se bez hlášky přeskočí na další slovo. |
| Nic nevybral | „Zkontrolovat“ je nedostupné, pod ním drobný text: „Vyber odpověď, nebo ťukni na Nevím.“ |

---

#### S13 Test — psaní — US-05, US-06, US-07

**Účel:** známá slova psaním. Hlavní návrhová otázka je prostor nad klávesnicí.

**Rozpočet výšky, iPhone na výšku (domněnka, změřit):**
- 393 × 852: nad klávesnicí zbývá zhruba 520 pt. Lišta (44) + štítky (28) + zadání (110) + pole (56) + lišta znaků (44) + tlačítka (56) + rozestupy (~60) = ~400 pt. Vejde se s rezervou.
- 375 × 667 (kontrolní): nad klávesnicí zbývá zhruba 340–380 pt. Stejný součet je těsný. Proto platí pravidlo: **zadání se smí zmenšit (min. 1 řádek) nebo rolovat, pole + lišta znaků + tlačítka jsou ukotvené nad klávesnicí a nikdy se neposouvají mimo obrazovku.** Při Dynamic Type nad „Large“ se karta zadání mění v rolovatelný blok.

```
┌────────────────────────────────┐
│ Skončit            ▓▓▓▓▓░░░ 5/12│
│ ČESKY → NĚMČINA        Napiš   │
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
│                    ČESKY → NĚMČINA      Napiš             │
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
| Přepnutí do jiné aplikace | Rozepsaný text i otázka zůstanou. |

---

#### S14 Vyhodnocení odpovědi — US-07, US-08, US-10, US-18

**Účel:** říct, jak to dopadlo, ukázat správný tvar a pokračovat. Žádné srážky bodů, žádná červená. Vyhodnocení probíhá lokálně a deterministicky (US-07).

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
│ ČESKY → NĚMČINA                │
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
| Německá přehláska | „Pozor na přehlásku: tady patří ü.“ (písmeno dle případu) |
| `ß` vs. `ss` | „Tady se píše ß, ne ss.“ |
| Špatný člen u správného slova | „Slovo máš dobře, jen člen je jiný: der Tisch, ne die Tisch.“ (správný člen jako první, tvar „Správně“ ukáže celý) |
| Chybí velké písmeno u německého podstatného jména | „Německá podstatná jména se píšou s velkým písmenem: Tisch.“ |

*Člen chybí (US-07: vyzvat k doplnění, nepočítat jako chybu):* karta nemá stav a odpověď se nehodnotí, dokud syn člen nedoplní:
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
Pokud syn dosud neodpověděl na žádné slovo: sheet nevyskakuje, „Skončit“ rovnou vrátí na S10 bez souhrnu.
Formulace „Zbylá slova se ti nabídnou příště“ je pravda jen tehdy, když plánovač nepřesune termíny nezodpovězených slov; **požadavek na plánovač (kap. 5).**

| Stav | Chování |
|---|---|
| Ostatní | Prázdno, načítání, chyba, offline, dlouhý obsah, sync konflikt: — (jednoduchý sheet bez dat). |

---

#### S16 Souhrn po pokusu — US-09 (varianta sloves: US-17; z historie: US-14)

**Účel:** začít tím, co se povedlo, ukázat, co procvičit, a nikam netlačit.

**iPhone:**
```
┌────────────────────────────────┐
│ Souhrn                         │
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
- Srovnání „O {d} víc než minule.“ nebo „Stejně jako minule.“ jen při ≥ minule. Při horším výsledku řádek **vůbec není**. Při prvním pokusu řádek není a nezůstává po něm prázdné místo. **Zdroj dat:** předchozí pokus stejných sad (US-10).
- „Na procvičení“ obsahuje slova „skoro“ a „zatím ne“ s **správnou odpovědí** (US-09). Zda se do „Ještě jednou chybná slova“ počítají i „skoro“, je předpoklad (G5): ano, obě skupiny.
- Předčasné ukončení: nad titulkem řádek „Skončil jsi po 5 z 12 slovech. Tohle je souhrn toho, co jsi stihl.“ (US-09).
- Bez chybných slov: „Ještě jednou chybná slova“ se nezobrazí, zůstane jen „Hotovo“.
- „Hotovo“ vrací na S10; žádné „Chceš pokračovat?“, žádný nátlak (US-09).
- Varianta sloves (US-17): hlavička „Nepravidelná slovesa“, titulek „Tvary, které sedí: {k} z {m}.“; seznam „Na procvičení“ ukazuje sloveso a tvar, který nesedí (viz S43). Souhrn a historie slovíček a sloves se nemíchají.
- Z historie (S50): stejná obrazovka, nahoře datum („Neděle 2026-11-01“), bez tlačítka „Ještě jednou chybná slova“, pokud ho US-09 pro historii nepožaduje (G5b: nerozhodnuto, návrh: tlačítko zůstane, vyrobí nové kolo ze stejných slov).

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

Je to jeden průvodce pro fotku, knihovnu fotek a CSV. Mikrotexty a stavy odpovídají `09-obsah-a-import.md`, kap. 2 a 4. Obrázek se zpracuje v zařízení; po uložení se smaže (předpoklad Q8, výchozí chování v 09).

#### S30 Přidat slova (volba zdroje) — US-02, US-19, US-04

```
┌────────────────────────────────┐
│ Přidat slova               ✕   │
│ [ Vyfotit stránku            ] │
│ [ Vybrat z fotek             ] │
│ [ Načíst soubor CSV          ] │   (US-19, P1)
│ [ Napsat ručně               ] │   → S23
└────────────────────────────────┘
```
**iPhone:** sheet od spodu, velké řádky ≥ 56 pt. **iPad:** popover od „+“ (na výšku i na šířku).
„Načíst soubor CSV“ se zobrazí jen, pokud je US-19 v buildu.

| Stav | Chování |
|---|---|
| Fotoaparát nedostupný (např. zařízení bez kamery) | „Vyfotit stránku“ je šedé, řádek pod ním: „Fotoaparát na tomhle zařízení není. Vyber fotku z knihovny.“ |
| Ostatní | Prázdno, načítání, chyba, offline, dlouhý obsah, sync konflikt: — (statický výběr; vše funguje offline). |

---

#### S31 Kam uložit — US-02, US-04 (jazyk patří k sadě, US-01)

**Účel:** zvolit sadu a jazyk **před** snímkem (nápověda pro OCR a směr sloupců, 09 kap. 2.1).

```
┌────────────────────────────────┐
│ Zrušit       Kam to uložit?    │
│ Sada                           │
│ (•) Deutsch 1          DE      │
│ ( ) Project 3          EN      │
│ ( ) Nová sada                  │
│     Název  [ Unit 2          ] │   při „Nová sada“
│     Jazyk  (Angličtina)(Němčina)│  povinné při nové sadě
│ [         Pokračovat         ] │
└────────────────────────────────┘
```
- Při první cestě (žádné sady) je výchozí „Nová sada“. Název je povinný, jazyk je povinný a nemá výchozí hodnotu (nehádáme).
- Jazyk existující sady se tu nemění.
- „Pokračovat“ nedostupné, dokud nejsou vyplněné název a jazyk.
- Zástupný text v názvu: „Např. Project 3 — Unit 2“.

**iPad:** formát (centrovaný sheet 540 pt); na šířku i na výšku stejný.

| Stav | Chování a text |
|---|---|
| Prázdno | Bez sad: viz výše; „Nová sada“ rozbalená. |
| Načítání | — |
| Chyba | Název prázdný: pod polem „Pojmenuj sadu, ať ji najdeš.“ Název už existuje: „Sada s tímhle názvem už je. Můžeš ji vybrat výše.“ (varování; duplicitní názvy nejsou zakázané, R4). |
| Offline | Beze změny. |
| Dlouhý obsah | Dlouhý seznam sad rolue; dlouhý název se zalomí. |
| Sync konflikt | — |

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
│  Čtení běží přímo v zařízení,  │
│  internet není potřeba.        │
└────────────────────────────────┘
```
- Po delší době (návrh: 10 s) přibude řádek: „Trvá to déle než obvykle. Čtu dál, můžeš počkat nebo zrušit.“
- „Zrušit“ zastaví čtení a vrátí do S30; nic se neuloží.

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

#### S35 Kontrolní obrazovka — US-03 (vstup z US-02 fotkou, z US-19 CSV)

**Účel:** jediné místo, kde se text z fotky mění. OCR se bude mýlit (R2), každý řádek jde opravit.

**Struktura:**
1. **Souhrn nahoře:** kolik dvojic aplikace přečetla a kolik chce kontrolu.
2. **Bannery** (jen když platí): prohozené sloupce, rukopis.
3. **K ověření:** rozbalené řádky (výřez z fotky, odznak důvodu, dvě pole, akce).
4. **Ostatní:** kompaktní řádky (dvě pole, žádný výřez). Ťuknutí do pole = úprava přímo v řádku (US-03: „jedno ťuknutí do pole“).
5. **Přidat slovo** na konci seznamu.
6. **Lišta dole** s primární akcí.

**iPhone:**
```
┌────────────────────────────────┐
│ Zrušit        Zkontroluj slova │
│ Deutsch 1 · němčina            │
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
│ │ Cizí   [ der Lehrer      ] │ │   člen: (— der die das)
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
- Člen u německých sad: u každého řádku malý přepínač `— / der / die / das` (US-03). Odznak „Chybí člen“ se ukáže jen u slov začínajících velkým písmenem bez členu; je to **heuristika** (09 neurčuje druh slova; viz kap. 5), ne chyba. Prázdný člen je platná volba (sloveso, přídavné jméno).
- Více českých významů se zapisuje do jednoho pole odděleně středníkem; pod polem při zaměření: „Víc významů odděl středníkem.“ (Q4).
- Pole mají vypnutou autokorekci a nad klávesnicí lištu `ä ö ü ß` a české diakritiky (09 kap. 2.1, krok 5).
- Akce řádku „⋯“ je i jako přístupnostní akce VoiceOveru; stejné akce jsou přes dlouhé podržení. Smazání swipem je navíc, nesmí být jediná cesta.
  - **Smazat:** řádek zmizí do „Vyřazené“ (dole, sbalené), jde vrátit ťuknutím „Vrátit“ (US-03: nic se nesmaže bez možnosti zpět).
  - **Spojit s předchozím:** spojí texty obou polí s řádkem nad ním.
  - **Rozdělit:** nejdřív ťukni do textu, kde se má rozdělit; zbytek textu se přesune do nového řádku pod ním. Interakci by měl ověřit uživatelský test, jde o nejméně jasnou akci návrhu.
- **Značky řádků a mikrotexty (podle 09 kap. 2.2–2.4):**
  | Značka | Text |
  |---|---|
  | Nízká jistota | „Ověř. Nejsem si jistý, jak jsem to přečetl.“ |
  | Neznámé slovo / diakritika | „Ověř diakritiku.“ |
  | Chybí překlad | „Chybí překlad.“ |
  | Chybí cizí slovo | „Chybí cizí slovo.“ |
  | Není slovíčko (nadpis, číslo strany) | „Není to slovíčko?“ Výchozí stav „vyřazeno“: „Vyřazeno. Ťukni, pokud to slovíčko je.“ |
  | Člen | „Chybí člen.“ |
  | Duplicita, stejný překlad | „Tohle slovo už v sadě máš. Přeskočím ho.“ (volba: Přeskočit / Přidat znovu) |
  | Duplicita, jiný překlad | „Slovo už v sadě máš. Přidám ten nový překlad k němu.“ (volba: Přidat k existujícímu / Nové slovo / Přeskočit) |
  | Duplicita v jiné sadě | „Slovo už máš v sadě {název}. Přidám ho i sem.“ |
  | CSV chybný řádek | „Řádek {n}: chybí překlad.“ / „Řádek {n}: neznámý jazyk.“ (viz S37) |
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
│ Přečetl jsem 23 dvojic, 4 … │  ┌──────────────────────────────┐  │
│ K ověření (4)                │  │  zvýrazněný výřez řádku       │  │
│  [!] der Lehrer · ucitel  ◄  │  │  na stránce                  │  │
│  [!] …                       │  └──────────────────────────────┘  │
│ Ostatní (19)                 │  Zobrazit celou fotku              │
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
| Částečná data | Výřez chybí: řádek bez výřezu, bez hlášky. Jen cizí nebo jen české pole: značka „Chybí překlad“ / „Chybí cizí slovo“ a nabídka „Připojit k předchozímu / následujícímu“ v ⋯ (09 kap. 2.3). |
| Chyba | Uložení selhalo: „Nepodařilo se uložit. Kontrola zůstává otevřená, nic se neztratilo. Zkus to prosím znovu.“ [ Zkusit znovu ]. |
| Offline | Beze změny (OCR i ukládání lokálně). |
| Dlouhý obsah | Stovky řádků: seznam se načítá líně, nahoře odkaz „K ověření (n)“ vždy přeskočí na označené. Dlouhý text v poli se zalomí; kompaktní řádek při Dynamic Type přejde na dva řádky pod sebou. |
| Sync konflikt | Duplicity proti knihovně z druhého zařízení: normální značka „Duplicita“. Sada mezitím smazána na druhém zařízení: při uložení „Sada {název} už neexistuje. Uložit do nové sady?“ [ Vytvořit sadu ] [ Zrušit ]. |
| Přerušení (aplikace ukončena během kontroly) | **Mezera G6:** bez konceptu kontroly se rozpracovaná kontrola ztratí. Návrh: banner na S20 „Máš rozdělanou kontrolu slov. [ Pokračovat ] [ Zahodit ]“. Vyžaduje rozhodnutí o ukládání fotky (Q8) a o lokálním (nesyncovaném) konceptu; neřeším dál. |

---

#### S36 Uloženo — US-02, US-03

```
┌────────────────────────────────┐
│ Hotovo                         │
│ Uloženo 23 slov do sady        │
│ Deutsch 1.                     │
│ (2 nové překlady přidané ke    │   jen když platí
│  stávajícím slovům, 1 přeskočeno)│
│ [      Procvičit teď        ]  │   → S10 / test
│ [          Hotovo           ]  │
└────────────────────────────────┘
```
Bez „Vrátit import“: 09 ho navrhuje, ale žádná story ho nemá (G8). Opravy po uložení jsou přes S23 (úprava a mazání slova, US-04).
„Procvičit teď“ je odkaz do US-05, ne samostatná funkce; nová slova se zařadí podle plánovače.

| Stav | Chování |
|---|---|
| Ostatní | Prázdno, načítání, chyba, offline, dlouhý obsah, sync konflikt: — (krátká obrazovka po dokončení). Uložení selže: patří do S35. iPad: formát (centrovaný sheet 540 pt). |

---

#### S37 CSV: vstupní kontrola souboru — US-19

Po výběru souboru systémovým výběrem a po S31 (sada, jazyk, pokud ve sloupcích nejsou):
- Úspěch: rovnou S35 se značkami podle 09 kap. 4.
- Obecné chyby souboru (obrazovka s textem a jednou akcí):
  | Případ | Text |
  |---|---|
  | Chybí hlavička/povinný sloupec | „V souboru chybí sloupec {term / translations}. Přidej ho do první řádky a zkus to znovu.“ |
  | Poškozená diakritika (kódování) | „Diakritika v souboru vypadá poškozená. Ulož ho prosím jako CSV v UTF-8 a zkus to znovu.“ |
  | Prázdný soubor | „V souboru nejsou žádná slovíčka.“ |
  | Příliš velký (návrh 2 000 řádků) | „Soubor má víc než 2 000 řádků. Rozděl ho prosím na menší.“ |
  | Neznámé sloupce | Banner v S35: „Sloupce {…} jsem nepoužil.“ |
- Formát CSV je otevřená otázka Q11; texty sloupců se přizpůsobí, až se rozhodne.

| Stav | Chování |
|---|---|
| Načítání | „Čtu soubor…“ (rychlé, bez procent). |
| Offline | Beze změny. |
| Ostatní | Prázdno, dlouhý obsah, sync konflikt: viz S35; samotná obrazovka je jen zpráva. |

---

### 4.4 Sady a slova

#### S20 Seznam sad — US-04 (banner konfliktu: US-12)

**iPhone:**
```
┌────────────────────────────────┐
│ Sady                       [+] │
│ ┌────────────────────────────┐ │
│ │ [!] Slovo „Tisch“ jsi smazal│ │   banner jen při konfliktu (S63 logika)
│ │ na iPhonu, na iPadu jsi ho  │ │
│ │ ale upravil.                │ │
│ │ [ Nechat ]  [ Smazat ]      │ │
│ └────────────────────────────┘ │
│ Deutsch 1          DE  · 34 slov │
│ v procvičování                 │
│ Project 3          EN  · 21 slov │
│ Unit 4             EN  ·  9 slov │
├────────────────────────────────┤
│ tab bar                        │
└────────────────────────────────┘
```
- Řádek sady: název, štítek jazyka (EN/DE, text, ne jen vlajka), počet slov, malý štítek „v procvičování“ podle výběru v S11.
- Ťuknutí → S21. Smazání sady: swipe nebo podržení + přístupnostní akce → potvrzení S24. „+“ → S30 (nová sada vzniká až v S31).

**iPad na výšku:** dva sloupce: seznam sad (320 pt) | detail sady (S21).
**iPad na šířku:** sidebar | seznam sad (320 pt) | detail sady (S21).
```
┌────────┬───────────────┬──────────────────────────────────┐
│Procvič.│ Sady      [+] │ Deutsch 1 · DE        [Upravit]  │
│ Sady ● │ Deutsch 1  DE │ 34 slov                          │
│Slovesa │ Project 3  EN │ [ + Přidat slova ]               │
│Pokrok  │ Unit 4     EN │ das Kind          dítě     [♪]   │
│Nastav. │               │ der Tisch         stůl     [♪]   │
└────────┴───────────────┴──────────────────────────────────┘
```
Bez vybrané sady: pravý panel je prázdný se zprávou „Vyber sadu vlevo.“

| Stav | Chování a text |
|---|---|
| První spuštění / prázdno | „Zatím tu nejsou žádné sady.“ „Vyfoť stránku ze sešitu a sada se vytvoří.“ [ Vyfotit slovíčka ] [ Vybrat z fotek ] [ Napsat ručně ] |
| Načítání | Skeleton řádků (lokální data, rychlé). |
| Chyba | „Sady se nepodařilo načíst. Zkus aplikaci zavřít a znovu otevřít.“ [ Zkusit znovu ] |
| Offline | Beze změny. |
| Dlouhý obsah | Dlouhý název se zalomí na 2 řádky, počet slov se přesune pod název; sady se řadí abecedně (návrh), s hlavičkami podle jazyka až při > 8 sadách. |
| Sync konflikt | Banner nahoře (viz výše): objeví se jen při rozporu smazané vs. upravené slovo; po volbě zmizí. Duplicity: „Máš zdvojená slova. [ Prohlédnout ]“ → S62. |

---

#### S21 Detail sady (seznam slov) — US-04, US-18

**iPhone:**
```
┌────────────────────────────────┐
│ ‹ Sady       Deutsch 1  [Upravit]│
│ DE · 34 slov                   │
│ [ + Přidat slova ]             │   → S30
│ das Kind           dítě   [♪]  │
│ der Tisch          stůl   [♪]  │
│ lernen             učit se[♪]  │
│ …                              │
└────────────────────────────────┘
```
- Ťuknutí na slovo → S23. Ťuknutí na [ ♪ ] přehraje (C1), nezavádí navigaci.
- „Upravit“ → S22 (přejmenování, smazání sady).
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

#### S22 Úprava sady (sheet) — US-04

Pole „Název“ (editovatelný), štítek jazyka (jen zobrazený, nemění se), řádek „Smazat sadu“ (červeně, jediné místo červené mimo potvrzení). „Hotovo“ v záhlaví.

| Stav | Chování |
|---|---|
| Chyba | Prázdný název: „Pojmenuj sadu, ať ji najdeš.“ |
| Ostatní | Prázdno, načítání, offline, dlouhý obsah, sync konflikt: — (jednoduchý formulář, lokální). iPad: popover/sheet 540 pt. |

---

#### S23 Slovo (karta a úprava) — US-04, US-18, US-03

**Účel:** zobrazit, opravit, přidat a smazat jedno slovo. Stejná obrazovka slouží k ručnímu přidání (z S30 „Napsat ručně“).

**iPhone:**
```
┌────────────────────────────────┐
│ Zrušit        Slovo      Uložit│
│ Cizí slovo                     │
│ [ Tisch                      ] │
│ Člen (jen němčina)             │
│ ( — )( der )( die )( das )     │
│ Česky                          │
│ [ stůl; stolek               ] │
│ Víc významů odděl středníkem.  │
│ [ ♪ Přehrát výslovnost ]       │
│ Sada: Deutsch 1                │
│ [ Smazat slovo ]               │   červeně → S24
└────────────────────────────────┘
```
- „Uložit“ je dostupné, když je vyplněné cizí slovo a aspoň jeden český význam.
- Varování, ne blokace: německé slovo bez členu, začínající velkým písmenem: „U německého podstatného jména chybí člen. Můžeš ho doplnit teď, nebo později.“
- Při přidávání nového slova: „Uložit“ a „Uložit a přidat další“.
- Vypnutá autokorekce, lišta znaků (kap. 3.4).
- Pole množné číslo, příklad, výslovnostní přepis, poznámka z 09 **nejsou navržena** (G10: bez story).

**iPad:** na obou orientacích sheet 540 pt na výšku obrazovky (nebo v pravém panelu S21 v režimu úpravy na šířku).

| Stav | Chování a text |
|---|---|
| Prázdno | Nové slovo: prázdná pole, „Uložit“ nedostupné. |
| Načítání | — |
| Chyba | Duplicita: „Slovo Tisch už v téhle sadě máš. [ Otevřít existující ]“ (varování, uložit lze). |
| Offline | Beze změny; [ ♪ ] viz C1. |
| Dlouhý obsah | Dlouhé pole se zalomí; více významů nad 5: scroll v poli, žádný limit nevymýšlím. |
| Sync konflikt | Slovo bylo mezitím změněno jinde: při uložení se uloží pozdější změna; viz S63 (US-12, Q7). |
| Smazání | S24. |

---

#### S24 Potvrzení smazání (alert) — US-04

Slovo: „Smazat slovo Tisch? Smaže se i historie jeho procvičování. To nejde vrátit.“ [ Smazat ] [ Nechat ]
Sada: „Smazat sadu Deutsch 1? Smaže se 34 slov a jejich historie procvičování. To nejde vrátit.“ [ Smazat sadu ] [ Nechat ]
(US-04: ukáže, co se smaže, včetně počtu slov a historie.) „Nechat“ je výchozí a nejvýraznější.

| Stav | Chování |
|---|---|
| Ostatní | Prázdno, načítání, chyba, offline, dlouhý obsah, sync konflikt: — (alert); smazání probíhá lokálně a synchronizuje se později (viz S63 pro „oživení“). |

---

### 4.5 Nepravidelná slovesa

Samostatný vstup (D8, US-17). Zadávají se ručně (US-16). Datový model slovesa je v `09-obsah-a-import.md` kap. 5.

#### S40 Seznam sloves — US-16, US-17

**iPhone:**
```
┌────────────────────────────────┐
│ Slovesa                    [+] │
│ (Angličtina) (Němčina)         │   přepínač jazyka
│ [      Procvičit slovesa     ] │   primární (US-17)
│ go – went – gone       [♪]     │
│ jít, jet                       │
│ be – was/were – been           │
│ …                              │
└────────────────────────────────┘
```
Německá varianta: `fahren – fährt – fuhr – ist gefahren` s českým významem pod ním.

**iPad na výšku:** seznam | detail (úprava, S41). **iPad na šířku:** sidebar | seznam | detail.

| Stav | Chování a text |
|---|---|
| První spuštění / prázdno | „Zatím tu nejsou žádná nepravidelná slovesa.“ „Zadej je z učebnice a pak je tu můžeš procvičovat.“ [ Přidat sloveso ] |
| Načítání | Skeleton. |
| Chyba | „Slovesa se nepodařilo načíst.“ [ Zkusit znovu ] |
| Offline | Beze změny. |
| Dlouhý obsah | Řádek se zalomí; českých významů zobrazit max. 2, zbytek „+ další“. |
| Sync konflikt | Jako u slov (S20, S62, S63). |
| Málo sloves | „Procvičit slovesa“ nedostupné bez aspoň 1 slovesa. Zda minimální počet pro kolo zadá plánovač, jinak návrh neimprovizuje. |

---

#### S41 Přidat / upravit sloveso — US-16

**Cíl:** syn píše z učebnice, nejčastěji jednou řádkou. Podle 09 kap. 5.2: první cesta je **rychlé zadání celé řádky**, druhá pole po tvarech.

**iPhone:**
```
┌────────────────────────────────┐
│ Zrušit    Nové sloveso    Uložit│
│ (Angličtina) (Němčina)         │
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
- **Validace je jen varování, neblokuje uložení (US-16):**
  | Případ | Text |
  |---|---|
  | Prázdný tvar | „Chybí příčestí. Můžeš uložit i tak, tenhle tvar se pak zkoušet nebude.“ |
  | Němčina bez pomocného slovesa | „Chybí pomocné sloveso (haben, sein).“ |
  | Infinitiv už existuje | „Sloveso fahren už máš. [ Upravit existující ]“ |
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

#### S42 Procvičování sloves — otázka — US-17, US-18

**Účel:** dotaz na chybějící tvary. Zadaná část je infinitiv + český význam; hledané tvary se píšou (US-17: „zadaná část, hledané tvary psaním“). Layout je obecný (daný tvar jako štítek, ostatní jako pole), takže zadání jiného tvaru než infinitivu by šlo, ale **mezera G12:** story říká jen „zadaná část“ a nerozhoduje, který tvar je daný.

**iPhone (němčina), klávesnice nahoře:**
```
┌────────────────────────────────┐
│ Skončit            ▓▓░░░░░░ 3/8│
│ NEPRAVIDELNÁ SLOVESA · NĚMČINA │
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
- Angličtina: pole „Minulý čas“ a „Příčestí“ vedle sebe.
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

#### S43 Vyhodnocení sloves — US-17 (pravidla překlepů a diakritiky jako US-07)

Hodnocení po tvarech: syn vidí, který tvar seděl a který ne.

```
┌────────────────────────────────┐
│ fahren – jet, jezdit      [♪]  │
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

#### S50 Vývoj — US-14

**Účel:** syn vidí, jestli se zlepšuje. Tón bez odsuzování; mezera v čase není selhání.

**iPhone:**
```
┌────────────────────────────────┐
│ Pokrok                         │
│ [ Vývoj ][ Slabá místa ]       │   přepínač S50 / S51
│ (Slovíčka)(Slovesa)            │
│ (Vše)(Angličtina)(Němčina) Sada▾│
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

- Graf: týdny na ose X, metrika přepínačem („Naučená slova“, „Úspěšnost“). Týdny bez procvičování se nekreslí jako nula: je tam slabá tečka a popisek VoiceOveru „Tento týden bez procvičování“ (US-14).
- Graf má textovou alternativu (tabulka „Týden / hodnota“), zapnutelnou odkazem „Zobrazit jako seznam“ a čtenou VoiceOverem.
- „Naučené slovo“ musí mít v aplikaci jasnou definici (Q1). Do rozhodnutí se v UI používá štítek „Naučená slova“, ale **definice musí být vidět na ťuknutí na (i)**: „Slovo, které sis správně vybavil psaním i po delší pauze.“ Tohle znění je **příklad ze story**, finální definici dá learning-scientist.
- Slovesa: samostatný přepínač „Slovesa“ (US-17: odděleně).
- Řádek v „Poslední procvičování“ ukazuje datum a „8 z 12“ (kolik hned) bez barevného hodnocení.

**iPad na výšku:** filtry nahoře vodorovně v jednom řádku; trend karta a graf pod sebou; seznam pokusů pod grafem; max. šířka obsahu 720 pt.
**iPad na šířku:**
```
┌────────┬───────────────────────────────────────────────────┐
│Sidebar │ Pokrok  [Vývoj][Slabá místa]  (filtry)             │
│        │ ┌──────────────────┐ ┌──────────────────────────┐ │
│        │ │ Zlepšuje se      │ │ Poslední procvičování     │ │
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
| Načítání | Skeleton grafu (lokální výpočet; u velké historie krátký). |
| Chyba | „Pokrok se nepodařilo spočítat. Zkus to prosím znovu.“ [ Zkusit znovu ] |
| Offline | Beze změny. |
| Dlouhý obsah | Historie za roky: graf ukazuje posledních 8 týdnů (iPad 12), starší vodorovným posunem. Seznam pokusů se načítá líně. |
| Sync konflikt | Pokusy ze dvou zařízení se po syncu sloučí do jedné historie; trend se přepočítá z historie (US-12). Nic uživatele nevyrušuje. |
| Částečná data | Sada s menším počtem pokusů: její řádek „Zatím málo dat“; celkový graf zůstává. |
| Mezera v čase | Prázdniny: týdny jsou tečky bez textu o „výpadku“. |

---

#### S51 Slabá místa — US-15

**Účel:** ukázat cestu dál, ne počet chyb.

**iPhone:**
```
┌────────────────────────────────┐
│ Pokrok                         │
│ [ Vývoj ][ Slabá místa ]       │
│ Na co se zaměřit               │
│ [  Procvičit slabá slova  ]    │   primární, jedno ťuknutí (US-15)
│                                │
│ Co se opakuje                  │
│ ┌────────────────────────────┐ │
│ │ Členy der, die, das        │ │   karta vzorce
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
- „Procvičit slabá slova“ spustí kolo jen ze slabých slov (US-15) → S12/S13. Konkrétní práh slabého slova je Q1.
- Seznam je krátký (max. 10, US-15). Řádek: slovo, překlad, štítek důvodu (člen, diakritika, směr). Počet chyb se nezobrazuje.
- Karty vzorců (US-15: členy, diakritika, směr). Každá se zobrazí jen, když je pro ni dost dat. Ťuknutí na kartu zúží seznam na její slova (jen filtr seznamu, **žádné další tlačítko**; kolo jen ze slabých slov je jedno).
- Znění karet:
  - Členy: „Členy der, die, das. U některých německých slov se členy pletou.“
  - Diakritika: „Diakritika. Občas chybí háček, čárka nebo přehláska.“
  - Směr: „Z češtiny do němčiny je to zatím těžší než naopak. U všech to tak bývá.“ (směr podle skutečných dat; text se obrátí podle toho, který směr je slabší, a jazyk se dosadí).
- Ťuknutí na slovo → S23. [ ♪ ] zde není (US-18 ho pro tuto obrazovku neuvádí); přehrát lze v S23.
- Slabá místa sloves: **mezera G14**, US-15 je nezmiňuje; v návrhu nejsou.

**iPad na výšku:** karty vzorců vedle sebe (2 sloupce), pod nimi seznam. **iPad na šířku:** vlevo karty vzorců a tlačítko, vpravo seznam slov.

| Stav | Chování a text |
|---|---|
| První spuštění / málo dat | „Zatím tu nic nezlobí.“ „Slabá místa se ukážou po pár procvičováních.“ Tlačítko „Procvičit slabá slova“ skryté. |
| Prázdno | Nikde nic slabého: „Žádné slovo teď nezlobí.“ + odkaz „Zpět na Vývoj“. |
| Načítání | Skeleton. |
| Chyba | „Slabá místa se nepodařilo spočítat.“ [ Zkusit znovu ] |
| Offline | Beze změny. |
| Dlouhý obsah | Top 10 (limit), víc se nezobrazuje. Dlouhý překlad se zalomí. |
| Sync konflikt | Seznam se po syncu přepočítá z historie; žádný zásah uživatele. |

---

### 4.7 Nastavení, sync, smazání

#### S60 Nastavení — US-11, US-12, US-20

```
┌────────────────────────────────┐
│ Nastavení                      │
│ iCloud a synchronizace      ›  │   → S61, vpravo stav slovem
│ Duplicity (3)               ›  │   jen když jsou; → S62
│ Přepsané změny (1)          ›  │   jen když jsou; → S63
│ ───────────────────────────    │
│ Smazat všechna data         ›  │   červeně; → S64
└────────────────────────────────┘
```
Žádný jiný obsah (hlas, verze, „O aplikaci“ nemají story; G15).
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
│ der Tisch · stůl        ×2     │
│ [ Sloučit ] [ Nechat obě ]     │
│ das Kind · dítě         ×2     │
│ [ Sloučit ] [ Nechat obě ]     │
└────────────────────────────────┘
```
- Nic se nesluční automaticky (US-12). Sloučení spojí české významy a historii procvičování obou záznamů (**požadavek na architekta**, kap. 5).
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
│ der Tisch                      │
│ Platí:      stůl; stolek       │
│ Přepsáno:   stůl               │
│ [ Vrátit přepsané ] [ Nechat ] │
└────────────────────────────────┘
```
- Zobrazuje se jen u slov, u nichž k přepsání došlo. Starší hodnota je dostupná jen, pokud ji model uchová (**požadavek na zdroj dat**, kap. 5: CloudKit tak sám neuchovává, domněnka). Pokud ji architekt nezaručí, obrazovka ztrácí smysl a story US-12 (Q7) se musí upravit; nevymýšlím ji.
- Název zařízení („na iPadu“) nepoužívám, dokud není zdroj (kap. 5); píšu „na druhém zařízení“.
- „Slovo bylo smazáno a jinde upraveno“ (oživení) řeší banner v S20, stejná logika.

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

Dvoustupňové potvrzení (US-20). Vstup je z S60, červený řádek.

**Krok 1 (obrazovka):**
- Nadpis: „Smazat všechna data?“
- Text: „Smaže se všechno: {n} sad, {m} slov, {p} sloves a celá historie procvičování. Smaže se v tomhle zařízení i v iCloudu, takže zmizí i na ostatních zařízeních. Tohle nejde vrátit.“
- Poznámka: „Když aplikaci jen odinstaluješ, data v iCloudu zůstanou.“ (US-20: v nastavení je to vysvětleno; **domněnka, ověřit**.)
- Tlačítka: „Pokračovat“, „Zrušit“ (výchozí).
- Export před smazáním (Q12): nenavrhuji, dokud se nerozhodne; pokud ano, vloží se mezi krok 1 a 2.

**Krok 2 (alert):**
- „Opravdu smazat všechno? Po smazání se aplikace vrátí na začátek.“
- Tlačítka: „Smazat všechno“ (červené), „Nemazat“ (výchozí).

Po smazání se zobrazí S01.

| Stav | Chování a text |
|---|---|
| Prázdno | Nic k mazání: „Není co mazat. Aplikace je prázdná.“ „Pokračovat“ nedostupné. |
| Načítání | Při mazání spinner „Mažu…“; neblokuje bezpečné přerušení (nedělat modální blokaci bez „Zrušit“, dokud to jde). |
| Chyba | „Smazání se nepovedlo. Nic se nesmazalo úplně, zkus to prosím znovu.“ [ Zkusit znovu ] (přesné chování při částečném smazání určí architekt). |
| Offline | Smazání kopie v iCloudu potřebuje internet: „Bez internetu to teď nejde. Smazání se týká i iCloudu, proto se musíš připojit. Zkus to znovu, až budeš online.“ „Pokračovat“ nedostupné. |
| Dlouhý obsah | Text se zalomí. |
| Sync konflikt | Druhé zařízení ještě pracuje: „Na jiném zařízení se právě synchronizuje. Smazání může chvíli trvat.“ (domněnka, závisí na R4). |

---

### 4.8 Sdílená komponenta: tlačítko výslovnosti

#### C1 Tlačítko výslovnosti — US-18

**Kde je:** S12 a S13 (zadání cizího slova ve směru cizí → česky), S14 a S43 (u správné odpovědi), S16 (u slov k procvičení), S21 a S23 (karta a seznam), S42 (daný infinitiv). V S51 není (viz S51).
Výklad: story říká „na kartě slova, při ukázání správné odpovědi i v souhrnu“. Zadání cizího slova v testu je výklad slova „Given slovo“; vlastní reproduktor u české strany zadání nikdy není. Viz G7.

**Chování:**
- Ikona reproduktoru, velikost ≥ 44 × 44 pt, popisek VoiceOveru „Přehrát výslovnost: {slovo}“.
- Přehraje se jen na ťuknutí, nikdy automaticky (US-18).
- Mluví se cizí slovo; u německých podstatných jmen včetně členu (09 kap. 6): „das Kind“. Hlas německý (de-DE) u DE sad, britský (en-GB) u EN sad (D16).
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

## 5. Požadavky na zdroj dat

Návrh s těmito daty počítá, ale aplikace je zatím odnikud nemá nebo nejsou zaručená. Označuji je jako požadavky (na architekta, learning-scientist, prototypera), ne jako fakta.

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
| D-13 | Dostupnost hlasu a zjištění „chybí hlas“ | C1 | Architekt, R3 (domněnka) |
| D-14 | Druh slova (podstatné jméno, sloveso, …) | S35, S23 (heuristika „Chybí člen“) | 09 pole `kind` existuje; kdo ho vyplňuje (OCR heuristika, uživatel), není ve stories určeno. Návrh: jen heuristika podle velkého písmene a členu, uživatel typ nevybírá. |
| D-15 | Prázdné tvary sloves se při procvičování vynechají | S41, S42 | Plánovač (US-16, US-17) |
| D-16 | Nezodpovězená slova při ukončeném kole nepřijdou o termín | S15 | Plánovač (US-08) |
| D-17 | Počet stránek při výběru fotek (maximum) | S32 | Architekt |

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
| — | Gamifikace, odznaky, série, denní cíle | US-24, D13 | P2 a bez rozhodnutí. Série a denní cíle navíc odporují principům 1 a 3. |
| — | Rodičovský pohled, připomenutí | US-23, US-25 | P2, nenavrhováno. Architektura obrazovek je nevylučuje. |
| — | Hotové sady, kontrola výslovnosti hlasem | US-21, US-22 | P2. |

**Mezery ve stories a rozhodnutí, na kterých návrh visí:**

| # | Mezera | Dopad na obrazovky |
|---|---|---|
| G1 | US-08 nedefinuje, co aplikace nabídne, když není co opakovat | S10 „Na dnes je hotovo“, bez tlačítka „Procvičit navíc“ |
| G2 | Odkud brát chybné možnosti, když sada má méně než 4 slova | S12 |
| G4 | „Nevím“ nemá ve stories oporu; předpokládám výsledek „špatně“ | S12, S13, S14 |
| G5 | Do „Ještě jednou chybná slova“ patří „skoro“ i „zatím ne“? Navrženo: obojí | S16 |
| G5b | Má mít souhrn otevřený z historie také „Ještě jednou chybná slova“? | S16 |
| G6 | Koncept rozpracované kontroly po násilném ukončení | S35 |
| G7 | [ ♪ ] u zadání v testu: story neříká výslovně | S12, S13, C1 |
| G12 | Který tvar slovesa je zadaný | S42 |
| G13 | Pomocné sloveso: segment vs. jedno pole „Perfekt“; jak se tvary skládají do výsledku pro plánovač | S42, S43 |
| G14 | Slabá místa sloves nejsou ve stories | S51 |

---

## 7. Otevřené otázky pro zadavatele a ostatní agenty

1. **iPhone na šířku:** zamknout aplikaci na výšku, nebo navrhnout i šířku? Zadání říká „iPhone na výšku“; návrh šířku neřeší.
2. **Klávesnice v testu:** zůstává nahoře i při vyhodnocení, nebo se schová a otevře znovu? Navrhuji cíl „bez cukání“; vyžaduje zkoušku na zařízení.
3. **Počet možností (Q5)** a velikost kola (Q6): návrh používá 4 a „dnešní kolo“ bez konkrétního čísla.
4. **Definice „naučené slovo“ (Q1)** a pravidlo trendu: bez nich je S50 jen kostra.
5. **Ukládání fotek (Q8):** texty v S32 a S35 předpokládají, že se fotka po uložení smaže. Změna rozhodnutí mění texty.
6. **Koncept kontroly (G6):** chceme ho? Dotýká se soukromí (R5), protože by musel uchovat obrázek nebo OCR text lokálně.
7. **Který z tabů je v buildu 1:** návrh je úplný, výběr je rozhodnutí o releasu. Podle `04-story-mapa.md` build 1 neobsahuje Slovesa, Pokrok, S61–S63.
8. **Zadání jazyka rozhraní (Q15):** návrh je čistě česky.
9. **Ověřit na zařízení (ux-verifier):** výška klávesnice a rozpočet S13/S42 na 375 × 667; chování tab baru/sidebaru iPadu na iOS 26; autokorekce vypnutá v polích; interakce „Rozdělit řádek“ v S35.

---

## 8. Mapa story → obrazovky

| Story | Priorita | Obrazovky |
|---|---|---|
| US-01 První spuštění | P0 | S01, S10 (prázdný stav), S32 |
| US-02 Import fotkou | P0 | S30, S31, S32, S34, S35, S36 |
| US-03 Oprava OCR | P0 | S35 (a výřezy), S36, banner S35 |
| US-04 Správa sad a slov | P0 | S11, S20, S21, S22, S23, S24, S31 |
| US-05 Test: mix výběr/psaní | P0 | S10, S12, S13, S15 |
| US-06 Oba směry | P0 | S11 (omezení směru), štítek směru v S12/S13 |
| US-07 Vyhodnocení | P0 | S14 (výběr: S12, psaní: S13) |
| US-08 Plánování | P0 | bez obrazovky; projevy v S10, S12/S13 („Znovu“), S14 |
| US-09 Souhrn | P0 | S15, S16 |
| US-10 Uložení | P0 | bez obrazovky; chyba ukládání v S14 |
| US-11 iCloud sync | P0 | S01 (krok 3), S61 |
| US-12 Konflikty | P0 | S20 (banner), S62, S63 |
| US-13 Offline | P0 | průřezově, kap. 3.6, řádek „Offline“ každé obrazovky |
| US-14 Historie pokroku | P1 | S50 (a S16 z historie) |
| US-15 Slabá místa | P1 | S51 |
| US-16 Zadání sloves | P1 | S40, S41 |
| US-17 Procvičování sloves | P1 | S40, S42, S43, S16 (varianta) |
| US-18 Výslovnost | P1 | C1 (S12, S13, S14, S16, S21, S23, S42, S43) |
| US-19 CSV import | P1 | S30, S35, S37 |
| US-20 Smazání dat | P1 | S60, S64 |

Každá obrazovka v kap. 4 má alespoň jednu story. Obrazovky bez story jsou v kap. 6 označené jako scope creep a nenavrženy.

# 02 — User stories

Stav k 2026-11-07. Zdroj pravdy: `00-zadani.md`. Priority P0/P1/P2 jsou převzaty ze zadání (návrh k odsouhlasení). Kde zadání prioritu neuvádí, je to označeno **(předpoklad)**.

## Závěr
25 stories: 13 × P0, 7 × P1, 5 × P2. P0 tvoří celou smyčku „fotka → kontrola → test → vyhodnocení → plán → souhrn" plus uložení, iCloud sync a offline. Stories pro rodičovský pohled (D11, US-23) a gamifikaci (D13, US-24) jsou P2. Každá story respektuje principy z `01-vize.md`: žádný tlak, neúspěch nedemotivuje, krátké relace, soukromí.

## Přehled

| ID | Story | Priorita | Rozhodnutí |
|---|---|---|---|
| US-01 | První spuštění | P0 (předpoklad) | D5, D10 |
| US-02 | Import slovíček fotkou | P0 | D7 |
| US-03 | Oprava výsledku OCR | P0 | D7 |
| US-04 | Správa sad a slov | P0 (předpoklad) | D7 |
| US-05 | Test: mix výběr/psaní | P0 | D3 |
| US-06 | Test v obou směrech | P0 | D2 |
| US-07 | Vyhodnocení odpovědi | P0 | D1, D3 |
| US-08 | Plánování opakování | P0 | zadání |
| US-09 | Souhrn po pokusu | P0 | D4 |
| US-10 | Uložení výsledků | P0 | zadání |
| US-11 | iCloud sync | P0 | D6 |
| US-12 | Konflikty při syncu | P0 (předpoklad) | D6, R4 |
| US-13 | Offline provoz | P0 (předpoklad) | R3, R5 |
| US-14 | Historie pokroku | P1 | D4 |
| US-15 | Slabá místa | P1 | D4 |
| US-16 | Zadání nepravidelných sloves | P1 | D8 |
| US-17 | Procvičování nepravidelných sloves | P1 | D8 |
| US-18 | Výslovnost: přehrání | P1 | D9 |
| US-19 | CSV import | P1 | D7 |
| US-20 | Smazání dat | P1 (předpoklad) | R5 |
| US-21 | Hotové sady | P2 | D7 |
| US-22 | Kontrola výslovnosti hlasem | P2 | D9 |
| US-23 | Rodičovský pohled | P2 | D11 |
| US-24 | Gamifikace | P2 | D13 |
| US-25 | Připomenutí | P2 | zadání |

---

## P0

### US-01 První spuštění — P0 (předpoklad)
Jako syn chci při prvním otevření aplikace hned vidět, co dělat dál, abych se nemusel nic učit ani nastavovat.
- Given čistá instalace, When otevřu aplikaci, Then vidím krátkou úvodní obrazovku (max. 3 kroky) a nabídku „Vyfotit slovíčka".
- Úvod nevyžaduje registraci, přihlášení ani souhlas s analytikou (žádná se nesbírá, R5).
- Pokud není dostupný iCloud, aplikace nabídne pokračování lokálně a srozumitelně vysvětlí, že se nebude synchronizovat (viz US-11).
- Přístup k fotoaparátu a fotkám se žádá až ve chvíli, kdy ho import potřebuje, s vysvětlením účelu.
- Žádný povinný výběr jazyka: jazyk (EN/DE) patří k sadě (US-04).

### US-02 Import slovíček fotkou — P0
Jako syn chci vyfotit stránku ze sešitu nebo učebnice, abych slovíčka nemusel opisovat.
- Given sadu nebo novou sadu, When vyfotím stránku nebo vyberu fotku z knihovny, Then aplikace rozpozná text v zařízení (bez odeslání obrázku ven, R5) a zobrazí návrh dvojic „cizí slovo — čeština".
- Podporované jazyky rozpoznání: čeština, angličtina, němčina včetně ä ö ü ß a české diakritiky.
- Podporován je i případ, kdy fotku vloží otec (výběr z knihovny fotek); viz otevřená otázka Q3.
- Dvousloupcová tabulka se rozloží na dvojice. Co se nepodaří spárovat, aplikace označí k ruční kontrole, ne zahodí.
- Obrázek se po zpracování neukládá, pokud syn nerozhodne jinak (předpoklad, Q8).
- Selže-li rozpoznání (rozmazáno, prázdná stránka), zobrazí se přátelská zpráva a možnost vyfotit znovu nebo zadat ručně.
- Kvalita OCR u ručně psaného textu je riziko R2, ne záruka.

### US-03 Oprava výsledku OCR — P0
Jako syn chci před uložením rychle opravit chyby rozpoznání, abych v sadě neměl špatná slovíčka.
- Given návrh dvojic z US-02, When ho prohlížím, Then každou dvojici mohu upravit, smazat, přidat nebo spojit/rozdělit řádky.
- Podezřelé položky (nízká jistota, nespárované, podezřelá diakritika) jsou vizuálně označeny a nabídnuty jako první.
- U německých podstatných jmen lze vyplnit nebo opravit člen der/die/das (US-07).
- Do sady se uloží jen to, co jsem potvrdil. Zrušení kontroly nic neuloží a nic nesmaže.
- Oprava slova je jedno ťuknutí do pole; netřeba otevírat další obrazovku.
- Cíl (M5): u tištěného textu ≤ 10 % slov vyžaduje opravu, na 20 slov do 5 minut.

### US-04 Správa sad a slov — P0 (předpoklad)
Jako syn chci mít slovíčka rozdělená do sad podle jazyka a lekce, abych mohl procvičovat to, co se zrovna učíme.
- Given uložená slovíčka, When otevřu seznam sad, Then vidím sady s jazykem (EN/DE), názvem a počtem slov.
- Mohu vytvořit, přejmenovat a smazat sadu a upravit, přidat nebo smazat jednotlivé slovo.
- Smazání sady nebo slova vyžaduje potvrzení a ukáže, co se smaže (včetně počtu slov a jejich historie).
- Slovo může mít víc českých významů; test uzná kterýkoli uvedený (předpoklad, Q4).
- Vyberu, které sady jdou do testu.

### US-05 Test: mix výběr/psaní — P0
Jako syn chci, aby nová slova byla výběrem z možností a známá psaním, abych se nezasekl na tom, co ještě neumím.
- Given slovo označené jako nové, When je v testu, Then vybírám z možností (správná + chybné z téže sady, počet možností viz Q5).
- Given slovo označené jako známé, When je v testu, Then odpověď píšu.
- Hranici „nové → známé" určuje plánovač z historie odpovědí (US-08); přesné pravidlo je otevřená otázka Q1.
- Žádné odpočítávání ani časový limit. Odpověď lze potvrdit kdykoli.
- Relace má omezenou délku (výchozí 10–15 slov, předpoklad Q6) a lze ji kdykoli ukončit; již dané odpovědi se uloží.
- Chyba není trestána: zobrazí se správný tvar neutrálním tónem a pokračuje se dál.

### US-06 Test v obou směrech — P0
Jako syn chci být zkoušen z cizího slova do češtiny i z češtiny do cizího, abych slovo opravdu uměl, ne jen poznal.
- Given slovo, When je v testu, Then směr je cizí → čeština nebo čeština → cizí.
- Směr se vybírá tak, aby se slovo ověřovalo v obou; vybavení v každém směru se eviduje zvlášť.
- Směr cizí → čeština je zpravidla snazší, takže pro nové slovo se začíná jím (předpoklad, Q1).
- Správná odpověď v jednom směru neposouvá plán druhého směru, dokud pro něj nejsou data.
- Syn může omezit test na jeden směr (předpoklad).

### US-07 Vyhodnocení odpovědi — P0
Jako syn chci, aby aplikace nebyla zbytečně přísná na překlepy, ale aby mi řekla, co přesně bylo špatně, abych se z chyb poučil.
- Given napsaná odpověď, When ji potvrdím, Then výsledek je jeden ze tří stavů: **správně**, **skoro správně**, **špatně**.
- **Překlep:** záměna, vynechání nebo přidání jednoho znaku u slova od 5 znaků = skoro správně; správný tvar se ukáže zvýrazněně (prahy Q2).
- **Diakritika:** chybějící nebo zaměněná diakritika (české znaky, německé ä ö ü, ß vs. ss) = skoro správně, s upozorněním, kde chybí. Přísnost lze změnit po rozhodnutí Q2.
- **Členy der/die/das:** německé podstatné jméno se zadává s členem. Správné slovo se špatným členem = skoro správně, evidováno zvlášť jako chyba rodu (vstup pro US-15). Člen chybí = vyzvat k doplnění, ne počítat jako chybu (předpoklad).
- Velká/malá písmena se u cizích slov nerozlišují, kromě německých podstatných jmen, kde je nepsání velkého písmene skoro správně (předpoklad, Q2).
- Víc českých významů: uznán kterýkoli uvedený (US-04).
- Odpověď výběrem: jen správně/špatně.
- Vyhodnocení je deterministické a běží lokálně.
- Pokaždé ukáže správnou odpověď a vzor chyby neutrálním jazykem bez bodových srážek.

### US-08 Plánování opakování — P0
Jako syn chci, aby se slova, která mi jdou, připomínala později a ta, která nejdou, častěji, abych se učil to, co potřebuji.
- Given výsledek odpovědi (správně / skoro správně / špatně, způsob odpovědi, směr), When se uloží, Then se slovu přepočítá termín dalšího opakování pro daný směr.
- Správně → interval se prodlouží. Špatně → slovo se vrátí brzy (v téže relaci nebo další den). Skoro správně → interval se neprodlouží, nezkrátí se zásadně.
- Správná odpověď psaním má větší váhu než výběrem (pravděpodobnost uhodnutí).
- Sestavení relace: nejdřív slova, jejichž termín nastal, doplněná novými; objem omezen délkou relace (US-05).
- Slova s dnešním termínem, která se do relace nevešla, se neztrácejí ani se nehromadí jako „dluh": zobrazuje se jen aktuální nabídka bez počítadla resty (princip bez tlaku).
- Konkrétní algoritmus a intervaly jsou otevřená otázka Q1; musí být vysvětlitelné a otestovatelné jednotkovými testy.
- Plán lze přepočítat z historie pokusů (nutné pro US-12).

### US-09 Souhrn po pokusu — P0
Jako syn chci po každém pokusu vidět krátký souhrn, abych věděl, jak mi to šlo, a nebyl z toho smutný.
- Given dokončená (nebo ukončená) relace, When skončí, Then vidím souhrn: počet slov, kolik se vybavilo hned, kolik s drobnou chybou, kolik ne, a které slovo se mělo „znovu".
- Souhrn začíná tím, co se povedlo (např. počet správných nebo posun oproti minulému pokusu). Při slabém výsledku nezobrazuje odsuzující text ani červené hodnocení.
- Seznam slov k procvičení: chybná slova se správnou odpovědí.
- Tlačítka: „Ještě jednou chybná slova" a „Hotovo". Žádný nátlak k další relaci.
- Souhrn je dostupný i později z historie (US-14).
- Souhrn se zobrazí i po předčasném ukončení, pro dosud zodpovězená slova.

### US-10 Uložení výsledků — P0
Jako syn chci, aby se mé výsledky uložily samy, abych o ně nikdy nepřišel.
- Given odpověď, When ji potvrdím, Then se uloží okamžitě (každá odpověď zvlášť, ne až na konci relace).
- Po násilném ukončení aplikace nebo vybití zařízení zůstanou uložené všechny odpovědi do poslední potvrzené.
- Ukládá se: slovo, směr, způsob odpovědi, výsledek, čas; z toho jsou odvozena data plánu (US-08) a historie (US-14).
- Model musí splňovat omezení SwiftData přes CloudKit (R4): žádné unikátní atributy, vlastnosti volitelné nebo s defaultem, relace volitelné.
- Data lze číst způsobem, který nevylučuje pozdější rodičovský pohled (D11).

### US-11 iCloud sync — P0
Jako syn chci mít stejné sady a výsledky na iPhonu i iPadu, abych mohl pokračovat na kterémkoli zařízení.
- Given dvě zařízení se stejným iCloud účtem, When změním sadu nebo uložím výsledek na jednom, Then se po připojení objeví na druhém (cíl M6: do 5 min, domněnka).
- Sync běží na pozadí, bez ručního spuštění. Stav (synchronizováno / čeká / chyba) je k nalezení v nastavení, ne vnucený.
- Nepřihlášený iCloud nebo vypnutý iCloud Drive/CloudKit: aplikace funguje lokálně a jednou srozumitelně řekne proč; po zapnutí se data synchronizují.
- Do iCloudu nejdou fotky, jen strukturovaná data (sady, slova, pokusy).
- Platnost závisí na R1 (účet dítěte, TestFlight vs. iCloud) a R4; neověřeno.

### US-12 Konflikty při syncu — P0 (předpoklad)
Jako syn chci, aby se při procvičování na dvou zařízeních nic neztratilo ani nezdvojilo, abych si nemusel dávat pozor, kde zrovna učím.
- Given stejná slova procvičená na obou zařízeních offline, When se zařízení znovu spojí, Then zůstanou všechny pokusy z obou zařízení.
- Plán opakování se po sloučení přepočítá z historie pokusů, nikoli „poslední zápis vyhrává".
- Úprava téhož slova na obou zařízeních: vyhraje pozdější změna, v nastavení lze podívat, co bylo přepsáno (předpoklad, Q7).
- Smazání na jednom zařízení nesmí „oživit" slovo z druhého zařízení bez upozornění.
- Duplicitní slova po sloučení se nabídnou ke sloučení, nikoli automaticky smažou (R4: žádné unikátní atributy, takže duplicity jsou možné).

### US-13 Offline provoz — P0 (předpoklad)
Jako syn chci moci procvičovat bez internetu (v autobuse, o prázdninách), abych se mohl učit kdekoli.
- Given zařízení bez připojení, When otevřu aplikaci, Then funguje import fotkou (OCR v zařízení), správa sad, test, vyhodnocení, plán i souhrn.
- Změny se při offline uloží lokálně a po připojení se synchronizují (US-11, US-12).
- Přehrání výslovnosti (US-18) funguje offline, pokud je hlas stažen; jinak aplikace srozumitelně řekne, co chybí (R3, domněnka).
- Žádná funkce bez dobrého důvodu nevyžaduje síť; aplikace nehlásí „chyba sítě" tam, kde síť nepotřebuje.

---

## P1

### US-14 Historie pokroku — P1
Jako syn chci vidět, jestli se zlepšuji, abych měl radost z pokroku.
- Given dostatek pokusů, When otevřu historii, Then vidím vývoj v čase (týdně) po sadě a jazyce: počet naučených slov a úspěšnost.
- „Naučené slovo" má jasnou definici (např. vybavené psaním po delším intervalu; přesně Q1).
- Zobrazení vždy říká i „zlepšuje se / drží se / zatím se nezlepšuje", ale tónem bez odsuzování; při nedostatku dat řekne „zatím málo dat" místo grafu s nulou.
- Mezery v čase (prázdniny) nejsou zobrazeny jako selhání.
- Lze otevřít souhrn jednotlivého pokusu (US-09).

### US-15 Slabá místa — P1
Jako syn chci vědět, kde mi to nejde, abych se na to mohl zaměřit.
- Given historie pokusů, When otevřu slabá místa, Then vidím nejčastěji chybná slova, a také vzorce: chyby v členech der/die/das, diakritice, konkrétním směru.
- Z přehledu lze jedním ťuknutím spustit relaci jen na slabá slova.
- Seznam je krátký (např. top 10), zdůrazňuje cestu dál, ne počet chyb.
- Přesný práh „slabého" slova je Q1.

### US-16 Zadání nepravidelných sloves — P1
Jako syn chci zadat nepravidelná slovesa ručně, abych je mohl procvičovat podle učebnice.
- Given sada sloves, When přidám sloveso, Then zadám tvary (EN: infinitiv, minulý čas, příčestí; DE: infinitiv, 3. os. préteritum, perfektum s pomocným slovesem; předpoklad, Q9) a český význam.
- Slovesa se zadávají ručně (D8), ne fotkou.
- Validace: chybějící tvar upozorní, neblokuje uložení.
- Slovesa se synchronizují jako ostatní data (US-11).

### US-17 Procvičování nepravidelných sloves — P1
Jako syn chci nepravidelná slovesa procvičovat zvlášť, protože se učí jinak než běžná slovíčka.
- Given sada sloves, When spustím procvičování, Then jsem dotázán na chybějící tvary (zadaná část, hledané tvary psaním).
- Hodnocení tvarů je po tvarech: vím, který z tvarů byl špatně; pravidla překlepů a diakritiky jako v US-07.
- Plán opakování sloves používá stejný princip jako US-08 (předpoklad).
- Souhrn a historie zahrnují slovesa odděleně od slovíček.
- Procvičování sloves je samostatný vstup v aplikaci (D8), ne součást běžného testu.

### US-18 Výslovnost: přehrání — P1
Jako syn chci slyšet, jak se slovo správně vyslovuje, abych si ho zapamatoval nejen očima.
- Given slovo nebo odpověď, When ťuknu na ikonu reproduktoru, Then se přehraje cizí slovo hlasem (de-DE, en-GB/en-US) pomocí systémové syntézy.
- Dostupné na kartě slova, při ukázání správné odpovědi i v souhrnu.
- Funguje offline, pokud je hlas v zařízení (R3). Pokud ne, srozumitelná hláška.
- Přehrání se spouští jen na ťuknutí, nikdy automaticky; respektuje ztlumení zařízení (předpoklad).
- Varianta angličtiny (britská/americká) je otevřená otázka Q10.
- Kontrola výslovnosti syna je mimo tuto story (US-22).

### US-19 CSV import — P1
Jako syn (nebo otec) chci načíst slovíčka z CSV souboru, abych využil seznam, který už někde existuje.
- Given soubor CSV (UTF-8, dva až tři sloupce: cizí, česky, volitelně člen/poznámka), When ho vyberu, Then uvidím náhled a stejnou kontrolu a opravu jako u OCR (US-03).
- Chybné řádky se označí, neuloží se slova bez překladu.
- Rozpoznání oddělovače (čárka/středník) a kódování; diakritika se nezkomolí.
- Import jde do vybrané sady; duplicity se nabídnou ke sloučení.
- Přesný formát je otevřená otázka Q11.

### US-20 Smazání dat — P1 (předpoklad)
Jako syn (nebo otec) chci smazat všechna svá data, abych měl jistotu, že po mně nic nezůstalo.
- Given nastavení, When zvolím „Smazat všechna data", Then ukáže se, co se smaže (sady, slova, historie, iCloud kopie), a po potvrzení se smaže lokálně i v iCloudu.
- Smazání je potvrzené dvoustupňově a nevratné; předtím je nabídnut export (předpoklad, Q12).
- Po smazání se aplikace vrátí do stavu prvního spuštění (US-01).
- Smazání jedné sady a smazání celého účtu jsou oddělené akce (US-04).
- Odinstalování aplikace nemusí smazat iCloud data; v nastavení je to vysvětleno (domněnka, ověřit).

---

## P2

### US-21 Hotové sady — P2
Jako syn chci k některým učebnicím využít hotové sady, abych nemusel nic fotit.
- Pouze pokud je licence v pořádku (D7); bez ověřené licence se story neprovádí.
- Given hotová sada, When ji přidám, Then se chová jako vlastní sada (US-04) a lze ji upravit.
- Zdroj, autor a licence jsou u sady uvedeny.

### US-22 Kontrola výslovnosti hlasem — P2
Jako syn chci říct slovo nahlas a dozvědět se, jestli ho vyslovuji správně, abych si výslovnost zlepšil.
- Given slovo, When ho řeknu do mikrofonu, Then aplikace posoudí shodu s očekávaným slovem a řekne to neutrálně a povzbudivě.
- Rozpoznání musí běžet v zařízení (R5); jinak se story neprovádí.
- Samostatná, pozdější funkce (D9); jde o otevřenou otázku, ne závazek.
- Chybné rozpoznání není vina syna; aplikace nesmí zhoršit pocit z neúspěchu (princip 1).

### US-23 Rodičovský pohled — P2 (D11)
Jako otec chci vidět, jak synovi učení postupuje, abych mu mohl pomoci, ale ne ho kontrolovat.
- Zatím se nerealizuje (D11). Povinný požadavek na architekturu: výsledky a historie jsou uložené tak, aby šly číst z jiného účtu/zařízení.
- Rodič vidí jen agregace (pravidelnost, úspěšnost, slabá místa), ne jednotlivé „selhání" v reálném čase (předpoklad).
- Přístup vyžaduje souhlas syna nebo sdílení v rámci Family Sharing (způsob je Q13).
- Žádná analytika ani server třetí strany (R5).

### US-24 Gamifikace — P2 (D13)
Jako syn chci za učení dostávat zábavné odměny, abych měl větší chuť se vracet.
- Přání, ne priorita (D13). Nesmí porušit principy 1–3: žádné ztráty, žádné trestající série, žádné odpočítávání.
- Odměny se vztahují k pokroku a vytrvalosti, ne k rychlosti; vypínatelné v nastavení.
- Konkrétní podoba (odznaky, úrovně, motiv) je otevřená otázka Q14; do rozhodnutí se neimplementuje.

### US-25 Připomenutí — P2
Jako syn chci být zdvořile upozorněn, když mám slova k opakování, abych nezapomněl procvičovat.
- Upozornění je lokální, volitelné, ve výchozím stavu vypnuté, s volbou času.
- Text je neutrální nabídka, ne výtka; žádné opakované upozornění při ignorování.
- Syn může upozornění kdykoli vypnout; žádný odpočet „série".

---

## Otevřené otázky (co zadání neurčuje)
- **Q1 Algoritmus plánování a definice „nové/známé/naučené/slabé slovo".** Jaké intervaly, kolik úspěchů je „známé", jak se váží výběr vs. psaní a směr. Doporučení: zvolit osvědčené schéma (např. krabicový/Leitnerův nebo SM-2 odvozený) a rozhodnout s Pavlem.
- **Q2 Přísnost vyhodnocení.** Prahy překlepu, zacházení s diakritikou v češtině i němčině, s velkými písmeny a s chybějícím členem.
- **Q3 Vložení fotek otcem.** Vloží je na zařízení syna, nebo ze svého? Pokud ze svého, jak se data dostanou do iCloudu syna (jiné Apple ID)? Souvisí s R1.
- **Q4 Více významů a synonym u jednoho slova.** Jak se zadávají a co se uznává.
- **Q5 Počet možností u výběru** a odkud se berou chybné možnosti.
- **Q6 Velikost relace** (výchozí počet slov, max. délka v minutách).
- **Q7 Pravidlo při konfliktu úprav** téhož slova (poslední zápis, nebo slučování).
- **Q8 Uložení původních fotek.** Ukládat, nebo po zpracování smazat?
- **Q9 Sada tvarů nepravidelných sloves** v EN a DE a jak se zadávají.
- **Q10 Varianta angličtiny** (britská/americká) pro výslovnost a pravopis.
- **Q11 Formát CSV** (sloupce, oddělovač, kódování, jak označit jazyk/člen).
- **Q12 Export dat před smazáním** — ano/ne.
- **Q13 Způsob sdílení pokroku s rodičem** (Family Sharing, CloudKit sdílení) a souhlas syna.
- **Q14 Podoba gamifikace.**
- **Q15 Jazyk rozhraní.** Předpokládána čeština, v zadání výslovně není.
- **R1–R5** viz `00-zadani.md`; tvrzení o platformě v těchto stories jsou **domněnka**, dokud nejsou ověřena.

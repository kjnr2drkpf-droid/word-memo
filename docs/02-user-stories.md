# 02 — User stories

Stav k 2026-11-08. Zdroj pravdy: `00-zadani.md`. Priority P0/P1/P2 jsou převzaty ze zadání (návrh k odsouhlasení). Kde zadání prioritu neuvádí, je to označeno **(předpoklad)**.

## Závěr
26 stories: 14 × P0, 7 × P1, 5 × P2. P0 tvoří celou smyčku „fotka → kontrola → test → vyhodnocení → plán → souhrn" plus uložení, iCloud sync, offline a přepnutí aktivního jazyka (US-26). Podle D18 se jazyky nemíchají: fronta, test, souhrn, historie a slabá místa jsou vždy po jednom (aktivním) jazyce; import má svůj jazyk, který syn vybere jako první krok (D22), a aktivní jazyk nemění. Stories pro rodičovský pohled (D11, US-23) a gamifikaci (D13, US-24) jsou P2. Každá story respektuje principy z `01-vize.md`: žádný tlak, neúspěch nedemotivuje, krátké relace, soukromí.

## Přehled

| ID | Story | Priorita | Rozhodnutí |
|---|---|---|---|
| US-01 | První spuštění | P0 (předpoklad) | D5, D10, D18 |
| US-02 | Import slovíček fotkou | P0 | D7, D18, D22 |
| US-03 | Oprava výsledku OCR | P0 | D7, D22 |
| US-04 | Správa sad a slov | P0 (předpoklad) | D7, D18, D22 |
| US-05 | Test: mix výběr/psaní | P0 | D3, D18 |
| US-06 | Test v obou směrech | P0 | D2 |
| US-07 | Vyhodnocení odpovědi | P0 | D1, D3, D18 |
| US-08 | Plánování opakování | P0 | zadání, D18 |
| US-09 | Souhrn po pokusu | P0 | D4, D18 |
| US-10 | Uložení výsledků | P0 | zadání |
| US-11 | iCloud sync | P0 | D6 |
| US-12 | Konflikty při syncu | P0 (předpoklad) | D6, R4 |
| US-13 | Offline provoz | P0 (předpoklad) | R3, R5 |
| US-14 | Historie pokroku | P1 | D4, D18 |
| US-15 | Slabá místa | P1 | D4, D18 |
| US-16 | Zadání nepravidelných sloves | P1 | D8, D18 |
| US-17 | Procvičování nepravidelných sloves | P1 | D8, D18 |
| US-18 | Výslovnost: přehrání | P1 | D9, D18 |
| US-19 | CSV import | P1 | D7, D18, D22 |
| US-20 | Smazání dat | P1 (předpoklad) | R5 |
| US-21 | Hotové sady | P2 | D7 |
| US-22 | Kontrola výslovnosti hlasem | P2 | D9, D18 |
| US-23 | Rodičovský pohled | P2 | D11 |
| US-24 | Gamifikace | P2 | D13 |
| US-25 | Připomenutí | P2 | zadání |
| US-26 | Přepnutí aktivního jazyka | P0 | D18, D22 |

---

## P0

### US-01 První spuštění — P0 (předpoklad)
Jako syn chci při prvním otevření aplikace hned vidět, co dělat dál, abych se nemusel nic učit ani nastavovat.
- Given čistá instalace, When otevřu aplikaci, Then vidím krátkou úvodní obrazovku (max. 3 kroky) a nabídku „Vyfotit slovíčka".
- Úvod nevyžaduje registraci, přihlášení ani souhlas s analytikou (žádná se nesbírá, R5).
- Pokud není dostupný iCloud, aplikace nabídne pokračování lokálně a srozumitelně vysvětlí, že se nebude synchronizovat (viz US-11).
- Přístup k fotoaparátu a fotkám se žádá až ve chvíli, kdy ho import potřebuje, s vysvětlením účelu.
- **Prvním krokem úvodu je volba jazyka (D18):** Angličtina, nebo Němčina, jedno ťuknutí, bez dalších otázek. Zvolený jazyk se stane aktivním (US-26) a nabídka „Vyfotit slovíčka" i všechny další obrazovky se týkají jen jeho. Volba jazyka se počítá do max. 3 kroků úvodu.
- Volba není nevratná: jazyk lze kdykoli přepnout (US-26) a jazyk sady určuje sada (US-04). Aplikace jazyk za syna nikdy nevybere.

### US-02 Import slovíček fotkou — P0
Jako syn chci vyfotit stránku ze sešitu nebo učebnice, abych slovíčka nemusel opisovat.
- Given sadu nebo novou sadu, When vyfotím stránku nebo vyberu fotku z knihovny, Then aplikace rozpozná text v zařízení (bez odeslání obrázku ven, R5) a zobrazí návrh dvojic „cizí slovo — čeština".
- Podporované jazyky rozpoznání: čeština, angličtina, němčina včetně ä ö ü ß a české diakritiky.
- **Jazyk se volí jako první krok importu (D18, D22):** Given zahájený import, When se otevře jeho první krok, Then syn vybere, pro který jazyk slovíčka přidává (Angličtina / Němčina). Volba je předvyplněná aktivním jazykem, ale jde zvolit i druhý jazyk. Aplikace jazyk za syna nevybere ani ho nezmění podle obsahu fotky.
- **Sady podle zvoleného jazyka:** nabízejí se sady zvoleného jazyka (ne nutně aktivního) a nová sada vznikne ve zvoleném jazyce; existující sada si jazyk drží (US-04). Zvolený jazyk je po celý import pevný a viditelný i na kontrolní obrazovce (např. „Importuji do: Němčina · název sady"). Dva jazyky v jednom importu nejdou.
- **Import nemění aktivní jazyk zařízení:** Given aktivní jazyk A a import do jazyka B, When import dokončím nebo zruším, Then aktivní jazyk zůstává A; všechny vzniklé sady a slova mají jazyk B. Po uložení lze nabídnout „Přepnout na … a procvičit" (přepnutí je volba syna, US-26).
- **OCR dostane jazykový kontext:** rozpoznání se nastaví na češtinu a zvolený jazyk (cs + en, nebo cs + de), ne na oba cizí jazyky zároveň. Že to zvyšuje přesnost, je **domněnka** k ověření sondou (R2).
- Text, který vypadá jako druhý jazyk (např. německá slova při importu do anglické sady), se potichu neuloží: označí se k ruční kontrole (US-03) a nabídne se zrušení. Jazyk se nikdy nepřepne automaticky (Q21).
- Podporován je i případ, kdy fotku vloží otec (výběr z knihovny fotek); viz otevřená otázka Q3.
- Dvousloupcová tabulka se rozloží na dvojice. Co se nepodaří spárovat, aplikace označí k ruční kontrole, ne zahodí.
- Obrázek se po zpracování neukládá, pokud syn nerozhodne jinak (předpoklad, Q8).
- Selže-li rozpoznání (rozmazáno, prázdná stránka), zobrazí se přátelská zpráva a možnost vyfotit znovu nebo zadat ručně.
- Kvalita OCR u ručně psaného textu je riziko R2, ne záruka.

### US-03 Oprava výsledku OCR — P0
Jako syn chci před uložením rychle opravit chyby rozpoznání, abych v sadě neměl špatná slovíčka.
- Given návrh dvojic z US-02, When ho prohlížím, Then každou dvojici mohu upravit, smazat, přidat nebo spojit/rozdělit řádky.
- Podezřelé položky (nízká jistota, nespárované, podezřelá diakritika) jsou vizuálně označeny a nabídnuty jako první.
- Mezi podezřelé patří i dvojice, která vypadá jako jiný jazyk než jazyk zvolený při importu (D18, D22). Ukládá se jen do sady tohoto jazyka; žádná dvojice se bez vědomí syna nepřesune do sady druhého jazyka.
- Kontrola běží ve zvoleném jazyce importu, i když není aktivní; zvolený jazyk je na kontrolní obrazovce vidět a nelze ho uprostřed kontroly změnit (změna = zrušit a začít import znovu). Uložení ani zrušení kontroly nemění aktivní jazyk zařízení (D22).
- U německých podstatných jmen lze vyplnit nebo opravit člen der/die/das (US-07); pole pro člen se zobrazuje jen v sadě němčiny (D18).
- Do sady se uloží jen to, co jsem potvrdil. Zrušení kontroly nic neuloží a nic nesmaže.
- Oprava slova je jedno ťuknutí do pole; netřeba otevírat další obrazovku.
- Cíl (M5): u tištěného textu ≤ 10 % slov vyžaduje opravu, na 20 slov do 5 minut.

### US-04 Správa sad a slov — P0 (předpoklad)
Jako syn chci mít slovíčka rozdělená do sad podle jazyka a lekce, abych mohl procvičovat to, co se zrovna učíme.
- Given uložená slovíčka, When otevřu seznam sad, Then vidím sady **aktivního jazyka** (D18) s názvem a počtem slov; jazyk je uveden v záhlaví seznamu. Sady druhého jazyka se objeví až po přepnutí (US-26), v jednom seznamu se jazyky nemíchají.
- Mohu vytvořit, přejmenovat a smazat sadu a upravit, přidat nebo smazat jednotlivé slovo.
- **Jazyk sady je povinný při vytvoření** (předvyplněn aktivním jazykem; při importu jazykem zvoleným na jeho začátku, D22). Sada nemůže obsahovat slova obou jazyků a slovo, sloveso i pokus dědí jazyk sady; zvlášť se u nich jazyk nezadává (D18).
- **Po vytvoření je jazyk sady neměnný** (návrh). Zda změnu vůbec umožnit, případně s varováním o dopadu na plán opakování a statistiky, je otevřená otázka Q16.
- Slovo nelze přesunout ani zkopírovat do sady druhého jazyka.
- Smazání sady nebo slova vyžaduje potvrzení a ukáže, co se smaže (včetně počtu slov a jejich historie).
- Slovo může mít víc českých významů; test uzná kterýkoli uvedený (předpoklad, Q4).
- Vyberu, které sady jdou do testu; nabízejí se jen sady aktivního jazyka (D18).

### US-05 Test: mix výběr/psaní — P0
Jako syn chci, aby nová slova byla výběrem z možností a známá psaním, abych se nezasekl na tom, co ještě neumím.
- Given slovo označené jako nové, When je v testu, Then vybírám z možností (správná + chybné z téže sady, počet možností viz Q5). Nestačí-li sada na chybné možnosti, doplní se jen ze sad téhož jazyka, nikdy z druhého (D18).
- Given slovo označené jako známé, When je v testu, Then odpověď píšu.
- Hranici „nové → známé" určuje plánovač z historie odpovědí (US-08); přesné pravidlo je otevřená otázka Q1.
- Žádné odpočítávání ani časový limit. Odpověď lze potvrdit kdykoli.
- Relace má omezenou délku (výchozí 10–15 slov, předpoklad Q6) a lze ji kdykoli ukončit; již dané odpovědi se uloží.
- Chyba není trestána: zobrazí se správný tvar neutrálním tónem a pokračuje se dál.
- Relace čerpá výhradně ze sad a slov **aktivního jazyka** (D18); v jedné relaci nikdy nejsou slova angličtiny i němčiny. Jazyk aktivní při zahájení platí do konce relace, přepnutí uprostřed testu se nenabízí (US-26).

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
- Pravidla se volí podle jazyka sady (D18): členy der/die/das, ä ö ü a ß platí jen pro němčinu; u angličtiny (britské, D16) se rod ani ä ö ü ß nevyhodnocují. České významy se vždy hodnotí podle české diakritiky. Odpověď se nikdy neporovnává se slovy druhého jazyka.
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
- **Plán a fronta jsou po jazycích (D18):** termíny opakování se vedou a řadí zvlášť pro angličtinu a němčinu. Sestavení relace bere jen slova aktivního jazyka; slova druhého jazyka s nastalým termínem do fronty nevstupují a čekají na přepnutí (US-26).
- Odpověď v jednom jazyce neovlivňuje plán, intervaly ani stav „nové/známé" slov v druhém jazyce.
- Počet slov k procvičení v druhém jazyce se smí zobrazit jen jako neutrální nabídka u přepínače jazyka (US-26); pravidlo bez počítadla resty platí pro oba jazyky.

### US-09 Souhrn po pokusu — P0
Jako syn chci po každém pokusu vidět krátký souhrn, abych věděl, jak mi to šlo, a nebyl z toho smutný.
- Given dokončená (nebo ukončená) relace, When skončí, Then vidím souhrn: počet slov, kolik se vybavilo hned, kolik s drobnou chybou, kolik ne, a které slovo se mělo „znovu".
- Souhrn začíná tím, co se povedlo (např. počet správných nebo posun oproti minulému pokusu). Při slabém výsledku nezobrazuje odsuzující text ani červené hodnocení.
- Seznam slov k procvičení: chybná slova se správnou odpovědí.
- Tlačítka: „Ještě jednou chybná slova" a „Hotovo". Žádný nátlak k další relaci.
- Souhrn je dostupný i později z historie (US-14).
- Souhrn se zobrazí i po předčasném ukončení, pro dosud zodpovězená slova.
- Souhrn se týká jedné relace, tedy jednoho jazyka (D18): jazyk je uveden v záhlaví, srovnání s minulým pokusem se dělá jen s pokusem téhož jazyka a „Ještě jednou chybná slova" nabízí jen slova z této relace. Souhrn nikdy neslučuje výsledky angličtiny a němčiny.

### US-10 Uložení výsledků — P0
Jako syn chci, aby se mé výsledky uložily samy, abych o ně nikdy nepřišel.
- Given odpověď, When ji potvrdím, Then se uloží okamžitě (každá odpověď zvlášť, ne až na konci relace).
- Po násilném ukončení aplikace nebo vybití zařízení zůstanou uložené všechny odpovědi do poslední potvrzené.
- Ukládá se: slovo, směr, způsob odpovědi, výsledek, čas; z toho jsou odvozena data plánu (US-08) a historie (US-14).
- Model musí splňovat omezení SwiftData přes CloudKit (R4): žádné unikátní atributy, vlastnosti volitelné nebo s defaultem, relace volitelné.
- Data lze číst způsobem, který nevylučuje pozdější rodičovský pohled (D11).
- Jazyk každého pokusu je jednoznačně dohledatelný (dědí se ze sady slova), aby šlo plán, historii a slabá místa počítat po jazycích bez slučování (D18). K ukládání posledního aktivního jazyka viz US-26 a Q18.

### US-11 iCloud sync — P0
Jako syn chci mít stejné sady a výsledky na iPhonu i iPadu, abych mohl pokračovat na kterémkoli zařízení.
- Given dvě zařízení se stejným iCloud účtem, When změním sadu nebo uložím výsledek na jednom, Then se po připojení objeví na druhém (cíl M6: do 5 min, domněnka).
- Sync běží na pozadí, bez ručního spuštění. Stav (synchronizováno / čeká / chyba) je k nalezení v nastavení, ne vnucený.
- Nepřihlášený iCloud nebo vypnutý iCloud Drive/CloudKit: aplikace funguje lokálně a jednou srozumitelně řekne proč; po zapnutí se data synchronizují.
- Do iCloudu nejdou fotky, jen strukturovaná data (sady, slova, pokusy).
- Synchronizují se data obou jazyků; oddělení jazyků (D18) je věcí zobrazení, plánu a statistik, ne úložiště. Zda se mezi zařízeními synchronizuje i aktivní jazyk, je Q18.
- Platnost závisí na sondách U1–U3 (dětský iCloud a prostředí CloudKit při Ad Hoc, R6, `10`) a na R4; neověřeno.

### US-12 Konflikty při syncu — P0 (předpoklad)
Jako syn chci, aby se při procvičování na dvou zařízeních nic neztratilo ani nezdvojilo, abych si nemusel dávat pozor, kde zrovna učím.
- Given stejná slova procvičená na obou zařízeních offline, When se zařízení znovu spojí, Then zůstanou všechny pokusy z obou zařízení.
- Plán opakování se po sloučení přepočítá z historie pokusů, nikoli „poslední zápis vyhrává".
- Úprava téhož slova na obou zařízeních: vyhraje pozdější změna, v nastavení lze podívat, co bylo přepsáno (předpoklad, Q7).
- Smazání na jednom zařízení nesmí „oživit" slovo z druhého zařízení bez upozornění.
- Duplicitní slova po sloučení se nabídnou ke sloučení, nikoli automaticky smažou (R4: žádné unikátní atributy, takže duplicity jsou možné).
- Duplicity se hledají jen v rámci jednoho jazyka (D18): stejně zapsané slovo v angličtině a němčině (např. Hand / hand) není duplicita a nesloučí se.

### US-13 Offline provoz — P0 (předpoklad)
Jako syn chci moci procvičovat bez internetu (v autobuse, o prázdninách), abych se mohl učit kdekoli.
- Given zařízení bez připojení, When otevřu aplikaci, Then funguje import fotkou (OCR v zařízení), správa sad, test, vyhodnocení, plán i souhrn.
- Změny se při offline uloží lokálně a po připojení se synchronizují (US-11, US-12).
- Přehrání výslovnosti (US-18) funguje offline, pokud je hlas stažen; jinak aplikace srozumitelně řekne, co chybí (R3, domněnka).
- Žádná funkce bez dobrého důvodu nevyžaduje síť; aplikace nehlásí „chyba sítě" tam, kde síť nepotřebuje.

### US-26 Přepnutí aktivního jazyka — P0 (D18)
Jako syn chci jednoduše přepnout mezi angličtinou a němčinou, abych vždy procvičoval jen jeden jazyk a nic se mi nemíchalo.
- **Viditelná volba:** Given kterákoli obrazovka nejvyšší úrovně (domů, sady, historie, slabá místa), When se na ni podívám, Then vidím, který jazyk je aktivní (Angličtina / Němčina), a jedním ťuknutím ho mohu přepnout. Volba není schovaná v nastavení.
- Přepnutí je okamžité, funguje offline, nic nemaže ani nemění v datech a nespouští žádný test. Po přepnutí ukazují všechny obrazovky (sady, test, plán, souhrn, historie, slabá místa, slovesa, výslovnost) jen nový jazyk. Import je výjimka (D22): jazyk si volí syn jako první krok importu (předvyplněný aktivním jazykem) a import aktivní jazyk nemění.
- **Pamatuje si poslední jazyk:** Given zavřená a znovu otevřená aplikace, When ji spustím, Then je aktivní jazyk, který byl aktivní naposledy. Při úplně prvním spuštění ho volí syn (US-01). Aplikace jazyk nikdy nepřepíná sama (ani podle obsahu fotky, souboru nebo termínů). Zda se poslední jazyk sdílí mezi zařízeními, je Q18.
- **Nepřepíná se uprostřed testu:** Given probíhající relace (test nebo procvičování sloves), When jsem uvnitř, Then přepínač jazyka není dostupný (skrytý, nebo neaktivní s krátkým vysvětlením) a jazyk platí do konce relace. Relaci lze ukončit (odpovědi se uloží, US-05, US-09); po souhrnu lze jazyk přepnout. Odchod do jiné aplikace a návrat jazyk nemění.
- **Druhý jazyk bez míchání:** Given aktivní jazyk A a v druhém jazyce B slova s nastalým termínem, When se podívám na přepínač, Then u jazyka B vidím neutrální počet slov k procvičení (např. „Němčina · 12 slov k procvičení"). Je to jen informace u přepínače: slova se nepřidají do fronty, testu ani statistik aktivního jazyka. Ťuknutí na ni jazyk jen přepne, test nespustí. Číslo je nabídka, ne resta: bez červené a bez „dluhu" (US-08). Jak se zobrazí při velkém počtu, je Q19.
- **Prázdný stav jazyka bez sad:** Given jazyk, ve kterém zatím není žádná sada, When ho přepnu (nebo zvolím v US-01), Then vidím přívětivý prázdný stav (např. „Pro němčinu tu zatím nemáš žádná slovíčka") s nabídkami „Vyfotit slovíčka" a „Vytvořit sadu" (nová sada vznikne v tomto jazyce), bez tlačítka testu a bez chybové hlášky. U přepínače se místo počtu zobrazí „zatím žádné sady".
- Přepínač zůstává použitelný i v prázdném stavu, takže se lze kdykoli vrátit do jazyka se sadami.
- **Import do neaktivního jazyka nemění aktivní jazyk (D22):** Given aktivní jazyk A, When naimportuji slovíčka (fotka i CSV) do jazyka B, Then po importu je aktivní jazyk stále A, všechny nové sady a slova mají jazyk B a obrazovky aktivního jazyka A je neukazují. Nabídka „Přepnout na … a procvičit" jazyk přepne jen po ťuknutí syna. Uprostřed importu je přepínač nedostupný stejně jako uprostřed testu.
- Testovatelné v `Domain`: sestavení fronty a statistik pro jazyk A nikdy neobsahuje slovo, pokus ani sadu jazyka B; import do jazyka B nezmění aktivní jazyk a každý vzniklý záznam má jazyk B.

---

## P1

### US-14 Historie pokroku — P1
Jako syn chci vidět, jestli se zlepšuji, abych měl radost z pokroku.
- Given dostatek pokusů, When otevřu historii, Then vidím vývoj v čase (týdně) pro **aktivní jazyk** (D18), po sadě: počet naučených slov a úspěšnost. Žádný součet ani graf přes oba jazyky dohromady (Q17); druhý jazyk se zobrazí po přepnutí (US-26).
- „Naučené slovo" má jasnou definici (např. vybavené psaním po delším intervalu; přesně Q1).
- Zobrazení vždy říká i „zlepšuje se / drží se / zatím se nezlepšuje", ale tónem bez odsuzování; při nedostatku dat řekne „zatím málo dat" místo grafu s nulou.
- Mezery v čase (prázdniny) nejsou zobrazeny jako selhání.
- Lze otevřít souhrn jednotlivého pokusu (US-09).

### US-15 Slabá místa — P1
Jako syn chci vědět, kde mi to nejde, abych se na to mohl zaměřit.
- Given historie pokusů, When otevřu slabá místa, Then vidím nejčastěji chybná slova, a také vzorce: chyby v členech der/die/das, diakritice, konkrétním směru. Přehled je jen pro aktivní jazyk (D18); chyby v členech se proto objeví jen v němčině.
- Z přehledu lze jedním ťuknutím spustit relaci jen na slabá slova; relace zůstává v aktivním jazyce (D18).
- Seznam je krátký (např. top 10), zdůrazňuje cestu dál, ne počet chyb.
- Přesný práh „slabého" slova je Q1.

### US-16 Zadání nepravidelných sloves — P1
Jako syn chci zadat nepravidelná slovesa ručně, abych je mohl procvičovat podle učebnice.
- Given sada sloves, When přidám sloveso, Then zadám tvary (EN: infinitiv, minulý čas, příčestí; DE: infinitiv, 3. os. préteritum, perfektum s pomocným slovesem; předpoklad, Q9) a český význam.
- Slovesa se zadávají ručně (D8), ne fotkou.
- Jazyk slovesa dává sada sloves (D18): sada sloves patří jednomu jazyku a podle něj se nabídnou tvary (EN, DE výše). Sloveso nelze přidat do sady druhého jazyka; v seznamu se nabízejí jen sady sloves aktivního jazyka.
- Validace: chybějící tvar upozorní, neblokuje uložení.
- Slovesa se synchronizují jako ostatní data (US-11).

### US-17 Procvičování nepravidelných sloves — P1
Jako syn chci nepravidelná slovesa procvičovat zvlášť, protože se učí jinak než běžná slovíčka.
- Given sada sloves, When spustím procvičování, Then jsem dotázán na chybějící tvary (zadaná část, hledané tvary psaním).
- Hodnocení tvarů je po tvarech: vím, který z tvarů byl špatně; pravidla překlepů a diakritiky jako v US-07.
- Plán opakování sloves používá stejný princip jako US-08 (předpoklad).
- Souhrn a historie zahrnují slovesa odděleně od slovíček.
- Procvičování sloves běží v aktivním jazyce (D18): nabízí jen sady sloves tohoto jazyka a plán, souhrn i historie sloves se vedou po jazycích zvlášť.
- Procvičování sloves je samostatný vstup v aplikaci (D8), ne součást běžného testu.

### US-18 Výslovnost: přehrání — P1
Jako syn chci slyšet, jak se slovo správně vyslovuje, abych si ho zapamatoval nejen očima.
- Given slovo nebo odpověď, When ťuknu na ikonu reproduktoru, Then se přehraje cizí slovo hlasem (de-DE, en-GB/en-US) pomocí systémové syntézy.
- Dostupné na kartě slova, při ukázání správné odpovědi i v souhrnu.
- Funguje offline, pokud je hlas v zařízení (R3). Pokud ne, srozumitelná hláška.
- Přehrání se spouští jen na ťuknutí, nikdy automaticky; respektuje ztlumení zařízení (předpoklad).
- **Hlas určuje jazyk sady** (D18): slovo se vždy čte hlasem svého jazyka (angličtina, němčina), nikdy hlasem druhého jazyka a bez ručního výběru hlasu u slova. Chybějící hlas se kontroluje a hlásí jen pro jazyk, ve kterém se přehrává (R3).
- Varianta angličtiny (britská/americká) je otevřená otázka Q10.
- Kontrola výslovnosti syna je mimo tuto story (US-22).

### US-19 CSV import — P1
Jako syn (nebo otec) chci načíst slovíčka z CSV souboru, abych využil seznam, který už někde existuje.
- Given soubor CSV (UTF-8, dva až tři sloupce: cizí, česky, volitelně člen/poznámka), When ho vyberu, Then uvidím náhled a stejnou kontrolu a opravu jako u OCR (US-03).
- Chybné řádky se označí, neuloží se slova bez překladu.
- Rozpoznání oddělovače (čárka/středník) a kódování; diakritika se nezkomolí.
- **Jazyk se volí jako první krok importu (D22)**, stejně jako u fotky (US-02): předvyplněný aktivním jazykem, jde zvolit i druhý; nabízejí se sady zvoleného jazyka a nová sada vznikne ve zvoleném jazyce. Zvolený jazyk je po celý import pevný a vidět na kontrolní obrazovce. CSV import nemění aktivní jazyk zařízení; po uložení lze nabídnout „Přepnout na … a procvičit".
- Import jde do vybrané sady a jazyk importu je jazyk zvolený na začátku (D22), shodný s jazykem sady (D18); duplicity se nabídnou ke sloučení (jen v rámci jazyka). Jeden soubor = jedna sada = jeden jazyk: řádky, které vypadají jako druhý jazyk (nebo mají odlišné označení jazyka, Q11, Q21), se označí k ruční kontrole a automaticky se neuloží.
- Přesný formát je otevřená otázka Q11.

### US-20 Smazání dat — P1 (předpoklad)
Jako syn (nebo otec) chci smazat všechna svá data, abych měl jistotu, že po mně nic nezůstalo.
- Given nastavení, When zvolím „Smazat všechna data", Then ukáže se, co se smaže (sady, slova, historie, iCloud kopie), a po potvrzení se smaže lokálně i v iCloudu.
- Smazání je potvrzené dvoustupňově a nevratné; předtím je nabídnut export (předpoklad, Q12).
- Po smazání se aplikace vrátí do stavu prvního spuštění (US-01), tedy i s volbou jazyka (D18). Smazání dat jen jednoho jazyka je otevřená otázka Q20.
- Smazání jedné sady a smazání celého účtu jsou oddělené akce (US-04).
- Odinstalování aplikace nemusí smazat iCloud data; v nastavení je to vysvětleno (domněnka, ověřit).

---

## P2

### US-21 Hotové sady — P2
Jako syn chci k některým učebnicím využít hotové sady, abych nemusel nic fotit.
- Pouze pokud je licence v pořádku (D7); bez ověřené licence se story neprovádí.
- Given hotová sada, When ji přidám, Then se chová jako vlastní sada (US-04) a lze ji upravit.
- Zdroj, autor a licence jsou u sady uvedeny.
- Hotová sada patří jednomu jazyku (D18) a v seznamu se nabízí jen v tomto jazyce.

### US-22 Kontrola výslovnosti hlasem — P2
Jako syn chci říct slovo nahlas a dozvědět se, jestli ho vyslovuji správně, abych si výslovnost zlepšil.
- Given slovo, When ho řeknu do mikrofonu, Then aplikace posoudí shodu s očekávaným slovem a řekne to neutrálně a povzbudivě.
- Rozpoznání musí běžet v zařízení (R5); jinak se story neprovádí.
- Samostatná, pozdější funkce (D9); jde o otevřenou otázku, ne závazek.
- Rozpoznání řeči se nastaví na jazyk sady (D18), ne na oba jazyky zároveň.
- Chybné rozpoznání není vina syna; aplikace nesmí zhoršit pocit z neúspěchu (princip 1).

### US-23 Rodičovský pohled — P2 (D11)
Jako otec chci vidět, jak synovi učení postupuje, abych mu mohl pomoci, ale ne ho kontrolovat.
- Zatím se nerealizuje (D11). Povinný požadavek na architekturu: výsledky a historie jsou uložené tak, aby šly číst z jiného účtu/zařízení.
- Rodič vidí jen agregace (pravidelnost, úspěšnost, slabá místa), ne jednotlivé „selhání" v reálném čase (předpoklad).
- Přístup vyžaduje souhlas syna nebo sdílení v rámci Family Sharing (způsob je Q13).
- Žádná analytika ani server třetí strany (R5).
- Agregace jsou po jazycích, stejně jako u syna (D18). Zda rodič uvidí i celkový přehled přes oba jazyky, je Q17 (doporučení: ne).

### US-24 Gamifikace — P2 (D13)
Jako syn chci za učení dostávat zábavné odměny, abych měl větší chuť se vracet.
- Přání, ne priorita (D13). Nesmí porušit principy 1–3: žádné ztráty, žádné trestající série, žádné odpočítávání.
- Odměny se vztahují k pokroku a vytrvalosti, ne k rychlosti; vypínatelné v nastavení.
- Odměny a úrovně se vedou po jazycích a nepočítají se přes oba dohromady (D18); odměna nesmí navádět k míchání jazyků.
- Konkrétní podoba (odznaky, úrovně, motiv) je otevřená otázka Q14; do rozhodnutí se neimplementuje.

### US-25 Připomenutí — P2
Jako syn chci být zdvořile upozorněn, když mám slova k opakování, abych nezapomněl procvičovat.
- Upozornění je lokální, volitelné, ve výchozím stavu vypnuté, s volbou času.
- Text je neutrální nabídka, ne výtka; žádné opakované upozornění při ignorování.
- Upozornění nesčítá slova přes oba jazyky (D18): vztahuje se vždy k jednomu jazyku a ten uvádí; zda jen k aktivnímu, nebo ke každému zvlášť, je Q19.
- Syn může upozornění kdykoli vypnout; žádný odpočet „série".

---

## Otevřené otázky (co zadání neurčuje)
- **ROZHODNUTO (zadavatel, 2026-11-08, D24: stavy slova a plánování schváleny, naučené = zvládnuté)** — **Q1 Algoritmus plánování a definice „nové/známé/naučené/slabé slovo".** Jaké intervaly, kolik úspěchů je „známé", jak se váží výběr vs. psaní a směr. Doporučení: zvolit osvědčené schéma (např. krabicový/Leitnerův nebo SM-2 odvozený) a rozhodnout s Pavlem.
- **ROZHODNUTO (zadavatel, 2026-11-08, D25: přísnost vyhodnocení schválena)** — **Q2 Přísnost vyhodnocení.** Prahy překlepu, zacházení s diakritikou v češtině i němčině, s velkými písmeny a s chybějícím členem.
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
- **ROZHODNUTO (zadavatel, 2026-11-08, D19: ne, jazyk sady je neměnný)** — **Q16 Změna jazyka sady po vytvoření (D18).** Jde jazyk sady změnit? Doporučení: ne, jazyk je neměnný. Chybně založenou sadu syn opraví novou sadou ve správném jazyce (slova znovu naimportuje nebo zadá) a starou smaže. Alternativa: povolit změnu s varováním, že se plán a historie slov přepočítají pod novým jazykem (členy, výslovnost a vyhodnocení pak mohou dávat nesmysl). Rozhodne zadavatel.
- **ROZHODNUTO (zadavatel, 2026-11-08, D20: ne, žádný celkový souhrn)** — **Q17 Celkový souhrn přes oba jazyky.** Zobrazovat součet (počet slov, úspěšnost, pravidelnost) přes angličtinu i němčinu, u syna i v rodičovském pohledu (US-23)? Doporučení: ne. D18 jazyky odděluje a součet by je míchal; srovnávání „který jazyk jde hůř" by mohlo demotivovat (princip 1). Měřítko M1 (dny s relací v kterémkoli jazyce) se počítá lokálně a nezobrazuje.
- **ROZHODNUTO (zadavatel, 2026-11-08, D23): aktivní jazyk se drží lokálně na zařízení, nesynchronizuje se.** **Q18 Kde se drží aktivní jazyk.** Lokálně na zařízení, nebo synchronizovaně přes iCloud? Doporučení: lokálně na zařízení (iPhone a iPad smí být každý v jiném jazyce a jazyk se nepřepne sám z jiného zařízení); nové zařízení se při prvním spuštění zeptá (US-01).
- **ROZHODNUTO (zadavatel, 2026-11-08, D26 podle doporučení)** — **Q19 Počet k procvičení u druhého jazyka (US-26) a připomenutí (US-25).** Přesné číslo, číslo se stropem (např. „20+"), nebo jen „je co procvičit"? Mají se připomenutí týkat jen aktivního jazyka, nebo každého zvlášť? Doporučení: číslo se stropem bez barevného zvýraznění; připomenutí vždy s názvem jazyka.
- **ROZHODNUTO (zadavatel, 2026-11-08, D26 podle doporučení)** — **Q20 Smazání a export po jazycích (US-20).** Nabízet smazání nebo export dat jen jednoho jazyka? Doporučení: ne v první verzi; stačí smazání sady a smazání všeho.
- **ROZHODNUTO (zadavatel, 2026-11-08, D26 podle doporučení)** — **Q21 Text z druhého jazyka při importu (US-02, US-19).** Detekovat jazyk obsahu fotky nebo souboru automaticky a upozornit? Doporučení: jen upozornění a označení k ruční kontrole, žádné automatické přepnutí ani přesun; spolehlivost detekce u krátkých slov je **domněnka** k ověření (R2).
- **R1–R5** viz `00-zadani.md`; tvrzení o platformě v těchto stories jsou **domněnka**, dokud nejsou ověřena.

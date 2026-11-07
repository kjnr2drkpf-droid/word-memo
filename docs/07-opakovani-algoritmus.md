# 07 — Algoritmus opakování, hodnocení a metriky pokroku

Stav k 2026-11-07. Zdroj pravdy: `00-zadani.md` (D2, D3, D4, D8, D12). Rozsah produktu tento dokument nemění; parametry jsou **návrh k odsouhlasení**.
Označení: **simulace** = ověřeno v syntetickém modelu žáka (viz „Simulační model“), **ověřeno (zdroj)**, **domněnka** = nedoloženo.

## Závěr
1. **Plánování: FSRS-6 s výchozími parametry, cílová úspěšnost (desired retention) 0,93, bez optimalizace parametrů.** Ne kvůli lepší retenci: simulace ukazuje při stejné zátěži **paritu s Leitnerem** (slabá paměť: FSRS 7,8 opakování/kartu a p=0,68, Leitner 7,6 a p=0,72). Důvod je souvislá stabilita a pravděpodobnost vybavení u každé karty (potřebná pro metriky D4), jediný ladicí parametr a rozumné zacházení s nepravidelným cvičením. Leitner je záloha: plánovač je za rozhraním, výměna je levná.
2. **Výchozí FSRS s cílem 0,9 u slabé paměti cíl nedrží:** skutečná úspěšnost při opakování 0,71 místo 0,9 (výchozí parametry jsou pro dítě příliš optimistické). Proto 0,93 (skutečně 0,78) a **měřit skutečnou úspěšnost při opakování** (hlídač v tabulce parametrů).
3. **Nejdůležitější zjištění: správná odpověď z výběru z možností se hodnotí jako Hard (2), ne Good.** Výběr se dá trefit bez vybavení (hádání 25 %) a pak by plánovač slovo přeceňoval. Simulace (slabá paměť, mix, přechod na psaní při S ≥ 4 d): s „výběr správně = Good“ chybuje u psaní 33 % pokusů a po 14 dnech bez cvičení je vybavení 0,59; s „Hard“ chybuje 16 % a vybavení je 0,79 (cena: ~27 % více pokusů, hlavně levných výběrů).
4. **Režim testu (D3 mix) se řídí stavem karty:** Nové a Učí se = výběr ze 4, Známé (S ≥ 4 d a 2 úspěchy v řadě) a Zvládnuté (S ≥ 30 d) = psaní. Při neúspěchu u psaní S klesne a karta se sama vrátí k výběru. Mix je při srovnatelném čase lepší než samotné psaní (85 s vs. 111 s na kartu, 0,79 vs. 0,71 vybavení, 0,79 vs. 1,40 chyby na kartu).
5. **Překlep = Hard, ne Again.** Rozdíl ve výsledku je malý (0,59 vs. 0,62), ale Again stojí +0,5 pokusu na kartu a trestá správné vybavení.
6. **Nepravidelná slovesa:** jedna karta na sloveso, všechny tvary se zadávají najednou, samostatná fronta/relace, 3 nová slovesa na relaci seskupená podle vzoru. Frekvence relací rozhoduje víc než počet nových: 5 relací týdně → ~43 dní k 80 % zvládnutých, 3 týdně → ~86 dní, 2 týdně → téměř nikdy (simulace, 60 sloves).
7. **Trend:** okno 14 dnů s 25–60 pokusy je šum (falešný poplach při pravidle „±5 pp“ 57–65 %). Trend se hlásí až při ≥ 100 pokusech v každém z 28denních oken a z ≥ 1,64, jinak „málo dat“ (falešný poplach 10 %, záchyt zlepšení o 15 pp 74 %). Slabé kategorie: ≥ 30 pokusů a chybovost ≥ celková + 10 pp, vždy jako „podezření“ (záchyt 61 %, falešné označení 5 % kategorií).

## 1. Volba algoritmu

| Kritérium | Leitner (boxy 1,2,4,7,14,30 d) | SM-2 | FSRS-6 (výchozí parametry) |
|---|---|---|---|
| Kód, riziko | ~20 řádků, triviální | ~30 řádků | ~100 řádků vzorců, referenční implementace py-fsrs 6.3.2 (ověřeno: spuštěno). Swift port psát ručně (domněnka: žádná závislost není nutná) |
| Stabilita a P(vybavení) na kartu | jen číslo boxu | jen interval | ano, spojitě (pro D4) |
| Přeskočené dny | box +1 i při velkém zpoždění | stejně | používá skutečný odstup |
| Po neúspěchu | zpět do boxu 1 | EF klesá (riziko „ease hell“, domněnka) | S klesá úměrně, karta se vrací brzy |
| Potřebuje data na kartu | ne | ne | ne (výchozí parametry); optimalizace až po cca 1000 opakováních (domněnka, z dokumentace FSRS z paměti) |
| Simulace, slabá paměť (150 běhů) | 7,6 opak., p@d56 = 0,72 | 7,2 opak., 0,62 | 7,8 opak., 0,68 (cíl 0,93) |
| Simulace, průměrná paměť | 5,9 opak., 0,92 | 5,2 opak., 0,87 | 5,2 opak., 0,87 |

Obhajoba pro malé sady a dítě:
- **Malá sada, krátká historie na slovo:** žádný algoritmus se nemá z čeho učit; proto pevné výchozí parametry bez personalizace. Rozdíl mezi Leitnerem a FSRS je v simulaci malý (p@d56 do 0,06; FSRS s cílem 0,95 vychází o 0,01 až 0,06 výš, ale s ~1 opakováním navíc), SM-2 je nejhorší (0,62 vs. 0,72).
- **Dítě s horší pamětí:** důležité je méně neúspěchů (princip 1 ve vizi). Vyšší cíl 0,93 snižuje chyby při opakování z 1,73 (cíl 0,9) na 1,47 na kartu za 6 týdnů, za cenu +0,8 opakování na kartu.
- **Robustnost:** parita Leitner/FSRS platí i při nepravidelném cvičení (40 % dnů) a velké rozptýlenosti obtížnosti slov (simulace; Leitner vs. FSRS s cílem 0,95, rozdíl p@d56 ≤ 0,06 ve všech 4 variantách).
- **Co simulace nedokazuje:** že FSRS je lepší než Leitner. Dokazuje, že na volbě retence v tomto modelu nezáleží; volba stojí na metrikách a ladění. Pokud by implementace FSRS dělala potíže, Leitner je přijatelná náhrada s mapováním boxu na „stabilitu“ (box → dny).

Hlídač kalibrace (domněnka, nesimulováno): z posledních 100 opakování se počítá skutečná úspěšnost (podíl ≠ Again) u karet s odstupem ≥ 2 d. Je-li < 0,75, zvýšit cíl o 0,01 (max 0,96); je-li > 0,92, snížit (min 0,90).

## 2. Převod odpovědi na hodnocení

Hodnocení FSRS: 1 Again, 2 Hard, 3 Good, 4 Easy. **Easy se v první verzi nepoužívá** (domněnka: pro dítě s horší pamětí je riziko přeceňování větší než přínos úspory opakování).
Hodnotí se **první pokus na kartu v relaci**; opakování téhož slova po chybě v relaci se jen ukládá (neposílá se do FSRS).

| Situace | Výběr z možností (MC, 4 volby) | Psaní |
|---|---|---|
| Správně, bez nápovědy | **Hard (2)** | **Good (3)** |
| Správně, jen překlep (Damerauova vzdálenost 1, slovo ≥ 4 znaky; chybějící ä/ö/ü/ß také překlep) | n/a | **Hard (2)** + zvýraznit správný tvar |
| Německé podstatné jméno správně, špatný člen | člen je součástí voleb: chyba = Again | **Hard (2)**, fazeta „člen“ = chyba |
| Správně po nápovědě 1. úrovně (první písmeno + délka) | n/a | **Hard (2)** |
| Nápověda 2. úrovně nebo odhalení odpovědi | n/a | **Again (1)** |
| Špatně / „nevím“ | **Again (1)** | **Again (1)** |
| Čas odpovědi | ukládá se, do hodnocení nevstupuje | ukládá se, do hodnocení nevstupuje |

Zdůvodnění a ověření:
- **MC správně = Hard** je jediná volba z testovaných, která drží chyby u psaní na ~16 %. Citlivost: při síle rozpoznání p^e s e = 0,2 až 1,0 vychází Hard lepší ve všech čtyřech případech (p@d56 0,77 až 0,80 proti 0,58 až 0,59 u Good; **simulace**).
- **Překlep** (3 varianty, S ≥ 4 d): Again 0,62, Hard 0,59, Good 0,58 (slabá paměť), Again stojí 7,9 pokusu na kartu proti 7,4. Hard: nepotrestá znalost a nepřipíše překlep jako zvládnutí. Pravopis se sleduje samostatně (fazeta, kap. 5).
- **Čas:** žádné rozumné prahy bez dat (**domněnka**); měření pomalosti by navíc šlo proti zásadě „žádný tlak“ (01-vize, princip 3). Čas se jen loguje (`responseMs`) a po ~500 pokusech lze kalibrovat pravidlo „pomalá správná → Hard“ (např. nad 10 s + 1,5 s na znak, nepoužít nad 60 s). Do té doby vypnuto (`use_time_for_grade = false`).
- **Hard zvyšuje obtížnost D ve FSRS** (ověřeno během py-fsrs: 4× Hard posune D z 5,1 na 8,5; 4× Good drží 2,1). Proto D **nepoužívat jako metriku obtížnosti slova**; slouží k ní lapsy a S (kap. 5).

Pseudokód:
```
func rating(a: Attempt) -> Rating {                   // a = první pokus karty v relaci
  if a.dontKnow || a.outcome == .wrong || a.hintLevel >= 2 { return .again }
  if a.mode == .choice { return .hard }               // MC: správně = Hard
  // psaní
  if a.outcome == .typo || a.hintLevel == 1 || a.articleWrong { return .hard }
  return .good                                        // Easy se nepoužívá
}
```

## 3. Fáze karty a změna režimu testu

Stav se vede **na kartu = slovo × směr** (D2: cizí → čeština a čeština → cizí jsou dvě nezávislé karty). Směr čeština → cizí (produkce) se odemkne, jakmile karta cizí → čeština má alespoň jeden úspěch v jiný den (návrh, nesimulováno).

| Fáze | Podmínka | Režim testu | Poznámka |
|---|---|---|---|
| Nové | žádné opakování | ukázka (slovo, překlad, přehrání výslovnosti, D9), hned potom výběr ze 4 | první hodnocení jde do FSRS |
| Učí se | alespoň 1 opakování a (S < 4 d nebo méně než 2 úspěchy v řadě) | výběr ze 4; po chybě opakování v relaci po 3–5 jiných kartách, nejvýše 2× | zbytek čeká na plán FSRS |
| Známé | S ≥ 4 d a ≥ 2 úspěchy v řadě | **psaní** (tlačítko „nápověda“, „nevím“) | |
| Zvládnuté | S ≥ 30 d a ≥ 3 úspěchy v řadě | psaní | opakuje se dle plánu (interval ≥ ~30 d) |

- Fáze se **počítá ze stavu při každém výběru testu**, nepamatuje se zvlášť. Chyba u psaní proto automaticky vrací kartu do Učí se a k výběru (S po Again klesne pod 4 d).
- Úspěch v řadě = hodnocení ≠ Again; Hard se počítá jako úspěch.
- Distraktory ve výběru: ze stejného jazyka a slovního druhu, u německých podstatných jmen se stejným členem nebo odlišné (návrh pro content/UX).
- **Prahový S pro psaní** (simulace, MC → Hard, slabá paměť): 2 d → chyby u psaní 15 %, čas 99 s/kartu; **4 d → 16 %, 85 s**; 7 d → 18 %, 71 s; vybavení p@d56 0,79 / 0,79 / 0,78. Vybraných 4 d je kompromis; 7 d je nejkratší čas při stejném výsledku. Rozdíly jsou malé, volba není kritická.
- Délka relace (domněnka, nesimulováno čas): 30 karet ≈ 6–8 minut při odhadu 6 s na výběr a 15 s na psaní.

Plánování relace:
```
func buildSession(now) -> [Card] {
  due  = cards.filter { $0.due <= now }.sorted(by: due ascending)    // nejvíc po termínu první
  list = due.prefix(maxCardsPerSession)                              // 30
  if due.count <= 40 { list += nextNewCards(newPerSession) }         // 8 nových karet
  return list     // po Again: vložit zpět o 3–5 pozic dál, max 2×, výsledek neposílat do FSRS
}
```
Přeskočené dny nic nepenalizují: nedělá se „nedoplněný dluh“, po pauze se bere nejstarší termín jako první a relace má strop.

## 4. Nepravidelná slovesa (D8)

- **Entita:** jedna karta na sloveso (infinitiv + český význam + tvary). Angličtina: infinitiv, minulý čas, příčestí (3 tvary). Němčina: infinitiv, Präteritum, Partizip II s pomocným slovesem (hat/ist), 3. osoba přítomného času jen u sloves se změnou kmene (3–4 tvary). Zadávají se ručně, seznam je konečný (D8).
- **Test:** zadání je infinitiv + český význam, odpověď = všechny tvary najednou. Reverzní směr (tvar → význam) se neprocvičuje (domněnka: rozpoznání je lehčí a hlavní obtíž je produkce tvarů).
- **Hodnocení karty:** všechny tvary správně = Good (psaní) / Hard (výběr tvaru z voleb); jen překlepy = Hard; jakýkoli chybný či chybějící tvar = Again. Výsledky po tvarech se ukládají pro metriky. Po chybě se v relaci opakují jen chybné tvary.
- **Fáze a režim:** stejná pravidla jako u slov (Nové a Učí se výběr tvarů z voleb s distraktory „pravidelný tvar“ a „tvar podle jiného vzoru“, Známé psaní). Výběr tvarů nebyl simulován (**domněnka**).
- **Plán a tempo** (simulace: 60 sloves, každý tvar má vlastní paměť, slabá paměť, strop 25 sloves na relaci):

| Varianta | dní do stavu „80 % sloves vybavených ≥ 0,8“ | průměr kontrol/relace |
|---|---|---|
| 3 nová/relace, 5 relací týdně | **43** | 14,2 |
| 3 nová/relace, 3 relace týdně | **86** (nedosaženo za 120 dní v 6 % běhů) | 20,8 |
| 2 nová/relace, 3 relace týdně | 87 | 19,2 |
| 5 nových/relace, 3 relace týdně | 81 | 22,1 |
| 3 nová/relace, 2 relace týdně | téměř nikdy (92 % běhů nedosáhlo) | 20,8 |

  Vliv počtu nových sloves na relaci je malý (strop relace), rozhoduje četnost. **Doporučení:** 3 nová slovesa na relaci seskupená podle vzoru (sing–sang–sung, drink–drank–drunk), strop 25 sloves (~ 8–10 min při 20–25 s na sloveso, domněnka), alespoň 3 relace týdně; samostatná fronta, aby slovesa nepřebila běžná slovíčka. Při menší frekvenci je nutné jasně říct, že 60 sloves je záležitost týdnů, ne dní.

## 5. Metriky pokroku a slabých míst

Ukládá se každý pokus (viz „Co musí uchovávat datový model“). Metriky se vždy počítají **zvlášť pro psaní a výběr**: s postupem karet do psaní by společná úspěšnost sama klesala (simulace: u psaní se chybuje v 16–33 % pokusů, u výběru výrazně méně kvůli hádání), takže by „zhoršování“ ukazovala falešně.

| Metrika | Definice | Poznámka |
|---|---|---|
| **Úspěšnost** | podíl prvních pokusů s hodnocením ≠ Again; za okno 7 dnů pro zobrazení | psaní a výběr zvlášť; u výběru zobrazit i „nad náhodou“ = (úspěšnost − 0,25) / 0,75 |
| **Vybavení po odstupu** | úspěšnost u psaní s odstupem od minulého opakování ≥ 7 d | odpovídá M4 ve vizi (cíl ≥ 70 %) |
| **Stabilita** | S karty z FSRS ve dnech; slovo = minimum přes jeho karty; sada = medián S | popisná, bez testu |
| **Odhad zvládnutí sady** | průměr R(teď) přes karty; označit jako odhad | R je predikce plánovače, ne měření |
| **Zvládnuté slovo** | fáze Zvládnuté (S ≥ 30 d a ≥ 3 úspěchy v řadě) | „X z N“ |
| **Trend** | porovnání vybavení po odstupu a úspěšnosti u psaní v aktuálním a předchozím 28denním okně | pravidlo níže |
| **Slabé slovo** | karta s ≥ 2 pády (Again) za posledních 30 dní a S < 7 d; zobrazit nejvýš 10 řazených podle pádů, pak S vzestupně | |
| **Zaseknuté slovo** | ≥ 4 pády celkem; nabídnout jiný přístup (příklad ve větě, rozložení, přeskočit) | |
| **Slabá kategorie** | kategorie s ≥ 30 pokusy za 60 dní a chybovostí ≥ celková + 10 pp; nejvýš 3, jako „podezření“ | pravidlo níže |

**Trend (pravidlo):** v každém okně musí být ≥ 100 pokusů, jinak „málo dat“. Označit „zlepšuje se“ / „zhoršuje se“ jen při rozdílu ≥ 5 pp a z-testu dvou podílů z ≥ 1,64 v daném směru, jinak „beze změny“. **Simulace (binomiální):** při skutečně beze změny 10 % falešných poplachů (n = 100), záchyt zlepšení o 10 pp 43 %, o 15 pp 74 % (n = 150: 48 % a 82 %). U 14denního okna s 25–60 pokusy: pravidlo „±5 pp“ falešně hlásí změnu v 57–65 % případů; s z ≥ 1,64 a 10 pp záchyt zlepšení o 15 pp jen 30–55 %. Proto 28 dní a n ≥ 100. Zprávy musí být neutrální („beze změny“ není neúspěch).

**Slabá a zaseknutá slova (simulace, slabá paměť, 42 dní):** 23 % karet má ≥ 3 pády, 13 % ≥ 4. Pravidlo ≥ 2 pádů označí 39 % karet s přesností 43 % (náhodně 25 %), ≥ 4 pády 13 % s přesností 56 %. Značka je jen orientační a proto se zobrazuje seznam nejvýš 10 slov, ne absolutní práh.

**Kategorie (fazety) a jejich pokusy:**

| Kategorie | Kdy se počítá pokus | Chyba |
|---|---|---|
| Člen (der/die/das) | německé podstatné jméno, člen se testoval | špatný člen |
| Množné číslo | množné číslo se testovalo | špatný tvar |
| Nepravidelná slovesa | po tvarech (infinitiv, minulý čas, příčestí) | špatný tvar; zvlášť „který tvar“ |
| Pravopis | odpověď psaním | překlep (nebo chybějící ä ö ü ß) |
| Diakritika ä ö ü ß | v odpovědi je takový znak | chybějící či špatný znak |
| Směr | cizí → čeština vs. čeština → cizí | Again |
| Slovní druh, téma sady | tag slova | Again |

Simulace detekce (5 kategorií, jedna s chybovostí o 15 pp vyšší, celková 30 %): při n = 30 záchyt 61 % a falešné označení 5 % kategorií, při n = 50 63 % / 1 %, při n = 20 60 % / 9 %. Přísnější Wilsonův test záchyt zhoršil (40 % při n = 30), proto se nepoužívá. Fazety se používají **pro metriky a cílená mini-cvičení**, ne jako samostatné plánované karty (návrh; vyhne se nárůstu počtu karet).

## 6. Parametry

| Parametr | Hodnota | Stav |
|---|---|---|
| Plánovač | FSRS-6, 21 výchozích parametrů (py-fsrs 6.3.2) | ověřeno (spuštěno) |
| desired_retention | **0,93** | simulace (0,9: skutečná 0,71; 0,93: 0,78; 0,95: 0,83) |
| maximum_interval | 365 d | v simulaci 365, vliv neověřen |
| Fuzz intervalů | vypnuto | domněnka: u jednoho uživatele nepotřeba |
| learning/relearning steps | žádné; opakování v relaci řeší aplikace | simulace |
| Easy | nepoužívá se | domněnka |
| Prahy fází | Známé: S ≥ 4 d a 2 úspěchy v řadě; Zvládnuté: S ≥ 30 d a 3 úspěchy | simulace (4 d), 30 d domněnka |
| MC správně | Hard (2) | simulace |
| Překlep | Hard (2); prahy: Damerau–Levenshtein 1, délka ≥ 4 | simulace (volba), práh domněnka |
| Nápověda | 1. úroveň → Hard, 2. úroveň → Again | domněnka |
| Čas v hodnocení | vypnuto, jen log | domněnka |
| Počet voleb v MC | 4 | domněnka (hádání 25 %, použito v simulaci) |
| Max. kontrol v relaci | 30 karet; sloves 25 | domněnka (čas), simulace (verb) |
| Nových karet na relaci | 8 (0, pokud po termínu > 40 karet) | simulace (8) |
| Nová slovesa na relaci | 3, seskupená dle vzoru | simulace |
| Odemknutí směru čeština → cizí | úspěch v 1. směru v jiný den | návrh |
| Trend | okno 28 d, n ≥ 100, z ≥ 1,64, Δ ≥ 5 pp | simulace |
| Slabé slovo | ≥ 2 pády/30 d a S < 7 d; zaseknuté ≥ 4 pády | simulace (přesnost) |
| Slabá kategorie | n ≥ 30/60 d, ≥ celková + 10 pp | simulace |
| Hlídač kalibrace | 100 opakování, < 0,75 → +0,01 DR, > 0,92 → −0,01 | domněnka |
| Personalizace parametrů | až po ~1000 opakováních, dle ověření | domněnka |

## 7. Co musí uchovávat datový model (návrh pro architekta)
Pokus (`ReviewLog`): `cardId`, `timestamp`, `mode` (choice/typed), `direction`, `outcome` (správně / překlep / špatně / nevím), `hintLevel`, `rating`, `responseMs`, `typedText` (pro rozpoznání překlepu), `elapsedDays`, `S_before`, `R_before`, po fazetách (`articleOk`, `pluralOk`, `formResults[]`). Stav karty: FSRS (stabilita, obtížnost, termín, počet opakování, pády), počet úspěchů v řadě. Fáze se z něj počítá (nespoléhat na uložené pole). Logy jsou potřeba od první verze, jinak nepůjde kalibrovat čas ani personalizovat parametry. CloudKit omezení (R4) se tím nemění: vše volitelné nebo s výchozí hodnotou.

## 8. Simulační model (předpoklady, pro čtenáře čísel výše)
- **Žák:** exponenciální zapomínání P = 0,9^(t/s), kde s je čas do poklesu na 90 %. Plánovač používá jiný tvar křivky (mocninná ve FSRS), takže model plánovači nenahrává.
- **Slabá paměť:** medián s po 1. expozici 0,8 d, růst při úspěchu ×(1 + 2,2·(1−P)^0,5) (≈ ×1,7 při P = 0,9), obtížnost slov s rozptylem 0,35 (log). **Průměrná:** 2,0 d a 4,0. Neúspěch: s ×0,25 (min 0,3 d), pak opakování ×1,25. Všechny tyto hodnoty jsou **předpoklady, ne měření dítěte**.
- **MC:** rozpoznání P^0,4, jinak hádání 25 %; správné hádání paměť nemění; účinek zapamatování z rozpoznání 0,6× (citlivost: exponent 0,2–1,0). **Překlep:** 10 % správných odpovědí psaním.
- **Provoz:** 80 karet (40 slov × 2 směry), relace v 70 % dnů, 8 nových karet na relaci, strop 40, 42 dní + měření po 14 dnech bez cvičení (p@d56 = skutečná pravděpodobnost vybavení při psaní). 100–150 běhů na variantu.
- Skripty jsou mimo repozitář (`/tmp`, `engine.py`, `sim1–5.py`); nezahrnují reálná data. **Model nelze brát jako důkaz pro konkrétní dítě**: ukazuje směr a řád rozdílů. První 4 týdny provozu je potřeba porovnat skutečné chyby u psaní, skutečnou úspěšnost při opakování a pravidelnost cvičení s těmito čísly.

## Otevřené otázky pro zadavatele
- Má se pro slovíčka přidat možnost, aby syn sám zvolil „chci psát“ i u nových slov (mimo D3)?
- Zobrazovat u výběru „nad náhodou“, nebo jen úspěšnost u psaní? (UX; metrika je definována, zobrazení rozhodne UX.)

# 04 — Story mapa

Stav k 2026-11-07. Vychází z `00-zadani.md` a `02-user-stories.md`. Rozdělení do vln a návrh první verze jsou **návrh k odsouhlasení**; rozsah mění jen zadavatel.

## Závěr
Tři vlny podle priorit ze zadání: **vlna 1 = P0** (13 stories), **vlna 2 = P1** (7), **vlna 3 = P2** (5). Nejmenší smysluplná první verze pro instalaci Ad Hoc je **jádro učební smyčky bez iCloud syncu**: první spuštění, import fotkou s opravou, správa sad, test (mix, oba směry), vyhodnocení, plán, souhrn, uložení a offline (US-01 až US-10, US-13). iCloud sync (US-11, US-12) je P0 a následuje jako druhý build (pokud sondy U1–U3 ukážou, že funguje hned, může být už v prvním, viz `05` kap. 10); datový model se ale od začátku navrhuje pro CloudKit (R4). Před prvním buildem je nutné ověřit sondy U1–U3 (Ad Hoc pod dětským iCloudem a prostředí CloudKit, R6; R1 je uzavřeno, TestFlight dětský účet nespustí) a R2 (kvalita OCR), protože oba mohou změnit plán.

## Mapa po vlnách

| Vlna | Priorita | Stories |
|---|---|---|
| 1A | P0 jádro | US-01, US-02, US-03, US-04, US-05, US-06, US-07, US-08, US-09, US-10, US-13 |
| 1B | P0 sync | US-11, US-12 |
| 2 | P1 | US-14, US-15, US-16, US-17, US-18, US-19, US-20 |
| 3 | P2 | US-21, US-22, US-23, US-24, US-25 |

Vlna 1 je rozdělena na 1A a 1B jen kvůli pořadí buildů, priorita obou je P0.

## Závislosti

Šipka „A → B" znamená, že B potřebuje A.

| Story | Závisí na | Poznámka |
|---|---|---|
| US-01 První spuštění | — | Minimální verze stačí; úvod se rozšiřuje později. |
| US-04 Správa sad | — | Základ datového modelu (sada, slovo). |
| US-10 Uložení výsledků | US-04 | Model pokusů navržen podle omezení SwiftData/CloudKit (R4). |
| US-02 Import fotkou | US-04 | Cíl importu je sada. Riziko R2. |
| US-03 Oprava OCR | US-02 | Sdílená kontrolní obrazovka s US-19. |
| US-07 Vyhodnocení odpovědi | US-04 | Potřebuje slova, členy, více významů. |
| US-05 Test: mix | US-04, US-07, US-08 | Hranice nové/známé z plánovače. |
| US-06 Oba směry | US-05 | Vybavení se eviduje po směrech. |
| US-08 Plánování | US-07, US-10 | Vstup je výsledek vyhodnocení, výstup termíny. |
| US-09 Souhrn | US-05, US-07, US-10 | |
| US-13 Offline | US-02, US-05 | Vlastnost, ne samostatná funkce; ověřuje se průřezově. |
| US-11 iCloud sync | US-10, R1, R4 | Model musí být CloudKit-kompatibilní od začátku. |
| US-12 Konflikty | US-11, US-08 | Plán se přepočítává z historie pokusů. |
| US-14 Historie | US-09, US-10 | Definice „naučené slovo" (Q1). |
| US-15 Slabá místa | US-14, US-07 | Chyby členů a diakritiky z vyhodnocení. |
| US-16 Zadání sloves | US-04 | Samostatný datový typ. |
| US-17 Procvičování sloves | US-16, US-08 | Znovupoužije plánovač. |
| US-18 Výslovnost | US-04 | Nezávislé na testu; R3. |
| US-19 CSV import | US-03, US-04 | Znovupoužije kontrolní obrazovku. |
| US-20 Smazání dat | US-04, US-11 | Musí smazat i iCloud kopii. |
| US-21 Hotové sady | US-04, licence | Neprovádět bez ověřené licence (D7). |
| US-22 Kontrola výslovnosti | US-18 | Rozpoznání řeči v zařízení (R5). |
| US-23 Rodičovský pohled | US-10, US-11, US-14 | Jen architektonická podmínka od vlny 1. |
| US-24 Gamifikace | US-09, US-14 | Nesmí porušit principy 1–3. |
| US-25 Připomenutí | US-08 | |

```
US-04 ─┬─ US-02 ─ US-03 ─ US-19
       ├─ US-07 ─┐
       ├─ US-10 ─┼─ US-08 ─ US-05 ─ US-06 ─ US-09 ─ US-14 ─ US-15
       ├─ US-16 ─ US-17            └──────────────────────┘
       └─ US-18 ─ US-22
US-10 ─ US-11 ─ US-12 ;  US-11 ─ US-20 ;  US-14 ─ US-23, US-24 ;  US-08 ─ US-25
```

Kritická cesta pro první smysluplnou verzi: US-04 → US-10 → US-07 → US-08 → US-05 → US-06 → US-09, paralelně US-02 → US-03.

## Návrh nejmenší smysluplné první verze (instalace Ad Hoc)

**Cíl verze:** syn si nafotí stránku, zkontroluje slova, projde krátký test v obou směrech a dostane souhrn; příště mu aplikace nabídne slova podle plánu. Tím se ověří hlavní hypotéza: *syn se k aplikaci vrací a vybavuje si díky ní slova.*

**Obsahuje (build 1):**
- US-01 První spuštění (nejmenší verze: jedna obrazovka a nabídka „Vyfotit slovíčka").
- US-02 + US-03 Import fotkou s kontrolou a opravou, i z knihovny fotek.
- US-04 Správa sad a slov (vytvořit, upravit, smazat, jazyk EN/DE).
- US-05 + US-06 Test s mixem výběr/psaní a oběma směry.
- US-07 Vyhodnocení: překlepy, diakritika, členy.
- US-08 Plánování opakování (jednoduché schéma, algoritmus podle Q1).
- US-09 Souhrn po pokusu.
- US-10 Uložení výsledků po každé odpovědi.
- US-13 Offline (vlastnost, nic nevyžaduje síť).
- Datový model již s ohledem na CloudKit (R4), i když sync zatím nezapnutý.

**Neobsahuje:** iCloud sync a konflikty (build 2), historii a slabá místa, slovesa, výslovnost, CSV, smazání všech dat, hotové sady, gamifikaci, rodičovský pohled, připomenutí.

**Proč bez syncu v buildu 1:** sync je nejrizikovější část (U1–U3, R4, R6) a první verze potřebuje hlavně ověřit samotný učební cyklus. Pokud syn používá jedno zařízení, sync mu v prvních týdnech nechybí. Je to návrh k odsouhlasení: zadání řadí sync do P0, takže „P0 hotovo" nastane až s buildem 2.

**Podmínky před buildem 1:**
1. U1–U3 (`10` kap. 10): Ad Hoc instalace na synův iPhone a iPad pod dětským iCloudem (Developer Mode), sync v Development i Production a co se stane s daty při změně prostředí. R1 je uzavřeno: TestFlight dětský účet nespustí (ověřeno na zařízení, 2026-11-07). Stav U1–U3: **neověřeno**.
2. R2: vyzkoušet OCR (Vision/VisionKit) na reálných stránkách z jeho sešitu a učebnice (diakritika, ä ö ü ß, dvousloupcové tabulky, ručně psané). Stav: **neověřeno**. Výsledek rozhodne, jak důležitá je oprava (US-03) a zda je ruční zadání nutné jako záloha.
3. Odsouhlasit Q1 (algoritmus a definice stavů slova) a Q2 (přísnost vyhodnocení).

**Měření úspěchu verze 1:** M1, M2, M3, M5 z `01-vize.md` (po 2–4 týdnech); M6 až po buildu 2.

**Pořadí buildů (návrh):**
1. **Build 1:** jádro (vlna 1A).
2. **Build 2:** iCloud sync a konflikty (vlna 1B); úplné P0.
3. **Build 3+:** vlna 2 po jednotlivých kouscích; nejlevnější je US-18 (přehrání) a US-14/US-15 (data už jsou), poté slovesa a CSV, smazání dat před širším použitím (předpoklad).
4. **Vlna 3:** podle rozhodnutí zadavatele; US-23 se nerealizuje, jen se hlídá architektura (D11).

## Průřezové požadavky (platí pro všechny vlny)
- Soukromí: žádná analytika, OCR v zařízení, žádné odesílání obrázků ven (R5).
- Principy z `01-vize.md`: bez tlaku, neúspěch nedemotivuje, krátké relace.
- Rozšiřitelnost datového modelu pro rodičovský pohled a gamifikaci (D11, D13).
- Tvrzení o platformě jsou **domněnka**, dokud je neověříme podle zdroje.

## Otevřené otázky
- **O1 Rozdělení P0 na dva buildy** (jádro bez syncu, pak sync). Souhlasí zadavatel, nebo má být sync součástí první verze?
- **O2 Kdo a kdy ověří R1 a R2.** Bez R1 nelze plánovat distribuci dítěti, bez R2 nelze odhadnout objem oprav.
- **O3 Kolik zařízení syn reálně používá** (iPhone, iPad, obojí)? Ovlivňuje naléhavost syncu.
- **O4 Zda se má do buildu 1 přidat přehrání výslovnosti (US-18)** jako levná P1 věc. Návrh: ne, kvůli rozsahu; pozdější rozhodnutí zadavatele.
- **O5 Zařazení US-20 (smazání dat), US-04 (správa sad), US-01, US-12, US-13 do priorit.** Zadání je výslovně nezařazuje; v dokumentech jsou označeny jako předpoklad.
- **O6 Q1–Q15 z `02-user-stories.md`** (algoritmus, přísnost, vložení fotek otcem a další) čekají na rozhodnutí.

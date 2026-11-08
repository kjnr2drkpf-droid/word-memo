# 12 — Iterace 0: postup a zápis výsledků

Stav k 2026-11-08. Navazuje na `11-plan-stavby.md`. Spouští se **na Macu mini s Xcode**, ne v cloudu (v cloudové session není Swift toolchain).

## Závěr
Iterace 0 jsou čtyři krátké zkoušky na synových zařízeních (iPhone a iPad, iOS 26): **U1** spuštění pod dětským iCloudem, **U2** sync v Development i Production, **U3** změna prostředí, **T6** čtení češtiny z fotky. Výsledek rozhodne o syncu v prvním buildu (`05` kap. 10) a o nastavení rozpoznání textu. **Zkoušky se dělají s jednorázovou testovací aplikací a jednorázovým iCloud kontejnerem, ne s produkčním.** Nasazení schématu do Production je nevratné (`08` b) a nesmí se udělat na kontejneru pro skutečnou aplikaci.

## Pravidla bezpečnosti
1. Testovací aplikace má **jiný bundle ID a jiný iCloud kontejner** než budoucí word-memo (např. `…probe` a `iCloud.…probe`). Po skončení se kontejner nepoužívá dál.
2. Do zkoušek jdou jen vymyšlená data. **Žádná skutečná data syna.**
3. Na synově zařízení se Development a Production buildy téže aplikace nemíchají bez poznamenání výsledku (to je právě zkouška U3).
4. Instalaci na synova zařízení a nasazení schématu do Production dělá člověk, ne agent (`CLAUDE.md`).
5. Nic se nesdílí ven, nic se nenahrává na App Store Connect. Žádný TestFlight.

## Co je potřeba mít
Mac mini s Xcode a přihlášeným vývojářským účtem (tým s placeným Apple Developer Program), kabel, synův iPhone a iPad (Developer Mode zapnutý: Nastavení → Soukromí a zabezpečení → Režim vývojáře, volba se objeví po prvním pokusu o instalaci z Xcode), fotka stránky se slovíčky z jeho sešitu (do T6; fotka zůstane na Macu a nevkládá se do repozitáře), přístup do CloudKit Console.

## Zkoušky

### Příprava (jednou)
Nová SwiftUI aplikace „WordMemoProbe" (iOS 26, iPhone + iPad) mimo repozitář nebo v `probe/` ignorované gitem. SwiftData model s jedním polem (text, datum), `ModelConfiguration` s `cloudKitDatabase: .private("<kontejner>")`. Capabilities: iCloud → CloudKit, Background Modes → Remote notifications. Obrazovka: seznam, tlačítko přidat, štítek s ID zařízení.

### U1 — spuštění pod dětským iCloudem
Postup: připojit synův iPhone kabelem, v Xcode zvolit zařízení, Run. Pak totéž na iPadu.
Zapsat: spustí se aplikace? Jaké hlášky (podpis, Developer Mode, iCloud)? Je přihlášen dětský iCloud a má volné místo?

### U2 — sync v obou prostředích
a) **Development** (Xcode Run, kontejner v Development): přidat položku na iPhonu, sledovat iPad. Zapsat dobu do objevení, případné chyby účtu.
b) **Production**: v CloudKit Console nasadit schéma testovacího kontejneru do Production (`initializeCloudKitSchema()` v DEBUG, pak Deploy), registrovat UDID obou zařízení, Archive → Distribute → Ad Hoc s CloudKit prostředím Production, nainstalovat z Macu. Opakovat zkoušku.
Zapsat: funguje sync v Development a v Production pod dětským iCloudem? Jak dlouho trvá? Zapnutý Wi-Fi/mobilní data?

### U3 — změna prostředí na témže zařízení
Na zařízení s položkami z Development nainstalovat Production build téže aplikace. Zapsat: zůstanou lokální data? Co vidí zařízení z cloudu? Objeví se položky z Development? (Očekávání z `10` kap. 6: data se mezi prostředími nepřenášejí.)

### T6 — čtení češtiny z fotky (a sonda N8)
Postup: v probe aplikaci vypsat `supportedRecognitionLanguages` (nebo ekvivalent pro `RecognizeDocumentsRequest`); nafotit/vybrat fotku stránky; spustit rozpoznání s jazyky `[de, cs]` a `[en, cs]` a také bez nastavení jazyka. Vše na zařízení, bez sítě (letadlový režim).
Zapsat: je čeština v seznamu? Přečte se diakritika (á č ď é ě í ň ó ř š ť ú ů ý ž), německé ä ö ü ß? Poznají se dva sloupce „cizí – česky"? Co když je text rukou psaný? Kolik řádků z N se přečetlo správně (přibližně)?

## Zápis výsledků (vyplnit po zkouškách)

| Zkouška | Zařízení | Výsledek (OK / chyba / jinak) | Hláška nebo poznámka | Datum |
|---|---|---|---|---|
| U1 | iPhone | | | |
| U1 | iPad | | | |
| U2 Development | iPhone ↔ iPad | | doba do synchronizace: | |
| U2 Production (Ad Hoc) | iPhone ↔ iPad | | doba do synchronizace: | |
| U3 | iPhone nebo iPad | | | |
| T6 čeština + diakritika | | | | |
| T6 dva sloupce | | | | |
| T6 ručně psané | | | | |

## Co z výsledků plyne
- **U1 nebo U2 selže:** záložní cesty A (TestFlight s dospělým účtem v Média a nákupy) nebo C (nevypsaná aplikace v App Storu), `10` kap. 9. Sync v prvním buildu pak nelze slíbit.
- **U2 OK v obou prostředích:** sync může být v prvním buildu (iterace 5 se přesune dopředu), ADR-14 platí beze změny.
- **U3 ukáže ztrátu lokálních dat:** přechod Development → Production řešit jednorázovým exportem a importem (ADR-14, Q12), nikdy na zařízení se skutečnými daty.
- **T6 nečte češtinu nebo diakritiku:** import musí počítat s ručním zadáním a robustní opravou (US-03). Může se zvážit jiný způsob rozpoznání, ale jen on-device (R5).

## Pokyn pro Claude Code na Macu mini
V naklonovaném repozitáři (`~/word-memo`, větev `claude/friendly-wright-hj1b14`) spusť `claude` a zadej:

> Přečti `CLAUDE.md` a `docs/12-iterace-0-postup.md`. Založ testovací aplikaci WordMemoProbe podle sekce Příprava mimo repozitář (nebo v ignorovaném `probe/`), s vlastním bundle ID a iCloud kontejnerem. Postav ji a spusť v simulátoru. Pak mi krok za krokem řekni, co mám udělat na synových zařízeních u U1, U2, U3 a T6, a zapisuj výsledky do tabulky. Nikdy nenasazuj schéma do Production a neinstaluj na zařízení syna beze mě.

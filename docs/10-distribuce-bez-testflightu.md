# 10 — Distribuce bez TestFlightu (dítě v dětském účtu)

Stav k 2026-11-07. Autor: ověřovatel distribuce. Vstupy: `00-zadani.md` (D5, D6, D10, D15, D17, R1), `08-overeni-platformy.md` (a, b), `05-architektura.md` (A11, kap. 9 ADR-06, kap. 10, N6).
Neměním `00-zadani.md` ani jiné soubory. Změnu D5 a následné úpravy `05`/`08` navrhuji v kap. 7, rozhodne zadavatel.

## Závěr

1. **Nová skutečnost (ověřil uživatel na zařízení 2026-11-07):** dětský účet (Česko, 13 let) TestFlight stáhne, ale TestFlight nejde spustit. Předpoklad D5 „jen TestFlight“ proto neplatí tak, jak byl napsán. V oficiální dokumentaci Apple pro to **není vysvětlení ani pravidlo** (kap. 0). Přesnou hlášku je třeba zapsat (kap. 9).
2. **Nejmenší test hned, bez vývoje, 10 minut, zdarma, vratný (kap. 9):** na synově zařízení přihlásit v *Nastavení → [jméno] → Média a nákupy* dospělý Apple účet (iCloud nechat synův), otevřít už stažený TestFlight a zapsat, zda se spustí. Pak vrátit zpět. Výsledek rozhodne, jestli varianta A vůbec přichází v úvahu.
3. **Varianta A (TestFlight s dospělým účtem v Médiích a nákupech)** je technicky nejmenší změna (žádný nový kód, stále Production CloudKit), ale **oslabuje rodičovskou kontrolu na zařízení** (App Store pak běží pod dospělým účtem) a ponechává 90denní povinnost nového buildu. Zda to na dětském zařízení vůbec jde a zda TestFlight pak naběhne, je **domněnka k ověření** (T-A0).
4. **Varianta B (Ad Hoc přes UDID) a D (přímá instalace z Xcode, Developer Mode)** jsou **oficiálně zdokumentované** a obcházejí TestFlight i App Store úplně (**ověřeno**). Cena: potřebuje se Mac s Xcode, na synově zařízení musí být zapnutý Developer Mode a každá aktualizace znamená fyzické připojení (nebo vlastní OTA hosting). Platnost profilu: oficiální stránky, které jsem otevřel, délku neuvádějí, běžně se udává 1 rok (**domněnka**).
5. **Varianta C (nevypsaná aplikace v App Storu)** je dlouhodobě **nejméně pracná** (žádný 90denní ani roční expirační cyklus, žádné UDID, žádný Developer Mode), ale **vyžaduje schválení App Review a žádost Applu o unlisted odkaz**. Apple výslovně odmítá žádosti u aplikací „v beta nebo prerelease stavu“ (**ověřeno**), takže se nehodí na rané verze. Soukromá rodinná aplikace nepatří mezi Applem uvedené příklady (partnerské nástroje, zaměstnanci, výzkum), schválení proto není jisté (**domněnka**).
6. **iCloud sync (D6) a CloudKit:** ve všech variantách běží synchronizace v synově iCloud účtu a v soukromé databázi. Rozdíl je v **prostředí**: TestFlight a App Store používají jen Production (**ověřeno**, archivní stránka Apple), Ad Hoc export umožňuje zvolit Development nebo Production (**ověřeno**, tamtéž), přímý běh z Xcode Development nebo Production. **Data se mezi Development a Production nekopírují** (kopíruje se jen schéma, **ověřeno**), takže přechod mezi způsoby distribuce může znamenat ztrátu dat v cloudu, dokud se nepoužívá stejné prostředí.
7. **Doporučené pořadí:** (1) test A bez vývoje (10 min), (2) souběžně nebo místo něj „hello CloudKit“ přes přímou instalaci z Xcode na synovo zařízení (varianta D, ½ dne), (3) pro vývoj používat D/B s prostředím Development, (4) pro dlouhodobý provoz rozhodnout mezi A (pokud test prošel a rodiče přijmou oslabení kontroly) a C (pokud rodiče chtějí bez údržby a přijmou App Review).
8. **Co nedoporučuji:** měnit datum narození dítěte nebo jinak obcházet věkové hranice a omezení účtu (kap. 8). Použití dospělého účtu v Médiích a nákupech (A) je legitimní nastavení rodiče, ale je to **volba s dopady na kontrolu**, ne „trik“; je v textu popsána včetně dopadů.
9. **Architektura (`05`):** kód doménové vrstvy, datový model a hranice modulů se **nemění**. Mění se kap. 10 (distribuce a provoz), A11, N6 a pořadí prací kolem CloudKit prostředí a schématu (kap. 7).

**Omezení tohoto ověření:** stránky `support.apple.com` (Family Sharing, Ask to Buy, Média a nákupy, Apple Configurator) jsou z této sítě nedostupné (chyba překladu názvu hostitele). Tvrzení o dětských účtech a nastavení zařízení jsou tedy buď **domněnka**, nebo **ověřeno jen z výňatku vyhledávání** (výslovně označeno). Dále nedostupný `loopkit.github.io`. Stránky `developer.apple.com` byly dostupné; několik z nich je vykreslováno dynamicky a vrátilo jen kostru, proto jsem u nich použil verzi `.md` nebo to uvádím jako neověřené.

Legenda: **ověřeno (URL)** = potvrzeno textem stránky Apple, kterou jsem v této session otevřel. **domněnka** = logický závěr, zkušenost, neoficiální zdroj nebo výňatek vyhledávání. **k ověření na zařízení** = nutný reálný test.

---

## 0. Co víme o problému (a co ne)

| Tvrzení | Stav |
|---|---|
| Dětský účet (13 let, CZ) stáhne aplikaci TestFlight, ale TestFlight nejde spustit. | Zjištění uživatele na zařízení, 2026-11-07. Přesná hláška **není zapsaná**. |
| Dokumentace TestFlightu a App Store Connect Help neuvádí věkovou hranici testera ani pravidla pro dětský účet. | **ověřeno, že chybí** (https://developer.apple.com/help/app-store-connect/test-a-beta-version/testflight-overview, https://developer.apple.com/help/app-store-connect/reference/testflight/testflight-tester-information) |
| Apple potvrzuje jen: e-mail pozvánky „may or may not be the same as the Apple Account associated with their device“. | **ověřeno** (https://developer.apple.com/help/app-store-connect/reference/testflight/testflight-tester-information) |
| Uživatel (14 let, rodinná skupina) hlásí při stažení TestFlightu hlášku „you have to be at least 13“; ve vláknu je jediná odpověď (od uživatele, ne Applu) a řešení není. | **domněnka** (neoficiální, https://developer.apple.com/forums/thread/703620). Ukazuje, že se **hlášky o věku v TestFlightu vyskytují**, ne co je jejich příčinou. |
| Zda je příčinou věk účtu, režim dětského účtu (Family Sharing), omezení Screen Time, nebo něco jiného. | **domněnka**, bez důkazu. Přesná hláška a test A0 (kap. 9) to zúží. |
| DTS inženýr Apple (vlákno o hodinkách v dětském nastavení): TestFlight na rodičovském iPhonu nefunguje pro dětské hodinky, protože nesdílejí stejné Apple ID. Jen „pretty sure“, jiný případ (watchOS). | **ověřeno, že odpověď existuje** (https://developer.apple.com/forums/thread/848247). Pro iPhone dítěte **neplatí přímo**. |

---

## 1. Přehled variant

| | A: TestFlight, dospělý účet v „Média a nákupy“ | B: Ad Hoc (UDID) | C: Unlisted v App Storu | D: přímá instalace z Xcode (Developer Mode) |
|---|---|---|---|---|
| Potřebuje Mac a Xcode | ano (nahrání buildu) | ano | ano (nahrání buildu) | ano |
| Potřebuje App Review | TestFlight App Review (externí) | ne | **ano** + žádost o unlisted | ne |
| Zásah do synova zařízení | změna účtu v Médiích a nákupech | Developer Mode, instalace .ipa | žádný (jen Ask to Buy schválení) | Developer Mode, kabel/pairing |
| Údržba | nový build ≤ 90 dní (**ověřeno**) | profil ~1 rok (**domněnka**), update = Mac | update = App Review | profil ~1 rok (**domněnka**) |
| CloudKit prostředí | Production | Development nebo Production (volba při exportu) | Production | Development nebo Production |
| Náklady navíc (orientačně) | 0 Kč | 0 Kč (Mac a program už jsou) | 0 Kč | 0 Kč |
| Hlavní riziko | oslabení kontroly, nejistý start TestFlightu | Developer Mode na dětském zařízení, ruční aktualizace | zamítnutí / nejisté schválení | stejné jako B |
| Vhodné na | produkční provoz, pokud test A0 projde | vývoj a dlouhodobé zálohy | dlouhodobý provoz bez údržby | rané ladění na synově zařízení |

Náklady: členství v Apple Developer Program je 99 USD ročně nebo v místní měně, cena v Kč se zobrazí při zápisu (**ověřeno**, https://developer.apple.com/programs/ jen z výňatku vyhledávání; částku v Kč jsem nenašel). Orientačně 2 100–2 400 Kč (**domněnka**, kurz). D5 říká, že účet je k dispozici, takže **další náklad je 0 Kč**. Případný USB kabel nebo adaptér řádově stovky Kč (**domněnka**).

---

## 2. Varianta A: TestFlight, dospělý Apple účet v „Média a nákupy“, iCloud zůstane synův

### Postup
1. Na synově zařízení: *Nastavení → [jméno syna] → Média a nákupy → Odhlásit se*, pak přihlásit dospělý účet (otcův, nebo lépe samostatný dospělý účet bez platební metody). **iCloud (horní řádek Nastavení) se nesmí odhlásit.** Přesná cesta v iOS 26: **domněnka** (support.apple.com nedostupné).
2. Pozvat tento dospělý účet jako externího testera, nejlépe **veřejným odkazem** (e-mail pozvánky se nemusí shodovat s účtem na zařízení, **ověřeno**, https://developer.apple.com/help/app-store-connect/reference/testflight/testflight-tester-information). Pozvánku otevřít na synově zařízení.
3. Instalace a běh jako v `08` (a): externí skupina, první build projde TestFlight App Review (**ověřeno** jako postup, https://developer.apple.com/help/app-store-connect/test-a-beta-version/invite-external-testers).

### Co se na zařízení změní (vše kromě prvního řádku je domněnka k ověření, support.apple.com nedostupné)
| Oblast | Dopad |
|---|---|
| Apple účet pro Média a nákupy | Apple uvádí, že Média a nákupy a účet Family Sharing mají být stejné, jinak se **sdílený obsah rodiny neukáže**; lze nastavit „Share using secondary Apple Account“ (**ověřeno jen z výňatku vyhledávání**, https://support.apple.com/HT201454). |
| App Store: nové stažení | Běží pod dospělým účtem. **Ask to Buy (žádost o koupi) se na toto zařízení pro App Store nejspíš přestane uplatňovat** (**domněnka**). Syn by mohl stahovat aplikace bez schválení. |
| Věková omezení aplikací | Nastavuje Screen Time (Obsah a soukromí) u synova zařízení; dopad změny M&P účtu není doložen (**domněnka**: omezení zůstanou vázána na zařízení a účet v rodině). **k ověření na zařízení** |
| Screen Time | **domněnka**: vázán na iCloud/rodinný účet dítěte, ne na Média a nákupy. Ověřit po změně, zda omezení zůstala. |
| Family Sharing | Členství je vázáno na hlavní Apple účet (iCloud), **domněnka**. Sdílené nákupy rodiny (cizí aplikace, hudba) se mohou přestat nabízet (viz výše, **ověřeno jen z výňatku**). |
| Nákupy a platby | Pokud má dospělý účet platební metodu, je dostupná na synově zařízení. Proti náhodným nákupům: Screen Time → Nákupy v aplikacích „Nepovolit“ a vyžadovat heslo (**domněnka**). Proto samostatný dospělý účet **bez platební metody**. |
| Již stažené aplikace | Komunitní hlášení: aplikace stažené pod jiným ID mohou vyžadovat toto ID pro aktualizace (**domněnka**, výňatek komunitního vlákna https://discussions.apple.com/thread/254568066). |
| Apple hudba, předplatná | Mohou se na zařízení přestat nabízet (**domněnka**). |

### Jak vrátit zpět
*Média a nákupy → Odhlásit se → přihlásit účet dítěte (rodinný)* (**ověřeno jen z výňatku vyhledávání**, https://support.apple.com/HT201454: „Sign out of Media & Purchases … sign in with the Apple Account that you use for Family Sharing“). Po návratu dětský účet znovu spustit s Ask to Buy. Zda se nainstalovaný TestFlight build po návratu dál spouští a zda se dá aktualizovat bez dospělého účtu: **domněnka**, nejspíš ne aktualizovat (TestFlight bude opět nespustitelný). **k ověření (T-A1)**.

### Vliv na iCloud / CloudKit (D6)
- CloudKit používá iCloud účet zařízení, ne účet Médií a nákupů (**domněnka**, `08` (a); Apple to nikde výslovně nepíše). Pokud M&P ≠ iCloud, sync běží v synově soukromé databázi, žádná změna v kódu.
- TestFlight build používá vždy **Production** CloudKit („apps distributed via TestFlight or the store can't use the development environment“, **ověřeno**, https://developer.apple.com/library/content/documentation/DataManagement/Conceptual/CloudKitQuickStart/TestingYourApp/TestingYourApp.html, archivní stránka). Schéma musí být nasazeno **před prvním buildem** (**ověřeno** jako postup, https://developer.apple.com/documentation/cloudkit/deploying-an-icloud-container-s-schema).
- Pokud se po instalaci vrátí M&P na dětský účet, sync iCloudu to neovlivní (**domněnka**).

### Údržba
Nový build nejpozději každých 90 dní (**ověřeno**, https://developer.apple.com/testflight/). Při každé aktualizaci musí být na zařízení dospělý účet v M&P (pokud TestFlight pod dětským účtem dál nejde spustit; **domněnka**).

### Rizika
- Oslabení kontroly dítěte (viz tabulka). Je to rozhodnutí rodiče, ne technická nutnost.
- Nejistý start TestFlightu (příčina původního selhání není známa).
- Ztráta sdíleného rodinného obsahu na zařízení (nízký dopad, vratné).
- Apple podmínky (domněnka): dospělý účet na cizím zařízení v rodině je běžné rodičovské nastavení, nezjistil jsem v dokumentaci zákaz.

### Dopad na architekturu
Žádný kód. Mění se jen provoz (kap. 10 v `05`): musí platit „Brána 0: T2“ s tímto postupem.

---

## 3. Varianta B: Ad Hoc distribuce přes UDID

### Postup
1. **Registrace zařízení** (Account Holder nebo Admin): v *Certificates, Identifiers & Profiles → Devices → +* zadat platformu, název a UDID (**ověřeno**, https://developer.apple.com/help/account/register-devices/register-a-single-device). UDID z Device Hub nebo Finderu (**ověřeno**, https://developer.apple.com/documentation/xcode/distributing-your-app-to-registered-devices). Při automatickém podepisování Xcode zařízení zaregistruje samo (**ověřeno**, tamtéž).
2. **Ad Hoc profil:** potřeba App ID, jeden distribuční certifikát a více zaregistrovaných zařízení; profil se vybere jen na podmnožinu zařízení (**ověřeno**, https://developer.apple.com/help/account/provisioning-profiles/create-an-ad-hoc-provisioning-profile/). S automatickým podepisováním ho spravuje Xcode (**ověřeno**, tamtéž).
3. **Archiv a export:** *Product → Archive → Distribute App*; buď „Release Testing“ (podepíše automaticky podobně jako App Store a exportuje pro zařízení zaregistrovaná v App Store Connect), nebo *Custom → Ad Hoc* (**ověřeno**, https://developer.apple.com/documentation/xcode/distributing-your-app-for-beta-testing-and-releases). Testeři mohou aplikaci spustit jen na zařízeních v profilu (**ověřeno**, https://developer.apple.com/documentation/xcode/distributing-your-app-to-registered-devices).
4. **Doručení:**
   - **Device Hub (Xcode) nebo Apple Configurator** (Mac, kabel): přidat .ipa v Apps inspectoru (**ověřeno**, https://developer.apple.com/documentation/xcode/managing-apps-on-devices).
   - **OTA:** při Ad Hoc exportu je volba „Include manifest for over-the-air installation“ (**ověřeno**, https://developer.apple.com/documentation/xcode/distributing-your-app-for-beta-testing-and-releases). Vyžaduje vlastní hosting .ipa a manifestu přes HTTPS a odkaz `itms-services://` (**domněnka**, z obecných znalostí). Pro rodinný provoz nedoporučuji (hosting, odkaz veřejně dostupný).
5. **Developer Mode** musí být na zařízení zapnutý, aby se aplikace z .ipa spustila (**ověřeno**, https://developer.apple.com/documentation/xcode/distributing-your-app-to-registered-devices). Zapne se v *Soukromí a zabezpečení → Developer Mode*, vyžaduje restart a potvrzení kódem; **v Nastavení se položka objeví, jen když bylo zahájeno párování s Macem** (**ověřeno**, https://developer.apple.com/documentation/xcode/enabling-developer-mode-on-a-device). Developer Mode „neovlivňuje běžnou instalaci z App Storu ani účast v TestFlightu“ (tamtéž) a snižuje bezpečnost zařízení („reduces the security of your device“, tamtéž).
6. **První spuštění vyžaduje internet:** development- a ad hoc-podepsané aplikace pro týmy vytvořené po 2021-06-06 se při prvním spuštění přihlašují službě PPQ (**ověřeno**, https://developer.apple.com/help/account/provisioning-profiles/provisioning-profile-updates/).

### Limit a platnost
- **Limit zařízení:** 100 na kategorii zařízení (iPhone, iPad…) a členský rok (**ověřeno**, https://developer.apple.com/help/account/devices/devices-overview/). Vypnutí zařízení slot neuvolní; seznam se obnoví při prvním přihlášení v novém členském roce (**ověřeno**, tamtéž). Pro naše 2–3 zařízení (iPhone, iPad syna, případně otec) stačí s velkou rezervou.
- **Platnost Ad Hoc profilu:** stránky Apple, které jsem otevřel, délku neuvádějí (**ověřeno, že chybí**: https://developer.apple.com/help/account/provisioning-profiles/create-an-ad-hoc-provisioning-profile/, https://developer.apple.com/help/account/provisioning-profiles/edit-download-or-delete-profiles/). Z komunity: maximálně 1 rok a nejdéle do konce členství (**domněnka**, https://developer.apple.com/forums/thread/115245, odpověď není od Applu). Po expiraci se profil musí obnovit (Edit → Generate), odebrat ze zařízení a aplikaci znovu podepsat a rozdat (**ověřeno**, https://developer.apple.com/help/account/provisioning-profiles/edit-download-or-delete-profiles/).
- **Platnost členství:** při propadnutí členství se zařízení odeberou po 180 dnech (**ověřeno**, https://developer.apple.com/help/account/devices/devices-overview/).
- Apple nabízí i offline profil platný 7 dní (**ověřeno**, https://developer.apple.com/help/account/provisioning-profiles/provisioning-profile-updates/). Pro nás irelevantní.

### Prostředí CloudKit
**Ad Hoc export: při exportu se volí Development nebo Production** (**ověřeno**, https://developer.apple.com/library/content/documentation/DataManagement/Conceptual/CloudKitQuickStart/TestingYourApp/TestingYourApp.html, archivní stránka; potvrzení pro Xcode 27 beta pouze z fóra, **domněnka**, https://developer.apple.com/forums/thread/842909). Pokud schéma není nasazeno do Production, zvolit Development (**ověřeno**, tamtéž).
- Production: data synova účtu jsou v produkční DB, **stejné jako později v TestFlightu/App Storu** (výhoda). Schéma musí být předem nasazeno a je od té chvíle jen aditivní (**ověřeno**, `08` (b)).
- Development: schéma je volné, jde ho resetovat (**ověřeno**, https://developer.apple.com/documentation/cloudkit/deploying-an-icloud-container-s-schema), ale **data se do Production nepřenesou** (kopíruje se jen schéma, **ověřeno**, tamtéž).
- **Dopad na D6:** sync mezi iPhonem a iPadem funguje i v Development, pokud mají obě zařízení stejný iCloud účet (**domněnka**, k ověření U2). Zda účet bez vývojářských práv (dětský iCloud) smí používat Development prostředí build podepsaný vývojářským týmem: **domněnka k ověření na zařízení** (U2). Na to se spoléhá celé „hello CloudKit“ v kap. 9.

### Údržba
- Aktualizace aplikace = nový export a fyzické (nebo OTA) nasazení.
- Profil/certifikát jednou ročně (**domněnka**).
- Apple Configurator je jen Mac (**domněnka**).

### Rizika
- **Developer Mode** na synově zařízení (snížení bezpečnosti, tlak na dítě „co je to za režim“). Vypnutí Developer Mode po instalaci pravděpodobně znemožní spuštění aplikací z .ipa (**domněnka**; Apple píše, že po vypnutí „nelze spouštět aplikace z Xcode“, **ověřeno**, https://developer.apple.com/documentation/xcode/enabling-developer-mode-on-a-device).
- Expirace profilu bez upozornění. Doporučení: připomínka v kalendáři na 11 měsíců po vytvoření.
- Ztráta Macu/certifikátu: nelze vydat aktualizaci stejným podpisem (**domněnka**).

### Dopad na architekturu
Žádný kód. Mění se kap. 10 `05` a T3 (CloudKit prostředí). Entitlement `com.apple.developer.icloud-container-environment` má hodnoty Development/Production (**ověřeno**, https://developer.apple.com/documentation/bundleresources/entitlements/com.apple.developer.icloud-container-environment).

---

## 4. Varianta C: nevypsaná aplikace (Unlisted App Distribution)

### Co to je (**ověřeno**, https://developer.apple.com/support/unlisted-app-distribution)
- „Discoverable only with a direct link.“ Neobjeví se v kategoriích, doporučeních, žebříčcích ani vyhledávání.
- Cíl: „aplikace, které nejsou vhodné pro veřejnou distribuci“, příklady: partnerské prodejní nástroje, zdroje pro zaměstnance, výzkumné studie. **Rodinná výuková aplikace v seznamu příkladů není** (proto riziko zamítnutí, **domněnka**).
- Dostupná „anyone who has access to the link“; Apple doporučuje případně vlastní ochranu proti neoprávněnému použití (tamtéž).

### Postup
1. Aplikace musí být už v App Storu, nebo **připravená k finální distribuci a odeslaná do App Review**; do Review Notes uvést, že jde o unlisted distribuci (**ověřeno**, tamtéž). Žádost se **zamítne**, pokud aplikace nebyla odeslána do App Review nebo je v beta/prerelease stavu (**ověřeno**, tamtéž).
2. Distribuce musí být v *Pricing and Availability → App Distribution Methods* nastavena na **Public**; po odeslání do Review se podá formulář (https://developer.apple.com/contact/request/unlisted-app/) (**ověřeno**, https://developer.apple.com/help/app-store-connect/manage-your-apps-availability/set-distribution-methods). Role: Account Holder, Admin nebo App Manager (tamtéž). Formulář smí podat jen vlastník účtu (**domněnka**, třetí strany).
3. Po schválení se distribuční metoda změní na Unlisted a vygeneruje se odkaz (**ověřeno**, tamtéž). „Once your app is approved, the distribution method can't be changed. However, you can change a publicly available app to an unlisted app“ (**ověřeno**, https://developer.apple.com/help/app-store-connect/manage-your-apps-availability/set-distribution-methods). Praktický závěr: **nastavit ruční vydání verze**, aby aplikace po schválení nebyla ani na chvíli veřejně vyhledatelná (**domněnka**: nastavení ručního vydání je standardní volba verze v App Store Connectu, v otevřených stránkách jsem ji neověřoval).

### Požadavky App Review a metadat
- **Věkové hodnocení** je povinná vlastnost, bez ní nejde vydat („An Unrated app can't be published on the App Store“, **ověřeno**, https://developer.apple.com/help/app-store-connect/reference/app-information/age-ratings-values-and-definitions/ a https://developer.apple.com/help/app-store-connect/manage-app-information/set-an-app-age-rating/). Nastavuje se dotazníkem; pro naši aplikaci (bez obsahu, bez chatu, bez webu) očekávám 4+ (**domněnka**).
- **Zásady ochrany soukromí:** URL je podle stránky o App Privacy „povinné pro všechny aplikace na iOS“ a odpovědi App Privacy jsou potřeba pro App Store (`08` (e), **ověřeno**, https://developer.apple.com/help/app-store-connect/manage-app-information/manage-app-privacy). Žádný sběr dat: „No, we do not collect data from this app“ (tamtéž).
- **Kids kategorie není nutná** a pro 13 let se nehodí (`08` (e), **ověřeno**).
- Aplikace musí splnit Guidelines v plném rozsahu (neobvyklé funkce jako omezené mini-aplikace jsou riziko, **domněnka**, Guideline 4.2 jsem neotevřel).
- Metadata (popis, snímky obrazovky, URL podpory) jsou potřeba jako pro každou aplikaci v App Storu (**domněnka**, https://developer.apple.com/help/app-store-connect/reference/required-localizable-and-editable-properties jsem neotevřel).
- **Schéma CloudKit musí být v Production před odesláním** („Apps in the App Store can access only the production environment“, **ověřeno**, https://developer.apple.com/documentation/cloudkit/deploying-an-icloud-container-s-schema).

### Zda aplikaci dětský účet nainstaluje a zda je třeba schválení rodiče
- Dětský účet má App Store k dispozici a otevření odkazu vede na stránku aplikace v App Storu (**domněnka**).
- **Ask to Buy se vztahuje i na bezplatné stažení**: dítě vyvolá žádost, organizátor rodiny schválí (**ověřeno jen z výňatku vyhledávání / komunitní odpověď**, https://support.apple.com/105055 a https://discussions.apple.com/thread/6551423). Pouze pro zdarma aplikace vypnout nejde (**ověřeno jen z komunitní odpovědi**). Schválení tedy dělá otec, jednorázově při instalaci. Stažení aktualizací žádost nevyvolává (**ověřeno jen z výňatku vyhledávání**, starší znění Apple stránky).
- Věkové hodnocení aplikace (4+) musí odpovídat limitu v Screen Time pro dítě (**domněnka**). U 4+ by neměl být problém.
- **Zda dětský účet unlisted aplikaci reálně nainstaluje: k ověření na zařízení** (T-C1; testuje se až s reálným schváleným buildem, nejde dříve).

### Náklady a údržba
- Náklad 0 Kč navíc (program už je). Bez 90denní a bez roční expirace buildu; build nevyprší (**domněnka**, standardní App Store aplikace, v dokumentaci unlisted výslovně neuvedeno).
- Každá aktualizace prochází App Review. Doba review není v dokumentaci, běžně dny (**domněnka**).

### Vliv na iCloud / CloudKit (D6)
Production prostředí, soukromá DB synova účtu. Žádný zásah do účtu. Nejstabilnější varianta pro D6 (**domněnka**, odpovídá standardnímu App Storu).

### Rizika
- **Zamítnutí žádosti o unlisted** (rodinná aplikace není příklad Applu). Pak zbyde veřejné vydání (nechceme) nebo B/D.
- Odkaz dostupný komukoli: aplikace nemá žádný účet ani obsah třetích osob, riziko je nízké, ale aplikace pak může stahovat kdokoli (**domněnka**).
- Veřejná aplikace přináší povinnosti vývojáře (zásady, podpora). Dětská data jsou jen lokálně a v soukromém iCloudu syna.
- Použití pro rané verze nepřichází v úvahu (žádosti v beta stavu Apple zamítá).

### Dopad na architekturu
Kód se nemění. Platí všechna omezení `05` pro Production (schéma jen aditivní, `schemaVersion`). Přibývá práce na metadatech a zásadách ochrany soukromí (`05` kap. 10, T7).

---

## 5. Varianta D: ostatní možnosti

### D1. Přímá instalace z Xcode na synovo zařízení (Developer Mode)
- **Postup:** Mac s Xcode, spárování synova zařízení kabelem nebo bezdrátově přes Device Hub; zapnout Developer Mode; v Signing & Capabilities zapnout automatické podepisování, vybrat zařízení, Run; Xcode zařízení zaregistruje a přidá do profilu (**ověřeno**, https://developer.apple.com/documentation/xcode/distributing-your-app-to-registered-devices a https://developer.apple.com/documentation/xcode/enabling-developer-mode-on-a-device).
- **Platnost:** s placeným členstvím profil obvykle 1 rok (**domněnka**; stránky Apple, které jsem otevřel, délku pro placené členství neuvádějí). **Personal Team (bez členství): 7 dní, 3 zařízení, 3 aplikace na zařízení** (**ověřeno**, https://developer.apple.com/support/compare-memberships/). Zda free účet umožní iCloud/CloudKit capability: **domněnka**, že ne (Apple: pokročilé schopnosti vyžadují členství, **ověřeno** jen obecně na téže stránce). My máme placený účet, Personal Team nepoužívat.
- **CloudKit:** běh z Xcode může používat Development nebo Production (**ověřeno**, archivní stránka Apple, tabulka níže).
- **Rizika a údržba** shodná s B (Developer Mode, fyzická aktualizace). Rozdíl od B: je to debug-sestavení, nehodí se na dlouhodobý provoz; pro ladění je to nejrychlejší cesta.

### D2. Custom Apps (Apple Business / School Manager)
- Aplikace dostupná „only on Apple Business or Apple School Manager to specific businesses and organizations“ (**ověřeno**, https://developer.apple.com/help/app-store-connect/manage-your-apps-availability/set-distribution-methods). Určena pro organizace, ne pro rodinu. **Pro dětský účet Family Sharing nepoužitelné** (**domněnka**, vyžaduje členství v organizaci v ABM/ASM a spravované zařízení či účet). Nedoporučuji.

### D3. Apple Developer Enterprise Program
- Určen pro interní aplikace zaměstnanců organizace (**ověřeno**, https://developer.apple.com/business/distribute/). Pro rodinu nevhodné a v rozporu s účelem (**domněnka**). Nedoporučuji.

### D4. Veřejné vydání v App Storu
- Technicky nejjednodušší (stejné požadavky jako C bez žádosti o odkaz), ale aplikace je veřejná. **Není to cíl D5**. Uvádím jen jako krajní variantu pro zadavatele.

---

## 6. CloudKit prostředí podle způsobu distribuce (D6)

| Způsob | Prostředí | Zdroj |
|---|---|---|
| Xcode Run na testovacím zařízení | Development nebo Production (podle entitlementu) | **ověřeno** (tabulka v https://developer.apple.com/library/content/documentation/DataManagement/Conceptual/CloudKitQuickStart/TestingYourApp/TestingYourApp.html, archivní) |
| Ad Hoc export | volba při exportu; Development, pokud schéma není v Production | **ověřeno** (tamtéž) |
| TestFlight | **jen Production** | **ověřeno** (tamtéž: „apps distributed via TestFlight or the store can't use the development environment“) |
| App Store včetně Unlisted | **jen Production** | **ověřeno** (https://developer.apple.com/documentation/cloudkit/deploying-an-icloud-container-s-schema: „Apps in the App Store can access only the production environment“) |

Důsledky pro nás:
1. **Dvě oddělená úložiště.** Development a Production jsou samostatné databáze, nasazení schématu kopíruje jen strukturu, ne záznamy (**ověřeno**, https://developer.apple.com/documentation/cloudkit/deploying-an-icloud-container-s-schema). Syn, který by měsíc používal Development build a pak přešel na TestFlight/Unlisted (Production), **nepřenese data přes iCloud**. Přenos by musel jít jednorázovým exportem a importem (aplikace to z `05` kap. 10 / N6 už zvažuje, **domněnka**).
2. **Schéma ve Production je nevratné** (**ověřeno**, `08` (b)). Proto je rozumné během vývoje používat **Development** (lze resetovat, **ověřeno**) a do Production nasadit až stabilní schéma.
3. **Pravidlo:** po přechodu na dlouhodobý provoz (A nebo C) používat jen Production a nemíchat s Development buildy na témže zařízení. Co se stane s místním SwiftData úložištěm, když se na témže zařízení změní prostředí (build Development → Production při zachování lokálních dat), nevím: **domněnka k ověření** (U3, příbuzné N6).
4. **`initializeCloudKitSchema()`** pro SwiftData se používá v Development (**ověřeno**, `08` (b)). Při Ad Hoc/Xcode vývoji se schéma vytváří přirozeně, nasazení do Production zůstává ruční krok v CloudKit Console.
5. **Účet iCloud:** ve všech variantách je podstatný **iCloud účet na zařízení**, ne účet Médií a nákupů nebo TestFlight (**domněnka**, `08`). Dětský iCloud musí být přihlášený a mít místo (`08` (a)).

---

## 7. Dopad na zadání a architekturu (návrhy, nerozhoduji)

| Soubor | Co se mění |
|---|---|
| `00-zadani.md` D5 | „Distribuce jen přes TestFlight“ neplatí beze změny. Zadavatel rozhodne o nové formulaci (např. „TestFlight s dospělým účtem v M&P, jinak Ad Hoc/Xcode pro vývoj a Unlisted pro provoz“). R1 je ve skutečnosti už zčásti zodpovězeno: dětský účet TestFlight nespustí. |
| `05` A11 | „Distribuce jen TestFlight, build vyprší za 90 dní“ nahradit tabulkou variant z kap. 1 tohoto dokumentu (údržba podle varianty). |
| `05` kap. 10 | **Brána 0** přepsat: T1/T2 se nahrazují testy A0 (kap. 9) a U1/U2. **Před 1. externím buildem** platí jen u A. U B/D se schéma nasazuje do Production až před přechodem na provoz. U C před odesláním do Review. |
| `05` kap. 10, N6 | „Build 1 bez syncu“ už nemusí platit. Pokud se vývoj dělá přes Xcode/Ad Hoc v Development, bude sync funkční od začátku (po ověření U2). Pozor na prostředí (kap. 6 tohoto dokumentu). |
| `05` ADR-06 | Doplnit: během vývoje Development, schéma do Production až stabilní; aditivní pravidlo platí až od Production. |
| `05` kap. 11 | Přidat sondy U1–U3 (kap. 10 níže). |
| `08` (a) „Plán B“ | je naše varianta A, doplnit důsledky z kap. 2 tohoto dokumentu. „Plán C“ je naše B/D a je nyní **doložen oficiální dokumentací**. |
| `08` (b) | Tvrzení „TestFlight používá Production“ změnit z domněnky na **ověřeno** (archivní stránka Apple, citace v kap. 6). |

Kód doménové vrstvy (`Domain`), model (`Attempt`, `ReviewState`…) a hranice modulů: **beze změny**. Rozhodnutí o distribuci ovlivňuje jen entitlements/profil, CloudKit prostředí a provozní postup.

---

## 8. Co nedoporučuji

- **Měnit datum narození dítěte** (nebo zakládat účet s nepravdivým věkem) pro odblokování TestFlightu. Jde o obcházení věkových pravidel Applu, může porušit podmínky služby a změnit celou sadu ochran dětského účtu (Ask to Buy, obsahové limity). Nedoporučuji, ani jako „test“.
- Přihlašovat otcův **hlavní** Apple účet (s platební metodou a historií) do Médií a nákupů na synově zařízení. Pokud varianta A, tak samostatný dospělý účet bez platební metody a s omezením nákupů (**domněnka**, kap. 2).
- OTA hosting .ipa na veřejné adrese pro rodinné použití (kap. 3).
- Enterprise program a Custom Apps (kap. 5).

---

## 9. Doporučené pořadí zkoušení a nejmenší test

### Pořadí
1. **T-A0 (10 minut, bez vývoje, zdarma, vratný):** viz níže. Zároveň zapsat přesnou hlášku původního selhání.
2. **U1: „hello CloudKit“ přes Xcode na synovo zařízení (varianta D)**, pokud je k dispozici Mac s Xcode a placeným účtem (½ dne). Ověří Developer Mode na dětském zařízení, instalaci a sync mezi iPhonem a iPadem v synově iCloudu. Je to zároveň záložní cesta, pokud A0 selže.
3. **Vývoj:** D/B s prostředím **Development** (kap. 6).
4. **Rozhodnutí o provozu** (zadavatel): A (pokud A0 prošel a rodiče přijmou dopady), nebo C (bez údržby, nutný App Review a žádost o unlisted), nebo zůstat u B (Ad Hoc, údržba ročně).
5. **U C:** podat žádost o unlisted až s hotovou první verzí (ne beta stav).

### Nejmenší test pro uživatele (T-A0), krok za krokem
Čas: ~10 minut. Cena: 0 Kč. Nic se nevyvíjí, nepotřebuje se Mac. Potřebuje se: synovo zařízení, kód Screen Time (pokud je nastaven), dospělý Apple účet (heslo).

1. **Zapsat výchozí stav:** screenshot hlášky při pokusu otevřít TestFlight; verze iOS; jaký účet je přihlášen v *Nastavení → [jméno]* a v *Média a nákupy*. (Hláška je to nejcennější: odliší věkovou hranici od jiné příčiny.)
2. *Nastavení → [jméno syna] → Média a nákupy → Odhlásit se.* Pokud je položka zablokována, zapsat to (pak brání Screen Time → Obsah a soukromí → Změny účtu; po dočasném povolení pokračovat).
3. Přihlásit **dospělý Apple účet** (nejlépe samostatný, bez platební metody). **iCloud ani horní řádek „[jméno] / Apple účet“ se neodhlašuje.**
4. Otevřít TestFlight (už stažený). Zapsat výsledek:
   - (a) **spustí se** a nabídne uvítání/podmínky: A prochází prvním krokem (pak další test s reálným buildem, T-A1).
   - (b) **hláška o věku/účtu/stejná chyba:** A nepomůže, jít na D/B (U1) a C.
   - (c) jiná chyba: zapsat text, nehádat.
5. Zkontrolovat, že se iCloud nezměnil: *Nastavení → [jméno] → iCloud* (stále synův, bez změn).
6. **Vrátit zpět:** *Média a nákupy → Odhlásit se → přihlásit účet dítěte (rodinný)* (**ověřeno jen z výňatku vyhledávání**, https://support.apple.com/HT201454). Zkontrolovat, že App Store opět žádá o schválení stažení (Ask to Buy) a že rodinné sdílení nabízí sdílený obsah.
7. Zapsat: co se po kroku 3 na zařízení změnilo (App Store, Screen Time omezení, Family Sharing, hudba). Pomůže to doplnit kap. 2.

**Co tento test nerozhodne:** zda se TestFlight **build** nainstaluje a spustí, a zda CloudKit sync poběží v synově iCloudu při jiném M&P účtu. To rozhodne T-A1 (reálný nebo prázdný build s iCloud capability, pozvánka veřejným odkazem, kap. 10).

---

## 10. Nové testy na zařízení

| # | Test | Řeší | Pozn. |
|---|---|---|---|
| T-A0 | TestFlight se spustí, když je v M&P dospělý účet (kap. 9) | A | 10 min, bez vývoje |
| T-A1 | Prázdný/skutečný build přes externí TestFlight s M&P ≠ iCloud: instalace, spuštění, sync iPhone ↔ iPad v synově iCloudu; po návratu M&P na dětský účet: spouští se build dál? Jde aktualizovat? | A, D6 | Production, schéma nasazeno předem (T3 z `08`) |
| U1 | „Hello CloudKit“ (iCloud capability) z Xcode na synův iPhone a iPad: Developer Mode, pairing, spuštění pod dětským iCloudem | D, B | Development prostředí |
| U2 | Sync iPhone ↔ iPad v **Development** a v **Production** (Ad Hoc export) se synovým iCloudem (dětský účet); zapsat hlášky, případně chyby účtu | B, D, D6 | Ověřuje, že dětský iCloud smí v Development prostředí |
| U3 | Co se stane s místními SwiftData daty a s cloudem při změně prostředí na témže zařízení (Development build → Production build) | prostředí, N6 | Příbuzné N6 v `05` |
| T-C1 | Až po schválení: unlisted odkaz na synově zařízení: zobrazení stránky, Ask to Buy žádost, instalace, spuštění | C | Vyžaduje App Review |

---

## 11. Předpoklady, které jsem si musel doplnit

- Otec má **Mac s Xcode** a placený vývojářský účet s právy Account Holder/Admin (D5 říká jen „účet je k dispozici“). Pro B, C i D je Mac nutný (nahrání/podepsání buildu), nemá-li ho, zbývá jen A (a to také potřebuje Mac pro nahrání buildu).
- Syn má iPhone i iPad (D15), oba zapsat do UDID seznamu u B/D (2 sloty).
- Hláška při selhání TestFlightu je neznámá (kap. 0).
- Cena členství 99 USD ročně, převod 21–24 Kč/USD (**domněnka**).

## 12. Zdroje

Oficiální stránky `developer.apple.com`, které jsem v této session otevřel (**ověřeno**):
- https://developer.apple.com/help/app-store-connect/test-a-beta-version/testflight-overview
- https://developer.apple.com/help/app-store-connect/reference/testflight/testflight-tester-information
- https://developer.apple.com/support/unlisted-app-distribution
- https://developer.apple.com/help/app-store-connect/manage-your-apps-availability/set-distribution-methods
- https://developer.apple.com/business/distribute/
- https://developer.apple.com/help/account/register-devices/register-a-single-device
- https://developer.apple.com/help/account/register-devices/register-multiple-devices
- https://developer.apple.com/help/account/devices/devices-overview/
- https://developer.apple.com/help/account/provisioning-profiles/create-an-ad-hoc-provisioning-profile/
- https://developer.apple.com/help/account/provisioning-profiles/provisioning-profile-updates/
- https://developer.apple.com/help/account/provisioning-profiles/edit-download-or-delete-profiles/
- https://developer.apple.com/help/account/certificates/certificates-overview/
- https://developer.apple.com/support/compare-memberships/
- https://developer.apple.com/documentation/xcode/distributing-your-app-to-registered-devices
- https://developer.apple.com/documentation/xcode/distributing-your-app-for-beta-testing-and-releases
- https://developer.apple.com/documentation/xcode/managing-apps-on-devices
- https://developer.apple.com/documentation/xcode/enabling-developer-mode-on-a-device
- https://developer.apple.com/documentation/cloudkit/deploying-an-icloud-container-s-schema
- https://developer.apple.com/documentation/bundleresources/entitlements/com.apple.developer.icloud-container-environment
- https://developer.apple.com/library/content/documentation/DataManagement/Conceptual/CloudKitQuickStart/TestingYourApp/TestingYourApp.html (archivní dokumentace)
- https://developer.apple.com/help/app-store-connect/reference/app-information/age-ratings-values-and-definitions/
- https://developer.apple.com/help/app-store-connect/manage-app-information/set-an-app-age-rating/

Neoficiální (jen důkaz, že hlášení existují): https://developer.apple.com/forums/thread/703620, https://developer.apple.com/forums/thread/848247 (odpověď DTS inženýra, jiný případ), https://developer.apple.com/forums/thread/115245, https://developer.apple.com/forums/thread/842909, https://discussions.apple.com/thread/254568066, https://discussions.apple.com/thread/6551423.

Jen z výňatků vyhledávání (stránky se nepodařilo otevřít, `support.apple.com` nedostupné): https://support.apple.com/HT201454, https://support.apple.com/105055. Cena členství: výňatek z https://developer.apple.com/programs/ (částka v Kč nenalezena).

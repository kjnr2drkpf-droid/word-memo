# Ověření platformy iOS (R1–R5)

Stav k 2026-11-07. Autor: ověřovatel platformy iOS. Zdroj zadání: `docs/00-zadani.md`.

## Závěr

1. **R1 (TestFlight + dětský účet) je největší riziko a z dokumentace nejde rozhodnout.** Oficiální dokumentace Apple nikde neuvádí minimální věk testera ani pravidla pro dětský účet Family Sharing. Neoficiální hlášení z vývojářského fóra mluví o věkové hranici 13 let, ve společném příspěvku i o 16 letech v DE/AT/ES. Syn je v 7. třídě (zhruba 12–13 let) a v Česku je dětský účet do 15 let. Zda ho TestFlight pustí, se **musí vyzkoušet na jeho zařízení dřív, než se začne vyvíjet cokoli dalšího** (test T1 níže).
2. **Pozvánka:** syn nemůže být interní tester (ten musí být uživatel App Store Connect). Použije se **externí tester**: pozvánka e-mailem nebo **veřejný odkaz** otevřený na synově zařízení. E-mail pozvánky se nemusí shodovat s Apple účtem na zařízení (ověřeno).
3. **Jiný účet pro TestFlight než pro iCloud** je technicky pravděpodobné (TestFlight jede na účtu Média a nákupy, CloudKit na iCloud účtu zařízení), ale Apple to nikde výslovně nepopisuje. Je to **domněnka k ověření na zařízení** (T2). Je to zároveň nejschůdnější záložní plán pro R1.
4. **Build v TestFlightu vyprší po 90 dnech** (ověřeno). Je to provozní povinnost: nový build nejpozději každých 90 dní, jinak aplikace přestane fungovat.
5. **R4 (SwiftData + CloudKit):** omezení modelu jsou oficiálně potvrzená (žádné unique, relace volitelné, žádné `deny`). Schéma se po nasazení do produkce **jen rozšiřuje**, nic nejde smazat ani změnit. Model proto navrhnout pečlivě a hned s polem verze. Schéma je nutné před TestFlightem nasadit do Production (domněnka: Apple to výslovně píše jen pro App Store).
6. **Rozpor se zadáním D10/D11:** SwiftData synchronizuje jen do **soukromé databáze** přihlášeného iCloud účtu. Fotky, které „občas vloží otec“ z vlastního zařízení pod jiným Apple ID, se do synova účtu **nedostanou**. Rodičovský pohled (D11) bude potřebovat sdílení (CKShare), které SwiftData neumí (domněnka). Doporučení: otec vkládá fotky **na synově zařízení**; D11 řešit později samostatným návrhem.
7. **R3 (hlasy):** v API existují kvality default / enhanced / premium, enhanced a premium si musí uživatel stáhnout (ověřeno). Hlasy Siri aplikace nedostane. Jejich dostupnost pro de-DE, en-GB, en-US a offline provoz se musí ověřit na zařízení (T5).
8. **Kontrola výslovnosti (R3 část):** SFSpeechRecognizer **neposkytuje hodnocení výslovnosti**, jen přepis s mírou jistoty (ověřeno). Na izolovaná slova bude nespolehlivý. Zůstává P2, jak je v zadání.
9. **R2 (OCR):** seznam jazyků Vision není v dokumentaci, zjišťuje se za běhu. Čeština je pravděpodobná, ale **nedoložená** (T6). Tabulky umí nové `RecognizeDocumentsRequest` (iOS 26+). Ručně psané: bez oficiálního tvrzení.
10. **R5 / Kids:** kategorie Kids je dobrovolná a TestFlightu se netýká (ověřeno, že stránka Kids TestFlight nezmiňuje). Pro TestFlight platí obecná pravidla App Review. Vše běží na zařízení, žádné SDK třetích stran. Zbývá ověřit, co přesně chce Apple u externího TestFlightu (soukromí, URL zásad, T7).

**Omezení tohoto ověření:** v této session byly dostupné jen stránky `developer.apple.com`. Stránky `support.apple.com` a `www.apple.com` se nepodařilo otevřít (síťová politika prostředí). Tvrzení z těchto domén jsou označena **ověřeno jen z výňatku vyhledávání** a je dobré je před rozhodnutím otevřít ručně.

Legenda: **ověřeno (URL)** = potvrzeno textem oficiální stránky Apple. **domněnka** = logický závěr, zkušenost nebo neoficiální zdroj. **k ověření na zařízení** = nutný reálný test, viz seznam testů na konci.

---

## (a) TestFlight a dětský účet Family Sharing (R1)

| Tvrzení | Stav |
|---|---|
| Testeři instalují bezplatnou aplikaci TestFlight a přijmou pozvánku e-mailem nebo veřejným odkazem. | **ověřeno** (https://developer.apple.com/help/app-store-connect/test-a-beta-version/testflight-overview) |
| **Interní testeři**: až 100, musí to být uživatelé App Store Connect s rolí Account Holder, Admin, App Manager, Developer nebo Marketing. Uživatelé mimo účet nejdou do interní skupiny přidat. | **ověřeno** (https://developer.apple.com/help/app-store-connect/test-a-beta-version/add-internal-testers, https://developer.apple.com/testflight/) |
| **Externí testeři**: až 10 000, nemusí být uživateli App Store Connect. První build prochází TestFlight App Review. Interní skupina musí existovat dřív než externí. | **ověřeno** (https://developer.apple.com/help/app-store-connect/test-a-beta-version/invite-external-testers) |
| **Pozvánka**: e-mailem (i import CSV) nebo **veřejným odkazem** (limit 1–10 000 testerů, lze omezit dle verze OS/zařízení, lze kdykoli vypnout). Testeři přes odkaz jsou v App Store Connect anonymní, instalace, relace a pády se ale zobrazují. | **ověřeno** (https://developer.apple.com/help/app-store-connect/test-a-beta-version/invite-external-testers) |
| E-mail, na který byla pozvánka poslána, **se nemusí shodovat** s Apple účtem na zařízení testera. | **ověřeno** (https://developer.apple.com/help/app-store-connect/reference/testflight/testflight-tester-information) |
| Tester může mít build na až 30 zařízeních, build je dostupný 90 dní, pak vyprší. | **ověřeno** (https://developer.apple.com/testflight/, https://developer.apple.com/help/app-store-connect/test-a-beta-version/testflight-overview) |
| Spravované Apple účty z vyhrazených domén (Apple School Manager / Business) nelze k testování použít. Rodinný účet se to netýká. | **ověřeno** (https://developer.apple.com/help/app-store-connect/test-a-beta-version/invite-external-testers) |
| **Věková hranice testera** není v dokumentaci TestFlightu ani v App Store Connect Help uvedena. | **ověřeno, že chybí** (stránky výše; žádná nezmiňuje věk) |
| Uživatelé hlásí hlášku „minimální věk pro beta testování je 13“ i u starších osob; jeden uživatel (z telefonátu s podporou, z druhé ruky) uvádí 13 v USA a 16 v Německu, Rakousku a Španělsku. Žádná odpověď zaměstnance Apple ve vláknech není. | **domněnka** (neoficiální: https://developer.apple.com/forums/thread/675464, https://developer.apple.com/forums/thread/687266) |
| V Česku je hranice pro dětský Apple účet **15 let** (do 15 let musí být účet součástí Family Sharing). Syn proto téměř jistě používá dětský účet. | **ověřeno jen z výňatku vyhledávání** (https://support.apple.com/en-au/125666, https://support.apple.com/en-us/102617; stránku jsem neotevřel) |
| Dětský účet má Ask to Buy, kterou nelze vypnout pro osoby do 18 let podle země; instalace TestFlight a aplikací z něj může vyžadovat schválení rodiče. | **ověřeno jen z výňatku vyhledávání** (https://support.apple.com/en-us/102033). Konkrétní chování u TestFlightu: **k ověření na zařízení** |
| Family Sharing se v Sandboxu a TestFlightu nepodporuje (týká se sdílení nákupů, ne způsobilosti testera). | **domněnka** (odpověď ve fóru: https://developer.apple.com/forums/thread/706205) |
| Zda dětský účet může přijmout pozvánku a spustit z TestFlightu build. | **k ověření na zařízení** (T1). Žádný oficiální zdroj jsem nenašel. |
| Apple účet pro TestFlight (Média a nákupy / App Store) může být jiný než iCloud účet zařízení, který používá CloudKit. | **domněnka** (v dokumentaci jsem nenašel; Apple jen uvádí, že e-mail pozvánky se nemusí shodovat s účtem na zařízení). **k ověření na zařízení** (T2) |
| CloudKit soukromá databáze patří iCloud účtu přihlášenému na zařízení, ne vývojářskému účtu. | **domněnka** (zkušenost; ve fóru https://developer.apple.com/forums/thread/125142 se to řeší jako běžný stav) |

### Nejschůdnější postup pro rodiče

1. **Nejdřív zkouška bez vlastní aplikace (T1).** Nahrát libovolný malý testovací build (např. prázdná aplikace se stejným bundle ID nebo první skutečný build), vytvořit externí skupinu s veřejným odkazem a otevřít odkaz **na synově zařízení** v jeho účtu. Výsledek určí, jestli plán A funguje.
2. **Plán A (pokud dětský účet projde):**
   - Otec je Account Holder/Admin v App Store Connect (vývojářský účet je k dispozici, D5).
   - Vytvořit interní skupinu (jen otec, povinná kvůli externí skupině) a **externí skupinu** s veřejným odkazem, limit 2 testeři (syn, otec).
   - Na synově iPhonu/iPadu nainstalovat TestFlight z App Store (u dětského účtu případně schválit v Ask to Buy), otevřít odkaz, nainstalovat.
   - První externí build projde TestFlight App Review (nejspíš pár hodin až dní, domněnka). Další buildy stejné verze už jen lehčí kontrolou (ověřeno, že „první build obvykle plná kontrola“).
3. **Plán B (pokud TestFlight dětský účet odmítne kvůli věku):** na synově zařízení se v Nastavení přihlásit k **Médiím a nákupům** jiným, dospělým Apple účtem (např. otcovým), zatímco **iCloud zůstane synův**. TestFlight pak běží pod dospělým účtem, CloudKit pod synovým iCloudem. **Domněnka**, vyžaduje test T2. Vedlejší dopady (App Store na jeho zařízení pod otcovým účtem, Ask to Buy, Family Sharing nákupy) se musí rodiče zvážit. Apple poznámka o přesunu nákupů zmiňuje, že sekundární účet pro TestFlight se bere jako zvláštní případ (výňatek z https://support.apple.com/en-us/117294, **jen z vyhledávání**).
4. **Plán C (jen pokud zadavatel změní D5):** instalace jinak než TestFlightem (kabelem z Xcode, Ad Hoc). Nevhodné pro denní provoz, uvedeno jen jako možnost pro rozhodnutí zadavatele. Nic z toho jsem neověřoval.
5. **Provoz:** nový build nejpozději každých 90 dní (ověřeno), jinak aplikace přestane jít spustit. Zapsat do plánu údržby. Veřejný odkaz po instalaci vypnout, aby se nešířil.

### Návrhové dopady
- **iCloud účet na synově zařízení musí být přihlášený** a mít volné místo v iCloudu, jinak sync tiše selže (ve fóru popsaný případ plného iCloudu: https://developer.apple.com/forums/thread/756893, chyba `QUOTA_EXCEEDED`, **domněnka** pro náš případ). Aplikace by měla stav iCloudu zobrazit a nesmí na něm padat.
- Aplikace musí fungovat **plně offline a bez iCloudu** (lokální SwiftData), sync je jen doplněk.

---

## (b) SwiftData + CloudKit (R4)

| Tvrzení | Stav |
|---|---|
| Nutné capability: **iCloud (CloudKit)** s kontejnerem a **Background Modes: Remote notifications**. Vyžaduje vývojářský účet s právy admina. | **ověřeno** (https://developer.apple.com/documentation/swiftdata/syncing-model-data-across-a-persons-devices) |
| SwiftData sync používá `NSPersistentCloudKitContainer`. Kontejner se bere z entitlements (první nalezený) nebo se zadá `ModelConfiguration(cloudKitDatabase: .private("iCloud.…"))`. Lze vypnout `.none`. | **ověřeno** (tamtéž) |
| `@Attribute(.unique)` nelze (změny se synchronizují souběžně). | **ověřeno** (tamtéž) |
| Relace musí být **volitelné**. Inverzi je třeba nastavit explicitně, pokud ji SwiftData nedovodí. Pravidlo mazání `deny` nelze. | **ověřeno** (tamtéž) |
| Všechny atributy musí být volitelné **nebo mít výchozí hodnotu**; relace musí mít inverzi; neřazené (`ordered`) relace nelze; `Undefined` a `NSManagedObjectID` atributy nelze. | **ověřeno** pro Core Data model (https://developer.apple.com/documentation/coredata/creating-a-core-data-model-for-cloudkit). Pro SwiftData platí stejný základ (domněnka: SwiftData je nad ním postavené) |
| Vývojové schéma se inicializuje ručně (`initializeCloudKitSchema()` v `#if DEBUG`) a **před vydáním se musí povýšit do produkce**. | **ověřeno** (https://developer.apple.com/documentation/swiftdata/syncing-model-data-across-a-persons-devices) |
| Aplikace z App Store vidí **jen produkční prostředí**; schéma se nasazuje v CloudKit Console (Deploy Schema Changes); zkopíruje se schéma, **ne data**. | **ověřeno** (https://developer.apple.com/documentation/cloudkit/deploying-an-icloud-container-s-schema) |
| Po nasazení je schéma **jen aditivní**: nelze mazat typy záznamů ani pole, ani měnit existující atributy. | **ověřeno** (https://developer.apple.com/documentation/coredata/creating-a-core-data-model-for-cloudkit) |
| Doporučená strategie změn: přidávat pole, nový kontejner/úložiště, nebo atribut `version` od začátku a filtr kompatibilních záznamů. | **ověřeno** (tamtéž) |
| Build z TestFlightu používá **produkční** prostředí CloudKit, takže schéma musí být nasazeno **před** prvním TestFlight buildem. Apple píše výslovně „App Store“, TestFlight v těchto stránkách nezmiňuje. Entitlement `com.apple.developer.icloud-container-environment` má hodnoty Development / Production (https://developer.apple.com/documentation/bundleresources/entitlements/com.apple.developer.icloud-container-environment). | **domněnka** (silná, odpovídá praxi vývojářů). **k ověření na zařízení** (T3) |
| SwiftData synchronizuje jen do **soukromé** databáze přihlášeného iCloud účtu (v dokumentaci jen `.private`, `.automatic`, `.none`); sdílení mezi různými Apple ID (CKShare) SwiftData nenabízí. | **domněnka** (v dokumentaci chybí zmínka o sdílení) |
| Sync probíhá asynchronně přes tiché push notifikace; neslibuje okamžité doručení. | **ověřeno** (tamtéž: „changes aren’t synchronized immediately“, Remote notifications) |
| Diagnostika synchronizace: technické poznámky TN3163 a TN3164 (doporučil je DTS inženýr). | **ověřeno** (https://developer.apple.com/forums/thread/756893) |

### Doporučení pro model (navrhuji, nerozhoduji)
- Všechny atributy s výchozí hodnotou, relace volitelné s inverzí, žádná `.unique`; unikátnost slovíčka řešit v aplikaci (slučování duplicit po syncu).
- Enumy ukládat jako raw hodnoty (String/Int) s výchozí hodnotou, aby budoucí hodnota z novější verze nerozbila starší.
- Hned přidat `schemaVersion` (Int) do každé entity (strategie podle Apple, viz výše).
- Plánovací stav (interval, snadnost, další termín) a historie pokusů navrhnout **teď celé**, protože po nasazení do produkce nejde nic smazat ani přejmenovat.
- Obrázky stránek **neukládat do synchronizovaného úložiště** (R5, i kvůli iCloud kvótě dětského účtu). Ukládat jen výsledné texty.
- Před prvním TestFlight buildem: spustit `initializeCloudKitSchema()` v debug sestavení, zkontrolovat schéma v CloudKit Console, nasadit do Production.
- Sync testovat vždy na **dvou skutečných zařízeních** (iPhone + iPad) se stejným iCloud účtem. Simulátor jako důkaz nestačí (domněnka).

---

## (c) AVSpeechSynthesizer a rozpoznání řeči (R3)

### Přehrání výslovnosti (P1)

| Tvrzení | Stav |
|---|---|
| `AVSpeechSynthesisVoice` rozlišuje jazyk, lokalizaci a kvalitu; hlas se vybírá přes BCP 47 kód (`de-DE`, `en-GB`, `en-US`) nebo `speechVoices()` (všechny hlasy na zařízení). | **ověřeno** (https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice) |
| Kvalita `default` je na zařízení vždy; `enhanced` a `premium` si **uživatel musí stáhnout**. | **ověřeno** (https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoicequality) |
| Stažené hlasy se objeví v seznamu hlasů; hlasy Siri aplikacím nejsou přístupné (při výběru Siri hlasu se použije náhradní hlas téhož jazyka). | **ověřeno jen z výňatku vyhledávání** (WWDC18 https://developer.apple.com/videos/play/wwdc2018/236/, WWDC20 https://developer.apple.com/videos/play/wwdc2020/10022/) |
| Stažení hlasu dělá uživatel v Nastavení (Zpřístupnění, Mluvený obsah / Číst a mluvit; enhanced hlasy bývají 100 MB a víc). | **ověřeno jen z výňatku vyhledávání** (https://support.apple.com/en-us/111798) |
| Aplikace **nemůže** hlas programově stáhnout; může jen nabídnout návod. | **domněnka** (v dokumentaci chybí takové API) |
| Existují hlasy `de-DE`, `en-GB`, `en-US` (výchozí kvalita). | **domněnka** (běžná praxe; seznam jazyků na stránce dokumentace je jen seznam překladů dokumentace, ne hlasů). **k ověření na zařízení** (T5) |
| Syntéza funguje **offline**. | **domněnka** (dokumentace tvrzení o síti neobsahuje: https://developer.apple.com/documentation/avfaudio/avspeechsynthesizer). **k ověření na zařízení** v režimu Letadlo (T5) |
| Stažené (enhanced/premium) hlasy jsou lepší než výchozí. | **domněnka** (Apple píše „enhanced/premium quality“, míru zlepšení nezmiňuje); u dětského účtu a Čeština-systém jazyka **k ověření** |
| `write(_:toBufferCallback:)` umí vyrobit audio do bufferu (možnost ukládání). | **ověřeno** (https://developer.apple.com/documentation/avfaudio/avspeechsynthesizer). Není potřeba, jen poznámka |

**Návrh implementace:** pro každý jazyk vybrat z `speechVoices()` hlas dané lokalizace s nejvyšší `quality`; pokud je jen `default`, přehrát ho a v nastavení nabídnout návod ke stažení lepšího hlasu. Aplikace musí fungovat s kterýmkoli dostupným hlasem. Pozor na rozdíl britské a americké angličtiny: zvolit jedno (návrh: en-GB nebo en-US podle učebnice; **rozhodnutí zadavatele**, domněnka že škola učí britskou).

### Kontrola výslovnosti přes rozpoznání řeči (P2)

| Tvrzení | Stav |
|---|---|
| `SFSpeechRecognizer` je vázaný na **jeden jazyk** (locale). Dostupnost `isAvailable` se musí kontrolovat; pro některé jazyky je potřeba internet. | **ověřeno** (https://developer.apple.com/documentation/speech/sfspeechrecognizer) |
| `supportsOnDeviceRecognition` říká, zda recognizer umí pracovat bez sítě. `requiresOnDeviceRecognition = true` zabrání odeslání zvuku do sítě, ale **on-device výsledky nebudou tak přesné**; platí jen pokud `supportsOnDeviceRecognition` je true. | **ověřeno** (https://developer.apple.com/documentation/speech/sfspeechrecognitionrequest/requiresondevicerecognition) |
| Bez on-device režimu se zvuk posílá na servery Apple. | **ověřeno** (tamtéž a https://developer.apple.com/documentation/speech/sfspeechrecognizer, odkaz „using Apple’s servers“) |
| Limity: zhruba **1 minuta** zvuku na úlohu; denní limity na zařízení a aplikaci. | **ověřeno** (https://developer.apple.com/documentation/speech/sfspeechrecognizer) |
| Výsledek obsahuje přepis, **míru jistoty (`confidence`)** a alternativy segmentu. **Hodnocení výslovnosti nenabízí.** `voiceAnalytics` je zastaralé. | **ověřeno** (https://developer.apple.com/documentation/speech/sftranscriptionsegment) |
| Nápověda slovníku přes `contextualStrings`. | **ověřeno, že existuje** (https://developer.apple.com/documentation/speech/sfspeechrecognitionrequest/requiresondevicerecognition, sekce související). Jak moc pomáhá u izolovaných slov: **k ověření na zařízení** |
| Nový `SpeechTranscriber` (iOS 26+) běží na zařízení, jazyky se zjišťují přes `supportedLocales` / `installedLocales`, nezbytné jazykové modely se stahují. `DictationTranscriber` je kompatibilní se staršími zařízeními. | **ověřeno** (https://developer.apple.com/documentation/speech/speechtranscriber). Zda jsou **de-DE a en-GB/en-US on-device k dispozici**: **k ověření na zařízení** (T8) |
| Recognizer upravuje výsledek směrem k pravděpodobným slovům, takže špatně vyslovené slovo se může „opravit“ a hodnocení pak nebude pravdivé. U jednotlivých slov a dětského hlasu bude spolehlivost nízká. | **domněnka** (obecná vlastnost jazykových modelů; Apple to nepopisuje) |
| Aplikace potřebuje oprávnění k mikrofonu a rozpoznávání řeči. U dětského zařízení mohou být omezena Screen Time. | **domněnka** |

**Realita:** výsledkem může být jen „rozpoznáno slovo / nerozpoznáno“, ne známka výslovnosti. Takové hodnocení může dítě demotivovat při falešných zamítnutích. Zůstává **P2 a jen jako pomůcka**, nikdy ne jako hodnotící údaj pro plánování opakování. Zadání to takto už vede (D9).

---

## (d) Vision / VisionKit OCR (R2, částečně)

| Tvrzení | Stav |
|---|---|
| `VNRecognizeTextRequest` (a nové `RecognizeTextRequest`, iOS 18+) má `recognitionLanguages` v pořadí priority, `supportedRecognitionLanguages` pro zjištění podporovaných jazyků za běhu, `automaticallyDetectsLanguage`, `usesLanguageCorrection`, `customWords`. | **ověřeno** (https://developer.apple.com/documentation/vision/vnrecognizetextrequest, https://developer.apple.com/documentation/vision/recognizetextrequest) |
| Vision je standardně vychýlený k angličtině; jazyky se mají zadat v pořadí priority. Podpora jazyků závisí na zvolené cestě (fast / accurate) a revizi. Seznam konkrétních jazyků v dokumentaci **není**. | **ověřeno** (https://developer.apple.com/documentation/vision/recognizing-text-in-images) |
| Revize 3 je recognizer, který pohání Live Text; jazyky zjistit voláním `supportedRecognitionLanguages`. | **ověřeno jen z výňatku vyhledávání** (https://developer.apple.com/documentation/vision/vnrecognizetextrequestrevision3) |
| Revize 2 podporovala v režimu accurate en, zh, pt, fr, it, de, es (češtinu ne). | **domněnka** (citace hlavičkového souboru ve fóru: https://developer.apple.com/forums/thread/121048) |
| **Němčina** (ä ö ü ß) a **angličtina** jsou podporované. | **ověřeno částečně** (de a en v seznamu revize 2, viz výše). Správné čtení diakritiky: **k ověření na zařízení** (T6) |
| **Čeština (cs)** je podporovaná. Live Text v nejnovějším přehledu Apple (iOS a iPadOS 27, funkce Live Text) češtinu uvádí. Live Text a Vision API nejsou totéž. | **ověřeno jen z výňatku vyhledávání** (https://www.apple.com/ios/feature-availability/). Pro API: **domněnka**, rozhodne `supportedRecognitionLanguages` na cílovém iOS (T6) |
| U Vision se stává, že jazyk zmíněný na WWDC se v běhovém seznamu neobjeví (příklad švédštiny). Proto nespoléhat na marketingové seznamy. | **ověřeno, že spor existuje** (https://developer.apple.com/forums/thread/765905, odpověď není od Apple) |
| Jazyková korekce (`usesLanguageCorrection`) a `customWords` zmenšují chyby, ale u slovníčkových sloupců (cizí slovo vedle českého) mohou „opravovat“ správná slova. | **domněnka**; **k ověření na zařízení** (T6, porovnat zapnuto/vypnuto) |
| **Tabulky:** `RecognizeDocumentsRequest` (iOS 26+) vrací strukturu dokumentu včetně **tabulek** (buňky s řádkem a sloupcem), seznamů a odstavců. Seznam jazyků v dokumentaci chybí, je ve vlastnosti `supportedRecognitionLanguages`. | **ověřeno** (https://developer.apple.com/documentation/vision/recognizedocumentsrequest, https://developer.apple.com/videos/play/wwdc2025/272/ jen z výňatku) |
| Pro zařízení bez iOS 26: tabulku poskládat ze souřadnic řádků (sloupce podle osy x). | **domněnka** (návrh řešení) |
| **Ručně psané** texty. | Apple nic konkrétního netvrdí. **k ověření na zařízení** (T6); očekávat slabší výsledek (domněnka). Kontrola a oprava uživatelem (D7) je tedy nutná |
| `DataScannerViewController` (VisionKit, iOS 16+) skenuje text živě; má `supportedTextRecognitionLanguages`, vyžaduje oprávnění ke kameře. | **ověřeno** (https://developer.apple.com/documentation/visionkit/datascannerviewcontroller) |
| OCR běží v zařízení (bez odeslání obrázku ven). | **domněnka** (Vision API nemá síťový parametr a dokumentace neuvádí odeslání; ověřit sledováním sítě / režimem Letadlo, T6). Pro R5 je to klíčové, proto ověřit. |

**Návrh:** vstup foto → `RecognizeDocumentsRequest` (iOS 26+) nebo `RecognizeTextRequest` s `recognitionLanguages = [cs, de, en]` → obrazovka kontroly s **každým řádkem editovatelným**, u řádků s nízkou jistotou označených. Jazyky párovat podle záhlaví sloupce nebo podle volby uživatele („tato stránka je němčina“). Přesnost se nemá slibovat, kontrola je součástí postupu (už rozhodnuto v D7).

---

## (e) Dětský účet, soukromí, Kids kategorie (R5)

| Tvrzení | Stav |
|---|---|
| **Kids kategorie je dobrovolná**: „Pokud se chcete zúčastnit…“. Pro zařazení je potřeba věkové pásmo (5 a méně, 6–8, 9–11), rodičovská brána, ochrana dat. Stránka o Kids kategorii **TestFlight nezmiňuje**. Pro 7. třídu (12–13 let) by dítě beztak nespadlo do nejvyššího pásma 9–11. | **ověřeno** (https://developer.apple.com/app-store/kids-apps/, https://developer.apple.com/app-store/review/guidelines/ §1.3) |
| Kids pravidla: žádná analytika ani reklama třetích stran, žádné odesílání osobních či zařízení údajů třetím stranám. | **ověřeno** (https://developer.apple.com/app-store/review/guidelines/ §1.3, §5.1.4). Pro nás platí jako dobrá praxe (R5) i mimo Kids. |
| Aplikace, která od nezletilého sbírá osobní údaje, musí mít zásady ochrany soukromí a splnit dětské zákony (COPPA, GDPR). | **ověřeno** (§5.1.4(b)) |
| Označení „pro děti“ v názvu, podtitulu, ikoně, popisu je vyhrazeno Kids kategorii. V metadatech TestFlightu nepoužívat. | **ověřeno** (§5.1.4(b), pravidlo 2.3.8) |
| Pro TestFlight platí pravidla App Review: build má být „určen pro veřejné vydání“ a splňovat Guidelines; významné změny betatestů jdou přes TestFlight App Review. Soukromá rodinná aplikace tedy může při externím TestFlightu narazit na kontrolu „veřejného určení“. | **ověřeno** (https://developer.apple.com/app-store/review/guidelines/ §2.2). Reálný dopad: **k ověření** (T7) |
| Pro externí testování je povinný popis bety a e-mail pro zpětnou vazbu. | **ověřeno** (https://developer.apple.com/help/app-store-connect/test-a-beta-version/provide-test-information) |
| **App Privacy** odpovědi a URL zásad ochrany soukromí: stránka říká, že je potřeba při distribuci **na App Store**; URL zásad je „povinné pro všechny aplikace na iOS“. Zda se to vyžaduje i pro TestFlight-only, stránka neříká. | **ověřeno, že chybí** (https://developer.apple.com/help/app-store-connect/manage-app-information/manage-app-privacy). **k ověření** v App Store Connect při prvním externím buildu (T7) |
| Při žádném sběru dat se vybírá „No, we do not collect data from this app“. | **ověřeno** (tamtéž) |
| Aplikace smí žádat jen data potřebná pro hlavní funkci; u fotek má používat **out-of-process picker** (PHPicker) místo plného přístupu k Fotkám. | **ověřeno** (https://developer.apple.com/app-store/review/guidelines/ §5.1.1(iii)) |
| TestFlight sám sbírá pro vývojáře relace, pády a zpětnou vazbu testerů. Veřejný odkaz zobrazuje anonymní testery. | **ověřeno** (https://developer.apple.com/help/app-store-connect/test-a-beta-version/view-and-manage-tester-information/); možnost vypnout sdílení je **domněnka** |
| Data v soukromé CloudKit databázi vývojář nevidí, takže pro App Privacy lze pravděpodobně uvést „nesbíráme“. | **domněnka** (v dnes dostupných stránkách nepotvrzeno) |
| Český věk souhlasu se zpracováním osobních údajů dítěte je 15 let. | **domněnka** (nejde o Apple, nevyhledáno). Při dobré praxi (žádný sběr) se netýká |

**Pravidla pro R5 (návrh):** žádné SDK třetích stran, žádná analytika, žádná síť mimo CloudKit; OCR i hlas v zařízení (`requiresOnDeviceRecognition`); foto přes PHPicker nebo kameru s nejmenším oprávněním; obrázky po rozpoznání zahodit; jen soukromá CloudKit databáze. Před prvním externím buildem připravit krátké zásady ochrany soukromí (URL) a odpovědi App Privacy.

---

## Předpoklady, které jsem si musel doplnit

- Cílová verze iOS na synově zařízení není ve společném zadání. Předpokládám iOS 26 nebo novější (potřebné pro `RecognizeDocumentsRequest` a `SpeechTranscriber`). Pro starší verze existují náhrady (viz (d), (c)). **Zadavatel doplní model a verzi iOS.**
- Přesný věk syna a jeho země účtu (CZ) jsou předpoklad. Rozhoduje, pod jakou věkovou hranicí TestFlight účet odmítne.
- Vývojářský účet je placený individuální nebo organizační (D5). Pro interní testery by šlo použít otce jako Admin.

## Seznam testů na reálném zařízení

| # | Test | Řeší |
|---|---|---|
| T1 | Dětský účet syna: nainstalovat TestFlight, otevřít veřejný odkaz externí skupiny, nainstalovat build. Zaznamenat přesné hlášky (věk, Ask to Buy). | R1 |
| T2 | Na synově zařízení nechat iCloud = dětský účet a Média a nákupy = jiný (dospělý) účet. Ověřit, že TestFlight běží pod druhým a CloudKit sync pod synovým iCloudem. | R1, D6 |
| T3 | Build z TestFlightu bez nasazeného schématu a po nasazení: ověřit sync iPhone ↔ iPad (stejný iCloud). Sledovat telemetrii v CloudKit Console. | R4 |
| T4 | Zápis na jednom zařízení, čtení na druhém v letadlovém režimu a po připojení. Dedupe po syncu. | R4 |
| T5 | `AVSpeechSynthesisVoice.speechVoices()` na zařízení: vypsat jazyk/kvalitu pro de-DE, en-GB, en-US; přehrát v režimu Letadlo; ověřit hlas po stažení enhanced. | R3 |
| T6 | OCR: `supportedRecognitionLanguages` na cílovém iOS (je cs?); 10 reálných stránek (tisk, tabulka, ručně psané, ä ö ü ß, háčky a čárky), korekce zapnutá a vypnutá; v režimu Letadlo, případně sledování sítě. | R2, R5 |
| T7 | Prvním externím buildem projít TestFlight App Review a zapsat, co Apple požadoval (URL zásad, App Privacy, „veřejné určení“, věkový dotazník). | R5, D5 |
| T8 | `SFSpeechRecognizer(locale:)` a `SpeechTranscriber` pro de-DE a en: `supportsOnDeviceRecognition`, `installedLocales`; 20 izolovaných slov syna, míra falešných zamítnutí. | R3 (P2) |

## Zdroje (oficiální, otevřené v této session)

- https://developer.apple.com/help/app-store-connect/test-a-beta-version/testflight-overview
- https://developer.apple.com/help/app-store-connect/test-a-beta-version/invite-external-testers
- https://developer.apple.com/help/app-store-connect/test-a-beta-version/add-internal-testers
- https://developer.apple.com/help/app-store-connect/test-a-beta-version/provide-test-information
- https://developer.apple.com/help/app-store-connect/test-a-beta-version/view-and-manage-tester-information/
- https://developer.apple.com/help/app-store-connect/reference/testflight/testflight-tester-information
- https://developer.apple.com/help/app-store-connect/manage-app-information/manage-app-privacy
- https://developer.apple.com/testflight/
- https://developer.apple.com/app-store/review/guidelines/
- https://developer.apple.com/app-store/kids-apps/
- https://developer.apple.com/documentation/swiftdata/syncing-model-data-across-a-persons-devices
- https://developer.apple.com/documentation/coredata/creating-a-core-data-model-for-cloudkit
- https://developer.apple.com/documentation/cloudkit/deploying-an-icloud-container-s-schema
- https://developer.apple.com/documentation/bundleresources/entitlements/com.apple.developer.icloud-container-environment
- https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoice
- https://developer.apple.com/documentation/avfaudio/avspeechsynthesisvoicequality
- https://developer.apple.com/documentation/avfaudio/avspeechsynthesizer
- https://developer.apple.com/documentation/speech/sfspeechrecognizer
- https://developer.apple.com/documentation/speech/sfspeechrecognitionrequest/requiresondevicerecognition
- https://developer.apple.com/documentation/speech/sftranscriptionsegment
- https://developer.apple.com/documentation/speech/speechtranscriber
- https://developer.apple.com/documentation/vision/vnrecognizetextrequest
- https://developer.apple.com/documentation/vision/recognizetextrequest
- https://developer.apple.com/documentation/vision/recognizing-text-in-images
- https://developer.apple.com/documentation/vision/recognizedocumentsrequest
- https://developer.apple.com/documentation/visionkit/datascannerviewcontroller

Neoficiální (jen jako důkaz, že hlášení existují, ne jako pravidlo): vlákna na https://developer.apple.com/forums/ (675464, 687266, 706205, 756893, 125142, 121048, 765905).

Pouze z výňatků vyhledávání (stránky se v session nepodařilo otevřít): https://support.apple.com/en-au/125666, https://support.apple.com/en-us/102617, https://support.apple.com/en-us/102033, https://support.apple.com/en-us/117294, https://support.apple.com/en-us/111798, https://www.apple.com/ios/feature-availability/.

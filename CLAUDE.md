# word-memo — pokyny pro Claude

Aplikace na učení slovíček (angličtina en-GB a němčina → čeština) pro syna, 7. třída. iOS 26, SwiftUI, SwiftData + CloudKit, iPhone i iPad. Distribuce **Ad Hoc přes Mac a Xcode** (TestFlight dětský účet nespustí, viz `docs/10`).

## Jak číst repo
- `docs/00-zadani.md` je zdroj pravdy (rozhodnutí D1–D17, rizika R1–R6). Neměň ho bez pokynu zadavatele.
- `docs/01` vize, `02` user stories, `04` story mapa, `05` architektura (13+1 ADR), `06` UX, `07` algoritmus opakování, `08` ověření platformy, `09` obsah a import, `10` distribuce, `11` plán stavby.
- `prototype/index.html` je schválený klikací prototyp (simulace, ne produkční kód).

## Pravidla
- Česky, stručně, závěr na začátku. Datum ISO. Texty v aplikaci česky.
- Architektura: `Domain` je čisté Swift bez Apple frameworků. Adaptéry (`Persistence`, OCR/Vision, Speech) kolem. Zdroj pravdy je append-only log pokusů, stav FSRS je lokální cache (ADR-03).
- Model musí být CloudKit-kompatibilní: žádné `unique`, všechny atributy s defaultem nebo volitelné, žádné SwiftData relace (UUID reference), `schemaVersion` v každé entitě, enumy jako String.
- Soukromí dítěte: žádná síť kromě CloudKit, žádná SDK třetích stran, žádná analytika, fotka jen dočasně v zařízení.
- Tvrzení o platformě označuj **ověřeno (zdroj)** nebo **domněnka**. Nehádej chování frameworků, ověř sondou.
- Každý nález statického review je hypotéza, dokud ho `verifier` nepotvrdí.
- Prostředí CloudKit (ADR-14): vývoj v Development, skutečná data syna až po přechodu na Production. Nikdy nemíchej buildy obou prostředí na synově zařízení.
- Instalaci na zařízení syna a změnu prostředí CloudKit dělá člověk. Agent se před nimi zastaví.
- Žádný PR bez výslovného pokynu. Vývoj na větvi `claude/friendly-wright-hj1b14` (nebo na té, kterou zadá uživatel).

## Agenti (`.claude/agents/`)
`architect`, `ux-designer`, `learning-scientist`, `content-curator`, `prototyper` (návrh), `code-reviewer`, `verifier`, `test-author`, `test-runner`, `ux-verifier`, `release-engineer` (kvalitativní smyčka), `orchestrator` (koordinace).

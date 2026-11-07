# 11 — Plán stavby

Stav k 2026-11-07. Prototyp schválen zadavatelem. **Stavba se dělá na Macu s Xcode** — v cloudové session není Swift toolchain (ověřeno: `swift`, `xcodebuild` chybí, `download.swift.org` je z této sítě blokované, balíček `swiftlang` v Ubuntu 24.04 není). Bez kompilace by byl Swift kód nezkontrolovaný, proto se píše tam, kde se dá přeložit a testovat.

## Závěr
Doporučený postup: spustit Claude Code lokálně na Macu v naklonovaném repozitáři `word-memo` (načte `CLAUDE.md` a agenty). Iterace níže jsou seřazené podle priorit P0 → P1 → P2 z `00-zadani.md` a každá končí ověřením (testy, simulátor, review).

## Iterace

| # | Obsah | Stories | Ověření (agent) |
|---|---|---|---|
| 0 | Xcode projekt (iOS 26, iPhone + iPad), SwiftPM modul `Domain`, iCloud capability. **Sondy:** U1 (Hello CloudKit na synově iPhonu a iPadu, Developer Mode), U2 (sync v Development i Production), U3 (změna prostředí), T6 (OCR na fotce vlastní stránky se slovíčky). | — (R2, R6) | `verifier` |
| 1 | `Domain`: modely slova, karty, pokusu; vyhodnocení odpovědi (správně / skoro / zatím ne, překlepy, členy); plánovač FSRS-6 proti referenčním datům z py-fsrs; přestavba stavu z logu; fáze karty. `swift test`. | US-07, US-08 | `test-author`, `test-runner`, `code-reviewer` |
| 2 | `Persistence`: SwiftData modely podle `05` kap. 2 (CloudKit-kompatibilní), sady a slova, uložení pokusů, měkké mazání. | US-04, US-10 | `verifier` (N1–N3), `test-runner` |
| 3 | Test (mix výběr/psaní, oba směry), souhrn, první spuštění, offline. UI podle `06` a prototypu. | US-01, 05, 06, 09, 13 | `ux-verifier` (simulátor), `code-reviewer` |
| 4 | Import fotkou: OCR adaptér (`RecognizeDocumentsRequest`), párování řádků, kontrolní obrazovka, vrácení importu. | US-02, US-03 | `verifier` (T6), `ux-verifier` |
| 5 | iCloud sync a konflikty (pokud nebyl hotový od iterace 2; závisí na U2). | US-11, US-12 | `verifier` (T3, T4, N4–N6) |
| — | **Předání prvního buildu synovi** (P0 hotovo): Ad Hoc export, Production, `release-engineer`. Zastaví se před instalací. | | člověk |
| 6 | P1: historie pokroku, slabá místa, nepravidelná slovesa, výslovnost (přehrání), CSV import. | US-14 až US-20 | stejná smyčka |
| 7 | P2 podle rozhodnutí zadavatele: hotové sady, gamifikace, kontrola výslovnosti, rodičovský pohled, připomenutí. | US-21 až US-25 | |

## Co zadavatel rozhoduje před iterací 3
Q1 (definice naučeného slova a stavů), Q2 (přísnost vyhodnocení), zda psát i u nových slov (`07`), Q7 (stačí poslední úprava vyhrává), `typedText` ukládat či ne, lhůty koše a konceptu (`05` kap. 12).

## Co je potřeba mít po ruce
Mac s Xcode, kabel, synův iPhone a iPad (iOS 26, Developer Mode), tvoje fotka stránky se slovíčky pro T6, přístup do CloudKit Console.

# Zasady code review (szablon GTM AsapChat)

Plik czyta automatycznie `om-code-review`. Bramka walidacji: `.ai/agentic.config.json` (`validation.commands`). Reguły repo: `AGENTS.md`. Kontrakt loadera: `docs/CONNECT-API.md`.

## Priorytety

1. **Strona użytkownika nie może ucierpieć**: tag zawsze kończy się `gtmOnSuccess` albo `gtmOnFailure`, nie blokuje sekwencji w kontenerze.
2. **Uprawnienia**: jedno, `inject_script` dla `https://panel.asapchat.io/api/w/*`.
3. **Zgodność z zasadami galerii**: odrzucenie kosztuje tygodnie.
4. **Zakres**: v1 to to, co jest w specu. Reszta to osobne issue.
5. Styl i uproszczenia.

## Sprawdzenia

**Szablon**
- Pola, typy, wartości domyślne i teksty są identyczne z tabelami w specu, punkt 4, znak po znaku, po angielsku.
- Wartości pól są sprawdzane w definicji pola i drugi raz w kodzie tagu. Token przechodzi przez `encodeUriComponent`.
- Adres loadera i kolejność flag (`chat`, `search`, `cart`) zgodne ze specem, punkt 5.
- Brak `access_globals`, `logging` i gwiazdki na cały host.
- Brak pól wyglądu, opóźnienia i wykluczeń.

**Repo publiczne**
- W plikach, commitach, opisie PR i na zrzutach nie ma ID ani tokenu prawdziwego sklepu, adresów e-mail kont, identyfikatorów kontenerów ani treści wewnętrznych. Przykłady: `1284` i `abc123`.

**Galeria**
- `template.tpl`, `metadata.yaml`, `LICENSE` (Apache 2.0) i `README.md` są w katalogu głównym.
- Zasada `sha` z `AGENTS.md`, reguła 6, jest zachowana.

## Testy

- Zmiana kodu tagu ma test w sekcji `___TESTS___`. Błąd ma najpierw test, który go odtwarza. Zadanie 03 dodaje kod razem z testem 1 (poprawny adres), pozostałe testy z listy dochodzą w zadaniu 04.
- „Run tests” jest zielone, zrzut jest w PR.
- Zmiana pól: zrzut formularza na szerokości 390 px i 1280 px.

## Severity

- **Blocker:** szersze uprawnienia, ścieżka kodu bez `gtmOnSuccess`/`gtmOnFailure`, token albo dane klienta w repo, złamana zasada galerii, zmiana spoza zakresu v1.
- **Major:** brak testu zmiany zachowania, tekst niezgodny ze specem, brak wpisu `versions` po publikacji.
- **Minor/Nit:** styl, nazewnictwo, drobne uproszczenia (nie blokują).
- Czerwone CI lub konflikt zbierane razem z pozostałymi uwagami, nie zamiast przeglądu.

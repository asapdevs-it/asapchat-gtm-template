# Start dla dewelopera

Czytasz to raz, pierwszego dnia. Potem pracujesz z issues.

## 1. Co budujemy

Szablon tagu Google Tag Manager, który ładuje czat AsapChat i wyszukiwarkę AsapSearch. Trafia do galerii szablonów społeczności. To repo jest publiczne: nic wewnętrznego tu nie trafia.

## 2. Kolejność czytania w repo

1. `README.md` (po zadaniu 06 polski README dla deweloperów leży w `docs/README-dev.md`, a `README.md` jest angielską instrukcją dla użytkowników)
2. `docs/CONNECT-API.md`: adres loadera, jedyna rzecz, z którą rozmawia szablon
3. `.ai/specs/2026-11-11-connector-v1.md`: co dokładnie ma powstać
4. `docs/mockups/template.html`: otwórz w przeglądarce, tak wygląda formularz pól w GTM
5. `docs/marketplace-listing.md` i `docs/accounts.md`: wymagania galerii i konta do testów
6. `AGENTS.md`, `SDLC.md`, `CODE_REVIEW.md`: zasady pracy

Jeśli `README.md` podaje inną kolejność, obowiązuje ta stąd. `SDLC.md` jest wspólny dla wszystkich repo firmy i opisuje cały proces po angielsku. Wymienia skille (`om-...`), których nie musisz znać na start: wystarczą te z punktu 4. Wzmianki o `BACKWARD_COMPATIBILITY.md`, `.uxproof/` i `.ai/skills/` nie dotyczą tego repo.

## 3. Narzędzia

1. Zainstaluj `gh` (2.82.1 lub nowszy) i `jq`. Zaloguj się: `gh auth login`.
2. Zainstaluj Claude Code albo Codex CLI.
3. Zainstaluj skille SDLC (raz na komputer):

```bash
npx skills add https://github.com/open-mercato/skills.git --skill '*'
```

4. Zainstaluj PyYAML (`pip install pyyaml`): używa go bramka walidacji. Komenda z bramki zaczyna przechodzić po zadaniu 01, bo dopiero ono dodaje `metadata.yaml`.
5. Sklonuj repo i otwórz w nim agenta. Agent sam czyta `AGENTS.md` i `.ai/agentic.config.json`.

## 4. Jak wygląda jedno zadanie

1. Zadania mają w tytule prefiks `01` do `07`. To jest kolejność wykonania. Numer GitHuba (`#N`) jej nie oznacza. Bierzesz zadanie o najniższym prefiksie, które nie ma etykiety `in-progress` i którego zależności (sekcja „Zależności” w issue) są zamknięte. Gdy dokumenty mówią „zadanie 03”, chodzi o prefiks w tytule.
2. Powiedz agentowi: `/om-fix <numer GitHuba>` (małe zadanie) albo `/om-auto-create-pr <numer GitHuba>` (większe). Agent przypisze issue do Ciebie, doda `in-progress`, zrobi gałąź, zmiany i PR.
   Podział pracy: `template.tpl` zmieniasz Ty, w edytorze szablonów GTM, i eksportujesz plik do repo (zadania 01 do 04). Agent zajmuje się plikami repo (`metadata.yaml`, CI, dokumenty, README), commitem i PR. Agent nie pisze `template.tpl` od zera.
3. Przeczytaj cały diff, zanim poprosisz o review. Agent myli się pewnym tonem. Ty odpowiadasz za zmianę.
4. Bramka walidacji (komendy z `.ai/agentic.config.json`) musi przejść lokalnie.
5. PR do `main`. Każdy PR zatwierdza `prz10` (właściciel repo). To zasada zespołu: gałąź `main` nie jest chroniona technicznie, więc pilnujesz jej sam. Wcześniej możesz puścić `/om-auto-review-pr <numer PR>`.
6. PR z `needs-qa` czeka na `qa-approved`. QA robi drugi deweloper: nad wtyczkami pracują `jakubix2000` i `WanSpi`, QA robi ten, kto nie pisał PR. Importuje `template.tpl` z gałęzi PR do swojego kontenera testowego GTM, uruchamia „Run tests”, sprawdza zmianę w podglądzie na stronie testowej i dołącza zrzuty. Gdy drugi deweloper nie może tego zrobić w dwa dni robocze, QA robi `prz10`. Przy `risk-low` i `risk-medium` możesz też zrobić QA sam (self-QA z `SDLC.md`: dowód w komentarzu PR, etykiety `qa-approved` i `qa-self-verified`). Przy `risk-high` nigdy.
7. Merge przez squash. W opisie PR wpisz `Closes #N` z numerem GitHuba: tylko angielskie słowo zamyka issue automatycznie.

## 5. Zasady, których nie łamiemy

- Zakres v1 to to, co jest w specu. Pomysł na coś więcej: nowe issue, nie dodatkowa zmiana w PR.
- Formularz pól rysuje GTM. Nie stylujemy go. Jedyny nasz element graficzny to ikona szablonu.
- Wszystko, co widzi użytkownik szablonu, jest po angielsku: etykiety, teksty pomocy, komunikaty błędów, opis, README. Teksty bierz znak po znaku ze specu, punkt 4. Bez pauz (znaku —).
- Repo jest publiczne: żadnych tokenów, danych klientów, adresów e-mail kont ani treści wewnętrznych w plikach, commitach, issues i PR.

## 6. Kiedy pytać

Pytasz przez issue w tym repo przypisane do `prz10`. Nie czekaj z pytaniem, gdy:

- coś kosztuje (płatne narzędzie, domena, konto),
- potrzebne jest konto Google wydawcy (wysyłka zgłoszenia do galerii, zadanie 07),
- dokumenty sobie przeczą (spec, makieta, `docs/marketplace-listing.md`, issue),
- potrzebna jest zmiana loadera.

W pozostałych sprawach decyduje spec. Gdy spec milczy, wybierz prostsze rozwiązanie i opisz wybór w PR.

## 7. Od właściciela repo potrzebujesz

Nic przed zadaniem 07. Zgłoszenie do galerii idzie z nowego konta Google wydawcy (AsapCommerce): gdy zadanie 07 jest gotowe, przypisz issue do `prz10` z komentarzem „ready for owner” i daj mu znać. Konto i samo zgłoszenie są po jego stronie (`docs/accounts.md`, punkt 4).

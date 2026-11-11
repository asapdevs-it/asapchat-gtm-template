# Community Template Gallery: treść wpisu i zgłoszenie

Galeria nie ma formularza z opisem marketingowym. Wpis powstaje z plików w publicznym repo GitHub i z pól w zakładce Info szablonu. Nazwy przycisków w GTM sprawdź w dniu zgłoszenia, bo Google je zmienia.

## 1. Pola Info szablonu (zakładka Info w edytorze)

Wszystkie teksty po angielsku. Galeria jest globalna.

| Pole | Wartość |
|---|---|
| Name | AsapChat: AI Chat and Product Search |
| Description (krótki, widoczny w galerii) | Adds the AsapChat AI chat widget and the AsapSearch product search to your site. Requires a free AsapChat account. The chat shows only on the domain set as the shop address in your AsapChat panel. |
| Type | Tag |
| Categories | dokładnie dwie wartości z listy w edytorze: `CHAT` i `PERSONALIZATION` |
| Brand name | AsapChat |
| Icon / thumbnail | znak AsapChat (biały dymek na fioletowym tle `#6543f0`), PNG, kwadrat, min. 64 x 64 px, maks. 50 KB. Plik robisz sam ze znaku z `docs/assets/asapchat-logo-white.svg` (sam dymek, bez napisu) na kwadratowym fioletowym tle. Wklejasz w edytorze, trafia do `template.tpl` |
| Agree to Community Template Gallery Terms of Service | zaznaczone (wymagane) |

Wydawca: AsapCommerce. W galerii jako autor widoczna jest organizacja GitHub `asapdevs-it`.

Etykiety pól tagu, teksty pomocy i komunikaty błędów: specyfikacja, punkt 4.

## 2. README.md po angielsku

README w katalogu głównym zastępujemy wersją angielską dla użytkowników (zadanie 06). Obecny polski README dla deweloperów przenosimy do `docs/README-dev.md`.

```
# AsapChat for Google Tag Manager

Adds the AsapChat AI chat widget and the AsapSearch product search to any site that uses Google Tag Manager.
Published by AsapCommerce.

## Setup

1. In your AsapChat panel open your shop settings, click Plugin and copy the Shop ID and the Widget token.
2. In GTM go to Templates > Search Gallery, find "AsapChat" and add it to your workspace.
3. Create a new tag from the template. Paste the values into "Shop ID" and "Widget token".
4. Keep "Load chat" and "Load AsapSearch" ticked, or untick the one you do not need.
5. Untick "Allow add to cart from search" if visitors should not add products to the cart from search results.
6. Use the "All Pages" trigger, preview, then publish.

## Requirements

* A free AsapChat account (https://asapchat.io).
* The chat shows only on the domain set as the shop address in your AsapChat panel.
* AsapSearch shows results after your products are imported into AsapChat.

## Look and texts

Colors, the assistant name and greetings are set in your AsapChat panel, not in GTM.
A change reaches your site within 5 minutes.

## Delay and excluded pages

The template has no delay or exclusion fields. Use GTM triggers:

* Delay: use a Timer trigger instead of All Pages. Interval in milliseconds (5000 = 5 seconds), Limit 1.
* Excluded pages: add a Page View trigger with a Page Path condition as an Exception on the tag.
* Delay and exclusion together: put the Page Path condition ("does not contain") into the Timer trigger itself.
  A Page View exception does not block a Timer trigger.
* Delay the chat only: create two tags. One with "Load AsapSearch" only on All Pages,
  one with "Load chat" only on the Timer trigger.

## Permissions

* Injects one script from https://panel.asapchat.io/api/w/. Nothing else.

## Data

The widgets send chat messages and search queries typed by visitors to AsapChat.
Privacy policy: https://asapchat.io/polityka-prywatnosci
Terms: https://asapchat.io/regulamin

## Support

TODO(support address)
```

Adres wsparcia podaje właściciel repo, `prz10` (`ONBOARDING-DEV.md`, punkt 7). Zadanie 06 zostawia wiersz `TODO(support address)`, jeśli adresu jeszcze nie ma. Zadanie 07 nie może być zamknięte, dopóki ten wiersz istnieje.

## 3. Pliki wymagane w katalogu głównym repo

- [ ] `template.tpl` (eksport z edytora, z zaakceptowanymi warunkami galerii, sekcja `___TERMS_OF_SERVICE___`)
- [ ] `metadata.yaml` (`homepage`, `documentation`, `versions` z `sha` i `changeNotes`)
- [ ] `LICENSE` z treścią Apache License 2.0 (galeria wymaga dokładnie tej licencji)
- [ ] `README.md` po angielsku

## 4. Wymagania i zasady

- [ ] Repo `asapdevs-it/asapchat-gtm-template` jest publiczne, gałąź domyślna `main`.
- [ ] Jedno repo = jeden szablon.
- [ ] `sha` w `metadata.yaml` to pełny sha commita z `main`, w którym `template.tpl` ma zgłaszaną treść (zasada: `AGENTS.md`, reguła 6).
- [ ] W `README.md` nie ma wiersza `TODO(support address)`.
- [ ] Jedno uprawnienie: `inject_script` dla `https://panel.asapchat.io/api/w/*`.
- [ ] Sam szablon nie zbiera danych osobowych.
- [ ] Nazwa i opis nie wprowadzają w błąd, brak cudzych marek.
- [ ] Szablon przechodzi własne testy („Run tests”).
- [ ] Zgodność ze style guide galerii (nazwy pól, teksty pomocy).
- [ ] W repo nie ma tokenów, danych klientów ani treści wewnętrznych.

## 5. Typowe powody odrzucenia

1. Brak pliku `LICENSE` Apache 2.0 albo inna licencja.
2. Niezaznaczone warunki galerii w szablonie.
3. Błędny `sha` w `metadata.yaml` (commit bez `template.tpl` albo skrócony sha).
4. Zbyt szerokie uprawnienia (`inject_script` na `https://*`).
5. Tag, który nie woła `gtmOnSuccess` / `gtmOnFailure`.

Ryzyko: galeria może odrzucić zgłoszenie także z innego powodu. Poprawiamy według uwag z maila i zgłaszamy ponownie.

## 6. Zgłoszenie krok po kroku

1. Przejrzyj pliki i historię commitów: żadnych tokenów, żadnych danych klientów, żadnych treści wewnętrznych.
2. Sprawdź, że `metadata.yaml` ma aktualny `sha`.
3. Ten krok wykonuje właściciel repo (`prz10`): wejdź na https://tagmanager.google.com/gallery/ , zaloguj się kontem Google wydawcy (`docs/accounts.md`), wybierz zgłoszenie szablonu i podaj adres repo. Deweloper kończy na kroku 2 i oznacza issue zadania 07 komentarzem „ready for owner”.
4. Google sprawdza repo automatycznie i ręcznie. Wynik przychodzi mailem na konto Google.
5. Po akceptacji szablon jest widoczny w galerii. Aktualizacja: zmiana `template.tpl` przez PR, potem jednolinijkowy PR z nowym wpisem w `metadata.yaml` (sha commita z `main`). Użytkownicy widzą w GTM informację o dostępnej aktualizacji.

## 7. Czas przeglądu

Google nie podaje terminu. Licz się z kilkoma dniami do kilku tygodni.

## 8. Po publikacji

- Wpisz link do szablonu w galerii w issue „Zgłoszenie do galerii” i zamknij je.
- Gdy powstanie strona z instrukcją na asapchat.io, podmień `documentation` w `metadata.yaml`.

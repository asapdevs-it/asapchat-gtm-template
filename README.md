# AsapChat: szablon tagu Google Tag Manager

Szablon tagu (Custom Template) dla Google Tag Managera. Dodaje na stronę czat AI AsapChat i wyszukiwarkę produktów AsapSearch. Miejsce publikacji: Community Template Gallery (publiczna galeria szablonów GTM). Wydawca: AsapCommerce.

GTM działa na każdej platformie, więc ten szablon pasuje do każdego sklepu i każdej strony z GTM.

To repo jest publiczne. Nie wpisuj tu tokenów, danych klientów ani niczego wewnętrznego.

## Co robi wersja 1

1. Użytkownik wpisuje dwa pola: „Shop ID” i „Widget token”. Oba kopiuje z panelu AsapChat: Sklep → Wtyczka.
2. Zaznacza, co ma się ładować: „Load chat”, „Load AsapSearch” albo oba.
3. Przy wyszukiwarce może odznaczyć „Allow add to cart from search”.
4. Tag ładuje jeden plik, loader AsapChat:

```
https://panel.asapchat.io/api/w/{Shop ID}.js?t={Widget token}
```

Loader sam dokłada czat i wyszukiwarkę z aktualnymi ustawieniami sklepu. Odznaczone pole dopisuje do adresu `&chat=0`, `&search=0` albo `&cart=0`.

Wygląd, nazwę asystenta i powitania zmieniasz w panelu AsapChat. Zmiana jest na stronie po maksymalnie 5 minutach.

Czat pojawia się tylko na domenie ustawionej jako adres sklepu w panelu AsapChat.

## Czego wersja 1 nie robi

- Nie ma przycisku „Połącz z AsapChat” ani tajnego tokenu. Token widgetu jest publiczny.
- Nie ma pól wyglądu, opóźnienia ani wykluczeń. Opóźnienie i wykluczenia robisz regułami GTM (Timer, wyjątki z Page Path). Instrukcja: specyfikacja, punkt 7.
- Nie ma ekranu statusu. Status widgetu jest w panelu AsapChat.
- Nie wysyła zdarzeń do dataLayer, nie mierzy konwersji.

## Zacznij tutaj

1. `ONBOARDING-DEV.md` i `SDLC.md`: narzędzia i proces.
2. `.ai/specs/2026-11-11-connector-v1.md`: specyfikacja i lista issues.
3. `docs/mockups/template.html`: makieta pól szablonu.
4. `AGENTS.md`: reguły.
5. `docs/CONNECT-API.md`, punkt 6: kontrakt loadera.
6. `docs/marketplace-listing.md` i `docs/accounts.md`: zgłoszenie do galerii i konta do testów.
7. Issues w kolejności ze specyfikacji.

## Linki

- Dokumentacja szablonów: https://developers.google.com/tag-platform/tag-manager/templates
- Zgłaszanie do galerii: https://developers.google.com/tag-manager/templates/gallery
- Zasady galerii: https://developers.google.com/tag-platform/tag-manager/templates/policies
- Panel AsapChat: https://panel.asapchat.io

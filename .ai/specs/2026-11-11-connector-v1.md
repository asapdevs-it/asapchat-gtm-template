# Specyfikacja: szablon tagu GTM „AsapChat”, wersja 1

Data: 2026-11-11. Status: zatwierdzona.

To repo jest publiczne. Nie wpisuj tu tokenów, danych klientów ani niczego wewnętrznego.

## 1. Cel

Osoba, która ma na stronie Google Tag Managera, dodaje czat AsapChat i wyszukiwarkę AsapSearch bez wklejania kodu. Wybiera szablon z galerii, wpisuje ID sklepu i token widgetu, publikuje kontener.

Szablon robi jedną rzecz: ładuje loader AsapChat. Loader sam dokłada czat i wyszukiwarkę z aktualnymi ustawieniami sklepu. Kontrakt loadera: `docs/CONNECT-API.md`, sekcja „Loader”. Loader jest częścią usługi AsapChat. W tym repo go nie zmieniasz.

## 2. Słownik

- **GTM**: Google Tag Manager. Narzędzie, w którym właściciel strony zarządza skryptami („tagami”) bez edycji kodu strony.
- **Szablon tagu (Custom Template)**: przepis na tag z formularzem pól. Kod szablonu działa w piaskownicy.
- **Sandboxed JavaScript**: okrojony JavaScript szablonów. Nie ma `window`, `document`, `setTimeout`. Skrypt zewnętrzny ładujesz funkcją `injectScript(url, onSuccess, onFailure, cacheToken)`.
- **Loader**: jeden plik JS pod adresem `https://panel.asapchat.io/api/w/{shop_id}.js?t={token}`. Sam wstawia czat i wyszukiwarkę.
- **Token widgetu**: publiczny token sklepu. Ten sam, który jest w zwykłym kodzie do wklejenia. Widać go w źródle strony, więc nie jest sekretem.
- **Community Template Gallery**: publiczna galeria szablonów GTM. Szablon trafia tam z publicznego repo GitHub.

## 3. Czego szablon nie ma

- Nie ma przycisku „Połącz z AsapChat”. GTM nie ma własnego serwera ani ekranu, na który można wrócić.
- Nie ma żadnego tajnego tokenu. Jedyny token to publiczny token widgetu.
- Nie ma pól wyglądu. Wygląd, nazwę asystenta i powitania użytkownik zmienia w panelu AsapChat. Zmiana jest na stronie po maksymalnie 5 minutach (cache loadera).
- Nie ma pól „opóźnienie” ani „wykluczenia”. Patrz punkt 7.
- Nie ma ekranu statusu. Status widgetu użytkownik widzi w panelu AsapChat.

## 4. Pola szablonu

Makieta: `docs/mockups/template.html`. Ekran pól rysuje GTM. Nie da się go ostylować. Jedyny nasz element graficzny to ikona szablonu (znak AsapChat).

Etykiety, teksty pomocy i komunikaty błędów są po angielsku, bo galeria jest globalna. Wpisuj je dokładnie tak jak w tabelach, znak po znaku.

| Etykieta (EN) | Nazwa | Typ | Walidacja w definicji pola | Podpowiedź w pustym polu (`valueHint`) | Domyślnie | Widoczne |
|---|---|---|---|---|---|---|
| Shop ID | `shopId` | TEXT | `NON_EMPTY`, `REGEX` `^[1-9][0-9]*$` | `e.g. 1284` | puste | zawsze |
| Widget token | `token` | TEXT | `NON_EMPTY`, `REGEX` `^[A-Za-z0-9._~-]+$` | `e.g. 9f2c41d7a0b35e6688c1d4f7a2b90e13` | puste | zawsze |
| Load chat | `chatEnabled` | CHECKBOX | brak | brak | zaznaczone | zawsze |
| Load AsapSearch | `searchEnabled` | CHECKBOX | brak | brak | zaznaczone | zawsze |
| Allow add to cart from search | `cartEnabled` | CHECKBOX | brak | brak | zaznaczone | tylko gdy „Load AsapSearch” jest zaznaczone (`enablingConditions`: `searchEnabled` równe `true`) |

Teksty pomocy i błędów:

| Pole | Tekst pomocy (EN) | Komunikat błędu (EN) |
|---|---|---|
| Shop ID | Copy it from your AsapChat panel: shop settings > Plugin. | Shop ID is a number, for example 1284. |
| Widget token | The public widget token. You will find it next to the Shop ID. | Paste the widget token from your AsapChat panel: shop settings > Plugin. |
| Load chat | The chat shows only on the domain set as the shop address in your AsapChat panel. | brak |
| Load AsapSearch | Product search for your shop. | brak |
| Allow add to cart from search | Visitors can add products to the cart straight from the search results. | brak |

Opis szablonu (pole Description w zakładce Info): `docs/marketplace-listing.md`, punkt 1.

Reguły:

1. ID sklepu to dodatnia liczba całkowita. Bez zera na początku, bez minusa, bez kropki.
2. Token nie może być pusty. Dozwolone znaki: litery `A-Z`, `a-z`, cyfry, `.`, `_`, `~`, `-`. Spacja, cudzysłów, `<`, `&` są błędem.
3. Walidator pola (`REGEX`) nie dopuszcza spacji, także na końcach: GTM nie pozwoli zapisać tagu z wklejoną wartością ze spacją i pokaże komunikat błędu. To zamierzone. Kod tagu mimo to przycina oba pola (`trim`) jako drugie zabezpieczenie: wartość może przyjść ze zmiennej GTM, której walidator pola nie sprawdza.
4. Kod tagu sprawdza te same reguły jeszcze raz (punkt 5). Powód: w pole można wstawić zmienną GTM, a jej wartość znamy dopiero na stronie.
5. „Allow add to cart from search” jest opcjonalne. Ma znaczenie tylko wtedy, gdy ładuje się wyszukiwarka.

## 5. Działanie tagu

```
1. shopId = tekst z pola, po trim. token = tekst z pola, po trim.
2. Sprawdź shopId: dodatnia liczba całkowita. Zły = gtmOnFailure, koniec.
3. Sprawdź token: niepusty, tylko dozwolone znaki. Zły = gtmOnFailure, koniec.
4. „Load chat” i „Load AsapSearch” oba odznaczone = gtmOnFailure, koniec. Tag nie ma czego załadować.
5. url = 'https://panel.asapchat.io/api/w/' + shopId + '.js?t=' + encodeUriComponent(token)
6. „Load chat” odznaczone: dopisz '&chat=0'.
7. „Load AsapSearch” odznaczone: dopisz '&search=0'.
8. „Load AsapSearch” zaznaczone i „Allow add to cart from search” odznaczone: dopisz '&cart=0'.
9. injectScript(url, data.gtmOnSuccess, data.gtmOnFailure, url)
```

Szkic kodu. Sprawdź go w edytorze szablonów, zanim uznasz za gotowy:

```js
const injectScript = require('injectScript');
const encodeUriComponent = require('encodeUriComponent');
const makeString = require('makeString');
const makeInteger = require('makeInteger');

const TOKEN_CHARS = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789._~-';

const shopId = makeString(data.shopId || '').trim();
const token = makeString(data.token || '').trim();

const idNumber = makeInteger(shopId);
const idOk = idNumber > 0 && makeString(idNumber) === shopId;

let tokenOk = token.length > 0;
for (let i = 0; i < token.length; i++) {
  if (TOKEN_CHARS.indexOf(token.charAt(i)) === -1) tokenOk = false;
}

if (!idOk || !tokenOk || (!data.chatEnabled && !data.searchEnabled)) {
  return data.gtmOnFailure();
}

let url = 'https://panel.asapchat.io/api/w/' + shopId + '.js?t=' + encodeUriComponent(token);
if (!data.chatEnabled) url += '&chat=0';
if (!data.searchEnabled) url += '&search=0';
if (data.searchEnabled && !data.cartEnabled) url += '&cart=0';

injectScript(url, data.gtmOnSuccess, data.gtmOnFailure, url);
```

Uwagi:

- Kolejność flag w adresie jest stała: `chat`, `search`, `cart`. Testy porównują cały adres.
- `&cart=0` dopisujesz tylko przy zaznaczonej wyszukiwarce. Ukryte pole może mieć starą wartość, dlatego kod sprawdza też `searchEnabled`.
- Czwarty argument `injectScript` (cacheToken) to ten sam adres. Dzięki temu loader ładuje się raz, nawet gdy tag odpali się dwa razy.
- Zły ID sklepu albo token: loader odpowiada `404`. `injectScript` woła wtedy `gtmOnFailure`, tag ma w podglądzie status „Failed”.
- Tag zawsze kończy się `gtmOnSuccess` albo `gtmOnFailure`. Nigdy nie zostaje bez odpowiedzi.
- Szablon nie zapisuje zmiennych globalnych i nie czyta ciasteczek.

## 6. Uprawnienia (`___WEB_PERMISSIONS___`)

| Uprawnienie | Zakres |
|---|---|
| `inject_script` | `https://panel.asapchat.io/api/w/*` |

To jedyne uprawnienie. Bez `access_globals`, bez `logging`.

## 7. Opóźnienie i wykluczenia: reguły GTM, nie pola szablonu

Sandboxed JavaScript nie ma `setTimeout`. `callLater` odkłada kod tylko do następnego kroku, nie o podany czas. Dlatego szablon nie ma pola opóźnienia. Wykluczeń też nie dublujemy, bo GTM ma je wbudowane.

Ta instrukcja trafia do README po angielsku (zadanie 06):

**Opóźnienie czatu o X sekund**

1. Triggers → New → typ „Timer”.
2. Interval: czas w milisekundach, np. `5000` dla 5 sekund. Limit: `1`.
3. Warunek włączenia: `Page URL` → `matches RegEx` → `.*` (wszystkie strony).
4. Podepnij tę regułę pod tag AsapChat zamiast „All Pages”.

**Wykluczenie stron (tag na „All Pages”)**

1. Triggers → New → typ „Page View” → „Some Page Views”.
2. Warunek: `Page Path` → `contains` → np. `/cart`.
3. W tagu AsapChat dodaj tę regułę w sekcji „Exceptions”.

**Opóźnienie i wykluczenie naraz**

Wyjątek typu „Page View” nie zatrzyma reguły „Timer”, bo to dwa różne zdarzenia. Warunek wpisz w samej regule „Timer”: `Page Path` → `does not contain` → `/cart`.

**Opóźniony tylko czat, wyszukiwarka od razu**

Dwa tagi z tego samego szablonu. Pierwszy: tylko „Load AsapSearch”, reguła „All Pages”. Drugi: tylko „Load chat”, reguła „Timer”.

## 8. Domena sklepu

Czat pojawia się tylko na domenie ustawionej jako adres sklepu w panelu AsapChat albo na domenie połączonej wtyczką AsapChat. Na innej domenie czatu nie ma, nawet gdy tag ma status „Succeeded”. W tekstach dla użytkownika szablonu zostaje prostsze zdanie o adresie sklepu z panelu.

Ta informacja jest w trzech miejscach: w tekście pomocy pola „Load chat”, w README i w opisie w galerii.

## 9. Testy szablonu (`___TESTS___`)

Uruchamiasz je w edytorze szablonów przyciskiem „Run tests”. W każdym teście podmieniasz `injectScript` atrapą i zapisujesz, z czym została wywołana:

```js
// Setup wspólny dla testów
let calledUrl, calledToken, calls = 0;
mock('injectScript', function(url, onSuccess, onFailure, cacheToken) {
  calls++;
  calledUrl = url;
  calledToken = cacheToken;
  onSuccess();
});

// Przykład testu
runCode({shopId: '1284', token: 'abc123', chatEnabled: true, searchEnabled: false, cartEnabled: true});
assertThat(calledUrl).isEqualTo('https://panel.asapchat.io/api/w/1284.js?t=abc123&search=0');
assertThat(calledToken).isEqualTo(calledUrl);
assertApi('gtmOnSuccess').wasCalled();
```

Lista testów. Baza adresu: `https://panel.asapchat.io/api/w/1284.js?t=abc123`. Gdy tabela nie mówi inaczej, `cartEnabled` jest `true`.

| Nr | Dane | Oczekiwany wynik |
|---|---|---|
| 1 | chat tak, search tak, cart tak | adres = baza, `gtmOnSuccess` |
| 2 | chat nie, search tak | adres = baza + `&chat=0` |
| 3 | chat tak, search nie | adres = baza + `&search=0` |
| 4 | chat nie, search nie | `injectScript` nie wołany, `gtmOnFailure` |
| 5 | `shopId` pusty | `injectScript` nie wołany, `gtmOnFailure` |
| 6 | `shopId` = `sklep-12`, potem `0`, `-5`, `12.5`, `012` | jak w teście 5, dla każdej wartości |
| 7 | `token` pusty | jak w teście 5 |
| 8 | `token` = `abc 123`, potem `abc&x=1`, `<script>` | jak w teście 5 |
| 9 | `shopId` = ` 1284 `, `token` = ` abc123 ` (spacje na końcach, podane wprost w `runCode`, tak jak przyszłyby ze zmiennej GTM; walidator pola w testach nie działa) | adres = baza, bez spacji |
| 10 | atrapa woła `onFailure` zamiast `onSuccess` | `gtmOnFailure`, `gtmOnSuccess` nie wołany |
| 11 | dowolny poprawny zestaw | czwarty argument `injectScript` równy adresowi |
| 12 | chat tak, search tak, cart nie | adres = baza + `&cart=0` |
| 13 | chat tak, search nie, cart nie | adres = baza + `&search=0`, bez `&cart=0` |
| 14 | chat nie, search tak, cart nie | adres = baza + `&chat=0&cart=0` |

Przydatne asercje: `assertApi('injectScript').wasNotCalled()`, `assertApi('gtmOnFailure').wasCalled()`, `assertApi('gtmOnSuccess').wasNotCalled()`.

## 10. Testy ręczne (wyniki w komentarzu do issue)

Potrzebujesz: kontener testowy GTM, strona testowa i darmowe konto AsapChat (`docs/accounts.md`). Strona testowa to publiczna strona `https`, którą kontrolujesz (np. GitHub Pages), nie `localhost`. Do testu 7 druga strona na innej domenie (drugi darmowy hosting).

Wyniki zapisujesz w komentarzu do issue zadania 05: tabela z 11 testami (numer, wynik, zrzut albo jedno zdanie). PR otwierasz tylko wtedy, gdy trzeba poprawić instrukcję albo spec.

1. Podgląd GTM: tag ma status „Succeeded”, czat widać na stronie.
2. Odznacz „Load chat”: w zakładce Network adres loadera ma `&chat=0`, czatu nie ma.
3. Odznacz „Load AsapSearch”: adres loadera w zakładce Network ma `&search=0`. Pole „Allow add to cart from search” znika z formularza.
4. Zaznacz „Load AsapSearch”, odznacz „Allow add to cart from search”: adres loadera ma `&cart=0`.

   Testy 3 i 4 na stronie testowej kończą się na adresie. Wyszukiwarka potrzebuje zaimportowanych produktów, a strona testowa nie jest sklepem, więc wyników wyszukiwania tam nie ma. Dodatkowo pobierz adres loadera z flagą i bez flagi (`curl`) i zapisz w komentarzu z wynikami, czy odpowiedź się różni (na koncie bez produktów loader nie dokłada wyszukiwarki, więc różnicy może nie być: wtedy to zapisz). Część wizualną (brak wyszukiwarki, brak przycisku koszyka w wynikach) sprawdza właściciel repo (`prz10`) na prawdziwym sklepie przed wysłaniem zgłoszenia.
5. Zły token: loader zwraca `404`, tag ma status „Failed”.
6. Tag z dwiema regułami na tej samej stronie: jeden czat, jedno żądanie loadera.
7. Strona na domenie innej niż adres sklepu w panelu AsapChat: czatu nie ma. Zapisz status tagu i to, co widać w konsoli.
8. Zmień kolor w panelu AsapChat. Po maksymalnie 5 minutach nowy kolor jest na stronie, bez zmian w GTM.
9. Reguła „Timer” 5000 ms, limit 1: czat pojawia się po 5 sekundach.
10. Wyjątek „Page Path contains /cart” przy regule „All Pages”: na tej stronie nie ma żądania loadera.
11. Szerokość okna 390 px: pola szablonu i czat są czytelne.

## 11. Wymagania Community Template Gallery

Pełna lista: `docs/marketplace-listing.md`. Najważniejsze:

- `template.tpl` w katalogu głównym repo. Eksport z edytora szablonów.
- `metadata.yaml` w katalogu głównym: `homepage`, `documentation`, lista `versions` (pełny `sha` commita i `changeNotes`).
- `LICENSE` w katalogu głównym: Apache License 2.0. Galeria nie przyjmuje innej licencji.
- W szablonie zaakceptowane warunki galerii: pole wyboru w zakładce Info edytora, w pliku sekcja `___TERMS_OF_SERVICE___`.
- Repo publiczne: `asapdevs-it/asapchat-gtm-template`. Wydawca: AsapCommerce.

```yaml
homepage: "https://asapchat.io"
documentation: "https://github.com/asapdevs-it/asapchat-gtm-template"
versions:
  - sha: <pełny sha commita z main, w którym template.tpl ma zgłaszaną treść>
    changeNotes: Initial release.
```

Zasada `sha`: `AGENTS.md`, reguła 6. W skrócie: do pierwszego zgłoszenia jest jeden wpis z tymczasowym sha (40 zer), prawdziwy sha wpisuje zadanie 07 osobnym jednolinijkowym PR. Po publikacji nowa wersja szablonu = nowy wpis na górze listy `versions`, dodany jednolinijkowym PR po merge'u zmiany.

Ryzyko: galeria może odrzucić zgłoszenie. Poprawiamy wtedy według uwag z maila i zgłaszamy ponownie. To nie wstrzymuje kodu.

## 12. Definicja ukończenia

- `template.tpl` importuje się do czystego kontenera. Ma pięć pól z punktu 4 i jedno uprawnienie z punktu 6.
- Etykiety, teksty pomocy i komunikaty błędów są po angielsku, identyczne z tabelami w punkcie 4.
- Testy 1 do 14 z punktu 9 są zielone w edytorze. Zrzut jest w PR.
- Testy ręczne 1 do 11 z punktu 10 są opisane ze zrzutami w komentarzu do issue zadania 05.
- W katalogu głównym są `template.tpl`, `metadata.yaml`, `LICENSE` (Apache 2.0) i README po angielsku.
- README ma instrukcję instalacji, informację o domenie (punkt 8) oraz instrukcję opóźnienia i wykluczeń (punkt 7).
- Warunki galerii są zaakceptowane w szablonie.
- Szablon ma ikonę AsapChat.
- W repo nie ma tokenów, danych klientów ani treści wewnętrznych.
- Zgłoszenie do galerii jest wysłane (wysyła właściciel repo z konta wydawcy, deweloper przygotowuje wszystko do tego kroku).

## 13. Otwarte pytania (nie blokują kodu)

1. Adres dokumentacji w `metadata.yaml`. Na start adres repo. Gdy powstanie strona z instrukcją na asapchat.io, podmieniamy.
2. Adres wsparcia do README: zgłoszenia w tym repo, https://github.com/asapdevs-it/asapchat-gtm-template/issues .

## Zadania

Kolejność wykonania to prefiks w tytule (`01` do `07`), nie numer GitHuba. Etykiety z taksonomii w `SDLC.md`. Treść zadań jest w issues na GitHubie, poniżej ich kryteria.

### 01 Szkielet repo: LICENSE, metadata.yaml, CI

Etykiety: `feature`, `skip-qa`, `priority-high`, `risk-low`.

- W katalogu głównym jest `LICENSE` z pełną treścią Apache License 2.0.
- W katalogu głównym jest `metadata.yaml` z `homepage`, `documentation` i jednym wpisem `versions` z tymczasowym sha (40 zer). Prawdziwy sha wpisuje zadanie 07.
- Workflow GitHub Actions sprawdza składnię `metadata.yaml` na każdym PR.
- Jest pusty `template.tpl` wyeksportowany z edytora (sam szkielet sekcji), żeby kolejne PR miały czytelny diff.

### 02 Pola szablonu, ikona i uprawnienia

Etykiety: `feature`, `needs-qa`, `priority-high`, `risk-low`.

- Szablon ma dokładnie pięć pól z punktu 4. Nazwy, typy, wartości domyślne, podpowiedzi (`valueHint`), angielskie etykiety, teksty pomocy i komunikaty błędów są zgodne z tabelami.
- „Allow add to cart from search” widać tylko przy zaznaczonym „Load AsapSearch”.
- GTM nie pozwala zapisać tagu z „Shop ID” = `sklep-12`, `0` albo pustym, ani z pustym „Widget token” lub tokenem ze spacją (w środku albo na końcu).
- Jedyne uprawnienie to `inject_script` dla `https://panel.asapchat.io/api/w/*`.
- Zakładka Info: nazwa, opis, kategorie i ikona według `docs/marketplace-listing.md`, punkt 1. Ikonę PNG robisz ze znaku w `docs/assets/`. Warunki galerii zaznaczone. Zrzut pól na szerokości 390 px i 1280 px jest w PR.

### 03 Kod tagu: adres loadera i injectScript

Etykiety: `feature`, `needs-qa`, `priority-high`, `risk-medium`.

- Tag buduje adres według punktu 5. Token przechodzi przez `encodeUriComponent`.
- Odznaczone „Load chat” dopisuje `&chat=0`, odznaczone „Load AsapSearch” dopisuje `&search=0`.
- `&cart=0` pojawia się tylko wtedy, gdy „Load AsapSearch” jest zaznaczone, a „Allow add to cart from search” odznaczone.
- Błędny `shopId`, błędny token albo „Load chat” i „Load AsapSearch” oba odznaczone: `gtmOnFailure`, bez wywołania `injectScript`.
- `injectScript` dostaje adres jako czwarty argument. Każda ścieżka kodu kończy się `gtmOnSuccess` albo `gtmOnFailure`.
- Sekcja `___TESTS___` ma test 1 z punktu 9 (poprawny adres, `gtmOnSuccess`) z atrapą `injectScript`. „Run tests” jest zielone, zrzut jest w PR. Pozostałe testy dodaje zadanie 04.

### 04 Testy szablonu w edytorze

Etykiety: `feature`, `skip-qa`, `priority-medium`, `risk-low`.

- Sekcja `___TESTS___` zawiera testy 1 do 14 z punktu 9 (test 1 jest już z zadania 03, tu dochodzą 2 do 14).
- `injectScript` jest w testach atrapą (`mock`). Żaden test nie robi prawdziwego żądania.
- „Run tests” pokazuje wszystkie testy na zielono. Zrzut jest w PR.
- `AGENTS.md`, sekcja „Walidacja”, opisuje aktualny sposób uruchomienia testów.

### 05 Testy ręczne na stronie testowej

Etykiety: `feature`, `needs-qa`, `priority-medium`, `risk-low`.

- Testy ręczne 1 do 11 z punktu 10 są wykonane w kontenerze testowym. Wyniki są w komentarzu do issue: tabela, każdy test ma zrzut albo jedno zdanie wyniku. PR powstaje tylko wtedy, gdy instrukcja albo spec wymagają poprawek. Testy 3 i 4 w zakresie opisanym pod listą (adres loadera i odpowiedź z `curl`).
- Wynik testu 7 (inna domena) jest opisany w tym samym komentarzu jednym akapitem po angielsku, gotowym do wklejenia w README (zadanie 06 bierze go stamtąd).
- Instrukcja z punktu 7 (Timer, wyjątki) jest sprawdzona krok po kroku. Różnice w nazwach przycisków GTM są poprawione w specyfikacji.
- Znalezione błędy mają osobne issues z etykietą `bug`.

### 06 README po angielsku

Etykiety: `documentation`, `skip-qa`, `priority-medium`, `risk-low`.

- `README.md` w katalogu głównym jest po angielsku, według szkicu w `docs/marketplace-listing.md`, punkt 2.
- README mówi, skąd wziąć Shop ID i Widget token (panel AsapChat: shop settings > Plugin) i że wygląd zmienia się w panelu, z opóźnieniem do 5 minut.
- README mówi, że czat pojawia się tylko na domenie ustawionej jako adres sklepu w panelu AsapChat.
- README ma sekcję o opóźnieniu i wykluczeniach z punktu 7.
- Sekcja „Support” prowadzi do zgłoszeń repo: https://github.com/asapdevs-it/asapchat-gtm-template/issues .
- Obecny polski README dla deweloperów jest przeniesiony do `docs/README-dev.md`, a `ONBOARDING-DEV.md` i `AGENTS.md` wskazują ten plik.

### 07 Zgłoszenie do galerii

Etykiety: `feature`, `skip-qa`, `priority-medium`, `risk-medium`.

- `metadata.yaml` ma pełny `sha` commita z `main`, w którym `template.tpl` ma zgłaszaną treść (osobny jednolinijkowy PR po merge'u ostatniej zmiany szablonu).
- Checklisty z `docs/marketplace-listing.md`, punkty 3 i 4, są odhaczone w issue.
- Pliki i historia commitów są przejrzane: bez tokenów, bez danych klientów, bez treści wewnętrznych.
- Issue jest przypisane do `prz10` z komentarzem „ready for owner”. Na tym kończy się praca dewelopera.
- Właściciel repo wysyła zgłoszenie z konta Google wydawcy i wpisuje datę zgłoszenia w issue. Część wizualną testów ręcznych 3 i 4 sprawdza przedtem na prawdziwym sklepie.

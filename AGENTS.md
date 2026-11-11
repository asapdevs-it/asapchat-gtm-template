# AGENTS.md: szablon GTM AsapChat

Proces: `SDLC.md`. Zakres: `.ai/specs/2026-11-11-connector-v1.md`. Kontrakt loadera: `docs/CONNECT-API.md`, punkt 6.

To repo jest publiczne. Każdy plik, commit, issue i PR widzi cały świat.

## Stack

- Jeden plik `template.tpl` (format eksportu edytora szablonów GTM: sekcje `___TERMS_OF_SERVICE___`, `___INFO___`, `___TEMPLATE_PARAMETERS___`, `___SANDBOXED_JS_FOR_WEB_TEMPLATE___`, `___WEB_PERMISSIONS___`, `___TESTS___`).
- Sandboxed JavaScript: ograniczony JavaScript GTM. Brak `window`, `document`, `setTimeout`, `new`, DOM. Dostęp do świata tylko przez `require('nazwaApi')`.
- Brak npm, brak bundlera, brak zależności.

## Układ repo (wymóg galerii: pliki w katalogu głównym)

```
template.tpl      szablon, edytowany w edytorze GTM i eksportowany
metadata.yaml     strona, dokumentacja, lista wersji (sha commita + opis zmian)
LICENSE           Apache 2.0 (wymóg galerii)
README.md
docs/ .ai/        dokumentacja dla deweloperów (galeria ją ignoruje)
```

## Reguły

1. Szablon edytuj w edytorze GTM (Templates > New), testuj w podglądzie, eksportuj do `template.tpl`. Ręczna edycja pliku tylko dla drobnych poprawek tekstów.
2. Jedno uprawnienie: `inject_script` dla `https://panel.asapchat.io/api/w/*`. Bez `access_globals`, bez `logging`, bez gwiazdek na cały host.
3. Tag ładuje tylko loader: `https://panel.asapchat.io/api/w/{shopId}.js?t={token}` plus flagi `&chat=0`, `&search=0`, `&cart=0`. Token zawsze przez `encodeUriComponent`.
4. Zawsze wołaj `data.gtmOnSuccess()` albo `data.gtmOnFailure()`. Tag, który nie kończy, blokuje sekwencje w kontenerze.
5. Wartości pól waliduj w definicji pól (`NON_EMPTY`, `REGEX`) i drugi raz w kodzie tagu. Błędna wartość w kodzie = `gtmOnFailure`.
6. Każda zmiana `template.tpl` = nowy wpis w `metadata.yaml` (sha commita, w którym zmienił się szablon).
7. Sekcja `___TESTS___` musi przechodzić w edytorze („Run tests”) przed PR.
8. Wszystko, co widzi użytkownik szablonu, jest po angielsku: etykiety pól („Shop ID”, „Widget token”, „Load chat”, „Load AsapSearch”, „Allow add to cart from search”), teksty pomocy, komunikaty błędów, opis szablonu, README. Teksty bierz znak po znaku ze specyfikacji, punkt 4. Dokumenty planistyczne w `docs/` i `.ai/` są po polsku.

## Walidacja

Nie ma CLI do uruchomienia szablonu. Przed PR:

```
# 1. składnia metadata
python3 -c "import yaml,sys; yaml.safe_load(open('metadata.yaml'))"
# 2. ręcznie: import template.tpl do kontenera testowego, Run tests, podgląd na stronie testowej
```

Wynik kroku 2 (zrzut z zielonymi testami i z podglądu) wklej do PR.

## Agentowi nie wolno

- Używać tagu Custom HTML zamiast szablonu (galeria tego nie przyjmie).
- Rozszerzać uprawnień ponad regułę 2.
- Dodawać pól wyglądu, opóźnienia albo wykluczeń. Wygląd jest w panelu AsapChat, opóźnienie i wykluczenia w regułach GTM.
- Wpisywać w szablon, testy, dokumenty, issues albo zrzuty ID lub tokenu prawdziwego sklepu. W przykładach używaj `1284` i `abc123`.
- Wpisywać do repo haseł, kluczy, danych klientów ani treści wewnętrznych. Na zrzutach zasłoń adresy e-mail i identyfikatory kont.
- Obchodzić braków loadera w szablonie. Potrzebna zmiana loadera = issue w tym repo z opisem, czego brakuje.

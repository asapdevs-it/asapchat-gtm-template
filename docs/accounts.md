# Konta potrzebne do pracy nad szablonem GTM

Galeria szablonów GTM nie ma „konta wydawcy” z weryfikacją firmy i nic nie kosztuje. Do pracy potrzebujesz trzech rzeczy: kontenera testowego GTM, darmowego konta AsapChat i dostępu do repo.

To repo jest publiczne. Loginów, haseł, adresów e-mail kont ani identyfikatorów kontenerów nie wpisujesz do plików, issues ani PR.

## 1. Kontener testowy GTM

1. Wejdź na https://tagmanager.google.com i utwórz konto GTM z kontenerem typu Web. Wystarczy Twoje własne konto Google.
2. Wstaw kod kontenera na stronę testową. Strona testowa to dowolna publiczna strona `https`, którą kontrolujesz (wystarczy GitHub Pages albo inny darmowy hosting). `localhost` nie wystarcza: czat pojawia się tylko na domenie ustawionej jako adres sklepu w panelu AsapChat.
   Do testu ręcznego 7 (inna domena) potrzebujesz drugiej strony na innej domenie: drugi darmowy hosting z tym samym kodem kontenera.
3. Szablon importujesz w: Templates > New > menu z trzema kropkami > Import.

## 2. Konto AsapChat do testów

1. Załóż własne darmowe konto: https://panel.asapchat.io . Plan Free wystarcza.
2. Jako adres sklepu podaj adres swojej strony testowej. Czat pojawia się tylko na tej domenie.
   Wyszukiwarka wymaga zaimportowanych produktów, a strona testowa nie jest sklepem, więc wyników wyszukiwania na niej nie zobaczysz. Jak wtedy wyglądają testy ręczne 3 i 4: spec, punkt 10.
3. Shop ID i Widget token skopiuj z panelu: edycja sklepu, link „Wtyczka”. Te dwie wartości wpisujesz w pola tagu.
4. Prawdziwych wartości nie wklejaj do repo. W przykładach używaj `1284` i `abc123`.

## 3. Repo GitHub

- Repo: `asapdevs-it/asapchat-gtm-template`. Publiczne.
- Wydawca szablonu: AsapCommerce. W galerii jako autor widoczna jest organizacja GitHub `asapdevs-it`.
- Zmiany wchodzą przez PR do `main`.

## 4. Zgłoszenie do galerii

Zgłoszenie wysyła się z nowego konta Google wydawcy (AsapCommerce), zakładanego na potrzeby tego szablonu, według `docs/marketplace-listing.md`, punkt 6. Na to konto przychodzą maile z przeglądu. Dane tego konta nie są w repo i deweloper ich nie dostaje.

Podział pracy: deweloper wykonuje kroki 1 i 2 z punktu 6, odhacza checklisty w issue zadania 07 i przypisuje issue do `prz10` z komentarzem „ready for owner”. Krok 3 (logowanie kontem wydawcy i wysłanie adresu repo) wykonuje właściciel repo (`prz10`) i wpisuje datę zgłoszenia w issue.

## Checklista

- [ ] Kontener testowy GTM z kodem na stronie testowej
- [ ] Darmowe konto AsapChat, adres sklepu = adres strony testowej
- [ ] Shop ID i Widget token skopiowane z panelu, nigdzie nie wklejone do repo
- [ ] Dostęp do repo i pierwszy PR

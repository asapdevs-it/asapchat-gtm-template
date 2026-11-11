# Loader AsapChat

Jedyny adres, z którego korzysta ten szablon. Loader jest częścią usługi AsapChat.

## Loader

Jeden adres JS, który sam dokłada czat i wyszukiwarkę z aktualnymi atrybutami:

```
https://panel.asapchat.io/api/w/{shop_id}.js?t={token_widgetu}
```

- `t` to publiczny token widgetu (ten sam, który jest w zwykłym kodzie do wklejenia), nie `ack_...`.
- `&chat=0` albo `&search=0` wyłącza jeden widget, `&cart=0` wyłącza dodawanie do koszyka z wyszukiwarki.
- Loader działa na publicznym tokenie widgetu. Żeby wyłączyć widgety na stronie, użytkownik wstrzymuje albo usuwa tag w GTM i publikuje kontener.
- Czat pokaże się tylko na domenie sklepu z panelu AsapChat albo na domenie połączonej wtyczką.
- Odpowiedź ma `Cache-Control: public, max-age=300`. Serwer nie trzyma kopii: zmiana wyglądu w panelu jest widoczna po maks. 5 minutach (cache przeglądarki).
- Zły `shop_id` albo `t`: `404` z komentarzem JS (`/* AsapChat: nieznany sklep */`). Odpowiedź `404` nie jest cache'owana.
- Wszystkie widgety wyłączone flagami: `200` z komentarzem JS, nic się nie ładuje.

ID sklepu i token widgetu użytkownik kopiuje z panelu AsapChat: edycja sklepu, link „Wtyczka”.

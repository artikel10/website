---
title: Sicherer DNS Resolver
date: 2020-12-01T12:32:55+01:00
type: post
tags: [dienste, dns]
---

Bevor ein Webbrowser z.B. `artikel10.org` anzeigen kann, muss er die IP-Adresse
des Webservers herausfinden und befragt das [Domain Name System][]. Dies
geschieht leider häufig immer noch unverschlüsselt, was DNS anfällig für
Überwachung und Manipulation macht.

Um diese Schutzlücke zu schliessen, wird seit einigen Jahren an den Standards
[DNS over TLS][] und [DNS over HTTPS][] gearbeitet. Artikel10 betreibt unter
[dns.artikel10.org][] einen DNS Resolver, der beide Standards unterstützt.
Unsere [Tor Relays][] verwenden diesen DNS Resolver ebenfalls, so dass das
[Anonymity Set][] ausreichend groß sein dürfte.

Bitte beachtet: als ehrenamtliches Projekt können wir keine Hochverfügbarkeit
zusichern. Daher empfehlen wir, noch weitere sichere DNS Resolver im Inventar
zu haben, wie z.B. den von [Applied Privacy][].

[anonymity set]: https://privacypatterns.org/patterns/Anonymity-set
[applied privacy]: https://applied-privacy.net/de/services/dns/
[dns over https]: https://de.wikipedia.org/wiki/DNS_over_HTTPS
[dns over tls]: https://de.wikipedia.org/wiki/DNS_over_TLS
[dns.artikel10.org]: https://dns.artikel10.org/
[domain name system]: https://de.wikipedia.org/wiki/Domain_Name_System
[tor relays]: /dienste/tor-relays/

---
title: DNS-over-TLS mit OpenWrt
date: 2020-01-19T13:12:41+01:00
type: post
tags: [dns-over-tls, dot, dns, openwrt]
---

Webseiten ohne HTTPS sind zum Glück selten geworden. Allerdings werden
[DNS][]-Anfragen, die einem Webbrowser die IP-Adresse einer Webseite verraten,
in der Regel immer noch unverschlüsselt versendet. Dies macht sie anfällig für
Überwachung und Manipulation, was [DNS-over-TLS][] (DoT) verhindern möchte.

Für [OpenWrt]-Router ist es seit Version 19.07 verhältnismäßig einfach, das
private Netzwerk mit DoT abzusichern. Hierzu wird [Unbound][] installiert und
sendet öffentliche DNS-Anfragen an den DoT Server der Wahl.

Und das geht so:

## Unbound installieren

Neue Softwarepakete werden unter _System / Software_ installiert:

[![OpenWrt Softwaremanager](../openwrt-software.png)](../openwrt-software.png)

* Zuerst mit _Update lists..._ die Liste der verfügbaren Pakete aktualisieren.
* Dann folgende Pakete (in dieser Reihenfolge) installieren:
   * `ca-bundle`
   * `libunbound` bzw. `libunbound-light`
   * `unbound-daemon`
   * `luci-app-unbound`

## dnsmasq konfigurieren

dnsmasq wird unter _Network / DHCP and DNS_ konfiguriert:

[![OpenWrt dnsmasq-Konfiguration](../openwrt-dnsmasq.png)](../openwrt-dnsmasq.png)

* _General Settings_: bei _DNS forwardings_ den Eintrag `127.0.0.1#1053` hinzufügen.
* _Resolv and Hosts Files_: die Option _Ignore resolv file_ aktivieren.
* Die Änderungen mit _Save_ abspeichern.

Zu diesem Zeitpunkt sind die Änderungen nur abgespeichert. Sobald sie aktiviert
werden, leitet dnsmasq öffentliche DNS-Anfragen an den lokalen Port 1053 weiter
(auf dem in Kürze Unbound läuft) und ignoriert die vom Internetprovider
konfigurierten DNS-Server.

## Unbound konfigurieren

Unbound wird unter _Services / Recursive DNS_ konfiguriert:

[![OpenWrt Unbound-Konfiguration](../openwrt-unbound.png)](../openwrt-unbound.png)

* _Basic_: den _Listening Port_ auf `1053` setzen.
* Alle bisherigen Änderungen mit _Save & Apply_ aktivieren.

Zu diesem Zeitpunkt übernimmt Unbound öffentliche DNS-Anfragen, verwendet aber
noch kein DNS-over-TLS.

## DNS-over-TLS konfigurieren

Die Verwendung von DNS-over-TLS wird in den Unbound-Einstellungen unter _Zones_
konfiguriert. Alle vorkonfigurierten Zonen sollten inaktiv sein (kein Haken bei
_Enabled_) und können gelöscht werden.

Eine neue Zone anlegen und konfigurieren:

| Einstellung | Beschreibung |
| ----------- | ------------ |
| _Enabled_ | Option aktivieren. |
| _Zone Type_ | `Forward` |
| _Zone Names_ | `.` |
| _Servers_ | IP-Adressen des DoT Servers eintragen. |
| _DNS over TLS_ | Option aktivieren. |
| _TLS Name Index_ | Hostnamen des DoT Servers eintragen. |

Beispiel für [`dot1.applied-privacy.net`](https://applied-privacy.net/services/dns/):

[![OpenWrt Unbound Zone](../openwrt-unbound-zone.png)](../openwrt-unbound-zone.png)

Nachdem die Änderungen mit _Save & Apply_ aktiviert wurden, muss Unbound unter
_Status / Processes_ neugestartet werden:

[![OpenWrt Prozessmanager](../openwrt-processes.png)](../openwrt-processes.png)

* Unbound (_/usr/sbin/unbound_) per _Hang Up_ neustarten.

Zu diesem Zeitpunkt sollten öffentliche DNS-Anfragen an den DoT Server gehen.
Dies lässt sich mit dem [DNS Leak Test][] prüfen, der eine der konfigurierten
IP-Adressen anzeigen sollte.

Idealerweise validiert der DoT Server auch [DNSSEC][], was z.B. der
[DNSSEC Resolver Test][] anzeigt.

Bei Problemen kann die Zone deaktiviert und Unbound neugestartet werden.
Öffentliche DNS-Anfragen werden dann allerdings wieder unverschlüsselt
versendet.

## OpenWrt-Updates

Nach dem Einspielen eines neuen OpenWrt-Images müssen die oben genannten
Softwarepakete neu installiert werden. Dafür sind die beiden Einstellungen
unter [dnsmasq konfigurieren](#dnsmasq-konfigurieren) vorübergehend rückgängig
zu machen.

Wenn Unbound installiert ist, kann dnsmasq wieder wie beschrieben konfiguriert
werden. Der [DNS Leak Test][] sollte dann den gewählten DoT Server anzeigen!

---

Basierend auf [DNS over TLS with Unbound][].

[dns leak test]: https://dnsleaktest.com/
[dns over tls with unbound]: https://openwrt.org/docs/guide-user/services/dns/dot_unbound
[dns-over-tls]: https://de.wikipedia.org/wiki/DNS_over_TLS
[dns]: https://de.wikipedia.org/wiki/Domain_Name_System
[dnssec resolver test]: https://dnssec.vs.uni-due.de/
[dnssec]: https://de.wikipedia.org/wiki/Domain_Name_System_Security_Extensions
[openwrt]: https://openwrt.org/
[unbound]: https://openwrt.org/docs/guide-user/services/dns/unbound

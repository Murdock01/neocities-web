+++
date = '2025-10-24T15:13:02+02:00'
draft = false
title = 'GlassWorm Schadcode'
tags = ['IT-Security']
+++

# Glassworm - ein neues Erdbeben in der IT-Landschaft?
### Seit ein paar Tagen frisst sich eine neue Schadsoftware durch die Gemeinde der Softwareentwickler. Die Entdecker, das israelisch-amerikanische SW-Sicherheitsunternehmen KOI, haben sie "Glassworm" getauft, als Anspielung auf die Eigenschaft, sich durch ***unsichtbaren Code*** zu verstecken.
https://www.koi.ai/blog/glassworm-first-self-propagating-worm-using-invisible-code-hits-openvsx-marketplace

Die *Glassworm* getaufte Malware ist nicht nur ein einfacher Wurm, sondern einer der fortschrittlichsten Angriffe auf die Software-Lieferkette, den es je gab.
Er verbreitet sich über Erweiterungen des, unter Entwicklern sehr weit verbreiteten, Codeeditors VS-Code und dessen Abkömmlingen (wie VS Codium).
Die Malware nutzt dabei ein Mix fortschrittlicher Techniken, die in dieser Kombination bei Malware neu ist.

- Nicht sichtbarer Code in den betroffenen Extensions durch Benutzung nicht darstellbarer Unicode-Zeichen. Diese erschweren manuelle Code Reviews und machen ihn damit praktisch *unsichtbar*. Dazu benutzt der Wurm [Unicodeblock-Variantenselektoren](https://de.wikipedia.org/wiki/Unicodeblock_Variantenselektoren).
"Diese Technik bricht die traditionelle Codeüberprüfung komplett", sagte Idan Dardikman, Forscher bei KOI Security. "Man kann nicht erkennen, was man nicht sieht."
- Nachlade-Mechanismus, basierend auf Transaktionen in der Solana-Blockchain. Diese Transaktionen können, einmal in der Blockchain aufgenommen, nicht wieder gelöscht werden, sind also solange vorhanden, solange es die Blockchain gibt. In dieser Transaktion befindet sich der Verweis auf den C&C-Server mit dem Payload der nächsten Stufe. Wird dieser gesperrt oder beschlagnahmt, wird von den Malware-Betreibern einfach eine neue Transakktion mit einem Verweis auf einen anderen Server erzeugt, die Malware sucht stets nach der aktuellsten Transaktion.
- Eine Solana-Transaktion ist extrem billig, es fallen Transaktionskosten zwischen 0.0001 und 0.0025 USD an.
- Als Backup fungieren zusätzlich Google-Kalender-Einträge, die sich ohne kompletten Verzicht auf die Kalenderfunktion selbst in Firmennetzwerken nicht erfolgreich blockieren lassen.
-  Vom C&C-Server wird dann der eigentliche, mit AES-CBC-256 verschlüsselte, Payload heruntergeladen und installiert.
Dieser hat wiederum mehrere fortgeschrittene Funktionen.

    - Er sammelt NPM-, GitHub- und OpenVSX-Anmeldeinformationen
    - greift Browser-Wallet-Erweiterungen an (49 Wallet-Typen werden genannt)
    - richtet SOCKS-Proxies ein
    - installiert versteckte VNC-Dienste für Remotezugriff
    - nutzt Peer-to-peer-Mechanismen wie WebRTC und BitTorrent-DHT zur dezentralen Befehlsverteilung.
    
- In der letzten, von den Entdeckern und den Malware-Autoren ironischerweise "ZOMBI" genannten Stufe, verwandelt GlassWorm Entwicklerrechner in persistente, schwer detektierbare Infrastrukturknoten, über die Angreifer interne Netze ansteuern oder Transaktionen anonymisieren können.

## Automatisierte Verbreitung
Die Malware sucht auf den infizierten Systemen nach Credentials für verschiedene Entwickler-Plattformen und benutzt npm-Auth-Token, um sich über den Javascript-Paketmanager weiterzuverbreiten. Ausserdem sucht sie nach Github-Token und Git-Credentials, um weitere Repositories zu kompromittieren. Auch nach Zugangsdaten zu Open-VSX wird gesucht, um weitere VS Code-Erweiterungen zu infizieren.

Der [Beitrag auf der KOI Webseite](https://www.koi.ai/blog/glassworm-first-self-propagating-worm-using-invisible-code-hits-openvsx-marketplace) listet im Anhang die bisher bekannten, befallenen Erweiterungen auf. Diese Liste beinhaltet derzeit zwar eher obskure Extensions, es ist aber nur eine Frage der Zeit, bis Glassworm populärere Pakete befällt.

Gleichzeitig zeigt der Supply-Chain-Angriff erstmals deutlich, welche Möglichkeiten bestehen, mittels legitimer dezentraler Dienste eine C&C-Infrastruktur zu bauen, die sich nicht einfach abschalten lässt.

Der Angriff hat das Zeug dazu, die Schlagkraft und Reichweite anderer bekannter Attacken wie z.B. Shai Hulud nochmals deutlich zu übertreffen.
Die Entwicklergemeinde und die Betreiber von Software Marktplätzen und Code Repositories sind nun gefragt, wirksame Gegenmittel gegen solcherart Angriffe zu finden, wie z.B 2FA, granulare Access-Token für Repositories und "Trusted Publishing".

Die Lage bleibt ausgesprochen spannend.
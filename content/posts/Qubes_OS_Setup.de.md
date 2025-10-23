+++
date = '2025-10-22T10:36:59+02:00'
draft = true
title = 'Qubes OS Setup für Java-Entwickler'
tags = ['QubesOS']
+++

# QubesOS Setup für Java-Softwareentwickler

Dieses Dokument beschreibt ein sicheres und effizientes QubesOS-Setup für Softwareentwickler mit Schwerpunkt Java. Ziel ist es, Entwicklungs-, Build- und Sicherheitsprozesse klar zu trennen und gleichzeitig eine produktive Arbeitsumgebung zu schaffen.

---

## Grundprinzipien

- **Isolation durch VMs:** Trennung von Entwicklungsumgebungen, Build-Prozessen, Internetzugang und sensiblen Daten.
- **Sicherheit durch Kompartimentierung:** Schadsoftware in einer VM kann nicht auf andere VMs übergreifen.
- **Flexibilität durch TemplateVMs:** Wiederverwendbare Basis-Images für verschiedene Entwicklungsstacks.

---

## Qubes-Struktur für Java-Entwicklung

| Qube-Name      | Typ          | Zweck                                                                  |
| -------------- | ------------ | ---------------------------------------------------------------------- |
| `dev-java`     | AppVM        | Hauptentwicklungsumgebung mit JDK, Maven/Gradle, VS Code oder IntelliJ |
| `build-java`   | DisposableVM | Kompilierung und Packaging von Java-Projekten                          |
| `test-java`    | AppVM        | Ausführung von Tests, z. B. mit JUnit, TestNG                          |
| `vault-java`   | AppVM        | Speicherung von Keystores, Zertifikaten, GPG-Schlüsseln                |
| `git-sync`     | AppVM        | Git-VM mit Internetzugang, ohne Zugriff auf sensible Daten             |
| `doc-gen`      | AppVM        | Generierung von Javadoc, PDF-Dokumentation etc.                        |
| `sys-net`      | NetVM        | Netzwerkzugang                                                         |
| `sys-firewall` | FirewallVM   | Kontrolle über Netzwerkzugriffe der VMs                                |

---

## Tools und Konfiguration

- **JDKs:** Installation mehrerer Versionen (z. B. OpenJDK 8, 11, 17) in `dev-java`
- **Build-Tools:** Maven, Gradle, Ant
- **Editoren/IDEs:** VS Code mit Java-Erweiterung, IntelliJ IDEA
- **Test-Frameworks:** JUnit, TestNG, Mockito
- **Dokumentation:** Javadoc, AsciiDoc, PlantUML
- **Signierung:** Nutzung von `vault-java` für `jarsigner`, GPG, TLS-Zertifikate

---

## Beispielkonfigurationen

### Maven in dev-java

```bash
sudo dnf install maven
mvn --version
```

### IntelliJ IDEA Installation

```bash
# In TemplateVM (z. B. fedora-xx)
sudo dnf install snapd
sudo systemctl enable --now snapd.socket
sudo ln -s /var/lib/snapd/snap /snap
sudo snap install intellij-idea-community --classic
```

### JDKs verwalten mit alternatives

```bash
sudo dnf install java-1.8.0-openjdk java-11-openjdk java-17-openjdk
sudo alternatives --config java
```

### jarsigner mit Keystore aus vault-java

```bash
jarsigner -keystore ~/vault/java-keystore.jks -storepass geheim -keypass geheim -signedjar MeinProgramm_signed.jar MeinProgramm.jar meinAlias
```

### SSH-Agent-Forwarding für Git

In `vault-java`:

```bash
eval $(ssh-agent)
ssh-add ~/.ssh/id_rsa
```

In `git-sync`:

```bash
export SSH_AUTH_SOCK=/path/to/forwarded/socket
git clone git@github.com:meinprojekt.git
```

---

## Sicherheit und Best Practices

- Kein Internetzugang für `vault-java`
- Verwendung von `disposable:` VMs für Build-Prozesse
- SSH/GPG-Agent-Forwarding nur gezielt aktivieren
- Trennung von Quellcode und Build-Artefakten
- Verwendung von Qubes RPC für automatisierte Workflows

---

## Erweiterungen

- Integration mit CI/CD-Systemen über dedizierte Sync-VM
- SaltStack zur Verwaltung von TemplateVMs
- Automatisierte Javadoc-Generierung in `doc-gen`
- TLS-Zertifikatsverwaltung mit Java Keytool in `vault-java`

---

Dieses Setup bietet eine sichere, modulare und skalierbare Umgebung für professionelle Java-Entwicklung unter QubesOS.

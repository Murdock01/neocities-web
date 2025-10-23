+++
date = '2025-10-22T10:36:59+02:00'
draft = true
title = 'Qubes OS Setup for java developers'
tags = ['QubesOS']
+++

# QubesOS Setup for Java Software Developers

This document describes a secure and efficient QubesOS setup for software developers focused on Java. The goal is to clearly separate development, build, and security processes while providing a productive working environment.

---

## Core Principles

- **Isolation via VMs:** Separation of development environments, build processes, internet access, and sensitive data.
- **Security through Compartmentalization:** Malware in one VM cannot affect other VMs.
- **Flexibility via TemplateVMs:** Reusable base images for different development stacks.

---

## Qubes Structure for Java Development

| Qube Name      | Type         | Purpose                                                                  |
| -------------- | ------------ | ------------------------------------------------------------------------ |
| `dev-java`     | AppVM        | Main development environment with JDK, Maven/Gradle, VS Code or IntelliJ |
| `build-java`   | DisposableVM | Compilation and packaging of Java projects                               |
| `test-java`    | AppVM        | Execution of tests, e.g., with JUnit, TestNG                             |
| `vault-java`   | AppVM        | Storage of keystores, certificates, GPG keys                             |
| `git-sync`     | AppVM        | Git VM with internet access, without access to sensitive data            |
| `doc-gen`      | AppVM        | Generation of Javadoc, PDF documentation, etc.                           |
| `sys-net`      | NetVM        | Network access                                                           |
| `sys-firewall` | FirewallVM   | Control over network access of VMs                                       |

---

## Tools and Configuration

- **JDKs:** Install multiple versions (e.g., OpenJDK 8, 11, 17) in `dev-java`
- **Build Tools:** Maven, Gradle, Ant
- **Editors/IDEs:** VS Code with Java extension, IntelliJ IDEA
- **Test Frameworks:** JUnit, TestNG, Mockito
- **Documentation:** Javadoc, AsciiDoc, PlantUML
- **Signing:** Use `vault-java` for `jarsigner`, GPG, TLS certificates

---

## Example Configurations

### Maven in `dev-java`

```bash
sudo dnf install maven
mvn --version
```

### IntelliJ IDEA Installation

```bash
# In TemplateVM (e.g., fedora-xx)
sudo dnf install snapd
sudo systemctl enable --now snapd.socket
sudo ln -s /var/lib/snapd/snap /snap
sudo snap install intellij-idea-community --classic
```

### Manage JDKs with `alternatives`

```bash
sudo dnf install java-1.8.0-openjdk java-11-openjdk java-17-openjdk
sudo alternatives --config java
```

### `jarsigner` with keystore from `vault-java`

```bash
jarsigner -keystore ~/vault/java-keystore.jks -storepass secret -keypass secret -signedjar MyProgram_signed.jar MyProgram.jar myAlias
```

### SSH Agent Forwarding for Git

In `vault-java`:

```bash
eval $(ssh-agent)
ssh-add ~/.ssh/id_rsa
```

In `git-sync`:

```bash
export SSH_AUTH_SOCK=/path/to/forwarded/socket
git clone git@github.com:myproject.git
```

---

## Security and Best Practices

- No internet access for `vault-java`
- Use `disposable:` VMs for build processes
- Enable SSH/GPG agent forwarding only when needed
- Separate source code from build artifacts
- Use Qubes RPC for automated workflows

---

## Extensions

- Integration with CI/CD systems via dedicated sync VM
- Use SaltStack to manage TemplateVMs
- Automated Javadoc generation in `doc-gen`
- TLS certificate management with Java Keytool in `vault-java`

---

This setup provides a secure, modular, and scalable environment for professional Java development on QubesOS.

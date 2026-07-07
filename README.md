# swim-vmrequest-services

Gepinnter Mirror der Vorinstallations-Skripte für das SWIM-Moodle-Plugin
`mod_vmrequest` (Deliverable MI-2, Stream Moodle Integration). Ursprung:
[six7-click-n-deploy](https://github.com/six7-click-n-deploy) auf GitHub.

**Warum ein eigener Mirror:** Entkoppelt die VM-Provisionierung von der
Verfügbarkeit/Sichtbarkeit des Original-Repos (z. B. falls es privat gestellt
oder gelöscht wird) und macht Updates bewusst statt automatisch.

**Warum ein eigenes, separates Repo** (statt Teil des `vmrequest`-Plugin-Repos):
Das Plugin-Repo enthält an anderer Stelle produktive Zugangsdaten. Dieses Repo
enthält ausschließlich die Vorinstallations-Skripte und ist bewusst öffentlich
und credential-frei, damit VMs direkt (ohne Auth) daraus klonen können.

## Struktur

Jeder Unterordner entspricht einem Dienst-Repo aus `six7-click-n-deploy` und
enthält dessen Inhalt zum unten genannten Commit (`packer/scripts/provision.sh`
ist das eigentliche Installationsskript):

| Ordner | Ursprungs-Repo | Gepinnter Commit |
|---|---|---|
| `pgAdmin/` | six7-click-n-deploy/pgAdmin | `c5bd656fd003c929a43a4c5d44249620dbe7d648` |
| `Jupyter-Notebook/` | six7-click-n-deploy/Jupyter-Notebook | `bc9ba39ca73f4af38e36823902955b16391688ae` |
| `Online-IDE/` | six7-click-n-deploy/Online-IDE | `b2730fc8a6a3c417ad1306aeb95b0d6f1bdd4c96` |
| `Web-LaTeX/` | six7-click-n-deploy/Web-LaTeX | `74c43ee27f17ba6f1f6c5834d61db598437fdfa0` |
| `Ubuntu-App/` | six7-click-n-deploy/Ubuntu-App | `465bb21e5d22224a0b7d8966f7c6f40fe0fc8407` |

## Verwendung

Im Moodle-Admin unter „Vorinstallations-Skripte: Quelle" die Einstellung
„Mirror-Repo" auf `https://github.com/swausb/swim-vmrequest-services.git`
setzen. Leer lassen = Standardverhalten (Live-Klon der einzelnen
six7-click-n-deploy-Repos, unverändert).

## Aktualisieren

Referenz-Kopie liegt versioniert im `vmrequest`-Repo unter `services/` (als
Git-Submodule, dort mit Update-Anleitung und bekannten Eigenheiten je Dienst
dokumentiert). Ablauf: dort aktualisieren, verifizieren, dann den neuen Stand
hierher exportieren (`git archive`) und committen.

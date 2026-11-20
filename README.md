# swim-vmrequest-services

Gepinnter Mirror der Vorinstallations-Skripte für das SWIM-Moodle-Plugin
`mod_vmrequest` (Deliverable MI-2, Stream Moodle Integration). Ursprung:
[six7-click-n-deploy](https://github.com/six7-click-n-deploy) auf GitHub.

**Warum ein eigener Mirror:** Entkoppelt die VM-Provisionierung von der
Verfügbarkeit/Sichtbarkeit des Original-Repos (z. B. falls es privat gestellt
oder gelöscht wird) und macht Updates bewusst statt automatisch.

**Warum ein eigenes, separates Repo** (statt Teil des `vmrequest`-Plugin-Repos):
Das Plugin-Repo liegt im GitLab der DHBW und ist nur intern zugänglich. Die VMs
klonen die Skripte beim ersten Start ohne Anmeldung – dieses Repo ist daher
bewusst öffentlich und enthält ausschließlich die Vorinstallations-Skripte,
keine Zugangsdaten.

> **Produktiv im Einsatz:** Jede neu erstellte VM mit Vorinstallation klont
> diesen Stand von `main` und führt die Skripte mit Root-Rechten aus. Jede
> Änderung auf `main` wirkt sofort auf alle neuen VMs. `main` ist gegen
> Force-Push und Löschen geschützt.

## Struktur

Jeder Unterordner entspricht einem Dienst-Repo aus `six7-click-n-deploy` und
enthält dessen Inhalt zum unten genannten Commit (`packer/scripts/provision.sh`
ist das eigentliche Installationsskript):

| Ordner | Ursprungs-Repo | Gepinnter Commit | Übernommen am |
|---|---|---|---|
| `pgAdmin/` | six7-click-n-deploy/pgAdmin | `c5bd656fd003c929a43a4c5d44249620dbe7d648` | 07.07.2026 |
| `Jupyter-Notebook/` | six7-click-n-deploy/Jupyter-Notebook | `bc9ba39ca73f4af38e36823902955b16391688ae` | 07.07.2026 |
| `Online-IDE/` | six7-click-n-deploy/Online-IDE | `b2730fc8a6a3c417ad1306aeb95b0d6f1bdd4c96` | 07.07.2026 |
| `Web-LaTeX/` | six7-click-n-deploy/Web-LaTeX | `74c43ee27f17ba6f1f6c5834d61db598437fdfa0` | 07.07.2026 |
| `Ubuntu-App/` | six7-click-n-deploy/Ubuntu-App | `465bb21e5d22224a0b7d8966f7c6f40fe0fc8407` | 07.07.2026 |

Stand der Original-Repos am 20.11.2026: Alle fünf haben neuere Commits
(22.–25.07.2026, überwiegend Doku-Überarbeitungen; bei Online-IDE geänderte
Dateiübergabe). Diese sind **noch nicht** übernommen.

## Verwendung

Moodle → Website-Administration → Plugins → VM Request → Abschnitt
**„Vorinstallations-Skripte: Quelle"** → Feld **„Mirror-Repo (optional)"**
(`mod_vmrequest/preinstall_repo_base`):

```
https://github.com/swausb/swim-vmrequest-services.git
```

Leer lassen = Live-Klon der einzelnen six7-click-n-deploy-Repos (ungepinnt).
Das Plugin klont mit `git clone --depth 1` immer den Default-Branch `main`
und startet `<Ordner>/packer/scripts/provision.sh`.

## Aktualisieren

Updates nur bewusst und geprüft übernehmen – die Skripte laufen als root auf
den VMs der Studierenden.

1. **Änderungen im Original ansehen** (je Dienst, `<Pin>` aus der Tabelle):

   ```bash
   gh api repos/six7-click-n-deploy/<Repo>/compare/<Pin>...HEAD \
     --jq '.commits[].commit.message, (.files[] | .status + " " + .filename)'
   ```

   Besonders auf `packer/scripts/provision.sh` achten (Ports, Pakete,
   Benutzer, heruntergeladene Dateien).

2. **Neuen Stand in einen Update-Branch holen** (Beispiel `pgAdmin`):

   ```bash
   git switch -c update-$(date +%Y-%m-%d)
   git clone https://github.com/six7-click-n-deploy/pgAdmin /tmp/pgAdmin
   NEU=$(git -C /tmp/pgAdmin rev-parse HEAD)
   rm -rf pgAdmin && mkdir pgAdmin
   git -C /tmp/pgAdmin archive "$NEU" | tar -x -C pgAdmin
   ```

3. **Auf einer Test-VM prüfen:** Update-Branch auf der VM klonen
   (`git clone -b update-<Datum> …`) und das betroffene `provision.sh`
   ausführen – bei mehreren Diensten in der Reihenfolge, wie das Plugin sie
   installiert. Dienst im Browser bzw. per `systemctl` prüfen.

4. **Übernehmen:** Pin-Tabelle oben aktualisieren (Commit + Datum), Branch
   nach `main` mergen und pushen. Ab dann nutzen alle neuen VMs den Stand.

Ein Rückweg ist jederzeit möglich, indem der vorherige Ordnerinhalt aus der
Git-Historie wiederhergestellt und als neuer Commit auf `main` gepusht wird
(kein Force-Push).

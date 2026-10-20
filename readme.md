# LB 324 – Tagebbbuch

Ein einfaches Online-Tagebuch (Flask) mit einer CI/CD-Umgebung auf GitHub und Azure.

| | |
|---|---|
| **Autor** | Abbas Raza (GitHub: `Aboss3b13`) |
| **Live-App** | https://razaabbas-lb324-fmhzatesdhgpd8c9.switzerlandnorth-01.azurewebsites.net |
| **Login-Passwort (Live)** | `Aboss3b13` |

---

## Schnellstart (lokal)

1. Abhängigkeiten installieren:
   ```
   pip install -r requirements.txt
   ```
2. Im Projektordner eine Datei `.env` erstellen (wird **nicht** eingecheckt):
   ```
   PASSWORD="einSehrGeheimesPasswort"
   ```
3. App starten und http://127.0.0.1:5000 öffnen:
   ```
   flask run
   ```
4. Tests ausführen:
   ```
   python -m pytest
   ```

---

## Äste (Branches)

| Ast | Zweck |
|---|---|
| `main` | Nur Code, der ausgeliefert wird. `main` = Version auf Azure. |
| `dev` | Abgeschlossene und **getestete** Änderungen. |
| `feature/...` | Hier wird entwickelt. Jeder neue Ast startet von `dev`. |

---

## Aufgabe 1 – Anforderungen (Issues)

Jede Änderung beginnt mit einer Issue auf GitHub.

- **Vorlage für neue Funktionen:** *Issues → New issue → Anforderung*
  (Datei: `.github/ISSUE_TEMPLATE/anforderung.yml`).
  Die Vorlage fragt den Aufbau ab:
  **Zielsystem + Priorität + Systemaktivität + Ergänzungen + Funktionalität + Bedingungen**

  | Feld | Mögliche Werte |
  |---|---|
  | Priorität | `muss` (hoch), `soll` (mittel), `wird` (niedrig) |
  | Systemaktivität | selbständig · *dem Benutzer die Möglichkeit bieten* · *fähig sein* |
  | Bedingungen | zeitlich mit **wenn**, logisch mit **falls** |

  Beispiel: *Tagebbbuch **muss** dem Benutzer die Möglichkeit bieten, einen Eintrag mit einem Happiness-Emoji zu erfassen, **falls** der Benutzer eingeschrieben ist.*

- **Etiketten (Labels) für den Typ** – werden manuell gesetzt:
  `Funktionale Anforderung`, `Qualitätsanforderung`, `Randanforderung`

---

## Aufgabe 2
Erklären Sie hier, wie man `pre-commit` installiert.

Einmalig im Projektordner ausführen:

```
pip install -r requirements.txt
pip install pre-commit
pre-commit install
```

Fertig. `pre-commit install` richtet dank `default_install_hook_types` in der `.pre-commit-config.yaml` **beide** Hooks ein
(gleich wie `pre-commit install --hook-type pre-commit --hook-type pre-push`).

| Wann? | Was passiert? | Wenn es fehlschlägt |
|---|---|---|
| `git commit` | Code wird mit **black** automatisch formatiert. | Dateien wurden umformatiert → `git add .` und nochmals `git commit`. |
| `git push` | Alle Tests laufen mit **pytest**. | Ein Test ist rot → es wird **nicht** gepusht. Fehler beheben und erneut pushen. |

Hooks von Hand testen:

```
pre-commit run --all-files
pre-commit run --hook-stage pre-push --all-files
```

---

## Aufgabe 3 – Tests bei jedem Pull Request auf `dev`

Datei: `.github/workflows/test.yml`

- Startet **nur** bei einem Pull Request auf den Ast `dev`.
- Installiert die Abhängigkeiten und führt `pytest` aus. Sonst nichts.
- Das Ergebnis (grün/rot) erscheint direkt im Pull Request.

---

## Aufgabe 4
Erklären Sie hier, wie Sie das Passwort aus Ihrer lokalen `.env` auf Azure übertragen.

**URL der laufenden Applikation:** https://razaabbas-lb324-fmhzatesdhgpd8c9.switzerlandnorth-01.azurewebsites.net

Die `.env` steht in der `.gitignore` und kommt deshalb **nie** auf GitHub oder Azure.
Das Passwort wird darum in Azure als Umgebungsvariable gesetzt. Die App liest es mit `os.getenv("PASSWORD")`.

**Schritte im Azure-Portal:**

1. Web App `razaabbas-lb324` öffnen.
2. Links **Settings → Environment variables** → Tab **App settings** → **+ Add**.
3. Name: `PASSWORD` – Value: `Aboss3b13` (ohne Anführungszeichen).
4. **Apply** klicken und mit **Confirm** bestätigen. Die App startet neu.

Zusätzlich gesetzt: `SCM_DO_BUILD_DURING_DEPLOYMENT` = `true`, damit Azure beim Ausliefern `requirements.txt` installiert.

Alternativ mit der Azure CLI:

```
az webapp config appsettings set --name razaabbas-lb324 --resource-group zu-bbbearbeiten-abbas_group --settings PASSWORD=Aboss3b13
```

### Automatische Auslieferung bei jedem merge in `main`

Datei: `.github/workflows/deploy.yml` – startet bei jedem Push auf `main` (ein merge eines Pull Requests ist ein Push).

Einmalige Einrichtung:

1. Azure-Portal → Web App → **Overview** → **Download publish profile**.
2. GitHub → **Settings → Secrets and variables → Actions → New repository secret**.
3. Name: `AZURE_WEBAPP_PUBLISH_PROFILE`, Value: gesamter Inhalt der heruntergeladenen Datei.

---

## Aufgabe 5 – Neue Funktion nach github flow

Funktion: Jeder Tagebuch-Eintrag hat ein **Happiness-Emoji** (😃 🙂 😐 🙁 😢). Neue Einträge stehen zuoberst.

| Schritt | Umsetzung |
|---|---|
| 1. Issue erstellen | #1 *Happiness-Emoji pro Eintrag* (Vorlage *Anforderung*, Etikett *Funktionale Anforderung*) |
| 2. Ast von `dev` erstellen | `feature/happiness` |
| 3. Commits | Funktion + Testfall `test_add_entry_with_happiness` |
| 4. Pull Request → `dev` | #2 – Tests laufen automatisch (grün), dann merge |
| 5. Pull Request `dev` → `main` | #3 – merge löst die Auslieferung auf Azure aus, Issue #1 wird geschlossen |

Der Ast `feature/happiness` wurde gemäss Auftrag **nicht** gelöscht.

Weitere Änderungen liefen nach demselben Ablauf, z.B. #6 (Fehler: gleicher Zeitstempel) und #5 (diese Dokumentation).

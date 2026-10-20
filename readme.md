# LB 324 – Tagebbbuch

Abbas Raza

Live-Version auf Azure: https://razaabbas-lb324-fmhzatesdhgpd8c9.switzerlandnorth-01.azurewebsites.net

## Lokal starten

```
pip install -r requirements.txt
flask run
```

Die App braucht eine `.env`-Datei im Projektordner mit dem Passwort für das Login:

```
PASSWORD="einSehrGeheimesPasswort"
```

Die `.env` ist in der `.gitignore` und wird nicht eingecheckt.

## Äste

- `main`: Version, die auf Azure läuft. Hier wird nur gemergt, was ausgeliefert werden soll.
- `dev`: Fertige und getestete Änderungen.
- `feature/...`: Entwicklung. Jeder Feature-Ast wird von `dev` aus erstellt.

## Aufgabe 1

Für neue Funktionen gibt es auf GitHub die Issue-Vorlage **Anforderung** (`.github/ISSUE_TEMPLATE/anforderung.yml`).
Sie ist nach dem Schema *Zielsystem + Priorität + Systemaktivität + Ergänzungen + Funktionalität + Bedingungen* aufgebaut.

Für den Typ einer Issue gibt es die Etiketten `Funktionale Anforderung`, `Qualitätsanforderung` und `Randanforderung`.

## Aufgabe 2

pre-commit wird einmalig im Projektordner installiert:

```
pip install pre-commit
pre-commit install
```

Damit die Tests laufen können, müssen vorher die Pakete aus `requirements.txt` installiert sein.

`pre-commit install` installiert beide Hooks, weil in der `.pre-commit-config.yaml` `default_install_hook_types: [pre-commit, pre-push]` gesetzt ist.

- Bei jedem **commit** wird der Code mit `black` formatiert. Wenn black etwas ändert, bricht der Commit ab. Dann die Dateien nochmals mit `git add` hinzufügen und erneut committen.
- Bei jedem **push** werden die Tests mit `pytest` ausgeführt. Schlägt ein Test fehl, wird nicht gepusht.

## Aufgabe 3

Der Workflow `.github/workflows/test.yml` führt bei jedem Pull Request auf `dev` die Tests aus. Sonst macht er nichts.

## Aufgabe 4

Da die `.env` nicht auf GitHub ist, muss das Passwort direkt in Azure als Umgebungsvariable gesetzt werden. Die App liest es dort mit `os.getenv("PASSWORD")`.

1. Im Azure-Portal die Web App `razaabbas-lb324` öffnen.
2. **Settings → Environment variables → App settings → Add**
3. Name `PASSWORD`, als Wert das Passwort eintragen (für die Abgabe: mein GitHub-Benutzername), ohne Anführungszeichen.
4. **Apply** und **Confirm**. Die App startet danach neu.

Zusätzlich ist `SCM_DO_BUILD_DURING_DEPLOYMENT=true` gesetzt, damit Azure beim Deployment die `requirements.txt` installiert.

### Automatisches Deployment

Der Workflow `.github/workflows/deploy.yml` liefert die App bei jedem merge in `main` auf Azure aus.
Dafür habe ich im Azure-Portal das Publish Profile heruntergeladen (**Overview → Download publish profile**) und auf GitHub unter
**Settings → Secrets and variables → Actions** als Secret `AZURE_WEBAPP_PUBLISH_PROFILE` gespeichert.

## Aufgabe 5

Neue Funktion: Jeder Eintrag bekommt ein Happiness-Emoji. Umgesetzt nach github flow:

1. Issue #1 mit der Vorlage *Anforderung* erstellt
2. Ast `feature/happiness` von `dev` erstellt
3. Funktion und Testfall `test_add_entry_with_happiness` committet
4. Pull Request #2 auf `dev`, Tests liefen automatisch, danach gemergt
5. Pull Request #3 von `dev` auf `main` gemergt, dadurch wurde die App ausgeliefert

Der Ast `feature/happiness` wurde nicht gelöscht.

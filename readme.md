# LB 324

Tagebbbuch-Applikation von Abbas Raza.

Lokal starten (benötigt eine `.env` mit `PASSWORD="..."`):

```
pip install -r requirements.txt
flask run
```

Äste:
- `main`: nur ausgelieferte Änderungen (wird automatisch auf Azure ausgeliefert)
- `dev`: abgeschlossene, getestete Änderungen
- `feature/...`: Entwicklung, immer ausgehend von `dev`

## Aufgabe 2
Erklären Sie hier, wie man `pre-commit` installiert.

Einmalig im Projektordner ausführen:

```
pip install -r requirements.txt
pip install pre-commit
pre-commit install
```

`pre-commit install` installiert dank `default_install_hook_types` in der `.pre-commit-config.yaml` beide Hooks
(gleichbedeutend mit `pre-commit install --hook-type pre-commit --hook-type pre-push`):

- **pre-commit**: Bei jedem `git commit` wird der Code mit `black` automatisch formatiert. Wurde etwas umformatiert, bricht der Commit ab; die Änderungen mit `git add .` übernehmen und nochmals `git commit` ausführen.
- **pre-push**: Bei jedem `git push` laufen die Tests mit `pytest`. Schlägt ein Test fehl, wird nicht gepusht.

Manuell alle Hooks ausführen: `pre-commit run --all-files` bzw. `pre-commit run --hook-stage pre-push`.

## Aufgabe 4
Erklären Sie hier, wie Sie das Passwort aus Ihrer lokalen `.env` auf Azure übertragen.

URL der laufenden Applikation: https://razaabbas-lb324.azurewebsites.net

Die `.env` wird nicht eingecheckt (`.gitignore`), darum muss das Passwort als Umgebungsvariable in Azure gesetzt werden.
`load_dotenv()` überschreibt keine bestehenden Variablen, `os.getenv("PASSWORD")` liest also den Wert aus Azure.

1. Im Azure-Portal die Web App `razaabbas-lb324` öffnen.
2. **Settings → Environment variables → App settings → + Add**
3. Name: `PASSWORD`, Value: `Aboss3b13` (= GitHub-Benutzername, ohne Anführungszeichen)
4. **Apply** und mit **Confirm** bestätigen, die App startet neu.

Alternativ mit der Azure CLI:

```
az webapp config appsettings set --name razaabbas-lb324 --resource-group <resource-group> --settings PASSWORD=Aboss3b13
```

### Automatische Auslieferung
Der Workflow `.github/workflows/deploy.yml` liefert bei jedem Push auf `main` (also nach jedem merge eines Pull Requests in `main`) auf Azure aus.
Dafür wurde im Azure-Portal unter **Overview → Download publish profile** das Publish-Profil heruntergeladen und
auf GitHub unter **Settings → Secrets and variables → Actions → New repository secret** als `AZURE_WEBAPP_PUBLISH_PROFILE` gespeichert.
Damit `requirements.txt` beim Deployment installiert wird, ist in Azure zusätzlich die App setting `SCM_DO_BUILD_DURING_DEPLOYMENT=true` gesetzt.

Der Workflow `.github/workflows/test.yml` führt bei jedem Pull Request auf `dev` die Tests aus.

## Aufgabe 5
Die neue Funktionalität (Happiness-Emoji pro Eintrag) wurde nach github flow umgesetzt:
Issue erstellt → Ast `feature/happiness` von `dev` erstellt → commits → Pull Request nach `dev` (Tests laufen automatisch) → merge → Pull Request `dev` nach `main` → merge → automatische Auslieferung.

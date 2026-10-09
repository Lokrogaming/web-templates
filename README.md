# web-templates

Zentrales Template-Repo für **Web Template Studio** (Desktop-App + Website).

## Struktur

```
/templates.json              <- Mapping ALLER Templates (Name <-> Zip-Name) + Metadaten + verified-Flag
/templates/<datei>.zip       <- je ein Zip pro Template, enthält u. a. `.temp-config`
```

## `templates.json` – Format

```json
[
  {
    "id": "test-template",
    "name": "Test Template",
    "zip": "test-template.zip",
    "verified": true,
    "version": "1.0.0",
    "description": "Kurze Beschreibung",
    "author": "Lokrogaming",
    "updated": "2026-10-09",
    "type": "html",
    "languages": ["HTML", "CSS", "JavaScript"],
    "entry": "index.html"
  }
]
```

- `id`: Ordner-/Slug-Name, eindeutig, lowercase mit Bindestrichen
- `name`: Anzeigename für Cards / Modals
- `zip`: Dateiname unter `/templates/`
- `verified`: wenn `true`, zeigt die Desktop-App ein Verified-Icon (✅) auf der Card + Detailseite
- `preview` (optional): Pfad zu einem Vorschaubild im Repo, z. B. `previews/mein-template.png` – wird auf Cards + Detail-Panel gezeigt
- `deploy` (optional): Liste von Deploy-Zielen als Indikatoren, z. B. `["github-pages"]`, `["vercel"]`, `["netlify"]`, `["node"]`
- `features` (optional): Liste von Feature-Stichpunkten für die Detailseite
- `versions` (optional): Versionshistorie für die Detailseite, z. B. `[{"version": "1.1.0", "date": "2026-10-09", "notes": "Neues Farbschema."}]`
- `related` (geplant, siehe SiteSmith-Repo `IDEEN.md`): verwandte Templates/Versionen mit Relationstyp

## Template-Konfiguration (`config` + Platzhalter)

Templates können in `.temp-config` ein `config`-Array deklarieren. Beim Installieren
zeigt SiteSmith daraus ein Formular; die Werte ersetzen **vor** der Repo-Erstellung
alle `{id}`-Platzhalter in Textdateien (`.html`, `.css`, `.js(x)`, `.json`, `.md`, …).
`meta/`, `.git` und `node_modules` werden nie angefasst. IDs bitte namespacen
(z. B. `site.name`), damit sie nicht mit echtem Code (z. B. JSX `{f.title}`) kollidieren.

```json
"config": [
  { "id": "site.name", "name": "Website-Name", "type": "text", "required": true, "default": "Pulse" },
  { "id": "contact.email", "name": "Kontakt-E-Mail", "type": "email", "required": false, "default": "hallo@pulse.example" }
]
```

Typen: `text`, `textarea`, `email`, `url`, `number`, `boolean`, `color`, `select`
(bei `select` zusätzlich `"options": ["a", "b"]`). Fehlende Werte fallen auf `default`
zurück, damit keine Platzhalter übrig bleiben. Gespeicherte Werte landen in
`meta/meta.json` des installierten Projekts und können später in den
Project-Settings geändert bzw. auf neue Template-Versionen migriert werden.
- `version`, `description`, `author`, `updated` (`YYYY-MM-DD`), `type` (`html` | `node` | `static` …), `languages`, `entry` (Startdatei)

## `.temp-config` – Format (liegt IN jedem Zip im Root)

Jedes Zip **muss** eine `.temp-config` (JSON-Inhalt, kein Extension-Zwang) im Root enthalten:

```json
{
  "id": "test-template",
  "name": "Test Template",
  "type": "html",
  "languages": ["HTML", "CSS", "JavaScript"],
  "description": "Beschreibung …",
  "version": "1.0.0",
  "author": "Lokrogaming",
  "updated": "2026-10-09",
  "entry": "index.html"
}
```

Felder: `Art` = `type`, `Sprachen` = `languages`, dazu `description`, `version`, `author`, `updated` („Latest updated“).

Die Desktop-App liest für Cards/Detailseiten primär `templates.json` (schnell, ohne Download).
Nach dem Entpacken liest sie zusätzlich `.temp-config` zur Verifikation.

## Neues Template hinzufügen

1. Ordner bauen, z. B. `mein-template/` mit `index.html` + `.temp-config` (s. oben).
2. Zippen: Root des Zips = Dateien (kein Extra-Unterordner).
   ```powershell
   Compress-Archive -Path mein-template\* -DestinationPath templates\mein-template.zip -Force
   ```
3. Eintrag in `templates.json` ergänzen (inkl. `verified: false`, später ggf. auf `true` setzen).
4. Commit + Push. Die App lädt Zips via Raw-URL:
   `https://raw.githubusercontent.com/Lokrogaming/web-templates/main/templates/<zip>`
   Mapping via:
   `https://raw.githubusercontent.com/Lokrogaming/web-templates/main/templates.json`

## Deploy-Anleitungen (für Description-/Detailseiten)

- **Statisches HTML:** Repo pushen → GitHub → Settings → Pages → Deploy from branch `main` / `/ (root)` → URL `https://<user>.github.io/<repo>/`.
  Die Desktop-App macht das per Workflow automatisch (Pages-API).
- **Node-Projekt:** `npm install`, `npm run build`, `dist/` auf Pages / Vercel / Netlify deployen.

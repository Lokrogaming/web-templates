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

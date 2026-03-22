# Beitragen

Danke, dass du zu Claude HUD beiträgst. Dieses Repository ist klein und entwickelt sich schnell — wir legen Wert auf Klarheit und zügige Reviews.

## Wie du beitragen kannst

1. Repository forken und klonen
2. Einen Branch erstellen
3. Änderungen vornehmen
4. Tests ausführen und Dokumentation bei Bedarf aktualisieren
5. Pull Request öffnen

## Entwicklung

    npm ci
    npm run build
    npm test

## Tests

Die vollständige Teststrategie, Fixtures und Snapshot-Updates findest du in `TESTING.md`.

## Code-Stil

- Änderungen fokussiert und klein halten.
- Bei Verhaltensänderungen Tests bevorzugen.
- Keine Dependincies einführen, wenn nicht notwendig.

## Build-Prozess

**Wichtig**: PRs sollten nur Dateien in `src/` ändern — keine Änderungen an `dist/` einschließen.

CI baut und committet `dist/` automatisch, nachdem dein PR gemergt wurde. So bleiben PRs auf den Quellcode fokussiert und Reviews werden einfacher.

    Dein PR: nur src/-Änderungen → Merge → CI baut dist/ → Automatisch committet

## Pull Requests

- Problem und Lösung beschreiben.
- Tests hinzufügen oder erklären, warum sie nicht nötig sind.
- Relevante Issues verlinken.
- Nur `src/`-Dateien ändern — `dist/` wird von CI automatisch verwaltet.

## Neue Versionen veröffentlichen

Beim Veröffentlichen einer neuen Version:

1. **Versionsnummern** in allen drei Dateien aktualisieren:
   - `package.json` → `"version": "X.Y.Z"`
   - `.claude-plugin/plugin.json` → `"version": "X.Y.Z"`
   - `.claude-plugin/marketplace.json` → `"version": "X.Y.Z"`

2. **CHANGELOG.md** mit den Änderungen seit dem letzten Release aktualisieren

3. **Committen und mergen** — CI baut `dist/` automatisch

### Wie Nutzer Updates erhalten

Claude Code Plugins unterstützen Updates über das `/plugin`-Interface:

- **Jetzt aktualisieren** — Holt die neueste Version vom Main-Branch und installiert sie sofort
- **Für Update vormerken** — Stellt das Update für später bereit

Claude Code vergleicht das `version`-Feld in `plugin.json` mit der installierten Version. Das Erhöhen der Versionsnummer (z. B. 0.0.1 → 0.0.2) ermöglicht es Nutzern, ein verfügbares Update zu sehen.

### Versionsstrategie

Wir verwenden semantische Versionierung (`MAJOR.MINOR.PATCH`):

- **PATCH** (0.0.x): Fehlerbehebungen, kleinere Verbesserungen
- **MINOR** (0.x.0): Neue Funktionen, abwärtskompatible Änderungen
- **MAJOR** (x.0.0): Breaking Changes

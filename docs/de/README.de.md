# Claude HUD

Ein Claude Code Plugin, das anzeigt, was gerade passiert — Kontextnutzung, aktive Tools, laufende Agenten und Aufgabenfortschritt. Immer sichtbar unterhalb der Eingabe.

[![Lizenz](https://img.shields.io/github/license/jarrodwatts/claude-hud?v=2)](LICENSE)
[![Sterne](https://img.shields.io/github/stars/jarrodwatts/claude-hud)](https://github.com/jarrodwatts/claude-hud/stargazers)

![Claude HUD in Aktion](/claude-hud-preview-5-2.png)

## Installation

Führe in einer Claude Code Instanz folgende Befehle aus:

**Schritt 1: Marketplace hinzufügen**

    /plugin marketplace add jarrodwatts/claude-hud

**Schritt 2: Plugin installieren**

<details>
<summary><strong>⚠️ Linux-Nutzer: Zuerst hier klicken</strong></summary>

Unter Linux ist `/tmp` oft ein separates Dateisystem (tmpfs), was die Installation mit folgendem Fehler scheitern lässt:

    EXDEV: cross-device link not permitted

**Lösung**: TMPDIR vor der Installation setzen:

    mkdir -p ~/.cache/tmp && TMPDIR=~/.cache/tmp claude

Führe dann den Installationsbefehl in dieser Sitzung aus. Dies ist eine [Einschränkung der Claude Code Plattform](https://github.com/anthropics/claude-code/issues/14799).

</details>

    /plugin install claude-hud

**Schritt 3: Statuszeile konfigurieren**

    /claude-hud:setup

Fertig! Starte Claude Code neu, damit die neue statusLine-Konfiguration geladen wird — danach erscheint das HUD.

---

## Was ist Claude HUD?

Claude HUD gibt dir bessere Einblicke in das, was in deiner Claude Code Sitzung passiert.

| Was du siehst | Warum es wichtig ist |
|---------------|----------------------|
| **Projektpfad** | Weiß, in welchem Projekt du arbeitest (1–3 Verzeichnisebenen einstellbar) |
| **Kontextzustand** | Sieh genau, wie voll dein Kontextfenster ist — bevor es zu spät ist |
| **Tool-Aktivität** | Beobachte, wie Claude Dateien liest, bearbeitet und durchsucht |
| **Agenten-Tracking** | Sieh, welche Unteragenten laufen und was sie tun |
| **Aufgabenfortschritt** | Verfolge die Aufgabenerledigung in Echtzeit |

## Was du siehst

### Standard (2 Zeilen)

    [Opus | Max] │ my-project git:(main*)
    Context █████░░░░░ 45% │ Usage ██░░░░░░░░ 25% (1h 30m / 5h)

- **Zeile 1** — Modell, Planname (oder `Bedrock`), Projektpfad, Git-Branch
- **Zeile 2** — Kontextleiste (grün → gelb → rot) und Nutzungslimits

### Optionale Zeilen (aktivieren über `/claude-hud:configure`)

    ◐ Bearbeite: auth.ts | ✓ Lese ×3 | ✓ Grep ×2        ← Tool-Aktivität
    ◐ explore [haiku]: Auth-Code wird gesucht (2m 15s)    ← Agentenstatus
    ▸ Authentifizierungsfehler beheben (2/5)              ← Aufgabenfortschritt

---

## Wie es funktioniert

Claude HUD nutzt Claude Codes native **statusline API** — kein separates Fenster, kein tmux erforderlich, funktioniert in jedem Terminal.

    Claude Code → stdin JSON → claude-hud → stdout → in deinem Terminal angezeigt
               ↘ transcript JSONL (Tools, Agenten, Aufgaben)

**Hauptmerkmale:**
- Native Token-Daten von Claude Code (keine Schätzungen)
- Skaliert mit der von Claude Code gemeldeten Kontextfenstergröße, einschließlich neuerer 1M-Kontext-Sitzungen
- Parst das Transcript für Tool- und Agenten-Aktivität
- Aktualisiert sich alle ~300ms

---

## Konfiguration

Passe dein HUD jederzeit an:

    /claude-hud:configure

Der geführte Ablauf kümmert sich um Layout und Anzeigeoptionen. Erweiterte Einstellungen wie benutzerdefinierte Farben und Schwellenwerte werden dort gespeichert, müssen aber direkt in der Konfigurationsdatei bearbeitet werden:

- **Ersteinrichtung**: Preset wählen (Voll/Wesentlich/Minimal), dann einzelne Elemente feinjustieren
- **Jederzeit anpassen**: Elemente ein-/ausschalten, Git-Anzeigestil wechseln, Layout umschalten
- **Vorschau vor dem Speichern**: Sieh genau, wie dein HUD aussehen wird, bevor du die Änderungen übernehmst

### Presets

| Preset | Was angezeigt wird |
|--------|--------------------|
| **Voll** | Alles aktiviert — Tools, Agenten, Aufgaben, Git, Nutzung, Dauer |
| **Wesentlich** | Aktivitätszeilen + Git-Status, minimale Informationsüberladung |
| **Minimal** | Nur das Wichtigste — Modellname und Kontextleiste |

Nach der Preset-Auswahl kannst du einzelne Elemente ein- oder ausschalten.

### Manuelle Konfiguration

Bearbeite `~/.claude/plugins/claude-hud/config.json` direkt für erweiterte Einstellungen wie `colors.*`, `pathLevels` und Schwellenwertüberschreibungen. Das Ausführen von `/claude-hud:configure` erhält diese manuellen Einstellungen.

### Optionen

| Option | Typ | Standard | Beschreibung |
|--------|-----|----------|--------------|
| `lineLayout` | string | `expanded` | Layout: `expanded` (mehrzeilig) oder `compact` (einzeilig) |
| `pathLevels` | 1-3 | 1 | Verzeichnisebenen im Projektpfad |
| `elementOrder` | string[] | `["project","context","usage","environment","tools","agents","todos"]` | Elementreihenfolge im erweiterten Modus |
| `gitStatus.enabled` | boolean | true | Git-Branch im HUD anzeigen |
| `gitStatus.showDirty` | boolean | true | `*` für nicht committete Änderungen anzeigen |
| `gitStatus.showAheadBehind` | boolean | false | `↑N ↓N` für Ahead/Behind anzeigen |
| `gitStatus.showFileStats` | boolean | false | Dateiänderungszähler anzeigen `!M +A ✘D ?U` |
| `display.showModel` | boolean | true | Modellname anzeigen `[Opus]` |
| `display.showContextBar` | boolean | true | Visuelle Kontextleiste anzeigen `████░░░░░░` |
| `display.contextValue` | `percent` \| `tokens` \| `remaining` | `percent` | Kontextanzeigeformat |
| `display.showConfigCounts` | boolean | false | CLAUDE.md, Regeln, MCPs, Hooks-Anzahl anzeigen |
| `display.showDuration` | boolean | false | Sitzungsdauer anzeigen `⏱️ 5m` |
| `display.showSpeed` | boolean | false | Ausgabe-Token-Geschwindigkeit anzeigen `out: 42.1 tok/s` |
| `display.showUsage` | boolean | true | Nutzungslimits anzeigen (nur Pro/Max/Team) |
| `display.usageBarEnabled` | boolean | true | Nutzung als visuelle Leiste statt Text anzeigen |
| `display.sevenDayThreshold` | 0-100 | 80 | 7-Tage-Nutzung ab diesem Schwellenwert anzeigen (0 = immer) |
| `display.showTokenBreakdown` | boolean | true | Token-Details bei hohem Kontext (85%+) anzeigen |
| `display.showTools` | boolean | false | Tool-Aktivitätszeile anzeigen |
| `display.showAgents` | boolean | false | Agenten-Aktivitätszeile anzeigen |
| `display.showTodos` | boolean | false | Aufgabenfortschrittszeile anzeigen |
| `display.showSessionName` | boolean | false | Sitzungsname oder benutzerdefinierten Titel aus `/rename` anzeigen |
| `usage.cacheTtlSeconds` | number | 60 | Cachedauer einer erfolgreichen API-Antwort (Sekunden) |
| `usage.failureCacheTtlSeconds` | number | 15 | Cachedauer einer fehlgeschlagenen API-Antwort (Sekunden) |
| `colors.context` | Farbname | `green` | Grundfarbe für Kontextleiste und Prozentsatz |
| `colors.usage` | Farbname | `brightBlue` | Grundfarbe für Nutzungsleisten und Prozentsätze |
| `colors.warning` | Farbname | `yellow` | Warnfarbe für Kontextschwellenwerte |
| `colors.usageWarning` | Farbname | `brightMagenta` | Warnfarbe für Nutzungsleisten nahe dem Schwellenwert |
| `colors.critical` | Farbname | `red` | Kritische Farbe für erreichte Limits |

Unterstützte Farbnamen: `red`, `green`, `yellow`, `magenta`, `cyan`, `brightBlue`, `brightMagenta`.

### Nutzungslimits (Pro/Max/Team)

Die Nutzungsanzeige ist für Claude Pro, Max und Team **standardmäßig aktiviert**. Sie zeigt deinen Verbrauch der Ratenlimits in Zeile 2 neben der Kontextleiste.

Der 7-Tage-Prozentsatz erscheint, wenn er über `display.sevenDayThreshold` liegt (Standard 80%):

    Context █████░░░░░ 45% │ Usage ██░░░░░░░░ 25% (1h 30m / 5h) | ██████████ 85% (2d / 7d)

Zum Deaktivieren `display.showUsage` auf `false` setzen.

**Voraussetzungen:**
- Claude Pro, Max oder Team Abonnement (nicht für API-Nutzer verfügbar)
- OAuth-Anmeldedaten von Claude Code (werden beim Login automatisch erstellt)

**Fehlerbehebung:** Falls die Nutzung nicht erscheint:
- Stelle sicher, dass du mit einem Pro/Max/Team-Konto angemeldet bist (kein API-Schlüssel)
- Prüfe, dass `display.showUsage` nicht auf `false` gesetzt ist
- API-Nutzer sehen keine Nutzungsanzeige (sie zahlen pro Token)
- AWS Bedrock Modelle zeigen `Bedrock` an und verbergen Nutzungslimits
- Nicht-standardmäßige `ANTHROPIC_BASE_URL` / `ANTHROPIC_API_BASE_URL` Einstellungen deaktivieren die Nutzungsanzeige
- Hinter einem Proxy: `HTTPS_PROXY` (oder `HTTP_PROXY`/`ALL_PROXY`) und `NO_PROXY` setzen
- Bei hoher Latenz: `CLAUDE_HUD_USAGE_TIMEOUT_MS` erhöhen (Millisekunden)

### Beispielkonfiguration

```json
{
  "lineLayout": "expanded",
  "pathLevels": 2,
  "elementOrder": ["project", "tools", "context", "usage", "environment", "agents", "todos"],
  "gitStatus": {
    "enabled": true,
    "showDirty": true,
    "showAheadBehind": true,
    "showFileStats": true
  },
  "display": {
    "showTools": true,
    "showAgents": true,
    "showTodos": true,
    "showConfigCounts": true,
    "showDuration": true
  },
  "colors": {
    "context": "cyan",
    "usage": "cyan",
    "warning": "yellow",
    "usageWarning": "magenta",
    "critical": "red"
  },
  "usage": {
    "cacheTtlSeconds": 120,
    "failureCacheTtlSeconds": 30
  }
}
```

### Anzeigebeispiele

**1 Ebene (Standard):** `[Opus] │ my-project git:(main)`

**2 Ebenen:** `[Opus] │ apps/my-project git:(main)`

**3 Ebenen:** `[Opus] │ dev/apps/my-project git:(main)`

**Mit Änderungsindikator:** `[Opus] │ my-project git:(main*)`

**Mit Ahead/Behind:** `[Opus] │ my-project git:(main ↑2 ↓1)`

**Mit Dateistatistiken:** `[Opus] │ my-project git:(main* !3 +1 ?2)`
- `!` = geänderte Dateien, `+` = hinzugefügt/bereitgestellt, `✘` = gelöscht, `?` = nicht verfolgt
- Nullwerte werden für eine sauberere Anzeige weggelassen

### Fehlerbehebung

**Konfiguration wird nicht übernommen?**
- JSON-Syntaxfehler prüfen: Ungültiges JSON fällt stillschweigend auf Standardwerte zurück
- Gültige Werte sicherstellen: `pathLevels` muss 1, 2 oder 3 sein; `lineLayout` muss `expanded` oder `compact` sein
- Konfiguration löschen und `/claude-hud:configure` neu ausführen

**Git-Status fehlt?**
- Sicherstellen, dass du in einem Git-Repository bist
- Prüfen, dass `gitStatus.enabled` nicht auf `false` gesetzt ist

**Tool-/Agenten-/Aufgabenzeilen fehlen?**
- Diese sind standardmäßig ausgeblendet — mit `showTools`, `showAgents`, `showTodos` aktivieren
- Sie erscheinen nur, wenn entsprechende Aktivität vorhanden ist

**HUD erscheint nach der Einrichtung nicht?**
- Claude Code neu starten, damit die neue statusLine-Konfiguration geladen wird
- Unter macOS Claude Code vollständig beenden und `claude` erneut im Terminal ausführen

---

## Voraussetzungen

- Claude Code v1.0.80+
- Node.js 18+ oder Bun

---

## Entwicklung

```bash
git clone https://github.com/jarrodwatts/claude-hud
cd claude-hud
npm ci && npm run build
npm test
```

Siehe [CONTRIBUTING.md](CONTRIBUTING.md) für Richtlinien.

---

## Lizenz

MIT — siehe [LICENSE](LICENSE)

---

## Sternverlauf

[![Sternverlauf-Diagramm](https://api.star-history.com/svg?repos=jarrodwatts/claude-hud&type=Date)](https://star-history.com/#jarrodwatts/claude-hud&Date)

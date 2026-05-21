# PNPM Windows Troubleshooting Cheat Sheet

Kurzfassung fuer den Fall, dass `pnpm`, `node`, `where.exe` oder die Fortress-Policy
wieder Probleme machen.

## 1. Typische Symptome

- `pnpm` wird nicht gefunden
- `where.exe` wird nicht gefunden
- `pnpm -v` zeigt die falsche Version
- `pnpm install` scheitert mit:
  - `This project is configured to use ... of pnpm`
  - `ERR_PNPM_TRUST_DOWNGRADE`

## 2. Haupthintergrund

Die haeufigste Ursache war hier nicht PNPM selbst, sondern ein kaputter oder
uneinheitlicher Windows-`PATH`:

- alte PNPM-Installation unter `C:\Users\denni\AppData\Local\pnpm`
- aktive NVM-/Node-Installation unter `C:\nvm4w\nodejs`
- fehlende Windows-Basispfade wie `C:\Windows\System32`
- ein falscher, woertlicher `$PATH`-Eintrag im Windows-`Path`

## 3. Zielzustand

Fuer dieses Repo soll gelten:

- `node -v` -> `26.2.0`
- `pnpm -v` -> `11.2.2`
- `pnpm` soll aus `C:\nvm4w\nodejs` kommen

## 4. Schnelltest

In einem neuen Terminal pruefen:

```powershell
node -v
npm -v
pnpm -v
Get-Command node
Get-Command pnpm
```

Wenn `pnpm` nicht gefunden wird, direkt pruefen:

```powershell
& "C:\nvm4w\nodejs\pnpm.cmd" -v
```

Wenn das funktioniert, ist PNPM installiert und nur der `PATH` falsch.

## 5. PATH-Reparatur Schritt fuer Schritt

1. `Win + R`
2. `SystemPropertiesAdvanced`
3. `Umgebungsvariablen...`
4. Benutzer-`Path` bearbeiten
5. Sicherstellen, dass `C:\nvm4w\nodejs` vorhanden ist
6. Alte PNPM-Quelle `C:\Users\denni\AppData\Local\pnpm` entfernen, wenn sie noch drin ist
7. System-`Path` bearbeiten
8. Falschen `$PATH`-Eintrag entfernen
9. Sicherstellen, dass diese Basispfade vorhanden sind:

```text
C:\Windows\System32
C:\Windows
C:\Windows\System32\Wbem
C:\Windows\System32\WindowsPowerShell\v1.0\
C:\Windows\System32\OpenSSH\
```

10. Alle Terminals schliessen
11. Cursor komplett neu starten
12. Neues Terminal oeffnen und erneut testen:

```powershell
pnpm -v
node -v
Get-Command pnpm
```

## 6. Direkter Notfall-Bypass

Wenn `pnpm` ueber den `PATH` noch nicht sauber aufgeloest wird:

```powershell
& "C:\nvm4w\nodejs\pnpm.cmd" install
```

Das ist nur der Notfallweg. Dauerhaft soll der normale Befehl `pnpm` funktionieren.

## 7. Repo-Toolchain merken

Dieses Repo ist bewusst fail-closed konfiguriert:

- `packageManager`: `pnpm@11.2.2`
- `engines.node`: `26.2.0`
- `engines.pnpm`: `11.2.2`
- `devEngines.runtime.onFail`: `error`
- `devEngines.packageManager.onFail`: `error`
- `pmOnFail: error`

Das bedeutet: Schon kleine Versionsabweichungen blockieren absichtlich.

## 8. Trust-Downgrade richtig behandeln

Wenn `pnpm install` mit `ERR_PNPM_TRUST_DOWNGRADE` scheitert:

### Richtig

1. Erst sicherstellen, dass die lokale Toolchain stimmt
2. Lockfile-Diff anschauen
3. Lockfile sauber neu aufbauen
4. Falls noetig, die verursachenden Elternpakete aktualisieren

Typische Reihenfolge:

```powershell
git diff -- pnpm-lock.yaml
pnpm clean --lockfile
pnpm install
```

Wenn dieselben Treffer bleiben:

- direkte Elternpakete im `catalog` oder in `package.json` pruefen
- gezielt aktualisieren
- nur im Ausnahmefall exakte `trustPolicyExclude`-Eintraege setzen

### Falsch

Nicht pauschal:

```yaml
trustPolicy: off
trustPolicyIgnoreAfter: 525600
```

Nicht pauschal:

```yaml
trustPolicyExclude:
  - '*'
```

## 9. Merksatz

Bei diesem Setup gilt:

- erst `PATH` reparieren
- dann richtige `node`-/`pnpm`-Version bestaetigen
- dann Lockfile sauber neu aufbauen
- Trust-Policy nicht vorschnell abschwaechen

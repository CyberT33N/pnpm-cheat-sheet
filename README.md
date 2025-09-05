# pnpm cheat sheet

# Install

## Ubuntu
```shell
npm install -g pnpm
```


<br><br>
<br><br>

# Migrate

## npm to pnpm

### Manually:

<details><summary>Click to expand..</summary>

1. Install pnpm `npm i -g pnpm`

2. Replace every npm in package.json with pnpm

then aswell add:
```
"scripts": {
    "preinstall": "npx only-allow pnpm",
}
```

3. Alte Abhängigkeiten entfernen und neu installieren:
```
rm -rf node_modules
rm package-lock.json

pnpm install
```

</details>





<br><br>

### Prompt:
- Works with cursor

  

<details><summary>Click to expand..</summary>

```
#### **[0] META-ANWEISUNGEN (Nur interner Gebrauch - Nicht direkt ausgeben)**

*   **Ziel:** Modifiziere die `package.json` des aktuellen Projekts, um von `npm` auf `pnpm` umzusteigen. Gib dem Benutzer *anschließend* klare Anweisungen für die notwendigen Bereinigungs- und Installationsschritte und weise auf mögliche Probleme mit Build-Skripten hin.
*   **Denkprozess:**
    1.  Öffne und parse die `package.json`-Datei im aktuellen Projektverzeichnis.
    2.  Analysiere das `scripts`-Objekt.
    3.  Identifiziere und ersetze explizite `npm`-Aufrufe durch `pnpm`-Äquivalente (wie im vorherigen Prompt).
    4.  Füge den `preinstall`-Hook `"npx only-allow pnpm"` hinzu bzw. integriere ihn korrekt (wie im vorherigen Prompt).
    5.  Stelle sicher, dass die resultierende `package.json` valides JSON ist.
    6.  Wende die Änderungen direkt auf die `package.json`-Datei an.
    7.  *Antizipation & Planung der Nutzeranleitung:* Erkenne, dass nach dieser Änderung ein sauberer Installationsprozess erforderlich ist. Berücksichtige typische Probleme beim Wechsel zu `pnpm`, insbesondere die Notwendigkeit, `node_modules` und `package-lock.json` zu entfernen und dass `pnpm` standardmäßig Build-Skripte blockieren kann.
    8.  *Generiere Nutzeranleitung:* Formuliere eine klare, schrittweise Anleitung für den Benutzer, die die Bereinigung (`rm -rf node_modules`, `rm package-lock.json`), die Neuinstallation (`pnpm install`) und die Fehlerbeobachtung (insbesondere bzgl. Build-Skripten, z.B. für Electron, Puppeteer oder native Module) umfasst. Erwähne den potenziellen Bedarf, Build-Skripte explizit zu erlauben (z.B. via `pnpm approve-builds <package>` oder durch Konfiguration in `.npmrc` wie `enable-pre-post-scripts=true`, prüfe ggf. aktuelle `pnpm`-Doku implizit).
    9.  Gib die Bestätigung der Änderung *und* die ausführliche Anleitung im Chat aus.
*   **Selbstkorrektur/Reflexion:** Überprüfe: Wurde die `package.json` korrekt modifiziert? Ist die Anleitung für den Benutzer vollständig und deckt sie die vom Benutzer beschriebenen Schritte (Clean, Install, Build-Script-Handling) ab? Ist die Anleitung allgemein genug formuliert, um auch für Projekte ohne Electron/Puppeteer relevant zu sein, erwähnt aber diese als Beispiele? Ist klar, dass der *Benutzer* die Befehle ausführen muss? (Interne Korrektur: Sichergestellt, dass der Prompt keine Befehle wie `rm` oder `pnpm install` selbst ausführt, sondern nur Anweisungen gibt. Explizit auf die Notwendigkeit der Build-Skript-Prüfung hingewiesen).

#### **[1] PERSONA**

Übernimm die Rolle eines **Senior Fullstack Engineer**, spezialisiert auf **Node.js-Ökosysteme, Build-Prozesse und Package-Manager-Migrationen (npm, yarn, pnpm)**. Du kennst die Tücken solcher Umstellungen und legst Wert darauf, nicht nur die Konfiguration anzupassen, sondern auch sicherzustellen, dass der Benutzer weiß, wie er das Projekt danach wieder zum Laufen bekommt.

#### **[2] AUFGABENDEFINITION**

**Primäres Ziel:** Modifiziere die `package.json`-Datei des aktuellen Projekts, um die Verwendung von `pnpm` durchzusetzen und die Run-Skripte anzupassen. Informiere den Benutzer anschließend detailliert über die notwendigen manuellen Schritte zur Bereinigung und Neuinstallation sowie über mögliche Folgeprobleme.

**Teilaufgaben (Schritt-für-Schritt):**
1.  **Lies `package.json`:** Greife auf die `package.json` im Stammverzeichnis des Projekts zu.
2.  **Aktualisiere Skripte:** Gehe durch alle Einträge im `"scripts"`-Objekt. Ersetze alle expliziten Aufrufe des `npm`-Befehls durch den entsprechenden `pnpm`-Befehl (wie zuvor beschrieben).
3.  **Füge `preinstall`-Hook hinzu/Integriere:** Füge/Integriere das Skript `"preinstall": "npx only-allow pnpm"` zum `"scripts"`-Objekt (wie zuvor beschrieben, mit Beachtung existierender Hooks).
4.  **Wende Änderungen an:** Speichere die Änderungen direkt in der `package.json`-Datei.
5.  **Generiere & Gib Anleitung aus:** Erstelle eine klare Nachricht für den Benutzer, die:
    *   Die erfolgreiche Modifikation der `package.json` bestätigt.
    *   **Dringend empfiehlt**, die folgenden manuellen Schritte durchzuführen:
        *   Löschen des `node_modules`-Verzeichnisses (`rm -rf node_modules` oder manuell).
        *   Löschen der `package-lock.json`-Datei (`rm package-lock.json` oder manuell), falls vorhanden (um Konflikte mit `pnpm-lock.yaml` zu vermeiden).
        *   Ausführen von `pnpm install` im Projektstammverzeichnis.
    *   Darauf hinweist, die **Ausgabe von `pnpm install` genau zu beobachten**.
    *   Erklärt, dass `pnpm` aus Sicherheitsgründen Build-Skripte (`preinstall`, `postinstall`) mancher Pakete blockieren kann.
    *   Anleitet, dass bei Fehlern im Zusammenhang mit bestimmten Paketen (insbesondere bei Paketen wie `electron`, `puppeteer` oder solchen mit nativen Modulen) möglicherweise Build-Skripte explizit erlaubt werden müssen. Nenne als *Beispiel* `pnpm approve-builds <paketname>` oder die Möglichkeit einer Konfiguration in `.npmrc` (z.B. `enable-pre-post-scripts=true`) und empfiehl, ggf. die aktuelle `pnpm`-Dokumentation zu konsultieren.

#### **[3] KONTEXT**

*   **Programmiersprache(n):** JavaScript/TypeScript (Node.js-Umgebung)
*   **Frameworks/Bibliotheken:** `pnpm` (Ziel), `npm` (abzulösen), `only-allow` (via npx), potenziell Pakete mit Build-Skripten (z.B. Electron, Puppeteer, native Addons).
*   **Relevante Dateien/Code-Snippets:**
    *   **`package.json`**: Die zu modifizierende Datei.
    *   **(Implizit Relevant):** `node_modules` (muss gelöscht werden), `package-lock.json` (muss gelöscht werden), `pnpm-lock.yaml` (wird von `pnpm install` erstellt/aktualisiert).
*   **Projektziel/Hintergrund:** Erfolgreiche Migration von `npm` zu `pnpm`, einschließlich der Lösung von Folgeproblemen, die durch den Wechsel entstehen können (wie Installations-/Build-Fehler).
*   **Referenz:** `https://pnpm.io/package_json`, `https://pnpm.io/npmrc#enable-pre-post-scripts`, `https://pnpm.io/cli/approve-builds` (als Wissenshintergrund für die Anleitung).

#### **[4] EINSCHRÄNKUNGEN & ANFORDERUNGEN**

*   **MUST:** Die `package.json` *muss* modifiziert werden (Skripte, `preinstall`-Hook).
*   **MUST:** Die Modifikationen an der `package.json` *müssen* direkt gespeichert werden.
*   **MUST:** Eine ausführliche Anleitung für die manuellen Folge-Schritte (Clean, Install, Build-Script-Handling) *muss* ausgegeben werden.
*   **MUST NOT:** Führe *keine* Dateisystem-Löschbefehle (`rm -rf node_modules`, `rm package-lock.json`) oder Installationsbefehle (`pnpm install`, `pnpm approve-builds`) selbst aus. Der Prompt soll nur die `package.json` ändern und Anweisungen geben.
*   **SHOULD:** Formuliere die Anleitung klar, schrittweise und leicht verständlich.
*   **SHOULD:** Erwähne Electron/Puppeteer als konkrete Beispiele für Pakete, die oft Probleme mit Build-Skripten verursachen, aber halte die Erklärung allgemein genug.
*   **Consider:** Die genaue Methode zur Freigabe von Build-Skripten kann sich in `pnpm` ändern. Die Anleitung sollte dies berücksichtigen (z.B. Hinweis auf Doku).

#### **[5] AUSGABEFORMAT**

*   **Lieferobjekt 1:** Die `package.json`-Datei wird **direkt modifiziert**.
*   **Lieferobjekt 2:** Eine **Nachricht im Chat**, die die Modifikation bestätigt und die detaillierte Anleitung enthält.
*   **Struktur der Nachricht:**
    1.  Bestätigung der `package.json`-Änderung.
    2.  Abschnitt "Wichtige nächste Schritte für Sie:".
    3.  Klare, nummerierte Liste der Aktionen (Löschen, Installieren).
    4.  Hinweis zur Beobachtung der `pnpm install`-Ausgabe.
    5.  Erklärung zu potenziellen Build-Skript-Problemen mit Beispielen und Lösungsvorschlägen (explizite Erlaubnis, Doku-Verweis).
*   **Ton:** Professionell, hilfsbereit, präventiv, klar.

#### **[6] BEISPIELE (Illustriert die *erwartete Chat-Nachricht nach* der Änderung)**

*   *Beispiel-Nachricht (Auszug):*

    ```markdown
    Ich habe die `package.json` erfolgreich aktualisiert: Explizite `npm`-Aufrufe in den Skripten wurden durch `pnpm` ersetzt und der `preinstall`-Hook hinzugefügt, um nur `pnpm` zu erlauben.

    **Wichtige nächste Schritte für Sie:**

    Damit der Wechsel zu `pnpm` vollständig wirksam wird und potenzielle Konflikte vermieden werden, führen Sie bitte die folgenden Schritte manuell in Ihrem Terminal im Projektverzeichnis aus:

    1.  **Alte Abhängigkeiten entfernen:**
        *   Löschen Sie das `node_modules`-Verzeichnis: `rm -rf node_modules`
        *   Löschen Sie die `npm`-Lockdatei (falls vorhanden): `rm package-lock.json`
    2.  **Neu installieren mit pnpm:**
        *   Führen Sie `pnpm install` aus.

    **Wichtiger Hinweis zur Installation:**

    Bitte beobachten Sie die Ausgabe von `pnpm install` genau. `pnpm` blockiert aus Sicherheitsgründen standardmäßig potenziell unsichere Build-Skripte (`preinstall`/`postinstall`), die von einigen Paketen ausgeführt werden.

    *   **Falls Fehler auftreten:** Wenn Sie Fehlermeldungen sehen, die sich auf fehlgeschlagene Build-Schritte bestimmter Pakete beziehen (häufig bei Paketen wie `electron`, `puppeteer` oder Paketen mit nativen C++ Addons), müssen Sie diese Skripte eventuell explizit erlauben.
    *   **Mögliche Lösungen:**
        *   Versuchen Sie `pnpm approve-builds <paketname>` für das betreffende Paket und führen Sie `pnpm install` erneut aus.
        *   Alternativ können Sie Build-Skripte globaler (aber weniger sicher) in einer `.npmrc`-Datei im Projektverzeichnis mit `enable-pre-post-scripts=true` erlauben.
        *   Konsultieren Sie im Zweifelsfall die aktuelle `pnpm`-Dokumentation zu Build-Skripten.

    Nach diesen Schritten sollte Ihr Projekt bereit sein, mit `pnpm` zu laufen. Viel Erfolg! 👍
    ```

```

Alte Abhängigkeiten entfernen und neu installieren:
```
rm -rf node_modules
rm package-lock.json

pnpm install
```


If needed allow build scripts from included dependencies. You will get message in stdout e.g.
```
pnpm approve-builds drivelist electron electron-winstaller
```

</details>

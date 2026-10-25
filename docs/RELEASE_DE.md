# Release-Checkliste

Diese Checkliste beschreibt den empfohlenen Weg, um einen Release-Kandidaten oder eine stabile Version von `@compeso/node-red-contrib-imap-queue` zu veroeffentlichen.

## 1. Vorbedingungen

- Der aktuelle Stand ist in einem sauberen Git-Working-Tree.
- Der Live-Test gegen das Ziel-IMAP-System ist erfolgreich.
- GitHub Actions ist gruen.
- `README.md`, `CHANGELOG.md` und die Node-RED-Hilfetexte sind aktuell.

## 2. Lokale Pruefung

```bash
npm install --no-audit --no-fund
npm test
npm pack --dry-run
npm pack
```

Erwartung:

```text
# fail 0
```

`npm pack --dry-run` sollte nur die geplanten Paketdateien zeigen, insbesondere:

```text
nodes/
lib/
examples/
docs/
README.md
CHANGELOG.md
LICENSE
package.json
```

## 3. Git committen und taggen

Fuer die letzte Version:

```bash
git add .
git commit -m "Release 1.0.3"
git tag v1.0.3
git push
git push --tags
```

`1.0.3` ist die letzte Version dieses Pakets. Weitere Releases sind nicht geplant; siehe Abschnitt 9.

## 4. Installation aus GitHub testen

In einem Node-RED-User-Verzeichnis:

```bash
cd ~/.node-red
npm uninstall @compeso/node-red-contrib-imap-queue
npm install github:Harpau/node-red-contrib-imap-queue#v1.0.3
```

Danach Node-RED neu starten und den Beispiel-Flow importieren.

## 5. Installation aus dem Tarball testen

```bash
cd ~/.node-red
npm uninstall @compeso/node-red-contrib-imap-queue
npm install /path/to/compeso-node-red-contrib-imap-queue-1.0.3.tgz
```

Danach Node-RED neu starten.

## 6. npm-Veroeffentlichung

Das Paket ist ein scoped package. `package.json` enthaelt deshalb:

```json
"publishConfig": {
  "access": "public"
}
```

Fuer die stabile Version:

```bash
npm publish
```

Da das Paket scoped ist, muss es oeffentlich veroeffentlicht werden. `package.json` enthaelt dafuer bereits `publishConfig.access = public`.

## 7. GitHub Release

Erstelle auf GitHub einen Release fuer den Tag `v1.0.3`.

Empfohlene Assets:

```text
compeso-node-red-contrib-imap-queue-1.0.3.tgz
compeso-node-red-contrib-imap-queue-1.0.3.sha256
```

Empfohlene Release Notes fuer `1.0.3`:

```text
Final release. Deprecation notice only.

- Deprecates @compeso/node-red-contrib-imap-queue in favor of @compeso/node-red-contrib-imap-email (https://github.com/Harpau/node-red-contrib-imap-email).
- Adds migration guidance to README, installation guide, and release checklist.
- No further releases are planned for this package.
- No runtime behavior change.
```

## 8. Nach dem Release

Beobachte mindestens:

- GitHub Actions auf `main` und Tags.
- Installation aus GitHub in einer frischen Node-RED-Umgebung.
- Installation aus dem `.tgz`.
- ACK/NACK-Verhalten gegen ein STRATO-Testpostfach.
- Stats-Ausgaenge bei Rueckstand und Normalbetrieb.

Version `1.0.3` ist die letzte Version dieses Pakets. Weitere Releases sind nicht geplant.

## 9. Abkuendigung auf npm

Reihenfolge: zuerst `1.0.3` veroeffentlichen, dann abkuendigen. `npm deprecate` markiert nur die zum Zeitpunkt des Befehls vorhandenen Versionen; eine spaeter veroeffentlichte Version waere nicht markiert.

Alle Versionen des Pakets abkuendigen:

```bash
npm deprecate @compeso/node-red-contrib-imap-queue "Deprecated: superseded by @compeso/node-red-contrib-imap-email (https://www.npmjs.com/package/@compeso/node-red-contrib-imap-email). No further releases are planned. See the README for migration notes."
```

Bei aktivierter Zwei-Faktor-Authentifizierung den Einmalcode anhaengen:

```bash
npm deprecate @compeso/node-red-contrib-imap-queue "<gleiche Nachricht>" --otp=<code>
```

Kontrolle:

```bash
npm view @compeso/node-red-contrib-imap-queue@1.0.3 deprecated
```

Rueckgaengig machen; eine leere Nachricht hebt die Abkuendigung auf:

```bash
npm deprecate @compeso/node-red-contrib-imap-queue ""
```

Hinweise:

- `npm install` zeigt die Nachricht danach als Warnung, npmjs.com zeigt ein Banner. Das Paket bleibt installierbar.
- Die Node-RED Flow Library uebernimmt den Hinweis aus dem README. Dafuer muss `1.0.3` veroeffentlicht und der [Katalogeintrag](https://flows.nodered.org/node/@compeso/node-red-contrib-imap-queue) angemeldet ueber `request refresh` aktualisiert werden.
- Optional kann das GitHub-Repository archiviert werden (`gh repo archive Harpau/node-red-contrib-imap-queue`). Es wird dann schreibgeschuetzt, inklusive Issues und Pull Requests.

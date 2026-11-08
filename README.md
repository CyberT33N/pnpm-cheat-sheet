# pnpm cheat sheet

# Install

```shell
npm install -g pnpm
```







<br><br>


# Update

## Project
```shell
pnpm self-update
```




<br><br>

## Global


Method #1
```shell
pnpm self-update 11
# C:\Users\test\AppData\Local\pnpm\pnpm.CMD self-update 11
pnpm -v
```

Specific versiomn
```shell
pnpm self-update 11.1.3
# C:\Users\test\AppData\Local\pnpm\pnpm.CMD self-update 11.1.3

pnpm -v
```

Method #2
```shell
pnpm add -g pnpm
```

With nvm:
```
nvm use 26.2.0
npm install -g pnpm@11.5.0

where pnpm

pnpm -v
C:\nvm4w\nodejs\pnpm.cmd -v
```
















<br><br>
<br><br>

-- 


<br><br>
<br><br>




# CLI

## Dependencies

### Update

#### Update to latest version
```shell
pnpm up eslint --latest
```





























# Troubleshooting

## node-gyp

### gypfile:true not installing
- Es kann passieren, dass bei einem `pnpm install` `node-gyp` nicht direkt gestartet wird, weil kein `postinstall` für `gyp` vorhanden ist.

Man kann in der `package.json` zwar `gypfile` auf `true` setzen, wodurch es eigentlich starten sollte. Das funktioniert aber nicht automatisch, weil es bei `approved builds` noch nicht enthalten ist.

Wenn man es dann hinzufügt und anschließend neu installiert oder `pnpm rebuild` ausführt, wird es trotzdem nicht automatisch getriggert.

Eine Lösung ist, den `node_modules`-Ordner und die `pnpm-lock.yaml` zu löschen und dann neu zu installieren, weil `approved build` dann in der Workspace-Datei enthalten ist.

```Set-Location "C:\Projects\test"
Remove-Item ".\node_modules" -Recurse -Force

should work without delete pnpm-lock.yaml
# Remove-Item ".\pnpm-lock.yaml" -Force -ErrorAction SilentlyContinue

pnpm install
```

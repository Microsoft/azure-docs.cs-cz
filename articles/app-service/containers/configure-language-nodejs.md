---
title: Konfigurace aplikací v Node. js
description: Naučte se konfigurovat předem sestavený kontejner Node. js pro vaši aplikaci. Tento článek ukazuje nejběžnější konfigurační úlohy.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 45d7d141bc2ab85ab33be455fc3da5570b0e7f51
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920021"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Konfigurace aplikace pro Linux Node. js pro Azure App Service

Aplikace Node. js musí být nasazeny se všemi požadovanými závislostmi NPM. Kudu (App Service Deployment Engine) se automaticky spustí `npm install --production` při nasazení [úložiště Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)nebo [balíčku zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) s procesy sestavení přepnutými na. Pokud nasadíte soubory pomocí [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), budete muset požadované balíčky nahrát ručně.

Tato příručka poskytuje klíčové koncepty a pokyny pro vývojáře Node. js, kteří používají integrovaný kontejner Linux v nástroji App Service. Pokud jste nikdy Azure App Service nepoužili, postupujte nejprve podle kurzu [rychlý Start pro Node. js](quickstart-nodejs.md) a [Node. js s MongoDB](tutorial-nodejs-mongodb-app.md) .

## <a name="show-nodejs-version"></a>Zobrazit verzi Node. js

Chcete-li zobrazit aktuální verzi Node. js, spusťte následující příkaz v [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Pokud chcete zobrazit všechny podporované verze Node. js, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Nastavení verze Node. js

Chcete-li nastavit aplikaci na [podporovanou verzi Node. js](#show-nodejs-version), spusťte následující příkaz v [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Toto nastavení určuje verzi Node. js, která se má použít, a to za běhu i při automatickém obnovení balíčku v Kudu.

> [!NOTE]
> V `package.json`projektu byste měli nastavit verzi Node. js. Modul pro nasazení běží v samostatném kontejneru, který obsahuje všechny podporované verze Node. js.

## <a name="customize-build-automation"></a>Přizpůsobení automatizace sestavení

Pokud nasadíte aplikaci s použitím balíčků Git nebo zip se zapnutou možností automatizace sestavení, App Service sestavování kroků automatizace pomocí následujícího postupu:

1. Pokud je zadaný pomocí `PRE_BUILD_SCRIPT_PATH`, spusťte vlastní skript.
1. Spusťte `npm install` bez jakýchkoli příznaků, což zahrnuje npm `preinstall` a `postinstall` skripty a nainstaluje také `devDependencies`.
1. Spusťte `npm run build`, pokud je v *balíčku. JSON*zadán skript sestavení.
1. Spusťte `npm run build:azure`, pokud je v *balíčku. JSON*zadané sestavení: Azure Script.
1. Pokud je zadaný pomocí `POST_BUILD_SCRIPT_PATH`, spusťte vlastní skript.

> [!NOTE]
> Jak je popsáno v [npm docs](https://docs.npmjs.com/misc/scripts), skripty s názvem `prebuild` a `postbuild` spouštěny před a po `build`, v uvedeném pořadí, pokud jsou zadány. `preinstall` a `postinstall` běžet před `install`, respektive.

`PRE_BUILD_COMMAND` a `POST_BUILD_COMMAND` jsou proměnné prostředí, které jsou ve výchozím nastavení prázdné. Chcete-li spustit příkazy před sestavením, definujte `PRE_BUILD_COMMAND`. Chcete-li spustit příkazy po sestavení, definujte `POST_BUILD_COMMAND`.

Následující příklad určuje dvě proměnné pro řadu příkazů, které jsou odděleny čárkami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Další proměnné prostředí pro přizpůsobení automatizace sestavení naleznete v tématu [Oryx Configuration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Další informace o tom, jak App Service spouští a sestavuje aplikace Node. js v systému Linux, najdete v [dokumentaci k Oryx: jak se zjišťují a vytváří aplikace Node. js](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md).

## <a name="configure-nodejs-server"></a>Konfigurace serveru Node. js

Kontejnery Node. js se dodávají s [konfiguračního PM2](https://pm2.keymetrics.io/), což je správce produkčních procesů. Aplikaci můžete nakonfigurovat tak, aby začínala konfiguračního PM2, nebo s NPM, nebo pomocí vlastního příkazu.

- [Spustit vlastní příkaz](#run-custom-command)
- [Spustit npm Start](#run-npm-start)
- [Spustit s konfiguračního PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Spustit vlastní příkaz

App Service může aplikaci spustit pomocí vlastního příkazu, jako je například spustitelný soubor, například *Run.sh*. Pokud například chcete spustit `npm run start:prod`, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Spustit npm Start

Pokud chcete aplikaci spustit pomocí `npm start`, zajistěte, aby se v souboru *Package. JSON* používal skript `start`. Příklad:

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

Pokud chcete ve svém projektu použít vlastní soubor *Package. JSON* , spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Spustit s konfiguračního PM2

Kontejner automaticky spustí vaši aplikaci s konfiguračního PM2, když se v projektu najde jeden z běžných souborů Node. js:

- *přihrádka/webová*
- *Server. js*
- *App. js*
- *index. js*
- *hostingstart. js*
- Jeden z následujících [souborů konfiguračního PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *Process. JSON* a *ekosystém. config. js*

Můžete také nakonfigurovat vlastní spouštěcí soubor s následujícími příponami:

- Soubor *. js*
- [Soubor konfiguračního PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) s příponou *. JSON*, *. config. js*, *. yaml*nebo *. yml*

Chcete-li přidat vlastní spouštěcí soubor, spusťte následující příkaz v [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Vzdálené ladění

> [!NOTE]
> Vzdálené ladění je aktuálně ve verzi Preview.

Aplikaci Node. js můžete ladit vzdáleně v [Visual Studio Code](https://code.visualstudio.com/) , pokud ji nakonfigurujete tak, aby [běžela s konfiguračního PM2](#run-with-pm2), s výjimkou případů, kdy ji spustíte pomocí souboru *. config. js, *. yml nebo *. yaml*.

Ve většině případů není pro vaši aplikaci nutná žádná další konfigurace. Pokud je vaše aplikace spuštěná se souborem *Process. JSON* (výchozí nebo vlastní), musí mít v kořenu json vlastnost `script`. Příklad:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Chcete-li nastavit Visual Studio Code pro vzdálené ladění, nainstalujte [App Service rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Postupujte podle pokynů na stránce rozšíření a přihlaste se k Azure v Visual Studio Code.

V Průzkumníku Azure Najděte aplikaci, kterou chcete ladit, klikněte na ni pravým tlačítkem myši a vyberte **Spustit vzdálené ladění**. Klikněte na **Ano** a povolte ji pro vaši aplikaci. App Service spustí proxy server tunelu za vás a připojí ladicí program. Pak můžete v aplikaci předávat žádosti a sledovat, že se ladicí program pozastavuje v bodech přerušení.

Po dokončení ladění ukončete ladicí program výběrem možnosti **Odpojit**. Po zobrazení výzvy klikněte na **Ano** , pokud chcete zakázat vzdálené ladění. Pokud ho chcete později zakázat, klikněte znovu pravým tlačítkem myši na aplikaci v Průzkumníkovi Azure a vyberte **Zakázat vzdálené ladění**.

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

V App Service můžete [nastavit nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) mimo kód vaší aplikace. Pak k nim můžete přistupovat pomocí standardního vzoru Node. js. Chcete-li například získat přístup k nastavení aplikace s názvem `NODE_ENV`, použijte následující kód:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Spustit grunt/Bower/Gulp

Ve výchozím nastavení se Kudu spustí `npm install --production`, když rozpozná, že je nasazená aplikace Node. js. Pokud vaše aplikace vyžaduje některé z oblíbených nástrojů pro automatizaci, jako je grunt, Bower nebo Gulp, je potřeba pro její spuštění zadáním [vlastního skriptu nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) .

Pokud chcete vašemu úložišti povolit spouštění těchto nástrojů, musíte je přidat do závislostí v *balíčku. JSON.* Příklad:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Z místního okna terminálu změňte adresář na svůj kořenový adresář úložiště a spusťte následující příkazy:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Kořenový adresář úložiště má teď dva další soubory: *. Deployment* a *Deploy.sh*.

Otevřete *Deploy.sh* a najděte oddíl `Deployment`, který vypadá nějak takto:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Tato část končí spuštěným `npm install --production`. Přidejte část Code, kterou potřebujete ke spuštění požadovaného nástroje *na konci* `Deployment` části:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Podívejte se na [příklad v UKÁZCE střední. js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), kde skript nasazení také spustí vlastní příkaz `npm install`.

### <a name="bower"></a>Bower

Tento fragment kódu se spustí `bower install`.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Tento fragment kódu se spustí `gulp imagemin`.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Tento fragment kódu se spustí `grunt`.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Zjistit relaci HTTPS

V App Service dojde k [ukončení protokolu SSL](https://wikipedia.org/wiki/TLS_termination_proxy) v nástrojích pro vyrovnávání zatížení sítě, takže všechny požadavky HTTPS dosáhnou vaší aplikace jako nešifrované požadavky HTTP. Pokud vaše logika aplikace potřebuje zkontrolovat, jestli jsou požadavky uživatele zašifrované, nebo ne, zkontrolujte `X-Forwarded-Proto` záhlaví.

Oblíbená webová rozhraní umožňují přístup k informacím o `X-Forwarded-*` ve standardním vzorcích aplikací. V [expresním](https://expressjs.com/)případě můžete použít [důvěryhodné proxy](https://expressjs.com/guide/behind-proxies.html). Příklad:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otevřít relaci SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud se funkční aplikace Node. js chová odlišně v App Service nebo obsahuje chyby, zkuste následující:

- [Přístup ke streamu protokolů](#access-diagnostic-logs).
- Otestujte aplikaci místně v provozním režimu. App Service spouští aplikace v Node. js v produkčním režimu, takže je nutné zajistit, aby váš projekt fungoval v provozním režimu místně. Příklad:
    - V závislosti na vašem *balíčku. JSON*se můžou nainstalovat různé balíčky pro produkční režim (`dependencies` vs. `devDependencies`).
    - Některé webové architektury můžou nasazovat statické soubory odlišně v produkčním režimu.
    - Při spuštění v produkčním režimu mohou některé webové architektury používat vlastní spouštěcí skripty.
- Spusťte aplikaci v App Service v režimu pro vývoj. Například ve střední části [. js](https://meanjs.org/)můžete nastavit aplikaci do vývojového režimu v modulu runtime nastavením [nastavení aplikace `NODE_ENV`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: aplikace Node. js s MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Nejčastější dotazy k App Service Linux](app-service-linux-faq.md)

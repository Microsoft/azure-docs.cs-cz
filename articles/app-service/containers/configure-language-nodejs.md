---
title: Konfigurace aplikací v Node.js – Azure App Service | Dokumentace Microsoftu
description: Další informace o konfiguraci aplikací v Node.js v Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 43dc76e6d1e1ec2a6167f1d3e3cc7b8780f843db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850169"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Konfigurace aplikace v Node.js v Linuxu pro Azure App Service

Aplikace v Node.js se musí nasadit všechny požadované závislosti NPM. Modul pro nasazení služby App Service (Kudu) automaticky spustí `npm install --production` za vás při nasazování [úložiště Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), nebo [zazipovaný balíček s](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) s procesy sestavení, které jsou zapnuty. Pokud nasazujete soubory pomocí [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), ale budete muset ručně nahrát požadované balíčky.

Tato příručka obsahuje klíčové koncepty a pokyny pro vývojáře v Node.js, kteří používají předdefinované kontejneru Linuxu ve službě App Service. Pokud jste nikdy použili službu Azure App Service, postupujte [rychlý úvod k Node.js](quickstart-nodejs.md) a [Node.js s MongoDB kurzu](tutorial-nodejs-mongodb-app.md) první.

## <a name="show-nodejs-version"></a>Zobrazit verze Node.js

Chcete-li zobrazit aktuální verzi Node.js, spusťte následující příkaz [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Chcete-li zobrazit všechny podporované verze Node.js, spusťte následující příkaz [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Nastavení verze Node.js

K nastavení aplikace [podporovaná verze Node.js](#show-nodejs-version), spusťte následující příkaz [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Toto nastavení určuje verze Node.js se má použít, za běhu i během obnovení automatizované balíčku v Kudu.

> [!NOTE]
> Verze Node.js byste měli nastavit ve vašem projektu `package.json`. Modul pro nasazení se spustí v samostatný kontejner, který obsahuje všechny podporované verze Node.js.

## <a name="configure-nodejs-server"></a>Konfigurace serveru Node.js

Kontejnery Node.js součástí [PM2](http://pm2.keymetrics.io/), vedoucí proces výroby. Můžete nakonfigurovat aplikaci tak, aby PM2, nebo s NPM nebo pomocí vlastního příkazu start.

- [Spustit vlastní příkaz.](#run-custom-command)
- [Spuštění npm](#run-npm-start)
- [Spustit s PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Spustit vlastní příkaz.

App Service můžete začít vaší aplikace pomocí vlastního příkazu jako spustitelný soubor, jako jsou *run.sh*. Chcete-li například spustit `npm run start:prod`, spusťte následující příkaz [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Spuštění npm

Spustit vaši aplikaci s použitím `npm start`, dejte pozor `start` skript musí být v *package.json* souboru. Příklad:

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

Chcete-li použít vlastní *package.json* ve vašem projektu, spusťte následující příkaz [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Spustit s PM2

Kontejneru automaticky spustí vaši aplikaci s PM2, když některý z běžných souborů Node.js se nachází ve vašem projektu:

- *BIN/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Jeden z následujících [PM2 soubory](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.json* a *ecosystem.config.js*

Můžete taky nakonfigurovat vlastní spouštěcího souboru s těmito příponami:

- A *js* souboru
- A [souboru PM2](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) s příponou *.json*, *. config.js*, *.yaml*, nebo *.yml*

Chcete-li přidat vlastní spouštěcího souboru, spusťte následující příkaz [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Vzdálené ladění

> [!NOTE]
> Vzdálené ladění je aktuálně ve verzi Preview.

Můžete ladit vzdáleně v aplikaci Node.js [Visual Studio Code](https://code.visualstudio.com/) Pokud nastavujete ji na [spustit s PM2](#run-with-pm2), s výjimkou při spuštění pomocí *. config.js, *.yml, nebo *.yaml*.

Ve většině případů není žádná další konfigurace požadovaných pro vaši aplikaci. Pokud vaše aplikace se spustí s *process.json* souboru (výchozí nebo vlastní), musí mít `script` vlastnost v kořenovém adresáři JSON. Příklad:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Pokud chcete nainstalovat Visual Studio Code pro vzdálené ladění, nainstalujte [rozšíření služby App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Postupujte podle pokynů na stránce rozšíření a přihlášení do Azure ve Visual Studio Code.

V Azure explorer vyhledejte aplikaci, kterou chcete ladit, pravým tlačítkem myši a vyberte **spustit vzdálené ladění**. Klikněte na tlačítko **Ano** , aby je pro vaši aplikaci. Služby App Service se spustí tunelového propojení proxy pro vás a připojí ladicí program. Můžete pak provádět požadavky na aplikaci a zobrazit pozastavení na body přerušení ladicího programu.

Jakmile budete hotovi s laděním, ukončete ladicí program výběrem **odpojit**. Po zobrazení výzvy, měli byste kliknout na **Ano** zakázání vzdálené ladění. Zakázat později, klikněte pravým tlačítkem na aplikaci v Azure explorer znovu a vyberte **zakázat vzdálené ladění**.

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

Ve službě App Service můžete [nastavení aplikace](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) mimo kód vaší aplikace. Potom můžete přistupovat pomocí standardní vzor Node.js. Například pro přístup k nastavení aplikace volá `NODE_ENV`, použijte následující kód:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Spuštění nástroje Grunt/Bower/Gulp

Ve výchozím nastavení, spustí Kudu `npm install --production` když rozpozná nasazuje aplikace Node.js. Pokud vaše aplikace vyžaduje některé oblíbené automatizační nástroje, jako je Grunt, Bower nebo Gulp, budete muset zadat [vlastní skript nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) ho spustit.

Pokud chcete povolit úložiště ke spouštění těchto nástrojů, budete muset přidejte k závislostem v *souboru package.json.* Příklad:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

V místním okně terminálu přejděte do kořenového adresáře úložiště a spusťte následující příkazy:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Kořenového adresáře úložiště teď obsahuje dva další soubory: *.deployment* a *deploy.sh*.

Otevřít *deploy.sh* a najít `Deployment` část, která vypadá přibližně takto:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Tato část končí spuštění `npm install --production`. Přidejte část kódu, je potřeba spustit požadovaný nástroj *na konci* z `Deployment` části:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Najdete v článku [příklad v ukázce MEAN.js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), ve kterém skript nasazení také spouští vlastní `npm install` příkazu.

### <a name="bower"></a>Bower

Tento fragment kódu spustí `bower install`.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Tento fragment kódu spustí `gulp imagemin`.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Tento fragment kódu spustí `grunt`.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Zjistit relace HTTPS

Ve službě App Service [ukončení protokolu SSL](https://wikipedia.org/wiki/TLS_termination_proxy) se odehrává na nástroje pro vyrovnávání zatížení sítě, takže všechny požadavky HTTPS kontaktovat vaši aplikaci jako nešifrované požadavky HTTP. Pokud požadavkům vašich aplikací logiky ke kontrole, pokud jsou zašifrované požadavky uživatelů, nebo Ne, zkontrolujte `X-Forwarded-Proto` záhlaví.

Oblíbené webové architektury umožněte přístup `X-Forwarded-*` informace o vzoru standardní aplikace. V [Express](https://expressjs.com/), můžete použít [vztah důvěryhodnosti proxy](http://expressjs.com/guide/behind-proxies.html). Příklad:

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

Když pracovní aplikace v Node.js ve službě App Service se chová jinak nebo obsahuje chyby, proveďte následující kroky:

- [Přístup k datovému proudu protokolů](#access-diagnostic-logs).
- Místní testování aplikace v provozním režimu. App Service spouští vaše aplikace Node.js v provozním režimu, takže je třeba, abyste měli jistotu, že projekt pracuje podle očekávání v provozním režimu místně. Příklad:
    - V závislosti na vaší *package.json*, mohou být nainstalovány jiné balíčky k provozním režimu (`dependencies` vs. `devDependencies`).
    - Některá webová rozhraní může nasadit statické soubory jinak než v provozním režimu.
    - Některá webová rozhraní může používat vlastní spouštěcí skripty, při spuštění v produkčním režimu.
- Spuštění aplikace ve službě App Service v režimu pro vývoj. Například v [MEAN.js](http://meanjs.org/), aplikaci můžete nastavit do režimu pro vývoj v modulu runtime [nastavení `NODE_ENV` nastavení aplikace, které](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Aplikace v Node.js s MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [App Service Linuxu – nejčastější dotazy](app-service-linux-faq.md)
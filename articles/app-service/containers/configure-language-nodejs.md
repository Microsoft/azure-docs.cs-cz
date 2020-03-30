---
title: Konfigurace aplikací Node.js
description: Přečtěte si, jak pro vaši aplikaci nakonfigurovat předem sestavený kontejner Node.js. Tento článek ukazuje nejběžnější úlohy konfigurace.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: fdc5129fc395f99cb4c244414ea952b2776dc4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252724"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Konfigurace aplikace Linux Node.js pro službu Azure App Service

Aplikace Node.js musí být nasazeny se všemi požadovanými závislostmi NPM. Modul nasazení služby App Service (Kudu) se automaticky spustí `npm install --production` při nasazení úložiště [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)nebo [balíček Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) se zapnutými procesy sestavení. Pokud však nasadíte soubory pomocí [protokolu FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), musíte požadované balíčky odeslat ručně.

Tato příručka obsahuje klíčové koncepty a pokyny pro vývojáře Node.js, kteří používají integrovaný kontejner Linuxu ve službě App Service. Pokud jste službu Azure App Service nikdy nepoužívali, postupujte nejprve podle [rychlého startu Node.js](quickstart-nodejs.md) a [node.js s kurzem MongoDB.](tutorial-nodejs-mongodb-app.md)

## <a name="show-nodejs-version"></a>Zobrazit verzi souboru Node.js

Chcete-li zobrazit aktuální verzi souboru Node.js, spusťte v [prostředí Cloud Shell](https://shell.azure.com)následující příkaz :

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Chcete-li zobrazit všechny podporované verze souboru Node.js, spusťte v [prostředí Cloud Shell](https://shell.azure.com)následující příkaz :

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Nastavit verzi souboru Node.js

Chcete-li aplikaci nastavit na [podporovanou verzi souboru Node.js](#show-nodejs-version), spusťte v [prostředí Cloud Shell](https://shell.azure.com)následující příkaz :

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Toto nastavení určuje verzi Node.js, která má být používána, a to jak za běhu, tak při automatickém obnovení balíčků v Kudu.

> [!NOTE]
> Měli byste nastavit verzi Node.js v `package.json`projektu . Modul nasazení běží v samostatném kontejneru, který obsahuje všechny podporované verze Node.js.

## <a name="customize-build-automation"></a>Přizpůsobení automatizace sestavení

Pokud aplikaci nasadíte pomocí balíčků Git nebo zip se zapnutou automatizací sestavení, služba App Service provede automatizaci automatizace následujícím pořadím:

1. Spusťte vlastní `PRE_BUILD_SCRIPT_PATH`skript, pokud je určen programem .
1. Spustit `npm install` bez příznaků, který `preinstall` zahrnuje `postinstall` npm a `devDependencies`skripty a také nainstaluje .
1. Pokud `npm run build` je v *souboru package.json*zadán skript sestavení.
1. Spusťte, `npm run build:azure` pokud je v *souboru package.json*zadán skript build:azure .
1. Spusťte vlastní `POST_BUILD_SCRIPT_PATH`skript, pokud je určen programem .

> [!NOTE]
> Jak je popsáno v [npm docs](https://docs.npmjs.com/misc/scripts), skripty pojmenované `prebuild` a `postbuild` spustit před a po `build`, respektive, pokud je zadán. `preinstall`a `postinstall` běžet před `install`a po , resp.

`PRE_BUILD_COMMAND`a `POST_BUILD_COMMAND` jsou proměnné prostředí, které jsou ve výchozím nastavení prázdné. Chcete-li spustit příkazy `PRE_BUILD_COMMAND`předběžného sestavení, definujte . Chcete-li spustit příkazy `POST_BUILD_COMMAND`po sestavení, definujte .

Následující příklad určuje dvě proměnné řady příkazů oddělených čárkami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Další proměnné prostředí pro přizpůsobení automatizace sestavení naleznete v [tématu Konfigurace Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Další informace o tom, jak služba App Service běží a vytváří aplikace Node.js v Linuxu, najdete v [dokumentaci oryxu: Jak jsou zjištěny a sestaveny aplikace Node.js](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md).

## <a name="configure-nodejs-server"></a>Konfigurace serveru Node.js

Kontejnery Node.js jsou dodávány s [pm2](https://pm2.keymetrics.io/), manažerem výrobního procesu. Aplikaci můžete nakonfigurovat tak, aby začínala pomocí PM2, npm nebo pomocí vlastního příkazu.

- [Spustit vlastní příkaz](#run-custom-command)
- [Spustit začátek npm](#run-npm-start)
- [Běh s PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Spustit vlastní příkaz

Služba App Service může aplikaci spustit pomocí vlastního příkazu, například spustitelného souboru, jako je *run.sh*. Chcete-li například spustit `npm run start:prod`, spusťte následující příkaz v prostředí Cloud [Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Spustit začátek npm

Chcete-li aplikaci spustit `npm start`pomocí `start` aplikace , zkontrolujte, zda je skript v souboru *package.json.* Například:

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

Chcete-li v projektu použít vlastní *soubor package.json,* spusťte v [prostředí Cloud Shell](https://shell.azure.com)následující příkaz :

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Běh s PM2

Kontejner automaticky spustí aplikaci s PM2, když se v projektu najde jeden ze společných souborů Node.js:

- *přihrádka/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Jeden z následujících [souborů PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.json* a *ecosystem.config.js*

Můžete také nakonfigurovat vlastní počáteční soubor s následujícími příponami:

- Soubor *JS*
- [Soubor PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) s příponou *JSon*, *.config.js*, *.yaml*nebo *.yml*

Chcete-li přidat vlastní počáteční soubor, spusťte v [prostředí Cloud Shell](https://shell.azure.com)následující příkaz :

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Vzdálené ladění

> [!NOTE]
> Vzdálené ladění je aktuálně ve verzi Preview.

Aplikaci Node.js můžete ladit vzdáleně v [kódu sady Visual Studio,](https://code.visualstudio.com/) pokud ji nakonfigurujete tak, aby běžela [s PM2](#run-with-pm2), s výjimkou případů, kdy ji spustíte pomocí *.config.js, *.yml nebo *.yaml*.

Ve většině případů není pro vaši aplikaci vyžadována žádná další konfigurace. Pokud je vaše aplikace spuštěná se souborem *process.json* `script` (výchozí nebo vlastní), musí mít vlastnost v kořenovém adresáři JSON. Například:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Chcete-li nastavit kód sady Visual Studio pro vzdálené ladění, nainstalujte [rozšíření služby App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Postupujte podle pokynů na stránce rozšíření a přihlaste se k Azure v kódu Visual Studia.

V Průzkumníkovi Azure najděte aplikaci, kterou chcete ladit, klikněte na ni pravým tlačítkem myši a vyberte **Spustit vzdálené ladění**. Kliknutím na **Ano** ji povolíte pro svou aplikaci. Služba App Service spustí proxy tunelové propojení za vás a připojí ladicí program. Potom můžete provést požadavky do aplikace a vidět ladicí program pozastavení v bodech přerušení.

Po dokončení ladění zastavte ladicí program výběrem **možnosti Odpojit**. Po zobrazení výzvy byste měli klepnutím na tlačítko **Ano** zakázat vzdálené ladění. Chcete-li ji později zakázat, klepněte znovu pravým tlačítkem myši na aplikaci v průzkumníku Azure a vyberte **zakázat vzdálené ladění**.

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

Ve službě App Service můžete [nastavit nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) mimo kód aplikace. Pak k nim můžete přistupovat pomocí standardního vzoru Node.js. Chcete-li například získat `NODE_ENV`přístup k nastavení aplikace s názvem , použijte následující kód:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Běh Grunt/Bower/Doušek

Ve výchozím nastavení se `npm install --production` Kudu spustí, když rozpozná, že je nasazena aplikace Node.js. Pokud vaše aplikace vyžaduje některý z oblíbených nástrojů pro automatizaci, jako je Grunt, Bower nebo Gulp, musíte zadat [vlastní skript nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) pro jeho spuštění.

Chcete-li povolit úložiště ke spuštění těchto nástrojů, je třeba je přidat do závislostí v *package.json.* Například:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Z okna místního terminálu změňte adresář do kořenového adresáře úložiště a spusťte následující příkazy:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Kořenúložiště má nyní dva další soubory: *.deployment* a *deploy.sh*.

Otevřete *deploy.sh* `Deployment` a najděte sekci, která vypadá takto:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Tato část končí `npm install --production`spuštěním . Přidejte část kódu, kterou potřebujete ke spuštění `Deployment` požadovaného nástroje na *konci* oddílu:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Podívejte se na [příklad v ukázce MEAN.js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), kde skript nasazení také spouští vlastní `npm install` příkaz.

### <a name="bower"></a>Bower

Tento úryvek `bower install`se spustí .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Tento úryvek `gulp imagemin`se spustí .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Tento úryvek `grunt`se spustí .

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Rozpoznat relaci HTTPS

Ve službě App Service dojde k [ukončení SSL](https://wikipedia.org/wiki/TLS_termination_proxy) v síťových nástrojích pro vyrovnávání zatížení, takže všechny požadavky HTTPS se dostanou do vaší aplikace jako nešifrované požadavky HTTP. Pokud vaše logika aplikace potřebuje zkontrolovat, jestli jsou požadavky `X-Forwarded-Proto` uživatelů zašifrované nebo ne, zkontrolujte záhlaví.

Oblíbené webové architektury umožňují `X-Forwarded-*` přístup k informacím ve standardním vzoru aplikace. V [expressu](https://expressjs.com/)můžete použít [proxy servery důvěryhodnosti](https://expressjs.com/guide/behind-proxies.html). Například:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otevření relace SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud se pracovní aplikace Node.js ve službě App Service chová jinak nebo má chyby, vyzkoušejte následující:

- [Přístup k datovému proudu protokolu](#access-diagnostic-logs).
- Otestujte aplikaci místně v produkčním režimu. Služba App Service spouští aplikace Node.js v produkčním režimu, takže se musíte ujistit, že váš projekt funguje podle očekávání v produkčním režimu místně. Například:
    - V závislosti na *souboru package.json*mohou být`dependencies` pro `devDependencies`produkční režim nainstalovány různé balíčky ( vs. ).
    - Některé webové architektury mohou nasadit statické soubory odlišně v produkčním režimu.
    - Některé webové architektury mohou při spuštění v produkčním režimu používat vlastní spouštěcí skripty.
- Spusťte aplikaci ve službě App Service v režimu vývoje. Například v [MEAN.js](https://meanjs.org/)můžete nastavit aplikaci do vývojového režimu za běhu [ `NODE_ENV` nastavením nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Aplikace Node.js s MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Nejčastější dotazy k aplikační službě Linux](app-service-linux-faq.md)

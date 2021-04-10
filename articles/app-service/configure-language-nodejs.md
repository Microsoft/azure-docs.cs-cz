---
title: Konfigurace aplikací Node.js
description: Naučte se konfigurovat aplikaci Node.js v nativních instancích systému Windows nebo v předem sestaveném kontejneru Linux v Azure App Service. Tento článek ukazuje nejběžnější konfigurační úlohy.
ms.custom: devx-track-js, devx-track-azurecli
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 6a6f782768db12c2ce75f5cf1e66100222f24446
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095199"
---
# <a name="configure-a-nodejs-app-for-azure-app-service"></a>Konfigurace aplikace Node.js pro Azure App Service

Node.js aplikace musí být nasazeny se všemi požadovanými závislostmi NPM. Modul pro nasazení App Service se automaticky spustí `npm install --production` při nasazení [úložiště Git](deploy-local-git.md)nebo [balíčku zip](deploy-zip.md) s povoleným automatizací sestavení. Pokud nasadíte soubory pomocí [FTP/S](deploy-ftp.md), budete muset požadované balíčky nahrát ručně.

Tato příručka poskytuje klíčové koncepty a pokyny pro Node.js vývojářů, kteří nasazují do App Service. Pokud jste Azure App Service nikdy nepoužili, postupujte jako první v kurzu [Node.js rychlý Start](quickstart-nodejs.md) a [Node.js s MongoDB](tutorial-nodejs-mongodb-app.md) .

## <a name="show-nodejs-version"></a>Zobrazit verzi Node.js

::: zone pivot="platform-windows"  

Chcete-li zobrazit aktuální verzi Node.js, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp config appsettings list --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

Pokud chcete zobrazit všechny podporované verze Node.js, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp list-runtimes | grep node
```

::: zone-end

::: zone pivot="platform-linux"

Chcete-li zobrazit aktuální verzi Node.js, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Pokud chcete zobrazit všechny podporované verze Node.js, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

::: zone-end

## <a name="set-nodejs-version"></a>Nastavit verzi Node.js

::: zone pivot="platform-windows"  

Chcete-li nastavit aplikaci na [podporovanou verzi Node.js](#show-nodejs-version), spusťte následující příkaz v [Cloud Shell](https://shell.azure.com) nastavte `WEBSITE_NODE_DEFAULT_VERSION` na podporovanou verzi:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

Toto nastavení určuje Node.js verzi, která se má použít, za běhu i během automatického obnovování balíčku během automatizace sestavení App Service.

> [!NOTE]
> Měli byste nastavit verzi Node.js v projektu `package.json` . Modul pro nasazení běží v samostatném procesu, který obsahuje všechny podporované verze Node.js.

::: zone-end

::: zone pivot="platform-linux"

Chcete-li nastavit aplikaci na [podporovanou verzi Node.js](#show-nodejs-version), spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Toto nastavení určuje Node.js verzi, která se má použít, za běhu i při automatickém obnovení balíčků v Kudu.

> [!NOTE]
> Měli byste nastavit verzi Node.js v projektu `package.json` . Modul pro nasazení běží v samostatném kontejneru, který obsahuje všechny podporované verze Node.js.

::: zone-end

## <a name="get-port-number"></a>Získat číslo portu

Node.js aplikace potřebuje naslouchat správnému portu pro příjem příchozích požadavků.

::: zone pivot="platform-windows"  

V App Service ve Windows se Node.js aplikace hostují s [IISNode](https://github.com/Azure/iisnode)a vaše Node.js aplikace by měla naslouchat portu zadanému v `process.env.PORT` proměnné. Následující příklad ukazuje, jak to provedete v jednoduché aplikaci Express:

::: zone-end

::: zone pivot="platform-linux"  

App Service nastaví proměnnou prostředí `PORT` v kontejneru Node.js a přepošle příchozí požadavky do kontejneru s tímto číslem portu. Aby bylo možné přijímat žádosti, vaše aplikace by měla naslouchat tomuto portu pomocí `process.env.PORT` . Následující příklad ukazuje, jak to provedete v jednoduché aplikaci Express:

::: zone-end

```javascript
const express = require('express')
const app = express()
const port = process.env.PORT || 3000

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`)
})
```

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Přizpůsobení automatizace sestavení

Pokud nasadíte aplikaci s použitím balíčků Git nebo zip se zapnutou možností automatizace sestavení, App Service sestavování kroků automatizace pomocí následujícího postupu:

1. Spusťte vlastní skript, pokud je určen `PRE_BUILD_SCRIPT_PATH` .
1. Spusťte `npm install` bez příznaků, které zahrnují npm `preinstall` a `postinstall` skripty a také nainstaluje `devDependencies` .
1. Spustit, `npm run build` Pokud je v *package.js* zadán skript sestavení.
1. Spustit, `npm run build:azure` Pokud je ve vašem *package.jsv* systému zadaný Build: Azure Script.
1. Spusťte vlastní skript, pokud je určen `POST_BUILD_SCRIPT_PATH` .

> [!NOTE]
> Jak je popsáno v [dokumentaci npm docs](https://docs.npmjs.com/misc/scripts), skripty s názvem `prebuild` a `postbuild` spouštěné před a za `build` , v uvedeném pořadí, pokud jsou zadány. `preinstall` a `postinstall` spusťte před a za v `install` uvedeném pořadí.

`PRE_BUILD_COMMAND` a `POST_BUILD_COMMAND` jsou proměnné prostředí, které jsou ve výchozím nastavení prázdné. Chcete-li spustit příkazy před sestavením, definujte `PRE_BUILD_COMMAND` . Chcete-li spustit příkazy po sestavení, definujte `POST_BUILD_COMMAND` .

Následující příklad určuje dvě proměnné pro řadu příkazů, které jsou odděleny čárkami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Další proměnné prostředí pro přizpůsobení automatizace sestavení naleznete v tématu [Oryx Configuration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Další informace o tom, jak App Service spouští a sestavuje Node.js aplikace v systému Linux, najdete v [dokumentaci k Oryx: jak se zjišťují a vytváří aplikace Node.js](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md).

## <a name="configure-nodejs-server"></a>Konfigurace serveru Node.js

Kontejnery Node.js jsou dodávány s [konfiguračního PM2](https://pm2.keymetrics.io/)a správcem produkčního procesu. Aplikaci můžete nakonfigurovat tak, aby začínala konfiguračního PM2, nebo s NPM, nebo pomocí vlastního příkazu.

- [Spustit vlastní příkaz](#run-custom-command)
- [Spustit npm Start](#run-npm-start)
- [Spustit s konfiguračního PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Spustit vlastní příkaz

App Service může aplikaci spustit pomocí vlastního příkazu, jako je například spustitelný soubor, například *Run.sh*. Pokud třeba chcete spustit `npm run start:prod` , spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Spustit npm Start

Pokud chcete aplikaci spustit pomocí `npm start` , stačí, když zajistěte, aby `start` byl skript v *package.js* souboru. Například:

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

Chcete-li použít vlastní *package.js* v projektu, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Spustit s konfiguračního PM2

Kontejner automaticky spustí vaši aplikaci s konfiguračního PM2, když se v projektu najde jeden ze běžných Node.js souborů:

- *přihrádka/webová*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Jeden z následujících [souborů konfiguračního PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.jsv* a *ecosystem.config.js*

Můžete také nakonfigurovat vlastní spouštěcí soubor s následujícími příponami:

- Soubor *. js*
- [Soubor konfiguračního PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) s příponou *. JSON*, *.config.js*, *. yaml* nebo *. yml*

Chcete-li přidat vlastní spouštěcí soubor, spusťte následující příkaz v [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Vzdálené ladění

> [!NOTE]
> Vzdálené ladění je aktuálně ve verzi Preview.

Aplikaci Node.js můžete vzdáleně ladit v [Visual Studio Code](https://code.visualstudio.com/) Pokud ji nakonfigurujete tak, aby [běžela s konfiguračního PM2](#run-with-pm2), s výjimkou případů, kdy ji spustíte pomocí * .config.js, *. yml nebo *. yaml*.

Ve většině případů není pro vaši aplikaci nutná žádná další konfigurace. Pokud je vaše aplikace spuštěná s *process.jsv* souboru (výchozí nebo vlastní), musí mít `script` v kořenu JSON vlastnost. Například:

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

::: zone-end

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

V App Service můžete [nastavit nastavení aplikace](configure-common.md) mimo kód vaší aplikace. Pak k nim můžete přistupovat pomocí standardního Node.jsho vzoru. Například pro přístup k aplikačnímu nastavení s názvem `NODE_ENV` použijete následující kód:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Spustit grunt/Bower/Gulp

Ve výchozím nastavení se App Service Automation Build spustí, `npm install --production` když rozpozná Node.js aplikace nasazené prostřednictvím nasazení Git nebo zip s povolenou automatizací sestavení. Pokud vaše aplikace vyžaduje některé z oblíbených nástrojů pro automatizaci, jako je grunt, Bower nebo Gulp, je potřeba pro její spuštění zadáním [vlastního skriptu nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) .

Pokud chcete vašemu úložišti povolit spouštění těchto nástrojů, musíte je přidat k závislostem v *package.jsna.* Například:

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

Otevřete *Deploy.sh* a vyhledejte `Deployment` část, která vypadá takto:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Tato část končí na běhu `npm install --production` . Přidejte část Code, kterou potřebujete ke spuštění požadovaného nástroje *na konci* `Deployment` oddílu:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Podívejte se na [příklad v ukázce MEAN.js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), kde skript nasazení také spustí vlastní `npm install` příkaz.

### <a name="bower"></a>Bower

Tento fragment kódu je spuštěn `bower install` .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Tento fragment kódu je spuštěn `gulp imagemin` .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Tento fragment kódu je spuštěn `grunt` .

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Zjistit relaci HTTPS

V App Service dojde k [ukončení protokolu SSL](https://wikipedia.org/wiki/TLS_termination_proxy) v nástrojích pro vyrovnávání zatížení sítě, takže všechny požadavky HTTPS dosáhnou vaší aplikace jako nešifrované požadavky HTTP. Pokud vaše logika aplikace potřebuje, aby zkontrolovala, jestli jsou požadavky uživatele zašifrované, zkontrolujte `X-Forwarded-Proto` záhlaví.

Oblíbená webová rozhraní umožňují přístup k `X-Forwarded-*` informacím ve standardním vzoru aplikace. V [expresním](https://expressjs.com/)případě můžete použít [důvěryhodné proxy](https://expressjs.com/guide/behind-proxies.html). Příklad:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

::: zone pivot="platform-windows"  

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end


::: zone pivot="platform-linux"

## <a name="monitor-with-application-insights"></a>Monitorování pomocí Application Insights

Application Insights umožňuje monitorovat výkon, výjimky a využití vaší aplikace bez nutnosti provádět změny kódu. Pokud chcete připojit agenta Application Insights, na portálu klikněte na svou webovou aplikaci a v části **Nastavení** vyberte **Application Insights** a pak vyberte **zapnout Application Insights**. Pak vyberte existující prostředek služby App Insights nebo vytvořte nový. Nakonec vyberte **použít** v dolní části. Při instrumentaci webové aplikace pomocí PowerShellu se prosím podívejte na [tyto pokyny](../azure-monitor/app/azure-web-apps.md?tabs=netcore#enabling-through-powershell) .

Tento agent bude monitorovat vaši aplikaci Node.js na straně serveru. Chcete-li monitorovat JavaScript na straně klienta, [přidejte do projektu sadu JavaScript SDK](../azure-monitor/app/javascript.md). 

Další informace najdete v [poznámkách k verzi rozšíření Application Insights](../azure-monitor/app/web-app-extension-release-notes.md).

::: zone-end

## <a name="troubleshooting"></a>Řešení potíží

Pokud se pracovní Node.js aplikace chová jinak v App Service nebo obsahuje chyby, zkuste následující:

- [Přístup ke streamu protokolů](#access-diagnostic-logs).
- Otestujte aplikaci místně v provozním režimu. App Service spouští aplikace Node.js v produkčním režimu, takže je potřeba zajistit, aby váš projekt fungoval v produkčním režimu v místním prostředí. Například:
    - V závislosti na vaší *package.js* se můžou v produkčním režimu ( `dependencies` vs.) nainstalovat různé balíčky `devDependencies` .
    - Některé webové architektury můžou nasazovat statické soubory odlišně v produkčním režimu.
    - Při spuštění v produkčním režimu mohou některé webové architektury používat vlastní spouštěcí skripty.
- Spusťte aplikaci v App Service v režimu pro vývoj. Například v [MEAN.js](https://meanjs.org/)můžete nastavit aplikaci do vývojového režimu v modulu runtime nastavením [ `NODE_ENV` nastavení aplikace](configure-common.md).

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Node.js aplikace pomocí MongoDB](tutorial-nodejs-mongodb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [Nejčastější dotazy k App Service v Linuxu](faq-app-service-linux.md)

::: zone-end

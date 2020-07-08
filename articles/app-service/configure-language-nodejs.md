---
title: Konfigurace aplikací pro Windows Node.js
description: Naučte se konfigurovat aplikaci Node.js v nativních instancích systému Windows App Service. Tento článek ukazuje nejběžnější konfigurační úlohy.
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 9f4ccdd04b8d57784f452dc28fa4507fb7ea94c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84908076"
---
# <a name="configure-a-windows-nodejs-app-for-azure-app-service"></a>Konfigurace aplikace Node.js pro Windows pro Azure App Service

Node.js aplikace musí být nasazeny se všemi požadovanými závislostmi NPM. Modul pro nasazení App Service se automaticky spustí `npm install --production` při nasazení [úložiště Git](deploy-local-git.md)nebo [balíčku zip](deploy-zip.md) s povoleným automatizací sestavení. Pokud nasadíte soubory pomocí [FTP/S](deploy-ftp.md), budete muset požadované balíčky nahrát ručně. Informace o aplikacích pro Linux najdete v tématu [Konfigurace aplikace typu php pro Linux pro Azure App Service](containers/configure-language-nodejs.md).

Tato příručka poskytuje klíčové koncepty a pokyny pro Node.js vývojářů, kteří nasazují do App Service. Pokud jste Azure App Service nikdy nepoužili, postupujte jako první v kurzu [Node.js rychlý Start](app-service-web-get-started-nodejs.md) a [Node.js s MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md) .

## <a name="show-nodejs-version"></a>Zobrazit verzi Node.js

Chcete-li zobrazit aktuální verzi Node.js, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

Pokud chcete zobrazit všechny podporované verze Node.js, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp list-runtimes | grep node
```

## <a name="set-nodejs-version"></a>Nastavit verzi Node.js

Chcete-li nastavit aplikaci na [podporovanou verzi Node.js](#show-nodejs-version), spusťte následující příkaz v [Cloud Shell](https://shell.azure.com) nastavte `WEBSITE_NODE_DEFAULT_VERSION` na podporovanou verzi:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

Toto nastavení určuje Node.js verzi, která se má použít, za běhu i během automatického obnovování balíčku během automatizace sestavení App Service.

> [!NOTE]
> Měli byste nastavit verzi Node.js v projektu `package.json` . Modul pro nasazení běží v samostatném procesu, který obsahuje všechny podporované verze Node.js.

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

V App Service můžete [nastavit nastavení aplikace](configure-common.md) mimo kód vaší aplikace. Pak k nim můžete přistupovat pomocí standardního Node.jsho vzoru. Chcete-li například získat přístup k nastavení aplikace s názvem `NODE_ENV` , použijte následující kód:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Spustit grunt/Bower/Gulp

Ve výchozím nastavení se App Service Automation Build spustí, `npm install --production` když rozpozná Node.js aplikace nasazené prostřednictvím Gitu (nebo pomocí nasazení zip s povolenou automatizací buildu). Pokud vaše aplikace vyžaduje některé z oblíbených nástrojů pro automatizaci, jako je grunt, Bower nebo Gulp, je potřeba pro její spuštění zadáním [vlastního skriptu nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) .

Pokud chcete vašemu úložišti povolit spouštění těchto nástrojů, musíte je přidat k závislostem v *package.jsna.* Příklad:

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

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud se pracovní Node.js aplikace chová jinak v App Service nebo obsahuje chyby, zkuste následující:

- [Přístup ke streamu protokolů](#access-diagnostic-logs).
- Otestujte aplikaci místně v provozním režimu. App Service spouští aplikace Node.js v produkčním režimu, takže je potřeba zajistit, aby váš projekt fungoval v produkčním režimu v místním prostředí. Příklad:
    - V závislosti na vaší *package.js*se můžou v produkčním režimu ( `dependencies` vs.) nainstalovat různé balíčky `devDependencies` .
    - Některé webové architektury můžou nasazovat statické soubory odlišně v produkčním režimu.
    - Při spuštění v produkčním režimu mohou některé webové architektury používat vlastní spouštěcí skripty.
- Spusťte aplikaci v App Service v režimu pro vývoj. Například v [MEAN.js](https://meanjs.org/)můžete nastavit aplikaci do vývojového režimu v modulu runtime nastavením [ `NODE_ENV` nastavení aplikace](configure-common.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Node.js aplikace pomocí MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)


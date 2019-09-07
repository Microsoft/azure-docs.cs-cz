---
title: Vytvoření webové aplikace v Node. js – Azure App Service
description: Jak nasadit aplikaci Node. js do Azure App Service
author: msangapu
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.service: app-service
ms.devlang: javascript
ms.openlocfilehash: 8d679a95cc89c2ae7774b7f7b51b9d0aadd89d12
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390978"
---
# <a name="create-a-nodejs-app-in-azure"></a>Vytvoření aplikace v Node. js v Azure

Azure App Service poskytuje vysoce škálovatelnou službu s automatickými opravami pro hostování webů. V tomto rychlém startu se dozvíte, jak nasadit aplikaci Node. js do Azure App Service.

## <a name="prerequisites"></a>Požadavky

Pokud nemáte účet Azure, [Zaregistrujte si ještě dnes](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) bezplatný účet s $200 v kreditech Azure, abyste si vyzkoušeli libovolnou kombinaci služeb.

Potřebujete [Visual Studio Code](https://code.visualstudio.com/) nainstalovat spolu s [Node. js a npm](https://nodejs.org/en/download), správce balíčků Node. js.

Budete taky muset nainstalovat [rozšíření Azure App Service](vscode:extension/ms-azuretools.vscode-azureappservice), které můžete použít k vytvoření, správě a nasazení Web Apps pro Linux na platformě Azure jako službu (PaaS).

### <a name="sign-in"></a>Přihlášení

Po instalaci rozšíření se přihlaste ke svému účtu Azure. V řádku aktivity klikněte na logo Azure a zobrazte Průzkumníka **Azure App Service** . Klikněte na **Přihlásit se k Azure...** a postupujte podle pokynů.

![Přihlaste se k Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Řešení potíží

Pokud se zobrazí chyba **"nelze najít předplatné s názvem [ID předplatného]"** , může to být způsobeno tím, že jste za proxy serverem a nemůžete získat přístup k rozhraní API Azure. Pomocí `HTTP_PROXY` použijte `HTTPS_PROXY`konfiguracia proměnné prostředí s informacemi o proxy serveru v terminálu. `export`

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Pokud se při nastavení proměnných prostředí problém nevyřeší, kontaktujte nás kliknutím na tlačítko **jsem v problému** níže.

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

Než budete pokračovat, ujistěte se, že máte nainstalované a nakonfigurované všechny požadavky.

V VS Code byste měli na stavovém řádku a v rámci vašeho předplatného v Průzkumníkovi **služby Azure App Service** vidět vaši e-mailovou adresu Azure.

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Vytvoření aplikace Node. js

Dále vytvořte aplikaci Node. js, která může být nasazena do cloudu. V tomto rychlém startu se používá generátor aplikace k rychlému vytvoření uživatelského rozhraní aplikace z terminálu.

> [!TIP]
> Pokud jste již dokončili [kurz k Node. js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), můžete přeskočit k [části nasazení webu](#deploy-the-website).

### <a name="install-the-express-generator"></a>Instalace expresního generátoru

[Express](https://www.expressjs.com) je oblíbená architektura pro sestavování a spouštění aplikací v Node. js. Pomocí nástroje [expresního generátoru](https://expressjs.com/en/starter/generator.html) můžete vytvořit novou aplikaci Express. Generátor Express se dodává jako modul npm a instaluje se pomocí nástroje `npm`příkazového řádku npm.

```bash
npm install -g express-generator
```

`-g` Přepínač na svém počítači globálně nainstaluje expresní generátor, takže ho můžete spustit odkudkoli.

### <a name="scaffold-a-new-application"></a>Vytvoření nové aplikace pomocí uživatelského rozhraní

V dalším kroku vygeneruje nová expresní aplikace `myExpressApp` volanou spuštěním:

```bash
express myExpressApp --view pug --git
```

Parametry říká generátoru, aby používal modul šablon [Pug](https://pugjs.org/api/getting-started.html) (dříve označovaný jako `jade`) a vytvořil `.gitignore` soubor. `--view pug --git`

Chcete-li nainstalovat všechny závislosti aplikace, klikněte na novou složku a spusťte příkaz `npm install`.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Spuštění aplikace

Dále zkontrolujte, zda je aplikace spuštěna. Z terminálu spusťte aplikaci pomocí `npm start` příkazu pro spuštění serveru.

```bash
npm start
```

Nyní otevřete prohlížeč a přejděte na [http://localhost:3000](http://localhost:3000)místo, kde by se mělo zobrazit něco podobného:

![Spuštění expresní aplikace](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-website"></a>Nasazení webu

V této části nasadíte web Node. js pomocí VS Code a rozšíření Azure App Service. V tomto rychlém startu se používá základní model nasazení, ve kterém je vaše aplikace zip a nasazená do Azure Web App on Linux.

### <a name="deploy-using-azure-app-service"></a>Nasazení pomocí Azure App Service

Nejprve otevřete složku aplikace v VS Code.

```bash
code .
```

V Průzkumníku **Azure App Service** klikněte na ikonu modré šipky nahoru a nasaďte svoji aplikaci do Azure.

![Nasazení do webové aplikace](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> Z **palety příkazů** (CTRL + SHIFT + P) se dá nasadit taky tak, že zadáte ' nasadit do webové aplikace ' **a spustíte Azure App Service: Příkaz nasadit do webové** aplikace

1. Vyberte adresář, který aktuálně máte otevřený `myExpressApp`.

2. Vyberte **vytvořit novou webovou aplikaci**.

3. Zadejte globálně jedinečný název vaší webové aplikace a stiskněte klávesu ENTER. Platnými znaky pro název aplikace jsou "a-z", "0-9" a "-".

4. Vyberte si **verzi Node. js**, doporučujeme LTS.

    Kanál oznámení zobrazuje prostředky Azure, které se vytvářejí pro vaši aplikaci.

Po zobrazení výzvy k aktualizaci konfigurace, která se má `npm install` spustit na cílovém serveru, klikněte na Ano. Vaše aplikace se pak nasadí.

![Nakonfigurované nasazení](./media/quickstart-nodejs/server-build.png)

Po zahájení nasazení se zobrazí výzva, abyste aktualizovali pracovní prostor tak, aby se později v nasazeních automaticky nacílena na stejnou App Service webovou aplikaci. Pokud chcete zajistit, aby se vaše změny nasadily do správné aplikace, klikněte na **Ano** .

![Nakonfigurované nasazení](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Ujistěte se, že aplikace naslouchá na portu poskytnutém proměnnou prostředí portu: `process.env.PORT`.

### <a name="browse-the-website"></a>Přejděte na web

Po dokončení nasazení klikněte v příkazovém řádku na **Procházet web** a zobrazte si hned nasazený web.

### <a name="troubleshooting"></a>Řešení potíží

Pokud se zobrazí chyba **"nemáte oprávnění k zobrazení tohoto adresáře nebo stránky."** , aplikace se pravděpodobně nespustila správně. Přejděte k další části a podívejte se na výstup protokolu, který vyhledá a opraví chybu. Pokud ji nemůžete opravit, kontaktujte nás kliknutím na tlačítko jsem v níže uvedeném **problému** . Rádi vám pomůžeme!

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="updating-the-website"></a>Aktualizace webu

Změny v této aplikaci můžete nasadit pomocí stejného procesu a volbou existující aplikace namísto vytváření nového.

## <a name="viewing-logs"></a>Zobrazení protokolů

V této části se dozvíte, jak zobrazit (neboli "koncová") protokoly z běžícího webu. Všechna volání na `console.log` webu se zobrazí v okně výstup v Visual Studio Code.

Najděte aplikaci v Průzkumníkovi **služby Azure App Service** , klikněte pravým tlačítkem na aplikaci a vyberte **Zobrazit protokoly streamování**.

Po zobrazení výzvy vyberte možnost povolit protokolování a restartovat aplikaci. Po restartování aplikace se otevře okno VS Code výstup s připojením k datovému proudu protokolu.

![Zobrazit protokoly streamování](./media/quickstart-nodejs/view-logs.png)

![Povolit protokolování a restartování](./media/quickstart-nodejs/enable-restart.png)

Po několika sekundách se zobrazí zpráva oznamující, že jste připojeni ke službě streamování protokolů.

```bash
Connecting to log-streaming service...
2017-12-21 17:33:51.428 INFO  - Container practical-mustache_2 for site practical-mustache initialized successfully.
2017-12-21 17:33:56.500 INFO  - Container logs
```

Aktualizujte stránku několikrát v prohlížeči, aby se zobrazil výstup protokolu.

```bash
2017-12-21 17:35:17.774 INFO  - Container logs
2017-12-21T17:35:14.955412230Z GET / 304 141.798 ms - -
2017-12-21T17:35:15.248930479Z GET /stylesheets/style.css 304 3.180 ms - -
2017-12-21T17:35:15.378623115Z GET /favicon.ico 404 53.839 ms - 995
```

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Další postup

Blahopřejeme, úspěšně jste dokončili tento rychlý Start.

Pak se podívejte na další rozšíření Azure.

* [Databáze Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Nástroje Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Nástroje rozhraní příkazového řádku Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Nástroje Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Nebo si je můžete stáhnout instalací sady [Node Pack pro rozšíření Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Extension Pack.

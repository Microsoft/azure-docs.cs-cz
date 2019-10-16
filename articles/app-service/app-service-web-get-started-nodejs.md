---
title: Vytvoření webové aplikace v Node. js – Azure App Service | Microsoft Docs
description: Během několika minut můžete nasadit svou první aplikaci Node.js Hello World pomocí služby Azure App Service Web Apps.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 09/30/2019
ms.author: cephalin
ms.custom: seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: 380e587fc8c921b395d63d1dbca10e2f5fb1b9ba
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433204"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Vytvoření webové aplikace Node.js ve službě Azure 

Azure App Service poskytuje vysoce škálovatelnou službu s automatickými opravami pro hostování webů. V tomto rychlém startu se dozvíte, jak nasadit aplikaci Node. js do Azure App Service.

## <a name="prerequisites"></a>Předpoklady

Pokud nemáte účet Azure, [Zaregistrujte si ještě dnes](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) bezplatný účet s $200 v kreditech Azure, abyste si vyzkoušeli libovolnou kombinaci služeb.

Potřebujete [Visual Studio Code](https://code.visualstudio.com/) nainstalovat spolu s [Node. js a npm](https://nodejs.org/en/download), správce balíčků Node. js.

Budete taky muset nainstalovat [rozšíření Azure App Service](vscode:extension/ms-azuretools.vscode-azureappservice), které můžete použít k vytvoření, správě a nasazení Web Apps pro Linux na platformě Azure jako službu (PaaS).

### <a name="sign-in"></a>Přihlásit se

Po instalaci rozšíření se přihlaste ke svému účtu Azure. V řádku aktivity vyberte na logo Azure, kde se zobrazí Průzkumník **služby Azure App Service** . Vyberte **Přihlásit se k Azure...** a postupujte podle pokynů.

![Přihlaste se k Azure](containers/media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Řešení potíží

Pokud se zobrazí chyba **"nelze najít předplatné s názvem [ID předplatného]"** , může to být způsobeno tím, že jste za proxy serverem a nemůžete získat přístup k rozhraní API Azure. Nakonfigurujte proměnné prostředí `HTTP_PROXY` a `HTTPS_PROXY` s informacemi svého proxy serveru v terminálu pomocí `export`.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Pokud nastavení proměnných prostředí problém nevyřeší, kontaktujte nás tak, že vyberete níže uvedené tlačítko **problému** .

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

Než budete pokračovat, ujistěte se, že máte nainstalované a nakonfigurované všechny požadavky.

V VS Code byste měli na stavovém řádku a v rámci vašeho předplatného v Průzkumníkovi **služby Azure App Service** vidět vaši e-mailovou adresu Azure.

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Vytvoření aplikace Node. js

Dále vytvořte aplikaci Node. js, která může být nasazena do cloudu. V tomto rychlém startu se používá generátor aplikace k rychlému vytvoření uživatelského rozhraní aplikace z terminálu.

> [!TIP]
> Pokud jste už dokončili [kurz k Node. js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), můžete přeskočit k části [nasazení do Azure](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Generování nové aplikace pomocí generátoru Express

[Express](https://www.expressjs.com) je oblíbená architektura pro sestavování a spouštění aplikací v Node. js. Pomocí nástroje [expresního generátoru](https://expressjs.com/en/starter/generator.html) můžete vytvořit novou aplikaci Express. Generátor Express se dodává jako modul npm a dá se spustit přímo (bez instalace) pomocí nástroje příkazového řádku npm `npx`.

```bash
npx express-generator myExpressApp --view pug --git
```

Parametry `--view pug --git` informují generátoru, aby používal modul šablon [Pug](https://pugjs.org/api/getting-started.html) (dříve označovaný jako `jade`) a vytvořil soubor `.gitignore`.

Chcete-li nainstalovat všechny závislosti aplikace, pokračujte do nové složky a spusťte `npm install`.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Spuštění aplikace

Dále zkontrolujte, zda je aplikace spuštěna. Z terminálu spusťte aplikaci pomocí příkazu `npm start` a spusťte server.

```bash
npm start
```

Nyní otevřete prohlížeč a přejděte na [http://localhost:3000](http://localhost:3000), kde by se mělo zobrazit něco jako:

![Spuštění expresní aplikace](containers/media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Nasadit do Azure

V této části nasadíte aplikaci Node. js pomocí VS Code a rozšíření Azure App Service. V tomto rychlém startu se používá základní model nasazení, ve kterém je vaše aplikace zip a nasazená do Azure Web App on Linux.

### <a name="deploy-using-azure-app-service"></a>Nasazení pomocí Azure App Service

Nejprve otevřete složku aplikace v VS Code.

```bash
code .
```

V Průzkumníku **Azure App Service** vyberte ikonu modré šipky nahoru a nasaďte aplikaci do Azure.

![Nasazení do webové aplikace](containers/media/quickstart-nodejs/deploy.png)

> [!TIP]
> Z **palety příkazů** (CTRL + SHIFT + P) se dá nasadit taky tak, že zadáte ' nasadit do webové aplikace ' a spustíte příkaz **Azure App Service: nasadit do webové aplikace** .

1. Vyberte adresář, který máte v současnosti otevřený, `myExpressApp`.

1. Vyberte možnost vytvoření v závislosti na operačním systému, do kterého chcete nasadit:

    - Linux: vyberte **vytvořit novou webovou aplikaci**.
    - Windows: vyberte **vytvořit novou webovou aplikaci... Upřesnit**.

1. Zadejte globálně jedinečný název vaší webové aplikace a stiskněte klávesu ENTER. Platnými znaky pro název aplikace jsou "a-z", "0-9" a "-".

1. Pokud se zaměřujete na Linux, po zobrazení výzvy vyberte verzi Node. js. Doporučuje se verze **LTS** .

1. Pokud cílíte na systém Windows pomocí možnosti *Advanced**, postupujte podle dalších pokynů:
    1. Vyberte **vytvořit novou skupinu prostředků**a potom zadejte název skupiny prostředků.
    1. Pro operační systém vyberte **systém Windows** .
    1. Vyberte existující plán App Service nebo vytvořte nový. Při vytváření nového plánu můžete vybrat cenovou úroveň.
    1. Po zobrazení výzvy k Application Insights **Nyní vyberte Přeskočit** .
    1. Vyberte oblast poblíž nebo poblíž zdrojů, ke kterým chcete získat přístup.

1. Až odpovíte na všechny výzvy, kanál oznámení zobrazuje prostředky Azure, které se vytvářejí pro vaši aplikaci.

1. Po zobrazení výzvy k aktualizaci konfigurace pro spuštění `npm install` na cílovém serveru vyberte **Ano** . Vaše aplikace se pak nasadí.

    ![Nakonfigurované nasazení](containers/media/quickstart-nodejs/server-build.png)

1. Po zahájení nasazení se zobrazí výzva, abyste aktualizovali pracovní prostor tak, aby se později v nasazeních automaticky nacílena na stejnou App Service webovou aplikaci. Pokud chcete zajistit, aby se vaše změny nasadily do správné aplikace, klikněte na **Ano** .

    ![Nakonfigurované nasazení](containers/media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Ujistěte se, že aplikace naslouchá na portu poskytnutém proměnnou prostředí portu: `process.env.PORT`.

### <a name="browse-the-app-in-azure"></a>Procházení aplikace v Azure

Až se nasazení dokončí, vyberte **Procházet web** v příkazovém řádku a zobrazte svou čerstvou nasazenou webovou aplikaci.

### <a name="troubleshooting"></a>Řešení potíží

Pokud se zobrazí chyba **"nemáte oprávnění k zobrazení tohoto adresáře nebo stránky."** , aplikace se pravděpodobně nespustila správně. Přejděte k další části a podívejte se na výstup protokolu, který vyhledá a opraví chybu. Pokud ji nemůžete opravit, kontaktujte nás tak, že vyberete níže uvedené tlačítko **problému** . Rádi vám pomůžeme!

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Aktualizace aplikace

Změny v této aplikaci můžete nasadit pomocí stejného procesu a volbou existující aplikace namísto vytváření nového.

## <a name="viewing-logs"></a>Zobrazení protokolů

V této části se dozvíte, jak zobrazit (neboli "koncová") protokoly z běžící aplikace App Service. Všechna volání `console.log` v aplikaci se zobrazí v okně výstup v Visual Studio Code.

Najděte aplikaci v Průzkumníkovi **služby Azure App Service** , klikněte pravým tlačítkem na aplikaci a vyberte **Zobrazit protokoly streamování**.

Po zobrazení výzvy vyberte možnost povolit protokolování a restartovat aplikaci. Po restartování aplikace se otevře okno VS Code výstup s připojením k datovému proudu protokolu.

![Zobrazit protokoly streamování](containers/media/quickstart-nodejs/view-logs.png)

![Povolit protokolování a restartování](containers/media/quickstart-nodejs/enable-restart.png)

Po několika sekundách se zobrazí zpráva oznamující, že jste připojeni ke službě streamování protokolů. Několikrát aktualizujte stránku, aby se zobrazila více aktivit.

    ```bash
    2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    ```

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Další kroky

Blahopřejeme, úspěšně jste dokončili tento rychlý Start.

Pak se podívejte na další rozšíření Azure.

* [Databáze Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Nástroje Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Nástroje rozhraní příkazového řádku Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Nástroje Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Nebo si je můžete stáhnout instalací sady [Node Pack pro rozšíření Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Extension Pack.

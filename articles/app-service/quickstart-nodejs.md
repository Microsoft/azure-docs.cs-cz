---
title: 'Rychlý Start: Vytvoření webové aplikace v Node.js'
description: Nasaďte první Node.js Hello World do Azure App Service v řádu minut.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-nodejs-uiex
ms.openlocfilehash: 6c6f0543dcfbecd16ba4176272f928ffd0eb54de
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735110"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Vytvoření webové aplikace Node.js ve službě Azure

::: zone pivot="platform-windows"  

Začněte s Azure App Service vytvořením aplikace Node.js/Express místně pomocí Visual Studio Code a pak nasazením aplikace do cloudu. Vzhledem k tomu, že používáte bezplatnou App Service úroveň, nebudete mít k dokončení tohoto rychlého startu žádné náklady.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- <a href="https://git-scm.com/" target="_blank">Nainstalovat Git</a>.
- [Node.js a npm](https://nodejs.org) Spusťte příkaz `node --version` a ověřte, zda je nainstalován Node.js.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Azure App Service rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) pro Visual Studio Code.

## <a name="clone-and-run-a-local-nodejs-application"></a>Klonování a spuštění místní Node.jsové aplikace

1. V místním počítači otevřete terminál a naklonujte ukázkové úložiště:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Přejděte do složky nová aplikace:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Spusťte aplikaci, abyste ji místně otestovali:

    ```bash
    npm start
    ```
    
1. Otevřete prohlížeč a přejděte na `http://localhost:1337` . V prohlížeči by se měl zobrazit Hello World!.

1. Stisknutím klávesy **CTRL** +  v terminálu zastavte Server.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Nasadit aplikaci do Azure

V této části nasadíte Node.js aplikaci do Azure pomocí VS Code a rozšíření Azure App Service.

1. V terminálu se ujistěte, že jste ve složce *NodeJS-docs-Hello-World* , a pak spusťte Visual Studio Code s následujícím příkazem:

    ```bash
    code .
    ```

1. Na řádku VS Code aktivity vyberte logo Azure, kde se zobrazí Průzkumník **služby Azure App Service** . Vyberte **Přihlásit se k Azure...** a postupujte podle pokynů. (Další informace najdete v tématu [řešení potíží s přihlášením do Azure](#troubleshooting-azure-sign-in) , pokud narazíte na chyby.) Po přihlášení by měl Průzkumník Zobrazit název vašeho předplatného Azure.

    ![Přihlášení k Azure](media/quickstart-nodejs/sign-in.png)

1. V VS Code Průzkumníku **Azure App Service** vyberte ikonu modré šipky nahoru a nasaďte aplikaci do Azure. (Můžete také vyvolat stejný příkaz z **palety příkazů** (**CTRL** + **SHIFT** + **+**) zadáním příkazu "nasadit do webové aplikace" a volbou **Azure App Service: nasadit do webové aplikace**).

    :::image type="content" source="media/quickstart-nodejs/deploy.png" alt-text="Snímek obrazovky služby Azure App Service v VS Code zobrazující vybranou modrou ikonu šipky":::
        
1. Vyberte složku *NodeJS-docs-Hello-World* .

1. Vyberte možnost vytvoření v závislosti na operačním systému, do kterého chcete nasadit:

    - Linux: vyberte **vytvořit novou webovou aplikaci** .
    - Windows: vyberte **vytvořit novou webovou aplikaci... Rozšířené možnosti**

1. Zadejte globálně jedinečný název vaší webové aplikace a stiskněte klávesu **ENTER**. Název musí být jedinečný ve všech Azure a používat pouze alfanumerické znaky (A-Z, a-z a ' 0-9 ') a spojovníky (-).

1. Pokud cílíte na Linux, po zobrazení výzvy vyberte verzi Node.js. Doporučuje se verze **LTS** .

1. Pokud cílíte na systém Windows, postupujte podle dalších pokynů:
    1. Vyberte **vytvořit novou skupinu prostředků** a potom zadejte název skupiny prostředků, například `AppServiceQS-rg` .
    1. Pro operační systém vyberte **systém Windows** .
    1. Vyberte **vytvořit nový App Service plán**, zadejte název plánu (například `AppServiceQS-plan` ) a pak pro cenovou úroveň vyberte **F1 zdarma** .
    1. Po zobrazení výzvy k Application Insights **Nyní vyberte Přeskočit** .
    1. Vyberte oblast poblíž nebo poblíž zdrojů, ke kterým chcete získat přístup.

1. Po reakci na všechny výzvy VS Code v místní nabídce oznámení zobrazit prostředky Azure, které jsou vytvářeny pro vaši aplikaci.

    Když nasazujete na Linux  , při zobrazení výzvy k aktualizaci konfigurace, která se má spustit `npm install` na cílovém serveru Linux, vyberte Ano.

    ![Výzva k aktualizaci konfigurace na cílovém serveru se systémem Linux](media/quickstart-nodejs/server-build.png)

1. Po zobrazení výzvy vyberte **Ano** , pokud **Chcete vždycky nasadit pracovní prostor "NodeJS-docs-Hello-World" do (App Name) "**. Když vyberete **Ano** , vs Code se automaticky cílit na stejnou App Service webovou aplikaci s následnými nasazeními.

1. Pokud nasazujete na Linux, vyberte **Procházet web** v příkazovém řádku a po dokončení nasazení si můžete hned nasazenou webovou aplikaci zobrazit. V prohlížeči by se měl zobrazit Hello World!

1. Pokud nasazujete do systému Windows, musíte nejdřív nastavit Node.js číslo verze webové aplikace:

    1. V VS Code rozbalte uzel pro novou službu App Service, klikněte pravým tlačítkem myši na **nastavení aplikace** a vyberte **Přidat nové nastavení...**:

        ![Přidat nastavení aplikace – příkaz](media/quickstart-nodejs/add-setting.png)

    1. Jako `WEBSITE_NODE_DEFAULT_VERSION` klíč nastavení zadejte.
    1. Jako `10.15.2` hodnotu nastavení zadejte.
    1. Klikněte pravým tlačítkem na uzel služby App Service a vyberte **restartovat** .

        ![Příkaz restartovat službu App Service](media/quickstart-nodejs/restart.png)

    1. Klikněte pravým tlačítkem na uzel pro službu App Service a vyberte **Procházet web**.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Řešení potíží s přihlášením do Azure

Pokud se vám při přihlašování k Azure zobrazí chyba **"nelze najít předplatné s názvem [ID předplatného]"** , může to být způsobeno tím, že jste za proxy serverem a nemůžete získat přístup k rozhraním API Azure. `HTTP_PROXY` `HTTPS_PROXY` Pomocí použijte konfiguraci a proměnné prostředí s informacemi o proxy serveru v terminálu `export` .

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Pokud se při nastavení proměnných prostředí problém nevyřeší, kontaktujte nás tak, že vyberete tlačítko pro **vydání problému** výše.

### <a name="update-the-app"></a>Aktualizace aplikace

Změny v této aplikaci můžete nasadit provedením úprav v VS Code, uložením souborů a následným použitím stejného procesu jako předtím, než vytvoříte nový.

## <a name="viewing-logs"></a>Zobrazení protokolů

Můžete zobrazit výstup protokolu (volání `console.log` ) z aplikace přímo v okně výstup vs Code.

1. V Průzkumníku **služby Azure App Service** klikněte pravým tlačítkem myši na uzel aplikace a vyberte **Spustit protokoly streamování**.

    ![Spustit streamování protokolů](media/quickstart-nodejs/view-logs.png)

1. Po zobrazení výzvy vyberte možnost povolit protokolování a restartovat aplikaci. Po restartování aplikace se otevře okno VS Code výstup s připojením k datovému proudu protokolu. 

    :::image type="content" source="media/quickstart-nodejs/enable-restart.png" alt-text="Snímek obrazovky Visual Studio Code výzvy pro povolení protokolování a restartování aplikace s vybraným tlačítkem Ano.":::

1. Po několika sekundách se v okně výstup zobrazí zpráva oznamující, že jste připojeni ke službě streamování protokolů. Další výstupní aktivitu můžete vygenerovat tak, že aktualizujete stránku v prohlížeči.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Další kroky

Blahopřejeme, úspěšně jste dokončili tento rychlý Start.

> [!div class="nextstepaction"]
> [Kurz: Node.js aplikace pomocí MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace Node.js](configure-language-nodejs.md)

Podívejte se na další rozšíření Azure.

* [Databáze Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Nástroje Dockeru](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Nástroje rozhraní příkazového řádku Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Nástroje Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Nebo si je můžete stáhnout instalací sady [Node Pack pro rozšíření Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Extension Pack.
::: zone-end

::: zone pivot="platform-linux"  
## <a name="prerequisites"></a>Požadavky

Pokud nemáte účet Azure, [Zaregistrujte si ještě dnes](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) bezplatný účet s $200 v kreditech Azure, abyste si vyzkoušeli libovolnou kombinaci služeb.

Potřebujete [Visual Studio Code](https://code.visualstudio.com/) nainstalovanou společně s [Node.js a npm](https://nodejs.org/en/download), správce balíčků Node.js.

Budete taky muset nainstalovat [rozšíření Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice), které můžete použít k vytvoření, správě a nasazení Web Apps pro Linux na platformě Azure jako službu (PaaS).

### <a name="sign-in"></a>Přihlásit se

Po instalaci rozšíření se přihlaste ke svému účtu Azure. V řádku aktivity vyberte logo Azure, které se zobrazí v Průzkumníku **Azure App Service** . Vyberte **Přihlásit se k Azure...** a postupujte podle pokynů.

![Přihlaste se k Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Řešení potíží

Pokud se zobrazí chyba **"nelze najít předplatné s názvem [ID předplatného]"**, může to být způsobeno tím, že jste za proxy serverem a nemůžete získat přístup k rozhraní API Azure. `HTTP_PROXY` `HTTPS_PROXY` Pomocí použijte konfiguraci a proměnné prostředí s informacemi o proxy serveru v terminálu `export` .

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Pokud nastavení proměnných prostředí problém nevyřeší, kontaktujte nás tak, že vyberete níže uvedené tlačítko **problému** .

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

Než budete pokračovat, ujistěte se, že máte nainstalované a nakonfigurované všechny požadavky.

V VS Code byste měli na stavovém řádku a v rámci vašeho předplatného v Průzkumníkovi **služby Azure App Service** vidět vaši e-mailovou adresu Azure.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Vytvoření aplikace Node.js

Pak vytvořte Node.js aplikaci, která se dá nasadit do cloudu. V tomto rychlém startu se používá generátor aplikace k rychlému vytvoření uživatelského rozhraní aplikace z terminálu.

> [!TIP]
> Pokud jste již dokončili [Node.js kurz](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), můžete přeskočit k části [nasazení do Azure](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Generování nové aplikace pomocí generátoru Express

[Express](https://www.expressjs.com) je oblíbená architektura pro sestavování a spouštění aplikací Node.js. Pomocí nástroje [expresního generátoru](https://expressjs.com/en/starter/generator.html) můžete vytvořit novou aplikaci Express. Generátor Express se dodává jako modul npm a dá se spustit přímo (bez instalace) pomocí nástroje příkazového řádku npm `npx` .

```bash
npx express-generator myExpressApp --view pug --git
```

`--view pug --git`Parametry říká generátoru, aby používal modul šablon [Pug](https://pugjs.org/api/getting-started.html) (dříve označovaný jako `jade` ) a vytvořil `.gitignore` soubor.

Chcete-li nainstalovat všechny závislosti aplikace, klikněte na novou složku a spusťte příkaz `npm install` .

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Spuštění aplikace

Dále zkontrolujte, zda je aplikace spuštěna. Z terminálu spusťte aplikaci pomocí `npm start` příkazu pro spuštění serveru.

```bash
npm start
```

Nyní otevřete prohlížeč a přejděte na `http://localhost:3000` místo, kde by se mělo zobrazit něco podobného:

![Spuštění expresní aplikace](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Nasazení do Azure

V této části nasadíte Node.js aplikaci pomocí VS Code a rozšíření Azure App Service. V tomto rychlém startu se používá základní model nasazení, ve kterém je vaše aplikace zip a nasazená do Azure Web App on Linux.

### <a name="deploy-using-azure-app-service"></a>Nasazení pomocí Azure App Service

Nejprve otevřete složku aplikace v VS Code.

```bash
code .
```

V Průzkumníku **Azure App Service** vyberte ikonu modré šipky nahoru a nasaďte aplikaci do Azure.

:::image type="content" source="./media/quickstart-nodejs/deploy.png" alt-text="Snímek obrazovky nabídky Azure App Service v Visual Studio Code s vybraným modrou šipkou nasazení":::

> [!TIP]
> Z **palety příkazů** (CTRL + SHIFT + P) se dá nasadit taky tak, že zadáte ' nasadit do webové aplikace ' a spustíte příkaz **Azure App Service: nasadit do webové aplikace** .

1. Vyberte adresář, který aktuálně máte otevřený `myExpressApp` .

1. Vyberte **vytvořit novou webovou aplikaci**, která ve výchozím nastavení nasadí App Service v systému Linux.

1. Zadejte globálně jedinečný název vaší webové aplikace a stiskněte klávesu ENTER. Platnými znaky pro název aplikace jsou "a-z", "0-9" a "-".

1. Vyberte **Node.js verzi**, doporučujeme LTS.

    Kanál oznámení zobrazuje prostředky Azure, které se vytvářejí pro vaši aplikaci.

1. Po  zobrazení výzvy k aktualizaci konfigurace pro spuštění `npm install` na cílovém serveru vyberte Ano. Vaše aplikace se pak nasadí.

    :::image type="content" source="./media/quickstart-nodejs/server-build.png" alt-text="Snímek obrazovky s výzvou k aktualizaci konfigurace na cílovém serveru tlačítkem Ano":::

1. Po zahájení nasazení se zobrazí výzva, abyste aktualizovali pracovní prostor tak, aby se později v nasazeních automaticky nacílena na stejnou App Service webovou aplikaci. Pokud chcete zajistit, aby se vaše změny nasadily do správné aplikace, klikněte na **Ano** .

    :::image type="content" source="./media/quickstart-nodejs/save-configuration.png" alt-text="Snímek obrazovky s výzvou, aby se pracovní prostor aktualizoval vybraným tlačítkem Ano":::

> [!TIP]
> Ujistěte se, že aplikace naslouchá na portu poskytnutém proměnnou prostředí portu: `process.env.PORT` .

### <a name="browse-the-app-in-azure"></a>Procházení aplikace v Azure

Až se nasazení dokončí, vyberte **Procházet web** v příkazovém řádku a zobrazte svou čerstvou nasazenou webovou aplikaci.

### <a name="troubleshooting"></a>Řešení potíží

Pokud se zobrazí chyba **"nemáte oprávnění k zobrazení tohoto adresáře nebo stránky."**, aplikace se pravděpodobně nespustila správně. Přejděte k další části a podívejte se na výstup protokolu, který vyhledá a opraví chybu. Pokud ji nemůžete opravit, kontaktujte nás tak, že vyberete níže uvedené tlačítko **problému** . Rádi vám pomůžeme!

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Aktualizace aplikace

Změny v této aplikaci můžete nasadit pomocí stejného procesu a volbou existující aplikace namísto vytváření nového.

## <a name="viewing-logs"></a>Zobrazení protokolů

V této části se dozvíte, jak zobrazit (neboli "koncová") protokoly z běžící aplikace App Service. Všechna volání `console.log` aplikace v aplikaci se zobrazí v okně výstup v Visual Studio Code.

Najděte aplikaci v Průzkumníkovi **služby Azure App Service** , klikněte pravým tlačítkem na aplikaci a vyberte **Zobrazit protokoly streamování**.

Otevře se okno VS Code výstup s připojením k datovému proudu protokolu.

![Zobrazit protokoly streamování](./media/quickstart-nodejs/view-logs.png)

:::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Snímek obrazovky VS Code výzvy k povolení protokolování souborů a restartování webové aplikace s vybraným tlačítkem Ano.":::

Po několika sekundách se zobrazí zpráva oznamující, že jste připojeni ke službě streamování protokolů. Několikrát aktualizujte stránku, aby se zobrazila více aktivit.

<pre>
2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
</pre>

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Další kroky

Blahopřejeme, úspěšně jste dokončili tento rychlý Start.

Pak se podívejte na další rozšíření Azure.

* [Databáze Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Nástroje Dockeru](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Nástroje rozhraní příkazového řádku Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Nástroje Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Nebo si je můžete stáhnout instalací sady [Node Pack pro rozšíření Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Extension Pack.


::: zone-end

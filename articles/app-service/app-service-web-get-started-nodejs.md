---
title: 'Rychlý Start: Vytvoření webové aplikace v Node. js'
description: Nasaďte první Hello World Node. js do Azure App Service v řádu minut. Nasadíte pomocí Visual Studio Code, což je jedním z mnoha způsobů, jak nasadit do App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: 1f105792a95115580d52444a617b3fc1678843ca
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374084"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Vytvoření webové aplikace Node.js ve službě Azure 

Začněte s Azure App Service vytvořením aplikace Node. js/Express místně pomocí Visual Studio Code a pak nasazením aplikace do cloudu. Vzhledem k tomu, že používáte bezplatnou App Service úroveň, nebudete mít k dokončení tohoto rychlého startu žádné náklady.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- [Node.js a npm](https://nodejs.org) Spusťte `node --version` příkazu, abyste ověřili, že je Node. js nainstalovaný.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Azure App Service rozšíření](vscode:extension/ms-azuretools.vscode-azureappservice) pro Visual Studio Code.

## <a name="clone-and-run-a-local-nodejs-application"></a>Klonování a spuštění místní aplikace Node. js

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
    
1. Otevřete prohlížeč a přejděte na [http://localhost:1337](http://localhost:1337). V prohlížeči by se měl zobrazit Hello World!.

1. Stisknutím **kombinace kláves Ctrl**+**C** v terminálu zastavte Server.

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Nasadit aplikaci do Azure

V této části nasadíte aplikaci Node. js do Azure pomocí VS Code a rozšíření Azure App Service.

1. V terminálu se ujistěte, že jste ve složce *NodeJS-docs-Hello-World* , a pak spusťte Visual Studio Code s následujícím příkazem:

    ```bash
    code .
    ```

1. Na řádku VS Code aktivity vyberte logo Azure, kde se zobrazí Průzkumník **služby Azure App Service** . Vyberte **Přihlásit se k Azure...** a postupujte podle pokynů. (Další informace najdete v tématu [řešení potíží s přihlášením do Azure](#troubleshooting-azure-sign-in) , pokud narazíte na chyby.) Po přihlášení by měl Průzkumník Zobrazit název vašeho předplatného Azure.

    ![Přihlášení k Azure](containers/media/quickstart-nodejs/sign-in.png)

1. V VS Code Průzkumníku **Azure App Service** vyberte ikonu modré šipky nahoru a nasaďte aplikaci do Azure. (Můžete také vyvolat stejný příkaz z **palety příkazů** (**Ctrl**+**SHIFT**+**P**) tak, že zadáte ' nasadit do webové aplikace ' a zvolíte **Azure App Service: nasadit do webové aplikace**).

    ![Nasazení do webové aplikace](containers/media/quickstart-nodejs/deploy.png)
        
1. Vyberte složku *NodeJS-docs-Hello-World* .

1. Vyberte možnost vytvoření v závislosti na operačním systému, do kterého chcete nasadit:

    - Linux: vyberte **vytvořit novou webovou aplikaci** .
    - Windows: vyberte **vytvořit novou webovou aplikaci... Rozšířené možnosti**

1. Zadejte globálně jedinečný název vaší webové aplikace a stiskněte klávesu **ENTER**. Název musí být jedinečný ve všech Azure a používat pouze alfanumerické znaky (A-Z, a-z a ' 0-9 ') a spojovníky (-).

1. Pokud se zaměřujete na Linux, po zobrazení výzvy vyberte verzi Node. js. Doporučuje se verze **LTS** .

1. Pokud cílíte na systém Windows, postupujte podle dalších pokynů:
    1. Vyberte **vytvořit novou skupinu prostředků**a potom zadejte název skupiny prostředků, například `AppServiceQS-rg`.
    1. Pro operační systém vyberte **systém Windows** .
    1. Vyberte **vytvořit nový plán App Service**a pak zadejte název plánu (například `AppServiceQS-plan`) a pro cenovou úroveň vyberte **F1 Free** .
    1. Po zobrazení výzvy k Application Insights **Nyní vyberte Přeskočit** .
    1. Vyberte oblast poblíž nebo poblíž zdrojů, ke kterým chcete získat přístup.

1. Po reakci na všechny výzvy VS Code v místní nabídce oznámení zobrazit prostředky Azure, které jsou vytvářeny pro vaši aplikaci.

    Když nasazujete na Linux, při zobrazení výzvy k aktualizaci konfigurace pro spuštění `npm install` na cílovém serveru Linux vyberte **Ano** .

    ![Výzva k aktualizaci konfigurace na cílovém serveru se systémem Linux](containers/media/quickstart-nodejs/server-build.png)

1. Po zobrazení výzvy vyberte **Ano** , pokud **Chcete vždycky nasadit pracovní prostor "NodeJS-docs-Hello-World" do (App Name) "** . Když vyberete **Ano** , vs Code se automaticky cílit na stejnou App Service webovou aplikaci s následnými nasazeními.

1. Pokud nasazujete na Linux, vyberte **Procházet web** v příkazovém řádku a po dokončení nasazení si můžete hned nasazenou webovou aplikaci zobrazit. V prohlížeči by se měl zobrazit Hello World!

1. Pokud nasazujete do systému Windows, musíte nejprve nastavit číslo verze Node. js pro webovou aplikaci:

    1. V VS Code rozbalte uzel pro novou službu App Service, klikněte pravým tlačítkem myši na **nastavení aplikace**a vyberte **Přidat nové nastavení...** :

        ![Přidat nastavení aplikace – příkaz](containers/media/quickstart-nodejs/add-setting.png)

    1. Jako klíč nastavení zadejte `WEBSITE_NODE_DEFAULT_VERSION`.
    1. Jako hodnotu nastavení zadejte `10.15.2`.
    1. Klikněte pravým tlačítkem na uzel služby App Service a vyberte **restartovat** .

        ![Příkaz restartovat službu App Service](containers/media/quickstart-nodejs/restart.png)

    1. Klikněte pravým tlačítkem na uzel pro službu App Service a vyberte **Procházet web**.

> [!div class="nextstepaction"]
> [Narazili jsme na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Řešení potíží s přihlášením do Azure

Pokud se vám při přihlašování k Azure zobrazí chyba **"nelze najít předplatné s názvem [ID předplatného]"** , může to být způsobeno tím, že jste za proxy serverem a nemůžete získat přístup k rozhraním API Azure. Nakonfigurujte `HTTP_PROXY` a `HTTPS_PROXY` proměnné prostředí pomocí informací o proxy serveru v terminálu pomocí `export`.

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Pokud se při nastavení proměnných prostředí problém nevyřeší, kontaktujte nás tak, že vyberete tlačítko pro **vydání problému** výše.

### <a name="update-the-app"></a>Aktualizace aplikace

Změny v této aplikaci můžete nasadit provedením úprav v VS Code, uložením souborů a následným použitím stejného procesu jako předtím, než vytvoříte nový.

## <a name="viewing-logs"></a>Zobrazení protokolů

Z aplikace můžete zobrazit výstup protokolu (volání do `console.log`) přímo v okně VS Code výstupu.

1. V Průzkumníku **služby Azure App Service** klikněte pravým tlačítkem myši na uzel aplikace a vyberte **Spustit protokoly streamování**.

    ![Spustit streamování protokolů](containers/media/quickstart-nodejs/view-logs.png)

1. Po zobrazení výzvy vyberte možnost povolit protokolování a restartovat aplikaci. Po restartování aplikace se otevře okno VS Code výstup s připojením k datovému proudu protokolu. 

    ![Povolit protokolování a restartování](containers/media/quickstart-nodejs/enable-restart.png)

1. Po několika sekundách se v okně výstup zobrazí zpráva oznamující, že jste připojeni ke službě streamování protokolů. Další výstupní aktivitu můžete vygenerovat tak, že aktualizujete stránku v prohlížeči.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
    </pre>

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

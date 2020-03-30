---
title: 'Úvodní příručka: Vytvoření webové aplikace Node.js'
description: Nasaďte svůj první Node.js Hello World do služby Azure App Service během několika minut. Nasadíte pomocí visual studio kód, což je jeden z mnoha způsobů, jak nasadit do služby App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: bcb0ae2457175e7511c3539187d72d48e80baa5b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047121"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Vytvoření webové aplikace Node.js ve službě Azure 

Můžete začít s Azure App Service vytvořením aplikace Node.js/Express místně pomocí kódu Visual Studia a pak nasadit aplikaci do cloudu. Vzhledem k tomu, že používáte bezplatnou úroveň služby App Service, nevznikají vám žádné náklady na dokončení tohoto rychlého startu.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- [Node.js a npm](https://nodejs.org) Spusťte `node --version` příkaz a ověřte, zda je nainstalován soubor Node.js.
- [Visual Studio kód](https://code.visualstudio.com/).
- Rozšíření [služby Azure App Service](vscode:extension/ms-azuretools.vscode-azureappservice) pro kód Visual Studia.

## <a name="clone-and-run-a-local-nodejs-application"></a>Klonovat a spouštět místní aplikaci Node.js

1. V místním počítači otevřete terminál a naklonujte ukázkové úložiště:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Přejděte do nové složky aplikace:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Spusťte aplikaci a otestujte ji místně:

    ```bash
    npm start
    ```
    
1. Otevřete prohlížeč a [http://localhost:1337](http://localhost:1337)přejděte na . V prohlížeči by se mělo zobrazit "Hello World!".

1. Stisknutím **klávesy Ctrl**+**C** v terminálu server zastavíte.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Nasadit aplikaci do Azure

V této části nasadíte aplikaci Node.js do Azure pomocí Kódu VS a rozšíření služby Azure App Service.

1. V terminálu se ujistěte, že jste ve složce *nodejs-docs-hello-world,* a pak spusťte kód Sady Visual Studio pomocí následujícího příkazu:

    ```bash
    code .
    ```

1. Na panelu aktivit kódu VS vyberte logo Azure, které se zobrazí **průzkumníkazure APP SERVICE.** Vyberte **Přihlásit se do Azure...** a postupujte podle pokynů. (Pokud narazíte na chyby, přečtěte si článek [Řešení potíží s přihlášením do Azure](#troubleshooting-azure-sign-in) níže.) Po přihlášení by měl průzkumník zobrazit název vašeho předplatného Azure.

    ![Přihlášení k Azure](containers/media/quickstart-nodejs/sign-in.png)

1. V průzkumníku **AZURE APP SERVICE** v Kódu VS vyberte modrou šipku nahoru a nasadíte aplikaci do Azure. (Můžete také vyvolat stejný příkaz z **palety příkazů** **(Ctrl**+**Shift**+**P**) zadáním 'nasadit do webové aplikace' a výběrem **Služby Aplikace Azure: Nasazení do webové aplikace).**

    ![Nasazení do webové aplikace](containers/media/quickstart-nodejs/deploy.png)
        
1. Zvolte složku *nodejs-docs-hello-world.*

1. Zvolte možnost vytvoření na základě operačního systému, do kterého chcete nasadit:

    - Linux: Zvolte **Vytvořit novou webovou aplikaci**
    - Windows: Zvolte **Vytvořit novou webovou aplikaci... Pokročilé**

1. Zadejte globálně jedinečný název webové aplikace a stiskněte **Enter**. Název musí být jedinečný ve všech Azure a používat pouze alfanumerické znaky ('A-Z', 'a-z', a '0-9') a pomlčky ('-').

1. Pokud cílíte na Linux, vyberte po zobrazení výzvy verzi node.js. Doporučuje se verze **LTS.**

1. Pokud cílíte na Windows, postupujte podle dalších pokynů:
    1. Vyberte **Vytvořit novou skupinu prostředků**a zadejte název `AppServiceQS-rg`skupiny prostředků, například .
    1. Vyberte **windows** pro operační systém.
    1. Vyberte **Vytvořit nový plán služby App Service**, `AppServiceQS-plan`zadejte název plánu (například) a pak vyberte **F1 Free** pro cenovou úroveň.
    1. Po zobrazení výzvy k aplikaci Application Insights **zvolte prozatím přeskočit.**
    1. Vyberte oblast ve vašem okolí nebo v blízkosti zdrojů, ke kterým chcete získat přístup.

1. Po odpovědi na všechny výzvy, VS Kód zobrazuje prostředky Azure, které se vytvářejí pro vaši aplikaci v jeho oznámení vyskakovací okno.

    Při nasazování do Linuxu vyberte **Ano,** `npm install` když se zobrazí výzva k aktualizaci konfigurace, aby se spouštěla na cílovém serveru Linux.

    ![Výzva k aktualizaci konfigurace na cílovém serveru Linux](containers/media/quickstart-nodejs/server-build.png)

1. Po zobrazení výzvy **Vždy nasadit pracovní prostor "nodejs-docs-hello-world" na (název aplikace)" vyberte**možnost **Ano.** Výběrem **možnosti Ano** se sdělujete kódu VS, aby automaticky zacílil na stejnou webovou aplikaci služby App Service s následnými nasazeními.

1. Pokud se nasazujete do Linuxu, vyberte v řádku procházet **web** a zobrazte čerstvě nasazenou webovou aplikaci po dokončení nasazení. V prohlížeči by se mělo zobrazit "Hello World!"

1. Pokud nasazujete do Windows, musíte nejprve nastavit číslo verze Node.js pro webovou aplikaci:

    1. V kódu VS rozbalte uzel pro novou službu aplikace, klikněte pravým tlačítkem na **Nastavení aplikace**a vyberte Přidat **nové nastavení...**:

        ![Příkaz Přidat nastavení aplikace](containers/media/quickstart-nodejs/add-setting.png)

    1. Zadejte `WEBSITE_NODE_DEFAULT_VERSION` pro klíč nastavení.
    1. Zadejte `10.15.2` pro hodnotu nastavení.
    1. Klikněte pravým tlačítkem myši na uzel pro službu aplikace a vyberte **Restartovat.**

        ![Příkaz Restartovat službu aplikace](containers/media/quickstart-nodejs/restart.png)

    1. Klikněte pravým tlačítkem myši na uzel služby aplikace ještě jednou a vyberte **Procházet web**.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Poradce při potížích s přihlášením k Azure

Pokud se při přihlašování do Azure zobrazí chyba **"Nelze najít předplatné s názvem [ID předplatného]",** může to být proto, že jste za proxy serverem a nemůžete se dostat do rozhraní API Azure. Konfigurace `HTTP_PROXY` `HTTPS_PROXY` a proměnné prostředí s informacemi `export`o proxy serveru v terminálu pomocí .

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Pokud nastavení proměnných prostředí problém nenapraví, kontaktujte nás výběrem výše uvedeného tlačítka **problému.**

### <a name="update-the-app"></a>Aktualizace aplikace

Změny v této aplikaci můžete nasadit provedením úprav v kódu VS, uložením souborů a následným použitím stejného procesu jako před výběrem pouze existující aplikace, nikoli vytvořením nové.

## <a name="viewing-logs"></a>Zobrazení protokolů

Výstup protokolu (volání do) `console.log`můžete zobrazit z aplikace přímo v okně výstupu VS Code.

1. V průzkumníku **AZURE APP SERVICE** klikněte pravým tlačítkem myši na uzel aplikace a zvolte Spustit **protokoly streamování**.

    ![Spustit protokoly datových proudů](containers/media/quickstart-nodejs/view-logs.png)

1. Po zobrazení výzvy zvolte povolení protokolování a restartování aplikace. Po restartování aplikace se otevře výstupní okno VS Code s připojením k datovému proudu protokolu. 

    ![Povolení protokolování a restartování](containers/media/quickstart-nodejs/enable-restart.png)

1. Po několika sekundách se ve výstupním okně zobrazí zpráva oznamující, že jste připojeni ke službě streamování protokolu. Další výstupní aktivitu můžete vygenerovat aktualizací stránky v prohlížeči.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
    </pre>

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Další kroky

Gratulujeme, úspěšně jste dokončili tento rychlý start!

Dále se podívejte na další rozšíření Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Nástroje Dockeru](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Nástroje azure cli](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Nástroje Správce prostředků Azure](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Nebo je všechny získejte instalací rozšíření [Sady Node Pack pro Azure.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)

---
title: 'Rychlý Start: Vytvoření webové aplikace v Node.js'
description: Nasaďte první Node.js Hello World do Azure App Service v řádu minut.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7cd9add699d9bd4a3eff9cdcf1e65af0d754b70a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101748384"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Vytvoření webové aplikace Node.js ve službě Azure

Začínáme s <abbr title="Služba založená na protokolu HTTP pro hostování webových aplikací, rozhraní REST API a mobilní back-endové aplikace.">Azure App Service</abbr> vytvořením aplikace Node.js/Express lokálně pomocí Visual Studio Code a pak nasadíte aplikaci do cloudu Azure. Protože používáte <abbr title="V Azure App Service základní vrstvu, ve které vaše aplikace běží na stejných virtuálních počítačích jako jiné aplikace, včetně aplikací jiných zákazníků. Tato úroveň je určená pro vývoj a testování.">úroveň Free</abbr>Nejste za dokončení tohoto rychlého startu zaznamenali žádné náklady.

## <a name="1-prepare-your-environment"></a>1. Příprava prostředí

- Účet Azure s aktivním <abbr title="Předplatné Azure je logický kontejner, který se používá ke zřizování prostředků v Azure. Obsahuje podrobnosti o všech vašich prostředcích, jako jsou virtuální počítače, databáze a podobně.">předplatné</abbr>. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- <a href="https://git-scm.com/" target="_blank">Nainstalovat Git</a>.
- [Node.js a npm](https://nodejs.org) Spusťte příkaz `node --version` a ověřte, zda je nainstalován Node.js.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Azure App Service rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) pro Visual Studio Code.

[Nahlášení problému](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)




<br>
<hr/>

## <a name="2-clone-and-run-a-local-nodejs-application"></a>2. klonování a spuštění místní Node.js aplikace

1. V místním počítači otevřete terminál a naklonujte ukázkové úložiště:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Přejděte do složky nová aplikace:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Nainstalujte závislosti:

    ```bash
    npm install
    ```

1. Spusťte aplikaci, abyste ji místně otestovali:

    ```bash
    npm start
    ```
    
1. Otevřete prohlížeč a přejděte na `http://localhost:1337` . V prohlížeči by se měl zobrazit Hello World!.

1. Stisknutím klávesy <kbd>CTRL</kbd>  +  <kbd></kbd> v terminálu zastavte Server.

[Nahlášení problému](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br>
<hr/>




<!-- VS Code extension works differently for Windows/Linus - Step 3 -->

::: zone pivot="platform-windows"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-windows.md)]


::: zone-end

::: zone pivot="platform-linux"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-linux.md)]

::: zone-end


## <a name="4-viewing-logs-from-visual-studio-code"></a>4. zobrazení protokolů z Visual Studio Code

Zobrazit <abbr title="Všechna volání `console.log` aplikace v aplikaci se zobrazí v okně výstup v Visual Studio Code.">protokolu</abbr> běžící aplikace App Service.

1. Najděte aplikaci v Průzkumníkovi **Azure App Service** , klikněte pravým tlačítkem na název aplikace a vyberte **Spustit protokoly streamování**.

1. Otevře se okno Visual Studio Code výstup.

    ![Zobrazit protokoly streamování](./media/quickstart-nodejs/view-logs.png)

    :::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Snímek obrazovky VS Code výzvy k povolení protokolování souborů a restartování webové aplikace s vybraným tlačítkem Ano.":::

1. Po několika sekundách se zobrazí zpráva oznamující, že jste připojeni ke službě streamování protokolů. 
1. Několikrát aktualizujte stránku, aby se zobrazila více aktivit.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    2020-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2020-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2020-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2020-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    </pre>

<br>

[Nahlášení problému](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. vyčištění prostředků

Najděte aplikaci v Průzkumníkovi **Azure App Service** , klikněte pravým tlačítkem na název aplikace a vyberte **Odstranit**. 

:::image type="content" source="./media/quickstart-nodejs/delete-resource-ieux.png" alt-text="Najděte aplikaci v Průzkumníkovi &quot;* *&quot; AZURE APP SERVICE * *, klikněte pravým tlačítkem na název aplikace a vyberte Odstranit.":::

## <a name="next-steps"></a>Další kroky

Blahopřejeme, úspěšně jste dokončili tento rychlý Start. Změny v této aplikaci můžete nasadit pomocí stejného procesu a volbou existující aplikace namísto vytváření nového.

Pak se podívejte na další rozšíření Azure.

* [Databáze Cosmos](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Nástroje Dockeru](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Nástroje rozhraní příkazového řádku Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Nástroje Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Nebo si je můžete stáhnout instalací sady [Node Pack pro rozšíření Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Extension Pack.
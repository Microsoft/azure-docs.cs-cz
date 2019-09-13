---
title: Vytvoření Durable Functions pomocí Azure Portal
description: Naučte se, jak nainstalovat rozšíření Durable Functions pro Azure Functions pro vývoj na portálu.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: glenga
ms.reviewer: azfuncdf
ms.openlocfilehash: b765e7a03f84211d4a86c4242e9484b3517c95f9
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933565"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Vytvoření Durable Functions pomocí Azure Portal

[Durable Functions](durable-functions-overview.md) rozšíření pro Azure Functions je k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Toto rozšíření musí být nainstalované ve vaší aplikaci Function App. Tento článek ukazuje, jak nainstalovat tento balíček, abyste mohli vyvíjet trvalé funkce v Azure Portal.

> [!NOTE]
> 
> * Pokud vyvíjíte trvalé funkce v nástroji C#, měli byste zvážit [vývoj v aplikaci Visual Studio 2019](durable-functions-create-first-csharp.md).
> * Pokud vyvíjíte trvalé funkce v JavaScriptu, měli byste zvážit [Visual Studio Code vývoj](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Vytvoření Function App

Musíte mít aplikaci Function App, která bude hostovat provádění jakékoli funkce. Aplikace Function App vám umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. Můžete vytvořit aplikaci pro .NET nebo JavaScript.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Ve výchozím nastavení aplikace Function App vytvořila používá modul runtime Azure Functions verze 2. x. Rozšíření Durable Functions pracuje na obou verzích 1. x a 2. x Azure Functions runtime v C#systému a verze 2. x v JavaScriptu. Šablony jsou však k dispozici pouze v případě cílení na verzi 2. x modulu runtime bez ohledu na zvolený jazyk.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Instalace balíčku odolného funkce NPM (jenom JavaScript)

Pokud vytváříte Durable Functions JavaScriptu, budete muset nainstalovat [ `durable-functions` balíček npm](https://www.npmjs.com/package/durable-functions).

1. Vyberte název vaší aplikace Function App a potom **funkce platformy**a pak **Rozšířené nástroje (Kudu)** .

   ![Funkce platformy Functions zvolit Kudu](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. V konzole Kudu vyberte **ladit konzolu** a pak **cmd**.

   ![Konzola ladění Kudu](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. Měla by se zobrazit adresářová struktura souboru vaší aplikace Function App. Přejděte do složky `site/wwwroot`. Odtud můžete `package.json` soubor nahrát přetažením a přetažením do okna adresář souborů. Ukázka `package.json` je následující:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.1.2"
      }
    }
    ```

   ![Kudu nahrát soubor Package. JSON](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. Po nahrání spusťte příkaz z konzoly pro vzdálené spuštění Kudu. `npm install` `package.json`

   ![Kudu spustit instalaci npm](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Vytvoření funkce Orchestrator

1. Rozbalte aplikaci Function App a klikněte na tlačítko **+** vedle položky **Funkce**. Pokud jde o první funkci ve vaší aplikaci Function App, vyberte **Na portálu** a potom **Pokračovat**. V opačném případě přejděte ke třetímu kroku.

   ![Stručný úvod do služby Functions na webu Azure Portal](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Zvolte **Další šablony** a potom **Dokončit a zobrazit šablony**.

    ![Rychlý start služby Functions – výběr dalších šablon](./media/durable-functions-create-portal/add-first-function.png)

1. Do vyhledávacího pole zadejte `durable` a pak zvolte šablonu **Durable Functions http Starter** .

1. Po zobrazení výzvy vyberte **nainstalovat** , abyste nainstalovali rozšíření Azure DurableTask a všechny závislosti v aplikaci Function App. Rozšíření stačí nainstalovat jenom jednou pro aplikaci Function App. Po úspěšném dokončení instalace vyberte **Pokračovat**.

    ![Instalace rozšíření vazby](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. Po dokončení instalace pojmenujte novou funkci `HttpStart` a klikněte na **vytvořit**. Vytvořená funkce se používá ke spuštění orchestrace.

1. Vytvořte v aplikaci Function App jinou funkci, tentokrát pomocí šablony **Durable Functions Orchestrator** . Pojmenujte novou funkci `HelloSequence`orchestrace.

1. Vytvořte třetí funkci s názvem `Hello` pomocí šablony **aktivity Durable Functions** .

## <a name="test-the-durable-function-orchestration"></a>Testování orchestrace trvalé funkce

1. Vraťte se do funkce **HttpStart** , vyberte **</> získat adresu URL funkce** a **zkopírujte** adresu URL. Pomocí této adresy URL spustíte funkci **HelloSequence** .

1. K odeslání požadavku POST na adresu URL, kterou jste zkopírovali, použijte nástroj HTTP, například post nebo kudrlinkou. Následující příklad je příkaz složeného příkazu, který odesílá požadavek POST do trvalé funkce:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    V tomto příkladu `{your-function-app-name}` je doménou název vaší aplikace Function App. Zpráva odpovědi obsahuje sadu koncových bodů identifikátoru URI, které lze použít k monitorování a správě spuštění, což vypadá jako v následujícím příkladu:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Zavolejte identifikátor URI koncového bodu a uvidíte aktuální stav trvalé funkce, která může vypadat jako v tomto příkladu: `statusQueryGetUri`

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Pokračujte v volání `statusQueryGetUri` koncového bodu, dokud se stav nezmění na **dokončeno**a vidíte odpověď jako v následujícím příkladu:

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Vaše první trvalá funkce je teď v Azure spuštěná.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o běžných vzorech trvalých funkcí](durable-functions-overview.md#application-patterns)

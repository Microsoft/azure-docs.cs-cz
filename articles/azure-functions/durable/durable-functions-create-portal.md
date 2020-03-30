---
title: Vytváření trvalých funkcí pomocí portálu Azure
description: Zjistěte, jak nainstalovat rozšíření Durable Functions pro Azure Functions pro vývoj portálu.
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: azfuncdf
ms.openlocfilehash: 0060088acb100036c094406e01d0d736a4af88eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769638"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Vytváření trvalých funkcí pomocí portálu Azure

Rozšíření [Durable Functions](durable-functions-overview.md) pro funkce Azure je k dispozici v balíčku NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Toto rozšíření musí být nainstalováno ve vaší aplikaci funkce. Tento článek ukazuje, jak nainstalovat tento balíček, abyste mohli vyvíjet trvalé funkce na webu Azure Portal.

> [!NOTE]
> 
> * Pokud vyvíjíte trvalé funkce v jazyce C#, měli byste místo toho zvážit [vývoj visual studia 2019](durable-functions-create-first-csharp.md).
> * Pokud vyvíjíte trvalé funkce v jazyce JavaScript, měli byste místo toho zvážit [vývoj kódu sady Visual Studio](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Vytvoření Function App

Musíte mít aplikaci funkce pro hostování spuštění libovolné funkce. Aplikace funkcí umožňuje seskupit funkce jako logická jednotka pro snadnější správu, nasazení, škálování a sdílení prostředků. Můžete vytvořit aplikaci .NET nebo JavaScript.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Ve výchozím nastavení používá vytvořená aplikace funkce verzi 2.x runtime Azure Functions. Rozšíření Durable Functions funguje na obou verzích 1.x a 2.x runtime Azure Functions v jazyce C# a verze 2.x v JavaScriptu. Šablony jsou však k dispozici pouze při cílení verze 2.x runtime bez ohledu na zvolený jazyk.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Instalace balíčku npm s trvalými funkcemi (pouze JavaScript)

Pokud vytváříte JavaScript Durable Functions, budete muset nainstalovat [ `durable-functions` balíček npm](https://www.npmjs.com/package/durable-functions).

1. Vyberte název aplikace funkce, následovanou **funkcemi platformy**a pak **pokročilými nástroji (Kudu).**

   ![Funkce funkce platformy zvolte Kudu](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. Uvnitř konzole Kudu vyberte **ladicí konzolu** a poté **CMD**.

   ![Konzola pro ladění Kudu](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. Měla by se zobrazit struktura adresáře souborů aplikace funkce. Přejděte do složky `site/wwwroot`. Odtud můžete nahrát `package.json` soubor přetažením do okna adresáře souborů. Ukázka `package.json` je níže:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   ![Kudu nahrát package.json](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. Po `package.json` nahrání spusťte `npm install` příkaz z konzoly vzdáleného spuštění Kudu.

   ![Kudu spustit npm nainstalovat](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Vytvoření funkce orchestrátoru

1. Rozbalte aplikaci funkcí **+** a klikněte na tlačítko vedle **položky Funkce**. Pokud jde o první funkci ve vaší aplikaci Function App, vyberte **Na portálu** a potom **Pokračovat**. V opačném případě přejděte ke třetímu kroku.

   ![Stručný úvod do služby Functions na webu Azure Portal](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Zvolte **Další šablony** a potom **Dokončit a zobrazit šablony**.

    ![Rychlý start služby Functions – výběr dalších šablon](./media/durable-functions-create-portal/add-first-function.png)

1. Do vyhledávacího pole `durable` zadejte a pak zvolte počáteční šablonu **PROTOKOLU HTTP durable functions.**

1. Po zobrazení výzvy vyberte **Nainstalovat,** chcete-li nainstalovat rozšíření Azure DurableTask a všechny závislosti v aplikaci funkce. Rozšíření pro danou aplikaci Function App stačí nainstalovat pouze jednou. Po úspěšném dokončení instalace vyberte **Pokračovat**.

    ![Instalace rozšíření vazby](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. Po dokončení instalace pojmenujte `HttpStart` novou funkci a zvolte **Vytvořit**. Vytvořená funkce se používá ke spuštění orchestrace.

1. Vytvořte další funkci v aplikaci funkce, tentokrát pomocí šablony **Durable Functions Orchestrator.** Pojmenujte novou `HelloSequence`funkci orchestrace .

1. Vytvořte třetí `Hello` funkci s názvem pomocí šablony **Aktivita trvalé funkce.**

## <a name="test-the-durable-function-orchestration"></a>Testování orchestrace trvanlivé funkce

1. Vraťte se k funkci **HttpStart,** zvolte **adresu URL funkce</> Získat** a **Zkopírujte** adresu URL. Tato adresa URL slouží ke spuštění funkce **HelloSequence.**

1. Pomocí nástroje HTTP, jako je Postman nebo cURL, odešlete požadavek POST na zkopírovanou adresu URL. Následující příklad je příkaz cURL, který odešle požadavek POST na trvalou funkci:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    V tomto `{your-function-app-name}` příkladu je doména, která je název aplikace funkce. Zpráva odpovědi obsahuje sadu koncových bodů URI, které můžete použít k monitorování a správě spouštění. Může vypadat jako v následujícím příkladu:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Zavolejte `statusQueryGetUri` identifikátor URI koncového bodu a zobrazí se aktuální stav trvalé funkce, která může vypadat jako v tomto příkladu:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Pokračujte `statusQueryGetUri` v volání koncového bodu, dokud se stav nezmění na **Dokončeno**a zobrazí se odpověď jako v následujícím příkladu:

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
> [Informace o běžných vzorcích trvalých funkcí](durable-functions-overview.md#application-patterns)

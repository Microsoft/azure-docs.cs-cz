---
title: Vytvoření Durable Functions pomocí Azure Portal
description: Naučte se, jak nainstalovat rozšíření Durable Functions pro Azure Functions pro vývoj na portálu.
ms.topic: conceptual
ms.date: 04/10/2020
ms.reviewer: azfuncdf
ms.openlocfilehash: b029fa246977dfe4210f6e8df242415f7e4103f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87081912"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Vytvoření Durable Functions pomocí Azure Portal

[Durable Functions](durable-functions-overview.md) rozšíření pro Azure Functions je k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Toto rozšíření musí být nainstalované ve vaší aplikaci Function App. Tento článek ukazuje, jak nainstalovat tento balíček, abyste mohli vyvíjet trvalé funkce v Azure Portal.

> [!NOTE]
> 
> * Při vývoji trvalých funkcí v jazyce C# byste místo toho měli zvážit [vývoj v aplikaci Visual Studio 2019](durable-functions-create-first-csharp.md).
> * Pokud vyvíjíte trvalé funkce v JavaScriptu, měli byste zvážit [Visual Studio Code vývoj](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Vytvoření aplikace funkcí

Musíte mít aplikaci Function App, která bude hostovat provádění jakékoli funkce. Aplikace Function App vám umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování, škálování a sdílení prostředků. Můžete vytvořit aplikaci pro .NET nebo JavaScript.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Ve výchozím nastavení aplikace Function App vytvořila používá modul runtime Azure Functions verze 2. x. Rozšíření Durable Functions pracuje na obou verzích 1. x a 2. x Azure Functions runtime v jazyce C# a verze 2. x v JavaScriptu. Šablony jsou však k dispozici pouze v případě cílení na verzi 2. x modulu runtime bez ohledu na zvolený jazyk.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Instalace balíčku odolného funkce NPM (jenom JavaScript)

Pokud vytváříte Durable Functions JavaScriptu, budete muset nainstalovat [ `durable-functions` balíček npm](https://www.npmjs.com/package/durable-functions):

1. Na stránce Function App vyberte v levém podokně v části **vývojové nástroje** možnost **Pokročilé nástroje** .

   :::image type="content" source="./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png" alt-text="Funkce platformy Functions zvolit Kudu":::

2. Na stránce **Rozšířené nástroje** vyberte **Přejít**.

3. V konzole Kudu vyberte **ladit Console**a pak **cmd**.

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Funkce platformy Functions zvolit Kudu"
      }
    }
    ```

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Funkce platformy Functions zvolit Kudu":::

4. Po `package.json` nahrání spusťte `npm install` příkaz z konzoly pro vzdálené spuštění Kudu.

   ![Kudu spustit instalaci npm](./media/durable-functions-create-portal/kudu-npm-install.png)
   
## <a name="create-an-orchestrator-function"></a>Vytvoření funkce Orchestrator

1. V aplikaci Function App vyberte v levém podokně **funkce** a pak v horní nabídce vyberte **Přidat** . 

1. Do vyhledávacího pole na stránce **Nová funkce** zadejte `durable` a pak zvolte šablonu **Durable Functions http Starter** .

   :::image type="content" source="./media/durable-functions-create-portal/durable-functions-http-starter-template.png" alt-text="Funkce platformy Functions zvolit Kudu":::

1. Pro **nový název funkce** zadejte `HttpStart` a pak vyberte **vytvořit funkci**.

   Vytvořená funkce se používá ke spuštění orchestrace.

1. Vytvořte v aplikaci Function App jinou funkci, tentokrát pomocí šablony **Durable Functions Orchestrator** . Pojmenujte novou funkci orchestrace `HelloSequence` .

1. Vytvořte třetí funkci s názvem `Hello` pomocí šablony **aktivity Durable Functions** .

## <a name="test-the-durable-function-orchestration"></a>Testování orchestrace trvalé funkce

1. Vraťte se do funkce **HttpStart** , zvolte **získat adresu URL funkce**a kliknutím na ikonu **Kopírovat do schránky** zkopírujte adresu URL. Pomocí této adresy URL spustíte funkci **HelloSequence** .

1. K odeslání požadavku POST na adresu URL, kterou jste zkopírovali, použijte nástroj HTTP, například post nebo kudrlinkou. Následující příklad je příkaz složeného příkazu, který odesílá požadavek POST do trvalé funkce:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence --header "Content-Length: 0"
    ```

    V tomto příkladu `{your-function-app-name}` je doménou název vaší aplikace Function App. Zpráva odpovědi obsahuje sadu koncových bodů URI, které můžete použít k monitorování a správě spouštění. Může vypadat jako v následujícím příkladu:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Zavolejte `statusQueryGetUri` identifikátor URI koncového bodu a uvidíte aktuální stav trvalé funkce, která může vypadat jako v tomto příkladu:

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

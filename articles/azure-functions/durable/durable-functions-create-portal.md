---
title: Vytvoření Durable Functions pomocí webu Azure portal
description: Zjistěte, jak nainstalovat rozšíření Durable Functions pro službu Azure Functions pro vývoj v portálu.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf, glenga
ms.openlocfilehash: acbba991e6dcce56fad7f27c45f85214cc8fc707
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642302"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Vytvoření Durable Functions pomocí webu Azure portal

[Durable Functions](durable-functions-overview.md) rozšíření pro službu Azure Functions je zahrnutý v balíčku NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Toto rozšíření musí být nainstalován ve své aplikaci function app. Tento článek popisuje, jak se Tenhle balíček nainstalovat, takže můžete vyvíjet odolná služba functions na webu Azure Portal.

>[!NOTE]
>
>* Pokud vyvíjíte odolná služba functions v C#, měli byste místo toho zvážit [vývoje sady Visual Studio 2017](durable-functions-create-first-csharp.md).
* Pokud vyvíjíte odolná služba functions v JavaScriptu, měli byste místo toho zvážit **vývoj pro Visual Studio Code**.
>
>Vytváření Durable Functions pomocí jazyka JavaScript se ještě nepodporuje na portálu. Místo toho použijte Visual Studio Code.

## <a name="create-a-function-app"></a>Vytvoření Function App

Musíte mít aplikaci function app k hostování provádění žádné funkce. Aplikace function app umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků. Je nutné vytvořit C# aplikace function app, protože šablony jazyka JavaScript pro Durable Functions zatím nepodporují.  

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Ve výchozím nastavení aplikace function app vytvořena používá verzi 2.x modulu runtime Azure Functions. Rozšíření Durable Functions funguje na obě verze 1.x a 2.x modulu runtime Azure Functions. Nicméně šablony jsou dostupné jenom při cílení na verzi 2.x modulu runtime.

## <a name="create-an-orchestrator-function"></a>Vytvoření funkce orchestrátoru

1. Rozbalte aplikaci Function App a klikněte na tlačítko **+** vedle položky **Funkce**. Pokud jde o první funkci ve vaší aplikaci Function App, vyberte **Na portálu** a potom **Pokračovat**. V opačném případě přejděte ke třetímu kroku.

   ![Stručný úvod do služby Functions na webu Azure Portal](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Zvolte **Další šablony** a potom **Dokončit a zobrazit šablony**.

    ![Rychlý start služby Functions – výběr dalších šablon](./media/durable-functions-create-portal/add-first-function.png)

1. Do vyhledávacího pole zadejte `durable` a klikněte na tlačítko **Spouštěč odolné funkce protokolu HTTP** šablony.

1. Po zobrazení výzvy vyberte **nainstalovat** k instalaci rozšíření Azure DurableTask případných závislostí do aplikace function App. Stačí nainstalovat rozšíření pro aplikaci function app udělení. Po úspěšném dokončení instalace vyberte **Pokračovat**.

    ![Instalace rozšíření vazby](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. Po dokončení instalace pojmenovat novou funkci `HttpStart` a zvolte **vytvořit**. Vytvořená funkce se používá ke spuštění orchestraci.

1. Vytvoření další funkce do aplikace function App, tentokrát pomocí **Orchestrátor Durable Functions** šablony. Pojmenujte svoji novou funkci Orchestrace `HelloSequence`.

1. Vytvořte třetí funkce s názvem `Hello` pomocí **aktivita Durable Functions** šablony.

## <a name="test-the-durable-function-orchestration"></a>Testování Orchestrace trvalý – funkce

1. Přejděte zpět **HttpStart** funkci, zvolte **<> / získat adresu URL funkce** a **kopírování** adresu URL. Pomocí této adresy URL pro spuštění **HelloSequence** funkce.

1. Pomocí nástroje protokolu HTTP jako je Postman nebo nástroj cURL k odeslání požadavku POST na adresu URL, kterou jste zkopírovali. V následujícím příkladu se příkaz cURL, který odešle požadavek POST do odolné funkce:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    V tomto příkladu `{your-function-app-name}` je shodný s doménou, která je název vaší aplikace function App. Zpráva odpovědi obsahuje sadu koncových bodů identifikátor URI, které můžete použít ke sledování a spravuje spouštění, která vypadá jako v následujícím příkladu:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Volání `statusQueryGetUri` identifikátor URI koncového bodu a zobrazit aktuální stav odolné funkce, která může vypadat jako v tomto příkladu:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Pokračovat volání `statusQueryGetUri` koncový bod, dokud se stav změní na **dokončeno**, a zobrazit odpověď podobná v následujícím příkladu: 

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

První odolné funkce je teď vytvořená a spuštěná v Azure.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o běžných vzorech trvalý – funkce](durable-functions-overview.md)
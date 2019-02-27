---
title: Rychlý start pro konfiguraci aplikací Azure s využitím Azure Functions | Dokumentace Microsoftu
description: Rychlý start pro konfiguraci aplikací Azure pomocí Azure Functions.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: Azure Functions
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: a6ed644334977f13107b12f955b1fbf015f731d1
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884886"
---
# <a name="quickstart-create-an-azure-function-with-app-configuration"></a>Rychlý start: Vytvoření funkce Azure s konfigurací aplikace

Konfigurace aplikace pro Azure je služba spravované konfigurace v Azure. To umožňuje snadno ukládat a spravovat nastavení aplikace na jednom místě, který je oddělen od kódu. V tomto rychlém startu se dozvíte, jak tuto službu začlenit do funkce Azure functions. 

K dokončení kroků v tomto rychlém startu můžete použít jakýkoli editor kódu. Skvělou volbou je však editor [Visual Studio Code](https://code.visualstudio.com/), který je dostupný pro platformy Windows, macOS a Linux.

![Dokončený rychlý start v místním prostředí](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, nainstalujte [Visual Studio 2017](https://visualstudio.microsoft.com/vs) (a ujistěte se, že **vývoj pro Azure** je nainstalovaná úloha) a [nejnovější nástroje Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-function-app"></a>Vytvoření Function App

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-app-configuration-store"></a>Připojit k úložišti konfigurace aplikace

1. Otevřít *Function1.cs* a přidejte odkaz na poskytovatele konfigurace aplikace konfigurace .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

2. Aktualizace `Run` používat konfiguraci aplikací pomocí volání metody `builder.AddAzureAppConfiguration()`.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        var config = builder.Build();
        string message = config["TestApp:Settings:Message"];
        message = message ?? req.Query["message"];

        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
        message = message ?? data?.message;

        return message != null
            ? (ActionResult) new OkObjectResult(message)
            : new BadRequestObjectResult("Please pass a message from a configuration store, on the query string or in the request body");
    }
    ```

## <a name="test-the-function-locally"></a>Místní testování funkce

1. Nastavte proměnnou prostředí s názvem **ConnectionString** a nastavte ho na přístupový klíč k úložišti konfigurace aplikace. Pokud používáte Windows příkazového řádku, spusťte následující příkaz a potom restartujte příkazového řádku umožňující změna se projeví:

        setx ConnectionString "Endpoint=<service_endpoint>;Id=<store_id>;Secret=<secret_key>="

    Pokud používáte prostředí Windows PowerShell, spusťte následující příkaz:

        $Env:ConnectionString = "Endpoint=<service_endpoint>;Id=<store_id>;Secret=<secret_key>="

    Pokud používáte v systému macOS nebo Linux, spusťte následující příkaz:

        export ConnectionString='Endpoint=<service_endpoint>;Id=<store_id>;Secret=<secret_key>='

2. Chcete-li funkci otestovat, stiskněte **F5**. Po výzvě přijměte požadavek ze sady Visual Studio ke stažení a instalaci **Azure Functions Core (CLI)** nástroje. Může být také potřeba povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP.

3. Zkopírujte adresu URL vaší funkce z výstupu modulu runtime služby Azure Functions.

    ![Rychlý start funkce ladění v sadě Visual Studio](./media/quickstarts/function-visual-studio-debugging.png)

4. Vložte adresu URL pro požadavek HTTP do panelu adresy prohlížeče. Následuje ukázka odezvy na místní požadavek GET vrácené funkcí v prohlížeči:

    ![Místní spuštění rychlý start – funkce](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili novým úložištěm konfigurace aplikace a použít ho pomocí funkce Azure. Další informace o použití konfigurace aplikace, pokračujte k dalšímu kurzu, který ukazuje ověření.

> [!div class="nextstepaction"]
> [Spravované identity pro integraci prostředků Azure](./integrate-azure-managed-service-identity.md)

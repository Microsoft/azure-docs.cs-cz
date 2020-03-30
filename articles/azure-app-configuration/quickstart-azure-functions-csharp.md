---
title: Úvodní příručka pro konfiguraci aplikací Azure s funkcemi Azure | Dokumenty společnosti Microsoft
description: Rychlý start pro používání Azure App Configuration s funkcemi Azure.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 2f6efdad7ab0685e58d2edd73bc36b758e8dbae2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245493"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Úvodní příručka: Vytvoření aplikace Azure Functions s konfigurací aplikací Azure

V tomto rychlém startu začleníte službu Azure App Configuration do aplikace Azure Functions, abyste centralizovali úložiště a správu všech nastavení aplikací oddělených od vašeho kódu.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) s **úlohou vývoje Azure.**
- [Nástroje Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikací

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **Průzkumník** > konfigurace **+ Vytvořit** > **klíč-hodnotu** přidat následující dvojice klíč-hodnota:

    | Klíč | Hodnota |
    |---|---|
    | testapp:nastavení:zpráva | Data z konfigurace aplikací Azure |

    Ponechte **popisek** a **typ obsahu** prozatím prázdný.

7. Vyberte **Použít**.

## <a name="create-a-functions-app"></a>Vytvoření aplikace Funkce

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikací

1. Klikněte pravým tlačítkem myši na projekt a vyberte **spravovat balíčky NuGet**. Na **kartě Procházet** vyhledejte `Microsoft.Extensions.Configuration.AzureAppConfiguration` a přidejte balíček NuGet do projektu. Pokud ji nemůžete najít, zaškrtněte políčko **Zahrnout předběžnou verzi.**

2. Open *Function1.cs*, and add the namespaces of the .NET Core configuration and the App Configuration configuration provider.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Přidejte `static` vlastnost `Configuration` s názvem k vytvoření `IConfiguration`instance singleton . Pak přidejte `static` konstruktor pro připojení `AddAzureAppConfiguration()`k konfiguraci aplikace voláním . To se načte konfiguraci jednou při spuštění aplikace. Stejná instance konfigurace bude použita pro všechna volání functions později.

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```

4. Aktualizujte `Run` metodu číst hodnoty z konfigurace.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Místní testování funkce

1. Nastavte proměnnou prostředí s názvem **ConnectionString**a nastavte ji na přístupový klíč do úložiště konfigurace aplikace. Pokud používáte příkazový řádek systému Windows, spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte prostředí Windows PowerShell, spusťte následující příkaz:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte macOS nebo Linux, spusťte následující příkaz:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Stisknutím klávesy F5 otestujte svou funkci. Pokud se zobrazí výzva, přijměte požadavek z Visual Studia na stažení a instalaci nástrojů **Azure Functions Core (CLI).** Může být také nutné povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP.

3. Zkopírujte adresu URL vaší funkce z výstupu modulu runtime služby Azure Functions.

    ![Ladění funkce rychlého startu ve VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Vložte adresu URL pro požadavek HTTP do panelu adresy prohlížeče. Následující obrázek znázorňuje odpověď v prohlížeči na místní požadavek GET vrácený funkcí.

    ![Funkce rychlého startu spustí místní](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nový obchod SKonfiguraci aplikací a použili ho s aplikací Azure Functions prostřednictvím [poskytovatele konfigurace aplikace](https://go.microsoft.com/fwlink/?linkid=2074664). Chcete-li se dozvědět, jak nakonfigurovat aplikaci Azure Functions pro dynamickou aktualizaci nastavení konfigurace, pokračujte dalším kurzem.

> [!div class="nextstepaction"]
> [Povolit dynamickou konfiguraci](./enable-dynamic-configuration-azure-functions-csharp.md)

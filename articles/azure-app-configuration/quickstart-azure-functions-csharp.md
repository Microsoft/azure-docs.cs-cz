---
title: Rychlý Start pro konfiguraci aplikací Azure s Azure Functions | Microsoft Docs
description: V tomto rychlém startu vytvoříte aplikaci Azure Functions s Azure App Configuration a C#. Vytvořte a připojte se k úložišti konfigurace aplikace. Otestujte funkci místně.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 9d378b21132e6646329c459401255ef9a3ed9426
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724233"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Rychlý Start: Vytvoření aplikace Azure Functions s využitím konfigurace aplikace Azure

V tomto rychlém startu zahrňte službu Azure App Configuration Service do aplikace Azure Functions, abyste mohli centralizovat úložiště a správu všech nastavení vaší aplikace odděleně od svého kódu.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/dotnet) .
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) s úlohou **vývoje Azure** .
- [Nástroje Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Vyberte možnost **Průzkumník konfigurace**  >  **+ vytvořit**  >  **klíč-hodnota** a přidejte následující páry klíč-hodnota:

    | Klíč | Hodnota |
    |---|---|
    | TestApp: nastavení: zpráva | Data z konfigurace aplikace Azure |

    V tuto chvíli ponechat **popisek** a **typ obsahu** prázdné.

8. Vyberte **Použít**.

## <a name="create-a-functions-app"></a>Vytvoření aplikace Functions

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikace
Tento projekt bude používat [Injektáže závislosti v rozhraní .net Azure Functions](../azure-functions/functions-dotnet-dependency-injection.md) a přidá konfiguraci aplikace Azure jako další zdroj konfigurace.

1. Klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**. Na kartě **Procházet** vyhledejte a přidejte do svého projektu následující balíčky NuGet.
   - [Microsoft.Extensions.Configuration. AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) verze 4.1.0 nebo novější
   - [Microsoft. Azure. Functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) verze 1.1.0 nebo novější 

2. Přidejte nový soubor *Startup.cs* s následujícím kódem. Definuje třídu s názvem `Startup` , která implementuje `FunctionsStartup` abstraktní třídu. Atribut assembly slouží k zadání názvu typu používaného během Azure Functionsho spuštění.

    `ConfigureAppConfiguration`Metoda je přepsaná a poskytovatel Azure App Configuration se přidá jako další zdroj konfigurace voláním `AddAzureAppConfiguration()` . `Configure`Metoda je ponechána prázdná, protože nemusíte v tomto okamžiku registrovat žádné služby.
    
    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
                string cs = Environment.GetEnvironmentVariable("ConnectionString");
                builder.ConfigurationBuilder.AddAzureAppConfiguration(cs);
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```

3. Otevřete *function1.cs* a přidejte následující obor názvů.

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

   Přidejte konstruktor, který slouží k získání instance `IConfiguration` prostřednictvím injektáže závislosti.

    ```csharp
    private readonly IConfiguration _configuration;

    public Function1(IConfiguration configuration)
    {
        _configuration = configuration;
    }
    ```

4. Aktualizujte `Run` metodu pro čtení hodnot z konfigurace.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

   `Function1`Třída a `Run` Metoda by neměly být statické. Odeberte `static` modifikátor, pokud byl automaticky vygenerován.

## <a name="test-the-function-locally"></a>Místní testování funkce

1. Nastavte proměnnou prostředí s názvem **ConnectionString** a nastavte ji na přístupový klíč na úložiště konfigurace aplikace. Použijete-li příkazový řádek systému Windows, spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte Windows PowerShell, spusťte následující příkaz:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte macOS nebo Linux, spusťte následující příkaz:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Pro otestování funkce stiskněte klávesu F5. Pokud se zobrazí výzva, přijměte požadavek ze sady Visual Studio a stáhněte a nainstalujte nástroje **Azure Functions Core (CLI)** . Je také možné, že budete muset povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP.

3. Zkopírujte adresu URL vaší funkce z výstupu modulu runtime služby Azure Functions.

    ![Rychlé ladění funkcí v VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Vložte adresu URL pro požadavek HTTP do panelu adresy prohlížeče. Na následujícím obrázku je znázorněna odpověď v prohlížeči na místní požadavek GET vrácený funkcí.

    ![Rychlé spuštění funkce spustit místně](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nové úložiště konfigurace aplikace a použili ho u Azure Functions aplikace přes [poskytovatele konfigurace aplikace](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration). Další informace o tom, jak aktualizovat aplikaci Azure Functions pro dynamickou aktualizaci konfigurace, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Povolení dynamické konfigurace v Azure Functions](./enable-dynamic-configuration-azure-functions-csharp.md)
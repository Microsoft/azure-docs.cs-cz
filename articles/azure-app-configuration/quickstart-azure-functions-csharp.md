---
title: Rychlý Start pro konfiguraci aplikací Azure s Azure Functions | Microsoft Docs
description: V tomto rychlém startu vytvoříte aplikaci Azure Functions s Azure App Configuration a C#. Vytvořte a připojte se k úložišti konfigurace aplikace. Otestujte funkci místně.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: lcozzens
ms.openlocfilehash: b62bf900e3e7859437b10b7b45801ee0dc575282
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767777"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Rychlý Start: Vytvoření aplikace Azure Functions s využitím konfigurace aplikace Azure

V tomto rychlém startu zahrňte službu Azure App Configuration Service do aplikace Azure Functions, abyste mohli centralizovat úložiště a správu všech nastavení vaší aplikace odděleně od svého kódu.

## <a name="prerequisites"></a>Předpoklady

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

1. Klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**. Na kartě **Procházet** vyhledejte a přidejte do `Microsoft.Extensions.Configuration.AzureAppConfiguration` svého projektu balíček NuGet. Pokud nemůžete najít, zaškrtněte políčko **zahrnout předběžné verze** .

2. Otevřete *function1.cs*a přidejte obory názvů konfigurace .NET Core a poskytovatele konfigurace aplikace.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Přidejte `static` vlastnost s názvem `Configuration` pro vytvoření instance singleton `IConfiguration` . Pak přidejte `static` konstruktor pro připojení ke konfiguraci aplikace voláním `AddAzureAppConfiguration()` . Tato akce načte konfiguraci jednou při spuštění aplikace. Stejná instance konfigurace bude použita pro všechny volání funkcí později.

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```

4. Aktualizujte `Run` metodu pro čtení hodnot z konfigurace.

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

1. Nastavte proměnnou prostředí s názvem **ConnectionString**a nastavte ji na přístupový klíč na úložiště konfigurace aplikace. Použijete-li příkazový řádek systému Windows, spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

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

V tomto rychlém startu jste vytvořili nové úložiště konfigurace aplikace a použili ho u Azure Functions aplikace přes [poskytovatele konfigurace aplikace](https://go.microsoft.com/fwlink/?linkid=2074664). Další informace o tom, jak nakonfigurovat aplikaci Azure Functions, aby dynamicky aktualizovala nastavení konfigurace, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Povolení dynamické konfigurace](./enable-dynamic-configuration-azure-functions-csharp.md)

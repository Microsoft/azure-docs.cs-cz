---
title: Kurz použití dynamické konfigurace Azure App Configuration v aplikaci Azure Functions | Microsoft Docs
description: V tomto kurzu se naučíte dynamicky aktualizovat konfigurační data pro Azure Functions aplikace.
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: devx-track-csharp, azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: e603aa8ba85fdd214c04de515f405bcf9028791e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88207105"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Kurz: použití dynamické konfigurace v aplikaci Azure Functions

Poskytovatel konfigurace .NET Standard konfigurace aplikace podporuje ukládání do mezipaměti a dynamické obnovení konfigurace, která je dynamicky řízená aktivitou aplikace. V tomto kurzu se dozvíte, jak můžete implementovat aktualizace dynamické konfigurace do kódu. Sestavuje se v aplikaci Azure Functions představené v rychlých startech. Než budete pokračovat, dokončete nejprve možnost [vytvořit aplikaci Azure Functions s konfigurací Azure App Configuration](./quickstart-azure-functions-csharp.md) .

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte si aplikaci Azure Functions pro aktualizaci konfigurace v reakci na změny v úložišti konfigurace aplikace.
> * Vloží nejnovější konfiguraci do vašich Azure Functions volání.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) s úlohou **vývoj pro Azure**
- [Nástroje Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Dokončení rychlého startu [Vytvoření aplikace Azure Functions pomocí konfigurace aplikace Azure](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Znovu načíst data z konfigurace aplikace

1. Otevřete *function1.cs*. Kromě `static` vlastnosti `Configuration` přidejte novou `static` vlastnost, `ConfigurationRefresher` která zachová instanci typu Singleton `IConfigurationRefresher` , která bude použita k signalizaci aktualizací konfigurace během volání funkcí později.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. Aktualizujte konstruktor a pomocí `ConfigureRefresh` metody určete nastavení, které má být obnoveno z úložiště konfigurace aplikace. Instance `IConfigurationRefresher` je načtena pomocí `GetRefresher` metody. Volitelně také změníme časový interval vypršení platnosti mezipaměti konfigurace na 1 minutu z výchozích 30 sekund.

    ```csharp
    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .ConfigureRefresh(refreshOptions =>
                        refreshOptions.Register("TestApp:Settings:Message")
                                      .SetCacheExpiration(TimeSpan.FromSeconds(60))
            );
            ConfigurationRefresher = options.GetRefresher();
        });
        Configuration = builder.Build();
    }
    ```

3. Aktualizujte `Run` metodu a signál pro aktualizaci konfigurace pomocí `TryRefreshAsync` metody na začátku volání funkce. Pokud nedosáhnete časového intervalu vypršení platnosti mezipaměti, nebude to nic. Odeberte `await` operátor, pokud dáváte přednost aktualizaci konfigurace bez blokování.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await ConfigurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Místní testování funkce

1. Nastavte proměnnou prostředí s názvem **ConnectionString**a nastavte ji na přístupový klíč na úložiště konfigurace aplikace. Použijete-li příkazový řádek systému Windows, spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte Windows PowerShell, spusťte následující příkaz:

    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte macOS nebo Linux, spusťte následující příkaz:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Pokud chcete funkci otestovat, stiskněte F5. Pokud se zobrazí výzva, přijměte požadavek ze sady Visual Studio a stáhněte a nainstalujte nástroje **Azure Functions Core (CLI)** . Je také možné, že budete muset povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP.

3. Zkopírujte adresu URL vaší funkce z výstupu modulu runtime služby Azure Functions.

    ![Rychlé ladění funkcí v VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Vložte adresu URL pro požadavek HTTP do panelu adresy prohlížeče. Na následujícím obrázku je znázorněna odpověď v prohlížeči na místní požadavek GET vrácený funkcí.

    ![Rychlé spuštění funkce spustit místně](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **všechny prostředky**a vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v rychlém startu.

6. Vyberte **Průzkumník konfigurace**a aktualizujte hodnoty následujícího klíče:

    | Klíč | Hodnota |
    |---|---|
    | TestApp: nastavení: zpráva | Data z konfigurace aplikace Azure – Aktualizováno |

7. Aktualizujte prohlížeč několikrát. Jakmile vyprší platnost nastavení uloženého v mezipaměti po minutě, stránka zobrazuje odpověď volání funkcí s aktualizovanou hodnotou.

    ![Funkce rychlého startu – místní aktualizace](./media/quickstarts/dotnet-core-function-refresh-local.png)

Vzorový kód použitý v tomto kurzu se dá stáhnout z [úložiště GitHub konfigurace aplikace](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction) .

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili aplikaci Azure Functions k dynamické aktualizaci nastavení konfigurace z konfigurace aplikace. Další informace o tom, jak používat spravovanou identitu Azure ke zjednodušení přístupu ke konfiguraci aplikace, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Spravovaná integrace identit](./howto-integrate-azure-managed-service-identity.md)

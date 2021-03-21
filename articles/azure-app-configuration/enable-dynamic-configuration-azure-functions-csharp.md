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
ms.openlocfilehash: add4b54adb02db09536f4e56a7f039c46245c182
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97963552"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Kurz: použití dynamické konfigurace v aplikaci Azure Functions

Zprostředkovatel konfigurace .NET konfigurace aplikace podporuje ukládání do mezipaměti a dynamicky se aktualizuje pomocí aktivity aplikace. V tomto kurzu se dozvíte, jak můžete implementovat aktualizace dynamické konfigurace do kódu. Sestavuje se v aplikaci Azure Functions představené v rychlých startech. Než budete pokračovat, dokončete nejprve možnost [vytvořit aplikaci Azure Functions s konfigurací Azure App Configuration](./quickstart-azure-functions-csharp.md) .

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte si aplikaci Azure Functions pro aktualizaci konfigurace v reakci na změny v úložišti konfigurace aplikace.
> * Vloží nejnovější konfiguraci do vašich Azure Functions volání.

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) s úlohou **vývoj pro Azure**
- [Nástroje Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Dokončení rychlého startu [Vytvoření aplikace Azure Functions pomocí konfigurace aplikace Azure](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Znovu načíst data z konfigurace aplikace

1. Otevřete *Startup. cs* a aktualizujte `ConfigureAppConfiguration` metodu. 

   `ConfigureRefresh`Metoda registruje nastavení, které bude kontrolovat změny vždy, když se aktivuje aktualizace v rámci aplikace, kterou provedete v pozdějším kroku při přidávání `_configurationRefresher.TryRefreshAsync()` . `refreshAll`Parametr nastaví poskytovatele konfigurace aplikace tak, aby znovu znovu nahlásil celou konfiguraci při zjištění změny v registrovaném nastavení.

    Všechna nastavení zaregistrovaná pro obnovení mají výchozí dobu platnosti mezipaměti 30 sekund. Dá se aktualizovat voláním `AzureAppConfigurationRefreshOptions.SetCacheExpiration` metody.

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   // Load all keys that start with `TestApp:`
                   .Select("TestApp:*")
                   // Configure to reload configuration if the registered 'Sentinel' key is modified
                   .ConfigureRefresh(refreshOptions =>
                      refreshOptions.Register("TestApp:Settings:Sentinel", refreshAll: true));
        });
    }
    ```

   > [!TIP]
   > Když aktualizujete více hodnot klíče v konfiguraci aplikace, normálně nechcete, aby aplikace znovu znovu načítat konfiguraci před provedením všech změn. Můžete zaregistrovat klíč **Sentinel** a aktualizovat ho pouze v případě, že jsou dokončeny všechny ostatní změny konfigurace. To pomáhá zajistit konzistenci konfigurace ve vaší aplikaci.

2. Aktualizujte `Configure` metodu tak, aby byly dostupné služby Azure App Configuration prostřednictvím injektáže závislostí.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
    }
    ```

3. Otevřete *function1. cs* a přidejte následující obory názvů.

    ```csharp
    using System.Linq;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   Aktualizujte konstruktor tak, aby získal instanci `IConfigurationRefresherProvider` prostřednictvím injektáže závislosti, ze které můžete získat instanci `IConfigurationRefresher` .

    ```csharp
    private readonly IConfiguration _configuration;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IConfiguration configuration, IConfigurationRefresherProvider refresherProvider)
    {
        _configuration = configuration;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

4. Aktualizujte `Run` metodu a signál pro aktualizaci konfigurace pomocí `TryRefreshAsync` metody na začátku volání funkce. Pokud není dosaženo časového intervalu vypršení platnosti mezipaměti, bude to no-op. `await`Pokud dáváte přednost konfiguraci, která se má aktualizovat bez blokování aktuálních volání funkcí, odeberte operátor. V takovém případě se později volání funkcí zobrazí aktualizovaná hodnota.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Místní testování funkce

1. Nastavte proměnnou prostředí s názvem **ConnectionString** a nastavte ji na přístupový klíč na úložiště konfigurace aplikace. Použijete-li příkazový řádek systému Windows, spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

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

5. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **všechny prostředky** a vyberte úložiště konfigurace aplikace, které jste vytvořili v rychlém startu.

6. Vyberte **Průzkumník konfigurace** a aktualizujte hodnotu následujícího klíče:

    | Klíč | Hodnota |
    |---|---|
    | TestApp: nastavení: zpráva | Data z konfigurace aplikace Azure – Aktualizováno |

   Pak vytvořte klíč Sentinel nebo změňte jeho hodnotu, pokud již existuje, například

    | Klíč | Hodnota |
    |---|---|
    | TestApp: nastavení: Sentinel | V1 |


7. Aktualizujte prohlížeč několikrát. Pokud platnost nastavení v mezipaměti vyprší po 30 sekundách, stránka zobrazuje odpověď volání funkcí s aktualizovanou hodnotou.

    ![Funkce rychlého startu – místní aktualizace](./media/quickstarts/dotnet-core-function-refresh-local.png)

> [!NOTE]
> Vzorový kód použitý v tomto kurzu se dá stáhnout z [úložiště GitHub konfigurace aplikace](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction).

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili aplikaci Azure Functions k dynamické aktualizaci nastavení konfigurace z konfigurace aplikace. Další informace o tom, jak používat spravovanou identitu Azure ke zjednodušení přístupu ke konfiguraci aplikace, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Spravovaná integrace identit](./howto-integrate-azure-managed-service-identity.md)

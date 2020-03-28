---
title: Kurz pro použití dynamické konfigurace konfigurace konfigurace aplikace Azure v aplikaci Azure Functions | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak dynamicky aktualizovat konfigurační data pro aplikace Azure Functions
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
ms.custom: azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: ba70d5f186c1424b2019716ab7a87aeae85f8913
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74185447"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Kurz: Použití dynamické konfigurace v aplikaci Azure Functions

Zprostředkovatel konfigurace Konfigurace konfigurace Konfigurace konfigurace Konfigurace konfigurace Konfigurace konfigurace Konfigurace konfigurace Konfigurace konfigurace konfigurace konfigurace podporuje ukládání do mezipaměti a aktualizace konfigurace dynamicky řízena aktivitou aplikace. Tento kurz ukazuje, jak můžete implementovat dynamické aktualizace konfigurace v kódu. Vychází z aplikace Azure Functions zavedené v rychlých startech. Než budete pokračovat, nejdřív [dokončete vytvoření aplikace Azure functions s Azure App Configuration.](./quickstart-azure-functions-csharp.md)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte aplikaci Azure Functions tak, aby aktualizovala svou konfiguraci v reakci na změny v obchodě konfigurace aplikací.
> * Vstříkněte nejnovější konfiguraci do volání Azure Functions.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) s **úlohou vývoje Azure**
- [Nástroje Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Dokončení rychlého startu [Vytvoření aplikace Azure functions s konfigurací aplikací Azure](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Opětovné načtení dat z konfigurace aplikace

1. Otevřít *Function1.cs*. Kromě vlastnosti `static` `Configuration`přidejte novou `static` `ConfigurationRefresher` vlastnost, která zachová `IConfigurationRefresher` instanci singleton, která bude použita k signalizaci aktualizací konfigurace během volání funkce později.

    ```csharp
    private static IConfiguration Configuration { set; get; }
    private static IConfigurationRefresher ConfigurationRefresher { set; get; }
    ```

2. Aktualizujte konstruktor a `ConfigureRefresh` pomocí metody určete nastavení, které má být aktualizováno z úložiště konfigurace aplikace. Instance `IConfigurationRefresher` je načten pomocí `GetRefresher` metody. Volitelně také změníme časové okno vypršení platnosti mezipaměti konfigurace na 1 minutu z výchozích 30 sekund.

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

3. Aktualizujte `Run` metodu a signál pro `Refresh` aktualizaci konfigurace pomocí metody na začátku volání Funkce. To bude no-op, pokud není dosaženo časového období vypršení platnosti mezipaměti. Pokud `await` dáváte přednost aktualizaci konfigurace bez blokování, odeberte operátor.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await ConfigurationRefresher.Refresh();

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Místní testování funkce

1. Nastavte proměnnou prostředí s názvem **ConnectionString**a nastavte ji na přístupový klíč do úložiště konfigurace aplikace. Pokud používáte příkazový řádek systému Windows, spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Pokud používáte prostředí Windows PowerShell, spusťte následující příkaz:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Pokud používáte macOS nebo Linux, spusťte následující příkaz:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Pokud chcete funkci otestovat, stiskněte F5. Pokud se zobrazí výzva, přijměte požadavek z Visual Studia na stažení a instalaci nástrojů **Azure Functions Core (CLI).** Může být také nutné povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP.

3. Zkopírujte adresu URL vaší funkce z výstupu modulu runtime služby Azure Functions.

    ![Ladění funkce rychlého startu ve VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Vložte adresu URL pro požadavek HTTP do panelu adresy prohlížeče. Následující obrázek znázorňuje odpověď v prohlížeči na místní požadavek GET vrácený funkcí.

    ![Funkce rychlého startu spustí místní](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **Všechny prostředky**a vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v rychlém startu.

6. Vyberte **Průzkumník konfigurace**a aktualizujte hodnoty následujícího klíče:

    | Klíč | Hodnota |
    |---|---|
    | testapp:nastavení:zpráva | Data z konfigurace aplikací Azure – aktualizována |

7. Několikrát aktualizujte prohlížeč. Když vyprší nastavení mezipaměti po minutě, stránka zobrazí odpověď volání Funkce s aktualizovanou hodnotou.

    ![Aktualizace funkce rychlého startu místní](./media/quickstarts/dotnet-core-function-refresh-local.png)

Ukázkový kód použitý v tomto kurzu lze stáhnout z [úložiště Konfigurace aplikace GitHub](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili aplikaci Azure Functions dynamicky aktualizovat nastavení konfigurace z konfigurace aplikace. Chcete-li se dozvědět, jak používat spravovanou identitu Azure ke zjednodušení přístupu ke konfiguraci aplikací, pokračujte dalším kurzem.

> [!div class="nextstepaction"]
> [Integrace spravované identity](./howto-integrate-azure-managed-service-identity.md)

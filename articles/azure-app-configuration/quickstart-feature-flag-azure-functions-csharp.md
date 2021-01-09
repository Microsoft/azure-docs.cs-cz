---
title: Rychlý Start pro přidání příznaků funkcí do Azure Functions | Microsoft Docs
description: V tomto rychlém startu použijte Azure Functions s příznaky funkcí z konfigurace aplikace Azure a místně otestujte funkci.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 8/26/2020
ms.author: alkemper
ms.openlocfilehash: 6996fdd9dce4314e9365177815d7d310ac80c7cb
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98046069"
---
# <a name="quickstart-add-feature-flags-to-an-azure-functions-app"></a>Rychlý Start: Přidání příznaků funkcí do aplikace Azure Functions

V tomto rychlém startu vytvoříte aplikaci Azure Functions a v ní použijete příznaky funkcí. Pomocí správy funkcí z Azure App Configuration můžete centrálně ukládat všechny příznaky funkcí a řídit jejich stavy.

Knihovny pro správu funkcí .NET rozšíří rozhraní s podporou příznaků funkcí. Tyto knihovny jsou postaveny na systému konfigurace .NET. Integrují s konfigurací aplikace prostřednictvím poskytovatele konfigurace .NET.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) s úlohou **vývoje Azure** .
- [Nástroje Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Vyberte **správce funkcí**  >  **+ Přidat** a přidejte příznak funkce s názvem `Beta` .

    > [!div class="mx-imgBorder"]
    > ![Povolit příznak funkce s názvem beta](media/add-beta-feature-flag.png)

    `label` `Description` Pro nyní nechejte a nedefinované.

8. Výběrem **použít** uložte příznak nové funkce.

## <a name="create-a-functions-app"></a>Vytvoření aplikace Functions

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikace

Tento projekt bude používat [vkládání závislostí v rozhraní .net Azure Functions](/azure/azure-functions/functions-dotnet-dependency-injection). Přidá konfiguraci aplikace Azure jako další zdroj konfigurace, ve kterém jsou uložené vaše příznaky funkcí.

1. Klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**. Na kartě **Procházet** vyhledejte a přidejte do svého projektu následující balíčky NuGet.
   - [Microsoft.Extensions.Configuration. AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) verze 4.1.0 nebo novější
   - [Microsoft. FeatureManagement](https://www.nuget.org/packages/Microsoft.FeatureManagement/) verze 2.2.0 nebo novější
   - [Microsoft. Azure. Functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) verze 1.1.0 nebo novější 

2. Přidejte nový soubor *Startup.cs* s následujícím kódem. Definuje třídu s názvem `Startup` , která implementuje `FunctionsStartup` abstraktní třídu. Atribut assembly slouží k zadání názvu typu používaného během Azure Functionsho spuštění.

    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.FeatureManagement;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```


3. Aktualizujte `ConfigureAppConfiguration` metodu a přidejte poskytovatele konfigurace Azure App Provider jako další zdroj konfigurace voláním `AddAzureAppConfiguration()` . 

   `UseFeatureFlags()`Metoda instruuje poskytovatele, aby načetl příznaky funkcí. U všech příznaků funkcí je před opakovanou kontrolou změn použita výchozí doba platnosti mezipaměti 30 sekund. Interval vypršení platnosti lze aktualizovat nastavením `FeatureFlagsOptions.CacheExpirationInterval` vlastnosti předané `UseFeatureFlags` metodě. 

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .Select("_")
                   .UseFeatureFlags();
        });
    }
    ```
   > [!TIP]
   > Pokud nechcete, aby byla do aplikace načtena jiná konfigurace než příznaky funkce, můžete zavolat `Select("_")` pouze k načtení neexistujícího fiktivního klíče "_". Ve výchozím nastavení budou načteny všechny hodnoty konfiguračního klíče v úložišti konfigurace aplikace, pokud `Select` není volána žádná metoda.

4. Aktualizujte `Configure` metodu tak, aby služba Azure App Configuration Services a správce funkcí byly dostupné prostřednictvím injektáže závislostí.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
        builder.Services.AddFeatureManagement();
    }
    ```

5. Otevřete *function1.cs* a přidejte následující obory názvů.

    ```csharp
    using System.Linq;
    using Microsoft.FeatureManagement;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   Přidejte konstruktor, který slouží k získání instancí `_featureManagerSnapshot` a `IConfigurationRefresherProvider` prostřednictvím injektáže závislosti. Z rozhraní `IConfigurationRefresherProvider` můžete získat instanci `IConfigurationRefresher` .

    ```csharp
    private readonly IFeatureManagerSnapshot _featureManagerSnapshot;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IFeatureManagerSnapshot featureManagerSnapshot, IConfigurationRefresherProvider refresherProvider)
    {
        _featureManagerSnapshot = featureManagerSnapshot;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

6. Aktualizujte `Run` metodu pro změnu hodnoty zobrazené zprávy v závislosti na stavu příznaku funkce.

   `TryRefreshAsync`Metoda je volána na začátku volání funkce pro aktualizaci příznaků funkcí. Pokud není dosaženo časového intervalu vypršení platnosti mezipaměti, bude to no-op. Pokud dáváte přednost příznakům funkcí, které se `await` mají aktualizovat bez blokování aktuálních volání funkcí, odeberte operátor. V takovém případě se později volání funkcí zobrazí aktualizovaná hodnota.

    ```csharp
    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
        ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync();

        string message = await _featureManagerSnapshot.IsEnabledAsync("Beta")
                ? "The Feature Flag 'Beta' is turned ON"
                : "The Feature Flag 'Beta' is turned OFF";

        return (ActionResult)new OkObjectResult(message);
    }
    ```

## <a name="test-the-function-locally"></a>Místní testování funkce

1. Nastavte proměnnou prostředí s názvem **ConnectionString**, kde hodnota je připojovací řetězec, který jste dříve získali v úložišti konfigurace aplikace v části **přístupové klíče**. Použijete-li příkazový řádek systému Windows, spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

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

1. Pro otestování funkce stiskněte klávesu F5. Pokud se zobrazí výzva, přijměte požadavek ze sady Visual Studio a stáhněte a nainstalujte nástroje **Azure Functions Core (CLI)** . Je také možné, že budete muset povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP.

1. Zkopírujte adresu URL vaší funkce z výstupu modulu runtime služby Azure Functions.

    ![Rychlé ladění funkcí v VS](./media/quickstarts/function-visual-studio-debugging.png)

1. Vložte adresu URL pro požadavek HTTP do panelu adresy prohlížeče. Následující obrázek ukazuje odpověď označující, že příznak funkce `Beta` je zakázán. 

    ![Příznak funkce funkce rychlého startu zakázán](./media/quickstarts/functions-launch-ff-disabled.png)

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **všechny prostředky** a vyberte úložiště konfigurace aplikace, které jste vytvořili.

1. Vyberte **správce funkcí** a změňte stav **beta** klíče na **zapnuto**.

1. Aktualizujte prohlížeč několikrát. Pokud platnost příznaku funkce v mezipaměti vyprší po 30 sekundách, stránka by se měla změnit tak, aby označovala příznak funkce `Beta` , jak je znázorněno na následujícím obrázku.
 
    ![Příznak funkce funkce rychlého startu povolen](./media/quickstarts/functions-launch-ff-enabled.png)

> [!NOTE]
> Vzorový kód použitý v tomto kurzu se dá stáhnout z [úložiště GitHub pro konfiguraci aplikací Azure](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction).

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili příznak funkce a použili ho v aplikaci Azure Functions pomocí knihovny [Microsoft. FeatureManagement](/dotnet/api/microsoft.featuremanagement) .

- Další informace o [správě funkcí](./concept-feature-management.md)
- [Správa příznaků funkcí](./manage-feature-flags.md)
- [Použití příznaků podmíněné funkce](./howto-feature-filters-aspnet-core.md)
- [Povolení připraveného zavedení funkcí pro cílové cílové skupiny](./howto-targetingfilter-aspnet-core.md)
- [Použití dynamické konfigurace v aplikaci Azure Functions](./enable-dynamic-configuration-azure-functions-csharp.md)
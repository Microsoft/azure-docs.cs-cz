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
ms.openlocfilehash: 5542dca6d9b1ab18cf1b9b93e20fa64da0eb1dd4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "91000932"
---
# <a name="quickstart-add-feature-flags-to-an-azure-functions-app"></a>Rychlý Start: Přidání příznaků funkcí do aplikace Azure Functions

V tomto rychlém startu vytvoříte implementaci správy funkcí v aplikaci Azure Functions pomocí Azure App Configuration. Službu konfigurace aplikací použijete k centrálnímu ukládání všech příznaků funkcí a řízení jejich stavů. 

Knihovny pro správu funkcí .NET rozšíří rozhraní s podporou příznaků funkcí. Tyto knihovny jsou postaveny na systému konfigurace .NET. Integrují s konfigurací aplikace prostřednictvím poskytovatele konfigurace .NET.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) s úlohou **vývoje Azure** .
- [Nástroje Azure Functions](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **správce funkcí**  >  **+ Přidat** a přidejte příznak funkce s názvem `Beta` .

    > [!div class="mx-imgBorder"]
    > ![Povolit příznak funkce s názvem beta](media/add-beta-feature-flag.png)

    `label` `Description` Pro nyní nechejte a nedefinované.

7. Výběrem **použít** uložte příznak nové funkce.

## <a name="create-a-functions-app"></a>Vytvoření aplikace Functions

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikace

1. Klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**. Na kartě **Procházet** vyhledejte a přidejte do svého projektu následující balíčky NuGet. Ověřte `Microsoft.Extensions.DependencyInjection` , že jste na nejnovějším stabilním sestavení. 

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration
    Microsoft.FeatureManagement
    ```


1. Otevřete *function1.cs*a přidejte obory názvů těchto balíčků.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.FeatureManagement;
    using Microsoft.Extensions.DependencyInjection;
    ```

1. Přidejte `Function1` statický konstruktor níže, abyste mohli spustit poskytovatele konfigurace Azure App. Dále přidejte dva `static` členy, pole s názvem `ServiceProvider` k vytvoření instance singleton `ServiceProvider` a vlastnost uvedenou níže `Function1` `FeatureManager` pro vytvoření instance singleton `IFeatureManager` . Pak se pomocí volání připojte ke konfiguraci aplikace `Function1` `AddAzureAppConfiguration()` . Tento proces načte konfiguraci při spuštění aplikace. Stejná instance konfigurace bude použita pro všechny volání funkcí později. 

    ```csharp
        // Implements IDisposable, cached for life time of function
        private static ServiceProvider ServiceProvider; 

        static Function1()
        {
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            var services = new ServiceCollection();                                                                             
            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            ServiceProvider = services.BuildServiceProvider(); 
        }

        private static IFeatureManager FeatureManager => ServiceProvider.GetRequiredService<IFeatureManager>();
    ```

1. Aktualizujte `Run` metodu pro změnu hodnoty zobrazené zprávy v závislosti na stavu příznaku funkce.

    ```csharp
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
                [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
                ILogger log)
            {
                string message = await FeatureManager.IsEnabledAsync("Beta")
                     ? "The Feature Flag 'Beta' is turned ON"
                     : "The Feature Flag 'Beta' is turned OFF";
                
                return (ActionResult)new OkObjectResult(message); 
            }
    ```

## <a name="test-the-function-locally"></a>Místní testování funkce

1. Nastavte proměnnou prostředí s názvem **ConnectionString**, kde hodnota je přístupový klíč, který jste dříve získali v úložišti konfigurace aplikace v **přístupových klíčích**. Použijete-li příkazový řádek systému Windows, spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

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

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **všechny prostředky**a vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili.

1. Vyberte **správce funkcí**a změňte stav **beta** klíče na **zapnuto**.

1. Vraťte se do příkazového řádku a stisknutím klávesy zrušte běžící proces `Ctrl-C` .  Stisknutím klávesy F5 restartujte aplikaci. 

1. Zkopírujte adresu URL vaší funkce z výstupu Azure Functions runtime pomocí stejného procesu jako v kroku 3. Vložte adresu URL pro požadavek HTTP do panelu adresy prohlížeče. Odpověď prohlížeče by se měla změnit tak, aby označovala příznak funkce `Beta` , jak je znázorněno na obrázku níže.
 
    ![Příznak funkce funkce rychlého startu povolen](./media/quickstarts/functions-launch-ff-enabled.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili příznak funkce a použili ho v aplikaci Azure Functions prostřednictvím [poskytovatele konfigurace aplikace](https://go.microsoft.com/fwlink/?linkid=2074664).

- Přečtěte si další informace o [správě funkcí](./concept-feature-management.md).
- [Správa příznaků funkcí](./manage-feature-flags.md).
- [Použití dynamické konfigurace v aplikaci Azure Functions](./enable-dynamic-configuration-azure-functions-csharp.md)

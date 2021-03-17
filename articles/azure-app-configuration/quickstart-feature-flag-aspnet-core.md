---
title: Rychlý Start pro přidání příznaků funkcí do ASP.NET Core
description: Přidání příznaků funkcí pro ASP.NET Core aplikací a jejich správa pomocí konfigurace aplikací Azure
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 8ca4a6e78ab3c6ccf492869bb68c5296ae91de21
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806066"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Rychlý Start: Přidání příznaků funkcí do aplikace ASP.NET Core

V tomto rychlém startu vytvoříte ucelenou implementaci správy funkcí v aplikaci ASP.NET Core pomocí Azure App Configuration. Službu konfigurace aplikací použijete k centrálnímu ukládání všech příznaků funkcí a řízení jejich stavů. 

Knihovny pro správu funkcí .NET Core přesahují rámec s kompletní podporou příznaků funkcí. Tyto knihovny jsou postaveny na systému konfigurace .NET Core. Bez problémů se integrují s konfigurací aplikací prostřednictvím poskytovatele konfigurace .NET Core.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/dotnet) .
* [Sada .NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

8. Vyberte **Operations**  >  **Manager**–  >  **Přidat** a přidejte příznak funkce s názvem *beta*.

    > [!div class="mx-imgBorder"]
    > ![Povolit příznak funkce s názvem beta](media/add-beta-feature-flag.png)

    Pro nyní nechejte **popisek** prázdné. Výběrem **použít** uložte příznak nové funkce.

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

K vytvoření nového projektu ASP.NET Core MVC použijte [rozhraní příkazového řádku .NET Core (CLI)](/dotnet/core/tools) . Výhodou použití .NET Core CLI místo sady Visual Studio je, že .NET Core CLI je k dispozici v rámci platforem Windows, macOS a Linux.

Spuštěním následujícího příkazu vytvořte projekt ASP.NET Core MVC v nové složce *TestFeatureFlags* :

```dotnetcli
dotnet new mvc --no-https --output TestFeatureFlags
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikace

1. Nainstalujte balíček NuGet [Microsoft. Azure. AppConfiguration. AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) a [Microsoft. FeatureManagement. AspNetCore](https://www.nuget.org/packages/Microsoft.FeatureManagement.AspNetCore) spuštěním následujících příkazů:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

    ```dotnetcli
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. Spusťte následující příkaz ve stejném adresáři jako soubor *. csproj* . Příkaz používá správce tajných klíčů k ukládání tajného kódu s názvem `ConnectionStrings:AppConfig` , který ukládá připojovací řetězec pro úložiště konfigurace aplikace. `<your_connection_string>`Zástupný symbol nahraďte připojovacím řetězcem konfiguračního úložiště aplikace. Připojovací řetězec najdete v části **přístupové klíče** v Azure Portal.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    Správce tajného klíče se používá jenom k místnímu testování webové aplikace. Když se aplikace nasadí do [Azure App Service](https://azure.microsoft.com/services/app-service/web), použijte nastavení aplikace **připojovací řetězce** v App Service namísto správce tajných klíčů k uložení připojovacího řetězce.

    Přístup k tomuto tajnému kódu pomocí konfiguračního rozhraní API .NET Core. Dvojtečka ( `:` ) funguje v názvu konfigurace s rozhraním API konfigurace na všech podporovaných platformách. Další informace najdete v tématu [konfigurační klíče a hodnoty](/aspnet/core/fundamentals/configuration#configuration-keys-and-values).

1. V *program.cs* aktualizujte `CreateWebHostBuilder` metodu tak, aby používala konfiguraci aplikace, voláním `AddAzureAppConfiguration` metody.

    > [!IMPORTANT]
    > `CreateHostBuilder` nahrazuje `CreateWebHostBuilder` v .NET Core 3. x. Vyberte správnou syntaxi na základě vašeho prostředí.

     #### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(connection).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(connection).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration(config =>
               {
                   var settings = config.Build();
                   var connection = settings.GetConnectionString("AppConfig");
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(connection).UseFeatureFlags());
               }).UseStartup<Startup>();
    ```

    ---

    V předchozí změně byl [Poskytovatel konfigurace pro konfiguraci aplikace](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration) zaregistrován s rozhraním .NET Core Configuration API.

1. Do *Startup.cs* přidejte odkaz na správce funkcí .NET Core:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Aktualizujte `Startup.ConfigureServices` metodu pro přidání podpory příznaků funkcí voláním `AddFeatureManagement` metody. Volitelně můžete zahrnout jakýkoli filtr, který se má použít s příznaky funkcí voláním `AddFeatureFilter<FilterType>()` :

     #### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc()
            .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
        services.AddFeatureManagement();
    }
    ```

    ---

1. Do kořenového adresáře projektu přidejte soubor *MyFeatureFlags.cs* s následujícím kódem:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Přidejte soubor *BetaController.cs* do adresáře *Controllers* pomocí následujícího kódu:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager) =>
                _featureManager = featureManager;

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index() => View();
        }
    }
    ```

1. V *zobrazeních/_ViewImports. cshtml* Zaregistrujte pomocníka značky správce funkcí pomocí `@addTagHelper` direktivy:

    ```cshtml
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

    Předchozí kód umožňuje `<feature>` použití pomocné rutiny tag v souborech *. cshtml* projektu.

1. Otevřete *_Layout. cshtml* ve sdíleném adresáři *views* \\  . Vyhledejte `<nav>` Čárový kód v části `<body>`  >  `<header>` . Vložte novou `<feature>` značku mezi položky *Domů* a navigační panel *ochrany osobních údajů* , jak je znázorněno na zvýrazněných řádcích níže.

    :::code language="html" source="../../includes/azure-app-configuration-navbar.md" range="15-38" highlight="14-18":::

1. Vytvořte adresář *views/beta* a soubor *index. cshtml* obsahující následující kód:

    ```cshtml
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>This is the beta website.</h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Pokud chcete aplikaci vytvořit pomocí .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

    ```dotnetcli
    dotnet build
    ```

1. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění webové aplikace:

    ```dotnetcli
    dotnet run
    ```

1. Otevřete okno prohlížeče a pro `http://localhost:5000` webovou aplikaci hostovanou místně použijte výchozí adresu URL. Pokud pracujete na Azure Cloud Shell, klikněte na tlačítko **Náhled webu** a potom na **Konfigurovat**. Po zobrazení výzvy vyberte port 5000.

    ![Najít tlačítko Náhled webu](./media/quickstarts/cloud-shell-web-preview.png)

    V prohlížeči by se měla zobrazit stránka podobná následujícímu obrázku.

    :::image type="content" source="media/quickstarts/aspnet-core-feature-flag-local-before.png" alt-text="Místní aplikace pro rychlý Start před změnou" border="true":::

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **všechny prostředky** a vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v rychlém startu.

1. Vyberte **správce funkcí**. 

1. Aktivujte příznak *beta* zaškrtnutím políčka v části **povoleno**.

1. Vraťte se do příkazového prostředí. Spuštění procesu ukončíte `dotnet` stisknutím <kbd>kombinace kláves CTRL + C</kbd>. Restartujte aplikaci pomocí `dotnet run` .

1. Aktualizujte stránku prohlížeče, aby se zobrazilo nové nastavení konfigurace.

    :::image type="content" source="media/quickstarts/aspnet-core-feature-flag-local-after.png" alt-text="Místní aplikace pro rychlý Start po změně" border="true":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nové úložiště konfigurace aplikace a použili ho ke správě funkcí v ASP.NET Core webové aplikaci přes [knihovny pro správu funkcí](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration).

* Přečtěte si další informace o [správě funkcí](./concept-feature-management.md).
* [Správa příznaků funkcí](./manage-feature-flags.md).
* [Používání příznaků funkcí v aplikaci ASP.NET Core](./use-feature-flags-dotnet-core.md).
* [Použití dynamické konfigurace v aplikaci ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md)
---
title: Rychlý start pro přidání příznaky funkcí pro ASP.NET Core | Dokumentace Microsoftu
description: Rychlý start pro přidání příznaky funkcí pro aplikace ASP.NET Core a jejich správě v konfiguraci aplikací Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: 38b404ec10fb7b66b5e276665b0c9047d0576c15
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798390"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Rychlý start: Přidání příznaků funkcí do aplikace ASP.NET Core

Je-li povolit funkce správy v ASP.NET Core, připojení aplikace k konfiguraci aplikací Azure. Můžete použít tuto spravovanou službu ukládat všechny příznaky funkcí a centrálně řídit jejich stavů. Tento rychlý start ukazuje, jak začlenit konfigurace aplikace do ASP.NET Core webové aplikace k vytvoření začátku do konce provádění funkce správy.

Knihovny .NET Core funkce správy rozšíření rozhraní s podporou příznak komplexní funkce. Tyto knihovny jsou postavené na konfigurační systém .NET Core. Bez problémů integrují se konfigurace aplikace prostřednictvím konfigurace zprostředkovatele .NET Core.

Provést kroky v tomto rychlém startu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) skvělou možnost je k dispozici ve Windows, macOS a Linux platformy.

## <a name="prerequisites"></a>Požadavky

To provedete v tomto rychlém startu, nainstalujete [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Vytvoření konfigurace aplikace úložiště

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **funkce správce** >  **+ vytvořit** přidáte následující příznaky funkcí:

    | Klíč | Stav |
    |---|---|
    | Beta | Vypnuto |

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

Můžete použít [rozhraní příkazového řádku .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) vytvořte nový projekt webové aplikace ASP.NET Core MVC. Výhodou použití rozhraní příkazového řádku .NET Core místo sady Visual Studio je, že rozhraní příkazového řádku .NET Core je dostupná ve Windows, macOS a Linux platformy.

1. Vytvořte novou složku pro váš projekt. Pro tento rychlý start, pojmenujte ho *TestFeatureFlags*.

1. V nové složce spuštěním následujícího příkazu vytvořte nový projekt webové aplikace ASP.NET Core MVC:

   ```    
   dotnet new mvc
   ```

## <a name="add-secret-manager"></a>Add Secret Manager

Přidat [nástroj tajný klíč správce](https://docs.microsoft.com/aspnet/core/security/app-secrets) do projektu. Tajný klíč správce nástroj ukládá citlivá data při vývojových pracích vně vašeho projektu stromu. Tento přístup pomáhá zabránit náhodnému sdílení tajných kódů aplikace ve zdrojovém kódu.

1. Otevřít *.csproj* souboru.
1. Přidat `UserSecretsId` elementu, jak je znázorněno v následujícím příkladu a nahraďte její hodnotu s vlastním, což je obvykle identifikátor GUID:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
        <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
    </ItemGroup>

    </Project>
    ```

1. Uložte soubor.

## <a name="connect-to-an-app-configuration-store"></a>Připojit k úložišti konfigurace aplikace

1. Přidat odkaz `Microsoft.Azure.AppConfiguration.AspNetCore` balíček NuGet spuštěním následujícího příkazu:

    ```
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-009200001-7
    ```

1. Spusťte následující příkaz k obnovení balíčků pro váš projekt:

    ```
    dotnet restore
    ```

1. Přidání tajného kódu s názvem **ConnectionStrings:AppConfig** správci tajný klíč.

    Tento tajný kód obsahuje připojovací řetězec pro přístup k úložišti konfigurace aplikace. Nahraďte `<your_connection_string>` hodnotu v následujícím příkazu s připojovacím řetězcem pro vaše konfigurace aplikace pro store.

    Tento příkaz se musí spustit ve stejném adresáři jako soubor *.csproj*.

    ```
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Tajný klíč správce pouze pro testování webové aplikace místně. Při nasazení aplikace do [služby Azure App Service](https://azure.microsoft.com/services/app-service), například pomocí aplikace nastavení s názvem **připojovací řetězce** ve službě App Service, namísto použití Správce tajný kód k uložení připojovacího řetězce.

    Tento tajný kód pomocí rozhraní API pro konfiguraci aplikace přístupná. Dvojtečka (:) lze použít v názvu konfigurace pomocí rozhraní API pro konfiguraci aplikace na všech podporovaných platformách. Zobrazit [konfigurace podle prostředí](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. Otevřít *Program.cs*a přidejte odkaz na poskytovatele konfigurace pro aplikace .NET Core:

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Aktualizace `CreateWebHostBuilder` používat konfiguraci aplikací pomocí volání metody `config.AddAzureAppConfiguration()` metody.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .UseFeatureFlags();
                });
            })
            .UseStartup<Startup>();
    ```

1. Otevřít *Startup.cs*a přidejte odkazy pro správce funkcí .NET Core:

    ```csharp
    using Microsoft.FeatureManagement.AspNetCore;
    ```

1. Aktualizace `ConfigureServices` způsob, jak přidat příznak funkce podporuje prostřednictvím volání `services.AddFeatureManagement()` metoda. Volitelně může obsahovat libovolný filtr pro použití s příznaky funkcí voláním `services.AddFeatureFilter<FilterType>()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
    ```

1. Aktualizace `Configure` způsob, jak přidat middleware pro povolení funkce hodnot příznaku se aktualizují v opakovaných intervalech při ASP.NET Core webová aplikace i nadále přijímat požadavky.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```

1. Přidat *MyFeatureFlags.cs* souboru:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Přidat *BetaController.cs* k *řadiče* adresáře:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager)
            {
                _featureManager = featureManager;
            }

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index()
            {
                return View();
            }
        }
    }
    ```

1. Otevřít *_ViewImports.cshtml* v *zobrazení* adresáře, a přidejte pomocné rutiny značky manager funkce:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Otevřít *_Layout.cshtml* v *zobrazení*\\*Shared* adresáře a nahradit `<nav>` čárového kódu v rámci `<body>`  >  `<header>` následujícím kódem:

    ```html
    <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
        <div class="container">
            <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">TestFeatureFlags</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target=".navbar-collapse" aria-controls="navbarSupportedContent"
            aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="navbar-collapse collapse d-sm-inline-flex flex-sm-row-reverse">
                <ul class="navbar-nav flex-grow-1">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Index">Home</a>
                    </li>
                    <feature name="Beta">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Beta" asp-action="Index">Beta</a>
                    </li>
                    </feature>
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    ```

1. Vytvoření *Beta* adresáře v části *zobrazení* a přidejte *Index.cshtml* do ní:

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Sestavte a spusťte aplikaci místně

1. Pokud chcete vytvořit aplikaci pomocí rozhraní příkazového řádku .NET Core, spusťte následující příkaz v příkazovém prostředí:

    ```
    dotnet build
    ```

1. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění webové aplikace:

    ```
    dotnet run
    ```

1. Otevřete okno prohlížeče a přejděte na `https://localhost:5001`, což je výchozí adresa URL pro webové aplikace hostované místně.

    ![Místní spuštění aplikace rychlý start](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **všechny prostředky**a vyberte instanci konfigurace aplikace pro store, kterou jste vytvořili v tomto rychlém startu.

1. Vyberte **funkce správce**a změní stav **Beta** klíč **na**:

    | Klíč | Stav |
    |---|---|
    | Beta | Zapnuto |

1. Aktualizujte stránku v prohlížeči zobrazíte nové nastavení konfigurace.

    ![Místní spuštění aplikace rychlý start](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili novým úložištěm konfigurace aplikace a použít ho ke správě funkce ve webové aplikaci ASP.NET Core pomocí [knihovny funkce správy](https://go.microsoft.com/fwlink/?linkid=2074664).

- Další informace o [funkce správy](./concept-feature-management.md).
- [Spravovat příznaky funkcí](./manage-feature-flags.md).
- [Použití příznaků funkcí v aplikaci ASP.NET Core](./use-feature-flags-dotnet-core.md).

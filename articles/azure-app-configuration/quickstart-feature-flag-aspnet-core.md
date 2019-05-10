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
ms.openlocfilehash: 95f702b1d85dc8fe22b1800df3f7b0ebc987bee5
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412382"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Rychlý start: Přidání příznaků funkcí do aplikace ASP.NET Core

Připojení aplikace k konfigurace aplikace pro Azure můžete povolit funkce správy v ASP.NET Core. Můžete použít tuto spravovanou službu ukládat všechny příznaky funkcí a centrálně řídit jejich stavů. V tomto rychlém startu se dozvíte, jak tuto službu začlenit do webové aplikace ASP.NET Core vytvořit začátku do konce provádění funkce správy.

Knihovny .NET Core funkce správy rozšíření rozhraní s podporou příznak komplexní funkce. Tyto šablony jsou sestaveny na konfigurační systém .NET Core. Bez problémů integrují se konfigurace aplikace prostřednictvím konfigurace zprostředkovatele .NET Core.

Provést kroky v tomto rychlém startu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) skvělou možnost je k dispozici ve Windows, macOS a Linux platformy.

## <a name="prerequisites"></a>Požadavky

To provedete v tomto rychlém startu, nainstalujete [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **funkce správce** > **+ vytvořit** přidáte následující příznaky funkcí:

    | Klíč | Stav |
    |---|---|
    | Betaverze | Vypnuto |

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

Můžete použít [rozhraní příkazového řádku .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) vytvořte nový projekt webové aplikace ASP.NET Core MVC. Výhodou použití rozhraní příkazového řádku .NET Core v sadě Visual Studio je, že je k dispozici ve Windows, macOS a Linux platformy.

1. Vytvořte novou složku pro váš projekt. Pro tento rychlý start, pojmenujte ho *TestFeatureFlags*.

2. V nové složce spuštěním následujícího příkazu vytvořte nový projekt webové aplikace ASP.NET Core MVC:

        dotnet new mvc

## <a name="add-secret-manager"></a>Add Secret Manager

Přidat [nástroj tajný klíč správce](https://docs.microsoft.com/aspnet/core/security/app-secrets) do projektu. Nástroj Secret Manager ukládá citlivá data související s vývojem mimo strom vašeho projektu. Tento přístup pomáhá zabránit náhodnému sdílení tajných kódů aplikace ve zdrojovém kódu.

- Otevřít *.csproj* souboru. Přidat `UserSecretsId` elementu, jak je znázorněno zde a nahraďte její hodnotu s vlastním, což je obvykle identifikátor GUID. Uložte soubor.

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

## <a name="connect-to-an-app-configuration-store"></a>Připojit k úložišti konfigurace aplikace

1. Přidat odkazy `Microsoft.Extensions.Configuration.AzureAppConfiguration` a `Microsoft.FeatureManagement` balíčky NuGet spuštěním následujících příkazů:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 1.0.0-preview-008520001

        dotnet add package Microsoft.FeatureManagement.AspNetCore --version 1.0.0-preview-008560001-910

2. Spusťte následující příkaz k obnovení balíčků pro váš projekt:

        dotnet restore

3. Přidání tajného kódu s názvem *ConnectionStrings:AppConfig* správci tajný klíč.

    Tento tajný kód obsahuje připojovací řetězec pro přístup k úložišti konfigurace vaší aplikace. Hodnota v následujícím příkazu nahraďte připojovacím řetězcem pro vaše aplikace úložiště konfigurace.

    Tento příkaz se musí spustit ve stejném adresáři jako soubor *.csproj*.

        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>

    Tajný klíč správce slouží pouze k otestování webové aplikace místně. Když je aplikace nasazená na [služby Azure App Service](https://azure.microsoft.com/services/app-service/web), například pomocí nastavení aplikace **připojovací řetězce** ve službě App Service místo pomocí Správce tajný kód k uložení připojovacího řetězce.

    Tento tajný kód je přistupováno pomocí rozhraní API konfigurace. Dvojtečka (:) funguje v názvu konfigurace s konfigurací rozhraní API na všech podporovaných platformách. Zobrazit [konfigurace podle prostředí](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Otevřít *Program.cs*a přidejte odkaz na poskytovatele konfigurace pro aplikace .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

5. Aktualizace `CreateWebHostBuilder` používat konfiguraci aplikací pomocí volání metody `config.AddAzureAppConfiguration()` metody.

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

6. Otevřít *Startup.cs*a přidejte odkazy pro správce funkcí .NET Core.

    ```csharp
    using Microsoft.FeatureManagement.AspNetCore;
    ```

7. Aktualizace `ConfigureServices` způsob, jak přidat příznak funkce podporují voláním `services.AddFeatureManagement()` metoda a volitelně obsahovat libovolný filtr pro použití s příznaky funkcí voláním `services.AddFeatureFilter<FilterType>()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
    ```

8. Přidat *MyFeatureFlags.cs* souboru.

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

9. Přidat *BetaController.cs* do adresáře řadiče:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement.AspNetCore;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager)
            {
                _featureManager = featureManager;
            }

            [Feature(MyFeatureFlags.Beta)]
            public IActionResult Index()
            {
                return View();
            }
        }
    }
    ```

10. Otevřít *_ViewImports.cshtml* v adresáři zobrazení a přidání pomocné rutiny značky manager funkce:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

11. Otevřít *_Layout.cshtml* v zobrazeních > sdílený adresář a nahradit `<nav>` panelu v části `<body>`  >  `<header>` následujícím kódem:

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

12. Vytvořte adresář beta verze v zobrazeních a přidejte *Index.cshtml* do ní:

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

        dotnet build

2. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění webové aplikace:

        dotnet run

3. Otevřete okno prohlížeče a přejděte na `https://localhost:5001`, což je výchozí adresa URL pro webové aplikace hostované místně.

    ![Místní spuštění aplikace rychlý start](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

4. Přihlaste se k webu [Azure Portal](https://aka.ms/azconfig/portal). Vyberte **všechny prostředky**a vyberte instance aplikace, který konfigurace úložiště, který jste vytvořili v tomto rychlém startu.

5. Vyberte **funkce správce**a změňte hodnotu vlastnosti *Beta* k *na*:

    | Klíč | Stav |
    |---|---|
    | Betaverze | Zapnuté |

6. Aktualizujte stránku v prohlížeči zobrazíte nové nastavení konfigurace.

    ![Místní spuštění aplikace rychlý start](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili novým úložištěm konfigurace aplikace a použít ho ke správě funkce ve webové aplikaci ASP.NET Core pomocí [knihovny funkce správy](https://go.microsoft.com/fwlink/?linkid=2074664).

* Další informace o [funkcí správy](./concept-feature-management.md)
* [Správa příznaků funkcí](./manage-feature-flags.md)
* [Použití příznaků funkcí v aplikaci ASP.NET Core](./use-feature-flags-dotnet-core.md)

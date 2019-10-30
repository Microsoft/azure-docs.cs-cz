---
title: Rychlý Start pro přidání příznaků funkcí do ASP.NET Core | Microsoft Docs
description: Rychlý Start pro přidání příznaků funkcí do ASP.NET Core aplikací a jejich správu v konfiguraci aplikací Azure
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
ms.openlocfilehash: c4faa29e092c7cbb550bca1daa87ce369bf03a14
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73099529"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Rychlý Start: Přidání příznaků funkcí do aplikace ASP.NET Core

V tomto rychlém startu zařadíte do webové aplikace ASP.NET Core službu Azure App Configuration, abyste vytvořili ucelenou implementaci správy funkcí. Službu konfigurace aplikací můžete použít k centrálnímu ukládání všech příznaků funkcí a řízení jejich stavů. 

Knihovny pro správu funkcí .NET Core přesahují rámec s kompletní podporou příznaků funkcí. Tyto knihovny jsou postaveny na systému konfigurace .NET Core. Bez problémů se integrují s konfigurací aplikací prostřednictvím poskytovatele konfigurace .NET Core.

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [.NET Core SDK](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **správce funkcí** >  **+ Přidat** a přidejte následující příznaky funkcí:

    | Klíč | Stav |
    |---|---|
    | Beta | Vypnuto |

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

K vytvoření nového projektu webové aplikace ASP.NET Core MVC použijete [rozhraní příkazového řádku .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) . Výhodou použití .NET Core CLI místo sady Visual Studio je, že .NET Core CLI je k dispozici v rámci platforem Windows, macOS a Linux.

1. Vytvořte novou složku pro váš projekt. V tomto rychlém startu pojmenujte ho *TestFeatureFlags*.

1. V nové složce spusťte následující příkaz pro vytvoření nového projektu webové aplikace ASP.NET Core MVC:

   ```    
   dotnet new mvc --no-https
   ```

## <a name="add-secret-manager"></a>Přidat správce tajných klíčů

Přidejte do projektu [Nástroj Správce tajných klíčů](https://docs.microsoft.com/aspnet/core/security/app-secrets) . Nástroj Správce tajných klíčů ukládá citlivá data pro vývojovou práci mimo strom projektu. Tento přístup pomáhá zabránit náhodnému sdílení tajných kódů aplikace ve zdrojovém kódu.

1. Otevřete soubor *. csproj* .
1. Přidejte prvek `UserSecretsId`, jak je znázorněno v následujícím příkladu, a nahraďte jeho hodnotu vlastním, což je obvykle identifikátor GUID:

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

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikace

1. Přidejte odkaz na `Microsoft.Azure.AppConfiguration.AspNetCore` a balíčky NuGet `Microsoft.FeatureManagement.AspNetCore` spuštěním následujících příkazů:

    ```
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-009470001-12
    dotnet add package Microsoft.FeatureManagement.AspNetCore --version 1.0.0-preview-009000001-1251
    ```

1. Spusťte následující příkaz pro obnovení balíčků pro váš projekt:

    ```
    dotnet restore
    ```

1. Přidejte tajný klíč s názvem **connectionStrings: appconfig** do správce tajných klíčů.

    Tento tajný klíč obsahuje připojovací řetězec pro přístup k úložišti konfigurace aplikace. Hodnotu `<your_connection_string>` v následujícím příkazu nahraďte připojovacím řetězcem pro úložiště konfigurace aplikace.

    Tento příkaz se musí spustit ve stejném adresáři jako soubor *.csproj*.

    ```
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Správce tajného kódu se používá jenom k místnímu testování webové aplikace. Když nasadíte aplikaci do [Azure App Service](https://azure.microsoft.com/services/app-service)například použijete nastavení aplikace s názvem **připojovací řetězce** v App Service namísto použití Správce tajných klíčů k uložení připojovacího řetězce.

    K tomuto tajnému kódu můžete přistupovat pomocí rozhraní API konfigurace aplikace. Dvojtečka (:) funguje v názvu konfigurace s rozhraním API konfigurace aplikace na všech podporovaných platformách. Viz [Konfigurace podle prostředí](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. Otevřete *program.cs*a přidejte odkaz na poskytovatele konfigurace aplikace .NET Core:

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Aktualizujte metodu `CreateWebHostBuilder` pro použití konfigurace aplikace voláním metody `config.AddAzureAppConfiguration()`.
    
    > [!IMPORTANT]
    > `CreateHostBuilder` nahrazuje `CreateWebHostBuilder` v .NET Core 3,0.  Vyberte správnou syntaxi na základě vašeho prostředí.

    ### <a name="update-createwebhostbuilder-for-net-core-2x"></a>Aktualizace `CreateWebHostBuilder` pro .NET Core 2. x

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

    ### <a name="update-createhostbuilder-for-net-core-3x"></a>Aktualizace `CreateHostBuilder` pro .NET Core 3. x

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
        })
        .UseStartup<Startup>());
    ```


1. Otevřete *Startup.cs*a přidejte odkazy na správce funkcí .NET Core:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Aktualizujte metodu `ConfigureServices` pro přidání podpory příznaků funkcí voláním metody `services.AddFeatureManagement()`. Volitelně můžete zahrnout jakýkoli filtr, který se má použít s příznaky funkcí voláním `services.AddFeatureFilter<FilterType>()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
    ```

1. Aktualizujte metodu `Configure`, aby se přidal middleware, aby bylo možné aktualizovat hodnoty příznaků funkcí v opakovaných intervalech, zatímco ASP.NET Core webová aplikace nadále přijímá požadavky.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```

1. Přidat soubor *MyFeatureFlags.cs* :

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Přidejte *BetaController.cs* do adresáře *Controllers* :

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

1. Otevřete *_ViewImports. cshtml* v adresáři *zobrazení* a přidejte pomocníka značek správce funkcí:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Otevřete *_Layout. cshtml* v *zobrazeních*\\*sdíleném* adresáři a nahraďte `<nav>` čárový kód v `<body>` > `<header>` s následujícím kódem:

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

1. V části *zobrazení* vytvořte adresář *beta* a přidejte do něj *index. cshtml* :

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Pokud chcete aplikaci vytvořit pomocí .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

    ```
    dotnet build
    ```

1. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění webové aplikace:

    ```
    dotnet run
    ```

1. Otevřete okno prohlížeče a vyberte `https://localhost:5001`, což je výchozí adresa URL pro webovou aplikaci hostovanou místně.

    ![Spuštění aplikace pro rychlý Start – místní](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Přihlaste se na web [Azure Portal](https://portal.azure.com). Vyberte **všechny prostředky**a vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v rychlém startu.

1. Vyberte **správce funkcí**a změňte stav **beta** klíče na **zapnuto**:

    | Klíč | Stav |
    |---|---|
    | Beta | Zapnuto |

1. Restartujte aplikaci tak, že přepnete zpět na příkazový řádek a stisknete `Ctrl-C`, čímž zrušíte spuštěný proces `dotnet` a pak znovu spustíte `dotnet run`.

1. Aktualizujte stránku prohlížeče, aby se zobrazilo nové nastavení konfigurace.

    ![Spuštění aplikace pro rychlý Start – místní](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nové úložiště konfigurace aplikace a použili ho ke správě funkcí v ASP.NET Core webové aplikaci přes [knihovny pro správu funkcí](https://go.microsoft.com/fwlink/?linkid=2074664).

- Přečtěte si další informace o [správě funkcí](./concept-feature-management.md).
- [Správa příznaků funkcí](./manage-feature-flags.md).
- [Používání příznaků funkcí v aplikaci ASP.NET Core](./use-feature-flags-dotnet-core.md).
- [Použití dynamické konfigurace v aplikaci ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md)

---
title: Úvodní příručka pro přidání příznaků funkcí do ASP.NET jádra
description: Přidání příznaků funkcí pro ASP.NET aplikace Core a jejich správu pomocí konfigurace aplikací Azure
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: lcozzens
ms.openlocfilehash: b3579d12981e2b0add916a280bac7b4f9392d8ba
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803139"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Úvodní příručka: Přidání příznaků funkcí do aplikace ASP.NET Core

V tomto rychlém startu vytvoříte komplexní implementaci správy funkcí v aplikaci ASP.NET Core pomocí konfigurace aplikací Azure. Službu Konfigurace aplikací použijete k centrálnímu ukládání všech příznaků funkcí a řízení jejich stavů. 

Knihovny správy základních funkcí .NET rozšiřují architekturu o komplexní podporu příznaku funkce. Tyto knihovny jsou postaveny na konfiguračním systému .NET Core. Hladce se integrují s konfigurací aplikací prostřednictvím poskytovatele konfigurace .NET Core.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
- [Sada SDK jádra .NET](https://dotnet.microsoft.com/download).

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikací

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **Správce** > funkcí **+Přidat,** `Beta`chcete-li přidat příznak prvku s názvem .

    > [!div class="mx-imgBorder"]
    > ![Povolit příznak funkce s názvem Beta](media/add-beta-feature-flag.png)

    Prozatím `label` nenechte být nedefinovaní. Vyberte **Použít,** chcete-li uložit příznak nové ho prvku.

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

Pomocí [rozhraní příkazového řádku .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) vytvořte nový ASP.NET projektu webové aplikace Core MVC. Výhodou použití rozhraní PŘÍKAZOVÉHO PŘÍKAZU .NET Core namísto visual studia je, že rozhraní PŘÍKAZOVÉHO NASTAVENÍ jádra .NET je k dispozici na platformách Windows, macOS a Linux.

1. Vytvořte novou složku pro váš projekt. Pro tento rychlý start jej pojmenujte *TestFeatureFlags*.

1. V nové složce spusťte následující příkaz a vytvořte nový ASP.NET projektu webové aplikace Core MVC:

   ```    
   dotnet new mvc --no-https
   ```

## <a name="add-secret-manager"></a>Přidat správce tajných služeb

Chcete-li použít Správce `UserSecretsId` tajných barev, přidejte prvek do souboru *.csproj.*

1. Otevřete soubor *.csproj.*

1.  Přidejte `UserSecretsId` prvek, jak je znázorněno zde. Můžete použít stejný identifikátor GUID nebo můžete tuto hodnotu nahradit vlastní hodnotou.

    > [!IMPORTANT]
    > `CreateHostBuilder`nahrazuje `CreateWebHostBuilder` v rozhraní .NET Core 3.0.  Vyberte správnou syntaxi na základě vašeho prostředí.

    #### <a name="net-core-2x"></a>[.NET Jádro 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Jádro 3.x](#tab/core3x)

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

        <PropertyGroup>
            <TargetFramework>netcoreapp3.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>

    </Project>
    ```
    ---

1. Uložte soubor *.csproj.*

Nástroj Secret Manager ukládá citlivá data související s vývojem mimo strom vašeho projektu. Tento přístup pomáhá zabránit náhodnému sdílení tajných kódů aplikace ve zdrojovém kódu.

> [!TIP]
> Další informace o Tajném správci najdete [v tématu Bezpečné ukládání tajných kódů aplikací ve vývoji v ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets).

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikací

1. Přidejte odkaz `Microsoft.Azure.AppConfiguration.AspNetCore` na `Microsoft.FeatureManagement.AspNetCore` balíčky NuGet a spuštěním následujících příkazů:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. Chcete-li obnovit balíčky pro projekt, spusťte následující příkaz:

    ```dotnetcli
    dotnet restore
    ```

1. Přidejte tajný klíč s názvem **ConnectionStrings:AppConfig** do Správce tajných služeb.

    Tento tajný klíč obsahuje připojovací řetězec pro přístup k úložišti konfigurace aplikace. Nahraďte hodnotu `<your_connection_string>` v následujícím příkazu připojovacím řetězcem pro úložiště konfigurace aplikací. Připojovací řetězec najdete v části **Přístupové klíče** na webu Azure Portal.

    Tento příkaz se musí spustit ve stejném adresáři jako soubor *.csproj*.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    Správce tajných barev slouží pouze k místnímu testování webové aplikace. Když nasadíte aplikaci do [služby Azure App Service](https://azure.microsoft.com/services/app-service), například použijete nastavení aplikace s názvem **Připojovací řetězce** v app service namísto použití Správce tajných služeb k uložení připojovacího řetězce.

    K tomuto tajnému klíči můžete přistupovat pomocí rozhraní API pro konfiguraci aplikací. Dvojtečka (:) funguje v názvu konfigurace s rozhraním API pro konfiguraci aplikací na všech podporovaných platformách. Viz [Konfigurace podle prostředí](https://docs.microsoft.com/aspnet/core/fundamentals/configuration).

1. V *Program.cs*aktualizujte metodu `CreateWebHostBuilder` pro použití `config.AddAzureAppConfiguration()` konfigurace aplikace voláním metody.

    > [!IMPORTANT]
    > `CreateHostBuilder`nahrazuje `CreateWebHostBuilder` v rozhraní .NET Core 3.0.  Vyberte správnou syntaxi na základě vašeho prostředí.

    #### <a name="net-core-2x"></a>[.NET Jádro 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Jádro 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options => {
                options.Connect(settings["ConnectionStrings:AppConfig"])
                    .UseFeatureFlags();
            });
        })
        .UseStartup<Startup>());
    ```
    ---

1. Otevřete *Startup.cs*a přidejte odkazy do správce funkcí jádra .NET:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Aktualizujte `ConfigureServices` metodu a přidejte `services.AddFeatureManagement()` podporu příznaku funkce voláním metody. Volitelně můžete zahrnout libovolný filtr, který bude `services.AddFeatureFilter<FilterType>()`použit s příznaky funkcí voláním :

    #### <a name="net-core-2x"></a>[.NET Jádro 2.x](#tab/core2x)
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);        
        services.AddFeatureManagement();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Jádro 3.x](#tab/core3x)
    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }

    ---

1. Update the `Configure` method to add a middleware to allow the feature flag values to be refreshed at a recurring interval while the ASP.NET Core web app continues to receive requests.

    #### [.NET Core 2.x](#tab/core2x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }

            app.UseStaticFiles();
            app.UseCookiePolicy();
            app.UseAzureAppConfiguration();
            app.UseMvc(routes =>
            {
                routes.MapRoute(
                    name: "default",
                    template: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Jádro 3.x](#tab/core3x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }
            app.UseStaticFiles();
            app.UseRouting();
            app.UseAuthorization();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
            app.UseAzureAppConfiguration();
    }
    ```
    ---

1. Přidejte *soubor MyFeatureFlags.cs:*

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Přidejte *BetaController.cs* do *adresáře Řadiče:*

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

1. Otevřete *_ViewImports.cshtml* v adresáři *Zobrazení* a přidejte pomocnou stránku značky správce funkcí:

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. Otevřete *_Layout.cshtml* v adresáři *Zobrazení*\\*sdílených* a nahraďte `<nav>` pod ním `<body>`  >  `<header>` čárový kód následujícím kódem:

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

1. Vytvořte adresář *beta v* části *Zobrazení* a přidejte do něj *index.cshtml:*

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Sestavení a spuštění aplikace místně

1. Chcete-li vytvořit aplikaci pomocí rozhraní PŘÍKAZU .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

    ```
    dotnet build
    ```

1. Po úspěšném dokončení sestavení spusťte následující příkaz a spusťte webovou aplikaci místně:

    ```
    dotnet run
    ```

1. Otevřete okno prohlížeče a `https://localhost:5000`přejděte na stránku , což je výchozí adresa URL webové aplikace hostované místně.
    Pokud pracujete v prostředí Azure Cloud Shell, vyberte tlačítko *Web Preview* následované *tlačítkem Configure*.  Po zobrazení výzvy vyberte port 5000.

    ![Vyhledání tlačítka Náhled na webu](./media/quickstarts/cloud-shell-web-preview.png)

    Váš prohlížeč by měl zobrazit stránku podobnou obrázku níže.
    ![Spuštění aplikace QuickStart místní](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Všechny prostředky**a vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v rychlém startu.

1. Vyberte **Správce funkcí**a změňte stav klíče **Beta** **na Zapnuto**.

1. Vraťte se do příkazového `dotnet` řádku `Ctrl-C`a zrušte spuštěný proces stisknutím klávesy .  Restartujte aplikaci pomocí aplikace `dotnet run`.

1. Aktualizujte stránku prohlížeče, abyste viděli nové nastavení konfigurace.

    ![Spuštění aplikace QuickStart místní](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nový obchod SKonfiguraci aplikací a použili jste ho ke správě funkcí ve webové aplikaci ASP.NET Core prostřednictvím [knihoven správy funkcí](https://go.microsoft.com/fwlink/?linkid=2074664).

- Další informace o [správě funkcí](./concept-feature-management.md).
- [Spravovat příznaky funkcí](./manage-feature-flags.md).
- [Používejte příznaky funkcí v aplikaci ASP.NET Core](./use-feature-flags-dotnet-core.md).
- [Použití dynamické konfigurace v aplikaci ASP.NET Core](./enable-dynamic-configuration-aspnet-core.md)

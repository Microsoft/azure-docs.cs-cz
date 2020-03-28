---
title: 'Kurz: Použití dynamické konfigurace konfigurace konfigurace aplikace v ASP.NET jádra'
titleSuffix: Azure App Configuration
description: V tomto kurzu se dozvíte, jak dynamicky aktualizovat konfigurační data pro ASP.NET základní aplikace
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: e9df6d2e7a8219d16e7b60f7c3b8d826a87e6110
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348857"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Kurz: Použití dynamické konfigurace v aplikaci ASP.NET Core

ASP.NET Core má připojitelný konfigurační systém, který dokáže číst konfigurační data z různých zdrojů. Může dynamicky zpracovávat změny, aniž by došlo k restartování aplikace. ASP.NET Core podporuje vazbu nastavení konfigurace na silné typy tříd .NET. Vstřikuje je do kódu `IOptions<T>` pomocí různých vzorů. Jeden z těchto vzorů, konkrétně `IOptionsSnapshot<T>`, automaticky znovu načte konfiguraci aplikace při změně podkladových dat. Můžete vložit `IOptionsSnapshot<T>` do řadičů ve vaší aplikaci pro přístup k nejnovější konfiguraci uložené v Azure App Configuration.

Můžete také nastavit konfiguraci aplikace ASP.NET základní klientskou knihovnu, aby se dynamicky aktualizovala sada nastavení konfigurace pomocí middlewaru. Nastavení konfigurace se aktualizují s úložištěm konfigurace pokaždé, dokud webová aplikace přijímá požadavky.

Konfigurace aplikace automaticky ukládá každé nastavení do mezipaměti, aby se zabránilo příliš mnoha voláním do úložiště konfigurace. Operace aktualizace čeká, až vyprší platnost hodnoty nastavení uložené v mezipaměti, aby bylo toto nastavení aktualizováno, a to i v případě, že se jeho hodnota změní v úložišti konfigurace. Výchozí doba vypršení platnosti mezipaměti je 30 sekund. V případě potřeby můžete tuto dobu vypršení platnosti přepsat.

Tento kurz ukazuje, jak můžete implementovat dynamické aktualizace konfigurace v kódu. Staví na webové aplikaci zavedené v rychlých startech. Než budete pokračovat, nejprve [dokončete vytvoření aplikace ASP.NET Jádro pomocí konfigurace aplikace.](./quickstart-aspnet-core-app.md)

Můžete použít libovolný editor kódu k tomu kroky v tomto kurzu. [Visual Studio Code](https://code.visualstudio.com/) je vynikající možnost, která je k dispozici na platformách Windows, macOS a Linux.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte aplikaci tak, aby aktualizovala svou konfiguraci v reakci na změny v úložišti konfigurace aplikací.
> * Vstříkněte nejnovější konfiguraci do řadičů aplikace.

## <a name="prerequisites"></a>Požadavky

Chcete-li tento kurz provést, nainstalujte sadu [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Než budete pokračovat, nejprve [dokončete vytvoření aplikace ASP.NET Jádro pomocí konfigurace aplikace.](./quickstart-aspnet-core-app.md)

## <a name="add-a-sentinel-key"></a>Přidání ověřovacího klíče

*Ověřovací klíč* je speciální klíč používaný k signalizaci při změně konfigurace. Aplikace sleduje změny pomocí ověřovacího klíče. Když je zjištěna změna, aktualizujete všechny hodnoty konfigurace. Tento přístup snižuje celkový počet požadavků provedených vaší aplikací na konfiguraci aplikace ve srovnání s monitorováním všech klíčů pro změny.

1. Na webu Azure Portal vyberte **Průzkumník konfigurace > vytvořit > hodnotu klíče**.

1. Do **položky Klíč**zadejte *TestApp:Settings:Sentinel*. Do **pole Hodnota**zadejte hodnotu 1. Ponechejte **typ popisku** a **obsahu** prázdný.

1. Vyberte **Použít**.

## <a name="reload-data-from-app-configuration"></a>Opětovné načtení dat z konfigurace aplikace

1. Přidejte odkaz `Microsoft.Azure.AppConfiguration.AspNetCore` na balíček NuGet spuštěním následujícího příkazu:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Otevřete *Program.cs*a `CreateWebHostBuilder` aktualizujte `config.AddAzureAppConfiguration()` metodu pro přidání metody.

    #### <a name="net-core-2x"></a>[.NET Jádro 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
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
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    ---

    Metoda `ConfigureRefresh` se používá k určení nastavení použitého k aktualizaci konfiguračních dat pomocí úložiště konfigurace aplikace při aktivaci operace aktualizace. Parametr `refreshAll` `Register` metody označuje, že všechny hodnoty konfigurace by měly být aktualizovány, pokud se změní klíč sentinel.

    `SetCacheExpiration` Metoda také přepíše výchozí dobu vypršení platnosti mezipaměti 30 sekund a místo toho určí čas 5 minut. To snižuje počet požadavků provedených na konfiguraci aplikace.

    > [!NOTE]
    > Pro účely testování můžete chtít snížit dobu vypršení platnosti mezipaměti.

    Chcete-li skutečně spustit operaci aktualizace, budete muset nakonfigurovat obnovovací middleware pro aplikaci, aby aktualizovala konfigurační data, když dojde ke změně. Uvidíte, jak to udělat v pozdějším kroku.

2. Přidejte *soubor Settings.cs,* který definuje `Settings` a implementuje novou třídu.

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. Otevřete *Startup.cs* `IServiceCollection.Configure<T>` a `ConfigureServices` použijte v metodě `Settings` vázat konfigurační data do třídy.

    #### <a name="net-core-2x"></a>[.NET Jádro 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Jádro 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
    }
    ```
    ---

4. Aktualizujte `Configure` metodu `UseAzureAppConfiguration` přidáním middlewaru, který umožní aktualizaci nastavení konfigurace registrované pro aktualizaci, zatímco ASP.NET webová aplikace Core nadále přijímá požadavky.


    #### <a name="net-core-2x"></a>[.NET Jádro 2.x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Jádro 3.x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    ---
    
    Middleware používá konfiguraci aktualizace `AddAzureAppConfiguration` zadanou `Program.cs` v metodě v aplikaci k aktivaci aktualizace pro každý požadavek přijatý ASP.NET webovou aplikací Core. Pro každý požadavek se aktivuje operace aktualizace a klientská knihovna zkontroluje, zda vypršela hodnota uložené v mezipaměti pro registrované nastavení konfigurace. Pokud vypršela její platnost, je obnovena.

## <a name="use-the-latest-configuration-data"></a>Použití nejnovějších konfiguračních dat

1. Otevřete *HomeController.cs* v adresáři Řadiče a `Microsoft.Extensions.Options` přidejte odkaz na balíček.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Aktualizujte `HomeController` třídu `Settings` přijímat prostřednictvím vkládání závislostí a využít jeho hodnoty.

    #### <a name="net-core-2x"></a>[.NET Jádro 2.x](#tab/core2x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Jádro 3.x](#tab/core3x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    ---



3. Otevřete *soubor Index.cshtml* v adresáři Zobrazení > domů a nahraďte jeho obsah následujícím skriptem:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"]px;
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Sestavení a spuštění aplikace místně

1. Chcete-li vytvořit aplikaci pomocí rozhraní PŘÍKAZU .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

        dotnet build

1. Po úspěšném dokončení sestavení spusťte následující příkaz a spusťte webovou aplikaci místně:

        dotnet run
1. Otevřete okno prohlížeče a přejděte na `dotnet run` adresu URL zobrazenou ve výstupu.

    ![Místní spuštění aplikace rychlého startu](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **Všechny prostředky**a vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v rychlém startu.

1. Vyberte **Průzkumník konfigurace**a aktualizujte hodnoty následujících klíčů:

    | Klíč | Hodnota |
    |---|---|
    | testapp:nastavení:barva na pozadí | green |
    | testapp:nastavení:barva písma | světleŠedá |
    | testapp:nastavení:zpráva | Data z Azure App Configuration – teď s živými aktualizacemi! |
    | testapp: nastavení: sentinel | 2 |

1. Aktualizujte stránku prohlížeče, abyste viděli nové nastavení konfigurace. Možná budete muset aktualizovat více než jednou, aby se změny projevily.

    ![Místní spuštění aktualizované aplikace rychlého startu](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili webové aplikaci ASP.NET Core dynamicky aktualizovat nastavení konfigurace z konfigurace aplikace. Chcete-li se dozvědět, jak používat identitu spravovanou Azure zjednodušit přístup ke konfiguraci aplikací, pokračujte dalším kurzem.

> [!div class="nextstepaction"]
> [Integrace spravované identity](./howto-integrate-azure-managed-service-identity.md)

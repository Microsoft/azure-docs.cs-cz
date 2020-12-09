---
title: 'Kurz: použití dynamické konfigurace konfigurace aplikace v ASP.NET Core'
titleSuffix: Azure App Configuration
description: V tomto kurzu se naučíte dynamicky aktualizovat konfigurační data pro ASP.NET Core aplikace.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/1/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 1fd495083f5f9be367dd0f125883b181e3bed27b
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930547"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Kurz: použití dynamické konfigurace v aplikaci ASP.NET Core

ASP.NET Core má připojitelná konfigurační systém, který může číst konfigurační data z nejrůznějších zdrojů. Může zpracovávat změny dynamicky, aniž by došlo k tomu, že by se aplikace restartovala. ASP.NET Core podporuje vazbu nastavení konfigurace na třídy .NET silného typu. Vloží je do kódu pomocí různých `IOptions<T>` vzorů. Jeden z těchto vzorů, konkrétně `IOptionsSnapshot<T>` , automaticky znovu načte konfiguraci aplikace, když se změní podkladová data. Můžete vložit `IOptionsSnapshot<T>` do řadičů v aplikaci, abyste měli přístup k nejnovější konfiguraci uložené v konfiguraci aplikace Azure.

Můžete také nastavit konfiguraci aplikace ASP.NET Core klientské knihovně pro dynamické obnovení sady nastavení konfigurace pomocí middlewaru. V případě, že webová aplikace přijímá požadavky, se nastavení konfigurace aktualizuje na úložiště konfigurace.

Konfigurace aplikace automaticky ukládá do mezipaměti každé nastavení, aby nedocházelo k příliš velkému počtu volání do úložiště konfigurace. Operace aktualizace počká, dokud nevyprší hodnota nastavení uložené v mezipaměti, a to i v případě, že se změní hodnota v úložišti konfigurace. Výchozí doba vypršení platnosti mezipaměti je 30 sekund. V případě potřeby můžete tento čas vypršení platnosti přepsat.

V tomto kurzu se dozvíte, jak můžete implementovat aktualizace dynamické konfigurace do kódu. Sestavuje se ve webové aplikaci představené v rychlých startech. Než budete pokračovat, dokončete nejprve [Vytvoření aplikace ASP.NET Core s konfigurací aplikace](./quickstart-aspnet-core-app.md) .

K provedení kroků v tomto kurzu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) je vynikající možnost, která je dostupná na platformách Windows, MacOS a Linux.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte svoji aplikaci tak, aby aktualizovala svou konfiguraci v reakci na změny v úložišti konfigurace aplikace.
> * Vloží nejnovější konfiguraci do řadičů vaší aplikace.

## <a name="prerequisites"></a>Předpoklady

K provedení tohoto kurzu nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Než budete pokračovat, dokončete nejprve [Vytvoření aplikace ASP.NET Core s konfigurací aplikace](./quickstart-aspnet-core-app.md) .

## <a name="add-a-sentinel-key"></a>Přidat klíč Sentinel

*Klíč Sentinel* je speciální klíč, který se používá k signalizaci, že se konfigurace změnila. Vaše aplikace sleduje změny v klíči Sentinel. Když se zjistí změna, aktualizují se všechny hodnoty konfigurace. Tento přístup snižuje celkový počet požadavků provedených vaší aplikací na konfiguraci aplikací v porovnání s monitorováním všech klíčů pro změny.

1. V Azure Portal vyberte možnost **Průzkumník konfigurace > vytvořit > klíč-hodnota**.
1. Jako **klíč** zadejte *TestApp: Settings: Sentinel*. Jako **hodnotu** zadejte 1. Ponechte **popisek** a **typ obsahu** prázdné.
1. Vyberte **Použít**.

> [!NOTE]
> Pokud nepoužíváte klíč Sentinel, je nutné ručně zaregistrovat každý klíč, který chcete sledovat.

## <a name="reload-data-from-app-configuration"></a>Znovu načíst data z konfigurace aplikace

1. Přidejte odkaz na `Microsoft.Azure.AppConfiguration.AspNetCore` balíček NuGet spuštěním následujícího příkazu:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Otevřete *program.cs* a aktualizujte `CreateWebHostBuilder` metodu pro přidání `config.AddAzureAppConfiguration()` metody.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

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

    `ConfigureRefresh`Metoda se používá k určení nastavení, která se použijí k aktualizaci konfiguračních dat pomocí úložiště konfigurace aplikace při aktivaci operace aktualizace. `refreshAll`Parametr `Register` metody označuje, že všechny konfigurační hodnoty by měly být aktualizovány, pokud se změní klíč Sentinel.

    `SetCacheExpiration`Metoda také přepíše výchozí dobu vypršení platnosti mezipaměti 30 sekund, přičemž místo toho zadá čas 5 minut. Tím se sníží počet požadavků provedených v konfiguraci aplikace.

    > [!NOTE]
    > Pro účely testování možná budete chtít snížit dobu platnosti mezipaměti.

    Chcete-li ve skutečnosti aktivovat operaci aktualizace, bude nutné nakonfigurovat middleware aktualizace, aby aplikace aktualizovala konfigurační data, když dojde ke změně. V pozdějším kroku se dozvíte, jak to udělat.

2. Přidejte soubor *Settings.cs* , který definuje a implementuje novou `Settings` třídu.

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

3. Otevřete *Startup.cs* a pomocí `IServiceCollection.Configure<T>` `ConfigureServices` metody navažte konfigurační data ke `Settings` třídě.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
    }
    ```
    ---
    > [!Tip]
    > Další informace o vzoru možností při čtení hodnot konfigurace najdete v tématu [vzory možností v ASP.NET Core](/aspnet/core/fundamentals/configuration/options?view=aspnetcore-3.1).

4. Aktualizujte `Configure` metodu přidáním `UseAzureAppConfiguration` middlewaru, který umožní aktualizaci nastavení konfigurace zaregistrovaných pro aktualizaci, když ASP.NET Core webová aplikace nadále přijímá požadavky.


    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

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
    
    Middleware používá konfiguraci aktualizace zadanou v `AddAzureAppConfiguration` metodě v `Program.cs` k aktivaci aktualizace pro každý požadavek přijatý webovou aplikací ASP.NET Core. Pro každý požadavek se aktivuje operace aktualizace a Klientská knihovna zkontroluje, jestli vypršela platnost hodnoty uložené v mezipaměti pro zaregistrované nastavení konfigurace. Pokud platnost vypršela, aktualizuje se.

    > [!NOTE]
    > Pokud chcete zajistit, aby se konfigurace aktualizovala, přidejte middleware jako nejdříve do vašeho kanálu požadavků, aby se v aplikaci nepřidal jiný middleware.

## <a name="use-the-latest-configuration-data"></a>Použít nejnovější konfigurační data

1. Otevřete *HomeController.cs* v adresáři Controllers a přidejte odkaz na `Microsoft.Extensions.Options` balíček.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Aktualizujte `HomeController` třídu tak, aby se přijímala `Settings` prostřednictvím injektáže závislosti, a využijte její hodnoty.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

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



3. Otevřete *index. cshtml* v zobrazeních > domovském adresáři a nahraďte jeho obsah následujícím skriptem:

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

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Pokud chcete aplikaci vytvořit pomocí .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

    ```console
        dotnet build
    ```

1. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění webové aplikace:

    ```console
        dotnet run
    ```

1. Otevřete okno prohlížeče a použijte adresu URL zobrazenou ve `dotnet run` výstupu.

    ![Místní spuštění aplikace pro rychlý Start](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. Přihlaste se na web [Azure Portal](https://portal.azure.com). Vyberte **všechny prostředky** a vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v rychlém startu.

1. Vyberte **Průzkumník konfigurace** a aktualizujte hodnoty následujících klíčů:

    | Klíč | Hodnota |
    |---|---|
    | TestApp: nastavení: BackgroundColor | green |
    | TestApp: nastavení: FontColor | lightGray |
    | TestApp: nastavení: zpráva | Data z konfigurace aplikace Azure – teď s aktualizacemi za provozu! |
    | TestApp: nastavení: Sentinel | 2 |

1. Aktualizujte stránku prohlížeče, aby se zobrazilo nové nastavení konfigurace. Aby se změny projevily, možná budete muset aktualizovat více než jednou.

    ![Místní spuštění aktualizované aplikace pro rychlý Start](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili ASP.NET Core webové aplikaci, abyste mohli dynamicky aktualizovat nastavení konfigurace z konfigurace aplikace. Další informace o tom, jak pomocí identity spravované v Azure zjednodušit přístup ke konfiguraci aplikace, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Spravovaná integrace identit](./howto-integrate-azure-managed-service-identity.md)

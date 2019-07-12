---
title: Kurz pro používání v aplikaci ASP.NET Core dynamickou konfiguraci konfigurace aplikace pro Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak se dynamicky aktualizovat konfigurační data pro aplikace ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 78c64786f523aa424e8a9816e42db70e2a2997c2
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798456"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Kurz: Použít dynamické konfiguraci aplikace ASP.NET Core

ASP.NET Core je modulární konfigurace systému, který může číst konfigurační data z různých zdrojů. Dokáže zpracovat změny v reálném čase bez způsobení aplikací k restartování. ASP.NET Core podporuje vazbu nastavení konfigurace má být silného typu třídy rozhraní .NET. To vloží do svého kódu s použitím různých `IOptions<T>` vzory. Jedna z následujících vzorů, konkrétně `IOptionsSnapshot<T>`, automaticky znovu načte konfiguraci vaší aplikace, když se změní podkladová data. Můžete vložit `IOptionsSnapshot<T>` do řadiče ve vaší aplikaci přístup k nejnovější konfigurace uložené v konfiguraci aplikací Azure.

Můžete také nastavit klientské knihovně pro aplikace konfigurace ASP.NET Core aktualizovat sadu nastavení konfigurace dynamicky pomocí middleware. Za předpokladu, stále přijímat žádosti o webovou aplikaci, i nadále aktualizovat s úložištěm konfigurace nastavení konfigurace.

Aby bylo možné zachovat nastavení aktualizována a vyhnout se příliš mnoho volání do konfigurace úložiště, mezipaměť se používá pro každé nastavení. Dokud se hodnota uložená v mezipaměti nastavení vypršela platnost, operace aktualizace neaktualizuje hodnotu, i v případě, že je hodnota změněna v úložišti konfigurace. Výchozí doba vypršení platnosti pro každý požadavek je 30 sekund, ale lze přepsat, pokud je to nutné.

Tento kurz ukazuje, jak můžete implementovat konfigurace dynamické aktualizace ve vašem kódu. Sestaví ve webové aplikaci zavedený rychlých startech. Než budete pokračovat, dokončete [vytvoření aplikace ASP.NET Core s konfigurací aplikace](./quickstart-aspnet-core-app.md) první.

Provést kroky v tomto kurzu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) je skvělou možnost, která je k dispozici ve Windows, macOS a Linux platformy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení aplikace se aktualizovat jeho konfiguraci v reakci na změny v obchodě s aplikacemi konfigurace.
> * Vloží nejnovější konfiguraci v řadiči vaší aplikace.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu provedete instalaci [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Znovu načíst data z konfigurace aplikace

1. Otevřít *Program.cs*a aktualizovat `CreateWebHostBuilder` způsob, jak přidat `config.AddAzureAppConfiguration()` metody.

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
                               refresh.Register("TestApp:Settings:BackgroundColor")
                                      .Register("TestApp:Settings:FontColor")
                                      .Register("TestApp:Settings:Message")
                           });
                }
            })
            .UseStartup<Startup>();
    ```

    `ConfigureRefresh` Metoda se používá k určení nastavení používá k aktualizaci konfiguračních dat s úložištěm konfigurace aplikace, když se aktivuje operace aktualizace. Aby bylo možné spustit ve skutečnosti operace aktualizace, aktualizace middlewaru je potřeba nakonfigurovat pro aplikaci aktualizovat konfigurační data, když dojde ke změně.

2. Přidat *Settings.cs* souboru, který definuje a implementuje nový `Settings` třídy.

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

3. Otevřít *Startup.cs*a aktualizovat `ConfigureServices` metody k vytvoření vazby na konfigurační data `Settings` třídy.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

4. Aktualizace `Configure` způsob, jak přidat middlewaru umožňuje nastavení konfigurace zaregistrovaný pro aktualizaci aktualizovat, i když stále webové aplikace ASP.NET Core pro příjem požadavků.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```
    
    Middleware použije zadaný v konfiguraci aktualizace `AddAzureAppConfiguration` metoda ve `Program.cs` aktivovat aktualizaci každé žádosti přijaté webové aplikace ASP.NET Core. Pro každý požadavek operace aktualizace se aktivuje a klientské knihovny zkontroluje, pokud vypršela platnost hodnotu uloženou v mezipaměti pro registrované konfigurační nastavení. Pro hodnoty uložené v mezipaměti, jejichž platnost vypršela jsou aktualizovány hodnoty pro nastavení konfigurace obchodu s aplikacemi a zbývající hodnoty zůstávají beze změny.
    
    > [!NOTE]
    > Výchozí doba vypršení platnosti mezipaměti pro nastavení konfigurace je 30 sekund, ale lze přepsat pomocí volání `SetCacheExpiration` metodě v inicializátoru možnosti předán jako argument pro `ConfigureRefresh` metody.

## <a name="use-the-latest-configuration-data"></a>Použít nejnovější konfigurační data

1. Otevřít *HomeController.cs* v adresáři řadiče a přidejte odkaz na `Microsoft.Extensions.Options` balíčku.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Aktualizace `HomeController` třídy pro příjem `Settings` pomocí vkládání závislostí a ujistěte se, využívají jeho hodnoty.

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

3. Otevřít *Index.cshtml* v zobrazeních > domácí adresář a nahraďte jeho obsah následujícího skriptu:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"];
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

## <a name="build-and-run-the-app-locally"></a>Sestavte a spusťte aplikaci místně

1. Pokud chcete vytvořit aplikaci pomocí rozhraní příkazového řádku .NET Core, spusťte následující příkaz v příkazovém prostředí:

        dotnet build

2. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění webové aplikace:

        dotnet run

3. Otevřete okno prohlížeče a přejděte na `http://localhost:5000`, což je výchozí adresa URL pro webové aplikace hostované místně.

    ![Místní spuštění aplikace rychlý start](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **všechny prostředky**a vyberte instance aplikace, který konfigurace úložiště, který jste vytvořili v tomto rychlém startu.

5. Vyberte **Průzkumník konfigurací**a aktualizujte hodnoty následujících klíčů:

    | Klíč | Value |
    |---|---|
    | TestAppSettings:BackgroundColor | green |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Data z konfigurace aplikace Azure – nyní se živé aktualizace! |

6. Aktualizujte stránku v prohlížeči zobrazíte nové nastavení konfigurace. Více než jeden aktualizovat stránku v prohlížeči se může vyžadovat změny se projeví.

    ![Místní aktualizace aplikace rychlý start](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > Protože nastavení konfigurace jsou ukládány do mezipaměti s výchozí doba vypršení platnosti 30 sekund, všechny změny provedené v app storu konfigurace nastavení by se projeví pouze ve webové aplikaci pokud vypršela platnost mezipaměti.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste přidali do Azure se identita spravované služby pro přístup ke konfiguraci aplikací pro zjednodušení a zlepšení Správa přihlašovacích údajů pro vaši aplikaci. Další informace o tom, jak používat konfiguraci aplikací, i nadále ukázky Azure CLI.

> [!div class="nextstepaction"]
> [Ukázky rozhraní příkazového řádku](./cli-samples.md)

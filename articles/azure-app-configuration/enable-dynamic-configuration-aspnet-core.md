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
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: d5e1e5989f9902d9ab8dcc3e6c2b9d2a71f12df9
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224375"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Kurz: Použít dynamické konfiguraci aplikace ASP.NET Core

ASP.NET Core je modulární konfigurace systému, který může číst konfigurační data z různých zdrojů. Dokáže zpracovat změny v reálném čase bez způsobení aplikací k restartování. ASP.NET Core podporuje vazbu nastavení konfigurace má být silného typu třídy rozhraní .NET. To vloží do svého kódu s použitím různých `IOptions<T>` vzory. Jedna z následujících vzorů, konkrétně `IOptionsSnapshot<T>`, automaticky znovu načte konfiguraci vaší aplikace, když se změní podkladová data. 

Můžete vložit `IOptionsSnapshot<T>` do řadiče ve vaší aplikaci přístup k nejnovější konfigurace uložené v konfiguraci aplikací Azure. Můžete také nastavit Klientská knihovna ASP.NET Core konfigurace aplikace průběžně monitorovat a načíst všechny změny v obchodě s aplikacemi konfigurace. Můžete definovat pravidelný interval pro dotazování.

Tento kurz ukazuje, jak můžete implementovat konfigurace dynamické aktualizace ve vašem kódu. Sestaví ve webové aplikaci zavedený rychlých startech. Než budete pokračovat, dokončete [vytvoření aplikace ASP.NET Core s konfigurací aplikace](./quickstart-aspnet-core-app.md) první.

Provést kroky v tomto rychlém startu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) je skvělou možnost, která je k dispozici ve Windows, macOS a Linux platformy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení aplikace se aktualizovat jeho konfiguraci v reakci na změny v obchodě s aplikacemi konfigurace.
> * Vloží nejnovější konfiguraci v řadiči vaší aplikace.

## <a name="prerequisites"></a>Požadavky

To provedete v tomto rychlém startu, nainstalujete [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Znovu načíst data z konfigurace aplikace

1. Otevřete soubor Program.cs a aktualizujte `CreateWebHostBuilder` metodu tak, že přidáte `config.AddAzureAppConfiguration()` metody.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.Connect(settings["ConnectionStrings:AppConfig"])
                    .Watch("TestApp:Settings:BackgroundColor", TimeSpan.FromSeconds(1))
                    .Watch("TestApp:Settings:FontColor", TimeSpan.FromSeconds(1))
                    .Watch("TestApp:Settings:Message", TimeSpan.FromSeconds(1)));
            })
            .UseStartup<Startup>();
    ```

    Druhý parametr `.Watch` metoda je interval dotazování, kdy Klientská knihovna ASP.NET dotazuje konfigurace app storu. Klientská knihovna zkontroluje nastavení konkrétní konfiguraci zobrazíte, pokud došlo k chybě změny.

2. Přidání souboru Settings.cs, který definuje a implementuje nový `Settings` třídy.

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

3. Otevřete Startup.cs a aktualizujte `ConfigureServices` metody k vytvoření vazby na konfigurační data `Settings` třídy.

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

## <a name="use-the-latest-configuration-data"></a>Použít nejnovější konfigurační data

1. Otevřete HomeController.cs v adresáři řadiče. Aktualizace `HomeController` třídy pro příjem `Settings` pomocí vkládání závislostí a ujistěte se, využívají jeho hodnoty.

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

2. Otevřít v zobrazení Index.cshtml > domácí adresář a nahraďte jeho obsah následujícího skriptu:

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

4. Přihlaste se k webu [Azure Portal](https://aka.ms/azconfig/portal). Vyberte **všechny prostředky**a vyberte instance aplikace, který konfigurace úložiště, který jste vytvořili v tomto rychlém startu.

5. Vyberte **klíč/hodnota Explorer**a aktualizujte hodnoty následujících klíčů:

    | Klíč | Hodnota |
    |---|---|
    | TestAppSettings:BackgroundColor | Modrá |
    | TestAppSettings:FontColor | lightGray |
    | TestAppSettings:Message | Data z konfigurace aplikace Azure – nyní se živé aktualizace! |

6. Aktualizujte stránku v prohlížeči zobrazíte nové nastavení konfigurace.

    ![Místní aktualizace aplikace rychlý start](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste přidali do Azure se identita spravované služby pro přístup ke konfiguraci aplikací pro zjednodušení a zlepšení Správa přihlašovacích údajů pro vaši aplikaci. Další informace o tom, jak používat konfiguraci aplikací, i nadále ukázky Azure CLI.

> [!div class="nextstepaction"]
> [Ukázky rozhraní příkazového řádku](./cli-samples.md)

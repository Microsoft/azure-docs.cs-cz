---
title: 'Kurz: použití dynamické konfigurace konfigurace aplikace v ASP.NET Core'
titleSuffix: Azure App Configuration
description: V tomto kurzu se naučíte dynamicky aktualizovat konfigurační data pro ASP.NET Core aplikace.
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
ms.openlocfilehash: 608368daa17246f2512d243b2656dd7702d84f50
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433708"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Kurz: použití dynamické konfigurace v aplikaci ASP.NET Core

ASP.NET Core má připojitelná konfigurační systém, který může číst konfigurační data z nejrůznějších zdrojů. Může zpracovávat změny průběžně bez toho, aby se aplikace mohla restartovat. ASP.NET Core podporuje vazbu nastavení konfigurace na třídy .NET silného typu. Vloží je do kódu pomocí různých `IOptions<T>`ch vzorů. Jeden z těchto vzorů, konkrétně `IOptionsSnapshot<T>`, automaticky znovu načte konfiguraci aplikace, když se změní podkladová data. Můžete vložit `IOptionsSnapshot<T>` do řadičů v aplikaci, abyste měli přístup k nejnovější konfiguraci uložené v konfiguraci aplikace Azure.

Můžete také nastavit konfiguraci aplikace ASP.NET Core klientské knihovně pro dynamické obnovení sady nastavení konfigurace pomocí middlewaru. Dokud bude webová aplikace nadále přijímat požadavky, nastavení konfigurace budou nadále aktualizována s úložištěm konfigurace.

Aby se nastavení zachovalo jako aktualizované a zabránilo se příliš velkému počtu volání do úložiště konfigurace, použije se pro každé nastavení mezipaměť. Dokud neuplyne hodnota nastavení uložené v mezipaměti, operace aktualizace neaktualizuje hodnotu, a to ani v případě, že se hodnota v úložišti konfigurace změnila. Výchozí doba vypršení platnosti každé žádosti je 30 sekund, ale v případě potřeby může být přepsána.

V tomto kurzu se dozvíte, jak můžete implementovat aktualizace dynamické konfigurace do kódu. Sestavuje se ve webové aplikaci představené v rychlých startech. Než budete pokračovat, dokončete nejprve [Vytvoření aplikace ASP.NET Core s konfigurací aplikace](./quickstart-aspnet-core-app.md) .

K provedení kroků v tomto kurzu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) je vynikající možnost, která je dostupná na platformách Windows, MacOS a Linux.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte svoji aplikaci tak, aby aktualizovala svou konfiguraci v reakci na změny v úložišti konfigurace aplikace.
> * Vloží nejnovější konfiguraci do řadičů vaší aplikace.

## <a name="prerequisites"></a>Požadavky

K provedení tohoto kurzu nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Než budete pokračovat, dokončete nejprve [Vytvoření aplikace ASP.NET Core s konfigurací aplikace](./quickstart-aspnet-core-app.md) .

## <a name="reload-data-from-app-configuration"></a>Znovu načíst data z konfigurace aplikace

1. Přidejte odkaz na `Microsoft.Azure.AppConfiguration.AspNetCore` balíček NuGet spuštěním následujícího příkazu:

    ```CLI
        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-010060003-1250
    ```

1. Otevřete *program.cs*a aktualizujte metodu `CreateWebHostBuilder` pro přidání metody `config.AddAzureAppConfiguration()`.

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
                                      .Register("TestApp:Settings:Message");
                           });
                });
            })
            .UseStartup<Startup>();
    ```

    Metoda `ConfigureRefresh` slouží k určení nastavení použitých k aktualizaci konfiguračních dat pomocí úložiště konfigurace aplikace při aktivaci operace aktualizace. Aby bylo možné spustit operaci aktualizace, je nutné nakonfigurovat middleware aktualizace, aby aplikace aktualizovala konfigurační data, když dojde k jakékoli změně.

2. Přidejte soubor *Settings.cs* , který definuje a implementuje novou třídu `Settings`.

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

3. Otevřete *Startup.cs*a aktualizujte metodu `ConfigureServices`, abyste navázali konfigurační data na třídu `Settings`.

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

4. Aktualizujte metodu `Configure`, aby se přidal middleware, aby bylo možné aktualizovat konfigurační nastavení zaregistrovaná pro aktualizaci, dokud bude webová aplikace ASP.NET Core nadále přijímat požadavky.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```
    
    Middleware používá konfiguraci aktualizace zadanou v metodě `AddAzureAppConfiguration` v `Program.cs` k aktivaci aktualizace pro každý požadavek přijatý webovou aplikací ASP.NET Core. Pro každý požadavek se aktivuje operace aktualizace a Klientská knihovna zkontroluje, jestli vypršela platnost hodnoty v mezipaměti pro zaregistrovaná nastavení konfigurace. U hodnot uložených v mezipaměti, jejichž platnost vypršela, se hodnoty pro nastavení aktualizují pomocí úložiště konfigurace aplikace a zbývající hodnoty zůstanou beze změny.
    
    > [!NOTE]
    > Výchozí doba vypršení platnosti mezipaměti pro konfigurační nastavení je 30 sekund, ale lze ji přepsat voláním metody `SetCacheExpiration` v inicializátoru možnosti předaného jako argument metody `ConfigureRefresh`.

## <a name="use-the-latest-configuration-data"></a>Použít nejnovější konfigurační data

1. Otevřete *HomeController.cs* v adresáři Controllers a přidejte odkaz na balíček `Microsoft.Extensions.Options`.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Aktualizujte třídu `HomeController` tak, aby přijímala `Settings` prostřednictvím injektáže závislosti, a využijte její hodnoty.

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

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Pokud chcete aplikaci vytvořit pomocí .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

        dotnet build

2. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění webové aplikace:

        dotnet run

3. Otevřete okno prohlížeče a pro webovou aplikaci hostovanou místně použijte `http://localhost:5000`, což je výchozí adresa URL.

    ![Spuštění aplikace pro rychlý Start – místní](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Přihlaste se na web [Azure Portal](https://portal.azure.com). Vyberte **všechny prostředky**a vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v rychlém startu.

5. Vyberte **Průzkumník konfigurace**a aktualizujte hodnoty následujících klíčů:

    | Klíč | Hodnota |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp: nastavení: zpráva | Data z konfigurace aplikace Azure – teď s aktualizacemi za provozu! |

6. Aktualizujte stránku prohlížeče, aby se zobrazilo nové nastavení konfigurace. Aby se změny projevily, mohou být vyžadovány více než jedna aktualizace stránky prohlížeče.

    ![Rychlé obnovení místní aktualizace aplikace](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > Vzhledem k tomu, že se nastavení konfigurace ukládají do mezipaměti s výchozím časem vypršení platnosti 30 sekund, všechny změny nastavení v úložišti konfigurace aplikace se projeví pouze ve webové aplikaci, až do vypršení platnosti mezipaměti.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili ASP.NET Core webové aplikaci, abyste mohli dynamicky aktualizovat nastavení konfigurace z konfigurace aplikace. Další informace o tom, jak používat spravovanou identitu Azure ke zjednodušení přístupu ke konfiguraci aplikace, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Spravovaná integrace identit](./howto-integrate-azure-managed-service-identity.md)

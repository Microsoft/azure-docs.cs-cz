---
title: Rychlý Start pro konfiguraci aplikací Azure s ASP.NET Core | Microsoft Docs
description: Rychlý Start pro použití konfigurace aplikací Azure s aplikacemi ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 10/11/2019
ms.author: yegu
ms.openlocfilehash: 91712b3f730317e65cda7b48c8f5636b2fb9ab2c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185087"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Rychlý Start: Vytvoření aplikace ASP.NET Core s využitím konfigurace aplikace Azure

V tomto rychlém startu zařadíte do ASP.NET Core aplikace konfiguraci aplikací Azure, která vám umožní centralizovat úložiště a správu nastavení aplikace odděleně od vašeho kódu. ASP.NET Core vytvoří objekt konfigurace s jedním klíčem na základě hodnoty pomocí nastavení z jednoho nebo více zdrojů dat, které jsou určeny aplikací. Tyto zdroje dat jsou známé jako *poskytovatelé konfigurace*. Vzhledem k tomu, že klient .NET Core konfigurace aplikace je implementován jako poskytovatel, služba se zobrazí jako jiný zdroj dat.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Sada .NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **Průzkumníka konfigurace** >  **+ vytvořit** a přidejte následující páry klíč-hodnota:

    | Klíč | Hodnota |
    |---|---|
    | TestApp:Settings:BackgroundColor | White |
    | TestApp: nastavení: FontSize | 24 |
    | TestApp:Settings:FontColor | Black |
    | TestApp: nastavení: zpráva | Data z konfigurace aplikace Azure |

    V tuto chvíli ponechat **popisek** a **typ obsahu** prázdné.

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

K vytvoření nového projektu webové aplikace ASP.NET Core MVC použijete [rozhraní příkazového řádku .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) . Výhodou použití .NET Core CLI přes Visual Studio je to, že je k dispozici na platformách Windows, macOS a Linux.

1. Vytvořte novou složku pro váš projekt. V tomto rychlém startu pojmenujte ho *TestAppConfig*.

2. V nové složce spusťte následující příkaz pro vytvoření nového projektu webové aplikace ASP.NET Core MVC:

    ```CLI
        dotnet new mvc --no-https
    ```

## <a name="add-secret-manager"></a>Přidat správce tajných klíčů

Chcete-li použít Správce tajných klíčů, přidejte `UserSecretsId` element do souboru *. csproj* .

- Otevřete soubor *. csproj* . Přidejte `UserSecretsId` element, jak je znázorněno zde. Můžete použít stejný identifikátor GUID, nebo můžete tuto hodnotu nahradit vlastními. Uložte soubor.

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

Nástroj Secret Manager ukládá citlivá data související s vývojem mimo strom vašeho projektu. Tento přístup pomáhá zabránit náhodnému sdílení tajných kódů aplikace ve zdrojovém kódu. Další informace o Správci tajných klíčů najdete v článku [bezpečné úložiště tajných kódů aplikací ve vývoji v tématu ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikace

1. Přidejte odkaz na `Microsoft.Azure.AppConfiguration.AspNetCore` balíček NuGet spuštěním následujícího příkazu:

    ```CLI
        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-010060003-1250
    ```
2. Spusťte následující příkaz pro obnovení balíčků pro váš projekt:

    ```CLI
        dotnet restore
    ```
3. Přidejte tajný klíč s názvem *connectionStrings: appconfig* do správce tajných klíčů.

    Tento tajný klíč obsahuje připojovací řetězec pro přístup k úložišti konfigurace aplikace. Hodnotu v následujícím příkazu nahraďte připojovacím řetězcem pro úložiště konfigurace aplikace.

    Tento příkaz se musí spustit ve stejném adresáři jako soubor *.csproj*.

    ```CLI
        dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Některá prostředí zkrátí připojovací řetězec, pokud není uzavřen v uvozovkách. Ujistěte se, že výstup příkazu `dotnet user-secrets` zobrazuje celý připojovací řetězec. Pokud tomu tak není, spusťte příkaz znovu a uzavřete připojovací řetězec do uvozovek.

    Správce tajného klíče se používá jenom k místnímu testování webové aplikace. Při nasazení aplikace do [Azure App Service](https://azure.microsoft.com/services/app-service/web)například použijete pro uložení připojovacího řetězce nastavení aplikace **připojovací řetězce** v App Service místo u správce tajného klíče.

    K tomuto tajnému kódu se dostanete pomocí konfiguračního rozhraní API. Dvojtečka (:) funguje v názvu konfigurace s rozhraním API konfigurace na všech podporovaných platformách. Viz [Konfigurace podle prostředí](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Otevřete *program.cs*a přidejte odkaz na poskytovatele konfigurace aplikace .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

5. Aktualizujte metodu `CreateWebHostBuilder`, aby používala konfiguraci aplikace, voláním metody `config.AddAzureAppConfiguration()`.
    
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

6. Otevřete *index. cshtml* v zobrazeních > domovském adresáři a nahraďte jeho obsah následujícím kódem:

    ```HTML
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]</h1>
    ```

7. Otevřete *_Layout. cshtml* v zobrazeních > sdíleném adresáři a nahraďte jeho obsah následujícím kódem:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Pokud chcete aplikaci vytvořit pomocí .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

    ```CLI
       dotnet build
    ```

2. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění webové aplikace:

    ```CLI
        dotnet run
    ```

3. Otevřete okno prohlížeče a pro webovou aplikaci hostovanou místně použijte `http://localhost:5000`, což je výchozí adresa URL.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nové úložiště konfigurace aplikace a použili ho s ASP.NET Core webovou aplikací prostřednictvím [poskytovatele konfigurace aplikace](https://go.microsoft.com/fwlink/?linkid=2074664). Další informace o tom, jak nakonfigurovat aplikaci ASP.NET Core, aby dynamicky aktualizovala nastavení konfigurace, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Povolit dynamickou konfiguraci](./enable-dynamic-configuration-aspnet-core.md)

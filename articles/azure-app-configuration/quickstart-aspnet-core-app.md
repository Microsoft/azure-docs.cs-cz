---
title: Rychlý start pro konfiguraci aplikací Azure pomocí ASP.NET Core | Dokumentace Microsoftu
description: Rychlý start pro konfiguraci aplikací Azure pomocí aplikace ASP.NET Core
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
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: f9d21cb1b047fcc1043ca2d92f718bb5821879a3
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226058"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Rychlý start: Vytvoření aplikace ASP.NET Core s konfigurací aplikace Azure

Konfigurace aplikace pro Azure je služba spravované konfigurace v Azure. Můžete ho snadno ukládat a spravovat nastavení aplikace na jednom místě, který je oddělen od kódu. V tomto rychlém startu se dozvíte, jak tuto službu začlenit do webové aplikace ASP.NET Core. 

ASP.NET Core vytvoří objekt konfigurace jedné založené na klíč hodnota s použitím nastavení z jednoho nebo více zdrojů dat, která jsou určena podle aplikace. Tyto zdroje dat jsou označovány jako *poskytovatelé konfigurace*. Protože konfigurace aplikace .NET Core, klient je implementovaná jako takové zprostředkovatele, zobrazí se jako jiný zdroj dat služby.

Provést kroky v tomto rychlém startu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) skvělou možnost je k dispozici ve Windows, macOS a Linux platformy.

## <a name="prerequisites"></a>Požadavky

To provedete v tomto rychlém startu, nainstalujete [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

Můžete použít [rozhraní příkazového řádku .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) vytvořte nový projekt webové aplikace ASP.NET Core MVC. Výhodou použití rozhraní příkazového řádku .NET Core v sadě Visual Studio je, že je k dispozici ve Windows, macOS a Linux platformy.

1. Vytvořte novou složku pro váš projekt. Pro tento rychlý start, pojmenujte ho *TestAppConfig*.

2. V nové složce spuštěním následujícího příkazu vytvořte nový projekt webové aplikace ASP.NET Core MVC:

        dotnet new mvc

## <a name="add-secret-manager"></a>Add Secret Manager

Přidat [nástroj tajný klíč správce](https://docs.microsoft.com/aspnet/core/security/app-secrets) do projektu. Nástroj Secret Manager ukládá citlivá data související s vývojem mimo strom vašeho projektu. Tento přístup pomáhá zabránit náhodnému sdílení tajných kódů aplikace ve zdrojovém kódu.

- Otevřete soubor *.csproj*. Přidat `UserSecretsId` elementu, jak je znázorněno zde a nahraďte její hodnotu s vlastním, což je obvykle identifikátor GUID. Uložte soubor.

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

1. Přidejte odkaz na `Microsoft.Extensions.Configuration.AzureAppConfiguration` balíček NuGet spuštěním následujícího příkazu:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. Spusťte následující příkaz k obnovení balíčků pro váš projekt:

        dotnet restore

3. Přidání tajného kódu s názvem *ConnectionStrings:AppConfig* správci tajný klíč.

    Tento tajný kód obsahuje připojovací řetězec pro přístup k úložišti konfigurace vaší aplikace. Hodnota v následujícím příkazu nahraďte připojovacím řetězcem pro vaše aplikace úložiště konfigurace.

    Tento příkaz se musí spustit ve stejném adresáři jako soubor *.csproj*.

        dotnet user-secrets set ConnectionStrings:AppConfig "Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>"

    Tajný klíč správce slouží pouze k otestování webové aplikace místně. Při nasazení aplikace, například na [služby Azure App Service](https://azure.microsoft.com/services/app-service/web), použijte aplikaci nastavení, například **připojovací řetězce** ve službě App Service. Toto nastavení použijte místo uložení připojovacího řetězce, pomocí tajného klíče správce.

    Tento tajný kód je přistupováno pomocí rozhraní API konfigurace. Dvojtečka (:) funguje v názvu konfigurace s konfigurací rozhraní API na všech podporovaných platformách. Zobrazit [konfigurace podle prostředí](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

4. Otevřete soubor Program.cs a aktualizujte `CreateWebHostBuilder` používat konfiguraci aplikací pomocí volání metody `config.AddAzureAppConfiguration()` metody.

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

5. Otevřít v zobrazení Index.cshtml > domácí adresář a nahraďte jeho obsah následujícím kódem:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@Configuration["TestApp:Settings:Message"]</h1>
    </body>
    </html>
    ```

6. Otevřít v zobrazení _Layout.cshtml > sdílený adresář a nahraďte jeho obsah následujícím kódem:

    ```html
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

## <a name="build-and-run-the-app-locally"></a>Sestavte a spusťte aplikaci místně

1. Pokud chcete vytvořit aplikaci pomocí rozhraní příkazového řádku .NET Core, spusťte následující příkaz v příkazovém prostředí:

        dotnet build

2. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění webové aplikace:

        dotnet run

3. Otevřete okno prohlížeče a přejděte na `http://localhost:5000`, což je výchozí adresa URL pro webové aplikace hostované místně.

    ![Místní spuštění aplikace rychlý start](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili novým úložištěm konfigurace aplikace a používat s webovou aplikaci ASP.NET Core. Další informace o tom, jak používat konfiguraci aplikací, pokračujte k dalšímu kurzu, který ukazuje ověření.

> [!div class="nextstepaction"]
> [Spravované identity pro integraci prostředků Azure](./integrate-azure-managed-service-identity.md)

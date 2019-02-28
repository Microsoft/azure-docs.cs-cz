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
ms.openlocfilehash: ce19041b29d567f061dde59fbe041adf61f889a0
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961478"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Rychlý start: Vytvoření aplikace ASP.NET Core s konfigurací aplikace Azure

Konfigurace aplikace pro Azure je služba spravované konfigurace v Azure. To umožňuje snadno ukládat a spravovat nastavení aplikace na jednom místě, který je oddělen od kódu. V tomto rychlém startu se dozvíte, jak tuto službu začlenit do webové aplikace ASP.NET Core. ASP.NET Core vytvoří objekt konfigurace na základě jednotného klíč hodnota pomocí nastavení z jednoho nebo více zdrojů dat, označované jako *poskytovatelé konfigurace*zadané aplikace. Protože klient je konfigurace aplikace .NET Core implementované jako takové zprostředkovatele, se zobrazí stejně jako jiný zdroj dat služby.

K dokončení kroků v tomto rychlém startu můžete použít jakýkoli editor kódu. Skvělou volbou je však editor [Visual Studio Code](https://code.visualstudio.com/), který je dostupný pro platformy Windows, macOS a Linux.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

Budete používat [rozhraní příkazového řádku .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) vytvoříte nový projekt webové aplikace ASP.NET Core MVC. Výhodou použití rozhraní .NET Core CLI místo sady Visual Studio je jeho dostupnost na platformách Windows, Linux i macOS.

1. Vytvořte novou složku pro váš projekt. Pro účely tohoto rychlého startu pojmenujeme ho *TestAppConfig*.

2. V nové složce spuštěním následujícího příkazu vytvořte nový projekt webové aplikace ASP.NET Core MVC:

        dotnet new mvc

## <a name="add-secret-manager"></a>Add Secret Manager

Přidáte [nástroj tajný klíč správce](https://docs.microsoft.com/aspnet/core/security/app-secrets) do projektu. Nástroj Secret Manager ukládá citlivá data související s vývojem mimo strom vašeho projektu. Tento přístup pomáhá zabránit náhodnému sdílení tajných kódů aplikace ve zdrojovém kódu.

- Otevřete soubor *.csproj*. Přidat `UserSecretsId` elementu, jak je znázorněno níže a nahraďte její hodnotu s vlastním, což je obvykle identifikátor GUID. Uložte soubor.

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

## <a name="connect-to-app-configuration-store"></a>Připojit k úložišti konfigurace aplikace

1. Přidejte odkaz na balíček `Microsoft.Extensions.Configuration.AzureAppConfiguration` spuštěním následujícího příkazu:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. Spuštěním následujícího příkazu obnovte balíčky pro váš projekt.

        dotnet restore

3. Přidání tajného kódu s názvem *ConnectionStrings:AppConfig* správci tajný klíč.

    Tento tajný kód bude obsahovat připojovací řetězec pro přístup k úložišti konfigurace vaší aplikace. Nahraďte hodnotu v následujícím připojovacím řetězcem pro vaši app store configuration.

    Tento příkaz se musí spustit ve stejném adresáři jako soubor *.csproj*.

        dotnet user-secrets set ConnectionStrings:AppConfig "Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>"

    Tajný klíč správce se použije pouze pro testování webové aplikace místně. Při nasazení aplikace (například [služby Azure App Service](https://azure.microsoft.com/services/app-service/web)), bude používat nastavení aplikace (například **připojovací řetězce** ve službě App Service) místo uložení připojovacího řetězce s tajným klíčem Správce.

    K tomuto tajnému klíči se přistupuje pomocí konfiguračního rozhraní API. Dvojtečka (:) v názvu konfigurace funguje s konfiguračním rozhraním API na všech podporovaných platformách, viz [Konfigurace podle prostředí](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0#configuration-by-environment).

4. Otevřít *Program.cs* a aktualizovat `CreateWebHostBuilder` používat konfiguraci aplikací pomocí volání metody `config.AddAzureAppConfiguration()` metoda.

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

5. Otevřít *Index.cshtml* v *zobrazení* > *Domů* adresáře a nahraďte jeho obsah následujícím kódem:

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

6. Otevřít *_Layout.cshtml* v *zobrazení* > *Shared* adresáře a nahraďte jeho obsah následujícím kódem:

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

1. Pokud chcete aplikaci sestavit pomocí .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

        dotnet build

2. Po úspěšném dokončení sestavení spuštěním následujícího příkazu místně spusťte webovou aplikaci:

        dotnet run

3. Spusťte okno prohlížeče a přejděte na `http://localhost:5000`, což je výchozí adresa URL pro webové aplikace hostované místně.

    ![Místní spuštění aplikace rychlý start](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili novým úložištěm konfigurace aplikace a používat s webovou aplikaci ASP.NET Core. Další informace o použití konfigurace aplikace, pokračujte k dalšímu kurzu, který ukazuje ověření.

> [!div class="nextstepaction"]
> [Spravované identity pro integraci prostředků Azure](./integrate-azure-managed-service-identity.md)

---
title: Úvodní příručka pro konfiguraci aplikací Azure s ASP.NET jádrem | Dokumenty společnosti Microsoft
description: Úvodní příručka pro používání konfigurace aplikací Azure s aplikacemi ASP.NET Core
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 537dabe09c41012b9e15998ce3af8198dcfb62d3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245770"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Úvodní příručka: Vytvoření aplikace ASP.NET Core s konfigurací aplikací Azure

V tomto rychlém startu použijete Azure App Configuration k centralizaci úložiště a správy nastavení aplikací pro ASP.NET základní aplikaci. ASP.NET Core vytvoří jeden konfigurační objekt založený na hodnotě klíče pomocí nastavení z jednoho nebo více zdrojů dat určených aplikací. Tyto zdroje dat jsou označovány jako *zprostředkovatelé konfigurace*. Vzhledem k tomu, že klient .NET Core konfigurace aplikace je implementován jako zprostředkovatel konfigurace, služba se zobrazí jako jiný zdroj dat.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
- [Sada .NET Core SDK](https://dotnet.microsoft.com/download)

>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění pokynů příkazového řádku v tomto článku.  Má společné nástroje Azure předinstalované, včetně sady .NET Core SDK. Pokud jste přihlášení k předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com) z shell.azure.com.  Další informace o Azure Cloud Shellu najdete v [naší dokumentaci](../cloud-shell/overview.md)

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikací

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **Průzkumník** > konfigurace**Vytvořit** > **hodnotu klíče a** přidejte následující dvojice klíč-hodnota:

    | Klíč | Hodnota |
    |---|---|
    | testapp:nastavení:barva na pozadí | White |
    | testapp:nastavení:velikost písma | 24 |
    | testapp:nastavení:barva písma | Black |
    | testapp:nastavení:zpráva | Data z konfigurace aplikací Azure |

    Ponechte **popisek** a **typ obsahu** prozatím prázdný. Vyberte **Použít**.

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

Pomocí [rozhraní příkazového řádku .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) vytvořte nový ASP.NET projektu webové aplikace Core MVC. [Azure Cloud Shell](https://shell.azure.com) poskytuje tyto nástroje pro vás.  Jsou také k dispozici na platformách Windows, macOS a Linux.

1. Vytvořte novou složku pro váš projekt. Pro tento rychlý start jej pojmenujte *TestAppConfig*.

1. V nové složce spusťte následující příkaz a vytvořte nový ASP.NET projektu webové aplikace Core MVC:

```dotnetcli
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
> Další informace o Tajném správci najdete [v tématu Bezpečné ukládání tajných kódů aplikací ve vývoji v ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikací

1. Přidejte odkaz `Microsoft.Azure.AppConfiguration.AspNetCore` na balíček NuGet spuštěním následujícího příkazu:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Chcete-li obnovit balíčky pro projekt, spusťte následující příkaz:

    ```dotnetcli
    dotnet restore
    ```

1. Přidejte tajný klíč s názvem *ConnectionStrings:AppConfig* do Správce tajných služeb.

    Tento tajný klíč obsahuje připojovací řetězec pro přístup k úložišti konfigurace aplikace. Nahraďte hodnotu v následujícím příkazu připojovacím řetězcem pro úložiště konfigurace aplikací. Připojovací řetězec najdete v části **Přístupové klíče** na webu Azure Portal.

    Tento příkaz se musí spustit ve stejném adresáři jako soubor *.csproj*.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Některá skořepiny zkrátí připojovací řetězec, pokud není uzavřen v uvozovkách. Ujistěte se, `dotnet user-secrets` že výstup příkazu zobrazuje celý připojovací řetězec. Pokud tomu tak není, znovu spusťte příkaz a ohraničovací řetězec ohraničující řetězec do uvozovek.

    Správce tajných barev se používá pouze k místnímu testování webové aplikace. Když se aplikace nasadí do [služby Azure App Service](https://azure.microsoft.com/services/app-service/web), použijete například nastavení aplikace **připojovací řetězce** ve službě App Service namísto Správce tajných služeb k uložení připojovacího řetězce.

    Přístup k tomuto tajnému klíči pomocí konfiguračního rozhraní API. Dvojtečka (:) pracuje v názvu konfigurace s konfiguračním rozhraním API na všech podporovaných platformách. Viz [Konfigurace podle prostředí](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

1. Otevřete *Program.cs*a přidejte odkaz na zprostředkovatele konfigurace aplikace .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Aktualizujte `CreateWebHostBuilder` metodu pro použití `config.AddAzureAppConfiguration()` konfigurace aplikace voláním metody.

    > [!IMPORTANT]
    > `CreateHostBuilder`nahrazuje `CreateWebHostBuilder` v rozhraní .NET Core 3.0.  Vyberte správnou syntaxi na základě vašeho prostředí.

    #### <a name="net-core-2x"></a>[.NET Jádro 2.x](#tab/core2x)

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

    #### <a name="net-core-3x"></a>[.NET Jádro 3.x](#tab/core3x)

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

    ---

1. Přejděte na * <app root>/Views/Home* a otevřete *soubor Index.cshtml*. Nahraďte jeho obsah následujícím kódem:

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

1. Přejděte na * <app root>/Zobrazení/Sdílené* a otevřete *soubor _Layout.cshtml*. Nahraďte jeho obsah následujícím kódem:

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

## <a name="build-and-run-the-app-locally"></a>Sestavení a spuštění aplikace místně

1. Chcete-li vytvořit aplikaci pomocí rozhraní PŘÍKAZOVÉHO PŘÍKAZU .NET Core, přejděte do kořenového adresáře aplikace a spusťte následující příkaz v příkazovém prostředí:

    ```dotnetcli
    dotnet build
    ```

1. Po úspěšném dokončení sestavení spusťte následující příkaz a spusťte webovou aplikaci místně:

    ```dotnetcli
    dotnet run
    ```

1. Pokud pracujete na místním počítači, přejděte `http://localhost:5000`pomocí prohlížeče na aplikaci . Toto je výchozí adresa URL webové aplikace hostované místně.  

Pokud pracujete v prostředí Azure Cloud Shell, vyberte tlačítko *Web Preview* následované *tlačítkem Configure*.  

![Vyhledání tlačítka Náhled na webu](./media/quickstarts/cloud-shell-web-preview.png)

Po zobrazení výzvy ke konfiguraci portu pro náhled zadejte "5000" a vyberte *Otevřít a procházet*.  Na webové stránce se bude číst "Data z konfigurace aplikace Azure".

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nový obchod SKonfiguraci aplikací a použili ho s ASP.NET webovou aplikací Core prostřednictvím [poskytovatele konfigurace aplikace](https://go.microsoft.com/fwlink/?linkid=2074664). Chcete-li se dozvědět, jak nakonfigurovat aplikaci ASP.NET Core pro dynamickou aktualizaci nastavení konfigurace, pokračujte dalším kurzem.

> [!div class="nextstepaction"]
> [Povolit dynamickou konfiguraci](./enable-dynamic-configuration-aspnet-core.md)

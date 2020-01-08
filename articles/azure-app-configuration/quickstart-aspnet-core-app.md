---
title: Rychlý Start pro konfiguraci aplikací Azure s ASP.NET Core | Microsoft Docs
description: Rychlý Start pro použití konfigurace aplikací Azure s aplikacemi ASP.NET Core
services: azure-app-configuration
author: jpconnock
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.date: 01/04/2020
ms.author: jeconnoc
ms.openlocfilehash: f625135f036ec8fc816bc3c3eb6c76c635c51fe9
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690205"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Rychlý Start: Vytvoření aplikace ASP.NET Core s využitím konfigurace aplikace Azure

V tomto rychlém startu použijete Azure App Configuration k centralizaci úložiště a správy nastavení aplikace pro ASP.NET Core aplikaci. ASP.NET Core vytvoří objekt konfigurace s jedním klíčem na základě hodnoty pomocí nastavení z jednoho nebo více zdrojů dat, které jsou určeny aplikací. Tyto zdroje dat jsou známé jako *poskytovatelé konfigurace*. Vzhledem k tomu, že klient .NET Core konfigurace aplikace je implementovaný jako poskytovatel konfigurace, služba se zobrazí jako jiný zdroj dat.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Sada .NET Core SDK](https://dotnet.microsoft.com/download)

>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění instrukcí příkazového řádku v tomto článku.  Má předinstalované běžné nástroje Azure, včetně .NET Core SDK. Pokud jste přihlášeni ke svému předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Další informace o Azure Cloud Shell najdete v [naší dokumentaci](../cloud-shell/overview.md) .

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **Průzkumník konfigurace** > **vytvořit** a přidejte následující páry klíč-hodnota:

    | Klíč | Hodnota |
    |---|---|
    | TestApp:Settings:BackgroundColor | Bílá |
    | TestApp: nastavení: FontSize | 24 |
    | TestApp:Settings:FontColor | Černá |
    | TestApp: nastavení: zpráva | Data z konfigurace aplikace Azure |

    V tuto chvíli ponechat **popisek** a **typ obsahu** prázdné.

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

K vytvoření nového projektu webové aplikace ASP.NET Core MVC použijte [rozhraní příkazového řádku .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) . [Azure Cloud Shell](https://shell.azure.com) poskytuje tyto nástroje za vás.  Jsou k dispozici také na platformách Windows, macOS a Linux.

1. Vytvořte novou složku pro váš projekt. V tomto rychlém startu pojmenujte ho *TestAppConfig*.

1. V nové složce spusťte následující příkaz pro vytvoření nového projektu webové aplikace ASP.NET Core MVC:

```dotnetcli
dotnet new mvc --no-https
```

## <a name="add-secret-manager"></a>Přidat správce tajných klíčů

Chcete-li použít Správce tajných klíčů, přidejte `UserSecretsId` element do souboru *. csproj* .

Otevřete soubor *. csproj* . Přidejte `UserSecretsId` element, jak je znázorněno zde. Můžete použít stejný identifikátor GUID, nebo můžete tuto hodnotu nahradit vlastními. Uložte soubor.

> [!IMPORTANT]
> `CreateHostBuilder` nahrazuje `CreateWebHostBuilder` v .NET Core 3,0.  Vyberte správnou syntaxi na základě vašeho prostředí.

#### <a name="net-core-2xtabcore2x"></a>[.NET Core 2.x](#tab/core2x)

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

#### <a name="net-core-3xtabcore3x"></a>[.NET Core 3. x](#tab/core3x)

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```
---

Nástroj Secret Manager ukládá citlivá data související s vývojem mimo strom vašeho projektu. Tento přístup pomáhá zabránit náhodnému sdílení tajných kódů aplikace ve zdrojovém kódu. Další informace o Správci tajných klíčů najdete v článku [bezpečné úložiště tajných kódů aplikací ve vývoji v tématu ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikace

1. Přidejte odkaz na `Microsoft.Azure.AppConfiguration.AspNetCore` balíček NuGet spuštěním následujícího příkazu:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 3.0.0-preview-010560002-1165
    ```
1. Spusťte následující příkaz pro obnovení balíčků pro váš projekt:

    ```dotnetcli
    dotnet restore
    ```
1. Přidejte tajný klíč s názvem *connectionStrings: appconfig* do správce tajných klíčů.

    Tento tajný klíč obsahuje připojovací řetězec pro přístup k úložišti konfigurace aplikace. Hodnotu v následujícím příkazu nahraďte připojovacím řetězcem pro úložiště konfigurace aplikace.

    Tento příkaz se musí spustit ve stejném adresáři jako soubor *.csproj*.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Některá prostředí zkrátí připojovací řetězec, pokud není uzavřen v uvozovkách. Ujistěte se, že výstup příkazu `dotnet user-secrets` zobrazuje celý připojovací řetězec. Pokud tomu tak není, spusťte příkaz znovu a uzavřete připojovací řetězec do uvozovek.

    Správce tajného klíče se používá jenom k místnímu testování webové aplikace. Při nasazení aplikace do [Azure App Service](https://azure.microsoft.com/services/app-service/web)například použijete nastavení aplikace **připojovací řetězce** v App Service namísto správce tajných klíčů k uložení připojovacího řetězce.

    Přístup k tomuto tajnému kódu pomocí konfiguračního rozhraní API. Dvojtečka (:) funguje v názvu konfigurace s rozhraním API konfigurace na všech podporovaných platformách. Viz [Konfigurace podle prostředí](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

1. Otevřete *program.cs*a přidejte odkaz na poskytovatele konfigurace aplikace .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Aktualizujte metodu `CreateWebHostBuilder`, aby používala konfiguraci aplikace, voláním metody `config.AddAzureAppConfiguration()`.
    
    > [!IMPORTANT]
    > `CreateHostBuilder` nahrazuje `CreateWebHostBuilder` v .NET Core 3,0.  Vyberte správnou syntaxi na základě vašeho prostředí.

    #### <a name="net-core-2xtabcore2x"></a>[.NET Core 2.x](#tab/core2x)

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

    #### <a name="net-core-3xtabcore3x"></a>[.NET Core 3. x](#tab/core3x)
    
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

1. Přejděte na *<app root>/views/Home* a otevřete *index. cshtml*. Nahraďte jeho obsah následujícím kódem:

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

1. Přejděte na *<app root>/views/Shared* a otevřete *_Layout. cshtml*. Nahraďte jeho obsah následujícím kódem:

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

1. Pokud chcete aplikaci vytvořit pomocí .NET Core CLI, přejděte do kořenového adresáře aplikace a spusťte v příkazovém prostředí následující příkaz:

    ```dotnetcli
    dotnet build
    ```

1. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění webové aplikace:

    ```dotnetcli
    dotnet run
    ```

1. Pokud pracujete na místním počítači, přejděte k `http://localhost:5000`pomocí prohlížeče. Toto je výchozí adresa URL webové aplikace hostované na místním počítači.  

Pokud pracujete na Azure Cloud Shell, klikněte na tlačítko *Náhled webu* a potom na *Konfigurovat*.  

![Najít tlačítko Náhled webu](./media/quickstarts/cloud-shell-web-preview.png)

Po zobrazení výzvy ke konfiguraci portu pro verzi Preview zadejte ' 5000 ' a vyberte *otevřít a procházet*.  Webová stránka načte data z konfigurace aplikace Azure.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nové úložiště konfigurace aplikace a použili ho s ASP.NET Core webovou aplikací prostřednictvím [poskytovatele konfigurace aplikace](https://go.microsoft.com/fwlink/?linkid=2074664). Další informace o tom, jak nakonfigurovat aplikaci ASP.NET Core, aby dynamicky aktualizovala nastavení konfigurace, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Povolit dynamickou konfiguraci](./enable-dynamic-configuration-aspnet-core.md)

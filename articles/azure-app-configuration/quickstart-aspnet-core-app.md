---
title: Rychlý Start pro konfiguraci aplikací Azure s ASP.NET Core | Microsoft Docs
description: Vytvoření aplikace ASP.NET Core s využitím konfigurace aplikace Azure k centralizaci úložiště a správy nastavení aplikace pro ASP.NET Core aplikace.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp, contperfq1
ms.topic: quickstart
ms.date: 09/25/2020
ms.author: lcozzens
ms.openlocfilehash: dc886ae2e113b77a6c49219a61a905aaeeb7d2a5
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91372323"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Rychlý Start: Vytvoření aplikace ASP.NET Core s využitím konfigurace aplikace Azure

V tomto rychlém startu použijete Azure App Configuration k centralizaci úložiště a správy nastavení aplikace pro ASP.NET Core aplikaci. ASP.NET Core vytvoří jeden objekt konfigurace založený na hodnotě klíč-hodnota pomocí nastavení z jednoho nebo více zdrojů dat určených aplikací. Tyto zdroje dat jsou známé jako *poskytovatelé konfigurace*. Vzhledem k tomu, že klient .NET Core konfigurace aplikace je implementovaný jako poskytovatel konfigurace, služba se zobrazí jako jiný zdroj dat.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/dotnet) .
* [Sada .NET Core SDK](https://dotnet.microsoft.com/download)

> [!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění instrukcí příkazového řádku v tomto článku. Má předinstalované běžné nástroje Azure, včetně .NET Core SDK. Pokud jste přihlášeni ke svému předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com. Další informace o Azure Cloud Shell najdete v [naší dokumentaci](../cloud-shell/overview.md) .

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

7. Vyberte **Operations**  >  **Explorer Configuration Explorer**  >  **vytvořit**  >  **klíč-hodnota** a přidejte následující páry klíč-hodnota:

    | Klíč                                | Hodnota                               |
    |------------------------------------|-------------------------------------|
    | `TestApp:Settings:BackgroundColor` | *#FFF*                              |
    | `TestApp:Settings:FontColor`       | *#000*                              |
    | `TestApp:Settings:FontSize`        | *24*                                |
    | `TestApp:Settings:Message`         | *Data z konfigurace aplikace Azure* |

    V tuto chvíli ponechat **popisek** a **typ obsahu** prázdné. Vyberte **Použít**.

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

K vytvoření nového projektu ASP.NET Core MVC použijte [rozhraní příkazového řádku .NET Core (CLI)](/dotnet/core/tools) . [Azure Cloud Shell](https://shell.azure.com) poskytuje tyto nástroje za vás. Jsou k dispozici také na platformách Windows, macOS a Linux.

Spuštěním následujícího příkazu vytvořte projekt ASP.NET Core MVC v nové složce *TestAppConfig* :

```dotnetcli
dotnet new mvc --no-https --output TestAppConfig
```

## <a name="add-secret-manager"></a>Přidat správce tajných klíčů

Nástroj s názvem správce tajných dat uchovává citlivá data pro vývojovou práci mimo strom projektu. Tento přístup pomáhá zabránit náhodnému sdílení tajných kódů aplikace ve zdrojovém kódu. Provedením následujících kroků povolíte použití Správce tajných klíčů v projektu:

#### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

Spusťte následující příkaz, který povolí tajné úložiště v projektu:

```dotnetcli
dotnet user-secrets init
```

`UserSecretsId`Do souboru *. csproj* se přidá element obsahující identifikátor GUID:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

1. Otevřete soubor *. csproj* .

1. Přidejte `UserSecretsId` element do souboru *. csproj* , jak je znázorněno zde. Můžete použít stejný identifikátor GUID, nebo můžete tuto hodnotu nahradit vlastními.

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
    
1. Uložte soubor *. csproj* .

---

> [!TIP]
> Další informace o Správci tajných klíčů najdete v tématu [bezpečné úložiště tajných kódů aplikací ve vývoji v ASP.NET Core](/aspnet/core/security/app-secrets).

## <a name="connect-to-the-app-configuration-store"></a>Připojení k úložišti konfigurace aplikace

1. Spuštěním následujícího příkazu přidejte odkaz na balíček NuGet [Microsoft. Azure. AppConfiguration. AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) :

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Spusťte následující příkaz ve stejném adresáři jako soubor *. csproj* . Příkaz používá správce tajných klíčů k ukládání tajného kódu s názvem `ConnectionStrings:AppConfig` , který ukládá připojovací řetězec pro úložiště konfigurace aplikace. `<your_connection_string>`Zástupný symbol nahraďte připojovacím řetězcem konfiguračního úložiště aplikace. Připojovací řetězec najdete v části **přístupové klíče** v Azure Portal.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    > [!IMPORTANT]
    > Některá prostředí zkrátí připojovací řetězec, pokud není uzavřen v uvozovkách. Ujistěte se, že výstup `dotnet user-secrets` příkazu zobrazuje celý připojovací řetězec. Pokud tomu tak není, spusťte příkaz znovu a uzavřete připojovací řetězec do uvozovek.

    Správce tajného klíče se používá jenom k místnímu testování webové aplikace. Když se aplikace nasadí do [Azure App Service](https://azure.microsoft.com/services/app-service/web), použijte nastavení aplikace **připojovací řetězce** v App Service namísto správce tajných klíčů k uložení připojovacího řetězce.

    Přístup k tomuto tajnému kódu pomocí konfiguračního rozhraní API .NET Core. Dvojtečka ( `:` ) funguje v názvu konfigurace s rozhraním API konfigurace na všech podporovaných platformách. Další informace najdete v tématu [konfigurační klíče a hodnoty](/aspnet/core/fundamentals/configuration#configuration-keys-and-values).

1. Do *program.cs*přidejte odkaz na obor názvů rozhraní .NET Core Configuration API:

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

1. Aktualizujte `CreateWebHostBuilder` metodu pro použití konfigurace aplikace voláním `AddAzureAppConfiguration` metody.

    > [!IMPORTANT]
    > `CreateHostBuilder` nahrazuje `CreateWebHostBuilder` v .NET Core 3. x. Vyberte správnou syntaxi na základě vašeho prostředí.

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(connection);
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                var connection = settings.GetConnectionString("AppConfig");
                config.AddAzureAppConfiguration(connection);
            })
            .UseStartup<Startup>();
    ```

    ---

    V předchozí změně byl [Poskytovatel konfigurace pro konfiguraci aplikace](https://go.microsoft.com/fwlink/?linkid=2074664) zaregistrován s rozhraním .NET Core Configuration API.

## <a name="read-from-the-app-configuration-store"></a>Čtení z úložiště konfigurace aplikace

Provedením následujících kroků přečtete a zobrazíte hodnoty uložené v úložišti konfigurace aplikace. Rozhraní .NET Core Configuration API se bude používat pro přístup k úložišti. Pro zobrazení hodnot klíčů se použije syntaxe Razor.

Otevřete * \<app root> /views/Home/index.cshtml*a nahraďte jeho obsah následujícím kódem:

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<style>
    body {
        background-color: @Configuration["TestApp:Settings:BackgroundColor"]
    }
    h1 {
        color: @Configuration["TestApp:Settings:FontColor"];
        font-size: @Configuration["TestApp:Settings:FontSize"]px;
    }
</style>

<h1>@Configuration["TestApp:Settings:Message"]</h1>
```

V předchozím kódu se používají klíče úložiště konfigurace aplikace následujícím způsobem:

* `TestApp:Settings:BackgroundColor`Hodnota klíče je přiřazena `background-color` Vlastnosti CSS.
* `TestApp:Settings:FontColor`Hodnota klíče je přiřazena `color` Vlastnosti CSS.
* `TestApp:Settings:FontSize`Hodnota klíče je přiřazena `font-size` Vlastnosti CSS.
* `TestApp:Settings:Message`Hodnota klíče se zobrazí jako záhlaví.

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Pokud chcete aplikaci vytvořit pomocí .NET Core CLI, přejděte do kořenového adresáře vašeho projektu. V příkazovém prostředí spusťte následující příkaz:

    ```dotnetcli
    dotnet build
    ```

1. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění webové aplikace:

    ```dotnetcli
    dotnet run
    ```

1. Pokud pracujete na místním počítači, přejděte k nástroji pomocí prohlížeče `http://localhost:5000` . Tato adresa je výchozí adresou URL pro místně hostovanou webovou aplikaci. Pokud pracujete na Azure Cloud Shell, klikněte na tlačítko **Náhled webu** a potom na **Konfigurovat**.

    ![Najít tlačítko Náhled webu](./media/quickstarts/cloud-shell-web-preview.png)

    Po zobrazení výzvy ke konfiguraci portu pro verzi Preview zadejte *5000* a vyberte **otevřít a procházet**. Webová stránka načte data z konfigurace aplikace Azure.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu:

* Zřizuje se nové úložiště konfigurace aplikace.
* Zaregistroval se poskytovatel konfigurace .NET Core úložiště konfigurace aplikace.
* Přečtěte si klíče pro úložiště konfigurace aplikace pomocí poskytovatele konfigurace.
* Zobrazily se klíčové hodnoty úložiště konfigurace aplikace pomocí syntaxe Razor.

Další informace o tom, jak nakonfigurovat aplikaci ASP.NET Core, aby dynamicky aktualizovala nastavení konfigurace, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Povolení dynamické konfigurace](./enable-dynamic-configuration-aspnet-core.md)

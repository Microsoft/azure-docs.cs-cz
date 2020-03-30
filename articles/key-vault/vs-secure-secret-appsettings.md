---
title: Bezpečné ukládání tajných nastavení aplikací pro webovou aplikaci – Azure Key Vault | Dokumenty společnosti Microsoft
description: Jak bezpečně uložit nastavení tajných aplikací, jako jsou pověření Azure nebo klíče rozhraní API třetích stran pomocí ASP.NET základního zprostředkovatele úložiště klíčů, tajného uživatele nebo tvůrce konfigurace .NET 4.7.1
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: cawa
ms.openlocfilehash: 6bbadd0deb1b7b6fe1056ed7bb47dc3e666a7b9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197704"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Bezpečné uložení tajných nastavení aplikace pro webovou aplikaci

## <a name="overview"></a>Přehled
Tento článek popisuje, jak bezpečně uložit nastavení konfigurace tajných aplikací pro aplikace Azure.

Všechna nastavení konfigurace webových aplikací jsou tradičně uložena v konfiguračních souborech, jako je web.config. Tento postup vede ke kontrole v tajných nastaveních, jako jsou přihlašovací údaje cloudu do systémů veřejné správy zdrojového kódu, jako je GitHub. Mezitím může být obtížné dodržovat osvědčené postupy zabezpečení z důvodu režie potřebné ke změně zdrojového kódu a překonfigurovat nastavení vývoje.

Chcete-li se ujistit, že proces vývoje je bezpečný, nástroje a framework knihovny jsou vytvořeny pro uložení nastavení tajných barev aplikace bezpečně s minimální nebo žádnou změnu zdrojového kódu.

## <a name="aspnet-and-net-core-applications"></a>ASP.NET a .NET Core aplikace

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Uložit tajné nastavení v úložišti uživatelských tajných kódů, které je mimo složku správy zdrojového kódu
Pokud provádíte rychlý prototyp nebo nemáte přístup k internetu, začněte přesunutím tajných nastavení mimo složku správy zdrojového kódu do úložiště User Secret. Úložiště uživatelských tajných kódů je soubor uložený ve složce profileru uživatele, takže tajné klíče nejsou se změnami do správy zdrojového kódu. Následující diagram ukazuje, jak funguje [tajný klíč uživatele.](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio)

![User Secret udržuje tajné nastavení mimo slučování zdrojů](./media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Pokud používáte aplikaci konzoly jádra .NET, bezpečně uložte tajný klíč pomocí trezoru klíčů.

### <a name="save-secret-settings-in-azure-key-vault"></a>Uložení tajných nastavení v trezoru klíčů Azure
Pokud vyvíjíte projekt a potřebujete bezpečně sdílet zdrojový kód, použijte [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Vytvořte trezor klíčů ve svém předplatném Azure. Vyplňte všechna povinná pole v ui a klikněte na *Vytvořit* na spodní straně okna

    ![Vytvoření trezoru klíčů Azure](./media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Udělte vám a vašim členům týmu přístup do trezoru klíčů. Pokud máte velký tým, můžete vytvořit [skupinu Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md) a přidat přístup skupiny zabezpečení do trezoru klíčů. V rozevíracím seznamu *Tajná oprávnění* zaškrtněte *políčko Získat* a *Seznam* v části Operace *tajné správy*.
Pokud už máte vytvořenou webovou aplikaci, udělte webové aplikaci přístup k trezoru klíčů, aby měla přístup k trezoru klíčů bez ukládání tajné konfigurace v nastavení aplikace nebo v souborech. Vyhledejte webovou aplikaci podle jejího názvu a přidejte ji stejným způsobem, jakým uživatelům udělíte přístup.

    ![Přidat zásady přístupu trezoru klíčů](./media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Přidejte svůj tajný klíč do trezoru klíčů na webu Azure Portal. Pro vnořené nastavení konfigurace nahraďte ":" slovem '--', aby byl tajný název trezoru klíčů platný. ':' nesmí být v názvu tajného klíče trezoru klíčů.

    ![Přidat tajný klíč trezoru klíčů](./media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE]
    > Před Visual Studio 2017 V15.6 jsme doporučili nainstalovat rozšíření Ověřování služeb Azure pro Visual Studio. Ale je nyní zastaralé jako funkce je integrován do sady Visual Studio . Pokud tedy používáte starší verzi visual studia 2017 , doporučujeme aktualizovat alespoň Na VS 2017 15.6 nebo novější, abyste mohli tuto funkci používat nativně a přistupovat k trezoru klíčů pomocí samotné přihlašovací identity Visual Studia.
    >

4. Přidejte do projektu následující balíčky NuGet:

    ```
    Microsoft.Azure.KeyVault
    Microsoft.Azure.Services.AppAuthentication
    Microsoft.Extensions.Configuration.AzureKeyVault
    ```
5. Do Program.cs souboru přidejte následující kód:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
             Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((ctx, builder) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        builder.AddAzureKeyVault(
                        keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
6. Přidejte adresu URL trezoru klíčů do souboru launchsettings.json. Název proměnné prostředí *KEYVAULT_ENDPOINT* je definován v kódu, který jste přidali v kroku 6.

    ![Přidat adresu URL úložiště klíčů jako proměnnou prostředí projektu](./media/vs-secure-secret-appsettings/add-keyvault-url.png)

7. Spusťte ladění projektu. Měl by být úspěšně spuštěn.

## <a name="aspnet-and-net-applications"></a>aplikace ASP.NET a .NET

.NET 4.7.1 podporuje key vault a tajné konfigurace tvůrci, který zajišťuje, že tajné klíče lze přesunout mimo složku správy zdrojového kódu bez změny kódu.
Chcete-li pokračovat, [stáhněte si rozhraní .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) a migrujte aplikaci, pokud používá starší verzi rozhraní .NET Framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Uložení tajných nastavení do tajného souboru, který je mimo složku správy zdrojového kódu
Pokud píšete rychlý prototyp a nechcete zřídit prostředky Azure, přejděte k této možnosti.

1. Nainstalujte do projektu následující balíček NuGet
    ```
    Microsoft.Configuration.ConfigurationBuilders.Base
    ```

2. Vytvořte soubor, který je podobný následujícímu. Uložte jej pod umístěním mimo složku projektu.

    ```xml
    <root>
        <secrets ver="1.0">
            <secret name="secret1" value="foo_one" />
            <secret name="secret2" value="foo_two" />
        </secrets>
    </root>
    ```

3. V souboru Web.config definujte tajný soubor jako tvůrce konfigurace. Tuto část předložte části *nastavení aplikace.*

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Zadejte appSettings sekce používá tvůrce tajných konfigurací. Ujistěte se, že je položka pro nastavení tajného klíče s fiktivní hodnotou.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Ladění aplikace. Měl by být úspěšně spuštěn.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Uložení tajných nastavení do trezoru klíčů Azure
Podle pokynů z ASP.NET základní části nakonfigurujte trezor klíčů pro váš projekt.

1. Nainstalujte do projektu následující balíček NuGet
   ```
   Microsoft.Configuration.ConfigurationBuilders.UserSecrets
   ```

2. Definujte tvůrce konfigurace úložiště klíčů v souboru Web.config. Tuto část předložte části *nastavení aplikace.* Nahradit *vaultName* být název trezoru klíčů, pokud váš trezor klíčů je ve veřejném Azure nebo úplné URI, pokud používáte cloud Sovereign.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>
    <configBuilders>
        <builders>
            <add name="AzureKeyVault" vaultName="Test911" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```
3. Zadejte, aby se část appSettings používala tvůrce konfigurace trezoru klíčů. Ujistěte se, že existuje nějaká položka pro nastavení tajného klíče s hodnotou figuríny.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. Spusťte ladění projektu. Měl by být úspěšně spuštěn.

---
title: Nastavení tajný aplikace pro webovou aplikaci bezpečně ukládání | Microsoft Docs
description: Jak bezpečně uložit nastavení tajný aplikace, jako je například přihlašovací údaje Azure nebo třetích stran rozhraní API pro klíče pomocí ASP.NET základní zprostředkovatel úložiště klíčů, tajného klíče uživatele nebo .NET 4.7.1 konfigurace počítačů
services: visualstudio
documentationcenter: ''
author: cawa
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: ''
ms.workload: web, azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cawa
ms.openlocfilehash: 61cb9fadd60ba35f11e2f3ade94bc647fac9ed72
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235919"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Bezpečně uložte nastavení tajný aplikace pro webovou aplikaci

## <a name="overview"></a>Přehled
Tento článek popisuje, jak bezpečně uložit nastavení konfigurace tajný aplikace pro aplikace Azure.

Tradičně všechny webové konfigurace aplikace, které nastavení se ukládají v konfiguračních souborech, jako je soubor Web.config. Tento postup vede k vrácení se změnami tajný nastavení, jako například cloudu přihlašovací údaje pro veřejné zdrojových systémů řízení jako Github. Mezitím může být obtížné podle z hlediska zabezpečení z důvodu zatížení potřebné ke změně zdrojového kódu a znovu nakonfigurovat nastavení pro vývoj.

Pokud chcete mít jistotu, že je bezpečné vývoje, nástrojů a framework knihovny vytvářejí se uložit nastavení tajný aplikace bezpečně s minimální nebo žádná změna zdrojového kódu.

## <a name="aspnet-and-net-core-applications"></a>Aplikace ASP.NET a rozhraní .NET

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Uložit nastavení tajný v úložišti tajného klíče uživatele, který je mimo složku Zdroj ovládacího prvku
Pokud byste rychlé prototypu nebo nemáte přístup k Internetu, začněte přesunutí nastavení tajný mimo zdrojové složce ovládacích prvků do úložiště tajného klíče uživatele. Uživatel tajný úložiště je soubor uložený ve složce profileru uživatele, takže tajné klíče se změnami do správy zdrojového kódu. Následující diagram ukazuje, jak [tajného klíče uživatele](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio#SecretManager) funguje.

![Tajného klíče uživatele zachová tajný nastavení mimo zdrojového kódu](./media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Pokud používáte aplikace konzoly .NET core, použijte Key Vault bezpečně uložit váš tajný klíč.

### <a name="save-secret-settings-in-azure-key-vault"></a>Uložit nastavení tajný v Azure Key Vault
Pokud jsou vývoj týmového projektu a potřebují bezpečně sdílet zdrojový kód, použijte [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Vytvoření trezoru klíč ve vašem předplatném Azure. Vyplňte všechna povinná pole na uživatelského rozhraní a klikněte na *vytvořit* v dolní části okna

    ![Vytvoření Azure Key Vault](./media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Udělení jste a členové týmu přístup do služby Key Vault. Pokud máte velké tým, můžete vytvořit [skupiny Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal) a přidejte skupiny zabezpečení přístup do služby Key Vault. V *tajný klíč oprávnění* rozevíracího seznamu, zkontrolujte *získat* a *seznamu* pod *operace správy tajný klíč*.

    ![Přidání zásady přístupu Key Vault](./media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Na portálu Azure přidejte váš tajný klíč pro Key Vault. Pro nastavení vnořených konfigurace, nahraďte ':' s ' – ', je platný název tajný klíč trezoru. ':' nesmí být název Key Vault tajný.

    ![Přidat tajný klíč Key Vault](./media/vs-secure-secret-appsettings/add-keyvault-secret.png)

4. Nainstalujte [rozšíření ověřování služby Azure pro sadu Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354). Prostřednictvím tohoto rozšíření přístup k aplikaci Key Vault pomocí sady Visual Studio přihlášení identity.

5. Přidejte následující balíčky NuGet do projektu:

    ```
    Microsoft.Azure.Services.AppAuthentication
    ```
6. Do souboru Program.cs přidejte následující kód:

    ```csharp
    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
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
                    .UseStartup<Startup>()
                    .Build();

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
7. Přidáte adresu URL trezoru klíč do souboru launchsettings.json. Název proměnné prostředí *KEYVAULT_ENDPOINT* je definována v kód, který jste přidali v kroku 6.

    ![Adresa URL trezoru klíčů přidat jako proměnnou prostředí projektu](./media/vs-secure-secret-appsettings/add-keyvault-url.png)

8. Spusťte ladění projektu. Má být spuštěn úspěšně.

## <a name="aspnet-and-net-applications"></a>Aplikací ASP.NET a rozhraní .NET

Rozhraní .NET 4.7.1 podporuje Key Vault a tajný klíč konfigurace počítačů, což zajistí, že tajných klíčů lze přesunout mimo zdrojové složce ovládacích prvků bez změny kódu.
Chcete-li pokračovat, [stáhnout .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) a migrovat aplikace, pokud používá starší verzi rozhraní .NET framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Uložit nastavení tajný v tajný soubor, který je mimo složku Zdroj ovládacího prvku
Pokud jsou psaní rychlé prototypu a nechcete použít ke zřízení prostředků Azure, přejděte pomocí této možnosti.

1. Nainstalujte následující balíček NuGet do projektu
    ```
    Microsoft.Configuration.ConfigurationBuilders.Basic.1.0.0-alpha1.nupkg
    ```

2. Vytvořte soubor, který je podobný těchto kroků. Uložte pod umístění mimo složku vašeho projektu.

    ```xml

       <root>
              <secrets ver="1.0">
                     <secret name="secret1" value="foo_one" />
                        <secret name="secret2" value="foo_two" />
                       </secrets>
      </root>
      ```

3. Zadejte tajný soubor jako tvůrce konfigurace v souboru Web.config. PUT v této části před *appSettings* části.

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Zadejte, že oddíl appSettings je pomocí Tvůrce tajný konfigurace. Ujistěte se, že všechny položky pro nastavení tajný s fiktivní hodnoty.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Ladění aplikace. Má být spuštěn úspěšně.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Uložit nastavení tajný v Azure Key Vault
Postupujte podle pokynů z oddílu ASP.NET základní konfigurace Key Vault pro váš projekt.

1. Nainstalujte následující balíček NuGet do projektu
```
Microsoft.Configuration.ConfigurationBuilders.UserSecrets.1.0.0-preview2.nupkg
```

2. Definujte Key Vault konfigurace tvůrce v souboru Web.config. PUT v této části před *appSettings* části. Nahraďte *vaultName* byl název trezoru klíč, pokud Key Vault je ve veřejné Azure, nebo úplný identifikátor URI, pokud používáte svrchovaných cloudu.

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
3.  Zadejte, že oddíl appSettings je pomocí Tvůrce konfigurace Key Vault. Ujistěte se, že všechny položky pro nastavení tajný s fiktivní hodnoty.

    ```xml
    <appSettings configBuilders="AzureKeyVault">
        <add key="webpages:Version" value="3.0.0.0" />
        <add key="webpages:Enabled" value="false" />
        <add key="ClientValidationEnabled" value="true" />
        <add key="UnobtrusiveJavaScriptEnabled" value="true" />
        <add key="secret" value="" />
    </appSettings>
    ```

4. Spusťte ladění projektu. Má být spuštěn úspěšně.

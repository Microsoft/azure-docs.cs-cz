---
title: Bezpečné ukládání tajných kódů aplikace nastavení pro webovou aplikaci | Dokumentace Microsoftu
description: Jak bezpečně uložit nastavení tajných kódů aplikace jako přihlašovací údaje Azure nebo třetích stran rozhraní API klíče pomocí ASP.NET core zprostředkovatele trezor klíčů, tajného klíče uživatele nebo .NET 4.7.1 konfigurace počítačů
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
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: cawa
ms.openlocfilehash: 1635d0fa51fb56f30dc7cf5864e49000d30cc25d
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719779"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Bezpečně uložte nastavení tajných kódů aplikace pro webovou aplikaci

## <a name="overview"></a>Přehled
Tento článek popisuje, jak bezpečně uložit nastavení konfigurace tajných kódů aplikace pro aplikace Azure.

Tradičně všechny webové konfigurace aplikace, které nastavení se ukládají v konfiguračních souborech, jako je například Web.config. Tento postup vede k vrácení se změnami nastavení tajných kódů, jako je například přihlašovací údaje ke cloudu pro systémy správy veřejné zdrojového kódu jako Github. Mezitím může být obtížné sledovat z hlediska zabezpečení z důvodu režie nutné změnit zdrojový kód a znovu nakonfigurovat nastavení pro vývoj.

Pokud chcete mít jistotu, že proces vývoje je zabezpečené, nástroje a rozhraní knihovny vytvářejí se uložit nastavení tajných kódů aplikace bezpečně s minimální nebo žádné změny zdrojového kódu.

## <a name="aspnet-and-net-core-applications"></a>Aplikace ASP.NET a .NET core

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Uložit nastavení tajných kódů v úložišti tajný klíč uživatele, které je mimo složku správy zdrojového kódu
Pokud provádíte rychlé prototyp nebo nemáte přístup k Internetu, začněte s přesunem vašich nastavení tajných kódů mimo složku správy zdrojového kódu do úložiště tajného klíče uživatele. Úložiště tajných kódů uživatelů je soubor uložený ve složce profiler uživatele, tak tajné kódy nejsou vrácené se změnami do správy zdrojového kódu. Následující diagram ukazuje, jak [tajného klíče uživatele](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio#SecretManager) funguje.

![Tajný kód uživatele zachová nastavení tajných kódů mimo správy zdrojového kódu](./media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Pokud spustíte konzolovou aplikaci .NET core, použijte Key Vault tajný klíč bezpečně uložit.

### <a name="save-secret-settings-in-azure-key-vault"></a>Uložit nastavení tajných kódů ve službě Azure Key Vault
Pokud vyvíjíte projekt a potřebujete bezpečně sdílet zdrojový kód, použijte [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Vytvořte službu Key Vault ve vašem předplatném Azure. Vyplňte všechna povinná pole na uživatelské rozhraní a klikněte na *vytvořit* v dolní části okna

    ![Vytvoření služby Azure Key Vault](./media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Vy a vaši členové týmu přístup ke službě Key Vault udělit. Pokud máte velký tým, můžete vytvořit [skupiny Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal) a přidejte skupiny zabezpečení přístup ke službě Key Vault. V *tajný kód oprávnění* rozevíracího seznamu, zkontrolujte *získat* a *seznamu* pod *operace správy tajný klíč*.

    ![Přidat zásady přístupu trezoru klíčů](./media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Přidáte tajný klíč do služby Key Vault na portálu Azure portal. Vnořené konfigurační nastavení, nahraďte ":" s "--" tak, že je platný název tajného kódu Key Vault. ":" není povolený jako název služby Key Vault tajného kódu.

    ![Přidání tajného kódu trezoru klíčů](./media/vs-secure-secret-appsettings/add-keyvault-secret.png)

4. Nainstalujte [rozšíření ověřování služeb Azure pro Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354). Prostřednictvím tohoto rozšíření aplikace mít přístup ke službě Key Vault pomocí sady Visual Studio přihlášení identity.

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
7. Přidejte do souboru launchsettings.json vaše adresa URL trezoru klíčů. Název proměnné prostředí *KEYVAULT_ENDPOINT* je definováno v kódu, který jste přidali v kroku 6.

    ![Přidat jako proměnnou prostředí projektu adresa URL trezoru klíčů](./media/vs-secure-secret-appsettings/add-keyvault-url.png)

8. Spusťte ladění projektu. By měl běžet úspěšně.

## <a name="aspnet-and-net-applications"></a>Aplikace ASP.NET a .NET

.NET 4.7.1 podporuje tvůrci konfigurace služby Key Vault a tajný klíč, který zajistí, že tajné kódy je možné přesunout mimo složku správy zdrojového kódu bez jakýchkoli změn kódu.
Chcete-li pokračovat, [stáhnout .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) a migrujte své aplikace, pokud aplikace používá starší verzi rozhraní .NET framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Uložit nastavení tajných kódů v souboru tajného kódu, který je mimo složku správy zdrojového kódu
Pokud píšete rychlé prototypu a nechcete, aby ke zřízení prostředků Azure, přejděte pomocí této možnosti.

1. Nainstalujte si do projektu následující balíček NuGet
    ```
    Microsoft.Configuration.ConfigurationBuilders.Basic.1.0.0-alpha1.nupkg
    ```

2. Vytvořte soubor, který se podobá následující. Uložte ho umístění mimo složku projektu.

    ```xml
    <root>
        <secrets ver="1.0">
            <secret name="secret1" value="foo_one" />
            <secret name="secret2" value="foo_two" />
        </secrets>
    </root>
    ```

3. Definujte soubor tajného kódu být Tvůrce konfigurace v souboru Web.config. Vložit tuto část před syntaxí *appSettings* oddílu.

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. Zadejte sekci appSettings je pomocí Tvůrce konfigurace tajného kódu. Ujistěte se, že není k dispozici žádné položky pro nastavení tajných kódů pomocí fiktivní hodnoty.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Ladění aplikace. By měl běžet úspěšně.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Uložit nastavení tajných kódů v Azure Key Vault
Postupujte podle pokynů v části ASP.NET core a nakonfigurovat služby Key Vault pro váš projekt.

1. Nainstalujte si do projektu následující balíček NuGet
```
Microsoft.Configuration.ConfigurationBuilders.UserSecrets.1.0.0-preview2.nupkg
```

2. Tvůrce konfigurace služby Key Vault se definují v souboru Web.config. Vložit tuto část před syntaxí *appSettings* oddílu. Nahraďte *vaultName* být názvem služby Key Vault, pokud Key Vault ve veřejné Azure nebo úplný identifikátor URI, pokud používáte suverénních cloudů.

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
3.  Zadejte sekci appSettings je pomocí Tvůrce konfigurace služby Key Vault. Ujistěte se, že není k dispozici žádné položky pro nastavení tajných kódů pomocí fiktivní hodnoty.

    ```xml
    <appSettings configBuilders="AzureKeyVault">
        <add key="webpages:Version" value="3.0.0.0" />
        <add key="webpages:Enabled" value="false" />
        <add key="ClientValidationEnabled" value="true" />
        <add key="UnobtrusiveJavaScriptEnabled" value="true" />
        <add key="secret" value="" />
    </appSettings>
    ```

4. Spusťte ladění projektu. By měl běžet úspěšně.

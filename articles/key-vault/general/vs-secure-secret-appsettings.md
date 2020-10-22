---
title: Bezpečné uložení nastavení tajné aplikace pro webovou aplikaci – Azure Key Vault | Microsoft Docs
description: Jak bezpečně ukládat nastavení tajných aplikací, jako jsou přihlašovací údaje Azure nebo klíče rozhraní API třetích stran, pomocí ASP.NET Core Key Vault poskytovatele, tajného uživatele nebo tvůrců konfigurace .NET 4.7.1
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 07/17/2019
ms.author: cawa
ms.custom: devx-track-csharp
ms.openlocfilehash: 96b6b262765a361befeadd9b5a42d37ca5e66497
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372051"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Bezpečně uložit nastavení tajné aplikace pro webovou aplikaci

## <a name="overview"></a>Přehled
Tento článek popisuje, jak bezpečně uložit nastavení konfigurace tajných aplikací pro aplikace Azure.

Všechna nastavení konfigurace webové aplikace se tradičně ukládají do konfiguračních souborů, jako je Web.config. Tento postup vede k vrácení nastavení tajného klíče, jako jsou například přihlašovací údaje cloudu, do veřejných systémů správy zdrojového kódu, jako je GitHub. V obou případech by mohlo být obtížné podléhat osvědčeným postupům zabezpečení z důvodu režie nutné ke změně zdrojového kódu a překonfigurování nastavení vývoje.

Chcete-li zajistit, aby byl proces vývoje zabezpečený, jsou vytvořeny knihovny nástrojů a architektury pro bezpečné uložení nastavení tajného klíče aplikace s minimální nebo žádnou změnou zdrojového kódu.

## <a name="aspnet-and-net-core-applications"></a>ASP.NET a .NET Core – aplikace

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Uložit nastavení tajného klíče v úložišti tajného uživatele, které je mimo složku správy zdrojového kódu
Pokud provádíte rychlý prototyp nebo nemáte přístup k Internetu, začněte s přesunutím nastavení tajného klíče mimo složku správy zdrojového kódu do úložiště tajného klíče uživatele. Úložiště tajného uživatele je soubor uložený ve složce Profiler uživatele, takže tajné klíče nejsou vráceny se změnami do správy zdrojového kódu. Následující obrázek ukazuje, jak funguje [tajný klíč uživatele](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio) .

![Tajný klíč uživatele uchovává nastavení tajného klíče mimo správu zdrojového kódu.](../media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Pokud používáte konzolovou aplikaci .NET Core, použijte Key Vault k bezpečnému uložení tajného kódu.

### <a name="save-secret-settings-in-azure-key-vault"></a>Uložit nastavení tajného klíče v Azure Key Vault
Pokud vyvíjíte projekt a potřebujete bezpečně sdílet zdrojový kód, použijte [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Vytvořte Key Vault v předplatném Azure. Vyplňte všechna povinná pole v uživatelském rozhraní a klikněte na *vytvořit* v dolní části okna.

    ![Vytvořit Azure Key Vault](../media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Udělte vám a členům vašeho týmu přístup k Key Vault. Máte-li velký tým, můžete vytvořit [skupinu Azure Active Directory](../../active-directory/active-directory-groups-create-azure-portal.md) a přidat tuto skupinu zabezpečení k Key Vault. V rozevíracím seznamu *oprávnění tajného klíče* zaškrtněte *získat* a *seznam* v části *operace správy tajných*kódů.
Pokud již máte vytvořenou webovou aplikaci, udělte webové aplikaci přístup k Key Vault, aby mohla přistupovat k trezoru klíčů bez uložení konfigurace tajného klíče do nastavení aplikace nebo souborů. Vyhledejte svou webovou aplikaci podle jejího názvu a přidejte ji stejným způsobem, jakým udělíte přístup uživatelům.

    ![Přidat zásady přístupu Key Vault](../media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Do Azure Portal Key Vault přidejte svůj tajný kód. Pro vnořená nastavení konfigurace nahraďte ': ' '--', aby byl název Key Vault tajného klíče platný. hodnota ': ' nesmí být v názvu Key Vault tajného klíče.

    ![Přidat Key Vault tajný klíč](../media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE]
    > Před verzí sady Visual Studio 2017 V 15.6 jsme použili k doporučení instalace rozšíření pro ověřování služeb Azure pro Visual Studio. Je ale teď zastaralá, protože je funkce integrovaná v rámci sady Visual Studio. Proto, pokud jste na starší verzi sady Visual Studio 2017, doporučujeme, abyste aktualizovali aspoň na 2017 15,6 nebo nahoru, abyste mohli tuto funkci používat nativně a přistupovat k trezoru klíčů pomocí samotné identity sady Visual Studio.
    >

4. Přihlaste se k Azure pomocí rozhraní příkazového řádku, můžete zadat:

    ```azurecli
    az login
    ```

5. Do projektu přidejte následující balíčky NuGet:

    ```
    Azure.Identity
    Azure.Extensions.AspNetCore.Configuration.Secrets
    ```
6. Do souboru Program.cs přidejte následující kód:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
             Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((ctx, builder) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        builder.AddAzureKeyVault(new Uri(keyVaultEndpoint), new DefaultAzureCredential(), new KeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```

7. Přidejte adresu URL Key Vault pro launchsettings.jssouboru. Název proměnné prostředí *KEYVAULT_ENDPOINT* je definován v kódu, který jste přidali v kroku 7.

    ![Přidat adresu URL Key Vault jako proměnnou prostředí projektu](../media/vs-secure-secret-appsettings/add-keyvault-url.png)

8. Spusťte ladění projektu. Mělo by se úspěšně spustit.

## <a name="aspnet-and-net-applications"></a>ASP.NET a aplikace .NET

.NET 4.7.1 podporuje sestavování konfigurací Key Vault a tajných kódů, které zajistí, aby tajné klíče bylo možné přesunout mimo složku správy zdrojového kódu bez změny kódu.
Pokud chcete pokračovat, [Stáhněte si .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) a migrujte svou aplikaci, pokud používá starší verzi rozhraní .NET Framework.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Uložit nastavení tajného klíče do tajného souboru, který je mimo složku správy zdrojového kódu
Pokud píšete rychlý prototyp a nechcete zřizovat prostředky Azure, Projděte si tuto možnost.

1. Klikněte pravým tlačítkem na projekt a vyberte **Správa uživatelských tajných klíčů**. Tím se nainstaluje balíček NuGet **Microsoft.Configuration.ConfigurationBuilders. UserSecrets** , vytvořte soubor pro uložení nastavení tajného klíče mimo web.config soubor a přidejte do souboru web.config oddíl **ConfigBuilders** .

2. Vložte nastavení tajného klíče do kořenového elementu. Níže je příklad

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <root>
      <secrets ver="1.0">
        <secret name="secret" value="foo"/>
        <secret name="secret1" value="foo_one" />
        <secret name="secret2" value="foo_two" />
      </secrets>
    </root>
    ```

3. Část určení appSettings používá tvůrce konfigurace tajného klíče. Ujistěte se, že existuje položka pro nastavení tajného klíče se fiktivní hodnotou.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Ladit aplikaci. Mělo by se úspěšně spustit.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Uložení nastavení tajného klíče v Azure Key Vault
Postupujte podle pokynů v části ASP.NET Core a nakonfigurujte Key Vault pro svůj projekt.

1. Do projektu nainstalujte následující balíček NuGet
   ```
   Microsoft.Configuration.ConfigurationBuilders.Azure
   ```

2. Definujte Key Vault Configuration Builder v Web.config. Tuto část vložte do oddílu *appSettings* . Pokud používáte službu svrchovaného cloudu, nahraďte název *trezoru* názvem Key Vault, pokud Key Vault v globálním prostředí Azure nebo v ÚPLNÉm identifikátoru URI.

    ```xml
     <configBuilders>
        <builders>
            <add name="Secrets" userSecretsId="695823c3-6921-4458-b60b-2b82bbd39b8d" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.UserSecrets, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
            <add name="AzureKeyVault" vaultName="[VaultName]" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
        </builders>
      </configBuilders>
    ```
3. Oddíl určení appSettings používá Key Vault Configuration Builder. Ujistěte se, že existuje nějaká položka pro nastavení tajného klíče se fiktivní hodnotou.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. Spusťte ladění projektu. Mělo by se úspěšně spustit.

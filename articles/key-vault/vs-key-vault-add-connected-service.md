---
title: Přidání podpory služby Key Vault do projektu ASP.NET pomocí sady Visual Studio – služby Azure Key Vault | Dokumentace Microsoftu
description: Pomocí tohoto kurzu vám pomůžou získat informace tom, jak přidat podporu služby Key Vault k webové aplikaci ASP.NET nebo ASP.NET Core.
services: key-vault
author: ghogen
manager: jillfra
ms.prod: visual-studio
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: ghogen
ms.openlocfilehash: d95bd114be712953b79ef5afbb0915173f6de26c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764475"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Přidání služby Key Vault do vaší webové aplikace pomocí připojených služeb sady Visual Studio

V tomto kurzu se dozvíte, jak snadno přidat všechno, co potřebujete, pokud chcete začít používat Azure Key Vault ke správě svých tajných kódů pro webové projekty v sadě Visual Studio, ať už jsou pomocí ASP.NET Core nebo ASP.NET projektu jakéhokoli typu. Pomocí funkce připojené služby v sadě Visual Studio může sada Visual Studio automaticky přidat balíčky NuGet a nastavení konfigurace, které potřebujete k připojení ke službě Key Vault v Azure. 

Podrobnosti o změnách, připojené služby umožňuje ve vašem projektu povolit služby Key Vault najdete v tématu [službu Key Vault připojené – co se stalo se Moje ASP.NET 4.7.1 projektu](#how-your-aspnet-framework-project-is-modified) nebo [službu Key Vault připojené – co se stalo s mým projektem ASP.NET Core](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**. Pokud žádné nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/)
- **Visual Studio 2019** nebo **Visual Studio 2017 verze 15.7** s **vývoj pro Web** nainstalovaná úloha. [Stáhnout](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)
- Pro technologii ASP.NET (ne jádro) pomocí sady Visual Studio 2017 budete potřebovat rozhraní .NET Framework 4.7.1 nebo vyšší vývojářské nástroje, které ve výchozím nastavení nenainstalují. K instalaci, spusťte instalační program sady Visual Studio, zvolte **změnit**a klikněte na tlačítko **jednotlivé komponenty**, potom na pravé straně, rozbalte **vývoj pro ASP.NET a web**a zvolte **vývojové nástroje .NET Framework 4.7.1**.
- Technologie ASP.NET 4.7.1 nebo novější, nebo webový projekt ASP.NET Core 2.0 otevřít.

## <a name="add-key-vault-support-to-your-project"></a>Přidání podpory služby Key Vault do projektu

1. V **Průzkumníku řešení** zvolte **Přidat**  > **Připojená služba**.
   Zobrazí se stránka Připojená služba se službami, které můžete přidat do projektu.
1. V nabídce dostupné služby, zvolte **zabezpečit tajné kódy s Azure Key Vault**.

   ![Zvolte možnost "Zabezpečené tajné klíče pomocí služby Azure Key Vault"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Pokud jste přihlášení k sadě Visual Studio a máte ke svému účtu přidružené předplatné Azure, zobrazí se stránka s rozevíracím seznamem vašich předplatných. Ujistěte se, že jste přihlášení do sady Visual Studio a že účet, jste zaregistrovaní v s se o stejný účet, který používáte pro vaše předplatné Azure.

1. Vyberte předplatné, které chcete použít a pak zvolte nový nebo existující Key Vault, nebo zvolte odkaz pro úpravy k úpravě automaticky vygenerovaným názvem.

   ![Vyberte své předplatné.](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Zadejte název, který chcete použít pro Key Vault.

   ![Přejmenování služby Key Vault a zvolte skupinu prostředků](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Vyberte existující skupinu prostředků, nebo můžete vytvořit nový s automaticky vygenerovaný jedinečný název.  Pokud chcete vytvořit novou skupinu s jiným názvem, můžete použít [webu Azure Portal](https://portal.azure.com)a pak zavřete stránku a restartuje, aby znovu načíst seznam skupin prostředků.
1. Vyberte oblast, ve kterém chcete vytvořit trezor klíčů. Pokud vaše webová aplikace je hostovaný v Azure, vyberte oblast, který je hostitelem webové aplikace pro optimální výkon.
1. Vyberte si cenový model. Podrobnosti najdete v tématu [cenách služby Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Klepněte na tlačítko OK potvrďte možnosti konfigurace.
1. Zvolte **přidat** k vytvoření služby Key Vault. Proces vytvoření může selhat, pokud zvolíte název, který již byl použit.  Pokud k tomu dojde, použijte **upravit** propojení přejmenujte služby Key Vault a zkuste to znovu.

   ![Přidává se připojená služba do projektu](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. Teď přidejte tajný kód v Key Vault v Azure. Chcete-li získat na správném místě na portálu, klikněte na odkaz pro spravovat tajnými kódy uloženými v této službě Key Vault. Pokud jste zavřeli, na stránce nebo projekt, můžete přejít k tomu [webu Azure portal](https://portal.azure.com) výběrem **všechny služby**v části **zabezpečení**, zvolte **služby Key Vault**, klikněte na tlačítko vytvoříte trezor klíčů.

   ![Přejděte na portálu](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. V části Key Vault pro klíč trezoru, kterou jste vytvořili, vyberte **tajných kódů**, pak **vygenerovat/importovat**.

   ![Vygenerovat/importovat tajného kódu](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Zadejte tajný klíč, jako je například "MySecret" a přiřaďte jí libovolnou hodnotou řetězce jako test a pak zvolte **vytvořit** tlačítko.

   ![Vytvoření tajného klíče](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (volitelné) Zadejte další tajný kód, ale tentokrát umístil do kategorie pojmenováním "Tajné kódy--MySecret". Následující syntaxe Určuje kategorii "Tajné", který obsahuje tajný klíč "MySecret."
 
Teď můžete přistupovat tajné klíče v kódu. Další postup se liší v závislosti na tom, zda používáte ASP.NET 4.7.1 nebo ASP.NET Core.

## <a name="access-your-secrets-in-code"></a>Přístup k vaší tajných kódů v kódu

1. Nainstalujte tyto dva balíčky nuget [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) a [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet knihovny.

2. Otevřete soubor Program.cs a aktualizujte kód následujícím kódem: 
   ```
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

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
               }
            ).UseStartup<Startup>()
             .Build();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

3. Potom otevřete soubor About.cshtml.cs a napsat následující kód:
   1. Zahrňte odkaz na Microsoft.Extensions.Configuration situace příkaz using:

       ```csharp
       using Microsoft.Extensions.Configuration
       ```

   1. Přidejte tento konstruktor:

       ```csharp
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Metoda OnGet aktualizace. Aktualizujte hodnotu zástupného symbolu, které jsou tady uvedené s název tajného klíče, který jste vytvořili výše uvedené příkazy.

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

Spusťte aplikaci místně tak, že přejdete na stránku o. Měli byste vidět vaše tajná hodnota načíst.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už ji nepotřebujete, odstraňte skupinu prostředků. Tím se odstraní služby Key Vault a související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Až se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.

## <a name="how-your-aspnet-core-project-is-modified"></a>Jak se mění váš projekt ASP.NET Core

Tato část identifikuje přesný změny provedené v projektu ASP.NET při přidávání služby Key Vault připojení služby pomocí sady Visual Studio.

### <a name="added-references"></a>Přidání odkazů

Ovlivňuje odkazy na rozhraní .NET soubory projektu a odkazy na balíčky NuGet.

| Type | Referenční informace |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files"></a>Přidání souborů

- Soubor ConnectedService.json přidali, která zaznamenává některé informace o poskytovateli připojenou službu, verzi a odkaz na dokumentaci.

### <a name="project-file-changes"></a>Změny v souboru projektu

- Přidání připojené služby ItemGroup a ConnectedServices.json souboru.

### <a name="launchsettingsjson-changes"></a>launchsettings.JSON změny

- Následující položky proměnná prostředí přidat do profilu služby IIS Express a profil, který odpovídá názvu vašeho webového projektu:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure"></a>Změny v Azure

- Vytvoří skupinu prostředků (nebo použít existující).
- V zadané skupině prostředků vytvořili službu Key Vault.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Jak se mění projektu ASP.NET Framework

Tato část identifikuje přesný změny provedené v projektu ASP.NET při přidávání služby Key Vault připojení služby pomocí sady Visual Studio.

### <a name="added-references"></a>Přidání odkazů

Má vliv na odkazy na soubory .NET projektu a `packages.config` (odkazy na NuGet).

| Type | Referenční informace |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files"></a>Přidání souborů

- Soubor ConnectedService.json přidali, která zaznamenává některé informace o poskytovateli připojenou službu, verzi a odkaz na dokumentaci.

### <a name="project-file-changes"></a>Změny v souboru projektu

- Přidání připojené služby ItemGroup a ConnectedServices.json souboru.
- Odkazy na sestavení .NET je popsáno v [přidali odkazy](#added-references) oddílu.

### <a name="webconfig-or-appconfig-changes"></a>změny v souboru Web.config nebo app.config

- Přidá následující položky konfigurace:

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" 
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add 
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral" 
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure"></a>Změny v Azure

- Vytvoří skupinu prostředků (nebo použít existující).
- V zadané skupině prostředků vytvořili službu Key Vault.

## <a name="next-steps"></a>Další postup

Další informace o vývoji pro Key Vault najdete [– Příručka vývojáře pro Key Vault](key-vault-developers-guide.md)
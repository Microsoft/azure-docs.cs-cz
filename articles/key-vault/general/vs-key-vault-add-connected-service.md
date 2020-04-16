---
title: Přidání podpory úložiště klíčů do projektu ASP.NET pomocí sady Visual Studio – Azure Key Vault | Dokumenty společnosti Microsoft
description: V tomto kurzu vám pomůže naučit se přidat podporu trezoru klíčů do ASP.NET nebo ASP.NET webové aplikace Core.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 68f8607db25b70ea5e6bfe21a7d6e002aeb127d6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429769"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Přidání trezoru klíčů do webové aplikace pomocí služby Visual Studio Connected Services

V tomto kurzu se dozvíte, jak snadno přidat vše, co potřebujete k zahájení používání azure key vault ke správě tajných kódů pro webové projekty v sadě Visual Studio, ať už používáte ASP.NET Core nebo jakýkoli typ ASP.NET projektu. Pomocí funkce Připojené služby v sadě Visual Studio můžete mít Visual Studio automaticky přidat všechny balíčky NuGet a nastavení konfigurace, které potřebujete k připojení k trezoru klíčů v Azure.

Podrobnosti o změnách, které připojené služby ve vašem projektu pro zapnutí trezoru klíčů, najdete v tématu [Key Vault Connected Service – Co se stalo s mým projektem 4.7.1 ASP.NET](#how-your-aspnet-framework-project-is-modified) nebo [službou Connected Service trezoru klíčů – Co se stalo s mým projektem ASP.NET Core](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**. Pokud nemáte předplatné, zaregistrujte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2019 verze 16.3** nebo novější nebo **Visual Studio 2017 verze 15.7** s nainstalovanou úlohou **pro vývoj webu.** [Stáhnout](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)
- Pro ASP.NET (ne jádro) s Visual Studio 2017, budete potřebovat .NET Framework 4.7.1 nebo novější vývojové nástroje, které nejsou nainstalovány ve výchozím nastavení. Chcete-li je nainstalovat, spusťte Instalační službu sady Visual Studio, zvolte **Změnit**a pak zvolte **Jednotlivé součásti**, potom na pravé straně rozbalte **ASP.NET a vývoj webových aplikací**a zvolte vývojové nástroje rozhraní **.NET Framework 4.7.1**.
- Otevře se ASP.NET 4.7.1 nebo novější, nebo ASP.NET webový projekt Core 2.0 nebo novější.

## <a name="add-key-vault-support-to-your-project"></a>Přidání podpory trezoru klíčů do projektu

Než začnete, ujistěte se, že jste přihlášeni do sady Visual Studio. Přihlaste se pomocí stejného účtu, který používáte pro předplatné Azure. Potom otevřete ASP.NET 4.7.1 nebo novějším nebo ASP.NET webový projekt Core 2.0 a postupujte takto:

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt, do kterého chcete přidat podporu trezoru klíčů, a zvolte **Přidat** > **připojenou službu**.
   Zobrazí se stránka Připojená služba se službami, které můžete přidat do projektu.
1. V nabídce dostupných služeb zvolte **Zabezpečené tajné klíče pomocí trezoru klíčů Azure**.

   ![Zvolte možnost Zabezpečené tajné klíče pomocí trezoru klíčů Azure](../media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Vyberte předplatné, které chcete použít, a pak zvolte nový nebo existující trezor klíčů. Pokud zvolíte nový trezor klíčů, zobrazí se odkaz **Upravit.** Vyberte ji a nakonfigurujte nový trezor klíčů.

   ![Vyberte své předplatné.](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. V **části Upravit trezor klíčů Azure**zadejte název, který chcete použít pro trezor klíčů.

1. Vyberte existující **skupinu prostředků**nebo zvolte vytvoření nové skupiny s automaticky generovaným jedinečným názvem.  Pokud chcete vytvořit novou skupinu s jiným názvem, můžete použít [portál Azure](https://portal.azure.com)a potom zavřete stránku a restartujte znovu načíst seznam skupin prostředků.
1. Zvolte **umístění,** ve kterém chcete vytvořit trezor klíčů. Pokud je vaše webová aplikace hostovaná v Azure, zvolte oblast, která je hostitelem webové aplikace pro optimální výkon.
1. Zvolte **cenovou úroveň**. Podrobnosti naleznete v tématu [Ceny trezoru klíčů](https://azure.microsoft.com/pricing/details/key-vault/).
1. Chcete-li přijmout volby konfigurace, zvolte **OK.**
1. Po výběru existujícího trezoru klíčů nebo konfiguraci nového trezoru klíčů vyberte na kartě **Trezor klíčů Azure** v sadě Visual Studio možnost **Přidat** a přidejte připojenou službu.
1. Chcete-li otevřít stránku **Tajné klíče** pro trezor klíčů, vyberte možnost Spravovat tajné klíče uložené v tomto odkazu **Trezor klíčů.** Pokud jste stránku nebo projekt zavřeli, můžete na ní přejít na [webu Azure Portal](https://portal.azure.com) tak, že v části **Zabezpečení**zvolíte **Trezor klíčů**a pak zvolte trezor klíčů. **All Services**
1. V části Trezor klíčů pro vytvořený trezor klíčů zvolte **Tajné klíče**a pak **Generovat/importovat**.

   ![Generovat nebo importovat tajný klíč](../media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Zadejte tajný klíč, například *MySecret* a přiřazujte mu libovolnou hodnotu řetězce jako test, a pak vyberte tlačítko **Vytvořit.**

   ![Vytvoření tajného klíče](../media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (nepovinné) Zadejte jiný tajný klíč, ale tentokrát jej uveďte do kategorie tím, že jej pojmenujete *Secrets -- MySecret*. Tato syntaxe určuje kategorii "Tajné klíče", která obsahuje tajný klíč "MySecret".

Nyní můžete přistupovat ke svým tajemstvím v kódu. Další kroky se liší v závislosti na tom, zda používáte ASP.NET 4.7.1 nebo ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Přístup k tajným kódům v kódu (ASP.NET Core)

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a vyberte **Spravovat balíčky NuGet**. Na kartě **Procházet** vyhledejte a nainstalujte tyto dva balíčky NuGet: [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) a pro rozhraní .NET Core 2, přidejte [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) nebo .NET Core 3, přidejte[Microsoft.Azure.KeyVault.Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core).

1. Pro rozhraní .NET Core `Program.cs` 2 vyberte kartu a změňte `BuildWebHost` definici ve třídě Program na následující:

   ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
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
            ).UseStartup<Startup>();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

   Pro rozhraní .NET Core 3 použijte následující kód.

   ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((context, config) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        config.AddAzureKeyVault(keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    ```

1. Dále otevřete jeden ze stránkovacích souborů, například *Index.cshtml.cs* a napište následující kód:
   1. Zahrnout odkaz `Microsoft.Extensions.Configuration` podle tohoto pomocí směrnice:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Přidejte konfigurační proměnnou.

      ```csharp
      private static readonly IConfiguration _configuration;
      ```

   1. Přidejte tento konstruktor nebo nahraďte existující konstruktor tímto:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Aktualizujte metodu `OnGet`. Aktualizujte zde zobrazenou zástupnou hodnotu s tajným názvem, který jste vytvořili ve výše uvedených příkazech.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. Chcete-li hodnotu potvrdit za běhu, přidejte kód, který se zobrazí `ViewData["Message"]` do souboru *CSHTML,* aby se tajný klíč zobrazil ve zprávě.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

Aplikaci můžete spustit místně a ověřit, zda je tajný klíč získán úspěšně z trezoru klíčů.

## <a name="access-your-secrets-aspnet"></a>Přístup k tajným kódům (ASP.NET)

Konfiguraci můžete nastavit tak, aby soubor web.config měl `appSettings` v prvku fiktivní hodnotu, která je nahrazena skutečnou hodnotou za běhu. Poté k němu můžete `ConfigurationManager.AppSettings` přistupovat prostřednictvím datové struktury.

1. Upravte soubor web.config.  Najděte značku appSettings, `configBuilders="AzureKeyVault"`přidejte atribut a přidejte řádek:

   ```xml
      <add key="mysecret" value="dummy"/>
   ```

1. Upravte `About` metodu v *HomeController.cs*, chcete-li zobrazit hodnotu pro potvrzení.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["mysecret"];
   }
   ```
1. Spusťte aplikaci místně pod ladicím programem, přepněte na kartu **Informace** a ověřte, zda je zobrazena hodnota z trezoru klíčů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, pokud ji už nepotřebujete. Tím odstraníte trezor klíčů a související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Jakmile se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název skupiny prostředků a vyberte **Odstranit**.

## <a name="troubleshooting"></a>Řešení potíží

Pokud je trezor klíčů spuštěn na jiném účtu Microsoft, než je ten, který jste přihlášeni k sadě Visual Studio (například trezor klíčů běží na vašem pracovním účtu, ale Visual Studio používá váš soukromý účet), zobrazí se v souboru Program.cs chyba, že visual studio nemůže získat přístup k trezoru klíčů. Pokud chcete tento problém vyřešit:

1. Přejděte na [portál Azure](https://portal.azure.com) a otevřete trezor klíčů.

1. Zvolte **zásady přístupu**, pak **přidejte zásady přístupu**a zvolte účet, se kterým jste přihlášeni jako hlavní.

1. Ve Visual Studiu zvolte**Nastavení účtu** **souboru** > .
V části Všechny **účet** vyberte **Přidat účet.** Přihlaste se pomocí účtu, který jste si vybrali jako hlavního přístupového účtu.

1. Zvolte**Možnosti** **nástrojů** > a vyhledejte **Azure Service Authentication**. Pak vyberte účet, který jste právě přidali do sady Visual Studio.

Nyní při ladění aplikace, Visual Studio připojí k účtu, ve které je umístěn trezor klíčů.

## <a name="how-your-aspnet-core-project-is-modified"></a>Jak se mění váš projekt ASP.NET Core

Tato část identifikuje přesné změny provedené v projektu ASP.NET při přidávání služby připojení trezoru klíčů pomocí sady Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Přidány odkazy na ASP.NET Core

Ovlivňuje odkazy na soubor projektu .NET a odkazy na balíček NuGet.

| Typ | Referenční informace |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Přidány soubory pro ASP.NET Core

- `ConnectedService.json`, který zaznamenává některé informace o poskytovateli připojených služeb, verzi a propojení dokumentace.

### <a name="project-file-changes-for-aspnet-core"></a>Změny souborů projektu pro ASP.NET Core

- Byla přidána skupina `ConnectedServices.json` a soubor Connected Services ItemGroup.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings.json změny pro ASP.NET Core

- Do profilu služby IIS Express i do profilu, který odpovídá názvu webového projektu, byly přidány následující položky proměnných prostředí:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Změny v Azure pro ASP.NET core

- Vytvořil skupinu prostředků (nebo použil existující).
- V zadané skupině prostředků byl vytvořen trezor klíčů.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Jak se mění váš projekt ASP.NET Framework

Tato část identifikuje přesné změny provedené v projektu ASP.NET při přidávání služby připojení trezoru klíčů pomocí sady Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Přidány odkazy na ASP.NET Framework

Ovlivňuje odkazy na soubor projektu `packages.config` .NET a (Odkazy NuGet).

| Typ | Referenční informace |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Webový klíč Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Přidány soubory pro ASP.NET Framework

- `ConnectedService.json`, který zaznamenává některé informace o poskytovateli připojených služeb, verzi a odkaz na dokumentaci.

### <a name="project-file-changes-for-aspnet-framework"></a>Změny souborů projektu pro ASP.NET Framework

- Byl přidán soubor Connected Services ItemGroup a ConnectedServices.json.
- Odkazy na sestavení .NET popsané v části [Přidané odkazy.](#added-references-for-aspnet-framework)

### <a name="webconfig-or-appconfig-changes"></a>web.config nebo app.config se mění

- Přidány následující položky konfigurace:

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

### <a name="changes-on-azure-for-aspnet-framework"></a>Změny v azure pro ASP.NET framework

- Vytvořil skupinu prostředků (nebo použil existující).
- V zadané skupině prostředků byl vytvořen trezor klíčů.

## <a name="next-steps"></a>Další kroky

Pokud jste postupovali podle tohoto kurzu, vaše oprávnění trezoru klíčů jsou nastaveny tak, aby se spouštěly s vlastním předplatným Azure, ale to nemusí být žádoucí pro produkční scénář. Můžete vytvořit spravovanou identitu pro správu přístupu trezoru klíčů pro vaši aplikaci. Viz [Poskytnutí ověřování trezoru klíčů se spravovanou identitou](/azure/key-vault/managed-identity).

Další informace o vývoji trezoru klíčů naleznete v [průvodci vývojářem trezoru klíčů](developers-guide.md).

---
title: Přidání podpory Key Vault projektu ASP.NET pomocí sady Visual Studio – Azure Key Vault | Microsoft Docs
description: V tomto kurzu se dozvíte, jak přidat Key Vault podporu webové aplikace v ASP.NET nebo ASP.NET Core.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 4cbc4044b5d1270cecd1a271d2a1db02801650dd
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012772"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Přidání Key Vault do webové aplikace pomocí připojených služeb sady Visual Studio

V tomto kurzu se naučíte, jak snadno přidat všechno, co potřebujete, abyste mohli začít používat Azure Key Vault ke správě tajných kódů pro webové projekty v aplikaci Visual Studio bez ohledu na to, jestli používáte ASP.NET Core nebo jakýkoli typ projektu ASP.NET. Pomocí funkce připojené služby v aplikaci Visual Studio může Visual Studio automaticky přidat všechny balíčky NuGet a nastavení konfigurace, které potřebujete pro připojení k Key Vault v Azure.

Podrobnosti o změnách, které připojené služby vytvoří v projektu, aby umožňovaly Key Vault, najdete v tématu [Key Vault připojené služby – co se stalo s mým projektem ASP.NET 4.7.1](#how-your-aspnet-framework-project-is-modified) nebo [Key Vault připojenou službou – co se stalo s mým ASP.NET corem projektem](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**. Pokud předplatné nemáte, zaregistrujte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
- **Visual studio 2019 verze 16,3 Preview 1** nebo novější nebo **Visual studio 2017 verze 15,7** s nainstalovanou úlohou **Vývoj webu** . [Stáhnout](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)
- Pro ASP.NET (ne jádro) se sadou Visual Studio 2017 potřebujete vývojové nástroje .NET Framework 4.7.1 nebo novější, které nejsou ve výchozím nastavení nainstalované. Pokud je chcete nainstalovat, spusťte Instalační program pro Visual Studio, zvolte **Upravit**a pak zvolte **jednotlivé komponenty**, pak na pravé straně, rozbalte **ASP.NET a vývoj webů**a zvolte **.NET Framework vývojové nástroje 4.7.1.** .
- Otevřete webový projekt ASP.NET 4.7.1 nebo novější nebo ASP.NET Core 2,0.

## <a name="add-key-vault-support-to-your-project"></a>Přidání podpory Key Vault do projektu

Než začnete, ujistěte se, že jste se přihlásili do sady Visual Studio. Přihlaste se pomocí stejného účtu, který používáte pro předplatné Azure. Pak otevřete webový projekt ASP.NET 4.7.1 nebo novější verze nebo ASP.NET Core 2,0 a postupujte podle následujících kroků:

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt, do kterého chcete přidat podporu trezoru klíčů, a vyberte **Přidat** > **připojenou službu**.
   Zobrazí se stránka Připojená služba se službami, které můžete přidat do projektu.
1. V nabídce dostupných služeb vyberte možnost **zabezpečené tajné klíče pomocí Azure Key Vault**.

   ![Vyberte zabezpečená tajná klíčová slova s Azure Key Vault.](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Vyberte předplatné, které chcete použít, a pak zvolte nové nebo existující Key Vault. Pokud zvolíte novou Key Vault, zobrazí se odkaz **Upravit** . Vyberte ji pro konfiguraci nového Key Vault.

   ![Vyberte své předplatné.](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. Do pole **upravit Azure Key Vault**zadejte název, který chcete použít pro Key Vault.

1. Vyberte existující **skupinu prostředků**nebo si vytvořte novou s automaticky generovaným jedinečným názvem.  Pokud chcete vytvořit novou skupinu s jiným názvem, můžete použít [Azure Portal](https://portal.azure.com)a potom stránku zavřít a restartovat, čímž znovu načtete seznam skupin prostředků.
1. Vyberte **umístění** , ve kterém chcete vytvořit Key Vault. Pokud je webová aplikace hostována v Azure, vyberte oblast, která je hostitelem webové aplikace pro optimální výkon.
1. Vyberte **cenovou úroveň**. Podrobnosti najdete v tématu [Key Vault ceny](https://azure.microsoft.com/pricing/details/key-vault/).
1. Kliknutím na **tlačítko OK** přijměte možnosti konfigurace.
1. Jakmile vyberete existující Key Vault nebo jste nakonfigurovali nový Key Vault, vyberte na kartě **Azure Key Vault** v aplikaci Visual Studio možnost **Přidat** a přidejte připojenou službu.
1. Vyberte odkaz **Správa tajných klíčů uložených v tomto Key Vault** odkaz a otevřete stránku **tajných** kódů pro vaši Key Vault. Pokud jste stránku nebo projekt zavřeli, můžete na ni přejít v [Azure Portal](https://portal.azure.com) tak, že vyberete **všechny služby** a v části **zabezpečení**zvolíte možnost **Key Vault**a pak zvolíte Key Vault.
1. V části Key Vault pro Trezor klíčů, který jste vytvořili, zvolte **tajné klíče**a pak **vygenerujte/importujte**.

   ![Generování/import tajného klíče](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Zadejte tajný kód, jako je například *MySecret* a poskytněte mu libovolnou řetězcovou hodnotu jako test, a pak vyberte tlačítko **vytvořit** .

   ![Vytvoření tajného klíče](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. volitelné Zadejte jiný tajný kód, ale tentokrát ho vložte do kategorie pojmenováním *tajných kódů--MySecret*. Tato syntaxe určuje kategorii "tajné klíče", která obsahuje tajný klíč "MySecret".

Nyní můžete k tajným klíčům přistupovat v kódu. Další postup se liší v závislosti na tom, zda používáte ASP.NET 4.7.1 nebo ASP.NET Core.

## <a name="access-your-secrets-in-code"></a>Přístup k tajným klíčům v kódu

1. V Průzkumník řešení klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**. Na kartě **Procházet** vyhledejte a nainstalujte tyto dva balíčky NuGet: [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) a [Microsoft. Azure. webtrezor](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

1. Vyberte kartu `Program.cs` a třídu program nahraďte následujícím kódem:

   ```csharp
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

1. Další soubor Open `About.cshtml.cs` a napište následující kód:
   1. Pomocí příkazu Using přidejte odkaz na `Microsoft.Extensions.Configuration`:

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

   1. Aktualizujte metodu `OnGet`. Aktualizujte zástupný symbol, který se tady zobrazuje, s názvem tajného klíče, který jste vytvořili v předchozích příkazech.

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

Spusťte aplikaci místně tak, že přejdete na stránku o produktu. Měli byste vidět, že se načetla vaše tajná hodnota.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už ji nepotřebujete, odstraňte skupinu prostředků. Tím se odstraní Key Vault a související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Jakmile se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **Zadejte název skupiny prostředků:** zadejte název skupiny prostředků a vyberte **Odstranit**.

## <a name="troubleshooting"></a>Řešení potíží

Pokud je váš Trezor klíčů spuštěný na jiném účet Microsoft než ten, který jste přihlásili do sady Visual Studio (například Trezor klíčů je spuštěný ve vašem pracovním účtu, ale Visual Studio používá váš privátní účet), zobrazí se v souboru Program.cs chyba. , že Visual Studio nemůže získat přístup k trezoru klíčů. Pokud chcete tento problém vyřešit:

1. Přejít na [Azure Portal](https://portal.azure.com) a otevřete Key Vault.

1. Zvolte **zásady přístupu**, pak **Přidat zásady přístupu**a zvolte účet, ke kterému jste přihlášeni jako objekt zabezpečení.

1. V aplikaci Visual Studio vyberte **soubor** > **Nastavení účtu**.
V části **všechny účty** vyberte **Přidat účet** . Přihlaste se pomocí účtu, který jste zvolili jako objekt zabezpečení vašich zásad přístupu.

1. Vyberte **nástroje** > **Možnosti**a vyhledejte **ověřování služby Azure**. Pak vyberte účet, který jste právě přidali do sady Visual Studio.

Nyní při ladění aplikace se Visual Studio připojí k účtu, na kterém je uložený Trezor klíčů.

## <a name="how-your-aspnet-core-project-is-modified"></a>Jak se upraví váš ASP.NET Core projekt

Tato část identifikuje přesné změny provedené v projektu ASP.NET při přidání propojené služby Key Vault pomocí sady Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Přidání odkazů pro ASP.NET Core

Má vliv na soubor projektu .NET References a odkazy na balíček NuGet.

| Typ | Referenční informace |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Přidané soubory pro ASP.NET Core

- `ConnectedService.json` přidáno, které zaznamenává některé informace o poskytovateli připojené služby, verzi a odkaz na dokumentaci.

### <a name="project-file-changes-for-aspnet-core"></a>Změny souborů projektu pro ASP.NET Core

- Přidání skupiny položek a `ConnectedServices.json` připojené služby

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>změny launchsettings. JSON pro ASP.NET Core

- Do profilu IIS Express byly přidány následující záznamy o proměnné prostředí a profil, který odpovídá názvu vašeho webového projektu:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Změny v Azure pro ASP.NET Core

- Vytvořili jste skupinu prostředků (nebo jste použili stávající).
- Vytvořil se Key Vault v zadané skupině prostředků.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Způsob úpravy projektu ASP.NET Framework

Tato část identifikuje přesné změny provedené v projektu ASP.NET při přidání propojené služby Key Vault pomocí sady Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Přidané odkazy pro ASP.NET Framework

Má vliv na soubor projektu .NET References a `packages.config` (odkazy NuGet).

| Typ | Referenční informace |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Přidané soubory pro ASP.NET Framework

- `ConnectedService.json` přidáno, které zaznamenává některé informace o poskytovateli, verzi a odkazu na připojené služby, které jsou uvedeny v dokumentaci.

### <a name="project-file-changes-for-aspnet-framework"></a>Změny souborů projektu pro ASP.NET Framework

- Přidal se soubor Items a připojených služeb. JSON připojené služby.
- Odkazy na sestavení .NET popsaná v části [přidané odkazy](#added-references-for-aspnet-framework) .

### <a name="webconfig-or-appconfig-changes"></a>změny souboru Web. config nebo App. config

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

### <a name="changes-on-azure-for-aspnet-framework"></a>Změny v Azure pro ASP.NET Framework

- Vytvořili jste skupinu prostředků (nebo jste použili stávající).
- Vytvořil se Key Vault v zadané skupině prostředků.

## <a name="next-steps"></a>Další kroky

Další informace o vývoji Key Vault najdete v [příručce pro vývojáře Key Vault](key-vault-developers-guide.md).

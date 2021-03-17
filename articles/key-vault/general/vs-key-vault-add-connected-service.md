---
title: Přidání podpory Key Vault projektu ASP.NET pomocí sady Visual Studio – Azure Key Vault | Microsoft Docs
description: V tomto kurzu se dozvíte, jak přidat Key Vault podporu webové aplikace v ASP.NET nebo ASP.NET Core.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure, devx-track-csharp
ms.topic: how-to
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 9c62534acdbfbff7fd4e718bad1f07a92c641626
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792391"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Přidání Key Vault do webové aplikace pomocí připojených služeb sady Visual Studio

V tomto kurzu se naučíte, jak snadno přidat všechno, co potřebujete, abyste mohli začít používat Azure Key Vault ke správě tajných kódů pro webové projekty v aplikaci Visual Studio bez ohledu na to, jestli používáte ASP.NET Core nebo jakýkoli typ projektu ASP.NET. Pomocí funkce připojené služby v aplikaci Visual Studio může Visual Studio automaticky přidat všechny balíčky NuGet a nastavení konfigurace, které potřebujete pro připojení k Key Vault v Azure.

Podrobnosti o změnách, které připojené služby vytvoří v projektu, aby umožňovaly Key Vault, najdete v tématu [Key Vault připojené služby – co se stalo s mým projektem ASP.NET 4.7.1](#how-your-aspnet-framework-project-is-modified) nebo [Key Vault připojenou službou – co se stalo s mým ASP.NET corem projektem](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Předpoklady

- **Předplatné Azure** . Pokud předplatné nemáte, zaregistrujte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2019 verze 16,3** nebo novější [Stáhnout nyní](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).


## <a name="add-key-vault-support-to-your-project"></a>Přidání podpory Key Vault do projektu

Než začnete, ujistěte se, že jste se přihlásili do sady Visual Studio. Přihlaste se pomocí stejného účtu, který používáte pro předplatné Azure. Pak otevřete webový projekt ASP.NET 4.7.1 nebo novější verze nebo ASP.NET Core 2,0 a postupujte podle následujících kroků:

1. V **Průzkumník řešení** klikněte pravým tlačítkem myši na projekt, do kterého chcete přidat podporu Key Vault a vyberte **Přidat**  >  **připojenou službu**  >  **Přidat** .
   Zobrazí se stránka Připojená služba se službami, které můžete přidat do projektu.
1. V nabídce dostupných služeb vyberte možnost **Azure Key Vault** a klikněte na tlačítko **Další** .

   ![Vyberte "Azure Key Vault"](../media/vs-key-vault-add-connected-service/key-vault-connected-service.png)

1. Vyberte předplatné, které chcete použít, a pak zvolte existující Key Vault a klikněte na **Dokončit** . 

   ![Výběr předplatného](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

Nyní je připojení k Key Vault navázáno a přístup k tajným klíčům můžete získat v kódu. Další postup se liší v závislosti na tom, zda používáte ASP.NET 4.7.1 nebo ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Přístup k tajným klíčům v kódu (ASP.NET Core)

1. Otevřete jeden ze souborů stránky, například *index.cshtml.cs* , a napište následující kód:
   1. Zahrnout odkaz na `Microsoft.Extensions.Configuration` tuto direktivu using:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Přidejte konfigurační proměnnou.

      ```csharp
      private static IConfiguration _configuration;
      ```

   1. Přidejte tento konstruktor nebo nahraďte existující konstruktor tímto:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Aktualizujte metodu `OnGet`. Aktualizujte zástupný symbol, který se tady zobrazuje, s názvem tajného klíče, který jste vytvořili v předchozích příkazech.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameStoredInKeyVault>"];
       }
       ```

   1. Pro potvrzení hodnoty za běhu přidejte kód, který se zobrazí `ViewData["Message"]` v souboru *. cshtml* pro zobrazení tajného klíče ve zprávě.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

Aplikaci můžete spustit místně, abyste ověřili, že se tajný klíč úspěšně získá z Key Vault.

## <a name="access-your-secrets-aspnet"></a>Přístup k vašim tajným klíčům (ASP.NET)
Konfiguraci můžete nastavit tak, aby soubor web.config měl v prvku zástupnou hodnotu `appSettings` , která je nahrazena skutečnou hodnotou za běhu. Pak můžete přistupovat přes `ConfigurationManager.AppSettings` datovou strukturu.

1. V Průzkumník řešení klikněte pravým tlačítkem na projekt a vyberte Spravovat balíčky NuGet. Na kartě Procházet vyhledejte a nainstalujte [Microsoft.Configuration.ConfigurationBuilders. Azure](https://www.nuget.org/packages/Microsoft.Configuration.ConfigurationBuilders.Azure/)
 
1. Otevřete soubor web.config a napište následující kód:
    1. Přidat `configSections` a `configBuilders` :
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
    1. Vyhledejte značku appSettings, přidejte atribut `configBuilders="AzureKeyVault"` a přidejte řádek:
        ```xml
         <add key="<secretNameInYourKeyVault>" value="dummy"/>
        ```

1. Upravte `About` metodu v *HomeController.cs* , aby se zobrazila hodnota pro potvrzení.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["<secretNameInYourKeyVault>"];
   }
   ```
1. Spusťte aplikaci místně v rámci ladicího programu, přepněte na kartu **o** a ověřte, zda se zobrazí hodnota z Key Vault.

## <a name="troubleshooting"></a>Řešení potíží

Pokud vaše Key Vault běží na jiném účet Microsoft než na tom, co jste přihlásili k aplikaci Visual Studio (například Key Vault je spuštěná v pracovním účtu, ale Visual Studio používá váš privátní účet), zobrazí se v souboru Program.cs chyba, že Visual Studio nemůže získat přístup k Key Vault. Pokud chcete tento problém vyřešit:

1. Přejít na [Azure Portal](https://portal.azure.com) a otevřete Key Vault.

1. Zvolte **zásady přístupu** , pak **Přidat zásady přístupu** a zvolte účet, ke kterému jste přihlášeni jako objekt zabezpečení.

1. V aplikaci Visual Studio vyberte **File**  >  **Nastavení účtu** souboru.
V části **všechny účty** vyberte **Přidat účet** . Přihlaste se pomocí účtu, který jste zvolili jako objekt zabezpečení vašich zásad přístupu.

1. Vyberte **Tools**  >  **Možnosti** nástroje a vyhledejte **ověřování služby Azure** . Pak vyberte účet, který jste právě přidali do sady Visual Studio.

Když teď ladíte aplikaci, Visual Studio se připojí k účtu, na kterém se nachází Key Vault.

## <a name="how-your-aspnet-core-project-is-modified"></a>Jak se upraví váš ASP.NET Core projekt

Tato část identifikuje přesné změny provedené v projektu ASP.NET při přidání propojené služby Key Vault pomocí sady Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Přidání odkazů pro ASP.NET Core

Má vliv na soubor projektu .NET References a odkazy na balíček NuGet.

| Typ | Referenční informace |
| --- | --- |
| NuGet | Microsoft. AspNetCore. AzureKeyVault. HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Přidané soubory pro ASP.NET Core

- `ConnectedService.json` přidáno, které zaznamenává některé informace o poskytovateli připojené služby, verzi a odkaz na dokumentaci.

### <a name="project-file-changes-for-aspnet-core"></a>Změny souborů projektu pro ASP.NET Core

- Přidala se skupina a soubor položek připojené služby `ConnectedServices.json` .

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings.jszměny pro ASP.NET Core

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
| Pohyby NuGet | Azure. identity |
| Pohyby NuGet | Azure. Security. Keys – trezory. Keys |
| Pohyby NuGet | Azure. Security. klíčů trezor. tajných kódů |

> [!IMPORTANT] 
> Ve výchozím nastavení je nainstalovaná služba Azure. identity 1.1.1, která nepodporuje přihlašovací údaje sady Visual Studio. Odkaz na balíček můžete aktualizovat ručně na 1,2 + pomocí pověření sady Visual Studio.

### <a name="added-files-for-aspnet-framework"></a>Přidané soubory pro ASP.NET Framework

- `ConnectedService.json` přidáno, které zaznamenává některé informace o poskytovateli připojené služby, verzi a odkazu na dokumentaci.

### <a name="project-file-changes-for-aspnet-framework"></a>Změny souborů projektu pro ASP.NET Framework

- Do souboru se přidala skupina položek připojené služby a ConnectedServices.js.
- Odkazy na sestavení .NET popsaná v části [přidané odkazy](#added-references-for-aspnet-framework) .

## <a name="next-steps"></a>Další kroky

Pokud jste postupovali podle tohoto kurzu, vaše oprávnění Key Vault se nastaví tak, aby běžela s vlastním předplatným Azure, ale nemusí být žádoucí pro produkční scénář. Můžete vytvořit spravovanou identitu pro správu přístupu k Key Vault vaší aplikaci. Další informace najdete v tématu [ověření Key Vault](./authentication.md) a [přiřazení zásad Key Vault přístupu](./assign-access-policy-portal.md).

Další informace o vývoji Key Vault najdete v [příručce pro vývojáře Key Vault](developers-guide.md).
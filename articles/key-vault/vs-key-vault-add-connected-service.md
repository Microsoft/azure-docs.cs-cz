---
title: Přidání podpory Key Vault do projektu ASP.NET pomocí sady Visual Studio | Microsoft Docs
description: Pomocí tohoto kurzu můžete informace o přidání podpory Key Vault k webové aplikaci ASP.NET nebo ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 9f0cc6ee06042948442aace05d56fcffa3742a8d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Přidejte klíč trezoru k vaší webové aplikaci pomocí Visual Studio připojené Services

V tomto kurzu se dozvíte, jak snadno přidat všechno, co potřebujete začít používat Azure Key Vault spravovat vaše tajné klíče pro webové projekty v sadě Visual Studio, zda používáte ASP.NET Core nebo kterýkoli typ projekt ASP.NET. Pomocí funkce připojení služby ve Visual Studio 2017 může mít Visual Studio automaticky přidat všechny balíčky NuGet a nastavení konfigurace, které potřebujete pro připojení k v Azure Key Vault. 

Podrobnosti o změnách, že připojení služby vytvoří ve vašem projektu povolit Key Vault najdete v tématu [klíč trezoru připojené služby - co se stalo s Moje ASP.NET 4.7.1 projektu](vs-key-vault-aspnet-what-happened.md) nebo [klíč trezoru připojené služby - co se stalo s Moje projektu ASP.NET Core](vs-key-vault-aspnet-core-what-happened.md).

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**. Pokud žádné nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/)
- **Visual Studio 2017 verze 15.7** s **vývoj webů** zatížení nainstalována. [Stáhněte si teď](https://aka.ms/vsdownload).
- Pro technologii ASP.NET (ne jádro) je nutné rozhraní .NET Framework 4.7.1 vývojovými nástroji, které nejsou ve výchozím nastavení nainstalovaná. K instalaci, spusťte instalační program Visual Studio, vyberte **upravit**a potom zvolte **jednotlivých součástí**, na pravé straně, rozbalte položku **ASP.NET a webové vývoj**a zvolte **nástrojů pro vývoj řešení pro rozhraní .NET Framework 4.7.1**.
- ASP.NET 4.7.1 nebo webový projekt ASP.NET 2.0 základní otevřete.

## <a name="add-key-vault-support-to-your-project"></a>Do projektu přidejte podporu Key Vault

1. V **Průzkumníku řešení**, zvolte **přidat** > **připojení službě**.
   Zobrazí se stránka připojení služby s služby, které můžete přidat do projektu.
1. V nabídce služby k dispozici, zvolte **zabezpečení tajných klíčů s Azure Key Vault**.

   ![Zvolte "Zabezpečené tajných klíčů s Azure Key Vault"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Pokud jste přihlášeni k sadě Visual Studio a máte předplatné Azure spojené s vaším účtem, se zobrazí stránka s rozevíracím seznamu k vašemu předplatnému.
1. Vyberte předplatné, které chcete použít a potom zvolte nový nebo existující Key Vault, nebo zvolte odkaz pro úpravy změnit automaticky generovaný název.

   ![Vyberte své předplatné.](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Zadejte název, který chcete použít pro Key Vault.

   ![Přejmenujte Key Vault a vyberte skupinu prostředků.](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Vyberte existující skupinu prostředků nebo se rozhodnete vytvořit nový s názvem automaticky vygenerovaným unqiue.  Pokud chcete vytvořit novou skupinu s jiným názvem, můžete použít [portálu Azure](https://portal.azure.com)a pak zavřete stránku a znovu spusťte znovu načíst seznam skupin prostředků.
1. Vyberte oblast, ve které chcete vytvořit Key Vault. Pokud je webová aplikace hostovaná v Azure, vyberte oblast, který je hostitelem webové aplikace pro optimální výkon.
1. Zvolte cenový model. Podrobnosti najdete v tématu [klíč trezoru ceny](https://azure.microsoft.com/pricing/details/key-vault/).
1. Klepněte na tlačítko OK tak, aby přijímal možnosti konfigurace.
1. Zvolte **přidat** vytvořit Key Vault. Proces vytvoření může selhat, pokud zvolte název, který se už použil.  Pokud k tomu dojde, použijte **upravit** odkaz přejmenovat Key Vault a zkuste to znovu.

   ![Přidání připojených služeb do projektu](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. Nyní přidáte tajný klíč v trezoru klíč v Azure. Abyste se dostali na správném místě na portálu, klikněte na odkaz pro spravovat tajné klíče uložené v tomto trezoru klíč. Pokud jste zavřeli stránce nebo projektu, můžete přejít na ho [portál Azure](https://portal.azure.com) výběrem **všechny služby**v části **zabezpečení**, zvolte **Key Vault**, zvolte Key Vault, kterou jste právě vytvořili.

   ![Navigace na portálu](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. V části Key Vault pro klíč trezoru, kterou jste vytvořili, zvolte **tajné klíče**, pak **generovat a Import**.

   ![Generování a Import tajný klíč](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Zadejte tajný klíč, jako je například "MySecret" a dejte mu libovolnou hodnotu řetězce jako testu a pak vyberte **vytvořit** tlačítko.

   ![Vytvoření tajného klíče](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (volitelné) Zadejte další sdílený tajný klíč, ale tentokrát ho přesuňte do kategorie pojmenováním "Tajemství – MySecret". Tuto syntaxi Určuje kategorii "Tajemství", který obsahuje tajný klíč "MySecret."
 
Teď můžete přistupovat tajných klíčů v kódu. Další postup se liší v závislosti na tom, jestli používáte 4.7.1 technologie ASP.NET nebo ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core-projects"></a>Přístup k tajných klíčů v kódu (projektů ASP.NET Core)

1. V sadě Visual Studio ve vašem projektu ASP.NET Core, můžete odkazovat těchto tajných klíčů pomocí těchto výrazů v kódu:
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

1. Na stránce .cshtml vyslovení About.cshtml, přidejte @inject direktivy v horní části souboru nastavit proměnnou můžete použít pro přístup ke konfiguračním Key Vault.

   ```cshtml
      @inject Microsoft.Extensions.Configuration.IConfiguration config
   ```

1. Jako testu můžete ověřit, že hodnota tajný klíč je k dispozici zobrazením na jednom ze stránky. Použití @config k odkazování proměnné konfigurace.
 
   ```cshtml
      <p> @config["MySecret"] </p>
      <p> @config.GetSection("Secrets")["MySecret"] </p>
      <p> @config["Secrets:MySecret"] </p>
   ```

1. Sestavení a spuštění webové aplikace, přejděte na stránku o a zobrazit tak hodnotu "tajný klíč".

## <a name="access-your-secrets-in-code-aspnet-471-projects"></a>Přístup k tajných klíčů v kódu (ASP.NET 4.7.1 projekty)

1. Upravte soubor web.config následujícím způsobem. U klíčů se zástupné symboly, které budou nahrazeny AzureKeyVault ConfigurationBuilder hodnotami tajných klíčů v Key Vault.

   ```xml
     <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="MySecret" value="dummy1"/>
       <add key="Secrets--MySecret" value="dummy2"/>
     </appSettings>
   ```

1. V HomeController, v metodě o kontroleru přidejte následující řádky můžete načíst tajný klíč a uložit ho do objekt ViewBag.
 
   ```csharp
            var secret = ConfigurationManager.AppSettings["MySecret"];
            var secret2 = ConfigurationManager.AppSettings["Secrets--MySecret"];
            ViewBag.Secret = $"Secret: {secret}";
            ViewBag.Secret2 = $"Secret2: {secret2}";
   ```

1. V zobrazení About.cshtml přidejte následující zobrazit hodnotu tajný klíč (pro pouze zkušební režim).

   ```csharp
      <h3>@ViewBag.Secret</h3>
      <h3>@ViewBag.Secret2</h3>
   ```

Blahopřejeme, nyní se ujistíte, že vaše webová aplikace použít Key Vault pro přístup bezpečně uloženým tajných klíčů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, odstraňte skupinu prostředků. To odstraní Key Vault a související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Až se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

Další informace o vývoji pro Key Vault načtením [Příručka vývojáře Key Vault](key-vault-developers-guide.md)
---
title: Přidání podpory služby Key Vault do projektu ASP.NET pomocí sady Visual Studio | Dokumentace Microsoftu
description: Pomocí tohoto kurzu vám pomůžou získat informace tom, jak přidat podporu služby Key Vault k webové aplikaci ASP.NET nebo ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 9cf49ae97da3bf67300bdc222c86bb712aeaed37
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465788"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Přidání služby Key Vault do vaší webové aplikace pomocí připojených služeb sady Visual Studio

V tomto kurzu se dozvíte, jak snadno přidat všechno, co potřebujete, pokud chcete začít používat Azure Key Vault ke správě svých tajných kódů pro webové projekty v sadě Visual Studio, ať už jsou pomocí ASP.NET Core nebo ASP.NET projektu jakéhokoli typu. Pomocí funkce připojené služby v sadě Visual Studio 2017 může sada Visual Studio automaticky přidat balíčky NuGet a nastavení konfigurace, které potřebujete k připojení ke službě Key Vault v Azure. 

Podrobnosti o změnách, připojené služby umožňuje ve vašem projektu povolit služby Key Vault najdete v tématu [službu Key Vault připojené – co se stalo se Moje ASP.NET 4.7.1 projektu](vs-key-vault-aspnet-what-happened.md) nebo [službu Key Vault připojené – co se stalo s mým projektem ASP.NET Core](vs-key-vault-aspnet-core-what-happened.md).

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**. Pokud žádné nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/)
- **Visual Studio 2017 verze 15.7** s **vývoj pro Web** nainstalovaná úloha. [Stáhnout nyní](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Pro technologii ASP.NET (ne jádro) potřebujete vývojářské nástroje .NET Framework 4.7.1, která ve výchozím nastavení nenainstalují. K instalaci, spusťte instalační program sady Visual Studio, zvolte **změnit**a klikněte na tlačítko **jednotlivé komponenty**, potom na pravé straně, rozbalte **vývoj pro ASP.NET a web**a zvolte **vývojové nástroje .NET Framework 4.7.1**.
- 4.7.1 technologie ASP.NET nebo ASP.NET Core 2.0 webový projekt otevřít.

## <a name="add-key-vault-support-to-your-project"></a>Přidání podpory služby Key Vault do projektu

1. V **Průzkumníka řešení**, zvolte **přidat** > **připojenou službu**.
   Se službami, které můžete přidat do projektu se zobrazí na stránce připojené služby.
1. V nabídce dostupné služby, zvolte **zabezpečit tajné kódy s Azure Key Vault**.

   ![Zvolte možnost "Zabezpečené tajné klíče pomocí služby Azure Key Vault"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Pokud jste se přihlašovali do sady Visual Studio a mít předplatné Azure spojené s vaším účtem, se zobrazí stránka s rozevíracím seznamu k vašemu předplatnému. Ujistěte se, že jste přihlášení do sady Visual Studio a že účet, jste zaregistrovaní v s se o stejný účet, který používáte pro vaše předplatné Azure.

1. Vyberte předplatné, které chcete použít a pak zvolte nový nebo existující Key Vault, nebo zvolte odkaz pro úpravy k úpravě automaticky vygenerovaným názvem.

   ![Vyberte své předplatné.](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Zadejte název, který chcete použít pro Key Vault.

   ![Přejmenování služby Key Vault a zvolte skupinu prostředků](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Vyberte existující skupinu prostředků, nebo můžete vytvořit nový s názvem automaticky vygenerovaný jedinečný.  Pokud chcete vytvořit novou skupinu s jiným názvem, můžete použít [webu Azure Portal](https://portal.azure.com)a pak zavřete stránku a restartuje, aby znovu načíst seznam skupin prostředků.
1. Vyberte oblast, ve kterém chcete vytvořit trezor klíčů. Pokud vaše webová aplikace je hostovaný v Azure, vyberte oblast, který je hostitelem webové aplikace pro optimální výkon.
1. Vyberte si cenový model. Podrobnosti najdete v tématu [cenách služby Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Klepněte na tlačítko OK potvrďte možnosti konfigurace.
1. Zvolte **přidat** k vytvoření služby Key Vault. Proces vytvoření může selhat, pokud zvolíte název, který již byl použit.  Pokud k tomu dojde, použijte **upravit** propojení přejmenujte služby Key Vault a zkuste to znovu.

   ![Přidává se připojená služba do projektu](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. Teď přidejte tajný kód v Key Vault v Azure. Chcete-li získat na správném místě na portálu, klikněte na odkaz pro spravovat tajnými kódy uloženými v této službě Key Vault. Pokud jste zavřeli, na stránce nebo projekt, můžete přejít k tomu [webu Azure portal](https://portal.azure.com) výběrem **všechny služby**v části **zabezpečení**, zvolte **služby Key Vault**, klikněte na tlačítko Key Vault, které jste právě vytvořili.

   ![Přejděte na portálu](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. V části Key Vault pro klíč trezoru, kterou jste vytvořili, vyberte **tajných kódů**, pak **vygenerovat/importovat**.

   ![Vygenerovat/importovat tajného kódu](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Zadejte tajný kód, jako je například "MySecret" a přiřaďte jí libovolnou hodnotou řetězce jako test a pak zvolte **vytvořit** tlačítko.

   ![Vytvoření tajného klíče](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (volitelné) Zadejte další tajný kód, ale tentokrát umístil do kategorie pojmenováním "Tajné kódy--MySecret". Následující syntaxe Určuje kategorii "Tajné", který obsahuje tajný klíč "MySecret."
 
Teď můžete přistupovat tajné klíče v kódu. Další postup se liší v závislosti na tom, zda používáte ASP.NET 4.7.1 nebo ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core-projects"></a>Přístup k vaší tajných kódů v kódu (projekty ASP.NET Core)

Připojení ke službě Key Vault je nastavený při spuštění třídou, která implementuje [Microsoft.AspNetCore.Hosting.IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1) pomocí způsob, jak rozšíření chování při spuštění, který je popsaný v [vylepšení aplikace z externího sestavení v ASP.NET Core s IHostingStartup](/aspnet/core/fundamentals/host/platform-specific-configuration). Třída při spuštění používá dvou proměnných prostředí, které obsahují informace o připojení služby Key Vault: ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED nastavena na hodnotu true a ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT, nastavte na klíč Adresa URL trezoru. Tyto jsou přidány do souboru launchsettings.json při spuštění **přidat připojenou službu** procesu.

Pro přístup k vaší tajných kódů:

1. V sadě Visual Studio v projektu ASP.NET Core teď můžete odkazovat těchto tajných kódů pomocí těchto výrazů v kódu:
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

1. Na stránce cshtml, Řekněme, že About.cshtml, přidejte @inject direktiv v horní části souboru, který má nastavit proměnnou můžete použít pro přístup ke konfiguračním služby Key Vault.

   ```cshtml
      @inject Microsoft.Extensions.Configuration.IConfiguration config
   ```

1. Jako test můžete ověřit, že hodnota tajný kód je k dispozici zobrazením na jednu ze stránek. Použití @config k odkazování proměnné konfigurace.
 
   ```cshtml
      <p> @config["MySecret"] </p>
      <p> @config.GetSection("Secrets")["MySecret"] </p>
      <p> @config["Secrets:MySecret"] </p>
   ```

1. Sestavení a spuštění webové aplikace, přejděte na stránku o a zobrazit hodnotu "tajné".

## <a name="access-your-secrets-in-code-aspnet-471-projects"></a>Přístup k vaší tajných kódů v kódu (ASP.NET 4.7.1 projektů)

Připojení ke službě Key Vault nastavuje třídu ConfigurationBuilder pomocí informace, které se přidal do souboru web.config, když se pustíte do **přidat připojenou službu** procesu.

Pro přístup k vaší tajných kódů:

1. Upravte soubor web.config následujícím způsobem. Klíče jsou zástupné symboly, které budou nahrazeny AzureKeyVault ConfigurationBuilder s hodnotami tajných klíčů ve službě Key Vault.

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

1. V HomeController v metodě o kontroleru přidejte následující řádky k načtení tajný klíč a uložte ho objekt ViewBag.
 
   ```csharp
            var secret = ConfigurationManager.AppSettings["MySecret"];
            var secret2 = ConfigurationManager.AppSettings["Secrets--MySecret"];
            ViewBag.Secret = $"Secret: {secret}";
            ViewBag.Secret2 = $"Secret2: {secret2}";
   ```

1. V zobrazení About.cshtml přidejte následující k zobrazení hodnoty tajný kód (pouze pro testování).

   ```csharp
      <h3>@ViewBag.Secret</h3>
      <h3>@ViewBag.Secret2</h3>
   ```

Blahopřejeme, nyní se ujistíte, že webové aplikace můžete použít služby Key Vault pro přístup k tajným klíčům bezpečně uložené.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků. Tím se odstraní služby Key Vault a související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Až se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

Další informace o vývoji pro Key Vault najdete [– Příručka vývojáře pro Key Vault](key-vault-developers-guide.md)
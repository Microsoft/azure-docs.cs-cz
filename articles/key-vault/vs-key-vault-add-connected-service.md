---
title: Přidání podpory služby Key Vault do projektu ASP.NET pomocí sady Visual Studio – služby Azure Key Vault | Dokumentace Microsoftu
description: Pomocí tohoto kurzu vám pomůžou získat informace tom, jak přidat podporu služby Key Vault k webové aplikaci ASP.NET nebo ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: ghogen
ms.openlocfilehash: a6de5385046918c48b3f606477727ca4623a784c
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998621"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Přidání služby Key Vault do vaší webové aplikace pomocí připojených služeb sady Visual Studio

V tomto kurzu se dozvíte, jak snadno přidat všechno, co potřebujete, pokud chcete začít používat Azure Key Vault ke správě svých tajných kódů pro webové projekty v sadě Visual Studio, ať už jsou pomocí ASP.NET Core nebo ASP.NET projektu jakéhokoli typu. Pomocí funkce připojené služby v sadě Visual Studio 2017 může sada Visual Studio automaticky přidat balíčky NuGet a nastavení konfigurace, které potřebujete k připojení ke službě Key Vault v Azure. 

Podrobnosti o změnách, připojené služby umožňuje ve vašem projektu povolit služby Key Vault najdete v tématu [službu Key Vault připojené – co se stalo se Moje ASP.NET 4.7.1 projektu](vs-key-vault-aspnet-what-happened.md) nebo [službu Key Vault připojené – co se stalo s mým projektem ASP.NET Core](vs-key-vault-aspnet-core-what-happened.md).

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**. Pokud žádné nemáte, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/)
- **Visual Studio 2017 verze 15.7** s nainstalovanou sadou funkcí **Vývoj pro web**. [Stáhnout](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)
- Pro technologii ASP.NET (ne jádro) potřebujete vývojářské nástroje .NET Framework 4.7.1, která ve výchozím nastavení nenainstalují. K instalaci, spusťte instalační program sady Visual Studio, zvolte **změnit**a klikněte na tlačítko **jednotlivé komponenty**, potom na pravé straně, rozbalte **vývoj pro ASP.NET a web**a zvolte **vývojové nástroje .NET Framework 4.7.1**.
- 4.7.1 technologie ASP.NET nebo ASP.NET Core 2.0 webový projekt otevřít.

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

1. Vyberte existující skupinu prostředků, nebo můžete vytvořit nový s názvem automaticky vygenerovaný jedinečný.  Pokud chcete vytvořit novou skupinu s jiným názvem, můžete použít [webu Azure Portal](https://portal.azure.com)a pak zavřete stránku a restartuje, aby znovu načíst seznam skupin prostředků.
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
3. Potom otevřete soubor About.cshtml.cs a napsat následující kód
    1. Zahrnout odkaz na Microsoft.Extensions.Configuration situace using – příkaz    
        ```
        using Microsoft.Extensions.Configuration
        ```
    2. Přidejte tento konstruktor
        ```
        public AboutModel(IConfiguration configuration)
        {
            _configuration = configuration;
        }
        ```
    3. Metoda OnGet aktualizace. Aktualizovat hodnotu zástupného symbolu, které jsou tady uvedené s název tajného klíče, který jste vytvořili výše uvedené příkazy
        ```
        public void OnGet()
        {
            //Message = "Your application description page.";
            Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
        }
        ```

Spusťte aplikaci místně tak, že přejdete do o stránce. Měli byste vaše tajná hodnota načíst

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už ji nepotřebujete, odstraňte skupinu prostředků. Tím se odstraní služby Key Vault a související prostředky. Odstranění skupiny prostředků přes portál:

1. Do pole Hledat v horní části portálu zadejte název vaší skupiny prostředků. Až se ve výsledcích hledání zobrazí skupina prostředků použitá v tomto rychlém startu, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. Do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ:** zadejte název vaší skupiny prostředků a vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

Další informace o vývoji pro Key Vault najdete [– Příručka vývojáře pro Key Vault](key-vault-developers-guide.md)
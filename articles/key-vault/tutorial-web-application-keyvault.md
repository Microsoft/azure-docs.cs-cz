---
title: Kurz Konfigurace webové aplikace Azure pro čtení tajného kódu ze služby Key Vault | Microsoft Docs
description: 'Kurz: Konfigurace aplikace ASP.Net Core pro čtení tajného kódu z trezoru klíčů'
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 05/17/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: ff8cfb84fda6d40cca6d61a956f05e28ea670bf6
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428770"
---
# <a name="tutorial-configure-an-azure-web-application-to-read-a-secret-from-key-vault"></a>Kurz: Konfigurace webové aplikace Azure pro čtení tajného kódu ze služby Key Vault

V tomto kurzu si projdete nezbytné kroky pro využití webové aplikace Azure k načtení informací z trezoru klíčů pomocí identit spravované služby. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření trezoru klíčů
> * Uložení tajného kódu ve službě Key Vault
> * Vytvoření webové aplikace Azure
> * Povolení identit spravované služby
> * Udělení požadovaných oprávnění k načtení dat z trezoru klíčů pro aplikaci

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte mít Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

Pokud se chcete přihlásit k Azure pomocí rozhraní příkazového řádku, můžete zadat:

```azurecli
az login
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků *ContosoResourceGroup* v umístění *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "ContosoResourceGroup" --location "East US"
```

V tomto kurzu se používá skupina prostředků, kterou jste právě vytvořili.

## <a name="create-an-azure-key-vault"></a>Vytvoření služby Azure Key Vault

Dále ve skupině prostředků vytvořené v předchozím kroku vytvoříte službu Key Vault. Přestože se v tomto kurzu jako název pro trezor klíčů používá ContosoKeyVault, musíte použít jedinečný název. Zadejte tyto informace:

* Název trezoru **ContosoKeyVault**
* Název skupiny prostředků **ContosoResourceGroup**
* Umístění **Východní USA**

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East US"
```

Výstup tohoto příkazu zobrazuje vlastnosti nově vytvořené služby Key Vault. Poznamenejte si hodnoty dvou vlastností uvedených níže:

* **Název trezoru**: V tomto příkladu je to **ContosoKeyVault**. Tento název trezoru použijete ve všech příkazech Key Vault.
* **Identifikátor URI trezoru:** V tomto příkladu je to https://<YourKeyVaultName>.vault.azure.net/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

>[!IMPORTANT]
> Pokud se zobrazí chyba, parametr vault_name musí odpovídat tomuto vzoru: '^[a-zA-Z0-9-]{3,24}$' Hodnota parametru -name nebyla jedinečná nebo neodpovídala řetězci vytvořenému z 3 až 24 alfanumerických znaků.

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět jakékoli operace s tímto novým trezorem.

## <a name="add-a-secret-to-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Tajný klíč přidáváme proto, abychom ukázali, jak to funguje. Mohli byste ukládat připojovací řetězec SQL nebo jakékoli jiné informace, které potřebujete zabezpečeně uchovávat a současně zpřístupnit vaší aplikaci. V tomto kurzu budeme heslo označovat jako **AppSecret** a budeme v něm ukládat hodnotu **MySecret**.

Zadáním následujících příkazů vytvořte ve službě Key Vault tajný kód **AppSecret**, který bude uchovávat hodnotu **MySecret**:

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "AppSecret" --value "MySecret"
```

Pokud chcete zobrazit hodnotu v tajném kódu jako prostý text:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "ContosoKeyVault"
```

Tento příkaz zobrazí informace o tajném kódu, včetně identifikátoru URI. Po dokončení těchto kroků byste měli mít URI pro tajný kód ve službě Azure Key Vault. Poznamenejte si tyto informace. Budete je potřebovat později.

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

V této části vytvoříte aplikaci ASP.NET MVC a nasadíte ji do Azure jako webovou aplikaci. Další informace o Azure Web Apps najdete v [přehledu Web Apps](../app-service/app-service-web-overview.md).

1. Ve Visual Studiu vytvořte projekt tak, že vyberete **Soubor > Nový > Projekt**. 

2. V dialogovém okně **Nový projekt** vyberte **Visual C# > Web > Webová aplikace ASP.NET Core**.

3. Aplikaci pojmenujte **WebKeyVault** a potom vyberte **OK**.
   >[!IMPORTANT]
   > Jako název aplikace musíte použít WebKeyVault, aby kód, který zkopírujete a vložíte, odpovídal oboru názvů. Pokud web pojmenujete jinak, budete muset kód upravit tak, aby odpovídal tomuto názvu.

    ![Dialogové okno Nový projekt ASP.NET](media/tutorial-web-application-keyvault/aspnet-dialog.png)

4. Do Azure můžete nasadit jakýkoli typ webové aplikace ASP.NET Core. V tomto kurzu vyberte šablonu **Webová aplikace** a ujistěte se, že u ověřování je nastavena možnost **Bez ověření**.

    ![Dialogové okno ASPNET bez ověřování](media/tutorial-web-application-keyvault/aspnet-noauth.png)

5. Vyberte **OK**.

6. Jakmile se vytvoří projekt ASP.NET Core, zobrazí se úvodní stránka ASP.NET Core s řadou odkazů, které vám pomohou v začátcích.

7. V nabídce vyberte **Ladit > Spustit bez ladění** a spusťte tak webovou aplikaci místně.

## <a name="modify-the-web-app"></a>Úprava webové aplikace

Vaše webová aplikace musí mít nainstalované dva balíčky NuGet. Při jejich instalaci použijte následující postup:

1. V průzkumníku řešení klikněte pravým tlačítkem na název vašeho webu.
2. Vyberte **Spravovat balíčky NuGet pro řešení**.
3. Zaškrtněte políčko vedle vyhledávacího pole. **Zahrnout předběžné verze**
4. Vyhledejte dva balíčky NuGet uvedené níž a potvrďte jejich přidání do vašeho řešení:

    * [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) – usnadňuje načítání přístupových tokenů u scénářů ověřování služeb pro služby Azure. 
    * [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) – obsahuje metody pro interakci se službou Key Vault.

5. Pomocí Průzkumníka řešení otevřete `Program.cs` a obsah souboru Program.cs nahraďte následujícím kódem. Nahraďte ```<YourKeyVaultName>``` názvem vašeho trezoru klíčů:

    ```csharp
    
    using Microsoft.AspNetCore;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureKeyVault;
    
    namespace WebKeyVault
    {
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
    }
    ```

6. Pomocí Průzkumníka řešení přejděte do části **Stránky** a otevřete `About.cshtml`. Obsah **About.cshtml.cs** nahraďte následujícím kódem:

    ```csharp
    
    using Microsoft.AspNetCore.Mvc.RazorPages;
    using Microsoft.Extensions.Configuration;
    
    namespace WebKeyVault.Pages
    {
        public class AboutModel : PageModel
        {
            public AboutModel(IConfiguration configuration)
            {
                _configuration = configuration;
            }
    
            private readonly IConfiguration _configuration = null;
            public string Message { get; set; }
    
            public void OnGet()
            {
                Message = "My key val = " +  _configuration["AppSecret"];
            }
        }
    }
    
    ```

7. V hlavní nabídce zvolte **Ladit** > **Spustit bez ladění**. Jakmile se zobrazí prohlížeč, přejděte na stránku **O aplikaci**. Zobrazí se hodnota pro AppSecret.

>[!IMPORTANT]
> Pokud se zobrazí chyba protokolu HTTP 502.5 – Selhání procesu,
> > ověřte název trezoru klíčů uvedený v souboru `Program.cs`.

## <a name="publish-the-web-application-to-azure"></a>Publikování webové aplikace do Azure

1. Nad editorem vyberte **WebKeyVault**.
2. Vyberte **Publikovat** a potom **Spustit**.
3. Vytvořte novou službu **App Service** a vyberte **Publikovat**.
4. Vyberte **Vytvořit**.

>[!IMPORTANT]
> Otevře se okno prohlížeče a uvidíte zprávu 502.5 – Selhání procesu. To se očekává. Budete muset identitě aplikace udělit práva pro čtení tajných kódů ze služby Key Vault.

## <a name="enable-managed-service-identity"></a>Povolení identity spravované služby

Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů a dalších klíčů a tajných kódů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst. Identita spravované služby (MSI) usnadňuje řešení tohoto problému tím, že poskytuje službám Azure automaticky spravovanou identitu v Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření pro jakoukoli službu, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje.

1. Vraťte se k Azure CLI.
2. Spusťte příkaz assign-identity a vytvořte identitu pro tuto aplikaci:

```azurecli
az webapp identity assign --name "WebKeyVault" --resource-group "ContosoResourcegroup"
```

>[!NOTE]
>Tento příkaz je ekvivalentem přechodu na portál a nastavení **Identity spravované služby** na hodnotu **Zapnuto** ve vlastnostech webové aplikace.

## <a name="grant-rights-to-the-application-identity"></a>Přidělení práv k identitě aplikace

Použijte Azure Portal, přejděte k zásadám přístupu služby Key Vault a přidělte si ke službě Key Vault přístup pro správu tajných kódů. To vám umožní spouštět tuto aplikaci na místním počítači pro vývoj.

1. Vyhledejte váš trezor klíčů v dialogovém okně **Hledat prostředky** na webu Azure Portal.
2. Vyberte **Zásady přístupu**.
3. Vyberte **Přidat nové**, v části **Oprávnění na základě tajných kódů** vyberte **Získat** a **Vypsat seznam**.
4. Vyberte **Výběr objektu zabezpečení** a přidejte identitu aplikace. Bude mít stejný název jako aplikace.
5. Vyberte **OK**.

Váš účet v Azure a identita aplikace teď mají práva ke čtení informací ze služby Key Vault. Když obnovíte stránku, měli byste vidět úvodní stránku webu. Když vyberete **O aplikaci**, zobrazí se hodnota uložená ve službě Key Vault.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odstranit skupinu prostředků a všechny její prostředky, použijte příkaz **az group delete**.

  ```azurecli
  az group delete -n "ContosoResourceGroup"
  ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Průvodce vývojáře pro Azure Key Vault](key-vault-developers-guide.md)

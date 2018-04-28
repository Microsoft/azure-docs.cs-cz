---
title: Konfigurace Azure webovou aplikaci čtení tajného klíče z Key vault | Microsoft Docs
description: Kurz konfiguraci aplikace ASP.Net core čtení tajného klíče z Key vault
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
ms.assetid: ''
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 04/16/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 4a765b314b9879877bb6ff926e4a6584456b7823
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-configure-an-azure-web-application-to-read-a-secret-from-key-vault"></a>Kurz: Konfigurace Azure webovou aplikaci čtení tajného klíče z Key Vault

V tomto kurzu projít nezbytnými kroky k Azure webovou aplikaci při čtení informací z pomocí identity spravované služby Key vault. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření trezoru klíčů.
> * Uložení tajný klíč v Key Vault.
> * Vytvoření Azure webovou aplikaci.
> * Povolit identita spravované služby
> * Přidělení požadovaných oprávnění pro aplikaci číst data z Key vault.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte mít Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

K přihlášení do Azure pomocí rozhraní příkazového řádku, můžete zadat:

```azurecli
az login
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli
az group create --name ContosoResourceGroup --location eastus
```

V tomto kurzu se používá skupina prostředků, kterou jste právě vytvořili.

## <a name="create-an-azure-key-vault"></a>Vytvoření služby Azure Key Vault

Dále vytvoříte Key Vault ve skupině prostředků vytvořili v předchozím kroku. Je třeba zadat některé informace:

>[!NOTE]
> I když "ContosoKeyVault" slouží jako název pro naše Key Vault v rámci tohoto kurzu, musíte použít jedinečný název.

* Název trezoru **ContosoKeyVault**.
* Název skupiny prostředků **ContosoResourceGroup**.
* Umístění **východní USA**.

```azurecli
az keyvault create --name '<YourKeyVaultName>' --resource-group ContosoResourceGroup --location eastus
```

Výstup tohoto příkazu se zobrazují vlastnosti nově vytvořený klíč trezoru. Poznamenejte si dvě vlastnosti uvedené níže:

* **Název trezoru**: V tomto příkladu je to **ContosoKeyVault**. Název trezoru klíč bude použit pro všechny příkazy Key Vault.
* **Identifikátor URI trezoru**: V příkladu je to https://<YourKeyVaultName>.vault.azure.net/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

>[!IMPORTANT]
> Pokud se zobrazí chyba 'vault_name' musí odpovídat vzoru následující parametr: ' ^ [-zA-Z0 - 9 –] {3,24} $'-Název param hodnotu nebyla jedinečný nebo neodpovídala požadavkům na řetězec tvořený alfanumerické znaky ze 3 až 24 dlouho.

V tomto okamžiku účtu Azure je pouze jeden oprávněni provádět žádné operace na tento nový trezor.

## <a name="add-a-secret-to-key-vault"></a>Do Key vault přidat tajný klíč

Přidáváme tajný klíč pro lepší znázornění, jak to funguje. Může být ukládání připojovacího řetězce SQL nebo Další informace, které je potřeba zachovat bezpečně ale zpřístupnit do vaší aplikace. V tomto kurzu bude volána heslo **AppSecret** a uloží hodnotu **MySecret** v ní.

Zadejte následující příkazy k vytvoření tajného klíče v Key Vault názvem **AppSecret** , uloží hodnotu **MySecret**:

```azurecli
az keyvault secret set --vault-name '<YourKeyVaultName>' --name 'AppSecret' --value 'MySecret'
```

Pokud chcete zobrazit hodnotu v tajném kódu jako prostý text:

```azurecli
az keyvault secret show --name 'AppSecret' --vault-name '<YourKeyVaultName>'
```

Tento příkaz zobrazí tajné informace, včetně identifikátor URI. Po dokončení těchto kroků byste měli mít identifikátorů URI tajného klíče v Azure Key Vault. Poznamenejte si tyto informace. Budete ho potřebovat později.

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

V této části vytvoříte aplikaci ASP.NET MVC a nasadit v Azure jako webovou aplikaci. Další informace o službě Azure Web Apps, naleznete v části [přehled Web Apps](../app-service/app-service-web-overview.md).

1. Ve Visual Studiu vytvořte projekt tak, že vyberete **Soubor > Nový > Projekt**. 

2. V dialogovém okně **Nový projekt** vyberte **Visual C# > Web > Webová aplikace ASP.NET Core**.

3. Název aplikace **WebKeyVault**a potom vyberte **OK**.
   >[!IMPORTANT]
   > Název aplikace WebKeyVault musí, zkopírujte a vložte kód bude odpovídat oboru názvů. Pokud název lokality, jakékoli jiné potřebujete změnit kód tak, aby odpovídaly názvu serveru.

    ![Dialogové okno Nový projekt ASP.NET](media/tutorial-web-application-keyvault/aspnet-dialog.png)

4. Do Azure můžete nasadit jakýkoli typ webové aplikace ASP.NET Core. V tomto kurzu vyberte **webové aplikace** šablony a zajistěte, aby ověřování nastavena na **bez ověřování**.

    ![ASPNET žádné dialogové okno ověřování](media/tutorial-web-application-keyvault/aspnet-noauth.png)

5. Vyberte **OK**.

6. Jakmile se vytvoří projekt ASP.NET Core, zobrazí se úvodní stránka ASP.NET Core s řadou odkazů, které vám pomohou v začátcích.

7. V nabídce vyberte **Ladit > Spustit bez ladění** a spusťte tak webovou aplikaci místně.

## <a name="modify-the-web-app"></a>Úprava webové aplikace

Existují dva balíčky NuGet, které webové aplikace musí mít nainstalovaný. K instalaci je postupujte podle následujících kroků:

1. V řešení explorer klikněte pravým tlačítkem na název vašeho webu.
2. Vyberte **balíčků spravovat balíčky NuGet pro řešení...**
3. Zaškrtněte políčko vedle pole hledání. **Zahrnout předběžné verze**
4. Vyhledejte dva balíčky NuGet uvedené níže a přijměte je přidat do vašeho řešení:

    * [Microsoft.Azure.Services.AppAuthentication (preview)](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) -umožňuje snadno načíst tokeny přístupu ke pro scénáře ověřování služby pro Azure Service. 
    * [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/2.4.0-preview) – obsahuje metody pro interakci s Key Vault.

5. Pomocí Průzkumníku řešení otevřete `Program.cs` a nahraďte obsah souboru Program.cs následujícím kódem. Nahraďte ```<YourKeyVaultName>``` název trezoru klíčů:

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
             )
                .UseStartup<Startup>()
                .Build();
    
            private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
        }
        }
    ```

6. Pomocí Průzkumníku řešení můžete přejít do **stránky** části a otevřete `About.cshtml`. Nahraďte obsah **About.cshtml.cs** pomocí kódu níže:

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

7. Z hlavní nabídky zvolte **ladění** > **spustit bez ladění**. Jakmile se zobrazí v prohlížeči, přejděte na **o** stránky. Zobrazí se hodnota AppSecret.

>[!IMPORTANT]
> Pokud se zobrazí chyba 502.5 HTTP - zpráva o selhání procesu ověřte název trezoru klíč zadaný v `Program.cs`

## <a name="publish-the-web-application-to-azure"></a>Publikování webové aplikace do Azure

1. Výše editoru vyberte **WebKeyVault**.
2. Vyberte **publikování**.
3. Vyberte **publikovat** znovu.
4. Vyberte **vytvořit**.

>[!IMPORTANT]
> Otevře se okno prohlížeče a zobrazí se zpráva 502.5 - selhání procesu. Toto chování se očekává. Je potřeba udělit práva identity aplikace pro čtení tajných klíčů v trezoru klíč.

## <a name="enable-managed-service-identity"></a>Povolit identita spravované služby

Azure Key Vault poskytuje způsob, jak bezpečně uložit přihlašovací údaje a další klíče a tajné klíče, je však nutné kódu k ověření Key Vault je znovu načíst. Identita spravované služby (MSI) usnadňuje řešení tohoto problému jednodušší tím, že služby Azure automaticky spravované identity v Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault, bez nutnosti všechny přihlašovací údaje ve vašem kódu.

1. Vrátit do Azure CLI
2. Spusťte příkaz přiřadit identity pro vytvoření identity pro tuto aplikaci:

```azurecli
az webapp assign-identity --name WebKeyVault --resource-group ContosoResourcegroup
```

>[!NOTE]
>Jedná se o ekvivalent přejdete na portál a přepínání **identita spravované služby** k **na** ve vlastnostech webové aplikace.

## <a name="grant-rights-to-the-application-identity"></a>Udělení oprávnění k identitě aplikací

Pomocí portálu Azure, přejděte do zásad přístupu k trezoru klíč a přidělte si přístup pro správu tajný klíč do služby Key Vault. To vám umožní spuštění aplikace na místním vývojovém počítači.

1. Vyhledejte v Key Vault **hledat prostředky** dialogové okno na portálu Azure.
2. Vyberte **zásady přístupu**.
3. Vyberte **přidat nové**v **tajný oprávnění** části vyberte **získat** a **seznamu**.
4. Vyberte **vyberte hlavní**a přidejte identitu aplikace. Bude mít stejný název jako aplikace.
5. Zvolte **Ok**

Teď váš účet ve službě Azure a identita aplikací mít oprávnění ke čtení informací z Key Vault. Pokud obnovíte stránku, měli byste vidět cílová stránka lokality. Pokud vyberete **o**. Zobrazí hodnotu, kterou jste uložili v Key Vault.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odstranit skupinu prostředků a všechny její prostředky, použijte **odstranění skupiny az** příkaz.

  ```azurecli
  az group delete -n ContosoResourceGroup
  ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Příručka pro vývojáře Azure Key Vault](key-vault-developers-guide.md)

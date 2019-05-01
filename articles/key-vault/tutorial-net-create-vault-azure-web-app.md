---
title: Kurz – použití Azure Key Vault pomocí webové aplikace Azure v rozhraní .NET | Dokumentace Microsoftu
description: V tomto kurzu nakonfigurujete aplikaci ASP.NET core pro čtení tajného klíče z trezoru klíčů.
services: key-vault
author: mbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 42c8d863a58c5f5d8f47f6686aa9a5b8f80277d2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64710501"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Kurz: Azure Key Vault používat s webovou aplikaci Azure v .NET

Služba Azure Key Vault pomáhá chránit tajné kódy jako jsou klíče rozhraní API a databázové připojovací řetězce. To vám poskytne přístup k aplikacím, služby a materiály k seriálu IT.

V tomto kurzu se dozvíte, jak vytvořit webové aplikace Azure, který může číst informace z trezoru klíčů Azure. Proces využívá spravovaných identit pro prostředky Azure. Další informace o Azure webové aplikace najdete v tématu [služby Azure App Service](../app-service/overview.md).

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvoření trezoru klíčů
> * Přidání tajného klíče do trezoru klíčů.
> * Načtení tajného klíče z trezoru klíčů
> * Vytvoření webové aplikace Azure.
> * Povolte spravované identity pro webové aplikace.
> * Přiřadíte oprávnění pro webovou aplikaci.
> * Spuštění webové aplikace v Azure.

Než začnete, přečtěte si [základní koncepty služby Key Vault](key-vault-whatis.md#basic-concepts). 

Pokud ještě nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

* Pro Windows: [sady SDK .NET Core 2.1 nebo novější](https://www.microsoft.com/net/download/windows)
* Pro Mac: [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/)
* Pro Windows, Mac a Linux:
  * [Git](https://git-scm.com/downloads)
  * Tento kurz vyžaduje, abyste spustili Azure CLI místně. Musíte mít Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku, přečtěte si téma [Instalace Azure CLI 2.0](https://review.docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>Informace o funkci Identita spravované služby

Azure Key Vault ukládá přihlašovací údaje bezpečně, takže nejsou zobrazeny ve vašem kódu. Však musíte pro ověření do služby Azure Key Vault k načtení klíče. K ověření do služby Key Vault, budete potřebovat přihlašovací údaje. Je classic dilema bootstrap. Identita spravované služby (MSI) řeší tento problém tím, že poskytuje _bootstrap identity_ , která zjednodušuje proces.

Při povolení MSI pro službu Azure, jako jsou Azure Virtual Machines, Azure App Service nebo Azure Functions, Azure vytvoří [instanční objekt služby](key-vault-whatis.md#basic-concepts). MSI se k tomu pro instanci služby v Azure Active Directory (Azure AD) a vloží pověření instančního objektu do této instance.

![MSI diagram](media/MSI.png)

V dalším kroku k získání přístupového tokenu, váš kód volá služba místních metadat, která je k dispozici u prostředku Azure. Váš kód používá přístupový token, který získá z místního koncového bodu MSI pro ověření do služby Azure Key Vault.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete přihlásit k Azure pomocí Azure CLI, zadejte:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create).

Potom vyberte název skupiny prostředků a vyplňte zástupný symbol. Následující příklad vytvoří skupinu prostředků v umístění Západní USA:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Tato skupina prostředků v celém tomto kurzu použijete.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Vytvoření služby key vault ve vaší skupině prostředků, zadejte následující informace:

* Název trezoru klíčů: řetězec dlouhý 3 až 24 znaků, které mohou obsahovat pouze číslice (0 – 9), písmena (a – z, A-Z) a pomlčky (-)
* Název skupiny prostředků
* Umístění: **Západní USA**

V rozhraní příkazového řádku Azure zadejte následující příkaz:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Váš účet Azure v tuto chvíli je pouze jeden, který má oprávnění k provádění operací s tímto novým trezorem.

## <a name="add-a-secret-to-the-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Nyní můžete přidat tajný klíč. Může být připojovací řetězec SQL nebo nějakých jiných informací, které potřebujete k zabezpečení a k dispozici pro vaši aplikaci.

Vytvoření tajného klíče v trezoru klíčů s názvem **AppSecret**, zadejte následující příkaz: 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Tento tajný kód ukládá hodnotu **MySecret**.

Chcete-li zobrazit hodnotu, která je obsažena v tajném kódu jako prostý text, zadejte následující příkaz:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Tento příkaz zobrazí tajné informace, včetně identifikátoru URI. 

Po dokončení těchto kroků byste měli mít identifikátor URI pro tajný klíč v trezoru klíčů. Poznamenejte si tyto informace použijete později v tomto kurzu. 

## <a name="create-a-net-core-web-app"></a>Vytvoření webové aplikace .NET Core

Vytvoření webové aplikace .NET Core a publikujete ji do Azure, postupujte podle pokynů v [vytvoření webové aplikace ASP.NET Core v Azure](../app-service/app-service-web-get-started-dotnet.md). 

Můžete se také podívat na toto video:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Otevření a úprava řešení

1. Přejděte **stránky** > **About.cshtml.cs** souboru.

1. Instalace těchto balíčků NuGet:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Importovat následující kód, který *About.cshtml.cs* souboru:

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   Váš kód ve třídě AboutModel by měl vypadat takto:

   ```csharp
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The next four lines of code show you how to use AppAuthentication library to fetch secrets from your key vault */
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;
            }
            /* If you have throttling errors see this tutorial https://docs.microsoft.com/azure/key-vault/tutorial-net-create-vault-azure-web-app */
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
            }
        }

        // This method implements exponential backoff if there are 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory, which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-web-app"></a>Spuštění webové aplikace

1. V hlavní nabídce sady Visual Studio 2017, vyberte **ladění** > **Start**, s nebo bez ladění. 
1. V prohlížeči přejděte **o** stránky.  
    Zobrazí se hodnota tajného klíče **AppSecret**.

## <a name="enable-a-managed-identity"></a>Povolit spravované identity

Služba Azure Key Vault poskytuje způsob, jak bezpečně ukládat přihlašovací údaje a dalších tajných kódů, ale váš kód potřebuje ověřit do služby Key Vault je načítat. [Spravovaných identit pro prostředky Azure přehled](../active-directory/managed-identities-azure-resources/overview.md) pomáhá tento problém vyřešit tím, že Azure services automaticky spravovanou identitu ve službě Azure AD. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste museli zobrazovat přihlašovací údaje ve vašem kódu.

V rozhraní příkazového řádku Azure CLI vytvořit identitu pro tuto aplikaci, spusťte příkaz přiřadit identity:

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

Nahraďte \<YourAppName > s názvem publikované aplikace v Azure.  
    Například, pokud byl název vaší aplikace publikované **MyAwesomeapp.azurewebsites.net**, nahraďte \<YourAppName > s **MyAwesomeapp**.

Poznamenejte si, `PrincipalId` po publikování aplikace do Azure. Výstup tohoto příkazu v kroku 1 by měla být v následujícím formátu:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>Příkaz v tomto postupu je ekvivalentem chystáte [webu Azure portal](https://portal.azure.com) a přepnete **Identity / systém přiřadil** nastavení **na** ve webové aplikaci Vlastnosti.

## <a name="assign-permissions-to-your-app"></a>Přiřazení oprávnění pro aplikaci

Nahraďte \<YourKeyVaultName > s názvem služby key vault, a nahraďte \<PrincipalId > hodnotou identifikátoru **PrincipalId** v následujícím příkazu:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Tento příkaz poskytuje identitu (MSI) služby oprávnění aplikace provedete **získat** a **seznamu** operace v trezoru klíčů.

## <a name="publish-the-web-app-to-azure"></a>Publikování webové aplikace do služby Azure

Publikování webové aplikace do Azure ještě jednou a ověřte, že svou živou webovou aplikaci můžete načíst hodnotu tajného kódu.

1. V sadě Visual Studio vyberte projekt **key-vault-dotnet-core-quickstart**.
2. Vyberte **Publikovat** > **Spustit**.
3. Vyberte **Vytvořit**.

Při spuštění aplikace, měli byste vidět, že můžete načíst tajná hodnota.

Teď jste úspěšně vytvořili webovou aplikaci v .NET, která ukládá a načítá jeho tajné klíče z trezoru klíčů.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už je nepotřebujete, můžete odstranit virtuální počítač a trezor klíčů.

## <a name="next-steps"></a>Další postup

>[!div class="nextstepaction"]
>[Průvodce vývojáře pro Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-developers-guide)

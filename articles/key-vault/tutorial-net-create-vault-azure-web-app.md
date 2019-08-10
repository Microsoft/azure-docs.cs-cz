---
title: Kurz – použití Azure Key Vault s webovou aplikací Azure v .NET | Microsoft Docs
description: V tomto kurzu nakonfigurujete aplikaci ASP.NET Core pro čtení tajného klíče z vašeho trezoru klíčů.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: c27c787eeac9bbf68b512b55b9ceab11074a81d8
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934356"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Kurz: Použití Azure Key Vault s webovou aplikací Azure v .NET

Azure Key Vault vám pomůže s ochranou tajných kódů, jako jsou klíče rozhraní API a databázové připojovací řetězce. Poskytuje přístup k vašim aplikacím, službám a prostředkům IT.

V tomto kurzu se naučíte, jak vytvořit webovou aplikaci Azure, která může číst informace z trezoru klíčů Azure. Proces používá spravované identity pro prostředky Azure. Další informace o webových aplikacích Azure najdete v tématu [Azure App Service](../app-service/overview.md).

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvoření trezoru klíčů
> * Přidejte tajný klíč do trezoru klíčů.
> * Načtení tajného klíče z trezoru klíčů
> * Vytvořte webovou aplikaci Azure.
> * Povolte spravovanou identitu pro webovou aplikaci.
> * Přiřaďte oprávnění pro webovou aplikaci.
> * Spusťte webovou aplikaci v Azure.

Než začnete, přečtěte si téma [Key Vault Basic koncepty](key-vault-whatis.md#basic-concepts). 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

* Pro Windows: [.NET Core 2,1 SDK nebo novější](https://www.microsoft.com/net/download/windows)
* Pro Mac: [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)
* Pro Windows, Mac a Linux:
  * [Git](https://git-scm.com/downloads)
  * Tento kurz vyžaduje, abyste spouštěli Azure CLI místně. Musíte mít nainstalovanou verzi Azure CLI 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku, přečtěte si téma [Instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>Informace o funkci Identita spravované služby

Azure Key Vault ukládá pověření bezpečně, takže se ve vašem kódu nezobrazují. K načtení klíčů ale musíte ověřit, Azure Key Vault. K ověření Key Vault budete potřebovat přihlašovací údaje. Je to klasický spouštěcí dilematem. Identita spravované služby (MSI) Tento problém řeší tím, že poskytuje _identitu_ spuštění, která zjednodušuje proces.

Když povolíte MSI pro službu Azure, například Azure Virtual Machines, Azure App Service nebo Azure Functions, vytvoří [Služba Azure instanční objekt](key-vault-whatis.md#basic-concepts). Služba MSI to provede pro instanci služby v Azure Active Directory (Azure AD) a vloží do této instance přihlašovací údaje instančního objektu.

![Diagram MSI](media/MSI.png)

Pokud chcete získat přístupový token, váš kód volá místní službu metadat, která je k dispozici v prostředku Azure. Váš kód používá přístupový token, který získává z místního koncového bodu MSI k ověření pro službu Azure Key Vault.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete přihlásit k Azure pomocí Azure CLI, zadejte:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create).

Pak vyberte název skupiny prostředků a vyplňte zástupný symbol. Následující příklad vytvoří skupinu prostředků v umístění Západní USA:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Tuto skupinu prostředků použijete v rámci tohoto kurzu.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Pokud chcete vytvořit Trezor klíčů ve skupině prostředků, zadejte následující informace:

* Název trezoru klíčů: řetězec na 3 až 24 znaků, který může obsahovat jenom číslice (0-9), písmena (a-z, A-Z) a spojovníky (-).
* Název skupiny prostředků
* Umístění: **Západní USA**

V rozhraní příkazového řádku Azure CLI zadejte tento příkaz:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

V tomto okamžiku je váš účet Azure jediným z nich, který je autorizovaný k provádění operací v tomto novém trezoru.

## <a name="add-a-secret-to-the-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Nyní můžete přidat tajný klíč. Může to být připojovací řetězec SQL nebo jakékoli jiné informace, které potřebujete k zajištění zabezpečení i k dispozici pro vaši aplikaci.

Pokud chcete v trezoru klíčů s názvem **AppSecret**vytvořit tajný klíč, zadejte následující příkaz: 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Tento tajný kód ukládá hodnotu **MySecret**.

Chcete-li zobrazit hodnotu, která je obsažena v tajném klíči jako prostý text, zadejte následující příkaz:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Tento příkaz zobrazí tajné informace, včetně identifikátoru URI. 

Po dokončení těchto kroků byste měli mít identifikátor URI pro tajný klíč v trezoru klíčů. Tyto informace si poznamenejte pro pozdější použití v tomto kurzu. 

## <a name="create-a-net-core-web-app"></a>Vytvoření webové aplikace .NET Core

Pokud chcete vytvořit webovou aplikaci .NET Core a publikovat ji v Azure, postupujte podle pokynů v tématu [vytvoření ASP.NET Core webové aplikace v Azure](../app-service/app-service-web-get-started-dotnet.md). 

Můžete se také podívat na toto video:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Otevření a úprava řešení

1. Přejít na soubor**About.cshtml.cs** **stránky** > .

1. Nainstalujte tyto balíčky NuGet:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Do souboru *About.cshtml.cs* importujte následující kód:

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

1. V hlavní nabídce sady Visual Studio 2019 vyberte **ladit** > **Spustit**s laděním nebo bez něj. 
1. V prohlížeči přejdete na stránku **o produktu** .  
    Zobrazí se hodnota tajného klíče **AppSecret**.

## <a name="enable-a-managed-identity"></a>Povolení spravované identity

Azure Key Vault poskytuje způsob, jak bezpečně ukládat přihlašovací údaje a další tajné kódy, ale váš kód musí ověřit, aby se Key Vaulty načetly. [Přehled spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) pomáhá vyřešit tento problém tím, že poskytuje službám Azure automaticky spravovanou identitu ve službě Azure AD. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, včetně Key Vault, bez nutnosti zobrazovat přihlašovací údaje v kódu.

V Azure CLI pro vytvoření identity pro tuto aplikaci spusťte příkaz Assign-identity:

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

Nahraďte \<soubor YourAppName > názvem publikované aplikace v Azure.  
    Pokud jste například název publikované aplikace **MyAwesomeapp.azurewebsites.NET**, nahraďte \<soubor YourAppName > pomocí **MyAwesomeapp**.

Při publikování aplikace do Azure `PrincipalId` si poznamenejte, jak je třeba. Výstup příkazu v kroku 1 by měl být v následujícím formátu:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>Příkaz v tomto postupu je ekvivalentem přechodu na [Azure Portal](https://portal.azure.com) a přepnutím nastavení **identity/systému přiřazené k nástroji** ve vlastnostech webové aplikace.

## <a name="assign-permissions-to-your-app"></a>Přiřazení oprávnění k aplikaci

Nahraďte \<YourKeyVaultName > názvem vašeho trezoru klíčů a nahraďte \<PrincipalId > hodnotou **PrincipalId** v následujícím příkazu:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Tento příkaz poskytuje identitu (MSI) oprávnění App Service, která umožňuje **získat** a **Zobrazit** operace v trezoru klíčů.

## <a name="publish-the-web-app-to-azure"></a>Publikování webové aplikace do služby Azure

Znovu publikujte webovou aplikaci do Azure, abyste ověřili, že živá webová aplikace dokáže načíst tajnou hodnotu.

1. V sadě Visual Studio vyberte projekt **key-vault-dotnet-core-quickstart**.
2. Vyberte **Publikovat** > **Spustit**.
3. Vyberte **Vytvořit**.

Při spuštění aplikace byste měli vidět, že může načíst vaši tajnou hodnotu.

Nyní jste úspěšně vytvořili webovou aplikaci v rozhraní .NET, která ukládá a načítá své tajné klíče z trezoru klíčů.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už je nepotřebujete, můžete virtuální počítač a trezor klíčů odstranit.

## <a name="next-steps"></a>Další postup

>[!div class="nextstepaction"]
>[Průvodce vývojáře pro Azure Key Vault](key-vault-developers-guide.md)

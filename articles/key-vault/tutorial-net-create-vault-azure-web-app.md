---
title: Kurz – použití Azure Key Vault pomocí webové aplikace Azure v rozhraní .NET | Dokumentace Microsoftu
description: Kurz – konfigurace aplikace ASP.NET core pro čtení tajného klíče ze služby Key vault
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 44f93cc72915b378d0e05287deff3540fe347f99
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995129"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Kurz: Azure Key Vault používat s webovou aplikaci Azure v .NET

Služba Azure Key Vault pomáhá chránit tajné kódy jako jsou klíče rozhraní API a databázové připojovací řetězce. To vám poskytne přístup k aplikacím, služby a materiály k seriálu IT.

V tomto kurzu se dozvíte, jak vytvořit webové aplikace Azure, který může číst informace z trezoru klíčů Azure. Proces využívá spravovaných identit pro prostředky Azure. Další informace o Azure webové aplikace najdete v tématu [služby Azure App Service](../app-service/overview.md).

Tento článek ukazuje, jak do:

> [!div class="checklist"]
> * Vytvoření trezoru klíčů
> * Uložení tajného klíče v trezoru klíčů
> * Načtení tajného klíče z trezoru klíčů
> * Vytvoření webové aplikace Azure
> * Povolení [spravované identity](../active-directory/managed-identities-azure-resources/overview.md) pro webovou aplikaci
> * Udělení požadovaných oprávnění k načtení dat z trezoru klíčů pro webovou aplikaci
> * Spusťte webovou aplikaci v Azure.

Než budete pokračovat, přečtěte si [základní koncepty služby Key Vault](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Požadavky

* Ve Windows:
  * [Sada .NET Core SDK 2.1 nebo novější](https://www.microsoft.com/net/download/windows)

* Na počítači Mac:
  * [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/)

* Všechny platformy:
  * [Git](https://git-scm.com/downloads)
  * Předplatné Azure <br />(Pokud ještě nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před zahájením.)
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) verze 2.0.4 nebo novější, která je dostupná pro Windows, Mac a Linux
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="managed-service-identity-and-how-it-works"></a>Identita spravované služby a jak to funguje

Azure Key Vault bezpečně ukládá přihlašovací údaje, a proto nejsou ve vašem kódu. Však musíte pro ověření do služby Azure Key Vault k načtení klíče. K ověření do služby Key Vault, budete potřebovat přihlašovací údaje. Je classic dilema bootstrap. Identita spravované služby (MSI) řeší tento problém tím, že poskytuje _bootstrap identity_ , která zjednodušuje proces.

Při povolení MSI pro služby Azure (třeba: Virtuální počítače, služby App Service nebo funkce), Azure vytvoří [instanční objekt služby](key-vault-whatis.md#basic-concepts). MSI se k tomu pro instanci služby v Azure Active Directory (Azure AD) a vkládá přihlašovací údaje pro instanční objekt do této instance.

![MSI diagram](media/MSI.png)

V dalším kroku váš kód volá místních metadat služba k dispozici u prostředku Azure získat přístupový token. Váš kód použije přístupový token, který získá z místního koncového bodu MSI_ENDPOINT, k ověření ve službě Azure Key Vault.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure pomocí rozhraní příkazového řádku Azure, zadejte:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

1. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create).
1. Vyberte název skupiny prostředků a nahraďte zástupný text. Následující příklad vytvoří skupinu prostředků v umístění Západní USA:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Tato skupina prostředků v celém tomto kurzu použijete.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Vytvoření služby key vault ve vaší skupině prostředků, zadejte následující informace:

* Název trezoru klíčů: řetězec dlouhý 3 až 24 znaků, které mohou obsahovat pouze číslice, písmena a pomlčky (například: 0 – 9, a – z, A-Z - a)
* Název skupiny prostředků
* Umístění: **Západní USA**

Zadejte následující příkaz v rozhraní příkazového řádku Azure:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět jakékoli operace s tímto novým trezorem.

## <a name="add-a-secret-to-the-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Nyní můžete přidat tajný klíč. Může být připojovací řetězec SQL nebo nějakých jiných informací, které potřebujete k zabezpečení a k dispozici pro vaši aplikaci.

Typ s názvem následující příkaz pro vytvoření tajného klíče v trezoru klíčů **AppSecret**. Tento tajný kód ukládá hodnotu **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Chcete-li zobrazit hodnotu v tajném kódu jako prostý text, zadejte následující příkaz:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Tento příkaz zobrazí informace o tajném klíči, včetně identifikátoru URI. Po dokončení těchto kroků byste měli mít identifikátor URI pro tajný klíč v trezoru klíčů. Poznamenejte si tyto informace. Budete je potřebovat později.

## <a name="create-a-net-core-web-app"></a>Vytvoření webové aplikace .NET Core

Použít tento [kurzu](../app-service/app-service-web-get-started-dotnet.md) k vytvoření webové aplikace .NET Core a **publikovat** ji do Azure. Můžete také zhlédnout následující video:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Otevření a úprava řešení

1. Přejděte **stránky** > **About.cshtml.cs** souboru.
2. Instalace těchto balíčků NuGet:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. Naimportujte následující kód do souboru About.cshtml.cs:

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

4. Váš kód ve třídě AboutModel by takto:

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
                /* The below 4 lines of code shows you how to use AppAuthentication library to fetch secrets from your Key Vault*/
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;

                /* The below do while logic is to handle throttling errors thrown by Azure Key Vault. It shows how to do exponential backoff which is the recommended client side throttling*/
                do
                {
                    long waitTime = Math.Min(getWaitTime(retries), 2000000);
                    secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                    retry = false;
                } 
                while(retry && (retries++ < 10));
            }
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
                if((int)keyVaultException.Response.StatusCode == 429)
                    retry = true;
            }
        }

        // This method implements exponential backoff incase of 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-app"></a>Spuštění aplikace

1. V hlavní nabídce sady Visual Studio 2017, vyberte **ladění** > **Start** s nebo bez ladění. 
1. Jakmile se zobrazí prohlížeč, přejděte na stránku **O aplikaci**.
1. Zobrazí se hodnota tajného klíče **AppSecret**.

## <a name="enable-a-managed-identity-for-the-web-app"></a>Povolení spravované identity pro webovou aplikaci

Služba Azure Key Vault poskytuje způsob, jak bezpečně ukládat přihlašovací údaje a dalších tajných kódů, ale váš kód potřebuje ověřit do služby Key Vault je načítat. [Spravovaných identit pro prostředky Azure přehled](../active-directory/managed-identities-azure-resources/overview.md) pomáhá tento problém vyřešit tím, že Azure services automaticky spravovanou identitu ve službě Azure AD. Tuto identitu můžete použít k ověření pro jakoukoli službu, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje.

1. V Azure CLI spusťte příkaz přiřadit identity vytvořit identitu pro tuto aplikaci:

   ```azurecli

   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"

   ```

   >[!NOTE]
   >Je nutné nahradit \<YourAppName\> s názvem publikované aplikace v Azure. Například, pokud byl název vaší aplikace publikované **MyAwesomeapp.azurewebsites.net**, nahraďte \<YourAppName\> s **MyAwesomeapp**.

1. Poznamenejte si, `PrincipalId` po publikování aplikace do Azure. Výstup tohoto příkazu v kroku 1 by měla být v následujícím formátu:

   ```json
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
   ```

>[!NOTE]
>Příkaz v tomto postupu je ekvivalentem přechodu na [portál](https://portal.azure.com) a přepnutí nastavení **Identita / Přiřazeno systémem** na hodnotu **Zapnuto** ve vlastnostech webové aplikace.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Přiřazení oprávnění ke čtení tajných kódů ze služby Key Vault vaší aplikaci

Nahraďte \<YourKeyVaultName\> s názvem trezoru klíčů a \<PrincipalId\> s hodnotou **PrincipalId** v následujícím příkazu:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Tento příkaz poskytuje identitu aplikace (MSI) služby oprávnění k proveďte **získat** a **seznamu** operace v trezoru klíčů.

## <a name="publish-the-web-application-to-azure"></a>Publikování webové aplikace do Azure

Publikování webové aplikace do Azure jednou znovu, abyste viděli, že svou živou webovou aplikaci můžete načíst hodnotu tajného kódu.

1. V sadě Visual Studio vyberte projekt **key-vault-dotnet-core-quickstart**.
2. Vyberte **Publikovat** > **Spustit**.
3. Vyberte **Vytvořit**.

Při spuštění aplikace, měli byste vidět, že můžete načíst tajná hodnota.

Nyní teď úspěšně jste vytvořili webovou aplikaci v .NET, která ukládá a načítá jeho tajné klíče ze služby Key Vault.

## <a name="next-steps"></a>Další postup

>[!div class="nextstepaction"]
>[Průvodce vývojáře pro Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-developers-guide)

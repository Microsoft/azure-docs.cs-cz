---
title: Kurz – použití trezoru klíčů Azure s webovou aplikací Azure v rozhraní .NET | Dokumenty společnosti Microsoft
description: V tomto kurzu nakonfigurujete základní aplikaci ASP.NET pro čtení tajného klíče z trezoru klíčů.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: bb907c809d411128ee799d5057379a2022144882
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422876"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Kurz: Použití Azure Key Vault u webové aplikace Azure v rozhraní .NET

Azure Key Vault pomáhá chránit tajné klíče, jako jsou klíče rozhraní API a připojovací řetězce databáze. Poskytuje přístup k vašim aplikacím, službám a prostředkům IT.

V tomto kurzu se dozvíte, jak vytvořit webovou aplikaci Azure, která umí číst informace z trezoru klíčů Azure. Proces používá spravované identity pro prostředky Azure. Další informace o webových aplikacích Azure najdete v [tématu Azure App Service](../../app-service/overview.md).

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Vytvoření trezoru klíčů
> * Přidejte tajný klíč do trezoru klíčů.
> * Načtení tajného klíče z trezoru klíčů
> * Vytvořte webovou aplikaci Azure.
> * Povolení spravované identity pro webovou aplikaci
> * Přiřaďte oprávnění pro webovou aplikaci.
> * Spusťte webovou aplikaci v Azure.

Než začnete, přečtěte si [základní koncepty trezoru klíčů](basic-concepts.md). 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

* Pro Windows: [.NET Core 2.1 SDK nebo novější](https://www.microsoft.com/net/download/windows)
* Pro Mac: [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/)
* Pro Windows, Mac a Linux:
  * [Git](https://git-scm.com/downloads)
  * Tento kurz vyžaduje, abyste místní spouštění příkazového příkazového odložení příkazového odlokala. Musíte mít nainstalovanou verzi Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku, přečtěte si téma [Instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>Informace o funkci Identita spravované služby

Azure Key Vault ukládá přihlašovací údaje bezpečně, takže nejsou zobrazeny ve vašem kódu. Je však nutné ověřit azure key vault načíst klíče. K ověření trezoru klíčů potřebujete pověření. Je to klasické dilema bootstrap. Identita spravované služby (MSI) řeší tento problém poskytnutím _identity zaváděcí pasti,_ která zjednodušuje proces.

Když povolíte MSI pro službu Azure, jako jsou virtuální počítače Azure, Azure App Service nebo Azure Functions, Azure vytvoří [instanční objekt .](basic-concepts.md) MSI to pro instanci služby ve službě Azure Active Directory (Azure AD) a vloží pověření instancí instancí.

![Diagram MSI](../media/MSI.png)

Dále chcete-li získat přístupový token, váš kód volá místní službu metadat, která je k dispozici na prostředek Azure. Váš kód používá přístupový token, který získá z místního koncového bodu MSI k ověření služby Azure Key Vault.

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Pokud se chcete přihlásit k Azure pomocí Azure CLI, zadejte:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create).

Potom vyberte název skupiny prostředků a vyplňte zástupný symbol. Následující příklad vytvoří skupinu prostředků v umístění USA – západ:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Tuto skupinu prostředků použijete v průběhu tohoto kurzu.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Chcete-li vytvořit trezor klíčů ve skupině prostředků, zadejte následující informace:

* Název trezoru klíčů: řetězec 3 až 24 znaků, který může obsahovat pouze čísla (0-9), písmena (a-z, A-Z) a pomlčky (-)
* Název skupiny prostředků
* Umístění: **Západní USA**

Do příkazového příkazu k příkazu Azure zadejte následující příkaz:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

V tomto okamžiku je váš účet Azure jediný, který je oprávněn provádět operace v tomto novém trezoru.

## <a name="add-a-secret-to-the-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Nyní můžete přidat tajemství. Může se jedná o připojovací řetězec SQL nebo jakékoli jiné informace, které potřebujete zachovat bezpečné a dostupné pro vaši aplikaci.

Chcete-li vytvořit tajný klíč v trezoru klíčů s názvem **AppSecret**, zadejte následující příkaz: 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Tento tajný klíč ukládá hodnotu **MySecret**.

Chcete-li zobrazit hodnotu obsaženou v tajném klíči jako prostý text, zadejte následující příkaz:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Tento příkaz zobrazí tajné informace, včetně identifikátoru URI. 

Po dokončení těchto kroků byste měli mít identifikátor URI pro tajný klíč v trezoru klíčů. Poznamenejte si tyto informace pro pozdější použití v tomto kurzu. 

## <a name="create-a-net-core-web-app"></a>Vytvoření webové aplikace .NET Core

Pokud chcete vytvořit webovou aplikaci .NET Core a publikovat ji do Azure, postupujte podle pokynů v části [Vytvoření ASP.NET základní webové aplikace v Azure](../../app-service/app-service-web-get-started-dotnet.md). 

Můžete se také podívat na toto video:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Otevření a úprava řešení

1. Přejděte do souboru**About.cshtml.cs** **stránek.** > 

1. Nainstalujte tyto balíčky NuGet:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Importujte do *About.cshtml.cs* souboru následující kód:

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

1. V hlavní nabídce Visual Studia 2019 vyberte **Ladění** > **startu**, s laděním nebo bez něj. 
1. V prohlížeči přejděte na stránku **Informace.**  
    Zobrazí se hodnota tajného klíče **AppSecret**.

## <a name="enable-a-managed-identity"></a>Povolení spravované identity

Azure Key Vault poskytuje způsob, jak bezpečně ukládat přihlašovací údaje a další tajné klíče, ale váš kód potřebuje k ověření key vault načíst. [Přehled spravovaných identit pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md) pomáhá tento problém vyřešit tím, že služby Azure automaticky spravované identity ve službě Azure AD. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, včetně trezoru klíčů, bez nutnosti zobrazení přihlašovacích údajů ve vašem kódu.

V nastavení příkazového příkazu K Ono Azure, chcete-li vytvořit identitu pro tuto aplikaci, spusťte příkaz assign-identity:

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

Nahraďte \<> YourAppName názvem publikované aplikace v Azure.  
    Pokud byl například název publikované aplikace \< **MyAwesomeapp.azurewebsites.net**, nahraďte> YourAppName **> myawesomeapp**.

Poznamenejte `PrincipalId` si při publikování aplikace do Azure. Výstup příkazu v kroku 1 by měl být v následujícím formátu:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>Příkaz v tomto postupu je ekvivalentní přechodu na [portál Azure](https://portal.azure.com) a přepnutí nastavení **Identity / System přiřazené** k **Zapnuto** ve vlastnostech webové aplikace.

## <a name="assign-permissions-to-your-app"></a>Přiřazení oprávnění k aplikaci

Nahraďte \<> KeyVaultName názvem trezoru klíčů \<a nahraďte> PrincipalId hodnotou **PrincipalId** v následujícím příkazu:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Tento příkaz poskytuje identitě (MSI) oprávnění služby aplikace k **získání** a **zobrazení** seznamu operací v trezoru klíčů.

## <a name="publish-the-web-app-to-azure"></a>Publikování webové aplikace do služby Azure

Znovu publikujte webovou aplikaci do Azure a ověřte, že vaše živá webová aplikace dokáže načíst tajnou hodnotu.

1. V sadě Visual Studio vyberte projekt **key-vault-dotnet-core-quickstart**.
2. Vyberte **možnost Publikovat** > **úvodní položku**.
3. Vyberte **Vytvořit**.

Při spuštění aplikace, měli byste vidět, že může načíst hodnotu tajného klíče.

Nyní jste úspěšně vytvořili webovou aplikaci v rozhraní .NET, která ukládá a načítá její tajné klíče z trezoru klíčů.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už nejsou potřeba, můžete odstranit virtuální počítač a trezor klíčů.

## <a name="next-steps"></a>Další kroky

>[!div class="nextstepaction"]
>[Průvodce vývojáře pro Azure Key Vault](developers-guide.md)

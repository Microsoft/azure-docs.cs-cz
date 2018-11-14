---
title: Kurz – Jak používat Azure Key Vault s webovou aplikací Azure v .NET | Microsoft Docs
description: 'Kurz: Konfigurace aplikace ASP.NET Core pro čtení tajného klíče z trezoru klíčů'
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
ms.openlocfilehash: 9cc22e158a9473b7b60f7e8bcb57174abc1fb8cc
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218548"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-web-app-in-net"></a>Kurz: Jak používat Azure Key Vault s webovou aplikací Azure v .NET

Azure Key Vault pomáhá chránit tajné klíče, jako jsou klíče rozhraní API nebo databázové připojovací řetězce potřebné pro přístup k aplikacím, službám a prostředkům IT.

V tomto kurzu si projdete nezbytné kroky pro využití webové aplikace Azure k načtení informací ze služby Azure Key Vault pomocí spravovaných identit pro prostředky Azure. Tento kurz je založený na službě [Azure Web Apps](../app-service/app-service-web-overview.md). V následujících částech získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření trezoru klíčů
> * Uložení tajného klíče v trezoru klíčů
> * Načtení tajného klíče z trezoru klíčů
> * Vytvoření webové aplikace Azure
> * Povolení [spravované identity](../active-directory/managed-identities-azure-resources/overview.md) pro webovou aplikaci
> * Udělení požadovaných oprávnění k načtení dat z trezoru klíčů pro webovou aplikaci
> * Spuštění webové aplikace v Azure

Než budeme pokračovat, přečtěte si [základní koncepty](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Požadavky

* Ve Windows:
  * [Sada .NET Core SDK 2.1 nebo novější](https://www.microsoft.com/net/download/windows)

* Na počítači Mac:
  * Přečtěte si, [Co je nového v sadě Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).

* Všechny platformy:
  * Git ([stáhnout](https://git-scm.com/downloads)).
  * Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) verze 2.0.4 nebo novější. K dispozici pro Windows, Mac a Linux.
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>Co je Identita spravované služby a jak funguje?
 Než budeme pokračovat, seznámíme se s MSI. Azure Key Vault umožňuje bezpečné ukládání přihlašovacích údajů, aby nemusely být v kódu. Abyste je však mohli načíst, musíte se ověřit ve službě Azure Key Vault. K ověření ve službě Key Vault potřebujete přihlašovací údaje. Jedná se o klasický problém. Prostřednictvím spojení Azure a Azure AD poskytuje MSI spouštěcí identitu, která výrazně usnadňuje začátek práce.

Funguje to takto. Když povolíte MSI pro službu Azure, jako je Virtual Machines, App Service nebo Functions, Azure vytvoří [instanční objekt](key-vault-whatis.md#basic-concepts) pro instanci služby v Azure Active Directory a vloží do instance služby přihlašovací údaje k instančnímu objektu. 

![MSI](media/MSI.png)

Potom váš kód zavolá místní službu Metadata Service, která je dostupná v prostředku Azure, a získá přístupový token.
Váš kód použije přístupový token, který získá z místního koncového bodu MSI_ENDPOINT, k ověření ve službě Azure Key Vault. 

Teď můžeme začít s kurzem.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete přihlásit k Azure pomocí Azure CLI, zadejte:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vyberte název skupiny prostředků a nahraďte zástupný text.
Následující příklad vytvoří skupinu prostředků v umístění USA – západ:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

V tomto článku se používá skupina prostředků, kterou jste právě vytvořili.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Dále ve skupině prostředků, kterou jste vytvořili v předchozím kroku, vytvoříte trezor klíčů. Zadejte tyto informace:

* Název trezoru klíčů: Název musí být řetězec dlouhý 3 až 24 znaků a může obsahovat pouze následující znaky: 0–9, a–z, A–Z a spojovník (-).
* Název skupiny prostředků.
* Umístění: **USA – západ**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět jakékoli operace s tímto novým trezorem.

## <a name="add-a-secret-to-the-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Tajný klíč přidáváme proto, abychom ukázali, jak to funguje. Mohli byste ukládat připojovací řetězec SQL nebo jakékoli jiné informace, které potřebujete zabezpečeně uchovávat a současně zpřístupnit vaší aplikaci.

Zadáním následujících příkazů vytvořte v trezoru klíčů tajný klíč **AppSecret**. V tomto tajném klíči bude uložená hodnota **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Pokud chcete zobrazit hodnotu v tajném kódu jako prostý text:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Tento příkaz zobrazí informace o tajném klíči, včetně identifikátoru URI. Po dokončení těchto kroků byste měli mít identifikátor URI pro tajný klíč v trezoru klíčů. Poznamenejte si tyto informace. Budete je potřebovat později.

## <a name="create-a-net-core-web-app"></a>Vytvoření webové aplikace .NET Core

Postupujte podle tohoto [kurzu](../app-service/app-service-web-get-started-dotnet.md) a vytvořte webovou aplikaci .NET Core a **publikujte** ji do Azure **NEBO** se podívejte na následující video.
> [!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Otevření a úprava řešení

1. Přejděte do části Stránky a k souboru About.cshtml.cs.
2. Nainstalujte tyto dva balíčky NuGet:
    - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
    - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. Do souboru About.cshtml.cs importujte následující:

    ```
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
    ```
4. Váš kód ve třídě AboutModel by měl vypadat následovně:
    ```
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

V hlavní nabídce sady Visual Studio 2017 vyberte **Ladit** > **Spustit** (s laděním nebo bez). Jakmile se zobrazí prohlížeč, přejděte na stránku **O aplikaci**. Zobrazí se hodnota tajného klíče **AppSecret**.

## <a name="enable-a-managed-identity-for-the-web-app"></a>Povolení spravované identity pro webovou aplikaci

Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů a dalších klíčů a tajných kódů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst. [Přehled spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) tuto překážku usnadňuje tím, že dává službám Azure v Azure Active Directory (Azure AD) automaticky spravovanou identitu. Tuto identitu můžete použít k ověření pro jakoukoli službu, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje.

1. Vraťte se k Azure CLI.
2. Spusťte příkaz assign-identity a vytvořte identitu pro tuto aplikaci: 

   ```azurecli
   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
   ```
   Mějte na paměti, že <YourAppName> je potřeba nahradit názvem publikované aplikace v Azure. To znamená, že pokud je název vaší publikované aplikace MyAwesomeapp.azurewebsites.net, nahraďte <YourAppName> za MyAwesomeapp.
 
 Výstup výše uvedeného příkazu vypadá nějak takto. Při publikování aplikace v Azure si poznamenejte ID instančního objektu. Měl by mít následující formát:
   ```
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
  ```
>[!NOTE]
>Příkaz v tomto postupu je ekvivalentem přechodu na [portál](https://portal.azure.com) a přepnutí nastavení **Identita / Přiřazeno systémem** na hodnotu **Zapnuto** ve vlastnostech webové aplikace.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Přiřazení oprávnění ke čtení tajných kódů ze služby Key Vault vaší aplikaci
        
Potom spusťte tento příkaz, ve kterém použijete název vašeho trezoru klíčů a hodnotu **PrincipalId**:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

## <a name="publish-the-web-application-to-azure"></a>Publikování webové aplikace do Azure

Znovu publikujte tuto aplikaci do Azure, abyste ji mohli zobrazit živě jako webovou aplikaci a abyste viděli, že můžete načíst hodnotu tajného klíče.

1. V sadě Visual Studio vyberte projekt **key-vault-dotnet-core-quickstart**.
2. Vyberte **Publikovat** > **Spustit**.
3. Vyberte **Vytvořit**.

Ve výše uvedeném příkazu udělujete identitě spravované služby App Service oprávnění k operacím **get** a **list** ve službě Key Vault. <br />
Když teď spustíte aplikaci, měla by se zobrazit načtená hodnota vašeho taného klíče. 

A to je vše. Právě jste úspěšně vytvořili webovou aplikaci v .NET, která ukládá tajné klíče ve službě Key Vault a načítá je z ní.

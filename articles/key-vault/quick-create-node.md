---
title: Rychlý start – nastavení a získání tajného klíče ze služby Azure Key Vault s využitím webovou aplikaci v Node | Dokumentace Microsoftu
description: V tomto rychlém startu nastavení a načtení tajného klíče ze služby Azure Key Vault s využitím webovou aplikaci v Node
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.workload: identity
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 1e234b599325da0626c83a57d86ff977b88b5577
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991270"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-node-web-app"></a>Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault s využitím webovou aplikaci v Node 

V tomto rychlém startu se dozvíte, jak uložíte tajný klíč ve službě Azure Key Vault a jak ho načíst pomocí webové aplikace. Pomocí služby Key Vault pomáhá udržovat informace v bezpečí. Pokud chcete zobrazit tajnou hodnotu, by musíte spustit tento rychlý start v Azure. V tomto rychlém startu se používá Node.js a spravované identity pro prostředky Azure. Získáte informace o těchto tématech:

* Vytvoření trezoru klíčů
* Uložení tajného klíče v trezoru klíčů
* Načtení tajného klíče z trezoru klíčů
* Vytvoření webové aplikace Azure
* Povolení [spravované identity](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) pro webovou aplikaci
* Udělení požadovaných oprávnění k načtení dat z trezoru klíčů pro webovou aplikaci

Než budete pokračovat, ujistěte se, že jste obeznámeni s [základní koncepty služby Key Vault](key-vault-whatis.md#basic-concepts).

> [!NOTE]
> Služba Key Vault je centrální úložiště pro ukládání tajných klíčů prostřednictvím kódu programu. Aby to bylo možné, aplikace a uživatelé se nejprve musí ve službě Key Vault ověřit, tedy předložit tajný klíč. Podle osvědčené postupy zabezpečení tento první tajný klíč musí pravidelně otočen. 
>
> S [identit spravovaných služeb pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md), aplikace, které běží v Azure získat identitu, která automaticky spravuje Azure. To vám pomůže vyřešit *problém se zavedením tajného klíče*, abyste uživatelům a aplikacím umožnili dodržovat osvědčené postupy bez starostí o obměňování prvního tajného klíče.

## <a name="prerequisites"></a>Požadavky

* [Node.js](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 nebo novější. Tento rychlý start vyžaduje, abyste spustili Azure CLI místně. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku, přečtěte si téma [Instalace Azure CLI 2.0](https://review.docs.microsoft.com/en-us/cli/azure/install-azure-cli?branch=master&view=azure-cli-latest).
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete přihlásit k Azure pomocí Azure CLI, zadejte:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vyberte název skupiny prostředků a nahraďte zástupný text.
Následující příklad vytvoří skupinu prostředků v umístění východní USA.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

V tomto článku se používá skupina prostředků, kterou jste právě vytvořili.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Dále vytvoříte trezor klíčů pomocí skupiny prostředků, kterou jste vytvořili v předchozím kroku. I když tento článek používá "ContosoKeyVault" jako název, budete muset použít jedinečný název. Zadejte tyto informace:

* Název trezoru klíčů.
* Název skupiny prostředků. Název musí být řetězec dlouhý 3 až 24 znaků a musí obsahovat pouze 0-9, a-z, A-Z a pomlčka (-).
* Umístění: **USA – východ**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět jakékoli operace s tímto novým trezorem.

## <a name="add-a-secret-to-the-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Tajný klíč přidáváme proto, abychom ukázali, jak to funguje. Mohli byste ukládat připojovací řetězec SQL nebo jakékoli jiné informace, které potřebujete zabezpečeně uchovávat a současně zpřístupnit vaší aplikaci. V tomto kurzu budeme heslo označovat jako **AppSecret** a budeme v něm ukládat hodnotu **MySecret**.

Zadáním následujících příkazů vytvořte v trezoru klíčů tajný klíč **AppSecret**. V tomto tajném klíči bude uložená hodnota **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Pokud chcete zobrazit hodnotu v tajném kódu jako prostý text:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Tento příkaz zobrazí informace o tajném klíči, včetně identifikátoru URI. Po dokončení těchto kroků byste měli mít identifikátor URI pro tajný klíč v trezoru klíčů. Poznamenejte si tyto informace. Budete je potřebovat později.

## <a name="clone-the-repo"></a>Klonování úložiště

Naklonováním úložiště vytvořte místní kopii, kde můžete upravit zdroj. Spusťte následující příkaz:

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Instalace závislostí

Spuštěním následujících příkazů nainstalujte závislosti:

```
cd key-vault-node-quickstart
npm install
```

Tento projekt používá dva moduly uzlu: [ms-rest azure](https://www.npmjs.com/package/ms-rest-azure) a [azure Key vaultu](https://www.npmjs.com/package/azure-keyvault).

## <a name="publish-the-web-app-to-azure"></a>Publikování webové aplikace do služby Azure

Vytvoření [služby Azure App Service](https://azure.microsoft.com/services/app-service/) plánu. V tomto plánu můžete uložit několik webových aplikací.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
V dalším kroku vytvoření webové aplikace. V následujícím příkladu nahraďte `<app_name>` s globálně jedinečným názvem aplikace (platné znaky jsou a – z, 0-9 a -). Modul runtime je nastavený na NODE|6.9. Pokud chcete zobrazit všechny podporované moduly runtime, spusťte `az webapp list-runtimes`.

    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    ```
Po vytvoření webové aplikace Azure CLI zobrazí výstup podobný následujícímu příkladu:

    ```
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "<app_name>.azurewebsites.net",
      "enabled": true,
      "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
      < JSON data removed for brevity. >
    }
    ```
Přechod do nově vytvořené webové aplikace a měli byste vidět, že funguje. Nahraďte `<app_name>` s jedinečným názvem aplikace.

    ```
    http://<app name>.azurewebsites.net
    ```
Předchozí příkaz také vytvoří aplikaci s podporou Git, které můžete nasadit do Azure ze svého místního úložiště Git. Místní úložiště Git má nakonfigurovanou tuto adresu URL: https://<username>@ .git.scm.azurewebsites.net/ < název_aplikace > < název_aplikace >.

Po dokončení předchozího příkazu, můžete přidat vzdálené Azure do místního úložiště Git. Nahraďte `<url>` za adresu URL úložiště Git.

    ```
    git remote add azure <url>
    ```

## <a name="enable-a-managed-identity-for-the-web-app"></a>Povolení spravované identity pro webovou aplikaci

Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů a dalších klíčů a tajných kódů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst. [Přehled spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) tuto překážku usnadňuje tím, že dává službám Azure v Azure Active Directory (Azure AD) automaticky spravovanou identitu. Tuto identitu můžete použít k ověření pro jakoukoli službu, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje.

Spusťte příkaz assign-identity a vytvořte identitu pro tuto aplikaci:

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

Tento příkaz je ekvivalentem přechodu na portál a přepnutí nastavení **Identita / Přiřazeno systémem** na hodnotu **Zapnuto** ve vlastnostech webové aplikace.

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Přiřazení oprávnění ke čtení tajných kódů ze služby Key Vault vaší aplikaci

Poznamenejte si výstupu předchozího příkazu. Měl by mít následující formát:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Spusťte následující příkaz s použitím názvu vaší služby key vault a hodnota **principalId**:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Uzel aplikaci nasadit do Azure a načíst tajná hodnota

Spuštěním následujícího příkazu Nasaďte aplikaci do Azure:

```
git push azure master
```

Za to když přejdete na https://<app_name>.azurewebsites.net, uvidíte tajná hodnota. Ujistěte se, že jste nahradili názvem <YourKeyVaultName> názvem vašeho trezoru.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Azure SDK pro Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)

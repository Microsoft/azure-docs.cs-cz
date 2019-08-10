---
title: Rychlý Start – Nastavení a načtení tajného klíče z Azure Key Vault pomocí webové aplikace v uzlu | Microsoft Docs
description: V tomto rychlém startu nastavíte a načtete tajný klíč z Azure Key Vault pomocí webové aplikace Node.
services: key-vault
author: msmbaldwin
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 5ca6289b1af02a54d8c66d5a9835e24f61c58559
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934461"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-node-web-app"></a>Rychlý start: Nastavení a načtení tajného klíče z Azure Key Vault pomocí webové aplikace Node 

V tomto rychlém startu se dozvíte, jak uložit tajný kód v Azure Key Vault a jak ho načíst pomocí webové aplikace. Používání Key Vault pomáhá udržet si informace v bezpečí. Pokud chcete zobrazit hodnotu tajného kódu, musíte spustit tento rychlý Start v Azure. V tomto rychlém startu se používá Node.js a spravované identity pro prostředky Azure. Získáte informace o těchto tématech:

* Vytvoření trezoru klíčů
* Uložení tajného klíče v trezoru klíčů
* Načtení tajného klíče z trezoru klíčů
* Vytvoření webové aplikace Azure
* Povolení [spravované identity](../active-directory/managed-service-identity/overview.md) pro webovou aplikaci
* Udělení požadovaných oprávnění k načtení dat z trezoru klíčů pro webovou aplikaci

Než budete pokračovat, ujistěte se, že jste obeznámeni se [základními koncepty pro Key Vault](key-vault-whatis.md#basic-concepts).

> [!NOTE]
> Služba Key Vault je centrální úložiště pro ukládání tajných klíčů prostřednictvím kódu programu. Aby to bylo možné, aplikace a uživatelé se nejprve musí ve službě Key Vault ověřit, tedy předložit tajný klíč. V souladu s osvědčenými postupy zabezpečení je nutné tento první tajný klíč pravidelně střídat. 
>
> V [případě identit spravované služby pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md)získávají aplikace, které běží v Azure, identitu, kterou Azure spravuje automaticky. To vám pomůže vyřešit *problém se zavedením tajného klíče*, abyste uživatelům a aplikacím umožnili dodržovat osvědčené postupy bez starostí o obměňování prvního tajného klíče.

## <a name="prerequisites"></a>Požadavky

* [Node.js](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 nebo novější. Tento rychlý Start vyžaduje, abyste spouštěli Azure CLI místně. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku, přečtěte si téma [Instalace Azure CLI 2.0](https://review.docs.microsoft.com/en-us/cli/azure/install-azure-cli?branch=master&view=azure-cli-latest).
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete přihlásit k Azure pomocí Azure CLI, zadejte:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vyberte název skupiny prostředků a nahraďte zástupný text.
Následující příklad vytvoří skupinu prostředků v umístění Východní USA.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

V tomto článku se používá skupina prostředků, kterou jste právě vytvořili.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

V dalším kroku vytvoříte Trezor klíčů pomocí skupiny prostředků, kterou jste vytvořili v předchozím kroku. I když tento článek používá jako název "ContosoKeyVault", musíte použít jedinečný název. Zadejte tyto informace:

* Název trezoru klíčů
* Název skupiny prostředků. Název musí být řetězec 3-24 znaků a musí obsahovat pouze 0-9, a-z, A-Z a spojovník (-).
* Umístění: **Východní USA**.

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

Pokud chcete nainstalovat závislosti, spusťte následující příkazy:

```
cd key-vault-node-quickstart
npm install
```

Tento projekt používá dva moduly uzlů: [MS-REST-Azure](https://www.npmjs.com/package/ms-rest-azure) a [Azure-webtrezor](https://www.npmjs.com/package/azure-keyvault).

## <a name="publish-the-web-app-to-azure"></a>Publikování webové aplikace do služby Azure

Vytvořte plán [Azure App Service](https://azure.microsoft.com/services/app-service/) . V tomto plánu můžete uložit několik webových aplikací.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
Potom vytvořte webovou aplikaci. V následujícím příkladu nahraďte `<app_name>` globálně jedinečným názvem aplikace (platné znaky jsou a-z, 0-9 a-). Modul runtime je nastavený na NODE|6.9. Pokud chcete zobrazit všechny podporované moduly runtime, `az webapp list-runtimes`spusťte příkaz.

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
Přejděte k nově vytvořené webové aplikaci a měli byste vidět, že funguje. Nahraďte `<app_name>` s jedinečným názvem aplikace.

    ```
    http://<app name>.azurewebsites.net
    ```
Předchozí příkaz také vytvoří aplikaci s podporou Gitu, která umožňuje nasazení do Azure z místního úložiště Git. Místní úložiště Git je nakonfigurované s touto adresou URL `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`:.

Po dokončení předchozího příkazu můžete přidat vzdálené úložiště Azure do místního úložiště Git. Nahraďte `<url>` adresou URL úložiště Git.

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

Poznamenejte si výstup předchozího příkazu. Měl by mít následující formát:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Pak spusťte následující příkaz pomocí názvu trezoru klíčů a hodnoty **principalId**:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get set
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Nasaďte aplikaci Node do Azure a načtěte tajnou hodnotu.

Spusťte následující příkaz, který nasadí aplikaci do Azure:

```
git push azure master
```

Po přechodu na `https://<app_name>.azurewebsites.net`adresu vidíte tajnou hodnotu. Ujistěte se, že jste nahradili `<YourKeyVaultName>` název názvem vašeho trezoru.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Azure SDK pro Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)

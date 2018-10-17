---
title: Rychlý start – Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webové aplikace Node | Microsoft Docs
description: Rychlý start – Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webové aplikace Node
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
ms.openlocfilehash: 860294ebc7fbadd3eeefc4298ec740ca7f704587
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714390"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-a-node-web-app"></a>Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí webové aplikace Node 

V tomto rychlém startu se dozvíte, jak uložit tajný klíč ve službě Key Vault a jak ho načíst pomocí webové aplikace. Pokud chcete zobrazit hodnotu tajného klíče, musíte webovou aplikaci spustit v Azure. V tomto rychlém startu se používá Node.js a spravované identity pro prostředky Azure.

> [!div class="checklist"]
> * Vytvoření trezoru klíčů
> * Uložení tajného kódu ve službě Key Vault
> * Načtení tajného kódu ze služby Key Vault
> * Vytvoření webové aplikace Azure
> * Povolení [spravované identity](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) pro webovou aplikaci
> * Udělení požadovaných oprávnění k načtení dat ze služby Key Vault pro webovou aplikaci

Než budete pokračovat, ujistěte se, že znáte [základní koncepty](key-vault-whatis.md#basic-concepts).

>[!NOTE]
Abyste pochopili, proč následující kurz představuje osvědčený postup, je potřeba porozumět několika konceptům. Služba Key Vault je centrální úložiště pro ukládání tajných klíčů prostřednictvím kódu programu. Aby to bylo možné, aplikace nebo uživatelé se nejprve musí ve službě Key Vault ověřit, tedy předložit tajný klíč. Za účelem dodržení osvědčených postupů zabezpečení je potřeba pravidelně obměňovat také tento první tajný klíč. Při použití [spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) se však aplikacím spouštěným v Azure udělí identita, kterou automaticky spravuje Azure. To vám pomůže vyřešit **problém se zavedením tajného klíče** a tím umožníte uživatelům a aplikacím dodržovat osvědčené postupy bez starostí o obměňování prvního tajného klíče.

## <a name="prerequisites"></a>Požadavky

* [Node.js](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 nebo novější
* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="login-to-azure"></a>Přihlášení k Azure

Pokud se chcete přihlásit k Azure pomocí rozhraní příkazového řádku, můžete zadat:

```azurecli
az login
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vyberte název skupiny prostředků a nahraďte zástupný text.
Následující příklad vytvoří skupinu prostředků *<YourResourceGroupName>* v umístění *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

V tomto kurzu se používá skupina prostředků, kterou jste právě vytvořili.

## <a name="create-an-azure-key-vault"></a>Vytvoření služby Azure Key Vault

Dále s použitím skupiny prostředků vytvořené v předchozím kroku vytvoříte službu Key Vault. Přestože se v tomto článku jako název pro trezor klíčů používá ContosoKeyVault, musíte použít jedinečný název. Zadejte tyto informace:

* Název trezoru – **tady vyberte název trezoru klíčů**.
* Název skupiny prostředků – **tady vyberte název skupiny prostředků**.
* Umístění – **USA – východ**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět jakékoli operace s tímto novým trezorem.

## <a name="add-a-secret-to-key-vault"></a>Přidání tajného klíče do trezoru klíčů

Tajný klíč přidáváme proto, abychom ukázali, jak to funguje. Mohli byste ukládat připojovací řetězec SQL nebo jakékoli jiné informace, které potřebujete zabezpečeně uchovávat a současně zpřístupnit vaší aplikaci. V tomto kurzu budeme heslo označovat jako **AppSecret** a budeme v něm ukládat hodnotu **MySecret**.

Zadáním následujících příkazů vytvořte ve službě Key Vault tajný kód **AppSecret**, který bude uchovávat hodnotu **MySecret**:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Pokud chcete zobrazit hodnotu v tajném kódu jako prostý text:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Tento příkaz zobrazí informace o tajném kódu, včetně identifikátoru URI. Po dokončení těchto kroků byste měli mít URI pro tajný kód ve službě Azure Key Vault. Tuto informaci si poznamenejte. Budete je potřebovat později.

## <a name="clone-the-repo"></a>Klonování úložiště

Spuštěním následujícího příkazu naklonujte úložiště, abyste získali místní kopii umožňující úpravu zdroje:

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Instalace závislostí

Teď nainstalujeme závislosti. Spusťte následující příkazy cd key-vault-node-quickstart npm install.

V tomto projektu se používají 2 moduly Node:

* [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) 
* [azure-keyvault](https://www.npmjs.com/package/azure-keyvault)

## <a name="publish-the-web-application-to-azure"></a>Publikování webové aplikace do Azure

Následuje několik kroků, které je potřeba provést.

- Prvním krokem je vytvořit plán služby [Azure App Service](https://azure.microsoft.com/services/app-service/). V tomto plánu můžete uložit několik webových aplikací.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
- Dále vytvoříme webovou aplikaci. V následujícím příkladu nahraďte <app_name> globálně jedinečným názvem aplikace (platné znaky jsou a–z, 0–9 a -). Modul runtime je nastavený na NODE|6.9. Pokud chcete zobrazit všechny podporované moduly runtime, spusťte příkaz az webapp list-runtimes.
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
    Přejděte do své nově vytvořené webové aplikace. Měla by se zobrazit funkční webová aplikace. Nahraďte <app_name> jedinečným názvem aplikace.

    ```
    http://<app name>.azurewebsites.net
    ```
    Výše uvedený příkaz také vytvoří aplikaci s podporou Gitu, která umožňuje nasazení do Azure z místního Gitu. 
    Místní Git má nakonfigurovanou adresu URL https://<username>@<název_aplikace>.scm.azurewebsites.net/<název_aplikace>.git.

- Po dokončení předchozího příkazu vytvořte uživatele nasazení, abyste do místního úložiště Git mohli přidat vzdálené úložiště Azure. Nahraďte <url> adresou URL vzdáleného úložiště Git, kterou jste získali při povolení Gitu pro vaši aplikaci.

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

Poznamenejte si nebo zkopírujte výstup výše uvedeného příkazu. Měl by mít následující formát:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Potom spusťte příkaz, ve kterém použijete název vašeho trezoru klíčů a zkopírovanou hodnotu PrincipalId:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Nasazení aplikace Node do Azure a načtení hodnoty tajného kódu

Teď je vše nastavené. Spuštěním následujícího příkazu nasaďte aplikaci do Azure.

```
git push azure master
```

Když teď přejdete na adresu https://<název_aplikace>.azurewebsites.net, zobrazí se hodnota tajného kódu.
Ujistěte se, že jste nahradili název <YourKeyVaultName> názvem vašeho trezoru.

## <a name="next-steps"></a>Další kroky

* [Domovská stránka služby Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Dokumentace ke službě Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
* [Azure SDK pro Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Reference k rozhraní Azure REST API](https://docs.microsoft.com/rest/api/keyvault/)

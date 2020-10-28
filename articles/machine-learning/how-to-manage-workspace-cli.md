---
title: Vytváření pracovních prostorů pomocí Azure CLI
titleSuffix: Azure Machine Learning
description: Naučte se používat rozhraní příkazového řádku Azure a vytvořit nový pracovní prostor Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 45f9f61712903436d63f483673705650f5470b3f
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635945"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Vytvoření pracovního prostoru pro Azure Machine Learning pomocí Azure CLI


V tomto článku se dozvíte, jak vytvořit pracovní prostor Azure Machine Learning pomocí Azure CLI. Rozhraní příkazového řádku Azure nabízí příkazy pro správu prostředků Azure. Rozšíření Machine Learning pro rozhraní příkazového řádku poskytuje příkazy pro práci s Azure Machine Learning prostředky.

## <a name="prerequisites"></a>Předpoklady

* **Předplatné Azure** Pokud ho nemáte, vyzkoušejte [bezplatnou nebo placená verzi Azure Machine Learning](https://aka.ms/AMLFree).

* Pokud chcete v tomto dokumentu použít příkazy rozhraní příkazového řádku z vašeho **místního prostředí** , potřebujete [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

    Použijete-li [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), k rozhraní příkazového řádku se dostanete v prohlížeči a v cloudu.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Připojení rozhraní příkazového řádku k předplatnému Azure

> [!IMPORTANT]
> Pokud používáte Azure Cloud Shell, můžete tuto část přeskočit. Cloud Shell se automaticky ověřuje pomocí účtu, který se přihlašujete k předplatnému Azure.

Existuje několik způsobů, jak můžete z CLI ověřit předplatné Azure. Nejjednodušším je interaktivní ověřování pomocí prohlížeče. Chcete-li provést interaktivní ověřování, otevřete příkazový řádek nebo terminál a použijte následující příkaz:

```azurecli-interactive
az login
```

Pokud rozhraní příkazového řádku může spustit výchozí prohlížeč, udělá to a načte přihlašovací stránku. V opačném případě je nutné otevřít prohlížeč a postupovat podle pokynů v příkazovém řádku. Pokyny zahrnují procházení [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadávání autorizačního kódu.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Další metody ověřování najdete v tématu [přihlášení pomocí Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true).

## <a name="install-the-machine-learning-extension"></a>Instalace rozšíření Machine Learning

Pokud chcete nainstalovat rozšíření Machine Learning, použijte následující příkaz:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pracovní prostor Azure Machine Learning spoléhá na tyto služby nebo entity Azure:

> [!IMPORTANT]
> Pokud nezadáte existující službu Azure, vytvoří se během vytváření pracovního prostoru automaticky. Vždy je nutné zadat skupinu prostředků. Při připojování vlastního účtu úložiště se ujistěte, že splňuje následující kritéria:
>
> * Účet _úložiště není účtem_ premium (Premium_LRS a Premium_GRS).
> * Funkce Azure Blob a Azure File jsou povolené.
> * Hierarchický obor názvů (ADLS Gen 2) je zakázaný.
>
> Tyto požadavky jsou určené jenom pro _výchozí_ účet úložiště, který používá pracovní prostor.

| Služba | Parametr pro určení existující instance |
| ---- | ---- |
| **Skupina prostředků Azure** | `-g <resource-group-name>`
| **Účet Azure Storage** | `--storage-account <service-id>` |
| **Application Insights Azure** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pracovní prostor Azure Machine Learning musí být vytvořený v rámci skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořit novou. Pokud chcete __vytvořit novou skupinu prostředků__ , použijte následující příkaz. Nahraďte `<resource-group-name>` názvem, který se má použít pro tuto skupinu prostředků. Nahraďte `<location>` oblastí Azure, kterou chcete použít pro tuto skupinu prostředků:

> [!TIP]
> Vyberte oblast, ve které je Azure Machine Learning k dispozici. Informace najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Odpověď z tohoto příkazu je podobná následujícímu kódu JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Další informace o práci se skupinami prostředků najdete v tématu [AZ Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest&preserve-view=true).

### <a name="automatically-create-required-resources"></a>Automaticky vytvářet požadované prostředky

Pokud chcete vytvořit nový pracovní prostor, ve kterém __se služby vytvoří automaticky__ , použijte následující příkaz:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> V názvu pracovního prostoru se nerozlišují malá a velká písmena.

Výstup tohoto příkazu je podobný následujícímu formátu JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="virtual-network-and-private-endpoint"></a>Virtuální síť a soukromý koncový bod

> [!IMPORTANT]
> Použití Azure Machine Learningho pracovního prostoru s privátním odkazem není k dispozici ve Azure Government oblastech nebo v oblastech Azure Čína 21Vianet.

Pokud chcete omezit přístup k pracovnímu prostoru na virtuální síť, můžete použít následující parametry:

* `--pe-name`: Název privátního koncového bodu, který je vytvořen.
* `--pe-auto-approval`: Zda mají být připojení privátního koncového bodu k pracovnímu prostoru automaticky schválena.
* `--pe-resource-group`: Skupina prostředků, ve které se vytvoří privátní koncový bod. Musí se jednat o stejnou skupinu, která obsahuje virtuální síť.
* `--pe-vnet-name`: Existující virtuální síť pro vytvoření privátního koncového bodu v nástroji.
* `--pe-subnet-name`: Název podsítě, v níž se má vytvořit privátní koncový bod. Výchozí hodnota je `default`.

Další informace o používání privátního koncového bodu a virtuální sítě s vaším pracovním prostorem najdete v tématu [Přehled izolace a ochrany osobních údajů virtuální sítě](how-to-network-security-overview.md).

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>Klíčový prostor spravovaný zákazníkem a vysokým obchodním dopadem

Ve výchozím nastavení se metriky a metadata pro pracovní prostor ukládají do Azure Cosmos DB instance, kterou Microsoft udržuje. Tato data se šifrují pomocí klíčů spravovaných Microsoftem. 

Místo používání klíče spravovaného společností Microsoft můžete použít vlastní klíč. Tím se vytvoří instance Azure Cosmos DB, která ukládá metriky a metadata v předplatném Azure. Pomocí `--cmk-keyvault` parametru zadejte Azure Key Vault, který obsahuje klíč, a `--resource-cmk-uri` Zadejte adresu URL klíče v trezoru.

Před použitím `--cmk-keyvault` parametrů a je `--resource-cmk-uri` třeba nejprve provést následující akce:

1. Autorizaci __aplikace Machine Learning__ (v části Správa identit a přístupu) s oprávněními přispěvatele v předplatném.
1. Postupujte podle kroků v části [konfigurace klíčů spravovaných zákazníkem](/azure/cosmos-db/how-to-setup-cmk) na:
    * Registrace poskytovatele Azure Cosmos DB
    * Vytvoření a konfigurace Azure Key Vault
    * Vygenerovat klíč

Nemusíte vytvářet instanci Azure Cosmos DB ručně, ta se vytvoří během vytváření pracovního prostoru. Tato instance Azure Cosmos DB se vytvoří v samostatné skupině prostředků s použitím názvu založeného na tomto vzoru: `<your-resource-group-name>_<GUID>` .

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Chcete-li omezit data, která společnost Microsoft shromažďuje v pracovním prostoru, použijte `--hbi-workspace` parametr. 

> [!IMPORTANT]
> Výběr vysokého dopadu na firmu se dá udělat jenom při vytváření pracovního prostoru. Po vytvoření pracovního prostoru toto nastavení nemůžete změnit.

Další informace o klíčích spravovaných zákazníkem a vysokém pracovním prostoru s obchodním dopadem najdete v článku [Podnikové zabezpečení pro Azure Machine Learning](concept-enterprise-security.md#encryption-at-rest).

### <a name="use-existing-resources"></a>Použití existujících prostředků

Pokud chcete vytvořit pracovní prostor, který používá stávající prostředky, musíte zadat ID pro prostředky. Pro získání ID pro služby použijte následující příkazy:

> [!IMPORTANT]
> Nemusíte zadávat všechny existující prostředky. Můžete zadat jednu nebo více. Můžete například zadat existující účet úložiště a pracovní prostor vytvoří další prostředky.

+ **Účet Azure Storage** : `az storage account show --name <storage-account-name> --query "id"`

    Odpověď z tohoto příkazu je podobná následujícímu textu a je ID účtu úložiště:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > Pokud chcete použít existující účet Azure Storage, nemůže to být účet Premium (Premium_LRS a Premium_GRS). Nemůže mít také hierarchický obor názvů (používá se s Azure Data Lake Storage Gen2). Ve _výchozím_ účtu úložiště pracovního prostoru není podporován ani obor názvů Premium Storage ani hierarchický obor názvů. Můžete použít Storage úrovně Premium nebo hierarchický obor názvů s účty úložiště, _které nejsou výchozí_ .

+ **Application Insights Azure** :

    1. Instalace rozšíření Application Insights:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Získejte ID vaší služby Application Insight:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Odpověď z tohoto příkazu je podobná následujícímu textu a je ID vaší služby Application Insights:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault** : `az keyvault show --name <key-vault-name> --query "ID"`

    Odpověď z tohoto příkazu je podobná následujícímu textu a je ID vašeho trezoru klíčů:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry** : `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Odpověď z tohoto příkazu je podobná následujícímu textu a je ID pro registr kontejneru:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > V registru kontejneru musí být povolen [účet správce](/azure/container-registry/container-registry-authentication#admin-account) , aby jej bylo možné použít s pracovním prostorem Azure Machine Learning.

Jakmile budete mít ID prostředků, které chcete používat s pracovním prostorem, použijte základní `az workspace create -w <workspace-name> -g <resource-group-name>` příkaz a přidejte parametry a ID pro existující prostředky. Například následující příkaz vytvoří pracovní prostor, který používá existující registr kontejnerů:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

Výstup tohoto příkazu je podobný následujícímu formátu JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>Zobrazit seznam pracovních prostorů

Pokud chcete zobrazit seznam všech pracovních prostorů pro vaše předplatné Azure, použijte následující příkaz:

```azurecli-interactive
az ml workspace list
```

Výstup tohoto příkazu je podobný následujícímu formátu JSON:

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

Další informace najdete v dokumentaci [AZ ml Workspace list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-list) .

## <a name="get-workspace-information"></a>Získat informace o pracovním prostoru

Chcete-li získat informace o pracovním prostoru, použijte následující příkaz:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

Výstup tohoto příkazu je podobný následujícímu formátu JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Další informace najdete v tématu o tom, jak [zobrazit dokumentaci AZ ml Workspace](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-show) .

## <a name="update-a-workspace"></a>Aktualizovat pracovní prostor

Chcete-li aktualizovat pracovní prostor, použijte následující příkaz:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

Výstup tohoto příkazu je podobný následujícímu formátu JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Další informace najdete v dokumentaci [AZ ml Workspace Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-update) .

## <a name="share-a-workspace-with-another-user"></a>Sdílení pracovního prostoru s jiným uživatelem

Pokud chcete sdílet pracovní prostor s jiným uživatelem v předplatném, použijte následující příkaz:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Další informace o řízení přístupu na základě role Azure (Azure RBAC) s Azure Machine Learning najdete v tématu [Správa uživatelů a rolí](how-to-assign-roles.md).

Další informace najdete v dokumentaci ke [sdílení pracovního prostoru AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-share) .

## <a name="sync-keys-for-dependent-resources"></a>Synchronizace klíčů pro závislé prostředky

Pokud změníte přístupové klíče pro některý z prostředků, které váš pracovní prostor používá, trvá to přibližně hodinu, než se pracovní prostor synchronizuje s novým klíčem. Pokud chcete vynutit okamžitou synchronizaci nových klíčů v pracovním prostoru, použijte následující příkaz:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Další informace o změně klíčů najdete v tématu [obnovení přístupových klíčů k úložišti](how-to-change-storage-access-key.md).

Další informace najdete v dokumentaci [AZ ml pracovní prostor Sync-Keys](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-sync-keys) .

## <a name="delete-a-workspace"></a>Odstranění pracovního prostoru

Pokud chcete pracovní prostor odstranit poté, co už ho nepotřebujete, použijte tento příkaz:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> Odstranění pracovního prostoru neodstraní přehled aplikací, účet úložiště, Trezor klíčů nebo registr kontejnerů, které používá pracovní prostor.

Můžete také odstranit skupinu prostředků, která odstraní pracovní prostor a všechny ostatní prostředky Azure ve skupině prostředků. Pokud chcete odstranit skupinu prostředků, použijte následující příkaz:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Další informace najdete v tématu [AZ ml Workspace Delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-delete) Document.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="resource-provider-errors"></a>Chyby poskytovatele prostředků

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Přesun pracovního prostoru

> [!WARNING]
> Přesunutím pracovního prostoru Azure Machine Learning do jiného předplatného nebo přesunutím vlastnícího předplatného na nového tenanta se nepodporuje. V takovém případě může dojít k chybám.

### <a name="deleting-the-azure-container-registry"></a>Odstranění Azure Container Registry

Azure Machine Learning pracovní prostor používá pro některé operace Azure Container Registry (ACR). Automaticky vytvoří instanci ACR, když ji poprvé potřebuje.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Další kroky

Další informace o rozšíření Azure CLI pro Machine Learning najdete v dokumentaci [AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest&preserve-view=true) .

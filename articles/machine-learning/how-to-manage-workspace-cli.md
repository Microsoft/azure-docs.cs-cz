---
title: Vytváření pracovních prostorů pomocí azure cli
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí azure cli vytvořit nový pracovní prostor Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.openlocfilehash: 9a7d0b75140c50df61ff63f350e5b312a6a684c7
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617787"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Vytvoření pracovního prostoru pro Azure Machine Learning pomocí azure cli
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak vytvořit pracovní prostor Azure Machine Learning pomocí azure cli. Azure CLI poskytuje příkazy pro správu prostředků Azure. Rozšíření strojového učení pro cli poskytuje příkazy pro práci s prostředky Azure Machine Learning.

## <a name="prerequisites"></a>Požadavky

* Předplatné **Azure**. Pokud ho nemáte, vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree).

* Chcete-li použít příkazy příkazu příkazu příkazu příkazového příkazu v tomto dokumentu z **místního prostředí**, potřebujete [příkazové příkazové příkazy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Pokud používáte [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/), cli je přístupná prostřednictvím prohlížeče a žije v cloudu.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Připojení příkazového příkazového příkazu k předplatnému Azure

> [!IMPORTANT]
> Pokud používáte Azure Cloud Shell, můžete tuto část přeskočit. Cloudové prostředí vás automaticky ověří pomocí účtu, který se přihlásíte k předplatnému Azure.

Existuje několik způsobů, jak můžete ověřit na vaše předplatné Azure z cli. Nejzákladnější je interaktivní ověření pomocí prohlížeče. Chcete-li se interaktivně ověřit, otevřete příkazový řádek nebo terminál a použijte následující příkaz:

```azurecli-interactive
az login
```

Pokud rozhraní příkazového řádku může spustit výchozí prohlížeč, udělá to a načte přihlašovací stránku. V opačném případě je třeba otevřít prohlížeč a postupovat podle pokynů na příkazovém řádku. Pokyny zahrnují procházení [https://aka.ms/devicelogin](https://aka.ms/devicelogin) a zadání autorizačního kódu.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Další způsoby ověřování najdete [v tématu Přihlášení pomocí azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="install-the-machine-learning-extension"></a>Instalace rozšíření strojového učení

Chcete-li nainstalovat rozšíření strojového učení, použijte následující příkaz:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pracovní prostor Azure Machine Learning závisí na následujících službách nebo entitách Azure:

> [!IMPORTANT]
> Pokud nezadáte existující službu Azure, bude vytvořena automaticky během vytváření pracovního prostoru. Vždy je nutné zadat skupinu prostředků.

| Služba | Parametr pro určení existující instance |
| ---- | ---- |
| **Skupina prostředků Azure** | `-g <resource-group-name>`
| **Účet úložiště Azure** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pracovní prostor Azure Machine Learning musí být vytvořen uvnitř skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořit novou. Chcete-li __vytvořit novou skupinu prostředků__, použijte následující příkaz. Nahraďte `<resource-group-name>` název, který má být pro tuto skupinu prostředků používán. Nahraďte `<location>` oblastí Azure, která se použije pro tuto skupinu prostředků:

> [!TIP]
> Měli byste vybrat oblast, kde azure machine learning je k dispozici. Další informace naleznete v [tématu Produkty dostupné podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Odpověď z tohoto příkazu je podobná následující JSON:

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

Další informace o práci se skupinami zdrojů naleznete [v tématu az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

### <a name="automatically-create-required-resources"></a>Automatické vytváření požadovaných zdrojů

Chcete-li vytvořit nový pracovní prostor, ve kterém __jsou služby automaticky vytvářeny__, použijte následující příkaz:

> [!TIP]
> Příkazy v této části vytvoří základní edici pracovního prostoru. Chcete-li vytvořit pracovní prostor `--sku enterprise` pro `az ml workspace create` podniky, použijte přepínač s příkazem. Další informace o edicích Azure Machine Learning najdete v tématu [Co je Azure Machine Learning](overview-what-is-azure-ml.md#sku).

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> Název pracovního prostoru nerozlišuje malá a velká písmena.

Výstup tohoto příkazu je podobný následujícímu jsonu:

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

### <a name="use-existing-resources"></a>Použití existujících prostředků

Chcete-li vytvořit pracovní prostor, který používá existující prostředky, musíte zadat ID pro prostředky. Pomocí následujících příkazů získáte ID služeb:

> [!IMPORTANT]
> Není nutné zadat všechny existující prostředky. Můžete zadat jeden nebo více. Můžete například zadat existující účet úložiště a pracovní prostor vytvoří další prostředky.

+ **Účet úložiště Azure**:`az storage account show --name <storage-account-name> --query "id"`

    Odpověď z tohoto příkazu je podobná následujícímu textu a je ID pro váš účet úložiště:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Přehledy aplikací Azure**:

    1. Nainstalujte rozšíření přehledů aplikací:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Získejte ID služby application insight:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Odpověď z tohoto příkazu je podobná následujícímu textu a je ID pro vaši službu Application Insights:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Trezor klíčů Azure**:`az keyvault show --name <key-vault-name> --query "ID"`

    Odpověď z tohoto příkazu je podobná následujícímu textu a je ID trezoru klíčů:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Registr kontejnerů Azure**:`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Odpověď z tohoto příkazu je podobná následujícímu textu a je ID pro registr kontejneru:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Registr kontejneru musí mít před použitím pracovního prostoru Azure Machine Learning povolen [účet správce.](/azure/container-registry/container-registry-authentication#admin-account)

Jakmile budete mít ID pro prostředky, které chcete použít s pracovním prostorem, použijte základní `az workspace create -w <workspace-name> -g <resource-group-name>` příkaz a přidejte parametry a ID pro existující prostředky. Například následující příkaz vytvoří pracovní prostor, který používá existující registr kontejneru:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

Výstup tohoto příkazu je podobný následujícímu jsonu:

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

## <a name="list-workspaces"></a>Seznam pracovních prostorů

Chcete-li vypsat všechny pracovní prostory pro vaše předplatné Azure, použijte následující příkaz:

```azurecli-interactive
az ml workspace list
```

Výstup tohoto příkazu je podobný následujícímu jsonu:

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

Další informace naleznete v dokumentaci [k seznamu pracovního prostoru az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list)

## <a name="get-workspace-information"></a>Získání informací o pracovním prostoru

Chcete-li získat informace o pracovním prostoru, použijte následující příkaz:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

Výstup tohoto příkazu je podobný následujícímu jsonu:

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

Další informace naleznete v dokumentaci k [zobrazení pracovního prostoru az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show)

## <a name="update-a-workspace"></a>Aktualizace pracovního prostoru

Chcete-li aktualizovat pracovní prostor, použijte následující příkaz:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

Výstup tohoto příkazu je podobný následujícímu jsonu:

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

Další informace naleznete v dokumentaci k [aktualizaci pracovního prostoru AZ ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update)

## <a name="share-a-workspace-with-another-user"></a>Sdílení pracovního prostoru s jiným uživatelem

Chcete-li sdílet pracovní prostor s jiným uživatelem v předplatném, použijte následující příkaz:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Další informace o řízení přístupu na základě rolí (RBAC) s Azure Machine Learning najdete v [tématu Správa uživatelů a rolí](how-to-assign-roles.md).

Další informace naleznete v dokumentaci [ke sdílení pracovního prostoru az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share)

## <a name="sync-keys-for-dependent-resources"></a>Synchronizace klíčů pro závislé prostředky

Pokud změníte přístupové klávesy pro jeden z prostředků používaných v pracovním prostoru, synchronizujte nové klíče s pracovním prostorem pomocí následujícího příkazu:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Další informace o změně klíčů naleznete v tématu [Regenerate storage access keys](how-to-change-storage-access-key.md).

Další informace naleznete v dokumentaci [k synchronizačním klíčům pracovního prostoru az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys)

## <a name="delete-a-workspace"></a>Odstranění pracovního prostoru

Chcete-li pracovní prostor odstranit poté, co již není potřeba, použijte následující příkaz:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> Odstraněním pracovního prostoru neodstraníte přehled aplikace, účet úložiště, trezor klíčů nebo registr kontejnerů používaný pracovním prostorem.

Můžete také odstranit skupinu prostředků, která odstraní pracovní prostor a všechny ostatní prostředky Azure ve skupině prostředků. Chcete-li skupinu prostředků odstranit, použijte následující příkaz:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Další informace naleznete v [dokumentaci k odstranění pracovního prostoru az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete)

## <a name="troubleshooting"></a>Řešení potíží

### <a name="resource-provider-errors"></a>Chyby zprostředkovatele prostředků

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Přesunutí pracovního prostoru

> [!WARNING]
> Přesunutí pracovního prostoru Azure Machine Learning do jiného předplatného nebo přesunutí vlastního předplatného do nového tenanta není podporováno. To může způsobit chyby.

### <a name="deleting-the-azure-container-registry"></a>Odstranění registru kontejnerů Azure

Pracovní prostor Azure Machine Learning používá azure kontejnerregistru (ACR) pro některé operace. Automaticky vytvoří instanci ACR, když ji poprvé potřebuje.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Další kroky

Další informace o rozšíření Azure CLI pro strojové učení najdete v dokumentaci [az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)

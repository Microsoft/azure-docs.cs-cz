---
title: Použití spravovaných identit pro řízení přístupu (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se používat spravované identity k řízení přístupu k prostředkům Azure z pracovního prostoru Azure Machine Learning.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: c4ea7609c343532f17144e388be7583eab427eee
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92440446"
---
# <a name="use-managed-identities-with-azure-machine-learning-preview"></a>Použití spravovaných identit s Azure Machine Learningm (Preview)

[Spravované identity](/azure/active-directory/managed-identities-azure-resources/overview) umožňují nakonfigurovat pracovní prostor s *minimálními požadovanými oprávněními pro přístup k prostředkům*. 

Když konfigurujete Azure Machine Learning pracovní prostor v rámci důvěryhodných způsobů, je důležité zajistit, aby různé služby přidružené k pracovnímu prostoru měly správnou úroveň přístupu. Pracovní prostor Machine Learning například potřebuje přístup k Azure Container Registry (ACR) pro Image Docker a účty úložiště pro školení dat. 

Spravované identity navíc umožňují jemně odstupňovanou kontrolu nad oprávněními, například můžete udělit nebo odvolat přístup ze specifických výpočetních prostředků na konkrétní ACR.

V tomto článku se dozvíte, jak používat spravované identity k těmto akcím:

 * Nakonfigurujte a použijte ACR pro váš pracovní prostor Azure Machine Learning, aniž byste museli povolit přístup uživatelů pro správce k ACR.
 * Přístup k privátnímu externímu ACR k vašemu pracovnímu prostoru a získání základních imagí pro školení nebo odvozování.

> [!IMPORTANT]
> Použití spravovaných identit k řízení přístupu k prostředkům pomocí Azure Machine Learning je aktuálně ve verzi Preview. Funkce ve verzi Preview je poskytována tak, jak je, bez záruky podpory nebo smlouvy o úrovni služeb. Další informace najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.
 
## <a name="prerequisites"></a>Požadavky

- Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).
- [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md)
- [Sada SDK Azure Machine Learning Pythonu](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
- Aby bylo možné přiřadit role, přihlášení k předplatnému Azure musí mít roli [spravovaného operátora identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nebo jinou roli, která uděluje požadované akce (například __vlastník__).
- Musíte být obeznámeni s vytvářením a práci se [spravovanými identitami](/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="configure-managed-identities"></a>Konfigurace spravovaných identit

V některých situacích je nutné zakázat přístup uživatelů správce k Azure Container Registry. Například ACR může být sdílená a vy budete muset zakázat přístup správce jiným uživatelům. Nebo vytvoření ACR s povoleným správcem na úrovni předplatného je zakázané zásadami na úrovni předplatného.

> [!IMPORTANT]
> Při použití Azure Machine Learning pro odvození v Azure Container instance (ACI) se __vyžaduje__přístup uživatelů správce na ACR. Nepovolujte ji, pokud plánujete nasazovat modely do ACI pro odvození.

Když vytváříte ACR bez povolení přístupu pro uživatele, spravované identity se používají pro přístup k ACR k vytváření a vyžádání imagí Docker.

Při vytváření pracovního prostoru můžete přenést vlastní ACR s uživatelem s rolí správce. Případně můžete Azure Machine Learning vytvořit pracovní prostor ACR a následně zakázat uživatele s oprávněními správce.

### <a name="bring-your-own-acr"></a>Přineste si vlastní ACR

Pokud je uživatel s rolí správce ACR zakázaný zásadami předplatného, měli byste nejdřív vytvořit ACR bez uživatele s oprávněními správce a pak ho přidružit k pracovnímu prostoru. Pokud už máte ACR se zakázaným správcem, můžete ho připojit k pracovnímu prostoru.

[Vytvořte ACR z Azure CLI](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli) bez nastavení ```--admin-enabled``` argumentu nebo z Azure Portal bez povolení uživatele s oprávněními správce. Při vytváření pracovního prostoru Azure Machine Learning zadejte ID prostředku Azure pro ACR. Následující příklad ukazuje vytvoření nového pracovního prostoru Azure ML, který používá stávající ACR:

> [!TIP]
> Hodnotu parametru získáte tak `--container-registry` , že pomocí příkazu [AZ ACR show](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az_acr_show) zobrazíte informace pro svůj ACR. `id`Pole obsahuje ID prostředku pro vaši ACR.

```azurecli-interactive
az ml workspace create -w <workspace name> \
-g <workspace resource group> \
-l <region> \
--container-registry /subscriptions/<subscription id>/resourceGroups/<acr resource group>/providers/Microsoft.ContainerRegistry/registries/<acr name>
```

### <a name="let-azure-machine-learning-service-create-workspace-acr"></a>Umožněte službě Azure Machine Learning vytvořit pracovní prostor ACR

Pokud nepřinesete vlastní ACR, služba Azure Machine Learning ji při provádění operace, která ji potřebuje, vytvoří. Můžete například odeslat školicí běh, který Výpočetní prostředky služby Machine Learning, vytvořte prostředí nebo nasaďte koncový bod webové služby. ACR vytvořený pracovním prostorem bude mít povoleného uživatele správce a vy budete muset uživatele správce zakázat ručně.

1. Vytvoření nového pracovního prostoru

    ```azurecli-interactive
    az ml workspace show -n <my workspace> -g <my resource group>
    ```

1. Proveďte akci, která vyžaduje ACR. Například [kurz pro školení modelu](tutorial-train-models-with-aml.md).

1. Získejte název ACR vytvořený clusterem:

    ```azurecli-interactive
    az ml workspace show -w <my workspace> \
    -g <my resource group>
    --query containerRegistry
    ```

    Tento příkaz vrátí hodnotu podobnou následujícímu textu. Přejete si pouze poslední část textu, což je název instance ACR:

    ```text
    /subscriptions/<subscription id>/resourceGroups/<my resource group>/providers/MicrosoftContainerReggistry/registries/<ACR instance name>
    ```

1. Aktualizujte ACR, aby se zakázal uživatel s rolí správce:

    ```azurecli-interactive
    az acr update --name <ACR instance name> --admin-enabled false
    ```

### <a name="create-compute-with-managed-identity-to-access-docker-images-for-training"></a>Vytvoření COMPUTE se spravovanou identitou pro přístup k imagí Docker pro školení

Pokud chcete získat přístup k pracovnímu prostoru ACR, vytvořte výpočetní cluster Machine Learning se zapnutou spravovanou identitou přiřazenou systémem. Identitu můžete povolit z Azure Portal nebo studia při vytváření výpočetních prostředků nebo z Azure CLI pomocí

# <a name="python"></a>[Python](#tab/python)

Při vytváření výpočetního clusteru s [AmlComputeProvisioningConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcomputeprovisioningconfiguration?view=azure-ml-py)použijte `identity_type` parametr pro nastavení spravovaného typu identity.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interaction
az ml computetarget create amlcompute --name cpucluster -w <workspace> -g <resource group> --vm-size <vm sku> --assign-identity '[system]'
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Informace o konfiguraci spravované identity při vytváření výpočetního clusteru v nástroji Studio najdete v tématu [nastavení spravované identity](how-to-create-attach-compute-studio.md#managed-identity).

---

Spravované identitě se automaticky přidělí role ACRPull v pracovním prostoru ACR, aby se povolilo zpřístupnění imagí Docker pro školení.

> [!NOTE]
> Pokud nejprve vytvoříte výpočetní výkon, před vytvořením pracovního prostoru ACR je potřeba přiřadit roli ACRPull ručně.

## <a name="access-base-images-from-private-acr"></a>Přístup k základním imagím z privátních ACR

Ve výchozím nastavení používá Azure Machine Learning základní image Docker, které pocházejí z veřejného úložiště spravovaného Microsoftem. Pak na těchto obrázcích vytvoří vaše školicí nebo odvozené prostředí. Další informace najdete v tématu [co jsou to prostředí ml](concept-environments.md).

Pokud chcete pro svůj podnik použít vlastní základní image, můžete pro přístup k privátním ACR používat spravované identity. Existují dva případy použití:

 * Použijte základní image pro školení, jak je.
 * Sestavte Azure Machine Learning spravovanou bitovou kopii s vlastní imagí jako základní.

### <a name="pull-docker-base-image-to-machine-learning-compute-cluster-for-training-as-is"></a>Základní bitová kopie pro vyžádání obsahu na výpočetním clusteru Machine Learning pro školení, jak je

Vytvořte výpočetní cluster Machine Learning s povolenou spravovanou identitou přiřazenou systémem, jak je popsáno výše. Pak určete ID objektu zabezpečení spravované identity.

```azurecli-interactive
az ml computetarget amlcompute identity show --name <cluster name> -w <workspace> -g <resource group>
```

Volitelně můžete aktualizovat výpočetní cluster pro přiřazení spravované identity přiřazené uživatelem:

```azurecli-interactive
az ml computetarget amlcompute identity assign --name cpucluster \
-w $mlws -g $mlrg --identities <my-identity-id>
```

Pokud chcete povolit, aby cluster COMPUTE vyčetl základní image, udělte roli spravované služby identity ACRPull na privátních ACR.

```azurecli-interactive
az role assignment create --assignee <principal ID> \
--role acrpull \
--scope "/subscriptions/<subscription ID>/resourceGroups/<private ACR resource group>/providers/Microsoft.ContainerRegistry/registries/<private ACR name>"
```

Nakonec při odeslání školicího běhu zadejte základní umístění bitové kopie v [definici prostředí](how-to-use-environments.md#use-existing-environments).

```python
from azureml.core import Environment
env = Environment(name="private-acr")
env.docker.base_image = "<ACR name>.azurecr.io/<base image repository>/<base image version>"
env.python.user_managed_dependencies = True
```

> [!IMPORTANT]
> Aby se zajistilo, že se základní image načte přímo do výpočetního prostředku, nastavte a nezadávejte `user_managed_dependencies = True` souboru Dockerfile. Jinak Azure Machine Learning služba se pokusí vytvořit novou image Docker a selže, protože k získání základní image z ACR má přístup jenom Compute Cluster.

### <a name="build-azure-machine-learning-managed-environment-into-base-image-from-private-acr-for-training-or-inference"></a>Sestavit Azure Machine Learning spravované prostředí na základní image z privátního ACR pro školení nebo odvozování

V tomto scénáři Azure Machine Learning služba sestaví školicí nebo odvozující prostředí nad základní imagí, kterou zadáte z privátního ACR. Vzhledem k tomu, že úloha sestavení obrázku se v pracovním prostoru ACR pomocí úloh ACR, je nutné provést další kroky, aby byl přístup povolen.

1. Vytvořte __uživatelsky přiřazenou spravovanou identitu__ a Udělte identitě ACRPull přístup k __privátnímu ACR__.  
1. Udělte __spravované identitě přiřazené systémem__ pracovního prostoru roli spravovaného operátoru identity v __uživatelsky přiřazené identitě__ z předchozího kroku. Tato role umožňuje pracovnímu prostoru přiřazení spravované identity přiřazené uživateli k ACR úlohy pro vytvoření spravovaného prostředí. 

    1. Získejte hlavní ID spravované identity přiřazené systémem pracovního prostoru:

        ```azurecli-interactive
        az ml workspace show -w <workspace name> -g <resource group> --query identityPrincipalId
        ```

    1. Udělte roli spravovaného operátora identity:

        ```azurecli-interactive
        az role assignment create --assignee <principal ID> --role managedidentityoperator --scope <UAI resource ID>
        ```

        ID prostředku UAI je ID prostředku Azure přiřazené identitě uživatele ve formátu `/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAI name>` .

1. V připojeních pracovního prostoru zadejte externí ACR a ID klienta __spravované identity přiřazené uživatelem__ pomocí [metody Workspace.set_connection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-):

    ```python
    workspace.set_connection(
        name="privateAcr", 
        category="ACR", 
        target = "<acr url>", 
        authType = "RegistryConnection", 
        value={"ResourceId": "<UAI resource id>", "ClientId": "<UAI client ID>"})
    ```

Po dokončení konfigurace můžete použít základní image z privátních ACR při vytváření prostředí pro školení nebo odvozování. Následující fragment kódu ukazuje, jak zadat základní image ACR a název Image v definici prostředí:

```python
from azureml.core import Environment

env = Environment(name="my-env")
env.docker.base_image = "<acr url>/my-repo/my-image:latest"
```

Volitelně můžete zadat adresu URL spravovaného prostředku identity a ID klienta v samotné definici prostředí pomocí [RegistryIdentity](https://docs.microsoft.com/python/api/azureml-core/azureml.core.container_registry.registryidentity?view=azure-ml-py). Pokud použijete identitu registru explicitně, potlačí všechna připojení k pracovnímu prostoru, která jste zadali dříve:

```python
from azureml.core.container_registry import RegistryIdentity

identity = RegistryIdentity()
identity.resource_id= "<UAI resource ID>"
identity.client_id="<UAI client ID>”
env.docker.base_image_registry.registry_identity=identity
env.docker.base_image = "my-acr.azurecr.io/my-repo/my-image:latest"
```

## <a name="use-docker-images-for-inference"></a>Použití imagí Docker pro odvození

Po nakonfigurování ACR bez uživatele s oprávněními správce, jak je popsáno výše, můžete získat přístup k obrázkům Docker pro odvození bez klíčů správce ze služby Azure Kubernetes (AKS). Když vytvoříte nebo připojíte AKS k pracovnímu prostoru, instanční objekt clusteru automaticky přiřadí ACRPull přístup k pracovnímu prostoru ACR.

> [!NOTE]
> Pokud přenesete vlastní cluster AKS, musí mít cluster povolený instanční objekt namísto spravované identity.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [podnikovém zabezpečení v Azure Machine Learning](concept-enterprise-security.md).

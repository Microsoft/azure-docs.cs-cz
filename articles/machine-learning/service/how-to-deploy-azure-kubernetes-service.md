---
title: Postup nasazení modelů do služby Azure Kubernetes
titleSuffix: Azure Machine Learning service
description: Naučte se, jak nasadit modely služby Azure Machine Learning jako webovou službu pomocí služby Azure Kubernetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.openlocfilehash: 302fad84a2f0443fb639e57d7bb0cfddef4c3443
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858736"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Nasazení modelu do clusteru služby Azure Kubernetes

Naučte se používat službu Azure Machine Learning k nasazení modelu jako webové služby ve službě Azure Kubernetes Service (AKS). Služba Azure Kubernetes je vhodná pro vysoce škálovatelná produkční nasazení. Použijte službu Azure Kubernetes, pokud potřebujete jednu nebo více z následujících možností:

- __Rychlá doba odezvy__.
- Automatické __škálování__ nasazené služby.
- Možnosti __hardwarové akcelerace__ , jako například GPU a pole programovatelné brány (FPGA).

> [!IMPORTANT]
> Škálování clusteru se neposkytuje prostřednictvím sady SDK Azure Machine Learning. Další informace o škálování uzlů v clusteru AKS najdete v tématu [škálování počtu uzlů v clusteru AKS](../../aks/scale-cluster.md).

Při nasazování do služby Azure Kubernetes nasadíte do clusteru AKS, který je __připojený k vašemu pracovnímu prostoru__. Existují dva způsoby, jak připojit cluster AKS k vašemu pracovnímu prostoru:

* Vytvořte cluster AKS pomocí sady SDK služby Azure Machine Learning Machine Learning CLI, [Azure Portal](https://portal.azure.com) nebo na [úvodní stránce pracovního prostoru (Preview)](https://ml.azure.com). Tento proces automaticky připojí cluster k pracovnímu prostoru.
* Připojte existující cluster AKS k pracovnímu prostoru služby Azure Machine Learning. Cluster se dá připojit pomocí sady Azure Machine Learning Service SDK, Machine Learning CLI nebo Azure Portal.

> [!IMPORTANT]
> Proces vytvoření nebo přílohy je jednorázovým úkolem. Jakmile je cluster AKS připojený k pracovnímu prostoru, můžete ho použít pro nasazení. Cluster AKS můžete odpojit nebo odstranit, pokud ho už nepotřebujete. Jakmile Detatched nebo odstraníte, nebudete už moct nasadit do clusteru.

## <a name="prerequisites"></a>Požadavky

- Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru služby Azure Machine Learning](how-to-manage-workspace.md).

- Model služby Machine Learning, který je zaregistrován ve vašem pracovním prostoru. Pokud nemáte registrovaný model, přečtěte si téma [jak a kde nasadit modely](how-to-deploy-and-where.md).

- [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)nebo [rozšíření Azure Machine Learning Visual Studio Code](how-to-vscode-tools.md).

- Fragmenty kódu __Pythonu__ v tomto článku předpokládají, že jsou nastavené následující proměnné:

    * `ws`– Nastavte na svůj pracovní prostor.
    * `model`– Nastavte na registrovaný model.
    * `inference_config`– Nastavte na odvození konfigurace pro model.

    Další informace o nastavení těchto proměnných najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

- Fragmenty rozhraní příkazového __řádku__ v tomto článku předpokládají, že jste `inferenceconfig.json` vytvořili dokument. Další informace o vytváření tohoto dokumentu najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

## <a name="create-a-new-aks-cluster"></a>Vytvoření nového clusteru AKS

**Časový odhad**: Přibližně 20 minut.

Vytvoření nebo připojení clusteru AKS je jednorázový proces pro váš pracovní prostor. Tento cluster pro více nasazení můžete znovu použít. Pokud odstraníte cluster nebo skupinu prostředků, která ho obsahuje, musíte při příštím nasazení vytvořit nový cluster. K vašemu pracovnímu prostoru můžete připojit více clusterů AKS.

> [!TIP]
> Pokud chcete svůj cluster AKS zabezpečit pomocí Virtual Network Azure, musíte nejdřív vytvořit virtuální síť. Další informace najdete v tématu [zabezpečené experimenty a odvozování pomocí Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Pokud chcete vytvořit cluster AKS pro __vývoj__, __ověřování__a __testování__ namísto produkčního prostředí, můžete určit __účel clusteru__ pro vývoj v __testovacím__prostředí.

> [!WARNING]
> Pokud jste nastavili `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, cluster, který se vytvoří, není vhodný pro provoz na úrovni produkčního prostředí a může prodloužit dobu odvození. Clustery pro vývoj a testování také nezaručují odolnost proti chybám. Pro clustery pro vývoj a testování doporučujeme aspoň 2 virtuální procesory.

Následující příklady ukazují, jak vytvořit nový cluster AKS pomocí sady SDK a rozhraní příkazového řádku:

**Používání sady SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> `cluster_purpose` `vm_size` `agent_count` `vm_size` V případě `agent_count` , že vyberete vlastní hodnoty pro`DEV_TEST`a a není, je nutné zajistit, aby vynásobený hodnotou byla větší než nebo rovna 12 virtuálním procesorům. [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py) Pokud například použijete `vm_size` "Standard_D3_v2", který má 4 virtuální procesory, měli byste `agent_count` vybrat 3 nebo vyšší.
>
> Sada SDK pro Azure Machine Learning neposkytuje podporu škálování clusteru AKS. Pro horizontální navýšení kapacity uzlů v clusteru použijte uživatelské rozhraní pro cluster AKS v Azure Portal. Můžete změnit jenom počet uzlů, nikoli velikost virtuálního počítače v clusteru.

Další informace o třídách, metodách a parametrech použitých v tomto příkladu naleznete v následujících referenčních dokumentech:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Použití rozhraní příkazového řádku**

```azurecli
az ml computetarget create aks -n myaks
```

Další informace najdete v tématu [AZ ml computetarget Create ASK](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference.

## <a name="attach-an-existing-aks-cluster"></a>Připojit existující cluster AKS

**Časový odhad:** Přibližně 5 minut.

Pokud už máte v předplatném Azure cluster AKS a je to verze 1.12. # #, můžete ho použít k nasazení image.

> [!TIP]
> Stávající cluster AKS může být v oblasti Azure, než je váš pracovní prostor služby Azure Machine Learning.
>
> Pokud chcete svůj cluster AKS zabezpečit pomocí Virtual Network Azure, musíte nejdřív vytvořit virtuální síť. Další informace najdete v tématu [zabezpečené experimenty a odvozování pomocí Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

> [!WARNING]
> Při připojování clusteru AKS k pracovnímu prostoru můžete definovat, jak budete cluster používat, nastavením `cluster_purpose` parametru.
>
> Pokud parametr nezadáte nebo nastavíte `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, cluster musí mít k dispozici alespoň 12 virtuálních procesorů. `cluster_purpose`
>
> Pokud nastavíte `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, cluster nemusí mít 12 virtuálních procesorů. Pro vývoj a testování doporučujeme aspoň 2 virtuální procesory. Cluster, který je nakonfigurovaný pro vývoj a testování, ale není vhodný pro provoz na úrovni produkčního prostředí a může prodloužit dobu odvození. Clustery pro vývoj a testování také nezaručují odolnost proti chybám.

Další informace o vytvoření clusteru AKS pomocí Azure CLI nebo portálu najdete v následujících článcích:

* [Vytvoření clusteru AKS (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Vytvoření clusteru AKS (portál)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Následující příklady ukazují, jak připojit existující cluster AKS 1.12. # # k vašemu pracovnímu prostoru:

**Používání sady SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)
```

Další informace o třídách, metodách a parametrech použitých v tomto příkladu naleznete v následujících referenčních dokumentech:

* [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. Attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Použití rozhraní příkazového řádku**

Pokud chcete připojit existující cluster pomocí rozhraní příkazového řádku, musíte získat ID prostředku pro existující cluster. Tuto hodnotu získáte pomocí následujícího příkazu. Nahraďte `myexistingcluster` názvem vašeho clusteru AKS. Nahraďte `myresourcegroup` skupinou prostředků, která obsahuje cluster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Tento příkaz vrátí hodnotu podobnou následujícímu textu:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Pokud chcete připojit existující cluster k pracovnímu prostoru, použijte následující příkaz. Nahraďte `aksresourceid` hodnotou vrácenou předchozím příkazem. Nahraďte `myresourcegroup` skupinou prostředků, která obsahuje váš pracovní prostor. Nahraďte `myworkspace` názvem vašeho pracovního prostoru.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Další informace najdete v referenčních informacích [AZ ml computetarget Attach AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) .

## <a name="deploy-to-aks"></a>Nasazení do AKS

Pokud chcete nasadit model do služby Azure Kubernetes, vytvořte __konfiguraci nasazení__ , která popisuje potřebné výpočetní prostředky. Například počet jader a paměti. Potřebujete také __konfiguraci odvození__, která popisuje prostředí potřebné pro hostování modelu a webové služby. Další informace o vytvoření konfigurace odvození najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Použití sady SDK

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Další informace o třídách, metodách a parametrech použitých v tomto příkladu naleznete v následujících referenčních dokumentech:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Model. deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)
* [WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Použití rozhraní příkazového řádku

Chcete-li nasadit pomocí rozhraní příkazového řádku, použijte následující příkaz. Nahraďte `myaks` názvem výpočetního cíle AKS. Nahraďte `mymodel:1` názvem a verzí registrovaného modelu. Nahraďte `myservice` názvem, který tuto službu poskytne:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

Další informace najdete v referenčních informacích k [nasazení modelu AZ ml model](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) . 

### <a name="using-vs-code"></a>Použití VS Code

Informace o použití VS Code najdete v tématu [nasazení do AKS prostřednictvím rozšíření vs Code](how-to-vscode-tools.md#deploy-and-manage-models).

> [!IMPORTANT] 
> Nasazení prostřednictvím VS Code vyžaduje, aby byl cluster AKS vytvořen nebo připojen k vašemu pracovnímu prostoru předem.

## <a name="web-service-authentication"></a>Ověřování webové služby

Při nasazování do služby Azure Kubernetes je ve výchozím nastavení povolené ověřování __na základě klíčů__ . Můžete také povolit ověřování __na základě tokenů__ . Ověřování na základě tokenu vyžaduje, aby klienti používali účet Azure Active Directory k vyžádání ověřovacího tokenu, který se používá k provádění požadavků na nasazenou službu.

Chcete-li __Zakázat__ ověřování, `auth_enabled=False` nastavte parametr při vytváření konfigurace nasazení. Následující příklad zakáže ověřování pomocí sady SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Informace o ověřování od klientské aplikace najdete v tématu [využívání modelu Azure Machine Learning nasazeného jako webové služby](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Ověřování pomocí klíčů

Pokud je povolené klíčové ověřování, můžete k načtení primárního `get_keys` a sekundárního ověřovacího klíče použít metodu:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Pokud potřebujete znovu vygenerovat klíč, použijte[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Ověřování pomocí tokenů

Pokud chcete povolit ověřování pomocí tokenu `token_auth_enabled=True` , nastavte parametr při vytváření nebo aktualizaci nasazení. Následující příklad povoluje ověření tokenu pomocí sady SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Pokud je povoleno ověřování tokenu, můžete použít `get_token` metodu k načtení tokenu JWT a času vypršení platnosti tokenu:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Po `refresh_by` čase tokenu budete muset požádat o nový token.
>
> Microsoft důrazně doporučuje vytvořit pracovní prostor Azure Machine Learning ve stejné oblasti jako cluster služby Azure Kubernetes. K ověřování pomocí tokenu webová služba provede volání do oblasti, ve které je vytvořen Azure Machine Learning pracovní prostor. Pokud oblast pracovního prostoru není k dispozici, nebudete moci načíst token pro webovou službu, a to i v případě, že se váš cluster nachází v jiné oblasti než váš pracovní prostor. To efektivně vede k nedostupnosti ověřování na základě tokenů, dokud nebude oblast pracovního prostoru znovu dostupná. Navíc čím větší je vzdálenost mezi oblastí vašeho clusteru a oblastí vašeho pracovního prostoru, tím déle bude trvat Načtení tokenu.

## <a name="update-the-web-service"></a>Aktualizovat webovou službu

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Další postup

* [Zabezpečené experimentování a odvozování ve virtuální síti](how-to-enable-virtual-network.md)
* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Řešení potíží s nasazením](how-to-troubleshoot-deployment.md)
* [Zabezpečení webových služeb Azure Machine Learning s protokolem SSL](how-to-secure-web-service.md)
* [Používání modelu ML nasadit jako webovou službu](how-to-consume-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)

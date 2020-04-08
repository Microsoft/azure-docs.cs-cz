---
title: Jak nasadit modely do služby Azure Kubernetes
titleSuffix: Azure Machine Learning
description: Zjistěte, jak nasadit modely Azure Machine Learning jako webovou službu pomocí služby Azure Kubernetes Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 01/16/2020
ms.openlocfilehash: aec1b7f7bf60be34d21d52ca652a776cf3275fe8
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811765"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Nasazení modelu do clusteru služby Azure Kubernetes
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Zjistěte, jak pomocí Azure Machine Learning nasadit model jako webovou službu ve službě Azure Kubernetes Service (AKS). Služba Azure Kubernetes service je vhodná pro nasazení ve vysokém měřítku. Pokud potřebujete jednu nebo více z následujících funkcí, použijte službu Azure Kubernetes:

- __Rychlá doba odezvy__.
- __Automatické škálování__ nasazené služby.
- __Možnosti hardwarové akcelerace,__ jako je GPU a pole programovatelná brány pole (FPGA).

> [!IMPORTANT]
> Škálování clusteru není k dispozici prostřednictvím sady Azure Machine Learning SDK. Další informace o škálování uzlů v clusteru AKS naleznete v [tématu Škálování počtu uzlů v clusteru AKS](../aks/scale-cluster.md).

Při nasazování do služby Azure Kubernetes se nasazujete do clusteru AKS, který je __připojený k vašemu pracovnímu prostoru__. Cluster AKS lze připojit ke svému pracovnímu prostoru dvěma způsoby:

* Vytvořte cluster AKS pomocí azure machine learnings sdk, machine learningcli nebo [Azure Machine Learning studio](https://ml.azure.com). Tento proces automaticky připojí cluster k pracovnímu prostoru.
* Připojte existující cluster AKS k pracovnímu prostoru Azure Machine Learning. Cluster lze připojit pomocí Azure Machine Learning SDK, Machine Learning CLI nebo Azure Machine Learning studio.

> [!IMPORTANT]
> Proces vytváření nebo připojování je jednorázový úkol. Jakmile je cluster AKS připojen k pracovnímu prostoru, můžete jej použít pro nasazení. Cluster AKS můžete odpojit nebo odstranit, pokud jej již nepotřebujete. Po odpojení nebo odstranění už nebudete moct nasadit do clusteru.

## <a name="prerequisites"></a>Požadavky

- Pracovní prostor služby Azure Machine Learning. Další informace najdete [v tématu Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

- Model strojového učení registrovaný ve vašem pracovním prostoru. Pokud nemáte registrovaný model, přečtěte si, [jak a kde nasadit modely](how-to-deploy-and-where.md).

- [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), Azure Machine Learning Python [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)nebo [rozšíření Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Fragmenty kódu __Pythonu__ v tomto článku předpokládají, že jsou nastaveny následující proměnné:

    * `ws`- Nastavte do pracovního prostoru.
    * `model`- Nastavte na váš registrovaný model.
    * `inference_config`- Nastavte na odvození konfigurace pro model.

    Další informace o nastavení těchto proměnných naleznete v tématu [Jak a kde nasadit modely](how-to-deploy-and-where.md).

- Fragmenty __zapínání na vod v__ ykrese v tomto článku předpokládají, že jste vytvořili `inferenceconfig.json` dokument. Další informace o vytvoření tohoto dokumentu naleznete v tématu [Jak a kde nasadit modely](how-to-deploy-and-where.md).

## <a name="create-a-new-aks-cluster"></a>Vytvoření nového clusteru AKS

**Odhad času**: Přibližně 20 minut.

Vytvoření nebo připojení clusteru AKS je jednorázový proces pro váš pracovní prostor. Tento cluster můžete znovu použít pro více nasazení. Pokud odstraníte cluster nebo skupinu prostředků, která jej obsahuje, musíte vytvořit nový cluster při příštím nasazení. K pracovnímu prostoru můžete mít připojeno více clusterů AKS.

> [!TIP]
> Pokud chcete zabezpečit cluster AKS pomocí virtuální sítě Azure, musíte nejprve vytvořit virtuální síť. Další informace najdete [v tématu Zabezpečené experimentování a odvození pomocí virtuální sítě Azure](how-to-enable-virtual-network.md#aksvnet).

Pokud chcete vytvořit cluster AKS pro __vývoj__, __ověřování__a __testování__ namísto výroby, můžete zadat __účel clusteru__ pro __vývoj .__

> [!WARNING]
> Pokud nastavíte `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, cluster, který je vytvořen není vhodný pro provoz na úrovni výroby a může zvýšit dobu odvození. Vývojové a testovací clustery také nezaručují odolnost proti chybám. Pro vývojové a testovací clustery doporučujeme alespoň 2 virtuální procesory.

Následující příklady ukazují, jak vytvořit nový cluster AKS pomocí sady SDK a CLI:

**Použití sady SDK**

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
> Pro [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), pokud vyberete `agent_count` `vm_size`vlastní `cluster_purpose` hodnoty `DEV_TEST`pro a , a `agent_count` není `vm_size` , pak je třeba se ujistit, vynásobené je větší nebo rovno 12 virtuálních procesorů. Například pokud používáte `vm_size` "Standard_D3_v2", který má 4 virtuální procesory, `agent_count` pak byste měli vybrat 3 nebo vyšší.
>
> Sada Azure Machine Learning SDK neposkytuje podporu škálování clusteru AKS. Chcete-li škálovat uzly v clusteru, použijte ui pro cluster AKS ve studiu Azure Machine Learning. Můžete změnit pouze počet uzlů, nikoli velikost virtuálního počítače clusteru.

Další informace o třídách, metodách a parametrech použitých v tomto příkladu naleznete v následujících referenčních dokumentech:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Použití cli**

```azurecli
az ml computetarget create aks -n myaks
```

Další informace naleznete [v az ml computetarget create aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) odkaz.

## <a name="attach-an-existing-aks-cluster"></a>Připojení existujícího clusteru AKS

**Časový odhad:** Přibližně 5 minut.

Pokud už máte cluster AKS ve vašem předplatném Azure a je verze 1.17 nebo nižší, můžete ji použít k nasazení image.

> [!TIP]
> Existující cluster AKS může být v jiné oblasti Azure než váš pracovní prostor Azure Machine Learning.
>
> Pokud chcete zabezpečit cluster AKS pomocí virtuální sítě Azure, musíte nejprve vytvořit virtuální síť. Další informace najdete [v tématu Zabezpečené experimentování a odvození pomocí virtuální sítě Azure](how-to-enable-virtual-network.md#aksvnet).

Při připojování clusteru AKS k pracovnímu prostoru můžete definovat, `cluster_purpose` jak bude cluster používat, nastavením parametru.

Pokud nenastavíte `cluster_purpose` parametr nebo `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`nastavení , musí mít cluster k dispozici alespoň 12 virtuálních procesorů.

Pokud nastavíte `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, pak cluster nemusí mít 12 virtuálních procesorů. Doporučujeme alespoň 2 virtuální procesory pro vývoj a testování. Cluster, který je nakonfigurován pro vývoj a testování, však není vhodný pro provoz na úrovni výroby a může zvýšit dobu odvození. Vývojové a testovací clustery také nezaručují odolnost proti chybám.

> [!WARNING]
> Nevytvářejte více souběžných příloh ke stejnému clusteru AKS z pracovního prostoru. Například připojení jednoho clusteru AKS k pracovnímu prostoru pomocí dvou různých názvů. Každá nová příloha přeruší předchozí existující přílohy.
>
> Chcete-li znovu připojit cluster AKS, například změnit tls nebo jiné nastavení konfigurace clusteru, musíte nejprve odebrat existující přílohu pomocí [AksCompute.detach()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Další informace o vytvoření clusteru AKS pomocí azure cli nebo portálu najdete v následujících článcích:

* [Vytvoření clusteru AKS (rozhraní příkazového řádku)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Vytvoření clusteru AKS (portál)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Následující příklady ukazují, jak připojit existující cluster AKS k pracovnímu prostoru:

**Použití sady SDK**

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

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Použití cli**

Chcete-li připojit existující cluster pomocí vykreslování se konzaličování linek, je třeba získat ID prostředku existujícího clusteru. Chcete-li získat tuto hodnotu, použijte následující příkaz. Nahraďte `myexistingcluster` název clusteru AKS. Nahraďte `myresourcegroup` skupinou prostředků, která obsahuje cluster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Tento příkaz vrátí hodnotu podobnou následujícímu textu:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Chcete-li připojit existující cluster k pracovnímu prostoru, použijte následující příkaz. Nahraďte `aksresourceid` hodnotou vrácenou předchozím příkazem. Nahraďte `myresourcegroup` skupinou prostředků, která obsahuje váš pracovní prostor. Nahraďte `myworkspace` název pracovního prostoru.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Další informace naleznete v tématu [az ml computetarget připojit aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) odkaz.

## <a name="deploy-to-aks"></a>Nasazení do AKS

Chcete-li nasadit model do služby Azure Kubernetes, vytvořte __konfiguraci nasazení,__ která popisuje potřebné výpočetní prostředky. Například počet jader a paměti. Potřebujete také __konfiguraci odvození__, která popisuje prostředí potřebné k hostování modelu a webové služby. Další informace o vytvoření konfigurace odvození naleznete v tématu [Jak a kde nasadit modely](how-to-deploy-and-where.md).

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
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Použití cli

Chcete-li nasadit pomocí příkazového příkazu k příkazu příkazu, použijte následující příkaz. Nahraďte `myaks` se názvem výpočetního cíle AKS. Nahraďte `mymodel:1` název a verzi registrovaného modelu. Chcete-li tuto službu poskytnout, nahraďte `myservice` názvem:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Další informace naleznete v odkazu [na nasazení modelu az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

### <a name="using-vs-code"></a>Použití VS Code

Informace o použití VS Code najdete [v tématu nasazení do AKS prostřednictvím rozšíření VS Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Nasazení prostřednictvím kódu VS vyžaduje, aby byl cluster AKS předem vytvořen nebo připojen k vašemu pracovnímu prostoru.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Nasazení modelů do AKS pomocí řízeného zavádění (náhled)

Analyzujte a povyšujte verze modelu řízeným způsobem pomocí koncových bodů. Můžete nasadit až šest verzí za jeden koncový bod. Koncové body poskytují následující funkce:

* Nakonfigurujte __procento vyhodnocování přenosů odeslaných do každého koncového bodu__. Například trasa 20 % provozu na koncový bod "test" a 80 % na "výrobu".

    > [!NOTE]
    > Pokud neúčtujete 100 % provozu, všechny zbývající procento jsou směrovány na __výchozí__ verzi koncového bodu. Pokud například nakonfigurujete verzi koncového bodu "test" tak, aby získala 10 % provozu, a "prod" pro 30 %, zbývajících 60 % je odesláno do výchozí verze koncového bodu.
    >
    > První vytvořená verze koncového bodu je automaticky nakonfigurována jako výchozí. To můžete změnit `is_default=True` nastavením při vytváření nebo aktualizaci verze koncového bodu.
     
* Označte verzi koncového bodu jako __ovládací prvek__ nebo __ošetření__. Například aktuální verze koncového bodu výroby může být ovládací prvek, zatímco potenciální nové modely jsou nasazeny jako verze zpracování. Po vyhodnocení výkonu verze léčby, pokud jeden překonává aktuální ovládací prvek, může být povýšen na novou výrobu/ovládací prvek.

    > [!NOTE]
    > Můžete mít pouze __jeden__ ovládací prvek. Můžete mít více ošetření.

Přehledy aplikací můžete povolit k zobrazení provozních metrik koncových bodů a nasazených verzí.

### <a name="create-an-endpoint"></a>Vytvoření koncového bodu
Jakmile budete připraveni k nasazení modelů, vytvořte koncový bod vyhodnocování a nasaďte svou první verzi. Následující příklad ukazuje, jak nasadit a vytvořit koncový bod pomocí sady SDK. První nasazení bude definováno jako výchozí verze, což znamená, že nespecifikovaný percentil provozu ve všech verzích přejde na výchozí verzi.  

> [!TIP]
> V následujícím příkladu konfigurace nastaví verzi počátečního koncového bodu pro zpracování 20 % provozu. Vzhledem k tomu, že se jedná o první koncový bod, je to také výchozí verze. A protože nemáme žádné jiné verze pro ostatní 80% provozu, je směrován a výchozí stejně. Dokud nebudou nasazeny jiné verze, které berou procento provozu, tato verze efektivně obdrží 100 % provozu.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Aktualizace a přidání verzí do koncového bodu

Přidejte do koncového bodu další verzi a nakonfigurujte percentil vyhodnocování provozu, který bude na verzi. Existují dva typy verzí, kontrolní a léčebná verze. Může existovat více verzí léčby, které vám pomohou porovnat s jednou verzí ovládacího prvku.

> [!TIP]
> Druhá verze, vytvořená následujícím fragmentem kódu, přijímá 10 % provozu. První verze je nakonfigurována pro 20 %, takže pouze 30 % provozu je konfigurováno pro konkrétní verze. Zbývajících 70 % je odesláno do první verze koncového bodu, protože je také výchozí verze.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

Aktualizujte existující verze nebo je odstraňte v koncovém bodě. Můžete změnit výchozí typ verze, typ ovládacího prvku a percentil provozu. V následujícím příkladu druhá verze zvyšuje jeho provoz na 40 % a je nyní výchozí.

> [!TIP]
> Po následujícím fragmentu kódu je nyní výchozí druhá verze. Nyní je nakonfigurován pro 40 %, zatímco původní verze je stále nakonfigurována pro 20 %. To znamená, že 40 % provozu není započítáno podle konfigurace verzí. Zbývající provoz bude směrován na druhou verzi, protože je nyní výchozí. Účinně přijímá 80% provozu.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Ověřování webové služby

Při nasazování do služby Azure Kubernetes je ověřování založené na __klíči__ ve výchozím nastavení povolené. Můžete také povolit ověřování __založené na tokenech.__ Ověřování založené na tokenech vyžaduje, aby klienti používali účet Azure Active Directory k vyžádání ověřovacího tokenu, který se používá k požadavkům na nasazenou službu.

Chcete-li __zakázat__ ověřování, nastavte `auth_enabled=False` parametr při vytváření konfigurace nasazení. Následující příklad zakáže ověřování pomocí sady SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Informace o ověřování z klientské aplikace najdete v tématu [využití modelu Azure Machine Learning nasazeného jako webová služba](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Ověřování pomocí klíčů

Pokud je povoleno ověřování pomocí `get_keys` klíče, můžete pomocí této metody načíst primární a sekundární ověřovací klíč:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Pokud potřebujete znovu vygenerovat klíč, použijte[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Ověřování pomocí tokenů

Chcete-li povolit `token_auth_enabled=True` ověřování tokenů, nastavte parametr při vytváření nebo aktualizaci nasazení. Následující příklad umožňuje ověřování tokenů pomocí sady SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Pokud je povoleno ověřování tokenu, můžete použít metodu `get_token` k načtení tokenu JWT a doby vypršení platnosti tohoto tokenu:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Budete muset požádat o nový token `refresh_by` po uplynutí doby tokenu.
>
> Microsoft důrazně doporučuje vytvořit pracovní prostor Azure Machine Learning ve stejné oblasti jako cluster služby Azure Kubernetes. Chcete-li ověřit pomocí tokenu, webová služba provede volání do oblasti, ve které se vytvoří pracovní prostor Azure Machine Learning. Pokud oblast vašeho pracovního prostoru není k dispozici, nebude možné načíst token pro webovou službu, i když se váš cluster nachází v jiné oblasti než váš pracovní prostor. To efektivně vede k ověřování na základě tokenu není k dispozici, dokud oblast vašeho pracovního prostoru je opět k dispozici. Kromě toho, čím větší je vzdálenost mezi oblastí clusteru a oblastí pracovního prostoru, tím déle bude trvat načtení tokenu.

## <a name="update-the-web-service"></a>Aktualizace webové služby

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Další kroky

* [Bezpečné experimentování a odvození ve virtuální síti](how-to-enable-virtual-network.md)
* [Jak nasadit model pomocí vlastní image Dockeru](how-to-deploy-custom-docker-image.md)
* [Řešení potíží s nasazením](how-to-troubleshoot-deployment.md)
* [Použití TLS k zabezpečení webové služby prostřednictvím Azure Machine Learning](how-to-secure-web-service.md)
* [Využití modelu ML nasazeného jako webová služba](how-to-consume-web-service.md)
* [Monitorování modelů Azure Machine Learning pomocí přehledů aplikací](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely ve výrobě](how-to-enable-data-collection.md)

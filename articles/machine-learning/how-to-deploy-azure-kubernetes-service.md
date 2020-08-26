---
title: Nasazení modelů ML do služby Kubernetes
titleSuffix: Azure Machine Learning
description: Naučte se, jak nasadit modely Azure Machine Learning jako webovou službu pomocí služby Azure Kubernetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: 6c85a7315fe05bb4fedabd176295523c2fa95d81
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855236"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Nasazení modelu do clusteru služby Azure Kubernetes
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Naučte se používat Azure Machine Learning k nasazení modelu jako webové služby ve službě Azure Kubernetes Service (AKS). Služba Azure Kubernetes je vhodná pro vysoce škálovatelná produkční nasazení. Použijte službu Azure Kubernetes, pokud potřebujete jednu nebo více z následujících možností:

- __Rychlá doba odezvy__.
- Automatické __škálování__ nasazené služby.
- Možnosti __hardwarové akcelerace__ , jako například GPU a pole programovatelné brány (FPGA).

> [!IMPORTANT]
> Škálování clusteru není k dispozici prostřednictvím sady Azure Machine Learning SDK. Další informace o škálování uzlů v clusteru AKS najdete v tématu. 
- [Ruční škálování počtu uzlů v clusteru AKS](../aks/scale-cluster.md)
- [Nastavení automatického škálování clusteru v AKS](../aks/cluster-autoscaler.md)

Při nasazování do služby Azure Kubernetes nasadíte do clusteru AKS, který je __připojený k vašemu pracovnímu prostoru__. Existují dva způsoby, jak připojit cluster AKS k vašemu pracovnímu prostoru:

* Vytvořte cluster AKS pomocí sady Azure Machine Learning SDK, Machine Learning CLI nebo [Azure Machine Learning Studio](https://ml.azure.com). Tento proces automaticky připojí cluster k pracovnímu prostoru.
* Připojte existující cluster AKS k pracovnímu prostoru Azure Machine Learning. Cluster se dá připojit pomocí Azure Machine Learning SDK, Machine Learning CLI nebo Azure Machine Learning studia.

Cluster AKS a pracovní prostor AML můžou být v různých skupinách prostředků.

> [!IMPORTANT]
> Proces vytvoření nebo přílohy je jednorázovým úkolem. Jakmile je cluster AKS připojený k pracovnímu prostoru, můžete ho použít pro nasazení. Cluster AKS můžete odpojit nebo odstranit, pokud ho už nepotřebujete. Až se odpojíte nebo odstraníte, nebudete už moct nasadit do clusteru.

> [!IMPORTANT]
> Doporučujeme, abyste před nasazením do webové služby ladit místně. Další informace najdete v tématu [ladění místně](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally) .
>
> Můžete využít také [poznámkový blok služby Azure Machine Learning pro místní nasazení](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local).

## <a name="prerequisites"></a>Předpoklady

- Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

- Model služby Machine Learning, který je zaregistrován ve vašem pracovním prostoru. Pokud nemáte registrovaný model, přečtěte si téma [jak a kde nasadit modely](how-to-deploy-and-where.md).

- [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)nebo [rozšíření Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Fragmenty kódu __Pythonu__ v tomto článku předpokládají, že jsou nastavené následující proměnné:

    * `ws` – Nastavte na svůj pracovní prostor.
    * `model` – Nastavte na registrovaný model.
    * `inference_config` – Nastavte na odvození konfigurace pro model.

    Další informace o nastavení těchto proměnných najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

- Fragmenty rozhraní příkazového __řádku__ v tomto článku předpokládají, že jste vytvořili `inferenceconfig.json` dokument. Další informace o vytváření tohoto dokumentu najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

- Pokud v clusteru potřebujete nasadit Standard Load Balancer (SLB) místo základního Load Balancer (BLB), vytvořte cluster na portálu AKS/CLI/SDK a pak ho připojte k pracovnímu prostoru AML.

- Pokud máte Azure Policy, která omezuje vytvoření veřejné IP adresy, vytvoření clusteru AKS se nezdaří. AKS vyžaduje veřejnou IP adresu pro [odchozí přenosy](https://docs.microsoft.com/azure/aks/limit-egress-traffic). Tento článek také poskytuje pokyny k uzamčení odchozího provozu z clusteru prostřednictvím veřejné IP adresy s výjimkou několika plně kvalifikovaných názvů domén. Existují dva způsoby, jak povolit veřejnou IP adresu:
  - Cluster může používat veřejnou IP adresu vytvořenou ve výchozím nastavení s BLB nebo SLB nebo
  - Cluster se dá vytvořit bez veřejné IP adresy a pak je nakonfigurovaná veřejná IP adresa s bránou firewall s trasou definovanou uživatelem, jak je uvedeno [tady](https://docs.microsoft.com/azure/aks/egress-outboundtype) . 
  
  Rovina ovládacího prvku AML nehovoří s touto veřejnou IP adresou. Mluví s rovinou ovládacího prvku AKS pro nasazení. 

- Pokud připojíte cluster AKS, který má [povolený povolený rozsah IP adres pro přístup k serveru rozhraní API](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges), povolte rozsahy IP adres AML contol pro cluster AKS. Rovina ovládacího prvku AML se nasadí mezi spárované oblasti a nasadí Inferencing lusky do clusteru AKS. Bez přístupu k serveru rozhraní API se Inferencing lusky nedají nasadit. Při povolování rozsahů IP adres v clusteru AKS použijte [rozsahy IP adres](https://www.microsoft.com/en-us/download/confirmation.aspx?id=56519) pro obě [spárované oblasti]( https://docs.microsoft.com/azure/best-practices-availability-paired-regions) .


  Rozsahy IP adres Authroized fungují jenom s Standard Load Balancer.
 
 - Název výpočtu musí být v rámci pracovního prostoru jedinečný.
   - Název je povinný a musí mít délku 3 až 24 znaků.
   - Platné znaky jsou velká a malá písmena, číslice a znak-znaku.
   - Název musí začínat písmenem.
   - Název musí být jedinečný v rámci všech stávajících výpočtů v oblasti Azure. Pokud zvolený název není jedinečný, zobrazí se upozornění.
   
 - Pokud chcete nasadit modely do uzlů GPU nebo FPGAch uzlů (nebo jakékoli konkrétní SKU), musíte vytvořit cluster s konkrétní SKU. Neexistuje žádná podpora pro vytváření fondu sekundárních uzlů v existujícím clusteru a nasazování modelů do fondu sekundárních uzlů.

## <a name="create-a-new-aks-cluster"></a>Vytvoření nového clusteru AKS

**Časový odhad**: přibližně 10 minut.

Vytvoření nebo připojení clusteru AKS je jednorázový proces pro váš pracovní prostor. Tento cluster můžete použít pro více nasazení. Pokud odstraníte cluster nebo skupinu prostředků, která ho obsahuje, musíte při příštím nasazení vytvořit nový cluster. K vašemu pracovnímu prostoru můžete připojit více clusterů AKS.
 
Azure Machine Learning teď podporuje používání služby Azure Kubernetes s povoleným privátním odkazem.
Pokud chcete vytvořit privátní cluster AKS, postupujte podle dokumentů [zde](https://docs.microsoft.com/azure/aks/private-clusters) .

> [!TIP]
> Pokud chcete svůj cluster AKS zabezpečit pomocí Virtual Network Azure, musíte nejdřív vytvořit virtuální síť. Další informace najdete v tématu [zabezpečené experimenty a odvozování pomocí Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Pokud chcete vytvořit cluster AKS pro __vývoj__, __ověřování__a __testování__ namísto produkčního prostředí, můžete určit __účel clusteru__ pro vývoj v __testovacím__prostředí.

> [!WARNING]
> Pokud jste nastavili `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` , cluster, který se vytvoří, není vhodný pro provoz na úrovni produkčního prostředí a může prodloužit dobu odvození. Clustery pro vývoj a testování také nezaručují odolnost proti chybám. Pro clustery pro vývoj a testování doporučujeme aspoň 2 virtuální procesory.

Následující příklady ukazují, jak vytvořit nový cluster AKS pomocí sady SDK a rozhraní příkazového řádku:

**Použití sady SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()
# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> V [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py) případě, že vyberete vlastní hodnoty pro `agent_count` a a `vm_size` `cluster_purpose` není, je `DEV_TEST` nutné zajistit, aby `agent_count` vynásobený hodnotou `vm_size` byla větší než nebo rovna 12 virtuálním procesorům. Pokud například použijete `vm_size` "Standard_D3_v2", která má 4 virtuální procesory, měli byste vybrat `agent_count` 3 nebo vyšší.
>
> Sada SDK pro Azure Machine Learning neposkytuje podporu škálování clusteru AKS. Pokud chcete škálovat uzly v clusteru, použijte uživatelské rozhraní pro cluster AKS v nástroji Azure Machine Learning Studio. Můžete změnit jenom počet uzlů, nikoli velikost virtuálního počítače v clusteru.

Další informace o třídách, metodách a parametrech použitých v tomto příkladu naleznete v následujících referenčních dokumentech:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget. wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Použití rozhraní příkazového řádku**

```azurecli
az ml computetarget create aks -n myaks
```

Další informace najdete v tématu [AZ ml computetarget Create AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference.

## <a name="attach-an-existing-aks-cluster"></a>Připojit existující cluster AKS

**Časový odhad:** Přibližně 5 minut.

Pokud už máte cluster AKS ve svém předplatném Azure a verze 1,17 nebo nižší, můžete ho použít k nasazení image.

> [!TIP]
> Stávající cluster AKS může být v jiné oblasti Azure, než je váš pracovní prostor Azure Machine Learning.
>
> Pokud chcete svůj cluster AKS zabezpečit pomocí Virtual Network Azure, musíte nejdřív vytvořit virtuální síť. Další informace najdete v tématu [zabezpečené experimenty a odvozování pomocí Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Při připojování clusteru AKS k pracovnímu prostoru můžete definovat, jak budete cluster používat, nastavením `cluster_purpose` parametru.

Pokud parametr nezadáte `cluster_purpose` nebo nastavíte `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD` , cluster musí mít k dispozici alespoň 12 virtuálních procesorů.

Pokud nastavíte `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` , cluster nemusí mít 12 virtuálních procesorů. Pro vývoj a testování doporučujeme aspoň 2 virtuální procesory. Cluster, který je nakonfigurovaný pro vývoj a testování, ale není vhodný pro provoz na úrovni produkčního prostředí a může prodloužit dobu odvození. Clustery pro vývoj a testování také nezaručují odolnost proti chybám.

> [!WARNING]
> Nevytvářejte více souběžných příloh ke stejnému AKS clusteru z vašeho pracovního prostoru. Například připojení jednoho clusteru AKS k pracovnímu prostoru pomocí dvou různých názvů. Každá nová příloha zruší předchozí existující přílohy.
>
> Pokud chcete cluster AKS znovu připojit, například pokud chcete změnit nastavení TLS nebo jiné konfigurace clusteru, musíte nejdřív odebrat existující přílohu pomocí [AksCompute. detach ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Další informace o vytvoření clusteru AKS pomocí Azure CLI nebo portálu najdete v následujících článcích:

* [Vytvoření clusteru AKS (rozhraní příkazového řádku)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Vytvoření clusteru AKS (portál)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)
* [Vytvoření clusteru AKS (šablona ARM v šablonách rychlého startu Azure)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

Následující příklady ukazují, jak připojit existující cluster AKS k vašemu pracovnímu prostoru:

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

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

Další informace o třídách, metodách a parametrech použitých v tomto příkladu naleznete v následujících referenčních dokumentech:

* [AksCompute. attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
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

> [!NOTE]
> Počet modelů, které se mají nasadit, je omezený na 1 000 modelů na jedno nasazení (na kontejner).


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
* [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Model. deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Použití rozhraní příkazového řádku

Chcete-li nasadit pomocí rozhraní příkazového řádku, použijte následující příkaz. Nahraďte `myaks` názvem výpočetního cíle AKS. Nahraďte `mymodel:1` názvem a verzí registrovaného modelu. Nahraďte `myservice` názvem, který tuto službu poskytne:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Další informace najdete v referenčních informacích k [nasazení modelu AZ ml model](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a name="using-vs-code"></a>Použití VS Code

Informace o použití VS Code najdete v tématu [nasazení do AKS prostřednictvím rozšíření vs Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Nasazení prostřednictvím VS Code vyžaduje, aby byl cluster AKS vytvořen nebo připojen k vašemu pracovnímu prostoru předem.

### <a name="understand-the-deployment-processes"></a>Pochopení procesů nasazení

Slovo "Deployment" se používá v Kubernetes i Azure Machine Learning. Nasazení má v těchto dvou kontextech různé významy. V Kubernetes `Deployment` je entita konkrétní entitou, která je zadána pomocí deklarativního souboru YAML. Kubernetes `Deployment` má definovaný životní cyklus a konkrétní vztahy k jiným entitám Kubernetes, jako jsou `Pods` a `ReplicaSets` . Informace o Kubernetes najdete v dokumentaci k dokumentům a videím na adrese [Kubernetes?](https://aka.ms/k8slearning).

V Azure Machine Learning se "nasazení" používá v obecnější smyslu, jak zpřístupnit a vyčistit prostředky projektu. Postup, který Azure Machine Learning považuje za součást nasazení, jsou tyto:

1. Zipování soubory ve složce projektu, které budou ignorovány, které jsou zadány v souboru. amlignore nebo. gitignore
1. Vertikální navýšení kapacity výpočetního clusteru (týká se Kubernetes)
1. Sestavování nebo stahování souboru Dockerfile do výpočetního uzlu (týká se Kubernetes)
    1. Systém vypočítá hodnotu hash: 
        - Základní obrázek 
        - Vlastní kroky Docker (viz [nasazení modelu pomocí vlastního Docker Base image](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image))
        - Definice conda YAML (viz téma [vytvoření & použití softwarových prostředí v Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments))
    1. Systém používá tuto hodnotu hash jako klíč při vyhledávání Azure Container Registry pracovního prostoru (ACR).
    1. Pokud se nenajde, vyhledá shodu v globálním ACR
    1. Pokud není nalezen, systém vytvoří novou bitovou kopii (která bude uložena do mezipaměti a bude registrována v pracovním prostoru ACR).
1. Stažení souboru projektu zip do dočasného úložiště na výpočetním uzlu
1. Rozzipovává soubor projektu
1. Prováděný výpočetní uzel `python <entry script> <arguments>`
1. Ukládání protokolů, souborů modelů a dalších souborů zapsaných do `./outputs` účtu úložiště přidruženého k pracovnímu prostoru
1. Horizontální snížení kapacity, včetně odebrání dočasného úložiště (týká se Kubernetes)

Při použití AKS se horizontální navýšení a snížení kapacity výpočetních prostředků řídí pomocí Kubernetes, a to pomocí souboru Dockerfile sestaveného nebo nalezeného, jak je popsáno výše. 

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Nasazení modelů do AKS pomocí řízeného zavedení (Preview)

Analyzujte a Propagujte verze modelu řízeným způsobem pomocí koncových bodů. Můžete nasadit až šest verzí za jedním koncovým bodem. Koncové body poskytují následující možnosti:

* Nakonfigurujte __procento provozu vyhodnocování odeslaného do každého koncového bodu__. Například směrujte 20% provozu do koncového bodu "test" a 80% na "produkční".

    > [!NOTE]
    > Pokud neprovedete účet pro 100% provozu, všechna zbývající procentuální hodnota se směruje do __výchozí__ verze koncového bodu. Pokud například nakonfigurujete verzi Endpoint pro test, abyste získali 10% provozu a "prod" po dobu 30%, bude zbývající 60% odesláno do výchozí verze koncového bodu.
    >
    > První vytvořená verze koncového bodu je automaticky nakonfigurovaná jako výchozí. Toto nastavení můžete změnit nastavením `is_default=True` při vytváření nebo aktualizaci verze koncového bodu.
     
* Označení verze koncového bodu buď jako __řízení__ , nebo jako __zpracování__. Například aktuální verze produkčního koncového bodu může být ovládací prvek, zatímco potenciální nové modely jsou nasazeny jako verze zpracování. Po vyhodnocení výkonu pro verze zpracování, pokud jeden z nich překoná aktuální ovládací prvek, může být povýšen na novou produkci nebo řízení.

    > [!NOTE]
    > Můžete mít jenom __jeden__ ovládací prvek. Můžete mít více způsobů zpracování.

Službu App Insights můžete povolit pro zobrazení provozních metrik koncových bodů a nasazených verzí.

### <a name="create-an-endpoint"></a>Vytvoření koncového bodu
Až budete připraveni k nasazení modelů, vytvořte hodnoticí koncový bod a nasaďte první verzi. Následující příklad ukazuje, jak nasadit a vytvořit koncový bod pomocí sady SDK. První nasazení bude definováno jako výchozí verze, což znamená, že nespecifikovaný percentil provozu napříč všemi verzemi přejde na výchozí verzi.  

> [!TIP]
> V následujícím příkladu konfigurace nastavuje počáteční verzi koncového bodu pro zpracování 20% provozu. Vzhledem k tomu, že se jedná o první koncový bod, je to také výchozí verze. A vzhledem k tomu, že pro ostatní 80% provozu nejsou k dispozici žádné jiné verze, je tato hodnota směrována i na výchozí. Dokud nebudou nasazeny jiné verze, které berou v úvahu procento provozu, bude tato hodnota efektivně přijímat 100% provozu.

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

Přidejte do svého koncového bodu jinou verzi a nakonfigurujte percentil pro přenos dat bodování na verzi. Existují dva typy verzí, ovládací prvek a verze zpracování. Pro porovnání s verzí s jediným ovládacím prvkem může být více verzí zpracování.

> [!TIP]
> Druhá verze vytvořená následujícím fragmentem kódu přijímá 10% provozu. První verze je nakonfigurovaná na 20%, takže se pro konkrétní verze nakonfigurují jenom 30% provozu. Zbývající 70% se pošle na první verzi koncového bodu, protože to je taky výchozí verze.

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

Aktualizujte existující verze nebo je odstraňte v koncovém bodu. Můžete změnit výchozí typ verze, typ ovládacího prvku a percentil provozu. V následujícím příkladu druhá verze zvyšuje svůj provoz na 40% a teď je výchozí hodnota.

> [!TIP]
> Po následujícím fragmentu kódu je teď ve výchozím nastavení druhá verze. Je teď nakonfigurované na 40%, ale původní verze je pořád nakonfigurovaná na 20%. To znamená, že 40% provozu není v konfiguracích verzí účtováno. Provoz zbylé bude směrován do druhé verze, protože je nyní výchozí. Efektivně přijímá 80% provozu.

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

Při nasazování do služby Azure Kubernetes je ve výchozím nastavení povolené ověřování __na základě klíčů__ . Můžete také povolit ověřování __na základě tokenů__ . Ověřování na základě tokenu vyžaduje, aby klienti používali účet Azure Active Directory k vyžádání ověřovacího tokenu, který se používá k provádění požadavků na nasazenou službu.

Chcete-li __Zakázat__ ověřování, nastavte `auth_enabled=False` parametr při vytváření konfigurace nasazení. Následující příklad zakáže ověřování pomocí sady SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Informace o ověřování od klientské aplikace najdete v tématu [využívání modelu Azure Machine Learning nasazeného jako webové služby](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Ověřování pomocí klíčů

Pokud je povolené klíčové ověřování, můžete `get_keys` k načtení primárního a sekundárního ověřovacího klíče použít metodu:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Pokud potřebujete znovu vygenerovat klíč, použijte [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Ověřování pomocí tokenů

Pokud chcete povolit ověřování pomocí tokenu, nastavte `token_auth_enabled=True` parametr při vytváření nebo aktualizaci nasazení. Následující příklad povoluje ověření tokenu pomocí sady SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Pokud je povoleno ověřování tokenu, můžete použít `get_token` metodu k načtení tokenu JWT a času vypršení platnosti tokenu:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Po čase tokenu budete muset požádat o nový token `refresh_by` .
>
> Microsoft důrazně doporučuje vytvořit pracovní prostor Azure Machine Learning ve stejné oblasti jako cluster služby Azure Kubernetes. K ověřování pomocí tokenu webová služba provede volání do oblasti, ve které je vytvořen Azure Machine Learning pracovní prostor. Pokud oblast pracovního prostoru není k dispozici, nebudete moci načíst token pro webovou službu, a to i v případě, že se váš cluster nachází v jiné oblasti než váš pracovní prostor. To efektivně vede k nedostupnosti ověřování na základě tokenů, dokud nebude oblast pracovního prostoru znovu dostupná. Navíc čím větší je vzdálenost mezi oblastí vašeho clusteru a oblastí vašeho pracovního prostoru, tím déle bude trvat Načtení tokenu.
>
> K načtení tokenu je nutné použít sadu SDK Azure Machine Learning nebo příkaz [AZ ml Service Get-Access-token](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-get-access-token) .

## <a name="next-steps"></a>Další kroky

* [Zabezpečené experimentování a odvozování ve virtuální síti](how-to-enable-virtual-network.md)
* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Řešení potíží s nasazením](how-to-troubleshoot-deployment.md)
* [Aktualizace webové služby](how-to-deploy-update-web-service.md)
* [Zabezpečení webové služby prostřednictvím služby Azure Machine Learning s využitím protokolu TLS](how-to-secure-web-service.md)
* [Využití modelu ML nasazeného jako webové služby](how-to-consume-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)

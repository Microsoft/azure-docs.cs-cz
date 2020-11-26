---
title: Nasazení modelů ML do služby Kubernetes
titleSuffix: Azure Machine Learning
description: Naučte se, jak nasadit modely Azure Machine Learning jako webovou službu pomocí služby Azure Kubernetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperfq1, deploy, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: d314c285057950ebaecb2e3f75a83948c8bf3cc2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182440"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Nasazení modelu do clusteru služby Azure Kubernetes

Naučte se používat Azure Machine Learning k nasazení modelu jako webové služby ve službě Azure Kubernetes Service (AKS). Služba Azure Kubernetes je vhodná pro vysoce škálovatelná produkční nasazení. Použijte službu Azure Kubernetes, pokud potřebujete jednu nebo více z následujících možností:

- __Doba rychlé odezvy__
- Automatické __škálování__ nasazené služby
- __Protokolování__
- __Shromažďování dat modelu__
- __Authentication__
- __Ukončení protokolu TLS__
- Možnosti __hardwarové akcelerace__ , jako je GPU a programovatelné pole brány (FPGA)

Při nasazování do služby Azure Kubernetes nasadíte do clusteru AKS, který je __připojený k vašemu pracovnímu prostoru__. Informace o připojení clusteru AKS k pracovnímu prostoru najdete v tématu [Vytvoření a připojení clusteru služby Azure Kubernetes](how-to-create-attach-kubernetes.md).

> [!IMPORTANT]
> Doporučujeme, abyste před nasazením do webové služby ladit místně. Další informace najdete v tématu [ladění místně](./how-to-troubleshoot-deployment-local.md) .
>
> Můžete využít také [poznámkový blok služby Azure Machine Learning pro místní nasazení](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local).

## <a name="prerequisites"></a>Předpoklady

- Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

- Model služby Machine Learning, který je zaregistrován ve vašem pracovním prostoru. Pokud nemáte registrovaný model, přečtěte si téma [jak a kde nasadit modely](how-to-deploy-and-where.md).

- [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)nebo [rozšíření Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Fragmenty kódu __Pythonu__ v tomto článku předpokládají, že jsou nastavené následující proměnné:

    * `ws` – Nastavte na svůj pracovní prostor.
    * `model` – Nastavte na registrovaný model.
    * `inference_config` – Nastavte na odvození konfigurace pro model.

    Další informace o nastavení těchto proměnných najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

- Fragmenty rozhraní příkazového __řádku__ v tomto článku předpokládají, že jste vytvořili `inferenceconfig.json` dokument. Další informace o vytváření tohoto dokumentu najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

- Cluster služby Azure Kubernetes je připojený k vašemu pracovnímu prostoru. Další informace najdete v tématu [Vytvoření a připojení clusteru služby Azure Kubernetes](how-to-create-attach-kubernetes.md).

    - Pokud chcete nasadit modely do uzlů GPU nebo FPGAch uzlů (nebo jakékoli konkrétní SKU), musíte vytvořit cluster s konkrétní SKU. Neexistuje žádná podpora pro vytváření fondu sekundárních uzlů v existujícím clusteru a nasazování modelů do fondu sekundárních uzlů.

## <a name="understand-the-deployment-processes"></a>Pochopení procesů nasazení

Slovo "Deployment" se používá v Kubernetes i Azure Machine Learning. Nasazení má v těchto dvou kontextech různé významy. V Kubernetes `Deployment` je entita konkrétní entitou, která je zadána pomocí deklarativního souboru YAML. Kubernetes `Deployment` má definovaný životní cyklus a konkrétní vztahy k jiným entitám Kubernetes, jako jsou `Pods` a `ReplicaSets` . Informace o Kubernetes najdete v dokumentaci k dokumentům a videím na adrese [Kubernetes?](https://aka.ms/k8slearning).

V Azure Machine Learning se "nasazení" používá v obecnější smyslu, jak zpřístupnit a vyčistit prostředky projektu. Postup, který Azure Machine Learning považuje za součást nasazení, jsou tyto:

1. Zipování soubory ve složce projektu, které budou ignorovány, které jsou zadány v souboru. amlignore nebo. gitignore
1. Vertikální navýšení kapacity výpočetního clusteru (týká se Kubernetes)
1. Sestavování nebo stahování souboru Dockerfile do výpočetního uzlu (týká se Kubernetes)
    1. Systém vypočítá hodnotu hash: 
        - Základní obrázek 
        - Vlastní kroky Docker (viz [nasazení modelu pomocí vlastního Docker Base image](./how-to-deploy-custom-docker-image.md))
        - Definice conda YAML (viz téma [vytvoření & použití softwarových prostředí v Azure Machine Learning](./how-to-use-environments.md))
    1. Systém používá tuto hodnotu hash jako klíč při vyhledávání Azure Container Registry pracovního prostoru (ACR).
    1. Pokud se nenajde, vyhledá shodu v globálním ACR
    1. Pokud není nalezen, systém vytvoří novou bitovou kopii (která bude uložena do mezipaměti a vložena do pracovního prostoru ACR).
1. Stažení souboru projektu zip do dočasného úložiště na výpočetním uzlu
1. Rozzipovává soubor projektu
1. Prováděný výpočetní uzel `python <entry script> <arguments>`
1. Ukládání protokolů, souborů modelů a dalších souborů zapsaných do `./outputs` účtu úložiště přidruženého k pracovnímu prostoru
1. Horizontální snížení kapacity, včetně odebrání dočasného úložiště (týká se Kubernetes)

### <a name="azure-ml-router"></a>Směrovač Azure ML

Front-end komponenta (AzureML-Fe), která směruje příchozí žádosti o odvození na nasazené služby, se automaticky škálují podle potřeby. Škálování služby AzureML-FE vychází z účelu a velikosti clusteru AKS (počet uzlů). Účel clusteru a uzly jsou nakonfigurovány, když [vytváříte nebo připojujete cluster AKS](how-to-create-attach-kubernetes.md). Existuje jedna služba AzureML-Fe na jeden cluster, která může běžet na více luskech.

> [!IMPORTANT]
> Při použití clusteru nakonfigurovaného jako __vývojového testování__ je samoobslužné škálování **zakázané**.

AzureML-FE škáluje jak nahoru (vertikálně) tak, aby používal více jader, a výstupní (horizontálně) pro použití více lusků. Při rozhodování o horizontálním navýšení kapacity se použije čas potřebný k směrování příchozích požadavků na odvození. Pokud tento čas překročí prahovou hodnotu, dojde k horizontálnímu navýšení kapacity. Pokud doba směrování příchozích požadavků pokračuje v překročení prahové hodnoty, dojde k horizontálnímu navýšení kapacity.

Při horizontálním navýšení kapacity a při jejich zmenšování se používá využití procesoru. Pokud je dosaženo prahové hodnoty využití procesoru, bude nejprve horizontální navýšení kapacity na front-endu. Pokud využití procesoru klesne do prahové hodnoty škálování, dojde k operaci škálování. Horizontální navýšení kapacity a navýšení kapacity proběhne, jenom když je k dispozici dostatek prostředků clusteru.

## <a name="deploy-to-aks"></a>Nasazení do AKS

Pokud chcete nasadit model do služby Azure Kubernetes, vytvořte __konfiguraci nasazení__ , která popisuje potřebné výpočetní prostředky. Například počet jader a paměti. Potřebujete také __konfiguraci odvození__, která popisuje prostředí potřebné pro hostování modelu a webové služby. Další informace o vytvoření konfigurace odvození najdete v tématu [jak a kde nasadit modely](how-to-deploy-and-where.md).

> [!NOTE]
> Počet modelů, které se mají nasadit, je omezený na 1 000 modelů na jedno nasazení (na kontejner).

<a id="using-the-cli"></a>

# <a name="python"></a>[Python](#tab/python)

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

* [AksCompute](/python/api/azureml-core/azureml.core.compute.aks.akscompute?preserve-view=true&view=azure-ml-py)
* [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?preserve-view=true&view=azure-ml-py)
* [Model. deploy](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truewait-for-deployment-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li nasadit pomocí rozhraní příkazového řádku, použijte následující příkaz. Nahraďte `myaks` názvem výpočetního cíle AKS. Nahraďte `mymodel:1` názvem a verzí registrovaného modelu. Nahraďte `myservice` názvem, který tuto službu poskytne:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Další informace najdete v referenčních informacích k [nasazení modelu AZ ml model](/cli/azure/ext/azure-cli-ml/ml/model?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Informace o použití VS Code najdete v tématu [nasazení do AKS prostřednictvím rozšíření vs Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Nasazení prostřednictvím VS Code vyžaduje, aby byl cluster AKS vytvořen nebo připojen k vašemu pracovnímu prostoru předem.

---

### <a name="autoscaling"></a>Automatické škálování

Komponenta, která zpracovává automatické škálování pro nasazení modelů Azure ML, je AzureML-FE, což je směrovač inteligentních žádostí. Vzhledem k tomu, že všechny požadavky na odvození procházejí, má data potřebná k automatickému škálování nasazených modelů.

> [!IMPORTANT]
> * **Pro nasazení modelů nepovolujte Kubernetes horizontálně pod autoscaleer (hPa)**. To by vedlo k tomu, že se dvě součásti automatického škálování vzájemně konkurují. Služba AzureML-FE je navržená tak, aby automaticky škáloval modely nasazené službou Azure ML, kde HPA by musela odhadnout nebo přibližná využití modelu z obecné metriky, jako je využití procesoru nebo konfigurace vlastní metriky.
> 
> * **AzureML-FE neškáluje počet uzlů v clusteru AKS**, protože to může vést k neočekávaným nákladům. Místo toho **škáluje počet replik modelu** v rámci hranic fyzického clusteru. Pokud potřebujete škálovat počet uzlů v rámci clusteru, můžete ručně škálovat cluster nebo [nakonfigurovat modul automatického škálování clusteru AKS](../aks/cluster-autoscaler.md).

Automatické škálování se dá řídit nastavením `autoscale_target_utilization` , `autoscale_min_replicas` a `autoscale_max_replicas` pro webovou službu AKS. Následující příklad ukazuje, jak povolit automatické škálování:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

Rozhodnutí o horizontálním navýšení/snížení kapacity vycházejí z využití aktuálních replik kontejnerů. Počet replik, které jsou zaneprázdněné (zpracování požadavku) dělený celkovým počtem aktuálních replik, je aktuální využití. Pokud tento počet překročí `autoscale_target_utilization` , vytvoří se další repliky. Pokud je nižší, jsou repliky sníženy. Ve výchozím nastavení je cílové využití 70%.

Rozhodnutí o přidání replik jsou Eager a rychlá (kolem 1 sekundy). Rozhodnutí o odebrání replik jsou konzervativní (přibližně 1 minutu).

Požadované repliky můžete vypočítat pomocí následujícího kódu:

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

Další informace o nastaveních `autoscale_target_utilization` , `autoscale_max_replicas` , a `autoscale_min_replicas` najdete v referenčních informacích k modulu [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py) .

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
> Pokud potřebujete znovu vygenerovat klíč, použijte [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py)

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
> K načtení tokenu je nutné použít sadu SDK Azure Machine Learning nebo příkaz [AZ ml Service Get-Access-token](/cli/azure/ext/azure-cli-ml/ml/service?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-get-access-token) .


### <a name="vulnerability-scanning"></a>Kontrola ohrožení zabezpečení

Azure Security Center zajišťuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami napříč hybridními cloudovými úlohami. Měli byste Azure Security Center, abyste mohli kontrolovat prostředky a dodržovat doporučení. Další informace najdete v tématu [integrace služeb Azure Kubernetes Services s Security Center](../security-center/defender-for-kubernetes-introduction.md).

## <a name="next-steps"></a>Další kroky

* [Použití Azure RBAC pro autorizaci Kubernetes](../aks/manage-azure-rbac.md)
* [Zabezpečené prostředí Inferencing s využitím Azure Virtual Network](how-to-secure-inferencing-vnet.md)
* [Postup nasazení modelu pomocí vlastní image Docker](how-to-deploy-custom-docker-image.md)
* [Řešení potíží s nasazením](how-to-troubleshoot-deployment.md)
* [Aktualizace webové služby](how-to-deploy-update-web-service.md)
* [Zabezpečení webové služby prostřednictvím služby Azure Machine Learning s využitím protokolu TLS](how-to-secure-web-service.md)
* [Využití modelu ML nasazeného jako webové služby](how-to-consume-web-service.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)

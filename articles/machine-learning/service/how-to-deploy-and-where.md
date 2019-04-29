---
title: Jak a kde nasadit modely
titleSuffix: Azure Machine Learning service
description: 'Zjistěte, jak a kde k nasazení vašich modelů služby Azure Machine Learning, včetně: Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge a Field-programmable gate Array.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: a6ef53d56fa293791658b37b16cbaff94aee6ef3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819859"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Nasazujte modely pomocí služby Azure Machine Learning

V tomto dokumentu zjistěte, jak model nasadit jako webovou službu v cloudu Azure nebo do zařízení IoT Edge. 

## <a name="compute-targets-for-deployment"></a>Cílových výpočetních prostředí pro nasazení

Pomocí sady SDK Azure Machine Learning trénovaný model nasadit do následujících umístění:

| Cílové výpočetní prostředí | Typ nasazení | Popis |
| ----- | ----- | ----- |
| [Azure Kubernetes Service (AKS)](#aks) | Odvození v reálném čase | Vhodné pro nasazení v produkčním prostředí vysoce škálovatelné. Nabízí automatické škálování a krátké doby odezvy. |
| [Azure Machine Learning Compute (amlcompute)](#azuremlcompute) | Odvození služby batch | Spusťte dávky předpovědi na výpočetní prostředí. Podporuje virtuální počítače s normální a s nízkou prioritou. |
| [Azure Container Instances (ACI)](#aci) | Testování | Je vhodný pro vývoj a testování. **Není vhodný pro úlohy v produkčním prostředí.** |
| [Azure IoT Edge](#iotedge) | (Preview) Modul IoT | Nasaďte modely na zařízeních IoT. Odvozování se stane v zařízení. |
| [Pole programmable gate array (FPGA)](#fpga) | (Preview) Webová služba | Mimořádně nízkou latenci pro odvozování v reálném čase. |

## <a name="deployment-workflow"></a>Pracovní postup nasazení

Proces nasazení modelu se podobá všech cílových výpočetních prostředí:

1. Trénování a zaregistrujte model.
1. Konfigurace a registrace bitovou kopii, která používá model.
1. Nasazení bitové kopie do cílového výpočetního prostředí.
1. Otestování nasazení

Následující video ukazuje nasazení do služby Azure Container Instances:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]


Další informace o konceptech pracovního postupu nasazení, najdete v části [spravovat, nasazovat a monitorovat modely pomocí služby Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites-for-deployment"></a>Požadavky na nasazení

[!INCLUDE [aml-prereq](../../../includes/aml-prereq.md)]

- Trénovaného modelu. Pokud nemáte trénovaného modelu, postupujte podle kroků v [trénování modelů](tutorial-train-models-with-aml.md) kurzu pro trénování a zaregistrovat ve službě Azure Machine Learning.

    > [!NOTE]
    > Zatímco služba Azure Machine Learning můžete pracovat s obecný model, který lze načíst v Python 3, příklady v tomto dokumentu ukazují uložené ve formátu Python pickle modelu.
    >
    > Další informace o použití modely ONNX, najdete v článku [ONNX a Azure Machine Learning](how-to-build-deploy-onnx.md) dokumentu.

## <a id="registermodel"></a> Registrace trénovaného modelu

Model registru je způsob, jak ukládat a uspořádat natrénované modely v cloudu Azure. Modely jsou registrované ve vašem pracovním prostoru služby Azure Machine Learning. Model lze trénuje pomocí Azure Machine Learning nebo jiné služby. Následující kód ukazuje, jak se zaregistrovat modelu ze souboru, nastavte název, značky a popis:

```python
from azureml.core.model import Model

model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

**Časový odhad**: Přibližně 10 sekund.

Příklad registrace modelu, naleznete v tématu [Natrénování obrázku třídění](tutorial-train-models-with-aml.md).

Další informace najdete v tématu v referenční dokumentaci [třída modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a id="configureimage"></a> Vytvoření a registrace bitovou kopii

Nasazených modelů jsou dodávány jako obrázek. Bitová kopie obsahuje závislosti potřebné ke spuštění modelu.

Pro **Azure Container Instance**, **Azure Kubernetes Service**, a **Azure IoT Edge** nasazení, [azureml.core.image.ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) třída se používá k vytvořit konfiguraci bitové kopie. Obrázek konfigurace, pak se k vytvoření nové image Dockeru.

Při vytváření konfigurace image, můžete použít buď __výchozí image__ poskytovaných službou Azure Machine Learning nebo __vlastní image__ , který zadáte.

Následující kód ukazuje, jak vytvořit novou konfiguraci bitové kopie:

```python
from azureml.core.image import ContainerImage

# Image configuration
image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                 runtime = "python",
                                                 conda_file = "myenv.yml"}
                                                 )
```

**Časový odhad**: Přibližně 10 sekund.

Důležité parametry v tomto příkladu jsou popsané v následující tabulce:

| Parametr | Popis |
| ----- | ----- |
| `execution_script` | Určuje skript v jazyce Python, který se používá pro příjem požadavků odeslaných do služby. V tomto příkladu je součástí skriptu `score.py` souboru. Další informace najdete v tématu [provádění skriptu](#script) oddílu. |
| `runtime` | Označuje, že image používá Python. Další možností je `spark-py`, která používá Python s Apache Sparkem. |
| `conda_file` | Umožňuje poskytnout soubor prostředí conda. Tento soubor definuje prostředí conda nasazeného modelu. Další informace o vytvoření tohoto souboru najdete v tématu [vytvořit soubor prostředí (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file). |

Příklad vytvoření konfiguraci služby image, najdete v části [nasazení image třídění](tutorial-deploy-models-with-aml.md).

Další informace najdete v tématu v referenční dokumentaci [ContainerImage třídy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)

### <a id="customimage"></a> Použití vlastní image

Při použití vlastní image, image musí splňovat následující požadavky:

* Ubuntu 16.04 nebo vyšší.
* Conda 4.5. # nebo vyšší.
* Python 3.5. # nebo 3.6. #.

Chcete-li použít vlastní image, nastavte `base_image` vlastnost konfigurace image na adresu bitové kopie. Následující příklad ukazuje, jak použít některou image z obou veřejných a privátních Azure Container Registry:

```python
# use an image available in public Container Registry without authentication
image_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"

# or, use an image available in a private Container Registry
image_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
image_config.base_image_registry.address = "myregistry.azurecr.io"
image_config.base_image_registry.username = "username"
image_config.base_image_registry.password = "password"
```

Další informace o nahrávání imagí do služby Azure Container Registry, najdete v části [nahrání první image do soukromého registru kontejnerů Dockeru](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli).

Pokud váš model se trénuje na Azure Machine Learning Compute, pomocí __verze 1.0.22 nebo větší__ sady Azure Machine Learning SDK, image se vytvoří během cvičení. Následující příklad ukazuje, jak použít tuto bitovou kopii:

```python
# Use an image built during training with SDK 1.0.22 or greater
image_config.base_image = run.properties["AzureML.DerivedImageName"]
```

### <a id="script"></a> Provádění skriptu

Spuštění skriptu přijímá data odeslaná do nasazené bitové kopie a předává je do modelu. Potom trvá odpovědi vrácené modelu a vrátí ji do klienta. **Skript je specifický pro váš model**; data, která očekává, že model a vrátí ji musíte znát. Ukázkový skript, který funguje s modelem klasifikace image, najdete v části [nasazení image třídění](tutorial-deploy-models-with-aml.md).

Skript obsahuje dvě funkce, které načíst a spustit model:

* `init()`: Tuto funkci obvykle načte modelu do globálního objektu. Tato funkce se spustí jenom jednou, a to při spuštění kontejneru Dockeru.

* `run(input_data)`: Tato funkce využívá model k predikci hodnoty založené na vstupní data. K serializaci a rušení serializace, vstupy a výstupy spustit obvykle používají JSON. Můžete také pracovat s Nezpracovaná binární data. Můžete transformovat data, před odesláním do modelu, nebo před vrácením klientovi.

#### <a name="working-with-json-data"></a>Práce s daty JSON

Následující ukázkový skript přijímá a vrací JSON data. `run` Funkce transformuje data z JSON do formátu, očekává, že model a potom transformuje před jeho vrácením odpovědi do formátu JSON:

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

# load the model
def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

# Passes data to the model and returns the prediction
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

#### <a name="working-with-binary-data"></a>Práce s binárními daty

Pokud váš model přijímá __binární data__, použijte `AMLRequest`, `AMLResponse`, a `rawhttp`. Následující ukázkový skript přijímá binárních dat a vrátí obrácený bajtů pro požadavky POST. Pro požadavky GET vrátí úplnou adresu URL v těle odpovědi:

```python
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

# Accept and return binary data
@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    # handle GET requests
    if request.method == 'GET':
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    # handle POST requests
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        respBody = bytearray(reqBody)
        respBody.reverse()
        respBody = bytes(respBody)
        return AMLResponse(respBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `azureml.contrib` Oboru názvů změní často, protože pracujeme na vylepšení služby. V důsledku toho cokoli, co je v tomto oboru názvů by měl být považovány za verzi preview a nejsou plně podporovány společností Microsoft.
>
> Pokud je potřeba otestovat na místním vývojovém prostředí, můžete nainstalovat komponenty `contrib` oboru názvů pomocí následujícího příkazu:
> ```shell
> pip install azureml-contrib-services
> ```

### <a id="createimage"></a> Registrace na obrázku

Po vytvoření bitové kopie konfigurace můžete zaregistrovat bitovou kopii. Tento image je uložen v registru kontejneru pro váš pracovní prostor. Po vytvoření můžete nasadit stejnou bitovou kopii k více službám.

```python
# Register the image from the image configuration
image = ContainerImage.create(name = "myimage",
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**Časový odhad**: Přibližně 3 minuty.

Image se systémovou správou verzí automaticky při registraci více bitových kopií se stejným názvem. Například první obrázek zaregistrovaný jako `myimage` je přiřazena ID `myimage:1`. Při příštím zaregistrujete bitovou kopii jako `myimage`, ID nové image je `myimage:2`.

Další informace najdete v tématu v referenční dokumentaci [ContainerImage třídy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

## <a id="deploy"></a> Nasadit jako webovou službu

Při přechodu na krok nasazení se mírně liší v závislosti na cílové výpočetní prostředí, který nasadíte do procesu. Použijte informace v následujících částech Další informace o nasazení do:

| Cílové výpočetní prostředí | Typ nasazení | Popis |
| ----- | ----- | ----- |
| [Azure Kubernetes Service (AKS)](#aks) | Webové služby (v reálném čase odvození)| Vhodné pro nasazení v produkčním prostředí vysoce škálovatelné. Nabízí automatické škálování a krátké doby odezvy. |
| [Azure ML Compute](#azuremlcompute) | Webové služby (Batch odvození)| Spusťte dávky předpovědi na výpočetní prostředí. Podporuje virtuální počítače s normální a s nízkou prioritou. |
| [Azure Container Instances (ACI)](#aci) | Webové služby (pro vývoj/testování)| Je vhodný pro vývoj a testování. **Není vhodný pro úlohy v produkčním prostředí.** |
| [Azure IoT Edge](#iotedge) | (Preview) Modul IoT | Nasaďte modely na zařízeních IoT. Odvozování se stane v zařízení. |
| [Pole programmable gate array (FPGA)](#fpga) | (Preview) Webová služba | Mimořádně nízkou latenci pro odvozování v reálném čase. |

> [!IMPORTANT]
> Nasazení modelu jako webové služby se momentálně nepodporuje prostředků mezi zdroji (CORS) pro sdílení obsahu.

Příklady v této části používají [deploy_from_image](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-), což vyžaduje registraci modelu a obrazu než přistoupíte k nasazení. Další informace o dalších metodách nasazení naleznete v tématu [nasazení](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) a [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-).

### <a id="aci"></a> Nasazení do služby Azure Container Instances (DEVTEST)

Použití Azure Container Instances pro nasazení modelů jako webové služby, pokud jeden nebo více z následujících podmínek je splněných:

- Potřebujete k rychlému nasazení a ověření modelu. Dokončení nasazení ACI za méně než 5 minut.
- Testování modelu, který je ve vývoji. Kvóty a regionální dostupnosti ACI najdete v tématu [kvóty a dostupnost oblastí pro Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) dokumentu.

Pokud chcete nasadit do služby Azure Container Instances, postupujte následovně:

1. Definujte konfiguraci nasazení. Tato konfigurace závisí na požadavcích modelu. Následující příklad definuje konfiguraci, která používá jedno Procesorové jádro a 1 GB paměti:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. Pokud chcete nasadit image vytvořené v [vytvoření bitové kopie](#createimage) části tohoto dokumentu, použijte následující kód:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **Časový odhad**: Přibližně 5 minut.

Další informace najdete v tématu v referenční dokumentaci [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) a [webová služba](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) třídy.

### <a id="aks"></a> Nasazení do služby Azure Kubernetes Service (produkce)

Nasazení modelu jako produkční vysoce škálovatelnou webovou službu, použijte Azure Kubernetes Service (AKS). Můžete použít existující cluster AKS, nebo vytvořte novou pomocí sady SDK Azure Machine Learning, rozhraní příkazového řádku nebo na webu Azure portal.

Vytvoření clusteru AKS je čas procesu pro váš pracovní prostor. Tento cluster pro více nasazení můžete znovu použít.

> [!IMPORTANT]
> Při odstranění clusteru, pak musíte vytvořit nový cluster, které se budete muset nasadit.

Azure Kubernetes Service poskytuje následující možnosti:

* Automatické škálování
* Protokolování
* Shromažďování dat modelů
* Rychlá doba odezvy pro webové služby
* Ukončení protokolu TLS
* Authentication

#### <a name="autoscaling"></a>Automatické škálování

Automatické škálování můžete řídit pomocí nastavení `autoscale_target_utilization`, `autoscale_min_replicas`, a `autoscale_max_replicas` AKS webové služby. Následující příklad ukazuje, jak povolit automatické škálování:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True,
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

Využití z aktuálního kontejneru replik je založen na rozhodnutí o škálování směrem nahoru nebo dolů. Počet replik, které jsou zpracovává (požadavek) dělený celkový počet aktuální replik je aktuální využití. Pokud toto číslo překročí cílové využití, se vytvoří víc replik. Pokud je nižší, jsou sníženy repliky. Ve výchozím nastavení je cílové využití 70 %.

Rozhodnutí, která chcete přidat repliky se dělají a rychle implementovat (přibližně 1 sekundu). Rozhodnutí, která chcete-li odebrat repliky trvat delší dobu (přibližně 1 minuta). Toto chování uchovává nečinnosti repliky kolem minutu v případě, že dokáže zpracovat dorazí nové žádosti.

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

Další informace o nastavení `autoscale_target_utilization`, `autoscale_max_replicas`, a `autoscale_min_replicas`, najdete v článku [AksWebservice.deploy_configuration](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-) odkaz.

#### <a name="create-a-new-cluster"></a>Vytvoření nového clusteru

Chcete-li vytvořit nový cluster Azure Kubernetes Service, použijte následující kód:

> [!IMPORTANT]
> Vytvoření clusteru AKS je čas procesu pro váš pracovní prostor. Po vytvoření můžete znovu použít tento cluster pro více nasazení. Při odstranění clusteru nebo skupinu prostředků, který jej obsahuje, pak musíte vytvořit nový cluster, které se budete muset nasadit.
> Pro [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), pokud vyberete vlastní hodnoty agent_count a vm_size, je třeba Ujistěte se, že agent_count vynásobené vm_size je větší než nebo roven 12 virtuálních procesorů. Například pokud použijete vm_size "Standard_D3_v2", který má 4 virtuální procesory, pak měli byste vybrat agent_count 3 nebo vyšší.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

**Časový odhad**: Přibližně 20 minut.

#### <a name="use-an-existing-cluster"></a>Použít existující cluster

Pokud už máte AKS cluster ve vašem předplatném Azure, a je verze 1.12. ## a obsahuje alespoň 12 virtuálních procesorů, ve kterém můžete nasadit svou image. Následující kód ukazuje, jak se připojit existující 1.12 AKS. ## clusteru do pracovního prostoru:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

**Časový odhad**: Přibližně 3 minuty.

Další informace o vytvoření clusteru AKS mimo sadu SDK Azure Machine Learning najdete v následujících článcích:

* [Vytvoření clusteru AKS](https://docs.microsoft.com/cli/azure/aks?toc=%2Fen-us%2Fazure%2Faks%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Vytvoření clusteru AKS (portál)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

#### <a name="deploy-the-image"></a>Nasazení bitové kopie

K nasazení bitové kopie vytvořené v [vytvoření bitové kopie](#createimage) část tohoto dokumentu na Azure Kubernetes Server cluster pomocí následujícího kódu:

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
service = Webservice.deploy_from_image(workspace = ws,
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
service.wait_for_deployment(show_output = True)
print(service.state)
```

**Časový odhad**: Přibližně 3 minuty.

Další informace najdete v tématu v referenční dokumentaci [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) a [webová služba](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) třídy.

### <a id="azuremlcompute"></a> Odvození pomocí Azure ML Compute

Azure ML cílových výpočetních prostředí se vytváří a spravují pomocí služby Azure Machine Learning. Se můžete využít k předpovědi batch z Azure ML kanály.

Návod k odvození služby batch pomocí Azure ML Compute, najdete v článku [způsob spouštění Predikcí služby Batch](how-to-run-batch-predictions.md) dokumentu.


### <a id="fpga"></a> Nasazení do pole programmable gate Array (FPGA)

Project Brainwave umožňuje dosáhnout mimořádně nízkou latenci pro požadavky v reálném čase odvozování. Project Brainwave zrychluje neuronových sítí (DNN) nasadit na pole programmable gate Array v cloudu Azure. Nejčastěji používané dopředné jsou k dispozici jako featurizers pro přenos učení nebo přizpůsobitelná váhy Trénink probíhal svoje vlastní data.

Postup nasazení model pomocí aplikace Project Brainwave, najdete v části [nasadit do FPGA](how-to-deploy-fpga-web-service.md) dokumentu.

## <a name="define-schema"></a>Definovat schéma

Vlastní dekoratéry lze použít pro [OpenAPI](https://swagger.io/docs/specification/about/) specifikace generování a vstupu zadejte manipulaci s při nasazení webové služby. V `score.py` soubor, zadejte vzorek vstup nebo výstup v konstruktoru pro jeden z objektů definovaný typ. a ukázka a typ se používají pro automatické vytvoření schématu. Aktuálně jsou podporovány následující typy:

* `pandas`
* `numpy`
* `pyspark`
* Standardní Pythonu

Nejprve zkontrolujte, že potřebné závislosti pro `inference-schema` balíčku jsou součástí vašeho `env.yml` souboru prostředí conda. V tomto příkladu `numpy` parametr typu schématu, takže další pip `[numpy-support]` je rovněž nainstalován.

```python
%%writefile myenv.yml
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn
    - inference-schema[numpy-support]
```

V dalším kroku změnit `score.py` soubor k importu `inference-schema` balíčky. Definování vstupních a výstupních formátů, ukázka v `input_sample` a `output_sample` proměnné, které představují formáty požadavků a odpovědí pro webovou službu. Používají tyto vzorky ve vstupu a výstupu dekoratéry funkce na `run()` funkce.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)


input_sample = np.array([[1.8]])
output_sample = np.array([43638.88])

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

Až projdete normální image registrace a webové služby procesu nasazení se aktualizovaný `score.py` souboru, načtení identifikátoru uri Swagger ze služby. Vyžádání tohoto identifikátoru uri vrátí `swagger.json` souboru.

```python
service.wait_for_deployment(show_output=True)
print(service.swagger_uri)
```



Když vytvoříte novou bitovou kopii, je nutné ručně aktualizovat každou službu, kterou chcete použít novou bitovou kopii. Pokud chcete aktualizovat webovou službu, použijte `update` metody. Následující kód ukazuje, jak aktualizovat webovou službu, která používá novou bitovou kopii:

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

Další informace najdete v tématu v referenční dokumentaci [webová služba](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) třídy.

## <a name="test-web-service-deployments"></a>Nasazení služeb webového testu

Chcete-li otestovat nasazení webové služby, můžete použít `run` metodu objektu webové služby. V následujícím příkladu dokument JSON je nastavena na webové služby a zobrazí výsledek. Data odeslaná musí odpovídat hodnotám modelu očekává. V tomto příkladu formát dat odpovídá vstup očekává modelem cukrovka.

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data = test_sample)
print(prediction)
```

Webové služby je rozhraní REST API, abyste mohli vytvořit klientských aplikací v řadě programovacích jazyků. Další informace najdete v tématu [vytvořit klientskou aplikaci vykreslující](how-to-consume-web-service.md).

## <a id="update"></a> Aktualizovat webovou službu

Když vytvoříte novou bitovou kopii, je nutné ručně aktualizovat každou službu, kterou chcete použít novou bitovou kopii. Pokud chcete aktualizovat webovou službu, použijte `update` metody. Následující kód ukazuje, jak aktualizovat webovou službu, která používá novou bitovou kopii:

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

Další informace najdete v tématu v referenční dokumentaci [webová služba](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) třídy.

## <a id="iotedge"></a> Nasazení do Azure IoT Edge

Zařízení Azure IoT Edge je na Linuxu nebo zařízení se systémem Windows, který spustí modul runtime Azure IoT Edge. Pomocí služby Azure IoT Hub, můžete nasadit modely strojového učení do těchto zařízení jako moduly IoT Edge. Nasazení modelu do zařízení IoT Edge umožňuje zařízení využívat model přímo, namísto toho, aby k zasílání dat do cloudu pro zpracování. Získáte rychlejší reakční dobu a menší datové přenosy.

Moduly Azure IoT Edge se nasadí do zařízení z registru kontejneru. Po vytvoření image z modelu je uložen v registru kontejneru pro váš pracovní prostor.

> [!IMPORTANT]
> Informace v této části se předpokládá, že jste už obeznámení s nainstalovanými moduly Azure IoT Hub a Azure IoT Edge. Zatímco některé informace v této části je specifické pro službu Azure Machine Learning, se stane, většinou procesu nasazení do hraničního zařízení ve službě Azure IoT.
>
> Pokud nejste obeznámeni s Azure IoT, přečtěte si téma [základy Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/) a [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) základní informace. Další informace o konkrétní operace použije další odkazy v této části.

### <a name="set-up-your-environment"></a>Nastavení prostředí

* Vývojové prostředí. Další informace najdete v tématu [jak nakonfigurovat prostředí pro vývoj](how-to-configure-environment.md) dokumentu.

* [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.

* Trénovaného modelu. Příklad toho, jak pro trénování modelu, najdete v článku [trénování modelu klasifikace obrázků s Azure Machine Learning](tutorial-train-models-with-aml.md) dokumentu. Předem natrénovaných modelů je k dispozici na [AI Toolkit pro Azure IoT Edge na Githubu úložiště](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

### <a id="getcontainer"></a> Získání přihlašovacích údajů registru kontejneru

Azure IoT pro nasazení modulu IoT Edge do zařízení, potřebuje přihlašovací údaje pro registr kontejneru, který ukládá Image dockeru ve službě Azure Machine Learning.

Přihlašovací údaje můžete získat dvěma způsoby:

+ **Na webu Azure Portal**:

  1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/signin/index).

  1. Přejděte do pracovního prostoru služby Azure Machine Learning a vyberte __přehled__. Chcete-li přejít do nastavení registru kontejneru, vyberte __registru__ odkaz.

     ![Obrázek položky registru kontejneru](./media/how-to-deploy-and-where/findregisteredcontainer.png)

  1. Jednou v registru kontejneru, vyberte **přístupové klíče** a pak povolte uživatele s rolí správce.

     ![Snímek obrazovky klíčů přístup](./media/how-to-deploy-and-where/findaccesskey.png)

  1. Uložte příslušné hodnoty pro **přihlašovací server**, **uživatelské jméno**, a **heslo**.

+ **Pomocí skriptu v jazyce Python**:

  1. Pomocí následujícího skriptu Pythonu za kód, který jste spustili výše vytvořte kontejner:

     ```python
     # Getting your container details
     container_reg = ws.get_details()["containerRegistry"]
     reg_name=container_reg.split("/")[-1]
     container_url = "\"" + image.image_location + "\","
     subscription_id = ws.subscription_id
     from azure.mgmt.containerregistry import ContainerRegistryManagementClient
     from azure.mgmt import containerregistry
     client = ContainerRegistryManagementClient(ws._auth,subscription_id)
     result= client.registries.list_credentials(resource_group_name, reg_name, custom_headers=None, raw=False)
     username = result.username
     password = result.passwords[0].value
     print('ContainerURL{}'.format(image.image_location))
     print('Servername: {}'.format(reg_name))
     print('Username: {}'.format(username))
     print('Password: {}'.format(password))
     ```
  1. Uložte hodnoty ContainerURL, servername, uživatelské jméno a heslo.

     Tyto přihlašovací údaje jsou nezbytné k zajištění na hraničních zařízeních IoT zařízení přístup k imagím v váš privátní registr kontejnerů.

### <a name="prepare-the-iot-device"></a>Připravte zařízení IoT

Registraci zařízení ve službě Azure IoT Hub a pak nainstalujte modul runtime IoT Edge na zařízení. Pokud nejste obeznámeni s tímto procesem, přečtěte si téma [rychlý start: Nasazení prvního modulu IoT Edge do zařízení Linux x64](../../iot-edge/quickstart-linux.md).

Další metody registrace zařízení jsou následující:

* [Azure Portal](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)
* [Azure CLI](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-cli)
* [Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-vscode)

### <a name="deploy-the-model-to-the-device"></a>Model nasadit do zařízení

Pokud chcete model nasadit do zařízení, použijte informace registru během dodavatelé technologií sešli v [získání přihlašovacích údajů registru kontejneru](#getcontainer) části modulu pro nasazení pomocí kroků pro moduly IoT Edge. Například když [moduly nasazení Azure IoT Edge z portálu Azure portal](../../iot-edge/how-to-deploy-modules-portal.md), musíte nakonfigurovat __nastavení registru__ pro zařízení. Použití __přihlašovací server__, __uživatelské jméno__, a __heslo__ pro svůj registr kontejneru pracovní prostor.

Můžete také nasadit pomocí [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-cli) a [Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-vscode).

## <a name="clean-up"></a>Vyčištění

Chcete-li odstranit nasazenou webovou službu, použijte `service.delete()`.

Chcete-li odstranit bitovou kopii, použijte `image.delete()`.

Chcete-li odstranit registrovaný model, použijte `model.delete()`.

Další informace najdete v tématu v referenční dokumentaci [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), [Image.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#delete--), a [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Další postup

* [Řešení potíží s nasazení](how-to-troubleshoot-deployment.md)
* [Zabezpečení webových služeb Azure Machine Learning s protokolem SSL](how-to-secure-web-service.md)
* [Používání modelu ML nasadit jako webovou službu](how-to-consume-web-service.md)
* [Jak spustit predikcí služby batch](how-to-run-batch-predictions.md)
* [Monitorování vašich modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat modelů v produkčním prostředí](how-to-enable-data-collection.md)
* [Sady SDK služby Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Použití služby Azure Machine Learning s Azure Virtual Network](how-to-enable-virtual-network.md)
* [Osvědčené postupy pro vytváření doporučovacích systémů](https://github.com/Microsoft/Recommenders)
* [Sestavení v reálném čase doporučení rozhraní API v Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)

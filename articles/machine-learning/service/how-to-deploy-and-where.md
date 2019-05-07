---
title: Jak a kde nasadit modely
titleSuffix: Azure Machine Learning service
description: 'Zjistěte, jak a kde k nasazení vašich modelů služby Azure Machine Learning, včetně: Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge a Field-programmable gate Array.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 113c894122fe2b0673dfb47f8a9c0cbecf4c6290
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205066"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Nasazujte modely pomocí služby Azure Machine Learning

Zjistěte, jak nasadit váš model strojového učení jako webovou službu v cloudu Azure nebo do zařízení IoT Edge. Informace v tomto dokumentu se naučíte, jak nasadit do následující cílových výpočetních prostředí:

| Cílové výpočetní prostředí | Typ nasazení | Popis |
| ----- | ----- | ----- |
| [Místní webové služby](#local) | Test/debug | Vhodné pro testování a řešení potíží omezené.
| [Azure Kubernetes Service (AKS)](#aks) | Odvození v reálném čase | Vhodné pro nasazení v produkčním prostředí vysoce škálovatelné. Nabízí automatické škálování a krátké doby odezvy. |
| [Azure Container Instances (ACI)](#aci) | Testování | Vhodné pro nízkého škálování, úlohy založené na procesoru. |
| [Azure Machine Learning Compute](how-to-run-batch-predictions.md) | (Preview) Odvození služby batch | Spusťte vyhodnocení na výpočetní prostředí služby batch. Podporuje virtuální počítače s normální a s nízkou prioritou. |
| [Azure IoT Edge](#iotedge) | (Preview) Modul IoT | Nasazení a poskytovat modelů ML na zařízeních IoT. |

## <a name="deployment-workflow"></a>Pracovní postup nasazení

Proces nasazení modelu se podobá všech cílových výpočetních prostředí:

1. Zaregistrujte modely.
1. Modely nasazení.
1. Test nasadit modely.

Další informace o konceptech pracovního postupu nasazení, najdete v části [spravovat, nasazovat a monitorovat modely pomocí služby Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites-for-deployment"></a>Požadavky na nasazení

- Model. Pokud nemáte trénovaného modelu, můžete použít model a soubory závislostí podle [v tomto kurzu](http://aka.ms/azml-deploy-cloud).

- [Rozšíření Azure CLI pro službu Machine Learning](reference-azure-machine-learning-cli.md), nebo [Azure Machine Learning Python SDK](https://aka.ms/aml-sdk).

## <a id="registermodel"></a> Zaregistrujte model strojového učení

Model registru je způsob, jak ukládat a uspořádat natrénované modely v cloudu Azure. Modely jsou registrované ve vašem pracovním prostoru služby Azure Machine Learning. Model lze trénuje pomocí Azure Machine Learning nebo importovat z modelů trénovaných jinde. Následující příklady ukazují, jak zaregistrovat modelu ze souboru:

### <a name="register-a-model-from-an-experiment-run"></a>Zaregistrujte model z spuštění experimentu

**Příklad Scikit-informace pomocí rozhraní příkazového řádku**
```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
```
**Pomocí sady SDK**
```python
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

### <a name="register-an-externally-created-model"></a>Registrovat externě vytvořené model
Můžete zaregistrovat externě vytvořené model poskytnutím **místní cesta** do modelu. Můžete zadat složku nebo jeden soubor.

**Příklad ONNX pomocí sady Python SDK:**
```python
onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
!tar xvzf mnist.tar.gz

model = Model.register(workspace = ws,
                       model_path ="mnist/model.onnx",
                       model_name = "onnx_mnist",
                       tags = {"onnx": "demo"},
                       description = "MNIST image classification CNN from ONNX Model Zoo",)
```

**Pomocí rozhraní příkazového řádku**
```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

**Časový odhad**: Přibližně 10 sekund.

Další informace najdete v tématu v referenční dokumentaci [třída modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="how-to-deploy"></a>Postup nasazení

Pokud chcete nasadit jako webovou službu, musíte vytvořit konfiguraci služby odvození (`InferenceConfig`) a konfigurace nasazení. V konfiguraci odvození určete skripty a závislosti potřebné pro obsluhu modelu. V konfiguraci nasazení zadat podrobnosti o tom, jak poskytovat modelu na cílové výpočetní prostředí.


### <a id="script"></a> 1. Definování závislosti & skript vstupního

Skript vstupního přijímá data odesílaná nasazenou webovou službu a předává je do modelu. Potom trvá odpovědi vrácené modelu a vrátí ji do klienta. **Skript je specifický pro váš model**; data, která očekává, že model a vrátí ji musíte znát.

Skript obsahuje dvě funkce, které načíst a spustit model:

* `init()`: Tuto funkci obvykle načte modelu do globálního objektu. Tato funkce se spustí pouze jednou při spuštění kontejneru Dockeru pro webovou službu.

* `run(input_data)`: Tato funkce využívá model k predikci hodnoty založené na vstupní data. K serializaci a rušení serializace, vstupy a výstupy spustit obvykle používají JSON. Můžete také pracovat s Nezpracovaná binární data. Můžete transformovat data, před odesláním do modelu, nebo před vrácením klientovi.

#### <a name="optional-automatic-swagger-schema-generation"></a>(Volitelné) Automatické generování schématu Swaggeru

Automaticky generovat schéma pro webovou službu, zadejte vzorek vstup nebo výstup v konstruktoru pro jeden z objektů definovaný typ. a typ a ukázka umožňují automaticky vytvořit schéma. Potom vytvoří služba Azure Machine Learning [OpenAPI](https://swagger.io/docs/specification/about/) specifikace (Swagger) pro webovou službu během nasazení.

Aktuálně jsou podporovány následující typy:

* `pandas`
* `numpy`
* `pyspark`
* Standardní objekt Pythonu

Použití generování schématu, uvést `inference-schema` balíčku v souboru prostředí conda. Následující příklad používá `[numpy-support]` vzhledem k tomu, že skript vstupního používá numpy typ parametru: 

#### <a name="example-dependencies-file"></a>Příklad souboru závislosti
Následující je příkladem souboru závislostí systému Conda pro odvození.
```python
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn
    - inference-schema[numpy-support]
```

Pokud chcete použít schématu automatického generování, váš skript vstupního **musí** importovat `inference-schema` balíčky. 

Definování vstupních a výstupních formátů, ukázka v `input_sample` a `output_sample` proměnné, které představují formáty požadavků a odpovědí pro webovou službu. Používají tyto vzorky ve vstupu a výstupu dekoratéry funkce na `run()` funkce. Scikit-informace níže uvedený příklad používá generování schématu.

> [!TIP]
> Po nasazení služby, použijte `swagger_uri` vlastnost pro načtení schématu dokumentu JSON.

#### <a name="example-entry-script"></a>Ukázkový skript položka

Následující příklad ukazuje, jak přijímají a vrací JSON data:

**Příklad Scikit informace s funkcí výstupu Swagger:**
```python
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType

def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = np.array([[10,9,8,7,6,5,4,3,2,1]])
output_sample = np.array([3726.995])

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Další příklady skriptů najdete v následujících příkladech:

* Pytorch: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* Vyhodnocení proti binárních dat: [Jak využívat webovou službu](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2. Definovat vaše InferenceConfig

Odvozování configuration popisuje postup konfigurace modelu k následné predikci. Následující příklad ukazuje, jak vytvořit konfiguraci odvození:

```python
inference_config = InferenceConfig(source_directory="C:/abc",
                                   runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

V tomto příkladu konfigurace obsahuje následující položky:

* Adresář, který obsahuje prostředky potřebné k odvození
* Vyžaduje tento model Python
* [Skript vstupního](#script), který se používá pro zpracování webových požadavků odeslaných v nasazené službě
* Soubor conda, který popisuje balíčky Pythonu potřebné ke spuštění odvozování

Informace o funkcích InferenceConfig najdete v tématu [pokročilou konfiguraci](#advanced-config) oddílu.

### <a name="3-define-your-deployment-configuration"></a>3. Definovat konfiguraci nasazení

Před nasazením, je nutné definovat konfiguraci nasazení. Konfigurace nasazení je specifické pro cílové výpočetní prostředí, který bude hostitelem webové služby. Například při nasazování místně musíte zadat port, kde služba přijímá požadavky.

Také budete muset vytvořit výpočetních prostředků. Například pokud jste ještě není na Azure Kubernetes Service propojené s pracovního prostoru.

Následující tabulka obsahuje příklad vytvoření konfigurace nasazení pro každé cílové výpočetní prostředí:

| Cílové výpočetní prostředí | Příklad konfigurace nasazení |
| ----- | ----- |
| Místní | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Instance kontejneru Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Následující části ukazují, jak vytvořit konfiguraci nasazení a použít ji k nasazení webové služby.

## <a name="where-to-deploy"></a>Pokud chcete nasadit

### <a id="local"></a> Místní nasazení

Příklady v této části používají [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-), což vyžaduje registraci modelu a obrazu než přistoupíte k nasazení. Další informace o dalších metodách nasazení naleznete v tématu [nasazení](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) a [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-).

**Pokud chcete nasadit místně, musíte mít v místním počítači nainstalovaný Docker.**

**Pomocí sady SDK**

```python
deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

**Pomocí rozhraní příkazového řádku**

```azurecli-interactive
az ml model deploy -m sklearn_mnist:1 -ic inferenceconfig.json -dc deploymentconfig.json
```

### <a id="aci"></a> Nasazení do služby Azure Container Instances (DEVTEST)

Použití Azure Container Instances pro nasazení modelů jako webové služby, pokud jeden nebo více z následujících podmínek je splněných:
- Potřebujete k rychlému nasazení a ověření modelu.
- Testování modelu, který je ve vývoji. 

Kvóty a regionální dostupnosti ACI najdete v tématu [kvóty a dostupnost oblastí pro Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) článku.

**Pomocí sady SDK**

```python
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

**Pomocí rozhraní příkazového řádku**

```azurecli-interactive
az ml model deploy -m sklearn_mnist:1 -n aciservice -ic inferenceconfig.json -dc deploymentconfig.json
```

Další informace najdete v tématu v referenční dokumentaci [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) a [webová služba](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) třídy.

### <a id="aks"></a> Nasazení do služby Azure Kubernetes Service (produkce)

Můžete použít existující cluster AKS, nebo vytvořte novou pomocí sady SDK Azure Machine Learning, rozhraní příkazového řádku nebo na webu Azure portal.


> [!IMPORTANT]
> Vytvoření clusteru AKS je čas procesu pro váš pracovní prostor. Tento cluster pro více nasazení můžete znovu použít.
> Pokud nebyl vytvořen nebo připojené AKS clusteru go <a href="#create-attach-aks">tady</a>.

#### Nasazení do AKS <a id="deploy-aks"></a>

Můžete nasadit do AKS pomocí Azure ML CLI:
```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n aksservice -ic inferenceconfig.json -dc deploymentconfig.json
```

Můžete také použít sadu Python SDK:
```python
aks_target = AksCompute(ws,"myaks")
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Další informace o konfiguraci vašeho nasazení služby AKS, včetně automatického škálování, najdete v článku [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) odkaz.

**Časový odhad:** Přibližně 5 minut.

#### Vytvořit nebo připojit AKS cluster <a id="create-attach-aks"></a>
Vytvoření nebo připojit AKS cluster je **jeden proces čas** pro váš pracovní prostor. Po clusteru byla přidružena pracovního prostoru, můžete ho použít pro více nasazení. 

Při odstranění clusteru nebo skupinu prostředků, který jej obsahuje, musíte vytvořit nový cluster, které se budete muset nasadit.

##### <a name="create-a-new-aks-cluster"></a>Vytvoření nového clusteru AKS
Další informace o nastavení `autoscale_target_utilization`, `autoscale_max_replicas`, a `autoscale_min_replicas`, najdete v článku [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-) odkaz.
Následující příklad ukazuje, jak vytvořit nový cluster Azure Kubernetes Service:

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Další informace o vytvoření clusteru AKS mimo sadu SDK Azure Machine Learning najdete v následujících článcích:
* [Vytvoření clusteru AKS](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Vytvoření clusteru AKS (portál)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)


> [!IMPORTANT]
> Pro [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), pokud vyberete vlastní hodnoty agent_count a vm_size, je třeba Ujistěte se, že agent_count vynásobené vm_size je větší než nebo roven 12 virtuálních procesorů. Například pokud použijete vm_size "Standard_D3_v2", který má 4 virtuální procesory, pak měli byste vybrat agent_count 3 nebo vyšší.

**Časový odhad**: Přibližně 20 minut.

##### <a name="attach-an-existing-aks-cluster"></a>Připojení existujícího clusteru AKS

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
```

## <a name="consume-web-services"></a>Využívání webových služeb
Každý nasazenou webovou službu poskytuje rozhraní REST API, takže klientské aplikace můžete vytvořit v řadě programovacích jazyků. Pokud jste povolili ověřování pro vaši službu, budete muset zadat klíč služby jako token v záhlaví požadavku.

Tady je příklad toho, jak volat služby v Pythonu:
```python
import requests
import json

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Další informace najdete v tématu [vytvořit klientskou aplikaci vykreslující](how-to-consume-web-service.md).

## <a id="update"></a> Aktualizovat webovou službu

Když vytvoříte nový model, je nutné ručně aktualizovat každou službu, kterou chcete použít nový model. Pokud chcete aktualizovat webovou službu, použijte `update` metody. Následující kód ukazuje, jak aktualizovat webovou službu, která používá nový model:

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# Update to new model(s)
service.update(models = [new_model])
print(service.state)
print(service.get_logs())
```

## <a name="clean-up"></a>Vyčištění
Chcete-li odstranit nasazenou webovou službu, použijte `service.delete()`.
Chcete-li odstranit registrovaný model, použijte `model.delete()`.

Další informace najdete v tématu v referenční dokumentaci [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), a [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## Upřesňující nastavení konfigurace <a id="advanced-config"></a>

### <a id="customimage"></a> Použití vlastní základní image

Interně InferenceConfig vytvoří image Dockeru, který obsahuje model a jiné prostředky vyžadované služby. Pokud není zadán, použije se výchozí základní image.

Při vytváření obrázek, který použijete s vaší konfigurací odvození, obrázek musí splňovat následující požadavky:

* Ubuntu 16.04 nebo vyšší.
* Conda 4.5. # nebo vyšší.
* Python 3.5. # nebo 3.6. #.

Chcete-li použít vlastní image, nastavte `base_image` vlastnost odvození konfigurace na adresu bitové kopie. Následující příklad ukazuje, jak použít některou image z obou veřejných a privátních Azure Container Registry:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"

# or, use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

Na následujícím obrázku identifikátory URI jsou určené pro bitových kopií poskytnutých společností Microsoft a můžou používat bez zadání uživatelského jména a hesla hodnoty:

* `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03`

Chcete-li používat tyto Image, nastavte `base_image` na identifikátor URI ze seznamu výše. Nastavte `base_image_registry.address` k `mcr.microsoft.com`.

> [!IMPORTANT]
> Microsoft obrazy, které používají CUDA nebo TensorRT musí použít pouze na služby Microsoft Azure.

Další informace o nahrání vlastních imagí do služby Azure Container Registry, najdete v části [nahrání první image do soukromého registru kontejnerů Dockeru](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli).

Pokud váš model se trénuje na Azure Machine Learning Compute, pomocí __verze 1.0.22 nebo větší__ sady Azure Machine Learning SDK, image se vytvoří během cvičení. Následující příklad ukazuje, jak použít tuto bitovou kopii:

```python
# Use an image built during training with SDK 1.0.22 or greater
image_config.base_image = run.properties["AzureML.DerivedImageName"]
```

## <a name="other-inference-options"></a>Další možnosti odvození

### <a id="azuremlcompute"></a> Odvození služby batch
Azure Machine Learning Compute cíle vytvoření a správa pomocí služby Azure Machine Learning. Použitím pro předpovědi batch z Azure Machine Learning kanály.

Návod k odvození služby batch pomocí Azure Machine Learning Compute, najdete v článku [způsob spouštění Predikcí služby Batch](how-to-run-batch-predictions.md) článku.

## <a id="iotedge"></a> Odvození na hraničních zařízeních IoT
Podpora pro nasazení do hraničních zařízení je ve verzi preview. Další informace najdete v tématu [nasadit aplikaci Azure Machine Learning jako modulu IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-deploy-machine-learning) článku.

## <a name="next-steps"></a>Další postup
* [Řešení potíží s nasazení](how-to-troubleshoot-deployment.md)
* [Zabezpečení webových služeb Azure Machine Learning s protokolem SSL](how-to-secure-web-service.md)
* [Používání modelu ML nasadit jako webovou službu](how-to-consume-web-service.md)
* [Monitorování vašich modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat modelů v produkčním prostředí](how-to-enable-data-collection.md)

---
title: Nasazení modelu pro odvozování s GPU
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak obsáhlý learning se model nasadíme jako webovou službu, která používá GPU pro odvozování. V tomto článku Tensorflow modelu se nasadí do clusteru Azure Kubernetes Service. Cluster používá virtuální počítač s podporou grafického procesoru hostitele webové služby a odvozování skóre žádosti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 7796e8dc07889c9816e4227f3b38904d91a24da3
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595676"
---
# <a name="deploy-a-deep-learning-model-for-inferencing-with-gpu"></a>Nasazení modelu obsáhlého learningu pro odvozování s GPU

Další informace o použití odvozování GPU pro machine learning model nasadit jako webovou službu. V tomto článku se dozvíte, jak pomocí služby Azure Machine Learning nasadit příklad Tensorflow hloubkového učení modelu. Model se nasadí do clusteru Azure Kubernetes Service (AKS), který se používá k hostování služby virtuálního počítače s podporou grafického procesoru. Při žádosti se odesílají do služby, modelu využívá GPU provádět odvozování.

Grafické procesory nabízí výhody výkonu přes procesory na vysoce paralelizovat výpočtu. Trénování a odvozování hloubkového učení modely (zejména u velkých dávky požadavků) představují vynikající využití pro GPU.  

V tomto příkladu se ukazují, jak nasadit TensorFlow uložit model Azure Machine Learning pomocí:
* Vytvoření clusteru s podporou grafického procesoru AKS
* Nasazení modelu s Tensorflow GPU

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning
* Python
* Tensorflow SavedModel zaregistrovaný. Další informace o registraci najdete v článku modely [modely nasazení](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#registermodel)

Tento článek je založen na Poznámkový blok Jupyter [modely Tensorflow nasazení do AKS](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb), TensorFlow, uložit, který používá modely a nasadí do clusteru AKS. Pomocí malé změny souboru bodování a soubor prostředí je pro jakoukoli architekturu learningu počítače, které podporují GPU.  

## <a name="provision-aks-cluster-with-gpus"></a>Zřízení clusteru AKS s grafickými procesory
Azure nabízí mnoho různých možností GPU, které lze použít k odvozování. Zobrazit [seznam řady N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) pro úplné rozpis funkcí a náklady. 

Další informace o používání AKS pomocí služby Azure Machine Learning, najdete v článku [jak nasadit a kde článku.](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#create-a-new-cluster)

```python
# Provision AKS cluster with GPU machine
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

# Create the cluster
aks_target = ComputeTarget.create(
    workspace=ws, name=aks_name, provisioning_configuration=prov_config
)

aks_target.wait_for_deployment()
```

> [!IMPORTANT]
> Azure vám bude fakturovat, tak dlouho, dokud je zřízení clusteru AKS. Ujistěte se, že odstranění clusteru AKS po dokončení jeho použití.


## <a name="write-entry-script"></a>Zapsat záznam skriptu

Uložte následující do pracovního adresáře jako `score.py`. Tento soubor se použije ke stanovení skóre imagí odesílané do služby. Tento soubor načte TensorFlow uložit model, a pak na každém příspěvku požadavek předá vstupního obrázku do relace TensorFlow a vrátí výsledné skóre.
Další architektury odvozování bude vyžadovat různé soubory vyhodnocení.

```python
import tensorflow as tf
import numpy as np
import ujson
from azureml.core.model import Model
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    global session
    global input_name
    global output_name
    
    session = tf.Session()

    model_path = Model.get_model_path('resnet50')
    model = tf.saved_model.loader.load(session, ['serve'], model_path)
    if len(model.signature_def['serving_default'].inputs) > 1:
        raise ValueError("This score.py only supports one input")
    input_name = [tensor.name for tensor in model.signature_def['serving_default'].inputs.values()][0]
    output_name = [tensor.name for tensor in model.signature_def['serving_default'].outputs.values()]
    

@rawhttp
def run(request):
    if request.method == 'POST':
        reqBody = request.get_data(False)
        resp = score(reqBody)
        return AMLResponse(resp, 200)
    if request.method == 'GET':
        respBody = str.encode("GET is not supported")
        return AMLResponse(respBody, 405)
    return AMLResponse("bad request", 500)

def score(data):
    result = session.run(output_name, {input_name: [data]})
    return ujson.dumps(result[1])

if __name__ == "__main__":
    init()
    with open("lynx.jpg", 'rb') as f: #load file for testing locally
        content = f.read()
        print(score(content))

```

## <a name="define-conda-environment"></a>Definujte prostředí Conda
Vytvořte soubor prostředí conda `myenv.yml` postup určení závislostí pro vaši službu. Je důležité určit, že používáte `tensorflow-gpu` k dosažení vyšší výkon.
```yaml
name: aml-accel-perf
channels:
  - defaults
dependencies:
  - tensorflow-gpu = 1.12
  - numpy
  - ujson
  - pip:
    - azureml-core
    - azureml-contrib-services
```

## <a name="define-gpu-inferenceconfig"></a>Definování GPU InferenceConfig

Vytvoření [ `InferenceConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) která určuje, že povolíte GPU. Tím se zajistí nainstalované CUDA pomocí bitové kopie.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='gpu-rn'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"resnet50")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   gpu_enabled=True)
```

Další informace najdete v tématu [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) a [AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py).
## <a name="deploy-the-model"></a>Nasazení modelu

Nasazení modelu do clusteru AKS a počkejte na vytvoření vaší služby.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Služba Azure Machine Learning model se nenasadí `InferenceConfig` , který očekává, že GPU do clusteru bez GPU.

Další informace najdete v tématu [modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-sample-query-to-deployed-model"></a>Problém ukázkový dotaz k nasazení modelu

Ukázkový dotaz vydejte nasazeného modelu. Tento model se skóre libovolné obrázku jpeg, který odeslat jako požadavek post. 

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
```

> [!IMPORTANT]
> Pokud chcete optimalizovat latenci a propustnost, musí být váš klient ve stejné oblasti Azure jako koncový bod.  Rozhraní API jsou aktuálně vytvořené v oblasti Azure USA – východ.

## <a name="cleaning-up-the-resources"></a>Vymazání prostředků

Odstraňte prostředky, jakmile budete hotovi s ukázkovou verzi.

> [!IMPORTANT]
> Azure bude fakturovat podle toho, jak dlouho se nasadí AKS cluster. Ujistěte se, že k vyčištění, jakmile budete hotovi s ním.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Další postup

* [Nasazení modelu na FPGA](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service)
* [Nasadit model s využitím ONNX](https://docs.microsoft.com/azure/machine-learning/service/how-to-build-deploy-onnx#deploy)
* [Trénování modelů Tensorflow DNN](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow)

---
title: Nasazení modelu pro odvození s GPU
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak obsáhlý learning se model nasadíme jako webovou službu, která používá GPU pro odvození. V tomto článku Tensorflow modelu se nasadí do clusteru Azure Kubernetes Service. Cluster používá virtuální počítač s podporou grafického procesoru hostitele webové služby a odvození skóre žádosti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: ec71165553a1d65ff133d605bf94255100f74e6e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388923"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Nasazení modelu obsáhlého learningu pro odvození s GPU

Další informace o použití odvození GPU pro machine learning model nasadit jako webovou službu. Odvození nebo vyhodnocení modelu je fáze použití nasazený model pro predikci, obvykle na produkční data.

V tomto článku se naučíte, jak pomocí služby Azure Machine Learning k nasazení příklad Tensorflow hloubkového učení modelu do clusteru Azure Kubernetes Service (AKS) ve virtuálním počítači s podporou grafického procesoru (VM). Při žádosti se odesílají do služby, model používá GPU pro spouštění úloh odvození.

Grafické procesory nabízí výhody výkonu přes procesory na vysoce paralelizovat výpočtu. Vynikající použití pro virtuální počítače s podporou grafického procesoru patří obsáhlý learning se model trénování a odvozování, zejména u velkých dávky požadavků.

Tento příklad ukazuje, jak nasadit TensorFlow uložit model Azure Machine Learning. Můžete provést následující kroky:

* Vytvoření clusteru s podporou grafického procesoru AKS
* Nasazení modelu Tensorflow GPU

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning services
* A Python distro
* Registrovaný Tensorflow uložit model. Informace o postupu registrace modely, najdete v článku [nasadit modely](../service/how-to-deploy-and-where.md#registermodel).

Tento článek je založen na Poznámkový blok Jupyter [modely Tensorflow nasazení do AKS](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb). Poznámkový blok Jupyter používá TensorFlow uloží modely a nasadí do clusteru AKS. Můžete také použít Poznámkový blok se žádné strojovým učením architektura, která podporuje GPU tím, že malé změny do souboru bodování a souborem prostředí.  

## <a name="provision-an-aks-cluster-with-gpus"></a>Zřízení clusteru AKS s grafickými procesory

Azure nabízí mnoho různých možností GPU. Můžete použít některý z nich pro odvozování. Zobrazit [seznam virtuálních počítačů řady N-series](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) pro úplné rozpis funkcí a náklady.

Další informace o používání AKS pomocí služby Azure Machine Learning najdete v tématu [nasazení a kde](../service/how-to-deploy-and-where.md#deploy-aks).

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
> Azure vám bude fakturovat, tak dlouho, dokud je zřízení clusteru AKS. Nezapomeňte odstranit AKS cluster, až budete hotovi s ním.

## <a name="write-the-entry-script"></a>Zapsat záznam skriptu

Uložte následující kód do pracovního adresáře jako `score.py`. Tento soubor stanoví skóre bitové kopie, jak jste odeslali do služby. Načte TensorFlow uložit model, předává vstupního obrázku do relace TensorFlow na každý požadavek POST a vrátí výsledný skóre. Další architektury odvozování vyžadují různé soubory vyhodnocení.

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

## <a name="define-the-conda-environment"></a>Definujte prostředí conda

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

## <a name="define-the-gpu-inferenceconfig-class"></a>Definice třídy GPU InferenceConfig

Vytvoření `InferenceConfig` objekt, který umožňuje GPU a zajistí, že se spouští CUDA instaluje s image Dockeru.

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

Další informace naleznete v tématu:

- [Třída InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [Třída AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>Nasazení modelu

Nasazení modelu do clusteru AKS a počkejte na vytvoření vaší služby.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Služba Azure Machine Learning model se nenasadí `InferenceConfig` objekt, který očekává, že GPU, aby mohl cluster, který nemá grafického procesoru.

Další informace najdete v tématu [třída modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-deployed-model"></a>Ukázkový dotaz vydat nasazeného modelu

Poslat testovací dotaz do nasazeného modelu. Při odesílání obrázků jpeg modelu stanoví skóre bitovou kopii.

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
```

> [!IMPORTANT]
> Minimalizovat latenci a optimalizovat propustnost, ujistěte se, že je váš klient ve stejné oblasti Azure jako koncový bod. V tomto příkladu se vytvoří rozhraní API v oblasti Azure USA – východ.

## <a name="clean-up-the-resources"></a>Vyčištění prostředků

Až to budete mít v tomto příkladu, odstranění vašich prostředků.

> [!IMPORTANT]
> Azure účtuje poplatky za vás v závislosti na jak dlouho se nasadí AKS cluster. Ujistěte se, že k vyčištění, jakmile budete hotovi s ním.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Další postup

* [Nasazení modelu na FPGA](../service/how-to-deploy-fpga-web-service.md)
* [Nasadit model s využitím ONNX](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Trénování modelů Tensorflow DNN](../service/how-to-train-tensorflow.md)

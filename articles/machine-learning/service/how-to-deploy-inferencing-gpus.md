---
title: Nasazení modelu pro odvození s GPU
titleSuffix: Azure Machine Learning service
description: Tento článek vás naučí, jak pomocí služby Azure Machine Learning nasadit s podporou grafického procesoru Tensorflow hloubkového učení modelu jako webové service.service a skóre odvození požadavky.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: 8086d059913cc61bff0bca31681368bea6d76777
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543803"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Nasazení modelu obsáhlého learningu pro odvození s GPU

V tomto článku se naučíte, jak pomocí služby Azure Machine Learning k nasazení hloubkové učení modelu jako webové služby s podporou grafického procesoru Tensorflow.

Model nasaďte do clusteru Azure Kubernetes Service (AKS) provádět odvozování s podporou grafického procesoru. Odvozování nebo vyhodnocení modelu je fáze, kde se používá nasazený model pro predikci. Použití grafických procesorů místo výhody výkonu nabídka procesory na vysoce paralelizovat výpočtu.

I když tato ukázka používá TensorFlow model, můžete provést následující kroky u jakékoli strojového učení architektura, která podporuje GPU tím, že malé změny do souboru bodování a souborem prostředí. 

V tomto článku proveďte následující kroky:

* Vytvoření clusteru s podporou grafického procesoru AKS
* Nasazení modelu Tensorflow GPU
* Ukázkový dotaz vydat nasazeného modelu

## <a name="prerequisites"></a>Požadavky

* Pracovnímu prostoru Azure Machine Learning services.
* A Python distro.
* Registrovaný Tensorflow uložit model.
    * Informace o postupu registrace modely, najdete v článku [nasadit modely](../service/how-to-deploy-and-where.md#registermodel).

Dokončení první části této série postupů, [trénování TensorFlow modelu](how-to-train-tensorflow.md), ke splnění nezbytných požadavků.

## <a name="provision-an-aks-cluster-with-gpus"></a>Zřízení clusteru AKS s grafickými procesory

Azure nabízí mnoho různých možností GPU. Můžete použít některý z nich pro odvozování. Zobrazit [seznam virtuálních počítačů řady N-series](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) pro úplné rozpis funkcí a náklady.

Další informace o používání AKS pomocí služby Azure Machine Learning najdete v tématu [nasazení a kde](../service/how-to-deploy-and-where.md#deploy-aks).

```Python
# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    compute_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Azure vám bude fakturovat, tak dlouho, dokud je zřízení clusteru AKS. Nezapomeňte odstranit AKS cluster, až budete hotovi s ním.

## <a name="write-the-entry-script"></a>Zapsat záznam skriptu

Uložte následující kód do pracovního adresáře jako `score.py`. Tento soubor stanoví skóre bitové kopie, jak jste odeslali do služby. Načte TensorFlow uložit model, předává vstupního obrázku do relace TensorFlow na každý požadavek POST a vrátí výsledný skóre. Další architektury odvozování vyžadují různé soubory vyhodnocení.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model

def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = Model.get_model_path('tf-dnn-mnist')
    saver = tf.train.import_meta_graph(os.path.join(model_root, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")
    
    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, 'mnist-tf.model'))

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()

```
## <a name="define-the-conda-environment"></a>Definujte prostředí conda

Vytvořte soubor prostředí conda `myenv.yml` postup určení závislostí pro vaši službu. Je důležité určit, že používáte `tensorflow-gpu` k dosažení vyšší výkon.

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  - azureml-defaults==1.0.43.*
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

## <a name="define-the-gpu-inferenceconfig-class"></a>Definice třídy GPU InferenceConfig

Vytvoření `InferenceConfig` objekt, který umožňuje GPU a zajistí, že se spouští CUDA instaluje s image Dockeru.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='aks-dnn-mnist'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"tf-dnn-mnist")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   enable_gpu=True)
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

## <a name="issue-a-sample-query-to-your-model"></a>Ukázkový dotaz vydat modelu

Poslat testovací dotaz do nasazeného modelu. Při odesílání obrázků jpeg modelu stanoví skóre bitovou kopii. Následující příklad kódu používá funkci externí nástroj pro načtení bitové kopie. Můžete najít odpovídající kód v pir [TensorFlow ukázka na Githubu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py). 

```python
# Used to test your webservice
from utils import load_data 

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
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

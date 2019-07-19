---
title: Nasazení modelu pro odvození pomocí GPU
titleSuffix: Azure Machine Learning service
description: V tomto článku se naučíte, jak používat službu Azure Machine Learning k nasazení modelu hloubkového učení s povoleným grafickým procesorem jako webové služby. požadavky na odvození služby a skóre.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: eeb1bc35e0438a7e99ea5ed8284f0c8611108da0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326989"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Nasazení modelu hloubkového učení pro odvození pomocí GPU

V tomto článku se naučíte, jak používat službu Azure Machine Learning k nasazení modelu hloubkového učení Tensorflow s povoleným GPU jako webové služby.

Nasaďte model do clusteru Azure Kubernetes Service (AKS), abyste mohli Inferencing s podporou GPU. Inferencing nebo model bodování je fáze, ve které se nasazený model používá pro předpověď. Použití GPU namísto CPU nabízí výhody výkonu při vysoce paralelizovat výpočtu.

I když tato ukázka používá model TensorFlow, můžete použít následující kroky pro jakékoli rozhraní Machine Learning, které podporuje GPU, a to provedením malých změn v souboru bodování a souboru prostředí. 

V tomto článku proveďte následující kroky:

* Vytvoření clusteru AKS s povoleným GPU
* Nasazení Tensorflow modelu GPU
* Vydejte vzorový dotaz na nasazený model.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning Services.
* Distribuce Pythonu.
* Registrovaný model Tensorflow uložený.
    * Informace o tom, jak zaregistrovat modely, najdete v tématu [nasazení modelů](../service/how-to-deploy-and-where.md#registermodel).

V rámci jedné z těchto postupů je možné vyškolit [TensorFlow model](how-to-train-tensorflow.md), abyste splnili nezbytné požadavky.

## <a name="provision-an-aks-cluster-with-gpus"></a>Zřízení clusteru AKS pomocí GPU

Azure má spoustu různých možností GPU. Pro Inferencing můžete použít kterýkoli z nich. Úplný rozpis možností a nákladů najdete v [seznamu virtuálních počítačů řady N-Series](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) .

Další informace o používání AKS se službou Azure Machine Learning najdete v tématu [Jak nasadit a kde](../service/how-to-deploy-and-where.md#deploy-aks).

```Python
# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
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
> Pokud se zřídí cluster AKS, Azure vám bude účtovat. Až s tím budete hotovi, nezapomeňte cluster AKS odstranit.

## <a name="write-the-entry-script"></a>Zápis vstupního skriptu

Následující kód uložte do pracovního adresáře jako `score.py`. Tento soubor vyhodnotí obrázky při jejich posílání do vaší služby. Načte uložený model TensorFlow, předá do relace TensorFlow v každé žádosti POST a vrátí výsledná skóre. Jiné Inferencing architektury vyžadují jiné soubory bodování.

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
## <a name="define-the-conda-environment"></a>Definice prostředí conda

Vytvořte soubor prostředí conda s názvem `myenv.yml` k určení závislostí pro vaši službu. Je důležité určit, že používáte `tensorflow-gpu` k dosažení urychleného výkonu.

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

## <a name="define-the-gpu-inferenceconfig-class"></a>Definování třídy InferenceConfig GPU

`InferenceConfig` Vytvořte objekt, který povolí GPU a zajistí, že se CUDA nainstaluje s imagí Docker.

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

- [InferenceConfig – třída](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [AksServiceDeploymentConfiguration – třída](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>Nasazení modelu

Nasaďte model do clusteru AKS a počkejte, než se vytvoří vaše služba.

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
> Služba Azure Machine Learning neimplementuje model s `InferenceConfig` objektem, který očekává, že GPU bude povolená na cluster, který nemá GPU.

Další informace naleznete v tématu [třída modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-model"></a>Vydat vzorový dotaz do modelu

Odešlete testovací dotaz do nasazeného modelu. Při odeslání obrázku JPEG do modelu se vyhodnotí obrázek. Následující ukázka kódu používá funkci externích nástrojů pro načtení obrázků. Příslušný kód najdete v [ukázce PIR TensorFlow na GitHubu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py). 

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
> K minimalizaci latence a optimalizaci propustnosti se ujistěte, že je klient ve stejné oblasti Azure jako koncový bod. V tomto příkladu jsou rozhraní API vytvořená v oblasti Východní USA Azure.

## <a name="clean-up-the-resources"></a>Vyčištění prostředků

Pokud jste cluster AKS vytvořili konkrétně pro tento příklad, po dokončení odstraňte prostředky.

> [!IMPORTANT]
> Azure účtuje na základě toho, jak dlouho je cluster AKS nasazený. Nezapomeňte ho vyčistit až po jeho dokončení.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Další kroky

* [Nasazení modelu na FPGA](../service/how-to-deploy-fpga-web-service.md)
* [Nasazení modelu pomocí ONNX](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Výukové modely Tensorflow DNN](../service/how-to-train-tensorflow.md)

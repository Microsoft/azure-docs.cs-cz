---
title: Nasazení modelu pro odvození pomocí GPU
titleSuffix: Azure Machine Learning
description: V tomto článku se naučíte, jak pomocí Azure Machine Learning nasadit model hloubkového učení s povoleným GPU jako webovou službu. požadavky na odvození služby a skóre.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 6797c32ded5c12bbac3fafa1eabd1e6f74d28e07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519244"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Nasazení modelu hloubkového učení pro odvození pomocí GPU


V tomto článku se naučíte, jak použít Azure Machine Learning k nasazení modelu s povoleným grafickým procesorem jako webové služby. Informace v tomto článku jsou založené na nasazení modelu ve službě Azure Kubernetes Service (AKS). Cluster AKS poskytuje prostředek GPU, který používá model pro odvození.

Odvození modelu nebo Bodové hodnocení je fáze, ve které je nasazený model použit k provedení předpovědi. Použití GPU místo CPU nabízí výhody výkonu při vysoce paralelizovat výpočtu.

> [!IMPORTANT]
> Pro nasazení webových služeb je odvození GPU podporováno pouze ve službě Azure Kubernetes. Pro odvození pomocí __kanálu strojového učení__ se GPU podporují jenom v Azure Machine Learning Compute. Další informace o použití kanálů ML najdete v tématu [kurz: sestavení kanálu Azure Machine Learning pro dávkové vyhodnocování](tutorial-pipeline-batch-scoring-classification.md). 

> [!TIP]
> I když fragmenty kódu v tomto článku používají model TensorFlow, můžete tyto informace použít pro jakékoli rozhraní Machine Learning, které podporuje GPU.

> [!NOTE]
> Informace v tomto článku jsou založeny na informacích v článku [Jak nasadit do služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md) . V tomto článku se obecně zabývá nasazením na AKS. Tento článek popisuje nasazení specifické pro grafické procesory.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

* Prostředí pro vývoj v Pythonu s nainstalovanou sadou Azure Machine Learning SDK. Další informace najdete v tématu [Azure Machine Learning SDK](/python/api/overview/azure/ml/install).  

* Registrovaný model, který používá GPU.

    * Informace o tom, jak zaregistrovat modely, najdete v tématu [nasazení modelů](how-to-deploy-and-where.md#registermodel).

    * Chcete-li vytvořit a zaregistrovat model Tensorflow, který jste použili k vytvoření tohoto dokumentu, přečtěte si téma [Postup výuky modelu Tensorflow](how-to-train-tensorflow.md).

* Obecné informace o [tom, jak a kde nasadit modely](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Připojení k pracovnímu prostoru

Pokud se chcete připojit k existujícímu pracovnímu prostoru, použijte následující kód:

> [!IMPORTANT]
> Tento fragment kódu očekává uložení konfigurace pracovního prostoru do aktuálního adresáře nebo jeho nadřazeného objektu. Další informace o vytváření pracovního prostoru najdete v tématu [Vytvoření a Správa pracovních prostorů Azure Machine Learning](how-to-manage-workspace.md).   Další informace o ukládání konfigurace do souboru najdete v tématu [vytvoření konfiguračního souboru pracovního prostoru](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Vytvoření clusteru s Kubernetes pomocí GPU

Služba Azure Kubernetes poskytuje mnoho různých možností GPU. Pro odvození modelu můžete použít kterýkoli z nich. Úplný rozpis možností a nákladů najdete v [seznamu virtuálních počítačů řady N-Series](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) .

Následující kód ukazuje, jak vytvořit nový cluster AKS pro váš pracovní prostor:

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

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
> Azure vám bude účtovat, dokud cluster AKS existuje. Až s tím budete hotovi, nezapomeňte cluster AKS odstranit.

Další informace o použití AKS s Azure Machine Learning najdete v tématu [nasazení do služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Zápis vstupního skriptu

Skript vstupu přijme data odeslaná webové službě, předává je do modelu a vrátí výsledky bodování. Následující skript načte model Tensorflow při spuštění a pak použije model k určení skóre dat.

> [!TIP]
> Vstupní skript je specifický pro váš model. Například skript musí znát rozhraní pro použití s modelem, datovými formáty atd.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

Tento soubor má název `score.py` . Další informace o vstupních skriptech najdete v tématu [jak a kde nasadit](how-to-deploy-and-where.md).

## <a name="define-the-conda-environment"></a>Definice prostředí conda

Soubor prostředí conda určuje závislosti služby. Obsahuje závislosti vyžadované modelem i vstupním skriptem. Všimněte si, že je třeba zadat hodnotu AzureML-Defaults s verze >= 1.0.45 jako závislostí PIP, protože obsahuje funkce potřebné pro hostování modelu jako webové služby. Následující YAML definuje prostředí pro model Tensorflow. Určí, že se `tensorflow-gpu` použije GPU používané v tomto nasazení:

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
  - numpy
  - tensorflow-gpu=1.12
channels:
- conda-forge
```

V tomto příkladu je soubor uložen jako `myenv.yml` .

## <a name="define-the-deployment-configuration"></a>Definování konfigurace nasazení

> [!IMPORTANT]
> AKS neumožňuje, aby lusky sdílely GPU, může existovat jenom tolik replik webové služby s povoleným GPU, protože v clusteru jsou GPU.

Konfigurace nasazení definuje prostředí služby Azure Kubernetes, které se používá ke spuštění webové služby:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Další informace najdete v referenční dokumentaci k [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Definovat odvozenou konfiguraci

Odvozená konfigurace odkazuje na skript vstupu a objekt prostředí, který používá Image Docker s podporou GPU. Upozorňujeme, že soubor YAML, který se používá pro definici prostředí, musí obsahovat hodnotu AzureML-Defaults s Version >= 1.0.45 jako závislost PIP, protože obsahuje funkce potřebné k hostování modelu jako webové služby.

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

Další informace o prostředích najdete v tématu [vytváření a Správa prostředí pro školení a nasazení](how-to-use-environments.md).
Další informace najdete v referenční dokumentaci k [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig).

## <a name="deploy-the-model"></a>Nasazení modelu

Nasaďte model do clusteru AKS a počkejte, než se vytvoří vaše služba.

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

Další informace najdete v referenční dokumentaci k [modelu](/python/api/azureml-core/azureml.core.model.model).

## <a name="issue-a-sample-query-to-your-service"></a>Vydat vzorový dotaz do vaší služby

Odešlete testovací dotaz do nasazeného modelu. Při odeslání obrázku JPEG do modelu se vyhodnotí obrázek. Následující ukázka kódu stáhne testovací data a pak vybere náhodný test obrazu, který se odešle službě.

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

Další informace o vytvoření klientské aplikace najdete v tématu [Vytvoření klienta pro využívání nasazené webové služby](how-to-consume-web-service.md).

## <a name="clean-up-the-resources"></a>Vyčištění prostředků

Pokud jste cluster AKS vytvořili konkrétně pro tento příklad, po dokončení odstraňte prostředky.

> [!IMPORTANT]
> Azure účtuje na základě toho, jak dlouho je cluster AKS nasazený. Nezapomeňte ho vyčistit až po jeho dokončení.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Další kroky

* [Nasazení modelu na FPGA](how-to-deploy-fpga-web-service.md)
* [Nasazení modelu pomocí ONNX](concept-onnx.md#deploy-onnx-models-in-azure)
* [Výukové modely Tensorflow DNN](how-to-train-tensorflow.md)
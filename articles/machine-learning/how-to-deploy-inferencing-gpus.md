---
title: Nasazení modelu pro odvození pomocí GPU
titleSuffix: Azure Machine Learning
description: Tento článek vás naučí, jak pomocí Azure Machine Learning nasadit model hloubkového učení tentrážního toku s podporou GPU jako požadavky na odvození webové služby a skóre.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: b0fd537d1930e7c9d5f7a33f56ec5d00b1556562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78398344"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Nasazení modelu hlubokého učení pro odvození pomocí GPU
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Tento článek vás naučí, jak pomocí Azure Machine Learning nasadit model s podporou GPU jako webovou službu. Informace v tomto článku je založen na nasazení modelu na Azure Kubernetes Service (AKS). Cluster AKS poskytuje prostředek GPU, který model používá pro odvození.

Odvození nebo vyhodnocování modelu je fáze, kde se nasazený model používá k předpovědi. Použití gpu místo procesorů nabízí výhody výkonu na vysoce paralelizovatelné výpočtu.

> [!IMPORTANT]
> Pro nasazení webových služeb je odvození GPU podporované jenom ve službě Azure Kubernetes. Pro odvození pomocí __kanálu strojového učení__jsou gpu podporované jenom v Azure Machine Learning Compute. Další informace o použití kanálů ML naleznete v [tématu Spuštění predikcí dávek](how-to-use-parallel-run-step.md). 

> [!TIP]
> Přestože fragmenty kódu v tomto článku používají model TensorFlow, můžete použít informace pro všechny rámce strojového učení, který podporuje gpu.

> [!NOTE]
> Informace v tomto článku vychází z informací v článku [Jak nasadit do služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md) Service. Kde tento článek obecně popisuje nasazení do AKS, tento článek se týká nasazení specifické pro GPU.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor služby Azure Machine Learning. Další informace najdete [v tématu Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

* Vývojové prostředí Pythonu s nainstalovanou sadou Azure Machine Learning SDK. Další informace najdete v [tématu Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* Registrovaný model, který používá GPU.

    * Informace o registraci modelů naleznete v [tématu Nasazení modelů](how-to-deploy-and-where.md#registermodel).

    * Chcete-li vytvořit a zaregistrovat model Tensorflow použitý k vytvoření tohoto dokumentu, přečtěte si téma [Jak trénovat model Tentenzorflow](how-to-train-tensorflow.md).

* Obecné znalosti o [tom, jak a kde nasadit modely](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Připojení k pracovnímu prostoru

Chcete-li se připojit k existujícímu pracovnímu prostoru, použijte následující kód:

> [!IMPORTANT]
> Tento fragment kódu očekává, že konfigurace pracovního prostoru bude uložena v aktuálním adresáři nebo jeho nadřazeném adresáři. Další informace o vytváření pracovního prostoru najdete v tématu [Vytvoření a správa pracovních prostorů Azure Machine Learning](how-to-manage-workspace.md).   Další informace o uložení konfigurace do souboru naleznete v [tématu Vytvoření konfiguračního souboru pracovního prostoru](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Vytvoření clusteru Kubernetes s grafickými procesory

Služba Azure Kubernetes service poskytuje mnoho různých možností GPU. Můžete použít některý z nich pro odvození modelu. Úplný rozpis možností a nákladů najdete [v seznamu virtuálních zařízení řady N.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series)

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
> Azure vám bude účtovat, dokud existuje cluster AKS. Ujistěte se, že odstranit cluster AKS, když jste hotovi s ním.

Další informace o používání AKS s Azure Machine Learning najdete v [tématu Jak nasadit do služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Napsat vstupní skript

Vstupní skript obdrží data odeslaná webové službě, předá je modelu a vrátí výsledky hodnocení. Následující skript načte model Tensorflow při spuštění a potom použije model k skóre dat.

> [!TIP]
> Vstupní skript je specifický pro váš model. Skript musí například znát rámec pro použití s modelem, datovými formáty atd.

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

Tento soubor `score.py`je pojmenován . Další informace o vstupních skriptech naleznete v tématu [Jak a kde se nasadit](how-to-deploy-and-where.md).

## <a name="define-the-conda-environment"></a>Definování prostředí conda

Soubor prostředí conda určuje závislosti pro službu. Obsahuje závislosti vyžadované modelem i vstupním skriptem. Vezměte prosím na vědomí, že je nutné označit azureml-defaults s verion >= 1.0.45 jako pip závislost, protože obsahuje funkce potřebné k hostování modelu jako webové služby. Následující YAML definuje prostředí pro model Tentenzorflow. Určuje `tensorflow-gpu`, který bude využívat grafický procesor použitý v tomto nasazení:

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

V tomto příkladu je `myenv.yml`soubor uložen jako .

## <a name="define-the-deployment-configuration"></a>Definování konfigurace nasazení

Konfigurace nasazení definuje prostředí služby Azure Kubernetes, které se používá ke spuštění webové služby:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Další informace naleznete v referenční dokumentaci pro [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Definování konfigurace odvození

Odvození konfigurace odkazuje na vstupní skript a objekt prostředí, který používá image dockeru s podporou GPU. Vezměte prosím na vědomí, že soubor YAML používaný pro definici prostředí musí obsahovat azureml-defaults s verzí >= 1.0.45 jako závislost pipu, protože obsahuje funkce potřebné k hostování modelu jako webové služby.

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

Další informace o prostředích naleznete v [tématu Vytváření a správa prostředí pro školení a nasazení](how-to-use-environments.md).
Další informace naleznete v referenční dokumentaci pro [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py).

## <a name="deploy-the-model"></a>Nasazení modelu

Nasaďte model do clusteru AKS a počkejte, až vytvoří vaši službu.

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

> [!NOTE]
> Pokud `InferenceConfig` objekt `enable_gpu=True`má , `deployment_target` pak parametr musí odkazovat na cluster, který poskytuje GPU. V opačném případě se nasazení nezdaří.

Další informace naleznete v referenční dokumentaci pro [Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-service"></a>Vydání ukázkového dotazu vaší službě

Odešlete testovací dotaz nasazenému modelu. Když do modelu odešlete obrázek jpeg, získá snímek. Následující ukázka kódu stáhne testovací data a potom vybere náhodný testovací obrázek, který se má odeslat službě.

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

Další informace o vytvoření klientské aplikace naleznete v [tématu Vytvoření klienta, který využívá nasazenou webovou službu](how-to-consume-web-service.md).

## <a name="clean-up-the-resources"></a>Vyčištění prostředků

Pokud jste vytvořili cluster AKS speciálně pro tento příklad, odstraňte prostředky po dokončení.

> [!IMPORTANT]
> Azure účtuje na základě toho, jak dlouho se nasadí cluster AKS. Ujistěte se, že vyčistit poté, co jste udělal s ním.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Další kroky

* [Nasazení modelu na FPGA](how-to-deploy-fpga-web-service.md)
* [Nasazení modelu s ONNX](concept-onnx.md#deploy-onnx-models-in-azure)
* [Vlak Tensorflow DNN modely](how-to-train-tensorflow.md)

---
title: Školení neuronové sítě s hloubkovým učením pomocí TensorFlow
titleSuffix: Azure Machine Learning
description: Naučte se, jak spouštět školicí skripty TensorFlow ve velkém měřítku pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: a1ab8f881aaee9e29519e99a5cd2a0e6fdbc9846
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489416"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Vytvářejte TensorFlow model hloubkového učení ve velkém měřítku pomocí Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak spustit školicí skripty [TensorFlow](https://www.tensorflow.org/overview) ve velkém měřítku pomocí třídy [estimator TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) v Azure Machine Learning. V tomto příkladu se navlacích a registruje TensorFlow model pro klasifikaci psaných číslic pomocí neuronové sítě (DNN).

Bez ohledu na to, jestli vyvíjíte model TensorFlow z provozu nebo přenášíte [existující model](how-to-deploy-existing-model.md) do cloudu, můžete použít Azure Machine Learning k horizontálnímu navýšení kapacity Open-Source školicích úloh pro sestavování, nasazování, správu a monitorování modelů produkčních prostředků. .

Přečtěte si další informace o službě [hloubkového učení vs Machine Learning](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Předpoklady

Spusťte tento kód v jednom z těchto prostředí:

 - Azure Machine Learning výpočetní instance – nepotřebujete žádné soubory ke stažení nebo instalaci

     - Dokončete [kurz: instalační prostředí a pracovní prostor](tutorial-1st-experiment-sdk-setup.md) pro vytvoření vyhrazeného serveru poznámkového bloku předem načteného se sadou SDK a s ukázkovým úložištěm.
    - Ve složce s ukázkami hloubkového učení na serveru poznámkového bloku najděte dokončený a rozbalený Poznámkový blok tak, že přejdete do tohoto adresáře: **postupy-použití-azureml > ml-framework > tensorflow > deployment > Výuková složka – parametr-Intune-Deploy-with-tensorflow** . 
 
 - Váš vlastní server Jupyter Notebook

    - [Nainstalujte sadu Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
    - [Stažení ukázkových souborů skriptu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` a `utils.py`
     
    Dokončenou [Jupyter notebook verzi](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) tohoto průvodce najdete na stránce ukázek na GitHubu. Poznámkový blok obsahuje rozšířené oddíly, které pokrývají inteligentní ladění parametrů, nasazení modelů a widgety poznámkových bloků.

## <a name="set-up-the-experiment"></a>Nastavení experimentu

Tato část nastavuje experiment pro školení načtením požadovaných balíčků Pythonu, inicializací pracovního prostoru, vytvořením experimentu a nahráním školicích dat a školicích skriptů.

### <a name="import-packages"></a>Import balíčků

Nejdřív importujte nezbytné knihovny Pythonu.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicializovat pracovní prostor

[Azure Machine Learning pracovní prostor](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte. V sadě Python SDK můžete získat přístup k artefaktům pracovního prostoru vytvořením objektu [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) .

Vytvořte objekt pracovního prostoru ze souboru `config.json` vytvořeného v [části požadavky](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Vytvoření experimentu s hloubkovým učením

Vytvořte experiment a složku, do které se budou ukládat skripty pro školení. V tomto příkladu vytvořte experiment s názvem TF-mnist ručně zapsaných.

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>Vytvoření datové sady souborů

Objekt `FileDataset` odkazuje na jeden nebo více souborů v úložišti dat pracovního prostoru nebo veřejné adresy URL. Soubory mohou být libovolného formátu a třída poskytuje možnost stahovat nebo připojovat soubory do výpočtů. Vytvořením `FileDataset` vytvoříte odkaz na umístění zdroje dat. Pokud jste v sadě dat použili jakékoli transformace, budou uloženy i v datové sadě. Data zůstanou ve svém stávajícím umístění, takže se neúčtují žádné dodatečné náklady na úložiště. Další informace najdete [v průvodci `Dataset`](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-register-datasets) .

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Pomocí metody `register()` zaregistrujete datovou sadu do svého pracovního prostoru, aby bylo možné je sdílet s ostatními uživateli, znovu použít v různých experimentech a v rámci školicího skriptu, na který se odkazuje podle názvu.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

## <a name="create-a-compute-target"></a>Vytvořit cíl výpočtů

Vytvořte výpočetní cíl pro úlohu TensorFlow, na které se má spustit. V tomto příkladu vytvoříte výpočetní cluster Azure Machine Learning s podporou GPU.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Další informace o výpočetních cílech najdete v článku [co je cílový výpočetní cíl](concept-compute-target.md) .

## <a name="create-a-tensorflow-estimator"></a>Vytvoření TensorFlow Estimator

[TensorFlow Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) poskytuje jednoduchý způsob, jak spustit školicí úlohu TensorFlow na cílovém výpočetním cíli.

TensorFlow Estimator je implementován prostřednictvím obecné třídy [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) , kterou lze použít k podpoře libovolného rozhraní. Další informace o školicích modelech pomocí obecného Estimator najdete v tématu [výuka modelů s Azure Machine Learning pomocí Estimator](how-to-train-ml-models.md) .

Pokud váš školicí skript potřebuje ke spuštění další balíčky PIP nebo Conda, můžete je nainstalovat ve výsledné imagi Docker předáním jejich názvů prostřednictvím `pip_packages` a `conda_packages`ch argumentů.

```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Odeslat běh

[Objekt Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) poskytuje rozhraní k historii spuštění, když je úloha spuštěná a po jejím dokončení.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Po spuštění se spustí v následujících fázích:

- **Příprava**: obrázek Docker se vytvoří podle TensorFlow Estimator. Obrázek se nahraje do registru kontejneru v pracovním prostoru a v mezipaměti pro pozdější spuštění. Protokoly se také streamují do historie spuštění a dají se zobrazit ke sledování průběhu.

- **Škálování**: cluster se pokusí o horizontální navýšení kapacity, pokud Batch AI cluster vyžaduje více uzlů pro spuštění běhu, než je aktuálně k dispozici.

- **Spuštěno**: všechny skripty ve složce skriptu se nahrají do cílového výpočetního prostředí, úložiště dat se připojí nebo zkopírují a entry_script se spustí. Výstupy z stdout a složky./logs se streamují do historie spuštění a dají se použít k monitorování běhu.

- **Následné zpracování**: složka./Outputs se v běhu kopíruje do historie spuštění.

## <a name="register-or-download-a-model"></a>Registrace nebo stažení modelu

Po proškolení modelu ho můžete zaregistrovat do svého pracovního prostoru. Registrace modelu umožňuje ukládat a modelovat vaše modely do svého pracovního prostoru, aby bylo možné zjednodušit [správu modelů a nasazení](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

Místní kopii modelu můžete také stáhnout pomocí objektu run. V školicím skriptu `mnist-tf.py` objekt spořiče TensorFlow uchovává model do místní složky (místní k cíli výpočtů). Kopii můžete stáhnout pomocí objektu spustit.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Distribuované trénování

[`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) Estimator také podporuje distribuované školení v rámci clusterů procesoru a GPU. Můžete snadno spouštět distribuované úlohy TensorFlow a Azure Machine Learning bude orchestrace spravovat za vás.

Azure Machine Learning podporuje dvě metody distribuovaného školení v TensorFlow:

- Distribuované školení [založené na MPI](https://www.open-mpi.org/) s využitím architektury [Horovod](https://github.com/uber/horovod)
- Nativní [distribuované TensorFlow](https://www.tensorflow.org/deploy/distributed) pomocí metody serveru parametrů

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) je open-source platforma pro distribuované školení vyvinuté pomocí Uber. Nabízí snadnou cestu k distribuovaným úlohám TensorFlow GPU.

Chcete-li použít Horovod, zadejte objekt [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) pro parametr `distributed_training` v konstruktoru TensorFlow. Tento parametr zajišťuje, že se knihovna Horovod nainstaluje pro použití ve školicím skriptu.

```Python
from azureml.core.runconfig import MpiConfiguration
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```

### <a name="parameter-server"></a>Server parametrů

Můžete také spustit [nativní distribuované TensorFlow](https://www.tensorflow.org/deploy/distributed), které používají model serveru parametrů. V této metodě budete vlakovat v clusteru parametrů serverů a pracovních procesů. Pracovní procesy vypočítávají přechody během školení, zatímco servery parametrů agreguje přechody.

Chcete-li použít metodu serveru parametrů, zadejte objekt [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) pro parametr `distributed_training` v konstruktoru TensorFlow.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>Definice specifikací clusteru v ' TF_CONFIG '

Pro [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)budete potřebovat taky síťové adresy a porty clusteru, takže Azure Machine Learning pro vás nastaví proměnnou prostředí `TF_CONFIG`.

Proměnná prostředí `TF_CONFIG` je řetězec JSON. Tady je příklad proměnné pro server parametrů:

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Pro TensorFlow rozhraní API pro [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) vysoké úrovně TensorFlow analyzuje proměnnou `TF_CONFIG` a vytvoří pro vás specifikaci clusteru.

Pro základní rozhraní API nižší úrovně TensorFlow pro školení, analyzujte proměnnou `TF_CONFIG` a sestavte `tf.train.ClusterSpec` ve školicím kódu.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="next-steps"></a>Další kroky

V tomto článku jste si vyškole a zaregistrovali model TensorFlow. Pokud chcete zjistit, jak nasadit model do clusteru s podporou GPU, pokračujte na náš článek nasazení modelu GPU.

> [!div class="nextstepaction"]
> [Jak a kde nasadit modely](how-to-deploy-and-where.md)
* [Sledovat metriky spuštění během školení](how-to-track-experiments.md)
* [Ladit parametry](how-to-tune-hyperparameters.md)
* [Nasazení trained model](how-to-deploy-and-where.md)
* [Referenční architektura distribuovaného školení pro hloubkové učení v Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)

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
ms.openlocfilehash: 52c675369fa70d1b1113f34b9b0dda2126547e0a
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002505"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Vytvářejte TensorFlow model hloubkového učení ve velkém měřítku pomocí Azure Machine Learning

V tomto článku se dozvíte, jak spustit školicí skripty [TensorFlow](https://www.tensorflow.org/overview) ve velkém měřítku pomocí třídy [estimator TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) v Azure Machine Learning. V tomto příkladu se navlacích a registruje TensorFlow model pro klasifikaci psaných číslic pomocí neuronové sítě (DNN).

Bez ohledu na to, jestli vyvíjíte model TensorFlow z provozu nebo přenášíte [existující model](how-to-deploy-existing-model.md) do cloudu, můžete použít Azure Machine Learning k horizontálnímu navýšení kapacity Open-Source školicích úloh pro sestavování, nasazování, správu a monitorování modelů produkčních prostředků. .

Přečtěte si další informace o službě [hloubkového učení vs Machine Learning](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Požadavky

Spusťte tento kód v jednom z těchto prostředí:

 - Virtuální počítač s poznámkovým blokem Azure Machine Learning – nemusíte stahovat nebo instalovat

     - Dokončete [kurz: Nastavte prostředí a pracovní](tutorial-1st-experiment-sdk-setup.md) prostor pro vytvoření vyhrazeného serveru poznámkového bloku předem načteného pomocí sady SDK a ukázkového úložiště.
    - Ve složce ukázek pro hloubkové učení na serveru poznámkového bloku najděte dokončený a rozbalený Poznámkový blok tak, že přejdete do tohoto adresáře: **postupy-použití-azureml > školení – s hloubkovým učením > výukový-tensorflow-Tune-Deploy-with-** složky. 
 
 - Váš vlastní server Jupyter Notebook

    - [Nainstalujte sadu Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
    - [Stažení ukázkových souborů skriptu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` a`utils.py`
     
    Dokončenou [Jupyter notebook verzi](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) tohoto průvodce najdete na stránce ukázek na GitHubu. Poznámkový blok obsahuje rozšířené oddíly, které pokrývají inteligentní ladění parametrů, nasazení modelů a widgety poznámkových bloků.

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

[Azure Machine Learning pracovní prostor](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte. V sadě Python SDK máte přístup k artefaktům pracovního prostoru vytvořením [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objektu.

Vytvořte objekt pracovního prostoru ze `config.json` souboru vytvořeného v [části požadavky](#prerequisites).

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

### <a name="upload-dataset-and-scripts"></a>Nahrát datovou sadu a skripty

[Úložiště](how-to-access-data.md) dat je místo, kde se můžou data ukládat a přistupovat jejich připojením k cíli výpočtů nebo jejich zkopírováním. Každý pracovní prostor poskytuje výchozí úložiště dat. Nahrajte data a školicí skripty do úložiště dat, aby k nim bylo možné snadno přistup během školení.

1. Stáhněte si datovou sadu MNIST ručně zapsaných místně.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Nahrajte datovou sadu MNIST ručně zapsaných do výchozího úložiště dat.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Nahrajte školicí skript TensorFlow, `tf_mnist.py`a `utils.py`soubor nápovědy.

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Vytvořte cílové výpočetní prostředí

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

TensorFlow Estimator je implementován prostřednictvím obecné [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) třídy, kterou lze použít k podpoře libovolného rozhraní. Další informace o školicích modelech pomocí obecného Estimator najdete v tématu [výuka modelů s Azure Machine Learning pomocí Estimator](how-to-train-ml-models.md) .

Pokud váš školicí skript potřebuje ke spuštění další balíčky PIP nebo Conda, můžete mít balíčky nainstalované ve výsledné imagi Docker tím, že předáte jejich názvy pomocí `pip_packages` argumentů a. `conda_packages`

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
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

- **Připravuje**se: Obrázek Docker se vytvoří podle TensorFlow Estimator. Obrázek se nahraje do registru kontejneru v pracovním prostoru a v mezipaměti pro pozdější spuštění. Protokoly se také streamují do historie spuštění a dají se zobrazit ke sledování průběhu.

- **Škálování:** Cluster se pokusí o horizontální navýšení kapacity, pokud Batch AI cluster vyžaduje více uzlů pro spuštění běhu, než je aktuálně k dispozici.

- **Spuštěno**: Všechny skripty ve složce skriptu se nahrají do cílového výpočetního prostředí, úložiště dat se připojí nebo zkopírují a entry_script se spustí. Výstupy z stdout a složky./logs se streamují do historie spuštění a dají se použít k monitorování běhu.

- **Následné zpracování**: Složka s příponou./Outputs se zkopíruje do historie spuštění.

## <a name="register-or-download-a-model"></a>Registrace nebo stažení modelu

Po proškolení modelu ho můžete zaregistrovat do svého pracovního prostoru. Registrace modelu umožňuje ukládat a modelovat vaše modely do svého pracovního prostoru, aby bylo možné zjednodušit [správu modelů a nasazení](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

Místní kopii modelu můžete také stáhnout pomocí objektu run. Ve školicím skriptu `mnist-tf.py`objekt TensorFlow spořiče uchovává model do místní složky (místní k cíli výpočtů). Kopii můžete stáhnout pomocí objektu spustit.

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

Azure Machine Learning podporuje dvě metody distribuované trénování TensorFlow:

- Distribuované školení [založené na MPI](https://www.open-mpi.org/) s využitím architektury [Horovod](https://github.com/uber/horovod)
- Nativní [distribuované TensorFlow](https://www.tensorflow.org/deploy/distributed) pomocí metody serveru parametrů

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) je open-source platforma pro distribuované školení vyvinuté pomocí Uber. Nabízí snadnou cestu k distribuovaným úlohám TensorFlow GPU.

Chcete-li použít Horovod, [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) zadejte objekt `distributed_training` pro parametr v konstruktoru TensorFlow. Tento parametr zajišťuje, že se knihovna Horovod nainstaluje pro použití ve školicím skriptu.

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

Můžete také spustit [nativní distribuované TensorFlow](https://www.tensorflow.org/deploy/distributed), model serveru parametr, který používá. V této metodě trénování napříč clusterem parametr serverů a pracovních procesů. Zaměstnanci vypočítat přechody během cvičení, zatímco parametr servery agregovat přechody.

Chcete-li použít metodu serveru parametrů, zadejte [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) objekt `distributed_training` pro parametr v konstruktoru TensorFlow.

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

Pro [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)můžete také potřebovat síťové adresy a porty clusteru, takže Azure Machine Learning pro vás `TF_CONFIG` nastaví proměnnou prostředí.

`TF_CONFIG` Proměnná prostředí je řetězec formátu JSON. Tady je příklad proměnné pro parametr serveru:

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

V případě rozhraní API na [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) vysoké úrovni TensorFlow TensorFlow analyzuje `TF_CONFIG` proměnnou a vytvoří pro vás specifikaci clusteru.

Pro základní rozhraní API nižší úrovně TensorFlow pro školení, analyzujte `TF_CONFIG` proměnnou a `tf.train.ClusterSpec` Sestavte kód školení.

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
* [Sledovat spustit metriky během cvičení](how-to-track-experiments.md)
* [Vyladění hyperparameters](how-to-tune-hyperparameters.md)
* [Nasazení trénovaného modelu](how-to-deploy-and-where.md)
* [Referenční architektura distribuovaného školení pro hloubkové učení v Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)

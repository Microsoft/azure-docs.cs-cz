---
title: Trénování a nasazování modelu TensorFlow
titleSuffix: Azure Machine Learning
description: Zjistěte, jak spouštět školicí skripty TensorFlow ve velkém měřítku pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 2bbd81f3858aa78b9e0e2d610c0fdb0a67816c8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228309"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Sestavte si model hlubokého učení TensorFlow ve velkém měřítku pomocí Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Tento článek ukazuje, jak spustit [tentenorflow](https://www.tensorflow.org/overview) školicí skripty ve velkém měřítku pomocí třídy [odhadu Tentenflow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) Azure Machine Learning. Tento příklad trénuje a registruje model TensorFlow pro klasifikaci ručně psaných číslic pomocí hluboké neuronové sítě (DNN).

Ať už vyvíjíte model TensorFlow od základu nebo přinášíte [existující model](how-to-deploy-existing-model.md) do cloudu, můžete pomocí Azure Machine Learning škálovat úlohy s otevřeným zdrojovým kódem k vytváření, nasazování, verzím a monitorování produkčních modelů.

Další informace o [hloubkovém učení vs. strojové učení](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Požadavky

Spusťte tento kód v některém z těchto prostředí:

 - Výpočetní instance Azure Machine Learning – není nutné žádné stahování ani instalace

     - Dokončete [kurz: Instalační prostředí a pracovní prostor](tutorial-1st-experiment-sdk-setup.md) a vytvořte vyhrazený notebook ový server s předinstalovaným sadou SDK a ukázkovým úložištěm.
    - Ve složce ukázky hluboké učení na serveru notebooku, najít dokončené a rozšířené poznámkový blok přechodem do tohoto adresáře: **how-to-use-azureml > ml-frameworks > tendentou> nasazení > vlak-hyperparameter-tune-deploy-with-tensorflow** složky. 
 
 - Váš vlastní Jupyter Notebook server

    - [Nainstalujte azure machine learning sdk](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
    - [Stáhněte si ukázkové soubory skriptů](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` a`utils.py`
     
    Můžete také najít vyplněnou [verzi jupyterského poznámkového bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) této příručky na stránce ukázek GitHub. Poznámkový blok obsahuje rozšířené sekce, které zahrnují inteligentní ladění hyperparametrů, nasazení modelu a widgety poznámkových bloků.

## <a name="set-up-the-experiment"></a>Nastavení experimentu

Tato část nastavuje trénovací experiment načtením požadovaných balíčků Pythonu, inicializací pracovního prostoru, vytvořením experimentu a nahráním trénovacích dat a trénovacích skriptů.

### <a name="import-packages"></a>Import balíčků

Nejprve importujte potřebné knihovny Pythonu.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import TensorFlow
```

### <a name="initialize-a-workspace"></a>Inicializaci pracovního prostoru

[Pracovní prostor Azure Machine Learning](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje vám centralizované místo pro práci se všemi artefakty, které vytvoříte. V pythonu SDK můžete přistupovat k artefaktům [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) pracovního prostoru vytvořením objektu.

Vytvořte objekt pracovního `config.json` prostoru ze souboru vytvořeného v [části požadavky](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Vytvoření experimentu s hlubokým učením

Vytvořte experiment a složku pro uložení školicích skriptů. V tomto příkladu vytvořte experiment s názvem "tf-mnist".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>Vytvoření datové sady souborů

Objekt `FileDataset` odkazuje na jeden nebo více souborů v úložišti dat pracovního prostoru nebo na veřejné adresy URL. Soubory mohou mít libovolný formát a třída vám poskytuje možnost stáhnout nebo připojit soubory k výpočtu. Vytvořením `FileDataset`aplikace vytvoříte odkaz na umístění zdroje dat. Pokud jste na sadu dat použili nějaké transformace, budou uloženy také v sadě dat. Data zůstanou ve stávajícím umístění, takže nevznikají žádné další náklady na úložiště. Další [how-to](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) informace naleznete v `Dataset` návodu k balíčku.

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

Pomocí `register()` této metody můžete zaregistrovat sadu dat do pracovního prostoru, aby mohla být sdílena s ostatními, znovu použita v různých experimentech a v trénovacím skriptu označována podle názvu.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

## <a name="create-a-compute-target"></a>Vytvoření výpočetního cíle

Vytvořte výpočetní cíl pro úlohu TensorFlow, na které se má spustit. V tomto příkladu vytvořte výpočetní cluster Azure Machine Learning s podporou GPU.

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

Další informace o výpočetních cílech najdete v článku, [co je cíl výpočetního cíle.](concept-compute-target.md)

## <a name="create-a-tensorflow-estimator"></a>Vytvoření odhadu Tentendotu

[Odhad TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) poskytuje jednoduchý způsob spuštění trénovací úlohy TensorFlow na výpočetní cíl.

Odhad TentenzorFlow je implementován prostřednictvím obecné [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) třídy, kterou lze použít pro podporu libovolného rámce. Další informace o trénovacích modelech pomocí obecného odhadu najdete v tématu [trénování modelů s Azure Machine Learning pomocí odhadu](how-to-train-ml-models.md)

Pokud váš školicí skript potřebuje další pip nebo conda balíčky ke spuštění, můžete mít `pip_packages` `conda_packages` balíčky nainstalované na výsledné image Dockeru předáním jejich názvy prostřednictvím a argumenty.

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
                 use_gpu=True,
                 pip_packages=['azureml-dataprep[pandas,fuse]'])
```

> [!TIP]
> Podpora pro **Tensorflow 2.0** byla přidána do třídy odhadu Tentenzorflow. Další informace najdete v tomto [blogovém příspěvku](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/).

Další informace o přizpůsobení prostředí Pythonu najdete v [tématu Vytváření a správa prostředí pro školení a nasazení](how-to-use-environments.md). 

## <a name="submit-a-run"></a>Odeslat běh

Run [Objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) poskytuje rozhraní pro historii spuštění, zatímco úloha je spuštěna a po dokončení.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Jako run je proveden, prochází následujícími fázemi:

- **Příprava**: Obrázek Dockeru se vytvoří podle odhadu Tentenzorflow. Bitová kopie se nahraje do registru kontejnerů pracovního prostoru a uloží do mezipaměti pro pozdější spuštění. Protokoly jsou také přenášeny do historie spuštění a lze je zobrazit za účelem sledování průběhu.

- **Škálování:** Cluster se pokusí vertikálně navýšit kapacitu, pokud cluster Batch AI vyžaduje ke spuštění spuštění více uzlů, než je aktuálně k dispozici.

- **Spuštění**: Všechny skripty ve složce skriptu jsou odeslány do výpočetního cíle, úložiště dat jsou připojena nebo zkopírována a entry_script se spustí. Výstupy ze složky stdout a ./logs jsou přenášeny do historie spuštění a lze je použít ke sledování běhu.

- **Post-Processing**: Složka ./výstupy spuštění je zkopírována do historie spuštění.

## <a name="register-or-download-a-model"></a>Registrace nebo stažení modelu

Jakmile model vycvičíte, můžete jej zaregistrovat do pracovního prostoru. Registrace modelu umožňuje ukládat a nakládat s modely v pracovním prostoru a zjednodušit [tak správu a nasazení modelu](concept-model-management-and-deployment.md). Zadáním parametrů `model_framework`, `model_framework_version`, `resource_configuration`a , bez kódu nasazení modelu bude k dispozici. To umožňuje přímo nasadit model jako webovou službu `ResourceConfiguration` z registrovaného modelu a objekt definuje výpočetní prostředek pro webovou službu.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Můžete také stáhnout místní kopii modelu pomocí Spustit objekt. V trénovacím skriptu `mnist-tf.py`objekt spořiče Tentenredutu zachová model do místní složky (místní podle výpočetního cíle). Ke stažení kopie můžete použít objekt Spustit.

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

Odhad [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) také podporuje distribuované školení napříč clustery CPU a GPU. Můžete snadno spouštět distribuované úlohy TensorFlow a Azure Machine Learning bude spravovat orchestraci za vás.

Azure Machine Learning podporuje dvě metody distribuovaného školení v TensorFlow:

- Distribuovaná školení [založená na MPI](https://www.open-mpi.org/) pomocí [horovodového](https://github.com/uber/horovod) rámce
- Nativní [distribuovaný TensorFlow](https://www.tensorflow.org/deploy/distributed) pomocí metody parametrového serveru

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) je open-source framework pro distribuované školení vyvinuté společností Uber. Nabízí snadnou cestu k distribuovaným úlohám GPU TensorFlow.

Chcete-li použít Horovod, zadejte [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objekt pro `distributed_training` parametr v konstruktoru TensorFlow. Tento parametr zajišťuje, že horovodská knihovna je nainstalována pro použití v tréninkovém skriptu.

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
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])
```

### <a name="parameter-server"></a>Parametrický server

Můžete také spustit [nativní distribuovaný TensorFlow](https://www.tensorflow.org/deploy/distributed), který používá model parametrového serveru. V této metodě trénování přes cluster parametr serverů a pracovníků. Pracovníci vypočítat přechody během školení, zatímco parametr servery agregovat přechody.

Chcete-li použít metodu [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) parametrserveru, `distributed_training` zadejte objekt pro parametr v konstruktoru TensorFlow.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
tf_est= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>Definování specifikací clusteru v TF_CONFIG

Potřebujete také síťové adresy a porty clusteru [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)pro , takže `TF_CONFIG` Azure Machine Learning nastaví proměnnou prostředí pro vás.

Proměnná `TF_CONFIG` prostředí je řetězec JSON. Zde je příklad proměnné pro parametrový server:

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

Pro rozhraní API na [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) vysoké úrovni TensorFlow TensorFlow analyzuje `TF_CONFIG` proměnnou a vytváří specifikace clusteru za vás.

Pro základní api tensorflow nižší úrovně pro školení, `TF_CONFIG` analyzovat proměnnou a vytvořit v tréninkovém `tf.train.ClusterSpec` kódu.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="deploy-a-tensorflow-model"></a>Nasazení modelu TensorFlow

Model, který jste právě zaregistrovali, můžete nasadit přesně stejným způsobem jako jakýkoli jiný registrovaný model v Azure Machine Learning, bez ohledu na to, který odhad jste použili pro školení. Postup nasazení obsahuje oddíl o registraci modelů, ale můžete přeskočit přímo k [vytvoření výpočetního cíle](how-to-deploy-and-where.md#choose-a-compute-target) pro nasazení, protože již máte registrovaný model.

## <a name="preview-no-code-model-deployment"></a>(Náhled) Nasazení modelu bez kódu

Namísto tradiční trasy nasazení můžete také použít funkci nasazení bez kódu (náhled) pro Tentenzorflow. Registrací modelu, jak je `model_framework`znázorněno výše, pomocí , `model_framework_version`a `resource_configuration` parametry, můžete jednoduše použít statickou `deploy()` funkci k nasazení modelu.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Úplné [návody na](how-to-deploy-and-where.md) nasazení v Azure Machine Learning do větší hloubky.

## <a name="next-steps"></a>Další kroky

V tomto článku jste vyškolili a zaregistrovali model TensorFlow a dozvěděli se o možnostech nasazení. Další informace o Azure Machine Learning najdete v těchto dalších článcích.

* [Sledovat metriky běhu během tréninku](how-to-track-experiments.md)
* [Ladění hyperparametrů](how-to-tune-hyperparameters.md)
* [Referenční architektura pro distribuované školení pro hloubkové učení v Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)

---
title: Trénování a zaregistrujte TensorFlow modelů
titleSuffix: Azure Machine Learning service
description: Tento článek popisuje, jak pro trénování a zaregistrujte model TensorFlow pomocí služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 05/28/2019
ms.custom: seodec18
ms.openlocfilehash: f3d675d0eac1255974995fd7717192ec6a21bac1
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400217"
---
# <a name="use-azure-machine-learning-service-to-train-and-register-tensorflow-models"></a>Použijte službu Azure Machine Learning k trénování a registraci TensorFlow modelů

Tento článek popisuje, jak pro trénování a zaregistrujte model TensorFlow pomocí služby Azure Machine Learning. Budeme používat Oblíbené [datovou sadu mnist ručně](http://yann.lecun.com/exdb/mnist/) klasifikovat ručně číslic, pomocí hluboké neuronové sítě využívající TensorFlow.

Služba Azure Machine Learning budete moct rychle horizontálně navýšit kapacitu vaší úlohy trénování open source pomocí elastických cloudových výpočetních prostředků. Budete také moci sledovat tréninkových spuštění, verze modelů, nasazení modelů a mnoho dalšího. Ať už vyvíjíte modelu TensorFlow od základů nebo existující model spojili do cloudu, služby Azure Machine Learning je připraven vám pomůže vytvářet modely připravené pro produkční prostředí.

## <a name="prerequisites"></a>Požadavky

- Nainstalujte aplikaci Azure Machine Learning sady SDK pro Python
- Volitelné: Vytvořte konfigurační soubor pracovního prostoru
- Stáhněte si [ukázkové soubory skriptu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` a `utils.py`

Můžete postupovat podle [Průvodce nastavením Python SDK](setup-create-workspace.md#sdk) podrobné pokyny k nastavení vašeho prostředí. Ukázkové soubory školení najdete na naší [stránku GitHub s ukázkami](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) spolu se rozšířený, Poznámkový blok Juypter verzi této příručky.

## <a name="set-up-the-experiment"></a>Nastavení testu

### <a name="import-packages"></a>Import balíčků

Nejdřív potřebujeme naimportujte potřebné knihovny jazyka Python.

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

Pracovní prostor je prostředek nejvyšší úrovně služby. To vám poskytne centrální místo, kde můžete pracovat s všechny artefakty, které vytvoříte.

Pokud jste dokončili krok v [části s předpoklady](#prerequisites), můžete použít `Workspace.from_config()` rychle vytvořit objekt pracovního prostoru z podrobné informace uložené v konfiguračním souboru.

```Python
ws = Workspace.from_config()
```

Můžete také můžete vytvořit pracovní prostor explicitně.

```Python
ws = Workspace.create(name='<workspace-name>',
                      subscription_id='<azure-subscription-id>',
                      resource_group='<choose-a-resource-group>',
                      create_resource_group=True,
                      location='<select-location>' # For example: 'eastus2'
                      )
```

### <a name="create-an-experiment"></a>Vytvoření experimentu

Vytvoření experimentu a složku pro uložení trénovací skripty. V tomto příkladu vytvoření experimentu s názvem "tf mnist ručně"

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Nahrání datové sady a skripty

[Datastore](how-to-access-data.md) je místo, kde můžete data ukládají a získávají připojením nebo kopírování dat do cílového výpočetního prostředí. Každý pracovní prostor poskytuje výchozí úložiště. Nahrajeme našich dat a trénovací skripty tak, aby k nim mohou snadno přistupovat během cvičení.

1. Stáhnout datovou sadu mnist ručně místně

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Nahrajte datovou sadu mnist ručně do výchozího úložiště.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Uložení skriptu trénování TensorFlow, `tf_mnist.py`a pomocný soubor `utils.py`.

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Vytvořte cílové výpočetní prostředí

Vytvořte cílové výpočetní prostředí pro vaši úlohu TensorFlow ke spuštění na. V tomto příkladu vytvoříme cluster s podporou grafického procesoru AmlCompute. Seznam dostupných školení cílových výpočetních prostředí najdete v tématu [v tomto článku](how-to-set-up-training-targets.md#compute-targets-for-training)

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

## <a name="create-a-tensorflow-estimator"></a>Vytvoření TensorFlow odhad

[TensorFlow estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) poskytuje jednoduchý způsob spouštění úlohy trénování TensorFlow na cílové výpočetní prostředí. Bude automaticky poskytovat image dockeru, který má TensorFlow nainstalované.

Můžete zahrnout další balíčky pip nebo conda výsledný image dockeru předáním názvů prostřednictvím `pip_packages` a `conda_packages` argumenty.

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='tf_mnist.py',
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Odeslat spustit

[Spustit objekt](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) poskytuje rozhraní pro historie spuštění, když úloha běží, a po jejím dokončení.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Jak spustit provádí, projdou následujících fází:

- **Příprava**: Podle TensorFlow odhaduje se vytvoří image dockeru. Na obrázku je odeslat do registru kontejnerů pracovního prostoru a uložená v mezipaměti pro pozdější spuštění. Protokoly se také streamují do historie spouštění a lze je zobrazit můžete sledovat průběh.

- **Škálování**: Cluster se pokusí vertikálně navýšit kapacitu if clusteru Batch AI vyžaduje ke spuštění spustit, než je aktuálně k dispozici více uzlů.

- **Spuštění**: Všechny skripty ve složce script se nahrají do cílové výpočetní prostředí, úložiště dat se připojí, tedy zkopírují a entry_script provádí. Výstup z výstupu stdout a. / složky protokolů se streamují do historie spouštění a slouží k monitorování spuštění.

- **Následné zpracování**: . / Výstupy, složka spuštění se kopíruje do historie spuštění.

## <a name="register-or-download-a-model"></a>Zaregistrovat nebo stáhnout modelu

Když jsme natrénovali model, můžete ho zaregistrovat do pracovního prostoru. Registrace modelu vám umožní úložiště a verzí vašich modelů v pracovním prostoru pro zjednodušení [model nasazení a správu](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

Můžete také stáhnout místní kopii modelu s použitím objektu spuštění. Ve skriptu školení `mnist-tf.py`, objekt spořič TensorFlow nevyřeší modelu do místní složky (místní cílové výpočetní prostředí). Můžeme použít objekt spuštění a stáhněte si kopii.

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

[ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) Estimator také podporuje distribuované trénování napříč clustery CPU a GPU. Můžete snadno spouštět distribuované TensorFlow úlohy a služby Azure Machine Learning spravovat orchestraci za vás.

Služba Azure Machine Learning podporuje dvě metody distribuované trénování TensorFlow:

* [Na základě MPI](https://www.open-mpi.org/) distribuované trénování pomocí [Horovod](https://github.com/uber/horovod) framework
* Nativní [distribuované TensorFlow](https://www.tensorflow.org/deploy/distributed) pomocí parametru metody serveru

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) je open-source platforma pro distribuované trénování vypracovanou organizací cccppf Uber. Nabízí Snadná cesta k distribuované úlohy GPU TensorFlow.

Chcete-li použít Horovod, zadejte `mpi` pro `distributed_training` parametr v konstruktoru TensorFlow odhad. Horovod se nainstalují pro použití ve vašem skriptu školení.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',
                      use_gpu=True)
```

### <a name="parameter-server"></a>Server parametrů

Můžete také spustit [nativní distribuované TensorFlow](https://www.tensorflow.org/deploy/distributed), model serveru parametr, který používá. V této metodě trénování napříč clusterem parametr serverů a pracovních procesů. Zaměstnanci vypočítat přechody během cvičení, zatímco parametr servery agregovat přechody.

Chcete-li použít parametr metody serveru, zadejte `ps` pro `distributed_training` parametr v konstruktoru TensorFlow odhad.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='ps',
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="note-on-tfconfig"></a>Poznámka: na `TF_CONFIG`

Budete také potřebovat síťové adresy a porty u clusteru [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), takže se nastaví Azure Machine Learning `TF_CONFIG` proměnnou prostředí za vás.

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

Pro TensorFlow na vysoké úrovni [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) rozhraní API, TensorFlow, provede analýzu to `TF_CONFIG` proměnné a sestavení clusteru spec za vás.

TensorFlow na nižší úrovni základní rozhraní API pro trénování, analyzovat `TF_CONFIG` proměnné a sestavení `tf.train.ClusterSpec` ve vašem kódu školení. V [v tomto příkladu](https://aka.ms/aml-notebook-tf-ps), provedli byste proto v **cvičný skript** následujícím způsobem:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="next-steps"></a>Další postup

V tomto článku školení a zaregistrován TensorFlow modelu ve službě Azure Machine Learning. Informace o nasazení modelu najdete dál náš model nasazení článkem.

> [!div class="nextstepaction"]
> [Jak a kde nasadit modely](how-to-deploy-and-where.md)

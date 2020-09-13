---
title: Výuka a nasazení modelu TensorFlow
titleSuffix: Azure Machine Learning
description: Naučte se, jak spouštět školicí skripty TensorFlow ve velkém měřítku pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c25464444abe8b4bc274f71618c62a751143d594
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89648275"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Vytvářejte TensorFlow model hloubkového učení ve velkém měřítku pomocí Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak spustit školicí skripty [TensorFlow](https://www.tensorflow.org/overview) ve velkém měřítku pomocí třídy [estimator TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py&preserve-view=true) v Azure Machine Learning. V tomto příkladu se navlacích a registruje TensorFlow model pro klasifikaci psaných číslic pomocí neuronové sítě (DNN).

Bez ohledu na to, jestli vyvíjíte model TensorFlow z provozu nebo přenášíte [existující model](how-to-deploy-existing-model.md) do cloudu, můžete použít Azure Machine Learning k horizontálnímu navýšení kapacity Open-Source školicích úloh pro sestavování, nasazování, správu a monitorování modelů produkčního prostředí.

Přečtěte si další informace o službě [hloubkového učení vs Machine Learning](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Požadavky

Spusťte tento kód v jednom z těchto prostředí:

 - Azure Machine Learning výpočetní instance – nepotřebujete žádné soubory ke stažení nebo instalaci

     - Dokončete [kurz: instalační prostředí a pracovní prostor](tutorial-1st-experiment-sdk-setup.md) pro vytvoření vyhrazeného serveru poznámkového bloku předem načteného se sadou SDK a s ukázkovým úložištěm.
    - V části ukázek hloubkového učení na serveru poznámkového bloku najděte dokončený a rozbalený Poznámkový blok tak, že přejdete do tohoto adresáře: How-to-tensorflow---- **> ml-framework > > deployment >** 
 
 - Váš vlastní server Jupyter Notebook

    - [Nainstalujte sadu Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).
    - [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
    - [Stažení ukázkových souborů skriptu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` ani `utils.py`
     
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
from azureml.train.dnn import TensorFlow
```

### <a name="initialize-a-workspace"></a>Inicializovat pracovní prostor

[Azure Machine Learning pracovní prostor](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte. V sadě Python SDK máte přístup k artefaktům pracovního prostoru vytvořením [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) objektu.

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

### <a name="create-a-file-dataset"></a>Vytvoření datové sady souborů

`FileDataset`Objekt odkazuje na jeden nebo více souborů v úložišti dat pracovního prostoru nebo veřejných adresách URL. Soubory mohou být libovolného formátu a třída poskytuje možnost stahovat nebo připojovat soubory do výpočtů. Vytvořením vytvoříte `FileDataset` odkaz na umístění zdroje dat. Pokud jste v sadě dat použili jakékoli transformace, budou uloženy i v datové sadě. Data zůstanou ve svém stávajícím umístění, takže se neúčtují žádné dodatečné náklady na úložiště. Další informace najdete v průvodci [vytvořením](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) `Dataset` balíčku.

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

Pomocí `register()` metody zaregistrujete datovou sadu do svého pracovního prostoru, aby bylo možné je sdílet s ostatními, znovu použít v různých experimentech a v rámci školicího skriptu, na které se odkazuje pomocí názvu.

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Další informace o výpočetních cílech najdete v článku [co je cílový výpočetní cíl](concept-compute-target.md) .

## <a name="create-a-tensorflow-estimator"></a>Vytvoření TensorFlow Estimator

[TensorFlow Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py&preserve-view=true) poskytuje jednoduchý způsob, jak spustit školicí úlohu TensorFlow na cílovém výpočetním cíli.

TensorFlow Estimator je implementován prostřednictvím obecné [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py&preserve-view=true) třídy, kterou lze použít k podpoře libovolného rozhraní. Další informace o školicích modelech pomocí obecného Estimator najdete v tématu [výuka modelů s Azure Machine Learning pomocí Estimator](how-to-train-ml-models.md) .

Pokud váš školicí skript potřebuje ke spuštění další balíčky PIP nebo Conda, můžete mít balíčky nainstalované ve výsledné imagi Docker tím, že předáte jejich názvy pomocí `pip_packages` argumentů a `conda_packages` .


> [!WARNING]
> Azure Machine Learning spouští školicí skripty zkopírováním celého zdrojového adresáře. Pokud máte citlivá data, která nechcete nahrávat, použijte [soubor. Ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) nebo ho nezahrnujte do zdrojového adresáře. Místo toho přístup k datům pomocí [úložiště](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true)dat.


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
> Do třídy Estimator Tensorflow se přidala podpora pro **Tensorflow 2,0** . Další informace najdete v tomto [blogovém příspěvku](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/).

Další informace o přizpůsobení prostředí Pythonu najdete v tématu [vytváření a Správa prostředí pro školení a nasazení](how-to-use-environments.md). 

## <a name="submit-a-run"></a>Odeslat běh

[Objekt Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) poskytuje rozhraní k historii spuštění, když je úloha spuštěná a po jejím dokončení.

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

Po proškolení modelu ho můžete zaregistrovat do svého pracovního prostoru. Registrace modelu umožňuje ukládat a modelovat vaše modely do svého pracovního prostoru, aby bylo možné zjednodušit [správu modelů a nasazení](concept-model-management-and-deployment.md). Zadáním parametrů `model_framework` , `model_framework_version` a `resource_configuration` nebudete mít k dispozici nasazení modelu bez kódu. To vám umožní přímo nasadit model jako webovou službu z registrovaného modelu a `ResourceConfiguration` objekt definuje výpočetní prostředek pro webovou službu.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Místní kopii modelu můžete také stáhnout pomocí objektu run. Ve školicím skriptu `mnist-tf.py` objekt TensorFlow spořiče uchovává model do místní složky (místní k cíli výpočtů). Kopii můžete stáhnout pomocí objektu spustit.

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

[`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py&preserve-view=true)Estimator také podporuje distribuované školení v rámci clusterů procesoru a GPU. Můžete snadno spouštět distribuované úlohy TensorFlow a Azure Machine Learning bude orchestrace spravovat za vás.

Azure Machine Learning podporuje dvě metody distribuovaného školení v TensorFlow:

- Distribuované školení [založené na MPI](https://www.open-mpi.org/) s využitím architektury [Horovod](https://github.com/uber/horovod)
- Nativní [distribuované TensorFlow](https://www.tensorflow.org/deploy/distributed) pomocí metody serveru parametrů

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) je open-source platforma pro distribuované školení vyvinuté pomocí Uber. Nabízí snadnou cestu k distribuovaným úlohám TensorFlow GPU.

Chcete-li použít Horovod, zadejte [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true) objekt pro `distributed_training` parametr v konstruktoru TensorFlow. Tento parametr zajišťuje, že se knihovna Horovod nainstaluje pro použití ve školicím skriptu.

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

### <a name="parameter-server"></a>Server parametrů

Můžete také spustit [nativní distribuované TensorFlow](https://www.tensorflow.org/deploy/distributed), které používají model serveru parametrů. V této metodě budete vlakovat v clusteru parametrů serverů a pracovních procesů. Pracovní procesy vypočítávají přechody během školení, zatímco servery parametrů agreguje přechody.

Chcete-li použít metodu serveru parametrů, zadejte [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py&preserve-view=true) objekt pro `distributed_training` parametr v konstruktoru TensorFlow.

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

#### <a name="define-cluster-specifications-in-tf_config"></a>Definice specifikací clusteru v ' TF_CONFIG '

Pro můžete také potřebovat síťové adresy a porty clusteru [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec) , takže Azure Machine Learning `TF_CONFIG` pro vás nastaví proměnnou prostředí.

`TF_CONFIG`Proměnná prostředí je řetězec JSON. Tady je příklad proměnné pro server parametrů:

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

V případě rozhraní API na vysoké úrovni TensorFlow [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) TensorFlow analyzuje `TF_CONFIG` proměnnou a vytvoří pro vás specifikaci clusteru.

Pro základní rozhraní API nižší úrovně TensorFlow pro školení, analyzujte `TF_CONFIG` proměnnou a sestavte `tf.train.ClusterSpec` kód školení.

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

Model, který jste právě zaregistrovali, lze nasadit stejným způsobem jako jakýkoli jiný registrovaný model v Azure Machine Learning, bez ohledu na to, který Estimator jste použili pro školení. Postup nasazení obsahuje část týkající se registrace modelů, ale můžete přeskočit přímo na [Vytvoření výpočetního cíle](how-to-deploy-and-where.md#choose-a-compute-target) pro nasazení, protože již máte registrovaný model.

## <a name="preview-no-code-model-deployment"></a>Tisk Nasazení modelu bez kódu

Místo tradičního postupu nasazení můžete také použít funkci nasazení bez kódu (Preview) pro Tensorflow. Registrací modelu, jak je uvedeno výše, `model_framework` pomocí `model_framework_version` parametrů, a `resource_configuration` můžete jednoduše použít `deploy()` statickou funkci pro nasazení modelu.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Úplný [postup](how-to-deploy-and-where.md) pokrývá nasazení v Azure Machine Learning s větší hloubkou.

## <a name="next-steps"></a>Další kroky

V tomto článku jste si naučili a zaregistrovali model TensorFlow a seznámili jste se s možnostmi nasazení. Další informace o Azure Machine Learning najdete v těchto článcích.

* [Sledovat metriky spuštění během školení](how-to-track-experiments.md)
* [Ladění hyperparametrů](how-to-tune-hyperparameters.md)
* [Referenční architektura distribuovaného školení pro hloubkové učení v Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)

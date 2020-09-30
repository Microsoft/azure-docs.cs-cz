---
title: Výuka a nasazení modelu TensorFlow
titleSuffix: Azure Machine Learning
description: Naučte se, jak spouštět školicí skripty TensorFlow ve velkém měřítku pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 618889f40816ec8ccc64487778bf1f6fbdd3b886
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536541"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>TensorFlow se škálováním modelů pomocí Azure Machine Learning

V tomto článku se naučíte, jak spustit školicí skripty [TensorFlow](https://www.tensorflow.org/overview) ve velkém měřítku pomocí Azure Machine Learning.

V tomto příkladu se navlacích a registruje TensorFlow model pro klasifikaci psaných číslic pomocí neuronové sítě (DNN).

Bez ohledu na to, jestli vyvíjíte model TensorFlow z provozu nebo přenášíte [existující model](how-to-deploy-existing-model.md) do cloudu, můžete použít Azure Machine Learning k horizontálnímu navýšení kapacity Open-Source školicích úloh pro sestavování, nasazování, správu a monitorování modelů produkčního prostředí.

## <a name="prerequisites"></a>Předpoklady

Spusťte tento kód v jednom z těchto prostředí:

 - Azure Machine Learning výpočetní instance – nepotřebujete žádné soubory ke stažení nebo instalaci

     - Dokončete [kurz: instalační prostředí a pracovní prostor](tutorial-1st-experiment-sdk-setup.md) pro vytvoření vyhrazeného serveru poznámkového bloku předem načteného se sadou SDK a s ukázkovým úložištěm.
    - Ve složce s ukázkami hloubkového učení na serveru poznámkového bloku najděte dokončený a rozbalený Poznámkový blok přechodem do tohoto adresáře: How-to-tensorflow------ **> ml-framework > > výuka – tensorflow** . 
 
 - Váš vlastní server Jupyter Notebook

    - [Nainstalujte sadu Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) (>= 1.15.0).
    - [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).
    - [Stažení ukázkových souborů skriptu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` ani `utils.py`
     
    Dokončenou [Jupyter notebook verzi](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) tohoto průvodce najdete na stránce ukázek na GitHubu. Poznámkový blok obsahuje rozšířené oddíly, které pokrývají inteligentní ladění parametrů, nasazení modelů a widgety poznámkových bloků.

## <a name="set-up-the-experiment"></a>Nastavení experimentu

Tato část nastavuje experimenty školení načtením požadovaných balíčků Pythonu, inicializací pracovního prostoru, vytvořením cíle výpočtů a definováním školicího prostředí.

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

[Azure Machine Learning pracovní prostor](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte. V sadě Python SDK máte přístup k artefaktům pracovního prostoru vytvořením [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) objektu.

Vytvořte objekt pracovního prostoru ze `config.json` souboru vytvořeného v [části požadavky](#prerequisites).

```Python
ws = Workspace.from_config()
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
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>Vytvořit cíl výpočtů

Vytvořte výpočetní cíl pro úlohu TensorFlow, na které se má spustit. V tomto příkladu vytvoříte výpočetní cluster Azure Machine Learning s podporou GPU.

```Python
cluster_name = "gpu-cluster"

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

### <a name="define-your-environment"></a>Definice prostředí

Pokud chcete definovat [prostředí](concept-environments.md) Azure ml, které zapouzdřuje závislosti školicího skriptu, můžete buď definovat vlastní prostředí, použití a prostředí Azure ml.

#### <a name="create-a-custom-environment"></a>Vytvoření vlastního prostředí

Definujte prostředí Azure ML, které zapouzdřuje závislosti školicího skriptu.

Nejprve v souboru YAML definujte závislosti conda; v tomto příkladu je soubor pojmenován `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
```

Vytvořte prostředí Azure ML z této specifikace prostředí conda. Prostředí se zabalí do kontejneru Docker za běhu.

Ve výchozím nastavení, pokud není zadaná žádná základní image, Azure ML použije image procesoru `azureml.core.runconfig.DEFAULT_CPU_IMAGE` jako základní image. Vzhledem k tomu, že tento příklad spustí školení v clusteru GPU, budete muset zadat základní image GPU, která má potřebné ovladače a závislosti GPU. Azure ML udržuje sadu základních imagí publikovaných v Microsoft Container Registry (MCR), které můžete použít, další informace najdete v úložišti GitHub [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) .

```python
from azureml.core import Environment

tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Volitelně můžete zachytit všechny závislosti přímo ve vlastní imagi Docker nebo v souboru Dockerfile a vytvořit prostředí z něj. Další informace najdete v tématu [výuka s vlastní imagí](how-to-train-with-custom-image.md).

Další informace o vytváření a používání prostředí najdete v tématu [vytváření a používání softwarových prostředí v Azure Machine Learning](how-to-use-environments.md).

#### <a name="use-a-curated-environment"></a>Použití spravovaného prostředí
V případě potřeby poskytuje Azure ML předem vytvořená a poduspořádaná prostředí, pokud nechcete vytvořit vlastní image. Azure ML obsahuje několik PROCESORových a procesorových prostředí GPU pro TensorFlow, která odpovídají různým verzím TensorFlow. Další informace najdete [tady](resource-curated-environments.md).

Pokud chcete použít konkrétní prostředí, můžete místo toho spustit následující příkaz:

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

Pokud chcete zobrazit balíčky zahrnuté ve spravovaném prostředí, můžete na disk zapsat závislosti conda:
```python
tf_env.save_to_directory(path=curated_env_name)
```

Ujistěte se, že ve spravovaném prostředí jsou všechny závislosti vyžadované školicím skriptem. V takovém případě budete muset upravit prostředí tak, aby obsahovalo chybějící závislosti. Všimněte si, že pokud je prostředí upraveno, budete ho muset dát novému názvu, protože předpona ' AzureML ' je vyhrazena pro prostředí, která jsou v něm uspořádaná. Pokud jste změnili soubor conda závislosti YAML, můžete z něj vytvořit nové prostředí s novým názvem, třeba:
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

Pokud jste místo toho změnili objekt s podmnožinou prostředí přímo, můžete toto prostředí klonovat pomocí nového názvu:
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
```

## <a name="configure-and-submit-your-training-run"></a>Konfigurace a odeslání školicích běhů

### <a name="create-a-scriptrunconfig"></a>Vytvoření ScriptRunConfig

Vytvořte objekt [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) a zadejte podrobnosti o konfiguraci školicí úlohy, včetně vašeho školicího skriptu, prostředí, které se má použít, a výpočetní cíl, který se má spustit. Jakékoli argumenty školicího skriptu budou předány prostřednictvím příkazového řádku, pokud je zadáno v `arguments` parametru.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 64,
        '--first-layer-neurons', 256,
        '--second-layer-neurons', 128,
        '--learning-rate', 0.01]

src = ScriptRunConfig(source_directory=script_folder,
                      script='tf_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
```

> [!WARNING]
> Azure Machine Learning spouští školicí skripty zkopírováním celého zdrojového adresáře. Pokud máte citlivá data, která nechcete nahrávat, použijte [soubor. Ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) nebo ho nezahrnujte do zdrojového adresáře. Místo toho ke svým datům dostanete přístup pomocí [datové sady](how-to-train-with-datasets.md)Azure ml.

Další informace o konfiguraci úloh pomocí ScriptRunConfig najdete v tématu [Konfigurace a odeslání školicích běhů](how-to-set-up-training-targets.md).

> [!WARNING]
> Pokud jste dříve používali TensorFlow Estimator ke konfiguraci školicích úloh TensorFlow, Upozorňujeme, že odhady bude v budoucí verzi sady Azure ML SDK zastaralá. S Azure ML SDK >= 1.15.0, ScriptRunConfig je doporučeným způsobem konfigurace školicích úloh, včetně těch, které používají rozhraní DL.

### <a name="submit-a-run"></a>Odeslat běh

[Objekt Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) poskytuje rozhraní k historii spuštění, když je úloha spuštěná a po jejím dokončení.

```Python
run = Experiment(workspace=ws, name='tf-mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>Co se stane při spuštění
Po spuštění se spustí v následujících fázích:

- **Příprava**: image Docker se vytvoří podle definovaného prostředí. Obrázek se nahraje do registru kontejneru v pracovním prostoru a v mezipaměti pro pozdější spuštění. Protokoly se také streamují do historie spuštění a dají se zobrazit ke sledování průběhu. Pokud je místo toho zadáno spravované prostředí, bude použit obrázek uložený v mezipaměti, který bude toto prostředí obsahovat.

- **Škálování**: cluster se pokusí o horizontální navýšení kapacity, pokud Batch AI cluster vyžaduje více uzlů pro spuštění běhu, než je aktuálně k dispozici.

- **Spuštěno**: všechny skripty ve složce skriptu se nahrají do cílového výpočetního prostředí, úložiště dat se připojí nebo zkopírují a `script` spustí se. Výstupy z stdout a složky **./logs** se streamují do historie spuštění a dají se použít k monitorování běhu.

- **Následné zpracování**: složka **./Outputs** se v běhu kopíruje do historie spuštění.

## <a name="register-or-download-a-model"></a>Registrace nebo stažení modelu

Po proškolení modelu ho můžete zaregistrovat do svého pracovního prostoru. Registrace modelu umožňuje ukládat a modelovat vaše modely do svého pracovního prostoru, aby bylo možné zjednodušit [správu modelů a nasazení](concept-model-management-and-deployment.md). Volitelné: zadáním parametrů `model_framework` , `model_framework_version` , a `resource_configuration` , nasazení modelu bez kódu bude k dispozici. To vám umožní přímo nasadit model jako webovou službu z registrovaného modelu a `ResourceConfiguration` objekt definuje výpočetní prostředek pro webovou službu.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Místní kopii modelu můžete také stáhnout pomocí objektu run. Ve školicím skriptu `tf_mnist.py` objekt TensorFlow spořiče uchovává model do místní složky (místní k cíli výpočtů). Kopii můžete stáhnout pomocí objektu spustit.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>Distribuované trénování

Azure Machine Learning podporuje také distribuované úlohy TensorFlow s více uzly, abyste mohli škálovat školicí úlohy. Můžete snadno spouštět distribuované úlohy TensorFlow a Azure ML bude spravovat orchestraci za vás.

Azure ML podporuje spouštění distribuovaných úloh TensorFlow s integrovaným rozhraním API pro distribuované školení Horovod a TensorFlow.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) je open source, což snižuje rámec pro distribuované školení vyvinuté pomocí Uber. Nabízí snadnou cestu k psaní distribuovaného TensorFlow kódu pro školení.

Školicí kód bude nutné instrumentovat s Horovod pro distribuované školení. Další informace o použití Horovod s TensorFlow najdete v dokumentaci Horovod:

Další informace o použití Horovod s TensorFlow najdete v dokumentaci Horovod:

* [Horovod s TensorFlow](https://github.com/horovod/horovod/blob/master/docs/tensorflow.rst)
* [Horovod s rozhraním API Keras TensorFlow](https://github.com/horovod/horovod/blob/master/docs/keras.rst)

Navíc se ujistěte, že vaše školicí prostředí obsahuje balíček **horovod** . Pokud používáte TensorFlowé prostředí, horovod už je součástí jedné ze závislostí. Pokud používáte vlastní prostředí, ujistěte se, že je zahrnutá závislost horovod, například:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
  - horovod==0.19.5
```

Aby bylo možné spustit distribuovanou úlohu pomocí MPI/Horovod v Azure ML, je nutné zadat [MpiConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py&preserve-view=true) do `distributed_job_config` parametru konstruktoru ScriptRunConfig. Níže uvedený kód nakonfiguruje distribuovanou úlohu se dvěma uzly se spuštěným jedním procesem na jeden uzel. Pokud chcete spustit více procesů na jeden uzel (tj. Pokud má SKU clusteru více GPU), zadejte také `process_count_per_node` parametr v MpiConfiguration (výchozí nastavení je `1` ).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='tf_horovod_word2vec.py',
                      arguments=['--input_data', dataset.as_mount()],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Úplný kurz pro spouštění distribuovaných TensorFlow s Horovod v Azure ML najdete v tématu [Distributed TensorFlow with Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/distributed-tensorflow-with-horovod).

### <a name="tfdistribute"></a>TF. Distribute

Pokud používáte [nativní distribuované TensorFlow](https://www.tensorflow.org/guide/distributed_training) v kódu školení, např. TensorFlow 2. x `tf.distribute.Strategy` rozhraní API, můžete také spustit distribuovanou úlohu prostřednictvím Azure ml. 

Provedete to tak, že zadáte [TensorflowConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py&preserve-view=true) do `distributed_job_config` parametru konstruktoru ScriptRunConfig. Pokud používáte `tf.distribute.experimental.MultiWorkerMirroredStrategy` , zadejte `worker_count` v TensorflowConfiguration odpovídající počtu uzlů pro vaši úlohu školení.

```python
import os
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import TensorflowConfiguration

distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

model_path = os.path.join("./outputs", "keras-model")

src = ScriptRunConfig(source_directory=source_dir,
                      script='train.py',
                      arguments=["--epochs", 30, "--model-dir", model_path],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=distr_config)
```

V TensorFlow `TF_CONFIG` je proměnná prostředí nutná pro školení na více počítačích. Azure ML `TF_CONFIG` před spuštěním školicího skriptu nakonfiguruje a nastaví proměnnou odpovídajícím způsobem pro každý pracovní proces. `TF_CONFIG`Pokud potřebujete přes, můžete získat přístup ze školicího skriptu `os.environ['TF_CONFIG']` .

Příklad struktury `TF_CONFIG` sady pro hlavní pracovní uzel:
```JSON
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

Pokud váš školicí skript používá strategii serveru parametrů pro distribuované školení, tj. pro starší verze TensorFlow 1. x, bude také potřeba zadat počet serverů parametrů, které mají být v úloze použity, např. `distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)` .

## <a name="deploy-a-tensorflow-model"></a>Nasazení modelu TensorFlow

Postup nasazení obsahuje část týkající se registrace modelů, ale můžete přeskočit přímo na [Vytvoření výpočetního cíle](how-to-deploy-and-where.md#choose-a-compute-target) pro nasazení, protože již máte registrovaný model.

### <a name="preview-no-code-model-deployment"></a>Tisk Nasazení modelu bez kódu

Místo tradičního postupu nasazení můžete také použít funkci nasazení bez kódu (Preview) pro TensorFlow. Registrací modelu, jak je uvedeno výše, `model_framework` pomocí `model_framework_version` parametrů, a `resource_configuration` můžete jednoduše použít `deploy()` statickou funkci pro nasazení modelu.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Úplný [postup](how-to-deploy-and-where.md) pokrývá nasazení v Azure Machine Learning s větší hloubkou.

## <a name="next-steps"></a>Další kroky

V tomto článku jste si naučili a zaregistrovali model TensorFlow a seznámili jste se s možnostmi nasazení. Další informace o Azure Machine Learning najdete v těchto článcích.

* [Sledovat metriky spuštění během školení](how-to-track-experiments.md)
* [Ladění hyperparametrů](how-to-tune-hyperparameters.md)
* [Referenční architektura distribuovaného školení pro hloubkové učení v Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)

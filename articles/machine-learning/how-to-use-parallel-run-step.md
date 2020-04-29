---
title: Spuštění dávkového předpovědiu pro velké objemy dat
titleSuffix: Azure Machine Learning
description: Naučte se, jak získat asynchronně odvozené informace o velkých objemech dat pomocí ParallelRunStep v Azure Machine Learning. ParallelRunStep poskytuje možnosti paralelního zpracování vycházející z okna a optimalizuje při použití s vysokou propustností, započetím požáru a zapomenutí pro případy využití velkých objemů dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: vaidyas
author: vaidya-s
ms.date: 01/15/2020
ms.custom: Ignite2019
ms.openlocfilehash: 3d283d1094336b928869aa281b4a640d7a62dd94
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "79477183"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Spuštění dávkového odvozování pro velké objemy dat pomocí Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Naučte se, jak zpracovávat velké objemy dat asynchronně a paralelně pomocí Azure Machine Learning. Funkce ParallelRunStep popsané tady je ve verzi Public Preview. Je to vysoce výkonná a vysoká propustnost pro generování odvození a zpracování dat. Poskytuje asynchronní možnosti mimo pole.

S ParallelRunStep je jednoduché škálovat offline oddělení na velké clustery počítačů na terabajtech produkčních dat, což vede k lepší produktivitě a optimalizaci nákladů.

V tomto článku se seznámíte s následujícími úlohami:

> * Vytvořte vzdálený výpočetní prostředek.
> * Zapište vlastní skript pro odvození.
> * Vytvořte [kanál machine learningu](concept-ml-pipelines.md) , který zaregistruje předem vyškolený model klasifikace imagí na základě datové sady [mnist ručně zapsaných](https://publicdataset.azurewebsites.net/dataDetail/mnist/) . 
> * Pomocí modelu spusťte odvozování dávky na ukázkových imagí dostupných v účtu úložiště Azure Blob. 

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

* Průvodce rychlým startem vám umožní absolvovat [kurz instalace](tutorial-1st-experiment-sdk-setup.md) , pokud ještě nemáte virtuální počítač s Azure Machine Learning pracovním prostorem nebo notebookem. 

* Informace o [tom, jak](how-to-configure-environment.md) spravovat vlastní prostředí a závislosti, najdete v tématu Průvodce konfigurací vlastního prostředí. Spusťte `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` ve svém prostředí, aby se stáhly nezbytné závislosti.

## <a name="set-up-machine-learning-resources"></a>Nastavení prostředků strojového učení

Následující akce nastaví prostředky, které potřebujete ke spuštění kanálu odvození dávky:

- Vytvořte úložiště dat, které odkazuje na kontejner objektů BLOB s obrázky, které mají být odvozeny.
- Nastavte odkazy na data jako vstupy a výstupy pro krok kanálu pro odvození dávky.
- Nastavte výpočetní cluster tak, aby spouštěl krok odvození dávky.

### <a name="create-a-datastore-with-sample-images"></a>Vytvoření úložiště dat s ukázkovými obrázky

Získejte zkušební sadu MNIST ručně zapsaných z veřejného kontejneru `sampledata` objektů BLOB v účtu s názvem. `pipelinedata` Vytvořte úložiště dat s názvem `mnist_datastore`, které odkazuje na tento kontejner. V následujícím volání do `register_azure_blob_container`nastavte `overwrite` příznak pro `True` přepsání libovolného úložiště dat, které bylo dříve vytvořeno s tímto názvem. 

Tento krok můžete změnit tak, aby odkazoval na kontejner objektů BLOB tím, že poskytuje vaše `datastore_name`vlastní `container_name`hodnoty pro `account_name`, a.

```python
from azureml.core import Datastore
from azureml.core import Workspace

# Load workspace authorization details from config.json
ws = Workspace.from_config()

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Potom zadejte výchozí úložiště dat pracovního prostoru jako výstupní úložiště dat. Použijete ho k odvození výstupu.

Při vytváření pracovního prostoru jsou [soubory](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) Azure a [BLOB Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) ve výchozím nastavení připojené k pracovnímu prostoru. Služba soubory Azure je výchozím úložištěm dat pro pracovní prostor, ale můžete také použít úložiště objektů BLOB jako úložiště dat. Další informace najdete v tématu [Možnosti služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-inputs-and-outputs"></a>Konfigurace vstupů a výstupů dat

Nyní potřebujete nakonfigurovat vstupy a výstupy dat, včetně:

- Adresář, který obsahuje vstupní obrázky.
- Adresář, ve kterém je uložen předučený model.
- Adresář, který obsahuje popisky.
- Adresář pro výstup.

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)je třída pro zkoumání, transformaci a správu dat v Azure Machine Learning. Tato třída má dva typy: [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) a [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py). V tomto příkladu použijete `FileDataset` jako vstupy pro krok kanálu odvození dávky. 

> [!NOTE] 
> `FileDataset`podpora ve službě Batch pro odvození se teď omezuje na úložiště objektů BLOB v Azure. 

Můžete také odkazovat na jiné datové sady ve vlastním skriptu pro odvození. Můžete ho například použít pro přístup k popiskům ve skriptu pro označování obrázků pomocí `Dataset.register` a. `Dataset.get_by_name`

Další informace o Azure Machine Learning datových sadách najdete v tématu [Vytvoření a přístup k datovým sadám (Preview)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)objekty se používají k přenosu mezilehlých dat mezi jednotlivými kroky kanálu. V tomto příkladu je použit pro odvození výstupů.

```python
from azureml.core.dataset import Dataset

mnist_ds_name = 'mnist_sample_data'

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
registered_mnist_ds = input_mnist_ds.register(ws, mnist_ds_name, create_new_version=True)
named_mnist_ds = registered_mnist_ds.as_named_input(mnist_ds_name)

output_dir = PipelineData(name="inferences", 
                          datastore=def_data_store, 
                          output_path_on_compute="mnist/results")
```

### <a name="set-up-a-compute-target"></a>Nastavení cíle výpočtů

V Azure Machine Learning *výpočetní* prostředí (nebo *target COMPUTE*) odkazuje na počítače nebo clustery, které provádějí výpočetní kroky v kanálu Machine Learning. Spusťte následující kód, který vytvoří [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) cíl na základě procesoru.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

## <a name="prepare-the-model"></a>Příprava modelu

[Stáhněte si předem vyškolený model klasifikace imagí](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)a pak ho rozbalte do `models` adresáře.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

Pak model zaregistrujte do svého pracovního prostoru, aby byl dostupný pro váš vzdálený výpočetní prostředek.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Zápis skriptu pro odvození

>[!Warning]
>Následující kód je pouze ukázka, kterou používá [ukázkový Poznámkový blok](https://aka.ms/batch-inference-notebooks) . Pro svůj scénář budete muset vytvořit vlastní skript.

Skript *musí obsahovat* dvě funkce:
- `init()`: Tuto funkci použijte pro veškerou nákladný nebo běžnou přípravu pro pozdější odvození. Můžete ji například použít k načtení modelu do globálního objektu. Tato funkce bude volána pouze jednou na začátku procesu.
-  `run(mini_batch)`: Funkce se spustí pro každou `mini_batch` instanci.
    -  `mini_batch`: Krok paralelního spuštění vyvolá metodu Run a předá jako argument metody metodu list nebo PANDAS dataframe. Každá položka v min_batch bude – cesta k souboru, pokud je vstupem datová sada, PANDAS dataframe, pokud je vstupem TabularDataset.
    -  `response`: metoda Run () by měla vracet PANDAS dataframe nebo Array. Pro append_row output_action jsou tyto vrácené prvky připojeny do společného výstupního souboru. V případě summary_only se obsah prvků ignoruje. U všech výstupních akcí každý vrácený element Output označuje jedno úspěšné spuštění vstupního prvku ve vstupní Mini-Batch. Měli byste se ujistit, že je ve výsledku spuštění k dispozici dostatek dat pro mapování vstupu na výsledek spuštění. Výstup spuštění se zapíše do výstupního souboru a nebude zaručit, že bude v pořádku, abyste ho namapovali na vstup, měli byste použít nějaký klíč ve výstupu.

```python
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://aka.ms/batch-inference-notebooks)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

### <a name="how-to-access-other-files-in-source-directory-in-entry_script"></a>Přístup k jiným souborům ve zdrojovém adresáři v entry_script

Pokud máte ve stejném adresáři jako svůj vstupní skript jiný soubor nebo složku, můžete na něj odkazovat pomocí hledání aktuálního pracovního adresáře.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>Sestavování a spouštění kanálu obsahujícího ParallelRunStep

Teď máte všechno, co potřebujete k vytvoření kanálu.

### <a name="prepare-the-run-environment"></a>Příprava prostředí pro spuštění

Nejdřív zadejte závislosti pro váš skript. Tento objekt použijete později při vytváření kroku kanálu.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
batch_env.spark.precache_packages = False
```

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>Zadejte parametry pro krok kanálu odvození dávky.

`ParallelRunConfig`je hlavní konfigurací nově zavedené instance odvození `ParallelRunStep` dávky v rámci kanálu Azure Machine Learning. Použijete ho k zabalení skriptu a ke konfiguraci nezbytných parametrů, včetně všech následujících parametrů:
- `entry_script`: Uživatelský skript jako cesta k místnímu souboru, který bude spuštěn paralelně na více uzlech. Pokud `source_directory` je k dispozici, použijte relativní cestu. V opačném případě použijte jakoukoli cestu, která je přístupná v počítači.
- `mini_batch_size`: Velikost malé dávky předaná jednomu `run()` volání. (volitelné; výchozí hodnota je `10` soubory pro datovou sadu souborů `1MB` a pro TabularDataset.)
    - `FileDataset`V případě je to počet souborů s minimální hodnotou `1`. Můžete zkombinovat více souborů do jedné Mini-dávky.
    - Pro `TabularDataset`je to velikost dat. Příklady hodnot jsou `1024`, `1024KB`, `10MB`a `1GB`. Doporučená hodnota je `1MB`. Ze `TabularDataset` zkrácené dávky nebude nikdy mezi hranicemi souborů. Například pokud máte soubory. csv s různými velikostmi, nejmenší soubor je 100 KB a největší je 10 MB. Pokud nastavíte `mini_batch_size = 1MB`, budou se soubory s velikostí menší než 1 MB považovat za jednu miniickou dávku. Soubory o velikosti větší než 1 MB budou rozděleny do několika Mini-dávek.
- `error_threshold`: Počet selhání záznamu `TabularDataset` a selhání souborů pro `FileDataset` , které by měly být během zpracování ignorovány. Pokud se počet chyb pro celý vstup překročí k této hodnotě, bude úloha přerušena. Prahová hodnota chyby je pro celý vstup a nikoli pro jednotlivé Mini-dávky odeslané do `run()` metody. Rozsah je `[-1, int.max]`. `-1` Část indikuje ignorování všech selhání během zpracování.
- `output_action`: Jedna z následujících hodnot indikuje, jak bude uspořádán výstup:
    - `summary_only`: Uživatelský skript uloží výstup. `ParallelRunStep`použije výstup pouze pro výpočet prahové hodnoty chyby.
    - `append_row`: Pro všechny vstupní soubory se ve výstupní složce vytvoří pouze jeden soubor pro připojení všech výstupů oddělených čárou. Název souboru bude `parallel_run_step.txt`.
- `source_directory`: Cesty ke složkám, které obsahují všechny soubory, které mají být spuštěny na cílovém výpočetním cíli (volitelné).
- `compute_target`: Podporuje `AmlCompute` se jenom.
- `node_count`: Počet výpočetních uzlů, které se mají použít ke spuštění skriptu uživatele.
- `process_count_per_node`: Počet procesů na uzel.
- `environment`: Definice prostředí Pythonu. Můžete ji nakonfigurovat tak, aby používala existující prostředí Pythonu, nebo nastavit dočasné prostředí pro experiment. Definice je také zodpovědná za nastavení požadovaných závislostí aplikace (volitelné).
- `logging_level`: Podrobnosti protokolu. Hodnoty při zvyšování podrobností jsou: `WARNING`, `INFO`a. `DEBUG` (volitelné; výchozí hodnota je `INFO`)
- `run_invocation_timeout`: Časový `run()` limit volání metody v sekundách. (volitelné; výchozí hodnota je `60`)

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory=scripts_folder,
    entry_script="digit_identification.py",
    mini_batch_size="5",
    error_threshold=10,
    output_action="append_row",
    environment=batch_env,
    compute_target=compute_target,
    node_count=4)
```

### <a name="create-the-pipeline-step"></a>Vytvoření kroku kanálu

Vytvořte krok kanálu pomocí skriptu, konfigurace prostředí a parametrů. Určete výpočetní cíl, který jste už ke svému pracovnímu prostoru připojili jako cíl provádění skriptu. Použijte `ParallelRunStep` k vytvoření kroku kanálu odvození dávky, který převezme všechny následující parametry:
- `name`: Název kroku s následujícími omezeními pro pojmenovávání: jedinečné, 3-32 znaky a Regex ^\[a-z\]([-a-Z0-9] * [a-Z0-9])? $.
- `models`: Nula nebo více názvů modelů, které jsou již registrovány v registru Azure Machine Learningho modelu.
- `parallel_run_config`: `ParallelRunConfig` Objekt, jak je definováno výše.
- `inputs`: Jedna nebo více Azure Machine Learning datových sad s jedním typem.
- `output`: `PipelineData` Objekt, který odpovídá výstupnímu adresáři.
- `arguments`: Seznam argumentů předaných uživatelskému skriptu (volitelné).
- `allow_reuse`: Zda by měl krok při spuštění se stejnými nastaveními nebo vstupy znovu použít předchozí výsledky. Pokud je `False`tento parametr, nové spuštění bude pro tento krok vždy vygenerováno během provádění kanálu. (volitelné; výchozí hodnota je `True`.)

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="batch-mnist",
    models=[model],
    parallel_run_config=parallel_run_config,
    inputs=[named_mnist_ds],
    output=output_dir,
    arguments=[],
    allow_reuse=True
)
```

>[!Note]
> Výše uvedený krok závisí na `azureml-contrib-pipeline-steps`tom, jak je popsáno v části [požadavky](#prerequisites). 

### <a name="submit-the-pipeline"></a>Odeslat kanál

Teď kanál spusťte. Nejprve vytvořte [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) objekt pomocí odkazu na pracovní prostor a kroku kanálu, který jste vytvořili. `steps` Parametr je pole kroků. V tomto případě je pro dávkové vyhodnocování k dispozici pouze jeden krok. Chcete-li vytvořit kanály, které mají více kroků, umístěte kroky v tomto poli do pořadí.

Dále pomocí `Experiment.submit()` funkce odešlete kanál ke spuštění.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-parallel-run-job"></a>Monitorování úlohy paralelního spuštění

Dokončení úlohy odvození dávky může trvat dlouhou dobu. Tento příklad sleduje průběh pomocí widgetu Jupyter. Průběh úlohy můžete také spravovat pomocí:

* Azure Machine Learning Studio. 
* Výstup z [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) objektu Console

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>Další kroky

Chcete-li zobrazit tento proces na konci, zkuste [Poznámkový blok pro odvození dávky](https://aka.ms/batch-inference-notebooks). 

Pokyny k ladění a řešení potíží pro ParallelRunStep najdete v tématu [Průvodce postupy](how-to-debug-parallel-run-step.md).

Pokyny k ladění a řešení potíží s kanály najdete v [Průvodci postupy](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]


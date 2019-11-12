---
title: Spuštění dávkového odvozování pro velké objemy dat
titleSuffix: Azure Machine Learning
description: Naučte se, jak získat asynchronně odvozené informace o velkých objemech dat pomocí dávkového odvozování v Azure Machine Learning. Odvození dávky poskytuje možnosti paralelního zpracování vycházející z pole a optimalizuje odvozenou a nezapomenutou odvozování pro případy využití velkých objemů dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: vaidyas
author: vaidya-s
ms.date: 11/04/2019
ms.custom: Ignite2019
ms.openlocfilehash: 4e7ddf7fc7b18d57b8251d4fb8293ed2f6e83d17
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929562"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Spuštění dávkového odvozování pro velké objemy dat pomocí Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto postupu se naučíte, jak získat asynchronní a souběžné zpracování velkých objemů dat pomocí Azure Machine Learning. Funkce odvození dávky, která je popsaná tady, je ve verzi Public Preview. Je to vysoce výkonná a vysoká propustnost pro generování odvození a zpracování dat. Poskytuje asynchronní možnosti mimo pole.

Díky odvození dávky je snadné škálovat offline oddělení na velké clustery počítačů na terabajtech produkčních dat, což má za následek lepší produktivitu a optimalizované náklady.

V tomto postupu se naučíte následující úlohy:

> * Vytvořte vzdálený výpočetní prostředek.
> * Zapište vlastní skript pro odvození.
> * Vytvořte [kanál machine learningu](concept-ml-pipelines.md) , který zaregistruje předem vyškolený model klasifikace imagí na základě datové sady [mnist ručně zapsaných](https://publicdataset.azurewebsites.net/dataDetail/mnist/) . 
> * Pomocí modelu spusťte odvozování dávky na ukázkových imagí dostupných v účtu úložiště Azure Blob. 

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

* Průvodce rychlým startem vám umožní absolvovat [kurz instalace](tutorial-1st-experiment-sdk-setup.md) , pokud ještě nemáte virtuální počítač s Azure Machine Learning pracovním prostorem nebo notebookem. 

* Informace o [tom, jak](how-to-configure-environment.md) spravovat vlastní prostředí a závislosti, najdete v tématu Průvodce konfigurací vlastního prostředí. Pokud chcete stáhnout potřebné závislosti, spusťte `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` ve vašem prostředí.

## <a name="set-up-machine-learning-resources"></a>Nastavení prostředků strojového učení

Následující akce nastaví prostředky, které potřebujete ke spuštění kanálu odvození dávky:

- Vytvořte úložiště dat, které odkazuje na kontejner objektů BLOB s obrázky, které mají být odvozeny.
- Nastavte odkazy na data jako vstupy a výstupy pro krok kanálu pro odvození dávky.
- Nastavte výpočetní cluster tak, aby spouštěl krok odvození dávky.

### <a name="create-a-datastore-with-sample-images"></a>Vytvoření úložiště dat s ukázkovými obrázky

Získejte zkušební sadu MNIST ručně zapsaných z veřejného kontejneru objektů BLOB `sampledata` na účtu s názvem `pipelinedata`. Vytvořte úložiště dat s názvem `mnist_datastore`, který odkazuje na tento kontejner. V následujícím volání `register_azure_blob_container`nastavením příznaku `overwrite` `True` přepíše jakékoli úložiště dat, které bylo dříve vytvořeno s tímto názvem. 

Tento krok můžete změnit tak, aby odkazoval na kontejner objektů BLOB tím, že poskytuje vaše vlastní hodnoty pro `datastore_name`, `container_name`a `account_name`.

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

Při vytváření pracovního prostoru jsou [soubory Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) a [BLOB Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) ve výchozím nastavení připojené k pracovnímu prostoru. Služba soubory Azure je výchozím úložištěm dat pro pracovní prostor, ale můžete také použít úložiště objektů BLOB jako úložiště dat. Další informace najdete v tématu [Možnosti služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-inputs-and-outputs"></a>Konfigurace vstupů a výstupů dat

Nyní potřebujete nakonfigurovat vstupy a výstupy dat, včetně:

- Adresář, který obsahuje vstupní obrázky.
- Adresář, ve kterém je uložen předučený model.
- Adresář, který obsahuje popisky.
- Adresář pro výstup.

`Dataset` je třída pro zkoumání, transformaci a správu dat v Azure Machine Learning. Tato třída má dva typy: `TabularDataset` a `FileDataset`. V tomto příkladu použijete `FileDataset` jako vstupy pro krok kanálu pro odvození dávky. 

> [!NOTE] 
> Podpora `FileDataset` v dávkovém odvození se pro teď omezuje na úložiště objektů BLOB v Azure. 

Můžete také odkazovat na jiné datové sady ve vlastním skriptu pro odvození. Můžete ho například použít k přístupu k popiskům ve skriptu pro označování obrázků pomocí `Dataset.register` a `Dataset.get_by_name`.

Další informace o Azure Machine Learning datových sadách najdete v tématu [Vytvoření a přístup k datovým sadám (Preview)](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-register-datasets).

objekty `PipelineData` se používají k přenosu mezilehlých dat mezi jednotlivými kroky kanálu. V tomto příkladu je použit pro odvození výstupů.

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

[Stáhněte si předem vyškolený model klasifikace imagí](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)a pak ho rozbalte do adresáře `models`.

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
- `init()`: tuto funkci použijte pro veškerou nákladný nebo běžnou přípravu pro pozdější odvození. Můžete ji například použít k načtení modelu do globálního objektu.
-  `run(mini_batch)`: funkce se spustí pro každou instanci `mini_batch`.
    -  `mini_batch`: odvození dávky vyvolá metodu Run a předá jako argument pro metodu buď list, nebo PANDAS dataframe. Každá položka v min_batch bude – FilePath, pokud je vstupem datová sada, PANDAS dataframe, pokud je vstupem TabularDataset.
    -  `response`: metoda Run () by měla vracet PANDAS dataframe nebo Array. Pro append_row output_action jsou tyto vrácené prvky připojeny do společného výstupního souboru. V případě summary_only se obsah prvků ignoruje. U všech výstupních akcí každý vrácený element Output označuje jedno úspěšné odvození vstupního prvku ve vstupní Mini-Batch. Uživatel by měl mít jistotu, že je do odvozeného výstupu zahrnutá dostatečná data pro mapování vstupu na odvození. Výstup odvození se zapíše do výstupního souboru a nezaručujeme, že by měl být v daném pořadí, uživatel by měl ve výstupu použít nějaký klíč k namapování na vstup.

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

## <a name="build-and-run-the-batch-inference-pipeline"></a>Sestavení a spuštění kanálu odvození dávky

Teď máte všechno, co potřebujete k vytvoření kanálu.

### <a name="prepare-the-run-environment"></a>Příprava prostředí pro spuštění

Nejdřív zadejte závislosti pro váš skript. Tento objekt použijete později při vytváření kroku kanálu.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_CPU_IMAGE
batch_env.spark.precache_packages = False
```

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>Zadejte parametry pro krok kanálu odvození dávky.

`ParallelRunConfig` je hlavní konfigurace nově zavedené instance `ParallelRunStep` odvození dávky v rámci kanálu Azure Machine Learning. Použijete ho k zabalení skriptu a ke konfiguraci nezbytných parametrů, včetně všech těchto:
- `entry_script`: uživatelský skript jako cesta k místnímu souboru, který se bude spouštět paralelně na více uzlech. Pokud je k dispozici `source_directly`, použijte relativní cestu. V opačném případě použijte jakoukoli cestu, která je přístupná v počítači.
- `mini_batch_size`: velikost zkrácené dávky předané do jednoho `run()` volání. (Volitelné; výchozí hodnota je `1`.)
    - U `FileDataset`se jedná o počet souborů s minimální hodnotou `1`. Můžete zkombinovat více souborů do jedné Mini-dávky.
    - Pro `TabularDataset`se jedná o velikost dat. Příklady hodnot jsou `1024`, `1024KB`, `10MB`a `1GB`. Doporučená hodnota je `1MB`. Všimněte si, že se pro Mini dávku z `TabularDataset` nikdy nepřekročí hranice souborů. Například pokud máte soubory. csv s různými velikostmi, nejmenší soubor je 100 KB a největší je 10 MB. Pokud nastavíte `mini_batch_size = 1MB`, budou se soubory s velikostí menší než 1 MB považovat za jednu miniskou dávku. Soubory o velikosti větší než 1 MB budou rozděleny do několika Mini-dávek.
- `error_threshold`: počet neúspěšných záznamů pro `TabularDataset` a chyby souborů pro `FileDataset`, které by se měly během zpracování ignorovat. Pokud se počet chyb pro celý vstup překročí k této hodnotě, úloha se zastaví. Prahová hodnota chyby je pro celý vstup a nikoli pro jednotlivé Mini-dávky odeslané do metody `run()`. Rozsah je `[-1, int.max]`. Část `-1` označuje, že se při zpracování budou ignorovat všechny chyby.
- `output_action`: jedna z následujících hodnot indikuje, jak bude výstup uspořádán:
    - `summary_only`: uživatelský skript uloží výstup. `ParallelRunStep` použije výstup pouze pro výpočet prahu chyby.
    - `append_row`: pro všechny vstupní soubory se ve výstupní složce vytvoří pouze jeden soubor pro připojení všech výstupů oddělených čárou. Název souboru bude parallel_run_step. txt.
- `source_directory`: cesty ke složkám, které obsahují všechny soubory, které mají být spuštěny na cílovém výpočetním cíli (volitelné).
- `compute_target`: podporuje se jenom `AmlCompute`.
- `node_count`: počet výpočetních uzlů, které se mají použít ke spuštění skriptu uživatele.
- `process_count_per_node`: počet procesů na uzel.
- `environment`: definice prostředí Pythonu. Můžete ji nakonfigurovat tak, aby používala existující prostředí Pythonu, nebo nastavit dočasné prostředí pro experiment. Definice je také zodpovědná za nastavení požadovaných závislostí aplikace (volitelné).
- `logging_level`: podrobnosti protokolu. Hodnoty ve zvyšování podrobností jsou: `WARNING`, `INFO`a `DEBUG`. Výchozí hodnota je `INFO` (volitelné).
- `run_invocation_timeout`: časový limit volání metody `run()` v sekundách. Výchozí hodnota je `60`.

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

Vytvořte krok kanálu pomocí skriptu, konfigurace prostředí a parametrů. Určete výpočetní cíl, který jste už ke svému pracovnímu prostoru připojili jako cíl provádění skriptu. Pomocí `ParallelRunStep` vytvořit krok kanálu odvození dávky, který převezme všechny následující parametry:
- `name`: název kroku s následujícími omezeními pojmenovávání: jedinečné, 3-32 znaky a Regex ^\[a-z\]([-a-Z0-9] * [a-Z0-9])? $.
- `models`: v registru Azure Machine Learning modelu již byly registrovány nula nebo více názvů modelů.
- `parallel_run_config`: objekt `ParallelRunConfig`, jak je definován výše.
- `inputs`: jedna nebo více Azure Machine Learning datových sad s jedním typem.
- `output`: objekt `PipelineData`, který odpovídá výstupnímu adresáři.
- `arguments`: seznam argumentů předaných uživatelskému skriptu (volitelné).
- `allow_reuse`: Určuje, zda má být při spuštění se stejnými nastaveními nebo vstupy použit krok znovu použít předchozí výsledky. Pokud je tento parametr `False`, pro tento krok se při spuštění kanálu vždy vygeneruje nový běh. (Volitelné; výchozí hodnota je `True`.)

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

### <a name="run-the-pipeline"></a>Spuštění kanálu

Teď kanál spusťte. Nejprve vytvořte objekt `Pipeline` pomocí odkazu na pracovní prostor a kroku kanálu, který jste vytvořili. Parametr `steps` je pole kroků. V tomto případě je pro dávkové vyhodnocování k dispozici pouze jeden krok. Chcete-li vytvořit kanály, které mají více kroků, umístěte kroky v tomto poli do pořadí.

Dále pomocí funkce `Experiment.submit()` odešlete kanál ke spuštění.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>Monitorování úlohy odvození dávky

Dokončení úlohy odvození dávky může trvat dlouhou dobu. Tento příklad sleduje průběh pomocí widgetu Jupyter. Průběh úlohy můžete také spravovat pomocí:

* Azure Machine Learning Studio. 
* Výstup konzoly z objektu [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py)

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>Další kroky

Chcete-li zobrazit tento proces na konci, zkuste [Poznámkový blok pro odvození dávky](https://aka.ms/batch-inference-notebooks). 

Pokyny k ladění a řešení potíží s kanály najdete v [Průvodci postupy](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]


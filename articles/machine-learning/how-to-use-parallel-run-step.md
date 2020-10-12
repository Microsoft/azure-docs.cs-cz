---
title: Spuštění dávkového předpovědiu pro velké objemy dat
titleSuffix: Azure Machine Learning
description: Naučte se, jak získat asynchronně odvozené informace o velkých objemech dat pomocí ParallelRunStep v Azure Machine Learning. ParallelRunStep poskytuje možnosti paralelního zpracování vycházející z okna a optimalizuje při použití s vysokou propustností, započetím požáru a zapomenutí pro případy využití velkých objemů dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: jmartens, larryfr
ms.author: tracych
author: tracychms
ms.date: 08/14/2020
ms.custom: Build2020, devx-track-python
ms.openlocfilehash: 8e8d0a13bc01e95311345154648ecb00b624c4bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905610"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Spuštění dávkového odvozování pro velké objemy dat pomocí Azure Machine Learning


V tomto článku se dozvíte, jak paralelně spustit model služby Azure Machine Learning a rychle vyhodnocovat velké objemy dat. 

Inferencing prostřednictvím velkých datových sad nebo složitých modelů může být časově náročná. Tato `ParallelRunStep` Třída umožňuje zpracovávat paralelně a může rychleji získat celkové výsledky. I v případě, že je jedno vyhodnocení poměrně rychlé, mnoho scénářů (detekce objektů, zpracování videa, zpracování přirozeného jazyka atd.) zahrnuje spouštění mnoha hodnocení. 

V systému je `ParallelRunStep` jednoduché škálovat dávky z dávek na velké clustery počítačů. Tyto clustery můžou zpracovávat terabajty strukturovaných nebo nestrukturovaných dat s vyšší produktivitou a optimalizovanými náklady.

V tomto článku se seznámíte s následujícími úlohami:

> 1. Nastavte prostředky strojového učení.
> 1. Nakonfigurujte vstupy a výstupy dat o odvození dávky.
> 1. Připravte předem vyškolený model klasifikace imagí na základě datové sady [mnist ručně zapsaných](https://publicdataset.azurewebsites.net/dataDetail/mnist/) . 
> 1.  Napište svůj skript pro odvození.
> 1. Vytvořte [kanál strojového učení](concept-ml-pipelines.md) obsahující ParallelRunStep a spusťte odvození dávky na mnist ručně zapsaných testovacích imagí. 
> 1. Opětovné odeslání odvozeného odvození dávky s novým vstupem a parametry dat 
> 1. Zkontrolujte výsledky.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

* Průvodce rychlým startem vám umožní absolvovat [kurz instalace](tutorial-1st-experiment-sdk-setup.md) , pokud ještě nemáte pracovní prostor Azure Machine Learning. 

* Pokud chcete spravovat vlastní prostředí a závislosti, přečtěte si návod, [jak](how-to-configure-environment.md) nakonfigurovat vlastní místní prostředí.

## <a name="set-up-machine-learning-resources"></a>Nastavení prostředků strojového učení

Následující akce nastaví prostředky strojového učení, které potřebujete ke spuštění kanálu odvození dávky:

- Připojte se k pracovnímu prostoru.
- Vytvořte nebo připojte stávající výpočetní prostředek.

### <a name="configure-workspace"></a>Konfigurace pracovního prostoru

Vytvořte objekt pracovního prostoru z existujícího pracovního prostoru. `Workspace.from_config()` přečte config.jsv souboru a načte podrobnosti do objektu s názvem WS.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

> [!IMPORTANT]
> Tento fragment kódu očekává uložení konfigurace pracovního prostoru do aktuálního adresáře nebo jeho nadřazeného objektu. Další informace o vytváření pracovního prostoru najdete v tématu [Vytvoření a Správa pracovních prostorů Azure Machine Learning](how-to-manage-workspace.md). Další informace o ukládání konfigurace do souboru najdete v tématu [vytvoření konfiguračního souboru pracovního prostoru](how-to-configure-environment.md#workspace).

### <a name="create-a-compute-target"></a>Vytvořit cíl výpočtů

V Azure Machine Learning *výpočetní* prostředí (nebo *target COMPUTE*) odkazuje na počítače nebo clustery, které provádějí výpočetní kroky v kanálu Machine Learning. Spusťte následující kód, který vytvoří [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py&preserve-view=true) cíl na základě procesoru.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

## <a name="configure-inputs-and-output"></a>Konfigurace vstupů a výstupů

### <a name="create-a-datastore-with-sample-images"></a>Vytvoření úložiště dat s ukázkovými obrázky

Získejte zkušební sadu MNIST ručně zapsaných z veřejného kontejneru objektů BLOB `sampledata` v účtu s názvem `pipelinedata` . Vytvořte úložiště dat s názvem `mnist_datastore` , které odkazuje na tento kontejner. V následujícím volání do `register_azure_blob_container` nastavte `overwrite` příznak pro `True` přepsání libovolného úložiště dat, které bylo dříve vytvořeno s tímto názvem. 

Tento krok můžete změnit tak, aby odkazoval na kontejner objektů BLOB tím, že poskytuje vaše vlastní hodnoty pro `datastore_name` , `container_name` a `account_name` .

```python
from azureml.core import Datastore
from azureml.core import Workspace

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Potom zadejte výchozí úložiště dat pracovního prostoru jako výstupní úložiště dat. Použijete ho k odvození výstupu.

Při vytváření pracovního prostoru jsou [soubory Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)   a [BLOB Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)   ve výchozím nastavení připojené k pracovnímu prostoru. Služba soubory Azure je výchozím úložištěm dat pro pracovní prostor, ale můžete také použít úložiště objektů BLOB jako úložiště dat. Další informace najdete v tématu [Možnosti služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="create-the-data-inputs"></a>Vytvoření vstupních dat

Vstupy pro odvození dávky jsou data, která chcete rozdělit na oddíly pro paralelní zpracování. Kanál odvození dávky přijímá datové vstupy prostřednictvím [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py&preserve-view=true) .

`Dataset` slouží k prozkoumávání, transformaci a správě dat v Azure Machine Learning. Existují dva typy: [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) a [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py&preserve-view=true) . V tomto příkladu použijete `FileDataset` jako vstupy. `FileDataset` poskytuje možnost stahovat soubory nebo je připojit k výpočetnímu prostředí. Vytvořením datové sady vytvoříte odkaz na umístění zdroje dat. Pokud jste pro datovou sadu použili jakékoli transformace podNastavení, budou uloženy i v datové sadě. Data zůstanou ve svém stávajícím umístění, takže se neúčtují žádné dodatečné náklady na úložiště.

Další informace o Azure Machine Learning datových sadách najdete v tématu [Vytvoření a přístup k datovým sadám (Preview)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

```python
from azureml.core.dataset import Dataset

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
```

Aby bylo možné používat dynamické datové vstupy při spuštění kanálu odvození dávky, můžete zadat vstupy `Dataset` jako [`PipelineParameter`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py&preserve-view=true) . Můžete zadat datovou sadu vstupů pokaždé, když znovu odešlete běh kanálu pro odvození dávky.

```python
from azureml.data.dataset_consumption_config import DatasetConsumptionConfig
from azureml.pipeline.core import PipelineParameter

pipeline_param = PipelineParameter(name="mnist_param", default_value=input_mnist_ds)
input_mnist_ds_consumption = DatasetConsumptionConfig("minist_param_config", pipeline_param).as_mount()
```

### <a name="create-the-output"></a>Vytvoření výstupu

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) objekty se používají k přenosu mezilehlých dat mezi jednotlivými kroky kanálu. V tomto příkladu je použit pro výstup odvození.

```python
from azureml.pipeline.core import Pipeline, PipelineData

output_dir = PipelineData(name="inferences", datastore=def_data_store)
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

Pak model zaregistrujte do svého pracovního prostoru, abyste ho mohli použít pro výpočetní prostředky.

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
>Následující kód je pouze ukázka, kterou používá [ukázkový Poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run) . Pro svůj scénář budete muset vytvořit vlastní skript.

Skript *musí obsahovat* dvě funkce:
- `init()`: Tuto funkci použijte pro veškerou nákladný nebo běžnou přípravu pro pozdější odvození. Můžete ji například použít k načtení modelu do globálního objektu. Tato funkce bude volána pouze jednou na začátku procesu.
-  `run(mini_batch)`: Funkce se spustí pro každou `mini_batch` instanci.
    -  `mini_batch`: `ParallelRunStep` vyvolá metodu Run a předá `DataFrame` jako argument metody seznam nebo PANDAS. Každá položka v mini_batch bude cestou k souboru, pokud je vstupem `FileDataset` nebo PANDAS, `DataFrame` Pokud je vstupem `TabularDataset` .
    -  `response`: metoda Run () by měla vracet PANDAS `DataFrame` nebo Array. Pro append_row output_action jsou tyto vrácené prvky připojeny do společného výstupního souboru. V případě summary_only se obsah prvků ignoruje. U všech výstupních akcí každý vrácený element Output označuje jedno úspěšné spuštění vstupního prvku ve vstupní Mini-Batch. Ujistěte se, že je ve výsledku spuštění k dispozici dostatek dat pro mapování vstupu na výsledek výstupu. Výstup spuštění se zapíše do výstupního souboru a nebude zaručit, že bude v pořádku, abyste ho namapovali na vstup, měli byste použít nějaký klíč ve výstupu.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
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

Pokud máte jiný soubor nebo složku ve stejném adresáři jako skript pro odvození, můžete na něj odkazovat pomocí hledání aktuálního pracovního adresáře.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>Sestavování a spouštění kanálu obsahujícího ParallelRunStep

Teď máte všechno, co potřebujete: datové vstupy, model, výstup a skript pro odvození. Pojďme sestavit kanál odvození dávky obsahující ParallelRunStep.

### <a name="prepare-the-environment"></a>Příprava prostředí

Nejdřív zadejte závislosti pro váš skript. Díky tomu budete moct instalovat balíčky PIP i nakonfigurovat prostředí.

Vždy zahrňte do seznamu balíčků PIP modul **AzureML-Core** a **AzureML-DataSet-runtime [PANDAS, pojistka]** . Pokud používáte vlastní image Docker (user_managed_dependencies = true), měli byste také mít nainstalované conda.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.15.2", "pillow", 
                                                          "azureml-core", "azureml-dataset-runtime[pandas, fuse]"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="specify-the-parameters-using-parallelrunconfig"></a>Zadejte parametry pomocí ParallelRunConfig

`ParallelRunConfig` je hlavní konfigurací `ParallelRunStep` instance v rámci Azure Machine Learning kanálu. Použijete ho k zabalení skriptu a ke konfiguraci nezbytných parametrů, včetně všech následujících položek:
- `entry_script`: Uživatelský skript jako cesta k místnímu souboru, který bude spuštěn paralelně na více uzlech. Pokud `source_directory` je k dispozici, použijte relativní cestu. V opačném případě použijte jakoukoli cestu, která je přístupná v počítači.
- `mini_batch_size`: Velikost malé dávky předaná jednomu `run()` volání. (volitelné; výchozí hodnota je `10` soubory pro `FileDataset` a `1MB` pro `TabularDataset` .)
    - V případě je `FileDataset` to počet souborů s minimální hodnotou `1` . Můžete zkombinovat více souborů do jedné Mini-dávky.
    - Pro `TabularDataset` je to velikost dat. Příklady hodnot jsou `1024` , `1024KB` , `10MB` a `1GB` . Doporučená hodnota je `1MB` . Ze zkrácené dávky `TabularDataset` nebude nikdy mezi hranicemi souborů. Například pokud máte soubory. csv s různými velikostmi, nejmenší soubor je 100 KB a největší je 10 MB. Pokud nastavíte `mini_batch_size = 1MB` , budou se soubory s velikostí menší než 1 MB považovat za jednu miniickou dávku. Soubory o velikosti větší než 1 MB budou rozděleny do několika Mini-dávek.
- `error_threshold`: Počet selhání záznamu `TabularDataset` a selhání souborů pro `FileDataset` , které by měly být během zpracování ignorovány. Pokud se počet chyb pro celý vstup překročí k této hodnotě, bude úloha přerušena. Prahová hodnota chyby je pro celý vstup a nikoli pro jednotlivé Mini-dávky odeslané do `run()` metody. Rozsah je `[-1, int.max]` . `-1`Část indikuje ignorování všech selhání během zpracování.
- `output_action`: Jedna z následujících hodnot indikuje, jak bude uspořádán výstup:
    - `summary_only`: Uživatelský skript uloží výstup. `ParallelRunStep` použije výstup pouze pro výpočet prahové hodnoty chyby.
    - `append_row`: Pro všechny vstupy se ve výstupní složce vytvoří pouze jeden soubor pro připojení všech výstupů oddělených čárou.
- `append_row_file_name`: Chcete-li přizpůsobit název výstupního souboru pro append_row output_action (volitelné; výchozí hodnota je `parallel_run_step.txt` ).
- `source_directory`: Cesty ke složkám, které obsahují všechny soubory, které mají být spuštěny na cílovém výpočetním cíli (volitelné).
- `compute_target`: `AmlCompute` Podporuje se jenom.
- `node_count`: Počet výpočetních uzlů, které se mají použít ke spuštění skriptu uživatele.
- `process_count_per_node`: Počet procesů na uzel. Osvědčeným postupem je nastavit počet GPU nebo PROCESORového jednoho uzlu (volitelné; výchozí hodnota je `1` ).
- `environment`: Definice prostředí Pythonu. Můžete ji nakonfigurovat tak, aby používala existující prostředí Pythonu, nebo nastavit dočasné prostředí. Definice je také zodpovědná za nastavení požadovaných závislostí aplikace (volitelné).
- `logging_level`: Podrobnosti protokolu. Hodnoty při zvyšování podrobností jsou: `WARNING` , `INFO` a `DEBUG` . (volitelné; výchozí hodnota je `INFO` )
- `run_invocation_timeout`: `run()` Časový limit volání metody v sekundách. (volitelné; výchozí hodnota je `60` )
- `run_max_try`: Maximální počet testovaných položek `run()` pro Mini-Batch. V `run()` případě, že dojde k výjimce, se nezdařila nebo není vrácena žádná hodnota, pokud `run_invocation_timeout` je dosaženo (volitelné; výchozí hodnota je `3` ). 

Můžete zadat `mini_batch_size` , `node_count` ,, `process_count_per_node` `logging_level` , `run_invocation_timeout` a `run_max_try` jako, aby `PipelineParameter` při opakovaném odesílání kanálu mohli doladit hodnoty parametrů. V tomto příkladu použijete `PipelineParameter` pro `mini_batch_size` a `Process_count_per_node` a tyto hodnoty změníte, pokud je znovu spustíte později. 

V tomto příkladu se předpokládá, že používáte `digit_identification.py` skript, který jste si poznamenali dříve. Pokud používáte vlastní skript, změňte `source_directory` `entry_script` parametry a odpovídajícím způsobem.

```python
from azureml.pipeline.core import PipelineParameter
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory='.',
    entry_script="digit_identification.py",
    mini_batch_size=PipelineParameter(name="batch_size_param", default_value="5"),
    error_threshold=10,
    output_action="append_row",
    append_row_file_name="mnist_outputs.txt",
    environment=batch_env,
    compute_target=compute_target,
    process_count_per_node=PipelineParameter(name="process_count_param", default_value=2),
    node_count=2)
```

### <a name="create-the-parallelrunstep"></a>Vytvoření ParallelRunStep

Vytvořte ParallelRunStep pomocí skriptu, konfigurace prostředí a parametrů. Určete výpočetní cíl, který jste už připojili k pracovnímu prostoru jako cíl provádění pro váš skript pro odvození. Použijte `ParallelRunStep` k vytvoření kroku kanálu odvození dávky, který převezme všechny následující parametry:
- `name`: Název kroku s následujícími omezeními pro pojmenovávání: jedinečné, 3-32 znaky a Regex ^ \[ a-z \] ([-a-Z0-9] * [a-Z0-9])? $.
- `parallel_run_config`: `ParallelRunConfig` Objekt, jak je definováno výše.
- `inputs`: Jedna nebo více Azure Machine Learning datových sad s jedním typem, které mají být rozděleny na oddíly pro paralelní zpracování.
- `side_inputs`: Jedno nebo více referenčních dat nebo datových sad, které se používají jako vstupy na straně, není nutné rozdělit na oddíly.
- `output`: `PipelineData` Objekt, který odpovídá výstupnímu adresáři.
- `arguments`: Seznam argumentů předaných uživatelskému skriptu. Pomocí unknown_args je načtěte v vstupním skriptu (volitelné).
- `allow_reuse`: Zda by měl krok při spuštění se stejnými nastaveními nebo vstupy znovu použít předchozí výsledky. Pokud je tento parametr `False` , nové spuštění bude pro tento krok vždy vygenerováno během provádění kanálu. (volitelné; výchozí hodnota je `True` .)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```
### <a name="create-and-run-the-pipeline"></a>Vytvoření a spuštění kanálu

Teď kanál spusťte. Nejprve vytvořte [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py&preserve-view=true) objekt pomocí odkazu na pracovní prostor a kroku kanálu, který jste vytvořili. `steps`Parametr je pole kroků. V tomto případě existuje pouze jeden krok pro odvození dávky. Chcete-li vytvořit kanály, které mají více kroků, umístěte kroky v tomto poli do pořadí.

Dále pomocí `Experiment.submit()` funkce odešlete kanál ke spuštění.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
experiment = Experiment(ws, 'digit_identification')
pipeline_run = experiment.submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>Monitorování úlohy odvození dávky

Dokončení úlohy odvození dávky může trvat dlouhou dobu. Tento příklad sleduje průběh pomocí widgetu Jupyter. Průběh úlohy můžete také monitorovat pomocí:

* Azure Machine Learning Studio. 
* Výstup z objektu Console [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py&preserve-view=true)

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="resubmit-a-run-with-new-data-inputs-and-parameters"></a>Opětovné odeslání běhu s novými vstupy a parametry dat

Vzhledem k tomu, že jste provedli vstupy a několik konfigurací jako `PipelineParameter` , můžete znovu odeslat odvození dávky s jiným vstupem datové sady a doladit parametry bez nutnosti vytvářet zcela nový kanál. Použijete stejné úložiště dat, ale použijete pouze jeden obrázek jako vstup dat.

```python
path_on_datastore = mnist_blob.path('mnist/0.png')
single_image_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)

pipeline_run_2 = experiment.submit(pipeline, 
                                   pipeline_parameters={"mnist_param": single_image_ds, 
                                                        "batch_size_param": "1",
                                                        "process_count_param": 1}
)

pipeline_run_2.wait_for_completion(show_output=True)
```
## <a name="view-the-results"></a>Zobrazení výsledků

Výsledky z výše uvedeného běhu jsou zapsány do `DataStore` zadaného `PipelineData` objektu v objektu jako výstupní data, která jsou v tomto případě označována jako *odvozená*. Výsledky jsou uložené ve výchozím kontejneru objektů blob, můžete přejít k účtu úložiště a zobrazit ho prostřednictvím Průzkumník služby Storage, cesta k souboru je AzureML-SAS-*GUID*/AzureML/*RunId* / *output_dir*.

Můžete si také stáhnout tato data a zobrazit výsledky. Níže je ukázkový kód pro zobrazení prvních 10 řádků.

```python
import pandas as pd
import tempfile

batch_run = pipeline_run.find_step_run(parallelrun_step.name)[0]
batch_output = batch_run.get_output_data(output_dir.name)

target_dir = tempfile.mkdtemp()
batch_output.download(local_path=target_dir)
result_file = os.path.join(target_dir, batch_output.path_on_datastore, parallel_run_config.append_row_file_name)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10) 
```

## <a name="next-steps"></a>Další kroky

Chcete-li zobrazit tento proces na konci, zkuste [Poznámkový blok pro odvození dávky](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run). 

Pokyny k ladění a řešení potíží pro ParallelRunStep najdete v tématu [Průvodce postupy](how-to-debug-parallel-run-step.md).

Pokyny k ladění a řešení potíží s kanály najdete v [Průvodci postupy](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]


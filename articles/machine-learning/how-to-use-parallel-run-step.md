---
title: Spuštění predikcí dávek u velkých objemů dat
titleSuffix: Azure Machine Learning
description: Zjistěte, jak získat odvození asynchronně na velké množství dat pomocí ParallelRunStep v Azure Machine Learning. ParallelRunStep poskytuje možnosti paralelního zpracování po vybalení z krabice a optimalizuje pro vysokou propustnost, fire-and-forget inference pro případy použití velkých objemů dat.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477183"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Spuštění dávkového odvození velkého množství dat pomocí Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Zjistěte, jak zpracovat velké množství dat asynchronně a paralelně pomocí Azure Machine Learning. Funkce ParallelRunStep je popsána zde je ve verzi Public Preview. Je to vysoce výkonný a vysoce propustný způsob generování odvození a zpracování dat. Poskytuje asynchronní funkce po vybalení z krabice.

S ParallelRunStep je jednoduché škálovat offline odvození na velké clustery počítačů na terabajtů produkčních dat, což vede ke zvýšení produktivity a optimalizované náklady.

V tomto článku se dozvíte následující úkoly:

> * Vytvořte vzdálený výpočetní prostředek.
> * Napište vlastní odvození skriptu.
> * Vytvořte [kanál strojového učení](concept-ml-pipelines.md) pro registraci předem trénovaného modelu klasifikace bitových obrázků na základě datové sady [MNIST.](https://publicdataset.azurewebsites.net/dataDetail/mnist/) 
> * Model slouží ke spuštění dávkového odvození na ukázkových ibi dostupných v účtu úložiště objektů Blob Azure. 

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree).

* Pro rychlý start s průvodcem dokončete [kurz nastavení,](tutorial-1st-experiment-sdk-setup.md) pokud ještě nemáte pracovní prostor Azure Machine Learning nebo virtuální počítač s notebookem. 

* Chcete-li spravovat vlastní prostředí a závislosti, přečtěte [si návod na](how-to-configure-environment.md) konfiguraci vlastního prostředí. Spusťte `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` ve vašem prostředí a stáhněte potřebné závislosti.

## <a name="set-up-machine-learning-resources"></a>Nastavení zdrojů strojového učení

Následující akce nastaví prostředky, které potřebujete ke spuštění kanálu dávkových odvození:

- Vytvořte úložiště dat, které odkazuje na kontejner objektů blob, který má image k odvození.
- Nastavte odkazy na data jako vstupy a výstupy pro krok kanálu odvození dávky.
- Nastavte výpočetní cluster pro spuštění kroku odvození dávky.

### <a name="create-a-datastore-with-sample-images"></a>Vytvoření úložiště dat s ukázkovými obrázky

Získejte sadu vyhodnocení MNIST z `sampledata` veřejného kontejneru objektů blob na účtu s názvem `pipelinedata`. Vytvořte úložiště dat `mnist_datastore`s názvem , který odkazuje na tento kontejner. V následující volání `register_azure_blob_container`, `overwrite` nastavení `True` příznak přepíše všechny úložiště dat, která byla vytvořena dříve s tímto názvem. 

Tento krok můžete změnit tak, aby přecšuje `datastore_name` `container_name`kontejner `account_name`objektů blob tím, že zadáte vlastní hodnoty pro aplikace , a .

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

Dále zadejte výchozí úložiště dat pracovního prostoru jako výstupní úložiště dat. Budete ji používat pro odvození výstupu.

Při vytváření pracovního prostoru jsou [soubory](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) Azure a [úložiště](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) objektů blob ve výchozím nastavení připojené k pracovnímu prostoru. Azure Files je výchozí úložiště dat pro pracovní prostor, ale můžete také použít úložiště objektů blob jako úložiště dat. Další informace najdete v tématu [možnosti úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-inputs-and-outputs"></a>Konfigurace datových vstupů a výstupů

Nyní je třeba nakonfigurovat datové vstupy a výstupy, včetně:

- Adresář, který obsahuje vstupní obrázky.
- Adresář, kde je uložen předem trénovaný model.
- Adresář, který obsahuje popisky.
- Adresář pro výstup.

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)je třída pro zkoumání, transformaci a správu dat v Azure Machine Learning. Tato třída má [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) dva [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py)typy: a . V tomto příkladu budete `FileDataset` používat jako vstupy do kroku kanálu odvození dávky. 

> [!NOTE] 
> `FileDataset`podpora v dávkovém odvození je prozatím omezená na úložiště objektů Blob Azure. 

Můžete také odkazovat na jiné datové sady ve vlastním odvození skriptu. Můžete ji například použít k přístupu k popiskům `Dataset.register` `Dataset.get_by_name`ve skriptu pro označování obrázků pomocí a .

Další informace o datových sadách Azure Machine Learning najdete v [tématu Vytváření a přístup k datovým souborům (preview).](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)objekty se používají pro přenos mezilehlých dat mezi kroky kanálu. V tomto příkladu jej použijete pro odvození výstupů.

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

### <a name="set-up-a-compute-target"></a>Nastavení výpočetního cíle

V Azure Machine Learning, *výpočetní* (nebo *výpočetní cíl)* odkazuje na počítače nebo clustery, které provádějí výpočetní kroky v kanálu strojového učení. Spusťte následující kód a vytvořte cíl [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) založený na procesoru.

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

[Stáhněte si předem trénovaný model](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)klasifikace `models` obrázků a pak jej extrahujte do adresáře.

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

Pak model zaregistrujte v pracovním prostoru, aby byl k dispozici pro vzdálený výpočetní prostředek.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Napište svůj odvození skript

>[!Warning]
>Následující kód je pouze ukázka, která používá [ukázkový poznámkový blok.](https://aka.ms/batch-inference-notebooks) Budete muset vytvořit vlastní skript pro váš scénář.

Skript *musí obsahovat* dvě funkce:
- `init()`: Tuto funkci použijte pro jakoukoli nákladnou nebo společnou přípravu na pozdější odvození. Použijte jej například k načtení modelu do globálního objektu. Tato funkce bude volána pouze jednou na začátku procesu.
-  `run(mini_batch)`: Funkce bude spuštěna pro každou `mini_batch` instanci.
    -  `mini_batch`: Paralelní spuštění krok vyvolá run metoda a předat seznam nebo Pandas DataFrame jako argument metody. Každá položka v min_batch bude - cesta k souboru, pokud je vstup FileDataset, Pandas DataFrame, pokud je vstup TabularDataset.
    -  `response`: run() metoda by měla vrátit Pandas DataFrame nebo pole. Pro append_row output_action jsou tyto vrácené prvky připojeny do společného výstupního souboru. Pro summary_only jsou ignorovány obsah prvků. Pro všechny výstupní akce každý vrácený výstupní prvek označuje jeden úspěšný běh vstupního prvku ve vstupní mini-batch. Měli byste se ujistit, že dostatek dat je zahrnuta v výsledku spuštění mapovat vstup ke spuštění výsledku. Spustit výstup bude zapsán ve výstupním souboru a není zaručeno, že bude v pořádku, měli byste použít nějaký klíč ve výstupu k mapování na vstup.

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

### <a name="how-to-access-other-files-in-source-directory-in-entry_script"></a>Jak získat přístup k dalším souborům ve zdrojovém adresáři v entry_script

Pokud máte ve stejném adresáři jiný soubor nebo složku jako vstupní skript, můžete na něj odkazovat vyhledáním aktuálního pracovního adresáře.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>Sestavení a spuštění kanálu obsahujícího ParallelRunStep

Nyní máte vše, co potřebujete k vybudování potrubí.

### <a name="prepare-the-run-environment"></a>Příprava prostředí spuštění

Nejprve zadejte závislosti skriptu. Tento objekt se použije později při vytváření kroku kanálu.

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

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>Určení parametrů pro krok kanálu odvození dávky

`ParallelRunConfig`je hlavní konfigurace pro nově zavedenou `ParallelRunStep` instanci dávkového odvození v rámci kanálu Azure Machine Learning. Můžete jej použít k zabalení skriptu a konfiguraci nezbytných parametrů, včetně všech následujících parametrů:
- `entry_script`: Uživatelský skript jako místní cesta k souboru, která bude spuštěna paralelně na více uzlech. Pokud `source_directory` je k dispozici, použijte relativní cestu. V opačném případě použijte libovolnou cestu, která je přístupná v počítači.
- `mini_batch_size`: Velikost minidávky předaná `run()` jednomu volání. (volitelné; výchozí hodnota `10` je soubory pro `1MB` FileDataset a tabularDataset.)
    - Pro `FileDataset`, je to počet souborů s `1`minimální hodnotou . Můžete kombinovat více souborů do jedné minidávky.
    - Pro `TabularDataset`, je to velikost dat. Příklady `1024`hodnot `1024KB` `10MB`jsou `1GB`, , a . Doporučená hodnota `1MB`je . Mini-batch z `TabularDataset` nikdy nepřekročí hranice souboru. Například pokud máte soubory .csv s různými velikostmi, nejmenší soubor je 100 KB a největší je 10 MB. Pokud nastavíte `mini_batch_size = 1MB`, budou soubory o velikosti menší než 1 MB považovány za jednu minidávku. Soubory o velikosti větší než 1 MB budou rozděleny do více minidávek.
- `error_threshold`: Počet selhání záznamů `TabularDataset` a selhání souborů `FileDataset` pro které by měly být během zpracování ignorovány. Pokud počet chyb pro celý vstup přejde nad tuto hodnotu, úloha bude přerušena. Prahová hodnota chyby je pro celý vstup a ne `run()` pro jednotlivé minidávky odeslané do metody. Rozsah je `[-1, int.max]`. Část `-1` označuje ignorování všech chyb během zpracování.
- `output_action`: Jedna z následujících hodnot označuje, jak bude výstup uspořádán:
    - `summary_only`: Uživatelský skript uloží výstup. `ParallelRunStep`použije výstup pouze pro výpočet prahové hodnoty chyby.
    - `append_row`: Pro všechny vstupní soubory bude ve výstupní složce vytvořen pouze jeden soubor, který připojí všechny výstupy oddělené řádkem. Název souboru `parallel_run_step.txt`bude .
- `source_directory`: Cesty ke složkám, které obsahují všechny soubory, které mají být spuštěny v cílovém výpočetním prostředí (volitelné).
- `compute_target`: `AmlCompute` Je podporována pouze.
- `node_count`: Počet výpočetních uzlů, které mají být použity pro spuštění uživatelského skriptu.
- `process_count_per_node`: Počet procesů na uzel.
- `environment`: Definice prostředí Pythonu. Můžete ji nakonfigurovat tak, aby používala existující prostředí Pythonu nebo nastavila dočasné prostředí pro experiment. Definice je také zodpovědná za nastavení požadovaných závislostí aplikací (volitelné).
- `logging_level`: Log podrobnost. Hodnoty zvyšující se podrobnosti `WARNING` `INFO`jsou: `DEBUG`, , a . (volitelné, výchozí hodnota `INFO`je )
- `run_invocation_timeout`: `run()` Časový čas vyvolání metody v sekundách. (volitelná; výchozí `60`hodnota je )

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

Krok kanálu vytvořte pomocí skriptu, konfigurace prostředí a parametrů. Zadejte výpočetní cíl, který jste již připojili k pracovnímu prostoru jako cíl spuštění skriptu. Slouží `ParallelRunStep` k vytvoření kroku kanálu odvození dávky, který přebírá všechny následující parametry:
- `name`: Název kroku s následujícími omezeními pojmenování: jedinečný, 3-32\[znaků\]a regex ^ a-z ([-a-z0-9]*[a-z0-9])?$.
- `models`: Nula nebo více názvů modelů, které jsou již registrované v registru modelu Azure Machine Learning.
- `parallel_run_config`: `ParallelRunConfig` Objekt, jak je definováno dříve.
- `inputs`: Jedna nebo více datových sad Azure Machine Learning s jedním typem.
- `output`: `PipelineData` Objekt, který odpovídá výstupnímu adresáři.
- `arguments`: Seznam argumentů předaných uživatelskému skriptu (volitelné).
- `allow_reuse`: Zda krok by měl znovu použít předchozí výsledky při spuštění se stejným nastavením/ vstupy. Pokud tento `False`parametr je , bude vždy generováno nové spuštění pro tento krok během spuštění kanálu. (volitelné, výchozí hodnota `True`je .)

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
> Výše uvedený krok `azureml-contrib-pipeline-steps`závisí na , jak je popsáno v [požadavky](#prerequisites). 

### <a name="submit-the-pipeline"></a>Odeslat potrubí

Teď spusťte potrubí. Nejprve vytvořte [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) objekt pomocí odkazu na pracovní prostor a kroku kanálu, který jste vytvořili. Parametr `steps` je pole kroků. V tomto případě je pouze jeden krok pro dávkové bodování. Chcete-li vytvořit kanály, které mají více kroků, umístěte kroky v pořadí v tomto poli.

Dále použijte `Experiment.submit()` funkci k odeslání kanálu k provedení.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-parallel-run-job"></a>Sledování úlohy paralelního spuštění

Dokončení dávkové odvození může trvat dlouhou dobu. Tento příklad sleduje průběh pomocí widgetu Jupyter. Průběh úlohy můžete také spravovat pomocí:

* Azure Machine Learning Studio. 
* Výstup konzoly [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) z objektu.

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>Další kroky

Chcete-li zobrazit tento proces pracovní konec na konec, zkuste [dávkové odvození poznámkového bloku](https://aka.ms/batch-inference-notebooks). 

Ladění a řešení potíží pokyny pro ParallelRunStep, naleznete v [návodu k obsluze](how-to-debug-parallel-run-step.md).

Pokyny k ladění a řešení potíží pro kanály naleznete v [příručce s postupy](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]


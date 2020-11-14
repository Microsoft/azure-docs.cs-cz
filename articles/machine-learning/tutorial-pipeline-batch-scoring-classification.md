---
title: 'Kurz: kanály ML pro dávkové vyhodnocování'
titleSuffix: Azure Machine Learning
description: V tomto kurzu vytvoříte kanál strojového učení, který bude provádět dávkové vyhodnocování. Zaměřte se na strojové učení místo infrastruktury a automatizace.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: lobrien
ms.author: laobri
ms.reviewer: laobri
ms.date: 10/13/2020
ms.custom: contperfq4, devx-track-python
ms.openlocfilehash: 8748d6e155eb84cf948966d768dda1a992207f7e
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629627"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Kurz: vytvoření kanálu Azure Machine Learning pro dávkové vyhodnocování



V tomto rozšířeném kurzu se naučíte, jak vytvořit [kanál Azure Machine Learning](concept-ml-pipelines.md) pro spuštění úlohy vyhodnocování dávek. Kanály strojového učení optimalizují pracovní postup s využitím rychlosti, přenositelnosti a opakovaného použití, takže se můžete soustředit na strojové učení místo infrastruktury a automatizace. Po sestavení a publikování kanálu nakonfigurujete koncový bod REST, který můžete použít ke spuštění kanálu z libovolné knihovny HTTP na libovolné platformě. 

V příkladu se používá předem provedený [model konvoluční neuronové](https://arxiv.org/abs/1512.00567) , který implementuje v Tensorflow pro klasifikaci neoznačených obrázků. 

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> * Konfigurace pracovního prostoru 
> * Stažení a uložení ukázkových dat
> * Vytvoření objektů DataSet pro načtení a výstup dat
> * Stažení, příprava a registrace modelu v pracovním prostoru
> * Zřizování výpočetních cílů a vytvoření skriptu bodování
> * Použít `ParallelRunStep` třídu pro asynchronní vyhodnocování dávky
> * Sestavování, spouštění a publikování kanálu
> * Povolit koncový bod REST pro kanál

Pokud ještě předplatné Azure nemáte, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

## <a name="prerequisites"></a>Předpoklady

* Pokud ještě nemáte virtuální počítač s Azure Machine Learning pracovním prostorem nebo notebookem, vyplňte [část 1 tohoto kurzu instalace](tutorial-1st-experiment-sdk-setup.md).
* Po dokončení kurzu instalace použijte stejný server poznámkového bloku a otevřete Poznámkový blok *kurzy/Machine-Learning-Pipelines-Advanced/tutorial-Pipeline-Batch-scoring-Classification. ipynb* .

Pokud chcete spustit kurz nastavení ve vlastním [místním prostředí](how-to-configure-environment.md#local), můžete získat přístup k kurzu na [GitHubu](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials). Spusťte `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` , abyste získali požadované balíčky.

## <a name="configure-workspace-and-create-a-datastore"></a>Konfigurace pracovního prostoru a vytvoření úložiště dat

Vytvořte objekt pracovního prostoru z existujícího pracovního prostoru Azure Machine Learning.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

> [!IMPORTANT]
> Tento fragment kódu očekává uložení konfigurace pracovního prostoru do aktuálního adresáře nebo jeho nadřazeného objektu. Další informace o vytváření pracovního prostoru najdete v tématu [Vytvoření a Správa pracovních prostorů Azure Machine Learning](how-to-manage-workspace.md). Další informace o ukládání konfigurace do souboru najdete v tématu [vytvoření konfiguračního souboru pracovního prostoru](how-to-configure-environment.md#workspace).

## <a name="create-a-datastore-for-sample-images"></a>Vytvoření úložiště dat pro ukázkové image

Na `pipelinedata` účtu Získejte ukázku veřejné dat ImageNet Evaluation z `sampledata` veřejného kontejneru objektů BLOB. Zavolejte `register_azure_blob_container()` k zpřístupnění dat pro pracovní prostor pod názvem `images_datastore` . Pak nastavte výchozí úložiště dat pracovního prostoru jako výstupní úložiště dat. Použijte výstupní úložiště dat k určení skóre výstupu v kanálu.

Další informace o přístupu k datům najdete v tématu [Jak získat přístup k datům](./how-to-access-data.md).

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>Vytváření objektů DataSet

Při sestavování kanálů se `Dataset` objekty používají pro čtení dat z úložiště dat v pracovním prostoru a `PipelineData` objekty slouží k přenosu mezilehlých dat mezi jednotlivými kroky kanálu.

> [!Important]
> Příklad dávkového vyhodnocování v tomto kurzu používá pouze jeden krok kanálu. V případech použití, které mají více kroků, bude typický tok zahrnovat tyto kroky:
>
> 1. Použijte `Dataset` objekty jako *vstupy* pro načtení nezpracovaných dat, proveďte nějakou transformaci a pak *výstup* `PipelineData` objektu.
>
> 2. Použijte `PipelineData` *výstupní objekt* v předchozím kroku jako *vstupní objekt*. Opakujte postup pro následné kroky.

V tomto scénáři vytvoříte `Dataset` objekty, které odpovídají adresářům úložiště dat pro vstupní image i popisky klasifikace (hodnoty y-test). Také vytvoříte `PipelineData` objekt pro výstupní data dávkového vyhodnocování.

```python
from azureml.core.dataset import Dataset
from azureml.pipeline.core import PipelineData

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/"))
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

Zaregistrujte datové sady do pracovního prostoru, pokud ho chcete znovu použít později. Tento krok je volitelný.

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>Stažení a registrace modelu

Stáhněte si předTensorflowný model, který se použije pro dávkové vyhodnocování v kanálu. Nejprve vytvořte místní adresář, do kterého chcete model Uložit. Pak stáhněte a extrahujte model.

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

Dále zaregistrujte model do svého pracovního prostoru, abyste ho mohli snadno načíst v procesu kanálu. Ve `register()` funkci static `model_name` je parametr klíč, který použijete k vyhledání modelu v rámci sady SDK.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Vytvoření a připojení vzdáleného cíle výpočtů

Kanály strojového učení nejde spouštět místně, takže je spouštíte na cloudových prostředcích nebo *vzdálených výpočetních cílech*. Vzdálený výpočetní cíl je opakovaně použitelný virtuální výpočetní prostředí, ve kterém spouštíte experimenty a pracovní postupy strojového učení. 

Spusťte následující kód pro vytvoření cíle s povoleným GPU [`AmlCompute`](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py) a pak ho připojte k pracovnímu prostoru. Další informace o cílových výpočetních operacích najdete v [článku konceptu](./concept-compute-target.md).


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>Zápis vyhodnocovacího skriptu

Chcete-li provést bodování, vytvořte skript vyhodnocování s názvem `batch_scoring.py` a pak ho zapište do aktuálního adresáře. Skript bere vstupní image, použije model klasifikace a pak výstup předpovědi do souboru výsledků.

`batch_scoring.py`Skript přebírá následující parametry, které se předávají z `ParallelRunStep` vytvořeného nástroje později:

- `--model_name`: Název používaného modelu.
- `--labels_dir`: Umístění `labels.txt` souboru.

Infrastruktura kanálu používá `ArgumentParser` třídu k předání parametrů do kroků kanálu. Například v následujícím kódu je první argument `--model_name` dán identifikátor vlastnosti `model_name` . Ve `init()` funkci `Model.get_model_path(args.model_name)` se používá pro přístup k této vlastnosti.


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3

from azureml.core import Run
from azureml.core.model import Model
from azureml.core.dataset import Dataset

slim = tf.contrib.slim

image_size = 299
num_channel = 3


def get_class_label_dict(labels_dir):
    label = []
    labels_path = os.path.join(labels_dir, 'labels.txt')
    proto_as_ascii_lines = tf.gfile.GFile(labels_path).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_dir', dest="labels_dir", required=True)
    args, _ = parser.parse_known_args()

    label_dict = get_class_label_dict(args.labels_dir)
    classes_num = len(label_dict)

    with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
        input_images = tf.placeholder(tf.float32, [1, image_size, image_size, num_channel])
        logits, _ = inception_v3.inception_v3(input_images,
                                              num_classes=classes_num,
                                              is_training=False)
        probabilities = tf.argmax(logits, 1)

    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    g_tf_sess = tf.Session(config=config)
    g_tf_sess.run(tf.global_variables_initializer())
    g_tf_sess.run(tf.local_variables_initializer())

    model_path = Model.get_model_path(args.model_name)
    saver = tf.train.Saver()
    saver.restore(g_tf_sess, model_path)


def file_to_tensor(file_path):
    image_string = tf.read_file(file_path)
    image = tf.image.decode_image(image_string, channels=3)

    image.set_shape([None, None, None])
    image = tf.image.resize_images(image, [image_size, image_size])
    image = tf.divide(tf.subtract(image, [0]), [255])
    image.set_shape([image_size, image_size, num_channel])
    return image


def run(mini_batch):
    result_list = []
    for file_path in mini_batch:
        test_image = file_to_tensor(file_path)
        out = g_tf_sess.run(test_image)
        result = g_tf_sess.run(probabilities, feed_dict={input_images: [out]})
        result_list.append(os.path.basename(file_path) + ": " + label_dict[result[0]])
    return result_list
```

> [!TIP]
> Kanál v tomto kurzu má pouze jeden krok a zapisuje výstup do souboru. Pro kanály s více kroky můžete použít také `ArgumentParser` k definování adresáře pro zápis výstupních dat pro vstup do následujících kroků. Příklad předávání dat mezi různými kroky kanálu pomocí `ArgumentParser` vzoru návrhu najdete v [poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-the-pipeline"></a>Vytvoření kanálu

Před spuštěním kanálu vytvořte objekt, který definuje prostředí Python, a vytvoří závislosti, které váš `batch_scoring.py` skript vyžaduje. Hlavní požadovaná závislost je Tensorflow, ale nainstalujete i `azureml-core` ty, `azureml-dataprep[fuse]` které vyžaduje ParallelRunStep. Také zadejte Docker a Docker-GPU support.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.15.2",
                                            "azureml-core", "azureml-dataprep[fuse]"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>Vytvoření konfigurace pro zabalení skriptu

Vytvořte krok kanálu pomocí skriptu, konfigurace prostředí a parametrů. Zadejte cíl služby COMPUTE, který jste už připojili k vašemu pracovnímu prostoru.

```python
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    environment=env,
    entry_script="batch_scoring.py",
    source_directory=".",
    output_action="append_row",
    mini_batch_size="20",
    error_threshold=1,
    compute_target=compute_target,
    process_count_per_node=2,
    node_count=1
)
```

### <a name="create-the-pipeline-step"></a>Vytvoření kroku kanálu

Krok kanálu je objekt, který zapouzdřuje všechno, co potřebujete ke spuštění kanálu, včetně:

* Nastavení prostředí a závislostí
* Výpočetní prostředek, na kterém se má kanál spustit
* Vstupní a výstupní data a libovolné vlastní parametry
* Odkaz na skript nebo logiku sady SDK, které se spustí během kroku

Více tříd dědí z nadřazené třídy [`PipelineStep`](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?preserve-view=true&view=azure-ml-py) . Můžete zvolit třídy pro použití specifických rozhraní nebo zásobníků k vytvoření kroku. V tomto příkladu použijete `ParallelRunStep` třídu k definování kroku logiky pomocí vlastního skriptu Pythonu. Je-li argument skriptu buď vstupem do kroku, nebo výstupem kroku, musí být *argument definován v poli i* `arguments` v *and* `input` parametru nebo v `output` parametru. 

Ve scénářích, kde je více než jeden krok, se odkaz na objekt v `outputs` poli zpřístupní jako *vstup* pro následný krok kanálu.

```python
from azureml.pipeline.steps import ParallelRunStep
from datetime import datetime

parallel_step_name = "batchscoring-" + datetime.now().strftime("%Y%m%d%H%M")

label_config = label_ds.as_named_input("labels_input")

batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--model_name", "inception",
               "--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

Seznam všech tříd, které lze použít pro různé typy kroků, naleznete v [balíčku kroků](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py).

## <a name="submit-the-pipeline"></a>Odeslat kanál

Teď kanál spusťte. Nejprve vytvořte `Pipeline` objekt pomocí odkazu na pracovní prostor a kroku kanálu, který jste vytvořili. `steps`Parametr je pole kroků. V tomto případě je pro dávkové vyhodnocování k dispozici pouze jeden krok. Chcete-li vytvořit kanály, které mají více kroků, umístěte kroky v tomto poli do pořadí.

Dále pomocí `Experiment.submit()` funkce odešlete kanál ke spuštění. `wait_for_completion`Funkce ukládá výstupy do protokolů během procesu sestavení kanálu. Pomocí protokolů můžete zobrazit aktuální průběh.

> [!IMPORTANT]
> První spuštění kanálu trvá přibližně *15 minut*. Všechny závislosti je nutné stáhnout, vytvoří se image Docker a prostředí Pythonu se zřídí a vytvoří. Opětovné spuštění kanálu bude trvat mnohem méně času, protože se místo vytvoření znovu použijí tyto prostředky. Celková doba běhu pro kanál ale závisí na zatížení skriptů a procesech, které jsou spuštěné v jednotlivých krocích kanálu.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Stáhnout a zkontrolovat výstup

Spuštěním následujícího kódu Stáhněte výstupní soubor, který je vytvořen ze `batch_scoring.py` skriptu. Pak prozkoumejte výsledky bodování.

```python
import pandas as pd

batch_run = next(pipeline_run.get_children())
batch_output = batch_run.get_output_data("scores")
batch_output.download(local_path="inception_results")

for root, dirs, files in os.walk("inception_results"):
    for file in files:
        if file.endswith("parallel_run_step.txt"):
            result_file = os.path.join(root, file)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10)
```

## <a name="publish-and-run-from-a-rest-endpoint"></a>Publikování a spuštění z koncového bodu REST

Spusťte následující kód k publikování kanálu do svého pracovního prostoru. Ve vašem pracovním prostoru v Azure Machine Learning Studiu uvidíte metadata pro kanál, včetně historie spuštění a trvání. Kanál můžete také spustit ručně ze sady Studio.

Publikování kanálu umožňuje koncovému bodu REST, který můžete použít ke spuštění kanálu z libovolné knihovny HTTP na libovolné platformě.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Pokud chcete kanál spustit z koncového bodu REST, budete potřebovat hlavičku ověřování typu OAuth2 Bearer. Následující příklad používá interaktivní ověřování (pro ilustraci), ale pro většinu produkčních scénářů, které vyžadují automatizované nebo bezobslužné ověřování, použijte ověřování instančního objektu, jak je [popsáno v tomto článku](how-to-setup-authentication.md).

Ověřování instančního objektu zahrnuje vytvoření *Registrace aplikace* v *Azure Active Directory*. Nejdřív vygenerujete tajný klíč klienta a potom udělíte roli instančního objektu *přístup* k pracovnímu prostoru Machine Learning. [`ServicePrincipalAuthentication`](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?preserve-view=true&view=azure-ml-py)Ke správě toku ověřování použijte třídu. 

Obojí [`InteractiveLoginAuthentication`](/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?preserve-view=true&view=azure-ml-py) a `ServicePrincipalAuthentication` dědí z `AbstractAuthentication` . V obou případech použijte [`get_authentication_header()`](/python/api/azureml-core/azureml.core.authentication.abstractauthentication?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-authentication-header--) funkci ve stejném způsobu, jak načíst hlavičku:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Získejte adresu URL REST z `endpoint` vlastnosti publikovaného objektu kanálu. Adresu URL REST můžete také najít ve vašem pracovním prostoru v Azure Machine Learning Studiu. 

Sestavte požadavek HTTP POST na koncový bod. Zadejte hlavičku ověřování v žádosti. Přidejte objekt datové části JSON, který má název experimentu.

Vytvořte žádost o aktivaci běhu. Chcete-li získat hodnotu ID běhu, přidejte kód pro přístup k `Id` klíči z slovníku odpovědí.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"process_count_per_node": 6}})
run_id = response.json()["Id"]
```

Ke sledování stavu nového běhu použijte ID běhu. Nové spuštění trvá dokončení dalších 10-15. 

Nové spuštění bude vypadat podobně jako u kanálu, který jste spustili dříve v tomto kurzu. Můžete zvolit, aby se nezobrazil úplný výstup.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tuto část neprovádějte, pokud máte v plánu spouštět další kurzy Azure Machine Learning.

### <a name="stop-the-compute-instance"></a>Zastavení výpočetní instance

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Odstranit vše

Pokud neplánujete použít prostředky, které jste vytvořili, odstraňte je, takže se vám neúčtují žádné poplatky:

1. V Azure Portal v nabídce vlevo vyberte **skupiny prostředků**.
1. V seznamu skupin prostředků vyberte skupinu prostředků, kterou jste vytvořili.
1. Vyberte **Odstranit skupinu prostředků**.
1. Zadejte název skupiny prostředků. Pak vyberte **Odstranit**.

Můžete také zachovat skupinu prostředků, ale odstranit jeden pracovní prostor. Zobrazte vlastnosti pracovního prostoru a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu strojového učení se provedly následující úlohy:

> [!div class="checklist"]
> * Vytvořili jste kanál se závislostmi prostředí pro spouštění ve vzdáleném výpočetním prostředku GPU.
> * Vytvořili jste skript bodování pro spouštění Batch předpovědi pomocí předTensorflowho modelu.
> * Publikování kanálu a jeho povolení ke spuštění z koncového bodu REST.

Další příklady vytváření kanálů pomocí sady Machine Learning SDK najdete v [úložišti poznámkových bloků](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).

---
title: 'Kurz: Kanály ML pro dávkové vyhodnocování'
titleSuffix: Azure Machine Learning
description: V tomto kurzu vytvoříte kanál strojového učení k provádění dávkového vyhodnocování na modelu klasifikace bitových bitů. Azure Machine Learning vám umožní soustředit se na strojové učení namísto infrastruktury a automatizace.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: laobri
ms.date: 03/11/2020
ms.openlocfilehash: 1ccd7a7f33c6ee5cab8b7173d8eb93365b6cb587
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472216"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Kurz: Vytvoření kanálu Azure Machine Learning pro dávkové vyhodnocování

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Zjistěte, jak vytvořit kanál v Azure Machine Learning spustit úlohu dávkového vyhodnocování. Kanály strojového učení optimalizují váš pracovní postup s rychlostí, přenositelností a opětovným použitím, takže se můžete soustředit na strojové učení namísto infrastruktury a automatizace. Po sestavení a publikování kanálu nakonfigurujete koncový bod REST, který můžete použít k aktivaci kanálu z libovolné knihovny HTTP na libovolné platformě. 

Příklad používá předem vycvičený model konvoluční neuronové sítě [Inception-V3](https://arxiv.org/abs/1512.00567) implementovaný v Tentenzorflow pro klasifikaci neoznačených obrazů. [Další informace o kanálech strojového učení](concept-ml-pipelines.md).

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> * Konfigurace pracovního prostoru 
> * Stažení a uložení ukázkových dat
> * Vytvoření objektů datové sady pro načtení a výstup dat
> * Stažení, příprava a registrace modelu v pracovním prostoru
> * Zřízení výpočetních cílů a vytvoření skriptu hodnocení
> * Použití `ParallelRunStep` třídy pro asynchronní dávkové vyhodnocování
> * Sestavení, spuštění a publikování kanálu
> * Povolení koncového bodu REST pro kanál

Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte pracovní prostor Azure Machine Learning nebo virtuální počítač s notebookem, dokončete [část 1 kurzu pro nastavení](tutorial-1st-experiment-sdk-setup.md).
* Po dokončení kurzu instalace použijte stejný notebook k otevření *poznámkového bloku kurzy/strojové učení-kanály-pokročilé/tutorial-pipeline-batch-scoring-classification.ipynb.*

Pokud chcete spustit instalační kurz ve svém vlastním [místním prostředí](how-to-configure-environment.md#local), můžete přistupovat k kurzu na [GitHubu](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials). Spuštěním `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps pandas requests` získáte požadované balíčky.

## <a name="configure-workspace-and-create-a-datastore"></a>Konfigurace pracovního prostoru a vytvoření úložiště dat

Vytvořte objekt pracovního prostoru z existujícího pracovního prostoru Azure Machine Learning.

- [Pracovní prostor](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) je třída, která přijímá vaše informace o předplatném Azure a o prostředcích. Pracovní prostor také vytvoří cloudový prostředek, který můžete použít ke sledování a sledování spuštění modelu. 
- `Workspace.from_config()`Přečte `config.json` soubor a potom načte podrobnosti ověřování do objektu s názvem `ws`. Objekt `ws` se používá v kódu v průběhu tohoto kurzu.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

## <a name="create-a-datastore-for-sample-images"></a>Vytvoření úložiště dat pro ukázkové obrazy

Na `pipelinedata` účtu získat ImageNet hodnocení veřejné datové `sampledata` vzorky z veřejného kontejneru objektů blob. Volání `register_azure_blob_container()` zpřístupnit data do pracovního prostoru `images_datastore`pod názvem . Potom nastavte výchozí úložiště dat pracovního prostoru jako výstupní úložiště dat. Použití výstupní úložiště dat skóre výstup v kanálu.

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>Vytváření objektů datové sady

Při vytváření kanálů `Dataset` se objekty používají pro čtení dat `PipelineData` z úložišť dat pracovního prostoru a objekty se používají pro přenos zprostředkujících dat mezi kroky kanálu.

> [!Important]
> Příklad dávkového vyhodnocování v tomto kurzu používá pouze jeden krok kanálu. V případech použití, které mají více kroků, typický tok bude zahrnovat tyto kroky:
>
> 1. Pomocí `Dataset` objektů jako *vstupů* načíst nezpracovaná data, provést některé transformace a potom *výstup* objektu. `PipelineData`
>
> 2. Jako *vstupní objekt*použijte výstupní objekt v předchozím kroku . *output object* `PipelineData` Opakujte jej pro další kroky.

V tomto scénáři `Dataset` vytvoříte objekty, které odpovídají adresářům úložiště dat pro vstupní obrazy i popisky klasifikace (hodnoty y-test). Můžete také `PipelineData` vytvořit objekt pro výstupní data dávkového vyhodnocování.

```python
from azureml.core.dataset import Dataset
from azureml.pipeline.core import PipelineData

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/*.txt"))
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

Dále zaregistrujte datové sady do pracovního prostoru.

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>Stažení a registrace modelu

Stáhněte si předem trénovaný model Tensorflow a použijte jej pro dávkové vyhodnocování v kanálu. Nejprve vytvořte místní adresář, do kterého uložíte model. Potom stáhněte a extrahujte model.

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

Dále zaregistrujte model do pracovního prostoru, abyste mohli model snadno načíst v procesu kanálu. Ve `register()` statické funkci `model_name` je parametr klíčem, který použijete k vyhledání modelu v celé sadě SDK.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Vytvoření a připojení vzdáleného výpočetního cíle

Kanály strojového učení nelze spustit místně, takže je spustíte na cloudových prostředcích nebo *vzdálených výpočetních cílech*. Vzdálený výpočetní cíl je opakovaně použitelné virtuální výpočetní prostředí, ve kterém spouštějíte experimenty a pracovní postupy strojového učení. 

Spusťte následující kód a vytvořte cíl s podporou [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) GPU a připojte ho k pracovnímu prostoru. Další informace o výpočetních cílech naleznete v [koncepčním článku](https://docs.microsoft.com/azure/machine-learning/concept-compute-target).


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

## <a name="write-a-scoring-script"></a>Napsat bodovací skript

Chcete-li provést vyhodnocování, vytvořte dávkový bodovací skript s názvem `batch_scoring.py`a zapište jej do aktuálního adresáře. Skript pořídí vstupní obrazy, použije klasifikační model a potom vyvede předpovědi do souboru výsledků.

Skript `batch_scoring.py` přebírá následující parametry, které se `ParallelRunStep` předají z vytvoření později:

- `--model_name`: Název používaného modelu.
- `--labels_name`: Název souboru, `Dataset` `labels.txt` který obsahuje soubor.

Infrastruktura kanálu `ArgumentParser` používá třídu k předání parametrů do kroků kanálu. Například v následujícím kódu je `--model_name` prvnímu argumentu `model_name`přidělen identifikátor vlastnosti . Ve `init()` funkci `Model.get_model_path(args.model_name)` se používá pro přístup k této vlastnosti.


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


def get_class_label_dict():
    label = []
    proto_as_ascii_lines = tf.gfile.GFile("labels.txt").readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_name', dest="labels_name", required=True)
    args, _ = parser.parse_known_args()

    workspace = Run.get_context(allow_offline=False).experiment.workspace
    label_ds = Dataset.get_by_name(workspace=workspace, name=args.labels_name)
    label_ds.download(target_path='.', overwrite=True)

    label_dict = get_class_label_dict()
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
> Kanál v tomto kurzu má pouze jeden krok a zapíše výstup do souboru. Pro vícekrokové kanály můžete `ArgumentParser` také definovat adresář pro zápis výstupních dat pro vstup do následujících kroků. Příklad předávání dat mezi více kroky kanálu `ArgumentParser` pomocí návrhového vzoru naleznete v [poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-the-pipeline"></a>Sestavení kanálu

Před spuštěním kanálu vytvořte objekt, který definuje prostředí Pythonu `batch_scoring.py` a vytvoří závislosti, které váš skript vyžaduje. Hlavní požadovanou závislostí je Tentenorflow, ale také nainstalovat `azureml-defaults` pro procesy na pozadí. Vytvořte `RunConfiguration` objekt pomocí závislostí. Zadejte také podporu Dockeru a Docker-GPU.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>Vytvoření konfigurace pro zalomení skriptu

Vytvořte krok kanálu pomocí skriptu, konfigurace prostředí a parametrů. Zadejte výpočetní cíl, který jste už připojili k pracovnímu prostoru.

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

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

Krok kanálu je objekt, který zapouzdřuje vše, co potřebujete ke spuštění kanálu, včetně:

* Nastavení prostředí a závislostí
* Výpočetní prostředek pro spuštění kanálu na
* Vstupní a výstupní data a všechny vlastní parametry
* Odkaz na skript nebo logiku sady SDK, která má být spuštěna během kroku

Více tříd dědí [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py)z nadřazené třídy . Můžete si vybrat třídy pro použití konkrétní architektury nebo zásobníky k vytvoření kroku. V tomto příkladu `ParallelRunStep` použijete třídu k definování logiky kroku pomocí vlastního skriptu Pythonu. Pokud je argument skriptu vstupem do kroku nebo výstupem kroku, musí být `arguments` argument definován jak v `output` *poli,* `input` *tak* v parametru nebo v parametru. 

Ve scénářích, kde je více než jeden `outputs` krok, odkaz na objekt v poli k dispozici jako *vstup* pro další krok kanálu.

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

batch_score_step = ParallelRunStep(
    name="parallel-step-test",
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    models=[model],
    arguments=["--model_name", "inception",
               "--labels_name", "label_ds"],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

Seznam všech tříd, které můžete použít pro různé typy kroků, naleznete v [balíčku kroků](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

## <a name="submit-the-pipeline"></a>Odeslat potrubí

Teď spusťte potrubí. Nejprve vytvořte `Pipeline` objekt pomocí odkazu na pracovní prostor a kroku kanálu, který jste vytvořili. Parametr `steps` je pole kroků. V tomto případě je pouze jeden krok pro dávkové bodování. Chcete-li vytvořit kanály, které mají více kroků, umístěte kroky v pořadí v tomto poli.

Dále použijte `Experiment.submit()` funkci k odeslání kanálu k provedení. Můžete také zadat `param_batch_size`vlastní parametr . Funkce `wait_for_completion` výstupy protokoly během procesu sestavení kanálu. Protokoly můžete použít k zobrazení aktuálního průběhu.

> [!IMPORTANT]
> První spuštění potrubí trvá zhruba *15 minut*. Všechny závislosti musí být staženy, je vytvořena image Dockeru a prostředí Pythonu je zřízeno a vytvořeno. Spuštění kanálu znovu trvá podstatně méně času, protože tyto prostředky jsou znovu použity namísto vytvořené. Celková doba běhu pro kanál však závisí na zatížení skriptů a procesů, které jsou spuštěny v každém kroku kanálu.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Stažení a revize výstupu

Spusťte následující kód a stáhněte výstupní `batch_scoring.py` soubor, který je vytvořen ze skriptu. Potom prozkoumejte výsledky bodování.

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

Spusťte následující kód a publikujte kanál do pracovního prostoru. Ve vašem pracovním prostoru ve studiu Azure Machine Learning uvidíte metadata pro kanál, včetně historie spuštění a doby trvání. Můžete také spustit kanál ručně ze studia.

Publikování kanálu umožňuje koncový bod REST, který můžete použít ke spuštění kanálu z libovolné knihovny HTTP na libovolné platformě.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Chcete-li spustit kanál z koncového bodu REST, potřebujete ověřovací hlavičku typu Nosiče OAuth2. Následující příklad používá interaktivní ověřování (pro ilustrační účely), ale pro většinu produkčních scénářů, které vyžadují automatické nebo bezhlavé ověřování, použijte ověřování instančního objektu, jak [je popsáno v tomto článku](how-to-setup-authentication.md).

Ověřování instančního objektu zahrnuje vytvoření *registrace aplikace* ve službě Azure *Active Directory*. Nejprve vygenerujete tajný klíč klienta a pak udělíte hlavní roli služby *přístup k* pracovnímu prostoru strojového učení. Pomocí [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) třídy můžete spravovat tok ověřování. 

Oba [`InteractiveLoginAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?view=azure-ml-py) `ServicePrincipalAuthentication` a `AbstractAuthentication`dědit od . V obou případech [`get_authentication_header()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.abstractauthentication?view=azure-ml-py#get-authentication-header--) použijte funkci stejným způsobem k načtení záhlaví:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Získejte adresu URL `endpoint` REST z vlastnosti publikovaného objektu kanálu. Adresu URL rest najdete také ve vašem pracovním prostoru ve studiu Azure Machine Learning. 

Vytvořte požadavek HTTP POST do koncového bodu. Zadejte ověřovací hlavičku v požadavku. Přidejte datový objekt JSON, který má název experimentu a parametr velikosti dávky. Jak je uvedeno dříve `param_batch_size` v kurzu, `batch_scoring.py` je předán do `PipelineParameter` skriptu, protože jste jej definovali jako objekt v konfiguraci kroku.

Proveďte požadavek na spuštění. Zahrnout kód pro `Id` přístup ke klíči ze slovníku odpovědí získat hodnotu id spuštění.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Pomocí ID spuštění můžete sledovat stav nového spuštění. Nový běh trvá dalších 10-15 minut až do konce. 

Nové spuštění bude vypadat podobně jako kanál, který jste spustili dříve v kurzu. Můžete se rozhodnout, že nebudete zobrazovat celý výstup.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nedokončete tuto část, pokud plánujete spustit další kurzy Azure Machine Learning.

### <a name="stop-the-compute-instance"></a>Zastavení výpočetní instance

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Odstranit vše

Pokud neplánujete použít prostředky, které jste vytvořili, odstraňte je, abyste vám nevznikly žádné poplatky:

1. Na portálu Azure vyberte v levé nabídce **skupiny prostředků**.
1. V seznamu skupin prostředků vyberte skupinu prostředků, kterou jste vytvořili.
1. Vyberte **Odstranit skupinu prostředků**.
1. Zadejte název skupiny prostředků. Potom vyberte **Odstranit**.

Skupinu prostředků můžete také zachovat, ale odstranit jeden pracovní prostor. Zobrazte vlastnosti pracovního prostoru a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu kanálu strojového učení jste provedli následující úkoly:

> [!div class="checklist"]
> * Vytvořeno kanálu se závislostmi prostředí pro spuštění na vzdáleném výpočetním prostředku GPU.
> * Vytvořil bodovací skript pro spuštění predikcí dávky pomocí předem trénovaného modelu Tensorflow.
> * Publikoval kanál a povolil jeho spuštění z koncového bodu REST.

Další příklady vytváření kanálů pomocí sady SDK pro strojové učení najdete v [tématu úložiště poznámkových bloků](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).

---
title: 'Kurz: Kanály strojového učení pro dávkové vyhodnocování'
titleSuffix: Azure Machine Learning
description: Sestavte kanál strojového učení pro spuštění dávkového vyhodnocování pro model klasifikace obrázků v Azure Machine Learning. Kanály strojového učení optimalizují pracovní postup s využitím rychlosti, přenositelnosti a opakovaného použití, takže se můžete soustředit na vaše odbornosti a strojové učení – místo na infrastruktuře a automatizaci.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/05/2019
ms.openlocfilehash: 978cfa7926e7a035494aae11351c15a45c0251e4
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350431"
---
# <a name="use-a-machine-learning-pipeline-for-batch-scoring"></a>Použití kanálu strojového učení pro dávkové vyhodnocování

V tomto kurzu použijete kanál v Azure Machine Learning ke spuštění úlohy dávkového vyhodnocování. V příkladu se používá [předem](https://arxiv.org/abs/1512.00567) vydaný model Tensorflow sítě konvoluční neuronové pro klasifikaci neoznačených obrázků. Po sestavení a publikování kanálu nakonfigurujete koncový bod REST, který můžete použít ke spuštění kanálu z libovolné knihovny HTTP na libovolné platformě.

Kanály strojového učení optimalizují pracovní postup s využitím rychlosti, přenositelnosti a opakovaného použití, takže se můžete soustředit na vaše odbornosti a strojové učení – místo na infrastruktuře a automatizaci. [Přečtěte si další informace o kanálech strojového učení](concept-ml-pipelines.md).

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> * Konfigurace pracovního prostoru a stažení ukázkových dat
> * Vytvoření datových objektů pro načtení a výstup dat
> * Stažení, příprava a registrace modelu v pracovním prostoru
> * Zřizování výpočetních cílů a vytvoření skriptu bodování
> * Sestavování, spouštění a publikování kanálu
> * Povolit koncový bod REST pro kanál

Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte virtuální počítač s Azure Machine Learning pracovním prostorem nebo notebookem, vyplňte [část 1 tohoto kurzu instalace](tutorial-1st-experiment-sdk-setup.md).
* Po dokončení kurzu instalace použijte stejný server poznámkového bloku a otevřete Poznámkový blok *kurzy/tutorial-Pipeline-Batch-scoring-Classification. ipynb* .

Pokud chcete spustit kurz nastavení ve vlastním [místním prostředí](how-to-configure-environment.md#local), můžete získat přístup k kurzu na [GitHubu](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials). Spusťte `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` , abyste získali požadované balíčky.

## <a name="configure-workspace-and-create-a-datastore"></a>Konfigurace pracovního prostoru a vytvoření úložiště dat

Vytvořte objekt pracovního prostoru z existujícího pracovního prostoru Azure Machine Learning.

- [Pracovní prostor](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) je třída, která přijímá vaše předplatné a informace o prostředcích Azure. Pracovní prostor také vytvoří cloudový prostředek, který můžete použít k monitorování a sledování spuštění modelu. 
- `Workspace.from_config()` přečte soubor `config.json` a potom načte podrobnosti ověřování do objektu s názvem `ws`. Objekt `ws` se používá v kódu v celém tomto kurzu.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>Vytvoření úložiště dat pro ukázkové image

Na účtu `pipelinedata` Získejte ukázku veřejné dat vyhodnocení ImageNet z veřejného kontejneru objektů BLOB `sampledata`. Voláním `register_azure_blob_container()` zpřístupníte data v pracovním prostoru pod názvem `images_datastore`. Pak nastavte výchozí úložiště dat pracovního prostoru jako výstupní úložiště dat. Použijte výstupní úložiště dat k určení skóre výstupu v kanálu.

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-data-objects"></a>Vytváření datových objektů

Když vytvoříte kanál, objekt `DataReference` načte data z úložiště dat pracovního prostoru. Objekt `PipelineData` přenáší mezilehlá data mezi kroky kanálu.

> [!Important]
> Příklad dávkového vyhodnocování v tomto kurzu používá pouze jeden krok kanálu. V případech použití, které mají více kroků, bude typický tok zahrnovat tyto kroky:
>
> 1. Pro načtení nezpracovaných dat použijte objekty `DataReference` jako *vstupy* , proveďte nějakou transformaci a pak *výstup* objektu `PipelineData`.
>
> 2. Použijte *výstupní objekt* `PipelineData` v předchozím kroku jako *vstupní objekt*. Opakujte postup pro následné kroky.

V tomto scénáři vytvoříte objekty `DataReference`, které odpovídají adresářům úložiště dat pro vstupní image a popisky klasifikace (hodnoty y-test). Také vytvoříte `PipelineData` objekt pro výstupní data dávkového vyhodnocování.

```python
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

input_images = DataReference(datastore=batchscore_blob, 
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download"
                            )

label_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download"                          
                         )

output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
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

Dále zaregistrujte model do svého pracovního prostoru, abyste ho mohli snadno načíst v procesu kanálu. `register()` Ve funkci`model_name` static je parametr klíč, který použijete k vyhledání modelu v rámci sady SDK.

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

Spusťte následující kód pro vytvoření cíle [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) s povoleným GPU a pak ho připojte k pracovnímu prostoru. Další informace o cílových výpočetních operacích najdete v [článku konceptu](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target).


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

Chcete-li provést bodování, vytvořte skript vyhodnocování dávkového vyhodnocování s názvem `batch_scoring.py` a pak jej zapište do aktuálního adresáře. Skript bere vstupní image, použije model klasifikace a pak výstup předpovědi do souboru výsledků.

Skript `batch_scoring.py` používá následující parametry, které jsou předány z kroku kanálu, který vytvoříte později v tomto kurzu:

- `--model_name`: Název používaného modelu.
- `--label_dir`: Adresář, který obsahuje soubor `labels.txt`.
- `--dataset_path`: Adresář, který obsahuje vstupní obrázky.
- `--output_dir`: Výstupní adresář pro soubor `results-label.txt` po spuštění skriptu spustí model pro data.
- `--batch_size`: Velikost dávky použitá při spuštění modelu.

Infrastruktura kanálu používá třídu `ArgumentParser` k předání parametrů do kroků kanálu. Například v následujícím kódu je první argument `--model_name`, kterému je přiřazen identifikátor vlastnosti `model_name`. Ve funkci `main()` se pro přístup k této vlastnosti používá `Model.get_model_path(args.model_name)`.


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
from azureml.core.model import Model

slim = tf.contrib.slim

parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
parser.add_argument('--model_name', dest="model_name", required=True)
parser.add_argument('--label_dir', dest="label_dir", required=True)
parser.add_argument('--dataset_path', dest="dataset_path", required=True)
parser.add_argument('--output_dir', dest="output_dir", required=True)
parser.add_argument('--batch_size', dest="batch_size", type=int, required=True)

args = parser.parse_args()

image_size = 299
num_channel = 3

# create output directory if it does not exist
os.makedirs(args.output_dir, exist_ok=True)


def get_class_label_dict(label_file):
    label = []
    proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


class DataIterator:
    def __init__(self, data_dir):
        self.file_paths = []
        image_list = os.listdir(data_dir)
        self.file_paths = [data_dir + '/' + file_name.rstrip() for file_name in image_list]
        self.labels = [1 for file_name in self.file_paths]

    @property
    def size(self):
        return len(self.labels)

    def input_pipeline(self, batch_size):
        images_tensor = tf.convert_to_tensor(self.file_paths, dtype=tf.string)
        labels_tensor = tf.convert_to_tensor(self.labels, dtype=tf.int64)
        input_queue = tf.train.slice_input_producer([images_tensor, labels_tensor], shuffle=False)
        labels = input_queue[1]
        images_content = tf.read_file(input_queue[0])

        image_reader = tf.image.decode_jpeg(images_content, channels=num_channel, name="jpeg_reader")
        float_caster = tf.cast(image_reader, tf.float32)
        new_size = tf.constant([image_size, image_size], dtype=tf.int32)
        images = tf.image.resize_images(float_caster, new_size)
        images = tf.divide(tf.subtract(images, [0]), [255])

        image_batch, label_batch = tf.train.batch([images, labels], batch_size=batch_size, capacity=5 * batch_size)
        return image_batch


def main(_):
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)
    count = 0

    # get model from model registry
    model_path = Model.get_model_path(args.model_name)

    with tf.Session() as sess:
        test_images = test_feeder.input_pipeline(batch_size=args.batch_size)
        with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
            input_images = tf.placeholder(tf.float32, [args.batch_size, image_size, image_size, num_channel])
            logits, _ = inception_v3.inception_v3(input_images,
                                                  num_classes=classes_num,
                                                  is_training=False)
            probabilities = tf.argmax(logits, 1)

        sess.run(tf.global_variables_initializer())
        sess.run(tf.local_variables_initializer())
        coord = tf.train.Coordinator()
        threads = tf.train.start_queue_runners(sess=sess, coord=coord)
        saver = tf.train.Saver()
        saver.restore(sess, model_path)
        out_filename = os.path.join(args.output_dir, "result-labels.txt")
        with open(out_filename, "w") as result_file:
            i = 0
            while count < total_size and not coord.should_stop():
                test_images_batch = sess.run(test_images)
                file_names_batch = test_feeder.file_paths[i * args.batch_size:
                                                          min(test_feeder.size, (i + 1) * args.batch_size)]
                results = sess.run(probabilities, feed_dict={input_images: test_images_batch})
                new_add = min(args.batch_size, total_size - count)
                count += new_add
                i += 1
                for j in range(new_add):
                    result_file.write(os.path.basename(file_names_batch[j]) + ": " + label_dict[results[j]] + "\n")
                result_file.flush()
            coord.request_stop()
            coord.join(threads)

        shutil.copy(out_filename, "./outputs/")

if __name__ == "__main__":
    tf.app.run()

```

> [!TIP]
> Kanál v tomto kurzu má pouze jeden krok a zapisuje výstup do souboru. U kanálů s více kroky můžete také pomocí `ArgumentParser` definovat adresář, do kterého se zapisují výstupní data pro vstup do následujících kroků. Příklad předávání dat mezi různými kroky kanálu pomocí vzoru návrhu `ArgumentParser` najdete v [poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-and-run-the-pipeline"></a>Sestavení a spuštění kanálu

Před spuštěním kanálu vytvořte objekt, který definuje prostředí Python, a vytvoří závislosti, které váš skript `batch_scoring.py` vyžaduje. Hlavní požadovaná závislost je Tensorflow, ale také nainstalujete `azureml-defaults` ze sady SDK pro procesy na pozadí. Vytvořte objekt `RunConfiguration` pomocí závislostí. Také zadejte Docker a Docker-GPU support.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import CondaDependencies, RunConfiguration

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])

amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="parameterize-the-pipeline"></a>Parametrizovat kanál

Definujte vlastní parametr pro kanál, který bude řídit velikost dávky. Po publikování a zpřístupnění kanálu prostřednictvím koncového bodu REST jsou také zpřístupněny všechny nakonfigurované parametry. Vlastní parametry můžete zadat v datové části JSON při opětovném spuštění kanálu přes požadavek HTTP.

Vytvořte objekt `PipelineParameter` pro povolení tohoto chování a definování názvu a výchozí hodnoty.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(name="param_batch_size", default_value=20)
```

### <a name="create-the-pipeline-step"></a>Vytvoření kanálu krok

Krok kanálu je objekt, který zapouzdřuje všechno, co potřebujete ke spuštění kanálu, včetně:

* nastavení prostředí a závislostí
* výpočetní prostředek, na kterém se má kanál spustit
* vstupní a výstupní data a libovolné vlastní parametry
* Odkaz na skript nebo logiku sady SDK, které se spustí během kroku

Více tříd dědí z nadřazené třídy [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py). Můžete zvolit třídy pro použití specifických rozhraní nebo zásobníků k vytvoření kroku. V tomto příkladu použijete třídu [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) k definování kroku logiky pomocí vlastního skriptu Pythonu. Je-li argument skriptu buď vstupem do kroku, nebo výstupem kroku, musí být *argument definován v* poli `arguments` *a* v parametru `input` @no__t nebo v uvedeném pořadí. 

Ve scénářích, kde je více než jeden krok, se odkaz na objekt v poli `outputs` zpřístupní jako *vstup* pro další krok kanálu.

```python
from azureml.pipeline.steps import PythonScriptStep

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_scoring.py",
    arguments=["--dataset_path", input_images, 
               "--model_name", "inception",
               "--label_dir", label_dir, 
               "--output_dir", output_dir, 
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config
)
```

Seznam všech tříd, které lze použít pro různé typy kroků, naleznete v [balíčku kroků](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

### <a name="run-the-pipeline"></a>Spuštění kanálu

Teď kanál spusťte. Nejprve vytvořte objekt `Pipeline` pomocí odkazu na pracovní prostor a kroku kanálu, který jste vytvořili. Parametr `steps` je pole kroků. V tomto případě je pro dávkové vyhodnocování k dispozici pouze jeden krok. Chcete-li vytvořit kanály, které mají více kroků, umístěte kroky v tomto poli do pořadí.

Dále pomocí funkce `Experiment.submit()` odešlete kanál ke spuštění. Zadejte také vlastní parametr `param_batch_size`. Funkce `wait_for_completion` ukládá do procesu sestavení kanálu protokoly výstupů. Pomocí protokolů můžete zobrazit aktuální průběh.

> [!IMPORTANT]
> První spuštění kanálu trvá přibližně *15 minut*. Všechny závislosti je nutné stáhnout, vytvoří se image Docker a prostředí Pythonu se zřídí a vytvoří. Opětovné spuštění kanálu bude trvat mnohem méně času, protože se místo vytvoření znovu použijí tyto prostředky. Celková doba běhu pro kanál ale závisí na zatížení skriptů a procesech, které jsou spuštěné v jednotlivých krocích kanálu.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Stáhnout a zkontrolovat výstup

Spuštěním následujícího kódu Stáhněte výstupní soubor, který je vytvořen ze skriptu `batch_scoring.py`. Pak prozkoumejte výsledky bodování.

```python
import pandas as pd

step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th: pouze of-type {vertical-align: uprostřed;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Název souboru</th>
      <th>Předpověď</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>ILSVRC2012_val_00000102. JPEG</td>
      <td>Rhodesian Ridgeback</td>
    </tr>
    <tr>
      <td>1</td>
      <td>ILSVRC2012_val_00000103. JPEG</td>
      <td>tripod</td>
    </tr>
    <tr>
      <td>2</td>
      <td>ILSVRC2012_val_00000104. JPEG</td>
      <td>klávesnice psacího stroje</td>
    </tr>
    <tr>
      <td>3</td>
      <td>ILSVRC2012_val_00000105. JPEG</td>
      <td>hedvábí Terrier</td>
    </tr>
    <tr>
      <td>4</td>
      <td>ILSVRC2012_val_00000106. JPEG</td>
      <td>Windsor</td>
    </tr>
    <tr>
      <td>5</td>
      <td>ILSVRC2012_val_00000107. JPEG</td>
      <td>sklizeň</td>
    </tr>
    <tr>
      <td>6</td>
      <td>ILSVRC2012_val_00000108. JPEG</td>
      <td>Violin</td>
    </tr>
    <tr>
      <td>7</td>
      <td>ILSVRC2012_val_00000109. JPEG</td>
      <td>reproduktor</td>
    </tr>
    <tr>
      <td>8</td>
      <td>ILSVRC2012_val_00000110. JPEG</td>
      <td>odbavovací</td>
    </tr>
    <tr>
      <td>9</td>
      <td>ILSVRC2012_val_00000111. JPEG</td>
      <td>USA – sever</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="publish-and-run-from-a-rest-endpoint"></a>Publikování a spuštění z koncového bodu REST

Spusťte následující kód k publikování kanálu do svého pracovního prostoru. V pracovním prostoru v Azure Portal uvidíte metadata pro kanál, včetně historie spuštění a trvání. Kanál můžete také spustit ručně z portálu.

Publikování kanálu umožňuje koncovému bodu REST, který můžete použít ke spuštění kanálu z libovolné knihovny HTTP na libovolné platformě.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Pokud chcete kanál spustit z koncového bodu REST, budete potřebovat hlavičku ověřování typu OAuth2 Bearer. Následující příklad používá interaktivní ověřování (pro ilustraci), ale u většiny produkčních scénářů, které vyžadují automatizované nebo bezobslužné ověřování, použijte ověřování instančního objektu, jak je [popsáno v tomto poznámkovém bloku](https://aka.ms/pl-restep-auth).

Ověřování instančního objektu zahrnuje vytvoření *Registrace aplikace* v *Azure Active Directory*. Nejdřív vygenerujete tajný klíč klienta a potom udělíte roli instančního objektu *přístup* k pracovnímu prostoru Machine Learning. Ke správě toku ověřování použijte třídu [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) . 

@No__t-0 a `ServicePrincipalAuthentication` dědí z `AbstractAuthentication`. V obou případech použijte funkci `get_authentication_header()` ve stejném způsobu, jak načíst hlavičku:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Získejte adresu URL REST z vlastnosti `endpoint` publikovaného objektu kanálu. Adresu URL REST můžete najít také v pracovním prostoru v Azure Portal. 

Sestavte požadavek HTTP POST na koncový bod. Zadejte hlavičku ověřování v žádosti. Přidejte objekt datové části JSON, který má název experimentu a parametr velikosti dávky. Jak bylo uvedeno výše v tomto kurzu, `param_batch_size` se předává do skriptu `batch_scoring.py`, protože jste ho v konfiguraci kroku definovali jako objekt `PipelineParameter`.

Vytvořte žádost o aktivaci běhu. Přidejte kód pro přístup k klíči `Id` z slovníku odpovědí, abyste získali hodnotu ID běhu.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
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

### <a name="stop-the-notebook-vm"></a>Zastavení virtuálního počítače poznámkového bloku

Pokud jste použili server cloudového poznámkového bloku a chcete snížit náklady, zastavte virtuální počítač, když ho your'e nepoužíváte:

1. V pracovním prostoru vyberte **virtuální počítače poznámkového bloku**.
1. V seznamu virtuálních počítačů vyberte virtuální počítač, který chcete zastavit.
1. Vyberte **zastavit**.
1. Až budete chtít znovu použít server, vyberte **Spustit**.

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

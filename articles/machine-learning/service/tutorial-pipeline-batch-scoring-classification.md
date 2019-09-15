---
title: 'Kurz: Kanály Azure ML pro dávkové vyhodnocování'
titleSuffix: Azure Machine Learning
description: Vytvořte kanál ML pro spuštění dávkového vyhodnocování pro model klasifikace obrázků. Kanály strojového učení optimalizují pracovní postup s využitím rychlosti, přenositelnosti a opakovaného použití, abyste se mohli soustředit na vaše odbornosti, strojové učení, nikoli na infrastrukturu a automatizaci.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/05/2019
ms.openlocfilehash: aa280ea71735ce7c4000951e5c11de2913aaebe8
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001623"
---
# <a name="use-azure-machine-learning-pipelines-for-batch-scoring"></a>Použití Azure Machine Learningch kanálů pro dávkové vyhodnocování

V tomto kurzu použijete Azure Machine Learning kanály ke spuštění bodování dávky nebo odvození úlohy. Tato příklad úlohy používá předem [vyškolený](https://arxiv.org/abs/1512.00567) model konvoluční neuronové Network Tensorflow pro klasifikaci neoznačených obrázků. Po sestavení a publikování kanálu nakonfigurujete koncový bod REST, abyste mohli kanál aktivovat z libovolné knihovny HTTP na libovolné platformě.

Kanály strojového učení optimalizují pracovní postup s využitím rychlosti, přenositelnosti a opakovaného použití, abyste se mohli soustředit na vaše odbornosti, strojové učení, nikoli na infrastrukturu a automatizaci. [Přečtěte si další informace o kanálech ml](concept-ml-pipelines.md).

V tomto kurzu se seznámíte s následujícími úlohami:

> [!div class="checklist"]
> * Konfigurace pracovního prostoru a stažení ukázkových dat
> * Vytvoření datových objektů pro načtení a výstup dat
> * Stáhněte, připravte a zaregistrujte model do svého pracovního prostoru.
> * Zřizování výpočetních cílů a vytvoření skriptu bodování
> * Sestavování, spouštění a publikování kanálu
> * Povolit koncový bod REST pro kanál

Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte virtuální počítač s Azure Machine Learning pracovním prostorem nebo poznámkovým blokem, vyplňte [část 1 kurzu instalace](tutorial-1st-experiment-sdk-setup.md) .
* Po dokončení kurzu instalace otevřete Poznámkový blok **kurzy/tutorial-Pipeline-Batch-scoring-Classification. ipynb** pomocí stejného serveru poznámkového bloku.

Tento kurz je také k dispozici na [GitHubu](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) , pokud ho chcete spustit ve vašem vlastním [místním prostředí](how-to-configure-environment.md#local). Spusťte `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` , abyste získali požadované balíčky.

## <a name="configure-workspace-and-create-datastore"></a>Konfigurace pracovního prostoru a vytvoření úložiště dat

Vytvořte objekt pracovního prostoru z existujícího pracovního prostoru Azure Machine Learning. 
+ [Pracovní prostor](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) je třída, která přijímá vaše předplatné a informace o prostředcích Azure. Vytvoří také cloudový prostředek pro monitorování a sledování spuštění modelu. 

+ `Workspace.from_config()`přečte soubor **config. JSON** a načte podrobnosti ověřování do objektu s názvem `ws`. `ws` se používá ve zbývající části kódu v tomto kurzu.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>Vytvoření úložiště dat pro ukázkové image

Získejte ukázku veřejného data ImageNet Evaluation z veřejného kontejneru `sampledata` objektů blob na účtu. `pipelinedata` Volání `register_azure_blob_container()` zpřístupňuje data v pracovním prostoru pod názvem `images_datastore`. Pak zadejte výchozí úložiště dat pracovního prostoru jako výstupní úložiště dat, které použijete pro vyhodnocování výstupu kanálu.

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

Při sestavování kanálů `DataReference` se objekty používají pro čtení dat z úložiště dat v pracovním prostoru a `PipelineData` objekty slouží k přenosu mezilehlých dat mezi jednotlivými kroky kanálu.

> [!Important]
> Tento příklad dávkového vyhodnocování používá pouze jeden krok kanálu, ale v případech použití s více kroky bude typický tok zahrnovat:
>
> 1. Použití `DataReference` objektů jako **vstupů** k načtení nezpracovaných dat, provádění některých transformací a `PipelineData` pak výstup objektu.
>
> 2. Použijte **výstupní objekt** předchozího kroku `PipelineData` jako *vstupní objekt*, který se opakuje pro následné kroky.

V tomto scénáři vytvoříte `DataReference` objekty odpovídající adresářům úložiště dat pro vstupní image i popisky klasifikace (hodnoty y-test). Také vytvoříte `PipelineData` objekt pro výstupní data dávkového vyhodnocování.

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

Stáhněte si předem vyškolený model Tensorflow, který se použije pro dávkové vyhodnocování v kanálu. Nejdřív vytvořte místní adresář, kam model uložíte, a pak ho stáhněte a extrahujte.

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

Nyní tento model zaregistrujete do svého pracovního prostoru, který umožňuje jeho snadné načtení v procesu kanálu. `register()` Ve funkci`model_name` static je parametr klíč, který použijete k vyhledání modelu v rámci sady SDK.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-remote-compute-target"></a>Vytvořit a připojit vzdálený cíl výpočtů

Vzhledem k tomu, že kanály ML nelze spustit místně, je nutné je spustit na cloudových prostředcích. Budeme na ně odkazovat jako na vzdálené výpočetní cíle, což jsou opakovaně použitelná virtuální výpočetní prostředí, ve kterých spouštíte experimenty a pracovní postupy ML. Spusťte následující kód pro vytvoření cíle s povoleným [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) GPU a připojte ho k pracovnímu prostoru. Další informace o výpočetních cílech najdete v [koncepčním článku](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target) .


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

Chcete-li provést vyhodnocování, vytvořte skript `batch_scoring.py`vyhodnocování dávky a zapište ho do aktuálního adresáře. Skript bere vstupní image, použije model klasifikace a výstup předpovědi do souboru výsledků.

Skript `batch_scoring.py` přebírá následující parametry, které se předávají z kroku kanálu, který vytvoříte později v tomto kurzu:

- `--model_name`: název používaného modelu
- `--label_dir`: adresář, který uchovává `labels.txt` soubor 
- `--dataset_path`: adresář obsahující vstupní image
- `--output_dir`: skript spustí model pro data a výstup `results-label.txt` a do tohoto adresáře.
- `--batch_size`: velikost dávky použitou při spuštění modelu

Infrastruktura kanálů používá `ArgumentParser` třídu k předání parametrů do kroků kanálu. Například v kódu pod prvním argumentem `--model_name` je uveden identifikátor `model_name`vlastnosti. Ve funkci je k této vlastnosti přistupovaná `Model.get_model_path(args.model_name)`pomocí. `main()`


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
> Kanál v tomto kurzu má pouze jeden krok a zapisuje výstup do souboru, ale u kanálů s více kroky můžete také použít `ArgumentParser` k definování adresáře pro zápis výstupních dat pro vstup do následujících kroků. Příklad předávání dat mezi různými kroky kanálu pomocí `ArgumentParser` vzoru návrhu najdete v [poznámkovém bloku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) .

## <a name="build-and-run-the-pipeline"></a>Sestavení a spuštění kanálu

Před spuštěním kanálu vytvoříte objekt definující prostředí Python a závislosti, které váš skript `batch_scoring.py`potřebuje. Hlavní požadovaná závislost je Tensorflow, ale také se instaluje `azureml-defaults` pro procesy na pozadí ze sady SDK. `RunConfiguration` Vytvořte objekt pomocí závislostí a také zadejte Docker a Docker-GPU support.

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

Definujte vlastní parametr pro kanál, který bude řídit velikost dávky. Po publikování a zpřístupnění kanálu prostřednictvím koncového bodu REST jsou při opětovném spuštění kanálu s požadavkem HTTP také zpřístupněny všechny nakonfigurované parametry a mohou být zadány v datové části JSON.

`PipelineParameter` Vytvořte objekt pro povolení tohoto chování a definujte název a výchozí hodnotu.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(name="param_batch_size", default_value=20)
```

### <a name="create-the-pipeline-step"></a>Vytvoření kanálu krok

Krok kanálu je objekt, který zapouzdřuje všechno, co potřebujete ke spuštění kanálu, včetně:

* nastavení prostředí a závislostí
* výpočetní prostředek, na kterém se má kanál spustit
* vstupní a výstupní data a libovolné vlastní parametry
* odkaz na skript nebo logiku sady SDK ke spuštění během kroku

Existuje několik tříd, které dědí z nadřazené třídy [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) pro pomoc s vytvořením kroku pomocí určitých rozhraní a zásobníků. V tomto příkladu použijete [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) třídu k definování kroku logiky pomocí vlastního skriptu jazyka Python. Všimněte si, že pokud je argument skriptu buď vstupem do kroku, nebo výstupem kroku, musí **být definován v** `arguments` poli a `input` **také** buď v parametru nebo `output` , v uvedeném pořadí. 

Odkaz na objekt v `outputs` poli se zpřístupní jako **vstup** pro následný krok kanálu, a to pro scénáře, kde je více než jeden krok.

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

Seznam všech tříd pro různé typy kroků naleznete v [balíčku kroků](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

### <a name="run-the-pipeline"></a>Spuštění kanálu

Nyní kanál spustíte. Nejprve vytvořte `Pipeline` objekt s odkazem na pracovní prostor a krokem kanálu, který jste vytvořili. `steps` Parametr je pole kroků a v tomto případě je pro dávkové vyhodnocování k dispozici pouze jeden krok. Chcete-li vytvořit kanály s více kroky, umístěte kroky v tomto poli do pole pořadí.

Dále použijte `Experiment.submit()` funkci k odeslání kanálu ke spuštění. Zadejte také vlastní parametr `param_batch_size`. `wait_for_completion` Funkce vytvoří výstup protokolů během procesu sestavení kanálu, což vám umožní zobrazit aktuální průběh.

> [!IMPORTANT]
> První spuštění kanálu trvá přibližně **15 minut**, protože se musí stáhnout všechny závislosti, vytvoří se image Docker a prostředí Pythonu se zřídí/vytvoří. Spuštění bude zase trvat delší dobu, než se tyto prostředky znovu použijí. Celková doba běhu ale závisí na zatížení skriptů a procesů spuštěných v jednotlivých krocích kanálu.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Stáhnout a zkontrolovat výstup

Spusťte následující kód ke stažení výstupního souboru vytvořeného ze `batch_scoring.py` skriptu a pak prozkoumejte výsledky bodování.

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
      <td>Rhodesian ridgeback</td>
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

## <a name="publish-and-run-from-rest-endpoint"></a>Publikování a spuštění z koncového bodu REST

Spusťte následující kód k publikování kanálu do svého pracovního prostoru. Ve vašem pracovním prostoru na portálu uvidíte metadata pro kanál, včetně historie spuštění a trvání. Kanál můžete také spustit ručně z portálu.

Publikování kanálu navíc umožňuje koncovému bodu REST znovu spustit kanál z jakékoli knihovny HTTP na libovolné platformě.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Pokud chcete kanál spustit z koncového bodu REST, budete nejdřív potřebovat hlavičku ověřování typu OAuth2 Bearer. Tento příklad používá pro ilustraci interaktivní ověřování, ale pro většinu produkčních scénářů vyžadujících automatizované nebo bezobslužné ověřování použijte princip použití služby, jak je [popsáno v tomto poznámkovém bloku](https://aka.ms/pl-restep-auth).

Princip ověřování služby zahrnuje vytvoření **Registrace aplikace** v **Azure Active Directory**, generování tajného klíče klienta a udělení **přístupu role** instančního objektu vašemu pracovnímu prostoru Machine Learning. Pak použijete [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) ke správě toku ověřování třídu. 

Obojí `InteractiveLoginAuthentication` a `ServicePrincipalAuthentication` dědí `get_authentication_header()` z `AbstractAuthentication`a v obou případech použijete funkci stejným způsobem, jak načíst hlavičku.

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Získejte adresu URL REST z `endpoint` vlastnosti publikovaného objektu kanálu. Adresu URL REST můžete také najít ve vašem pracovním prostoru na portálu. Sestavte požadavek HTTP POST na koncový bod a zadejte hlavičku ověřování. Kromě toho přidejte objekt datové části JSON s názvem experimentu a parametrem velikosti dávky. Jako připomenutí `param_batch_size` se předává `batch_scoring.py` do skriptu, protože `PipelineParameter` jste ho definovali jako objekt v konfiguraci kroku.

Vytvořte žádost o aktivaci běhu. Přístup ke `Id` klíči z slovníku odpovědí vám umožní získat hodnotu ID běhu.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Ke sledování stavu nového běhu použijte ID běhu. Bude to trvat déle než 10-15 minut a bude vypadat podobně jako předchozí spuštění kanálu, takže pokud nepotřebujete vidět jiné spuštění kanálu, můžete přeskočit celý výstup.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tuto část neprovádějte, pokud máte v plánu spouštět jiné kurzy Azure Machine Learning.

### <a name="stop-the-notebook-vm"></a>Zastavení virtuálního počítače poznámkového bloku

Pokud jste použili server cloudového poznámkového bloku, zastavte virtuální počítač, pokud ho nepoužíváte ke snížení nákladů.

1. V pracovním prostoru vyberte **virtuální počítače poznámkového bloku**.
1. V seznamu vyberte virtuální počítač.
1. Vyberte **zastavit**.
1. Až budete chtít znovu použít server, vyberte **Spustit**.

### <a name="delete-everything"></a>Odstranit vše

Pokud neplánujete použít prostředky, které jste vytvořili, odstraňte je, takže se vám neúčtují žádné poplatky.

1. Úplně nalevo na webu Azure Portal vyberte **Skupiny prostředků**.
1. V seznamu vyberte skupinu prostředků, kterou jste vytvořili.
1. Vyberte **Odstranit skupinu prostředků**.
1. Zadejte název skupiny prostředků. Vyberte **Odstranit**.

Můžete také zachovat skupinu prostředků, ale odstranit jeden pracovní prostor. Zobrazte vlastnosti pracovního prostoru a vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu strojového učení se provedly následující úlohy:

> [!div class="checklist"]
> * Vytvoření kanálu se závislostmi prostředí pro spuštění ve vzdáleném výpočetním prostředku GPU
> * Vytvořili jste skript bodování pro spuštění dávkového předpovědiu s předem vyškoleným modelem Tensorflow
> * Publikování kanálu a jeho povolení ke spuštění z koncového bodu REST

Další příklady vytváření kanálů pomocí sady Machine Learning SDK najdete v [úložišti poznámkových bloků](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines) .

---
title: Spuštění dávkového předpovědiu pro velké objemy dat s kanály
titleSuffix: Azure Machine Learning
description: Naučte se asynchronně předpovědi Batch na velkých objemech dat pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 07/12/2019
ms.openlocfilehash: 910974eac6a67c9c9fe68c502f2876ef68bb94eb
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028521"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-pipelines"></a>Spuštění dávkového předpovědiu pro velké datové sady s kanály Azure Machine Learning

V tomto článku se dozvíte, jak předpovědiovat velké objemy dat asynchronně pomocí kanálů ML s Azure Machine Learning.

Předpovědi dávky (nebo vyhodnocování dávek) poskytuje nákladově efektivní odvození s využitím neparalelní propustnosti pro asynchronní aplikace. Kanály předpovědi pro dávku se můžou škálovat, aby se provádělo odvozování z terabajtů produkčních dat. Předpověď dávky je optimalizovaná pro vysokou propustnost, předpovědi s požárem a zapomenout pro velkou kolekci dat.

>[!TIP]
> Pokud váš systém vyžaduje zpracování s nízkou latencí (rychlé zpracování jediného dokumentu nebo malé sady dokumentů), místo předpovědi dávky použijte [bodování v reálném čase](how-to-consume-web-service.md) .

V následujících krocích vytvoříte [kanál strojového učení](concept-ml-pipelines.md) pro registraci předem vyučeného modelu počítačové vize ([čas zahájení-V3](https://arxiv.org/abs/1512.00567)). Pak použijete předem vyškolený model pro dávkové vyhodnocování pro Image dostupné v účtu úložiště Azure Blob. Tyto obrázky použité pro vyhodnocování jsou neoznačené obrázky z datové sady [ImageNet](http://image-net.org/) .

## <a name="prerequisites"></a>Předpoklady

- Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

- Nakonfigurujte vývojové prostředí pro instalaci sady Azure Machine Learning SDK. Další informace najdete v tématu [Konfigurace vývojového prostředí pro Azure Machine Learning](how-to-configure-environment.md).

- Vytvořte pracovní prostor Azure Machine Learning, který bude obsahovat všechny prostředky kanálu. Můžete použít následující kód nebo další možnosti najdete v tématu [vytvoření konfiguračního souboru pracovního prostoru](how-to-configure-environment.md#workspace).

  ```python
  from azureml.core import Workspace
  ws = Workspace.create(name = '<workspace-name>',
                        subscription_id = '<subscription-id>',
                        resource_group = '<resource-group>',
                        location = '<workspace_region>',
                        exist_ok = True
                        )
  ```

## <a name="set-up-machine-learning-resources"></a>Nastavení prostředků strojového učení

Následující postup nastaví prostředky, které potřebujete ke spuštění kanálu:

- Přístup k úložišti dat, který už má předem vydaný model, vstupní popisky a obrázky do skóre (to je už pro vás nastavené).
- Nastavte úložiště dat pro ukládání výstupů.
- Nakonfigurujte @ no__t-0 @ no__t-1objects, aby odkazoval na data z předchozích úložišť dat.
- Nastavte výpočetní počítače nebo clustery, ve kterých se spustí postup kanálu.

### <a name="access-the-datastores"></a>Přístup k úložišti dat

Nejprve přístup k úložišti dat s modelem, popisky a obrázky.

Pomocí veřejného kontejneru objektů BLOB s názvem *sampleData*v účtu *pipelinedata* , který obsahuje obrázky ze sady hodnocení ImageNet. Název úložiště dat pro tento veřejný kontejner je *images_datastore*. Zaregistrujte toto úložiště dat ve vašem pracovním prostoru:

```python
from azureml.core import Datastore

account_name = "pipelinedata"
datastore_name = "images_datastore"
container_name = "sampledata"

batchscore_blob = Datastore.register_azure_blob_container(ws,
                                                          datastore_name=datastore_name,
                                                          container_name=container_name,
                                                          account_name=account_name,
                                                          overwrite=True)
```

V dalším kroku nastavte, aby se pro výstupy používaly výchozí úložiště dat.

Když vytváříte pracovní prostor, [soubory Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)@no__t – 1and [BLOB Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) are připojené k pracovnímu prostoru ve výchozím nastavení. Služba soubory Azure je výchozím úložištěm dat pro pracovní prostor, ale můžete také použít úložiště objektů BLOB jako úložiště dat. Další informace najdete v tématu [Možnosti služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>Konfigurace odkazů na data

Nyní na data v kanálu odkázat jako na vstupy pro kroky kanálu.

Zdroj dat v kanálu je reprezentován objektem [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) object. Hodnota @ no__t-0 @ no__t-1object odkazuje na data, která jsou v nebo jsou přístupná z úložiště dat. Potřebujete `DataReference` @ no__t-1objects pro adresář, který se používá pro vstupní image, adresář, ve kterém je uložený předvedený model, adresář pro popisky a výstupní adresář.

```python
from azureml.data.data_reference import DataReference

input_images = DataReference(datastore=batchscore_blob,
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download")

model_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_model",
                          path_on_datastore="batchscoring/models",
                          mode="download")

label_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download")

output_dir = PipelineData(name="scores",
                          datastore=def_data_store,
                          output_path_on_compute="batchscoring/results")
```

### <a name="set-up-compute-target"></a>Nastavit cíl výpočtů

V Azure Machine Learning *výpočetní* prostředí (nebo *target COMPUTE*) odkazuje na počítače nebo clustery, které provádějí výpočetní kroky v kanálu Machine Learning. Můžete například vytvořit Azure Machine Learning COMPUTE pomocí třídy [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29?view=azure-ml-py) .

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

compute_name = "gpucluster"
compute_min_nodes = 0
compute_max_nodes = 4
vm_size = "STANDARD_NC6"

if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target. just use it. ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(
        vm_size=vm_size,  # NC6 is GPU-enabled
        vm_priority='lowpriority',  # optional
        min_nodes=compute_min_nodes,
        max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws,
                                          compute_name,
                                          provisioning_config)

    compute_target.wait_for_completion(
        show_output=True,
        min_node_count=None,
        timeout_in_minutes=20)
```

## <a name="prepare-the-model"></a>Příprava modelu

Předtím, než budete moci použít předvedený model, je nutné stáhnout model a zaregistrovat ho v pracovním prostoru.

### <a name="download-the-pretrained-model"></a>Stažení předvýukového modelu

Stáhněte si předem vydaný model InceptionV3 (Computer Vision model) z <http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz>. Pak ho rozbalte do podsložky `models`.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url = "http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>Registrace modelu

Tady je postup, jak tento model zaregistrovat:

```python
import shutil
from azureml.core.model import Model

# register downloaded model
model = Model.register(
    model_path="models/inception_v3.ckpt",
    model_name="inception",  # This is the name of the registered model
    tags={'pretrained': "inception"},
    description="Imagenet trained tensorflow inception",
    workspace=ws)
```

## <a name="write-your-scoring-script"></a>Zápis vyhodnocovacího skriptu

>[!Warning]
>Následující kód je pouze ukázka toho, co je obsaženo v [batch_score. py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/batch_scoring.py) používané [ukázkovým poznámkovým blokem](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/pipeline-batch-scoring.ipynb). Pro svůj scénář budete muset vytvořit vlastní hodnoticí skript.

Skript `batch_score.py` používá vstupní image v *dataset_path*, předem vydaných modelech *model_dir* a výstup *Results-Label. txt* do *output_dir*.

```python
# Snippets from a sample scoring script
# Refer to the accompanying batch-scoring Notebook
# https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb
# for the implementation script

# Get labels
def get_class_label_dict(label_file):
  label = []
  proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
  for l in proto_as_ascii_lines:
    label.append(l.rstrip())
  return label

class DataIterator:
  # Definition of the DataIterator here

def main(_):
    # Refer to batch-scoring Notebook for implementation.
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)

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

        # copy the file to artifacts
        shutil.copy(out_filename, "./outputs/")
```

## <a name="build-and-run-the-batch-scoring-pipeline"></a>Sestavování a spouštění kanálu vyhodnocování dávky

### <a name="prepare-the-run-environment"></a>Příprava prostředí pro spuštění

Zadejte závislosti conda pro váš skript. Tento objekt budete potřebovat později, když vytvoříte krok kanálu.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

cd = CondaDependencies.create(
    pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>Zadejte parametr pro svůj kanál

Vytvořte parametr kanálu pomocí [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) object s výchozí hodnotou.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(
    name="param_batch_size",
    default_value=20)
```

### <a name="create-the-pipeline-step"></a>Vytvoření kroku kanálu

Vytvořte krok kanálu pomocí skriptu, konfigurace prostředí a parametrů. Zadejte cíl služby COMPUTE, který jste už připojili k pracovnímu prostoru, jako cíl provádění skriptu. Pomocí [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) vytvořte krok kanálu.

```python
from azureml.pipeline.steps import PythonScriptStep
inception_model_name = "inception_v3.ckpt"

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_score.py",
    arguments=["--dataset_path", input_images,
               "--model_name", "inception",
               "--label_dir", label_dir,
               "--output_dir", output_dir,
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config,
    source_directory=scripts_folder
```

### <a name="run-the-pipeline"></a>Spuštění kanálu

Teď kanál spusťte a prověřte výstup, který vytvořil. Výstup má skóre odpovídající každému vstupnímu obrázku.

```python
import pandas as pd
from azureml.pipeline.core import Pipeline

# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(
    pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>Publikování kanálu

Až budete s výsledkem spuštění spokojeni, publikujte kanál, abyste ho mohli později spustit s různými vstupními hodnotami. Při publikování kanálu získáte koncový bod REST. Tento koncový bod akceptuje vyvolání kanálu se sadou parametrů, které jste už zahrnuli pomocí [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py).

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring",
    description="Batch scoring using Inception v3 model",
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>Opětovné spuštění kanálu pomocí koncového bodu REST

Chcete-li znovu spustit kanál, budete potřebovat token hlaviček ověřování Azure Active Directory, jak je popsáno v tématu [Třída AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py).

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>Další kroky

Pokud chcete tento pracovní postup zobrazit, vyzkoušejte si Poznámkový blok pro dávkové vyhodnocování na [GitHubu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)nebo přejděte do [centra architektury Azure](/azure/architecture/reference-architectures/ai/batch-scoring-python) , kde se zobrazí ukázková Architektura řešení.

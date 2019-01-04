---
title: Spustit dávky předpovědí na velkých objemů dat
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak k vytvoření predikcí dávky asynchronně velkých objemů dat pomocí služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 7b0e3bc14c97c874b9d5936c025f4534665a461e
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2018
ms.locfileid: "53752618"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-service"></a>Predikce služby batch spouštět rozsáhlé datové sady se službou Azure Machine Learning

V tomto článku se dozvíte, jak asynchronně, vytvářením předpovědí na velkých objemů dat pomocí služby Azure Machine Learning.

Predikce služby Batch (nebo dávkové bodování) poskytuje nákladově efektivní odvození s bezkonkurenční propustnost pro asynchronní aplikace. Kanály predikcí služby batch můžete škálovat provádět odvození na terabajty dat v produkčním prostředí. Predikce služby batch je optimalizovaná pro vysokou propustnost, fire a zapomenout předpovědi pro velkou kolekci data.

>[!TIP]
> Pokud váš systém vyžaduje zpracování s nízkou latencí (pro rychlé zpracování jednoho dokumentu nebo malou sadu dokumentů), použijte [vyhodnocování v reálném čase](how-to-consume-web-service.md) místo predikcí služby batch.

V následujících krocích vytvoříte [kanálu strojového učení](concept-ml-pipelines.md) k registraci modelu které je předem vytrénované počítačového zpracování obrazu ([vzniku V3](https://arxiv.org/abs/1512.00567)). Pak použijete pretrained model pro dávkové bodování na imagích, které jsou k dispozici ve vašem účtu úložiště objektů Blob v Azure. Jsou tyto Image použít pro vyhodnocování neoznačených obrázků z [ImageNet](http://image-net.org/) datové sady.

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](http://aka.ms/AMLFree).

- Konfigurace vývojového prostředí pro instalaci sady SDK Azure Machine Learning. Další informace najdete v tématu [nakonfigurovat prostředí pro vývoj pro Azure Machine Learning](how-to-configure-environment.md).

- Vytvoření pracovního prostoru Azure Machine Learning, která bude obsahovat všechny vaše prostředky kanálu. Pomocí následujícího kódu nebo další možnosti najdete v tématu [vytvořit konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).

  ```python
  ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
  ```

## <a name="set-up-machine-learning-resources"></a>Nastavení prostředků machine learning

Následující kroky nastavit prostředky, které potřebujete ke spuštění kanálu:

- Přístup k úložišti dat, který už má, které je předem vytrénované modelu, vstupní popisky a obrázky ke stanovení skóre (nastavuje se to už pro vás).
- Nastavení datového úložiště pro ukládání vašich výstupy.
- Konfigurace `DataReference` objekty tak, aby odkazoval na data v předchozím úložišť.
- Nastavte výpočetní prostředky počítače nebo clustery ve kterém se spustí kanál kroky.

### <a name="access-the-datastores"></a>Přístup k úložiště dat

Nejprve přístup k úložišti dat, který má model, popisků a obrázky.

Použijete-li veřejného kontejneru blob s názvem *sampledata*v *pipelinedata* účet, který obsahuje Image ze sady ImageNet hodnocení. Název úložiště dat pro tento veřejný kontejner je *images_datastore*. Zaregistrujte toto úložiště dat pomocí pracovního prostoru:

```python
# Public blob container details
account_name = "pipelinedata"
datastore_name="images_datastore"
container_name="sampledata"
 
batchscore_blob = Datastore.register_azure_blob_container(ws,
                      datastore_name=datastore_name,
                      container_name= container_name,
                      account_name=account_name,
                      overwrite=True)
```

Další nastavení pro použití výchozí úložiště pro výstup.

Při vytváření pracovního prostoru [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) a [úložiště objektů Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) jsou připojeny k pracovním prostoru ve výchozím nastavení. Služba soubory Azure je výchozí úložiště dat pro pracovní prostor, ale můžete také použít úložiště objektů Blob jako datového úložiště. Další informace najdete v tématu [možnosti služby Azure storage](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>Konfigurovat odkazy na data

Nyní odkazují na data ve vašem kanálu jako vstupy do kanálu kroků.

Zdroj dat v kanálu je reprezentována [odkaz DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) objektu.  `DataReference` Objektu odkazuje na data, která se nachází, nebo k ní z datového úložiště. Potřebujete `DataReference`  objektů v adresáři používá vstupní imagí, adresáře, ve kterém je uložen pretrained modelu adresáři popisků a výstupní adresář.

```python
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

### <a name="set-up-compute-target"></a>Nastavení cílové výpočetní prostředí

V Azure Machine Learning *compute* (nebo *cílové výpočetní prostředí*) odkazuje na počítačích nebo clustery, které provádějí výpočetní kroky ve vašem kanálu machine learning. Například můžete vytvořit `Azure Machine Learning compute`.

```python
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
                     vm_size = vm_size, # NC6 is GPU-enabled
                     vm_priority = 'lowpriority', # optional
                     min_nodes = compute_min_nodes, 
                     max_nodes = compute_max_nodes)

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

Před použitím pretrained modelu, budete muset stáhnout modelu a zaregistrovat ji pomocí pracovního prostoru.

### <a name="download-the-pretrained-model"></a>Stáhněte si pretrained modelu

Stáhněte si modelu které je předem vytrénované počítačového zpracování obrazu (InceptionV3) z <http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz>. Pak rozbalte ho do `models` podsložky.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>Zaregistrujte model

Tady je postup při registraci modelu:

```python
import shutil
from azureml.core.model import Model

# register downloaded model 
model = Model.register(
        model_path = "models/inception_v3.ckpt",
        model_name = "inception", # This is the name of the registered model
        tags = {'pretrained': "inception"},
        description = "Imagenet trained tensorflow inception",
        workspace = ws)
```

## <a name="write-your-scoring-script"></a>Zápis hodnoticí skript

>[!Warning]
>Následující kód je jenom ukázka co je součástí [batch_score.py](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/batch_score.py) používané [ukázkový poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-batch-scoring.ipynb). Budete muset vytvořit vlastní hodnoticí skript pro váš scénář.

`batch_score.py` Skript přijímá vstupní image *dataset_path*, které je předem vytrénované modelů v *model_dir,* a vypíše *výsledky label.txt* k *output_dir*.

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

## <a name="build-and-run-the-batch-scoring-pipeline"></a>Sestavte a spusťte dávkového vyhodnocování kanálu

Budete mít všechno, co potřebujete k vytvoření kanálu, takže teď všech součástí dohromady.

### <a name="prepare-the-run-environment"></a>Příprava prostředí pro spuštění

Určení závislostí systému conda vašeho skriptu. Tento objekt budete potřebovat později při vytváření kanálu kroku.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>Zadejte parametr pro svůj kanál

Vytvořit parametr kanálu pomocí [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) objektu s výchozí hodnotou.

```python
batch_size_param = PipelineParameter(
                    name="param_batch_size", 
                    default_value=20)
```

### <a name="create-the-pipeline-step"></a>Vytvoření kanálu krok

Vytvoření kanálu krok pomocí skriptu, prostředí konfigurace a parametry. Zadejte cílové výpočetní prostředí, který je již připojen k vašemu pracovnímu prostoru jako cíl provádění skriptu. Použití [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) vytvořit kanál krok.

```python
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

Nyní spuštění kanálu a prohlédněte si výstup, který je vytvořen. Výstup má skóre odpovídající každého vstupního obrázku.

```python
# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

import pandas as pd
df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>Publikování kanálu

Jakmile budete spokojeni s výsledkem spuštění, publikování kanálu, můžete ji spustit s různými hodnotami vstupní později. Když publikujete kanálu, získáte koncový bod REST. Tento koncový bod přijímá volání kanálu sadu parametrů, které již byly zahrnuty pomocí [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py).

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", 
    description="Batch scoring using Inception v3 model", 
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>Opětovné spuštění kanálu pomocí koncového bodu REST

Spustit kanál znovu, budete potřebovat tokenu hlavičky ověřování Azure Active Directory, jak je popsáno v [AzureCliAuthentication třídy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py).

```python
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint, 
        headers=aad_token, 
        json={"ExperimentName": "batch_scoring",
               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
from azureml.pipeline.core.run import PipelineRun
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>Další postup

Pokud chcete zobrazit tento pracovní začátku do konce, zkuste dávkového vyhodnocování Poznámkový blok v [Githubu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]


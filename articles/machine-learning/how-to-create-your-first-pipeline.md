---
title: Vytvoření, spuštění & sledování kanálů ML
titleSuffix: Azure Machine Learning
description: Vytvořte a spusťte kanál strojového učení pomocí sady Azure Machine Learning SDK pro Python. Pomocí kanálů ML můžete vytvářet a spravovat pracovní postupy, které spojují fáze strojového učení (ML). Tyto fáze zahrnují přípravu dat, školení modelu, nasazení modelu a odvození/vyhodnocování.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: d175a2cea685585da3767acdb0ab77a99c541d09
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80873867"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Vytváření a spouštění kanálů strojového učení pomocí Azure Machine Learning SDK

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak vytvořit, publikovat, spustit a sledovat [kanál strojového učení](concept-ml-pipelines.md) pomocí [sady Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Pomocí **kanálů ML** vytvořte pracovní postup, který spojí různé fáze ML, a pak tento kanál publikujte do pracovního prostoru Azure Machine Learning, abyste k němu měli přístup později nebo je mohl sdílet s ostatními.  Ml kanály jsou ideální pro scénáře dávkového vyhodnocování, pomocí různých výpočetních postupů, opětovné použití kroků namísto jejich opětovného spuštění, stejně jako sdílení pracovních postupů ML s ostatními.

Zatímco můžete použít jiný druh kanálu s názvem [Azure Pipeline](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) pro automatizaci CI/CD ml úlohy, tento typ kanálu se nikdy neukládá uvnitř pracovního prostoru. [Porovnejte tyto různé kanály](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Každá fáze kanálu ML, jako je například příprava dat a trénování modelu, může zahrnovat jeden nebo více kroků.

Kanály ML, které vytvoříte, jsou viditelné pro členy [pracovního prostoru](how-to-manage-workspace.md)Azure Machine Learning . 

Ml kanály používají vzdálené výpočetní cíle pro výpočet a ukládání zprostředkující a konečná data spojená s tímto kanálem. Můžou číst a zapisovat data do a z podporovaných umístění [Azure Storage.](https://docs.microsoft.com/azure/storage/)

Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Požadavky

* Vytvořte [pracovní prostor Azure Machine Learning pro](how-to-manage-workspace.md) všechny prostředky kanálu.

* [Nakonfigurujte své vývojové prostředí](how-to-configure-environment.md) tak, aby instalovalo sis stesku Azure Machine Learning SDK, nebo použijte [výpočetní instanci (preview) Azure Machine Learning](concept-compute-instance.md) s již nainstalovanou sadou SDK.

Začněte připojením pracovního prostoru:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

## <a name="set-up-machine-learning-resources"></a>Nastavení zdrojů strojového učení

Vytvořte prostředky potřebné ke spuštění kanálu ML:

* Nastavte úložiště dat používané pro přístup k datům potřebným v krocích kanálu.

* Nakonfigurujte `Dataset` objekt tak, aby ukazoval na trvalá data, která se v úložišti dat nachází nebo jsou v ním přístupná. Nakonfigurujte `PipelineData` objekt pro dočasná data předaná mezi kroky kanálu. 

* Nastavte [výpočetní cíle,](concept-azure-machine-learning-architecture.md#compute-targets) na kterých se budou spouštět kroky kanálu.

### <a name="set-up-a-datastore"></a>Nastavení úložiště dat

Úložiště dat ukládá data pro kanál pro přístup. Každý pracovní prostor má výchozí úložiště dat. Můžete zaregistrovat další úložiště dat. 

Když vytvoříte pracovní prostor, [soubory Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) a úložiště [objektů blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) se připojí k pracovnímu prostoru. Výchozí úložiště dat je registrované pro připojení k úložišti objektů blob Azure. Další informace najdete v [tématu Rozhodování o tom, kdy použít Azure Files, Azure Blobs nebo Azure Disky](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Nahrajte datové soubory nebo adresáře do úložiště dat, aby byly přístupné z vašich kanálů. Tento příklad používá úložiště objektů blob jako úložiště dat:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups",
    overwrite=True)
```

Kanál se skládá z jednoho nebo více kroků. Krok je jednotka spuštěná na výpočetním cíli. Kroky mohou spotřebovávat zdroje dat a vytvářet "zprostředkující" data. Krok můžete vytvořit data, jako je například model, adresář s modelem a závislé soubory nebo dočasná data. Tato data jsou pak k dispozici pro další kroky později v kanálu.

Další informace o připojení kanálu k datům najdete v článcích [Jak získat přístup k datům](how-to-access-data.md) a Jak [zaregistrovat datové sady](how-to-create-register-datasets.md). 

### <a name="configure-data-using-dataset-and-pipelinedata-objects"></a>Konfigurace dat `Dataset` `PipelineData` pomocí objektů a objektů

Právě jste vytvořili zdroj dat, na který lze odkazovat v kanálu jako vstup do kroku. Upřednostňovaným způsobem, jak poskytnout data do kanálu, je objekt [dataset.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.Dataset) Objekt `Dataset` odkazuje na data, která se nachází v úložišti dat nebo jsou přístupná z úložiště dat nebo na webové adrese URL. Třída `Dataset` je abstraktní, takže vytvoříte instanci `FileDataset` buď (odkazující na jeden `TabularDataset` nebo více souborů) nebo která je vytvořena z jednoho nebo více souborů s oddělenými sloupci dat.

`Dataset`objekty podporují správu verzí, rozdíly a souhrnné statistiky. `Dataset`s jsou líně vyhodnocovány (jako Python generátory) a je efektivní je podmnožinou rozdělením nebo filtrováním. 

Vytvoření pomocí `Dataset` metody, jako [je from_file](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) nebo [from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-).

```python
from azureml.core import Dataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/tabular/iris.csv')])
```

Zprostředkující data (nebo výstup kroku) je reprezentován [pipelinedata](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) objektu. `output_data1`je vytvořen jako výstup kroku a použit jako vstup jednoho nebo více budoucích kroků. `PipelineData`zavádí závislost dat mezi kroky a vytvoří implicitní pořadí provádění v kanálu. Tento objekt bude použit později při vytváření kroků kanálu.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

Další podrobnosti a ukázkový kód pro práci s datovými sadami a daty kanálu najdete v [části Přesun dat do a mezi kroky kanálu ML (Python).](how-to-move-data-in-out-of-pipelines.md)

## <a name="set-up-a-compute-target"></a>Nastavení výpočetního cíle

V Azure Machine Learning termín __výpočetní__ (nebo __výpočetní cíl)__ odkazuje na počítače nebo clustery, které provádějí výpočetní kroky v kanálu strojového učení.   Podívejte se na [výpočetní cíle pro trénování modelu](how-to-set-up-training-targets.md) pro úplný seznam výpočetních cílů a jak je vytvořit a připojit k vašemu pracovnímu prostoru.  Proces pro vytvoření nebo připojení výpočetní cíl je stejný bez ohledu na to, zda trénujete model nebo spuštění kroku kanálu. Po vytvoření a připojení výpočetního `ComputeTarget` cíle použijte objekt v [kroku kanálu](#steps).

> [!IMPORTANT]
> Provádění operací správy na výpočetní cíle není podporována z evnitř vzdálené úlohy. Vzhledem k tomu, že kanály strojového učení jsou odeslány jako vzdálená úloha, nepoužívejte operace správy na výpočetní cíle zevnitř kanálu.

Níže jsou uvedeny příklady vytváření a připojování výpočetních cílů pro:

* Azure Machine Learning Compute
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Výpočetní prostředky Azure Machine Learning

Můžete vytvořit výpočetní prostředky Azure Machine Learning pro spuštění kroků.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks je prostředí založené na Apache Spark v cloudu Azure. Dá se použít jako výpočetní cíl s kanálem Azure Machine Learning.

Před použitím vytvořte pracovní prostor Azure Databricks. Pokud chcete vytvořit prostředek pracovního prostoru, přečtěte [si tématu Spuštění úlohy Spark v dokumentu Azure Databricks.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

Pokud chcete jako výpočetní cíl připojit Azure Databricks, zadejte následující informace:

* __Databricks vypočítaný název__: Název, který chcete přiřadit k tomuto výpočetnímu prostředku.
* __Název pracovního prostoru Databricks__: Název pracovního prostoru Azure Databricks.
* __Přístupový token Databricks__: Přístupový token používaný k ověření azure databricks. Pokud chcete vygenerovat přístupový token, přečtěte si dokument [Ověřování.](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html)

Následující kód ukazuje, jak připojit Azure Databricks jako výpočetní cíl s Azure Machine Learning SDK __(Pracovní prostor Databricks musí být k dispozici ve stejném předplatném jako váš pracovní prostor AML__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Podrobnější příklad najdete v [ukázkovém poznámkovém bloku](https://aka.ms/pl-databricks) na GitHubu.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics je platforma pro analýzu velkých objemů dat v cloudu Azure. Dá se použít jako výpočetní cíl s kanálem Azure Machine Learning.

Před použitím si vytvořte účet Azure Data Lake Analytics. Pokud chcete vytvořit tento prostředek, přečtěte si téma [Začínáme s dokumentem Azure Data Lake Analytics.](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal)

Chcete-li připojit Data Lake Analytics jako výpočetní cíl, musíte použít Azure Machine Learning SDK a poskytnout následující informace:

* __Název výpočetního prostředku__: Název, který chcete přiřadit tomuto výpočetnímu prostředku.
* __Skupina prostředků__: Skupina prostředků, která obsahuje účet Data Lake Analytics.
* __Název účtu__: Název účtu Data Lake Analytics.

Následující kód ukazuje, jak připojit Data Lake Analytics jako výpočetní cíl:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Podrobnější příklad najdete v [ukázkovém poznámkovém bloku](https://aka.ms/pl-adla) na GitHubu.

> [!TIP]
> Kanály Azure Machine Learning můžou fungovat jenom s daty uloženými ve výchozím úložišti dat účtu Data Lake Analytics. Pokud jsou data, se kterými potřebujete pracovat, v nevýchozím úložišti, můžete je před trénovací zkopírovat [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) pomocí a.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>Vytvoření kroků potrubí

Jakmile vytvoříte a připojíte výpočetní cíl k pracovnímu prostoru, jste připraveni definovat krok kanálu. Existuje mnoho předdefinovaných kroků, které jsou k dispozici prostřednictvím sady Azure Machine Learning SDK. Nejzákladnější z těchto kroků je [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), který spouští skript Pythonu v zadaném výpočetním cíli:

```python
from azureml.pipeline.steps import PythonScriptStep

ds_input = my_dataset.as_named_input('input1')

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", ds_input.as_download(), "--output", output_data1],
    inputs=[ds_input],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder,
    allow_reuse=True
)
```

Opakované použití předchozích`allow_reuse`výsledků ( ) je klíčové při použití kanálů v prostředí pro spolupráci, protože eliminace zbytečných repríz nabízí agilitu. Opakované použití je výchozí chování, když script_name, vstupy a parametry kroku zůstávají stejné. Při opětovném použití výstupu kroku úlohy není odeslána výpočetní, místo toho výsledky z předchozího spuštění jsou okamžitě k dispozici pro spuštění dalšího kroku. Pokud `allow_reuse` je nastavena na false, bude vždy generováno nové spuštění pro tento krok během spuštění kanálu. 

Po definování kroků vytvoříte kanál pomocí některých nebo všech těchto kroků.

> [!NOTE]
> Žádný soubor nebo data se nahrají do Azure Machine Learning při definování kroků nebo sestavení kanálu.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

Následující příklad používá cíl výpočetního výkonu Azure Databricks vytvořený dříve: 

```python
from azureml.pipeline.steps import DatabricksStep

dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

### <a name="use-a-dataset"></a>Použití datové sady 

Datové sady vytvořené z úložiště objektů blob Azure, soubory Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database a Azure Database for PostgreSQL se můžou použít jako vstup do každého kroku kanálu. S výjimkou zápisu výstupu [dataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) nebo [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py), výstupní data[(PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)) lze zapisovat jenom do azure blob a azure file share datastores.

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Potom načíst datovou sadu v kanálu pomocí [slovníku Run.input_datasets.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets)

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

Linka `Run.get_context()` stojí za zdůraznění. Tato funkce načte `Run` představující aktuální experimentální spuštění. Ve výše uvedeném vzorku ji používáme k načtení registrované datové sady. Dalším běžným `Run` použitím objektu je načtení samotného experimentu a pracovního prostoru, ve kterém se experiment nachází: 

```python
# Within a PythonScriptStep

ws = Run.get_context().experiment.workspace
```

Další podrobnosti, včetně alternativních způsobů předání a přístupu k datům, naleznete v [tématu Přesun dat do a mezi kroky kanálu ML (Python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="submit-the-pipeline"></a>Odeslat potrubí

Při odeslání kanálu Azure Machine Learning zkontroluje závislosti pro každý krok a nahraje snímek zdrojového adresáře, který jste zadali. Pokud není zadán žádný zdrojový adresář, je odeslán aktuální místní adresář. Snímek je také uložen jako součást experimentu v pracovním prostoru.

> [!IMPORTANT]
> Chcete-li zabránit zahrnutí souborů do snímku, `.amlignore` vytvořte v adresáři [soubor .gitignore](https://git-scm.com/docs/gitignore) nebo soubor a přidejte do něj soubory. Soubor `.amlignore` používá stejnou syntaxi a vzorky jako soubor [.gitignore.](https://git-scm.com/docs/gitignore) Pokud oba soubory `.amlignore` existují, má soubor přednost.
>
> Další informace naleznete v [tématu Snímky](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Při prvním spuštění kanálu Azure Machine Learning:

* Stáhne snímek projektu do výpočetního cíle z úložiště objektů blob přidruženého k pracovnímu prostoru.
* Vytvoří image Dockeru odpovídající každému kroku v kanálu.
* Stáhne image Dockeru pro každý krok k cíli výpočetního prostředí z registru kontejneru.
* Konfiguruje přístup k objektům `Dataset` a `PipelineData` objektům. Jako `as_mount()` režim přístupu se fuse používá k poskytování virtuálního přístupu. Pokud připojení není podporováno nebo pokud `as_download()`uživatel zadal přístup jako , data se místo toho zkopírují do výpočetního cíle.
* Spustí krok v cílové výpočetní zadané v definici kroku. 
* Vytvoří artefakty, jako jsou protokoly, stdout a stderr, metriky a výstup určený krokem. Tyto artefakty jsou pak odeslány a uchovávány ve výchozím úložišti dat uživatele.

![Diagram spuštění experimentu jako kanálu](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Další informace naleznete v odkazu na [třídu Experiment.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)

### <a name="view-results-of-a-pipeline"></a>Zobrazení výsledků kanálu

Podívejte se na seznam všech vašich kanálů a jejich podrobnosti o spuštění ve studiu:

1. Přihlaste se do [studia Azure Machine Learning Studio](https://ml.azure.com).

1. [Zobrazení pracovního prostoru](how-to-manage-workspace.md#view).

1. Na levé straně vyberte **Pipelines,** abyste viděli všechny spuštěné kanály.
 ![seznam kanálů strojového učení](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Vyberte konkrétní kanál, abyste viděli výsledky spuštění.

## <a name="git-tracking-and-integration"></a>Git sledování a integrace

Když spustíte trénovací běh, kde zdrojový adresář je místní úložiště Git, informace o úložišti jsou uloženy v historii spuštění. Další informace najdete v [tématu Integrace Gitu pro Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="publish-a-pipeline"></a>Publikování kanálu

Můžete publikovat kanál pro jeho spuštění s různými vstupy později. Pro koncový bod REST již publikovaného kanálu přijímat parametry, musíte parametrizovat kanálu před publikováním.

1. Chcete-li vytvořit parametr kanálu, použijte objekt [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) s výchozí hodnotou.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Přidejte `PipelineParameter` tento objekt jako parametr do některého z kroků v kanálu následujícím způsobem:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Publikujte tento kanál, který při vyvolání přijme parametr.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Spuštění publikovaného kanálu

Všechny publikované kanály mají koncový bod REST. Tento koncový bod vyvolá spuštění kanálu z externích systémů, jako jsou například klienti mimo Python. Tento koncový bod umožňuje "spravované opakovatelnost" ve scénářích dávkového vyhodnocování a rekvalifikace.

Chcete-li vyvolat spuštění předchozího kanálu, potřebujete token hlavičky ověřování Azure Active Directory, jak je popsáno v odkazu [na třídu AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) nebo získat další podrobnosti v poznámkovém bloku [Ověřování v Azure Machine Learning.](https://aka.ms/pl-restep-auth)

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Vytvoření koncového bodu kanálu s verzí

Můžete vytvořit koncový bod kanálu s více publikovaných kanálů za ním. To lze použít jako publikovaný kanál, ale poskytuje pevný koncový bod REST při itetování a aktualizaci kanálů ML.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>Odeslání úlohy do koncového bodu kanálu

Úlohu můžete odeslat do výchozí verze koncového bodu kanálu:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

Úlohu můžete také odeslat do určité verze:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Totéž lze provést pomocí rozhraní REST API:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Použití publikovaných kanálů ve studiu

Můžete také spustit publikovaný kanál ze studia:

1. Přihlaste se do [studia Azure Machine Learning Studio](https://ml.azure.com).

1. [Zobrazení pracovního prostoru](how-to-manage-workspace.md#view).

1. Vlevo vyberte **Koncové body**.

1. Nahoře vyberte **Koncové body kanálu**.
 ![seznam publikovaných kanálů strojového učení](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Vyberte konkrétní kanál pro spuštění, využití nebo kontrolu výsledků předchozích spuštění koncového bodu kanálu.

### <a name="disable-a-published-pipeline"></a>Zakázání publikovaného kanálu

Chcete-li skrýt kanál ze seznamu publikovaných kanálů, zakažte jej ve studiu nebo ze sady SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Můžete jej znovu `p.enable()`povolit pomocí . Další informace naleznete v tématu [PublishedPipeline odkaz na třídu.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py)

## <a name="caching--reuse"></a>Opětovné použití & ukládání do mezipaměti  

Chcete-li optimalizovat a přizpůsobit chování kanálů, můžete provést několik věcí týkajících se ukládání do mezipaměti a opakovaného použití. Můžete například zvolit:
+ **Vypnutí výchozího opakovaného použití výstupu** spuštění `allow_reuse=False` kroku nastavením během [definice kroku](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Opakované použití je klíčové při použití kanálů v prostředí pro spolupráci, protože odstranění zbytečných spuštění nabízí agilitu. Můžete se však odhlásit z opakovaného použití.
+ **Vynutit regeneraci výstupu pro všechny kroky v běhu**`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Ve výchozím `allow_reuse` nastavení je povoleno `source_directory` pro kroky a zadané v definici kroku je zajistěn. Pokud tedy skript pro daný krok zůstane`script_name`stejný ( , vstupy a parametry) a nic jiného v` source_directory` něm se nezměnilo, je znovu použit výstup předchozího kroku, úloha není odeslána k výpočtu a výsledky z předchozího spuštění jsou okamžitě k dispozici k dalšímu kroku.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="next-steps"></a>Další kroky

- Pomocí [těchto poznámkových bloků Jupyter na GitHubu](https://aka.ms/aml-pipeline-readme) můžete dále prozkoumat kanály strojového učení.
- Podívejte se na referenční nápovědu sady SDK pro balíček [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) a balíček [azureml-pipelines-steps.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)
- Tipy pro ladění a odstraňování problémů s postupy najdete [v](how-to-debug-pipelines.md) návodu k ladění.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

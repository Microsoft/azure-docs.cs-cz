---
title: Vytváření, spouštění, & sledování kanálů ML
titleSuffix: Azure Machine Learning
description: Vytvořte a spusťte kanál strojového učení s Azure Machine Learning SDK pro Python. Pomocí kanálů ML můžete vytvářet a spravovat pracovní postupy, které spojí dohromady fáze strojového učení (ML). Tyto fáze zahrnují přípravu dat, školení modelů, nasazení modelu a odvození nebo bodování.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/05/2019
ms.custom: seodec18, tracking-python
ms.openlocfilehash: 50bd9d620fac4031ff398bb13d40b288488dda95
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559391"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Vytvoření a spuštění kanálů strojového učení s Azure Machine Learning SDK

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se naučíte, jak pomocí [sady Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)vytvořit, publikovat, spouštět a sledovat [kanál strojového učení](concept-ml-pipelines.md) .  Pomocí **kanálů ml** můžete vytvořit pracovní postup, který spojuje různé fáze ml, a pak tento kanál publikovat do svého pracovního prostoru Azure Machine Learning pro pozdější přístup nebo sdílení s ostatními.  Kanály ML jsou ideální pro scénáře dávkového vyhodnocování, které používají různé výpočetní prostředky, místo jejich spouštění a sdílení pracovních postupů ML s ostatními.

I když můžete použít jiný typ kanálu, který se nazývá [kanál Azure](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) pro automatizaci úloh v ml/CD pro CI, tento typ kanálu se nikdy neuloží do vašeho pracovního prostoru. [Porovnejte tyto různé kanály](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Každá fáze kanálu ML, jako je třeba Příprava dat a školení k modelu, může zahrnovat jeden nebo více kroků.

Kanály ML, které vytvoříte, jsou viditelné pro členy [pracovního prostoru](how-to-manage-workspace.md)Azure Machine Learning. 

Kanály ML používají vzdálené výpočetní cíle pro výpočet a úložiště mezia konečných dat přidružených k tomuto kanálu. Můžou číst a zapisovat data do a z podporovaných [Azure Storage](https://docs.microsoft.com/azure/storage/) umístění.

Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet, ještě než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Požadavky

* Vytvořte [pracovní prostor Azure Machine Learning](how-to-manage-workspace.md) pro uložení všech prostředků kanálu.

* [Nakonfigurujte vývojové prostředí](how-to-configure-environment.md) pro instalaci Azure Machine Learning sady SDK nebo použijte [výpočetní instanci Azure Machine Learning (Preview)](concept-compute-instance.md) s již nainstalovanou sadou SDK.

Začněte připojením pracovního prostoru:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

## <a name="set-up-machine-learning-resources"></a>Nastavení prostředků strojového učení

Vytvoření prostředků potřebných ke spuštění kanálu ML:

* Nastavte úložiště dat, které se používá pro přístup k datům potřebným v postupu kanálu.

* Nakonfigurujte `Dataset` objekt tak, aby odkazoval na trvalá data, která se nachází v nebo jsou přístupná v úložišti dat. Nakonfigurujte `PipelineData` objekt pro dočasná data předaná mezi kroky kanálu. 

* Nastavte [výpočetní cíle](concept-azure-machine-learning-architecture.md#compute-targets) , na kterých se budou spouštět vaše kroky kanálu.

### <a name="set-up-a-datastore"></a>Nastavení úložiště dat

Úložiště dat uchovává data pro kanál k přístupu. Každý pracovní prostor má výchozí úložiště dat. Můžete zaregistrovat další úložiště dat. 

Při vytváření pracovního prostoru jsou [soubory Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) a [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) připojené k pracovnímu prostoru. Pro připojení k úložišti objektů BLOB v Azure je zaregistrované výchozí úložiště dat. Další informace najdete v tématu [rozhodování, kdy používat soubory Azure, objekty blob Azure nebo disky Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Nahrajte datové soubory nebo adresáře do úložiště dat, aby byly dostupné z vašich kanálů. V tomto příkladu se jako úložiště dat používá úložiště objektů BLOB:

```python
def_blob_store.upload_files(
    ["iris.csv"],
    target_path="train-dataset",
    overwrite=True)
```

Kanál se skládá z jednoho nebo více kroků. Krok je jednotka spuštěná na výpočetním cíli. Kroky můžou spotřebovávat zdroje dat a vytvářet "zprostředkující" data. Krok může vytvářet data, jako je model, adresář s modelem a závislými soubory nebo dočasná data. Tato data jsou pak k dispozici pro další kroky později v kanálu.

Další informace o připojení kanálu k vašim datům najdete v článcích [o tom, jak získat přístup k datům](how-to-access-data.md) a [jak registrovat datové sady](how-to-create-register-datasets.md). 

### <a name="configure-data-using-dataset-and-pipelinedata-objects"></a>Konfigurace dat pomocí `Dataset` `PipelineData` objektů a

Právě jste vytvořili zdroj dat, na který se dá odkazovat v kanálu jako vstup do kroku. Upřednostňovaným způsobem, jak poskytnout data kanálu, je objekt [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.Dataset) . `Dataset`Objekt odkazuje na data, která jsou v nebo jsou přístupná z úložiště dat nebo na webové adrese URL. `Dataset`Třída je abstraktní, takže vytvoříte instanci buď a `FileDataset` (odkazující na jeden nebo více souborů), nebo `TabularDataset` vytvořenou z jednoho nebo více souborů s oddělenými sloupci dat.

`Dataset`objekty podporují správu verzí, rozdílů a souhrnnou statistiku. `Dataset`s jsou vyhodnocené laxně vytvářená (například generátory Pythonu) a je efektivní je poddělit rozdělením nebo filtrováním. 

Vytvoříte `Dataset` pomocí metod, jako je [from_file](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) nebo [from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-).

```python
from azureml.core import Dataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/iris.csv')])
```

Mezilehlé údaje (nebo výstup kroku) jsou reprezentovány objektem [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) . `output_data1`je vytvářen jako výstup kroku a slouží jako vstup jednoho nebo více budoucích kroků. `PipelineData`zavádí datovou závislost mezi kroky a vytvoří implicitní pořadí spouštění v kanálu. Tento objekt bude použit později při vytváření kroků kanálu.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

Další podrobnosti a ukázkový kód pro práci s datovými sadami a data kanálu najdete v části [přesun dat do a mezi kroky kanálu ml (Python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="set-up-a-compute-target"></a>Nastavení cíle výpočtů

V Azure Machine Learning pojem __výpočetní__ prostředí (neboli __cíl výpočetní__služby) odkazuje na počítače nebo clustery, které provádějí výpočetní kroky v kanálu Machine Learning.   Úplný seznam cílů výpočetních prostředků a jejich vytvoření a připojení k pracovnímu prostoru najdete v tématu [výpočetní cíle pro školení modelů](how-to-set-up-training-targets.md) .  Proces vytvoření a připojení cíle výpočtů je stejný, bez ohledu na to, jestli budete modelovat, nebo spustit krok kanálu. Po vytvoření a připojení cíle výpočetní služby použijte `ComputeTarget` objekt v [kroku kanálu](#steps).

> [!IMPORTANT]
> V rámci vzdálených úloh není podporováno provádění operací správy na cílech výpočtů. Vzhledem k tomu, že kanály strojového učení se odesílají jako Vzdálená úloha, nepoužívejte v rámci kanálu operace správy pro výpočetní cíle.

Níže jsou uvedeny příklady vytváření a připojování výpočetních cílů pro:

* Azure Machine Learning Compute
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning COMPUTE

Pro spuštění kroků můžete vytvořit Azure Machine Learning Compute.

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

Azure Databricks je prostředí založené na Apache Spark v cloudu Azure. Dá se použít jako cíl služby COMPUTE s kanálem Azure Machine Learning.

Před použitím vytvořte pracovní prostor Azure Databricks. Pokud chcete vytvořit prostředek pracovního prostoru, přečtěte si téma [spuštění úlohy Spark v dokumentu Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) .

Pokud chcete připojit Azure Databricks jako cíl výpočetních prostředků, zadejte následující informace:

* __Výpočetní název datacihly__: název, který chcete přiřadit k tomuto výpočetnímu prostředku.
* __Název pracovního prostoru datacihly__: název pracovního prostoru Azure Databricks.
* __Přístupový token datacihly__: přístupový token, který se používá k ověření Azure Databricks. Přístup k vygenerování přístupového tokenu najdete v dokumentu [ověřování](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) .

Následující kód ukazuje, jak připojit Azure Databricks jako výpočetní cíl se sadou Azure Machine Learning SDK (__pracovní prostor datacihly musí být přítomen ve stejném předplatném jako váš pracovní prostor AML__):

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

Azure Data Lake Analytics je platforma pro analýzu velkých objemů dat v cloudu Azure. Dá se použít jako cíl služby COMPUTE s kanálem Azure Machine Learning.

Před použitím vytvořte účet Azure Data Lake Analytics. Informace o vytvoření tohoto prostředku najdete v dokumentu [Začínáme s Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) .

Chcete-li připojit Data Lake Analytics jako cíl výpočtů, je nutné použít sadu Azure Machine Learning SDK a zadat následující informace:

* __Název výpočtu__: název, který chcete přiřadit k tomuto výpočetnímu prostředku.
* __Skupina prostředků__: Skupina prostředků, která obsahuje účet Data Lake Analytics.
* __Název účtu__: název Data Lake Analytics účtu.

Následující kód ukazuje, jak připojit Data Lake Analytics jako cíl výpočtů:

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
> Kanály Azure Machine Learning můžou pracovat jenom s daty uloženými ve výchozím úložišti dat účtu Data Lake Analytics. Pokud jsou data, se kterými pracujete, v nevýchozím úložišti, můžete použít [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) ke zkopírování dat před školeními.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>Vytvoření postupu kanálu

Po vytvoření a připojení cílového výpočetního prostředí k pracovnímu prostoru budete připraveni definovat krok kanálu. V sadě Azure Machine Learning SDK je k dispozici celá řada integrovaných kroků. Nejběžnějším z těchto kroků je [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), který spouští skript Pythonu v zadaném cílovém výpočetním prostředí:

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

Použití předchozích výsledků ( `allow_reuse` ) je klíč při použití kanálů v prostředí pro spolupráci, protože odstranění zbytečných opakovaných spuštění nabízí flexibilitu. Opakované použití je výchozí chování, když script_name, vstupy a parametry kroku zůstanou stejné. Když se výstup kroku znovu použije, úloha se neodešle do výpočetního prostředí, ale výsledky z předchozího běhu jsou hned dostupné pro spuštění dalšího kroku. Pokud `allow_reuse` je hodnota nastavena na false, nové spuštění bude pro tento krok vždy vygenerováno během provádění kanálu. 

Po definování kroků sestavíte kanál pomocí některých nebo všech těchto kroků.

> [!NOTE]
> Do Azure Machine Learning se při definování kroků nebo sestavení kanálu neodesílají žádné soubory ani data.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

V následujícím příkladu je použit cíl výpočetního prostředí Azure Databricks vytvořen dříve: 

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

Datové sady vytvořené ze služby Azure Blob Storage, souborů Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database a Azure Database for PostgreSQL lze použít jako vstup do libovolného kroku kanálu. Můžete napsat výstup do [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py), [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)nebo pokud chcete zapisovat data do konkrétního úložiště dat [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)použijte. 

> [!IMPORTANT]
> Zápis výstupních dat zpět do úložiště dat pomocí PipelineData se podporuje jenom pro úložiště Azure Blob a Azure File Share. Tato funkce není v tuto chvíli podporovaná pro [úložiště dat adls Gen 2](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) .

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Datovou sadu v kanálu pak načtěte pomocí slovníku [Run. input_datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets) .

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

Řádek `Run.get_context()` je zvýrazněný. Tato funkce načte `Run` představující aktuální experimentální běh. Ve výše uvedené ukázce používáme k načtení registrované datové sady. Dalším běžným použitím `Run` objektu je načíst samotný experiment i pracovní prostor, ve kterém se nachází experiment: 

```python
# Within a PythonScriptStep

ws = Run.get_context().experiment.workspace
```

Další podrobnosti, včetně alternativních způsobů, jak předat a přistupovat k datům, najdete v tématu [přesun dat do a mezi kroky kanálu ml (Python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="submit-the-pipeline"></a>Odeslat kanál

Když kanál odešlete, Azure Machine Learning zkontroluje závislosti pro jednotlivé kroky a nahraje snímek zadaného zdrojového adresáře. Pokud není zadán žádný zdrojový adresář, je odeslán aktuální místní adresář. Snímek je také uložen jako součást experimentu v pracovním prostoru.

> [!IMPORTANT]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
>
> Další informace najdete v tématu [snímky](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

Při prvním spuštění kanálu Azure Machine Learning:

* Stáhne snímek projektu do cílového výpočetní služby z úložiště objektů BLOB přidruženého k pracovnímu prostoru.
* Vytvoří Image Docker odpovídající jednotlivým krokům v kanálu.
* Stáhne image Docker pro každý krok do cílového výpočetního prostředí z registru kontejneru.
* Nakonfiguruje přístup `Dataset` k `PipelineData` objektům a. V případě `as_mount()` režimu přístupu se k zajištění virtuálního přístupu používá POjistka. Pokud se připojení nepodporuje nebo pokud uživatel zadal přístup jako `as_download()` , data se zkopírují do cíle výpočtů.
* Spustí krok na výpočetním cíli zadaném v definici kroku. 
* Vytvoří artefakty, jako jsou protokoly, stdout a stderr, metriky a výstup určený krokem. Tyto artefakty se pak nahrají a uchovávají ve výchozím úložišti dat uživatele.

![Diagram spuštění experimentu jako kanálu](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Další informace najdete v referenčních informacích ke [třídě experimentu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) .

### <a name="view-results-of-a-pipeline"></a>Zobrazení výsledků kanálu

Podívejte se na seznam všech vašich kanálů a jejich podrobnosti o spuštěních v studiu:

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com).

1. [Zobrazení pracovního prostoru](how-to-manage-workspace.md#view)

1. Na levé straně vyberte **kanály** a zobrazte všechny spuštěné kanály.
 ![seznam kanálů strojového učení](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Výběrem konkrétního kanálu zobrazíte výsledky spuštění.

## <a name="git-tracking-and-integration"></a>Sledování a integrace Git

Když spustíte školicí kurz, kde zdrojový adresář je místní úložiště Git, informace o úložišti se ukládají v historii spuštění. Další informace najdete v tématu [integrace Gitu pro Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="publish-a-pipeline"></a>Publikování kanálu

Můžete publikovat kanál a později ho spustit s různými vstupy. Pro koncový bod REST již publikovaného kanálu pro příjem parametrů je před publikováním nutné tento kanál parametrizovat.

1. Chcete-li vytvořit parametr kanálu, použijte objekt [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) s výchozí hodnotou.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Tento `PipelineParameter` objekt přidejte jako parametr do některého z kroků v kanálu následujícím způsobem:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Publikování tohoto kanálu, který při vyvolání přijme parametr.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Spuštění publikovaného kanálu

Všechny publikované kanály mají koncový bod REST. Pomocí koncového bodu kanálu můžete aktivovat spuštění kanálu z libovolného externího systému, včetně klientů mimo Python. Tento koncový bod povoluje ve scénářích dávkového vyhodnocování a přeškolení možnost spravovaná opakovatelnost.

Chcete-li vyvolat spuštění předchozího kanálu, budete potřebovat token hlaviček ověřování Azure Active Directory, jak je popsáno v tématu Referenční dokumentace [třídy AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) , nebo získat další podrobnosti v rámci [ověřování v](https://aka.ms/pl-restep-auth) poznámkovém bloku Azure Machine Learning.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Vytvoření koncového bodu kanálu se správou verzí

Koncový bod kanálu můžete vytvořit s několika publikovanými kanály. Tato možnost se dá použít jako publikovaný kanál, ale při iteraci a aktualizaci kanálů ML vám poskytne pevný koncový bod REST.

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

Můžete také odeslat úlohu do konkrétní verze:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Totéž můžete dosáhnout pomocí REST API:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Použití publikovaných kanálů v studiu

Můžete také spustit publikovaný kanál z studia:

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com).

1. [Zobrazení pracovního prostoru](how-to-manage-workspace.md#view)

1. Na levé straně vyberte **koncové body**.

1. V horní části vyberte **koncové body kanálu**.
 ![Seznam publikovaných kanálů ve strojovém učení](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Vyberte konkrétní kanál, který se má spustit, spotřebovat nebo zkontrolovat výsledky předchozích spuštění koncového bodu kanálu.

### <a name="disable-a-published-pipeline"></a>Zakázání publikovaného kanálu

Pokud chcete kanál ze seznamu publikovaných kanálů skrýt, můžete ho zakázat v sadě Studio nebo v sadě SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Můžete ho znovu povolit pomocí `p.enable()` . Další informace naleznete v tématu [PublishedPipeline Class Reference třídy](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) .

## <a name="caching--reuse"></a>Ukládání do mezipaměti & opakované použití  

Aby bylo možné optimalizovat a přizpůsobit chování kanálů, můžete provést několik věcí při ukládání do mezipaměti a opakovaném použití. Můžete například zvolit:
+ **Vypněte výchozí opakované použití pro krok spustit výstup** nastavením `allow_reuse=False` během [definice kroku](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Při použití kanálů ve spolupráci prostředí je klíč znovu použitelný, protože odstranění zbytečných běhů nabízí flexibilitu. Můžete se ale odhlásit z opakovaného použití.
+ **Vynutí generování výstupu pro všechny kroky v běhu** s`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Ve výchozím nastavení jsou `allow_reuse` pro kroky povolené a `source_directory` zadaná v definici kroku má hodnotu hash. Takže pokud skript pro daný krok zůstane stejný ( `script_name` , vstupy a parametry) a nic jiného v takovém případě se ` source_directory` nezmění, výstup spuštění předchozího kroku se znovu použije, úloha se neodešle do výpočetního výkonu a výsledky z předchozího běhu jsou hned k dispozici pro další krok.

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
- Podívejte se na referenční nápovědu sady SDK pro balíček [AzureML-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) a balíček [AzureML-Pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .
- Tipy k ladění kanálů a řešení potíží najdete v tématu [postupy](how-to-debug-pipelines.md) .

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

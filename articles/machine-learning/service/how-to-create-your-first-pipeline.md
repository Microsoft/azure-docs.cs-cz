---
title: Vytváření, spouštění, & sledování kanálů ML
titleSuffix: Azure Machine Learning service
description: Vytvoření a spuštění služby machine learning kanálu s využitím Azure Machine Learning SDK pro Python. Pomocí kanálů ML můžete vytvářet a spravovat pracovní postupy, které spojí dohromady fáze strojového učení (ML). Tyto fáze zahrnují přípravu dat, školení modelů, nasazení modelu a odvození nebo bodování.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: 12ba2991f22576dc62559d5c62dc4a0e769d2681
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858767"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Vytvoření a spuštění kanálů strojového učení s Azure Machine Learning SDK

V tomto článku se naučíte, jak pomocí [sady Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)vytvořit, publikovat, spouštět a sledovat [kanál strojového učení](concept-ml-pipelines.md) .  Pomocí **kanálů ml** můžete vytvořit pracovní postup, který spojuje různé fáze ml, a pak tento kanál publikovat do svého pracovního prostoru Azure Machine Learning pro pozdější přístup nebo sdílení s ostatními.  Kanály ML jsou ideální pro scénáře dávkového vyhodnocování, které používají různé výpočetní prostředky, místo jejich spouštění a sdílení pracovních postupů ML s ostatními. 

I když můžete použít jiný typ kanálu, který se nazývá [kanál Azure](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) pro automatizaci úloh v ml/CD pro CI, tento typ kanálu se nikdy neuloží do vašeho pracovního prostoru. [Porovnejte tyto různé kanály](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Každá fáze kanálu ML, jako je třeba Příprava dat a školení k modelu, může zahrnovat jeden nebo více kroků.

Kanály ML, které vytvoříte, jsou viditelné pro členy [pracovního prostoru](how-to-manage-workspace.md)služby Azure Machine Learning. 

Kanály ML používají vzdálené výpočetní cíle pro výpočet a úložiště mezia konečných dat přidružených k tomuto kanálu. Můžou číst a zapisovat data do a z podporovaných [Azure Storage](https://docs.microsoft.com/azure/storage/) umístění.

Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušejte [bezplatnou nebo placená verzi služby Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Požadavky

* Vytvoření [pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md) pro uložení všech vašich prostředků kanálu.

* [Nakonfigurujte vývojové prostředí](how-to-configure-environment.md) pro instalaci Azure Machine Learning sady SDK nebo použijte [virtuální počítač Poznámkový blok](tutorial-1st-experiment-sdk-setup.md#azure) s již nainstalovanou sadou SDK.

Začněte připojením pracovního prostoru:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```


## <a name="set-up-machine-learning-resources"></a>Nastavení prostředků machine learning

Vytvoření prostředků potřebných ke spuštění kanálu ML:

* Nastavení úložiště dat používá pro přístup k datům, je potřeba v kanálu kroky.

* `DataReference` Nakonfigurujte objekt tak, aby odkazoval na data, která se nachází v nebo jsou přístupná v úložišti dat.

* Nastavit [cílových výpočetních prostředí](concept-azure-machine-learning-architecture.md#compute-targets) podle postupu k kanál se spouští.

### <a name="set-up-a-datastore"></a>Nastavení úložiště dat

Úložiště dat ukládá data pro kanál tak, aby přístup. Každý pracovní prostor má výchozí úložiště. Můžete zaregistrovat další úložiště. 

Při vytváření pracovního prostoru jsou [soubory Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) a [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) připojené k pracovnímu prostoru. Pro připojení k úložišti objektů BLOB v Azure je zaregistrované výchozí úložiště dat. Další informace najdete v tématu [rozhodování, kdy používat soubory Azure, objekty blob Azure nebo disky Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Nahrajte datové soubory nebo adresáře do úložiště dat, aby byla přístupná z vašich kanálů. V tomto příkladu se jako úložiště dat používá úložiště objektů BLOB:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups",
    overwrite=True)
```

Kanál se skládá z jednoho nebo více kroků. Krok je jednotka spustit na cílové výpočetní prostředí. Kroky mohou využívat zdroje dat. a vygenerovat "zprostředkující" data. Krok můžete vytvořit data, jako jsou modelu, adresář s modelem a závislé soubory, nebo dočasná data. Tato data je pak k dispozici pro další kroky později v kanálu.

### <a name="configure-data-reference"></a>Nastavit odkaz na data

Právě jste vytvořili zdroje dat, který může být odkazováno v kanálu jako vstup do kroku. Zdroj dat v kanálu je reprezentována [odkaz DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) objektu. `DataReference` Objektu odkazuje na data, která se nachází nebo je přístupný z datového úložiště.

```python
from azureml.data.data_reference import DataReference

blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Je reprezentována dočasných dat (nebo výstupní kroku) [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) objektu. `output_data1`je vytvářen jako výstup kroku a slouží jako vstup jednoho nebo více budoucích kroků. `PipelineData`zavádí datovou závislost mezi kroky a vytvoří implicitní pořadí spouštění v kanálu.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

## <a name="set-up-compute-target"></a>Nastavení cílové výpočetní prostředí

V Azure Machine Learning pojem computes__ (nebo cíl služby __COMPUTE__) odkazuje na počítače nebo clustery, které provádějí výpočetní kroky v kanálu strojového učení.   Úplný seznam cílů výpočetních prostředků a jejich vytvoření a připojení k pracovnímu prostoru najdete v tématu [výpočetní cíle pro školení modelů](how-to-set-up-training-targets.md) .  Proces vytvoření a připojení cíle výpočtů je stejný, bez ohledu na to, jestli budete modelovat, nebo spustit krok kanálu. Po vytvoření a připojení cíle výpočetní služby použijte `ComputeTarget` objekt v [kroku kanálu](#steps).

> [!IMPORTANT]
> V rámci vzdálených úloh není podporováno provádění operací správy na cílech výpočtů. Vzhledem k tomu, že kanály strojového učení se odesílají jako Vzdálená úloha, nepoužívejte v rámci kanálu operace správy pro výpočetní cíle.

Níže jsou uvedeny příklady vytváření a připojování výpočetních cílů pro:

* Azure Machine Learning Compute
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning compute

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

### <a id="databricks"></a>Azure Databricks

Azure Databricks je prostředí založené na Apache Spark v cloudu Azure. Dá se použít jako cíl služby COMPUTE s kanálem Azure Machine Learning.

Před použitím vytvořte pracovní prostor Azure Databricks. Pokud chcete vytvořit prostředek pracovního prostoru, přečtěte si téma [spuštění úlohy Spark v dokumentu Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) .

Pokud chcete připojit Azure Databricks jako cíl výpočetních prostředků, zadejte následující informace:

* __Název výpočtu__datacihly: Název, který chcete přiřadit k tomuto výpočetnímu prostředku.
* __Název pracovního prostoru__datacihly: Název pracovního prostoru Azure Databricks.
* __Přístupový token__datacihly: Přístupový token, který se používá k ověření Azure Databricks. K vygenerování přístupového tokenu, najdete v článku [ověřování](https://docs.azuredatabricks.net/api/latest/authentication.html) dokumentu.

Následující kód ukazuje, jak připojit Azure Databricks jako výpočetní cíl s Azure Machine Learning SDK:

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

Podrobnější příklad najdete v ukázkovém poznámkovém [bloku](https://aka.ms/pl-databricks) na GitHubu.

### <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics je platforma analýzy velkých objemů dat v cloudu Azure. Dá se použít jako cíl služby COMPUTE s kanálem Azure Machine Learning.

Před použitím vytvořte účet Azure Data Lake Analytics. Chcete-li vytvořit tento prostředek, najdete v článku [Začínáme s Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) dokumentu.

Připojit Data Lake Analytics jako cílové výpočetní prostředí, musíte používat sadu SDK Azure Machine Learning a zadejte následující informace:

* __Název výpočtu__: Název, který chcete přiřadit k tomuto výpočetnímu prostředku.
* __Skupina prostředků__: Skupina prostředků, která obsahuje účet Data Lake Analytics.
* __Název účtu__: Název Data Lake Analytics účtu

Následující kód ukazuje, jak se připojit Data Lake Analytics jako cílové výpočetní prostředí:

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

Podrobnější příklad najdete v ukázkovém poznámkovém [bloku](https://aka.ms/pl-adla) na GitHubu.

> [!TIP]
> Azure Machine Learning kanály funguje jenom s daty uloženými v úložišti dat výchozího účtu Data Lake Analytics. Pokud data, je potřeba pracovat s je v jiné než výchozí úložiště, můžete použít [ `DataTransferStep` ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) ke kopírování dat před školení.

## <a id="steps"></a>Vytvoření postupu kanálu

Po vytvoření a připojení cílového výpočetního prostředí k pracovnímu prostoru budete připraveni definovat krok kanálu. Nejsou k dispozici prostřednictvím sady SDK Azure Machine Learning mnoho předdefinovaných kroků. Nejběžnějším z těchto kroků je [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), který spouští skript Pythonu v zadaném cílovém výpočetním prostředí:

```python
from azureml.pipeline.steps import PythonScriptStep

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", output_data1],
    inputs=[blob_input_data],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Použití předchozích výsledků (`allow_reuse`) je klíč při použití kanálů v prostředí pro spolupráci, protože odstranění zbytečných opakovaných spuštění nabízí flexibilitu. Opakované použití je výchozí chování, pokud script_name, vstupy a parametry kroku zůstanou stejné. Když se výstup kroku znovu použije, úloha se neodešle do výpočetního prostředí, ale výsledky z předchozího běhu jsou hned dostupné pro spuštění dalšího kroku. Pokud `allow_reuse` je hodnota nastavena na false, nové spuštění bude pro tento krok vždy vygenerováno během provádění kanálu. 

Po definování kroků sestavíte kanál pomocí některých nebo všech těchto kroků.

> [!NOTE]
> Při definování kroků nebo sestavení kanálu se do služby Azure Machine Learning neodesílají žádné soubory ani data.

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

Další informace najdete v tématu věnovaném [balíčku Azure-Pipeline-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) a [kanálu třídy kanálu](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) .

## <a name="submit-the-pipeline"></a>Odeslat kanálu

Když kanál odešlete, služba Azure Machine Learning kontroluje závislosti pro jednotlivé kroky a nahraje snímek zadaného zdrojového adresáře. Pokud není zadán žádný zdrojový adresář, se nahraje aktuální místní adresář. Snímek je také uložen jako součást experimentu v pracovním prostoru.

> [!IMPORTANT]
> Chcete-li zabránit zahrnutí souborů do snímku, vytvořte soubor [. gitignore](https://git-scm.com/docs/gitignore) nebo `.amlignore` v adresáři a přidejte do něj soubory. Soubor používá stejnou syntaxi a vzory jako soubor [. gitignore.](https://git-scm.com/docs/gitignore) `.amlignore` Pokud oba soubory existují, `.amlignore` má soubor přednost.
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
* Připojí úložiště dat, je- `DataReference` li v kroku zadán objekt. Pokud připojení není podporován, data je místo toho zkopírován do cílového výpočetního prostředí.
* Spustí krok na výpočetním cíli zadaném v definici kroku. 
* Vytvoří artefakty, jako jsou protokoly, stdout a stderr, metriky a výstup určený krokem. Tyto artefakty se pak nahrají a uchovávají ve výchozím úložišti dat uživatele.

![Diagram spuštění experimentu jako kanálu](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Další informace najdete v referenčních informacích ke [třídě experimentu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) .



## <a name="github-tracking-and-integration"></a>Sledování a integrace GitHubu

Když spustíte školicí kurz, kde zdrojový adresář je místní úložiště Git, informace o úložišti se ukládají v historii spuštění. Například aktuální ID potvrzení pro úložiště je protokolováno jako součást historie.

## <a name="publish-a-pipeline"></a>Publikování kanálu

Můžete publikovat v kanálu ho později spustit s různými vstupy. Pro koncový bod REST již publikovaného kanálu pro příjem parametrů je před publikováním nutné tento kanál parametrizovat.

1. Chcete-li vytvořit kanál parametr, použijte [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) objektu s výchozí hodnotou.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Přidejte tuto `PipelineParameter` objektu jako parametr pro některý z kroků v kanálu následujícím způsobem:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     target=compute_target,
     source_directory=project_folder)
   ```

3. Publikujte tento kanál, který přijme parametr při vyvolání.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Spuštění publikované kanálu

Všechny publikované kanály mají koncový bod REST. Tento koncový bod vyvolá spuštění kanálu z externích systémů, například klientů mimo Python. Tento koncový bod povoluje ve scénářích dávkového vyhodnocování a přeškolení možnost spravovaná opakovatelnost.

Chcete-li vyvolat spuštění předchozího kanálu, budete potřebovat token hlaviček ověřování Azure Active Directory, jak je popsáno v tématu Referenční dokumentace [třídy AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) , nebo získat další podrobnosti v rámci [ověřování v](https://aka.ms/pl-restep-auth) poznámkovém bloku Azure Machine Learning.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

### <a name="view-results-of-a-published-pipeline"></a>Zobrazení výsledků publikovaného kanálu

Podívejte se na seznam všech publikovaných kanálů a jejich podrobnosti o spuštění:
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. [Zobrazení pracovního prostoru](how-to-manage-workspace.md#view) k nalezení seznamu kanálů.
 ![seznam kanálů strojového učení](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. Vyberte konkrétní kanálu pro zobrazení výsledků spuštění.

Tyto výsledky jsou také k dispozici na [cílové stránce pracovního prostoru (Preview)](https://ml.azure.com).

### <a name="disable-a-published-pipeline"></a>Zakázání publikovaného kanálu

Pokud chcete kanál ze seznamu publikovaných kanálů skrýt, můžete ho zakázat:

```
# Get the pipeline by using its ID from the Azure portal
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Můžete ho znovu povolit pomocí `p.enable()`. Další informace naleznete v tématu [PublishedPipeline Class Reference třídy](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) .


## <a name="caching--reuse"></a>Ukládání do mezipaměti & opakované použití  

Aby bylo možné optimalizovat a přizpůsobit chování kanálů, můžete provést několik věcí při ukládání do mezipaměti a opakovaném použití. Můžete například zvolit:
+ **Vypněte výchozí opakované použití pro krok spustit výstup** `allow_reuse=False` nastavením během [definice kroku](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Při použití kanálů ve spolupráci prostředí je klíč znovu použitelný, protože odstranění zbytečných běhů nabízí flexibilitu. Můžete se ale odhlásit z opakovaného použití.
+ **Rozšíříte pomocí algoritmu hash nad skriptem**také absolutní cestu nebo relativní cesty k source_directory jiným souborům a adresářům pomocí`hash_paths=['<file or directory']` 
+ **Vynutí generování výstupu pro všechny kroky v běhu** s`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Ve výchozím nastavení `allow_reuse` je pro kroky povolený a jenom hlavní soubor skriptu se vyhodnotí jako hash. Takže pokud skript pro daný krok zůstane stejný (`script_name`, vstupy a parametry), bude se znovu používat výstup spuštění předchozího kroku, úloha se neodešle do výpočetní služby a výsledky z předchozího běhu jsou hned dostupné pro další krok. .  

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```
 

## <a name="next-steps"></a>Další kroky

- Použití [tyto poznámkové bloky Jupyter v Githubu](https://aka.ms/aml-pipeline-readme) prozkoumat machine learning další kanály.
- Podívejte se na referenční nápovědu sady SDK pro balíček [AzureML-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) a balíček [AzureML-Pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

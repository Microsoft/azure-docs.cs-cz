---
title: Vytváření, spouštění, & sledování kanálů ML
titleSuffix: Azure Machine Learning
description: Vytvořte a spusťte kanál strojového učení s Azure Machine Learning SDK pro Python. Pomocí kanálů ML můžete vytvářet a spravovat pracovní postupy, které spojí dohromady fáze strojového učení (ML). Tyto fáze zahrnují přípravu dat, školení modelů, nasazení modelu a odvození nebo bodování.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: af20c9e3a50c0c60135b1e447e7e1cba1fc36526
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815732"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Vytvoření a spuštění kanálů strojového učení s Azure Machine Learning SDK

V tomto článku se naučíte, jak pomocí [sady Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)vytvořit, publikovat, spouštět a sledovat [kanál strojového učení](concept-ml-pipelines.md) .  Pomocí **kanálů ml** můžete vytvořit pracovní postup, který spojuje různé fáze ml, a pak tento kanál publikovat do svého pracovního prostoru Azure Machine Learning pro pozdější přístup nebo sdílení s ostatními.  Kanály ML jsou ideální pro scénáře dávkového vyhodnocování, které používají různé výpočetní prostředky, místo jejich spouštění a sdílení pracovních postupů ML s ostatními. 

I když můžete použít jiný typ kanálu, který se nazývá [kanál Azure](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) pro automatizaci úloh v ml/CD pro CI, tento typ kanálu se nikdy neuloží do vašeho pracovního prostoru. [Porovnejte tyto různé kanály](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Každá fáze kanálu ML, jako je třeba Příprava dat a školení k modelu, může zahrnovat jeden nebo více kroků.

Kanály ML, které vytvoříte, jsou viditelné pro členy [pracovního prostoru](how-to-manage-workspace.md)Azure Machine Learning. 

Kanály ML používají vzdálené výpočetní cíle pro výpočet a úložiště mezia konečných dat přidružených k tomuto kanálu. Můžou číst a zapisovat data do a z podporovaných [Azure Storage](https://docs.microsoft.com/azure/storage/) umístění.

Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Předpoklady

* Vytvořte [pracovní prostor Azure Machine Learning](how-to-manage-workspace.md) pro uložení všech prostředků kanálu.

* [Nakonfigurujte vývojové prostředí](how-to-configure-environment.md) pro instalaci Azure Machine Learning sady SDK nebo použijte [virtuální počítač Poznámkový blok](tutorial-1st-experiment-sdk-setup.md#azure) s již nainstalovanou sadou SDK.

Začněte připojením pracovního prostoru:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```


## <a name="set-up-machine-learning-resources"></a>Nastavení prostředků strojového učení

Vytvoření prostředků potřebných ke spuštění kanálu ML:

* Nastavte úložiště dat, které se používá pro přístup k datům potřebným v postupu kanálu.

* Nakonfigurujte objekt `DataReference` tak, aby odkazoval na data, která se nacházejí v nebo jsou přístupná v úložišti dat.

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
    ["./data/20news.pkl"],
    target_path="20newsgroups",
    overwrite=True)
```

Kanál se skládá z jednoho nebo více kroků. Krok je jednotka spuštěná na výpočetním cíli. Kroky můžou spotřebovávat zdroje dat a vytvářet "zprostředkující" data. Krok může vytvářet data, jako je model, adresář s modelem a závislými soubory nebo dočasná data. Tato data jsou pak k dispozici pro další kroky později v kanálu.

### <a name="configure-data-reference"></a>Konfigurace odkazu na data

Právě jste vytvořili zdroj dat, na který se dá odkazovat v kanálu jako vstup do kroku. Zdroj dat v kanálu je reprezentován objektem [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) . Objekt `DataReference` ukazuje na data, která jsou v nebo jsou přístupná z úložiště dat.

```python
from azureml.data.data_reference import DataReference

blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Mezilehlé údaje (nebo výstup kroku) jsou reprezentovány objektem [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) . `output_data1` se vytvoří jako výstup kroku a použije se jako vstup jednoho nebo více budoucích kroků. `PipelineData` zavádí závislost mezi jednotlivými kroky a vytvoří v kanálu implicitní pořadí spouštění.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

## <a name="set-up-compute-target"></a>Nastavit cíl výpočtů

V Azure Machine Learning pojem computes__ (nebo cíl služby __COMPUTE__) odkazuje na počítače nebo clustery, které provádějí výpočetní kroky v kanálu strojového učení.   Úplný seznam cílů výpočetních prostředků a jejich vytvoření a připojení k pracovnímu prostoru najdete v tématu [výpočetní cíle pro školení modelů](how-to-set-up-training-targets.md) .  Proces vytvoření a připojení cíle výpočtů je stejný, bez ohledu na to, jestli budete modelovat, nebo spustit krok kanálu. Po vytvoření a připojení cíle výpočetní služby použijte objekt `ComputeTarget` v [kroku kanálu](#steps).

> [!IMPORTANT]
> V rámci vzdálených úloh není podporováno provádění operací správy na cílech výpočtů. Vzhledem k tomu, že kanály strojového učení se odesílají jako Vzdálená úloha, nepoužívejte v rámci kanálu operace správy pro výpočetní cíle.

Níže jsou uvedeny příklady vytváření a připojování výpočetních cílů pro:

* Azure Machine Learning COMPUTE
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

### <a id="databricks"></a>Azure Databricks

Azure Databricks je prostředí založené na Apache Spark v cloudu Azure. Dá se použít jako cíl služby COMPUTE s kanálem Azure Machine Learning.

Před použitím vytvořte pracovní prostor Azure Databricks. Pokud chcete vytvořit prostředek pracovního prostoru, přečtěte si téma [spuštění úlohy Spark v dokumentu Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) .

Pokud chcete připojit Azure Databricks jako cíl výpočetních prostředků, zadejte následující informace:

* __Výpočetní název datacihly__: název, který chcete přiřadit k tomuto výpočetnímu prostředku.
* __Název pracovního prostoru datacihly__: název pracovního prostoru Azure Databricks.
* __Přístupový token datacihly__: přístupový token, který se používá k ověření Azure Databricks. Přístup k vygenerování přístupového tokenu najdete v dokumentu [ověřování](https://docs.azuredatabricks.net/api/latest/authentication.html) .

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

Podrobnější příklad najdete v [ukázkovém poznámkovém bloku](https://aka.ms/pl-databricks) na GitHubu.

### <a id="adla"></a>Azure Data Lake Analytics

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
> Kanály Azure Machine Learning můžou pracovat jenom s daty uloženými ve výchozím úložišti dat účtu Data Lake Analytics. Pokud data, která potřebujete k práci, jsou v nevýchozím úložišti, můžete k kopírování dat před školením použít [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) .

## <a id="steps"></a>Vytvoření postupu kanálu

Po vytvoření a připojení cílového výpočetního prostředí k pracovnímu prostoru budete připraveni definovat krok kanálu. V sadě Azure Machine Learning SDK je k dispozici celá řada integrovaných kroků. Nejběžnějším z těchto kroků je [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), který spouští skript Pythonu v zadaném cílovém výpočetním prostředí:

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

Použití předchozích výsledků (`allow_reuse`) je klíč při použití kanálů v prostředí pro spolupráci, protože odstranění zbytečných opakovaných spuštění nabízí flexibilitu. Opakované použití je výchozí chování, pokud script_name, vstupy a parametry kroku zůstanou stejné. Když se výstup kroku znovu použije, úloha se neodešle do výpočetního prostředí, ale výsledky z předchozího běhu jsou hned dostupné pro spuštění dalšího kroku. Je-li hodnota `allow_reuse` nastavená na hodnotu false, bude při provádění kanálu vždy vygenerován nový běh pro tento krok. 

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

Další informace najdete v tématu věnovaném [balíčku Azure-Pipeline-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) a [kanálu třídy kanálu](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) .

## <a name="submit-the-pipeline"></a>Odeslat kanál

Když kanál odešlete, Azure Machine Learning zkontroluje závislosti pro jednotlivé kroky a nahraje snímek zadaného zdrojového adresáře. Pokud není zadán žádný zdrojový adresář, je odeslán aktuální místní adresář. Snímek je také uložen jako součást experimentu v pracovním prostoru.

> [!IMPORTANT]
> Chcete-li zabránit zahrnutí souborů do snímku, vytvořte soubor [. gitignore](https://git-scm.com/docs/gitignore) nebo `.amlignore` v adresáři a přidejte do něj soubory. Soubor `.amlignore` používá stejnou syntaxi a vzory jako soubor [. gitignore](https://git-scm.com/docs/gitignore) . Pokud oba soubory existují, má soubor `.amlignore` přednost.
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
* Připojí úložiště dat, pokud je v kroku zadán objekt `DataReference`. Pokud se připojení nepodporuje, zkopírují se data do cílového výpočetního prostředí.
* Spustí krok na výpočetním cíli zadaném v definici kroku. 
* Vytvoří artefakty, jako jsou protokoly, stdout a stderr, metriky a výstup určený krokem. Tyto artefakty se pak nahrají a uchovávají ve výchozím úložišti dat uživatele.

![Diagram spuštění experimentu jako kanálu](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Další informace najdete v referenčních informacích ke [třídě experimentu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) .



## <a name="github-tracking-and-integration"></a>Sledování a integrace GitHubu

Když spustíte školicí kurz, kde zdrojový adresář je místní úložiště Git, informace o úložišti se ukládají v historii spuštění. Například aktuální ID potvrzení pro úložiště je protokolováno jako součást historie.

## <a name="publish-a-pipeline"></a>Publikování kanálu

Můžete publikovat kanál a později ho spustit s různými vstupy. Pro koncový bod REST již publikovaného kanálu pro příjem parametrů je před publikováním nutné tento kanál parametrizovat.

1. Chcete-li vytvořit parametr kanálu, použijte objekt [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) s výchozí hodnotou.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Tento objekt `PipelineParameter` přidejte jako parametr do některého z kroků v kanálu následujícím způsobem:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     target=compute_target,
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
1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

1. [Zobrazte svůj pracovní prostor](how-to-manage-workspace.md#view) a najděte seznam kanálů.
 @no__t – 0list kanálů strojového učení @ no__t-1
 
1. Výběrem konkrétního kanálu zobrazíte výsledky spuštění.

Tyto výsledky jsou také k dispozici na [cílové stránce pracovního prostoru (Preview)](https://ml.azure.com).

### <a name="disable-a-published-pipeline"></a>Zakázání publikovaného kanálu

Pokud chcete kanál ze seznamu publikovaných kanálů skrýt, můžete ho zakázat:

```
# Get the pipeline by using its ID from the Azure portal
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Můžete ji znovu povolit s `p.enable()`. Další informace naleznete v tématu [PublishedPipeline Class Reference třídy](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) .


## <a name="caching--reuse"></a>Ukládání do mezipaměti & opakované použití  

Aby bylo možné optimalizovat a přizpůsobit chování kanálů, můžete provést několik věcí při ukládání do mezipaměti a opakovaném použití. Můžete například zvolit:
+ **Vypněte výchozí opakované použití pro krok spustit výstup** nastavením `allow_reuse=False` během [definice kroku](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Při použití kanálů ve spolupráci prostředí je klíč znovu použitelný, protože odstranění zbytečných běhů nabízí flexibilitu. Můžete se ale odhlásit z opakovaného použití.
+ **Rozšíříte hashování mimo skript**, aby zahrnovalo také absolutní cestu nebo relativní cesty k source_directory do jiných souborů a adresářů pomocí `hash_paths=['<file or directory']`. 
+ **Vynutit generování výstupu pro všechny kroky v běhu** s `pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Ve výchozím nastavení je `allow_reuse` pro kroky povolený a jenom hlavní soubor skriptu má hodnotu hash. Takže pokud skript pro daný krok zůstane stejný (`script_name`, vstupy a parametry), bude výstup spuštění předchozího kroku znovu použit, úloha není odeslána do výpočetního výkonu a výsledky z předchozího běhu jsou okamžitě k dispozici pro další krok.  

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

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

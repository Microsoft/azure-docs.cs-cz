---
title: Vytvoření a spuštění kanálů ML
titleSuffix: Azure Machine Learning
description: Vytvářejte a spouštějte kanály strojového učení, abyste mohli vytvářet a spravovat pracovní postupy, které dohromady spojí fáze strojového učení (ML).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: nilsp
author: NilsPohlmann
ms.date: 03/02/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 188df9564905443b8f975eb743b24885b5d03c32
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618198"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Vytvoření a spuštění kanálů strojového učení s Azure Machine Learning SDK

V tomto článku se dozvíte, jak vytvořit a spustit [kanály strojového učení](concept-ml-pipelines.md) pomocí [sady Azure Machine Learning SDK](/python/api/overview/azure/ml/intro). Použití **kanálů ml** k vytvoření pracovního postupu, který spojuje různé fáze ml. Pak tento kanál publikujte pro pozdější přístup nebo sdílení s ostatními. Sledujte kanály ML a podívejte se, jak váš model funguje v reálném světě a že se má detekovat posun dat. Kanály ML jsou ideální pro scénáře dávkového vyhodnocování, které používají různé výpočetní prostředky, místo jejich spouštění a sdílení pracovních postupů ML s ostatními.

Tento článek není kurz. Pokyny k vytvoření prvního kanálu najdete v tématu [kurz: sestavení kanálu Azure Machine Learning pro dávkové vyhodnocování](tutorial-pipeline-batch-scoring-classification.md) nebo [Použití automatizovaného ML v kanálu Azure Machine Learning v Pythonu](how-to-use-automlstep-in-pipelines.md). 

I když můžete použít jiný typ kanálu, který se nazývá [kanál Azure](/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2fmachine-learning%2fservice%2fcontext%2fml-context&tabs=yaml) pro automatizaci úloh na základě CI/CD, tento typ kanálu není uložený ve vašem pracovním prostoru. [Porovnejte tyto různé kanály](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Kanály ML, které vytvoříte, jsou viditelné pro členy [pracovního prostoru](how-to-manage-workspace.md)Azure Machine Learning. 

Kanály ML se spouštějí na výpočetních cílech (viz [co jsou výpočetní cíle v Azure Machine Learning](./concept-compute-target.md)). Kanály můžou číst a zapisovat data do a z podporovaných [Azure Storage](../storage/index.yml) umístění.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Předpoklady

* Vytvořte [pracovní prostor Azure Machine Learning](how-to-manage-workspace.md) pro uložení všech prostředků kanálu.

* [Nakonfigurujte vývojové prostředí](how-to-configure-environment.md) pro instalaci Azure Machine Learning sady SDK nebo použijte [výpočetní instanci Azure Machine Learning](concept-compute-instance.md) s již nainstalovanou sadou SDK.

Začněte připojením pracovního prostoru:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

## <a name="set-up-machine-learning-resources"></a>Nastavení prostředků strojového učení

Vytvoření prostředků potřebných ke spuštění kanálu ML:

* Nastavte úložiště dat, které se používá pro přístup k datům potřebným v postupu kanálu.

* Nakonfigurujte `Dataset` objekt tak, aby odkazoval na trvalá data, která se nachází v nebo jsou přístupná v úložišti dat. Nakonfigurujte `OutputFileDatasetConfig` objekt pro dočasná data předaná mezi kroky kanálu. 

* Nastavte [výpočetní cíle](concept-azure-machine-learning-architecture.md#compute-targets) , na kterých se budou spouštět vaše kroky kanálu.

### <a name="set-up-a-datastore"></a>Nastavení úložiště dat

Úložiště dat uchovává data pro kanál k přístupu. Každý pracovní prostor má výchozí úložiště dat. Můžete zaregistrovat více úložišť dat. 

Při vytváření pracovního prostoru jsou [soubory Azure](../storage/files/storage-files-introduction.md) a [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) připojené k pracovnímu prostoru. Pro připojení k úložišti objektů BLOB v Azure je zaregistrované výchozí úložiště dat. Další informace najdete v tématu [rozhodování, kdy používat soubory Azure, objekty blob Azure nebo disky Azure](../storage/common/storage-introduction.md). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Postup obecně spotřebovává data a vytváří výstupní data. Krok může vytvářet data, jako je model, adresář s modelem a závislými soubory nebo dočasná data. Tato data jsou pak k dispozici pro další kroky později v kanálu. Další informace o připojení kanálu k vašim datům najdete v článcích [o tom, jak získat přístup k datům](how-to-access-data.md) a [jak registrovat datové sady](how-to-create-register-datasets.md). 

### <a name="configure-data-with-dataset-and-outputfiledatasetconfig-objects"></a>Konfigurace dat s `Dataset` `OutputFileDatasetConfig` objekty a

Upřednostňovaným způsobem, jak poskytnout data kanálu, je objekt [DataSet](/python/api/azureml-core/azureml.core.dataset.Dataset) . `Dataset`Objekt odkazuje na data, která jsou v nebo jsou přístupná z úložiště dat nebo na webové adrese URL. `Dataset`Třída je abstraktní, takže vytvoříte instanci buď a `FileDataset` (odkazující na jeden nebo více souborů), nebo `TabularDataset` vytvořenou z jednoho nebo více souborů s oddělenými sloupci dat.

Vytvoříte `Dataset` pomocí metod, jako je [from_files](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) nebo [from_delimited_files](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-).

```python
from azureml.core import Dataset

my_dataset = Dataset.File.from_files([(def_blob_store, 'train-images/')])
```

Mezilehlé údaje (nebo výstup kroku) jsou reprezentovány objektem [OutputFileDatasetConfig](/python/api/azureml-pipeline-core/azureml.data.output_dataset_config.outputfiledatasetconfig) . `output_data1` je vyráběn jako výstup kroku. Tato data lze volitelně zaregistrovat jako datovou sadu voláním `register_on_complete` . Pokud vytvoříte `OutputFileDatasetConfig` v jednom kroku a použijete ho jako vstup do jiného kroku, tato závislost dat mezi kroky vytvoří implicitní pořadí spouštění v kanálu.

`OutputFileDatasetConfig` objekty vracejí adresář a ve výchozím nastavení zapisuje výstup do výchozího úložiště dat pracovního prostoru.

```python
from azureml.data import OutputFileDatasetConfig

output_data1 = OutputFileDatasetConfig(destination = (datastore, 'outputdataset/{run-id}'))
output_data_dataset = output_data1.register_on_complete(name = 'prepared_output_data')

```

> [!IMPORTANT]
> Dočasná data uložená pomocí `OutputFileDatasetConfig` se v Azure automaticky neodstraní.
> Na konci spuštění kanálu byste měli buď programově odstranit mezilehlé údaje, použít úložiště dat se zkrácenými zásadami pro uchovávání dat, nebo pravidelně provádět vyčištění ručně.

> [!TIP]
> Nahrávejte pouze soubory související s danou úlohou. Jakékoli změny v souborech v adresáři dat se zobrazí jako důvod pro opětovné spuštění kroku při příštím spuštění kanálu i v případě, že je parametr znovu použit. 

## <a name="set-up-a-compute-target"></a>Nastavení cíle výpočtů


V Azure Machine Learning pojem __výpočetní__ prostředí (neboli __cíl výpočetní__ služby) odkazuje na počítače nebo clustery, které provádějí výpočetní kroky v kanálu Machine Learning.   Seznamte se s [výpočetními cíli pro školení modelů](concept-compute-target.md#train) pro úplný seznam cílových výpočetních cílů a [vytvořte výpočetní cíle](how-to-create-attach-compute-studio.md) pro vytvoření a připojení k pracovnímu prostoru.   Proces vytvoření a připojení cíle výpočtů je stejný, bez ohledu na to, jestli provádíte školení modelu nebo spustíte krok kanálu. Po vytvoření a připojení cíle výpočetní služby použijte `ComputeTarget` objekt v [kroku kanálu](#steps).

> [!IMPORTANT]
> Provádění operací správy cílových výpočetních objektů se nepodporuje ze vzdálených úloh. Vzhledem k tomu, že se kanály strojového učení odesílají jako vzdálené úlohy, nepoužívejte v kanálu operace správy cílových výpočetních objektů.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning COMPUTE

Pro spuštění kroků můžete vytvořit Azure Machine Learning Compute. Kód pro jiné výpočetní cíle je podobný, s mírně odlišnými parametry v závislosti na typu. 

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

## <a name="configure-the-training-runs-environment"></a>Konfigurace prostředí výukového běhu

V dalším kroku se ujistěte, že se na vzdáleném cvičení spouští všechny závislosti, které jsou potřeba pro kroky školení. Závislosti a kontext modulu runtime jsou nastaveny vytvořením a konfigurací `RunConfiguration` objektu. 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import Environment 

aml_run_config = RunConfiguration()
# `compute_target` as defined in "Azure Machine Learning compute" section above
aml_run_config.target = compute_target

USE_CURATED_ENV = True
if USE_CURATED_ENV :
    curated_environment = Environment.get(workspace=ws, name="AzureML-Tutorial")
    aml_run_config.environment = curated_environment
else:
    aml_run_config.environment.python.user_managed_dependencies = False
    
    # Add some packages relied on by data prep step
    aml_run_config.environment.python.conda_dependencies = CondaDependencies.create(
        conda_packages=['pandas','scikit-learn'], 
        pip_packages=['azureml-sdk', 'azureml-dataprep[fuse,pandas]'], 
        pin_sdk_version=False)
```

Výše uvedený kód ukazuje dvě možnosti pro zpracování závislostí. Jak je uvedeno v `USE_CURATED_ENV = True` , je Konfigurace založená na spravovaném prostředí. Prebakedá prostředí jsou "" s běžnými nezávislými knihovnami a dají se rychleji převést do online režimu. Ve spravovaném prostředí jsou předem připravené image Docker v [Microsoft Container Registry](https://hub.docker.com/publishers/microsoftowner). Další informace najdete v tématu [Azure Machine Learning v nich uspořádaná prostředí](resource-curated-environments.md).

Cesta, která se bere v případě, že se změní, `USE_CURATED_ENV` se `False` zobrazí jako způsob explicitního nastavení závislostí. V takovém scénáři se vytvoří nová vlastní image Docker, která se zaregistruje v Azure Container Registry v rámci vaší skupiny prostředků (viz [Úvod do registrů kontejnerů privátního Docker v Azure](../container-registry/container-registry-intro.md)). Sestavení a registrace tohoto obrázku může trvat několik minut.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>Vytvoření postupu kanálu

Jakmile budete mít výpočetní prostředky a prostředí vytvořené, budete připraveni k definování kroků kanálu. V sadě Azure Machine Learning SDK je k dispozici celá řada integrovaných kroků, jak vidíte v [referenční dokumentaci k `azureml.pipeline.steps` balíčku](/python/api/azureml-pipeline-steps/azureml.pipeline.steps). Nejpružnější třída je [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), která spouští skript Pythonu.

```python
from azureml.pipeline.steps import PythonScriptStep
dataprep_source_dir = "./dataprep_src"
entry_point = "prepare.py"
# `my_dataset` as defined above
ds_input = my_dataset.as_named_input('input1')

# `output_data1`, `compute_target`, `aml_run_config` as defined above
data_prep_step = PythonScriptStep(
    script_name=entry_point,
    source_directory=dataprep_source_dir,
    arguments=["--input", ds_input.as_download(), "--output", output_data1],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

Výše uvedený kód ukazuje typický krok počátečního kanálu. Kód pro přípravu dat je v podadresáři (v tomto příkladu `"prepare.py"` v adresáři `"./dataprep.src"` ). V rámci procesu vytváření kanálu je tento adresář zip a nahrán do `compute_target` a krok spustí skript zadaný jako hodnota pro `script_name` .

`arguments`Hodnoty určují vstupy a výstupy kroku. V předchozím příkladu je základní data `my_dataset` datovou sadou. Odpovídající data budou stažena do výpočetního prostředku, protože tento kód určuje jako `as_download()` . Skript `prepare.py` provádí jakékoli úlohy transformace dat, které jsou vhodné pro úlohy, a výstupy dat do `output_data1` typu `OutputFileDatasetConfig` . Další informace najdete v tématu [přesun dat do a mezi kroky kanálu ml (Python)](how-to-move-data-in-out-of-pipelines.md). Krok se spustí na počítači definovaném `compute_target` pomocí konfigurace `aml_run_config` . 

Použití předchozích výsledků ( `allow_reuse` ) je klíč při použití kanálů v prostředí pro spolupráci, protože odstranění zbytečných opakovaných spuštění nabízí flexibilitu. Opakované použití je výchozí chování, když script_name, vstupy a parametry kroku zůstanou stejné. Pokud je povoleno opakované použití, výsledky z předchozího spuštění se okamžitě odešlou na další krok. Pokud `allow_reuse` je nastavená na `False` , nový běh se pro tento krok vždy vygeneruje během provádění kanálu.

Je možné vytvořit kanál s jediným krokem, ale téměř vždy se rozhodnete rozdělit celkový proces do několika kroků. Například můžete mít kroky pro přípravu dat, školení, porovnávání modelů a nasazení. Jeden krok může například představit, že po `data_prep_step` výše uvedeném postupu může být dalším krokem školení:

```python
train_source_dir = "./train_src"
train_entry_point = "train.py"

training_results = OutputFileDatasetConfig(name = "training_results",
    destination = def_blob_store)

    
train_step = PythonScriptStep(
    script_name=train_entry_point,
    source_directory=train_source_dir,
    arguments=["--prepped_data", output_data1.as_input(), "--training_results", training_results],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

Výše uvedený kód je podobný kódu v kroku Příprava dat. Školicí kód je v adresáři, který je oddělený od kódu pro přípravu dat. `OutputFileDatasetConfig`Výstupem kroku Příprava dat `output_data1` se používá jako _vstup_ do kroku školení. Vytvoří se nový `OutputFileDatasetConfig` objekt, `training_results` který bude obsahovat výsledky pro následné porovnání nebo krok nasazení. 

Další příklady kódu najdete v tématu Jak [vytvořit dva kroky kanálu ml](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb) a [jak zapisovat data zpátky do úložiště dat při dokončení běhu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb).

Po definování kroků sestavíte kanál pomocí některých nebo všech těchto kroků.

> [!NOTE]
> Do Azure Machine Learning se při definování kroků nebo sestavení kanálu neodesílají žádné soubory ani data. Soubory jsou odeslány při volání metody [experiment. Submit ()](/python/api/azureml-core/azureml.core.experiment.experiment#submit-config--tags-none----kwargs-).

```python
# list of steps to run (`compare_step` definition not shown)
compare_models = [data_prep_step, train_step, compare_step]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compare_models])
```

### <a name="use-a-dataset"></a>Použití datové sady 

Datové sady vytvořené ze služby Azure Blob Storage, souborů Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database a Azure Database for PostgreSQL lze použít jako vstup do libovolného kroku kanálu. Můžete napsat výstup do [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep), [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep)nebo pokud chcete zapisovat data do konkrétního úložiště dat [OutputFileDatasetConfig](/python/api/azureml-pipeline-core/azureml.data.outputfiledatasetconfig)použijte. 

> [!IMPORTANT]
> Zápis výstupních dat zpět do úložiště dat pomocí `OutputFileDatasetConfig` je podporovaný jenom pro úložiště Azure Blob, sdílenou složku Azure, adls pro dataúložiště pro obecné 1 a obecné 2. 

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Datovou sadu v kanálu pak načtěte pomocí [Run.input_datasets](/python/api/azureml-core/azureml.core.run.run#input-datasets) slovníku.

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

## <a name="caching--reuse"></a>Ukládání do mezipaměti & opakované použití  

Aby bylo možné optimalizovat a přizpůsobit chování kanálů, můžete provést několik věcí při ukládání do mezipaměti a opakovaném použití. Můžete například zvolit:
+ **Vypněte výchozí opakované použití pro krok spustit výstup** nastavením `allow_reuse=False` během [definice kroku](/python/api/azureml-pipeline-steps/). Při použití kanálů ve spolupráci prostředí je klíč znovu použitelný, protože odstranění zbytečných běhů nabízí flexibilitu. Můžete se ale odhlásit z opakovaného použití.
+ **Vynutí generování výstupu pro všechny kroky v běhu** s `pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Ve výchozím nastavení jsou `allow_reuse` pro kroky povolené a `source_directory` zadaná v definici kroku má hodnotu hash. Takže pokud skript pro daný krok zůstane stejný ( `script_name` , vstupy a parametry) a nic jiného v takovém případě se ` source_directory` nezmění, výstup spuštění předchozího kroku se znovu použije, úloha se neodešle do výpočetního výkonu a výsledky z předchozího běhu jsou hned k dispozici pro další krok.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

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
* Nakonfiguruje přístup `Dataset` k `OutputFileDatasetConfig` objektům a. V případě `as_mount()` režimu přístupu se k zajištění virtuálního přístupu používá POjistka. Pokud se připojení nepodporuje nebo pokud uživatel zadal přístup jako `as_upload()` , data se zkopírují do cíle výpočtů.

* Spustí krok na výpočetním cíli zadaném v definici kroku. 
* Vytvoří artefakty, jako jsou protokoly, stdout a stderr, metriky a výstup určený krokem. Tyto artefakty se pak nahrají a uchovávají ve výchozím úložišti dat uživatele.

![Diagram spuštění experimentu jako kanálu](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Další informace najdete v referenčních informacích ke [třídě experimentu](/python/api/azureml-core/azureml.core.experiment.experiment) .

## <a name="use-pipeline-parameters-for-arguments-that-change-at-inference-time"></a>Použití parametrů kanálu pro argumenty, které se mění v čase odvození

V některých případech se argumenty jednotlivých kroků v rámci kanálu vztahují na období vývoje a školení: věci, jako jsou kurzy školení a potenciál, nebo cesty k datům nebo konfiguračním souborům. Když se model nasadí, ale budete chtít dynamicky předat argumenty, na kterých jste Inferencing (to znamená dotaz, na který jste model sestavili tak, aby odpovídal!). Tyto typy parametrů kanálu by měly být zadány. Chcete-li to provést v Pythonu, použijte `azureml.pipeline.core.PipelineParameter` třídu, jak je znázorněno v následujícím fragmentu kódu:

```python
from azureml.pipeline.core import PipelineParameter

pipeline_param = PipelineParameter(name="pipeline_arg", default_value="default_val")
train_step = PythonScriptStep(script_name="train.py",
                            arguments=["--param1", pipeline_param],
                            target=compute_target,
                            source_directory=project_folder)
```

### <a name="how-python-environments-work-with-pipeline-parameters"></a>Jak prostředí Python pracují s parametry kanálu

Jak bylo popsáno dříve v [části Konfigurace prostředí výukového běhu](#configure-the-training-runs-environment), stavu prostředí a závislostí knihovny Pythonu, jsou určeny pomocí `Environment` objektu. Obecně můžete zadat existující `Environment` odkazem na jeho název a volitelně na verzi:

```python
aml_run_config = RunConfiguration()
aml_run_config.environment.name = 'MyEnvironment'
aml_run_config.environment.version = '1.0'
```

Pokud se ale rozhodnete použít `PipelineParameter` objekty k dynamickému nastavení proměnných za běhu pro kroky vašeho kanálu, nemůžete použít tuto techniku pro odkazování na existující `Environment` . Místo toho, pokud chcete použít `PipelineParameter` objekty, je nutné nastavit `environment` pole `RunConfiguration` `Environment` objektu na objekt. Je vaše zodpovědnost za to, že je nutné zajistit, aby `Environment` se jeho závislosti na externích balíčcích Python správně nastavily.


## <a name="view-results-of-a-pipeline"></a>Zobrazení výsledků kanálu

Podívejte se na seznam všech vašich kanálů a jejich podrobnosti o spuštěních v studiu:

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com).

1. [Zobrazení pracovního prostoru](how-to-manage-workspace.md#view)

1. Na levé straně vyberte **kanály** a zobrazte všechny spuštěné kanály.
 ![seznam kanálů strojového učení](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Výběrem konkrétního kanálu zobrazíte výsledky spuštění.

### <a name="git-tracking-and-integration"></a>Sledování a integrace Git

Když spustíte školicí kurz, kde zdrojový adresář je místní úložiště Git, informace o úložišti se ukládají v historii spuštění. Další informace najdete v tématu [integrace Gitu pro Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="next-steps"></a>Další kroky

- Pokud chcete svůj kanál sdílet s kolegy nebo zákazníky, přečtěte si téma [publikování kanálů strojového učení](how-to-deploy-pipelines.md) .
- Využijte [tyto Jupyter poznámkové bloky na GitHubu](https://aka.ms/aml-pipeline-readme) a prozkoumejte kanály strojového učení.
- Podívejte se na referenční nápovědu sady SDK pro balíček [AzureML-Pipelines-Core](/python/api/azureml-pipeline-core/) a balíček [AzureML-Pipelines-Steps](/python/api/azureml-pipeline-steps/) .
- Tipy k ladění kanálů a odstraňování potíží najdete v tématu [návody](how-to-debug-pipelines.md) :
- Postupujte podle pokynů v článku věnovaném [využití poznámkových bloků Jupyter k prozkoumání této služby](samples-notebooks.md) a zjistěte, jak provozovat poznámkové bloky.
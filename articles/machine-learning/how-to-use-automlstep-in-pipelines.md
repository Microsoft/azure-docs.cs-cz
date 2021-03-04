---
title: Použití automatizovaného ML v kanálech ML
titleSuffix: Azure Machine Learning
description: AutoMLStep umožňuje používat automatizované strojové učení ve vašich kanálech.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
manager: cgronlun
ms.date: 02/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, automl
ms.openlocfilehash: da973cf377ceace4a92d1cdd1e956321a5592e6a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692211"
---
# <a name="use-automated-ml-in-an-azure-machine-learning-pipeline-in-python"></a>Použití automatizovaného ML v kanálu Azure Machine Learning v Pythonu


Funkce automatizovaného škálování na základě Azure Machine Learning vám pomůže zjistit modely s vysokým výkonem bez nutnosti opětovné implementace každého možného přístupu. V kombinaci s Azure Machine Learning kanály můžete vytvářet nasaditelné pracovní postupy, které umožňují rychle zjistit algoritmus, který je pro vaše data nejvhodnější. V tomto článku se dozvíte, jak efektivně připojit krok přípravy dat k automatizovanému kroku ML. Automatizovaná ML může rychle zjistit algoritmus, který je pro vaše data nejvhodnější, a přitom vás zavede na cestách, aby MLOps a modeloval provozní životní cyklus s kanály.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

* Pracovní prostor služby Azure Machine Learning. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).  

* Seznamte se s [automatizovanými strojovým učením](concept-automated-ml.md) a sadou SDK pro [strojové](concept-ml-pipelines.md) učení v Azure.

## <a name="review-automated-mls-central-classes"></a>Kontrola centrálních tříd automatizovaného ML

Automatizovaná ML v kanálu je reprezentována `AutoMLStep` objektem. `AutoMLStep`Třída je podtřídou třídy `PipelineStep` . Graf `PipelineStep` objektů definuje `Pipeline` .

Existuje několik podtříd třídy `PipelineStep` . Kromě toho se v `AutoMLStep` tomto článku zobrazí `PythonScriptStep` Příprava dat a další pro registraci modelu.

Upřednostňovaným způsobem, jak zpočátku přesunout data _do_ kanálu ml, je `Dataset` objekty. Chcete-li přesunout data _mezi_ kroky a možnou možností uložit výstup z běhu, preferovaným způsobem jsou [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) [`OutputTabularDatasetConfig`](/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?preserve-view=true&view=azure-ml-py) objekty a. Chcete-li použít s `AutoMLStep` , `PipelineData` objekt musí být transformován do `PipelineOutputTabularDataset` objektu. Další informace najdete v tématu [vstupní a výstupní data z kanálů ml](how-to-move-data-in-out-of-pipelines.md).

`AutoMLStep`Je nakonfigurován prostřednictvím `AutoMLConfig` objektu. `AutoMLConfig` je flexibilní třída, jak je popsáno v tématu [Konfigurace automatizované experimenty ml v Pythonu](./how-to-configure-auto-train.md#configure-your-experiment-settings). 

`Pipeline`Spuštění v `Experiment` . Kanál `Run` má pro každý krok podřízenou položku `StepRun` . Výstupy automatizovaného ML `StepRun` jsou metriky školení a model nejvyšší výkon.

V tomto článku se vytvoří jednoduchý kanál pro úlohu klasifikace. Úkol předpovídá přežití Titanic, ale data ani úkoly nebudeme diskutovat s výjimkou předání.

## <a name="get-started"></a>Začínáme

### <a name="retrieve-initial-dataset"></a>Načíst počáteční datovou sadu

Pracovní postup ML se často spouští s předem existujícími daty směrného plánu. Toto je dobrý scénář pro registrovanou datovou sadu. Datové sady jsou viditelné v pracovním prostoru, podporují správu verzí a dají se interaktivně prozkoumat. Existuje mnoho způsobů, jak vytvořit a naplnit datovou sadu, jak je popsáno v tématu [vytváření Azure Machine Learning datových sad](how-to-create-register-datasets.md). Vzhledem k tomu, že k vytvoření našeho kanálu použijeme sadu Python SDK, pomocí sady SDK si stáhněte základní data a zaregistrujte ji s názvem titanic_ds.

```python
from azureml.core import Workspace, Dataset

ws = Workspace.from_config()
if not 'titanic_ds' in ws.datasets.keys() :
    # create a TabularDataset from Titanic training data
    web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
                 'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
    titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

    titanic_ds.register(workspace = ws,
                                     name = 'titanic_ds',
                                     description = 'Titanic baseline data',
                                     create_new_version = True)

titanic_ds = Dataset.get_by_name(ws, 'titanic_ds')
```

Kód se nejprve přihlásí k pracovnímu prostoru Azure Machine Learning definovanému v **config.js** (vysvětlení najdete v tématu [vytvoření konfiguračního souboru pracovního prostoru](how-to-configure-environment.md#workspace)). Pokud již není datová sada s názvem `'titanic_ds'` registrována, pak ji vytvoří. Kód stáhne data ve formátu CSV z webu, používá je k vytvoření instance `TabularDataset` a a pak tuto datovou sadu zaregistruje v pracovním prostoru. Nakonec funkce `Dataset.get_by_name()` přiřadí `Dataset` k `titanic_ds` . 

### <a name="configure-your-storage-and-compute-target"></a>Konfigurace úložiště a cíle výpočtů

Další materiály, které bude kanál potřebovat, jsou úložiště a obecně Azure Machine Learning výpočetní prostředky. 

```python
from azureml.core import Datastore
from azureml.core.compute import AmlCompute, ComputeTarget

datastore = ws.get_default_datastore()

compute_name = 'cpu-cluster'
if not compute_name in ws.compute_targets :
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                min_nodes=0,
                                                                max_nodes=1)
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)

    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # Show the result
    print(compute_target.get_status().serialize())

compute_target = ws.compute_targets[compute_name]
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Mezilehlá data mezi přípravou dat a krokem automatizovaného ML se dají uložit do výchozího úložiště dat v pracovním prostoru, takže nepotřebujeme volat `get_default_datastore()` `Workspace` objekt. 

Potom kód zkontroluje, zda již existuje cíl výpočtu AML `'cpu-cluster'` . Pokud ne, určíme, že chceme použít malý výpočetní cíl založený na procesoru. Pokud máte v úmyslu používat funkce hloubkového učení automatizovaného programu ML (například text featurization s podporou DNN), měli byste zvolit výpočetní výkon pomocí silné podpory GPU, jak je popsáno v tématu [velikosti virtuálních počítačů](../virtual-machines/sizes-gpu.md)s podporou GPU. 

Bloky kódu až do zřízení cíle a pak vytisknou nějaké podrobnosti o právě vytvořeném cílovém výpočetním cíli. Nakonec se pojmenovaný cíl výpočtů načte z pracovního prostoru a přiřadí se k `compute_target` . 

### <a name="configure-the-training-run"></a>Konfigurace spuštění školení

AutoMLStep konfiguruje své závislosti automaticky během odesílání úlohy. Běhový kontext je nastaven vytvořením a konfigurací `RunConfiguration` objektu. Tady nastavíme cíl výpočtů.

```python
from azureml.core.runconfig import RunConfiguration

aml_run_config = RunConfiguration()
# Use just-specified compute target ("cpu-cluster")
aml_run_config.target = compute_target
```

## <a name="prepare-data-for-automated-machine-learning"></a>Příprava dat pro automatizované Machine Learning

### <a name="write-the-data-preparation-code"></a>Zápis kódu pro přípravu dat

Datová sada Titanic standardních hodnot se skládá ze smíšených číselných a textových dat, u kterých chybí některé hodnoty. V rámci přípravy na automatizované strojové učení bude krok přípravek přípravy dat:

- Vyplňte chybějící data buď náhodnými daty, nebo kategorií odpovídající "neznámá".
- Transformovat kategorií data na celá čísla
- Vyřadit sloupce, které neplánujeme použít
- Rozdělení dat na školicí a testovací sady
- Zapsat transformovaná data do `OutputFileDatasetConfig` výstupních cest

```python
%%writefile dataprep.py
from azureml.core import Run

import pandas as pd 
import numpy as np 
import argparse

RANDOM_SEED=42

def prepare_age(df):
    # Fill in missing Age values from distribution of present Age values 
    mean = df["Age"].mean()
    std = df["Age"].std()
    is_null = df["Age"].isnull().sum()
    # compute enough (== is_null().sum()) random numbers between the mean, std
    rand_age = np.random.randint(mean - std, mean + std, size = is_null)
    # fill NaN values in Age column with random values generated
    age_slice = df["Age"].copy()
    age_slice[np.isnan(age_slice)] = rand_age
    df["Age"] = age_slice
    df["Age"] = df["Age"].astype(int)
    
    # Quantize age into 5 classes
    df['Age_Group'] = pd.qcut(df['Age'],5, labels=False)
    df.drop(['Age'], axis=1, inplace=True)
    return df

def prepare_fare(df):
    df['Fare'].fillna(0, inplace=True)
    df['Fare_Group'] = pd.qcut(df['Fare'],5,labels=False)
    df.drop(['Fare'], axis=1, inplace=True)
    return df 

def prepare_genders(df):
    genders = {"male": 0, "female": 1, "unknown": 2}
    df['Sex'] = df['Sex'].map(genders)
    df['Sex'].fillna(2, inplace=True)
    df['Sex'] = df['Sex'].astype(int)
    return df

def prepare_embarked(df):
    df['Embarked'].replace('', 'U', inplace=True)
    df['Embarked'].fillna('U', inplace=True)
    ports = {"S": 0, "C": 1, "Q": 2, "U": 3}
    df['Embarked'] = df['Embarked'].map(ports)
    return df
    
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
    
titanic_ds = Run.get_context().input_datasets['titanic_ds']
df = titanic_ds.to_pandas_dataframe().drop(['PassengerId', 'Name', 'Ticket', 'Cabin'], axis=1)
df = prepare_embarked(prepare_genders(prepare_fare(prepare_age(df))))

df.to_csv(os.path.join(args.output_path,"prepped_data.csv"))

print(f"Wrote prepped data to {args.output_path}/prepped_data.csv")
```

Výše uvedený fragment kódu je kompletní, ale minimální příklad přípravy dat pro Titanic data. Fragment kódu začíná Jupyter "Magic Command" pro výstup kódu do souboru. Pokud nepoužíváte Poznámkový blok Jupyter, odeberte tento řádek a vytvořte soubor ručně.

Různé `prepare_` funkce v předchozím fragmentu kódu upravují příslušný sloupec ve vstupní datové sadě. Tyto funkce pracují s daty po změně na `DataFrame` objekt PANDAS. V každém případě chybějící data jsou vyplněna buď reprezentativními náhodnými daty, nebo kategorií daty ukazujícími "neznámé". Data kategorií založená na textu jsou mapována na celá čísla. Již nepotřebné sloupce jsou přepsány nebo vyřazeny. 

Poté, co kód definuje funkce přípravy dat, analyzuje kód vstupní argument, což je cesta, na kterou chceme zapisovat data.  (Tyto hodnoty budou určeny `OutputFileDatasetConfig` objekty, které budou popsány v následujícím kroku.) Kód načte registrovanou `'titanic_cs'` `Dataset` , převede ho na PANDAS `DataFrame` a zavolá různé funkce pro přípravu dat. 

Vzhledem k tomu `output_path` , že je adresář, volání `to_csv()` Určuje název souboru `prepped_data.csv` .

### <a name="write-the-data-preparation-pipeline-step-pythonscriptstep"></a>Zapsat krok kanálu přípravy dat ( `PythonScriptStep` )

Výše popsaný kód pro přípravu dat musí být přidružený k `PythonScripStep` objektu, který se má použít s kanálem. Cesta, na kterou je zapsán výstup sdíleného svazku clusteru, je vygenerována `OutputFileDatasetConfig` objektem. Prostředky připravené dříve, jako `ComputeTarget` `RunConfig` jsou, a, `'titanic_ds' Dataset` jsou použity k dokončení specifikace.

```python
from azureml.data import OutputFileDatasetConfig
from azureml.pipeline.steps import PythonScriptStep

prepped_data_path = OutputFileDatasetConfig(name="output_path")

dataprep_step = PythonScriptStep(
    name="dataprep", 
    script_name="dataprep.py", 
    compute_target=compute_target, 
    runconfig=aml_run_config,
    arguments=["--output_path", prepped_data_path],
    inputs=[titanic_ds.as_named_input('titanic_ds')],
    allow_reuse=True
)
```

`prepped_data_path`Objekt je typu, `OutputFileDatasetConfig` který odkazuje na adresář.  Všimněte si, že je zadáno v `arguments` parametru. Pokud si provedete předchozí krok, uvidíte, že v rámci kódu pro přípravu dat je hodnota argumentu `'--output_path'` cesta k adresáři, ve kterém byl soubor CSV zapsán. 

## <a name="train-with-automlstep"></a>Výuka pomocí AutoMLStep

Konfigurace postupu automatizovaného kanálu se provádí s `AutoMLConfig` třídou. Tato flexibilní třída je popsaná v tématu [Konfigurace automatizované experimenty ml v Pythonu](./how-to-configure-auto-train.md). Vstupní a výstupní data jsou jediné aspekty konfigurace, které vyžadují zvláštní pozornost v kanálu ML. Vstupní a výstupní `AutoMLConfig` informace pro v kanálech jsou podrobněji popsány níže. Kromě dat je výhodou kanálů ML možnost použít pro různé kroky různé výpočetní cíle. Můžete se rozhodnout, že budete používat výkonnější `ComputeTarget` jenom pro proces automatizovaného ml. Uděláte to jednoduše tak, že přiřadíte výkonnějšímu `RunConfiguration` `AutoMLConfig` `run_configuration` parametru objektu.

### <a name="send-data-to-automlstep"></a>Odeslat data do `AutoMLStep`

Vstupní data v kanálu ML musí být `Dataset` objekt. Nejvyšší způsob provádění je poskytnout vstupní data ve formě `OutputTabularDatasetConfig` objektů. Vytvoříte objekt daného typu s příponou, jako je například  `read_delimited_files()` `OutputFileDatasetConfig` `prepped_data_path` `prepped_data_path` objekt.

```python
# type(prepped_data) == OutputTabularDatasetConfig
prepped_data = prepped_data_path.read_delimited_files()
```

Další možností je použít `Dataset` objekty registrované v pracovním prostoru:

```python
prepped_data = Dataset.get_by_name(ws, 'Data_prepared')
```

Porovnávání dvou technik:

| Technika | Výhody a nevýhody | 
|-|-|
|`OutputTabularDatasetConfig`| Vyšší výkon | 
|| Fyzická trasa z `OutputFileDatasetConfig` | 
|| Po spuštění kanálu nejsou data trvalá. |
|| [Postup ukazující na Poznámkový blok `OutputTabularDatasetConfig`](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) |
| Registrovat `Dataset` | Nižší výkon |
| | Může být vygenerováno mnoha způsoby | 
| | Data se přetrvají a jsou viditelná v celém pracovním prostoru. |
| | [Poznámkový blok znázorňující registrovanou `Dataset` techniku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/continuous-retraining/auto-ml-continuous-retraining.ipynb)


### <a name="specify-automated-ml-outputs"></a>Zadat automatizované výstupy ML

Výstupy `AutoMLStep` představují konečné skóre metriky modelu s vyšším výkonem a tento model sám sebe. Chcete-li tyto výstupy použít v dalších krocích kanálu, připravte `OutputFileDatasetConfig` objekty, aby je přijímaly.

```python
from azureml.pipeline.core import TrainingOutput

metrics_data = PipelineData(name='metrics_data',
                            datastore=datastore,
                            pipeline_output_name='metrics_output',
                            training_output=TrainingOutput(type='Metrics'))

model_data = PipelineData(name='best_model_data',
                          datastore=datastore,
                          pipeline_output_name='model_output',
                          training_output=TrainingOutput(type='Model'))
```

Výše uvedený fragment kódu vytvoří dva `PipelineData` objekty pro metriky a výstup modelu. Každý má název, přiřazen k výchozímu úložišti dat dříve načtený a přidružený ke konkrétnímu `type` z `TrainingOutput` `AutoMLStep` . Vzhledem k tomu, že přiřadíme `pipeline_output_name` tyto `PipelineData` objekty, budou jejich hodnoty k dispozici nejen z kroku jednotlivého kanálu, ale z kanálu jako celku, jak je popsáno níže v části "kontrola výsledků kanálu". 

### <a name="configure-and-create-the-automated-ml-pipeline-step"></a>Konfigurace a vytvoření kroku pro kanál automatizovaného kanálu

Jakmile jsou vstupy a výstupy definovány, je čas vytvořit `AutoMLConfig` a `AutoMLStep` . Podrobnosti o konfiguraci budou záviset na vaší úloze, jak je popsáno v tématu [Konfigurace automatizovaných experimentů ml v Pythonu](./how-to-configure-auto-train.md). Pro úlohu klasifikace přežití Titanic ukazuje následující fragment kódu jednoduchou konfiguraci.

```python
from azureml.train.automl import AutoMLConfig
from azureml.pipeline.steps import AutoMLStep

# Change iterations to a reasonable number (50) to get better accuracy
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 2,
    "experiment_timeout_hours" : 0.25,
    "primary_metric" : 'AUC_weighted'
}

automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)

train_step = AutoMLStep(name='AutoML_Classification',
    automl_config=automl_config,
    passthru_automl_config=False,
    outputs=[metrics_data,model_data],
    enable_default_model_output=False,
    enable_default_metrics_output=False,
    allow_reuse=True)
```
Fragment kódu ukazuje idiom, který se běžně používá s `AutoMLConfig` . Argumenty, které jsou větší než kapalina (delšími), jsou určeny v samostatném slovníku, zatímco hodnoty, které jsou menší, se mohou měnit, jsou určeny přímo v `AutoMLConfig` konstruktoru. V takovém případě `automl_settings` Zadejte krátký běh: běh se zastaví po 2 iteracích nebo 15 minutách, podle toho, co nastane dřív.

`automl_settings`Slovník se předává `AutoMLConfig` konstruktoru jako kwargs. Ostatní parametry nejsou komplexní:

- `task` je nastaven na `classification` pro tento příklad. Další platné hodnoty jsou `regression` a `forecasting`
- `path` a `debug_log` popište cestu k projektu a místní soubor, do kterého budou zapsány informace o ladění. 
- `compute_target` je dříve definovaná `compute_target` , to znamená, že v tomto příkladu je levný počítač založený na procesoru. Pokud používáte zařízení s hloubkovým učením v AutoML, měli byste změnit cíl výpočetní služby na bázi GPU.
- `featurization` je nastaven na `auto` . Další podrobnosti najdete v části [Featurization dat](./how-to-configure-auto-train.md#data-featurization) v dokumentu konfigurace AUTOMATIZOVANÉho ml. 
- `label_column_name` Určuje, který sloupec má zajímat předpověď. 
- `training_data` je nastaven na `OutputTabularDatasetConfig` objekty vytvořené z výstupu kroku Příprava dat. 

`AutoMLStep`Sám o sobě použije `AutoMLConfig` a má jako výstupy `PipelineData` objekty vytvořené pro uchování metrik a data modelu. 

>[!Important]
> Je nutné nastavit `enable_default_model_output` a `enable_default_metrics_output`  na `True` pouze v případě, že používáte  `AutoMLStepRun` .

V tomto příkladu proces automatizovaného ML provede křížové ověřování v `training_data` . Můžete řídit počet křížových ověření s `n_cross_validations` argumentem. Pokud jste již data školení rozdělili jako součást kroků přípravy dat, můžete nastavit `validation_data` vlastní `Dataset` .

Občas se může zobrazit použití `X` datových funkcí a `y` popisků dat. Tato technika je zastaralá a měli byste ji použít `training_data` pro vstup. 

## <a name="register-the-model-generated-by-automated-ml"></a>Registrace modelu vygenerovaného pomocí automatizovaného ML 

Poslední krok v jednoduchém kanálu ML registruje vytvořený model. Přidáním modelu do registru modelu pracovního prostoru bude k dispozici na portálu a může být ve verzi. Pro registraci modelu zapište jiný `PythonScriptStep` , který přebírá `model_data` výstup `AutoMLStep` .

### <a name="write-the-code-to-register-the-model"></a>Zápis kódu pro registraci modelu

Model je zaregistrován v `Workspace` . Už jste obeznámeni s `Workspace.from_config()` tím, že používáte pro přihlášení ke svému pracovnímu prostoru na místním počítači, ale existuje jiný způsob, jak tento pracovní prostor získat z běžícího kanálu ml. `Run.get_context()`Načte aktivní `Run` . Tento `run` objekt poskytuje přístup k mnoha důležitým objektům, včetně `Workspace` zde používaného.

```python
%%writefile register_model.py
from azureml.core.model import Model, Dataset
from azureml.core.run import Run, _OfflineRun
from azureml.core import Workspace
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--model_name", required=True)
parser.add_argument("--model_path", required=True)
args = parser.parse_args()

print(f"model_name : {args.model_name}")
print(f"model_path: {args.model_path}")

run = Run.get_context()
ws = Workspace.from_config() if type(run) == _OfflineRun else run.experiment.workspace

model = Model.register(workspace=ws,
                       model_path=args.model_path,
                       model_name=args.model_name)

print("Registered version {0} of model {1}".format(model.version, model.name))
```

### <a name="write-the-pythonscriptstep-code"></a>Zápis kódu PythonScriptStep

Model – registrace `PythonScriptStep` používá `PipelineParameter` pro jeden z jejích argumentů. Parametry kanálu jsou argumenty pro kanály, které je možné snadno nastavit v době spuštění odeslání. Po deklarování jsou předány jako normální argumenty. 

```python

from azureml.pipeline.core.graph import PipelineParameter

# The model name with which to register the trained model in the workspace.
model_name = PipelineParameter("model_name", default_value="TitanicSurvivalInitial")

register_step = PythonScriptStep(script_name="register_model.py",
                                       name="register_model",
                                       allow_reuse=False,
                                       arguments=["--model_name", model_name, "--model_path", model_data],
                                       inputs=[model_data],
                                       compute_target=compute_target,
                                       runconfig=aml_run_config)
```

## <a name="create-and-run-your-automated-ml-pipeline"></a>Vytvoření a spuštění automatizovaného kanálu ML

Vytvoření a spuštění kanálu, který obsahuje, `AutoMLStep` se neliší od normálního kanálu. 

```python
from azureml.pipeline.core import Pipeline
from azureml.core import Experiment

pipeline = Pipeline(ws, [dataprep_step, train_step, register_step])

experiment = Experiment(workspace=ws, name='titanic_automl')

run = experiment.submit(pipeline, show_output=True)
run.wait_for_completion()
```

Výše uvedený kód kombinuje kroky pro přípravu dat, automatizované ML a model-registrování do `Pipeline` objektu. Pak vytvoří `Experiment` objekt. `Experiment`Konstruktor Získá pojmenovaný experiment, pokud existuje, nebo ho v případě potřeby vytvoří. Odešle `Pipeline` do a `Experiment` vytvoří `Run` objekt, který bude asynchronně spouštět kanál. `wait_for_completion()`Funkce blokuje až do dokončení běhu.

### <a name="examine-pipeline-results"></a>Kontrola výsledků kanálu 

Po `run` dokončení můžete načíst `PipelineData` objekty, které byly přiřazeny `pipeline_output_name` . Výsledky si můžete stáhnout a načíst pro další zpracování.  

```python
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

Stažené soubory jsou zapsány do podadresáře `azureml/{run.id}/` . Soubor metriky je ve formátu JSON a je možné ho převést na PANDAS dataframe pro kontrolu.

Pro místní zpracování možná budete muset nainstalovat relevantní balíčky, jako je PANDAS, rozevírací seznam, sada AzureML SDK a tak dále. V tomto příkladu je pravděpodobnější, že nejlepší model nalezený pomocí automatizovaného ML bude záviset na XGBoost.

```bash
!pip install xgboost==0.90
```

```python
import pandas as pd
import json

metrics_filename = metrics_output._path_on_datastore
# metrics_filename = path to downloaded file
with open(metrics_filename) as f:
   metrics_output_result = f.read()
   
deserialized_metrics_output = json.loads(metrics_output_result)
df = pd.DataFrame(deserialized_metrics_output)
df
```

Výše uvedený fragment kódu ukazuje soubor metrik načtený z jeho umístění v úložišti Azure DataStore. Můžete ho také načíst ze staženého souboru, jak je znázorněno v komentáři. Po deserializaci a jejím převedení na PANDAS dataframe uvidíte detailní metriky pro každou iteraci kroku automatizovaného ML.

Soubor modelu lze deserializovat do `Model` objektu, který lze použít pro Inferencing, další analýzy metrik a tak dále. 

```python
import pickle

model_filename = model_output._path_on_datastore
# model_filename = path to downloaded file

with open(model_filename, "rb" ) as f:
    best_model = pickle.load(f)

# ... inferencing code not shown ...
```

Další informace o načítání a práci se stávajícími modely najdete v tématu [použití existujícího modelu s Azure Machine Learning](how-to-deploy-existing-model.md).

### <a name="download-the-results-of-an-automated-ml-run"></a>Stažení výsledků automatizovaného běhu ML

Pokud jste spolu s článkem popracovali, budete mít instanci `run` objektu. Můžete ale také načíst dokončené `Run` objekty z rozhraní `Workspace` pomocí `Experiment` objektu.

Pracovní prostor obsahuje úplný záznam všech experimentů a spuštění. Portál můžete buď použít k vyhledání a stažení výstupů experimentů nebo použití kódu. Pokud chcete získat přístup k záznamům z historických běhů, pomocí Azure Machine Learning Najděte ID běhu, ve kterém se zajímáte. S tímto ID můžete zvolit konkrétní `run` metodou `Workspace` a `Experiment` .

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['titanic_automl']
run = next(run for run in ex.get_runs() if run.id == run_id)
```

Museli byste změnit řetězce ve výše uvedeném kódu na konkrétní z historických běhů. Výše uvedený fragment kódu předpokládá, že jste se přiřadili `ws` k relevantnímu `Workspace` s normálním `from_config()` . Experiment o zájmu je přímo načten a potom kód vyhledá v `Run` zájmu porovnání s `run.id` hodnotou.

Jakmile budete mít `Run` objekt, můžete si stáhnout metriky a model. 

```python
automl_run = next(r for r in run.get_children() if r.name == 'AutoML_Classification')
outputs = automl_run.get_outputs()
metrics = outputs['default_metrics_AutoML_Classification']
model = outputs['default_model_AutoML_Classification']

metrics.get_port_data_reference().download('.')
model.get_port_data_reference().download('.')
```

Každý `Run` objekt obsahuje `StepRun` objekty, které obsahují informace o spuštění jednotlivých kroků kanálu. `run`Je prohledán `StepRun` objekt pro `AutoMLStep` . Metriky a model jsou načteny pomocí jejich výchozích názvů, které jsou k dispozici, i když nepředáte `PipelineData` objekty do `outputs` parametru `AutoMLStep` . 

Nakonec se aktuální metriky a model stáhnou do místního počítače, jak bylo popsáno výše v části Kontrola výsledků kanálu.

## <a name="next-steps"></a>Další kroky

- Spusťte tento Jupyter Poznámkový blok, který ukazuje [kompletní příklad automatizovaného ml v kanálu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) , který používá regresi k předvídání taxislužby tarifů.
- [Vytváření automatizovaných experimentů ML bez psaní kódu](how-to-use-automated-ml-for-ml-models.md)
- Prozkoumejte celou řadu [poznámkových bloků Jupyter, které demonstrují automatizované ml](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) .
- Přečtěte si informace o integraci kanálu do aplikace až po koncovou [MLOps](./concept-model-management-and-deployment.md#automate-the-ml-lifecycle) nebo prozkoumejte [úložiště GitHub MLOps](https://github.com/Microsoft/MLOpspython) .

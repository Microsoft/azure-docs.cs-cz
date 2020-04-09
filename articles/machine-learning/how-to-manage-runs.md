---
title: Spuštění, monitorování a zrušení tréninkových běhů v Pythonu
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak začít, nastavit stav, označit a uspořádat experimenty strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 01/09/2020
ms.openlocfilehash: c1b70aaef49cc2b993c873509dc935d71069efa2
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985911"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Spuštění, monitorování a zrušení tréninkových běhů v Pythonu
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

[Sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), [CLI machine learningu](reference-azure-machine-learning-cli.md)a [studio Azure Machine Learning](https://ml.azure.com) poskytují různé metody pro monitorování, uspořádání a správu spuštění pro školení a experimentování.

Tento článek ukazuje příklady následujících úkolů:

* Sledování výkonu spuštění.
* Zrušit nebo nevyhovět spuštění.
* Vytvořit podřízené spuštění.
* Tag a najít běží.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat následující položky:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

* Pracovní [prostor Azure Machine Learning](how-to-manage-workspace.md).

* Sada Azure Machine Learning SDK pro Python (verze 1.0.21 nebo novější). Informace o instalaci nebo aktualizaci nejnovější verze sady SDK naleznete v tématu [Instalace nebo aktualizace sady SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Chcete-li zkontrolovat verzi sady Azure Machine Learning SDK, použijte následující kód:

    ```python
    print(azureml.core.VERSION)
    ```

* Rozšíření [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) a [CLI pro Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="start-a-run-and-its-logging-process"></a>Spuštění běhu a procesu protokolování

### <a name="using-the-sdk"></a>Použití sady SDK

Nastavte experiment importem tříd [Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)a [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) z balíčku [azureml.core.](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py)

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Spusťte spuštění a jeho [`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) proces protokolování s metodou.

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>Použití cli

Chcete-li zahájit spuštění experimentu, postupujte takto:

1. Z prostředí nebo příkazového řádku použijte azure cli k ověření na vaše předplatné Azure:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

1. Připojte konfiguraci pracovního prostoru ke složce, která obsahuje trénovací skript. Nahraďte `myworkspace` pracovní prostor Azure Machine Learning. Nahraďte `myresourcegroup` skupinou prostředků Azure, která obsahuje váš pracovní prostor:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Tento příkaz `.azureml` vytvoří podadresář, který obsahuje příklad runconfig a conda soubory prostředí. Obsahuje také `config.json` soubor, který se používá ke komunikaci s pracovním prostorem Azure Machine Learning.

    Další informace naleznete [v tématu az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. Chcete-li spustit, použijte následující příkaz. Při použití tohoto příkazu zadejte název souboru runconfig (text před \*.runconfig, pokud se díváte na systém souborů) proti parametru -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Příkaz `az ml folder attach` vytvořil `.azureml` podadresář, který obsahuje dva příklady runconfig souborů.
    >
    > Pokud máte skript Pythonu, který programově vytvoří objekt konfigurace, můžete jej uložit jako soubor runconfig pomocí [runconfig.save().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-)
    >
    > Další příklad runconfig soubory [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)naleznete v tématu .

    Další informace naleznete [v tématu az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

### <a name="using-azure-machine-learning-studio"></a>Použití Studia Azure Machine Learning

Chcete-li spustit odeslat kanál v návrháři (náhledu), použijte následující kroky:

1. Nastavte výchozí výpočetní cíl pro váš kanál.

1. V horní části plátna kanálu vyberte **Spustit.**

1. Vyberte experiment, chcete-li seskupit spuštění kanálu.

## <a name="monitor-the-status-of-a-run"></a>Sledování stavu běhu

### <a name="using-the-sdk"></a>Použití sady SDK

Získejte stav spuštění s [`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) metodou.

```python
print(notebook_run.get_status())
```

Chcete-li získat ID spuštění, čas spuštění a další [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) podrobnosti o spuštění, použijte metodu.

```python
print(notebook_run.get_details())
```

Po úspěšném dokončení spuštění použijte [`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) metodu k jeho označení jako dokončené.

```python
notebook_run.complete()
print(notebook_run.get_status())
```

Pokud použijete návrhový `with...as` vzor Pythonu, spuštění se automaticky označí jako dokončené, když je spuštění mimo rozsah. Není nutné ručně označit spuštění jako dokončené.

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>Použití cli

1. Chcete-li zobrazit seznam spuštění experimentu, použijte následující příkaz. Nahraďte `experiment` se názvem experimentu:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    Tento příkaz vrátí dokument JSON, který obsahuje informace o spuštění chodů pro tento experiment.

    Další informace naleznete [v seznamu experimentů az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Chcete-li zobrazit informace o konkrétním spuštění, použijte následující příkaz. Nahraďte `runid` id běhu:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    Tento příkaz vrátí dokument JSON, který obsahuje informace o spuštění.

    Další informace naleznete [v tématu az ml run show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).


### <a name="using-azure-machine-learning-studio"></a>Použití Studia Azure Machine Learning

Zobrazení počtu aktivních běhů experimentu ve studiu.

1. Přejděte do části **Experimenty.** 

1. Vyberte experiment.

    Na stránce experimentu uvidíte počet aktivních výpočetních cílů a dobu trvání každého spuštění. 

1. Vyberte konkrétní číslo spuštění.

1. Na kartě **Protokoly** můžete najít diagnostické a chybové protokoly pro spuštění kanálu.


## <a name="cancel-or-fail-runs"></a>Zrušit nebo nevyhovět spuštění

Pokud si všimnete chyby nebo pokud spuštění trvá příliš dlouho, můžete spuštění zrušit.

### <a name="using-the-sdk"></a>Použití sady SDK

Chcete-li zrušit spuštění pomocí sady [`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) SDK, použijte metodu:

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

Pokud spuštění skončí, ale obsahuje chybu (například byl použit nesprávný trénovací skript), můžete použít metodu [`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) k jeho označení jako neúspěšné.

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>Použití cli

Chcete-li zrušit spuštění pomocí příkazového příkazu příkazu, použijte následující příkaz. Nahradit `runid` ID běhu

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Další informace naleznete [v tématu az ml run cancel](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

### <a name="using-azure-machine-learning-studio"></a>Použití Studia Azure Machine Learning

Chcete-li zrušit spuštění ve studiu, postupujte takto:

1. Přejděte do spuštěného kanálu v části **Experimenty** nebo **Kanály.** 

1. Vyberte číslo spuštění kanálu, které chcete zrušit.

1. Na panelu nástrojů vyberte **Storno.**


## <a name="create-child-runs"></a>Vytvořit podřízené spuštění

Vytvořit podřízené spustí seskupit související spuštění, například pro různé iterací hyperparameter tuning.

> [!NOTE]
> Podřízené spuštění lze vytvořit pouze pomocí sady SDK.

Tento příklad kódu `hello_with_children.py` používá skript k vytvoření dávky pěti podřízených [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) spuštění z v rámci odeslaného spuštění pomocí metody:

```python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Při přesunu mimo rozsah jsou podřízené spuštění automaticky označeny jako dokončené.

Chcete-li vytvořit mnoho podřízených spustí efektivně, použijte metodu. [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) Vzhledem k tomu, že každé vytvoření vede k síťovému volání, je vytvoření dávky spuštění efektivnější než jejich vytvoření jeden po druhém.

### <a name="submit-child-runs"></a>Odeslat podřízené běhy

Podřízené spuštění lze také odeslat z nadřazeného spuštění. To umožňuje vytvářet hierarchie nadřazených a podřízených spuštění. 

Můžete chtít, aby vaše dítě běží použít jinou konfiguraci spuštění než nadřazené spuštění. Můžete například použít méně výkonnou konfiguraci založenou na procesoru pro nadřazenou konfiguraci, zatímco používáte konfigurace založené na GPU pro vaše děti. Další společnou touhou je předat každému dítěti různé argumenty a data. Chcete-li přizpůsobit podřízené `RunConfiguration` spuštění, předavěji objekt konstruktoru dítěte. `ScriptRunConfig` Tento příklad kódu, který by `ScriptRunConfig` byl součástí skriptu nadřazeného objektu:

- Vytvoří `RunConfiguration` načtení pojmenovaného výpočetního prostředku.`"gpu-compute"`
- Iterates přes různé hodnoty argumentů, `ScriptRunConfig` které mají být předány podřízené objekty
- Vytvoří a odešle nový podřízený běh pomocí vlastního výpočetního prostředku a argumentu.
- Blokuje, dokud se nedokončí všechny podřízené

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig, RunConfiguration

run_config_for_aml_compute = RunConfiguration()
run_config_for_aml_compute.target = "gpu-compute"
run_config_for_aml_compute.environment.docker.enabled = True 

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], run_config = run_config_for_aml_compute)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

Chcete-li efektivně vytvořit mnoho podřízených spuštění s identickými konfiguracemi, argumenty a vstupy, použijte metodu. [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) Vzhledem k tomu, že každé vytvoření vede k síťovému volání, je vytvoření dávky spuštění efektivnější než jejich vytvoření jeden po druhém.

V rámci podřízeného spuštění můžete zobrazit id nadřazeného spuštění:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Dotaz podřízené spustí

Chcete-li dotaz podřízené spuštění konkrétní [`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) nadřazené, použijte metodu. Argument ``recursive = True`` umožňuje dotaz na vnořený strom dětí a vnoučat.

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Označit a najít běží

V Azure Machine Learning můžete použít vlastnosti a značky k uspořádání a dotazování spuštění pro důležité informace.

### <a name="add-properties-and-tags"></a>Přidání vlastností a značek

#### <a name="using-the-sdk"></a>Použití sady SDK

Chcete-li do spuštění přidat prohledávatelná metadata, použijte metodu. [`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) Například následující kód přidá `"author"` vlastnost spustit:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Vlastnosti jsou neměnné, takže vytvářejí trvalý záznam pro účely auditování. Následující příklad kódu má za následek chybu, protože jsme již přidali `"azureml-user"` jako hodnotu `"author"` vlastnosti v předchozím kódu:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

Na rozdíl od vlastností jsou tagy proměnlivé. Chcete-li přidat prohledávatelné a smysluplné [`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) informace pro spotřebitele experimentu, použijte metodu.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Můžete také přidat jednoduché značky řetězců. Když se tyto značky zobrazí ve slovníku značek `None`jako klíče, mají hodnotu .

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>Použití cli

> [!NOTE]
> Pomocí cli můžete přidávat nebo aktualizovat pouze značky.

Chcete-li přidat nebo aktualizovat značku, použijte následující příkaz:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

Další informace naleznete [v tématu az ml run update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).

### <a name="query-properties-and-tags"></a>Vlastnosti a značky dotazu

Můžete dotaz běží v rámci experimentu vrátit seznam spustí, které odpovídají určité vlastnosti a značky.

#### <a name="using-the-sdk"></a>Použití sady SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>Použití cli

Rozhraní příkazového příkazu Azure podporuje dotazy [JMESPath,](http://jmespath.org) které lze použít k filtrování spuštění na základě vlastností a značek. Chcete-li použít dotaz JMESPath s rozhraním `--query` příkazového řádku Azure, zadejte jej pomocí parametru. Následující příklady ukazují základní dotazy pomocí vlastností a značek:

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Další informace o dotazování výsledků azure cli najdete [v tématu dotaz na výstup příkazu Azure CLI](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).

### <a name="using-azure-machine-learning-studio"></a>Použití Studia Azure Machine Learning

1. Přejděte do části **Kanály.**

1. Pomocí vyhledávacího panelu můžete filtrovat kanály pomocí značek, popisů, názvů experimentů a názvu předkladatele.

## <a name="example-notebooks"></a>Ukázkové poznámkové bloky

Následující poznámkové bloky ukazují koncepty v tomto článku:

* Další informace o rozhraních API protokolování najdete v [poznámkovém bloku rozhraní API pro protokolování](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* Další informace o správě spuštění pomocí sady Azure Machine Learning SDK najdete v tématu [správa spustí poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak protokolovat metriky pro experimenty, najdete [v tématu Protokolmetriky během školení spustí](how-to-track-experiments.md).
* Informace o tom, jak monitorovat prostředky a protokoly z Azure Machine Learning, najdete [v tématu monitorování Azure Machine Learning](monitor-azure-machine-learning.md).
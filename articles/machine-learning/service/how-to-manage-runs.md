---
title: Spuštění, monitorování a zrušení školicích běhů v Pythonu
titleSuffix: Azure Machine Learning service
description: Naučte se, jak začít, nastavit stav, označit a uspořádat experimenty pro strojové učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2019
ms.openlocfilehash: 9b58d6e189c891d0dd2917d7d150f133dc35f917
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019100"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Spuštění, monitorování a zrušení školicích běhů v Pythonu

[Sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) a [Machine Learning CLI](reference-azure-machine-learning-cli.md) nabízí různé metody pro monitorování, uspořádání a správu vašich běhů pro školení a experimentování.

Tento článek ukazuje příklady následujících úloh:

* Monitorujte výkon spouštění.
* Zrušení nebo selhání spuštění.
* Vytvoření podřízených spuštění.
* Označení a hledání spuštění.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat následující položky:

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze služby Azure Machine Learning](https://aka.ms/AMLFree) dnes

* [Pracovní prostor služby Azure Machine Learning](how-to-manage-workspace.md).

* Sada SDK Azure Machine Learning pro Python (verze 1.0.21 nebo novější). Chcete-li nainstalovat nebo aktualizovat nejnovější verzi sady SDK, přečtěte si téma [instalace nebo aktualizace sady SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Chcete-li zjistit verzi sady Azure Machine Learning SDK, použijte následující kód:

    ```python
    print(azureml.core.VERSION)
    ```

* Rozšíření [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) a rozhraní příkazového [řádku pro službu Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="start-a-run-and-its-logging-process"></a>Spuštění běhu a jeho procesu protokolování

### <a name="using-the-sdk"></a>Použití sady SDK

Nastavte experiment tak, že naimportujete třídy [pracovní prostor](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)a [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) z balíčku [AzureML. Core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) .

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Spusťte běh a jeho proces protokolování s [`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) metodou.

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>Použití rozhraní příkazového řádku

Pro spuštění experimentu použijte následující postup:

1. V prostředí nebo příkazovém řádku můžete pomocí Azure CLI ověřit předplatné Azure:

    ```azurecli-interactive
    az login
    ```

1. Připojte konfiguraci pracovního prostoru ke složce, která obsahuje váš školicí skript. Nahraďte `myworkspace` pracovním prostorem služby Azure Machine Learning. Nahraďte `myresourcegroup` skupinou prostředků Azure, která obsahuje váš pracovní prostor:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Tento příkaz vytvoří `.azureml` podadresář, který obsahuje příklady souborů prostředí RunConfig a conda. Obsahuje `config.json` taky soubor, který se používá ke komunikaci s vaším pracovním prostorem Azure Machine Learning.

    Další informace najdete v tématu [AZ ml složka připojit](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. Chcete-li spustit spuštění, použijte následující příkaz. Při použití tohoto příkazu zadejte název souboru RunConfig (text před \*. RunConfig, pokud hledáte v systému souborů) s parametrem-c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` Příkaz`.azureml` vytvořil podadresář, který obsahuje dva příklady souborů RunConfig.
    >
    > Pokud máte skript Pythonu, který vytvoří objekt konfigurace spuštění programově, můžete použít [RunConfig. Save ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) a uložit ho jako soubor RunConfig.
    >
    > Další příklady souborů RunConfig naleznete v tématu [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml).

    Další informace najdete v tématu [AZ ml Run odeslání-Script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

## <a name="monitor-the-status-of-a-run"></a>Monitoruje stav spuštění.

### <a name="using-the-sdk"></a>Použití sady SDK

Získejte stav spuštění s [`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) metodou.

```python
print(notebook_run.get_status())
```

Chcete-li získat ID spuštění, dobu provádění a další podrobnosti o spuštění, použijte [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) metodu.

```python
print(notebook_run.get_details())
```

Po úspěšném dokončení spuštění použijte [`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) metodu k označení jako dokončenou.

```python
notebook_run.complete()
print(notebook_run.get_status())
```

Použijete-li vzor `with...as` návrhu Pythonu, bude spuštění při nedostatku rozsahu automaticky označen jako dokončené. Nemusíte ručně označit rutinu Run jako dokončenou.

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>Použití rozhraní příkazového řádku

1. Chcete-li zobrazit seznam spuštění experimentu, použijte následující příkaz. Nahraďte `experiment` názvem experimentu:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    Tento příkaz vrátí dokument JSON se seznamem informací o běhu pro tento experiment.

    Další informace najdete v tématu [AZ ml experiment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Chcete-li zobrazit informace o konkrétním spuštění, použijte následující příkaz. Nahraďte `runid` ID běhu:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    Tento příkaz vrátí dokument JSON se seznamem informací o běhu.

    Další informace najdete v tématu [AZ ml Run show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).

## <a name="cancel-or-fail-runs"></a>Zrušení nebo selhání spuštění

Pokud si všimnete omylem nebo pokud dokončení běhu trvá příliš dlouho, můžete spustit operaci.

### <a name="using-the-sdk"></a>Použití sady SDK

Chcete-li zrušit běh pomocí sady SDK, použijte [`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) metodu:

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

Pokud se vaše spuštění dokončí, ale obsahuje chybu (například byl použit nesprávný školicí skript), můžete použít [`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) metodu k jejímu označení jako neúspěšné.

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>Použití rozhraní příkazového řádku

Chcete-li zrušit běh pomocí rozhraní příkazového řádku, použijte následující příkaz. Nahraďte `runid` ID běhu.

```azurecli-interactive
az ml run cancel -r runid
```

Další informace najdete v tématu [AZ ml Run Cancel](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

## <a name="create-child-runs"></a>Vytvořit podřízená spuštění

Vytvoření podřízených běhů pro seskupení souvisejících běhů, například pro různé iterace ladění parametrů.

> [!NOTE]
> Podřízená spuštění lze vytvořit pouze pomocí sady SDK.

Tento příklad kódu používá `hello_with_children.py` skript k vytvoření dávky pěti podřízených spuštění v rámci odeslaného běhu [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) pomocí metody:

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
> Při přesunu z oboru jsou podřízená spuštění automaticky označena jako dokončená.

Chcete-li vytvořit mnoho podřízených spuštění efektivně [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) , použijte metodu. Vzhledem k tomu, že při každém vytváření dojde k síťovému volání, vytvoření dávky spuštění je efektivnější než jejich vytvoření po jednom.

### <a name="submit-child-runs"></a>Odeslat podřízená spuštění

Podřízené běhy lze také odeslat z nadřazeného spuštění. To vám umožňuje vytvářet hierarchie nadřazených a podřízených spuštění, z nichž každý běží v různých cílových výpočetních prostředích, které jsou propojeny běžným ID nadřazeného spuštění.

Použijte metodu [submit_child ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#submit-child-count-none--tag-key-none--tag-values-none-) pro odeslání podřízeného spuštění v rámci nadřazeného spuštění. Pokud to chcete provést v nadřazeném skriptu spuštění, Získejte kontext spuštění a odešlete podřízený běh pomocí metody submit_child instance kontextu.

```python
## In parent run script
parent_run = Run.get_context()
child_run_config = ScriptRunConfig(source_directory='.', script='child_script.py')
parent_run.submit_child(child_run_config)
```

V rámci podřízeného spuštění můžete zobrazit ID nadřazeného spuštění:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Dotaz na podřízená spuštění

Chcete-li zadat dotaz na podřízená spuštění konkrétního nadřazeného [`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) objektu, použijte metodu. Argument ' ' rekurzivní = true ' ' umožňuje zadat dotaz na vnořený strom podřízených a podřízené.

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Označení a hledání spuštění

Ve službě Azure Machine Learning můžete pomocí vlastností a značek lépe organizovat a dotazovat vaše běhy na důležité informace.

### <a name="add-properties-and-tags"></a>Přidat vlastnosti a značky

#### <a name="using-the-sdk"></a>Použití sady SDK

Chcete-li přidat hledaná metadata k vašim běhům, [`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) použijte metodu. Například následující kód přidá `"author"` vlastnost do běhu:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Vlastnosti jsou neměnné, takže vytvoří trvalý záznam pro účely auditování. Následující příklad kódu má za následek chybu, protože jsme už přidali `"azureml-user"` `"author"` jako hodnotu vlastnosti v předchozím kódu:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

Na rozdíl od vlastností jsou značky proměnlivé. K přidání prohledávatelných a smysluplných informací pro uživatele experimentu použijte [`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) metodu.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Můžete také přidat jednoduché řetězcové značky. Když se tyto značky objeví ve slovníku značek jako klíče, mají hodnotu `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>Použití rozhraní příkazového řádku

> [!NOTE]
> Pomocí rozhraní příkazového řádku můžete přidat nebo aktualizovat pouze značky.

Chcete-li přidat nebo aktualizovat značku, použijte následující příkaz:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

Další informace najdete v tématu [AZ ml Run Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).

### <a name="query-properties-and-tags"></a>Vlastnosti a značky dotazu

Dotazování můžete spustit v experimentu a vrátit seznam spuštění, který odpovídá specifickým vlastnostem a značkám.

#### <a name="using-the-sdk"></a>Použití sady SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>Použití rozhraní příkazového řádku

Rozhraní příkazového řádku Azure podporuje dotazy [JMESPath](http://jmespath.org) , které lze použít k filtrování spuštění na základě vlastností a značek. Pokud chcete použít dotaz JMESPath s rozhraním příkazového řádku Azure CLI, `--query` zadejte ho s parametrem. Následující příklady znázorňují základní dotazy pomocí vlastností a značek:

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Další informace o dotazování na výsledky rozhraní příkazového řádku Azure najdete v tématu [dotazování výstupu příkazu Azure CLI](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).

## <a name="example-notebooks"></a>Příklad poznámkové bloky

Následující poznámkové bloky ukazují koncepty v tomto článku:

* Další informace o rozhraních API pro protokolování najdete v [poznámkovém bloku rozhraní API pro protokolování](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Další informace o správě běžících v sadě Azure Machine Learning SDK najdete v poznámkovém [bloku spravovat běhy](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Další postup

* Informace o tom, jak zaznamenat metriky pro vaše experimenty, najdete v tématu [metriky protokolu během školicích běhů](how-to-track-experiments.md).

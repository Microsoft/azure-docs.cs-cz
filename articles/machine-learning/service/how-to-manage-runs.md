---
title: Spustit, sledovat a zrušit tréninkových spuštění v Pythonu
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak pro spouštění, nastavit stav značky a organizaci své experimenty strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: a93492b8ea97500fe3c761f3ac0c49f8c1342d09
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074963"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Spustit, sledovat a zrušit tréninkových spuštění v Pythonu

[Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) a [Machine Learning CLI](reference-azure-machine-learning-cli.md) poskytují různé metody pro monitorování, organizovat a spravovat vaše spuštění pro trénování a experimentování.

Tento článek ukazuje příklady z následujících úloh:

* Monitorování spuštění výkonu.
* Zrušení nebo selhání spuštění.
* Vytváření podřízených spuštění.
* Označit a najít spuštění.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat tyto položky:

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

* [Pracovního prostoru služby Azure Machine Learning](setup-create-workspace.md).

* Sady SDK Azure Machine Learning pro Python (verze 1.0.21 nebo novější). Pro instalaci nebo aktualizaci na nejnovější verzi sady SDK, naleznete v tématu [nainstalovat nebo aktualizovat sadu SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Pokud chcete zkontrolovat vaši verzi sady Azure Machine Learning SDK, použijte následující kód:

    ```Python
    print(azureml.core.VERSION)
    ```

* [Rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) a [rozšíření rozhraní příkazového řádku pro službu Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="start-a-run-and-its-logging-process"></a>Spustit běh a jeho proces protokolování

### <a name="using-the-sdk"></a>Použití sady SDK

Nastavit experimentu pomocí importu [pracovní prostor](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [experimentovat](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [spustit](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py), a [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) tříd z [azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) balíčku.

```Python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Spustit běh a jeho zpracování protokolování pomocí [ `start_logging()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) metody.

```Python
notebook_run = exp.start_logging()

notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>Pomocí rozhraní příkazového řádku

Pokud chcete začít spuštění experimentu, postupujte následovně:

1. K ověření ke svému předplatnému Azure z prostředí nebo příkazového řádku, použijte rozhraní příkazového řádku Azure:

    ```azurecli-interactive
    az login
    ```

1. Konfigurace pracovního prostoru připojte ke složce, která obsahuje cvičný skript. Nahraďte `myworkspace` s pracovním prostorem služby Azure Machine Learning. Nahraďte `myresourcegroup` s vybranou skupinou prostředků Azure, která obsahuje váš pracovní prostor:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Tento příkaz vytvoří `.azureml` podadresář, který obsahuje soubory příklad runconfig a conda prostředí. Obsahuje taky `config.json` soubor, který se používá ke komunikaci s pracovního prostoru Azure Machine Learning.

    Další informace najdete v tématu [az ml složky připojit](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. Pro spuštění testu, použijte následující příkaz. Při použití tohoto příkazu, zadejte název souboru runconfig (text před \*.runconfig, pokud chcete v systému souborů) pro parametr - c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` Příkaz vytvoří `.azureml` podadresář, který obsahuje dva soubory runconfig příklad. 
    >
    > Pokud máte skript v jazyce Python, která vytvoří objekt konfigurace spuštění prostřednictvím kódu programu, můžete použít [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) ji uložit jako soubor runconfig.
    >
    > Další příklad runconfig soubory, naleznete v tématu [ https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml ](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml).

    Další informace najdete v tématu [az ml spuštění odeslat skript](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

## <a name="monitor-the-status-of-a-run"></a>Monitorovat stav spuštění

### <a name="using-the-sdk"></a>Použití sady SDK

Získání stavu spuštění s [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) metody.

```Python
print(notebook_run.get_status())
```

Chcete-li získat další podrobnosti o daném spuštění, použijte [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) metody.

```Python
notebook_run.get_details()
```

Při spuštění dokončí úspěšně, použijte [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) metoda jej označte jako dokončené.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Pokud používáte Python `with...as` vzor, spustit automaticky označí sama jako dokončené při spuštění je mimo rozsah. Není nutné ručně označit spustit jako dokončené.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

### <a name="using-the-cli"></a>Pomocí rozhraní příkazového řádku

1. Chcete-li zobrazit seznam spuštění experimentu, použijte následující příkaz. Nahraďte `experiment` s názvem svého experimentu:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    Tento příkaz vrátí dokument JSON s informacemi o běhy pro tento experiment.

    Další informace najdete v tématu [az ml experimentovat seznamu](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Chcete-li zobrazit informace na určité spuštění, použijte následující příkaz. Nahraďte `runid` s ID spuštění:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    Tento příkaz vrátí dokument JSON s informacemi o spuštění.

    Další informace najdete v tématu [az ml spuštění zobrazit](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).

## <a name="cancel-or-fail-runs"></a>Zrušení nebo selhání spuštění

Pokud zjistíte chybu nebo spuštění trvá moc dlouho na dokončení, můžete zrušit jeho spuštění.

### <a name="using-the-sdk"></a>Použití sady SDK

Chcete-li zrušit spuštění pomocí sady SDK, použijte [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) metody:

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Pokud se spuštění dokončí, ale obsahuje chybu (například nesprávné cvičný skript byl použit), můžete použít [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) metoda jej označte jako neúspěšný.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>Pomocí rozhraní příkazového řádku

Pokud chcete zrušit běh pomocí rozhraní příkazového řádku, použijte následující příkaz. Nahraďte `runid` s ID spuštění

```azurecli-interactive
az ml run cancel -r runid
```

Další informace najdete v tématu [az ml spuštění zrušit](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

## <a name="create-child-runs"></a>Vytvořit podřízený běhy

Vytváření podřízených spuštění seskupit související spuštění, například pro různé iterace hyperparametrů.

> [!NOTE]
> Spustí podřízený lze vytvořit pouze pomocí sady SDK.

Tento příklad kódu používá `hello_with_children.py` skript k vytvoření dávky pět podřízených spuštění z v rámci odeslal běh s použitím [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) metody:

```Python
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
> Při přechodu mimo rozsah, jsou podřízené spustí automaticky označen jako dokončený.

Můžete také spustit podřízené spuštění jednu po druhé, ale protože každý vytváření výsledkem je volání sítě, je méně efektivní než dávkové spuštění.

Dotaz podřízené výskytů určité nadřazené, použijte [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) metody.

```Python
list(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Označit a najít spuštění

Ve službě Azure Machine Learning můžete použít vlastností a značek k uspořádání a dotazování spuštěních důležité informace.

### <a name="add-properties-and-tags"></a>Přidání vlastností a značek

#### <a name="using-the-sdk"></a>Použití sady SDK

K přidání metadaty s možností prohledávání do spuštění, použijte [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) metody. Například následující kód přidá `"author"` vlastnost spustit:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Vlastnosti jsou neměnné, takže uživatel vytvořit záznam trvalé pro účely auditování. Následující kód příklad způsobí chybu, protože už přidali `"azureml-user"` jako `"author"` hodnotu vlastnosti v předchozím kódu:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

Klíčová slova jsou na rozdíl od vlastnosti, změnit. Přidat prohledávatelný a smysluplné informace pro zákazníky experimentu, použijte [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) metody.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Můžete také přidat značky jednoduchým řetězcem. Pokud tyto značky se zobrazí ve slovníku značky, mají hodnotu `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>Pomocí rozhraní příkazového řádku

> [!NOTE]
> Pomocí rozhraní příkazového řádku, můžete pouze přidat nebo aktualizovat značky.

Chcete-li přidat nebo aktualizovat značku, použijte následující příkaz:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

Další informace najdete v tématu [az ml spuštění aktualizace](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).

### <a name="query-properties-and-tags"></a>Dotazy na vlastnosti a značky

Můžete dát dotaz na spuštění v rámci experimentu se seznam běhů, které odpovídají konkrétním vlastnostem a značky.

#### <a name="using-the-sdk"></a>Použití sady SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>Pomocí rozhraní příkazového řádku

Azure CLI podporuje [JMESPath](http://jmespath.org) dotazy, které je možné použít k filtrování testy podle vlastností a značek. Použití dotazů JMESPath pomocí Azure CLI, zadejte ho `--query` parametru. Následující příklady ukazují základní dotazy pomocí vlastnosti a značky:

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Další informace o dotazování výsledků rozhraní příkazového řádku Azure najdete v tématu [výstup příkazu rozhraní příkazového řádku Azure dotazu](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).

## <a name="example-notebooks"></a>Příklad poznámkové bloky

Tyto poznámkové bloky předvedení konceptů v tomto článku:

* Další informace o protokolování rozhraní API, najdete v článku [Poznámkový blok protokolování rozhraní API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Další informace o správě spuštěna se sadou SDK Azure Machine Learning, najdete v článku [spravovat Poznámkový blok spustí](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Další postup

* Zjistěte, jak protokolovat metriky pro vaše experimenty, najdete v článku [přihlásit metriky průběhu tréninková spuštění](how-to-track-experiments.md).

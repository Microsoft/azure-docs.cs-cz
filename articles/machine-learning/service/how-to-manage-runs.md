---
title: Spustit, sledovat a zrušit tréninkových spuštění v Pythonu
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak začít, stav, značky a uspořádání vašich se strojovým učením.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 4/5/2019
ms.openlocfilehash: 726273024a2da0cea5207c86140f3c31263a208f
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426689"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Spustit, sledovat a zrušit tréninkových spuštění v Pythonu

[Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) poskytuje různé metody k monitorování, uspořádání a správě vašich spuštění pro trénování a experimentování.

Tento návod ukazuje příklady z následujících úloh:

* [Monitorování spuštění výkonu](#monitor)
* [Zrušení nebo selhání spuštění](#cancel)
* [Vytvořit podřízený běhy](#children)
* [Označit a najít spuštění](#tag)

## <a name="prerequisites"></a>Požadavky

Budete potřebovat následující:

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

* Pracovní prostor služby Azure Machine Learning. Zobrazit [vytvořit pracovní prostor služby Azure Machine Learning](setup-create-workspace.md).

* Machine Learning Azure SDK for Python nainstalován (verze 1.0.21 nebo novější). Pro instalaci nebo aktualizaci na nejnovější verzi sady SDK, přejděte [nainstalovat nebo aktualizovat sadu SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) stránky.

    Pokud chcete zkontrolovat vaši verzi sady Azure Machine Learning SDK, použijte následující kód.

    ```Python
    print(azureml.core.VERSION)
    ```

<a name="monitor"></a>

## <a name="start-and-status-a-run"></a>Spuštění a stav spuštění

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

Získání stavu spuštění s [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--).

```Python
print(notebook_run.get_status())
```

Chcete-li získat další podrobnosti o spuštění pomocí [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--).

```Python
notebook_run.get_details()
```

Při spuštění dokončí úspěšně, použijte [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) metoda jej označte jako dokončené.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Můžete také použít Python `with...as` vzor. V tomto kontextu spuštění automaticky označí sama jako dokončený při spuštění je mimo rozsah. Tímto způsobem není nutné ručně označit spustit jako dokončené.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

<a name="cancel"></a>

## <a name="cancel-or-fail-runs"></a>Zrušení nebo selhání spuštění

 Pokud zjistíte chybu nebo spuštění zdá se, že to trvá dlouho dokončení, použijte [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) metoda zastavení spuštění před dokončením a označte ji jako zrušená.

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Pokud se spuštění dokončí, ale obsahuje chyba typu, byl použit nesprávný cvičný skript, můžete použít [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) metoda jej označte jako neúspěšný.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

<a name="children"></a>

## <a name="create-child-runs"></a>Vytvořit podřízený běhy

Vytváření podřízených spuštění k seskupení souvisejících spuštění, jako je pro různé hyperparameter ladění iterací.

Tento příklad kódu používá hello_with_children.py skript k vytvoření dávky pět podřízených spuštění z v rámci zadané spuštění pomocí [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) metody.

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
> Podřízená úloha poběží dokončení automaticky při přechodu mimo rozsah.

Můžete také spustit podřízené spuštění jednu po druhé, ale protože každý vytváření výsledkem je volání sítě, je méně efektivní než dávkové spuštění.

Použití [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) spuštěním metody k dotazování podřízené konkrétní nadřazeného objektu.

```Python
list(parent_run.get_children())
```

<a name="tag"></a>

## <a name="tag-and-find-runs"></a>Označit a najít spuštění

Ve službě Azure Machine Learning můžete použít vlastností a značek k uspořádání a dotazování spuštěních důležité informace.

### <a name="add-properties-and-tags"></a>Přidání vlastností a značek

Použití [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) doplnit spuštěních metadaty s možností prohledávání. Například následující kód přidá vlastnost "Autor" spustit.

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Vlastnosti jsou neměnné, což je užitečné jako trvalé záznam pro účely auditování. Následující příklad kódu způsobí chybu, protože už jsme přidali "azureml – user" vlastnosti "Autor" v předchozím kódu.

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

Značky, ale jsou změnit. Použití [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) přidat prohledávatelný a smysluplné informace pro zákazníky experimentu.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

Můžete také přidat značku jednoduchým řetězcem. Zobrazí se ve slovníku značky s hodnotou `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>Dotazy na vlastnosti a značky

Můžete dát dotaz na spuštění v rámci experimentu, které odpovídají konkrétním vlastnostem a značky.

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>Příklad poznámkové bloky

Tyto poznámkové bloky předvedení konceptů v tomto článku:

* Další informace o protokolování rozhraní API, najdete v článku [Poznámkový blok protokolování rozhraní API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Další informace o správě spuštěna se sadou SDK Azure Machine Learning, najdete v článku [spravovat Poznámkový blok spustí](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Další postup

* Zjistěte, jak protokolovat metriky pro vaše experimenty, najdete v článku [přihlásit metriky průběhu tréninková spuštění](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiment) článku.
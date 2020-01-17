---
title: Ladění a řešení potíží s ParallelRunStep
titleSuffix: Azure Machine Learning
description: Ladění a řešení potíží s ParallelRunStep v kanálech strojového učení v sadě Azure Machine Learning SDK pro Python. Seznamte se s běžnými nástrahy pro vývoj s kanály a tipů, které vám pomůžou ladit skripty před a během vzdáleného spouštění.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122961"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Ladění a řešení potíží s ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak ladit a řešit potíže se třídou [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) z [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Místní testování skriptů

Přečtěte si [část testování skriptů místně](how-to-debug-pipelines.md#testing-scripts-locally) pro kanály strojového učení. Vaše ParallelRunStep se spouští jako krok v kanálech ML, takže stejná odpověď platí i pro obě.

## <a name="debugging-scripts-from-remote-context"></a>Ladění skriptů ze vzdáleného kontextu

Přechod z ladění skriptu bodování místně na ladění vyhodnocovacího skriptu ve skutečném kanálu může být obtížné. Informace o hledání protokolů na portálu najdete v [části kanály strojového učení na webu ladění skriptů ze vzdáleného kontextu](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Informace v této části se vztahují také na běh v paralelním kroku.

Například soubor protokolu `70_driver_log.txt` obsahuje informace z kontroleru, který spouští kód paralelního spuštění.

Z důvodu distribuované povahy úloh paralelního spuštění jsou protokoly z několika různých zdrojů. Vytvoří se ale dva konsolidované soubory, které poskytují informace vysoké úrovně:

- `~/logs/overview.txt`: Tento soubor poskytuje informace vysoké úrovně o počtu Mini dávek (označovaných také jako úlohy), které byly doposud vytvořeny a počtu mininích dávek zpracovaných zatím. Na tomto konci se zobrazuje výsledek úlohy. Pokud se úloha nezdařila, zobrazí se chybová zpráva a kde začít odstraňování potíží.

- `~/logs/sys/master.txt`: Tento soubor poskytuje hlavní uzel (také označovaný jako Orchestrator) zobrazení spuštěné úlohy. Zahrnuje vytvoření úlohy, monitorování průběhu, výsledek spuštění.

Protokoly generované ze vstupního skriptu pomocí EntryScript. protokolovacího nástroje a příkazy Print budou nalezeny v následujících souborech:

- `~/logs/user/<ip_address>/Process-*.txt`: Tento soubor obsahuje protokoly napsané z entry_script pomocí EntryScript. protokolovacího nástroje. Obsahuje také příkaz Print (stdout) z entry_script.

Pokud potřebujete úplný přehled o tom, jak každý uzel spustil skript skóre, podívejte se na jednotlivé protokoly procesu pro každý uzel. Protokoly procesu lze nalézt ve složce `sys/worker` seskupené podle uzlů pracovních procesů:

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: Tento soubor poskytuje podrobné informace o každé Mini dávce, jak si je pracovník vybral nebo dokončí. Pro každou miniskou dávku tento soubor obsahuje:

    - IP adresa a PID pracovního procesu. 
    - Celkový počet položek, počet úspěšně zpracovaných položek a počet neúspěšných položek.
    - Čas spuštění, doba trvání, doba zpracování a metoda spuštění.

Můžete také vyhledat informace o využití prostředků pro jednotlivé pracovní procesy. Tyto informace jsou ve formátu CSV a jsou umístěné na `~/logs/sys/perf/<ip_address>/`. V případě jednoho uzlu budou soubory úlohy k dispozici v části `~logs/sys/perf`. Například při kontrole využití prostředků se podívejte na následující soubory:

- `Process-*.csv`: využití prostředků v rámci pracovního procesu. 
- `sys.csv`: protokol na uzel.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Návody se protokolovat z uživatelského skriptu ze vzdáleného kontextu?
Můžete získat protokolovací nástroj z EntryScript, jak je znázorněno v následujícím ukázkovém kódu, aby se protokoly zobrazovaly ve složce **logs/uživatel** na portálu.

**Ukázkový vstupní skript s použitím protokolovacího nástroje:**
```python
from entry_script import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Jak můžu předat vstup ze strany, například soubor nebo soubory obsahující vyhledávací tabulku, do všech mých pracovníků?

Sestavte objekt [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) obsahující vstup na straně a proveďte registraci v pracovním prostoru. Poté, co k němu máte přístup ve skriptu pro odvození (například v metodě Init ()) následujícím způsobem:

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>Další kroky

* Nápovědu k balíčku [AzureML-contrib-Pipeline-Step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) a [dokumentaci](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) ke třídě ParallelRunStep najdete v referenčních informacích k sadě SDK.

* Postupujte podle pokynů v [rozšířeném kurzu](tutorial-pipeline-batch-scoring-classification.md) použití kanálů pomocí paralelního spuštění.

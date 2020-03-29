---
title: Ladění a řešení potíží s parallelrunstepem
titleSuffix: Azure Machine Learning
description: Ladění a řešení potíží s ParallelRunStep v kanálech strojového učení v Azure Machine Learning SDK pro Python. Seznamte se s běžnými náskali pro vývoj s kanály a tipy, které vám pomohou ladit skripty před a během vzdáleného spuštění.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122961"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Ladění a řešení potíží s parallelrunstepem
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak ladit a řešit potíže s třídou [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) z sady [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Testování skriptů místně

V [části Testování skriptů místně](how-to-debug-pipelines.md#testing-scripts-locally) najdete kanály strojového učení. ParallelRunStep běží jako krok v kanálu ML, takže stejná odpověď platí pro oba.

## <a name="debugging-scripts-from-remote-context"></a>Ladění skriptů ze vzdáleného kontextu

Přechod z ladění bodovací skript místně ladění bodování skript u skutečného kanálu může být obtížný skok. Informace o hledání protokolů na portálu, [části kanály strojového učení o ladění skriptů ze vzdáleného kontextu](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Informace v této části platí také pro paralelní krok spustit.

Například soubor `70_driver_log.txt` protokolu obsahuje informace z řadiče, který spouští paralelní spustit krok kód.

Z důvodu distribuované povahy úloh paralelního spuštění existují protokoly z několika různých zdrojů. Jsou však vytvořeny dva konsolidované soubory, které poskytují informace na vysoké úrovni:

- `~/logs/overview.txt`: Tento soubor poskytuje informace na vysoké úrovni o počtu mini-dávky (také známý jako úkoly) vytvořené tak daleko a počet mini-dávky zpracovány tak daleko. Na tomto konci ukazuje výsledek úlohy. Pokud se úloha nezdařila, zobrazí se chybová zpráva a kde se má řešení potíží spustit.

- `~/logs/sys/master.txt`: Tento soubor poskytuje zobrazení hlavního uzlu (označovaného také jako orchestrator) spuštěné úlohy. Zahrnuje vytváření úkolů, sledování průběhu, výsledek spuštění.

Protokoly generované ze vstupního skriptu pomocí EntryScript.logger a tiskových příkazů budou nalezeny v následujících souborech:

- `~/logs/user/<ip_address>/Process-*.txt`: Tento soubor obsahuje protokoly napsané z entry_script pomocí EntryScript.logger. Obsahuje také příkaz print (stdout) z entry_script.

Pokud potřebujete úplné pochopení toho, jak každý uzel provedl skript skóre, podívejte se na jednotlivé protokoly procesů pro každý uzel. Protokoly procesů lze nalézt `sys/worker` ve složce seskupené podle pracovních uzlů:

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: Tento soubor poskytuje podrobné informace o každé mini-batch, jak je vyzvednout nebo dokončit pracovníkem. Pro každou minidávku obsahuje tento soubor:

    - IP adresa a PID pracovního procesu. 
    - Celkový počet položek, počet úspěšně zpracovaných položek a počet položek se nezdařilo.
    - Čas zahájení, doba trvání, čas zpracování a čas metody spuštění.

Můžete také najít informace o využití prostředků procesů pro každého pracovníka. Tyto informace jsou ve formátu CSV a jsou umístěny na adrese `~/logs/sys/perf/<ip_address>/`. Pro jeden uzel budou soubory úloh `~logs/sys/perf`k dispozici v části . Při kontrole využití prostředků se například podívejte na následující soubory:

- `Process-*.csv`: Využití prostředků pracovního procesu. 
- `sys.csv`: Protokol na uzel.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Jak se lze přihlásit ze svého uživatelského skriptu ze vzdáleného kontextu?
Můžete získat logger z EntryScript, jak je znázorněno v níže ukázkový kód, aby protokoly se zobrazí v **protokolech / uživatelské** složky na portálu.

**Ukázkový vstupní skript pomocí protokolovacího nástroje:**
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

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Jak bych mohl předat boční vstup, jako je soubor nebo soubory obsahující vyhledávací tabulku, všem svým pracovníkům?

Vytvořte objekt [datové sady](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) obsahující boční vstup a zaregistrujte se s pracovním prostorem. Poté k němu můžete přistupovat ve skriptu odvození (například v metodě init() následujícím způsobem:

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>Další kroky

* Podívejte se na odkaz na sdk nápovědu k balíčku [azureml-contrib-pipeline-step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) a [dokumentaci](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) pro třídu ParallelRunStep.

* Postupujte podle [pokročilého kurzu](tutorial-pipeline-batch-scoring-classification.md) o používání kanálů s paralelním krokem spuštění.

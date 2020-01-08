---
title: Ladění a řešení potíží s ParallelRunStep
titleSuffix: Azure Machine Learning
description: Ladit a řešit potíže s kanály strojového učení v sadě Azure Machine Learning SDK pro Python. Seznamte se s běžnými nástrahy pro vývoj s kanály a tipů, které vám pomůžou ladit skripty před a během vzdáleného spouštění.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 11/21/2019
ms.openlocfilehash: 20e88da6721a6715a7f31f5c423e305625528383
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540604"
---
# <a name="debug-and-troubleshoot-using-parallelrun"></a>Ladění a řešení potíží pomocí ParallelRun
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak ladit a řešit potíže se třídou [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) z [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Místní testování skriptů

Přečtěte si [část testování skriptů místně](how-to-debug-pipelines.md#testing-scripts-locally) pro kanály strojového učení. Vaše ParallelRunStep se spouští jako krok v kanálech ML, takže stejná odpověď platí i pro obě.

## <a name="debugging-scripts-from-remote-context"></a>Ladění skriptů ze vzdáleného kontextu

Přechod z ladění skriptu bodování místně na ladění vyhodnocovacího skriptu ve skutečném kanálu může být obtížné. Informace o hledání protokolů na portálu najdete v [části kanály strojového učení na webu ladění skriptů ze vzdáleného kontextu](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). Informace v této části platí i pro spuštění odvození dávky.

Například soubor protokolu `70_driver_log.txt` obsahuje také: 

* Všechny tištěné příkazy během provádění skriptu.
* Trasování zásobníku skriptu.

Z důvodu distribuované povahy úloh odvození dávky jsou protokoly z několika různých zdrojů. Vytvoří se ale dva konsolidované soubory, které poskytují informace vysoké úrovně:

- `~/logs/overview.txt`: Tento soubor poskytuje informace vysoké úrovně o počtu Mini dávek (označovaných také jako úlohy), které byly doposud vytvořeny a počtu mininích dávek zpracovaných zatím. Na tomto konci se zobrazuje výsledek úlohy. Pokud se úloha nezdařila, zobrazí se chybová zpráva a kde začít odstraňování potíží.

- `~/logs/master.txt`: Tento soubor poskytuje hlavní uzel (také označovaný jako Orchestrator) zobrazení spuštěné úlohy. Zahrnuje vytvoření úlohy, monitorování průběhu, výsledek spuštění.

Pokud potřebujete úplný přehled o tom, jak každý uzel spustil skript skóre, podívejte se na jednotlivé protokoly procesu pro každý uzel. Protokoly procesu lze nalézt ve složce `worker` seskupené podle uzlů pracovních procesů:

- `~/logs/worker/<ip_address>/Process-*.txt`: Tento soubor poskytuje podrobné informace o každé Mini dávce, jak si je pracovník vybral nebo dokončí. Pro každou miniskou dávku tento soubor obsahuje:

    - IP adresa a PID pracovního procesu. 
    - Celkový počet položek a počet úspěšně zpracovaných položek. 
    - Čas zahájení a ukončení v časech hodin (`start1` a `end1`). 
    - Čas zahájení a ukončení v době trvání procesoru (`start2` a `end2`). 

Můžete také vyhledat informace o využití prostředků pro jednotlivé pracovní procesy. Tyto informace jsou ve formátu CSV a jsou umístěné na `~/logs/performance/<ip_address>/`. Například při kontrole využití prostředků se podívejte na následující soubory:

- `process_resource_monitor_<ip>_<pid>.csv`: využití prostředků v rámci pracovního procesu. 
- `sys_resource_monitor_<ip>.csv`: protokol na uzel.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Návody se protokolovat z uživatelského skriptu ze vzdáleného kontextu?
Pomocí níže uvedených kroků můžete nastavit, aby se protokoly zobrazovaly ve složce **logs/uživatelé** na portálu:
1. První část kódu uložte do souboru entry_script_helper. py a vložte soubor do stejné složky jako váš vstupní skript. Tato třída Získá cestu v rámci AmlCompute. Pro místní test můžete změnit `get_working_dir()` pro vrácení místní složky.
2. V metodě `init()` config protokolovací nástroj a pak ho použijte. Druhá část kódu je příklad. 

**entry_script_helper. py:**
```python
"""
This module provides helper features for entry script.
This file should be in Python search paths or in the same folder as the entry script.
"""
import os
import socket
import logging
import time
from multiprocessing import current_process
from azureml.core import Run


class EntryScriptHelper:
    """ A helper to provide common features for entry script."""

    LOG_CONFIGED = False

    def get_logger(self, name="EntryScript"):
        """ Return a logger.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        return logging.getLogger(name)

    def config(self, name="EntryScript", level="INFO"):
        """ Config a logger. This should be called in init() in score module.
            Config the logger one time if not configed.
            The logger will write to the 'users' folder and show up in azure portal.
        """
        logger = logging.getLogger(name)

        formatter = logging.Formatter(
            "%(asctime)s|%(name)s|%(levelname)s|%(process)d|%(thread)d|%(funcName)s()|%(message)s"
        )
        formatter.converter = time.gmtime

        logger.setLevel(level)

        handler = logging.FileHandler(self.get_log_file_path())
        handler.setLevel(level)
        handler.setFormatter(formatter)
        logger.addHandler(handler)

        return logger

    def get_log_file_path(self):
        """ Get the log file path for users.
            Each process has its own log file, so there is not race issue among multiple processes.
        """
        ip_address = socket.gethostbyname(socket.gethostname())
        log_dir = os.path.join(self.get_log_dir(), "user", ip_address)
        os.makedirs(log_dir, exist_ok=True)
        return os.path.join(log_dir, f"{current_process().name}.txt")

    def get_log_dir(self):
        """ Return the folder for logs.
            Files and folders in it will be uploaded and show up in run detail page in the azure portal.
        """
        log_dir = os.path.join(self.get_working_dir(), "logs")
        os.makedirs(log_dir, exist_ok=True)
        return log_dir

    def get_working_dir(self):
        """ Return the working directory."""
        return os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), self.get_run().id)

    def get_temp_dir(self):
        """ Return local temp directory."""
        local_temp_dir = os.path.join(
            os.environ.get("AZ_BATCHAI_JOB_TEMP", ""), "azureml-bi", str(os.getpid())
        )
        os.makedirs(local_temp_dir, exist_ok=True)
        return local_temp_dir

    def get_run(self):
        """ Return the Run from the context."""
        return Run.get_context(allow_offline=False)

```

**Ukázkový vstupní skript s použitím protokolovacího nástroje:**
```python
"""
This is a sample scoring module.

This module provides a sample which passes the input back without any change.
"""
import os
import logging
from entry_script_helper import EntryScriptHelper

LOG_NAME = "score_file_list"


def init():
    """ Init """
    EntryScriptHelper().config(LOG_NAME)
    logger = logging.getLogger(LOG_NAME)
    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    logger.info(f"{__file__}.output_folder:{output_folder}")
    logger.info("init()")
    os.makedirs(output_folder, exist_ok=True)


def run(mini_batch):
    """ Accept and return the list back."""
    logger = logging.getLogger(LOG_NAME)
    logger.info(f"{__file__}, run({mini_batch})")

    output_folder = os.path.join(os.environ.get("AZ_BATCHAI_INPUT_AZUREML", ""), "temp/output")
    for file_name in mini_batch:
        with open(file_name, "r") as file:
            lines = file.readlines()
        base_name = os.path.basename(file_name)
        name = os.path.join(output_folder, base_name)
        logger.info(f"{__file__}: {name}")
        with open(name, "w") as file:
            file.write(f"ouput file {name} from {__file__}:\n")
            for line in lines:
                file.write(line)

    return mini_batch
```

## <a name="next-steps"></a>Další kroky

* Nápovědu k balíčku [AzureML-contrib-Pipeline-Step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) a [dokumentaci](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) ke třídě ParallelRunStep najdete v referenčních informacích k sadě SDK.

* Postupujte podle [podrobného kurzu](tutorial-pipeline-batch-scoring-classification.md) použití kanálů pro dávkové vyhodnocování.

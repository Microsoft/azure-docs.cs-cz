---
title: Najít spuštění s největší přesností a nejkratší dobou trvání v aplikaci Azure Machine Learning Workbench | Dokumentace Microsoftu
description: Začátku do konce případ použití najít největší přesností prostřednictvím rozhraní příkazového řádku pomocí Azure Machine Learning Workbench
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs, jmartens, jasonwhowell
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 094fd6d8c6c6d647533cf5409d1a85283c71c80e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953238"
---
# <a name="find-runs-with-the-best-accuracy-and-lowest-duration"></a>Najít spuštění s největší přesností a nejkratší dobou trvání
Zadaný více spuštění, případem použití je najít spuštění s největší přesností. Jedním z přístupů je použít rozhraní příkazového řádku (CLI) s [JMESPath](http://jmespath.org/) dotazu. Další informace o tom, jak používat JMESPath v Azure CLI najdete v tématu [dotazů JMESPath pomocí Azure CLI](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest). V následujícím příkladu jsou vytvořeny čtyři spuštění s přesnost hodnoty 0, 0,98, 1 a 1. Spustí se filtrují v případě, že jsou v rozsahu `[MaxAccuracy-Threshold, MaxAccuracy]` kde `Threshold = .03`.

## <a name="sample-data"></a>Ukázková data
Pokud nemáte existující spuštění s `Accuracy` hodnota následující kroky generovat spuštění pro dotazování.

Nejprve vytvořte soubor Pythonu v aplikaci Azure Machine Learning Workbench, pojmenujte ho `log_accuracy.py`a vložte následující kód:
```python
from azureml.logging import get_azureml_logger

logger = get_azureml_logger()

accuracy_value = 0.5

if len(sys.argv) > 1:
     accuracy_value = float(sys.argv[1])

logger.log("Accuracy", accuracy_value)
```

V dalším kroku vytvořte soubor `run.py`a vložte následující kód:
```python
import os

accuracy_values = [0, 0.98, 1.0, 1.0]
for value in accuracy_values:
    os.system('az ml experiment submit -c local ./log_accuracy.py {}'.format(value))
```

A konečně, otevřete rozhraní příkazového řádku pomocí aplikace Workbench a spusťte příkaz `python run.py` odeslat čtyři pokusy. Po dokončení skriptu, měli byste vidět čtyři další spuštění v `Run History` kartu.

## <a name="query-the-run-history"></a>Dotaz se historie spuštění
První příkaz najde hodnotu Maximální přesnost.
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

Pomocí této hodnoty maximální přesnost `1` a prahovou hodnotu z `0.03`, druhý příkaz filtry běží za použití `Accuracy` a pak spustí seřadí tabulku podle `duration` vzestupně.
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> Pokud chcete kontrolu striktní horní mez, je formát dotazu ``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]``

Pokud používáte PowerShell, následující kód používá místní proměnné k ukládání prahové hodnoty a maximální přesnost:
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>Další postup
Další informace o protokolování naleznete v tématu [použití historie spuštění a metrik modelů v Azure Machine Learning Workbench](how-to-use-run-history-model-metrics.md).    

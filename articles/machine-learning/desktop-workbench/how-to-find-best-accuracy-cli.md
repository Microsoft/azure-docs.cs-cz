---
title: Najít spustí s nejlepší přesnost a nejnižší doba trvání v Azure Machine Learning Workbench | Microsoft Docs
description: Případ použití začátku do konce pro vyhledání nejlepší přesnost prostřednictvím rozhraní příkazového řádku pomocí Azure Machine Learning Workbench
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs, jmartens, jasonwhowell
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 077af8b5d3367dd2188cbd6e5d76aaf52512a1e8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830795"
---
# <a name="find-runs-with-the-best-accuracy-and-lowest-duration"></a>Najít spustí s nejlepší přesnost a nejnižší doba trvání
Zadány více běží, je nalezení spustí s nejlepší přesnost jeden případ použití. Jeden ze způsobů je použití rozhraní příkazového řádku (CLI) s [JMESPath](http://jmespath.org/) dotazu. Další informace o tom, jak používat JMESPath v rozhraní příkazového řádku Azure najdete v tématu [JMESPath používat dotazy pomocí Azure CLI 2.0](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest). V následujícím příkladu jsou vytvořeny čtyři spustí s přesnost hodnoty 0, 0,98, 1 a 1. Spustí se filtrují, pokud jsou v rozsahu `[MaxAccuracy-Threshold, MaxAccuracy]` kde `Threshold = .03`.

## <a name="sample-data"></a>Ukázková data
Pokud nemáte existující spustí s `Accuracy` hodnotu, následující kroky generovat spuštění pro dotazování.

Nejprve vytvořte soubor Python v nástroji Azure Machine Learning Workbench, pojmenujte ji `log_accuracy.py`a vložte následující kód:
```python
from azureml.logging import get_azureml_logger

logger = get_azureml_logger()

accuracy_value = 0.5

if len(sys.argv) > 1:
     accuracy_value = float(sys.argv[1])

logger.log("Accuracy", accuracy_value)
```

Dále vytvořte soubor `run.py`a vložte následující kód:
```python
import os

accuracy_values = [0, 0.98, 1.0, 1.0]
for value in accuracy_values:
    os.system('az ml experiment submit -c local ./log_accuracy.py {}'.format(value))
```

Nakonec otevřete rozhraní příkazového řádku pomocí Workbench a spusťte příkaz `python run.py` odeslat čtyři pokusy. Po dokončení skriptu, měli byste vidět další čtyři se spustí v `Run History` kartě.

## <a name="query-the-run-history"></a>Historie spouštění dotazu
První příkaz vyhledá hodnotu Maximální přesnost.
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

Hodnota Maximální přesnost pomocí `1` a prahovou hodnotu `0.03`, druhý příkaz filtry se spouští pomocí `Accuracy` a potom se spustí řazení `duration` vzestupně.
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> Pokud chcete kontrolu striktní horní mez, není ve formátu dotazu ``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]``

Pokud používáte prostředí PowerShell, následující kód používá k uložení prahovou hodnotu a maximální přesnost lokální proměnné:
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>Další postup
Další informace o protokolování naleznete v tématu [použití historie spouštění a metriky modelu v Azure Machine Learning Workbench](how-to-use-run-history-model-metrics.md).    

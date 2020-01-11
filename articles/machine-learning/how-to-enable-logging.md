---
title: Povolit přihlašování Azure Machine Learning
description: Naučte se, jak povolit protokolování Azure Machine Learning pomocí výchozího balíčku protokolování Pythonu i pomocí funkcí specifických pro sadu SDK.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 07/12/2019
ms.openlocfilehash: 65db6cfa64658edc4e604a009e672ba3aa5e86f3
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893982"
---
# <a name="enable-logging-in-azure-machine-learning"></a>Povolit přihlašování Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Sada SDK Azure Machine Learning Pythonu vám umožňuje povolit protokolování pomocí výchozího protokolovacího balíčku Pythonu i pomocí funkce specifické pro sadu SDK jak pro místní protokolování, tak pro protokolování do vašeho pracovního prostoru na portálu. Protokoly poskytují vývojářům informace o stavu aplikace v reálném čase a můžou vám pomáhat s diagnostikou chyb nebo upozornění. V tomto článku se seznámíte s různými způsoby, jak povolit protokolování v následujících oblastech:

> [!div class="checklist"]
> * Školicí modely a cíle výpočtů
> * Vytvoření Image
> * Nasazené modely
> * Nastavení `logging` Pythonu

[Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md). Pro další informace o sadě SDK použijte [Průvodce](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) .

## <a name="training-models-and-compute-target-logging"></a>Školicí modely a protokolování cílů výpočtů

Existuje několik způsobů, jak povolit protokolování během procesu školicích modelů, a příklady zobrazené v příkladu ilustrují běžné vzory návrhu. Data týkající se spuštění můžete do svého pracovního prostoru v cloudu snadno protokolovat pomocí funkce `start_logging` na `Experiment` třídě.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Další funkce protokolování najdete v referenční dokumentaci pro třídu [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) .

Pokud chcete povolit místní protokolování stavu aplikace během procesu školení, použijte parametr `show_output`. Povolení podrobného protokolování vám umožní zobrazit podrobnosti z procesu školení a také informace o všech vzdálených prostředcích nebo výpočetních cílech. Pomocí následujícího kódu Povolte protokolování při odeslání experimentu.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Ve výsledném běhu můžete použít také stejný parametr ve funkci `wait_for_completion`.

```python
run.wait_for_completion(show_output=True)
```

Sada SDK také podporuje použití výchozího balíčku protokolování Pythonu v některých scénářích pro školení. Následující příklad povoluje úroveň protokolování `INFO` v objektu `AutoMLConfig`.

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task='regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

Při vytváření trvalého cíle výpočtů můžete použít také parametr `show_output`. Zadáním parametru ve funkci `wait_for_completion` povolíte protokolování během vytváření cílového cíle.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Protokolování nasazených modelů

Chcete-li načíst protokoly z dříve nasazené webové služby, načtěte službu a použijte funkci `get_logs()`. Protokoly mohou obsahovat podrobné informace o všech chybách, ke kterým došlo během nasazení.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Můžete také protokolovat vlastní trasování zásobníku webové služby tím, že povolíte Application Insights, což vám umožní monitorovat časy požadavků a odpovědí, míry selhání a výjimky. Voláním funkce `update()` v existující webové službě povolíte Application Insights.

```python
service.update(enable_app_insights=True)
```

Další informace o tom, jak pracovat s Application Insights v Azure Machine Learning studiu, najdete v tématu [How to](how-to-enable-app-insights.md) .

## <a name="python-native-logging-settings"></a>Nastavení nativního protokolování Pythonu

Některé protokoly v sadě SDK mohou obsahovat chybu, která dává pokyn k nastavení úrovně protokolování na ladění. Chcete-li nastavit úroveň protokolování, přidejte do skriptu následující kód.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

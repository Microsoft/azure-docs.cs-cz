---
title: Povolení protokolování v Azure Machine Learning
description: Zjistěte, jak povolit protokolování v Azure Machine Learning pomocí výchozího balíčku protokolování Pythonu, stejně jako pomocí funkce specifické pro SDK.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 03/05/2020
ms.openlocfilehash: 73b9ae6bc3c15526bfdafd74330c7b86286631b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78396141"
---
# <a name="enable-logging-in-azure-machine-learning"></a>Povolení protokolování v Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning Python SDK umožňuje povolit protokolování pomocí výchozí ho protokolování balíčku Pythonu, stejně jako pomocí funkce specifické pro SDK pro místní protokolování a protokolování do pracovního prostoru na portálu. Protokoly poskytují vývojářům informace o stavu aplikace v reálném čase a mohou pomoci s diagnostikou chyb nebo upozornění. V tomto článku se dozvíte různé způsoby povolení protokolování v následujících oblastech:

> [!div class="checklist"]
> * Tréninkové modely a výpočetní cíle
> * Vytvoření obrázku
> * Nasazené modely
> * Nastavení `logging` Pythonu

[Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md). Další informace naleznete v [příručce](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) sdk.

## <a name="training-models-and-compute-target-logging"></a>Trénovací modely a protokolování výpočetních cílů

Existuje několik způsobů, jak povolit protokolování během procesu školení modelu a uvedené příklady budou ilustrovat běžné návrhové vzory. Data související s spuštěním můžete snadno protokolovat do `start_logging` pracovního `Experiment` prostoru v cloudu pomocí funkce ve třídě.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Další funkce protokolování naleznete v referenční dokumentaci pro třídu [Run.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)

Chcete-li povolit místní protokolování stavu `show_output` aplikace během průběhu školení, použijte parametr. Povolení podrobné protokolování umožňuje zobrazit podrobnosti z procesu školení, stejně jako informace o všech vzdálených prostředků nebo výpočetních cílů. Pomocí následujícího kódu povolte protokolování při odesílání experimentu.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Můžete také použít stejný parametr `wait_for_completion` ve funkci na výsledné spuštění.

```python
run.wait_for_completion(show_output=True)
```

Sada SDK také podporuje použití výchozího balíčku protokolování pythonu v určitých scénářích pro školení. Následující příklad umožňuje úroveň `INFO` protokolování `AutoMLConfig` v objektu.

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

Parametr můžete také `show_output` použít při vytváření trvalého výpočetního cíle. Zadejte parametr `wait_for_completion` ve funkci, která umožní protokolování během vytváření výpočetního cíle.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(
    workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Protokolování pro nasazené modely

Chcete-li načíst protokoly z dříve nasazené webové `get_logs()` služby, načtěte službu a použijte funkci. Protokoly mohou obsahovat podrobné informace o všech chybách, ke kterým došlo během nasazení.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Můžete také protokolovat vlastní trasování zásobníku pro webovou službu povolením Application Insights, který umožňuje sledovat doby požadavku/odpovědi, selhání a výjimky. Volání `update()` funkce na existující webové služby povolit Application Insights.

```python
service.update(enable_app_insights=True)
```

Další informace naleznete v [tématu Sledování a shromažďování dat z koncových bodů webové služby ML](how-to-enable-app-insights.md).

## <a name="python-native-logging-settings"></a>Nastavení nativního protokolování v Pythonu

Některé protokoly v sadě SDK mohou obsahovat chybu, která vás nastaví úroveň protokolování na DEBUG. Chcete-li nastavit úroveň protokolování, přidejte do skriptu následující kód.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="next-steps"></a>Další kroky

* [Sledování a shromažďování dat z koncových bodů webové služby ML](how-to-enable-app-insights.md)
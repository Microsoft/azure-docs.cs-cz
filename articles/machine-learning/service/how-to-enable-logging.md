---
title: Povolení protokolování ve službě Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak povolit protokolování ve službě Azure Machine Learning pomocí obou výchozího protokolování balíček Pythonu a jak používat funkce specifické pro sadu SDK.
ms.author: trbye
author: trevorbye
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.date: 02/11/2019
ms.openlocfilehash: bff0feab7ce062c9e2515fa038d249838a809591
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56101128"
---
# <a name="enable-logging-in-azure-machine-learning-service"></a>Povolení protokolování ve službě Azure Machine Learning

Azure Machine Learning Python SDK můžete povolit protokolování použití balíčku výchozí protokolování Python a jak používat funkce specifické pro sadu SDK pro místní protokolování a protokolování do pracovního prostoru na portálu. Protokoly vývojáři poskytnout informace o stavu aplikace v reálném čase a pomáhá s diagnostikou chyby nebo varování. V tomto článku se dozvíte, různé způsoby povolení protokolování v následujících oblastech:

> [!div class="checklist"]
> * Trénování modelů a cílových výpočetních prostředí
> * Vytvoření Image
> * Nasazených modelů
> * Python `logging` nastavení

Použití [průvodce](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) k instalaci sady SDK a [Začínáme](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python) pomocí sady SDK k vytvoření pracovního prostoru na webu Azure Portal.

## <a name="training-models-and-compute-target-logging"></a>Trénování modelů a protokolování cílové výpočetní prostředky

Existuje několik způsobů, jak povolit protokolování během procesu trénování modelu a příkladů uvedených popisuje běžné vzory návrhu. Můžete jednoduše zaznamenávat data související s spuštění do pracovního prostoru v cloudu pomocí `start_logging` na fungovat `Experiment` třídy.

```python
from azureml.core import Experiment

exp = Experiment(workspace=ws, name='test_experiment')
run = exp.start_logging()
run.log("test-val", 10)
```

Viz odkaz [dokumentaci](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) pro `Run` třídy pro funkce dodatečné protokolování.

Chcete-li povolit místní protokolování stav aplikace v průběhu školení, použijte `show_output` parametru. Povolení podrobného protokolování můžete zobrazit podrobnosti o procesu trénování, stejně jako informace o všech vzdálených prostředků nebo cílových výpočetních prostředí. Povolení protokolování po odeslání experimentu pomocí následujícího kódu.

```python
from azureml.core import Experiment

experiment = Experiment(ws, experiment_name)
run = experiment.submit(config=run_config_object, show_output=True)
```

Můžete také použít stejný parametr v `wait_for_completion` funkce výsledná spuštění.

```python
run.wait_for_completion(show_output=True)
```

Sada SDK podporuje také pomocí výchozího protokolování balíčku python v určitých scénářích pro vzdělávání. Následující příklad povolí úroveň protokolování `INFO` v `AutoMLConfig` objektu.

```python
from azureml.train.automl import AutoMLConfig
import logging

automated_ml_config = AutoMLConfig(task = 'regression',
                                   verbosity=logging.INFO,
                                   X=your_training_features,
                                   y=your_training_labels,
                                   iterations=30,
                                   iteration_timeout_minutes=5,
                                   primary_metric="spearman_correlation")
```

Můžete také použít `show_output` parametr při vytváření cílového výpočetního prostředí trvalé. Zadejte parametr do `wait_for_completion` funkce povolení protokolování během vytváření cílové výpočetní prostředky.

```python
from azureml.core.compute import ComputeTarget

compute_target = ComputeTarget.attach(workspace=ws, name="example", attach_configuration=config)
compute.wait_for_completion(show_output=True)
```

## <a name="logging-during-image-creation"></a>Protokolování během vytváření bitové kopie

Povolení protokolování během vytváření bitové kopie vám umožní zobrazit všechny chyby během procesu sestavení. Nastavte `show_output` param na `wait_for_deployment()` funkce.

```python
from azureml.core.webservice import Webservice

service = Webservice.deploy_from_image(deployment_config=your_config,
                                            image=image,
                                            name="example-image",
                                            workspace=ws)

service.wait_for_deployment(show_output=True)
```

## <a name="logging-for-deployed-models"></a>Protokolování nasazených modelů

Pokud chcete načíst protokoly z dříve nasazené webové služby, zavedení služby a použít `get_logs()` funkce. Protokoly mohou obsahovat podrobné informace o případných chybách, ke kterým došlo během nasazení.

```python
from azureml.core.webservice import Webservice

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Můžete také Protokolovat vlastní trasování zásobníku pro webovou službu povolením Application Insights, který umožňuje sledování žádosti a doby odezvy, chybovost a výjimky. Volání `update()` funkce na existující webovou službu povolit Application Insights.

```python
service..update(enable_app_insights=True)
```

Zobrazit [postupy](how-to-enable-app-insights.md#enable-and-disable-in-the-portal) Další informace o tom, jak pracovat s Application Insights na webu Azure Portal.

## <a name="python-native-logging-settings"></a>Nastavení nativní protokolování Pythonu

Některé protokoly v sadě SDK mohou obsahovat chybu, která dává pokyn k nastavení úrovně protokolování ladění. Pokud chcete nastavit úroveň protokolování, přidejte následující kód do vašeho skriptu.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```
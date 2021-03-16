---
title: Protokolování metrik a experimentů strojového učení
titleSuffix: Azure Machine Learning
description: Povolte protokolování na vaše běhy školení na ML, abyste mohli monitorovat metriky spuštění v reálném čase a pomáhat diagnostikovat chyby a upozornění.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 9576730d9c4f8d4d237dce9ce8f207ea14b04f45
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471600"
---
# <a name="enable-logging-in-ml-training-runs"></a>Povolit protokolování zkušebních běhů v ML


Sada SDK Azure Machine Learning Pythonu umožňuje protokolovat informace v reálném čase s využitím výchozího balíčku protokolování Pythonu a funkcí specifických pro tuto sadu. Můžete protokolovat místně a odesílat protokoly do svého pracovního prostoru na portálu.

Protokoly vám pomohou při diagnostice chyb a upozornění a také při sledování metriky výkonu, jako jsou parametry a výkon modelu. V tomto článku se dozvíte, jak povolit protokolování v následujících scénářích:

> [!div class="checklist"]
> * Interaktivní trénovací relace
> * Odesílání trénovacích úloh pomocí ScriptRunConfig
> * Nastavení `logging` nativní pro Python
> * Protokolování z dalších zdrojů


> [!TIP]
> V tomto článku se dozvíte, jak monitorovat proces trénování modelu. Pokud vás zajímá monitorování využití prostředků a událostí z Azure Machine Learningu, jako jsou kvóty, dokončená trénovací spuštění nebo dokončená nasazení modelů, přečtěte si téma věnované [monitorování služby Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Typy dat

Můžete protokolovat různé datové typy, včetně skalárních hodnot, seznamů, tabulek, obrázků, adresářů a dalších prvků. Další informace a ukázky kódu v Pythonu pro různé datové typy najdete na [referenční stránce k třídě Run](/python/api/azureml-core/azureml.core.run%28class%29).

### <a name="logging-run-metrics"></a>Protokolování spuštění metrik 

Pomocí následujících metod v rozhraních API protokolování můžete ovlivnit vizualizace metrik. Poznamenejte si [omezení služby](https://docs.microsoft.com/azure/machine-learning/resource-limits-quotas-capacity#metrics) pro tyto protokolované metriky. 

|Hodnota protokolu|Příklad kódu| Formátování na portálu|
|----|----|----|
|Protokolování pole číselných hodnot| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|Spojnicový graf s jednou proměnnou|
|Zaprotokoluje jednu číselnou hodnotu se stejným názvem metriky, který se opakovaně používá (například v rámci smyčky for).| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Spojnicový graf s jednou proměnnou|
|Opakované zaznamenání řádku se dvěma číselnými sloupci|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Spojnicový graf se dvěma proměnnými|
|Tabulka protokolu se dvěma číselnými sloupci|`run.log_table(name='Sine Wave', value=sines)`|Spojnicový graf se dvěma proměnnými|
|Obrázek protokolu|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|Tuto metodu použijte k zaznamenání souboru obrázku nebo matplotlib vykreslení do běhu. Tyto obrázky budou viditelné a srovnatelné v záznamu spuštění.|

### <a name="logging-with-mlflow"></a>Protokolování pomocí MLflow
Pomocí MLFlowLogger můžete protokolovat metriky.

```python
from azureml.core import Run
# connect to the workspace from within your running code
run = Run.get_context()
ws = run.experiment.workspace

# workspace has associated ml-flow-tracking-uri
mlflow_url = ws.get_mlflow_tracking_uri()

#Example: PyTorch Lightning
from pytorch_lightning.loggers import MLFlowLogger

mlf_logger = MLFlowLogger(experiment_name=run.experiment.name, tracking_uri=mlflow_url)
mlf_logger._run_id = run.id
```

## <a name="interactive-logging-session"></a>Interaktivní relace protokolování

Interaktivní relace protokolování se obvykle používají v prostředích poznámkových bloků. Interaktivní relace protokolování se pouštějí pomocí metody [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-). Všechny metriky zaznamenávané během relace se přidají do záznamu spuštění v experimentu. Metoda [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) ukončí relace a označí běh jako dokončený.

## <a name="scriptrun-logs"></a>Protokoly ScriptRun

V této části se dozvíte, jak přidat kód protokolování do běhů vytvořených při využití konfigurace pomocí třídy ScriptRunConfig. K zapouzdření skriptů a prostředí pro opakovatelná spouštění můžete využít třídu [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig). Tuto možnost můžete také využít pro zobrazení vizuálního widgetu pro Jupyter Notebook pro monitorování.

V tomto příkladu se provede uklizení parametrů přes hodnoty alfa a k zaznamenání výsledků se použije metoda [run. log ()](/python/api/azureml-core/azureml.core.run%28class%29#log-name--value--description----).

1. Vytvořte trénovací skript, který obsahuje logiku protokolování: `train.py`.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Odešlete skript ```train.py``` pro spuštění v prostředí spravovaném uživatelem. K trénování se odešle celá složka skriptu.

   [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? název = src)]


   [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? název = run)]

    Parametr `show_output` zapne podrobné protokolování, které vám umožní zobrazit podrobnosti z procesu trénování a také informace o všech vzdálených prostředcích nebo cílových výpočetních objektech. Pomocí následujícího kódu zapněte podrobné protokolování při odeslání experimentu.

```python
run = exp.submit(src, show_output=True)
```

Ve výsledném běhu můžete použít také stejný parametr ve funkci `wait_for_completion`.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Nativní protokolování v Pythonu

Některé protokoly v sadě SDK mohou obsahovat chybu, která dává pokyn k nastavení úrovně protokolování na ladění. Pokud chcete nastavit úroveň protokolování, přidejte do skriptu následující kód.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Další zdroje protokolování

Azure Machine Learning může během trénování protokolovat také informace z jiných zdrojů, jako jsou například spuštění automatizovaného strojové učení nebo kontejnery Docker, které spouští úlohy. Tyto protokoly nejsou zdokumentované, ale pokud narazíte na problémy a kontaktujete podporu Microsoftu, dají se tyto protokoly využít při řešení potíží.

Informace o protokolování metrik v designeru služby Azure Machine Learning najdete v tématu věnovaném [postupu při protokolování metrik v designeru](how-to-track-designer-experiments.md).

## <a name="example-notebooks"></a>Příklady poznámkových bloků

Následující poznámkové bloky ukazují koncepty popsané v tomto článku:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat Azure Machine Learning, najdete v těchto článcích:

* Přečtěte si, jak [protokolovat metriky v designeru služby Azure Machine Learning](how-to-track-designer-experiments.md).

* Příklad postupu při registraci nejvhodnějšího modelu a jeho nasazení najdete v kurzu [Trénování modelu klasifikace obrázků s Azure Machine Learning](tutorial-train-models-with-aml.md).

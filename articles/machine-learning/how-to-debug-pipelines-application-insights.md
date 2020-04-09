---
title: Ladění a řešení potíží s kanály strojového učení v Application Insights
titleSuffix: Azure Machine Learning
description: Přidejte protokolování do svých kanálů školení a dávkového vyhodnocování a zobrazte zaznamenané výsledky v Application Insights.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: b3e4bf19a7ec153f85483f3c5028e468e06ed7f0
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982357"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines-in-application-insights"></a>Ladění a řešení potíží s kanály strojového učení v Application Insights
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

[Knihovnu Pythonu OpenCensus](https://opencensus.io/quickstart/python/) lze použít ke směrování protokolů do Application Insights z vašich skriptů. Agregace protokolů z kanálu běží na jednom místě umožňuje vytvářet dotazy a diagnostikovat problémy. Použití Application Insights vám umožní sledovat protokoly v průběhu času a porovnat protokoly kanálu napříč spuštěními.

S vaše protokoly v jednou místo bude poskytovat historii výjimek a chybových zpráv. Vzhledem k tomu, že application insights integruje s Azure Alerts, můžete také vytvářet výstrahy na základě dotazů Application Insights.

## <a name="prerequisites"></a>Požadavky

* Podle pokynů vytvořte pracovní prostor [Azure Machine Learning](./how-to-manage-workspace.md) a [vytvořte svůj první kanál.](./how-to-create-your-first-pipeline.md)
* [Nakonfigurujte vývojové prostředí](./how-to-configure-environment.md) tak, aby bylo nainstalováno sada Azure Machine Learning SDK.
* Nainstalujte balíček [OpenCensus Azure Monitor Exporter](https://pypi.org/project/opencensus-ext-azure/) místně:
  ```python
  pip install opencensus-ext-azure
  ```
* Vytvoření [instance Application Insights](../azure-monitor/app/opencensus-python.md) (tento dokument také obsahuje informace o získání připojovacího řetězce pro prostředek)

## <a name="getting-started"></a>začínáme

Tato část je úvod emituje specifické pro použití OpenCensus z kanálu Azure Machine Learning. Podrobný kurz najdete [v tématu OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

Přidejte pythonscriptstep do kanálu Azure ML. Nakonfigurujte [runconfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) se závislostí na opencensus-ext-azure. Nakonfigurujte `APPLICATIONINSIGHTS_CONNECTION_STRING` proměnnou prostředí.

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

Vytvořte soubor s názvem `sample_step.py`. Importujte třídu AzureLogHandler do směrování protokolů do Application Insights. Budete také muset importovat knihovnu protokolování Pythonu.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Dále přidejte AzureLogHandler do protokolování pythonu.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Protokolování s vlastními dimenzemi
 
Ve výchozím nastavení protokoly předané do Application Insights nebudou mít dostatek kontextu, aby bylo možné trasovat zpět do běhu nebo experimentu. Chcete-li protokoly žalovatelné pro diagnostiku problémů, jsou potřeba další pole. 

Chcete-li přidat tato pole, vlastní dimenze mohou být přidány poskytnout kontext zprávy protokolu. Jedním z příkladů je, když někdo chce zobrazit protokoly přes více kroků ve stejném kanálu spustit.

Vlastní dimenze tvoří slovník párů klíč-hodnota (uložená jako řetězec, řetězec). Slovník je pak odeslán do Application Insights a zobrazí se jako sloupec ve výsledcích dotazu. Jeho jednotlivé rozměry lze použít jako [parametry dotazu](#additional-helpful-queries).

### <a name="helpful-context-to-include"></a>Užitečný kontext, který má být zahrnut

| Pole                          | Uvažování/příklad                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | Může dotaz protokoly pro ty se stejným parent_run_id zobrazit protokoly v průběhu času pro všechny kroky, namísto nutnosti ponořit se do každého jednotlivého kroku                                        |
| step_id                        | Může dotaz protokoly pro ty se stejným step_id zjistit, kde došlo k problému s úzkým rozsahem pouze jednotlivé kroky                                                        |
| step_name                      | Může dotaz protokoly zobrazit výkon kroku v průběhu času. Také pomáhá najít step_id pro nedávné běhy bez potápění do portálu UI                                          |
| experiment_name                | Můžete dotaz přes protokoly zobrazit výkon experimentu v průběhu času. Také pomáhá najít parent_run_id nebo step_id pro nedávné běhy bez potápění do portálu UI                   |
| run_url                 | Může poskytnout odkaz přímo zpět na útěk u vyšetřování. |

**Další užitečná pole**

Tato pole mohou vyžadovat další instrumentaci kódu a nejsou poskytovány kontextem spuštění.

| Pole                   | Uvažování/příklad                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | Pokud k nasazení používáte CI/CD, může toto pole korelovat protokoly s verzí kódu, která poskytla logiku kroku a kanálu. Tento odkaz může dále pomoci diagnostikovat problémy nebo identifikovat modely s určitými vlastnostmi (hodnoty protokolu/metriky) |
| run_type                       | Dokáže rozlišovat mezi různými typy modelů nebo školením vs. spuštění majení |

### <a name="creating-a-custom-dimensions-dictionary"></a>Vytvoření slovníku vlastních dimenzí

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", custom_dimensions)
```

## <a name="opencensus-python-logging-considerations"></a>Důležité informace o protokolování pythonu OpenCensus

OpenCensus AzureLogHandler se používá ke směrování protokolů Pythonu do Application Insights. V důsledku toho by měly být zváženy nuance protokolování Pythonu. Při vytvoření protokolovacího nástroje má výchozí úroveň protokolu a zobrazí protokoly větší než nebo rovno této úrovni. Dobrým referencem pro používání funkcí protokolování pythonu je [Kuchařka protokolování](https://docs.python.org/3/howto/logging-cookbook.html).

Proměnná `APPLICATIONINSIGHTS_CONNECTION_STRING` prostředí je potřebná pro knihovnu OpenCensus. Doporučujeme nastavit tuto proměnnou prostředí namísto předání jako parametr kanálu, aby se zabránilo předávání kolem připojovacích řetězců prostého textu.

## <a name="querying-logs-in-application-insights"></a>Dotazování protokolů v Přehledech aplikací

Protokoly směrované do Application Insights se zobrazí pod 'trasování' nebo "výjimky". Nezapomeňte upravit časové okno tak, aby zahrnovalo spuštění kanálu.

![Výsledek dotazu na přehledy aplikací](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Výsledek v Application Insights zobrazí zprávu protokolu a úroveň, cestu k souboru a číslo řádku kódu. Zobrazí se také všechny vlastní dimenze. V tomto obrázku slovník customDimensions zobrazuje dvojice klíč/hodnota z předchozí [ukázky kódu](#creating-a-custom-dimensions-dictionary).

### <a name="additional-helpful-queries"></a>Další užitečné dotazy

Některé z níže uvedených dotazů používají 'customDimensions.Level'. Tyto úrovně závažnosti odpovídají úrovni, se kterou byl protokol Pythonu původně odeslán. Další informace o dotazu naleznete v [tématu Dotazy protokolu sledování Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

| Případ použití                                                               | Dotaz                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Protokolovat výsledky pro konkrétní vlastní dimenzi, například "parent_run_id" | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Log výsledky pro všechny tréninkové běží za posledních 7 dní                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Protokolovat výsledky s chybou závažnosti úrovně za posledních 7 dní              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Počet výsledků protokolu s chybou závažnosti úrovně za posledních 7 dní     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Další kroky

Jakmile máte protokoly v instanci Application Insights, můžete je použít k nastavení [výstrah Azure Monitor](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on) na základě výsledků dotazu.

Můžete také přidat výsledky z dotazů na [řídicí panel Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards#add-logs-analytics-query) pro další přehledy.

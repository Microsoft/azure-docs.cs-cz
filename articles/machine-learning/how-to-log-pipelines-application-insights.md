---
title: Monitorování & shromažďování souborů protokolu kanálu
titleSuffix: Azure Machine Learning
description: Přidejte protokolování do kanálů pro vyhodnocování školení a Batch a zobrazte výsledky protokolu v Application Insights.
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.date: 08/11/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 58e604eccaca4630a235f4ae83724df20d6b1e26
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592527"
---
# <a name="collect-machine-learning-pipeline-log-files-in-application-insights-for-alerts-and-debugging"></a>Shromažďování souborů protokolu kanálu Machine Learning v Application Insights pro výstrahy a ladění


Knihovna Pythonu [OpenCensus](https://opencensus.io/quickstart/python/) se dá použít k směrování protokolů, které se Application Insights ze skriptů. Agregace protokolů z kanálu na jednom místě umožňuje vytvářet dotazy a diagnostikovat problémy. Použití Application Insights vám umožní sledovat protokoly v průběhu času a porovnat protokoly kanálu v různých spuštěních.

V případě, že se vaše protokoly nacházejí v jednom místě, budete mít k dispozici historii výjimek a chybových zpráv. Vzhledem k tomu, že Application Insights se integruje s výstrahami Azure, můžete také vytvořit výstrahy na základě Application Insights dotazů.

## <a name="prerequisites"></a>Požadavky

* Podle pokynů vytvořte pracovní prostor [Azure Machine Learning](./how-to-manage-workspace.md) a [vytvořte svůj první kanál](./how-to-create-machine-learning-pipelines.md) .
* [Nakonfigurujte vývojové prostředí](./how-to-configure-environment.md) pro instalaci sady Azure Machine Learning SDK.
* Místní instalace balíčku [OpenCensus Azure monitor Exportér](https://pypi.org/project/opencensus-ext-azure/) :
  ```python
  pip install opencensus-ext-azure
  ```
* Vytvoření [instance Application Insights](../azure-monitor/app/opencensus-python.md) (Tento dokument obsahuje také informace o získání připojovacího řetězce pro prostředek)

## <a name="getting-started"></a>Začínáme

V této části je Úvod specifický pro použití OpenCensus z kanálu Azure Machine Learning. Podrobný kurz najdete v tématu [OpenCensus Azure monitor vývozců](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure) .

Přidejte PythonScriptStep do kanálu Azure ML. Nakonfigurujte své [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration?preserve-view=true&view=azure-ml-py) se závislostí na opencensus-EXT-Azure. Nakonfigurujte `APPLICATIONINSIGHTS_CONNECTION_STRING` proměnnou prostředí.

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

Vytvořte soubor s názvem `sample_step.py`. Importujte třídu AzureLogHandler pro směrování protokolů do Application Insights. Také budete muset importovat knihovnu protokolování Pythonu.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Dále přidejte AzureLogHandler do protokolovacího nástroje Python.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Protokolování s vlastními rozměry
 
Ve výchozím nastavení se protokoly předané do Application Insights nebudou mít dostatečný kontext pro trasování zpět ke spuštění nebo experimentování. Aby se protokoly daly dělat pro diagnostiku problémů, vyžadují se další pole. 

Chcete-li přidat tato pole, lze přidat vlastní dimenze k poskytnutí kontextu zprávy protokolu. Příkladem je, že někdo chce zobrazit protokoly v rámci jednoho spuštění kanálu v několika krocích.

Vlastní dimenze tvoří slovník párů klíč-hodnota (uložený jako řetězec, řetězec). Slovník se pak pošle Application Insights a zobrazí se jako sloupec ve výsledcích dotazu. Jednotlivé dimenze lze použít jako [parametry dotazu](#additional-helpful-queries).

### <a name="helpful-context-to-include"></a>Užitečný kontext, který se má zahrnout

| Pole                          | Důvod/příklad                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | Umožňuje dotazovat se na protokoly se stejnými parent_run_id pro zobrazení protokolů v čase pro všechny kroky, místo abyste museli podrobně do každého jednotlivého kroku.                                        |
| step_id                        | Může dotazovat se na protokoly se stejnými step_id, abyste viděli, kde došlo k potížím s úzkým rozsahem pouze k jednotlivým krokům.                                                        |
| step_name                      | Může pomocí dotazů na protokoly zobrazit krok v čase. Také pomáhá najít step_id pro poslední spuštění bez začnete do uživatelského rozhraní portálu.                                          |
| experiment_name                | Umožňuje dotazování napříč protokoly, aby se zobrazily informace o výkonu v průběhu času. Také pomáhá najít parent_run_id nebo step_id pro poslední spuštění bez začnete do uživatelského rozhraní portálu.                   |
| run_url                 | Může poskytnout odkaz přímo zpět ke spuštění šetření. |

**Další užitečná pole**

Tato pole mohou vyžadovat další instrumentaci kódu a nejsou poskytovány kontextem spuštění.

| Pole                   | Důvod/příklad                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | Pokud k nasazení použijete CI/CD, může toto pole koreluje s protokolem verze kódu, která poskytuje krok a logiku kanálu. Tento odkaz může další pomoc při diagnostice problémů nebo identifikaci modelů s konkrétními vlastnostmi (hodnoty protokolu/metriky). |
| run_type                       | Může rozlišovat různé typy modelů nebo školení vs. bodování spouští. |

### <a name="creating-a-custom-dimensions-dictionary"></a>Vytvoření vlastního slovníku dimenzí

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
logger.info("I will be sent to Application Insights with Custom Dimensions", extra= {"custom_dimensions":custom_dimensions})
```

## <a name="opencensus-python-logging-considerations"></a>OpenCensus požadavky na protokolování Pythonu

OpenCensus AzureLogHandler se používá k směrování protokolů Pythonu do Application Insights. V důsledku toho by se mělo vzít v úvahu drobné odlišnosti protokolování v jazyce Python. Při vytvoření protokolovacího nástroje má výchozí úroveň protokolování a zobrazí protokoly, které jsou větší nebo rovny této úrovni. Dobrým odkazem na použití funkcí protokolování Pythonu je [protokolování kuchařka](https://docs.python.org/3/howto/logging-cookbook.html).

`APPLICATIONINSIGHTS_CONNECTION_STRING`Pro knihovnu OpenCensus je potřeba proměnná prostředí. Doporučujeme nastavit tuto proměnnou prostředí, abyste ji nemuseli předávat jako parametr kanálu, aby nedocházelo k předávání řetězců připojení ve formátu prostého textu.

## <a name="querying-logs-in-application-insights"></a>Dotazování na protokoly v Application Insights

Protokoly směrované do Application Insights se zobrazí pod položkou Traces nebo Exceptions. Nezapomeňte upravit svůj časový interval, aby zahrnoval váš běh kanálu.

![Výsledek dotazu Application Insights](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Výsledek v Application Insights zobrazí zprávu protokolu a úroveň, cestu k souboru a číslo řádku kódu. Zobrazí se také všechny zahrnuté vlastní dimenze. V tomto obrázku customDimensions slovník zobrazuje páry klíč/hodnota z předchozí [ukázky kódu](#creating-a-custom-dimensions-dictionary).

### <a name="additional-helpful-queries"></a>Další užitečné dotazy

Některé z následujících dotazů používají ' customDimensions. Level '. Tyto úrovně závažnosti odpovídají úrovni, pomocí které byl protokol Python původně odeslán. Další informace o dotazech najdete v tématu [Azure monitor dotazů protokolu](/azure/data-explorer/kusto/query/).

| Případ použití                                                               | Dotaz                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Výsledky protokolu pro konkrétní vlastní dimenzi, například ' parent_run_id ' | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Výsledky protokolu pro všechny školicí běhy za posledních 7 dnů                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Protokoluje výsledky s severityLevel chybou za posledních 7 dnů.              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Počet výsledků protokolu s chybou severityLevel za posledních 7 dnů     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Další kroky

Jakmile budete mít v instanci Application Insights protokoly, dají se použít k nastavení [Azure monitor výstrahy](../azure-monitor/alerts/alerts-overview.md#what-you-can-alert-on) na základě výsledků dotazu.

Můžete také přidat výsledky z dotazů na [řídicí panel Azure](../azure-monitor/app/tutorial-app-dashboards.md#add-logs-query) pro další přehledy.
---
title: Monitorování aplikací v Pythonu pomocí Azure Monitor (Preview) | Microsoft Docs
description: Poskytuje pokyny pro vedení OpenCensus Pythonu pomocí Azure Monitor
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: b9d2bda1d3f01d2bf4bb152c0f62ade87bb61b4c
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368267"
---
# <a name="set-up-azure-monitor-for-your-python-application-preview"></a>Nastavení Azure Monitor pro aplikaci v Pythonu (Preview)

Azure Monitor podporuje distribuované trasování, shromažďování metrik a protokolování aplikací Pythonu prostřednictvím integrace s [OpenCensus](https://opencensus.io). Tento článek vás provede procesem nastavení OpenCensus pro Python a odeslání dat monitorování do Azure Monitor.

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Instalace Pythonu Tento článek používá [Python 3.7.0](https://www.python.org/downloads/), i když starší verze budou nejspíš fungovat s menšími změnami.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Portál Azure](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Vytvoření prostředku Application Insights v Azure Monitor

Nejdřív je potřeba vytvořit prostředek Application Insights v Azure Monitor, který vygeneruje klíč instrumentace (ikey). Ikey se pak použije ke konfiguraci sady OpenCensus SDK, aby odesílala data telemetrie Azure Monitor.

1. Vyberte **Vytvořit prostředek** > **Vývojářské nástroje** > **Application Insights**.

   ![Přidání prostředku Application Insights](./media/opencensus-python/0001-create-resource.png)

1. Zobrazí se konfigurační pole. K vyplnění vstupních polí použijte následující tabulku.

   | Nastavení        | Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | Globálně jedinečná hodnota | Název, který identifikuje aplikaci, kterou sledujete |
   | **Skupina prostředků**     | myResourceGroup      | Název nové skupiny prostředků pro hostování Application Insightsch dat |
   | **Umístění** | USA – východ | Umístění poblíž vaší oblasti nebo poblíž místa, kde je vaše aplikace hostována |

1. Vyberte **Create** (Vytvořit).

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumentace se sadou OpenCensus Python SDK pro Azure Monitor

Instalace OpenCensus Azure Monitor vývozců:

```console
python -m pip install opencensus-ext-azure
```

Úplný seznam balíčků a integrací najdete v tématu [OpenCensus Packages](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> Příkaz `python -m pip install opencensus-ext-azure` předpokládá, že máte nastavenou sadu proměnných `PATH` prostředí pro instalaci Pythonu. Pokud jste tuto proměnnou nenakonfigurovali, musíte zadat úplnou cestu k adresáři, kde se nachází spustitelný soubor Pythonu. Výsledkem je příkaz podobný tomuto: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

Sada SDK používá tři Azure Monitor vývozců k posílání různých typů telemetrie do Azure Monitor: trasování, metriky a protokoly. Další informace o těchto typech telemetrie najdete v tématu [Přehled datové platformy](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Pomocí následujících pokynů můžete odeslat tyto typy telemetrie prostřednictvím tří vývozců.

## <a name="telemetry-type-mappings"></a>Mapování typů telemetrie

Tady jsou vývozci, kteří OpenCensus poskytují mapování na typy telemetrie, které se zobrazí v Azure Monitor.

![Snímek obrazovky s mapováním typů telemetrie z OpenCensus na Azure Monitor](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Trasování

> [!NOTE]
> `Trace` v OpenCensus odkazuje na [distribuované trasování](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing). `AzureExporter` odesílá `requests` a `dependency` telemetrie do Azure Monitor.

1. Nejdřív vygenerujte data trasování místně. V Pythonu nečinné nebo v editoru podle vlastního výběru zadejte následující kód.

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2. Spuštění kódu opakovaně zobrazí výzvu k zadání hodnoty. U každé položky se hodnota vypíše do prostředí a modul Pythonu OpenCensus vygeneruje odpovídající část `SpanData`. Projekt OpenCensus definuje [trasování jako strom rozpětí](https://opencensus.io/core-concepts/tracing/).
    
    ```
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

3. I když zadáním hodnot je užitečné pro demonstrační účely, nakonec chceme vygenerovat `SpanData` k Azure Monitor. Předání připojovacího řetězce přímo do exportéra nebo ho můžete zadat v proměnné prostředí `APPLICATIONINSIGHTS_CONNECTION_STRING`. Upravte kód z předchozího kroku na základě následujícího příkladu kódu:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

4. Když teď spouštíte skript Pythonu, měli byste být pořád vyzváni k zadání hodnot, ale v prostředí se tiskne jenom hodnota. Vytvořený `SpanData` bude odeslán do Azure Monitor. Data emitovaného rozsahu můžete najít v části `dependencies`.

5. Informace o vzorkování v OpenCensus se podíváme na [vzorkování v OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

6. Podrobnosti o korelaci telemetrie v datech trasování najdete v [korelaci telemetrie](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)OpenCensus.

### <a name="metrics"></a>Metriky

1. Nejdřív vygenerujeme některá místní data metriky. Vytvoříme jednoduchou metriku pro sledování počtu, kolikrát uživatel stiskne klávesu ENTER.

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
2. Spuštění kódu opakovaně zobrazí výzvu k stisknutí klávesy ENTER. Je vytvořena metrika, která sleduje počet stisknutí klávesy ENTER. U každé položky se hodnota zvýší a v konzole se zobrazí informace o metrikě. Informace obsahují aktuální hodnotu a aktuální časové razítko při aktualizaci metriky.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. I když zadáním hodnot je užitečné pro demonstrační účely, nakonec chceme vygenerovat data metriky pro Azure Monitor. Předání připojovacího řetězce přímo do exportéra nebo ho můžete zadat v proměnné prostředí `APPLICATIONINSIGHTS_CONNECTION_STRING`. Upravte kód z předchozího kroku na základě následujícího příkladu kódu:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

4. Exportér pošle data metriky Azure Monitor v pevném intervalu. Výchozí hodnota je každých 15 sekund. Sledujeme jednu metriku, takže tato data metriky s libovolným hodnotou a časovým razítkem, které obsahuje, se budou posílat každý interval. Data můžete najít v části `customMetrics`.

### <a name="logs"></a>Protokoly

1. Nejdřív vygenerujeme některá místní data protokolu.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2.  Kód se průběžně vyžádá, aby se zadala hodnota. Záznam protokolu je vygenerován pro každou zadanou hodnotu.

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. I když zadáním hodnot je užitečné pro demonstrační účely, nakonec chceme vygenerovat data protokolu pro Azure Monitor. Předání připojovacího řetězce přímo do exportéra nebo ho můžete zadat v proměnné prostředí `APPLICATIONINSIGHTS_CONNECTION_STRING`. Upravte kód z předchozího kroku na základě následujícího příkladu kódu:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

4. Exportér odešle data protokolu do Azure Monitor. Data můžete najít v části `traces`. 

> [!NOTE]
> `traces` v tomto kontextu nejsou stejné jako `Tracing`. `traces` odkazuje na typ telemetrie, který se zobrazí v Azure Monitor při použití `AzureLogHandler`. `Tracing` odkazuje na koncept v OpenCensus a má vztah k [distribuovanému trasování](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing).

5. Pokud chcete naformátovat zprávy protokolu, můžete použít `formatters` v integrovaném [rozhraní API pro protokolování](https://docs.python.org/3/library/logging.html#formatter-objects)Pythonu.

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    format_str = '%(asctime)s - %(levelname)-8s - %(message)s'
    date_format = '%Y-%m-%d %H:%M:%S'
    formatter = logging.Formatter(format_str, date_format)
    # TODO: replace the all-zero GUID with your instrumentation key.
    handler = AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

6. Pomocí pole custom_dimensions můžete do zpráv protokolu přidat také vlastní vlastnosti v argumentu klíčového slova *extra* . Ty se zobrazí jako páry klíč-hodnota v `customDimensions` v Azure Monitor.
> [!NOTE]
> Aby tato funkce fungovala, musíte do pole custom_dimensions předat slovník. Pokud předáte argumenty jiného typu, protokolovací nástroj je bude ignorovat.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)

    # Use properties in exception logs
    try:
        result = 1 / 0  # generate a ZeroDivisionError
    except Exception:
    logger.exception('Captured an exception.', extra=properties)
    ```

7. Podrobnosti o tom, jak rozšířit protokoly pomocí dat kontextu trasování, najdete v tématu OpenCensus Python [logs Integration](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation).

## <a name="view-your-data-with-queries"></a>Zobrazení dat pomocí dotazů

Data telemetrie, která byla odeslána z vaší aplikace, můžete zobrazit prostřednictvím karty **protokoly (Analytics)** .

![Snímek obrazovky s podoknem přehled se zvolenými možnostmi protokoly (Analytics) v červeném poli](./media/opencensus-python/0010-logs-query.png)

V seznamu pod položkou **aktivní**:

- U telemetrie odeslaných pomocí Azure Monitorho vývozce trasování se příchozí žádosti zobrazí v části `requests`. Odchozí nebo vnitroprocesové požadavky se zobrazují v části `dependencies`.
- V případě telemetrie odeslaných pomocí Azure Monitorů pro export metrik se v části `customMetrics`zobrazí metriky.
- U telemetrie odesílaných pomocí Azure Monitorch protokolů se zobrazí protokoly v části `traces`. Výjimky se zobrazí v části `exceptions`.

Podrobnější informace o používání dotazů a protokolů najdete [v tématu protokoly v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>Další informace o OpenCensus pro Python

* [OpenCensus Python na GitHubu](https://github.com/census-instrumentation/opencensus-python)
* [Uživatelských](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Integrace na baňce](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Integrace Django](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [Integrace MySQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Další kroky

* [Mapa aplikace](./../../azure-monitor/app/app-map.md)
* [Monitorování výkonu na konci](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Výstrahy

* [Testy dostupnosti:](../../azure-monitor/app/monitor-web-app-availability.md) Vytvářejte testy, abyste ověřili viditelnost svého webu na internetu.
* [Inteligentní diagnostika:](../../azure-monitor/app/proactive-diagnostics.md) Tyto testy se spouštějí automaticky, takže je nemusíte nijak nastavovat. Upozorní vás, pokud má aplikace nezvykle velký podíl neúspěšných požadavků.
* [Výstrahy metriky](../../azure-monitor/app/alerts.md): Nastavte výstrahy, které vás upozorní, pokud metrika překračuje prahovou hodnotu. Upozornění můžete nastavit u vlastních metrik, které v aplikaci naprogramujete.

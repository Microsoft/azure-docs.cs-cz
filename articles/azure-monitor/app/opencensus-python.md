---
title: Monitorování aplikací pythonu pomocí Azure Monitoru (preview) | Dokumenty společnosti Microsoft
description: Obsahuje pokyny k drátu OpenCensus Python s Azure Monitor
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6ef0675e3ae3f7a5da38138177f3033051723411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537104"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Nastavení Azure Monitoru pro aplikaci Pythonu

Azure Monitor podporuje distribuované trasování, shromažďování metrik a protokolování aplikací Pythonu prostřednictvím integrace s [OpenCensus](https://opencensus.io). Tento článek vás provede procesem nastavení OpenCensus pro Python a odesílání dat monitorování do Azure Monitoru.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
- Instalace Pythonu. Tento článek používá [Python 3.7.0](https://www.python.org/downloads/), i když starší verze budou pravděpodobně fungovat s menšími změnami.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Vytvoření prostředku Přehledy aplikací v Azure Monitoru

Nejprve musíte vytvořit prostředek Application Insights ve službě Azure Monitor, který vygeneruje klíč instrumentace (ikey). Ikey se pak používá ke konfiguraci OpenCensus SDK pro odesílání telemetrických dat do Azure Monitoru.

1. Vyberte **Vytvořit nástroje** > **Developer tools** > pro vývojáře prostředků Application**Insights**.

   ![Přidání prostředku Application Insights](./media/opencensus-python/0001-create-resource.png)

1. Zobrazí se konfigurační pole. Pomocí následující tabulky vyplňte vstupní pole.

   | Nastavení        | Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | Globálně jedinečná hodnota | Název, který identifikuje aplikaci, kterou sledujete |
   | **Skupina prostředků**     | myResourceGroup      | Název nové skupiny prostředků pro hostování dat Application Insights |
   | **Umístění** | USA – východ | Poloha ve vašem okolí nebo poblíž místa, kde je aplikace hostovaná |

1. Vyberte **Vytvořit**.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Nástroj s opencensus Python SDK pro Azure Monitor

Nainstalujte exportéry programu OpenCensus Azure Monitor:

```console
python -m pip install opencensus-ext-azure
```

Úplný seznam balíčků a integrací naleznete [v tématu OpenCensus packages](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> Příkaz `python -m pip install opencensus-ext-azure` předpokládá, že máte `PATH` proměnnou prostředí nastavenou pro instalaci Pythonu. Pokud jste tuto proměnnou nenakonfigurovali, musíte poskytnout úplnou cestu k adresáři, kde se nachází váš spustitelný soubor Pythonu. Výsledkem je příkaz, `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`jako je tento: .

Sada SDK používá tři exportéry Azure Monitor u odesílání různých typů telemetrie do Azure Monitoru: trasování, metriky a protokoly. Další informace o těchto typech telemetrie naleznete [v přehledu datové platformy](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Pomocí následujících pokynů odeslat tyto typy telemetrie prostřednictvím tří vývozců.

## <a name="telemetry-type-mappings"></a>Mapování typu telemetrie

Tady jsou exportéři, které OpenCensus poskytuje mapované na typy telemetrie, které se zobrazí v Azure Monitor.

![Snímek obrazovky s mapováním typů telemetrie z OpenCensus na Azure Monitor](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Trasování

> [!NOTE]
> `Trace`v OpenCensus odkazuje na [distribuované trasování](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing). `AzureExporter` Odesílá `requests` `dependency` a telemetrie do Azure Monitoru.

1. Nejprve vygenerujeme některá data trasování místně. V aplikaci Nečinnosti v Pythonu nebo v editoru zadejte následující kód.

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

2. Spuštění kódu vás opakovaně vyzve k zadání hodnoty. S každou položkou bude hodnota vytištěna do prostředí a modul OpenCensus Python `SpanData`vygeneruje odpovídající část . Projekt OpenCensus definuje [trasování jako strom rozpětí](https://opencensus.io/core-concepts/tracing/).
    
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

3. I když zadávání hodnot je užitečné pro demonstrační `SpanData` účely, nakonec chceme vyzařovat do Azure Monitoru. Předejte připojovací řetězec přímo exportérovi nebo `APPLICATIONINSIGHTS_CONNECTION_STRING`jej můžete zadat v proměnné prostředí . Upravte kód z předchozího kroku na základě následující ukázky kódu:

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

4. Nyní, když spustíte skript Pythonu, měli byste být stále vyzváni k zadání hodnot, ale pouze hodnota je vytištěna v prostředí. Vytvořené `SpanData` se odešlou do Azure Monitoru. Data vyzařovaného rozsahu naleznete v `dependencies`části . Další podrobnosti o odchozích požadavcích naleznete v tématu OpenCensus Python [dependencies](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency).
Další podrobnosti o příchozích požadavcích naleznete v tématu OpenCensus Python [requests](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request).

#### <a name="sampling"></a>Vzorkování

Informace o odběru vzorků v opencensus, podívejte se na [vzorkování v OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Trasová korelace

Podrobnosti o korelaci telemetrie v datech trasování se podívejte na [telemetrickou korelaci OpenCensus](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)Python .

#### <a name="modify-telemetry"></a>Úprava telemetrie

Podrobnosti o tom, jak upravit sledovaná telemetrická data před odesláním do Azure Monitoru, najdete v [tématu OpenCensus Python telemetrie procesory](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors).

### <a name="metrics"></a>Metriky

1. Nejprve vygenerujme některá místní metrická data. Vytvoříme jednoduchou metriku, která sleduje, kolikrát uživatel stiskne enter.

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
2. Spuštění kódu vás opakovaně vyzve ke stisknutí klávesy Enter. Metrika je vytvořena ke sledování počtu stisknutí enteru. S každou položkou se hodnota zvýší a informace o metrikách se zobrazí v konzole. Informace zahrnují aktuální hodnotu a aktuální časové razítko při aktualizaci metriky.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. I když zadávání hodnot je užitečné pro demonstrační účely, nakonec chceme vypouštět metrická data do Azure Monitoru. Předejte připojovací řetězec přímo exportérovi nebo `APPLICATIONINSIGHTS_CONNECTION_STRING`jej můžete zadat v proměnné prostředí . Upravte kód z předchozího kroku na základě následující ukázky kódu:

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

4. Exportér odešle metrická data do Azure Monitoru v pevném intervalu. Výchozí hodnota je každých 15 sekund. Sledujeme jednu metriku, takže tato metrická data s jakoukoli hodnotou a časovým razítkem, které obsahuje, budou odesílána v každém intervalu. Data najdete v `customMetrics`části .

#### <a name="standard-metrics"></a>Standardní metriky

Ve výchozím nastavení exportér metrik odešle sadu standardních metrik do Azure Monitoru. Můžete zakázat nastavením `enable_standard_metrics` příznak `False` u konstruktoru exportéru metriky.

    ```python
    ...
    exporter = metrics_exporter.new_metrics_exporter(
      enable_standard_metrics=False,
      connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    ...
    ```
Níže je uveden seznam standardních metrik, které jsou aktuálně odesílány:

- Dostupná paměť (bajty)
- Čas procesoru PROCESORU (procento)
- Míra příchozích požadavků (za sekundu)
- Průměrná doba spuštění příchozích požadavků (milisekundy)
- Míra odchozích požadavků (za sekundu)
- Využití procesoru procesu (procento)
- Zpracovat soukromé bajty (bajty)

Tyto metriky byste měli vidět `performanceCounters`v . Míra příchozích požadavků `customMetrics`by byla pod . Další informace naleznete v [tématu čítače výkonu](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters).

#### <a name="modify-telemetry"></a>Úprava telemetrie

Podrobnosti o tom, jak upravit sledovaná telemetrická data před odesláním do Azure Monitoru, najdete v [tématu OpenCensus Python telemetrie procesory](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors).

### <a name="logs"></a>Protokoly

1. Nejprve vygenerujme některá data místního protokolu.

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

2.  Kód bude průběžně požadovat zadání hodnoty. Položka protokolu je vyzařována pro každou zadanou hodnotu.

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

3. I když zadávání hodnot je užitečné pro účely demonstrace, nakonec chceme vyzařovat data protokolu do Azure Monitoru. Předejte připojovací řetězec přímo exportérovi nebo `APPLICATIONINSIGHTS_CONNECTION_STRING`jej můžete zadat v proměnné prostředí . Upravte kód z předchozího kroku na základě následující ukázky kódu:

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

4. Exportér odešle data protokolu do Azure Monitoru. Data najdete v `traces`části . 

> [!NOTE]
> `traces`v této souvislosti není `Tracing`totéž jako . `traces`odkazuje na typ telemetrie, které se zobrazí v `AzureLogHandler`Azure Monitor při využití . `Tracing`odkazuje na pojem v OpenCensus a týká se [distribuovaného trasování](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing).

5. Chcete-li formátovat zprávy `formatters` protokolu, můžete použít v integrovaném [rozhraní API pro protokolování pythonu](https://docs.python.org/3/library/logging.html#formatter-objects).

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

6. Můžete také přidat vlastní vlastnosti zprávy protokolu v argumentu *extra* klíčové slovo pomocí pole custom_dimensions. Ty se zobrazí jako páry `customDimensions` klíč hodnota v Azure Monitoru.
> [!NOTE]
> Aby tato funkce fungovala, musíte předat slovník do pole custom_dimensions. Pokud předáte argumenty jiného typu, protokolovací protokol je bude ignorovat.

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
    ```

#### <a name="sending-exceptions"></a>Odesílání výjimek

OpenCensus Python automaticky nesleduje `exception` a neodesílá telemetrii. Jsou odesílány `AzureLogHandler` prostřednictvím pomocí výjimky prostřednictvím knihovny protokolování Pythonu. Vlastní vlastnosti můžete přidat stejně jako při normálním protokolování.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in exception logs
    try:
        result = 1 / 0  # generate a ZeroDivisionError
    except Exception:
        logger.exception('Captured an exception.', extra=properties)
    ```
Vzhledem k tomu, že je nutné explicitně protokolovat výjimky, je na uživateli v tom, jak chtějí protokolovat neošetřené výjimky. OpenCensus neumístí omezení v tom, jak to chce uživatel provést, pokud explicitně protokolují telemetrii výjimky.

#### <a name="sampling"></a>Vzorkování

Informace o odběru vzorků v opencensus, podívejte se na [vzorkování v OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Korelace protokolů

Podrobnosti o tom, jak obohatit protokoly s daty kontextu trasování, naleznete v tématu OpenCensus Python [protokoly integrace](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation).

#### <a name="modify-telemetry"></a>Úprava telemetrie

Podrobnosti o tom, jak upravit sledovaná telemetrická data před odesláním do Azure Monitoru, najdete v [tématu OpenCensus Python telemetrie procesory](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors).

## <a name="view-your-data-with-queries"></a>Zobrazení dat pomocí dotazů

Telemetrická data odeslaná z vaší aplikace můžete zobrazit na kartě **Protokoly (Analytics).**

![Snímek obrazovky podokna přehledu s vybranou možností Protokoly (Analytics) v červeném poli](./media/opencensus-python/0010-logs-query.png)

V seznamu **aktivní**:

- Pro telemetrii odeslanou s exportérem `requests`trasování Azure Monitoru se příchozí požadavky zobrazí v části . Odchozí nebo v rámci procesu `dependencies`požadavky se zobrazí v části .
- U telemetrie odeslané s vývozcem metrik `customMetrics`Azure Monitoru se metriky odeslané zobrazí v části .
- Pro telemetrie odeslané s vývozcem protokolů Azure Monitor protokoly protokoly se zobrazí v části `traces`. Výjimky se `exceptions`zobrazí v části .

Podrobnější informace o použití dotazů a protokolů najdete [v tématu protokoly v Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>Další informace o OpenCensus pro Python

* [OpenCensus Python na GitHubu](https://github.com/census-instrumentation/opencensus-python)
* [Přizpůsobení](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Exportéři monitorů Azure na GitHubu](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [Integrace opencensus](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Ukázkové aplikace Azure Monitor](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Další kroky

* [Sledování příchozích požadavků](./../../azure-monitor/app/opencensus-python-dependency.md)
* [Sledování odchozích požadavků](./../../azure-monitor/app/opencensus-python-request.md)
* [Mapa aplikace](./../../azure-monitor/app/app-map.md)
* [Komplexní sledování výkonu](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Výstrahy

* [Testy dostupnosti:](../../azure-monitor/app/monitor-web-app-availability.md) Vytvářejte testy, abyste ověřili viditelnost svého webu na internetu.
* [Inteligentní diagnostika:](../../azure-monitor/app/proactive-diagnostics.md) Tyto testy se spouštějí automaticky, takže je nemusíte nijak nastavovat. Upozorní vás, pokud má aplikace nezvykle velký podíl neúspěšných požadavků.
* [Upozornění na metriky](../../azure-monitor/app/alerts.md): Nastavte výstrahy, které vás upozorní, pokud metrika překročí prahovou hodnotu. Upozornění můžete nastavit u vlastních metrik, které v aplikaci naprogramujete.

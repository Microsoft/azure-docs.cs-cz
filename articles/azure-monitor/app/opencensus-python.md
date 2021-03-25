---
title: Monitorování aplikací v Pythonu pomocí Azure Monitor | Microsoft Docs
description: Poskytuje pokyny pro vedení OpenCensus Pythonu pomocí Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2020
ms.reviewer: mbullwin
ms.custom: devx-track-python
ms.openlocfilehash: 69472da4f774a1dfae86e1891255907ad711175a
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047418"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Nastavení Azure Monitor pro aplikaci Python

Azure Monitor podporuje distribuované trasování, shromažďování metrik a protokolování aplikací Pythonu prostřednictvím integrace s [OpenCensus](https://opencensus.io). Tento článek vás provede procesem nastavení OpenCensus pro Python a odeslání dat monitorování do Azure Monitor.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).
- Instalace Pythonu Tento článek používá [Python 3.7.0](https://www.python.org/downloads/release/python-370/), i když jiné verze budou nejspíš fungovat s menšími změnami. Sada SDK podporuje pouze Python verze 2,7 a 3.6 +.
- Vytvořte [prostředek](./create-new-resource.md)Application Insights. K vašemu prostředku budete mít přiřazený vlastní klíč instrumentace (ikey).

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumentace se sadou OpenCensus Python SDK pro Azure Monitor

Instalace OpenCensus Azure Monitor vývozců:

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> `python -m pip install opencensus-ext-azure`Příkaz předpokládá, že máte `PATH` nastavenou proměnnou prostředí pro instalaci Pythonu. Pokud jste tuto proměnnou nenakonfigurovali, musíte zadat úplnou cestu k adresáři, kde se nachází spustitelný soubor Pythonu. Výsledkem je příkaz podobný tomuto: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure` .

Sada SDK používá tři Azure Monitor vývozců k posílání různých typů telemetrie do Azure Monitor. Jedná se o trasování, metriky a protokoly. Další informace o těchto typech telemetrie najdete v tématu [Přehled datové platformy](../data-platform.md). Pomocí následujících pokynů můžete odeslat tyto typy telemetrie prostřednictvím tří vývozců.

## <a name="telemetry-type-mappings"></a>Mapování typů telemetrie

Tady jsou vývozci, kteří OpenCensus poskytují mapování na typy telemetrie, které vidíte v Azure Monitor.

| Pilíř pro pozorování | Typ telemetrie v Azure Monitor    | Vysvětlení                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| Protokoly                    | Trasování, výjimky, customEvents   | Telemetrie protokolů, telemetrie výjimek, telemetrie událostí |
| Metriky                 | customMetrics, čítače výkonu | Čítače výkonu vlastních metrik                |
| Trasování                 | Požadavky na závislosti             | Příchozí požadavky, odchozí požadavky                |

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

1. Kód průběžně žádá o zadání hodnoty. Záznam protokolu je vygenerován pro každou zadanou hodnotu.

    ```output
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

1. I když zadáním hodnot je užitečné pro demonstrační účely, nakonec chceme vygenerovat data protokolu pro Azure Monitor. Předání připojovacího řetězce přímo do vývozce. Nebo ji můžete zadat v proměnné prostředí `APPLICATIONINSIGHTS_CONNECTION_STRING` . Upravte kód z předchozího kroku na základě následujícího příkladu kódu:

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

1. Exportér odesílá data protokolu do Azure Monitor. Data můžete najít v části `traces` . 

    > [!NOTE]
    > V tomto kontextu `traces` není stejný jako `tracing` . Tady `traces` odkazuje na typ telemetrie, který se zobrazí v Azure monitor při využití `AzureLogHandler` . Ale `tracing` odkazuje na koncept v OpenCensus a vztahuje se na [distribuované trasování](./distributed-tracing.md).

    > [!NOTE]
    > Kořenový protokolovací nástroj je nakonfigurovaný s úrovní upozornění. To znamená, že všechny protokoly, které odesíláte méně závažnosti, se ignorují a pak se nebudou odesílat do Azure Monitor. Další informace najdete v [dokumentaci](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel).

1. Pomocí pole custom_dimensions můžete do svých zpráv protokolu přidat také vlastní vlastnosti v argumentu klíčového slova *extra* . Tyto vlastnosti se zobrazí jako páry klíč-hodnota v `customDimensions` v Azure monitor.
    > [!NOTE]
    > Aby tato funkce fungovala, musíte do pole custom_dimensions předat slovník. Pokud předáte argumenty jiného typu, protokolovací nástroj je ignoruje.

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

#### <a name="configure-logging-for-django-applications"></a>Konfigurace protokolování pro aplikace Django

Protokolování můžete nakonfigurovat explicitně v kódu aplikace, jak je uvedeno výše pro aplikace Django, nebo můžete zadat v konfiguraci protokolování Django. Tento kód může přejít do libovolného souboru, který používáte pro konfiguraci nastavení Django. Postup konfigurace nastavení Django najdete v tématu [Nastavení Django](https://docs.djangoproject.com/en/3.0/topics/settings/). Další informace o konfiguraci protokolování najdete v tématu [protokolování Django](https://docs.djangoproject.com/en/3.0/topics/logging/).

```json
LOGGING = {
    "handlers": {
        "azure": {
            "level": "DEBUG",
        "class": "opencensus.ext.azure.log_exporter.AzureLogHandler",
            "instrumentation_key": "<your-ikey-here>",
         },
        "console": {
            "level": "DEBUG",
            "class": "logging.StreamHandler",
            "stream": sys.stdout,
         },
      },
    "loggers": {
        "logger_name": {"handlers": ["azure", "console"]},
    },
}
```

Ujistěte se, že používáte protokolovací nástroj se stejným názvem, jaký je zadaný ve vaší konfiguraci.

```python
import logging

logger = logging.getLogger("logger_name")
logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>Odeslat výjimky

OpenCensus Python automaticky nesleduje a neposílá `exception` telemetrii. Odesílají se prostřednictvím `AzureLogHandler` pomocí výjimek prostřednictvím knihovny protokolování Pythonu. Vlastní vlastnosti můžete přidat stejně jako u normálního protokolování.

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
Vzhledem k tomu, že je nutné výjimky protokolovat explicitně, je až uživateli, jak chce protokolovat neošetřené výjimky. OpenCensus neumísťuje omezení na to, jak to uživatel chce udělat, pokud explicitně protokolují telemetrii výjimek.

#### <a name="send-events"></a>Odesílání událostí

Telemetrii můžete poslat `customEvent` přesně stejným způsobem jako `trace` telemetrii, s výjimkou použití `AzureEventHandler` místo toho.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>Vzorkování

Informace o vzorkování v OpenCensus se podíváme na [vzorkování v OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Korelace protokolů

Podrobnosti o tom, jak rozšířit protokoly pomocí dat kontextu trasování, najdete v tématu OpenCensus Python [logs Integration](./correlation.md#log-correlation).

#### <a name="modify-telemetry"></a>Úprava telemetrie

Podrobnosti o tom, jak upravit sledovanou telemetrii před odesláním do Azure Monitor, najdete v tématu OpenCensus Python [telemetrie](./api-filtering-sampling.md#opencensus-python-telemetry-processors).


### <a name="metrics"></a>Metriky

OpenCensus. stats podporuje 4 agregační metody, ale poskytuje částečnou podporu pro Azure Monitor:

- **Počet:** Počet bodů měření. Hodnota je kumulativní, může se zvýšit a nastavit na hodnotu 0 při restartu. 
- **Součet:** Součet bodů měření. Hodnota je kumulativní, může se zvýšit a nastavit na hodnotu 0 při restartu. 
- **LastValue:** Uchová poslední zaznamenanou hodnotu a všechno ostatní.
- **Distribuce:** Rozdělení histogramu bodů měření. Tato metoda není **nástrojem Azure Exportér podporována**.

### <a name="count-aggregation-example"></a>Příklad agregace Count

1. Nejdřív vygenerujeme některá místní data metriky. Vytvoříme jednoduchou metriku pro sledování počtu, kolikrát uživatel vybere klíč **ENTER** .

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
1. Spuštění kódu opakovaně vyzve k výběru **ENTER**. Vytvoří se metrika, která sleduje počet vybraných časů **zadání** . U každé položky se hodnota zvýší a v konzole se zobrazí informace o metrikách. Informace obsahují aktuální hodnotu a aktuální časové razítko při aktualizaci metriky.

    ```output
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. I když zadáním hodnot je užitečné pro demonstrační účely, nakonec chceme vygenerovat data metriky pro Azure Monitor. Předání připojovacího řetězce přímo do vývozce. Nebo ji můžete zadat v proměnné prostředí `APPLICATIONINSIGHTS_CONNECTION_STRING` . Upravte kód z předchozího kroku na základě následujícího příkladu kódu:

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

1. Exportér posílá data metriky Azure Monitor v pevném intervalu. Výchozí hodnota je každých 15 sekund. Sledujeme jednu metriku, takže tato data metriky s libovolným hodnotou a časovým razítkem, které obsahuje, se odesílají každý interval. Hodnota je kumulativní, může se zvýšit a nastavit na hodnotu 0 při restartu. Data můžete najít v části `customMetrics` , ale `customMetrics` vlastnosti ValueCount, ValueSum, ValueMin, ValueMax a valueStdDev se nepoužívají efektivně.

#### <a name="performance-counters"></a>Čítače výkonu

Ve výchozím nastavení odesílá Exportér metrik sadu čítačů výkonu pro Azure Monitor. Tuto možnost můžete zakázat nastavením `enable_standard_metrics` příznaku na `False` v konstruktoru pro exportéra metrik.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

V současné době jsou odesílány tyto čítače výkonu:

- Dostupná paměť (bajty)
- Čas procesoru procesoru (v procentech)
- Míra příchozích požadavků (za sekundu)
- Průměrná doba provádění příchozího požadavku (milisekundy)
- Využití procesoru procesem (procento)
- Zpracovat soukromé bajty (bajty)

Tyto metriky byste měli být schopní zobrazit v `performanceCounters` . Další informace najdete v tématu [čítače výkonu](./performance-counters.md).

#### <a name="modify-telemetry"></a>Úprava telemetrie

Informace o tom, jak upravit sledovanou telemetrii před odesláním do Azure Monitor, najdete v tématu OpenCensus [telemetrie](./api-filtering-sampling.md#opencensus-python-telemetry-processors)Python.

### <a name="tracing"></a>Trasování

> [!NOTE]
> V OpenCensus `tracing` odkazuje na [distribuované trasování](./distributed-tracing.md). `AzureExporter`Odeslání `requests` a `dependency` telemetrie Azure monitor.

1. Nejdřív vygenerujte data trasování místně. V Pythonu nečinné nebo v editoru podle vlastního výběru zadejte následující kód:

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

1. Spuštění kódu opakovaně vyzve k zadání hodnoty. U každé položky se hodnota vytiskne do prostředí. Modul Python OpenCensus vygeneruje odpovídající část `SpanData` . Projekt OpenCensus definuje [trasování jako strom rozpětí](https://opencensus.io/core-concepts/tracing/).
    
    ```output
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

1. I když je zadání hodnot užitečné pro demonstrační účely, nakonec chceme vygenerovat `SpanData` Azure monitor. Předání připojovacího řetězce přímo do vývozce. Nebo ji můžete zadat v proměnné prostředí `APPLICATIONINSIGHTS_CONNECTION_STRING` . Upravte kód z předchozího kroku na základě následujícího příkladu kódu:

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

1. Když teď spouštíte skript Pythonu, měli byste být pořád vyzváni k zadání hodnot, ale v prostředí se tiskne jenom hodnota. Vytvořená `SpanData` je odeslána do Azure monitor. Data emitovaného rozsahu najdete v části `dependencies` . Další informace o odchozích žádostech najdete v tématu OpenCensus [v Pythonu](./opencensus-python-dependency.md).
Další informace o příchozích požadavcích naleznete v tématu OpenCensus Python [requests](./opencensus-python-request.md).

#### <a name="sampling"></a>Vzorkování

Informace o vzorkování v OpenCensus se podíváme na [vzorkování v OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Korelace trasování

Další informace o korelaci telemetrie v datech trasování najdete v OpenCensus [korelace telemetrie](./correlation.md#telemetry-correlation-in-opencensus-python)Pythonu.

#### <a name="modify-telemetry"></a>Úprava telemetrie

Další informace o tom, jak upravit sledovanou telemetrii před odesláním do Azure Monitor, najdete v tématu OpenCensus Python [telemetrie](./api-filtering-sampling.md#opencensus-python-telemetry-processors).

## <a name="configure-azure-monitor-exporters"></a>Konfigurace Azure Monitor vývozců

Jak je znázorněno, existují tři různé Azure Monitor vývozců, kteří podporují OpenCensus. Každé z nich odesílá různé typy telemetrie Azure Monitor. Chcete-li zjistit, jaké typy telemetrie každý vývozce odesílá, přečtěte si následující seznam.

Každý vývozce přijímá stejné argumenty pro konfiguraci a předává je prostřednictvím konstruktorů. Podrobnosti o každé z nich můžete zobrazit tady:

- `connection_string`: Připojovací řetězec, který se používá pro připojení k vašemu Azure Monitor prostředku. Má přednost před `instrumentation_key` .
- `enable_standard_metrics`: Používá se pro `AzureMetricsExporter` . Signalizuje, že vývozce automaticky pošle metriky [čítače výkonu](../essentials/app-insights-metrics.md#performance-counters) Azure monitor. Výchozí hodnota je `True` .
- `export_interval`: Používá se k určení frekvence v sekundách exportu.
- `instrumentation_key`: Klíč instrumentace, který se používá pro připojení k vašemu Azure Monitor prostředku.
- `logging_sampling_rate`: Používá se pro `AzureLogHandler` . Poskytuje vzorkovací frekvenci [0, 1,0] pro export protokolů. Výchozí hodnota je 1,0.
- `max_batch_size`: Určuje maximální velikost telemetrie, která je exportována najednou.
- `proxies`: Určuje sekvenci proxy serverů, které se mají použít k odesílání dat do Azure Monitor. Další informace najdete v tématu [proxy](https://requests.readthedocs.io/en/master/user/advanced/#proxies).
- `storage_path`: Cesta k umístění místní složky úložiště (neodeslané telemetrie). Jako `opencensus-ext-azure` v 1.0.3 je výchozí cesta dočasným adresářem operačního systému + `opencensus-python`  +  `your-ikey` . Před až v 1.0.3 je výchozí cesta $USER + `.opencensus`  +  `.azure`  +  `python-file-name` .

## <a name="view-your-data-with-queries"></a>Zobrazení dat pomocí dotazů

Data telemetrie, která byla odeslána z vaší aplikace, můžete zobrazit prostřednictvím karty **protokoly (Analytics)** .

![Snímek obrazovky s podoknem přehled se zvolenými možnostmi protokoly (Analytics) v červeném poli](./media/opencensus-python/0010-logs-query.png)

V seznamu pod položkou **aktivní**:

- U telemetrie odeslaných pomocí Azure Monitorho vývozce trasování se příchozí požadavky zobrazí v části `requests` . Odchozí nebo vnitroprocesové požadavky se zobrazí v části `dependencies` .
- U telemetrie odesílaných pomocí Azure Monitorch metriky se v části zobrazuje metrika `customMetrics` .
- U telemetrie odesílaných pomocí nástroje Azure Monitorch protokolů se zobrazí protokoly v části `traces` . Výjimky se zobrazí v části `exceptions` .

Podrobnější informace o používání dotazů a protokolů najdete [v tématu protokoly v Azure monitor](../logs/data-platform-logs.md).

## <a name="learn-more-about-opencensus-for-python"></a>Další informace o OpenCensus pro Python

* [OpenCensus Python na GitHubu](https://github.com/census-instrumentation/opencensus-python)
* [Přizpůsobení](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Azure Monitor vývozců na GitHubu](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [Integrace OpenCensus](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Ukázkové aplikace Azure Monitor](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Další kroky

* [Sledování příchozích požadavků](./opencensus-python-dependency.md)
* [Sledování požadavků](./opencensus-python-request.md)
* [Mapa aplikace](./app-map.md)
* [Monitorování výkonu na konci](../app/tutorial-performance.md)

### <a name="alerts"></a>Výstrahy

* [Testy dostupnosti:](./monitor-web-app-availability.md) Vytvářejte testy, abyste ověřili viditelnost svého webu na internetu.
* [Inteligentní diagnostika:](./proactive-diagnostics.md) Tyto testy se spouštějí automaticky, takže je nemusíte nijak nastavovat. Upozorní vás, pokud má aplikace nezvykle velký podíl neúspěšných požadavků.
* [Výstrahy metriky](../alerts/alerts-log.md): Nastavte výstrahy, které vás upozorní, pokud metrika překračuje prahovou hodnotu. Upozornění můžete nastavit u vlastních metrik, které v aplikaci naprogramujete.


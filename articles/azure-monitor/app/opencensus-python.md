---
title: Monitorování aplikací v Pythonu pomocí Azure Monitor | Microsoft Docs
description: Poskytuje pokyny pro vedení OpenCensus Pythonu pomocí Azure Monitor
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 1316cf6808f6ccfc4165ad162c51421638b130be
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294005"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Nastavení Azure Monitor pro aplikaci Python

Azure Monitor podporuje distribuované trasování, shromažďování metrik a protokolování aplikací Pythonu prostřednictvím integrace s [OpenCensus](https://opencensus.io). Tento článek vás provede procesem nastavení OpenCensus pro Python a získání dat monitorování pro Azure Monitor.

## <a name="prerequisites"></a>Požadavky

- Potřebujete předplatné Azure.
- Python by měl být nainstalovaný, v tomto článku se používá [Python 3.7.0](https://www.python.org/downloads/), i když starší verze budou pravděpodobně fungovat s menšími úpravami.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlaste se k Azure Portal

Přihlaste se k [Azure Portal](https://portal.azure.com/).

## <a name="create-application-insights-resource-in-azure-monitor"></a>Vytvořit prostředek Application Insights v Azure Monitor

Nejdřív je potřeba vytvořit prostředek Application Insights v Azure Monitor, který vygeneruje klíč instrumentace (ikey). Ikey se pak použije ke konfiguraci sady OpenCensus SDK, aby odesílala data telemetrie Azure Monitor.

1. Vyberte **vytvořit prostředek** > **vývojářské nástroje** **Application Insights** > .

   ![Přidání prostředku Application Insights](./media/opencensus-python/0001-create-resource.png)

   Zobrazí se konfigurační pole. k vyplnění vstupních polí použijte následující tabulku.

    | Nastavení        | Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Jméno**      | Globálně jedinečná hodnota | Název, který identifikuje monitorovanou aplikaci |
   | **Skupina prostředků**     | MyResourceGroup      | Název nové skupiny prostředků pro hostování dat App Insights |
   | **Poloha** | Východní USA | Vyberte umístění poblíž vaší aplikace nebo poblíž místa, kde je vaše aplikace hostovaná. |

2. Klikněte na **vytvořit**.

## <a name="instrumenting-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumentace pomocí sady OpenCensus Python SDK pro Azure Monitor

1. Instalace OpenCensus Azure Monitor vývozců:

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` předpokládá, že máte nastavenou proměnnou prostředí PATH pro instalaci Pythonu. Pokud jste to ještě nenakonfigurovali, museli byste zadat úplnou cestu k adresáři, kde je umístěný spustitelný soubor Pythonu, což by vedlo k příkazu jako: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

2. Sada SDK využívá tři Azure Monitor vývozců k posílání různých typů telemetrie do Azure Monitor: trasování, metriky a protokoly. Podívejte [se na přehled datové platformy,](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform) kde najdete další podrobnosti o těchto různých typech. Podle pokynů níže Zjistěte, jak odeslat tyto různé typy prostřednictvím tří vývozců.

### <a name="trace"></a>Přehled

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

2. Spuštění kódu opakovaně zobrazí výzvu k zadání hodnoty. U každé položky se hodnota vytiskne do prostředí a v modulu OpenCensus Python se vygeneruje odpovídající **SpanData** . Projekt OpenCensus definuje [_trasování jako strom rozpětí_](https://opencensus.io/core-concepts/tracing/).
    
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

3. I když je to užitečné pro demonstrační účely, chceme nakonec pro Azure Monitor vygenerovat `SpanData`. Upravte kód z předchozího kroku na základě následujícího příkladu kódu:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        ),
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

4. Když teď spouštíte skript Pythonu, měli byste být pořád vyzváni k zadání hodnot, ale teď se v prostředí tiskne jenom hodnota. Vytvořená `SpanData` se pošle do Azure Monitor. Data emitovaného rozsahu můžete najít v části `dependencies`.

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
2. Spuštění kódu opakovaně zobrazí výzvu k stisknutí klávesy ENTER. Vytvoří se metrika pro sledování počtu stisknutých klávesových úhozů. U každé položky se hodnota zvýší a v konzole se zobrazí informace o metrikě s aktuální hodnotou a aktuálním časovým razítkem, kdy byla metrika aktualizována.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. I když je to užitečné pro demonstrační účely, chceme nakonec vygenerovat data metriky k Azure Monitor. Upravte kód z předchozího kroku na základě následujícího příkladu kódu:

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
    )
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

4. Exportér pošle data metriky Azure Monitor v pevném intervalu, přičemž výchozí hodnota je každých 15 sekund. Sledujeme jednu metriku, takže tato data metriky s jakoukoli hodnotou a časovým razítkem, která obsahuje, se budou posílat každý interval. Data najdete v části `customMetrics`.

### <a name="logs"></a>Protokolování

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

2.  Kód se průběžně vyzve k zadání hodnoty, která se má zadat. Položka protokolu je vygenerována pro každou zadanou hodnotu obsahující uvedenou hodnotu.

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

3. I když je to užitečné pro demonstrační účely, chceme nakonec vygenerovat data metriky k Azure Monitor. Upravte kód z předchozího kroku na základě následujícího příkladu kódu:

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

4. Exportér odešle data protokolu do Azure Monitor. Data najdete v části `traces`.

## <a name="start-monitoring-in-the-azure-portal"></a>Spustit monitorování v Azure Portal

1. Nyní můžete znovu otevřít stránku **přehled** Application Insights v Azure Portal a zobrazit podrobnosti o aktuálně spuštěné aplikaci. Vyberte **živý stream metriky**.

   ![Snímek obrazovky s aktivním datovým proudem metriky vybraným v červeném poli](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Přejděte zpět na stránku **Přehled** a vyberte **Mapa aplikace** pro vizuální rozložení vztahů závislosti a časování volání mezi komponentami vaší aplikace.

    ![Snímek obrazovky se základní mapou aplikace](./media/opencensus-python/0007-application-map.png)

    Vzhledem k tomu, že jsme provedli pouze trasování jednoho volání metody, není naše mapa aplikace zajímavá. Mapa aplikace se ale může škálovat a vizualizovat mnohem více distribuovaných aplikací:

   ![Mapa aplikace](media/opencensus-python/application-map.png)

3. Vyberte možnost **prozkoumat výkon** a proveďte podrobnou analýzu výkonu a určete původní příčinu pomalého výkonu.

    ![Snímek obrazovky s podoknem výkonu](./media/opencensus-python/0008-performance.png)

4. Když vyberete **ukázky** a pak kliknete na kteroukoli z ukázek, které se zobrazí v pravém podokně, spustí se prostředí s podrobnostmi o koncových transakcích. I když naše ukázková aplikace zobrazí jenom jednu událost, složitější aplikace vám umožní prozkoumat koncovou transakci dolů na úroveň zásobníku volání jednotlivých událostí.

     ![Snímek obrazovky s koncovým rozhraním transakce](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="view-your-data-with-queries"></a>Zobrazení dat pomocí dotazů

1. Data telemetrie, která byla odeslána z vaší aplikace, můžete zobrazit prostřednictvím karty protokoly (Analytics).

    ![Snímek obrazovky s podoknem přehled s protokoly (Analytics) vybranými v červeném poli](./media/opencensus-python/0010-logs-query.png)

2. U telemetrie odeslaných pomocí Azure Monitorho exportéra sledování se budou příchozí žádosti zobrazovat pod `requests` a žádosti o zpracování odeslané/v se zobrazí v části `dependencies`.

3. U telemetrie odeslaných pomocí Azure Monitor pro export metrik se zasílané metriky zobrazí v části `customMetrics`.

4. U telemetrie odesílaných pomocí Azure Monitorch protokolů se budou protokoly zobrazovat v části `traces` a výjimky se zobrazí v části `exceptions`.

5. Podrobnější informace o používání dotazů a protokolů najdete [v protokolech v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) .

## <a name="opencensus-for-python"></a>OpenCensus pro Python

* [OpenCensus Python na GitHubu](https://github.com/census-instrumentation/opencensus-python)
* [Uživatelských](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Integrace na baňce](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Integrace Django](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [Integrace MySQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Další kroky

* [Přehled rozhraní API](./../../azure-monitor/app/api-custom-events-metrics.md)
* [Mapa aplikace](./../../azure-monitor/app/app-map.md)
* [Monitorování výkonu na konci](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Upozornění

* [Testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md): Vytvořte testy, abyste se ujistili, že je váš web na webu viditelný.
* [Inteligentní Diagnostika](../../azure-monitor/app/proactive-diagnostics.md): tyto testy se spouštějí automaticky, takže nemusíte nic dělat, abyste je nastavili. Dozvíte se, jestli má vaše aplikace nezvyklé míry neúspěšných žádostí.
* [Výstrahy metriky](../../azure-monitor/app/alerts.md): Nastavte výstrahy, které vás upozorní, pokud metrika překračuje prahovou hodnotu. Můžete je nastavit na vlastní metriky, které zadáváte do své aplikace.
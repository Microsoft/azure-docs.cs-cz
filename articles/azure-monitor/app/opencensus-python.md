---
title: Monitorování aplikací v Pythonu pomocí Azure Application Insights | Dokumentace Microsoftu
description: Obsahuje pokyny, které propojí OpenCensus Pythonu s využitím Application Insights
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 07/02/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 2c043ad793dcf5e59eaf460d1ec4aa7a3b48810d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541436"
---
# <a name="collect-distributed-traces-from-python-preview"></a>Shromažďovat distribuované trasování z Pythonu (Preview)

Application Insights teď podporuje distribuované trasování aplikací v Pythonu pomocí integrace s [OpenCensus](https://opencensus.io). Tento článek vás provede procesem nastavování OpenCensus pro Python a získávání dat monitorování do Application Insights.

## <a name="prerequisites"></a>Požadavky

- Mít předplatné Azure.
- Python by měly být nainstalovány, tento článek používá [Python 3.7.0](https://www.python.org/downloads/), i když dřívějších verzích bude pravděpodobně pracovat s menší úpravu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Vytvořte prostředek Application Insights

Nejprve je nutné vytvořit prostředek Application Insights, která bude generovat key(ikey) instrumentaci. Ikey pak slouží ke konfiguraci OpenCensus sadu SDK k odesílání telemetrických dat do služby Application Insights.

1. Vyberte **vytvořit prostředek** > **vývojářské nástroje** > **Application Insights**.

   ![Přidání prostředku Application Insights](./media/opencensus-python/0001-create-resource.png)

   Zobrazí se konfigurační pole. K vyplnění vstupních polí použijte následující tabulku.

    | Nastavení        | Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | Globálně jedinečná hodnota | Název identifikující aplikaci, kterou monitorujete |
   | **Skupina prostředků**     | myResourceGroup      | Název pro novou skupinu prostředků, která bude hostovat data App Insights |
   | **Location** | East US | Vyberte umístění ve vaší blízkosti nebo v blízkosti místa, kde se vaše aplikace hostuje. |

2. Klikněte na možnost **Vytvořit**.

## <a name="install-opencensus-azure-monitor-exporters"></a>Nainstalujte vývozci OpenCensus Azure Monitor

1. Nainstalujte Monitor vývozci OpenCensus Azure:

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` předpokládá, že máte nastaven pro vaši instalaci Pythonu proměnné prostředí PATH. Pokud jste nenakonfigurovali toto, je třeba poskytnout úplné adresář na umístění spustitelný soubor Pythonu který jako výsledek v příkazu: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

2. První umožňuje generovat některá data trasování místně. V Pythonu NEČINNÉ nebo vašem editoru podle výběru zadejte následující kód:

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

3. Spuštění kódu opakovaně vás vyzve k zadání hodnoty. S každou položku se vytisknou hodnotu do prostředí a odpovídající část **SpanData** vygeneruje OpenCensus modul Pythonu. Definuje OpenCensus projektu [ _trasování jako strom rozsahy_](https://opencensus.io/core-concepts/tracing/).
    
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

4. Zatímco je užitečné pro demonstrační účely, takže v konečném důsledku chceme, aby generoval SpanData do Application Insights. Upravte kód z předchozího kroku podle následující ukázka kódu:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
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
5. Nyní když spustíte skript v jazyce Python, můžete stále vyzváni, zadejte hodnoty, ale teď je pouze hodnotu tisku v prostředí.

## <a name="start-monitoring-in-the-azure-portal"></a>Zahájení monitorování na webu Azure Portal

1. Teď můžete znovu otevřít Application Insights **přehled** stránky na webu Azure Portal, chcete-li zobrazit podrobné informace o aktuálně spuštěné aplikaci. Vyberte **Live Stream metrik**.

   ![Snímek obrazovky podokna přehled s živý stream metrik, které jsou vybrané v červeným rámečkem](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Přejděte zpět **přehled** stránku a vybrat **Mapa aplikace** pro vizuální rozložení vztahů závislosti a časování volání mezi komponentami vaší aplikace.

    ![Snímek obrazovky základní aplikace mapy](./media/opencensus-python/0007-application-map.png)

    Protože jsme byly trasování pouze jedno volání metody, není jako zajímavé Mapa aplikace. Ale můžete škálovat mapu aplikace k vizualizaci mnohem více distribuované aplikace:

   ![Mapa aplikace](media/opencensus-python/application-map.png)

3. Vyberte **zkoumání výkonu** provádět podrobnou analýzu výkonu a určení původní příčiny snížení výkonu.

    ![Snímek obrazovky podokna výkonu](./media/opencensus-python/0008-performance.png)

4. Výběr **ukázky** a potom kliknutím na některé z ukázek, které se zobrazují v pravém podokně se spustí prostředí podrobnosti transakce začátku do konce. Když naše ukázková aplikace se pouze zobrazí jednu událost, složitější aplikaci umožní prozkoumat začátku do konce transakcí na úrovni jednotlivých událostí zásobníku volání.

     ![Snímek obrazovky začátku do konce transakce rozhraní](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-for-python"></a>OpenCensus for Python

* [Vlastní nastavení](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Integrace Flask](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Integrace Django](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL Integration](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Další postup

* [OpenCensus Python on GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Mapa aplikace](./../../azure-monitor/app/app-map.md)
* [Sledování výkonu začátku do konce](./../../azure-monitor/learn/tutorial-performance.md)
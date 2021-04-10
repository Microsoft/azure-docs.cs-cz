---
title: Get-Metric v Azure Monitor Application Insights
description: Zjistěte, jak efektivně používat volání getmetric () k zachycení místně předem agregovaných metrik pro aplikace .NET a .NET Core pomocí Azure Monitor Application Insights
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 22baa1ae9554601a72ffdb848b87d99281067967
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384285"
---
# <a name="custom-metric-collection-in-net-and-net-core"></a>Kolekce vlastních metrik v rozhraní .NET a .NET Core

Sady SDK Azure Monitor Application Insights .NET a .NET Core mají dvě různé metody shromažďování vlastních metrik, `TrackMetric()` a `GetMetric()` . Klíčový rozdíl mezi těmito dvěma metodami je místní agregace. `TrackMetric()` neobsahuje předagregaci, zatímco `GetMetric()` má předagregaci. Doporučený postup je použít agregaci, proto už `TrackMetric()` není upřednostňovanou metodou shromažďování vlastních metrik. Tento článek vás provede použitím metody getmetric () a některých z odůvodnění, jak to funguje.

## <a name="pre-aggregating-vs-non-pre-aggregating-api"></a>Předběžná agregace rozhraní API, které není předem agregováno

`TrackMetric()` odesílá nezpracované telemetrie, která označuje metriku. Je neefektivní odeslat jednu položku telemetrie pro každou hodnotu. `TrackMetric()` je také neefektivní z pohledu výkonu, protože každý `TrackMetric(item)` projde kompletním kanálem sady SDK inicializátorů telemetrie a procesorů. Na rozdíl od `TrackMetric()` , `GetMetric()` zpracovává místní předagregaci za vás a poté odesílá agregovanou souhrnnou metriku v pevném intervalu 1 minuty. Takže pokud potřebujete úzce monitorovat určitou vlastní metriku na druhé nebo dokonce úrovni milisekund, můžete tak učinit, ale jenom náklady na úložiště a síťovou komunikaci jenom sledujete každou minutu. Tím se značně snižuje riziko omezování, protože celkový počet položek telemetrie, které je potřeba odeslat pro agregovanou metriku, se výrazně sníží.

V Application Insights vlastní metriky shromažďované přes `TrackMetric()` a `GetMetric()` nepodléhají [vzorkování](./sampling.md). Vzorkování důležitých metrik může vést k situacím, kdy upozornění, která jste mohli vygenerovat kolem těchto metrik, by mohla být nespolehlivá. Když si vaše vlastní metriky nikdy nevzorkování, můžete si obecně být jistí, že když dojde k porušení prahových hodnot upozornění, aktivuje se výstraha.  Ale vzhledem k tomu, že vlastní metriky nejsou vzorkované, existují potenciální obavy.

Pokud potřebujete sledovat trendy v metrikě každou sekundu, nebo v podrobnějším intervalu, může to mít za následek:

- Zvýšené náklady na úložiště dat. Existují náklady spojené s množstvím dat, která odesíláte do Azure Monitor. (Další data, která posíláte větší náklady na monitorování.)
- Zvýšení zátěže sítě/režie výkonu. (V některých případech to může mít náklady na výkon peněžních i aplikačních aplikací.)
- Riziko přijímání omezení příjmu. (Služba Azure Monitor vyřazuje datové body ("omezení"), když vaše aplikace pošle velmi vysokou míru telemetrie v krátkém časovém intervalu.)

Omezování je obzvláště důležité v takovém případě, jako je vzorkování, omezování může vést k chybějícím výstrahám, protože podmínka pro aktivaci výstrahy může probíhat místně a pak se vynechá na koncovém bodu ingestování kvůli příliš velkému počtu odesílaných dat. To je důvod, proč rozhraní .NET a .NET Core nedoporučujeme používat, `TrackMetric()` Pokud jste neimplementovali vlastní místní logiku agregace. Pokud se snažíte sledovat každou instanci, ke které dojde v daném časovém období, může se stát, že [`TrackEvent()`](./api-custom-events-metrics.md#trackevent) se to lépe hodí. I když mějte na paměti, že na rozdíl od vlastních metrik se vlastní události vztahují k odběru vzorků. Samozřejmě můžete kurz stále používat `TrackMetric()` i bez psaní vlastní místní předběžné agregace, ale pokud to uděláte, budete mít na nástrah vědět.

V souhrnu `GetMetric()` je doporučený přístup, protože se používá před agregací, shromažďuje hodnoty ze všech volání stop () a odesílá souhrn nebo agregaci jednou za minutu. To může významně snížit náklady a režii na výkon tím, že posílá méně datových bodů a stále shromažďuje všechny relevantní informace.

> [!NOTE]
> Pouze sady SDK .NET a .NET Core mají metodu getmetric (). Pokud používáte Java, můžete použít [metriky mikroměřiče](./micrometer-java.md) nebo `TrackMetric()` . Pro JavaScript a Node.js, které byste pořád používali `TrackMetric()` , ale mějte na paměti upozornění, která byla popsaných v předchozí části. V případě Pythonu můžete pomocí [OpenCensus. stat](./opencensus-python.md#metrics) odesílat vlastní metriky, ale implementace metrik se liší.

## <a name="getting-started-with-getmetric"></a>Začínáme se službou getmetric

V našich příkladech budeme používat základní aplikaci pracovní služby pro .NET Core 3,1. Pokud chcete provést naprostou replikaci testovacího prostředí, které bylo použito s těmito příklady, postupujte podle kroků 1-6 [článku o službě monitoring Worker](./worker-service.md#net-core-30-worker-service-application) a přidejte Application Insights do šablony projektu základní pracovní služby. Tyto koncepty se vztahují na všechny obecné aplikace, ve kterých je možné sadu SDK použít, včetně webových aplikací a konzolových aplikací.

### <a name="sending-metrics"></a>Odesílají se metriky.

Obsah souboru nahraďte `worker.cs` následujícím:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.ApplicationInsights;

namespace WorkerService3
{
    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {   // The following line demonstrates usages of GetMetric API.
            // Here "computersSold", a custom metric name, is being tracked with a value of 42 every second.
            while (!stoppingToken.IsCancellationRequested)
            {
                _telemetryClient.GetMetric("ComputersSold").TrackValue(42);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(1000, stoppingToken);
            }
        }
    }
}
```

Pokud spustíte výše uvedený kód a budete sledovat telemetrii, která je odesílána prostřednictvím okna výstupu sady Visual Studio, nebo pomocí nástroje, jako je Fiddler Telerik, uvidíte, že Smyčka while bude opakovaně prováděna bez odeslání telemetrie a pak bude jedna položka telemetrie odeslána pomocí značky 60-sekund, který v případě našeho testu vypadá takto. :

```json
Application Insights Telemetry: {"name":"Microsoft.ApplicationInsights.Dev.00000000-0000-0000-0000-000000000000.Metric", "time":"2019-12-28T00:54:19.0000000Z",
"ikey":"00000000-0000-0000-0000-000000000000",
"tags":{"ai.application.ver":"1.0.0.0",
"ai.cloud.roleInstance":"Test-Computer-Name",
"ai.internal.sdkVersion":"m-agg2c:2.12.0-21496",
"ai.internal.nodeName":"Test-Computer-Name"},
"data":{"baseType":"MetricData",
"baseData":{"ver":2,"metrics":[{"name":"ComputersSold",
"kind":"Aggregation",
"value":1722,
"count":41,
"min":42,
"max":42,
"stdDev":0}],
"properties":{"_MS.AggregationIntervalMs":"42000",
"DeveloperMode":"true"}}}}
```

Tato jediná položka telemetrie představuje agregaci 41 různých měření metriky. Vzhledem k tomu, že jsme přeposlali stejnou hodnotu znovu a znovu, máme *směrodatnou odchylku (stDev)* 0 se stejnými hodnotami *maxima (max)* a *minimum (min)* . Vlastnost *Value* představuje součet všech hodnot, které byly agregovány.

> [!NOTE]
> Getmetric nepodporuje sledování poslední hodnoty (tj. měřidla) nebo sledování histogramů/distribucí.

Pokud prověříme náš Application Insights prostředek v prostředí log (Analytics), bude tato samostatná položka telemetrie vypadat takto:

![Zobrazení dotazu Log Analytics](./media/get-metric/log-analytics.png)

> [!NOTE]
> I když nezpracovaná položka telemetrie neobsahovala explicitní vlastnost Sum nebo pole, vytvoříme pro vás jednu. V tomto případě `value` `valueSum` představuje vlastnost i stejnou věc.

K vlastní telemetrii metriky můžete také přistupovat v části [_metriky_](../essentials/metrics-charts.md) na portálu. Jak v závislosti na protokolu, tak i ve [vlastní metrikě](pre-aggregated-metrics-log-metrics.md). (Na snímku obrazovky níže je příklad založený na protokolu.) ![Zobrazení Průzkumníka metrik](./media/get-metric/metrics-explorer.png)

### <a name="caching-metric-reference-for-high-throughput-usage"></a>Ukládání referenčních informací metriky do mezipaměti pro použití s vysokou propustností

V některých případech jsou hodnoty metriky pozorovány velmi často. Například služba s vysokou propustností, která zpracovává 500 požadavků za sekundu, může chtít vygenerovat 20 metrik telemetrie pro každý požadavek. To znamená sledování hodnot 10 000 za sekundu. V takových scénářích s vysokou propustností můžou uživatelé potřebovat sadu SDK, aby vyloučí některá hledání.

V tomto případě příklad provedl vyhledávání pro popisovač metriky "ComputersSold" a následně sledoval zjištěnou hodnotu 42. Místo toho může být popisovač uložen do mezipaměti pro více volání sledování:

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is where the cache is stored to handle faster lookup
            Metric computersSold = _telemetryClient.GetMetric("ComputersSold");
            while (!stoppingToken.IsCancellationRequested)
            {

                computersSold.TrackValue(42);

                computersSold.TrackValue(142);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

Kromě ukládání popisovače metrik do mezipaměti je výše uvedený příklad také snížen na `Task.Delay` 50 milisekund, aby smyčka prováděla častěji v důsledku 772 `TrackValue()` vyvolání.

## <a name="multi-dimensional-metrics"></a>Multidimenzionální metriky

Příklady v předchozí části ukazují nulové multidimenzionální metriky. Metriky mohou být také multidimenzionální. V současné době podporujeme až 10 dimenzí.

 Tady je příklad vytvoření jednorozměrné metriky:

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is an example of a metric with a single dimension.
            // FormFactor is the name of the dimension.
            Metric computersSold= _telemetryClient.GetMetric("ComputersSold", "FormFactor");

            while (!stoppingToken.IsCancellationRequested)
            {
                // The number of arguments (dimension values)
                // must match the number of dimensions specified while GetMetric.
                // Laptop, Tablet, etc are values for the dimension "FormFactor"
                computersSold.TrackValue(42, "Laptop");
                computersSold.TrackValue(20, "Tablet");
                computersSold.TrackValue(126, "Desktop");


                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

Spuštění tohoto kódu pro minimálně 60 sekund bude mít za následek, že se do Azure pošle tři různé položky telemetrie, z nichž každá představuje agregaci jednoho ze tří faktorů formuláře. Stejně jako předtím je můžete prozkoumávat v zobrazení protokolů (Analytics):

![Zobrazení Log Analytics multidimenzionální metriky](./media/get-metric/log-analytics-multi-dimensional.png)

A také v prostředí Průzkumníka metrik:

![Vlastní metriky](./media/get-metric/custom-metrics.png)

Všimněte si ale, že nebudete schopni rozdělit metriku podle vaší nové vlastní dimenze nebo zobrazit vlastní dimenzi pomocí zobrazení metrik:

![Rozdělení podpory](./media/get-metric/splitting-support.png)

Ve výchozím nastavení se multidimenzionální metriky v prostředí metrického Průzkumníka nezapnou v Application Insights prostředky.

### <a name="enable-multi-dimensional-metrics"></a>Zapnout multidimenzionální metriky

Pokud chcete pro prostředek Application Insights povolit multidimenzionální metriky, vyberte **využití a odhadované náklady**.  >  **vlastní metriky**  >  **umožňují upozorňování na vlastní dimenze metriky**  >  **OK**. Další podrobnosti najdete [tady](pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

Po provedení této změny a odeslání nové multidimenzionální telemetrie budete moci **použít rozdělení**.

> [!NOTE]
> Po zapnutí funkce na portálu budou mít dimenze uložené jenom nově odeslané metriky.

![Použít rozdělení](./media/get-metric/apply-splitting.png)

A zobrazte agregace metrik pro každou dimenzi _FormFactor_ :

![Faktory formuláře](./media/get-metric/formfactor.png)

### <a name="how-to-use-metricidentifier-when-there-are-more-than-three-dimensions&quot;></a>Jak používat MetricIdentifier, pokud existuje více než tři dimenze

V současné době je dostupná 10 dimenzí, ale více než tři dimenze vyžaduje použití `MetricIdentifier` :

```csharp
// Add &quot;using Microsoft.ApplicationInsights.Metrics;&quot; to use MetricIdentifier
// MetricIdentifier id = new MetricIdentifier(&quot;[metricNamespace]&quot;,&quot;[metricId],&quot;[dim1]&quot;,&quot;[dim2]&quot;,&quot;[dim3]&quot;,&quot;[dim4]&quot;,&quot;[dim5]");
MetricIdentifier id = new MetricIdentifier("CustomMetricNamespace","ComputerSold", "FormFactor", "GraphicsCard", "MemorySpeed", "BatteryCapacity", "StorageCapacity");
Metric computersSold  = _telemetryClient.GetMetric(id);
computersSold.TrackValue(110,"Laptop", "Nvidia", "DDR4", "39Wh", "1TB");
```

## <a name="custom-metric-configuration"></a>Konfigurace vlastní metriky

Pokud chcete změnit konfiguraci metriky, musíte to udělat v místě, kde je metrika inicializovaná.

### <a name="special-dimension-names"></a>Speciální názvy dimenzí

Metriky nepoužívají kontext telemetrie pro `TelemetryClient` použití pro přístup k nim, speciální názvy dimenzí, které jsou k dispozici jako konstanty ve `MetricDimensionNames` třídě, jsou nejlepším řešením pro toto omezení.

Agregace metrik odesílané níže "velikost požadavku na speciální operaci" – metrika **nebude mít** `Context.Operation.Name` nastavenou hodnotu "speciální operace". Vzhledem k tomu, že `TrackMetric()` nebo jakýkoli jiný TrackXXX () bude `OperationName` správně nastaven na "speciální operace".

``` csharp
        //...
        TelemetryClient specialClient;
        private static int GetCurrentRequestSize()
        {
            // Do stuff
            return 1100;
        }
        int requestSize = GetCurrentRequestSize()

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                //...
                specialClient.Context.Operation.Name = "Special Operation";
                specialClient.GetMetric("Special Operation Request Size").TrackValue(requestSize);
                //...
            }
                   
        }
```

V takovém případě použijte názvy zvláštních dimenzí uvedené ve třídě, aby `MetricDimensionNames` bylo možné zadat `TelemetryContext` hodnoty.

Například při odeslání agregované metriky z dalšího příkazu do koncového bodu Application Insights cloudu `Context.Operation.Name` bude jeho datové pole nastaveno na "speciální operace":

```csharp
_telemetryClient.GetMetric("Request Size", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation");
```

Hodnoty této speciální dimenze budou zkopírovány do objektu a nebudou `TelemetryContext` použity jako dimenze ' Normal '. Pokud chcete také zachovat dimenzi operace pro normální zkoumání metriky, musíte pro tento účel vytvořit samostatnou dimenzi:

```csharp
_telemetryClient.GetMetric("Request Size", "Operation Name", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation", "Special Operation");
```

### <a name="dimension-and-time-series-capping"></a>Dimenze a časová řada capping

 Pokud nechcete, aby subsystém telemetrie omylem používal vaše prostředky, můžete řídit maximální počet datových řad na metriku. Výchozí omezení nejsou na více než 1000 celkových datových řad na jednu metriku a maximálně 100 různých hodnot na dimenzi.

 V kontextu dimenzí a časových řad capping používáme k zajištění `Metric.TrackValue(..)` dodržování limitů. Pokud jsou limity již dosaženy, `Metric.TrackValue(..)` vrátí hodnotu false a hodnota nebude sledována. V opačném případě vrátí "true". To je užitečné, pokud data metriky pocházejí ze vstupu uživatele.

`MetricConfiguration`Konstruktor přebírá některé možnosti pro správu různých řad v rámci příslušné metriky a objektu `IMetricSeriesConfiguration` , který implementuje třídu, která určuje chování agregace pro každou jednotlivou sérii metriky:

``` csharp
var metConfig = new MetricConfiguration(seriesCountLimit: 100, valuesPerDimensionLimit:2,
                new MetricSeriesConfigurationForMeasurement(restrictToUInt32Values: false));

Metric computersSold = _telemetryClient.GetMetric("ComputersSold", "Dimension1", "Dimension2", metConfig);

// Start tracking.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value1");
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value2");

// The following call gives 3rd unique value for dimension2, which is above the limit of 2.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3");
// The above call does not track the metric, and returns false.
```

* `seriesCountLimit` je maximální počet datových řad časových řad, které může metrika obsahovat. Po dosažení tohoto limitu volání `TrackValue()` , která by obvykle způsobila, že nová série vrátí hodnotu false.
* `valuesPerDimensionLimit` podobným způsobem omezuje počet jedinečných hodnot na dimenzi.
* `restrictToUInt32Values` Určuje, zda mají být sledovány pouze nezáporné celočíselné hodnoty.

Tady je příklad, jak odeslat zprávu s upozorněním na překročení limitu zakončení:

```csharp
if (! computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3"))
{
// Add "using Microsoft.ApplicationInsights.DataContract;" to use SeverityLevel.Error
_telemetryClient.TrackTrace("Metric value not tracked as value of one of the dimension exceeded the cap. Revisit the dimensions to ensure they are within the limits",
SeverityLevel.Error);
}
```

## <a name="next-steps"></a>Další kroky

* [Přečtěte si další informace ](./worker-service.md)o monitorování aplikací služby Worker.
* Pro další podrobnosti o [protokolech založených na protokolu a předem agregovaných metrikách](./pre-aggregated-metrics-log-metrics.md).
* [Průzkumník metrik](../essentials/metrics-getting-started.md)
* Postup povolení Application Insights pro [ASP.NET Core aplikace](asp-net-core.md)
* Postup povolení Application Insights pro [aplikace ASP.NET](asp-net.md)

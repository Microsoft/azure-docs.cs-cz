---
title: Synchronizace modelů mezi digitálními vlákna Azure a Time Series Insights | Microsoft Docs
description: Osvědčené postupy a nástroje, které se používají k překladu modelu assetu v ADT na model assetu v TSI Azure
ms.service: time-series-insights
services: time-series-insights
author: msrsrinivasan
ms.author: radhsrin
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/19/2021
ms.custom: dpalled
ms.openlocfilehash: 5e8e5367daa532fa0cc36a18cd28b382e7cd8f78
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682898"
---
# <a name="model-synchronization-between-azure-digital-twins-and-time-series-insights-gen2"></a>Synchronizace modelů mezi Azure Digital Twins a Time Series Insights Gen2

Tento článek vysvětluje osvědčené postupy a nástroje, které se používají k překladu modelu assetu v Azure Digital nevlákens (ADT) na model assetů v Azure Time Series Insights (TSI).  Tento článek je druhou částí série kurzů se dvěma částmi, které objasňují integraci digitálních vláken Azure s Azure Time Series Insights. Integrace digitálních vláken Azure s Time Series Insights umožňuje archivaci a sledování historie telemetrií a počítaných vlastností digitálních vláken. Tato série kurzů se zaměřuje na vývojáře, kteří pracují na integraci Time Series Insights s využitím digitálních vláken Azure. Část 1 vysvětluje  [, jak vytvořit datový kanál, který přináší data skutečného časového seriálu z digitálních vláken Azure do Time Series Insights](../digital-twins/how-to-integrate-time-series-insights.md) a tato druhá část série kurzů vysvětluje synchronizaci modelů prostředků mezi digitálními a Time Series Insightsmi Azure. Tento kurz vysvětluje osvědčené postupy při výběru a stanovení konvence vytváření názvů pro ID služby Time Series ID (TS ID) a ruční vytváření hierarchií v modelu časové řady (TSM).

## <a name="choosing-a-time-series-id"></a>Výběr ID časové řady

ID časové řady je jedinečný identifikátor, který slouží k identifikaci prostředků v Time Series Insights. Data časové řady (telemetrií z pole, které jsou páry časová hodnota), jsou reprezentovaná pomocí proměnných uvedených v části TSID. V rámci digitálních vláken Azure se pro reprezentaci stavu vlákna a měření vytvářených v tomto pořadí používají zdvojené vlastnosti a telemetrií. Pro aktuální návrh TSM musí být TSIDs jedinečný. Použití dvojitých ID vláken v Azure Digital provlákna nebo v kombinaci s vlastností nebo názvem telemetrie v TSM vždycky provede jedinečné ID TS. V typickém případě se **_`<Twin ID>`_** bude identifikační číslo volající stanice a názvy vlastností a telemetrie budou proměnné v TSM. Existují však případy použití, kde jsou upřednostňovány, že hierarchie prostředků v Time Series Insights být sloučeny pomocí formátu složených klíčů, jako je například **_`<Twin ID>+ <Delimiter of Choice> + <Name of the Property or Telemetry>`_** . Pojďme se na to podíváme, abychom vysvětlují pozdější případ. Vezměte v úvahu místnost v budově sestavenou jako dvojitou hodnotu a s dvojitým ID Room22. Vlastnost nastavení teploty se má přeložit jako **_Room22_TempSetting TSID_** a měření teploty se má přeložit na **_Room22_TempMea_** v TSM.

[![Výběr ID časové řady](media/tutorials-model-sync/choosing-tsid.png)](media/tutorials-model-sync/choosing-tsid.png#lightbox)

## <a name="contextualizing-time-series"></a>Contextualizing časová řada

Kontextová data (převážně prostorová v podstatě) v Time Series Insights dosahují pomocí hierarchií prostředků a stejné se používají pro snadné procházení dat prostřednictvím stromového zobrazení v Time Series Insights Exploreru. Typy časových řad a hierarchie jsou definovány pomocí TSM (Time Series model) v Time Series Insights. Typy v TSM nápovědě k definování proměnných, zatímco úrovně hierarchie a hodnoty polí instance slouží k vytvoření stromového zobrazení v Průzkumníkovi Time Series Insights. Další informace o TSM najdete v dokumentaci k [online Time Series Insights](./concepts-model-overview.md).

V případě digitálních vláken Azure se spojení mezi prostředky vyjadřuje pomocí zdvojených vztahů. Zdvojené relace jsou jednoduše grafem propojených prostředků. V rámci Time Series Insight je ale relace mezi prostředky hierarchicky povaze. To znamená, že prostředky sdílejí typ nadřízený-podřízený od a jsou reprezentovány pomocí stromové struktury. Abychom přeložili informace o vztahu z digitálních vláken Azure do Time Series Insights hierarchií, musíme zvolit relevantní hierarchické vztahy z digitálních vláken Azure. Digitální vlákna Azure používá otevřený standardní Modelovací jazyk nazvaný Digital DTDL Definition Language (). V modelech DTDL jsou popsány pomocí variant formátu JSON s názvem JSON-LD. Úplné podrobnosti o specifikaci najdete v [dokumentaci k DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) .

[![Propojení mezi prostředky](media/tutorials-model-sync/asset-connection.png)](media/tutorials-model-sync/asset-connection.png#lightbox)

## <a name="translating-graph-representation-in-azure-digital-twins-to-tree-structure-in-time-series-insights"></a>Překlad reprezentace grafu v oblasti digitálních vláken Azure do stromové struktury v Time Series Insights

V následujících částech kurzu se zachytí několik základních scénářů ručního překladu struktury grafu ve službě Azure Digital autocaptures na stromovou strukturu v Time Series Insights.

Vzorový systém: v tomto kurzu se dozvíte, jak níže popsaných konceptů vysvětlit následující příklad. Je to jednoduchý fiktivní systém pro správu, který má jednu podlahu a dvě místnosti. Má tři termostaty, jeden v každé z místností a druhý společný k podlaze. Kromě toho má měřič vodního toku měření toku vody z Room21 do Room22 prostřednictvím připojení kanálu mezi místnostmi. Při hledání prostorových vztahů mezi dvojitými závislostmi má oba typy vztahů.

1. Nejběžnější hierarchický vztah. Například Building40-> Floor01-> FloorTS *-> teploty
1. Neobvyklý a cyklický vztah. Například počínaje z Building40 se FlowMtr dá trasovat prostřednictvím dvou různých cest.

   1. Building40-> Floor01-> Room21-> FlowMtr *-> Flow
   1. Building40-> Floor01-> Room22-> FlowMtr *-> Flow  
      Kde "Flow" je skutečná telemetrie měření toku vody mezi Room21 a Room22

> [!Note]
>
> `*` FloorTS představuje pro měřiče toku FloorThermoStat a FlowMtr a obvykle se volí jako identifikační číslo volající stanice (TSID). Teplota a tok jsou nezpracovaná telemetrie, která se označuje jako proměnné v Time Series Insights.

V závislosti na aktuálním omezení Time Series Insights, že jeden Asset nemůže být reprezentován ve více větvích, v následujících částech je vysvětleno modelování hierarchických a cyklických vztahů v Time Series Insights.

## <a name="case-1-hierarchical-parent-child-relationship"></a>Případ 1: vztah hierarchického vztahu (nadřazený-podřízený)

Jedná se o nejběžnější typ relace mezi vlákna. modelování čistě nadřazeného a podřízeného vztahu je vysvětleno na následujícím obrázku. Pole instancí a TSID jsou odvozena z identifikátorů s dvojitou ID, jak je uvedeno níže. I když by se pole instancí daly ručně aktualizovat pomocí Time Series Insights Exploreru, v části pod názvem "aktualizace polí instance pomocí rozhraní API" je vysvětleno naslouchání změnám modelu v Azure Digital autogrids a aktualizace polí instancí v Time Series Insights pomocí Azure Functions.

[![Mapování dvojitých ID](media/tutorials-model-sync/mapping-twin-ids.png)](media/tutorials-model-sync/mapping-twin-ids.png#lightbox)

[![Mapování dvojitých ID 2](media/tutorials-model-sync/mapping-twin-ids2.png)](media/tutorials-model-sync/mapping-twin-ids2.png#lightbox)

## <a name="case-2-circular-relationship"></a>Případ 2: cyklický vztah

### <a name="circular-relationship-in-azure-digital-twins-to-single-hierarchy-relationship-in-time-series-insights"></a>Cyklická relace v Azure Digital provlákna pro relaci s jednou hierarchií v Time Series Insights

Vzhledem k tomu, že TSID musí být jedinečné a může být pouze v jedné hierarchii, představuje tento případ _"FlowMtr"_ s telemetrie s názvem _"Flow"_ , a to pouze v rámci vláken _"Room21"_. V budoucnu, když Time Series Insights může podporovat více reprezentace časových řad v TSM, telemetrie _"Flow_ " by představovalo " _místnost 21 '_ a _" místnost 22 "_ .

Na následujícím snímku obrazovky vidíte ruční mapování dvojitých ID v TSMch digitálních vláken Azure na pole instance v a výslednou hierarchii v Time Series Insights.

[![Mapování dvojitých ID v digitálních prozdvojeních Azure](media/tutorials-model-sync/mapping-twin-ids-adt.png)](media/tutorials-model-sync/mapping-twin-ids-adt.png#lightbox)

### <a name="circular-relationship-in-azure-digital-twins-to-multiple-hierarchies-in-time-series-insights-using-duplicates"></a>Cyklická relace v Azure Digital provlákna na více hierarchií ve Time Series Insights s použitím duplicitních hodnot

Část 1 tohoto kurzu vysvětluje, jak klientský program (funkce Azure) pomáhá přenášet data telemetrie z IoT Hub nebo jiných zdrojů událostí do digitálních vláken Azure. Tento přístup navrhuje použití stejného klientského programu k provádění aktualizací relevantních vlastností nadřazených vláken. V uvedeném příkladu se při čtení telemetrie FlowMtr z IoT Hub a aktualizace vlastnosti Flow v FlowMtr s dvojitou smyčkou může program aktualizovat také odpovídající vlastnosti ve všech možných nadřazených vlákenách zdroje. V našem příkladu by byl "outflowmea" (identifikovaný pomocí vztahu "úbytku") vlastnosti Room21 a vlastnost inflowmea třídy Room22.  Pod snímkem obrazovky se zobrazuje koncové uživatelské prostředí v Time Series Insights Exploreru. Je nutné poznamenat, že máme duplicity dat pomocí tohoto přístupu.

[![Průzkumník Time Series Insights](media/tutorials-model-sync/tsi-explorer.png)](media/tutorials-model-sync/tsi-explorer.png#lightbox)

Následující fragment kódu ukazuje, jak klientská aplikace dokázala procházet zdvojený vztah pomocí rozhraní API pro digitální vlákna Azure.

> [!Note]
>
> Tento příklad fragmentu kódu předpokládá, že čtenáři jsou obeznámené s [částí 01](../digital-twins/tutorial-end-to-end.md#set-up-the-sample-function-app) kurzu a tato změna kódu byla provedena uvnitř funkce "ProcessHubToDTEvents".

```csharp
if (propertyPath.Equals("/Flow"))
{
//Update the flow value property of the flow meter
await AdtUtilities.UpdateTwinProperty(client, twinId, "replace",
propertyPath, "double", propertyValue, log);

//also update the sending end flow
string parentIdOutflow = await AdtUtilities.FindParent(client, twinId,
"outflow", log);
if (parentIdOutflow != null)
await AdtUtilities.UpdateTwinProperty(client, parentIdOutflow, "replace", "outflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with outflow
relationship for " + twinId );
//and receiving end flow value
string parentIdinflow = await AdtUtilities.FindParent(client, twinId,
"inflow", log);
if (parentIdinflow != null)

await AdtUtilities.UpdateTwinProperty(client, parentIdinflow,
"replace", "inflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with inflow
relationship for " + twinId);
}
```

## <a name="updating-instance-fields-using-apis"></a>Aktualizace polí instance pomocí rozhraní API

V této části kurzu se dozvíte, jak naslouchat změnám modelu v digitálních proobjektech Azure, jako je vytváření, odstraňování zdvojených operací nebo změna v relacích mezi dvojitými a aktualizovanými poli instancí a hierarchií prostřednictvím rozhraní API modelů Time Series Insights. Tato metoda aktualizace modelu Time Series Insights se obvykle dosahuje prostřednictvím služby Azure Functions. V rámci digitálních vláken Azure je možné směrovat oznámení o událostech, jako je například doplněné přidání nebo odstranění dat, jako je například Event Hubs, které je možné doplňovat do Azure Functions. Další podrobnosti o směrování a filtrování událostí jsou vysvětleny [zde](../digital-twins/how-to-manage-routes-portal.md).  Zbývající část tohoto oddílu vysvětluje použití rozhraní API modelu Time Series Insights ve službě Azure Functions k aktualizaci modelu Time Series Insights v reakci na zdvojené sčítání (jeden typ změny modelu) v digitálních proobjektech Azure.

### <a name="receiving-and-identifying-twin-addition-event-notification"></a>Příjem a identifikace oznámení o událostech, která je k události přidání

```csharp
[FunctionName("RouteEventsToTsi")]
public async Task Run([EventGridTrigger]EventGridEvent eventGridEvent)
{
    try
    {
        if (eventGridEvent != null && eventGridEvent.Data != null)
        {
            logger.LogInformation($"EventType: {eventGridEvent.EventType}");
            logger.LogInformation($"EventGridEvent: {JsonConvert.SerializeObject(eventGridEvent)}");

            //Shape event and Send event data to event hub and tsi
            await SendEventToEventHubAsync(eventGridEvent).ConfigureAwait(false);

            //If a new twin was created, update the newly created instance in TSI with info retrieved from ADT
            if (eventGridEvent.EventType == Constants.TwinCreateEventType)
            {
                //retrieve building, floor and room of value twin
                var twinInfo = await RetrieveTwinInfoAsync(eventGridEvent).ConfigureAwait(false);
                //Update Tsi instance with type(sensor type), hierarchy(space hierarchy) and instance fields(twin info retrieved above)
                var instance = await CreateInstanceToSendAsync(twinInfo).ConfigureAwait(false);
                var instanceToUpdate = new List<TimeSeriesInstance>() { instance };
                var response = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(new InstancesBatchRequest(update: instanceToUpdate)).ConfigureAwait(false);
            }
        }
    }
    catch (Exception ex)
    {
        logger.LogError($"Exception: {ex.Message}");
    }
}
```

### <a name="creating-time-series-insights-client-and-adding-instance-details"></a>Vytváření klienta Time Series Insights a přidávání podrobností instance

```csharp
private async Task<TimeSeriesInstance> CreateInstanceToSendAsync(Dictionary<string, string> twinInfo)
{
    try
    {
        tsiClient = await GetTSIClientAsync().ConfigureAwait(false);

        var timeSeriesId = new object[] { twinInfo[Constants.DtId] };
        var instances = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(
            new InstancesBatchRequest(
                get: new InstancesRequestBatchGetOrDelete(
                    new IList<object>[] { timeSeriesId }))).ConfigureAwait(false);
        var instance = instances.Get.First().Instance;

        if (instance != null)
        {
            instance = await AddHierarchyToInstanceAsync(instance).ConfigureAwait(false);
            instance = await AddTypeToInstanceAsync(instance, twinInfo[Constants.TwinType]).ConfigureAwait(false);

            instance.InstanceFields = new Dictionary<string, object>
                            {
                                { "Building", twinInfo[Constants.BuildingName] },
                                { "Floor", twinInfo[Constants.FloorName] },
                                { "Room", twinInfo[Constants.ParentName] }
                            };

            //If value twin is a sensor value twin, add sensor type instance field to diff from spatial value twin
            if (twinInfo[Constants.ParentType] == Constants.Sensor)
            {
                instance.InstanceFields.Add(Constants.SensorType, twinInfo[Constants.TwinType]);
            }
            return instance;
        }
        else
        {
            logger.LogError($"instance with id {twinInfo[Constants.DtId]} not found");
            return new TimeSeriesInstance();
        }
    }
    catch (Exception e)
    {
        logger.LogError(e.Message);
        throw;
    }
}
```

### <a name="applying-hierarchy-info-to-instance"></a>Použití informací o hierarchii na instanci

```csharp
private async Task<TimeSeriesInstance> AddHierarchyToInstanceAsync(TimeSeriesInstance instance)
{
    if (instance.HierarchyIds == null)
    {
        TimeSeriesHierarchy spacesHierarchy = null;
        try
        {
            var hierarchy = await RunGetHierarchiesAsync(Constants.SpaceHierarchy).ConfigureAwait(false);
            spacesHierarchy = hierarchy.First(h => h.Name.Equals(Constants.SpaceHierarchy));
            instance.HierarchyIds = new List<Guid?>();
            instance.HierarchyIds.Add(spacesHierarchy.Id);
        }
        catch (Exception ex)
        {
            logger.LogWarning($"Hierarchy 'space hierarchy' not found, {ex}");
            throw;
        }
    }
    return instance;
}
```

## <a name="next-steps"></a>Další kroky

Třetí v řadě kurzů je Ukázat, jak pomocí rozhraní API Time Series Insights dotazovat historická data z digitálních vláken Azure. Jedná se o probíhající práci a oddíl bude aktualizován, až bude připravený. Během této doby se čtenáři doporučuje odkázat na [Time Series Insights dokumentaci k rozhraní API pro dotazy na data](./concepts-query-overview.md).

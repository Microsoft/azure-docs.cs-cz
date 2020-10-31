---
title: Monitorování IoT Hub Azure
description: Začněte tady, kde se dozvíte, jak monitorovat Azure IoT Hub
author: robinsh
ms.author: robinsh
ms.topic: conceptual
ms.service: iot-hub
ms.date: 10/22/2020
ms.openlocfilehash: 577ec9e01310787dacb45a03e1efae128cbab872
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078216"
---
# <a name="monitoring-azure-iot-hub"></a>Monitorování IoT Hub Azure

Pokud máte důležité aplikace a obchodní procesy spoléhající se na prostředky Azure, budete chtít tyto prostředky sledovat pro jejich dostupnost, výkon a provoz. Tento článek popisuje data monitorování vygenerovaná službou Azure IoT Hub a způsob použití funkcí Azure Monitor k analýze a upozornění na tato data.

## <a name="monitor-overview"></a>Přehled monitorování

Stránka **Přehled** v Azure Portal pro každé centrum IoT obsahuje grafy, které poskytují některé metriky využití, jako je počet použitých zpráv a počet zařízení připojených ke službě IoT Hub.

:::image type="content" source="media/monitor-iot-hub/overview-portal.png" alt-text="Výchozí grafy metrik na stránce s přehledem IoT Hub.":::

Tyto informace jsou užitečné, ale představují jenom malé množství dat monitorování, která jsou k dispozici pro Centrum IoT. Některá data monitorování se shromažďují automaticky a jsou dostupná k analýze hned po vytvoření centra IoT. Můžete povolit další typy shromažďování dat s určitou konfigurací.

## <a name="what-is-azure-monitor"></a>Co je Azure Monitor?

Azure IoT Hub vytváří data monitorování pomocí [Azure monitor](/azure/azure-monitor/overview), což je plná služba pro monitorování zásobníku v Azure, která poskytuje kompletní sadu funkcí pro monitorování prostředků Azure kromě prostředků v jiných cloudech a místních prostředích.

Začněte s článkem [monitorování prostředků Azure pomocí Azure monitor](/azure/azure-monitor/insights/monitor-azure-resource), který popisuje následující koncepty:

- Co je Azure Monitor?
- Náklady spojené s monitorováním
- Monitorování dat shromážděných v Azure
- Konfigurace shromažďování dat
- Standardní nástroje v Azure pro analýzu a upozorňování na data monitorování

Následující části jsou uvedené v tomto článku, které popisují konkrétní data shromážděná pro Azure IoT Hub a poskytují příklady pro konfiguraci shromažďování dat a analýzu těchto dat pomocí nástrojů Azure.

## <a name="monitoring-data"></a>Data monitorování

Azure IoT Hub shromažďuje stejné typy dat monitorování jako další prostředky Azure, které jsou popsané v tématu [monitorování dat z prostředků Azure](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data-from-Azure-resources).

Podrobné informace o metrikách a protokolech, které vytvořila služba Azure IoT Hub, najdete v tématu [monitorování referenčních dat Azure IoT Hub](monitor-iot-hub-reference.md) .

> [!IMPORTANT]
> Události generované službou IoT Hub pomocí protokolů Azure Monitor prostředků nejsou zaručeny jako spolehlivé nebo seřazené. Některé události mohou být ztraceny nebo doručeny mimo pořadí. Protokoly prostředků také nemají smysl v reálném čase a může trvat několik minut, než se události zaprotokolují do svého výběru cíle.

## <a name="collection-and-routing"></a>Shromažďování a směrování

Metriky platforem a protokol aktivit se shromažďují a ukládají automaticky, ale můžete je směrovat do jiných umístění pomocí diagnostického nastavení.

Protokoly prostředků se neshromažďují a ukládají, dokud nevytvoříte nastavení diagnostiky a nebudete je směrovat do jednoho nebo více umístění.

Metriky a protokoly je možné směrovat do několika umístění, včetně:
- Azure Monitor protokoluje úložiště prostřednictvím přidruženého pracovního prostoru Log Analytics. Dá se analyzovat pomocí Log Analytics.
- Azure Storage k archivaci a offline analýze 
- Event Hubs koncový bod, ve kterém mohou být čteny externími aplikacemi, například nástroje SIEM třetích stran.

V Azure Portal můžete vybrat **nastavení diagnostiky** v části **monitorování** v levém podokně služby IoT Hub a potom **Přidat nastavení** diagnostiky a vytvořit tak nastavení diagnostiky pro protokoly a metriky platforem vydávané službou IoT Hub.

Následující snímek obrazovky ukazuje nastavení diagnostiky pro směrování *operací připojení* typu protokolu prostředků a všechny metriky platforem do log Analyticsho pracovního prostoru.

:::image type="content" source="media/monitor-iot-hub/diagnostic-setting-portal.png" alt-text="Výchozí grafy metrik na stránce s přehledem IoT Hub.":::

Podrobný postup pro vytvoření nastavení diagnostiky pomocí Azure Portal, CLI nebo PowerShellu najdete v tématu [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik platforem v Azure](/azure/azure-monitor/platform/diagnostic-settings) . Při vytváření nastavení diagnostiky určíte, které kategorie protokolů se mají shromáždit. Kategorie pro Azure IoT Hub najdete v části [protokoly prostředků v referenčních informacích k datům monitorování azure IoT Hub](monitor-iot-hub-reference.md#resource-logs).

Při směrování IoT Hub metriky platforem do jiných umístění mějte na paměti, že:

- Následující metriky platformy nelze exportovat prostřednictvím nastavení diagnostiky: *připojená zařízení (Preview)* a *Celkový počet zařízení (Preview)* .

- Multidimenzionální metriky, například některé [metriky směrování](monitor-iot-hub-reference.md#routing-metrics), jsou aktuálně exportovány jako ploché jednorozměrné metriky agregované napříč hodnotami dimenzí. Další podrobnosti najdete v tématu [Export metrik platforem do jiných umístění](/azure/azure-monitor/platform/metrics-supported#exporting-platform-metrics-to-other-locations).

## <a name="analyzing-metrics"></a>Analýza metrik

Metriky pro Azure IoT Hub můžete analyzovat pomocí metrik z jiných služeb Azure pomocí Průzkumníka metrik, a to tak, že v nabídce **Azure monitor** otevřete **metriky** . Podrobnosti o použití tohoto nástroje najdete v tématu [Začínáme s Azure Průzkumník metrik](/azure/azure-monitor/platform/metrics-getting-started) .

V Azure Portal můžete v levém podokně služby IoT Hub vybrat **metriky** a ve výchozím nastavení otevřít Průzkumníka **metrik ve výchozím** nastavení metriky platforem vydávané službou IoT Hub:

:::image type="content" source="media/monitor-iot-hub/metrics-portal.png" alt-text="Výchozí grafy metrik na stránce s přehledem IoT Hub.":::

Seznam metrik platforem shromážděných pro Azure IoT Hub najdete v tématu [metriky v referenčních informacích k datům monitorování azure IoT Hub](monitor-iot-hub-reference.md#metrics). Seznam metrik platforem shromážděných pro všechny služby Azure najdete v tématu [podporované metriky s Azure monitor](/azure/azure-monitor/platform/metrics-supported).

Pro IoT Hub metriky platforem, které jsou shromažďovány v jednotkách Count, nemusí být některé agregace dostupné nebo použitelné. Další informace najdete v tématu [podporované agregace v referenčních informacích monitorování Azure IoT Hub](monitor-iot-hub-reference.md#supported-aggregations).

Některé metriky IoT Hub, jako jsou [metriky směrování](monitor-iot-hub-reference.md#routing-metrics), jsou multidimenzionální. Pro tyto metriky můžete použít [filtry](/azure/azure-monitor/platform/metrics-charts#apply-filters-to-charts) a [rozdělit](/azure/azure-monitor/platform/metrics-charts#apply-splitting-to-a-chart) je do grafů na základě dimenze.

## <a name="analyzing-logs"></a>Analýza protokolů

Data v Azure Monitor protokoly se ukládají v tabulkách, kde každá tabulka má svou vlastní sadu jedinečných vlastností. Data v těchto tabulkách jsou přidružená k Log Analyticsmu pracovnímu prostoru a lze je dotazovat v Log Analytics. Další informace o protokolech Azure Monitor najdete v tématu [Přehled protokolů Azure monitor](/azure/azure-monitor/platform/data-platform-logs) v dokumentaci k Azure monitor. 

Chcete-li směrovat data do protokolů Azure Monitor, je nutné vytvořit nastavení diagnostiky k odeslání protokolů prostředků nebo metriky platformy do pracovního prostoru Log Analytics. Další informace najdete v tématu [shromažďování a směrování](#collection-and-routing).

V Azure Portal můžete v levém podokně služby IoT Hub vybrat **protokoly** **a ve výchozím** nastavení provádět Log Analytics dotazy v oboru, do protokolů a metrik shromážděných v protokolech Azure monitor pro Centrum IoT.

:::image type="content" source="media/monitor-iot-hub/logs-portal.png" alt-text="Výchozí grafy metrik na stránce s přehledem IoT Hub.":::

Seznam tabulek používaných Azure Monitor protokolů a Queryable podle Log Analytics naleznete [v tématu Azure monitor logs tabulky v referenčních informacích k monitorování Azure IoT Hub](monitor-iot-hub-reference.md#azure-monitor-logs-tables).

Všechny protokoly prostředků v Azure Monitor mají stejná pole následovaná poli pro konkrétní služby. Společné schéma je popsáno v [Azure monitor schéma protokolu prostředků](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-schema#top-level-resource-logs-schema). Schéma a kategorie protokolů prostředků shromážděných pro Azure IoT Hub najdete v [protokolech prostředků v referenčních informacích k monitorování azure IoT Hub](monitor-iot-hub-reference.md#resource-logs).

[Protokol aktivit](/azure/azure-monitor/platform/activity-log) je protokol platformy v Azure, který poskytuje přehled o událostech na úrovni předplatného. Můžete ji zobrazit nezávisle nebo ji směrovat do Azure Monitor protokolů, kde můžete provádět mnohem složitější dotazy pomocí Log Analytics.  

Při směrování IoT Hub metriky platformy do protokolů Azure Monitor mějte na paměti, že:

- Následující metriky platformy nelze exportovat prostřednictvím nastavení diagnostiky: *připojená zařízení (Preview)* a *Celkový počet zařízení (Preview)* .

- Multidimenzionální metriky, například některé [metriky směrování](monitor-iot-hub-reference.md#routing-metrics), jsou aktuálně exportovány jako ploché jednorozměrné metriky agregované napříč hodnotami dimenzí. Další podrobnosti najdete v tématu [Export metrik platforem do jiných umístění](/azure/azure-monitor/platform/metrics-supported#exporting-platform-metrics-to-other-locations).

Některé běžné dotazy s IoT Hub najdete v tématu [Ukázky dotazů Kusto](#sample-kusto-queries). Podrobné informace o použití Log Analyticsch dotazů najdete [v tématu Přehled dotazů protokolu v Azure monitor](/azure/azure-monitor/log-query/log-query-overview).

### <a name="sdk-version-in-iot-hub-logs"></a>Verze sady SDK v protokolu IoT Hub

Některé operace v IoT Hub protokoly prostředků vrátí `sdkVersion` vlastnost ve svém `properties` objektu. Pro tyto operace, když zařízení nebo aplikace back-end používá jednu ze sad SDK služby Azure IoT, obsahuje tato vlastnost informace o používané sadě SDK, verzi sady SDK a platformě, na které je sada SDK spuštěná. Následující příklad ukazuje vlastnost, která byla `sdkVersion` generována pro [`deviceConnect`](monitor-iot-hub-reference.md#connections) operaci při použití sady Node.js SDK pro zařízení: `"azure-iot-device/1.17.1 (node v10.16.0; Windows_NT 10.0.18363; x64)"` . Tady je příklad hodnoty emitované pro sadu .NET (C#) SDK: `".NET/1.21.2 (.NET Framework 4.8.4200.0; Microsoft Windows 10.0.17763 WindowsProduct:0x00000004; X86)"` .

V následující tabulce je uveden název sady SDK používané pro různé sady SDK Azure IoT:

| Název sady SDK ve vlastnosti sdkVersion | Jazyk |
|----------|----------|
| .NET | .NET (C#) |
| Microsoft. Azure. Devices | Sada SDK služby .NET (C#) |
| Microsoft. Azure. Devices. Client | Sada SDK pro zařízení .NET (C#) |
| iothubclient | C nebo Python V1 (nepoužívané) sada SDK pro zařízení |
| iothubserviceclient | C nebo Python V1 (nepoužívané) sada SDK služby |
| Azure-IoT-Device-iothub-py | Sada SDK pro zařízení Python |
| azure-iot-device | Sada SDK pro Node.js zařízení |
| azure-iothub | Sada SDK služby Node.js |
| com. Microsoft. Azure. iothub-Java-Client | Sada SDK pro zařízení Java |
| com. Microsoft. Azure. iothub. Service. SDK | Sada SDK služby Java |
| com. Microsoft. Azure. SDK. IoT. IoT-Device-Client | Sada SDK pro zařízení Java |
| com. Microsoft. Azure. SDK. IoT. IoT-Service-Client | Sada SDK služby Java |
| C | Vložené C |
| C + (OSSimplified = Azure RTO) | Azure RTOS |

Vlastnost verze sady SDK můžete extrahovat při provádění dotazů na IoT Hubch protokolů prostředků. Následující dotaz například extrahuje vlastnost verze sady SDK (a ID zařízení) z vlastností vrácených operacemi připojení. Tyto dvě vlastnosti se zapisují do výsledků společně s časem operace a ID prostředku centra IoT, ke kterému se zařízení připojuje.

```kusto
// SDK version of devices
// List of devices and their SDK versions that connect to IoT Hub
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s) 
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
| distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
```

### <a name="sample-kusto-queries"></a>Ukázkové dotazy Kusto

> [!IMPORTANT]
> Když vyberete **protokoly** v nabídce IoT hub, Log Analytics se otevře s oborem dotazu nastaveným na aktuální centrum IoT. To znamená, že dotazy protokolu budou zahrnovat pouze data z tohoto prostředku. Pokud chcete spustit dotaz, který obsahuje data z jiných služeb Azure hub nebo dat z jiných služeb Azure, vyberte z nabídky **Azure monitor** **protokoly** . Podrobnosti najdete [v tématu Rozsah dotazů protokolu a časový rozsah v Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/) .

Níže jsou uvedené dotazy, které vám pomůžou s monitorováním služby IoT Hub.

- Chyby připojení: Identifikujte chyby připojení zařízení.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and Level == "Error"
    ```

- Chyby omezování: Identifikujte zařízení, která provedla nejvíce požadavků, což vede k chybám omezování.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where ResultType == "429001"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize count() by DeviceId, Category, _ResourceId
    | order by count_ desc
    ```

- Mrtvé koncové body: Identifikujte nefunkční koncové body v nesprávném stavu podle počtu nahlášeného problému a také z důvodu příčiny.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Routes" and OperationName in ("endpointDead", "endpointUnhealthy")
    | extend parsed_json = parse_json(properties_s)
    | extend Endpoint = tostring(parsed_json.endpointName), Reason = tostring(parsed_json.details) 
    | summarize count() by Endpoint, OperationName, Reason, _ResourceId
    | order by count_ desc
    ```

- Shrnutí chyb: počet chyb v rámci všech operací podle typu.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Level == "Error"
    | summarize count() by ResultType, ResultDescription, Category, _ResourceId
    ```

- Nedávno připojená zařízení: seznam zařízení, která se IoT Hub připojila v zadaném časovém období.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and OperationName == "deviceConnect"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize max(TimeGenerated) by DeviceId, _ResourceId
    ```

- Verze sady SDK zařízení: seznam zařízení a jejich verze SDK pro připojení zařízení nebo zařízení k necloudovým operacím.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" or Category == "D2CTwinOperations"
    | extend parsed_json = parse_json(properties_s)
    | extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
    | distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
    ```

### <a name="read-logs-from-azure-event-hubs"></a>Čtení protokolů z Azure Event Hubs

Po nastavení protokolování událostí prostřednictvím nastavení diagnostiky můžete vytvářet aplikace, které čtou protokoly, takže můžete provádět akce na základě informací v nich. Tento ukázkový kód načte protokoly z centra událostí:

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="alerts"></a>Výstrahy

Azure Monitor výstrahy proaktivně upozorní na to, že se ve vašich datech monitorování nacházejí důležité podmínky. Umožňují identifikovat a řešit problémy v systému před tím, než si je vaši zákazníci všimnete. Můžete nastavit výstrahy na [metrikách](/azure/azure-monitor/platform/alerts-metric-overview), [protokolech](/azure/azure-monitor/platform/alerts-unified-log)a [protokolu aktivit](/azure/azure-monitor/platform/activity-log-alerts). Různé typy výstrah mají výhody a nevýhody.

Při vytváření pravidla výstrahy na základě metrik platforem mějte na paměti, že pro IoT Hub metriky platforem, které jsou shromažďovány v jednotkách Count, nemusí být některé agregace dostupné nebo použitelné. Další informace najdete v tématu [podporované agregace v referenčních informacích monitorování Azure IoT Hub](monitor-iot-hub-reference.md#supported-aggregations).

## <a name="next-steps"></a>Další kroky

- Odkazy na metriky, protokoly a další důležité hodnoty vytvořené nástrojem [název služby] najdete v tématu [monitorování referenčních informací o službě Azure IoT Hub](monitor-iot-hub-reference.md) .

- Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](/azure/azure-monitor/insights/monitor-azure-resource) .

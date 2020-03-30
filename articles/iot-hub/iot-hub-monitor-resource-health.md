---
title: Sledování stavu služby Azure IoT Hub | Dokumenty společnosti Microsoft
description: Pomocí Azure Monitoru a Azure Resource Health monitorujete ioT hub a rychle diagnostikují problémy.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: kgremban
ms.openlocfilehash: f801abc40caf273c28a0c01dedf9735f5198c2af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271080"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Monitorování stavu služby Azure IoT Hub a rychlá diagnostika potíží

Firmy, které implementují Azure IoT Hub, očekávají spolehlivý výkon ze svých prostředků. Služba IoT Hub je plně integrovaná s Azure [Monitor](../azure-monitor/index.yml) a [Azure Resource Health,](../service-health/resource-health-overview.md)které vám pomohou pečlivě sledovat vaše operace. Tyto dvě služby vám poskytují data, která potřebujete k tomu, aby vaše řešení IoT byla v provozu v pořádku.

Azure Monitor je jediný zdroj monitorování a protokolování pro všechny vaše služby Azure. Diagnostické protokoly, které Azure Monitor generuje, můžete odeslat do protokolů Azure Monitor, event hubů nebo úložiště Azure pro vlastní zpracování. Azure Monitor metriky a nastavení diagnostiky vám přehled o výkonu vašich prostředků. Pokračujte ve čtení tohoto článku a zjistěte, jak [používat Azure Monitor](#use-azure-monitor) s centrem IoT. 

> [!IMPORTANT]
> Události vyzařované službou IoT Hub pomocí diagnostických protokolů Azure Monitor upozorní, že budou spolehlivé nebo objednané. Některé události mohou být ztraceny nebo doručeny mimo objednávku. Diagnostické protokoly také nejsou určeny k real-time a může trvat několik minut pro události, které mají být zaznamenány na výběr cíle.

Azure Resource Health vám pomůže diagnostikovat a získat podporu, když problém Azure ovlivní vaše prostředky. Řídicí panel poskytuje aktuální a minulý stav pro každý z vašich center IoT. Pokračujte v části v dolní části tohoto článku, kde se dozvíte, jak [používat Azure Resource Health](#use-azure-resource-health) s centrem IoT. 

IoT Hub také poskytuje vlastní metriky, které můžete použít k pochopení stavu prostředků IoT. Další informace najdete [v tématu Principy metrik centra IoT Hub](iot-hub-metrics.md).

## <a name="use-azure-monitor"></a>Použití Azure Monitoru

Azure Monitor poskytuje diagnostické informace pro prostředky Azure, což znamená, že můžete monitorovat operace, které probíhají v rámci služby IoT hub.

Nastavení diagnostiky Azure Monitoru nahrazuje monitorování operací služby IoT Hub. Pokud v současné době používáte monitorování operací, měli byste migrovat pracovní postupy. Další informace naleznete v [tématu Migrace z monitorování operací do nastavení diagnostiky](iot-hub-migrate-to-diagnostics-settings.md).

Další informace o konkrétních metrikách a událostech, které Azure Monitor sleduje, najdete v [tématu Podporované metriky s Azure Monitorem](../azure-monitor/platform/metrics-supported.md) a [podporovanými službami, schématy a kategoriemi pro diagnostické protokoly Azure](../azure-monitor/platform/diagnostic-logs-schema.md).

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Vysvětlení protokolů

Azure Monitor sleduje různé operace, ke kterým dochází v centru IoT Hub. Každá kategorie má schéma, které definuje, jak jsou hlášeny události v této kategorii.

#### <a name="connections"></a>Připojení

Kategorie připojení sleduje události připojení zařízení a odpojení od centra IoT a také chyby. Tato kategorie je užitečná pro identifikaci pokusů o neoprávněné připojení a upozornění, když ztratíte připojení k zařízením.

> [!NOTE]
> Pro spolehlivý stav připojení zařízení zkontrolujte [prezenční signál zařízení](iot-hub-devguide-identity-registry.md#device-heartbeat).

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-commands"></a>Příkazy cloud-to-device

Kategorie příkazů cloud-zařízení sleduje chyby, ke kterým dochází v centru IoT a souvisejí s kanálem zpráv mezi cloudy a zařízení. Tato kategorie zahrnuje chyby, ke kterým dochází z:

* Odesílání zpráv z cloudu na zařízení (jako jsou chyby neoprávněného odesílatele),
* Příjem zpráv mezi cloudy (jako je počet doručení překročen počet chyb) a
* Příjem zpětné vazby ze zpráv z cloudu na zařízení (jako jsou chyby, jejichž platnost vypršela).

Tato kategorie nezachytí chyby při úspěšném doručení zprávy cloud zařízení, ale potom nesprávně zpracována zařízením.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-identity-operations"></a>Operace identity zařízení

Kategorie operací identity zařízení sleduje chyby, ke kterým dochází při pokusu o vytvoření, aktualizaci nebo odstranění položky v registru identit služby IoT hub. Sledování této kategorie je užitečné pro zřizování scénáře.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="routes"></a>Trasy

Kategorie směrování zpráv sleduje chyby, ke kterým dochází během vyhodnocení trasy zprávy a stavu koncového bodu, jak je vnímá služba IoT Hub. Tato kategorie zahrnuje události, jako jsou:

* Pravidlo se vyhodnotí jako "nedefinované",
* IoT Hub označí koncový bod jako mrtvý nebo
* Všechny chyby přijaté z koncového bodu. 

Tato kategorie nezahrnuje konkrétní chyby týkající se samotných zpráv (jako jsou chyby omezení zařízení), které jsou hlášeny v kategorii "telemetrie zařízení".

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "endpointUnhealthy",
            "category": "Routes",
            "level": "Error",
            "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-telemetry"></a>Telemetrie zařízení

Kategorie telemetrie zařízení sleduje chyby, ke kterým dochází v centru IoT a souvisejí s kanálem telemetrie. Tato kategorie zahrnuje chyby, ke kterým dochází při odesílání událostí telemetrie (například omezení) a přijímání telemetrických událostí (například neautorizovaného čtecího zařízení). Tato kategorie nemůže zachytit chyby způsobené kódem spuštěným na samotném zařízení.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

#### <a name="file-upload-operations"></a>Operace nahrávání souborů

Kategorie nahrávání souborů sleduje chyby, ke kterým dochází v centru IoT a souvisí s funkcí nahrávání souborů. Tato kategorie zahrnuje:

* Chyby, ke kterým dochází s identifikátorem URI SAS, například když vyprší jeho platnost předtím, než zařízení upozorní centrum dokončeného nahrávání.

* Neúspěšná nahraná nahlášená zařízením.

* Chyby, ke kterým dochází, když soubor není nalezen v úložišti během vytváření zpráv oznámení služby IoT Hub.

Tato kategorie nemůže zachytit chyby, ke kterým dochází přímo, když zařízení nahrává soubor do úložiště.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-twin-operations"></a>Operace s dvojčaty cloud-to-device

Kategorie operací dvojčete cloud-to-device sleduje události iniciované službou na dvojčatech zařízení. Tyto operace mohou zahrnovat získání dvojčete, aktualizaci nebo nahrazení značek a aktualizaci nebo nahrazení požadovaných vlastností.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-to-cloud-twin-operations"></a>Operace dvojčete zařízení cloudu

Kategorie operací dvojčete zařízení cloud sleduje události iniciované zařízením na dvojčatech zařízení. Tyto operace mohou zahrnovat získat dvojče, aktualizovat hlášené vlastnosti a přihlásit se k odběru požadovaných vlastností.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="twin-queries"></a>Dvojité dotazy

Twin dotazy kategorie sestavy na požadavky na dotazy pro dvojčata zařízení, které jsou inicializovány v cloudu.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="jobs-operations"></a>Operace úloh

Sestavy kategorie operací úloh y na základě požadavků na úlohy pro aktualizaci dvojčat zařízení nebo vyvolání přímých metod na více zařízeních. Tyto požadavky jsou inicializovány v cloudu.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="direct-methods"></a>Přímé metody

Kategorie přímých metod sleduje interakce požadavku a odpovědi odeslané jednotlivým zařízením. Tyto požadavky jsou inicializovány v cloudu.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="distributed-tracing-preview"></a>Distribuované trasování (náhled)

Kategorie distribuovaného trasování sleduje ID korelace pro zprávy, které nesou záhlaví kontextu trasování. Chcete-li tyto protokoly plně povolit, musí být kód na straně klienta aktualizován pomocí [funkce Analyzovat a diagnostikovat aplikace IoT od konce pomocí distribuovaného trasování služby IoT Hub (náhled).](iot-hub-distributed-tracing.md)

Všimněte `correlationId` si, že je v souladu s [W3C trasovací kontext](https://github.com/w3c/trace-context) návrhu, kde obsahuje `trace-id` i . `span-id`

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>Protokoly Služby IoT Hub D2C (zařízení cloud)

Služba IoT Hub zaznamená tento protokol, když do služby IoT Hub dorazí zpráva obsahující platné vlastnosti trasování.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

Zde `durationMs` se nepočítá, protože hodiny služby IoT Hub nemusí být synchronizovány s hodinami zařízení, a proto může být výpočet doby trvání zavádějící. Doporučujeme psát logiku pomocí časová razítka v `properties` části zachytit špičky v latenci zařízení cloud.

| Vlastnost | Typ | Popis |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Integer | Velikost zprávy mezi zařízeními a cloudem v bajtech |
| **deviceId** | Řetězec 7bitových alfanumerických znaků ASCII | Identita zařízení |
| **volajícíLocalTimeUtc** | Časové razítko Času UTC | Čas vytvoření zprávy, jak je hlášenmístní hodiny zařízení |
| **voleLocalTimeUtc** | Časové razítko Času UTC | Čas doručení zprávy na bránu služby IoT Hub, jak je hlásí hodiny na straně služby služby Služby IoT Hub |

##### <a name="iot-hub-ingress-logs"></a>Protokoly příchozích dat v centru IoT

Služba IoT Hub zaznamenává tento protokol, když zpráva obsahující platné vlastnosti trasování zapíše do interního nebo předdefinovaného centra událostí.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

V `properties` části tento protokol obsahuje další informace o příchozím přenosu dat zprávy.

| Vlastnost | Typ | Popis |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **jepovoleno RoutingEnabled** | Řetězec | True nebo false označuje, zda je směrování zpráv povoleno v centru IoT Hub |
| **parentSpanId** | Řetězec | [Span-id](https://w3c.github.io/trace-context/#parent-id) nadřazené zprávy, která by trasování zprávy D2C v tomto případě |

##### <a name="iot-hub-egress-logs"></a>Protokoly odchozích přenosů v centru IoT

Služba IoT Hub zaznamenává tento protokol, když je [směrování](iot-hub-devguide-messages-d2c.md) povoleno a zpráva je zapsána do [koncového bodu](iot-hub-devguide-endpoints.md). Pokud směrování není povoleno, služba IoT Hub tento protokol nezaznamená.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

V `properties` části tento protokol obsahuje další informace o příchozím přenosu dat zprávy.

| Vlastnost | Typ | Popis |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **název koncového bodu** | Řetězec | Název koncového bodu směrování |
| **endpointType** | Řetězec | Typ koncového bodu směrování |
| **parentSpanId** | Řetězec | [Span-id](https://w3c.github.io/trace-context/#parent-id) nadřazené zprávy, která by byla trasování příchozích zpráv služby IoT Hub v tomto případě |

#### <a name="configurations"></a>Konfigurace

Protokoly konfigurace služby IoT Hub sledují události a chyby sady funkcí Automatické správy zařízení.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Datové proudy zařízení (náhled)

Kategorie streamů zařízení sleduje interakce požadavku a odpovědi odeslané jednotlivým zařízením.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Čtení protokolů z Azure Event Hubs

Po nastavení protokolování událostí prostřednictvím nastavení diagnostiky můžete vytvořit aplikace, které odečtou protokoly, takže můžete provést akci na základě informací v nich. Tento ukázkový kód načte protokoly z centra událostí:

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

## <a name="use-azure-resource-health"></a>Použití stavu prostředků Azure

Pomocí Azure Resource Health můžete sledovat, jestli je vaše centrum IoT zprovozněné. Můžete se také dozvědět, jestli místní výpadek ovlivňuje zdraví vašeho centra IoT hub. Chcete-li porozumět konkrétním podrobnostem o stavu služby Azure IoT Hub, doporučujeme [použít Azure Monitor](#use-azure-monitor).

Azure IoT Hub označuje stav na místní úrovni. Pokud místní výpadek ovlivní vaše centrum IoT, stav se zobrazí jako **Neznámý**. Další informace najdete v tématu [typy prostředků a kontroly stavu v Azure stavu prostředků](../service-health/resource-health-checks-resource-types.md).

Chcete-li zkontrolovat stav vašich center IoT, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. Přejděte na**stav zdroje** **stavu** > služby .

3. V rozevíracích seznamech vyberte předplatné a pak vyberte **IoT Hub** jako typ prostředku.

Další informace o interpretaci dat o stavu najdete v [tématu Přehled stavu prostředků Azure](../service-health/resource-health-overview.md).

## <a name="next-steps"></a>Další kroky

* [Principy metrik centra IoT Hub](iot-hub-metrics.md)
* [Vzdálené monitorování ioT a oznámení pomocí aplikací Azure Logic Apps, které propojují vaše centrum IoT a poštovní schránku](iot-hub-monitoring-notifications-with-azure-logic-apps.md)

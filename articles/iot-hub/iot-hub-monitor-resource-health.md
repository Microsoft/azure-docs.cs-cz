---
title: Monitorujte stav služby Azure IoT Hub | Microsoft Docs
description: Pomocí Azure Monitor a Azure Resource Health můžete rychle monitorovat IoT Hub a diagnostikovat problémy.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: kgremban
ms.openlocfilehash: f801abc40caf273c28a0c01dedf9735f5198c2af
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271080"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Sledujte stav služby Azure IoT Hub a rychle Diagnostikujte problémy

Firmy, které implementují Azure IoT Hub očekávají od svých prostředků spolehlivý výkon. Abychom vám pomohli udržovat v rámci svých operací uzavřený kukátko, IoT Hub je plně integrovaná s [Azure monitor](../azure-monitor/index.yml) a [Azure Resource Health](../service-health/resource-health-overview.md). Tyto dvě služby fungují tak, aby vám poskytovaly data, která potřebujete k zajištění aktuálnosti řešení IoT a jejich spuštění v dobrém stavu.

Azure Monitor je jeden zdroj monitorování a protokolování pro všechny služby Azure. Můžete odeslat diagnostické protokoly, které Azure Monitor generuje pro Azure Monitor protokoly, Event Hubs nebo Azure Storage pro vlastní zpracování. Metriky a nastavení diagnostiky Azure Monitor poskytují přehled o výkonu vašich prostředků. V tomto článku se dozvíte, jak [používat Azure monitor](#use-azure-monitor) ve službě IoT Hub. 

> [!IMPORTANT]
> Události generované službou IoT Hub pomocí protokolů Azure Monitor diagnostiky nejsou zaručené jako spolehlivé nebo seřazené. Některé události mohou být ztraceny nebo doručeny mimo pořadí. Diagnostické protokoly také nemají smysl v reálném čase a může trvat několik minut, než se události zaprotokolují do svého výběru cíle.

Azure Resource Health vám pomůže diagnostikovat a získat podporu v případě, že problém Azure ovlivňuje vaše prostředky. Řídicí panel poskytuje aktuální a minulý stav pro každé z vašich Center IoT. V části v dolní části tohoto článku se dozvíte, jak [používat Azure Resource Health](#use-azure-resource-health) ve službě IoT Hub. 

IoT Hub také nabízí vlastní metriky, které vám pomohou pochopit stav svých prostředků IoT. Další informace najdete v tématu [vysvětlení metrik IoT Hub](iot-hub-metrics.md).

## <a name="use-azure-monitor"></a>Použití Azure Monitoru

Azure Monitor poskytuje diagnostické informace o prostředcích Azure, což znamená, že můžete sledovat operace, které probíhají v rámci služby IoT Hub.

Nastavení diagnostiky Azure Monitor nahrazuje monitorování operací IoT Hub. Pokud aktuálně používáte monitorování provozu, měli byste pracovní postupy migrovat. Další informace najdete v tématu [migrace z monitorování provozu na nastavení diagnostiky](iot-hub-migrate-to-diagnostics-settings.md).

Další informace o konkrétních metrikách a událostech, které Azure Monitor sleduje, najdete v tématu [podporované metriky s Azure monitor](../azure-monitor/platform/metrics-supported.md) a [podporovanými službami, schématy a kategoriemi pro diagnostické protokoly Azure](../azure-monitor/platform/diagnostic-logs-schema.md).

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Pochopení protokolů

Azure Monitor sleduje různé operace, ke kterým dochází v IoT Hub. Každá kategorie má schéma, které definuje způsob hlášení událostí v této kategorii.

#### <a name="connections"></a>Připojení

Kategorie připojení sleduje události připojení zařízení a odpojení od služby IoT Hub a také chyby. Tato kategorie je užitečná pro identifikaci neautorizovaných pokusů o připojení a upozornění, když ztratíte připojení k zařízením.

> [!NOTE]
> U spolehlivých stavů připojení zařízení se kontrolují [prezenční signály zařízení](iot-hub-devguide-identity-registry.md#device-heartbeat).

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

#### <a name="cloud-to-device-commands"></a>Příkazy z cloudu do zařízení

Kategorie příkazy Cloud-zařízení sleduje chyby, ke kterým dochází ve službě IoT Hub a které souvisejí s kanálem zpráv z cloudu na zařízení. Tato kategorie zahrnuje chyby, ke kterým dochází:

* Posílání zpráv z cloudu na zařízení (jako jsou chyby neautorizovaných odesílatelů),
* Příjem zpráv z cloudu na zařízení (jako je počet předaných doručení překročil chyby) a
* Příjem zpětné vazby ze zpráv typu cloud-zařízení (jako jsou chyby vypršení zpětné vazby)

Tato kategorie nezachycuje chyby, když se zpráva z cloudu na zařízení úspěšně doručí, ale zařízení nesprávně zpracuje.

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

Kategorie operace identity zařízení sleduje chyby, ke kterým dochází při pokusu o vytvoření, aktualizaci nebo odstranění záznamu v registru identit ve službě IoT Hub. Sledování této kategorie je užitečné pro scénáře zřizování.

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

Kategorie směrování zpráv sleduje chyby, ke kterým došlo během hodnocení směrování zpráv a stavu koncového bodu, jak je uvedeno IoT Hub. Tato kategorie zahrnuje události jako:

* Pravidlo se vyhodnotí jako nedefinované.
* IoT Hub označí koncový bod jako mrtvý nebo
* Jakékoli chyby přijaté z koncového bodu. 

Tato kategorie neobsahuje konkrétní chyby týkající se samotných zpráv (například chyby omezování zařízení), které jsou uvedeny v kategorii telemetrie zařízení.

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

Kategorie telemetrie zařízení sleduje chyby, ke kterým dochází ve službě IoT Hub a souvisí s kanálem telemetrie. Tato kategorie zahrnuje chyby, ke kterým dochází při posílání událostí telemetrie (například omezování) a přijímání událostí telemetrie (například neoprávněné čtecí zařízení). Tato kategorie nemůže zachytit chyby způsobené kódem běžícím na samotném zařízení.

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

Kategorie nahrání souborů sleduje chyby, ke kterým dochází ve službě IoT Hub a souvisí s funkcemi pro nahrávání souborů. Tato kategorie zahrnuje:

* Chyby, ke kterým dochází s identifikátorem URI SAS, například když vyprší platnost před tím, než zařízení upozorní centrum dokončeného nahrávání.

* Neúspěšná odeslání uvedená v zařízení

* Chyby, ke kterým dochází, když se během vytváření zprávy IoT Hub oznámení v úložišti nenajde soubor

Tato kategorie nemůže zachytit chyby, ke kterým přímo dojde, když zařízení nahrává do úložiště soubor.

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

#### <a name="cloud-to-device-twin-operations"></a>Zdvojené operace z cloudu na zařízení

Kategorie dvojitých operací z cloudu na zařízení sleduje události iniciované službou v případě vláken zařízení. Tyto operace mohou zahrnovat získání dvojitých značek, aktualizovat nebo nahradit značky a aktualizovat nebo nahrazovat požadované vlastnosti.

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

#### <a name="device-to-cloud-twin-operations"></a>Zdvojené operace mezi zařízeními a cloudem

Kategorie operace s dvojitým cloudem v zařízení sleduje události iniciované zařízením v případě nevláken zařízení. Tyto operace mohou zahrnovat získat zdvojené, aktualizovat hlášené vlastnosti a přihlásit se k odběru požadovaných vlastností.

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

#### <a name="twin-queries"></a>Zdvojené dotazy

Kategorie dvojitých dotazů se sestavuje na dotazech na požadavky na vlákna zařízení, která jsou inicializovaná v cloudu.

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

Kategorie operace úlohy se sestavuje na žádostech o úlohy, aby se aktualizovaly vlákna zařízení nebo vyvolaly přímé metody na více zařízeních. Tyto požadavky jsou iniciovány v cloudu.

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

Kategorie přímé metody sleduje interakce odpovědí na požadavky odeslané na jednotlivá zařízení. Tyto požadavky jsou iniciovány v cloudu.

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

#### <a name="distributed-tracing-preview"></a>Distribuované trasování (Preview)

Kategorie distribuované trasování sleduje ID korelace pro zprávy, které přenášejí hlavičku kontextu trasování. Aby bylo možné tyto protokoly plně povolit, musí být kód na straně klienta aktualizován pomocí následujících příkazů [analyzovat a diagnostikovat aplikace IoT pomocí IoT Hub distribuované trasování (Preview)](iot-hub-distributed-tracing.md).

Všimněte si, že `correlationId` odpovídá návrhu [kontextu trasování W3C](https://github.com/w3c/trace-context) , kde obsahuje `trace-id` a také `span-id`.

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>Protokoly IoT Hub D2C (zařízení-Cloud)

IoT Hub zaznamenává tento protokol, když se do IoT Hub dorazí zpráva obsahující platné vlastnosti trasování.

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

V tomto případě se `durationMs` nepočítá, protože hodiny IoT Hub nemusí být synchronizované s hodinami zařízení, takže výpočet doby trvání může být zavádějící. Pro zachycení špičky v latenci při zachytávání doporučujeme napsat logiku pomocí časových razítek v části `properties`.

| Vlastnost | Typ | Popis |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Celé číslo | Velikost zprávy typu zařízení-Cloud v bajtech |
| **deviceId** | Řetězec alfanumerických alfanumerických znaků ASCII | Identita zařízení |
| **callerLocalTimeUtc** | Časové razítko UTC | Čas vytvoření zprávy, jak je uvedeno v místních hodinách zařízení |
| **calleeLocalTimeUtc** | Časové razítko UTC | Čas doručení zprávy na bránu IoT Hub, jak je uvedeno v IoT Hub hodiny na straně služby |

##### <a name="iot-hub-ingress-logs"></a>IoT Hub protokoly příchozího přenosu dat

IoT Hub zaznamenává tento protokol, pokud zpráva obsahující platné vlastnosti trasování zapisuje do interního nebo integrovaného centra událostí.

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

V části `properties` tento protokol obsahuje další informace o příchozím přenosu zpráv.

| Vlastnost | Typ | Popis |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | Hodnota true nebo false označuje, zda je v IoT Hub povoleno směrování zpráv. |
| **parentSpanId** | String | [Identifikátor rozsahu](https://w3c.github.io/trace-context/#parent-id) nadřazené zprávy, který by byl trasováním zpráv D2C v tomto případě |

##### <a name="iot-hub-egress-logs"></a>Protokoly odchozího IoT Hub

IoT Hub zaznamenává tento protokol, pokud je povoleno [Směrování](iot-hub-devguide-messages-d2c.md) a zpráva je zapsána do [koncového bodu](iot-hub-devguide-endpoints.md). Pokud směrování není povolené, IoT Hub tento protokol nezaznamenává.

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

V části `properties` tento protokol obsahuje další informace o příchozím přenosu zpráv.

| Vlastnost | Typ | Popis |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **koncový bod** | String | Název koncového bodu směrování |
| **endpointType** | String | Typ koncového bodu směrování |
| **parentSpanId** | String | [Identifikátor ID](https://w3c.github.io/trace-context/#parent-id) nadřazené zprávy, který bude v tomto případě IoT Hub trasování zprávy příchozího přenosu dat |

#### <a name="configurations"></a>Konfigurace

Protokoly konfigurace IoT Hub sledují události a chyby pro sadu funkcí automatické správy zařízení.

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

### <a name="device-streams-preview"></a>Datové proudy zařízení (Preview)

Kategorie streamy zařízení sleduje interakce požadavků a odpovědí odeslaných na jednotlivá zařízení.

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

## <a name="use-azure-resource-health"></a>Použít Azure Resource Health

Pomocí Azure Resource Health můžete monitorovat, jestli je vaše centrum IoT v provozu. Můžete se také seznámit s tím, jestli je dopad na stav služby IoT Hub ovlivněný oblastní výpadky. Pro pochopení konkrétních podrobností o stavu IoT Hub Azure doporučujeme [používat Azure monitor](#use-azure-monitor).

Azure IoT Hub indikuje stav na regionální úrovni. Pokud oblastní výpadek ovlivňuje vaše centrum IoT, stav se zobrazí jako **Neznámý**. Další informace najdete v tématu [typy prostředků a kontroly stavu v Azure Resource Health](../service-health/resource-health-checks-resource-types.md).

Pokud chcete zjistit stav vašich Center IoT, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Přejděte na **Service Health** > **Resource Health**.

3. V rozevíracích seznamech vyberte své předplatné a pak jako typ prostředku vyberte **IoT Hub** .

Další informace o tom, jak interpretovat údaje o stavu, najdete v tématu [Přehled Azure Resource Health](../service-health/resource-health-overview.md).

## <a name="next-steps"></a>Další kroky

* [Pochopení IoT Hub metriky](iot-hub-metrics.md)
* [Vzdálené monitorování a oznámení IoT pomocí Azure Logic Apps připojení ke službě IoT Hub a poštovní schránce](iot-hub-monitoring-notifications-with-azure-logic-apps.md)

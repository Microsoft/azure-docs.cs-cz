---
title: Monitorování stavu služby Azure IoT Hub | Dokumentace Microsoftu
description: Azure Monitor a Azure Resource Health umožňuje monitorování služby IoT Hub a rychlá Diagnostika potíží
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: kgremban
ms.openlocfilehash: 8c575c6d34543cbd8f692c64b43cf738b4c22617
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415625"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Monitorování stavu služby Azure IoT Hub a rychlá Diagnostika potíží

Podnikům, které implementují službu Azure IoT Hub můžete očekávat spolehlivý výkon z jejich prostředků. Abyste mohli udržovat zavřít podívejte na vaše operace, IoT Hub je plně integrována s [Azure Monitor] [ lnk-AM] a [Azure Resource Health] [ lnk-ARH]. Tyto dvě služby fungovat v kombinaci, kde přinášejí dat je potřeba nechat si řešení IoT a spuštěná v dobrém stavu. 

Azure Monitor je jediný zdroj monitorování a protokolování všech služeb Azure. Diagnostické protokoly, které generuje Azure Monitor můžete odeslat do Log Analytics a Event Hubs, Azure Storage pro vlastní zpracování. Diagnostika a metriky nastavení Azure Monitor vám poskytnou přehled o výkonu vašich prostředků. Pokračujte ve čtení tohoto článku se dozvíte, jak [použití Azure monitoru](#use-azure-monitor) službou IoT hub. 

> [!IMPORTANT]
> Události generované ve službě IoT Hub pomocí diagnostické protokoly Azure monitoru nemusí být spolehlivé nebo nejsou seřazené. Některé události může být ztracené nebo odeslaná mimo pořadí. Diagnostické protokoly také nejsou určeny v reálném čase a může trvat několik minut, než pro události zaznamenávané do cílového umístění podle vašeho výběru.

Azure Resource Health pomáhá při diagnostice a získání podpory v případě, že problém Azure ovlivňuje vaše prostředky. Přizpůsobený řídicí panel poskytuje stav aktuálním a minulém stavu pro IoT hub. Pokračujte ve čtení tohoto článku se dozvíte, jak [pomocí Azure Resource Health](#use-azure-resource-health) službou IoT hub. 

IoT Hub poskytuje také vlastní metriky, který vám pomůže porozumět stavu vašich prostředků IoT. Další informace najdete v tématu [Principy centra IoT metriky][lnk-metrics].

## <a name="use-azure-monitor"></a>Použití Azure Monitoru

Platforma Azure Monitor poskytuje diagnostické informace pro prostředky Azure, což znamená, že můžete monitorovat operace, které se provedou v rámci služby IoT hub. 

Nahrazuje nastavení diagnostiky Azure Monitor monitorovat operací služby IoT Hub. Pokud aktuálně používáte službu monitorování operací, měli byste migrovat vaše pracovní postupy. Další informace najdete v tématu [migrace ze služby operations nastavení monitorování pro diagnostiku][lnk-migrate].

Další informace o něm konkrétní metriky a události, které sleduje Azure Monitor, naleznete v tématu [podporované metriky ve službě Azure Monitor] [ lnk-AM-metrics] a [podporované služby, schémat a kategorie pro Azure Diagnostické protokoly][lnk-AM-schemas].

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Vysvětlení protokolů

Azure Monitor sleduje různé operace, ke kterým dochází ve službě IoT Hub. Každá kategorie má schéma, které definuje způsob hlášení událostí do této kategorie spadají. 

#### <a name="connections"></a>Připojení

Připojení zařízení sleduje kategorie připojit a odpojit události ze služby IoT hub, jakož i chyby. Tato kategorie je užitečné pro identifikaci pokusy o neautorizovaný připojení a nebo výstrahy, pokud ztratíte připojení k zařízení.

> [!NOTE]
> Stav spolehlivé připojení zařízení zkontrolujte [prezenčního signálu zařízení][lnk-devguide-heartbeat].


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

#### <a name="cloud-to-device-commands"></a>Příkazy z cloudu na zařízení

Příkazy typu cloud zařízení kategorie sleduje chyby, ke kterým dochází za služby IoT hub, jež se vztahují k kanál zpracování zpráv typu cloud zařízení. Tato kategorie zahrnuje chyby, ke kterým dochází z:

* Odesílání zpráv typu cloud zařízení (jako jsou chyby neautorizovaného odesílatele)
* Příjem zpráv typu cloud zařízení (jako jsou chyby byl překročen počet doručení), a
* Příjem zpráv typu cloud zařízení zpětnou vazbu (například zpětnou vazbu s prošlou platností chyby). 

Tato kategorie nebude zachytávat chyby při nesprávně zpracovat zařízení úspěšně doručit zprávu typu cloud zařízení.

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
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddresss\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-identity-operations"></a>Operace identity zařízení

Kategorie zařízení identity operace sleduje chyby, ke kterým dochází při pokusu o vytvoření, aktualizaci nebo odstraňte položku v registru identit služby IoT hub. Sledování této kategorie je užitečné pro zřizování scénáře.

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

Kategorie směrování zpráv sleduje chyb vzniklých při hodnocení trasy zpráv a koncový bod stavu vnímanou ve službě IoT Hub. Tato kategorie zahrnuje události, jako například:

* Pravidlo vyhodnotí na "undefined",
* IoT Hub označí koncový bod jako nedoručenou, nebo
* Žádné chyby přijaté z koncového bodu. 

Tato kategorie neobsahuje konkrétní chyby o samotné zprávy (např. zařízení chybám omezování), které jsou hlášeny v kategorii "telemetrii zařízení".

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

Kategorie telemetrie zařízení sleduje chyby, ke kterým dochází za služby IoT hub, jež se vztahují k telemetrická data profilace. Tato kategorie zahrnuje chyby, ke kterým dochází při odesílání telemetrických událostí (například omezování využití) a příjem telemetrických událostí (například neoprávněné čtečky). Tato kategorie nemůže zachytávat chyby způsobené kód spuštěný na samotném zařízení.

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

Kategorie nahrávání souborů sleduje chybách, ke kterým dochází za služby IoT hub a souvisí s funkcí odesílání souborů. Tato kategorie zahrnuje:

* Chyby, ke kterým dochází s identifikátorem URI SAS, jako je například vypršení platnosti předtím, než oznámí zařízení centra dokončená nahrávání.
* Nepovedlo nahrávání oznámí zařízení.
* Chyby, ke kterým dochází při soubor nebyl nalezen v úložišti během vytváření zprávy oznámení služby IoT Hub.

Tato kategorie nemůže zachytávat chyby, které se stanou přímo ve chvíli, kdy zařízení odesílá do souboru do úložiště.

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

#### <a name="cloud-to-device-twin-operations"></a>Operace dvojčete typu cloud zařízení

Kategorie typu cloud zařízení dvojčete operace sleduje spouštěných službou události na dvojčata zařízení. Tyto operace můžete zahrnout dvojčete get, aktualizovat nebo nahradit značek a aktualizovat nebo nahradit požadované vlastnosti. 

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

#### <a name="device-to-cloud-twin-operations"></a>Operace dvojčete zařízení cloud

Kategorie operace dvojčete zařízení cloud sleduje zařízení iniciované události na dvojčata zařízení. Tyto operace můžete zahrnout dvojčete get, aktualizaci ohlášených vlastností a přihlaste se k požadované vlastnosti odběru.

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

#### <a name="twin-queries"></a>Dotazy dvojčete

Kategorie dotazy dvojčete hlásí požadavků na dotazy na dvojčata zařízení, které jsou spouštěné v cloudu. 

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

Kategorie úlohy operace hlásí žádosti o úlohu Aktualizovat dvojče zařízení nebo vyvolání přímých metod na více zařízeních. Tyto požadavky jsou spuštěny v cloudu. 

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

Kategorie přímých metod sleduje zasílat jednotlivým příjemcům interakce typu žádost odpověď. Tyto požadavky jsou spuštěny v cloudu. 

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

Kategorie distribuované trasování sleduje ID korelace pro zprávy, které zajišťují trasování hlavičku kontextu. Tyto protokoly povolit plně, musí aktualizovat kód na straně klienta podle [analýza a Diagnostika IoT aplikace začátku do konce pomocí distribuované trasování služby IoT Hub (preview)](iot-hub-distributed-tracing.md).

Všimněte si, že `correlationId` a odpovídají [W3C trasování kontextu](https://github.com/w3c/trace-context) návrh, kde obsahuje `trace-id` a také `span-id`. 

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub D2C protokoly (typu zařízení cloud)

Při doručení zprávy obsahující vlastnosti platný trasování ve službě IoT Hub, IoT Hub zaznamenává tento protokol. 

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

Tady `durationMs` se vypočte jako nemusí být služby IoT Hub hodiny synchronizované s hodinami zařízení, a proto může být zavádějící výpočet doby trvání. Doporučujeme zápis pomocí logiky časová razítka v `properties` oddílu zachycení špičky v latence typu zařízení cloud.

| Vlastnost | Typ | Popis |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Integer | Velikost zprávy typu zařízení cloud v bajtech |
| **deviceId** | Řetězec alfanumerických znaků ASCII 7 bitů | Identita zařízení |
| **callerLocalTimeUtc** | Časové razítko UTC | Čas vytvoření zprávy podle zařízení místní hodiny |
| **calleeLocalTimeUtc** | Časové razítko UTC | Čas doručení zprávy ve službě IoT Hub gateway podle hodin na straně služby IoT Hub |

##### <a name="iot-hub-ingress-logs"></a>Protokoly příchozího přenosu dat centra IoT

IoT Hub zaznamenává tento protokol, pokud zpráva obsahující vlastnosti platný trasování zapíše do interní nebo integrované centra událostí.

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

V `properties` část, tento protokol obsahuje další informace o zprávě příchozího přenosu dat

| Vlastnost | Typ | Popis |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | True nebo false, označuje, zda je povoleno směrování zpráv ve službě IoT Hub |
| **parentSpanId** | String | [Rozsah id](https://w3c.github.io/trace-context/#parent-id) nadřazené zprávy, která by v tomto případě D2C trasování zpráv |

##### <a name="iot-hub-egress-logs"></a>Protokoly služby IoT Hub odchozího přenosu dat

IoT Hub záznamy to při protokolování [směrování](iot-hub-devguide-messages-d2c.md) je povolená a zapíše se do [koncový bod](iot-hub-devguide-endpoints.md). Pokud není povoleno směrování, IoT Hub nezaznamenává tento protokol.

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

V `properties` část, tento protokol obsahuje další informace o zprávě příchozího přenosu dat

| Vlastnost | Typ | Popis |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | String | Název směrování koncového bodu |
| **endpointType** | String | Typ směrování koncového bodu |
| **parentSpanId** | String | [Rozsah id](https://w3c.github.io/trace-context/#parent-id) nadřazené zprávy, kterou v tomto případě by trasování zpráv služby IoT Hub příchozího přenosu dat |


### <a name="read-logs-from-azure-event-hubs"></a>Čtení protokolů z Azure Event Hubs

Jakmile nastavíte přes nastavení diagnostiky protokolování událostí, můžete vytvořit aplikace, které se přečte nahlas protokoly tak, aby mohli podniknout kroky na základě informací v nich. Tento ukázkový kód načte protokoly z centra událostí:

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
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds; 
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

## <a name="use-azure-resource-health"></a>Pomocí Azure Resource Health

Pomocí Azure Resource Health můžete sledovat, jestli služby IoT hub je zprovozněný. Můžete také zjistěte, zda je k oblastnímu výpadku vliv na stav služby IoT hub. Informace o tom konkrétní podrobnosti o stavu služby Azure IoT Hub, doporučujeme vám [použití Azure monitoru](#use-azure-monitor). 

Azure IoT Hub označuje stav na místní úrovni. Pokud oblastní výpadek ovlivní služby IoT hub, stav zobrazuje jako **neznámý**. Další informace najdete v tématu [typy prostředků a kontroly stavu ve službě Azure resource health][lnk-ARH-checks].

Pokud chcete zkontrolovat stav vašeho centra IoT hub, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte do **Service Health** > **Resource health**.
1. V rozevíracích seznamech vyberte předplatné a **služby IoT Hub**.

Další informace o tom, jak interpretovat data o stavu, najdete v článku [přehled Azure resource health][lnk-ARH]

## <a name="next-steps"></a>Další postup

- [Vysvětlení metriky služby IoT Hub][lnk-metrics]
- [Sada IoT vzdálené monitorování a oznámení pomocí Azure Logic Apps propojení vaší služby IoT hub a poštovní schránky][lnk-monitoring-notifications]


[lnk-AM]: ../azure-monitor/index.yml
[lnk-ARH]: ../service-health/resource-health-overview.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-migrate]: iot-hub-migrate-to-diagnostics-settings.md
[lnk-AM-metrics]: ../azure-monitor/platform/metrics-supported.md
[lnk-AM-schemas]: ../azure-monitor/platform/diagnostic-logs-schema.md
[lnk-ARH-checks]: ../service-health/resource-health-checks-resource-types.md
[lnk-monitoring-notifications]: iot-hub-monitoring-notifications-with-azure-logic-apps.md
[lnk-devguide-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat

---
title: Monitorování stavu služby Azure IoT Hub | Microsoft Docs
description: Použít ke sledování služby IoT Hub a rychle diagnostikovat problémy Azure monitorování a stav prostředků Azure
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/09/2017
ms.author: kgremban
ms.openlocfilehash: 39171f7d7a7b27ec54f67b592e184e90134a1a52
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850385"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Monitorování stavu služby Azure IoT Hub a rychle diagnostikovat problémy

Podnikům, které implementují Azure IoT Hub očekávat spolehlivý výkon z jejich prostředky. Chcete-li udržovat zavřít sledovat na vaše operace, IoT Hub je plně integrována s [Azure monitorování] [ lnk-AM] a [stav prostředku Azure] [ lnk-ARH]. Tyto dvě služby fungovat v kombinaci, kde přinášejí dat je třeba zachovat řešení IoT nahoru a spouštění v dobrém stavu. 

Azure monitorování je jednoho zdroje sledování a protokolování pro všechny vaše služby Azure. Diagnostické protokoly, které generuje monitorování Azure můžete poslat analýzy protokolů a Event Hubs, Azure Storage pro vlastní zpracování. Nastavení metriky a diagnostiky Azure monitorování získáte přehled o výkonu z vašich prostředků. Pokračujte ve čtení v tomto článku se dozvíte jak [použití Azure monitorování](#use-azure-monitor) službou IoT hub. 

> [!IMPORTANT]
> Události vygenerované službou IoT Hub pomocí diagnostických protokolů Azure monitorování není zaručena bezpečnost pro spolehlivé nebo seřazené. Některé události může být ke ztrátě nebo doručit mimo pořadí. Diagnostické protokoly také nejsou určeny jako v reálném čase a že může trvat několik minut pro události, která je zaznamenána do zvoleného cíle.

Azure Resource Health umožňuje diagnostikovat a získat podporu, když Azure problémy ovlivní vaše prostředky. Přizpůsobený řídicí panel poskytuje aktuální a starší stav pro vaše centra IoT. Pokračujte ve čtení v tomto článku se dozvíte jak [pomocí Azure Resource Health](#use-azure-resource-health) službou IoT hub. 

Kromě integraci s těmito dvěma službami, služba IoT Hub zajišťuje také vlastní metriky, které můžete použít k pochopení stavu prostředků IoT. Další informace najdete v tématu [pochopit IoT Hub metriky][lnk-metrics].

## <a name="use-azure-monitor"></a>Použití Azure Monitoru

Monitorování Azure poskytuje úrovni prostředků diagnostické informace, což znamená, že můžete monitorovat operace, které v rámci služby IoT hub. 

Nahrazuje nastavení diagnostiky Azure monitorování monitorování činnosti IoT Hub. Pokud aktuálně používáte operace sledování, je potřeba migrovat vaše pracovní postupy. Další informace najdete v tématu [migrací z operace nastavení sledování diagnostiku][lnk-migrate].

Další informace o konkrétní metriky a události, které sleduje monitorování Azure najdete v tématu [podporované metriky s Azure monitorování] [ lnk-AM-metrics] a [podporované kategorií, schémat a služby pro Azure. Diagnostické protokoly][lnk-AM-schemas].

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Pochopení protokoly

Azure Monitor sleduje různé operace, které se vyskytují v IoT Hub. Každá kategorie obsahuje schéma, které definuje způsob hlášení události v této kategorii. 

#### <a name="connections"></a>Připojení

Připojení zařízení sleduje kategorie připojit a odpojit události od služby IoT hub, jakož i chyby. Sledování této kategorie jsou užitečné pro identifikaci pokusy o neoprávněné připojení a sledování při ztrátě pro zařízení v oblastech malé možnosti připojení připojení.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Information",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}", 
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-commands"></a>Příkazy typu cloud zařízení

Kategorie příkazy typu cloud zařízení sleduje chybách, ke kterým dochází při služby IoT hub a souvisí s kanálu zpráv typu cloud zařízení. Tato kategorie zahrnuje chyb vzniklých při odesílání zpráv typu cloud zařízení (například neautorizovaného odesílatele), přijímání zpráv typu cloud zařízení (například počet doručení překročena) a přijímání zpráv typu cloud zařízení zpětnou vazbu (například zpětné vazby jeho platnost). Tuto kategorii nezachytí chyby ze zařízení, která nesprávně zpracovává zprávy typu cloud zařízení, pokud zpráva cloud zařízení byla úspěšně doručeno.

```json
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
```

#### <a name="device-identity-operations"></a>Operace identity zařízení

Kategorie operations identity zařízení sleduje chyby, ke kterým dochází při pokusu o vytvoření, aktualizace nebo odstranění položku v registru identit služby IoT hub. Sledování této kategorie jsou užitečné pro zřizování scénáře.

```json
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
```

#### <a name="routes"></a>Trasy

Kategorie směrování zpráv sleduje chyb vzniklých při vyhodnocení zpráva trasy a stav koncového bodu zaznamenatelného službou IoT Hub. Tato kategorie zahrnuje události, například pokud se pravidlo vyhodnotí jako "undefined", když IoT Hub označí koncový bod zpráv a všechny ostatní chyby přijaté z koncového bodu. Tato kategorie nezahrnuje konkrétní chyby týkající se zpráv sami (například zařízení omezení chyby), které jsou v části kategorie "zařízení telemetrická".

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "endpointUnhealthy",
    "category": "Routes",
    "level": "Error",
    "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
    "location": "Resource location"
}
```

#### <a name="device-telemetry"></a>Zařízení telemetrie

Kategorie zařízení telemetrie sleduje chybách, ke kterým dochází při služby IoT hub a souvisí s telemetrie kanálu. Tato kategorie zahrnuje chyb vzniklých při odesílání telemetrie událostí (například omezení šířky pásma) a příjem telemetrické události (například neoprávněným čtečky). Tuto kategorii nelze catch chyby způsobené kód spuštěný v samotném zařízení.

```json
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
```

#### <a name="file-upload-operations"></a>Operace nahrávání souborů

Kategorie nahrávání souboru sleduje chybách, ke kterým dochází při služby IoT hub a souvisí s funkcí nahrávání souboru. Tato kategorie zahrnuje:

* Chyby, které se identifikátor URI SAS, jako je například vypršení platnosti před zařízení upozorní rozbočovače dokončená nahrávání.
* Nepodařilo nahrávání údajů ze zařízení.
* Chyby, které nastat, pokud soubor nebyl nalezen v úložišti během vytváření zpráv oznámení služby IoT Hub.

Tuto kategorii nelze catch chyb, které přímo nastat, když je zařízení nahrání souboru do úložiště.

```json
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
```

#### <a name="cloud-to-device-twin-operations"></a>Operace dvojče cloud zařízení

Kategorie operations dvojče cloud zařízení sleduje události spouštěná služba na dvojčata zařízení. Tyto operace může obsahovat dvojici get, hlášen vlastnostech aktualizace a přihlásit se k požadované vlastnosti

```json
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
```

#### <a name="device-to-cloud-twin-operations"></a>Operace twin zařízení cloud

Operace kategorii zařízení cloud twin sleduje události spouštěná zařízení na dvojčata zařízení. Tyto operace můžete zahrnout get twin, aktualizovat nebo nahradit značky a aktualizovat nebo nahradit požadované vlastnosti. 

```json
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
```

#### <a name="twin-queries"></a>Dotazy Twin

Kategorie dotazy twin hlásí požadavků na dotazy pro dvojčata zařízení, které jsou zahájeny v cloudu. 

```json
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
```

#### <a name="jobs-operations"></a>Operace úloh

Kategorie úloh operations hlásí žádostí o úlohy aktualizace dvojčata zařízení nebo volat přímé metody na několika zařízeních. Tyto požadavky se spouští v cloudu. 

```json
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
```

#### <a name="direct-methods"></a>Přímé metody

Kategorie přímé metody sleduje interakce odezvy požadavku, odeslané do jednotlivých zařízení. Tyto požadavky se spouští v cloudu. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "send",
    "category": "DirectMethods",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\", \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}", 
    "location": "Resource location"
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Protokoly pro čtení z Azure Event Hubs

Po nastavení protokolování prostřednictvím nastavení diagnostiky událostí můžete vytvořit aplikace, které čtou se protokoly tak, aby můžete provést akci na základě informací v nich. Tento ukázkový kód načte protokoly z centra událostí:

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

## <a name="use-azure-resource-health"></a>Použití stavu prostředků Azure.

Pomocí Azure Resource Health sledovat, jestli služby IoT hub je spuštěná. Můžete také zjistěte, zda je místní výpadku ovlivňující stav služby IoT hub. Zjistit konkrétní podrobnosti o stavu služby Azure IoT Hub, doporučujeme, aby vám [použití Azure monitorování](#use-azure-monitor). 

Azure IoT Hub značí stavu na místní úrovni. Pokud existuje místní výpadku služby IoT hub, které mají vliv stav zobrazuje jako **neznámé**. Další informace o konkrétní stav kontroly, které provádí stav prostředku Azure, najdete v části [typy prostředků a stav kontrol ve stavu prostředků Azure][lnk-ARH-checks].

Pokud chcete zkontrolovat stav vašeho centra IoT, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte na **stav služby** > **stav prostředku**.
1. Z rozevíracího seznamu polí, vyberte své předplatné a **IoT Hub**.

Další informace o tom, jak interpretovat data o stavu, v tématu [přehled stavu prostředků Azure.][lnk-ARH]

## <a name="next-steps"></a>Další postup

- [Pochopení metriky služby IoT Hub][lnk-metrics]
- [IoT pro vzdálené monitorování a oznámení službou Azure Logic Apps připojení služby IoT hub a poštovní schránky][lnk-monitoring-notifications]


[lnk-AM]: ../monitoring-and-diagnostics/index.yml
[lnk-ARH]: ../service-health/resource-health-overview.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-migrate]: iot-hub-migrate-to-diagnostics-settings.md
[lnk-AM-metrics]: ../monitoring-and-diagnostics/monitoring-supported-metrics.md
[lnk-AM-schemas]: ../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md
[lnk-ARH-checks]: ../service-health/resource-health-checks-resource-types.md
[lnk-monitoring-notifications]: iot-hub-monitoring-notifications-with-azure-logic-apps.md

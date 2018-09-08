---
title: Monitorování operací Azure IoT Hub | Dokumentace Microsoftu
description: Jak používat Azure IoT Hub operace monitorování ke sledování stavu operací ve službě IoT hub v reálném čase.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.author: nberdy
ms.openlocfilehash: 3aa452cd178bd0d064726c5be7dbdf65c6ef8d92
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160044"
---
# <a name="iot-hub-operations-monitoring"></a>Monitorování operací služby IoT Hub

Monitorování operací služby IoT Hub umožňuje monitorovat stav operací ve službě IoT hub v reálném čase. IoT Hub sleduje události napříč několika kategoriemi těchto operací. Můžete se rozhodnout do odesílání událostí z jedné nebo více kategorií pro koncový bod služby IoT hub pro zpracování. Můžete monitorovat data o chybách nebo nastavení složitější zpracování na základě způsobů data.

>[!NOTE]
>Monitorování operací služby IoT Hub je zastaralá a bude odebrán ze služby IoT Hub na 10. března 2019. Monitorování operací a stavu služby IoT Hub, najdete v části [monitorování stavu služby Azure IoT Hub a rychlá Diagnostika potíží][lnk-monitor]. Další informace o vyřazení časové osy, naleznete v tématu [monitorování řešení Azure IoT pomocí Azure monitoru a Azure Resource Health][lnk-blog-announcement].

IoT Hub monitoruje šest kategorie události:

* Operace identity zařízení
* Telemetrie zařízení
* Zprávy typu cloud zařízení
* Připojení
* Nahrání souborů
* Směrování zpráv

> [!IMPORTANT]
> Monitorování operací služby IoT Hub nezaručuje doručení událostí spolehlivé nebo nejsou seřazené. V závislosti na základní infrastruktury služby IoT Hub může být některé události ztráty nebo odeslaná mimo pořadí. Pomocí operací monitorování Generovat výstrahy na základě signálů chyby, jako je například neúspěšné pokusy o připojení nebo odpojení vysokou frekvencí pro konkrétní zařízení. Neměli byste tedy spoléhat na provoz, monitorování událostí k vytvoření konzistentní úložiště pro stav zařízení, třeba úložiště sledování připojení nebo odpojení stav zařízení. 

## <a name="how-to-enable-operations-monitoring"></a>Povolení monitorování operací

1. Vytvoření služby IoT hub. Můžete najít pokyny o tom, jak vytvořit IoT hub v [Začínáme] [ lnk-get-started] průvodce.

1. Otevře se okno služby IoT hub. Odtud, klikněte na tlačítko **monitorování operací**.

    ![Konfigurace na portálu pro sledování operací přístupu k][1]

1. Vyberte monitorování kategorie, které chcete monitorovat a potom klikněte na **Uložit**. Události jsou k dispozici pro čtení z koncového bodu kompatibilní s centrem událostí uvedený v **nastavení monitorování**. Koncový bod služby IoT Hub se nazývá `messages/operationsmonitoringevents`.

    ![Konfigurace monitorování ve službě IoT hub operací][2]

> [!NOTE]
> Výběr **Verbose** monitorování **připojení** kategorie způsobí, že služby IoT Hub generovat další diagnostické informace. U všech ostatních kategorií **Verbose** množství informací o službě IoT Hub změny nastavení obsahuje každá chybová zpráva.

## <a name="event-categories-and-how-to-use-them"></a>Kategorie události a jejich použití

Každé monitorování sleduje kategorii operací má jiný typ interakce s centrem IoT a každou kategorii monitorování schéma, který definuje, jak jsou strukturované události do této kategorie spadají.

### <a name="device-identity-operations"></a>Operace identity zařízení

Kategorie zařízení identity operace sleduje chyby, ke kterým dochází při pokusu o vytvoření, aktualizaci nebo odstraňte položku v registru identit služby IoT hub. Sledování této kategorie je užitečné pro zřizování scénáře.

```json
{
    "time": "UTC timestamp",
    "operationName": "create",
    "category": "DeviceIdentityOperations",
    "level": "Error",
    "statusCode": 4XX,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "durationMs": 1234,
    "userAgent": "userAgent",
    "sharedAccessPolicy": "accessPolicy"
}
```

### <a name="device-telemetry"></a>Telemetrie zařízení

Kategorie telemetrie zařízení sleduje chyby, ke kterým dochází za služby IoT hub, jež se vztahují k telemetrická data profilace. Tato kategorie zahrnuje chyby, ke kterým dochází při odesílání telemetrických událostí (například omezování využití) a příjem telemetrických událostí (například neoprávněné čtečky). Tato kategorie nemůže zachytávat chyby způsobené kód spuštěný na samotném zařízení.

```json
{
    "messageSizeInBytes": 1234,
    "batching": 0,
    "protocol": "Amqp",
    "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="cloud-to-device-commands"></a>Příkazy typu cloud zařízení

Příkazy typu cloud zařízení kategorie sleduje chyby, ke kterým dochází za služby IoT hub, jež se vztahují k kanál zpracování zpráv typu cloud zařízení. Tato kategorie zahrnuje chyby, ke kterým dochází při odesílání zprávy typu cloud zařízení (například neautorizovaného odesílatele), příjem zpráv typu cloud zařízení (například překročení počtu doručených položek) a příjem zpráv typu cloud zařízení zpětnou vazbu (např. zpětnou vazbu s prošlou platností). Tato kategorie nebude zachytávat chyby ze zařízení, která zpracovává nesprávně zprávu typu cloud zařízení, pokud byl úspěšně doručit zprávu typu cloud zařízení.

```json
{
    "messageSizeInBytes": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "deliveryAcknowledgement": 0,
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "C2DCommands",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="connections"></a>Připojení

Kategorie připojení sleduje chyby, ke které dochází, když zařízení připojit nebo odpojit od služby IoT hub. Sledování této kategorie je užitečné pro identifikaci pokusy o neautorizovaný připojení a pro sledování při ztrátě zařízení v oblastech špatnému připojení k připojení.

```json
{
    "durationMs": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID"
}
```

### <a name="file-uploads"></a>Nahrání souborů

Kategorie nahrávání souborů sleduje chybách, ke kterým dochází za služby IoT hub a souvisí s funkcí odesílání souborů. Tato kategorie zahrnuje:

* Chyby, ke kterým dochází s identifikátorem URI SAS, jako je například vypršení platnosti předtím, než oznámí zařízení centra dokončená nahrávání.
* Nepovedlo nahrávání oznámí zařízení.
* Chyby, ke kterým dochází při soubor nebyl nalezen v úložišti během vytváření zprávy oznámení služby IoT Hub.

Tato kategorie nemůže zachytávat chyby, které se stanou přímo ve chvíli, kdy zařízení odesílá do souboru do úložiště.

```json
{
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "HTTP",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "fileUpload",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "blobUri": "http//bloburi.com",
    "durationMs": 1234
}
```

### <a name="message-routing"></a>Směrování zpráv

Kategorie směrování zpráv sleduje chyb vzniklých při hodnocení trasy zpráv a koncový bod stavu vnímanou ve službě IoT Hub. Tato kategorie zahrnuje události, jako když pravidlo vyhodnotí jako "undefined", když IoT Hub označí koncový bod jako dead a případných dalších chybách přijatých z koncového bodu. Tato kategorie neobsahuje konkrétní chyby o samotné zprávy (jako je například zařízení chybám omezování), které jsou hlášeny v kategorii "telemetrii zařízení".

```json
{
    "messageSizeInBytes": 1234,
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "routes",
    "level": "Error",
    "deviceId": "device-ID",
    "messageId": "ID of message",
    "routeName": "myroute",
    "endpointName": "myendpoint",
    "details": "ExternalEndpointDisabled"
}
```

## <a name="connect-to-the-monitoring-endpoint"></a>Připojte se k monitorování koncového bodu

Monitorování koncového bodu ve službě IoT hub je koncový bod kompatibilní s centrem událostí. Můžete použít libovolný mechanismus, který funguje s Event Hubs slouží ke čtení z tohoto koncového bodu monitorování zpráv. Následující příklad vytvoří čtečku, která není vhodná pro vysoce výkonná nasazení. Další informace o zpracování zpráv ze služby Event Hubs najdete v kurzu [Začínáme se službou Event Hubs][lnk-eventhubs-tutorial].

Pro připojení k monitorování koncového bodu, budete potřebovat připojovací řetězec a název koncového bodu. Následující kroky ukazují, jak najít potřebné hodnoty na portálu:

1. Na portálu přejděte do okna vaší služby IoT Hub prostředků.

1. Zvolte **monitorování operací**a poznamenejte si, **název kompatibilní s centrem událostí** a **koncový bod kompatibilní s centrem událostí** hodnoty:

    ![Hodnoty koncového bodu kompatibilního s centrem událostí][img-endpoints]

1. Zvolte **zásady sdíleného přístupu**, klikněte na tlačítko **služby**. Poznamenejte si, **primární klíč** hodnotu:

    ![Primární klíč zásad sdíleného přístupu služby][img-service-key]

Následující vzorový kód jazyka C# je přijata ze sady Visual Studio **klasická plocha Windows** konzolovou aplikaci C#. Projekt má **WindowsAzure.ServiceBus** nainstalovaný balíček NuGet.

* Nahraďte zástupný symbol připojovacího řetězce připojovacím připojovacím řetězcem, který používá **koncový bod kompatibilní s centrem událostí** a služba **primární klíč** hodnoty, které jste si poznamenali dříve, jak je znázorněno v následujícím příkladu:

    ```cs
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Nahraďte monitorování zástupný název koncového bodu s **název kompatibilní s centrem událostí** hodnotu, kterou jste si poznamenali dříve.

```cs
class Program
{
    static string connectionString = "{your monitoring endpoint connection string}";
    static string monitoringEndpointName = "{your monitoring endpoint name}";
    static EventHubClient eventHubClient;

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
            }
        }
    }
}
```

## <a name="next-steps"></a>Další postup
Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

* [Příručka vývojáře pro IoT Hub][lnk-devguide]
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png
[img-endpoints]: media/iot-hub-operations-monitoring/monitoring-endpoint.png
[img-service-key]: media/iot-hub-operations-monitoring/service-key.png

[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health
[lnk-monitor]: iot-hub-monitor-resource-health.md
[lnk-get-started]: quickstart-send-telemetry-dotnet.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md

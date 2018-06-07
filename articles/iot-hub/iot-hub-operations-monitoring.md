---
title: Operace v Azure IoT Hub monitorování | Microsoft Docs
description: Jak používat Azure IoT Hub operations monitorování ke sledování stavu operací ve službě IoT hub v reálném čase.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.author: nberdy
ms.openlocfilehash: 0a1da3812d6f11aa6525857596b394fbfa3dc88a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634800"
---
# <a name="iot-hub-operations-monitoring"></a>Operace služby IoT Hub monitorování

Operace služby IoT Hub monitorování umožňuje sledovat stav operace ve službě IoT hub v reálném čase. IoT Hub sleduje události napříč několika kategorií operací. Můžete se taky rozhodnout do odesílání událostí z jedné nebo více kategorií pro koncový bod služby IoT hub pro zpracování. Můžete sledovat data chyby nebo nastavit složitější zpracování vámi data.

>[!NOTE]
>Monitorování operací služby IoT Hub je zastaralá a odebere ze služby IoT Hub na 10. října 2018. Monitorování operací a stavu služby IoT Hub, najdete v části [monitorování stavu služby Azure IoT Hub a rychle diagnostikovat problémy][lnk-monitor]. Další informace o vyřazení časovou osu najdete v tématu [monitorovat řešení Azure IoT s monitorování Azure a Azure Resource Health][lnk-blog-announcement].

IoT Hub monitoruje šesti kategorie události:

* Operace identity zařízení
* Zařízení telemetrie
* Zprávy typu cloud zařízení
* Připojení
* Nahrávání souborů
* Směrování zpráv

> [!IMPORTANT]
> Operace služby IoT Hub monitorování nezaručuje spolehlivé nebo seřazené doručení událostí. V závislosti na IoT Hub podpůrné infrastruktuře může být některé události ztráty nebo doručit mimo pořadí. Pomocí operací monitorování Generovat výstrahy založené na signály chyba například neúspěšné pokusy o připojení nebo odpojení vysoká frekvence pro konkrétní zařízení. Neměli byste tedy spoléhat na operace sledování událostí pro vytvoření konzistentní úložiště pro stavu zařízení, např. obchod sledování připojení nebo odpojení stavu zařízení. 

## <a name="how-to-enable-operations-monitoring"></a>Postup povolení operací monitorování

1. Vytvoření služby IoT hub. Pokyny k vytvoření služby IoT hub v naleznete [Začínáme] [ lnk-get-started] průvodce.

1. Otevřete okno služby IoT hub. Zde, klikněte na tlačítko **Operations monitorování**.

    ![Monitorování konfigurace na portálu pro operace přístupu.][1]

1. Vyberte monitorování kategorie, které chcete monitorovat a pak klikněte na tlačítko **Uložit**. Události jsou k dispozici pro čtení z koncového bodu kompatibilního s centrem událostí uvedený v **nastavení sledování**. Koncový bod centra IoT se nazývá `messages/operationsmonitoringevents`.

    ![Konfigurace operací monitorování ve službě IoT hub][2]

> [!NOTE]
> Výběr **podrobné** monitorování **připojení** kategorie způsobí, že Centrum IoT k vygenerování hlášení další diagnostiky. U všech ostatních kategorií **podrobné** změny nastavení množství informací IoT Hub obsahuje v každém chybová zpráva.

## <a name="event-categories-and-how-to-use-them"></a>Kategorie události a jejich použití

Každý operations monitorování sleduje kategorie má jiný typ interakci s IoT Hub a každou kategorii monitorování schéma, které definuje, jak jsou strukturovaná události v této kategorii.

### <a name="device-identity-operations"></a>Operace identity zařízení

Kategorie operations identity zařízení sleduje chyby, ke kterým dochází při pokusu o vytvoření, aktualizace nebo odstranění položku v registru identit služby IoT hub. Sledování této kategorie jsou užitečné pro zřizování scénáře.

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

### <a name="device-telemetry"></a>Zařízení telemetrie

Kategorie zařízení telemetrie sleduje chybách, ke kterým dochází při služby IoT hub a souvisí s telemetrie kanálu. Tato kategorie zahrnuje chyb vzniklých při odesílání telemetrie událostí (například omezení šířky pásma) a příjem telemetrické události (například neoprávněným čtečky). Tuto kategorii nelze catch chyby způsobené kód spuštěný v samotném zařízení.

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

Kategorie příkazy typu cloud zařízení sleduje chybách, ke kterým dochází při služby IoT hub a souvisí s kanálu zpráv typu cloud zařízení. Tato kategorie zahrnuje chyb vzniklých při odesílání zpráv typu cloud zařízení (například neautorizovaného odesílatele), přijímání zpráv typu cloud zařízení (například počet doručení překročena) a přijímání zpráv typu cloud zařízení zpětnou vazbu (například zpětné vazby jeho platnost). Tuto kategorii nezachytí chyby ze zařízení, která nesprávně zpracovává zprávy typu cloud zařízení, pokud zpráva cloud zařízení byla úspěšně doručeno.

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

Kategorie připojení sleduje chyb vzniklých při zařízení připojení nebo odpojení od služby IoT hub. Sledování této kategorie jsou užitečné pro identifikaci pokusy o neoprávněné připojení a sledování při ztrátě pro zařízení v oblastech malé možnosti připojení připojení.

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

### <a name="file-uploads"></a>Nahrávání souborů

Kategorie nahrávání souboru sleduje chybách, ke kterým dochází při služby IoT hub a souvisí s funkcí nahrávání souboru. Tato kategorie zahrnuje:

* Chyby, které se identifikátor URI SAS, jako je například vypršení platnosti před zařízení upozorní rozbočovače dokončená nahrávání.
* Nepodařilo nahrávání údajů ze zařízení.
* Chyby, které nastat, pokud soubor nebyl nalezen v úložišti během vytváření zpráv oznámení služby IoT Hub.

Tuto kategorii nelze catch chyb, které přímo nastat, když je zařízení nahrání souboru do úložiště.

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

Kategorie směrování zpráv sleduje chyb vzniklých při vyhodnocení zpráva trasy a stav koncového bodu zaznamenatelného službou IoT Hub. Tato kategorie zahrnuje události, například pokud se pravidlo vyhodnotí jako "undefined", když IoT Hub označí koncový bod zpráv a všechny ostatní chyby přijaté z koncového bodu. Tato kategorie nezahrnuje konkrétní chyby týkající se zpráv sami (například zařízení omezení chyby), které jsou v části kategorie "zařízení telemetrická".

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

## <a name="view-events"></a>Zobrazení událostí

Můžete použít *iothub-explorer* nástroj rychle otestovat, zda služby IoT hub je generování události monitorování. Chcete-li nainstalovat nástroj, postupujte podle pokynů v [iothub-explorer] [ lnk-iothub-explorer] úložiště GitHub.

1. Zajistěte, aby **připojení** monitorování kategorie je nastaven na **podrobné** na portálu.

1. V příkazovém řádku spusťte následující příkaz pro čtení z koncového bodu monitorování:

    ```
    iothub-explorer monitor-ops --login {your iothubowner connection string}
    ```

1. V jiné příkazového řádku spusťte následující příkaz k simulaci zařízení odesílání zpráv typu zařízení cloud:

    ```
    iothub-explorer simulate-device {your device name} --send "My test message" --login {your iothubowner connection string}
    ```

1. První příkazového řádku ukazuje události monitorování simulované zařízení připojí ke službě IoT hub.

## <a name="connect-to-the-monitoring-endpoint"></a>Připojení ke koncovému bodu monitorování

Monitorování koncový bod ve službě IoT hub je koncový bod kompatibilní s centrem událostí. Můžete použít všechny mechanismus, který funguje se službou Event Hubs umožní číst zprávy typu monitorování z tento koncový bod. Následující příklad vytvoří základní čtečka, která není vhodná pro vysoce výkonná nasazení. Další informace o zpracování zpráv ze služby Event Hubs najdete v kurzu [Začínáme se službou Event Hubs][lnk-eventhubs-tutorial].

Pro připojení k monitorování koncového bodu, budete potřebovat připojovací řetězec a název koncového bodu. Následující kroky vám ukážou, jak najít potřebné hodnoty na portálu:

1. Na portálu přejděte do okna prostředků vaší služby IoT Hub.

1. Zvolte **Operations monitorování**a poznamenejte si **název kompatibilní s centrem událostí** a **koncový bod kompatibilní s centrem událostí** hodnoty:

    ![Hodnoty koncový bod kompatibilní s centrem událostí][img-endpoints]

1. Zvolte **zásady sdíleného přístupu**, zvolte **služby**. Poznamenejte si **primární klíč** hodnotu:

    ![Primární klíč pro zásady sdíleného přístupu služby][img-service-key]

Následující ukázka kódu C# jsou převzaty ze sady Visual Studio **Windows Classic Desktop** konzolovou aplikaci C#. Tento projekt **WindowsAzure.ServiceBus** nainstalovat balíček NuGet.

* Nahraďte zástupný symbol připojovacího řetězce připojovací řetězec, který používá **koncový bod kompatibilní s centrem událostí** a služba **primární klíč** hodnoty, které jste si poznamenali dříve, jak je znázorněno v následujícím příkladu:

    ```cs
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Nahraďte monitorování zástupný symbol název koncového bodu se **název kompatibilní s centrem událostí** hodnoty, které jste si poznamenali dříve.

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
Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

* [Příručka vývojáře pro službu IoT Hub][lnk-devguide]
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png
[img-endpoints]: media/iot-hub-operations-monitoring/monitoring-endpoint.png
[img-service-key]: media/iot-hub-operations-monitoring/service-key.png

[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health
[lnk-monitor]: iot-hub-monitor-resource-health.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md

---
title: Monitorování provozu služby Azure IoT Hub (zastaralé) | Dokumenty společnosti Microsoft
description: Jak pomocí monitorování operací Azure IoT Hub sledovat stav operací na vašem centru IoT v reálném čase.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: nberdy
ms.custom: amqp
ms.openlocfilehash: edbc3431c860794c7cd1dd8e5011c0d7d11d692d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732232"
---
# <a name="iot-hub-operations-monitoring-deprecated"></a>Monitorování operací ioT hubu (zastaralé)

Monitorování operací ioT hubu umožňuje sledovat stav operací ve vašem centru IoT hub v reálném čase. IoT Hub sleduje události v několika kategoriích operací. Můžete se přihlásit k odesílání událostí z jedné nebo více kategorií do koncového bodu vašeho centra IoT hub pro zpracování. Můžete sledovat data na chyby nebo nastavit složitější zpracování na základě datových vzorů.

>[!NOTE]
>Monitorování operací **ioT Hubu je zastaralé a bylo odebráno z IoT Hubu 10.** Monitorování provozu a stavu služby IoT Hub najdete [v tématu Sledování stavu služby Azure IoT Hub a rychlé diagnostiky problémů](iot-hub-monitor-resource-health.md). Další informace o časové ose vyřazení najdete [v tématu Monitorování řešení Azure IoT pomocí Azure Monitor a Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health).

IoT Hub monitoruje šest kategorií událostí:

* Operace identity zařízení
* Telemetrie zařízení
* Zprávy z cloudu na zařízení
* Připojení
* Nahrání souborů
* Směrování zpráv

> [!IMPORTANT]
> Monitorování provozu ioT Hubnerské operace nezaručuje spolehlivé nebo objednané doručení událostí. V závislosti na základní infrastruktuře IoT Hubu mohou být některé události ztraceny nebo doručeny mimo pořadí. Monitorování operací slouží ke generování výstrah na základě chybových signálů, jako jsou neúspěšné pokusy o připojení nebo vysokofrekvenční odpojení pro konkrétní zařízení. Při vytváření konzistentního úložiště pro stav zařízení, například na sledování úložiště připojeného nebo odpojeného stavu zařízení, byste neměli spoléhat na události monitorování operací. 

## <a name="how-to-enable-operations-monitoring"></a>Jak povolit monitorování operací

1. Vytvořte centrum IoT. Pokyny k vytvoření centra IoT najdete v příručce [Začínáme.](quickstart-send-telemetry-dotnet.md)

2. Otevřete čepel svého centra IoT hub. Odtud klepněte na **položku Sledování operací**.

    ![Konfigurace monitorování operací přístupu na portálu](./media/iot-hub-operations-monitoring/enable-OM-1.png)

3. Vyberte kategorie monitorování, které chcete sledovat, a klepněte na tlačítko **Uložit**. Události jsou k dispozici pro čtení z koncového bodu kompatibilního s centrem událostí uvedeného v **nastavení monitorování**. Koncový bod centra IoT `messages/operationsmonitoringevents`se nazývá .

    ![Konfigurace monitorování operací v centru IoT hub](./media/iot-hub-operations-monitoring/enable-OM-2.png)

> [!NOTE]
> Výběr **podrobné** monitorování pro kategorie **připojení** způsobí, že služby IoT Hub generovat další diagnostické zprávy. Pro všechny ostatní kategorie **změní** podrobné nastavení množství informací, které služba IoT Hub obsahuje v každé chybové zprávě.

## <a name="event-categories-and-how-to-use-them"></a>Kategorie událostí a jejich použití

Každá kategorie monitorování operací sleduje jiný typ interakce s ioT hubem a každá kategorie monitorování má schéma, které definuje, jak jsou strukturovány události v této kategorii.

### <a name="device-identity-operations"></a>Operace identity zařízení

Kategorie operací identity zařízení sleduje chyby, ke kterým dochází při pokusu o vytvoření, aktualizaci nebo odstranění položky v registru identit služby IoT hub. Sledování této kategorie je užitečné pro zřizování scénáře.

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

Kategorie telemetrie zařízení sleduje chyby, ke kterým dochází v centru IoT a souvisejí s kanálem telemetrie. Tato kategorie zahrnuje chyby, ke kterým dochází při odesílání událostí telemetrie (například omezení) a přijímání telemetrických událostí (například neautorizovaného čtecího zařízení). Tato kategorie nemůže zachytit chyby způsobené kódem spuštěným na samotném zařízení.

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

### <a name="cloud-to-device-commands"></a>Příkazy cloud-to-device

Kategorie příkazů cloud-zařízení sleduje chyby, ke kterým dochází v centru IoT a souvisejí s kanálem zpráv mezi cloudy a zařízení. Tato kategorie zahrnuje chyby, ke kterým dochází při odesílání zpráv z cloudu na zařízení (například neoprávněný odesílatel), přijímání zpráv z cloudu na zařízení (například překročení počtu doručení) a přijímání zpětné vazby zpráv z cloudu na zařízení (například vypršela platnost zpětné vazby). Tato kategorie nezachytí chyby ze zařízení, které nesprávně zpracovává zprávu z cloudu na zařízení, pokud byla zpráva z cloudu na zařízení úspěšně doručena.

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

Kategorie připojení sleduje chyby, ke kterým dochází, když se zařízení připojují nebo odpojují od služby IoT hub. Sledování této kategorie je užitečné pro identifikaci pokusů o neoprávněné připojení a pro sledování, kdy dojde ke ztrátě připojení pro zařízení v oblastech se špatným připojením.

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

Kategorie nahrávání souborů sleduje chyby, ke kterým dochází v centru IoT a souvisí s funkcí nahrávání souborů. Tato kategorie zahrnuje:

* Chyby, ke kterým dochází s identifikátorem URI SAS, například když vyprší jeho platnost předtím, než zařízení upozorní centrum dokončeného nahrávání.

* Neúspěšná nahraná nahlášená zařízením.

* Chyby, ke kterým dochází, když soubor není nalezen v úložišti během vytváření zpráv oznámení služby IoT Hub.

Tato kategorie nemůže zachytit chyby, ke kterým dochází přímo, když zařízení nahrává soubor do úložiště.

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

Kategorie směrování zpráv sleduje chyby, ke kterým dochází během vyhodnocení trasy zprávy a stavu koncového bodu, jak je vnímá služba IoT Hub. Tato kategorie zahrnuje události, jako když pravidlo vyhodnotí na "undefined", když IoT Hub označí koncový bod jako mrtvý a všechny ostatní chyby přijaté z koncového bodu. Tato kategorie nezahrnuje konkrétní chyby týkající se samotných zpráv (například chyby omezení zařízení), které jsou hlášeny v kategorii "telemetrie zařízení".

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

## <a name="connect-to-the-monitoring-endpoint"></a>Připojení ke koncovému bodu monitorování

Koncový bod monitorování ve vašem centru IoT hub je koncový bod kompatibilní s centrem událostí. Můžete použít libovolný mechanismus, který pracuje s Event Hubs ke čtení zpráv monitorování z tohoto koncového bodu. Následující ukázka vytvoří základní čtečku, která není vhodná pro nasazení s vysokou propustností. Další informace o zpracování zpráv ze služby Event Hubs najdete v kurzu [Začínáme se službou Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

Chcete-li se připojit ke koncovému bodu monitorování, potřebujete připojovací řetězec a název koncového bodu. Následující kroky ukazují, jak najít potřebné hodnoty na portálu:

1. Na portálu přejděte na okno prostředků centra IoT Hub.

2. Zvolte **Sledování operací**a poznamenejte si **název kompatibilní s** centrem událostí a hodnoty **koncového bodu kompatibilní s centrem událostí:**

    ![Hodnoty koncového bodu kompatibilní s centrem událostí](./media/iot-hub-operations-monitoring/monitoring-endpoint.png)

3. Zvolte **Zásady sdíleného přístupu**a pak zvolte **služba**. Poznamenejte si hodnotu **primárního klíče:**

    ![Primární klíč zásad sdíleného přístupu služby](./media/iot-hub-operations-monitoring/service-key.png)

Následující ukázka kódu Jazyka C# je převzata z konzolové aplikace Visual Studio **Pro Klasickou plochu** C#. V projektu je nainstalován balíček **WindowsAzure.ServiceBus** NuGet.

* Nahraďte zástupný symbol připojovacího řetězce připojovacím řetězcem, který používá koncové hodnoty a hodnoty **primárního klíče** **kompatibilní s centrem událostí,** které jste si dříve poznamenali, jak je znázorněno v následujícím příkladu:

    ```csharp
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Nahraďte zástupný symbol názvu koncového bodu monitorování hodnotou **názvu kompatibilní s centrem událostí,** kterou jste si dříve poznamenali.

```csharp
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

## <a name="next-steps"></a>Další kroky

Další informace o možnostech IoT Hubu najdete v následujících tématech:

* [Průvodce vývojáři ioT Hubu](iot-hub-devguide.md)

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
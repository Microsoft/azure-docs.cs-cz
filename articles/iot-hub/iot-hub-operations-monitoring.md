---
title: Monitorování operací Azure IoT Hub (zastaralé) | Microsoft Docs
description: Jak používat Azure IoT Hub Operations monitoring k monitorování stavu operací ve službě IoT Hub v reálném čase.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: robinsh
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 045d5693c4388c6285bc6983ac2a385ceac9f6d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94408120"
---
# <a name="iot-hub-operations-monitoring-deprecated"></a>Monitorování operací IoT Hub (zastaralé)

IoT Hub monitorování provozu vám umožní monitorovat stav operací ve službě IoT Hub v reálném čase. IoT Hub sleduje události napříč několika kategoriemi operací. Můžete se rozhodnout, že budete odesílat události z jedné nebo více kategorií do koncového bodu služby IoT Hub ke zpracování. Můžete monitorovat data pro chyby nebo nastavit složitější zpracování na základě vzorců dat.

>[!NOTE]
>**Monitorování operací IoT Hub je zastaralé a bylo odebráno z IoT Hub 10. března 2019**. Informace o monitorování provozu a stavu IoT Hub najdete v tématu [monitorování IoT Hub](monitor-iot-hub.md). Další informace o časové ose zastaralosti najdete v tématu [monitorování řešení Azure IoT pomocí Azure monitor a Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health).

IoT Hub monitoruje šest kategorií událostí:

* Operace identity zařízení
* Telemetrie zařízení
* Zprávy z cloudu na zařízení
* Připojení
* Nahrání souborů
* Směrování zpráv

> [!IMPORTANT]
> Monitorování operací IoT Hub nezaručuje spolehlivé nebo seřazené doručování událostí. V závislosti na IoT Hub základní infrastruktuře se můžou některé události ztratit nebo se doručit mimo pořadí. Pomocí monitorování operací můžete vygenerovat výstrahy na základě chybových signálů, jako jsou neúspěšné pokusy o připojení, nebo připojení s vysokou frekvencí pro konkrétní zařízení. Neměli byste spoléhat na události monitorování operací, aby se vytvořilo konzistentní úložiště pro stav zařízení, např. sledování úložiště s připojením nebo odpojením stavu zařízení. 

## <a name="how-to-enable-operations-monitoring"></a>Postup povolení monitorování operací

1. Vytvořte centrum IoT. Pokyny, jak vytvořit centrum IoT, najdete [v příručce Začínáme](quickstart-send-telemetry-dotnet.md) .

2. Otevřete okno centra IoT. Odtud klikněte na **monitorování operací**.

    ![Přístup k konfiguraci monitorování operací na portálu](./media/iot-hub-operations-monitoring/enable-OM-1.png)

3. Vyberte kategorie monitorování, které chcete monitorovat, a potom klikněte na **Uložit**. Události jsou k dispozici pro čtení z koncového bodu kompatibilního s centrem událostí uvedených v části **nastavení monitorování**. Je volán koncový bod IoT Hub `messages/operationsmonitoringevents` .

    ![Konfigurace monitorování operací ve službě IoT Hub](./media/iot-hub-operations-monitoring/enable-OM-2.png)

> [!NOTE]
> Výběr **podrobného** monitorování pro kategorii **připojení** způsobí, že IoT Hub generuje další diagnostické zprávy. U všech ostatních kategorií se v **podrobném** nastavení změní množství informací IoT Hub zahrnuje každou chybovou zprávu.

## <a name="event-categories-and-how-to-use-them"></a>Kategorie událostí a jejich použití

Každá kategorie monitorování operací sleduje jiný typ interakce s IoT Hub a každá kategorie monitorování má schéma, které definuje způsob strukturování událostí v této kategorii.

### <a name="device-identity-operations"></a>Operace identity zařízení

Kategorie operace identity zařízení sleduje chyby, ke kterým dochází při pokusu o vytvoření, aktualizaci nebo odstranění záznamu v registru identit ve službě IoT Hub. Sledování této kategorie je užitečné pro scénáře zřizování.

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

Kategorie telemetrie zařízení sleduje chyby, ke kterým dochází ve službě IoT Hub a souvisí s kanálem telemetrie. Tato kategorie zahrnuje chyby, ke kterým dochází při posílání událostí telemetrie (například omezování) a přijímání událostí telemetrie (například neoprávněné čtecí zařízení). Tato kategorie nemůže zachytit chyby způsobené kódem běžícím na samotném zařízení.

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

### <a name="cloud-to-device-commands"></a>Příkazy cloud-zařízení

Kategorie příkazy Cloud-zařízení sleduje chyby, ke kterým dochází ve službě IoT Hub a které souvisejí s kanálem zpráv z cloudu na zařízení. Tato kategorie zahrnuje chyby, ke kterým dochází při posílání zpráv z cloudu na zařízení (jako je například neoprávněný odesílatel), přijímání zpráv z cloudu na zařízení (například překročení počtu doručení) a příjem zpětné vazby ze zpráv typu cloud-zařízení (například vypršení zpětné vazby). Tato kategorie nezachycuje chyby ze zařízení, které nesprávně zpracovává zprávu typu cloud-zařízení, pokud byla zpráva typu cloud-zařízení úspěšně doručena.

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

Kategorie připojení sleduje chyby, ke kterým dochází, když se zařízení připojí nebo odpojí od služby IoT Hub. Sledování této kategorie je užitečné pro identifikaci neautorizovaných pokusů o připojení a pro sledování v případě ztráty připojení pro zařízení v oblastech špatného připojení.

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

Kategorie nahrání souborů sleduje chyby, ke kterým dochází ve službě IoT Hub a souvisí s funkcemi pro nahrávání souborů. Tato kategorie zahrnuje:

* Chyby, ke kterým dochází s identifikátorem URI SAS, například když vyprší platnost před tím, než zařízení upozorní centrum dokončeného nahrávání.

* Neúspěšná odeslání uvedená v zařízení

* Chyby, ke kterým dochází, když se během vytváření zprávy IoT Hub oznámení v úložišti nenajde soubor

Tato kategorie nemůže zachytit chyby, ke kterým přímo dojde, když zařízení nahrává do úložiště soubor.

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

Kategorie směrování zpráv sleduje chyby, ke kterým došlo během hodnocení směrování zpráv a stavu koncového bodu, jak je uvedeno IoT Hub. Tato kategorie zahrnuje události, jako je například když se pravidlo vyhodnotí jako nedefinované, když IoT Hub označí koncový bod jako mrtvý a jakékoliv další chyby přijaté z koncového bodu. Tato kategorie neobsahuje konkrétní chyby týkající se samotných zpráv (například chyby omezování zařízení), které jsou uvedeny v kategorii telemetrie zařízení.

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

## <a name="connect-to-the-monitoring-endpoint"></a>Připojit ke koncovému bodu monitorování

Koncový bod monitorování ve službě IoT Hub je koncový bod kompatibilní s centrem událostí. Ke čtení zpráv monitorování z tohoto koncového bodu můžete použít libovolný mechanismus, který spolupracuje s Event Hubs. Následující ukázka vytvoří základní čtecí modul, který není vhodný pro nasazení s vysokou propustností. Další informace o zpracování zpráv ze služby Event Hubs najdete v kurzu [Začínáme se službou Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

Pokud se chcete připojit ke koncovému bodu monitorování, potřebujete připojovací řetězec a název koncového bodu. Následující kroky ukazují, jak najít potřebné hodnoty na portálu:

1. Na portálu přejděte do okna prostředků IoT Hub.

2. Vyberte **monitorování operací** a poznamenejte si název, který je kompatibilní s centrem **událostí** a hodnoty **koncového bodu kompatibilního centra událostí** :

    ![Hodnoty koncového bodu kompatibilního s centrem událostí](./media/iot-hub-operations-monitoring/monitoring-endpoint.png)

3. Zvolte **zásady sdíleného přístupu** a pak zvolte **Služba**. Poznamenejte si hodnotu **primárního klíče** :

    ![Primární klíč zásad sdíleného přístupu služby](./media/iot-hub-operations-monitoring/service-key.png)

Následující ukázka kódu C# je pořízena z konzolové aplikace Visual Studio **Classic klasické pracovní plochy** c#. Projekt má nainstalovaný balíček NuGet **windowsazure. ServiceBus** .

* Nahraďte zástupný symbol připojovacího řetězce připojovacím řetězcem, který používá hodnoty **primárního klíče** **kompatibilního s centrem událostí** a služby, které jste si poznamenali dříve, jak je znázorněno v následujícím příkladu:

    ```csharp
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Zástupný symbol názvu koncového bodu monitorování nahraďte hodnotou **název kompatibilní** s centrem událostí, kterou jste si poznamenali dříve.

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

Další informace o monitorování IoT Hub pomocí Azure Monitor najdete v těchto tématech:

* [Monitorování IoT Hubu](monitor-iot-hub.md)

* [Migrace z monitorování IoT Hubch operací do Azure Monitor](iot-hub-migrate-to-diagnostics-settings.md)
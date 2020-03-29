---
title: Příjem událostí z Azure Event Grid do koncového bodu HTTP
description: Popisuje, jak ověřit koncový bod HTTP, pak přijímat a rekonstruovat události z Azure Event Grid
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: babanisa
ms.openlocfilehash: cb38fd17c0c1bfbe3e5957d8f432f0a43b285c93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60803822"
---
# <a name="receive-events-to-an-http-endpoint"></a>Příjem událostí pro koncový bod HTTP

Tento článek popisuje, jak [ověřit koncový bod HTTP](security-authentication.md#webhook-event-delivery) přijímat události z odběru událostí a potom přijímat a rekonstruovat události. Tento článek používá funkci Azure pro demonstrační účely, ale stejné koncepty platí bez ohledu na to, kde je aplikace hostované.

> [!NOTE]
> **Důrazně** doporučujeme použít aktivační [událost mřížky událostí](../azure-functions/functions-bindings-event-grid.md) při aktivaci funkce Azure s Event Grid. Použití obecné hospo- spoušť WebHook zde je demonstrativní.

## <a name="prerequisites"></a>Požadavky

Potřebujete aplikaci funkce s funkcí spouštěnou protokolem HTTP.

## <a name="add-dependencies"></a>Přidání závislostí

Pokud vyvíjíte v rozhraní .NET, [přidejte závislost](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) `Microsoft.Azure.EventGrid` do funkce pro [balíček Nuget](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). Příklady v tomto článku vyžadují verzi 1.4.0 nebo novější.

Sady SDK pro jiné jazyky jsou k dispozici prostřednictvím odkazu Publikovat sady [SDK.](./sdk-overview.md#data-plane-sdks) Tyto balíčky mají modely pro `EventGridEvent`nativní typy událostí, jako jsou , `StorageBlobCreatedEventData`a `EventHubCaptureFileCreatedEventData`.

Klikněte na odkaz Zobrazit soubory ve funkci Azure (většina podokna vpravo na portálu funkcí Azure) a vytvořte soubor s názvem project.json. Přidejte do souboru `project.json` následující obsah a uložte jej:

 ```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.Azure.EventGrid": "2.0.0"
      }
    }
   }
}
```

![Přidán balíček NuGet](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Ověření koncového bodu

První věc, kterou chcete `Microsoft.EventGrid.SubscriptionValidationEvent` udělat, je zpracování událostí. Pokaždé, když se někdo přihlásí k odběru události, Event Grid `validationCode` odešle ověřovací událost do koncového bodu s datovou datovou část. Koncový bod je nutné echo to zpět v těle odpovědi [prokázat koncový bod je platný a vlastněné vámi](security-authentication.md#webhook-event-delivery). Pokud používáte [aktivační událost mřížky událostí spíše](../azure-functions/functions-bindings-event-grid.md) než webhooku aktivované funkce, ověření koncového bodu je zpracována za vás. Pokud používáte službu rozhraní API jiného výrobce (například [Zapier](https://zapier.com) nebo [IFTTT](https://ifttt.com/)), nemusí být možné programově echo ověřovací kód. Pro tyto služby můžete ručně ověřit předplatné pomocí ověřovací adresy URL, která je odeslána v události ověření předplatného. Zkopírujte tuto `validationUrl` adresu URL ve vlastnosti a odešlete požadavek GET prostřednictvím klienta REST nebo webového prohlížeče.

V c# `DeserializeEventGridEvents()` funkce deserializuje události Event Grid. Deserializuje data události do příslušného typu, například StorageBlobCreatedEventData. Pomocí `Microsoft.Azure.EventGrid.EventTypes` třídy získáte podporované typy a názvy událostí.

Chcete-li programově opakovat ověřovací kód, použijte následující kód. Související ukázky najdete na [příkladu příjemce mřížky událostí](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridConsumer).

```csharp
using System.Net;
using Newtonsoft.Json;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;

    string requestContent = await req.Content.ReadAsStringAsync();
    log.Info($"Received events: {requestContent}");

    EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

    EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        if (eventGridEvent.Data is SubscriptionValidationEventData)
        {
            var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response

            var responseData = new SubscriptionValidationResponse()
            {
                ValidationResponse = eventData.ValidationCode
            };

            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};
```

### <a name="test-validation-response"></a>Odpověď na ověření testu

Otestujte funkci odpovědi na ověření vložením ukázkové události do testovacího pole pro funkci:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Po klepnutí na tlačítko Spustit by výstup `{"ValidationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` měl být 200 OK a v těle:

![odpověď ověření](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>Zpracování událostí úložiště objektů blob

Nyní rozšiřte funkci pro zpracování `Microsoft.Storage.BlobCreated`:

```cs
using System.Net;
using Newtonsoft.Json;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;

    string requestContent = await req.Content.ReadAsStringAsync();
    log.Info($"Received events: {requestContent}");

    EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

    EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        if (eventGridEvent.Data is SubscriptionValidationEventData)
        {
            var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response

            var responseData = new SubscriptionValidationResponse()
            {
                ValidationResponse = eventData.ValidationCode
            };

            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
        else if (eventGridEvent.Data is StorageBlobCreatedEventData)
        {
            var eventData = (StorageBlobCreatedEventData)eventGridEvent.Data;
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>Testování zpracování událostí vytvořené objektem blob

Otestujte nové funkce funkce vložením [události úložiště objektů blob](./event-schema-blob-storage.md#example-event) do testovacího pole a spuštěním:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Výstup adresy URL objektu blob byste měli vidět v protokolu funkcí:

![Výstupní protokol](./media/receive-events/blob-event-response.png)

Můžete také otestovat vytvořením účtu úložiště objektů Blob nebo účtu úložiště Pro obecné účely V2 (GPv2), [přidáním a odběrem událostí a](../storage/blobs/storage-blob-event-quickstart.md)nastavením koncového bodu na adresu URL funkce:

![Adresa URL funkce](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Zpracování vlastních událostí

Nakonec umožňuje rozšířit funkci ještě jednou tak, aby může také zpracovávat vlastní události. 

V aplikaci C# sada SDK podporuje mapování názvu typu události na datový typ události. Pomocí `AddOrUpdateCustomEventMapping()` funkce namapujte vlastní událost.

Přidejte šek na `Contoso.Items.ItemReceived`událost . Konečný kód by měl vypadat takto:

```cs
using System.Net;
using Newtonsoft.Json;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;

class ContosoItemReceivedEventData
{
    [JsonProperty(PropertyName = "itemSku")]
    public string ItemSku { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;

    string requestContent = await req.Content.ReadAsStringAsync();
    log.Info($"Received events: {requestContent}");

    EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();
    eventGridSubscriber.AddOrUpdateCustomEventMapping("Contoso.Items.ItemReceived", typeof(ContosoItemReceivedEventData));
    EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        if (eventGridEvent.Data is SubscriptionValidationEventData)
        {
            var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response

            var responseData = new SubscriptionValidationResponse()
            {
                ValidationResponse = eventData.ValidationCode
            };

            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
        else if (eventGridEvent.Data is StorageBlobCreatedEventData)
        {
            var eventData = (StorageBlobCreatedEventData)eventGridEvent.Data;
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
        else if (eventGridEvent.Data is ContosoItemReceivedEventData)
        {
            var eventData = (ContosoItemReceivedEventData)eventGridEvent.Data;
            log.Info($"Got ContosoItemReceived event data, item SKU {eventData.ItemSku}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = body.data;
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>Testování zpracování vlastních událostí

Nakonec otestujte, že vaše funkce nyní může zpracovat váš vlastní typ události:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Contoso.Items.ItemReceived",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "itemSku": "Standard"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

Tuto funkci můžete také testovat živě [odesláním vlastní události s CURL z portálu](./custom-event-quickstart-portal.md) nebo [odesláním do vlastního tématu](./post-to-custom-topic.md) pomocí jakékoli služby nebo aplikace, která může postovat do koncového bodu, jako je [Postman](https://www.getpostman.com/). Vytvořte vlastní téma a odběr událostí s koncovým bodem nastaveným jako adresa URL funkce.

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [sady SDK pro správu a publikování gridové mřížky událostí Azure](./sdk-overview.md)
* Přečtěte si, jak [psát k vlastnímu tématu](./post-to-custom-topic.md)
* Vyzkoušejte jeden z podrobných kurzů Event Grid a functions, jako je [změna velikosti obrázků nahraných do úložiště objektů Blob](resize-images-on-storage-blob-upload-event.md)

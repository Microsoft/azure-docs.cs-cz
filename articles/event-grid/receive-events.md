---
title: Příjem událostí z Azure Event Grid do koncového bodu HTTP
description: Popisuje, jak ověřit koncový bod HTTP a pak přijímat a deserializovat události z Azure Event Grid
ms.topic: conceptual
ms.date: 11/19/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 75c80fb85d39298f1130537971bc700897c039d0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "96023705"
---
# <a name="receive-events-to-an-http-endpoint"></a>Příjem událostí pro koncový bod HTTP

Tento článek popisuje, jak [ověřit koncový bod HTTP](webhook-event-delivery.md) pro příjem událostí z odběru události a pak přijímat a deserializovat události. Tento článek používá funkci Azure pro demonstrační účely, ale stejné koncepty platí bez ohledu na to, kde je aplikace hostovaná.

> [!NOTE]
> **Důrazně** doporučujeme použít [aktivační událost Event Grid](../azure-functions/functions-bindings-event-grid.md) při aktivaci funkce Azure pomocí Event Grid. Použití obecného triggeru Webhooku je tady demonstrované.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat aplikaci Function App s funkcí aktivovanou protokolem HTTP.

## <a name="add-dependencies"></a>Přidat závislosti

Pokud vyvíjíte v rozhraní .NET, [přidejte závislost](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) na funkci pro `Microsoft.Azure.EventGrid` [balíček NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). Příklady v tomto článku vyžadují verzi 1.4.0 nebo novější.

Sady SDK pro jiné jazyky jsou k dispozici prostřednictvím odkazu [publikovat sady SDK](./sdk-overview.md#data-plane-sdks) . Tyto balíčky mají modely pro nativní typy událostí, jako jsou `EventGridEvent` , `StorageBlobCreatedEventData` a `EventHubCaptureFileCreatedEventData` .

Klikněte na odkaz Zobrazit soubory v Azure Function (napravo od pravého podokna na portálu Azure Functions) a vytvořte soubor s názvem project.js. Do souboru přidejte následující obsah `project.json` a uložte ho:

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

![Přidaný balíček NuGet](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Ověření koncového bodu

První věc, kterou chcete udělat, je zpracování `Microsoft.EventGrid.SubscriptionValidationEvent` událostí. Pokaždé, když se někdo přihlašuje k události, Event Grid pošle událost ověření koncovému bodu s datovou `validationCode` částí. Koncový bod je nutný k tomu, aby vrátil zpátky v těle odpovědi, aby [prokázal, že koncový bod je platný a vlastní](webhook-event-delivery.md). Pokud používáte [trigger Event Grid](../azure-functions/functions-bindings-event-grid.md) místo funkce aktivované webhookem, bude se vám za vás zajišťovat ověření koncového bodu. Pokud používáte službu rozhraní API třetí strany (například [Zapier](https://zapier.com/home) nebo [IFTTT](https://ifttt.com/)), možná nebudete moci programově zobrazovat kód pro ověření. Pro tyto služby můžete odběr ověřit ručně pomocí adresy URL pro ověření, která je odeslána v události ověření předplatného. Zkopírujte tuto adresu URL do `validationUrl` vlastnosti a odešlete požadavek GET buď pomocí klienta REST nebo webového prohlížeče.

V jazyce C# `DeserializeEventGridEvents()` funkce deserializace události Event Grid. Deserializace data události do příslušného typu, například StorageBlobCreatedEventData. Použijte `Microsoft.Azure.EventGrid.EventTypes` třídu pro získání podporovaných typů a názvů událostí.

Chcete-li kód pro ověření programově vypsat, použijte následující kód. Související ukázky najdete v [příkladu Event Grid příjemce](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridConsumer).

```csharp
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;
namespace Function1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            string response = string.Empty;
            string requestContent = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation($"Received events: {requestContent}");

            EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

            EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                if (eventGridEvent.Data is SubscriptionValidationEventData)
                {
                    var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
                    log.LogInformation($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                    // Do any additional validation (as required) and then return back the below response

                    var responseData = new SubscriptionValidationResponse()
                    {
                        ValidationResponse = eventData.ValidationCode
                    };
                    return new OkObjectResult(responseData);
                }
            }
            return new OkObjectResult(response);
        }
   }
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

Otestujte funkci reakce na ověření vložením ukázkové události do pole test pro funkci:

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

Po kliknutí na tlačítko spustit by měl být výstup 200 OK a `{"validationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` v těle:

:::image type="content" source="./media/receive-events/validation-request.png" alt-text="Žádost o ověření":::

:::image type="content" source="./media/receive-events/validation-output.png" alt-text="Výstup ověřování":::

## <a name="handle-blob-storage-events"></a>Zpracování událostí služby Blob Storage

Teď rozšíříme funkci na zpracování `Microsoft.Storage.BlobCreated` :

```cs
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.EventGrid;
using Microsoft.Azure.EventGrid.Models;

namespace FunctionApp1
{
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation($"C# HTTP trigger function begun");
            string response = string.Empty;

            string requestContent = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation($"Received events: {requestContent}");

            EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

            EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                if (eventGridEvent.Data is SubscriptionValidationEventData)
                {
                    var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
                    log.LogInformation($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                    // Do any additional validation (as required) and then return back the below response

                    var responseData = new SubscriptionValidationResponse()
                    {
                        ValidationResponse = eventData.ValidationCode
                    };

                    return new OkObjectResult(responseData);
                }
            }

            return new OkObjectResult(response);
        }
    }
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

### <a name="test-blob-created-event-handling"></a>Zpracování událostí vytvořeného objektu BLOB testu

Otestujte novou funkčnost funkce tak, že do pole test vložíte [událost BLOB Storage](./event-schema-blob-storage.md#example-event) a spustíte:

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

V protokolu funkce by se měl zobrazit výstup adresy URL objektu BLOB:

![Výstupní protokol](./media/receive-events/blob-event-response.png)

Můžete také otestovat vytvořením účtu úložiště BLOB nebo účtu úložiště Pro obecné účely v2 (GPv2), [přidáním a odběru událostí](../storage/blobs/storage-blob-event-quickstart.md)a nastavením koncového bodu na adresu URL funkce:

![Adresa URL funkce](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Zpracování vlastních událostí

Nakonec umožňuje funkci širšího rozšiřování funkce, aby mohla také zpracovávat vlastní události. 

V jazyce C# sada SDK podporuje mapování názvu typu události na datový typ události. Pomocí `AddOrUpdateCustomEventMapping()` funkce namapujte vlastní událost.

Přidejte kontrolu události `Contoso.Items.ItemReceived` . Výsledný kód by měl vypadat takto:

```cs
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;
using Newtonsoft.Json;

namespace FunctionApp1
{
    class ContosoItemReceivedEventData
    {
        [JsonProperty("itemSku")]
        public string ItemSku { get; set; }
    }
    public static class Function1
    {
        [FunctionName("Function1")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation($"C# HTTP trigger function begun");
            string response = string.Empty;

            string requestContent = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogInformation($"Received events: {requestContent}");

            EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();
            eventGridSubscriber.AddOrUpdateCustomEventMapping("Contoso.Items.ItemReceived", typeof(ContosoItemReceivedEventData));
            EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

            foreach (EventGridEvent eventGridEvent in eventGridEvents)
            {
                if (eventGridEvent.Data is SubscriptionValidationEventData)
                {
                    var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
                    log.LogInformation($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
                    // Do any additional validation (as required) and then return back the below response

                    var responseData = new SubscriptionValidationResponse()
                    {
                        ValidationResponse = eventData.ValidationCode
                    };

                    return new OkObjectResult(responseData);
                }
                else if (eventGridEvent.Data is StorageBlobCreatedEventData)
                {
                    var eventData = (StorageBlobCreatedEventData)eventGridEvent.Data;
                    log.LogInformation($"Got BlobCreated event data, blob URI {eventData.Url}");
                }
                else if (eventGridEvent.Data is ContosoItemReceivedEventData)
                {
                    var eventData = (ContosoItemReceivedEventData)eventGridEvent.Data;
                    log.LogInformation($"Got ContosoItemReceived event data, item SKU {eventData.ItemSku}");
                }
            }

            return new OkObjectResult(response);
        }
    }
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

### <a name="test-custom-event-handling"></a>Testování vlastních zpracování událostí

Nakonec otestujte, že vaše funkce teď může zpracovat vlastní typ události:

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

Tuto funkci můžete také v reálném čase otestovat [odesláním vlastní události pomocí objektu kudrlinkou z portálu](./custom-event-quickstart-portal.md) nebo [publikováním do vlastního tématu](./post-to-custom-topic.md)  pomocí libovolné služby nebo aplikace, která může odesílat do koncového bodu, jako je například [post](https://www.getpostman.com/). Vytvořte vlastní téma a odběr událostí s nastaveným koncovým bodem jako adresu URL funkce.

[!INCLUDE [event-grid-message-headers](../../includes/event-grid-message-headers.md)]

## <a name="next-steps"></a>Další kroky

* Prozkoumejte sady [SDK pro správu Azure Event Grid a publikování](./sdk-overview.md)
* Přečtěte si, jak [publikovat do vlastního tématu](./post-to-custom-topic.md) .
* Vyzkoušejte jeden z podrobných kurzů Event Grid a funkcí, jako je [Změna velikosti imagí nahraných do úložiště objektů BLOB](resize-images-on-storage-blob-upload-event.md) .

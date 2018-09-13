---
title: Příjem událostí ze služby Azure Event Grid pro koncový bod HTTP
description: Popisuje, jak ověřit koncový bod HTTP, pak zobrazí a deserializovat události ze služby Azure Event Grid
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: babanisa
ms.openlocfilehash: 1ff0cf9d2733d7ebb6e739ce44da6442ffc26c1c
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643565"
---
# <a name="receive-events-to-an-http-endpoint"></a>Příjem událostí pro koncový bod HTTP

Tento článek popisuje, jak [ověřit koncový bod HTTP](security-authentication.md#webhook-event-delivery) přijímat události z odběru událostí a příjem deserializovat události. Tento článek používá funkci Azure pro demonstrační účely, ale stejné koncepty platí bez ohledu na to, kde je aplikace hostovaná.

> [!NOTE]
> Je **důrazně** doporučeno používat [Trigger služby Event Grid](../azure-functions/functions-bindings-event-grid.md) při aktivaci funkce Azure s využitím služby Event Grid. Použití obecného Webhooku trigger zde je Demonstrativní.

## <a name="prerequisites"></a>Požadavky

* Budete potřebovat aplikaci function app s [protokolu HTTP aktivované – funkce](../azure-functions/functions-create-generic-webhook-triggered-function.md)

## <a name="add-dependencies"></a>Přidat závislosti

Pokud vyvíjíte v rozhraní .NET, [Přidat závislost](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) na vaši funkci `Microsoft.Azure.EventGrid` [balíček Nuget](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). Jsou k dispozici prostřednictvím sady SDK pro ostatní jazyky [publikovat sady SDK](./sdk-overview.md#data-plane-sdks) odkaz. Tyto balíčky obsahují modely pro typy nativních událostí, například `EventGridEvent`, `StorageBlobCreatedEventData`, a `EventHubCaptureFileCreatedEventData`.

Klikněte na odkaz "Zobrazit soubory" ve své funkci Azure (pravém většiny podokně na portálu Azure functions) a vytvořte soubor s názvem souboru project.json. Přidejte následující obsah `project.json` soubor a uložte ho:

 ```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.Azure.EventGrid": "1.3.0"
      }
    }
   }
}
```

![Přidání balíčku NuGet](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Koncový bod ověření

První věc, kterou chcete udělat, je zpracování `Microsoft.EventGrid.SubscriptionValidationEvent` události. Pokaždé, když se uživatel přihlásí k události, služby Event Grid odešle událost ověření koncového bodu se `validationCode` v datové části data. Koncový bod je potřeba echo tomto zpět v textu odpovědi na [koncový bod je platný a je vlastnictví prokázat](security-authentication.md#webhook-event-delivery). Pokud používáte [Trigger služby Event Grid](../azure-functions/functions-bindings-event-grid.md) spíše než funkce aktivované Webhooku, koncový bod ověřování se udělá za vás. Pokud používáte rozhraní API služby třetí strany (například [Zapier](https://zapier.com) nebo [IFTTT](https://ifttt.com/)), nemusí být schopen prostřednictvím kódu programu echo ověřovacího kódu. Za tyto služby můžete ručně ověřit předplatné pomocí adresy URL ověřování, který se posílá událost ověření předplatného. Zkopírujte tuto adresu URL v `validationUrl` vlastnost a odeslat GET vyžádat buď pomocí klienta REST nebo ve webovém prohlížeči.

Ruční ověření je ve verzi preview. Pokud ji chcete používat, je nutné nainstalovat [rozšíření Event Grid ](/cli/azure/azure-cli-extensions-list) pro [Azure CLI](/cli/azure/install-azure-cli). Můžete si je nainstalovat pomocí příkazu `az extension add --name eventgrid`. Pokud používáte rozhraní REST API, zkontrolujte, že používáte `api-version=2018-05-01-preview`.

Pokud chcete prostřednictvím kódu programu echo ověřovací kód, použijte následující kód (můžete také vyhledat související ukázky v https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridConsumer):

```csharp
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{

    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponse();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript
var http = require('http');

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

### <a name="test-validation-response"></a>Test ověření odpovědí

Testování funkce odpověď ověření zadáním nebo vložením událost vzorku do pole testu pro funkci:

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

Po kliknutí na spustit, výstup by měl být 200 OK a `{"ValidationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` v textu:

![Odpověď ověřování](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>Zpracování událostí služby Blob storage

Teď můžeme rozšířit funkci pro zpracování `Microsoft.Storage.BlobCreated`:

```cs
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type 
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");

            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponse();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>();
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript
var http = require('http');

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

### <a name="test-blob-created-event-handling"></a>Otestovat zpracování události vytvoření objektu Blob

Vyzkoušejte nové funkce funkci tak, že vložíte [události úložiště objektů Blob](./event-schema-blob-storage.md#example-event) do pole testu a spuštění:

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

Byste měli vidět výstup adresy URL objektu blob v protokolu funkce:

![Výstup protokolu](./media/receive-events/blob-event-response.png)

Můžete také otestovat tak, že vytvoříte účet Blob storage nebo pro obecné účely V2 (GPv2) účtu, [přidávání a odběru událostí](../storage/blobs/storage-blob-event-quickstart.md)a nastavení koncového bodu na adresu URL funkce:

![Adresa URL funkce](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Zpracování vlastních událostí

A konečně umožňuje ještě jednou rozšířit funkci tak, aby také dokáže zpracovat vlastní události. Přidat kontrolu pro událost `Contoso.Items.ItemReceived`. Konečný kód by měl vypadat:

```cs
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json.Serialization;
using Microsoft.Azure.EventGrid.Models;

class ContosoItemReceivedEventData
{
    public string id { get; set; }
    public string message { get; set; }
    public string time { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;
    const string SubscriptionValidationEvent = "Microsoft.EventGrid.SubscriptionValidationEvent";
    const string StorageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    const string CustomTopicEvent = "Contoso.Items.ItemReceived";

    string requestContent = await req.Content.ReadAsStringAsync();
    EventGridEvent[] eventGridEvents = JsonConvert.DeserializeObject<EventGridEvent[]>(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        JObject dataObject = eventGridEvent.Data as JObject;

        // Deserialize the event data into the appropriate type based on event type
        if (string.Equals(eventGridEvent.EventType, SubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<SubscriptionValidationEventData>();
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response
            var responseData = new SubscriptionValidationResponse();
            responseData.ValidationResponse = eventData.ValidationCode;
            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }

        else if (string.Equals(eventGridEvent.EventType, StorageBlobCreatedEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<StorageBlobCreatedEventData>();
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }

        else if (string.Equals(eventGridEvent.EventType, CustomTopicEvent, StringComparison.OrdinalIgnoreCase))
        {
            var eventData = dataObject.ToObject<ContosoItemReceivedEventData>();
            log.Info($"Got ContosoItemReceived event data, item URI {eventData.id}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}

```

```javascript
var http = require('http');
var t = require('tcomb');

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";
    var ContosoItemReceivedEventData = t.struct({
        id: t.Str,
        message: t.Str,
        time: t.Str,
    })

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
            var payload = new ContosoItemReceivedEventData(body.data);
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
            context.log(payload instanceof ContosoItemReceivedEventData);
        }
    }
    context.done();
};

```

### <a name="test-custom-event-handling"></a>Vlastní události zpracování testu

Nakonec testu, rozšířeného funkce teď zvládne vaše vlastní typ události:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Microsoft.EventGrid.CustomEventType",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "id": "831e1650-001e-001b-66ab-eeb76e069631",
            "message": "Contoso item Foo",
            "time": "2017-06-26T18:41:00.9584103Z"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

Můžete také otestovat za provozu pomocí této funkce [odesílání vlastních událostí pomocí CURL z portálu](./custom-event-quickstart-portal.md) nebo [účtování do vlastního tématu](./post-to-custom-topic.md) pomocí libovolné služby nebo aplikace, která můžete vytvořit koncový bod jako je například [Postman](https://www.getpostman.com/). Vytvořte vlastní téma a odběr událostí s koncovým bodem nastavit jako adresu URL funkce.

## <a name="next-steps"></a>Další postup

* Prozkoumejte [Azure Event Grid Management a publikování sad SDK](./sdk-overview.md)
* Zjistěte, jak [odeslat do vlastního tématu](./post-to-custom-topic.md)
* Zkuste použít jeden z podrobné kurzy služby Event Grid a funkce, jako [změny velikosti obrázků nahrané do úložiště objektů Blob](resize-images-on-storage-blob-upload-event.md)

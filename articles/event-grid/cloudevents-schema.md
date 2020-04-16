---
title: Použití Azure Event Grid s událostmi ve schématu CloudEvents
description: Popisuje, jak používat schéma CloudEvents pro události v Azure Event Grid. Služba podporuje události v implementaci JSON cloudových událostí.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 404052984cb99e37f7404a47f3ac374088d32d6c
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393481"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Použití schématu CloudEvents v1.0 s mřížkou událostí
Kromě [výchozího schématu událostí](event-schema.md)azure event grid nativně podporuje události v [implementaci JSON cloudevents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) a [http protokolu vazby](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) je [otevřená specifikace](https://github.com/cloudevents/spec/blob/v1.0/spec.md) pro popis dat událostí.

CloudEvents zjednodušuje interoperabilitu tím, že poskytuje společné schéma událostí pro publikování a využívání cloudových událostí. Toto schéma umožňuje jednotné nástroje, standardní způsoby směrování & zpracování událostí a univerzální způsoby deserializace schématu vnější události. Díky běžnému schématu můžete snadněji integrovat práci napříč platformami.

CloudEvents je budován několika [spolupracovníky](https://github.com/cloudevents/spec/blob/master/community/contributors.md), včetně Microsoftu, prostřednictvím [Cloud Native Computing Foundation](https://www.cncf.io/). V současné době je k dispozici jako verze 1.0.

Tento článek popisuje, jak používat schéma CloudEvents s Event Grid.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalace funkce náhledu

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>Schéma CloudEvent

Tady je příklad události azure blob storage ve formátu CloudEvents:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

Podrobný popis dostupných polí, jejich typů a definic v CloudEvents v1.0 je [k dispozici zde](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Hodnoty záhlaví pro události dodané ve schématu CloudEvents a schéma mřížky událostí `content-type`jsou stejné s výjimkou . Pro schéma CloudEvents je `"content-type":"application/cloudevents+json; charset=utf-8"`tato hodnota záhlaví . Pro schéma mřížky událostí je `"content-type":"application/json; charset=utf-8"`tato hodnota záhlaví .

## <a name="configure-event-grid-for-cloudevents"></a>Konfigurace mřížky událostí pro cloududálosti

Event Grid můžete použít pro vstup i výstup událostí ve schématu CloudEvents. CloudEvents můžete použít pro systémové události, jako jsou události úložiště objektů blob a události služby IoT Hub a vlastní události. Může také transformovat tyto události na drátu tam a zpět.


| Vstupní schéma       | Výstupní schéma
|--------------------|---------------------
| Formát CloudEvents | Formát CloudEvents
| Formát mřížky událostí  | Formát CloudEvents
| Formát mřížky událostí  | Formát mřížky událostí

Pro všechna schémata událostí událost grid vyžaduje ověření při publikování na téma mřížky událostí a při vytváření odběr událostí. Další informace naleznete v [tématu Zabezpečení a ověřování mřížky událostí](security-authentication.md).

### <a name="input-schema"></a>Vstupní schéma

Vstupní schéma pro vlastní téma nastavíte při vytváření vlastního tématu.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>Výstupní schéma

Schéma výstupu nastavíte při vytváření odběr událostí.

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

Pokud používáte PowerShell, použijte:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 V současné době nelze použít aktivační událost Grid pro aplikaci Azure Functions při události se doručuje ve schématu CloudEvents. Použijte aktivační událost HTTP. Příklady implementace aktivační události HTTP, která přijímá události ve schématu CloudEvents, najdete [v tématu Použití CloudEvents s funkcemi Azure](#azure-functions).

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>Ověření koncového bodu s CloudEvents v1.0

Pokud jste již obeznámeni s Event Grid, může být vědomi ověření ověření argumentu pro event grid pro prevenci zneužití. CloudEvents v1.0 implementuje vlastní [sémantiku ochrany proti zneužívání](security-authentication.md#webhook-event-delivery) pomocí metody HTTP OPTIONS. Další informace si můžete přečíst [tady](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Při použití schématu CloudEvents pro výstup, Event Grid používá s CloudEvents v1.0 ochrany zneužití namísto mechanismu události ověřování Event Grid.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Použití s funkcemi Azure

Vazby [Azure Functions Event Grid](../azure-functions/functions-bindings-event-grid.md) nativně nepodporuje CloudEvents, takže funkce spouštěné HTTP se používají ke čtení zpráv CloudEvents. Při použití aktivační události HTTP ke čtení CloudEvents, budete muset napsat kód pro co událost mřížky událostí automaticky:

* Odešle odpověď ověření na [žádost o ověření předplatného](../event-grid/security-authentication.md#webhook-event-delivery).
* Vyvolá funkci jednou za prvek pole událostí obsažené v těle požadavku.

Informace o adrese URL, která se má použít pro vyvolání funkce místně nebo při spuštění v Azure, najdete v [tématu referenční dokumentace pro vazbu aktivační události HTTP](../azure-functions/functions-bindings-http-webhook.md)

Následující ukázkový kód Jazyka C# pro aktivační událost PROTOKOLU HTTP simuluje chování aktivační události mřížky událostí.  Tento příklad použijte pro události dodané ve schématu CloudEvents.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == "OPTIONS")
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request is not for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Následující ukázkový kód JavaScriptu pro aktivační událost HTTP simuluje chování aktivační události mřížky událostí. Tento příklad použijte pro události dodané ve schématu CloudEvents.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS) {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = { status: 200, body: { "ValidationResponse": code } };
        context.res.headers.append('Webhook-Allowed-Origin', 'eventgrid.azure.net');
    }
    else
    {
        var message = req.body;
        
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
 
    context.done();
};
```

## <a name="next-steps"></a>Další kroky

* Informace o dodávkách událostí monitorování naleznete v [tématu Sledování doručování zpráv v programu Event Grid](monitor-event-delivery.md).
* Doporučujeme vám testovat, komentovat a [přispívat do](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) CloudEvents.
* Další informace o vytvoření předplatného Služby Azure Event Grid najdete v [tématu schéma předplatného služby Event Grid](subscription-creation-schema.md).

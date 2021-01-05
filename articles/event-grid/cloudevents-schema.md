---
title: Použití Azure Event Grid s událostmi ve schématu CloudEvents
description: Popisuje, jak používat CloudEvents schéma pro události v Azure Event Grid. Služba podporuje události v implementaci JSON pro CloudEvents.
ms.topic: conceptual
ms.date: 11/10/2020
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 93e514e0eac40cfaa51d410a446608deca3cbd6d
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901398"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Použití schématu CloudEvents v 1.0 s Event Grid
Kromě [výchozího schématu událostí](event-schema.md)Azure Event Grid nativně podporuje události v [implementaci JSON pro vazby protokolu CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) a [http](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) je [otevřená specifikace](https://github.com/cloudevents/spec/blob/v1.0/spec.md) popisující data události.

CloudEvents zjednodušuje interoperabilitu tím, že poskytuje společné schéma událostí pro publikování a využívání cloudových událostí. Toto schéma umožňuje jednotným nástrojům, standardním způsobům směrování a zpracování událostí a univerzální způsoby deserializace vnějšího schématu události. Pomocí společného schématu můžete snadněji integrovat práci na různých platformách.

CloudEvents se sestavuje několika [spolupracovníky](https://github.com/cloudevents/spec/blob/master/community/contributors.md), včetně Microsoftu, prostřednictvím [základu cloud computingu Native](https://www.cncf.io/). Je aktuálně k dispozici jako verze 1,0.

Tento článek popisuje, jak používat schéma CloudEvents s Event Grid.

## <a name="cloudevent-schema"></a>CloudEvent schéma

Tady je příklad události Azure Blob Storage ve formátu CloudEvents:

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

Podrobný popis dostupných polí, jejich typy a definice najdete v tématu [CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Hodnoty hlaviček pro události doručené ve schématu CloudEvents a schématu Event Grid jsou stejné s výjimkou `content-type` . Pro schéma CloudEvents je tato hodnota hlavičky `"content-type":"application/cloudevents+json; charset=utf-8"` . Pro Event Grid schéma je hodnota hlavičky `"content-type":"application/json; charset=utf-8"` .

## <a name="configure-event-grid-for-cloudevents"></a>Konfigurace Event Grid pro CloudEvents

Event Grid můžete použít pro vstupní i výstupní události ve schématu CloudEvents. Následující tabulka popisuje možné transformace:

 Prostředek Event Grid | Vstupní schéma       | Schéma doručení
|---------------------|-------------------|---------------------
| Systémová témata       | Schéma služby Event Grid | Event Grid schématu nebo schématu CloudEvent
| Témata a domény uživatele | Schéma služby Event Grid | Schéma služby Event Grid
| Témata a domény uživatele | CloudEvent schéma | CloudEvent schéma
| Témata a domény uživatele | Vlastní schéma     | Vlastní schéma, Event Grid schéma nebo schéma CloudEvent
| PartnerTopics       | CloudEvent schéma | CloudEvent schéma

Pro všechna schémata událostí Event Grid vyžaduje ověření při publikování do Event Gridho tématu a při vytváření odběru událostí.

Další informace najdete v tématu [Event Grid zabezpečení a ověřování](security-authentication.md).

### <a name="input-schema"></a>Vstupní schéma

Vstupní schéma pro vlastní téma nastavíte při vytváření vlastního tématu.

Pro rozhraní příkazového řádku Azure použijte:

```azurecli-interactive
az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
New-AzEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

### <a name="output-schema"></a>Výstupní schéma

Výstupní schéma nastavíte při vytváření odběru události.

Pro rozhraní příkazového řádku Azure použijte:

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
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 V současné době nemůžete použít Trigger Event Grid pro aplikaci Azure Functions, když se událost doručí ve schématu CloudEvents. Použijte Trigger HTTP. Příklady implementace triggeru HTTP, který přijímá události ve schématu CloudEvents, najdete v tématu [použití CloudEvents s Azure Functions](#azure-functions).

## <a name="endpoint-validation-with-cloudevents-v10"></a>Ověření koncového bodu pomocí CloudEvents v 1.0

Pokud už jste obeznámeni s Event Grid, můžete znát metodu handshake ověřování koncového bodu, abyste zabránili zneužití. CloudEvents v 1.0 implementuje svou vlastní [sémantiku ochrany proti zneužívání](webhook-event-delivery.md) pomocí metody HTTP. Další informace najdete v tématu [Webhooky HTTP 1,1 pro doručování událostí – verze 1,0](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Když pro výstup použijete schéma CloudEvents, Event Grid používá místo mechanismu události ověřování Event Grid ochranu proti zneužívání CloudEvents v 1.0.

<a name="azure-functions"></a>

## <a name="use-with-azure-functions"></a>Použít s Azure Functions

[Vazba Azure Functions Event Grid](../azure-functions/functions-bindings-event-grid.md) nedokáže nativně podporovat CloudEvents, takže funkce aktivované protokolem HTTP se používají ke čtení zpráv CloudEvents. Při použití triggeru HTTP ke čtení CloudEvents je nutné napsat kód pro to, co aktivační událost Event Grid automaticky:

* Odešle odpověď ověření k žádosti o [ověření předplatného](../event-grid/webhook-event-delivery.md) .
* Vyvolá funkci jednou pro každý prvek pole události obsaženého v textu žádosti.

Informace o adrese URL, která se má použít k místnímu vyvolání funkce nebo při spuštění v Azure, najdete v tématu [Referenční dokumentace k aktivačním vazbám http](../azure-functions/functions-bindings-http-webhook.md).

Následující vzorový kód jazyka C# pro Trigger HTTP simuluje chování triggeru Event Grid. Tento příklad slouží pro události doručené ve schématu CloudEvents.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "post", "options", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    if (req.Method == HttpMethod.Options)
    {
        // If the request is for subscription validation, send back the validation code
        
        var response = req.CreateResponse(HttpStatusCode.OK);
        response.Headers.Add("Webhook-Allowed-Origin", "eventgrid.azure.net");

        return response;
    }

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    // The request isn't for subscription validation, so it's for an event.
    // CloudEvents schema delivers one event at a time.
    log.LogInformation($"Source: {message["source"]}");
    log.LogInformation($"Time: {message["eventTime"]}");
    log.LogInformation($"Event data: {message["data"].ToString()}");

    return req.CreateResponse(HttpStatusCode.OK);
}
```

Následující ukázkový kód JavaScriptu pro Trigger HTTP simuluje chování triggeru Event Grid. Tento příklad slouží pro události doručené ve schématu CloudEvents.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    
    if (req.method == "OPTIONS") {
        // If the request is for subscription validation, send back the validation code
        
        context.log('Validate request received');
        context.res = {
            status: 200,
            headers: {
                'Webhook-Allowed-Origin': 'eventgrid.azure.net',
            },
         };
    }
    else
    {
        var message = req.body;
        
        // The request isn't for subscription validation, so it's for an event.
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

* Informace o sledování doručení událostí najdete v tématu [monitorování Event Grid doručování zpráv](monitor-event-delivery.md).
* Doporučujeme vám testovat, komentovat a [přispívat k CloudEvents](https://github.com/cloudevents/spec/blob/master/community/CONTRIBUTING.md).
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).

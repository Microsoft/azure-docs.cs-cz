---
title: Nahrávání a stahování volání pomocí Event Grid – rychlý Start služby Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: V tomto rychlém startu se dozvíte, jak nahrávat a stahovat volání pomocí Event Grid.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/14/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 493a35a627f458fe649931d9fabc175b0affc3a6
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730559"
---
# <a name="record-and-download-calls-with-event-grid"></a>Záznam a stažení volání pomocí Event Grid

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Začněte s komunikačními službami Azure tím, že zaznamenáte volání komunikačních služeb pomocí Azure Event Grid.

## <a name="prerequisites"></a>Požadavky
- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Aktivní prostředek služby Communication Services. [Vytvořte prostředek služby Communications](../create-communication-resource.md?pivots=platform-azp&tabs=windows).
- [`Microsoft.Azure.EventGrid`](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)Balíček NuGet.

## <a name="create-a-webhook-and-subscribe-to-the-recording-events"></a>Vytvoření Webhooku a přihlášení k odběru událostí záznamu
*Webhooky* a *události* budeme používat k usnadnění nahrávání a stahování mediálních souborů. 

Nejprve vytvoříme Webhook. Prostředek komunikačních služeb bude používat Event Grid k oznámení tohoto Webhooku, když se `recording` událost aktivuje, a pak i po tom, co je nahrané médium připravené ke stažení.

Pro příjem těchto oznámení o událostech můžete napsat vlastní Webhook. U tohoto Webhooku je důležité reagovat na příchozí zprávy s ověřovacím kódem, aby se úspěšně přihlásilo k odběru Webhooku službě Event Service.

```
[HttpPost]
public async Task<ActionResult> PostAsync([FromBody] object request)
  {
   //Deserializing the request 
    var eventGridEvent = JsonConvert.DeserializeObject<EventGridEvent[]>(request.ToString())
        .FirstOrDefault();
    var data = eventGridEvent.Data as JObject;

    // Validate whether EventType is of "Microsoft.EventGrid.SubscriptionValidationEvent"
    if (string.Equals(eventGridEvent.EventType, EventTypes.EventGridSubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
   {
        var eventData = data.ToObject<SubscriptionValidationEventData>();
        var responseData = new SubscriptionValidationResponseData
        {
            ValidationResponse = eventData.ValidationCode
        };
        if (responseData.ValidationResponse != null)
        {
            return Ok(responseData);
        }
    }

    // Implement your logic here.
    ...
    ...
  }
```


Výše uvedený kód závisí na `Microsoft.Azure.EventGrid` balíčku NuGet. Další informace o Event Grid ověřování koncového bodu najdete v [dokumentaci k ověření koncového](https://docs.microsoft.com/azure/event-grid/receive-events#endpoint-validation) bodu.

Tento Webhook se pak přihlásí k `recording` události:

1. Vyberte okno `Events` z prostředku služby Azure Communication Services.
2. Vyberte `Event Subscription` , jak vidíte níže.
![Snímek obrazovky s uživatelským rozhraním Event gridu](./media/call-recording/image1-event-grid.png)
3. Nakonfigurujte odběr událostí a vyberte možnost `Call Recording File Status Update` jako `Event Type` . `Webhook`Jako. Vyberte `Endpoint type` .
![Vytvořit odběr události](./media/call-recording/image2-create-subscription.png)
4. Zadejte adresu URL Webhooku do `Subscriber Endpoint` .
![Přihlášení k odběru události](./media/call-recording/image3-subscribe-to-event.png)

Webhook se teď upozorní vždy, když se prostředek komunikačních služeb použije k zaznamenání volání.

## <a name="notification-schema"></a>Schéma oznámení
Po stažení nahrávky bude prostředek komunikačních služeb generovat oznámení s následujícím schématem událostí. ID dokumentů pro záznam lze načíst z `documentId` polí každého `recordingChunk` .

```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from AMS storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft”, etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}

```

## <a name="download-the-recorded-media-files"></a>Stažení nahraných mediálních souborů

Jakmile získáme ID dokumentu pro soubor, který chceme stáhnout, zavoláme následující rozhraní API služby Azure Communication Services, ve kterém si nahraje zaznamenaná média a metadata pomocí ověřování HMAC.

Maximální velikost souboru záznamu je 1,5 GB. Při překročení této velikosti souboru bude zapisovač automaticky rozdělit zaznamenaná média do více souborů.

Klient by měl být schopný stahovat všechny mediální soubory pomocí jediného požadavku. Pokud dojde k potížím, může se klient opakovat s hlavičkou rozsahu, aby se předešlo opětovnému stažení segmentů, které už byly staženy.

Stažení nahraných médií: 
- Metoda `GET` 
- Adresa URL https://contoso.communication.azure.com/recording/download/{documentId}?api-version=2021-04-15-preview1

Stažení zaznamenaných mediálních metadat: 
- Metoda `GET` 
- Adresa URL https://contoso.communication.azure.com/recording/download/{documentId}/metadata?api-version=2021-04-15-preview1


### <a name="authentication"></a>Authentication
Pokud chcete stáhnout záznamová média a metadata, použijte ověřování HMAC k ověření požadavku na rozhraní API komunikačních služeb Azure.

Vytvořte `HttpClient` a přidejte potřebné hlavičky pomocí `HmacAuthenticationUtils` níže uvedeného:

```
  var client = new HttpClient();

  // Set Http Method
  var method = HttpMethod.Get;
  StringContent content = null;

  // Build request
  var request = new HttpRequestMessage
  {
      Method = method, // Http GET method
      RequestUri = new Uri(<Download_Recording_Url>), // Download recording Url
      Content = content // content if required for POST methods
  };

  // Question: Why do we need to pass String.Empty to CreateContentHash() method?
  // Answer: In HMAC authentication, the hash of the content is one of the parameters used to generate the HMAC token.
  // In our case our recording download APIs are GET methods and do not have any content/body to be passed in the request. 
  // However in this case we still need the SHA256 hash for the empty content and hence we pass an empty string. 


  string serializedPayload = string.Empty;

  // Hash the content of the request.
  var contentHashed = HmacAuthenticationUtils.CreateContentHash(serializedPayload);

  // Add HAMC headers.
  HmacAuthenticationUtils.AddHmacHeaders(request, contentHashed, accessKey, method);

  // Make a request to the Azure Communication Services APIs mentioned above
  var response = await client.SendAsync(request).ConfigureAwait(false);
```

#### <a name="hmacauthenticationutils"></a>HmacAuthenticationUtils 
Pomocí níže uvedených nástrojů můžete spravovat svůj pracovní postup HMAC.

**Vytvořit hodnotu hash obsahu**

```
public static string CreateContentHash(string content)
{
    var alg = SHA256.Create();

    using (var memoryStream = new MemoryStream())
    using (var contentHashStream = new CryptoStream(memoryStream, alg, CryptoStreamMode.Write))
    {
        using (var swEncrypt = new StreamWriter(contentHashStream))
        {
            if (content != null)
            {
                swEncrypt.Write(content);
            }
        }
    }

    return Convert.ToBase64String(alg.Hash);
}
```

**Přidat hlavičky HMAC**

```
public static void AddHmacHeaders(HttpRequestMessage requestMessage, string contentHash, string accessKey)
{
    var utcNowString = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
    var uri = requestMessage.RequestUri;
    var host = uri.Authority;
    var pathAndQuery = uri.PathAndQuery;

    var stringToSign = $"{requestMessage.Method}\n{pathAndQuery}\n{utcNowString};{host};{contentHash}";
    var hmac = new HMACSHA256(Convert.FromBase64String(accessKey));
    var hash = hmac.ComputeHash(Encoding.ASCII.GetBytes(stringToSign));
    var signature = Convert.ToBase64String(hash);
    var authorization = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";

    requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
    requestMessage.Headers.Add("Date", utcNowString);
    requestMessage.Headers.Add("Authorization", authorization);
}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete vyčistit a odebrat předplatné služby Communications Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené. Přečtěte si další informace o [vyčištění prostředků](../create-communication-resource.md?pivots=platform-azp&tabs=windows#clean-up-resources).


## <a name="next-steps"></a>Další kroky
Další informace najdete v následujících článcích:

- Podívejte se na [ukázku našeho webového volání](https://docs.microsoft.com/azure/communication-services/samples/web-calling-sample)
- Další informace o [volání funkcí sady SDK](https://docs.microsoft.com/azure/communication-services/quickstarts/voice-video-calling/calling-client-samples?pivots=platform-web)
- Další informace o [volání funkce](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/about-call-types)

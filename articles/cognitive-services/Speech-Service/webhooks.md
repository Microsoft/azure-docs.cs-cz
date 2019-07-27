---
title: Webhooky – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Webhooky jsou testy volání HTTP, které jsou ideální pro optimalizaci řešení při práci s dlouho běžícími procesy, jako jsou import, přizpůsobení, testy přesnosti nebo přepisy dlouhotrvajících souborů.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 3d07e540bf88c956f61b5d3b2a98702cad616985
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558810"
---
# <a name="webhooks-for-speech-services"></a>Webhooky pro hlasové služby

Webhooky jsou jako zpětná volání HTTP, která aplikacím umožňují přijímat data ze služeb Speech, když budou k dispozici. Pomocí webhooků můžete optimalizovat používání našich rozhraní REST API tím, že Eliminujte nutnost nepřetržitého dotazování na odpověď. V následujících částech se dozvíte, jak používat Webhooky se službami Speech.

## <a name="supported-operations"></a>Podporované operace

Služba Speech Services podporuje Webhooky pro všechny dlouho běžící operace. Každá z níže uvedených operací může po dokončení aktivovat zpětné volání HTTP.

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

Nyní vytvoříme Webhook.

## <a name="create-a-webhook"></a>Vytvoření Webhooku

Pojďme vytvořit Webhook pro online přepis. Scénář: uživatel má dlouho běžící audio soubor, který by chtěl přepisovat asynchronně s rozhraním API služby Batch pro přepis.

Webhooky je možné vytvořit tak, že vytvoříte požadavek post na\<https://\>region. CRIS.AI/API/speechtotext/v2.1/Transcriptions/Hooks.

Parametry konfigurace pro požadavek se poskytují jako JSON:

```json
{
  "configuration": {
    "url": "https://your.callback.url/goes/here",
    "secret": "<my_secret>"
  },
  "events": [
    "TranscriptionCompletion"
  ],
  "active": true,
  "name": "TranscriptionCompletionWebHook",
  "description": "This is a Webhook created to trigger an HTTP POST request when my audio file transcription is completed.",
  "properties": {
      "Active" : "True"
  }

}
```
Všechny požadavky POST do rozhraní API Batch přepisu vyžadují `name`. Parametry `description` a`properties` jsou volitelné.

Tato `Active` vlastnost slouží k přepnutí zpětného volání zpět do vaší adresy URL bez nutnosti odstranit a znovu vytvořit registraci Webhooku. Pokud potřebujete volat pouze jednou po dokončení procesu, pak odstraňte Webhook a přepněte `Active` vlastnost na false.

Typ `TranscriptionCompletion` události je k dispozici v poli události. Pokud se přepis dostane do stavu terminálu (`Succeeded` nebo `Failed`), bude volat zpátky do koncového bodu. Při volání zpět na registrovanou adresu URL bude požadavek obsahovat `X-MicrosoftSpeechServices-Event` hlavičku obsahující jeden z registrovaných typů událostí. Na registrovaný typ události je jedna žádost.

Existuje jeden typ události, ke kterému se nelze přihlásit. Jedná se o `Ping` typ události. Požadavek s tímto typem se pošle na adresu URL, když se při použití adresy URL pro připojení k nástroji otestuje Webhook (viz níže).  

V konfiguraci `url` je vlastnost povinná. Žádosti POST se odesílají na tuto adresu URL. `secret` Slouží k vytvoření hodnoty hash SHA256 datové části s tajným klíčem jako klíč HMAC. Hodnota hash je nastavena jako `X-MicrosoftSpeechServices-Signature` záhlaví při volání zpět na registrovanou adresu URL. Tato hlavička je kódovaná v kódování Base64.

Tato ukázka ukazuje, jak ověřit datovou část C#pomocí:

```csharp

private const string EventTypeHeaderName = "X-MicrosoftSpeechServices-Event";
private const string SignatureHeaderName = "X-MicrosoftSpeechServices-Signature";

[HttpPost]
public async Task<IActionResult> PostAsync([FromHeader(Name = EventTypeHeaderName)]WebHookEventType eventTypeHeader, [FromHeader(Name = SignatureHeaderName)]string signature)
{
    string body = string.Empty;
    using (var streamReader = new StreamReader(this.Request.Body))
    {
        body = await streamReader.ReadToEndAsync().ConfigureAwait(false);
        var secretBytes = Encoding.UTF8.GetBytes("my_secret");
        using (var hmacsha256 = new HMACSHA256(secretBytes))
        {
            var contentBytes = Encoding.UTF8.GetBytes(body);
            var contentHash = hmacsha256.ComputeHash(contentBytes);
            var storedHash = Convert.FromBase64String(signature);
            var validated = contentHash.SequenceEqual(storedHash);
        }
    }

    switch (eventTypeHeader)
    {
        case WebHookEventType.Ping:
            // Do your ping event related stuff here (or ignore this event)
            break;
        case WebHookEventType.TranscriptionCompletion:
            // Do your subscription related stuff here.
            break;
        default:
            break;
    }

    return this.Ok();
}

```
V tomto fragmentu `secret` kódu je dekóduje a ověřen. Všimněte si také, že byl přepnut typ události Webhooku. V současné době je u každého dokončeného přepisu jedna událost. Kód opakuje pokusy o každou událost (s zpožděním sekund), než se zaregistruje.

### <a name="other-webhook-operations"></a>Další operace Webhooku

Pro získání všech registrovaných webhooků: ČTĚTE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Postup získání jednoho konkrétního Webhooku: ČTĚTE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

Postup odebrání jednoho konkrétního Webhooku: DELETE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note]
> V předchozím příkladu je oblast "westus". To by mělo být nahrazeno oblastí, ve které jste vytvořili prostředek služby Speech Services v Azure Portal.

Tělo https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping příspěvku: prázdné

Pošle požadavek POST na registrovanou adresu URL. Požadavek obsahuje `X-MicrosoftSpeechServices-Event` hlavičku s hodnotou "otestuje". Pokud byl Webhook zaregistrován s tajným klíčem, bude obsahovat `X-MicrosoftSpeechServices-Signature` hlavičku s hodnotou SHA256 hash datové části s tajným klíčem, jako je klíč HMAC. Hodnota hash je kódovaná v kódování Base64.

Tělo https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test příspěvku: prázdné

Pošle požadavek POST na registrovanou adresu URL, pokud se v systému nachází entita pro typ události s předplatným typem (přepis) a je v příslušném stavu. Datová část bude vygenerována z poslední entity, která by vyvolala webový Hook. Pokud není přítomna žádná entita, bude příspěvek odpovídat 204. Pokud se žádost o test dá provést, odpoví 200. Tělo požadavku má stejný tvar jako v žádosti o získání konkrétní entity, pro kterou se Webhook předplatil (například přepisu instance). Požadavek bude obsahovat `X-MicrosoftSpeechServices-Event` záhlaví a `X-MicrosoftSpeechServices-Signature` , jak je popsáno výše.

### <a name="run-a-test"></a>Spustit test

Rychlý test lze provést pomocí webu https://bin.webhookrelay.com. Odtud můžete získat adresy URL zpětného volání, které se mají předat jako parametr HTTP POST pro vytvoření Webhooku popsaného výše v dokumentu.

Klikněte na vytvořit sadu a postupujte podle pokynů na obrazovce pro získání zavěšení. Pak použijte informace uvedené na této stránce k registraci zavěšení ve službě Speech. Datová část zprávy přenosu – v reakci na dokončení přepisu vypadá takto:

```json
{
    "results": [],
    "recordingsUrls": [
        "my recording URL"
    ],
    "models": [
        {
            "modelKind": "AcousticAndLanguage",
            "datasets": [],
            "id": "a09c8c8b-1090-443c-895c-3b1cf442dec4",
            "createdDateTime": "2019-03-26T12:48:46Z",
            "lastActionDateTime": "2019-03-26T14:04:47Z",
            "status": "Succeeded",
            "locale": "en-US",
            "name": "v4.13 Unified",
            "description": "Unified",
            "properties": {
                "Purpose": "OnlineTranscription,BatchTranscription,LanguageAdaptation",
                "ModelClass": "unified-v4"
            }
        }
    ],
    "statusMessage": "None.",
    "id": "d41615e1-a60e-444b-b063-129649810b3a",
    "createdDateTime": "2019-04-16T09:35:51Z",
    "lastActionDateTime": "2019-04-16T09:38:09Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Simple transcription",
    "description": "Simple transcription description",
    "properties": {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True",
        "AddSentiment": "True",
        "Duration": "00:00:02"
    }
}
```
Zpráva obsahuje adresu URL záznamu a modely používané k přepisovat tohoto záznamu.

## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)

---
title: Webhooky – hlasové služby
titlesuffix: Azure Cognitive Services
description: Webhooky jsou ideální pro optimalizaci vašeho řešení při práci s dlouhým zpětných volání HTTP s procesy, jako je importy, přizpůsobení, testy přesnosti nebo přepisů dlouho běžící souborů.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 3ceaed2b1e27a1f5b910865f6e9d0e70ef347b71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515395"
---
# <a name="webhooks-for-speech-services"></a>Webhooky pro hlasové služby

Webhooky jsou jako zpětná volání HTTP, které umožňují vaší aplikaci přijímat data z hlasové služby, až bude k dispozici. Pomocí webhooků, můžete optimalizovat vaše užívání rozhraní REST API pomocí tím eliminuje nutnost neustálého dotazování pro odpověď. V následujících částech dozvíte postupy použití webhooků pomocí hlasové služby.

## <a name="supported-operations"></a>Podporované operace

Hlasové služby podpora webhooků pro všechny dlouho běžící operace. Každou z operací uvedené níže můžete aktivovat zpětné volání HTTP při dokončení. 

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

V dalším kroku vytvoříme webhooku.

## <a name="create-a-webhook"></a>Vytvořit webhook

Pojďme vytvořit webhook pro offline přepis. Scénář: uživatel má dlouho spuštěná zvukový soubor, který by chtěli přepisy asynchronně pomocí rozhraní API služby Batch určené k transkripci. 

K vytvoření web hook příspěvek https://<region>.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Parametry konfigurace pro žádost se poskytují jako JSON:

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
Vyžadovat všech požadavků POST na rozhraní API služby Batch určené k transkripci `name`. `description` a `properties` parametry jsou volitelné.

`Active` Vlastnost se používá k přepnutí zpětné volání do adresy URL a vypnout, aniž by bylo nutné odstranit a znovu vytvořit registrace webhooku. Pokud potřebujete pouze pro zpětné volání jednou po procesu kompletní, poté odstranit webhook a přepínač `Active` vlastnost na hodnotu false.

Typ události `TranscriptionCompletion` je uvedený v poli události. To zavolá zpět do vašeho koncového bodu při určené k transkripci dostane do konečného stavu (`Succeeded` nebo `Failed`). Při zpětné volání registrované adresy URL, bude obsahovat žádost `X-MicrosoftSpeechServices-Event` záhlaví obsahující jeden z typů registrované události. Existuje jeden požadavek na typ registrované události. 

Existuje jeden typ události, která se nemůže přihlásit k odběru. Je `Ping` typ události. Požadavek s tímto typem je odeslán na adresu URL po dokončení vytvoření webhooku, při použití příkazu ping adresy URL (viz níže).  

V konfiguraci `url` vlastnost je povinná. Požadavky POST se odesílají na tuto adresu URL. `secret` Slouží k vytvoření s tajným klíčem jako klíčem HMAC hash SHA256 objektu datové části. Hodnota hash je nastaven jako `X-MicrosoftSpeechServices-Signature` hlavičku při zpětné volání registrované adresy URL. Tato hlavička se kódování Base64.

Tento příklad ukazuje, jak ověřit datovou část pomocí C#:

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
V tomto fragmentu kódu `secret` je dekódovat a ověřit. Také můžete všimnout, že bylo změněno typ události webhooku. Aktuálně je jednu událost za dokončené určené k transkripci. Kód opakování pětkrát pro všechny události (s jeden druhý zpožděním), než se ukončí.

### <a name="other-webhook-operations"></a>Další operace webhooku

Pokud chcete získat všechny registrované webhooků: ZÍSKAT https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Chcete-li získat jeden konkrétní webhooku: ZÍSKAT https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

Chcete-li odebrat jednu konkrétní webhooku: DELETE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note] 
> V předchozím příkladu je oblast 'westus'. To je třeba nahradit oblasti, kde jste vytvořili váš prostředek hlasové služby na webu Azure Portal.

PŘÍSPĚVEK https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping subjekt: prázdné

Odešle požadavek POST na adresu registrovaný. Požadavek obsahuje `X-MicrosoftSpeechServices-Event` záhlaví pomocí příkazu ping hodnotu. Webhook byl zaregistrován s tajným kódem, bude obsahovat `X-MicrosoftSpeechServices-Signature` záhlaví s algoritmus hash SHA256 datové části s tajným klíčem HMAC klíče. Hodnota hash je kódování Base64. 

PŘÍSPĚVEK https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test subjekt: prázdné

Pokud entitu pro typ předplacenému události (určené k transkripci) je k dispozici v systému a je ve správném stavu, odešle požadavek POST na adresu registrované. Datová část se budou generovat z poslední entita, která by vyvolali volané webhookem. Pokud je k dispozici žádné entity, odpoví na příspěvek 204. Pokud testovací požadavek může být provedeno, odpoví 200. Text požadavku je stejný tvar stejně jako v požadavek GET na konkrétní entitu, že že webhook má přihlášený(á) k odběru (například určené k transkripci). Žádost bude mít `X-MicrosoftSpeechServices-Event` a `X-MicrosoftSpeechServices-Signature` záhlaví, jak je popsáno před.

### <a name="run-a-test"></a>Spuštění testu

Akci můžete udělat rychle otestovat na webu https://bin.webhookrelay.com. Odtud můžete získat volání zpět adresy URL pro předání jako parametru HTTP POST pro vytvoření webhooku je popsáno výše v dokumentu.

Klikněte na "Vytvoření kontejneru a použijte na obrazovce pokyny, jak získat hák. Potom použijte informace uvedené na této stránce k registraci háku službou Speech. Datová část přenos zprávy – v reakci na dokončení určené k transkripci – vypadá takto:

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
Zpráva obsahuje adresu URL záznam a modely pro přepisy tohoto záznamu.

## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)

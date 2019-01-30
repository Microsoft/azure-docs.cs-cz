---
title: Začínáme s využitím REST pomocí rozhraní API pro rozpoznávání řeči Bingu | Dokumentace Microsoftu
titlesuffix: Azure Cognitive Services
description: Použití REST pro přístup k rozhraní API pro rozpoznávání řeči v Microsoft Cognitive Services k převést mluvené slovo na text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: fc40f20349e3424dbcf50f5ca0395b06a818566f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224050"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>Rychlý start: Rozpoznávání řeči Bingu rozhraní REST API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Založené na cloudu Speech Service Bingu vám umožní vytvářet aplikace pomocí rozhraní REST API k převést mluvené slovo na text.

## <a name="prerequisites"></a>Požadavky

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Přihlaste se k rozhraní API pro rozpoznávání řeči odběru a získejte klíč bezplatné předplatné zkušební verze

Rozhraní Speech API je součástí služeb Cognitive Services (dříve Project Oxford). Můžete získat bezplatné předplatné zkušební verze klíče z [předplatné služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) stránky. Po výběru rozhraní API pro rozpoznávání řeči, vyberte **získat klíč rozhraní API** získat klíč. Vrátí primární a sekundární klíč. Oba klíče jsou svázány se stejnou kvótu, abyste mohli používat ani jeden klíč.

> [!IMPORTANT]
>* Získáte klíč předplatného. Pro přístup k rozhraní REST API, musíte mít [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).
>
>* Použijte váš klíč předplatného. V následující ukázky REST nahraďte YOUR_SUBSCRIPTION_KEY klíč předplatného. 
>
>* Odkazovat [ověřování](../how-to/how-to-authentication.md) stránky pro získání klíč předplatného.

### <a name="prerecorded-audio-file"></a>Zvukový soubor připraveného obsahu

V tomto příkladu používáme zaznamenané zvukový soubor si ukážeme, jak používat rozhraní REST API. Zaznamenejte zvukový soubor fotku říká krátkých frází. Předpokládejme například, že "Jak se počasí, stejně jako dnes?" nebo "Najít zábavných filmy a sledujte." Rozhraní API pro rozpoznávání řeči podporuje také externí mikrofon vstup.

> [!NOTE]
> V příkladu vyžaduje, že zvuk se zaznamenává jako soubor WAV s **PCM jeden kanál (mono), 16 KHz**.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Žádost o rozpoznávání sestavení a jejich odesílání do službu rozpoznávání řeči

Dalším krokem pro rozpoznávání řeči je odeslat požadavek POST do koncových bodů HTTP řeči se správnou žádost hlavička a tělo zprávy.

### <a name="service-uri"></a>Identifikátor URI služby

Službu rozpoznávání řeči, který je definován identifikátor URI na základě [rozpoznávání režimy](../concepts.md#recognition-modes) a [rozpoznávání jazyků](../concepts.md#recognition-languages):

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>` Určuje režim rozpoznávání a musí být jeden z následujících hodnot: `interactive`, `conversation`, nebo `dictation`. To je cesta požadovaný prostředek v identifikátoru URI. Další informace najdete v tématu [rozpoznávání režimy](../concepts.md#recognition-modes).

`<LANGUAGE_TAG>` je povinný parametr v řetězci dotazu. Definuje cílový jazyk pro převod zvuku: například `en-US` pro angličtinu (Spojené státy). Další informace najdete v tématu [rozpoznávání jazyků](../concepts.md#recognition-languages).

`<OUTPUT_FORMAT>` je volitelný parametr v řetězci dotazu. Povolené hodnoty jsou `simple` a `detailed`. Ve výchozím nastavení, tato služba vrátí výsledky v `simple` formátu. Další informace najdete v tématu [výstupní formát](../concepts.md#output-format).

V následující tabulce jsou uvedeny některé příklady identifikátorů URI služby.

| Režim rozpoznávání  | Jazyk | Výstupní formát | Identifikátor URI služby |
|---|---|---|---|
| `interactive` | pt-BR | Výchozí | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | en-US | Podrobný |https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | fr-FR | Jednoduchý | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> Identifikátor URI služby je potřeba pouze v případě, že vaše aplikace používá rozhraní REST API pro volání službu rozpoznávání řeči. Pokud použijete jeden z [klientské knihovny](GetStartedClientLibraries.md), obvykle není nutné vědět, jaké identifikátor URI se používá. Tyto klientské knihovny může používat jinou službu identifikátory URI, které se dají použít jenom pro konkrétní klientské knihovny. Další informace najdete v klientské knihovně podle vašeho výběru.

### <a name="request-headers"></a>Hlavičky požadavku

Následující pole musí být nastaveny v hlavičce žádosti:

- `Ocp-Apim-Subscription-Key`: Při každém volání služby, je nutné předat klíč předplatného. v `Ocp-Apim-Subscription-Key` záhlaví. Speech Service podporuje také povolení předávání tokenů místo klíče předplatného. Další informace najdete v tématu [Ověřování](../How-to/how-to-authentication.md).
- `Content-type`: `Content-type` Pole popisuje formátu a kodek zvukový datový proud. V současné době pouze soubor WAV a PCM Mono 16000 kódování je podporováno. Hodnota Content-type pro tento formát je `audio/wav; codec=audio/pcm; samplerate=16000`.

Pole `Transfer-Encoding` je nepovinné. Pokud nastavíte toto pole na `chunked`, můžete jej zvuku do malých bloků. Další informace najdete v tématu [přenos rozdělený do bloků dat](../How-to/how-to-chunked-transfer.md).

Hlavička požadavku vzorku je následující:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>Odeslat žádost o službu

Následující příklad ukazuje, jak odeslat žádost o rozpoznávání řeči pro koncové body REST řeči. Používá `interactive` rozpoznávání režimu.

> [!NOTE]
> Nahraďte `YOUR_AUDIO_FILE` cestou k souboru připraveného obsahu zvuku. Nahraďte `YOUR_SUBSCRIPTION_KEY` s klíči předplatného.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authorization token returned by the token service.
$RecoRequestHeader = @{
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY';
  'Transfer-Encoding' = 'chunked';
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

V příkladu používá curl v Linuxu pomocí prostředí bash. Pokud není dostupný na vaší platformě, může být potřeba nainstalovat nástroj curl. Tento příklad funguje taky na Cygwin na Windows, Git Bash, zsh a jiné prostředí.

> [!NOTE]
> Zachovat `@` před názvem zvukového souboru při nahrazování `YOUR_AUDIO_FILE` cestou k souboru připraveného obsahu zvuku, jako to znamená, že hodnota `--data-binary` je název souboru, ne data.

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

// Send an audio file by 1024 byte chunks
using (FileStream fs = new FileStream(YOUR_AUDIO_FILE, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

---

## <a name="process-the-speech-recognition-response"></a>Zpracování odpovědi rozpoznávání řeči

Speech Service po zpracování požadavku, vrátí výsledky v odpovědi ve formátu JSON.

> [!NOTE]
> Pokud předchozí kód vrátí chybu, přečtěte si téma [Poradce při potížích s](../troubleshooting.md) vyhledejte možnou příčinu.

Následující fragment kódu ukazuje příklad, jak si můžete přečíst odpověď z datového proudu.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[curl](#tab/curl)

V tomto příkladu vrátí curl přímo zprávy s odpovědí v řetězci. Pokud chcete zobrazit ve formátu JSON, můžete použít další nástroje, například jq.

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
/*
* Get the response from the service.
*/
Console.WriteLine("Response:");
using (WebResponse response = request.GetResponse())
{
    Console.WriteLine(((HttpWebResponse)response).StatusCode);

    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    {
        responseString = sr.ReadToEnd();
    }

    Console.WriteLine(responseString);
    Console.ReadLine();
}
```

---

Následující příklad je odpověď JSON:

```json
OK
{
  "RecognitionStatus": "Success",
  "Offset": 22500000,
  "Duration": 21000000,
  "NBest": [{
    "Confidence": 0.941552162,
    "Lexical": "find a funny movie to watch",
    "ITN": "find a funny movie to watch",
    "MaskedITN": "find a funny movie to watch",
    "Display": "Find a funny movie to watch."
  }]
}
```

## <a name="limitations"></a>Omezení

Rozhraní REST API má určitá omezení:

- Zvukový datový proud podporuje pouze do 15 sekund.
- Při rozpoznávání nepodporuje mezilehlých výsledků. Uživatelé dostanou jenom poslední rozpoznávání výsledek.

Chcete-li odebrat tato omezení, umožňuje využít řeč [klientské knihovny](GetStartedClientLibraries.md). Nebo může spolupracovat přímo s [protokol WebSocket řeči](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>Kam dál

- Chcete-li zjistit, jak používat rozhraní REST API v jazyce C#, Java, atd., informace najdete v těchto [ukázkové aplikace](../samples.md).
- Účelem vypátrání a opravení chyb, naleznete v tématu [Poradce při potížích s](../troubleshooting.md).
- Pokud chcete používat pokročilejší funkce, zjistit, jak začít tím, že pomocí řeči [klientské knihovny](GetStartedClientLibraries.md).

### <a name="license"></a>Licence

Všechny Cognitive Services SDK a ukázky jsou licencovány s licencí MIT. Další informace najdete v tématu [licence](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).

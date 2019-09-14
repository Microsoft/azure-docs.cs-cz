---
title: Začínáme s rozhraním API pro rozpoznávání Zpracování řeči Bingu pomocí REST | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Pro přístup k rozhraní API pro rozpoznávání řeči v Microsoft Cognitive Services použijte REST a převeďte mluvený zvuk na text.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e962a12c6c27737f95e78e80036e51bac41147d5
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965772"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>Rychlý start: Použití rozpoznávání Zpracování řeči Bingu REST API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Díky cloudové Zpracování řeči Bingu službě můžete vyvíjet aplikace pomocí REST API k převedení mluveného zvuku na text.

## <a name="prerequisites"></a>Požadavky

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Přihlaste se k odběru rozhraní API pro rozpoznávání řeči a Stáhněte si bezplatný zkušební klíč předplatného.

Rozhraní API pro rozpoznávání řeči je součástí Cognitive Services (dříve projekt Oxford). Můžete získat bezplatný zkušební odběr klíčů na stránce [Cognitive Services předplatné](https://azure.microsoft.com/try/cognitive-services/) . Po výběru rozhraní API pro rozpoznávání řeči vyberte **získat klíč rozhraní API** a získejte klíč. Vrátí primární a sekundární klíč. Oba klíče jsou vázané na stejnou kvótu, takže můžete použít kteroukoli z těchto klíčů.

> [!IMPORTANT]
>* Získejte klíč předplatného. Předtím, než budete moci získat přístup k REST API, je nutné mít [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).
>
>* Použijte svůj klíč předplatného. V následujících ukázkách REST nahraďte YOUR_SUBSCRIPTION_KEY vlastním klíčem předplatného.
>
>* Informace o tom, jak získat klíč předplatného, najdete na stránce [ověřování](../how-to/how-to-authentication.md) .

### <a name="prerecorded-audio-file"></a>Předzáznamový zvukový soubor

V tomto příkladu používáme zaznamenaný zvukový soubor k ilustraci způsobu použití REST API. Poznamenejte si zvukový soubor, který říká krátké slovní spojení. Řekněme například, že je to počasí jako v dnešní době? nebo "hledání filmů Funny ke sledování". Rozhraní API pro rozpoznávání řeči podporuje také externí vstup mikrofonu.

> [!NOTE]
> Příklad vyžaduje, aby se zvuk zaznamenal jako soubor WAV s **jedním kanálem PCM (mono), 16 kHz**.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Sestavení žádosti o rozpoznání a odeslání IT službě rozpoznávání řeči

Dalším krokem pro rozpoznávání řeči je odeslání požadavku POST koncovým bodům HTTP pomocí správné hlavičky a textu žádosti.

### <a name="service-uri"></a>Identifikátor URI služby

Identifikátor URI služby rozpoznávání řeči je definován na základě [režimů rozpoznávání](../concepts.md#recognition-modes) a [jazyků rozpoznávání](../concepts.md#recognition-languages):

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>`Určuje režim rozpoznávání a musí mít jednu z následujících hodnot: `interactive`, `conversation`, nebo `dictation`. Je to požadovaná cesta prostředku v identifikátoru URI. Další informace najdete v tématu [režimy rozpoznávání](../concepts.md#recognition-modes).

`<LANGUAGE_TAG>`je povinný parametr v řetězci dotazu. Definuje cílový jazyk pro převod zvuku: například `en-US` pro angličtinu (USA). Další informace najdete v tématu [jazyky rozpoznávání](../concepts.md#recognition-languages).

`<OUTPUT_FORMAT>`je volitelný parametr v řetězci dotazu. Povolené hodnoty jsou `simple` a `detailed`. Ve výchozím nastavení vrátí služba výsledky ve `simple` formátu. Další informace naleznete v tématu [Output Format](../concepts.md#output-format).

V následující tabulce jsou uvedeny některé příklady identifikátorů URI služby.

| Režim rozpoznávání  | Jazyk | Výstupní formát | Identifikátor URI služby |
|---|---|---|---|
| `interactive` | pt-BR | Výchozí | https:\//Speech.Platform.Bing.com/Speech/Recognition/Interactive/cognitiveservices/v1?Language=pt-br |
| `conversation` | en-US | Podrobný | https:\//Speech.Platform.Bing.com/Speech/Recognition/Conversation/cognitiveservices/v1?Language=en-us&Format=detailed |
| `dictation` | fr-FR | Jednoduchý | https:\//Speech.Platform.Bing.com/Speech/Recognition/Dictation/cognitiveservices/v1?language=fr-fr&Format=Simple |

> [!NOTE]
> Identifikátor URI služby je potřeba jenom v případě, že vaše aplikace používá rozhraní REST API pro volání služby rozpoznávání řeči. Pokud používáte jednu z [klientských knihoven](GetStartedClientLibraries.md), většinou nemusíte znát, který identifikátor URI se používá. Klientské knihovny můžou používat jiné identifikátory URI služby, které platí jenom pro konkrétní klientské knihovny. Další informace najdete v klientské knihovně podle vašeho výběru.

### <a name="request-headers"></a>Hlavičky požadavku

V hlavičce požadavku musí být nastavená následující pole:

- `Ocp-Apim-Subscription-Key`: Pokaždé, když zavoláte službu, musíte v `Ocp-Apim-Subscription-Key` hlavičce předat klíč předplatného. Služba Speech také podporuje předávání autorizačních tokenů místo klíčů předplatného. Další informace najdete v tématu [Ověřování](../How-to/how-to-authentication.md).
- `Content-type`: `Content-type` Pole popisuje formát a kodek zvukového datového proudu. V současné době se podporuje jenom soubory WAV a kódování PCM mono 16000. Hodnota Content-Type pro tento formát je `audio/wav; codec=audio/pcm; samplerate=16000`.

Pole `Transfer-Encoding` je nepovinné. Pokud toto pole nastavíte na `chunked`, můžete zvuk CHOP do malých bloků dat. Další informace najdete v části [přenos v bloku dat](../How-to/how-to-chunked-transfer.md).

Následuje ukázka hlavičky žádosti:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>Odeslat žádost službě

Následující příklad ukazuje, jak odeslat požadavek na rozpoznávání řeči koncovým bodům REST. Používá `interactive` režim rozpoznávání.

> [!NOTE]
> Nahraďte `YOUR_AUDIO_FILE` cestou k souboru připraveného obsahu zvuku. Nahraďte `YOUR_SUBSCRIPTION_KEY` vlastním klíčem předplatného.

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

# <a name="curltabcurl"></a>[Curl](#tab/curl)

V příkladu se používá oblé v systému Linux s bash. Pokud není na vaší platformě k dispozici, může být nutné nainstalovat kudrlinkou. Příklad funguje také na Cygwin ve Windows, Gitu bash, zsh a dalších prostředích.

> [!NOTE]
> `--data-binary` Při nahrazení `YOUR_AUDIO_FILE` cestou k souboru s předpsaným zvukovým souborem ponechte před název zvukový soubor, protože označuje, že hodnota je název souboru namísto dat. `@`

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

## <a name="process-the-speech-recognition-response"></a>Zpracování odpovědi na rozpoznávání řeči

Služba Speech po zpracování žádosti vrátí výsledky v odpovědi jako formát JSON.

> [!NOTE]
> Pokud předchozí kód vrátí chybu, přečtěte si téma [řešení potíží](../troubleshooting.md) a najděte možnou příčinu.

Následující fragment kódu ukazuje příklad, jak můžete číst odpověď z datového proudu.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[Curl](#tab/curl)

V tomto příkladu vytvoří funkce kudrlinkou přímo zprávu odpovědi v řetězci. Pokud ho chcete zobrazit ve formátu JSON, můžete použít další nástroje, například JQ.

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

Následující ukázka je odpověď JSON:

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

REST API má určitá omezení:

- Streamování podporuje jenom až 15 sekund.
- Při rozpoznávání nepodporuje mezilehlé výsledky. Uživatelé obdrží jenom konečný výsledek rozpoznávání.

Pokud chcete tato omezení odebrat, použijte [klientské knihovny](GetStartedClientLibraries.md)řeči. Nebo můžete pracovat přímo s [protokolem WebSocket pro řeč](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>Kam dál

- Chcete-li zjistit, jak používat REST API C#v jazyce, Java atd., přečtěte si tyto [ukázkové aplikace](../samples.md).
- Chcete-li vyhledat a opravit chyby, přečtěte si téma [řešení potíží](../troubleshooting.md).
- Pokud chcete použít pokročilejší funkce, přečtěte si téma jak začít pomocí [knihoven klienta](GetStartedClientLibraries.md)řeči.

### <a name="license"></a>Licence

Všechny sady SDK a ukázky pro Cognitive Services jsou licencované pomocí licence MIT. Další informace najdete v tématu [License](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).

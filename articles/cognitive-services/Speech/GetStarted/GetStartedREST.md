---
title: Začínáme s rozhraním API pro rozpoznávání řeči Microsoft pomocí REST | Microsoft Docs
description: REST použijte pro přístup k rozhraní API pro rozpoznávání řeči v na mluvené zvuk převedeno na text kognitivní služby společnosti Microsoft.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 53785cdfd75c23910802f2be20e6305817b3b097
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342660"
---
# <a name="get-started-with-speech-recognition-by-using-the-rest-api"></a>Začínáme s rozpoznávání řeči pomocí rozhraní REST API

Aplikace s cloudovou službou řeči můžete vyvíjet pomocí rozhraní REST API mluvené zvuk převést na text.

## <a name="prerequisites"></a>Požadavky

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Přihlášení k odběru do rozhraní API pro rozpoznávání řeči a získat klíč bezplatné předplatné zkušební verze

Rozhraní API pro rozpoznávání řeči je součástí služby kognitivní (dříve Oxford projektu). Můžete získat zkušební předplatné klíče z [kognitivní služby předplatného](https://azure.microsoft.com/try/cognitive-services/) stránky. Po výběru rozhraní API pro rozpoznávání řeči, vyberte **získat klíč rozhraní API** k získání klíče. Vrátí primární a sekundární klíč. Oba klíče jsou svázané s stejné kvótu, takže můžete použít buď klíč.

> [!IMPORTANT]
>* Získáte klíč předplatného. Než se dostanete k rozhraní REST API, musíte mít [klíč předplatného](https://azure.microsoft.com/try/cognitive-services/).
>
>* Použijte svůj klíč předplatného. V následující ukázky REST nahraďte YOUR_SUBSCRIPTION_KEY svůj vlastní klíč předplatného. 
>
>* Odkazovat [ověřování](../how-to/how-to-authentication.md) stránku jak získat klíč předplatného.

### <a name="prerecorded-audio-file"></a>Zvukový soubor připraveného obsahu

V tomto příkladu používáme zaznamenaný zvukový soubor pro ilustraci použití rozhraní REST API. Zaznamenejte zvukový soubor sami oznámením krátké věty. Řekněme například, "Co je počasí jako dnes?" nebo "Najít zábavného filmy si chcete přehrát." Rozpoznávání řeči rozhraní API podporuje také externí mikrofon vstup.

> [!NOTE]
> Příklad vyžaduje, že zvuk se zaznamenává jako soubor WAV s **PCM jeden kanál (mono), 16 KHz**.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Vytvořit žádost o rozpoznávání a odeslat do služby rozpoznávání řeči

Další krok pro rozpoznávání řeči je odeslat požadavek POST na koncové body HTTP řeči s hlavičky správné žádosti a text.

### <a name="service-uri"></a>Identifikátor URI služby

Služba rozpoznávání řeči identifikátor URI je definovaný na základě [režimy rozpoznávání](../concepts.md#recognition-modes) a [jazyky rozpoznávání](../concepts.md#recognition-languages):

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>` Určuje režim rozpoznávání a musí mít jednu z následujících hodnot: `interactive`, `conversation`, nebo `dictation`. Je cesta požadovaný prostředek v identifikátoru URI. Další informace najdete v tématu [režimy rozpoznávání](../concepts.md#recognition-modes).

`<LANGUAGE_TAG>` je povinný parametr v řetězci dotazu. Definuje cílový jazyk pro převod zvuk: například `en-US` pro angličtinu (Spojené státy). Další informace najdete v tématu [jazyky rozpoznávání](../concepts.md#recognition-languages).

`<OUTPUT_FORMAT>` je volitelný parametr v řetězci dotazu. Jeho povolené hodnoty jsou `simple` a `detailed`. Ve výchozím nastavení, služba vrátí výsledky v `simple` formátu. Další informace najdete v tématu [výstupní formát](../concepts.md#output-format).

Některé příklady služby identifikátory URI jsou uvedené v následující tabulce.

| Režim rozpoznávání  | Jazyk | Výstupní formát | Identifikátor URI služby |
|---|---|---|---|
| `interactive` | pt-BR | Výchozí | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | cs-CZ | Podrobný |https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | fr-FR | Jednoduchý | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> URI služby je potřeba jenom v případě, že vaše aplikace používá rozhraní REST API pro volání služby rozpoznávání řeči. Pokud použijete jednu z [klientské knihovny](GetStartedClientLibraries.md), obvykle nemusíte vědět, které URI se používá. Knihovny klienta může použít jinou službu identifikátory URI, které jsou k dispozici pouze pro konkrétního klienta knihovny. Další informace najdete v knihovně klienta podle vašeho výběru.

### <a name="request-headers"></a>Hlavičky požadavku

Následující pole musí být nastavena v hlavičce žádosti:

- `Ocp-Apim-Subscription-Key`: Při každém volání služby, je nutné předat svůj klíč předplatného v `Ocp-Apim-Subscription-Key` záhlaví. Služba rozpoznávání řeči také podporuje předávání autorizace tokeny místo předplatné klíče. Další informace najdete v tématu [ověřování](../How-to/how-to-authentication.md).
- `Content-type`: `Content-type` Pole popisuje formát a kodeků zvuk datového proudu. V současné době pouze WAV soubor a je podporováno kódování PCM Mono 16000. Hodnota Content-type pro tento formát je `audio/wav; codec=audio/pcm; samplerate=16000`.

`Transfer-Encoding` Pole je nepovinné. Pokud nastavíte toto pole na `chunked`, můžete jej zvukovém souboru na malé bloky. Další informace najdete v tématu [blokové přenos](../How-to/how-to-chunked-transfer.md).

Zde je ukázka hlavička požadavku:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>Poslat žádost o službu

Následující příklad ukazuje, jak k odeslání požadavku na rozpoznávání řeči ke koncovým bodům řeči REST. Použije `interactive` rozpoznávání režimu.

> [!NOTE]
> Nahraďte `YOUR_AUDIO_FILE` s cestou k souboru připraveného obsahu zvuk. Nahraďte `YOUR_SUBSCRIPTION_KEY` s svůj vlastní klíč předplatného.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

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

Tento příklad používá curl v systému Linux s bash. Pokud není k dispozici na vaši platformu, možná muset nainstalovat curl. Tento příklad funguje taky na emulaci ve Windows, Git Bash, zsh a další součásti pro.

> [!NOTE]
> Zachovat `@` před název souboru zvuk při nahrazování `YOUR_AUDIO_FILE` s cestou k souboru připraveného obsahu zvuk, jako je znamená, že hodnota `--data-binary` je název souboru místo data.

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

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

Po zpracování požadavku, služba rozpoznávání řeči vrátí výsledky v odpovědi jako formátu JSON.

> [!NOTE]
> Pokud předchozí kód vrátí chybu, najdete v části [Poradce při potížích s](../troubleshooting.md) vyhledejte možnou příčinu.

Následující fragment kódu ukazuje příklad jak přečíst odpověď z datového proudu.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[curl](#tab/curl)

V tomto příkladu curl přímo vrátí zprávu odpovědi v řetězci. Pokud chcete zobrazit ve formátu JSON, můžete další nástroje, například jq.

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

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

- Datový proud zvuku podporuje pouze do 15 sekund.
- Při rozpoznávání nepodporuje mezilehlých výsledků. Uživatelé obdrží pouze poslední rozpoznávání výsledek.

Chcete-li odebrat tato omezení, použijte řeči [klientské knihovny](GetStartedClientLibraries.md). Nebo může spolupracovat přímo s [protokol WebSocket řeči](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>Kam dál

- Chcete-li zjistit, jak používat rozhraní API REST v C#, Java, atd., podívejte na tyto [ukázkové aplikace](../samples.md).
- Vyhledejte a opravte chyby, přečtěte si téma [Poradce při potížích s](../troubleshooting.md).
- Použití dalších pokročilých funkcí naleznete v tématu jak začít pracovat s použitím řeči [klientské knihovny](GetStartedClientLibraries.md).

### <a name="license"></a>Licence

Všechny sady SDK kognitivní služeb a ukázky licenci s licencí MIT. Další informace najdete v tématu [licence](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).

---
title: Odkaz na rozhraní API pro převod řeči na text (REST) – služba řeči
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak používat rozhraní REST API pro převod řeči na text. V tomto článku se dozvíte o možnostech autorizace, možnostech dotazu, způsobu strukturování požadavku a přijetí odpovědi.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: fbb4d114d1fee21d7950e53b06fc16c96b5c930b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400183"
---
# <a name="speech-to-text-rest-api"></a>Rozhraní REST API pro převod řeči na text

Jako alternativu k [řeči SDK](speech-sdk.md), řeči služba umožňuje převést řeč na text pomocí rozhraní REST API. Každý přístupný koncový bod je přidružen k oblasti. Vaše aplikace vyžaduje klíč předplatného pro koncový bod, který chcete použít. Rozhraní REST API je velmi omezené a mělo by být použito pouze v případech, kdy sada [Speech SDK](speech-sdk.md) nemůže.

Před použitím rozhraní REST API pro převod řeči na text pochopte:

* Požadavky, které používají rozhraní REST API a přenášejí zvuk přímo, mohou obsahovat pouze 60 sekund zvuku.
* Rozhraní REST API pro převod řeči na text pouze vrací konečné výsledky. Částečné výsledky nejsou k dispozici.

Pokud je odesílání delšího zvuku požadavkem pro vaši aplikaci, zvažte použití [sady Speech SDK](speech-sdk.md) nebo rozhraní REST API založeného na souborech, jako je [dávkový přepis](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Oblasti a koncové body

Koncový bod pro rozhraní REST API má tento formát:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Nahraďte `<REGION_IDENTIFIER>` identifikátorem, který odpovídá oblasti předplatného z této tabulky:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Parametr jazyka musí být připojen k adrese URL, aby se zabránilo přijetí chyby 4xx HTTP. Například jazyk nastavený na americkou angličtinu pomocí `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`koncového bodu –USA je: .

## <a name="query-parameters"></a>Parametry dotazu

Tyto parametry mohou být zahrnuty v řetězci dotazu požadavku REST.

| Parametr | Popis | Povinné / Nepovinné |
|-----------|-------------|---------------------|
| `language` | Identifikuje mluvený jazyk, který je rozpoznán. Viz [Podporované jazyky](language-support.md#speech-to-text). | Požaduje se |
| `format` | Určuje formát výsledků. Přijaté hodnoty `simple` `detailed`jsou a . Jednoduché výsledky `RecognitionStatus` `DisplayText`zahrnují `Offset`, `Duration`, a . Podrobné odpovědi zahrnují více výsledků s hodnotami spolehlivosti a čtyři různé reprezentace. Výchozí hodnota je `simple`. | Nepovinné |
| `profanity` | Určuje způsob zpracování vulgárních výrazů ve výsledcích rozpoznávání. Přijaté hodnoty `masked`jsou , které nahrazují vulgární výrazy `removed`hvězdičkami , , které odstraní všechny `raw`vulgární výrazy z výsledku, nebo , který obsahuje vulgární výrazy ve výsledku. Výchozí hodnota je `masked`. | Nepovinné |
| `cid` | Při vytváření vlastních modelů [pomocí portálu Vlastní řeči](how-to-custom-speech.md) můžete použít vlastní modely prostřednictvím jejich **ID koncového bodu,** které se nachází na stránce **Nasazení.** Použijte **ID koncového bodu** jako `cid` argument pro parametr řetězce dotazu. | Nepovinné |

## <a name="request-headers"></a>Hlavičky požadavku

V této tabulce jsou uvedena povinná a volitelná záhlaví pro požadavky převodu řeči na text.

|Hlavička| Popis | Povinné / Nepovinné |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Klíč předplatného služby Speech. | Buď toto `Authorization` záhlaví nebo je povinné. |
| `Authorization` | Autorizační token, kterému `Bearer`předchází slovo . Další informace najdete v tématu [Ověřování](#authentication). | Buď toto `Ocp-Apim-Subscription-Key` záhlaví nebo je povinné. |
| `Content-type` | Popisuje formát a kodek poskytnutých zvukových dat. Přijaté hodnoty `audio/wav; codecs=audio/pcm; samplerate=16000` `audio/ogg; codecs=opus`jsou a . | Požaduje se |
| `Transfer-Encoding` | Určuje, že jsou odesílána data blouzniných zvukových dat, nikoli jeden soubor. Tuto hlavičku používejte pouze v případě, že jsou zvuková data zkazena. | Nepovinné |
| `Expect` | Pokud používáte blokový `Expect: 100-continue`přenos, odešlete . Služba Speech potvrzuje počáteční požadavek a čeká na další data.| Povinné při odesílání datových bloků zvukových dat. |
| `Accept` | Pokud je k dispozici, musí být `application/json`. Služba Řeč poskytuje výsledky v JSON. Některé rozhraní pro požadavky poskytují nekompatibilní výchozí hodnotu. Je vhodné vždy zahrnout `Accept`. | Nepovinné, ale doporučené. |

## <a name="audio-formats"></a>Zvukové formáty

Zvuk je odeslán v textu `POST` požadavku HTTP. Musí být v jednom z formátů v této tabulce:

| Formát | Kodek | Datový tok | Vzorkovací frekvence  |
|--------|-------|---------|--------------|
| WAV    | PCM   | 16bitový  | 16 kHz, mono |
| Ogg    | Opus  | 16bitový  | 16 kHz, mono |

>[!NOTE]
>Výše uvedené formáty jsou podporovány prostřednictvím rozhraní REST API a WebSocket ve službě Řeč. [Sada Speech SDK](speech-sdk.md) v současné době podporuje formát WAV s kodekem PCM a [dalšími formáty](how-to-use-codec-compressed-audio-input-streams.md).

## <a name="sample-request"></a>Požadavek na ukázku

Následující ukázka obsahuje název hostitele a požadovaná záhlaví. Je důležité si uvědomit, že služba také očekává zvuková data, která nejsou zahrnuta v této ukázce. Jak již bylo zmíněno dříve, chunking se doporučuje, nicméně, není nutné.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

## <a name="http-status-codes"></a>Stavové kódy HTTP

Stavový kód HTTP pro každou odpověď označuje úspěch nebo běžné chyby.

| Stavový kód HTTP | Popis | Možný důvod |
|------------------|-------------|-----------------|
| `100` | Pokračovat | Původní požadavek byl přijat. Pokračujte v odesílání zbývajících dat. (Používá se s chunked přenosu) |
| `200` | OK | Požadavek byl úspěšný. tělo odezvy je objekt JSON. |
| `400` | Chybný požadavek | Kód jazyka není k dispozici, není podporovaný jazyk, neplatný zvukový soubor, atd. |
| `401` | Neautorizováno | Klíč předplatného nebo autorizační token je v zadané oblasti neplatný nebo neplatný koncový bod. |
| `403` | Forbidden | Chybí klíč předplatného nebo autorizační token. |

## <a name="chunked-transfer"></a>Blokovaný přenos

Blokovaný přenos`Transfer-Encoding: chunked`( ) může pomoci snížit latenci rozpoznávání. Umožňuje službě Řeč zahájit zpracování zvukového souboru během přenosu. Rozhraní REST API neposkytuje částečné nebo průběžné výsledky.

Tato ukázka kódu ukazuje, jak odeslat zvuk v blocích. Pouze první blok by měl obsahovat záhlaví zvukového souboru. `request`je `HttpWebRequest` objekt připojený k příslušnému koncovému bodu REST. `audioFile`je cesta ke zvukovému souboru na disku.

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>Parametry odezvy

Výsledky jsou poskytovány jako JSON. Formát `simple` obsahuje tato pole nejvyšší úrovně.

| Parametr | Popis  |
|-----------|--------------|
|`RecognitionStatus`|Stav, například `Success` pro úspěšné uznání. Viz další stůl.|
|`DisplayText`|Rozpoznaný text po velkých písmenech, interpunkci, normalizaci inverzního textu (převod mluveného textu na kratší formy, například 200 pro "dvě stě" nebo "Dr. Smith" pro "doctor smith") a maskování vulgárních výrazů. Prezentujte pouze o úspěchu.|
|`Offset`|Čas (ve 100 nanosekundových jednotkách), kdy rozpoznaná řeč začíná v datovém proudu zvuku.|
|`Duration`|Doba trvání (ve 100 nanosekundových jednotkách) rozpoznané řeči v zvukovém proudu.|

Pole `RecognitionStatus` může obsahovat tyto hodnoty:

| Status | Popis |
|--------|-------------|
| `Success` | Uznání bylo úspěšné `DisplayText` a pole je přítomno. |
| `NoMatch` | V datovém proudu zvuku byla zjištěna řeč, ale nebyla spárována žádná slova z cílového jazyka. Obvykle znamená, že jazyk rozpoznávání je jiný jazyk než ten, který uživatel mluví. |
| `InitialSilenceTimeout` | Spuštění zvukového proudu obsahovalo pouze ticho a službě byl časový čas čekání na řeč. |
| `BabbleTimeout` | Spuštění zvukového proudu obsahovalo pouze šum a službě byl časový rozsah čekání na řeč. |
| `Error` | Služba rozpoznávání zjistila vnitřní chybu a nemohla pokračovat. Zkuste to znovu, pokud je to možné. |

> [!NOTE]
> Pokud se zvuk skládá pouze z vulgárních `profanity` výrazů a `remove`parametr dotazu je nastaven na , služba nevrátí výsledek řeči.

Formát `detailed` obsahuje stejná data `simple` jako formát, spolu s `NBest`, seznam alternativních interpretací stejného výsledku rozpoznávání. Tyto výsledky jsou seřazeny od nejpravděpodobnější nejméně pravděpodobné. První položka je stejná jako hlavní výsledek rozpoznávání.  Při použití `detailed` formátu, `DisplayText` je `Display` k dispozici jako `NBest` pro každý výsledek v seznamu.

Každý objekt `NBest` v seznamu obsahuje:

| Parametr | Popis |
|-----------|-------------|
| `Confidence` | Skóre důvěry vstupu z 0,0 (bez důvěry) na 1,0 (plná důvěra) |
| `Lexical` | Lexikální forma rozpoznaného textu: skutečná rozpoznaná slova. |
| `ITN` | Inverzní-text-normalizované ("kanonické") forma rozpoznaného textu, s telefonní miými čísly, čísly, zkratkami ("doctor smith" na "dr smith"), a další transformace použity. |
| `MaskedITN` | Formulář ITN s vulgárním maskováním použitý, pokud je požadováno. |
| `Display` | Zobrazovaná forma rozpoznaného textu s přidányu interpunkce a velkých písmen. Tento parametr je `DisplayText` stejný jako k dispozici, pokud je formát nastaven na hodnotu `simple`. |

## <a name="sample-responses"></a>Odpovědi na ukázky

Typická odpověď `simple` na uznání:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Typická odpověď `detailed` na uznání:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="next-steps"></a>Další kroky

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)

---
title: Referenční informace k rozhraní API pro převod řeči na text (REST) – služba Speech
titleSuffix: Azure Cognitive Services
description: Naučte se používat REST API převodu řeči na text. V tomto článku se dozvíte o autorizaci, možnosti dotazu, jak strukturovat žádost a přijetí odpovědi.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f617bed0d2d93d8c8586d5708e0e356934817f4a
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816630"
---
# <a name="speech-to-text-rest-api"></a>Rozhraní REST API pro převod řeči na text

Jako alternativu k [sadě Speech SDK](speech-sdk.md)vám služba rozpoznávání řeči umožňuje převod řeči na text pomocí REST API. Každý dostupný koncový bod je přidružen k oblasti. Vaše aplikace vyžaduje klíč předplatného pro koncový bod, který chcete použít.

Než začnete používat převod řeči na text, REST API pochopit:

* Požadavky, které používají REST API a přímo odesílají zvuk, můžou obsahovat až 60 sekund zvukového přenosu.
* Rozhraní REST API pro rozpoznávání řeči na text vrací pouze konečných výsledků. Nejsou k dispozici částečné výsledky.

Pokud je odeslání delšího zvukového požadavku nutné pro vaši aplikaci, zvažte použití [sady Speech SDK](speech-sdk.md) nebo souborového REST API, jako je například [Batch přepis](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Oblasti a koncových bodů

Tyto oblasti jsou podporovány pro určené k transkripci řeči na text pomocí rozhraní REST API. Ujistěte se, že vyberete koncového bodu, který odpovídá oblasti vašeho předplatného.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)] 

## <a name="query-parameters"></a>Parametry dotazu

Tyto parametry mohou být zahrnuty v řetězci dotazu požadavku REST.

| Parametr | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| `language` | Identifikuje mluvený jazyk, který je právě rozpoznán. Zobrazit [podporované jazyky](language-support.md#speech-to-text). | Požaduje se |
| `format` | Určuje formát výsledku. Platné hodnoty jsou `simple` a `detailed`. Jednoduché výsledky obsahují `RecognitionStatus`, `DisplayText`, `Offset`, a `Duration`. Podrobné odpovědi zahrnout více výsledků s jistotou hodnotami a čtyři různé reprezentace. Ve výchozím nastavení je `simple`. | Volitelné |
| `profanity` | Určuje způsob zpracování vulgárních výrazů v výsledky rozpoznávání. Přípustné hodnoty jsou `masked`, které nahradí vulgární znaky hvězdičkami, `removed`, které odstraní všechny vulgární výrazy z výsledku nebo `raw`, což zahrnuje vulgární výrazy ve výsledku. Ve výchozím nastavení je `masked`. | Volitelné |

## <a name="request-headers"></a>Hlavičky požadavku

Tato tabulka obsahuje povinné a nepovinné hlavičky pro žádosti o převod řeči na text.

|Hlavička| Popis | Povinné / volitelné |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Klíč předplatného služby Speech. | Buď toto záhlaví nebo `Authorization` je povinný. |
| `Authorization` | Autorizační token předcházet slovo `Bearer`. Další informace najdete v tématu [Ověřování](#authentication). | Buď toto záhlaví nebo `Ocp-Apim-Subscription-Key` je povinný. |
| `Content-type` | Popisuje formátu a kodek zadaná zvuková data. Platné hodnoty jsou `audio/wav; codecs=audio/pcm; samplerate=16000` a `audio/ogg; codecs=opus`. | Požaduje se |
| `Transfer-Encoding` | Určuje, že blokového zvukových dat je odesíláno, místo jednoho souboru. Tuto hlavičku používají pouze bloků zvuková data. | Volitelné |
| `Expect` | Pokud pomocí přenosu v bloku, pošlete `Expect: 100-continue`. Služba rozpoznávání řeči potvrdí počáteční požadavek a očekává další data.| Požadováno při odesílání bloku zvuková data. |
| `Accept` | Pokud je zadán, musí být `application/json`. Služba rozpoznávání řeči poskytuje výsledky ve formátu JSON. Některé architektury požadavků poskytují nekompatibilní výchozí hodnotu. Je vhodné vždy zahrnout `Accept`. | Volitelné, ale doporučené. |

## <a name="audio-formats"></a>Formáty zvuku

Zvuk se poslala v těle HTTP `POST` požadavku. Musí být v jednom z formátů, v této tabulce:

| Formát | Kodek | S přenosovou rychlostí | Vzorkovací frekvence |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bitů | 16 kHz, mono |
| OGG | DÍLE | 16 bitů | 16 kHz, mono |

>[!NOTE]
>Výše uvedené formáty jsou podporovány prostřednictvím REST API a WebSocket ve službě Speech. [Sadou SDK pro řeč](speech-sdk.md) aktuálně podporuje jenom WAV naformátuje PCM kodek.

## <a name="sample-request"></a>Ukázková žádost

Následující ukázka obsahuje název hostitele a požadované záhlaví. Je důležité si uvědomit, že služba také očekává, že zvukových dat, která není zahrnutá v této ukázce. Jak bylo zmíněno dříve, bloků se doporučuje, ale nevyžaduje.

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

Stavový kód HTTP pro každou odpověď indikuje úspěch nebo běžné chyby.

| Stavový kód HTTP | Popis | Možný důvod |
|------------------|-------------|-----------------|
| 100 | Pokračovat | Byla přijata v prvotní žádosti. Pokračujte v odesílání zbývajícími daty. (Používá se s bloku.) |
| 200 | OK | Žádost byla úspěšná. text odpovědi je objekt JSON. |
| 400 | Nesprávná žádost | Kód jazyka není k dispozici, nejedná se o podporovaný jazyk, neplatný zvukový soubor atd. |
| 401 | Neautorizováno | Klíč předplatného nebo autorizační token je neplatný. v zadané oblasti nebo neplatný koncový bod. |
| 403 | Forbidden | Chybí klíč předplatného nebo autorizační token. |

## <a name="chunked-transfer"></a>Bloku

Přenos v bloku dat (`Transfer-Encoding: chunked`) může pomáhat snižovat latenci při rozpoznávání. Umožňuje službě Speech Service zahájit zpracování zvukového souboru během přenosu. Rozhraní REST API neposkytuje výsledky částečné nebo dočasné.

Tento vzorový kód ukazuje, jak posílat zvuk v blocích. Zvukový soubor záhlaví by měl obsahovat pouze u prvního bloku. `request` připojen objekt HTTPWebRequest na příslušný koncový bod REST. `audioFile` je cesta k zvukový soubor na disku.

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
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

## <a name="response-parameters"></a>Parametry odpovědi

Výsledky jsou k dispozici jako dokumenty JSON. `simple` Formát obsahuje tato pole nejvyšší úrovně.

| Parametr | Popis  |
|-----------|--------------|
|`RecognitionStatus`|Stav, jako například `Success` pro úspěšné rozpoznávání. Najdete v následující tabulce.|
|`DisplayText`|Rozpoznaný text po použití velkých a malých písmen, normalizaci textu (konverze mluveného textu na kratších formulářích, například 200 pro "200" nebo "Dr. Smith" pro "Doctor Smith") a maskování vulgárních výrazů. K dispozici pouze v případě úspěchu.|
|`Offset`|Čas (v jednotkách 100 nanosekund), ve kterém začíná rozpoznané řeči v zvukový datový proud.|
|`Duration`|Doba (v jednotkách 100 nanosekund) rozpoznané řeči v zvukový datový proud.|

`RecognitionStatus` Pole může obsahovat tyto hodnoty:

| Stav | Popis |
|--------|-------------|
| `Success` | Rozpoznávání byla úspěšná a `DisplayText` pole je k dispozici. |
| `NoMatch` | V zvukový datový proud byl zjištěn řeči, ale žádná slova v cílovém jazyce se shoda našla. Obvykle znamená, že jazyk rozpoznávání je jiný jazyk než ty, které uživatel to mluví. |
| `InitialSilenceTimeout` | Začátek zvukový datový proud obsahovala pouze nečinnosti a službu vypršel časový limit čekání na rozpoznávání řeči. |
| `BabbleTimeout` | Začátek zvukový datový proud obsahovala pouze šum a službu vypršel časový limit čekání na řeč. |
| `Error` | Rozpoznávání služby došlo k interní chybě a nemohl pokračovat. Zkuste to znovu Pokud je to možné. |

> [!NOTE]
> Pokud se zvuk skládá pouze z vulgárních výrazů a `profanity` parametr dotazu je nastaven na `remove`, služba nevrací výsledek řeči.

Formát `detailed` obsahuje stejná data jako `simple` formátu společně se `NBest`m seznamem alternativních interpretů stejného výsledku rozpoznávání. Tyto výsledky jsou seřazené z nejpravděpodobnějšího nejnižší pravděpodobně. První položka je stejná jako u hlavního výsledku rozpoznávání.  Při použití `detailed` formátu `DisplayText` se poskytuje jako `Display` pro každého výsledku v `NBest` seznamu.

Každý objekt v `NBest` seznam obsahuje:

| Parametr | Popis |
|-----------|-------------|
| `Confidence` | Skóre spolehlivosti položky od 0.0 (bez obav) 1.0 (plnou důvěru) |
| `Lexical` | Lexikální formu textové rozpoznaných: vlastních slov rozpoznán. |
| `ITN` | Inverzní text normalized ("canonical") formu rozpoznaný text pomocí telefonního čísla, čísla, zkratky ("lékař smith" k "zotavení po havárii smith") a dalších transformací použít. |
| `MaskedITN` | Formulář není s vulgárních výrazů maskování použít, pokud o to požádá. |
| `Display` | Zobrazení formu rozpoznaný text s interpunkce a přidali malá a velká písmena. Tento parametr je stejný jako `DisplayText` při formátu je nastavena tak `simple`. |

## <a name="sample-responses"></a>Ukázkové odpovědi

Typická odpověď pro rozpoznávání `simple`:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Typická odpověď pro rozpoznávání `detailed`:

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

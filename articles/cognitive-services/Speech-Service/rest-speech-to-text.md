---
title: Referenční informace k rozhraní API pro převod řeči na text (REST) – služba Speech
titleSuffix: Azure Cognitive Services
description: Naučte se používat REST API převodu řeči na text. V tomto článku se seznámíte s možnostmi autorizace, možnostmi dotazů, postupy strukturování požadavků a přijetím odpovědi.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: yinhew
ms.openlocfilehash: 2f102199c14ba9611a83e3ed3b31ebcd189624d6
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82978616"
---
# <a name="speech-to-text-rest-api"></a>Rozhraní REST API pro převod řeči na text

Jako alternativu k [sadě Speech SDK](speech-sdk.md)vám služba rozpoznávání řeči umožňuje převod řeči na text pomocí REST API. Každý přístupný koncový bod je přidružen k oblasti. Vaše aplikace vyžaduje klíč předplatného pro koncový bod, který plánujete použít. REST API je velmi omezené a měl by se použít jenom v případě, že [sada Speech SDK](speech-sdk.md) nemůže.

Než začnete používat převod řeči na text, REST API pochopit:

* Požadavky, které používají REST API a přímo odesílají zvuk, můžou obsahovat až 60 sekund zvukového přenosu.
* REST API převodu řeči na text vrátí pouze konečné výsledky. Neposkytují se částečné výsledky.

Pokud je odeslání delšího zvukového požadavku nutné pro vaši aplikaci, zvažte použití [sady Speech SDK](speech-sdk.md) nebo souborového REST API, jako je například [Batch přepis](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Oblasti a koncové body

Koncový bod pro REST API má tento formát:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Nahraďte `<REGION_IDENTIFIER>` identifikátorem, který odpovídá oblasti vašeho předplatného z této tabulky:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Parametr Language se musí připojit k adrese URL, aby nedošlo k 4xx chybě HTTP. Například jazyk nastavený na AMERICKou angličtinu pomocí Západní USAho koncového bodu je: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

## <a name="query-parameters"></a>Parametry dotazů

Tyto parametry mohou být zahrnuty do řetězce dotazu žádosti REST.

| Parametr | Popis | Požadováno/volitelné |
|-----------|-------------|---------------------|
| `language` | Identifikuje mluvený jazyk, který se rozpozná. Viz [podporované jazyky](language-support.md#speech-to-text). | Požaduje se |
| `format` | Určuje formát výsledku. Přijaté hodnoty jsou `simple` a `detailed`. Jednoduché výsledky zahrnují `RecognitionStatus`, `DisplayText` `Offset`, a `Duration`. Podrobné odpovědi obsahují čtyři různé reprezentace zobrazovaného textu. Výchozí hodnota je `simple`. | Nepovinné |
| `profanity` | Určuje způsob zpracování vulgárních výrazů ve výsledcích rozpoznávání. Přijatelné jsou `masked`hodnoty, které nahradí vulgární znaky hvězdičkami, `removed`, které odstraní všechny vulgární výrazy z výsledku, nebo `raw`, které obsahují vulgární výrazy ve výsledku. Výchozí hodnota je `masked`. | Nepovinné |
| `pronunciationScoreParams` | Určuje parametry pro zobrazení skóre výslovnosti ve výsledcích rozpoznávání, které vyhodnocuje kvalitu výslovnosti vstupu řeči s indikátory přesnosti, Fluency, úplností atd. Tento parametr je JSON kódovaný v kódování Base64 obsahující několik podrobných parametrů. Jak tento parametr sestavit, najdete v tématu věnovaném [parametrům vyhodnocení výslovnosti](#pronunciation-assessment-parameters) . | Nepovinné |
| `cid` | Při použití [portálu Custom Speech](how-to-custom-speech.md) k vytváření vlastních modelů můžete na stránce **nasazení** použít vlastní modely přes **ID koncového bodu** . Jako argument pro parametr řetězce `cid` dotazu použijte **ID koncového bodu** . | Nepovinné |

## <a name="request-headers"></a>Hlavičky požadavku

Tato tabulka obsahuje seznam požadovaných a volitelných hlaviček pro žádosti o převod řeči na text.

|Hlavička| Popis | Požadováno/volitelné |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Klíč předplatného služby Speech. | Buď toto záhlaví, `Authorization` nebo je povinné. |
| `Authorization` | Autorizační token předchází slovu `Bearer`. Další informace najdete v tématu [Ověřování](#authentication). | Buď toto záhlaví, `Ocp-Apim-Subscription-Key` nebo je povinné. |
| `Content-type` | Popisuje formát a kodek poskytovaných zvukových dat. Přijaté hodnoty jsou `audio/wav; codecs=audio/pcm; samplerate=16000` a `audio/ogg; codecs=opus`. | Požaduje se |
| `Transfer-Encoding` | Určuje, zda jsou odesílána zvuková data v bloku, nikoli jeden soubor. Tuto hlavičku použijte pouze v případě, že jsou zvuková data v bloku. | Nepovinné |
| `Expect` | Pokud používáte přenos přes blok dat, `Expect: 100-continue`pošlete. Služba rozpoznávání řeči potvrdí počáteční požadavek a očekává další data.| Vyžaduje se, když se posílají zvuková data v bloku. |
| `Accept` | Je-li tento příkaz zadán `application/json`, musí být. Služba rozpoznávání řeči poskytuje výsledky ve formátu JSON. Některé architektury požadavků poskytují nekompatibilní výchozí hodnotu. Je vhodné vždy zahrnout `Accept`. | Volitelné, ale doporučené. |

## <a name="audio-formats"></a>Formáty zvuku

V těle požadavku HTTP `POST` se pošle zvuk. Musí být v jednom z formátů v této tabulce:

| Formát | Kodek | Přenosová rychlost | Vzorkovací frekvence  |
|--------|-------|----------|--------------|
| WAV    | PCM   | 256 KB/s | 16 kHz, mono |
| OGG    | OPUS  | 256 KPBS | 16 kHz, mono |

>[!NOTE]
>Výše uvedené formáty jsou podporovány prostřednictvím REST API a WebSocket ve službě Speech. [Sada Speech SDK](speech-sdk.md) aktuálně podporuje formát WAV pomocí kodeku PCM i [dalších formátů](how-to-use-codec-compressed-audio-input-streams.md).

## <a name="pronunciation-assessment-parameters"></a>Parametry vyhodnocení výslovnosti

Tato tabulka uvádí seznam požadovaných a volitelných parametrů pro posouzení výslovnosti.

| Parametr | Popis | Požadováno/volitelné |
|-----------|-------------|---------------------|
| ReferenceText | Text, proti kterému bude výslovnost vyhodnocena. | Požaduje se |
| GradingSystem | Systém bodů pro kalibraci skóre. Přijaté hodnoty jsou `FivePoint` a `HundredMark`. Výchozí hodnota je `FivePoint`. | Nepovinné |
| Členitost | Členitost vyhodnocení. Přípustné hodnoty jsou `Phoneme`, což zobrazuje skóre pro úplný text, na úrovni aplikace Word a foném `Word`, ve kterém se zobrazuje skóre pro úplný text a na úrovni `FullText`slova, ve kterém se zobrazuje skóre pouze na úrovni celého textu. Výchozí hodnota je `Phoneme`. | Nepovinné |
| Dimenze | Definuje výstupní kritéria. Akceptují se `Basic`hodnoty, které ukazují přesnost přesnosti, `Comprehensive` zobrazuje skóre dalších dimenzí (například skóre Fluency a skóre úplnosti na úrovni úplného textu, typ chyby na úrovni slova). Chcete-li zobrazit definice různých dimenzí skóre a typů chyb aplikace Word, zkontrolujte [parametry odpovědi](#response-parameters) . Výchozí hodnota je `Basic`. | Nepovinné |
| EnableMiscue | Povolí výpočet miscue. Když je tato možnost povolená, vyslovované slova se porovnají s referenčním textem a budou označená vynechání nebo vložení na základě porovnání. Přijaté hodnoty jsou `False` a `True`. Výchozí hodnota je `False`. | Nepovinné |
| ScenarioId | Identifikátor GUID označující systém přizpůsobeného bodu. | Nepovinné |

Níže je příklad kódu JSON obsahujícího parametry vyhodnocení výslovnosti:

```json
{
  "ReferenceText": "Good morning.",
  "GradingSystem": "HundredMark",
  "Granularity": "FullText",
  "Dimension": "Comprehensive"
}
```

Následující vzorový kód ukazuje, jak vytvořit parametry vyhodnocení výslovnosti do parametru dotazu adresy URL:

```csharp
var pronunciationScoreParamsJson = $"{{\"ReferenceText\":\"Good morning.\",\"GradingSystem\":\"HundredMark\",\"Granularity\":\"FullText\",\"Dimension\":\"Comprehensive\"}}";
var pronunciationScoreParamsBytes = Encoding.UTF8.GetBytes(pronunciationScoreParamsJson);
var pronunciationScoreParams = Convert.ToBase64String(pronunciationScoreParamsBytes);
```

## <a name="sample-request"></a>Ukázková žádost

Níže uvedená ukázka obsahuje název hostitele a požadované hlavičky. Je důležité si uvědomit, že služba také očekává zvuková data, která nejsou obsažena v této ukázce. Jak už bylo zmíněno dříve, doporučuje se používat bloky dat, ale nevyžadují se.

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
| `100` | Pokračovat | Byl přijat počáteční požadavek. Pokračujte v posílání zbývajících dat. (Používá se s přenosem v bloku) |
| `200` | OK | Požadavek byl úspěšný. tělo odpovědi je objekt JSON. |
| `400` | Chybný požadavek | Kód jazyka není k dispozici, nejedná se o podporovaný jazyk, neplatný zvukový soubor atd. |
| `401` | Neautorizováno | Klíč předplatného nebo autorizační token není v zadané oblasti platný, nebo je neplatný koncový bod. |
| `403` | Forbidden | Chybí klíč předplatného nebo autorizační token. |

## <a name="chunked-transfer"></a>Přenos v bloku

Přenos v bloku (`Transfer-Encoding: chunked`) může přispět ke snížení latence při rozpoznávání. Umožňuje službě Speech Service zahájit zpracování zvukového souboru během přenosu. REST API neposkytuje částečné nebo dočasné výsledky.

Tato ukázka kódu ukazuje, jak odeslat zvuk v blocích. Pouze první blok by měl obsahovat hlavičku zvukového souboru. `request`je `HttpWebRequest` objekt připojený k příslušnému koncovému bodu REST. `audioFile`je cesta ke zvukovému souboru na disku.

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

## <a name="response-parameters"></a>Parametry odpovědi

Výsledky se poskytují jako JSON. Tento `simple` formát zahrnuje tato pole nejvyšší úrovně.

| Parametr | Popis  |
|-----------|--------------|
|`RecognitionStatus`|Stav, například `Success` pro úspěšné rozpoznávání. Viz další tabulka.|
|`DisplayText`|Rozpoznaný text po použití velkých a malých písmen, normalizaci textu (konverze mluveného textu na kratších formulářích, například 200 pro "200" nebo "Dr. Smith" pro "Doctor Smith") a maskování vulgárních výrazů. Prezentovat pouze při úspěchu.|
|`Offset`|Čas (v jednotkách 100 – nanosekund), na kterém se ve zvukovém streamu zahájí rozpoznané rozpoznávání řeči.|
|`Duration`|Doba (v jednotkách 100 – nanosekund) rozpoznaného rozpoznávání řeči ve zvukovém datovém proudu.|

`RecognitionStatus` Pole může obsahovat tyto hodnoty:

| Status | Popis |
|--------|-------------|
| `Success` | Rozpoznávání bylo úspěšné a `DisplayText` pole je k dispozici. |
| `NoMatch` | Ve zvukovém datovém proudu byl zjištěn řeč, ale nebyla nalezena žádná slova z cílového jazyka. Obvykle znamená, že jazyk rozpoznávání je jiný jazyk než ten, který uživatel mluví. |
| `InitialSilenceTimeout` | Začátek zvukového datového proudu obsahoval pouze tiché a při čekání na řeč vypršel časový limit služby. |
| `BabbleTimeout` | Začátek zvukového datového proudu obsahoval pouze šum a při čekání na řeč vypršel časový limit služby. |
| `Error` | Služba pro rozpoznávání zjistila vnitřní chybu a nemůže pokračovat. Zkuste to znovu, pokud je to možné. |

> [!NOTE]
> Pokud zvuk obsahuje pouze vulgární výrazy a parametr `profanity` dotazu je nastaven na `remove`hodnotu, služba nevrátí výsledek řeči.

`detailed` Formát obsahuje další formy rozpoznaných výsledků.
Při použití `detailed` formátu `DisplayText` je k dispozici jako `Display` u každého výsledku v `NBest` seznamu.

Objekt v `NBest` seznamu může zahrnovat:

| Parametr | Popis |
|-----------|-------------|
| `Confidence` | Hodnocení spolehlivosti záznamu z 0,0 (bez spolehlivosti) do 1,0 (plná jistota) |
| `Lexical` | Lexikální forma rozpoznaného textu: skutečná slova byla rozpoznána. |
| `ITN` | Formát textu, který je v normalizovaném textu (kanonický), s telefonními čísly, čísly, zkratkami ("lékař Novák" na "Dr Smith") a jinými použitými transformacemi. |
| `MaskedITN` | Formulář vytvořené s použitým maskou vulgárních výrazů, pokud je to požadováno. |
| `Display` | Formulář pro zobrazení rozpoznaného textu s přidanými interpunkcí a velkými písmeny Tento parametr je stejný, jako `DisplayText` při nastavení Format na `simple`. |
| `AccuracyScore` | Skóre udávající přesnost výslovnosti daného řeči. |
| `FluencyScore` | Skóre udávající Fluency daného řeči. |
| `CompletenessScore` | Skóre udávající úplnost daného rozpoznávání řeči výpočtem poměru slov v celém vstupu. |
| `PronScore` | Celkové skóre udávající kvalitu výslovnosti daného řeči. Počítá se z `AccuracyScore` `FluencyScore` a `CompletenessScore` s váhou. |
| `ErrorType` | Tato hodnota označuje, zda je slovo vynecháno, vloženo nebo špatně vyslovované, `ReferenceText`ve srovnání s. Možné hodnoty jsou `None` (to znamená, `Omission` `Insertion` že toto slovo neobsahuje žádnou chybu) `Mispronunciation`, a. |

## <a name="sample-responses"></a>Ukázkové odpovědi

Typická odpověď pro `simple` rozpoznávání:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Typická odpověď pro `detailed` rozpoznávání:

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
      }
  ]
}
```

Typická odpověď pro rozpoznávání s hodnocením výslovnosti:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronScore" : 84.4,
        "AccuracyScore" : 100.0,
        "FluencyScore" : 74.0,
        "CompletenessScore" : 100.0,
        "Words": [
            {
              "Word" : "Good",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 500000,
              "Duration" : 2700000
            },
            {
              "Word" : "morning",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 5300000,
              "Duration" : 900000
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Další kroky

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)

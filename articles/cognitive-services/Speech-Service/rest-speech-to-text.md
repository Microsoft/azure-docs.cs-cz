---
title: Referenční informace k rozhraní API pro převod řeči na text (REST) – služba Speech
titleSuffix: Azure Cognitive Services
description: Naučte se používat REST API převodu řeči na text. V tomto článku se seznámíte s možnostmi autorizace, možnostmi dotazů, postupy strukturování požadavků a přijetím odpovědi.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 76b4a749bff94cbf218d3cd3c7d132eef119b7ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606901"
---
# <a name="speech-to-text-rest-api"></a>Rozhraní REST API pro převod řeči na text

Převod řeči na text má dvě různá rozhraní REST API. Každé rozhraní API slouží ke speciálnímu účelu a používá jiné sady koncových bodů.

Rozhraní REST API pro text jsou:
- [Převod řeči na text REST API v 3.0](#speech-to-text-rest-api-v30) se používá ke [dávkovému přepisu](batch-transcription.md) a [Custom Speech](custom-speech-overview.md). v 3.0 je [následníkem v 2.0](./migrate-v2-to-v3.md).
- [Převod řeči na text REST API pro krátký zvuk](#speech-to-text-rest-api-for-short-audio) se používá pro online přepis jako alternativu k [sadě Speech SDK](speech-sdk.md). Požadavky, které používají toto rozhraní API, můžou přenášet až 60 sekund zvuk na jednu žádost. 

## <a name="speech-to-text-rest-api-v30"></a>Převod řeči na text REST API v 3.0

Převod řeči na text REST API v 3.0 se používá ke [dávkovému přepisu](batch-transcription.md) a [Custom Speech](custom-speech-overview.md). Pokud potřebujete komunikovat s online přepisem přes REST, používejte [pro krátké zvukové REST API převod řeči na text](#speech-to-text-rest-api-for-short-audio).

REST API v 3.0 použijte k těmto akcím:
- Zkopírujte modely do jiných předplatných, pokud chcete, aby měli kolegové přístup k modelu, který jste vytvořili, nebo v případech, kdy chcete model nasadit do více než jedné oblasti.
- Přepisovat data z kontejneru (hromadný přepis) a poskytněte několik adres URL zvukového souboru.
- Nahrávání dat z Azure Storage účtů pomocí identifikátoru URI SAS
- Získat protokoly na koncový bod, pokud se pro tento koncový bod požadovaly protokoly
- Vyžádejte si manifest modelů, které vytvoříte, pro účely nastavení místních kontejnerů.

REST API v 3.0 zahrnuje tyto funkce:
- **Oznámení – Webhooky**– všechny spuštěné procesy služby teď podporují oznámení Webhooku. REST API v 3.0 poskytuje volání, která umožňují registrovat Webhooky, ve kterých se odesílají oznámení.
- **Aktualizace modelů za koncovými body** 
- **Přizpůsobení modelu s více datovými sadami**– přizpůsobení modelu pomocí více kombinací dat akustického, jazyka a výslovnosti pro více datových sad
- **Přineste si vlastní úložiště**– používejte vlastní účty úložiště pro protokoly, přepisované soubory a další data.

V [tomto článku](batch-transcription.md)najdete příklady použití REST API v 3.0 s dávkovým přepisem.

Pokud používáte převod řeči na text REST API v 2.0, přečtěte si téma jak můžete v [této příručce](./migrate-v2-to-v3.md)migrovat na verzi 3.0.

[Tady](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)najdete odkaz na celý převod řeči na text REST API v 3.0.

## <a name="speech-to-text-rest-api-for-short-audio"></a>REST API řeči na text pro krátký zvuk

Jako alternativu k [sadě Speech SDK](speech-sdk.md)vám služba rozpoznávání řeči umožňuje převod řeči na text pomocí REST API.
REST API pro krátký zvuk je velmi omezené a měl by se použít jenom v případě, že [sada Speech SDK](speech-sdk.md) není.

Před použitím REST API řeči na text pro krátký zvuk zvažte následující:

* Požadavky, které používají REST API pro krátký zvuk a přímý přenos zvuku, můžou obsahovat až 60 sekund zvukového přenosu.
* REST API řeči na text pro krátký zvuk vrátí pouze konečné výsledky. Neposkytují se částečné výsledky.

Pokud je odeslání delšího zvukového požadavku nutné pro vaši aplikaci, zvažte použití [sady Speech SDK](speech-sdk.md) nebo [řeči-to-text REST API v 3.0](#speech-to-text-rest-api-v30).

> [!TIP]
> V [tomto článku](sovereign-clouds.md) najdete Azure Government a koncových bodů Azure Čína.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

### <a name="regions-and-endpoints"></a>Oblasti a koncové body

Koncový bod pro REST API pro krátký zvuk má tento formát:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Nahraďte `<REGION_IDENTIFIER>` identifikátorem, který odpovídá oblasti vašeho předplatného z této tabulky:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Parametr Language se musí připojit k adrese URL, aby nedošlo k 4xx chybě HTTP. Například jazyk nastavený na AMERICKou angličtinu pomocí Západní USAho koncového bodu je: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

### <a name="query-parameters"></a>Parametry dotazů

Tyto parametry mohou být zahrnuty do řetězce dotazu žádosti REST.

| Parametr | Popis | Požadováno/volitelné |
|-----------|-------------|---------------------|
| `language` | Identifikuje mluvený jazyk, který se rozpozná. Viz [podporované jazyky](language-support.md#speech-to-text). | Vyžadováno |
| `format` | Určuje formát výsledku. Přijaté hodnoty jsou `simple` a `detailed` . Jednoduché výsledky zahrnují `RecognitionStatus` , `DisplayText` , `Offset` a `Duration` . Podrobné odpovědi obsahují čtyři různé reprezentace zobrazovaného textu. Výchozí hodnota je `simple`. | Volitelné |
| `profanity` | Určuje způsob zpracování vulgárních výrazů ve výsledcích rozpoznávání. Přijatelné jsou hodnoty `masked` , které nahradí vulgární znaky hvězdičkami, `removed` , které odstraní všechny vulgární výrazy z výsledku, nebo `raw` , které obsahují vulgární výrazy ve výsledku. Výchozí hodnota je `masked`. | Volitelné |
| `cid` | Při použití [portálu Custom Speech](./custom-speech-overview.md) k vytváření vlastních modelů můžete na stránce **nasazení** použít vlastní modely přes **ID koncového bodu** . Jako argument pro parametr řetězce dotazu použijte **ID koncového bodu** `cid` . | Volitelné |

### <a name="request-headers"></a>Hlavičky požadavku

Tato tabulka obsahuje seznam požadovaných a volitelných hlaviček pro žádosti o převod řeči na text.

|Hlavička| Description | Požadováno/volitelné |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Klíč předplatného služby Speech. | Buď toto záhlaví, nebo `Authorization` je povinné. |
| `Authorization` | Autorizační token předchází slovu `Bearer` . Další informace najdete v tématu [Ověřování](#authentication). | Buď toto záhlaví, nebo `Ocp-Apim-Subscription-Key` je povinné. |
| `Pronunciation-Assessment` | Určuje parametry pro zobrazení skóre výslovnosti ve výsledcích rozpoznávání, které vyhodnocuje kvalitu výslovnosti vstupu řeči s indikátory přesnosti, Fluency, úplností atd. Tento parametr je JSON kódovaný v kódování Base64 obsahující několik podrobných parametrů. Informace o tom, jak vytvořit tuto hlavičku, najdete v tématu [parametry vyhodnocení výslovnosti](#pronunciation-assessment-parameters) . | Volitelné |
| `Content-type` | Popisuje formát a kodek poskytovaných zvukových dat. Přijaté hodnoty jsou `audio/wav; codecs=audio/pcm; samplerate=16000` a `audio/ogg; codecs=opus` . | Vyžadováno |
| `Transfer-Encoding` | Určuje, zda jsou odesílána zvuková data v bloku, nikoli jeden soubor. Tuto hlavičku použijte pouze v případě, že jsou zvuková data v bloku. | Volitelné |
| `Expect` | Pokud používáte přenos přes blok dat, pošlete `Expect: 100-continue` . Služba rozpoznávání řeči potvrdí počáteční požadavek a očekává další data.| Vyžaduje se, když se posílají zvuková data v bloku. |
| `Accept` | Je-li tento příkaz zadán, musí být `application/json` . Služba rozpoznávání řeči poskytuje výsledky ve formátu JSON. Některé architektury požadavků poskytují nekompatibilní výchozí hodnotu. Je vhodné vždy zahrnout `Accept` . | Volitelné, ale doporučené. |

### <a name="audio-formats"></a>Formáty zvuku

V těle požadavku HTTP se pošle zvuk `POST` . Musí být v jednom z formátů v této tabulce:

| Formát | Kodek | Přenosová rychlost | Vzorkovací frekvence  |
|--------|-------|----------|--------------|
| WAV    | PCM   | 256 KB/s | 16 kHz, mono |
| OGG    | OPUS  | 256 KPBS | 16 kHz, mono |

>[!NOTE]
>Výše uvedené formáty jsou podporovány prostřednictvím REST API pro krátké zvukové a WebSocket ve službě Speech. [Sada Speech SDK](speech-sdk.md) aktuálně podporuje formát WAV pomocí kodeku PCM i [dalších formátů](how-to-use-codec-compressed-audio-input-streams.md).

### <a name="pronunciation-assessment-parameters"></a>Parametry vyhodnocení výslovnosti

Tato tabulka uvádí seznam požadovaných a volitelných parametrů pro posouzení výslovnosti.

| Parametr | Popis | Povinné? |
|-----------|-------------|---------------------|
| ReferenceText | Text, proti kterému bude výslovnost vyhodnocena. | Vyžadováno |
| GradingSystem | Systém bodů pro kalibraci skóre. `FivePoint`Systém poskytne skóre 0-5 s plovoucí desetinnou čárkou a `HundredMark` poskytne 0-100 skóre s plovoucí desetinnou čárkou. Výchozí: `FivePoint`. | Volitelné |
| Členitost | Členitost vyhodnocení. Přípustné hodnoty jsou, což zobrazuje skóre pro úplný text, na úrovni aplikace Word a foném, ve kterém se zobrazuje skóre `Phoneme` `Word` pro úplný text a na úrovni slova, ve `FullText` kterém se zobrazuje skóre pouze na úrovni celého textu. Výchozí hodnota je `Phoneme`. | Volitelné |
| Dimenze | Definuje výstupní kritéria. Akceptují se hodnoty `Basic` , které ukazují přesnost přesnosti, `Comprehensive` zobrazuje skóre dalších dimenzí (například skóre Fluency a skóre úplnosti na úrovni úplného textu, typ chyby na úrovni slova). Chcete-li zobrazit definice různých dimenzí skóre a typů chyb aplikace Word, zkontrolujte [parametry odpovědi](#response-parameters) . Výchozí hodnota je `Basic`. | Volitelné |
| EnableMiscue | Povolí výpočet miscue. Když je tato možnost povolená, vyslovované slova se porovnají s referenčním textem a budou označená vynechání nebo vložení na základě porovnání. Přijaté hodnoty jsou `False` a `True` . Výchozí hodnota je `False`. | Volitelné |
| ScenarioId | Identifikátor GUID označující systém přizpůsobeného bodu. | Volitelné |

Níže je příklad kódu JSON obsahujícího parametry vyhodnocení výslovnosti:

```json
{
  "ReferenceText": "Good morning.",
  "GradingSystem": "HundredMark",
  "Granularity": "FullText",
  "Dimension": "Comprehensive"
}
```

Následující vzorový kód ukazuje, jak vytvořit parametry vyhodnocení výslovnosti do `Pronunciation-Assessment` hlavičky:

```csharp
var pronAssessmentParamsJson = $"{{\"ReferenceText\":\"Good morning.\",\"GradingSystem\":\"HundredMark\",\"Granularity\":\"FullText\",\"Dimension\":\"Comprehensive\"}}";
var pronAssessmentParamsBytes = Encoding.UTF8.GetBytes(pronAssessmentParamsJson);
var pronAssessmentHeader = Convert.ToBase64String(pronAssessmentParamsBytes);
```

Při odesílání zvukových dat důrazně doporučujeme nahrávání streamování (blokování), což může výrazně snížit latenci. Jak povolit streamování, najdete [v tématu vzorový kód v různých programovacích jazycích](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment) .

>[!NOTE]
>Funkce hodnocení výslovnosti je aktuálně dostupná jenom pro `en-US` jazyk.

### <a name="sample-request"></a>Ukázková žádost

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

Pokud chcete povolit posouzení výslovnosti, můžete přidat pod záhlaví. Informace o tom, jak vytvořit tuto hlavičku, najdete v tématu [parametry vyhodnocení výslovnosti](#pronunciation-assessment-parameters) .

```HTTP
Pronunciation-Assessment: eyJSZWZlcm...
```

### <a name="http-status-codes"></a>Stavové kódy HTTP

Stavový kód HTTP pro každou odpověď indikuje úspěch nebo běžné chyby.

| Stavový kód HTTP | Popis | Možný důvod |
|------------------|-------------|-----------------|
| `100` | Pokračovat | Byl přijat počáteční požadavek. Pokračujte v posílání zbývajících dat. (Používá se s přenosem v bloku) |
| `200` | OK | Požadavek byl úspěšný. tělo odpovědi je objekt JSON. |
| `400` | Chybný požadavek | Kód jazyka není k dispozici, nejedná se o podporovaný jazyk, neplatný zvukový soubor atd. |
| `401` | Neautorizováno | Klíč předplatného nebo autorizační token není v zadané oblasti platný, nebo je neplatný koncový bod. |
| `403` | Forbidden | Chybí klíč předplatného nebo autorizační token. |

### <a name="chunked-transfer"></a>Přenos v bloku

Přenos v bloku ( `Transfer-Encoding: chunked` ) může přispět ke snížení latence při rozpoznávání. Umožňuje službě Speech Service zahájit zpracování zvukového souboru během přenosu. REST API pro krátký zvuk neposkytuje částečné nebo dočasné výsledky.

Tato ukázka kódu ukazuje, jak odeslat zvuk v blocích. Pouze první blok by měl obsahovat hlavičku zvukového souboru. `request` je `HttpWebRequest` objekt připojený k příslušnému koncovému bodu REST. `audioFile` je cesta ke zvukovému souboru na disku.

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

### <a name="response-parameters"></a>Parametry odpovědi

Výsledky se poskytují jako JSON. Tento `simple` Formát zahrnuje tato pole nejvyšší úrovně.

| Parametr | Popis  |
|-----------|--------------|
|`RecognitionStatus`|Stav, například `Success` pro úspěšné rozpoznávání. Viz další tabulka.|
|`DisplayText`|Rozpoznaný text po použití velkých a malých písmen, normalizaci textu (konverze mluveného textu na kratších formulářích, například 200 pro "200" nebo "Dr. Smith" pro "Doctor Smith") a maskování vulgárních výrazů. Prezentovat pouze při úspěchu.|
|`Offset`|Čas (v jednotkách 100 – nanosekund), na kterém se ve zvukovém streamu zahájí rozpoznané rozpoznávání řeči.|
|`Duration`|Doba (v jednotkách 100 – nanosekund) rozpoznaného rozpoznávání řeči ve zvukovém datovém proudu.|

`RecognitionStatus`Pole může obsahovat tyto hodnoty:

| Status | Popis |
|--------|-------------|
| `Success` | Rozpoznávání bylo úspěšné a `DisplayText` pole je k dispozici. |
| `NoMatch` | Ve zvukovém datovém proudu byl zjištěn řeč, ale nebyla nalezena žádná slova z cílového jazyka. Obvykle znamená, že jazyk rozpoznávání je jiný jazyk než ten, který uživatel mluví. |
| `InitialSilenceTimeout` | Začátek zvukového datového proudu obsahoval pouze tiché a při čekání na řeč vypršel časový limit služby. |
| `BabbleTimeout` | Začátek zvukového datového proudu obsahoval pouze šum a při čekání na řeč vypršel časový limit služby. |
| `Error` | Služba pro rozpoznávání zjistila vnitřní chybu a nemůže pokračovat. Zkuste to znovu, pokud je to možné. |

> [!NOTE]
> Pokud zvuk obsahuje pouze vulgární výrazy a `profanity` parametr dotazu je nastaven na hodnotu `remove` , služba nevrátí výsledek řeči.

`detailed`Formát obsahuje další formy rozpoznaných výsledků.
Při použití `detailed` formátu `DisplayText` je k dispozici jako `Display` u každého výsledku v `NBest` seznamu.

Objekt v `NBest` seznamu může zahrnovat:

| Parametr | Popis |
|-----------|-------------|
| `Confidence` | Hodnocení spolehlivosti záznamu z 0,0 (bez spolehlivosti) do 1,0 (plná jistota) |
| `Lexical` | Lexikální forma rozpoznaného textu: skutečná slova byla rozpoznána. |
| `ITN` | Formát textu, který je v normalizovaném textu (kanonický), s telefonními čísly, čísly, zkratkami ("lékař Novák" na "Dr Smith") a jinými použitými transformacemi. |
| `MaskedITN` | Formulář vytvořené s použitým maskou vulgárních výrazů, pokud je to požadováno. |
| `Display` | Formulář pro zobrazení rozpoznaného textu s přidanými interpunkcí a velkými písmeny Tento parametr je stejný, jako `DisplayText` při nastavení Format na `simple` . |
| `AccuracyScore` | Přesnost řeči v výslovnosti. Přesnost určuje, jak úzce se fonémy shoduje s výslovností nativního mluvčího. Skóre přesnosti aplikace Word a úplného textu je agregované ze foném skóre úrovně. |
| `FluencyScore` | Fluency zadaného řeči. Fluency označuje, jak blízkoně řeč odpovídá použití tichého zalomení mezi slovy v nativním mluvčím. |
| `CompletenessScore` | Úplnost rozpoznávání řeči určené výpočtem poměru slov, která jsou vyhodnocena jako odkaz na textové zadání. |
| `PronScore` | Celkové skóre označující kvalitu výslovnosti daného řeči. To je agregované z `AccuracyScore` `FluencyScore` a `CompletenessScore` s váhou. |
| `ErrorType` | Tato hodnota označuje, zda je slovo vynecháno, vloženo nebo špatně vyslovované, ve srovnání s `ReferenceText` . Možné hodnoty jsou `None` (to znamená, že toto slovo neobsahuje žádnou chybu), `Omission` `Insertion` a `Mispronunciation` . |

### <a name="sample-responses"></a>Ukázkové odpovědi

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

- [Vytvoření bezplatného účtu Azure](https://azure.microsoft.com/free/cognitive-services/)
- [Přizpůsobení akustických modelů](./how-to-custom-speech-train-model.md)
- [Přizpůsobení jazykových modelů](./how-to-custom-speech-train-model.md)
- [Seznámení se službou Batch – přepis](batch-transcription.md)


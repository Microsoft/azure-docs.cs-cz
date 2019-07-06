---
title: Převod řeči na text reference k rozhraní API (REST) – hlasové služby
titleSuffix: Azure Cognitive Services
description: Další informace o použití rozhraní REST API pro rozpoznávání řeči na text. V tomto článku se dozvíte o autorizaci, možnosti dotazu, jak strukturovat žádost a přijetí odpovědi.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 9d967fa4d5ba54e4470dadc5e797067454e1769a
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606353"
---
# <a name="speech-to-text-rest-api"></a>Rozhraní REST Speech to text API

Jako alternativu k [sadou SDK pro řeč](speech-sdk.md), hlasové služby umožňují převod řeči na text pomocí rozhraní REST API. Každý dostupný koncový bod je přidružen k oblasti. Vaše aplikace vyžaduje klíč předplatného pro koncový bod, který chcete použít.

Než začnete používat rozhraní REST API pro rozpoznávání řeči na text, pochopení:
* 10 sekund nahraného zvukového může obsahovat pouze požadavky, které využívají rozhraní REST API.
* Rozhraní REST API pro rozpoznávání řeči na text vrací pouze konečných výsledků. Nejsou k dispozici částečné výsledky.

Pokud odesílání delší zvuk představuje požadavek pro aplikace, zvažte použití [sadou SDK pro řeč](speech-sdk.md) nebo [batch určené k transkripci](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Oblasti a koncových bodů

Tyto oblasti jsou podporovány pro určené k transkripci řeči na text pomocí rozhraní REST API. Ujistěte se, že vyberete koncového bodu, který odpovídá oblasti vašeho předplatného.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

## <a name="query-parameters"></a>Parametry dotazu

Tyto parametry mohou být zahrnuty v řetězci dotazu požadavku REST.

| Parametr | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| `language` | Identifikuje mluvený jazyk, který je právě rozpoznán. Zobrazit [podporované jazyky](language-support.md#speech-to-text). | Požaduje se |
| `format` | Určuje formát výsledku. Platné hodnoty jsou `simple` a `detailed`. Jednoduché výsledky obsahují `RecognitionStatus`, `DisplayText`, `Offset`, a `Duration`. Podrobné odpovědi zahrnout více výsledků s jistotou hodnotami a čtyři různé reprezentace. Ve výchozím nastavení je `simple`. | Nepovinné |
| `profanity` | Určuje způsob zpracování vulgárních výrazů v výsledky rozpoznávání. Platné hodnoty jsou `masked`, hvězdičky, která nahradí vulgárních výrazů `removed`, výsledek, který odebrání všech vulgárních výrazů nebo `raw`, což zahrnuje vulgární výrazy ve výsledku. Ve výchozím nastavení je `masked`. | Nepovinné |

## <a name="request-headers"></a>Hlavičky požadavku

Tato tabulka obsahuje povinné a nepovinné hlavičky pro žádosti o převod řeči na text.

|Hlavička| Popis | Povinné / volitelné |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Váš klíč předplatného hlasové služby. | Buď toto záhlaví nebo `Authorization` je povinný. |
| `Authorization` | Autorizační token předcházet slovo `Bearer`. Další informace najdete v tématu [Ověřování](#authentication). | Buď toto záhlaví nebo `Ocp-Apim-Subscription-Key` je povinný. |
| `Content-type` | Popisuje formátu a kodek zadaná zvuková data. Platné hodnoty jsou `audio/wav; codecs=audio/pcm; samplerate=16000` a `audio/ogg; codecs=opus`. | Požaduje se |
| `Transfer-Encoding` | Určuje, že blokového zvukových dat je odesíláno, místo jednoho souboru. Tuto hlavičku používají pouze bloků zvuková data. | Nepovinné |
| `Expect` | Pokud pomocí přenosu v bloku, pošlete `Expect: 100-continue`. Hlasové služby potvrdí v prvotní žádosti a čeká další data.| Požadováno při odesílání bloku zvuková data. |
| `Accept` | Pokud je zadán, musí být `application/json`. Hlasové služby poskytují výsledky ve formátu JSON. Některé webové žádosti platformy poskytují nekompatibilní výchozí hodnotu, pokud není zadán, takže je dobrým zvykem vždy zahrnovat `Accept`. | Volitelné, ale doporučené. |

## <a name="audio-formats"></a>Formáty zvuku

Zvuk se poslala v těle HTTP `POST` požadavku. Musí být v jednom z formátů, v této tabulce:

| Formát | Kodek | S přenosovou rychlostí | Vzorkovací frekvence |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bitů | 16 kHz, mono |
| OGG | DÍLE | 16 bitů | 16 kHz, mono |

>[!NOTE]
>Výše uvedené formáty jsou podporované prostřednictvím rozhraní REST API a protokolu WebSocket v hlasových služeb. [Sadou SDK pro řeč](speech-sdk.md) aktuálně podporuje jenom WAV naformátuje PCM kodek.

## <a name="sample-request"></a>Ukázková žádost

Toto je typická požadavku HTTP. Následující ukázka obsahuje název hostitele a požadované záhlaví. Je důležité si uvědomit, že služba také očekává, že zvukových dat, která není zahrnutá v této ukázce. Jak bylo zmíněno dříve, bloků se doporučuje, ale nevyžaduje.

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
| 400 | Nesprávná žádost | Kód jazyka není k dispozici nebo není podporovaný jazyk; Neplatný zvukový soubor. |
| 401 | Neautorizováno | Klíč předplatného nebo autorizační token je neplatný. v zadané oblasti nebo neplatný koncový bod. |
| 403 | Zakázáno | Chybí klíč předplatného nebo autorizační token. |

## <a name="chunked-transfer"></a>Bloku

Přenos rozdělený do bloků dat (`Transfer-Encoding: chunked`) může pomoct snížit latenci rozpoznávání, protože povoluje hlasové služby začala zpracovat zvukový soubor při přenosu je zašifrované. Rozhraní REST API neposkytuje výsledky částečné nebo dočasné. Tato možnost je určena výhradně pro zvýšení rychlosti odezvy.

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
|`DisplayText`|Rozpoznaný text po malá a velká písmena, interpunkční znaménka, normalizace inverzní text (převod textu na mluvené slovo na kratší formulářů, jako je například 200 pro "dvě stě" nebo "zotavení po havárii. Smith""lékař Smith") a maskování vulgárních výrazů. K dispozici pouze v případě úspěchu.|
|`Offset`|Čas (v jednotkách 100 nanosekund), ve kterém začíná rozpoznané řeči v zvukový datový proud.|
|`Duration`|Doba (v jednotkách 100 nanosekund) rozpoznané řeči v zvukový datový proud.|

`RecognitionStatus` Pole může obsahovat tyto hodnoty:

| Status | Popis |
|--------|-------------|
| `Success` | Rozpoznávání byla úspěšná a `DisplayText` pole je k dispozici. |
| `NoMatch` | V zvukový datový proud byl zjištěn řeči, ale žádná slova v cílovém jazyce se shoda našla. Obvykle znamená, že jazyk rozpoznávání je jiný jazyk než ty, které uživatel to mluví. |
| `InitialSilenceTimeout` | Začátek zvukový datový proud obsahovala pouze nečinnosti a službu vypršel časový limit čekání na rozpoznávání řeči. |
| `BabbleTimeout` | Začátek zvukový datový proud obsahovala pouze šum a službu vypršel časový limit čekání na řeč. |
| `Error` | Rozpoznávání služby došlo k interní chybě a nemohl pokračovat. Zkuste to znovu Pokud je to možné. |

> [!NOTE]
> Pokud se zvuk skládá pouze z vulgárních výrazů a `profanity` parametr dotazu je nastaven na `remove`, služba nevrací výsledek řeči.

`detailed` Formát obsahuje stejná data jako `simple` formátovat, spolu s `NBest`, seznam alternativních interpretace stejný výsledek rozpoznání. Tyto výsledky jsou řazeny od nejpodezřelejších po pravděpodobně k nejméně pravděpodobně. První položka je stejný jako výsledek hlavní rozpoznání.  Při použití `detailed` formátu `DisplayText` se poskytuje jako `Display` pro každého výsledku v `NBest` seznamu.

Každý objekt v `NBest` seznam obsahuje:

| Parametr | Popis |
|-----------|-------------|
| `Confidence` | Skóre spolehlivosti položky od 0.0 (bez obav) 1.0 (plnou důvěru) |
| `Lexical` | Lexikální formu textové rozpoznaných: vlastních slov rozpoznán. |
| `ITN` | Inverzní text normalized ("canonical") formu rozpoznaný text pomocí telefonního čísla, čísla, zkratky ("lékař smith" k "zotavení po havárii smith") a dalších transformací použít. |
| `MaskedITN` | Formulář není s vulgárních výrazů maskování použít, pokud o to požádá. |
| `Display` | Zobrazení formu rozpoznaný text s interpunkce a přidali malá a velká písmena. Tento parametr je stejný jako `DisplayText` při formátu je nastavena tak `simple`. |

## <a name="sample-responses"></a>Ukázkové odpovědi

Toto je typická odpověď pro `simple` rozpoznávání.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Toto je typická odpověď pro `detailed` rozpoznávání.

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

## <a name="next-steps"></a>Další postup

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)

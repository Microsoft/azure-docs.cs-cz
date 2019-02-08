---
title: Rozpoznávání řeči služby rozhraní REST API – hlasové služby
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak používat rozhraní REST API řeči na text a převod textu na řeč. V tomto článku se dozvíte o autorizaci, možnosti dotazu, jak strukturovat žádost a přijetí odpovědi.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: c1693c5e04c33da3f21847aada0adfe1b66c4b52
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878064"
---
# <a name="speech-service-rest-apis"></a>Speech Service REST API

Jako alternativu k [sadou SDK pro řeč](speech-sdk.md), služba Speech umožňuje převod řeči na text a převod textu na řeč sadu rozhraní REST API. Každý dostupný koncový bod je přidružen k oblasti. Vaše aplikace vyžaduje klíč předplatného pro koncový bod, který chcete použít.

Než začnete používat rozhraní REST API, pochopit:
* Požadavky řeči na text pomocí rozhraní REST API může obsahovat jen 10 sekund nahraného zvukového.
* Rozhraní REST API pro rozpoznávání řeči na text vrací pouze konečných výsledků. Nejsou k dispozici částečné výsledky.
* Převod textu na řeč rozhraní REST API vyžaduje také hlavičku ověřování. To znamená, že je potřeba dokončit výměnu tokenů pro přístup ke službě. Další informace najdete v tématu [Ověřování](#authentication).

## <a name="authentication"></a>Authentication

Každý požadavek na buď speech to text nebo převod textu na řeč rozhraní REST API vyžaduje také hlavičku ověřování. Tato tabulka ukazuje, které hlavičky jsou podporovány pro každou službu:

| Podporované autorizační hlavičky | Převod řeči na text | Převod textu na řeč |
|------------------------|----------------|----------------|
| OCP-Apim-Subscription-Key | Ano | Ne |
| Autorizace: Nosiče | Ano | Ano |

Při použití `Ocp-Apim-Subscription-Key` záhlaví, je nutné pouze zadejte klíč předplatného. Příklad:

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Při použití `Authorization: Bearer` záhlaví, budete muset vytvořit žádost o `issueToken` koncového bodu. V této žádosti o výměnu váš klíč předplatného pro přístupový token, který je platný 10 minut. V následujících částech dozvíte, jak získat token, použijte token a aktualizovat token.

### <a name="how-to-get-an-access-token"></a>Jak získat přístupový token

K získání přístupového tokenu, je potřeba vytvořit žádost o `issueToken` pomocí koncového bodu `Ocp-Apim-Subscription-Key` a váš klíč předplatného.

Podporují se tyto oblasti a koncové body:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Tyto ukázky použijte při vytváření žádosti o token přístupu.

#### <a name="http-sample"></a>Ukázka protokolu HTTP

V tomto příkladu je jednoduché požadavku HTTP k získání tokenu. Nahraďte `YOUR_SUBSCRIPTION_KEY` s klíči předplatného Speech Service. Pokud vaše předplatné není v oblasti USA – Západ, nahraďte `Host` záhlaví s názvem hostitele vaší oblasti.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Text odpovědi obsahuje přístupový token ve formátu jazyka Java Web Token (JWT).

#### <a name="powershell-sample"></a>Ukázka PowerShellu

V tomto příkladu je jednoduchý skript prostředí PowerShell k získání přístupového tokenu. Nahraďte `YOUR_SUBSCRIPTION_KEY` s klíči předplatného Speech Service. Nezapomeňte použít správný koncový bod pro oblast, která odpovídá vašeho předplatného. V tomto příkladu je aktuálně nastavený na západní USA.

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>Ukázka cURL

cURL je nástroj příkazového řádku jsou k dispozici v systému Linux (a v subsystému Windows pro Linux). Tento příkaz cURL znázorňuje, jak získat přístupový token. Nahraďte `YOUR_SUBSCRIPTION_KEY` s klíči předplatného Speech Service. Nezapomeňte použít správný koncový bod pro oblast, která odpovídá vašeho předplatného. V tomto příkladu je aktuálně nastavený na západní USA.

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Ukázka v jazyce C#

To C# třídy ukazuje, jak získat přístupový token. Předejte klíč předplatného Speech Service při vytváření instance třídy. Pokud vaše předplatné není v oblasti USA – Západ, změňte hodnotu vlastnosti `FetchTokenUri` tak, aby odpovídaly oblast pro vaše předplatné.

```cs
/*
    * This class demonstrates how to get a valid access token.
    */
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="how-to-use-an-access-token"></a>Použití přístupového tokenu

Přístupový token odesílaných do služby, jako `Authorization: Bearer <TOKEN>` záhlaví. Každý přístupový token je platný 10 minut. Kdykoli můžete získat nový token, ale pokud chcete minimalizovat síťový provoz a latence, doporučujeme použít stejný token devět minut.

Tady je ukázka požadavku HTTP pro převod textu na řeč rozhraní REST API:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.tts.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Hello, world!
</voice></speak>
```

### <a name="how-to-renew-an-access-token-using-c"></a>Jak obnovit, aplikace access token pomocíC#

To C# je kód, což je náhrada třídy uvedené výše. `Authentication` Třídy automaticky získá nový přístupový token každých devět minut pomocí časovače. Tento přístup zajišťuje, že platný token je vždy k dispozici a je spuštěn program.

> [!NOTE]
> Namísto použití časovače, můžete uložit časové razítko při posledním tokenem byl získán. Potom můžete požádat o nový pouze v případě, že se nachází blízko vypršení platnosti. Tento přístup se vyhnete zbytečně požaduje nové tokeny a může být vhodnější pro programy, které úlohy s řídkým hlasové požadavky.

Stejně jako předtím, ujistěte se, že `FetchTokenUri` hodnota se shoduje s oblastí vašeho předplatného. Předejte klíč předplatného. Po vytvoření instance třídy.

```cs
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;
    private Timer accessTokenRenewer;

    //Access token expires every 10 minutes. Renew it every 9 minutes.
    private const int RefreshTokenDuration = 9;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

        // renew the token on set duration.
        accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                        this,
                                        TimeSpan.FromMinutes(RefreshTokenDuration),
                                        TimeSpan.FromMilliseconds(-1));
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private void RenewAccessToken()
    {
        this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
        Console.WriteLine("Renewed token.");
    }

    private void OnTokenExpiredCallback(object stateInfo)
    {
        try
        {
            RenewAccessToken();
        }
        catch (Exception ex)
        {
            Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
        }
        finally
        {
            try
            {
                accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
            }
        }
    }

    private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

## <a name="speech-to-text-api"></a>Rozhraní Speech to text API

Rozhraní REST API pro rozpoznávání řeči na text pouze podporuje promluvy krátké. Požadavky může obsahovat až 10 sekund zvuku se celková doba trvání 14 sekund. Rozhraní REST API vrátí pouze konečných výsledků, ne částečné nebo dočasné výsledky.

Pokud odesílání delší zvuk představuje požadavek pro aplikace, zvažte použití [sadou SDK pro řeč](speech-sdk.md) nebo [batch určené k transkripci](batch-transcription.md).

### <a name="regions-and-endpoints"></a>Oblasti a koncových bodů

Tyto oblasti jsou podporovány pro určené k transkripci řeči na text pomocí rozhraní REST API. Ujistěte se, že vyberete koncového bodu, který odpovídá oblasti vašeho předplatného.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="query-parameters"></a>Parametry dotazu

Tyto parametry mohou být zahrnuty v řetězci dotazu požadavku REST.

| Parametr | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| `language` | Identifikuje mluvený jazyk, který je právě rozpoznán. Zobrazit [podporované jazyky](language-support.md#speech-to-text). | Požaduje se |
| `format` | Určuje formát výsledku. Platné hodnoty jsou `simple` a `detailed`. Jednoduché výsledky obsahují `RecognitionStatus`, `DisplayText`, `Offset`, a `Duration`. Podrobné odpovědi zahrnout více výsledků s jistotou hodnotami a čtyři různé reprezentace. Ve výchozím nastavení je `simple`. | Nepovinné |
| `profanity` | Určuje způsob zpracování vulgárních výrazů v výsledky rozpoznávání. Platné hodnoty jsou `masked`, hvězdičky, která nahradí vulgárních výrazů `removed`, výsledek, který odebrání všech vulgárních výrazů nebo `raw`, což zahrnuje vulgární výrazy ve výsledku. Ve výchozím nastavení je `masked`. | Nepovinné |

### <a name="request-headers"></a>Hlavičky požadavku

Tato tabulka obsahuje povinné a nepovinné hlavičky pro žádosti o převod řeči na text.

|Hlavička| Popis | Povinné / volitelné |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Váš klíč předplatného Speech Service. | Buď toto záhlaví nebo `Authorization` je povinný. |
| `Authorization` | Autorizační token předcházet slovo `Bearer`. Další informace najdete v tématu [Ověřování](#authentication). | Buď toto záhlaví nebo `Ocp-Apim-Subscription-Key` je povinný. |
| `Content-type` | Popisuje formátu a kodek zadaná zvuková data. Platné hodnoty jsou `audio/wav; codecs=audio/pcm; samplerate=16000` a `audio/ogg; codecs=opus`. | Požaduje se |
| `Transfer-Encoding` | Určuje, že blokového zvukových dat je odesíláno, místo jednoho souboru. Tuto hlavičku používají pouze bloků zvuková data. | Nepovinné |
| `Expect` | Pokud pomocí přenosu v bloku, pošlete `Expect: 100-continue`. Speech Service uznává v prvotní žádosti a čeká další data.| Požadováno při odesílání bloku zvuková data. |
| `Accept` | Pokud je zadán, musí být `application/json`. Speech Service poskytuje výsledky ve formátu JSON. Některé webové žádosti platformy poskytují nekompatibilní výchozí hodnotu, pokud není zadán, takže je dobrým zvykem vždy zahrnovat `Accept`. | Volitelné, ale doporučené. |

### <a name="audio-formats"></a>Formáty zvuku

Zvuk se poslala v těle HTTP `POST` požadavku. Musí být v jednom z formátů, v této tabulce:

| Formát | Kodek | S přenosovou rychlostí | Vzorkovací frekvence |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bitů | 16 kHz, mono |
| OGG | DÍLE | 16 bitů | 16 kHz, mono |

>[!NOTE]
>Výše uvedené formáty jsou podporované prostřednictvím rozhraní REST API a objektu websocket na straně služby řeči. [Sadou SDK pro řeč](speech-sdk.md) aktuálně podporuje jenom WAV naformátuje PCM kodek.

### <a name="sample-request"></a>Ukázková žádost

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

### <a name="http-status-codes"></a>Stavové kódy HTTP

Stavový kód HTTP pro každou odpověď indikuje úspěch nebo běžné chyby.

| Stavový kód HTTP | Popis | Možný důvod |
|------------------|-------------|-----------------|
| 100 | Pokračovat | Byla přijata v prvotní žádosti. Pokračujte v odesílání zbývajícími daty. (Používá se s bloku.) |
| 200 | OK | Žádost byla úspěšná. text odpovědi je objekt JSON. |
| 400 | Nesprávná žádost | Kód jazyka není k dispozici nebo není podporovaný jazyk; Neplatný zvukový soubor. |
| 401 | Neautorizováno | Klíč předplatného nebo autorizační token je neplatný. v zadané oblasti nebo neplatný koncový bod. |
| 403 | Zakázáno | Chybí klíč předplatného nebo autorizační token. |

### <a name="chunked-transfer"></a>Bloku

Přenos rozdělený do bloků dat (`Transfer-Encoding: chunked`) může pomoct snížit latenci rozpoznávání, protože umožňuje Speech Service začala zpracovat zvukový soubor při přenosu je zašifrované. Rozhraní REST API neposkytuje výsledky částečné nebo dočasné. Tato možnost je určena výhradně pro zvýšení rychlosti odezvy.

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

### <a name="response-parameters"></a>Parametry odpovědi

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

`detailed` Formát obsahuje stejná data jako `simple` formátovat, spolu s `NBest`, seznam alternativních interpretace stejný výsledek rozpoznávání řeči. Tyto výsledky jsou seřazeny od nejpravděpodobněji nejméně pravděpodobně první položka je stejný jako výsledek hlavní rozpoznání.  Při použití `detailed` formátu `DisplayText` se poskytuje jako `Display` pro každého výsledku v `NBest` seznamu.

Každý objekt v `NBest` seznam obsahuje:

| Parametr | Popis |
|-----------|-------------|
| `Confidence` | Skóre spolehlivosti položky od 0.0 (bez obav) 1.0 (plnou důvěru) |
| `Lexical` | Lexikální formu textové rozpoznaných: vlastních slov rozpoznán. |
| `ITN` | Inverzní text normalized ("canonical") formu rozpoznaný text pomocí telefonního čísla, čísla, zkratky ("lékař smith" k "zotavení po havárii smith") a dalších transformací použít. |
| `MaskedITN` | Formulář není s vulgárních výrazů maskování použít, pokud o to požádá. |
| `Display` | Zobrazení formu rozpoznaný text s interpunkce a přidali malá a velká písmena. Tento parametr je stejný jako `DisplayText` při formátu je nastavena tak `simple`. |

### <a name="sample-responses"></a>Ukázkové odpovědi

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

## <a name="text-to-speech-api"></a>Převod textu na řeč rozhraní API

Převod textu na řeč rozhraní REST API podporuje neuronových sítí a standardní převod textu na řeč hlasů, z nichž každý podporuje konkrétní jazyku a dialektu, identifikovat podle národního prostředí.

* Úplný seznam hlasů, naleznete v tématu [jazykovou podporu](language-support.md#text-to-speech).
* Informace o dostupnosti v jednotlivých oblastech najdete v tématu [oblastech](regions.md#text-to-speech).

> [!IMPORTANT]
> Náklady se liší pro standardní, vlastní a neuronových sítí hlasy. Další informace najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="request-headers"></a>Hlavičky požadavku

Tato tabulka obsahuje povinné a nepovinné hlavičky pro žádosti o převod řeči na text.

| Hlavička | Popis | Povinné / volitelné |
|--------|-------------|---------------------|
| `Authorization` | Autorizační token předcházet slovo `Bearer`. Další informace najdete v tématu [ověřování](#authentication). | Požaduje se |
| `Content-Type` | Určuje typ obsahu pro zadaný text. Přijata hodnota: `application/ssml+xml`. | Požaduje se |
| `X-Microsoft-OutputFormat` | Určuje formát zvukového výstupu. Úplný seznam platných hodnot najdete v tématu [zvuku výstupy](#audio-outputs). | Požaduje se |
| `User-Agent` | Název aplikace. Musí být kratší než 255 znaků. | Požaduje se |

### <a name="audio-outputs"></a>Zvukový výstupy

Toto je seznam podporovaných formátů zvuku, které se odesílají v každé žádosti o jako `X-Microsoft-OutputFormat` záhlaví. Každý zahrnuje s přenosovou rychlostí a typ kódování. Speech Service podporuje 24 KHz, 16 KHz a vypíše 8 KHz zvuk.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> Pokud vybraný hlasový a výstupní formát různé přenosové rychlosti, zvuku poklesu podle potřeby. Ale 24khz hlasy nepodporují `audio-16khz-16kbps-mono-siren` a `riff-16khz-16kbps-mono-siren` formáty výstupu.

### <a name="request-body"></a>Text požadavku

Text jednotlivých `POST` žádosti se odešle jako [řeči syntézu Markup Language (SSML)](speech-synthesis-markup.md). SSML můžete rozhodnout voice a jazyk řečového vrácené službou převod textu na řeč. Úplný seznam podporovaných hlasů, naleznete v tématu [jazykovou podporu](language-support.md#text-to-speech).

> [!NOTE]
> Pokud používáte vlastní hlasové, tělo požadavku nelze odesílat jako prostý text (ASCII nebo UTF-8).

### <a name="sample-request"></a>Ukázková žádost

Tento požadavek HTTP používá k určení hlasu SSML. Text může mít maximálně 1 000 znaků.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>Stavové kódy HTTP

Stavový kód HTTP pro každou odpověď indikuje úspěch nebo běžné chyby.

| Stavový kód HTTP | Popis | Možný důvod |
|------------------|-------------|-----------------|
| 200 | OK | Žádost byla úspěšná. text odpovědi je zvukový soubor. |
| 400 | Chybný požadavek | Povinný parametr nebyl nalezen, prázdný nebo null. Nebo hodnota předaná buď povinný nebo volitelný parametr není platný. Běžné potíže se hlavičku, která je příliš dlouhý. |
| 401 | Neautorizováno | Požadavek není autorizovaný. Ověřte váš klíč předplatného nebo token je platný a v oblasti správné. |
| 413 | Příliš velká entita požadavku | Vstup SSML je delší než 1024 znaků. |
| 429 | Příliš mnoho žádostí | Překročili jste kvótu nebo počet požadavků pro vaše předplatné povolená. |
| 502 | Chybná brána | Problém sítě nebo na straně serveru. Může také znamenat neplatné záhlaví. |

Pokud je stav protokolu HTTP `200 OK`, tělo odpovědi obsahuje zvukový soubor v požadovanému formátu. Tento soubor můžete přehrát má přenést, uložit do vyrovnávací paměti, nebo uložit do souboru.

## <a name="next-steps"></a>Další postup

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)

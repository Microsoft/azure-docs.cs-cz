---
title: Speech service rozhraní REST API
description: Referenční informace pro rozhraní REST API pro službu rozpoznávání řeči.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/09/2018
ms.author: v-jerkin
ms.openlocfilehash: cc73be09cec4ef963a496687d112f98e05d98802
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48018515"
---
# <a name="speech-service-rest-apis"></a>Speech service rozhraní REST API

Rozhraní REST API služby Azure Cognitive Services unified Speech service jsou podobné rozhraní API poskytovaných [rozhraní API pro zpracování řeči Bingu](https://docs.microsoft.com/azure/cognitive-services/Speech). Koncové body se liší od koncových bodů použitých ve službě pro zpracování řeči Bingu. Místní koncové body jsou k dispozici a je nutné použít klíč předplatného, který odpovídá ke koncovému bodu, které používáte.

## <a name="speech-to-text"></a>Převod řeči na text

V následující tabulce jsou uvedeny koncové body pro převod řeči na Text REST API. Použijte ten, který odpovídá oblasti vašeho předplatného.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

> [!NOTE]
> Pokud jste si přizpůsobili akustický model nebo jazykového modelu nebo výslovnost, místo toho použijte vlastní koncový bod.

Toto rozhraní API podporuje pouze krátkou projevy. Požadavky může obsahovat až 10 sekund zvuk a naposledy maximálně 14 sekund celkové. Rozhraní REST API vrátí pouze konečných výsledků, částečné nebo dočasné výsledky. Speech service má také [batch určené k transkripci](batch-transcription.md) rozhraní API, které můžete přepisy delší zvuk.

### <a name="query-parameters"></a>Parametry dotazu

Následující parametry mohou být zahrnuty v řetězci dotazu požadavku REST.

|Název parametru|Požadované a volitelné|Význam|
|-|-|-|
|`language`|Požaduje se|Identifikátor jazyka, který má být rozpoznána. Zobrazit [podporované jazyky](language-support.md#speech-to-text).|
|`format`|Nepovinné<br>Výchozí hodnota: `simple`|Výsledný formát `simple` nebo `detailed`. Jednoduché výsledky obsahují `RecognitionStatus`, `DisplayText`, `Offset`a doby trvání. Podrobné výsledky obsahují více kandidátů s jistotou hodnotami a čtyři různé reprezentace.|
|`profanity`|Nepovinné<br>Výchozí hodnota: `masked`|Způsob zpracování vulgárních výrazů v výsledky rozpoznávání. Může být `masked` (nahradí vulgárních výrazů hvězdičky z obou stran), `removed` (odebere všechny vulgárních výrazů), nebo `raw` (včetně vulgárních výrazů).

### <a name="request-headers"></a>Hlavičky požadavku

Následující pole se odesílají v hlavičce požadavku protokolu HTTP.

|Záhlaví|Význam|
|------|-------|
|`Ocp-Apim-Subscription-Key`|Váš klíč předplatného služby řeči. Buď toto záhlaví nebo `Authorization` musí být zadaná.|
|`Authorization`|Autorizační token předcházet slovo `Bearer`. Buď toto záhlaví nebo `Ocp-Apim-Subscription-Key` musí být zadaná. Zobrazit [ověřování](#authentication).|
|`Content-type`|Popisuje formátu a kodek zvuková data. V současné době se tato hodnota musí být `audio/wav; codec=audio/pcm; samplerate=16000`.|
|`Transfer-Encoding`|Volitelné. Pokud tento parametr zadaný, musí být `chunked` umožňující zvukových dat k odeslání v několika menších dávkách místo jednoho souboru.|
|`Expect`|Pokud pomocí přenosu v bloku, pošlete `Expect: 100-continue`. Speech service uznává v prvotní žádosti a čeká další data.|
|`Accept`|Volitelné. Pokud je zadán, musí obsahovat `application/json`, jak služba Speech poskytuje výsledky ve formátu JSON. (Některé webové žádosti platformy poskytují nekompatibilní výchozí hodnotu, pokud není zadán, takže je dobrým zvykem vždy zahrnovat `Accept`.)|

### <a name="audio-format"></a>Formát zvuku

Zvuk se poslala v těle HTTP `PUT` požadavku. Musí být ve formátu WAV 16 bitů s PCM jeden kanál (mono) na 16 KHz.

### <a name="chunked-transfer"></a>Bloku

Přenos rozdělený do bloků dat (`Transfer-Encoding: chunked`) může pomoct snížit latenci rozpoznávání, protože umožňuje Speech service začala zpracovat zvukový soubor při přenosu je zašifrované. Rozhraní REST API neposkytuje výsledky částečné nebo dočasné. Tato možnost je určena výhradně pro zvýšení rychlosti odezvy.

Následující kód ukazuje, jak posílat zvuk v blocích. `request` připojen objekt HTTPWebRequest na příslušný koncový bod REST. `audioFile` je cesta k zvukový soubor na disku.

```csharp
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

### <a name="example-request"></a>Příklad žádosti

Následuje Typická žádosti.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status"></a>Stav protokolu HTTP

Stav protokolu HTTP odpovědi označuje úspěch nebo běžné chybové stavy.

Kód HTTP|Význam|Možný důvod
-|-|-|
100|Pokračovat|Byla přijata v prvotní žádosti. Pokračujte v odesílání zbývajícími daty. (Používá se s bloku.)
200|OK|Žádost byla úspěšná. text odpovědi je objekt JSON.
400|Nesprávná žádost|Kód jazyka není k dispozici nebo není podporovaný jazyk; Neplatný zvukový soubor.
401|Neautorizováno|Klíč předplatného nebo autorizační token je neplatný. v zadané oblasti nebo neplatný koncový bod.
403|Zakázáno|Chybí klíč předplatného nebo autorizační token.

### <a name="json-response"></a>Odpověď JSON

Výsledky jsou vráceny ve formátu JSON. `simple` Formátu obsahuje pouze následující pole nejvyšší úrovně.

|Název pole|Obsah|
|-|-|
|`RecognitionStatus`|Stav, jako například `Success` pro úspěšné rozpoznávání. Najdete v následující tabulce.|
|`DisplayText`|Rozpoznaný text po malá a velká písmena, interpunkční znaménka, normalizace inverzní text (převod textu na mluvené slovo na kratší formulářů, jako je například 200 pro "dvě stě" nebo "zotavení po havárii. Smith""lékař Smith") a maskování vulgárních výrazů. K dispozici pouze v případě úspěchu.|
|`Offset`|Čas (v jednotkách 100 nanosekund), ve kterém začíná rozpoznané řeči v zvukový datový proud.|
|`Duration`|Doba (v jednotkách 100 nanosekund) rozpoznané řeči v zvukový datový proud.|

`RecognitionStatus` Pole může obsahovat následující hodnoty.

|Hodnota stavu|Popis
|-|-|
| `Success` | Rozpoznávání byla úspěšná a ZobrazenýText pole je k dispozici. |
| `NoMatch` | V zvukový datový proud byl zjištěn řeči, ale žádná slova v cílovém jazyce se shoda našla. Obvykle znamená, že jazyk rozpoznávání je jiný jazyk než ty, které uživatel to mluví. |
| `InitialSilenceTimeout` | Začátek zvukový datový proud obsahovala pouze nečinnosti a službu vypršel časový limit čekání na rozpoznávání řeči. |
| `BabbleTimeout` | Začátek zvukový datový proud obsahovala pouze šum a službu vypršel časový limit čekání na řeč. |
| `Error` | Rozpoznávání služby došlo k interní chybě a nemohl pokračovat. Zkuste to znovu Pokud je to možné. |

> [!NOTE]
> Pokud se zvuk skládá pouze z vulgárních výrazů a `profanity` parametr dotazu je nastaven na `remove`, služba nevrací výsledek řeči. 


`detailed` Formátu obsahuje stejné pole, jako `simple` formátovat, spolu s `NBest` pole. `NBest` Seznam alternativních interpretace stejné řeči řazeny od nejpodezřelejších po nejpravděpodobněji nejméně pravděpodobně je pole. První položka je stejný jako výsledek hlavní rozpoznání. Každá položka obsahuje následující pole:

|Název pole|Obsah|
|-|-|
|`Confidence`|Skóre spolehlivosti položky od 0.0 (bez obav) 1.0 (plnou důvěru)
|`Lexical`|Lexikální formu textové rozpoznaných: vlastních slov rozpoznán.
|`ITN`|Inverzní text normalized ("canonical") formu rozpoznaný text pomocí telefonního čísla, čísla, zkratky ("lékař smith" k "zotavení po havárii smith") a dalších transformací použít.
|`MaskedITN`| Formulář není s vulgárních výrazů maskování použít, pokud o to požádá.
|`Display`| Zobrazení formu rozpoznaný text s interpunkce a přidali malá a velká písmena. Stejné jako `DisplayText` ve výsledku nejvyšší úrovně.

### <a name="sample-responses"></a>Ukázkové odpovědi

Následuje Typická odpověď pro `simple` rozpoznávání.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Následuje Typická odpověď pro `detailed` rozpoznávání.

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

## <a name="text-to-speech"></a>Převod textu na řeč

Níže jsou koncové body REST pro převod textu na řeč z služba Speech API. Použití koncového bodu, který odpovídá oblasti vašeho předplatného.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

Speech service podporuje výstupní zvuková 24 KHz kromě výstup 16 Khz podporována pro zpracování řeči Bingu. Čtyři 24 KHz výstupní formáty jsou k dispozici pro použití v `X-Microsoft-OutputFormat` hlavičky protokolu HTTP, jako jsou dvě hlasy 24 KHz, `Jessa24kRUS` a `Guy24kRUS`.

Národní prostředí | Jazyk   | Pohlaví | Název mapování služby
-------|------------|--------|------------
cs-CZ  | Jazykovou verzi US English | Žena | "Microsoft serveru řeči Text na řeč hlas (en US, Jessa24kRUS)" 
cs-CZ  | Jazykovou verzi US English | Muž   | "Microsoft serveru řeči Text na řeč hlas (en US, Guy24kRUS)"

Úplný seznam dostupných hlasů je k dispozici v [podporované jazyky](language-support.md#text-to-speech).

### <a name="request-headers"></a>Hlavičky požadavku

Následující pole se odesílají v hlavičce požadavku protokolu HTTP.

|Záhlaví|Význam|
|------|-------|
|`Authorization`|Autorizační token předcházet slovo `Bearer`. Povinná hodnota. Zobrazit [ověřování](#authentication).|
|`Content-Type`|Vstupní typ obsahu: `application/ssml+xml`.|
|`X-Microsoft-OutputFormat`|Zvukový formát výstupu. Najdete v následující tabulce.|
|`User-Agent`|Název aplikace. Nevyžaduje. musí obsahovat méně než 255 znaků.|

K dispozici zvukové formáty výstupu (`X-Microsoft-OutputFormat`) začlenit přenosové rychlosti a kódování.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `raw-8khz-8bit-mono-mulaw`
`riff-8khz-8bit-mono-mulaw`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

> [!NOTE]
> Pokud vybraný hlasový a výstupní formát různé přenosové rychlosti, zvuku poklesu podle potřeby. Ale 24khz hlasy nepodporují `audio-16khz-16kbps-mono-siren` a `riff-16khz-16kbps-mono-siren` formáty výstupu. 

### <a name="request-body"></a>Tělo požadavku

Text, který má být převeden do mluvené řeči se odešle jako těla protokolu HTTP `POST` vyžádat buď jako prostý text (ASCII nebo UTF-8) nebo [Markup Language syntézu řeči](speech-synthesis-markup.md) (SSML) formát (UTF-8). Požadavky na prostý text použijte výchozí hlasové služby a jazyka. Odešlete SSML použít jiný hlas.

### <a name="sample-request"></a>Ukázková žádost

Následující požadavek HTTP používá textu SSML zvolit hlasu. Tělo nesmí mít více než 1 000 znaků.

```xml
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

### <a name="http-response"></a>Odpověď protokolu HTTP

Stav protokolu HTTP odpovědi označuje úspěch nebo běžné chybové stavy.

Kód HTTP|Význam|Možný důvod
-|-|-|
200|OK|Žádost byla úspěšná. text odpovědi je zvukový soubor.
400 |Chybný požadavek |Povinný parametr nebyl nalezen, prázdný nebo null. Nebo hodnota předaná buď povinný nebo volitelný parametr není platný. Běžné potíže se hlavičku, která je příliš dlouhý.
401|Neautorizováno |Požadavek není autorizovaný. Ověřte váš klíč předplatného nebo token je platný a v oblasti správné.
413|Příliš velká entita požadavku|Vstup SSML je delší než 1024 znaků.
429|Příliš mnoho žádostí|Překročili jste kvótu nebo počet požadavků pro vaše předplatné povolená.
502|Chybná brána | Problém sítě nebo na straně serveru. Může také znamenat neplatné záhlaví.

Pokud je stav protokolu HTTP `200 OK`, tělo odpovědi obsahuje zvukový soubor v požadovanému formátu. Tento soubor můžete přehrát, jako má přenesených nebo uložit do vyrovnávací paměti nebo později přehrát či jiné použití souboru.

## <a name="authentication"></a>Authentication

Odesílání požadavku do rozhraní REST API služby řeči vyžaduje klíč předplatného nebo přístupového tokenu. Obecně je nejjednodušší přímé zasílání klíč předplatného. Speech service pak získá přístupový token za vás. Chcete-li minimalizovat dobu odezvy, můžete místo toho použít přístupový token.

K získání tokenu, k dispozici klíč předplatného. k místní službě řeči `issueToken` koncový bod, jak je znázorněno v následující tabulce. Použití koncového bodu, který odpovídá oblasti vašeho předplatného.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Každý přístupový token je platný 10 minut. Kdykoli můžete získat nový token. Pokud chcete můžete získat token pouze před každým požadavkem rozhraní REST API pro rozpoznávání řeči. Chcete-li minimalizovat síťový provoz a latence, doporučujeme použít stejný token devět minut.

Následující části vysvětlují, jak získat token a jak ji používat v požadavku.

### <a name="get-a-token-http"></a>Získání tokenu: HTTP

V následujícím příkladu je ukázka požadavku HTTP pro získání tokenu. Nahraďte `YOUR_SUBSCRIPTION_KEY` s klíči předplatného služby řeči. Pokud vaše předplatné není v oblasti USA – Západ, nahraďte `Host` záhlaví s názvem hostitele vaší oblasti.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Text odpovědi na tento požadavek je přístupový token ve formátu jazyka Java Web Token (JWT).

### <a name="get-a-token-powershell"></a>Získání tokenu: PowerShell

Následující skript prostředí Windows PowerShell ukazuje, jak získat přístupový token. Nahraďte `YOUR_SUBSCRIPTION_KEY` s klíči předplatného služby řeči. Pokud vaše předplatné není v oblasti USA – Západ, název hostitele z daného identifikátoru URI odpovídajícím způsobem měnit.

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

### <a name="get-a-token-curl"></a>Získání tokenu: cURL

cURL je nástroj příkazového řádku jsou k dispozici v systému Linux (a v subsystému Windows pro Linux). Následující příkaz cURL znázorňuje, jak získat přístupový token. Nahraďte `YOUR_SUBSCRIPTION_KEY` s klíči předplatného služby řeči. Pokud vaše předplatné není v oblasti USA – Západ, název hostitele z daného identifikátoru URI odpovídajícím způsobem měnit.

> [!NOTE]
> Příkaz se zobrazí na více řádků pro lepší čitelnost, ale zadejte ho na jednom řádku v příkazovém řádku shell.

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="get-a-token-c"></a>Získání tokenu: C#

Následující třída jazyka C# ukazuje, jak získat přístupový token. Předejte klíč předplatného. Služba Speech při vytvoření instance třídy. Pokud vaše předplatné není v oblasti USA – Západ, změňte název hostitele `FetchTokenUri` odpovídajícím způsobem.

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

### <a name="use-a-token"></a>Použít token

Použít token v požadavku REST API, zadejte ho `Authorization` záhlaví následující slovo `Bearer`. Tady je ukázkový Text na řeč REST požadavek, který obsahuje token. Nahraďte skutečné tokenu pro `YOUR_ACCESS_TOKEN`. Použijte název správného hostitele v `Host` záhlaví.

```xml
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

### <a name="renew-authorization"></a>Obnovit autorizaci

Ověřovací token, který vyprší za 10 minut. Obnovení vašeho oprávnění získat nový token, než vyprší její platnost. Například můžete získat nový token po devět minut.

Následující kód jazyka C# je, což je náhrada třídy uvedené výše. `Authentication` Třída automaticky získá nový přístupový token každých devět minut pomocí časovače. Tento přístup zajišťuje, že platný token je vždy k dispozici a je spuštěn program.

> [!NOTE]
> Namísto použití časovače, můžete uložit časové razítko při posledním tokenem byl získán. Potom můžete požádat o nový pouze v případě, že se nachází blízko vypršení platnosti. Tento přístup se vyhnete zbytečně požaduje nové tokeny a může být vhodnější pro programy, které úlohy s řídkým hlasové požadavky.

Stejně jako předtím, ujistěte se, že `FetchTokenUri` hodnota se shoduje s oblastí vašeho předplatného. Předejte klíč předplatného. Po vytvoření instance třídy.

```cs
/*
    * This class demonstrates how to maintain a valid access token.
    */
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

## <a name="next-steps"></a>Další postup

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)


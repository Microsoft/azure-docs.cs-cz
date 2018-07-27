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
ms.openlocfilehash: 9991e0a1fc54e6aa4a99b8bfbd93abdfe974b01b
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283065"
---
# <a name="speech-service-rest-apis"></a>Speech service rozhraní REST API

Rozhraní REST API služby pro jednotné řeči se podobají rozhraní API poskytovaných [rozhraní Speech API](https://docs.microsoft.com/azure/cognitive-services/Speech) (dříve označované jako Speech Service Bingu). Koncové body se liší od koncových bodů použitých podle předchozí Speech service.

## <a name="speech-to-text"></a>Převod řeči na text

Služba Speech to Text API pouze koncových bodů použitých liší od předchozí Speech service rozhraní API pro rozpoznávání řeči. Nové koncové body jsou uvedeny v následující tabulce. Použijte ten, který odpovídá oblasti vašeho předplatného.

[!include[](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

Služba Speech to Text API je podobný v opačném případě [rozhraní REST API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) pro předchozí rozhraní Speech API.

Převod řeči na Text REST API podporuje pouze krátkou projevy. Požadavky může obsahovat až 10 sekund zvuk a naposledy maximálně 14 sekund celkové. Rozhraní REST API vrátí pouze konečných výsledků, částečné nebo dočasné výsledky.

> [!NOTE]
> Pokud jste si přizpůsobili akustický model nebo jazykového modelu nebo výslovnost, místo toho použijte vlastní koncový bod.

## <a name="text-to-speech"></a>Převod textu na řeč

Nové textu na řeč rozhraní API podporuje výstupní zvuková 24 KHz. `X-Microsoft-OutputFormat` Záhlaví teď může obsahovat následující hodnoty.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `audio-16khz-16kbps-mono-siren`
`riff-16khz-16kbps-mono-siren`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

Speech service teď poskytuje dva 24 KHz hlasy:

Národní prostředí | Jazyk   | Pohlaví | Název mapování služby
-------|------------|--------|------------
cs-CZ  | Jazykovou verzi US English | Žena | "Microsoft serveru řeči Text na řeč hlas (en US, Jessa24kRUS)" 
cs-CZ  | Jazykovou verzi US English | Muž   | "Microsoft serveru řeči Text na řeč hlas (en US, Guy24kRUS)"

Níže jsou koncové body REST pro sjednocené Speech service převod textu na řeč rozhraní API. Použití koncového bodu, který odpovídá oblasti vašeho předplatného.

[!include[](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

Ponechte tyto rozdíly v úvahu jako odkazujete [dokumentace k rozhraní REST API](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/bingvoiceoutput) pro předchozí rozhraní Speech API.

## <a name="authentication"></a>Authentication

Odesílání požadavku do rozhraní REST API služby řeči vyžaduje přístupový token. Získání tokenu tím, že váš klíč předplatného do regionálních služeb řeč poskytuje `issueToken` koncový bod, je znázorněno v následující tabulce. Použití koncového bodu, který odpovídá oblasti vašeho předplatného.

[!include[](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Každý přístupový token je platný 10 minut. V každém okamžiku může získat nový token – včetně, pokud chcete, těsně před každým požadavkem rozhraní REST API pro rozpoznávání řeči. Chcete-li minimalizovat síťový provoz a latence, ale doporučujeme používat stejný token devět minut.

Následující části vysvětlují, jak získat token a jak ji používat v požadavku.

### <a name="getting-a-token-http"></a>Při získávání tokenu: HTTP

Následuje ukázka požadavku HTTP pro získání tokenu. Nahraďte `YOUR_SUBSCRIPTION_KEY` s klíči předplatného služby řeči. Pokud vaše předplatné není v oblasti USA – Západ, nahraďte `Host` záhlaví s názvem hostitele vaší oblasti.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Text odpovědi na tento požadavek je přístupový token ve formátu jazyka Java Web Token (JWT).

### <a name="getting-a-token-powershell"></a>Při získávání tokenu: PowerShell

Níže uvedený skript prostředí Windows PowerShell ukazuje, jak získat přístupový token. Nahraďte `YOUR_SUBSCRIPTION_KEY` s klíči předplatného služby řeči. Pokud vaše předplatné není v oblasti USA – Západ, název hostitele daného identifikátoru URI odpovídajícím způsobem měnit.

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

### <a name="getting-a-token-curl"></a>Při získávání tokenu: cURL

cURL je nástroj příkazového řádku jsou k dispozici v systému Linux (a v subsystému Windows pro Linux). Následující příkaz cURL znázorňuje, jak získat přístupový token. Nahraďte `YOUR_SUBSCRIPTION_KEY` s klíči předplatného služby řeči. Pokud vaše předplatné není v oblasti USA – Západ, název hostitele daného identifikátoru URI odpovídajícím způsobem měnit.

> [!NOTE]
> Příkaz se zobrazí na více řádků pro lepší čitelnost, ale by se zadat na jednom řádku v příkazovém řádku shell.

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="getting-a-token-c"></a>Při získávání tokenu: C#

C# třídy níže ukazuje, jak získat přístupový token. Předejte váš klíč předplatného Speech service při vytváření instance třídy. Pokud vaše předplatné není v oblasti USA – Západ, změňte název hostitele `FetchTokenUri` odpovídajícím způsobem.

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

### <a name="using-a-token"></a>Pomocí tokenu

Použít token v požadavku REST API, zadejte ho `Authorization` záhlaví následující slovo `Bearer`. Například tady je ukázkový Text na řeč REST žádost obsahující token. Nahraďte skutečné tokenu pro `YOUR_ACCESS_TOKEN` a použijte správný název hostitele v `Host` záhlaví.

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

### <a name="renewing-authorization"></a>Prodlužuje se platnost autorizace

Ověřovací token, který vyprší za 10 minut. Obnovení vašeho oprávnění získat nový token, než vyprší její platnost – například po devět minut. 

Následující kód jazyka C# je, což je náhrada třídy uvedené výše. `Authentication` Třída automaticky získá nový přístupový token každých devět minut pomocí časovače. Tento přístup zajišťuje, že platný token je vždy k dispozici a je spuštěn program.

> [!NOTE]
> Namísto použití časovač, může ukládat časové razítko, kdy byl získán aktuálního tokenu, pak pouze v případě, že blíží vypršení platnosti aktuálního tokenu požádat o nový. Tento přístup se vyhnete zbytečně požaduje nové tokeny a může být vhodnější pro programy, které úlohy s řídkým hlasové požadavky.

Stejně jako předtím, ujistěte se, že `FetchTokenUri` hodnota se shoduje s oblastí vašeho předplatného. Při vytváření instance třídy, předejte klíč předplatného.

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


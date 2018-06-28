---
title: Rozpoznávání řeči služby REST API | Microsoft Docs
description: Referenční dokumentace pro REST API pro rozpoznávání řeči službu.
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/09/2018
ms.author: v-jerkin
ms.openlocfilehash: a25c2b7ea7fdfcc6bcaa10baff3a5ae14ae9753b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37044815"
---
# <a name="speech-service-rest-apis"></a>REST API služby řeči

Rozhraní REST API služby jednotná řeči jsou podobná rozhraní API poskytovaných [rozhraní API pro rozpoznávání řeči](https://docs.microsoft.com/azure/cognitive-services/Speech) (dříve označované jako ke službě Bing řeči). Koncové body se liší od předchozí řeči služba používá koncových bodů.

## <a name="speech-to-text"></a>Převod řeči na text

V převod řeči na Text rozhraní API pouze koncových bodů použitých liší od předchozí řeči služby API pro rozpoznávání řeči. Nové koncové body jsou uvedeny v následující tabulce. Použijte ten, který odpovídá oblasti vašeho předplatného.

[!include[](includes/endpoints-speech-to-text.md)]

Převod řeči na Text rozhraní API je jinak podobná [REST API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) pro předchozí řeči API.

Převod řeči na Text REST API podporuje pouze krátké utterances. Žádosti o může obsahovat až 10 sekund zvuk a naposledy maximálně celkové 14 sekund. Rozhraní REST API vrátí pouze poslední výsledky, částečné nebo dočasné výsledky.

> [!NOTE]
> Pokud jste si přizpůsobili akustickými model nebo model jazyk nebo výslovnosti, použijte místo toho svůj vlastní koncový bod.

## <a name="text-to-speech"></a>Převod textu na řeč

Nové převod textu na řeč rozhraní API podporuje výstupní zvuková 24 KHz. `X-Microsoft-OutputFormat` Záhlaví může obsahovat nyní následující hodnoty.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `audio-16khz-16kbps-mono-siren`
`riff-16khz-16kbps-mono-siren`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

Služba rozpoznávání řeči teď poskytuje dva hlasy 24 KHz:

Národní prostředí | Jazyk   | Pohlaví | Mapování názvu služby
-------|------------|--------|------------
cs-CZ  | Čeština | Žena | "Microsoft Server řeči Text na hlas rozpoznávání řeči (en US, Jessa24kRUS)" 
cs-CZ  | Čeština | Muž   | "Microsoft Server řeči Text na hlas rozpoznávání řeči (en US, Guy24kRUS)"

Níže jsou koncové body REST pro jednotná převod služby textu na řeč rozhraní API. Pomocí koncového bodu, který odpovídá oblasti vašeho předplatného.

[!include[](includes/endpoints-text-to-speech.md)]

Zachovat tyto rozdíly v paměti, jako jste odkazovat [dokumentace k REST API](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/bingvoiceoutput) pro předchozí řeči API.

## <a name="authentication"></a>Authentication

Odesílání požadavku do rozhraní API REST služby řeči vyžaduje přístupový token. Získat token tím, že poskytuje svůj klíč předplatného v místní službě řeči `issueToken` koncový bod, uvedené v následující tabulce. Pomocí koncového bodu, který odpovídá oblasti vašeho předplatného.

[!include[](includes/endpoints-token-service.md)]

Každý přístupový token je platný 10 minut. Můžete kdykoli získat nový token – včetně, pokud chcete, těsně před každým požadavkem řeči REST API. Chcete-li minimalizovat síťový provoz a latenci, ale doporučujeme používat stejný token devět minut.

Následující části vysvětlují, jak získat token a způsobu jeho použití v požadavku.

### <a name="getting-a-token-http"></a>Získávání tokenu: HTTP

Níže je ukázka požadavek HTTP pro získání tokenu. Nahraďte `YOUR_SUBSCRIPTION_KEY` s svůj klíč předplatného služby řeči. Pokud vaše předplatné není v oblasti západní USA, nahraďte `Host` hlavička s vaší oblasti název hostitele.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Text odpovědi na tento požadavek je přístupového tokenu ve formátu Java webového tokenu (JWT).

### <a name="getting-a-token-powershell"></a>Získávání tokenu: prostředí PowerShell

Následující skript Windows Powershellu znázorňuje, jak získat přístupový token. Nahraďte `YOUR_SUBSCRIPTION_KEY` s svůj klíč předplatného služby řeči. Pokud vaše předplatné není v oblasti západní USA, změňte název hostitele zadaný identifikátor URI odpovídajícím způsobem.

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

### <a name="getting-a-token-curl"></a>Získávání tokenu: cURL

cURL je nástroj příkazového řádku k dispozici v systému Linux (a v subsystému Windows pro Linux). Následující příkaz cURL znázorňuje, jak získat přístupový token. Nahraďte `YOUR_SUBSCRIPTION_KEY` s svůj klíč předplatného služby řeči. Pokud vaše předplatné není v oblasti západní USA, změňte název hostitele zadaný identifikátor URI odpovídajícím způsobem.

> [!NOTE]
> Příkaz je zobrazený na více řádků čitelnější, ale musí být zadán na jeden řádek v řádku prostředí.

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="getting-a-token-c"></a>Získávání tokenu: C#

C# třída níže ukazuje, jak získat přístupový token. Předejte svůj klíč předplatného služby řeči při vytvoření instance třídy. Pokud vaše předplatné není v oblasti západní USA, změňte název hostitele `FetchTokenUri` správně.

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

Pokud chcete používat token v požadavku REST API, zadat ho `Authorization` záhlaví, následující slovo `Bearer`. Zde je například ukázkový Text řeči REST žádost obsahující token. Nahraďte skutečným tokenu pro `YOUR_ACCESS_TOKEN` a použít správný název hostitele ve `Host` záhlaví.

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

### <a name="renewing-authorization"></a>Obnovení autorizace

Autorizační token platnost vyprší za 10 minut. Obnovit vašich oprávnění tak, že získávání nový token, než vyprší její platnost – například po devět minut. 

Následující kód C# je drop-in nahrazení pro třídu uvedené výše. `Authentication` Třída automaticky získá nový přístupový token každých devět minut pomocí časovač. Tento přístup zajišťuje, že platný token je vždy k dispozici a když váš program běží.

> [!NOTE]
> Místo použití časovač, může ukládat časové razítko při aktuální token byl získán, pak požádat o nový, pouze pokud je aktuální token blíží vypršení platnosti. Tento přístup zabraňuje zbytečně žádají o nové tokeny a může být vhodnější pro programy, které žádají o nepravidelným řeči.

Jako dříve, zkontrolujte `FetchTokenUri` hodnota odpovídá oblasti vašeho předplatného. Při vytvoření instance třídy, předejte svůj klíč předplatného.

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

* [Získat zkušební verze předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
* [Zjistit, jak přizpůsobit rozpoznávání řeči modelu](how-to-customize-speech-models.md)

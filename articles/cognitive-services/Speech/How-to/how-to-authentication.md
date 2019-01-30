---
title: Ověření pro zpracování řeči Bingu | Dokumentace Microsoftu
titlesuffix: Azure Cognitive Services
description: Žádosti o ověření použití rozhraní API pro zpracování řeči Bingu
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 163ec61dc534255470626468195dcab28b3e3250
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221823"
---
# <a name="authenticate-to-the-speech-api"></a>Ověření rozhraním Speech API

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Pro zpracování řeči Bingu podporuje ověřování pomocí:

- Klíč předplatného.
- Autorizační token.

## <a name="use-a-subscription-key"></a>Použijte klíč předplatného

Speech Service použít, musíte nejprve odběru rozhraní Speech API, která je součástí služeb Cognitive Services (dříve Project Oxford). Můžete získat bezplatné předplatné zkušební verze klíče z [předplatné služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) stránky. Po výběru rozhraní API pro rozpoznávání řeči, vyberte **získat klíč rozhraní API** získat klíč. Vrátí primární a sekundární klíč. Oba klíče jsou svázány se stejnou kvótu, abyste mohli používat ani jeden klíč.

Dlouhodobé používání a zvýšené kvóty, zaregistrujte si [účtu Azure](https://azure.microsoft.com/free/).

Pokud chcete používat rozhraní REST API pro rozpoznávání řeči, je potřeba předat klíč předplatného v `Ocp-Apim-Subscription-Key` pole v hlavičce požadavku.

Name| Formát| Popis
----|-------|------------
OCP-Apim-Subscription-Key | ASCII | YOUR_SUBSCRIPTION_KEY

Následuje příklad hlavičky požadavku:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

> [!IMPORTANT]
> Pokud používáte [klientské knihovny](../GetStarted/GetStartedClientLibraries.md) ve vaší aplikaci, ověřte, že můžete získat ověřovací token, který s klíči předplatného, jak je popsáno v následující části. Tyto klientské knihovny klíč předplatného můžete získat autorizační token a pak pomocí tokenu pro ověření.

## <a name="use-an-authorization-token"></a>Použití autorizační token

Alternativně můžete autorizační token pro ověření jako důkaz o ověřování/autorizace. Chcete-li získat tento token, je nutné nejprve získat klíč předplatného z rozhraní API pro rozpoznávání řeči, jak je popsáno v [předchozí části](#use-a-subscription-key).

### <a name="get-an-authorization-token"></a>Získat autorizační token

Až budete mít klíč platné předplatné, odešlete požadavek POST do tokenu služby Cognitive Services. V odpovědi obdržíte ověřovací token, který jako JSON Web Token (token JWT).

> [!NOTE]
> Token, který má vypršení platnosti 10 minut. K obnovení tokenu, najdete v následující části.

Identifikátor URI tokenu služby se nachází tady:

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Následující příklad kódu ukazuje, jak získat přístupový token. Nahraďte `YOUR_SUBSCRIPTION_KEY` s klíči předplatného:

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

V příkladu používá curl v Linuxu pomocí prostředí bash. Pokud není dostupný na vaší platformě, může být potřeba nainstalovat nástroj curl. Tento příklad funguje taky na Cygwin na Windows, Git Bash, zsh a jiné prostředí.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
    /*
     * This class demonstrates how to get a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
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
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

---

Následuje ukázka požadavku POST:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

Když zrovna o autorizační token od služby tokenů, zkontrolujte, zda váš klíč předplatného je stále platný. Pokud používáte bezplatné zkušební verze klíče, přejděte [předplatné služeb Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) stránky, klikněte na "Přihlášení" přihlásit se pomocí účtu, který jste použili k použití bezplatné zkušební verze klíče a zkontrolujte, jestli klíč předplatného vypršela, nebo přesahuje kvóta.

### <a name="use-an-authorization-token-in-a-request"></a>Použití autorizační token v požadavku

Pokaždé, když volání rozhraní API pro rozpoznávání řeči, je nutné předat ověřovací token, který v `Authorization` záhlaví. `Authorization` Záhlaví musí obsahovat přístupový token JWT.

Následující příklad ukazuje způsob použití autorizační token při volání rozhraní REST API pro rozpoznávání řeči.

> [!NOTE]
> Nahraďte `YOUR_AUDIO_FILE` cestou k souboru připraveného obsahu zvuku. Nahraďte `YOUR_ACCESS_TOKEN` autorizačním tokenem, který jste získali v předchozím kroku [získat autorizační token](#get-an-authorization-token).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Authorization"] = "Bearer " + token;

// Send an audio file by 1024 byte chunks
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

---

### <a name="renew-an-authorization-token"></a>Obnovit autorizační token

Ověřovací token, který vyprší po určité časové období (aktuálně 10 minut). Potřebujete k obnovení tokenu autorizace, než vyprší její platnost.

Následující kód představuje příklad implementace v jazyce C#, jak obnovit ověřovací token, který:

```cs
    /*
     * This class demonstrates how to get a valid O-auth token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
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
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

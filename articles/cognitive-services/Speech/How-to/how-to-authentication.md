---
title: Ověřování pro Zpracování řeči Bingu | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Požádat o ověření, aby používal rozhraní API pro zpracování řeči Bingu
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d1e708ff29293b87935d0d191ba44ad4a11917a0
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965660"
---
# <a name="authenticate-to-the-speech-api"></a>Ověřování pro rozhraní API pro rozpoznávání řeči

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Zpracování řeči Bingu podporuje ověřování pomocí:

- Klíč předplatného.
- Autorizační token

## <a name="use-a-subscription-key"></a>Použít klíč předplatného

Pokud chcete používat službu Speech, musíte se nejdřív přihlásit k odběru rozhraní API pro rozpoznávání řeči, které je součástí Cognitive Services (dříve projekt Oxford). Můžete získat bezplatný zkušební odběr klíčů na stránce [Cognitive Services předplatné](https://azure.microsoft.com/try/cognitive-services/) . Po výběru rozhraní API pro rozpoznávání řeči vyberte **získat klíč rozhraní API** a získejte klíč. Vrátí primární a sekundární klíč. Oba klíče jsou vázané na stejnou kvótu, takže můžete použít kteroukoli z těchto klíčů.

V případě dlouhodobého využití nebo zvýšené kvóty si zaregistrujte [účet Azure](https://azure.microsoft.com/free/).

Chcete-li použít REST API řeči, je nutné předat klíč předplatného do `Ocp-Apim-Subscription-Key` pole v hlavičce požadavku.

Name| Formát| Popis
----|-------|------------
OCP-Apim-Subscription-Key | ASCII | YOUR_SUBSCRIPTION_KEY

Následuje příklad hlavičky žádosti:

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
> Pokud používáte [klientské knihovny](../GetStarted/GetStartedClientLibraries.md) ve své aplikaci, ověřte, že je možné získat autorizační token pomocí klíče předplatného, jak je popsáno v následující části. Klientské knihovny používají klíč předplatného k získání autorizačního tokenu a pak používají token pro ověřování.

## <a name="use-an-authorization-token"></a>Použití autorizačního tokenu

Alternativně můžete použít autorizační token pro ověřování jako kontrolu ověřování nebo autorizace. Chcete-li získat tento token, je nutné nejprve získat klíč předplatného z rozhraní API pro rozpoznávání řeči, jak je popsáno v [předchozí části](#use-a-subscription-key).

### <a name="get-an-authorization-token"></a>Získání autorizačního tokenu

Až budete mít platný klíč předplatného, odešlete požadavek POST do služby tokenu Cognitive Services. V odpovědi obdržíte autorizační token jako JSON Web Token (JWT).

> [!NOTE]
> Platnost tokenu vypršela 10 minut. Postup obnovení tokenu najdete v následující části.

Identifikátor URI služby tokenu je umístěný tady:

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Následující ukázka kódu ukazuje, jak získat přístupový token. Nahraďte `YOUR_SUBSCRIPTION_KEY` vlastním klíčem předplatného:

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

# <a name="curltabcurl"></a>[Curl](#tab/curl)

V příkladu se používá oblé v systému Linux s bash. Pokud není na vaší platformě k dispozici, může být nutné nainstalovat kudrlinkou. Příklad funguje také na Cygwin ve Windows, Gitu bash, zsh a dalších prostředích.

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

Následuje ukázka žádosti POST:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

Pokud ze služby tokenu nemůžete získat autorizační token, ověřte, jestli je klíč předplatného stále platný. Pokud používáte bezplatný zkušební klíč, přejděte na stránku [Cognitive Services předplatné](https://azure.microsoft.com/try/cognitive-services/) , klikněte na Přihlásit se a přihlaste se pomocí účtu, který jste použili pro použití bezplatného zkušebního klíče, a ověřte, jestli vypršela platnost klíče předplatného, nebo překračuje kvótu.

### <a name="use-an-authorization-token-in-a-request"></a>Použití autorizačního tokenu v žádosti

Pokaždé, když zavoláte rozhraní API pro rozpoznávání řeči, musíte v `Authorization` hlavičce předat autorizační token. `Authorization` Hlavička musí obsahovat přístupový token JWT.

Následující příklad ukazuje, jak použít autorizační token při volání REST API řeči.

> [!NOTE]
> Nahraďte `YOUR_AUDIO_FILE` cestou k souboru připraveného obsahu zvuku. Nahraďte `YOUR_ACCESS_TOKEN` autorizačním tokenem, který jste získali v předchozím kroku [získání autorizačního tokenu](#get-an-authorization-token).

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

# <a name="curltabcurl"></a>[Curl](#tab/curl)

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

### <a name="renew-an-authorization-token"></a>Obnovení autorizačního tokenu

Platnost autorizačního tokenu vyprší po určitém časovém období (aktuálně 10 minut). Než vyprší platnost autorizačního tokenu, musíte ho prodloužit.

Následující kód je příklad implementace v C# tématu Postup obnovení autorizačního tokenu:

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

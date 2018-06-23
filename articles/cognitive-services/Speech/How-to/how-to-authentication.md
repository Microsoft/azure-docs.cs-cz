---
title: Ověření pro službu Microsoft řeči | Microsoft Docs
description: Žádost o ověření použít rozhraní API pro rozpoznávání řeči Microsoft
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: e36168cf3ff938af44f1028c2d26fd475d60b148
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342640"
---
# <a name="authenticate-to-the-speech-api"></a>Ověření řeči rozhraní API

Rozpoznávání řeči služba podporuje ověřování pomocí:

- Klíč předplatného.
- Autorizační token.

## <a name="use-a-subscription-key"></a>Použijte klíč předplatného

Pomocí služby rozpoznávání řeči, se musíte se nejdřív přihlásit k řeči rozhraní API, která je součástí služby kognitivní (dříve Oxford projektu). Můžete získat zkušební předplatné klíče z [kognitivní služby předplatného](https://azure.microsoft.com/try/cognitive-services/) stránky. Po výběru rozhraní API pro rozpoznávání řeči, vyberte **získat klíč rozhraní API** k získání klíče. Vrátí primární a sekundární klíč. Oba klíče jsou svázané s stejné kvótu, takže můžete použít buď klíč.

Pro dlouhodobé používání, nebo vyšší kvóty, zaregistrujte si [účet Azure](https://azure.microsoft.com/free/).

Chcete-li používat rozhraní REST API pro rozpoznávání řeči, je potřeba předat klíč předplatného v `Ocp-Apim-Subscription-Key` pole v hlavičce žádosti.

Název| Formát| Popis
----|-------|------------
OCP-Apim-Subscription-Key | ASCII | YOUR_SUBSCRIPTION_KEY

Následuje příklad hlavičku požadavku:

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
> Pokud používáte [klientské knihovny](../GetStarted/GetStartedClientLibraries.md) ve vaší aplikaci, ověřte, že získáte autorizační token s svůj klíč předplatného, jak je popsáno v následující části. Knihovny klienta použít klíč předplatného získat autorizační token a pak pomocí tokenu pro ověřování.

## <a name="use-an-authorization-token"></a>Použít autorizační token

Alternativně můžete autorizační token pro ověřování jako doklad o ověřování/autorizace. Pokud chcete získat tento token, je nutné nejprve získat klíč předplatného z rozhraní API pro rozpoznávání řeči, jak je popsáno v [předcházející části](#use-a-subscription-key).

### <a name="get-an-authorization-token"></a>Získání tokenu ověřování

Až budete mít klíč platné předplatné, odeslat požadavek POST do tokenu služby kognitivní služeb. V odpovědi obdržíte autorizační token jako token JSON Web Token (JWT).

> [!NOTE]
> Token platnost 10 minut. K obnovení tokenu, najdete v následující části.

Služba tokenu URI se nachází zde:

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Následující příklad kódu ukazuje, jak získat přístupový token. Nahraďte `YOUR_SUBSCRIPTION_KEY` s svůj vlastní klíč předplatného:

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

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

Tento příklad používá curl v systému Linux s bash. Pokud není k dispozici na vaši platformu, možná muset nainstalovat curl. Tento příklad funguje taky na emulaci ve Windows, Git Bash, zsh a další součásti pro.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

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

Zde je ukázka požadavku POST:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

Pokud token povolení nelze získat od služby tokenů, zkontrolujte, zda svůj klíč předplatného je stále platný. Pokud používáte bezplatné zkušební verze klíč, přejděte k [kognitivní služby předplatného](https://azure.microsoft.com/try/cognitive-services/) klikněte na "Přihlášení" přihlášení pomocí účtu, který jste použili pro použití bezplatné zkušební verze klíč a zjistěte, zda klíč předplatného vypršela platnost, nebo překračuje kvóty.

### <a name="use-an-authorization-token-in-a-request"></a>Použít autorizační token v požadavku

Pokaždé, když volání rozhraní API pro rozpoznávání řeči, je třeba předat autorizační token v `Authorization` záhlaví. `Authorization` Záhlaví musí obsahovat přístupový token JWT.

Následující příklad ukazuje, jak používat autorizační token při volání rozhraní REST API pro rozpoznávání řeči.

> [!NOTE]
> Nahraďte `YOUR_AUDIO_FILE` s cestou k souboru připraveného obsahu zvuk. Nahraďte `YOUR_ACCESS_TOKEN` autorizačním tokenem, který jste získali v předchozím kroku [získat autorizační token](#get-an-authorization-token).

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

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

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

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

Autorizační token platnost vyprší za určité časové období (aktuálně 10 minut). Budete muset obnovit autorizačním tokenem, než vyprší její platnost.

Následující kód představuje příklad implementace v jazyce C# jak k obnovení tokenu autorizace:

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

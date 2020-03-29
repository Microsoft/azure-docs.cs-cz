---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: dc5e251fee00ee22edb2261c1abd8404714834ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78669260"
---
## <a name="authentication"></a>Ověřování

Každý požadavek vyžaduje hlavičku autorizace. Tato tabulka znázorňuje, která záhlaví jsou podporována pro každou službu:

| Podporovaná záhlaví autorizací | Převod řeči na text | Převod textu na řeč |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Ano | Ne |
| Povolení: Nosič | Ano | Ano |

Při použití `Ocp-Apim-Subscription-Key` záhlaví musíte zadat pouze klíč předplatného. Například:

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Při použití `Authorization: Bearer` záhlaví, budete muset podat žádost `issueToken` ke koncovému bodu. V tomto požadavku vyměníte klíč předplatného za přístupový token, který je platný po dobu 10 minut. V několika dalších částech se dozvíte, jak získat token a použít token.

### <a name="how-to-get-an-access-token"></a>Jak získat přístupový token

Chcete-li získat přístupový token, budete muset `issueToken` požádat o `Ocp-Apim-Subscription-Key` koncový bod pomocí klíče a předplatného.

Koncový `issueToken` bod má tento formát:

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Nahraďte `<REGION_IDENTIFIER>` identifikátorem, který odpovídá oblasti předplatného z této tabulky:

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

Tyto ukázky slouží k vytvoření požadavku na přístupový token.

#### <a name="http-sample"></a>Ukázka protokolu HTTP

Tento příklad je jednoduchý požadavek HTTP získat token. Nahraďte `YOUR_SUBSCRIPTION_KEY` pomocí klíče předplatného služby Speech Service. Pokud vaše předplatné není v oblasti USA `Host` – západ, nahraďte záhlaví názvem hostitele oblasti.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Tělo odpovědi obsahuje přístupový token ve formátu JSON Web Token (JWT).

#### <a name="powershell-sample"></a>Ukázka PowerShellu

Tento příklad je jednoduchý skript prostředí PowerShell získat přístupový token. Nahraďte `YOUR_SUBSCRIPTION_KEY` pomocí klíče předplatného služby Speech Service. Ujistěte se, že používáte správný koncový bod pro oblast, která odpovídá vašemu předplatnému. Tento příklad je aktuálně nastaven na západní USA.

```powershell
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

#### <a name="curl-sample"></a>ukázka cURL

cURL je nástroj příkazového řádku dostupný v Linuxu (a v podsystému Windows pro Linux). Tento příkaz cURL ukazuje, jak získat přístupový token. Nahraďte `YOUR_SUBSCRIPTION_KEY` pomocí klíče předplatného služby Speech Service. Ujistěte se, že používáte správný koncový bod pro oblast, která odpovídá vašemu předplatnému. Tento příklad je aktuálně nastaven na západní USA.

```console
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Ukázka v jazyce C#

Tato třída C# ukazuje, jak získat přístupový token. Předejte klíč předplatného služby Speech Service při vytváření instanování třídy. Pokud vaše předplatné není v oblasti USA – `FetchTokenUri` západ, změňte hodnotu aplikace, která odpovídá oblasti předplatného.

```csharp
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

#### <a name="python-sample"></a>Ukázka Pythonu

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'


def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>Použití přístupového tokenu

Přístupový token by měl být `Authorization: Bearer <TOKEN>` odeslán službě jako záhlaví. Každý přístupový token je platný po dobu 10 minut. Můžete získat nový token kdykoli, ale minimalizovat síťový provoz a latence, doporučujeme používat stejný token po dobu devíti minut.

Tady je ukázkový požadavek HTTP pro rozhraní REST API pro převod textu na řeč:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```

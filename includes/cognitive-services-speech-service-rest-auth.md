---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: dc5e251fee00ee22edb2261c1abd8404714834ba
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78669260"
---
## <a name="authentication"></a>Ověřování

Každá žádost vyžaduje autorizační hlavičku. Tato tabulka ukazuje, které hlavičky jsou podporovány pro každou službu:

| Podporované autorizační hlavičky | Převod řeči na text | Převod textu na řeč |
|------------------------|----------------|----------------|
| OCP-Apim-Subscription-Key | Ano | Ne |
| Autorizace: nosiče | Ano | Ano |

Když použijete hlavičku `Ocp-Apim-Subscription-Key`, budete muset zadat jenom svůj klíč předplatného. Příklad:

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Při použití hlavičky `Authorization: Bearer` je nutné vytvořit požadavek na koncový bod `issueToken`. V této žádosti o výměnu váš klíč předplatného pro přístupový token, který je platný 10 minut. V následujících částech se dozvíte, jak získat token a použít token.

### <a name="how-to-get-an-access-token"></a>Jak získat přístupový token

K získání přístupového tokenu budete muset na `issueToken` koncový bod vytvořit žádost pomocí `Ocp-Apim-Subscription-Key` a vašeho klíče předplatného.

`issueToken` koncový bod má tento formát:

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Nahraďte `<REGION_IDENTIFIER>` identifikátorem, který odpovídá oblasti vašeho předplatného z této tabulky:

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

Tyto ukázky použijte při vytváření žádosti o token přístupu.

#### <a name="http-sample"></a>Ukázka protokolu HTTP

V tomto příkladu je jednoduché požadavku HTTP k získání tokenu. Nahraďte `YOUR_SUBSCRIPTION_KEY` klíčem předplatného služby Speech. Pokud vaše předplatné není v oblasti Západní USA, nahraďte `Host` hlavičku názvem hostitele vaší oblasti.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Tělo odpovědi obsahuje přístupový token ve formátu JSON Web Token (JWT).

#### <a name="powershell-sample"></a>Ukázka PowerShellu

V tomto příkladu je jednoduchý skript prostředí PowerShell k získání přístupového tokenu. Nahraďte `YOUR_SUBSCRIPTION_KEY` klíčem předplatného služby Speech. Nezapomeňte použít správný koncový bod pro oblast, která odpovídá vašeho předplatného. V tomto příkladu je aktuálně nastavený na západní USA.

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

#### <a name="curl-sample"></a>Ukázka cURL

cURL je nástroj příkazového řádku jsou k dispozici v systému Linux (a v subsystému Windows pro Linux). Tento příkaz cURL znázorňuje, jak získat přístupový token. Nahraďte `YOUR_SUBSCRIPTION_KEY` klíčem předplatného služby Speech. Nezapomeňte použít správný koncový bod pro oblast, která odpovídá vašeho předplatného. V tomto příkladu je aktuálně nastavený na západní USA.

```console
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Ukázka v jazyce C#

To C# třídy ukazuje, jak získat přístupový token. Předejte klíč předplatného Speech Service při vytváření instance třídy. Pokud vaše předplatné není v oblasti Západní USA, změňte hodnotu `FetchTokenUri` tak, aby odpovídala oblasti vašeho předplatného.

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

Přístupový token by měl být službě odeslán jako `Authorization: Bearer <TOKEN>` záhlaví. Každý přístupový token je platný 10 minut. Kdykoli můžete získat nový token, ale pokud chcete minimalizovat síťový provoz a latence, doporučujeme použít stejný token devět minut.

Tady je ukázka požadavku HTTP pro převod textu na řeč rozhraní REST API:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```

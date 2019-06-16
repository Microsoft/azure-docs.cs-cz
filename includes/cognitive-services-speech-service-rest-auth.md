---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: 9cad860b8808dd2682995768c282d8376ab5d9be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66145381"
---
## <a name="authentication"></a>Authentication

Každý požadavek vyžaduje také hlavičku ověřování. Tato tabulka ukazuje, které hlavičky jsou podporovány pro každou službu:

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

[!INCLUDE [](./cognitive-services-speech-service-endpoints-token-service.md)]

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

Text odpovědi obsahuje přístupový token ve formátu JSON Web Token (JWT).

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

#### <a name="python-sample"></a>Ukázky Pythonu

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

Přístupový token odesílaných do služby, jako `Authorization: Bearer <TOKEN>` záhlaví. Každý přístupový token je platný 10 minut. Kdykoli můžete získat nový token, ale pokud chcete minimalizovat síťový provoz a latence, doporučujeme použít stejný token devět minut.

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

---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 01/08/2021
ms.author: erhopf
ms.openlocfilehash: 22127f81d871fe333750020196540db17e7544f7
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033439"
---
## <a name="authentication"></a>Authentication

Každá žádost vyžaduje autorizační hlavičku. Tato tabulka ukazuje, které hlavičky jsou pro každou službu podporované:

| Podporované autorizační hlavičky | Převod řeči na text | Převod textu na řeč |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | Ano | Ano |
| Autorizace: nosič | Ano | Ano |

Když použijete `Ocp-Apim-Subscription-Key` hlavičku, budete muset zadat svůj klíč předplatného. Příklad:

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Když použijete `Authorization: Bearer` hlavičku, je nutné, abyste si nahlásili požadavek na `issueToken` koncový bod. V této žádosti vyměňujete klíč předplatného pro přístupový token, který je platný po dobu 10 minut. V následujících částech se dozvíte, jak získat token a použít token.

### <a name="how-to-get-an-access-token"></a>Získání přístupového tokenu

K získání přístupového tokenu budete muset na `issueToken` koncový bod vytvořit žádost pomocí `Ocp-Apim-Subscription-Key` klíče a vašeho předplatného.

`issueToken`Koncový bod má tento formát:

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

Nahraďte `<REGION_IDENTIFIER>` identifikátorem, který odpovídá oblasti vašeho předplatného z této tabulky:

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

Pomocí těchto ukázek můžete vytvořit žádost o přístupový token.

#### <a name="http-sample"></a>Ukázka HTTP

V tomto příkladu je jednoduchý požadavek HTTP na získání tokenu. Nahraďte `YOUR_SUBSCRIPTION_KEY` klíčovým předplatným služby Speech. Pokud vaše předplatné není v oblasti Západní USA, nahraďte `Host` hlavičku názvem hostitele vaší oblasti.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Tělo odpovědi obsahuje přístupový token ve formátu JSON Web Token (JWT).

#### <a name="powershell-sample"></a>Ukázka PowerShellu

Tento příklad představuje jednoduchý skript prostředí PowerShell pro získání přístupového tokenu. Nahraďte `YOUR_SUBSCRIPTION_KEY` klíčovým předplatným služby Speech. Ujistěte se, že používáte správný koncový bod pro oblast, která odpovídá vašemu předplatnému. Tento příklad je aktuálně nastaven na Západní USA.

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

#### <a name="curl-sample"></a>Ukázka kudrlinkou

kudrlinkou je nástroj příkazového řádku, který je dostupný v systému Linux (a v subsystému Windows pro Linux). Tento příkaz složeného příkazu ukazuje, jak získat přístupový token. Nahraďte `YOUR_SUBSCRIPTION_KEY` klíčovým předplatným služby Speech. Ujistěte se, že používáte správný koncový bod pro oblast, která odpovídá vašemu předplatnému. Tento příklad je aktuálně nastaven na Západní USA.

```console
curl -v -X POST \
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Ukázka v jazyce C#

Tato třída jazyka C# ukazuje, jak získat přístupový token. Při vytváření instance třídy předejte klíč předplatného služby Speech. Pokud vaše předplatné není v oblasti Západní USA, změňte hodnotu `FetchTokenUri` tak, aby odpovídala oblasti vašeho předplatného.

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

### <a name="how-to-use-an-access-token"></a>Používání přístupového tokenu

Přístupový token by měl být službě odeslán jako `Authorization: Bearer <TOKEN>` Hlavička. Každý přístupový token je platný po dobu 10 minut. Nový token můžete kdykoli získat, ale k minimalizaci síťového provozu a latence doporučujeme použít stejný token po dobu devíti minut.

Tady je ukázka požadavku HTTP na REST API převodu řeči na text pro krátký zvuk:

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```

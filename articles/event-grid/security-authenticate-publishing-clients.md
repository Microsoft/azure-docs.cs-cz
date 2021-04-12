---
title: Ověřování klientů, kteří publikují události, aby Event Grid vlastní témata nebo domény
description: Tento článek popisuje různé způsoby ověřování klientů, kteří publikují události, aby Event Grid vlastní témata.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c415b7e1bb6bd7a2116da82c7d8f1de205009d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94886256"
---
# <a name="authenticate-publishing-clients-azure-event-grid"></a>Ověřování klientů publikování (Azure Event Grid)
Tento článek poskytuje informace o ověřování klientů, které publikují události pro Azure Event Grid témat nebo domén pomocí **přístupového klíče** nebo tokenu **sdíleného přístupového podpisu (SAS)** . Doporučujeme použít token SAS, ale ověřování pomocí klíče poskytuje jednoduché programování a je kompatibilní s mnoha existujícími vydavateli webhooků.  

## <a name="authenticate-using-an-access-key"></a>Ověřování pomocí přístupového klíče
Ověřování přístupového klíče je nejjednodušší forma ověřování. Přístupovou klávesu můžete předat jako hlavičku protokolu HTTP nebo parametr dotazu adresy URL. 

### <a name="access-key-in-a-http-header"></a>Přístupový klíč v hlavičce HTTP
Předejte přístupový klíč jako hodnotu pro hlavičku HTTP: `aeg-sas-key` .

```
aeg-sas-key: XXXXXXXXXXXXXXXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="access-key-as-a-query-parameter"></a>Přístupový klíč jako parametr dotazu
Můžete také zadat `aeg-sas-key` jako parametr dotazu. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/api/events?aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

Pokyny k získání přístupových klíčů k tématu nebo doméně najdete v tématu [získání přístupových klíčů](get-access-keys.md).

## <a name="authenticate-using-a-sas-token"></a>Ověřování pomocí tokenu SAS
Tokeny SAS pro prostředek Event Grid zahrnují prostředky, čas vypršení platnosti a signaturu. Formát tokenu SAS je: `r={resource}&e={expiration}&s={signature}` .

Prostředek je cesta k tématu Event gridu, do kterého odesíláte události. Například platná cesta prostředku je: `https://<yourtopic>.<region>.eventgrid.azure.net/api/events` . Pokud chcete zobrazit všechny podporované verze rozhraní API, přečtěte si téma [typy prostředků Microsoft. EventGrid](/azure/templates/microsoft.eventgrid/allversions). 

Nejprve programově vygenerujte token SAS a pak použijte `aeg-sas-token` záhlaví nebo `Authorization SharedAccessSignature` hlavičku k ověření pomocí Event Grid. 

### <a name="generate-sas-token-programmatically"></a>Programové generování tokenu SAS
Následující příklad vytvoří token SAS pro použití s Event Grid:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

```python
def generate_sas_token(uri, key, expiry=3600):
    ttl = datetime.datetime.utcnow() + datetime.timedelta(seconds=expiry)
    encoded_resource = urllib.parse.quote_plus(uri)
    encoded_expiration_utc = urllib.parse.quote_plus(ttl.isoformat())

    unsigned_sas = f'r={encoded_resource}&e={encoded_expiration_utc}'
    signature = b64encode(HMAC(b64decode(key), unsigned_sas.encode('utf-8'), sha256).digest())
    encoded_signature = urllib.parse.quote_plus(signature)
    
    token = f'r={encoded_resource}&e={encoded_expiration_utc}&s={encoded_signature}'

    return token
```

### <a name="using-aeg-sas-token-header"></a>Použití hlavičky AEG-SAS-token
Tady je příklad předání tokenu SAS jako hodnoty pro `aeg-sas-token` hlavičku. 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>Používání autorizační hlavičky
Tady je příklad předání tokenu SAS jako hodnoty pro `Authorization` hlavičku. 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>Další kroky
Další informace o ověřování pomocí obslužných rutin událostí pro doručování událostí najdete v tématu [ověřování událostí](security-authentication.md) . 

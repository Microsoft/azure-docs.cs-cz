---
title: Generovat token zabezpečení pro přístup k úložišti Náhled plug and play IoT | Dokumenty společnosti Microsoft
description: Vygenerujte token sdíleného přístupového podpisu, který se použije při programovém přístupu k úložišti modelu Náhled technologie IoT Plug and Play.
author: Philmea
ms.author: philmea
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f008627317588467d731ccc03aec7738f58e46e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159196"
---
# <a name="generate-sas-token"></a>Generovat token SAS

Tento návod vám ukáže, jak programově generovat token sdíleného přístupového podpisu (SAS) pro použití s úložištěm úložiště modelu IoT Plug and Play Preview.

## <a name="python"></a>Python

Následující úryvek ukazuje, jak generovat token SAS pomocí Pythonu:

```python
from base64 import b64decode, b64encode
from hashlib import sha256
from hmac import digest
from time import time
from urllib.parse import quote_plus, urlencode

def calculate_sas_token(hostname, repo_id, key_name, key, expiry_in_second):
    expire = int(time() + expiry_in_second)
    sign_value = "{0}\n{1}\n{2}".format(repo_id, quote_plus(hostname), expire)
    sig = b64encode(digest(b64decode(key), sign_value.encode("utf-8"), sha256))
    token = "SharedAccessSignature " + urlencode({
        "sr": hostname, 
        "sig": sig,
        "se": str(expire),
        "skn": key_name,
        "rid": repo_id
        })
    return token
```

## <a name="c"></a>C\#

Následující úryvek ukazuje, jak generovat token SAS pomocí C\#:

```csharp
public static string generateSasToken(string hostName, string repoId, string key, string keyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = repoId + "\n" + WebUtility.UrlEncode(hostName) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(
        CultureInfo.InvariantCulture,
        "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}&rid={4}",
        WebUtility.UrlEncode(hostName),
        WebUtility.UrlEncode(signature),
        expiry,
        keyName,
        repoId);

    return token;
}
```

## <a name="use-the-sas-token"></a>Použití tokenu SAS

Po vygenerování tokenu SAS jej můžete použít k vytvoření požadavku HTTP POST. Například:

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

Pokud klientovi přidáte token SAS, klient nemá primární klíč prostředku a nelze jej stornovat hash k jeho získání. Token SAS vám dává kontrolu nad tím, k čemu má klient přístup a na jak dlouho. Změníte-li primární klíč v zásadě, všechny tokeny SAS vytvořené z ní jsou zneplatněny.

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o generování tokenů zabezpečení pro přístup k modelu úložiště modelu IoT Plug and Play Preview, je dalším krokem další informace v [průvodci vývojářem pro modelování náhledu IoT Plug and Play](concepts-developer-guide.md).

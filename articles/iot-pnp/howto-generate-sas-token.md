---
title: Generovat token zabezpečení pro přístup do úložiště IoT technologie Plug and Play Preview | Microsoft Docs
description: Vygenerujte token sdíleného přístupového podpisu, který se použije při programovém přístupu do úložiště modelu IoT technologie Plug and Play Preview.
author: Philmea
ms.author: philmea
ms.date: 08/06/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f6c4f5b9784eeff9d03b6e93953674736fb78c6c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976022"
---
# <a name="generate-sas-token"></a>Generovat token SAS

V této příručce se dozvíte, jak programově generovat token sdíleného přístupového podpisu (SAS) pro použití s rozhraními API úložiště modelu IoT technologie Plug and Play Preview.

## <a name="python"></a>Python

Následující fragment kódu ukazuje, jak vygenerovat token SAS pomocí Pythonu:

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

Následující fragment kódu ukazuje, jak vygenerovat token SAS pomocí\#jazyka C:

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

## <a name="use-the-sas-token"></a>Použít token SAS

Po vygenerování tokenu SAS ho můžete použít k vytvoření požadavku HTTP POST. Například:

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

Pokud klientovi udělíte token SAS, klient nemá primární klíč prostředku a nemůže hodnotu hash vrátit, aby ho získal. Token SAS vám dává kontrolu nad tím, k čemu má klient přístup, a za jak dlouhou dobu. Když změníte primární klíč v zásadě, všechny tokeny SAS, které jsou z něho vytvořené, se zruší.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili generovat tokeny zabezpečení, které se mají použít pro přístup k modelům úložiště IoT technologie Plug and Play ve verzi Preview, je doporučený další krok, kde najdete další informace v [příručce pro vývojáře modelování ve službě iot technologie Plug and Play Preview](concepts-developer-guide.md).

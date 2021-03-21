---
title: Azure Active Directory REST API-test pomocí Fiddler
description: Použití Fiddler k otestování konfigurace aplikace Azure REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 1142aa25212d87c5484963cda4e172df3d1fbafc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932604"
---
# <a name="test-the-azure-app-configuration-rest-api-using-fiddler"></a>Testování REST API konfigurace aplikace Azure pomocí Fiddler

K otestování REST API pomocí [Fiddler](https://www.telerik.com/fiddler)budete muset do svých požadavků zahrnout hlavičky HTTP vyžadované pro [ověřování](./rest-api-authentication-hmac.md) . Tady je postup konfigurace Fiddler pro testování REST API a automatické generování ověřovacích hlaviček:

1. Ujistěte se, že TLS 1,2 je povolený protokol:
    1. Přejít na   >  **Možnosti** nástroje  >  **https**).
    1. Ujistěte se, že je zaškrtnuté políčko **dešifrovat přenos HTTPS** .
    1. V seznamu protokolů přidejte **TLS 1.2** , pokud není k dispozici.
1. Otevřete **Editor skriptů Fiddler** nebo v Fiddler stiskněte **CTRL + R** .
1. Před funkci přidejte do třídy následující kód. `Handlers` `OnBeforeRequest`

    ```js
    static function SignRequest(oSession: Session, credential: String, secret: String) {
        var utcNow = DateTimeOffset.UtcNow.ToString("r", System.Globalization.DateTimeFormatInfo.InvariantInfo);
        var contentHash = ComputeSHA256Hash(oSession.RequestBody);
        var stringToSign = oSession.RequestMethod.ToUpperInvariant() + "\n" + oSession.PathAndQuery + "\n" + utcNow +";" + oSession.hostname + ";" + contentHash;
        var signature = ComputeHMACHash(secret, stringToSign);

        oSession.oRequest.headers["x-ms-date"] = utcNow;
        oSession.oRequest.headers["x-ms-content-sha256"] = contentHash;
        oSession.oRequest.headers["Authorization"] = "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=" + signature;
    }

    static function ComputeSHA256Hash(content: Byte[]) {
        var sha256 = System.Security.Cryptography.SHA256.Create();
        try {
            return Convert.ToBase64String(sha256.ComputeHash(content));
        }
        finally {
            sha256.Dispose();
        }
    }

    static function ComputeHMACHash(secret: String, content: String) {
        var hmac = new System.Security.Cryptography.HMACSHA256(Convert.FromBase64String(secret));
        try {
            return Convert.ToBase64String(hmac.ComputeHash(System.Text.Encoding.ASCII.GetBytes(content)));
        }
        finally {
            hmac.Dispose();
        }
    }
    ```

1. Na konec funkce přidejte následující kód `OnBeforeRequest` . Aktualizujte přístupový klíč tak, jak je znázorněno v komentáři TODO.

    ```js
    if (oSession.isFlagSet(SessionFlags.RequestGeneratedByFiddler) &&
        oSession.hostname.EndsWith(".azconfig.io", StringComparison.OrdinalIgnoreCase)) {

        // TODO: Replace the following placeholders with your access key
        var credential = "<Credential>"; // Id
        var secret = "<Secret>"; // Value

        SignRequest(oSession, credential, secret);
    }
    ```
1. Použití [skladatele Fiddler](https://docs.telerik.com/fiddler/Generate-Traffic/Tasks/CreateNewRequest) k vygenerování a odeslání žádosti

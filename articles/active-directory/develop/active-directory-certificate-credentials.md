---
title: Certifikát přihlašovacích údajů ve službě Azure AD | Microsoft Docs
description: Tento článek popisuje registrace a používání certifikát přihlašovacích údajů pro ověřování aplikace
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: c782429ac2d8ee030ca8b589b4241242c7b101d6
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="certificate-credentials-for-application-authentication"></a>Přihlašovací údaje certifikátu pro ověřování aplikace

Azure Active Directory umožňuje aplikacím používat svoje vlastní přihlašovací údaje pro ověřování. Například v toku udělení pověření klienta OAuth 2.0 ([v1](active-directory-protocols-oauth-service-to-service.md), [v2](active-directory-v2-protocols-oauth-client-creds.md)) a tok On-Behalf-Of ([v1](active-directory-protocols-oauth-on-behalf-of.md), [v2](active-directory-v2-protocols-oauth-on-behalf-of.md)).
Jednu formu přihlašovacích údajů, které je možné je Token(JWT) webové JSON kontrolní výraz systému podepsané certifikátem, který vlastní aplikace.

## <a name="format-of-the-assertion"></a>Formát kontrolní výraz
Vypočítat kontrolní výraz, budete pravděpodobně chtít použít jednu z dalších [webových tokenů JSON](https://jwt.ms/) knihovny v jazyce podle svého výběru. Informace, které token je:

#### <a name="header"></a>Záhlaví

| Parametr |  Poznámka |
| --- | --- |
| `alg` | Musí být **RS256** |
| `typ` | Musí být **JWT** |
| `x5t` | Musí být kryptografický otisk certifikátu X.509 SHA-1 |

#### <a name="claims-payload"></a>Deklarace identity (datovou část)

| Parametr |  Poznámka |
| --- | --- |
| `aud` | Cílová skupina: By měla být  **https://login.microsoftonline.com/ *tenant_Id*  /oauth2/token** |
| `exp` | Datum vypršení platnosti: datum vypršení platnosti tokenu. Čas je reprezentován jako počet sekund od 1. ledna 1970 (pod hodnotou 1970-01-01T0:0:0Z) UTC až do okamžiku vypršení platnosti tokenu.|
| `iss` | Vystavitel: by měla být client_id (ID aplikace služby klienta) |
| `jti` | Identifikátor GUID: JWT ID |
| `nbf` | Neplatný před: datum, před kterou nelze použít daný token. Čas je reprezentován jako počet sekund od 1. ledna 1970 (pod hodnotou 1970-01-01T0:0:0Z) UTC až do okamžiku byl token vydán. |
| `sub` | Předmět: jako u `iss`, by měla být client_id (ID aplikace služby klienta) |

#### <a name="signature"></a>Podpis

Podpis je počítaný použití certifikátu, jak je popsáno v [JSON Web Token RFC7519 specifikace](https://tools.ietf.org/html/rfc7519)

### <a name="example-of-a-decoded-jwt-assertion"></a>Příklad dekódované assertion JWT

```
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,  
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"

```

### <a name="example-of-an-encoded-jwt-assertion"></a>Příklad kódovaného JWT kontrolní výrazy

Následující řetězec je příkladem kódovaného kontrolní výraz. Pokud se podíváte pečlivě, jste si všimli tři části oddělený tečkami (.).
V první části kóduje záhlaví, druhý datové části a poslední je podpis počítaný s certifikáty z obsahu první dva oddíly.
```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

### <a name="register-your-certificate-with-azure-ad"></a>Zaregistrujte svůj certifikát s Azure AD

Certifikát přihlašovacích údajů můžete přidružit klientská aplikace ve službě Azure AD pomocí portálu Azure pomocí kteréhokoli z následujících metod:

**Nahrání souboru certifikátu**

V registraci aplikace Azure pro klientskou aplikaci, klikněte na **nastavení**, klikněte na tlačítko **klíče** a pak klikněte na **nahrát veřejný klíč**. Vyberte soubor certifikátu, který chcete nahrát a klikněte na tlačítko **Uložit**. Po uložení, nahrání certifikátu a kryptografický otisk, počáteční datum a vyprší platnost hodnoty jsou zobrazeny. 

**Aktualizace manifest aplikace**

S podržení certifikát, je třeba k výpočtu:

- `$base64Thumbprint`, což je Base64, pomocí kódování hodnota Hash certifikátu
- `$base64Value`, což je Base64, pomocí kódování nezpracovaná data certifikátu

Také musíte zadat identifikátor GUID k identifikaci klíče v manifest aplikace (`$keyId`).

V registraci aplikace Azure pro klientskou aplikaci, otevřete manifest aplikace a nahradit *keyCredentials* vlastnost s vaší nové informace o certifikátu pomocí následující schématu:

```
"keyCredentials": [
    {
        "customKeyIdentifier": "$base64Thumbprint",
        "keyId": "$keyid",
        "type": "AsymmetricX509Cert",
        "usage": "Verify",
        "value":  "$base64Value"
    }
]
```

Chcete uložit změny do manifestu aplikace a nahrajte do Azure AD. Vlastnost keyCredentials je více hodnot, takže nahrajete více certifikátů pro širší správu klíčů.

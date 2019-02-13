---
title: Certifikát přihlašovacích údajů ve službě Azure AD | Dokumentace Microsoftu
description: Tento článek popisuje registraci a používání certifikát přihlašovacích údajů pro ověřování aplikace
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: celested
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4ee1ce56723e4a2c9ab80c12456bbc1b66f6d5
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56162794"
---
# <a name="certificate-credentials-for-application-authentication"></a>Certifikát přihlašovacích údajů pro ověřování aplikace

Azure Active Directory (Azure AD) umožňuje aplikaci pomocí vlastních přihlašovacích údajů pro ověřování, například v toku udělení přihlašovacích údajů klienta OAuth 2.0 ([v1.0](v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) a On-Behalf-Of tok ([v1.0](v1-oauth2-on-behalf-of-flow.md), [v2.0](v2-oauth2-on-behalf-of-flow.md)).

Jednu formu přihlašovacích údajů, které aplikace můžete použít pro ověřování je kontrolní výraz JSON Web Token(JWT) podepsané certifikátem, který vlastní aplikace.

## <a name="assertion-format"></a>Formát kontrolního výrazu
Pro výpočet kontrolního výrazu, můžete použít jednu z dalších [webového tokenu JSON](https://jwt.ms/) knihovny v jazyce podle vašeho výběru. Informace v tokenu jsou následující:

### <a name="header"></a>Hlavička

| Parametr |  Poznámka |
| --- | --- |
| `alg` | By měl být **RS256** |
| `typ` | By měl být **tokenů JWT** |
| `x5t` | By měl být kryptografický otisk certifikátu X.509 SHA-1 |

### <a name="claims-payload"></a>Deklarace identity (datová část)

| Parametr |  Poznámky |
| --- | --- |
| `aud` | Cílové skupiny: By měl být  **https://login.microsoftonline.com/ *tenant_Id*  /oauth2/token** |
| `exp` | Datum vypršení platnosti: datum, kdy vyprší platnost tokenu. Čas je vyjádřen takto: počet sekund od 1. ledna 1970 (1970-01-01T0:0:0Z) UTC až do okamžiku vypršení platnosti tokenu.|
| `iss` | Vystavitel: by měl být client_id (ID aplikace pro službu klienta) |
| `jti` | Identifikátor GUID: ID tokenů JWT |
| `nbf` | Ne dříve než: datum, před kterým nelze použít token. Čas je vyjádřen takto: počet sekund od 1. ledna 1970 (1970-01-01T0:0:0Z) UTC až do doby byl token vydán. |
| `sub` | Předmět: Jako u `iss`, by měl být client_id (ID aplikace pro službu klienta) |

### <a name="signature"></a>Podpis

Podpis je vypočítán s použitím certifikátu, jak je popsáno v [JSON Web Token RFC7519 specifikace](https://tools.ietf.org/html/rfc7519)

## <a name="example-of-a-decoded-jwt-assertion"></a>Příklad kontrolní výraz dekódovaný tokenů JWT

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

## <a name="example-of-an-encoded-jwt-assertion"></a>Příklad kódovaného kontrolní výraz tokenů JWT

Tento řetězec je příkladem kódovaného kontrolní výraz. Pokud jste pečlivě, Všimněte si tři oddíly oddělené tečkami (.):
* První část kóduje záhlaví
* Druhá část kóduje datové části
* Poslední část je podpisu vypočtenému pomocí certifikátů z obsahu první dva oddíly

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-azure-ad"></a>Registrace certifikátu v Azure AD

Certifikát přihlašovacích údajů můžete přidružit klientská aplikace ve službě Azure AD na webu Azure portal pomocí kteréhokoli z následujících metod:

### <a name="uploading-the-certificate-file"></a>Nahrání souboru certifikátu

V registraci aplikace Azure pro klientskou aplikaci:
1. Vyberte **Nastavení > klíče** a pak vyberte **nahrát veřejný klíč**. 
2. Vyberte soubor certifikátu, který chcete nahrát.
3. Vyberte **Uložit**. 
   
   Po uložení, nahrání certifikátu a kryptografický otisk, počáteční datum a hodnot vypršení platnosti jsou zobrazeny. 

### <a name="updating-the-application-manifest"></a>Aktualizuje se manifest aplikace

S blokování certifikátu, je třeba compute:

- `$base64Thumbprint`, což je base64 kódování hodnota hash certifikátu
- `$base64Value`, což je base64 kódování nezpracovaná data certifikátu

Budete taky muset zadat identifikátor GUID k identifikaci klíč v manifestu aplikace (`$keyId`).

V registraci aplikace Azure pro klientskou aplikaci:
1. Otevření manifestu aplikace.
2. Nahradit *keyCredentials* vlastnost s použitím následujícího schématu informacemi o novém certifikát.

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
3. Chcete uložit změny do manifestu aplikace a pak nahrajte manifest do služby Azure AD. 

   `keyCredentials` Vlastnost je více Vážíme si toho, takže můžete nahrát více certifikátů pro bohatší správu klíčů.
   
## <a name="code-sample"></a>Ukázka kódu

Vzorový kód na [ověřování v Azure AD aplikace démonů s certifikáty](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) ukazuje, jak aplikace používá svoje vlastní přihlašovací údaje pro ověřování. Také ukazuje, jak můžete [vytvořit certifikát podepsaný svým držitelem](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential#create-a-self-signed-certificate) pomocí `New-SelfSignedCertificate` příkaz prostředí Powershell. Můžete také využít a použít [skriptů pro vytvoření aplikace](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/AppCreationScripts/AppCreationScripts.md) k vytvoření certifikátů, compute kryptografický otisk a tak dále.

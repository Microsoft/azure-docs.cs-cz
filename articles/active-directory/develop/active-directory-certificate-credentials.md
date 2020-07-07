---
title: Přihlašovací údaje k certifikátu platformy Microsoft Identity Platform
titleSuffix: Microsoft identity platform
description: Tento článek popisuje registraci a použití přihlašovacích údajů certifikátu pro ověřování aplikací.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 47a35f70251622674205a28af9b7cc64132d0530
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82690284"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Přihlašovací údaje ověřovacího certifikátu aplikace Microsoft Identity Platform

Platforma Microsoft Identity umožňuje aplikaci používat pro ověřování vlastní přihlašovací údaje, například v [přihlašovacích údajích klienta OAuth 2,0 udělení flowv 2.0](v2-oauth2-client-creds-grant-flow.md) a [tok](v2-oauth2-on-behalf-of-flow.md)za běhu).

Jedna forma přihlašovacích údajů, kterou může aplikace použít k ověřování, je kontrolní výraz JSON Web Token (JWT) podepsaný certifikátem, který vlastní aplikace.

## <a name="assertion-format"></a>Formát kontrolního výrazu
Platforma Microsoft identity pro výpočet kontrolního výrazu, můžete použít jednu z mnoha knihoven [JSON web token](https://jwt.ms/) v jazyce podle vašeho výběru. Tato informace je převedená tímto tokenem:

### <a name="header"></a>Záhlaví

| Parametr |  Přeznačit |
| --- | --- |
| `alg` | By měl být **RS256** |
| `typ` | Měla by být **JWT** |
| `x5t` | Měl by se jednat o kryptografický otisk SHA-1 certifikátu X. 509. |

### <a name="claims-payload"></a>Deklarace identity (datová část)

| Parametr |  Poznámky |
| --- | --- |
| `aud` | Cílová skupina: by měla být ** https://login.microsoftonline.com/ *tenant_Id*/OAuth2/token** |
| `exp` | Datum vypršení platnosti: datum vypršení platnosti tokenu. Čas je reprezentován jako počet sekund od 1. ledna 1970 (1970-01-01T0:0: 0Z) UTC až do doby, kdy platnost tokenu vyprší.|
| `iss` | Vystavitel: mělo by se jednat o client_id (ID aplikace služby klienta). |
| `jti` | GUID: ID JWT |
| `nbf` | Ne před: datum, před kterým se token nedá použít. Čas je reprezentován jako počet sekund od 1. ledna 1970 (1970-01-01T0:0: 0Z) UTC až do doby, kdy byl token vydán. |
| `sub` | Předmět: jako pro `iss` by měl být client_id (ID aplikace služby klienta). |

### <a name="signature"></a>Podpis

Podpis se počítá s použitím certifikátu, jak je popsáno ve [specifikaci JSON web token RFC7519 Specification](https://tools.ietf.org/html/rfc7519) .

## <a name="example-of-a-decoded-jwt-assertion"></a>Příklad dekódového kontrolního výrazu JWT

```JSON
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

## <a name="example-of-an-encoded-jwt-assertion"></a>Příklad kódovaného kontrolního výrazu JWT

Následující řetězec je příkladem kódovaného kontrolního výrazu. Pokud pečlivě prohledáváte, všimnete si tří sekcí oddělených tečkami (.):
* První oddíl kóduje hlavičku.
* Druhá část kóduje datovou část.
* Poslední část je podpis vypočítanýý pomocí certifikátů z obsahu prvních dvou částí.

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Registrace certifikátu s platformou Microsoft identity

Přihlašovací údaje certifikátu můžete přidružit k klientské aplikaci na platformě Microsoft identity prostřednictvím Azure Portal pomocí kterékoli z následujících metod:

### <a name="uploading-the-certificate-file"></a>Nahrává se soubor certifikátu.

V registraci aplikace Azure pro klientskou aplikaci:
1. Vyberte **certifikáty & tajných**kódů.
2. Klikněte na **nahrát certifikát** a vyberte soubor certifikátu, který se má nahrát.
3. Klikněte na tlačítko **Add** (Přidat).
  Po nahrání certifikátu se zobrazí miniatura, datum zahájení a hodnoty vypršení platnosti.

### <a name="updating-the-application-manifest"></a>Aktualizace manifestu aplikace

Po uložení certifikátu je potřeba vypočítat:

- `$base64Thumbprint`, což je kódování Base64 hodnoty hash certifikátu
- `$base64Value`, což je kódování Base64 nezpracovaných dat certifikátu

Také je nutné zadat identifikátor GUID k identifikaci klíče v manifestu aplikace ( `$keyId` ).

V registraci aplikace Azure pro klientskou aplikaci:
1. Vyberte **manifest** pro otevření manifestu aplikace.
2. Nahraďte vlastnost *přihlašovací údaje* pomocí nových informací o certifikátu pomocí následujícího schématu.

   ```JSON
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
3. Uložte úpravy manifestu aplikace a pak nahrajte manifest na platformu Microsoft identity.

   `keyCredentials`Vlastnost má více hodnot, takže můžete nahrát více certifikátů pro bohatší správu klíčů.

## <a name="code-sample"></a>Ukázka kódu

> [!NOTE]
> Hlavičku X5T musíte vypočítat tak, že ji převedete na základní řetězec 64 pomocí hodnoty hash certifikátu. Kód, který se má provést v jazyce C# `System.Convert.ToBase64String(cert.GetCertHash());` , je.

Ukázková [aplikace konzoly .NET Core daemon s použitím platformy Microsoft Identity](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) ukazuje, jak aplikace používá vlastní přihlašovací údaje pro ověřování. Také ukazuje, jak můžete [vytvořit certifikát podepsaný svým držitelem](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) pomocí `New-SelfSignedCertificate` příkazu PowerShellu. Můžete také využít a použít [skripty pro vytváření aplikací](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) k vytvoření certifikátů, výpočtů kryptografických otisků a tak dále.

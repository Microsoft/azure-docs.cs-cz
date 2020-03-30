---
title: Pověření certifikátu platformy identit y Microsoft
titleSuffix: Microsoft identity platform
description: Tento článek popisuje registraci a použití pověření certifikátu pro ověřování aplikací.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: ryanwi
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 26030c12d98d796ceb1f66f198aede6e40eebd94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399022"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Pověření certifikátu pro ověřování aplikací platformy identit platformy Microsoft

Platforma identit microsoftu umožňuje aplikaci používat vlastní pověření pro ověřování, například v [oauth 2.0 klientská pověření grant flowv2.0](v2-oauth2-client-creds-grant-flow.md) a [on-behalf-of toku](v2-oauth2-on-behalf-of-flow.md)).

Jednou z forem pověření, které může aplikace použít pro ověřování, je kontrolní výraz JSON Web Token(JWT) podepsaný certifikátem, který aplikace vlastní.

## <a name="assertion-format"></a>Formát kontrolního výrazu
Platforma identit Microsoft Chcete-li vypočítat kontrolní výraz, můžete použít jednu z mnoha knihoven [JSON Web Token](https://jwt.ms/) v jazyce podle vašeho výběru. Informace přenášené žetonem jsou následující:

### <a name="header"></a>Hlavička

| Parametr |  Poznámka |
| --- | --- |
| `alg` | Měl by být **RS256** |
| `typ` | Měl by to být **JWT** |
| `x5t` | Měl by to být otisk palce Certifikát u X.509 SHA-1 |

### <a name="claims-payload"></a>Nároky (datová část)

| Parametr |  Poznámky |
| --- | --- |
| `aud` | Cílová skupina: Měla by být ** https://login.microsoftonline.com/ *tenant_Id*/oauth2/token** |
| `exp` | Datum vypršení platnosti: datum vypršení platnosti tokenu. Čas je reprezentován jako počet sekund od 1 ledna 1970 (1970-01-01T0:0:0Z) UTC až do doby vypršení platnosti tokenu.|
| `iss` | Vydavatel: by měl být client_id (ID aplikace klientské služby) |
| `jti` | GUID: the JWT ID |
| `nbf` | Ne před: datum, před kterým nelze token použít. Čas je reprezentován jako počet sekund od 1 ledna 1970 (1970-01-01T0:0:0Z) UTC až do doby, kdy byl vydán token. |
| `sub` | Předmět: Pokud `iss`jde o , by měla být client_id (ID aplikace klientské služby) |

### <a name="signature"></a>Podpis

Podpis je vypočítán použitím certifikátu, jak je popsáno ve [specifikaci JSON Web Token RFC7519](https://tools.ietf.org/html/rfc7519)

## <a name="example-of-a-decoded-jwt-assertion"></a>Příklad dekódovaného výrazu JWT

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

## <a name="example-of-an-encoded-jwt-assertion"></a>Příklad kódovaného výrazu JWT

Následující řetězec je příkladem kódovaného výrazu. Podíváte-li se pozorně, všimnete si tří částí oddělených tečkami (.):
* První oddíl zakóduje záhlaví
* Druhá část kóduje užitečné zatížení
* Poslední část je podpis vypočítaný s certifikáty z obsahu prvních dvou částí

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Registrace certifikátu pomocí platformy microsoft identit

Pověření certifikátu můžete přidružit ke klientské aplikaci v platformě microsoftových identit prostřednictvím portálu Azure pomocí některé z následujících metod:

### <a name="uploading-the-certificate-file"></a>Nahrání souboru certifikátu

V registraci aplikace Azure pro klientskou aplikaci:
1. Vyberte **možnost Certifikáty & tajných kódů**.
2. Klikněte na **Nahrát certifikát** a vyberte soubor certifikátu, který chcete nahrát.
3. Klikněte na **Přidat**.
  Po nahrání certifikátu se zobrazí kryptografický otisk, počáteční datum a hodnoty vypršení platnosti.

### <a name="updating-the-application-manifest"></a>Aktualizace manifestu aplikace

Po držení certifikátu je třeba vypočítat:

- `$base64Thumbprint`, což je základní kódování hash certifikátu 64
- `$base64Value`, což je základní kódování 64 nezpracovaných dat certifikátu

Je také nutné zadat identifikátor GUID k identifikaci`$keyId`klíče v manifestu aplikace ( ).

V registraci aplikace Azure pro klientskou aplikaci:
1. Vyberte **Manifest,** chcete-li otevřít manifest aplikace.
2. Nahraďte vlastnost *keyCredentials* novými informacemi o certifikátu pomocí následujícího schématu.

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
3. Uložte úpravy do manifestu aplikace a potom nahrajte manifest na platformu identit microsoftu.

   Vlastnost `keyCredentials` je multi-hodnota, takže můžete nahrát více certifikátů pro bohatší správu klíčů.

## <a name="code-sample"></a>Ukázka kódu

> [!NOTE]
> Hlavičku X5T je nutné vypočítat tak, že ji převedete na základní řetězec 64 pomocí hodnoty hash certifikátu. Kód k provedení v c# je `System.Convert.ToBase64String(cert.GetCertHash());`.

Ukázka kódu [.NET Core daemon konzoly aplikace pomocí platformy identit microsoft](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) ukazuje, jak aplikace používá vlastní pověření pro ověřování. Ukazuje také, jak můžete vytvořit certifikát podepsaný `New-SelfSignedCertificate` svým [držitelem](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) pomocí příkazu Powershell. Můžete také využít výhod a použít [skripty pro vytváření aplikací](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) k vytvoření certifikátů, výpočtu kryptografického otisku a tak dále.

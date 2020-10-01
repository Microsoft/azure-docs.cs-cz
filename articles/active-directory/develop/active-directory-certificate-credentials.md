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
ms.date: 09/30/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 77e34e4a18012f15b9e907e3b9efc1965b98f824
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612116"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Přihlašovací údaje ověřovacího certifikátu aplikace Microsoft Identity Platform

Platforma Microsoft Identity umožňuje aplikaci používat vlastní přihlašovací údaje pro ověřování kdekoli, kde se dá použít tajný klíč klienta, například v toku  [udělení přihlašovacích údajů klienta](v2-oauth2-client-creds-grant-flow.md) OAuth 2,0 a toku spouštěného za [běhu](v2-oauth2-on-behalf-of-flow.md) (OBO).

Jedna forma přihlašovacích údajů, kterou může aplikace použít k ověřování, je kontrolní výraz [JSON web token](./security-tokens.md#json-web-tokens-jwts-and-claims) (Jwt) podepsaný certifikátem, který vlastní aplikace.

## <a name="assertion-format"></a>Formát kontrolního výrazu

Chcete-li vypočítat kontrolní výraz, můžete použít jednu z mnoha knihoven JWT v jazyce podle vašeho výběru – [MSAL to podporuje pomocí nástroje `.WithCertificate()` ](msal-net-client-assertions.md). Tato informace je převedená tokenem ve své [hlavičce](#header), [deklaracích identity](#claims-payload)a [podpisu](#signature).

### <a name="header"></a>Záhlaví

| parametr |  Přeznačit |
| --- | --- |
| `alg` | By měl být **RS256** |
| `typ` | Měla by být **JWT** |
| `x5t` | Šestnáctková reprezentace certifikátu X. 509 (označovaná také jako *kryptografický otisk*SHA-1 certifikátu) kódovaná jako řetězcová hodnota base64. Například pokud je zadána hodnota hash certifikátu X. 509 `84E05C1D98BCE3A5421D225B140B36E86A3D5534` (Hex), `x5t` deklarace by byla `hOBcHZi846VCHSJbFAs26Go9VTQ=` (Base64). |

### <a name="claims-payload"></a>Deklarace identity (datová část)

Typ deklarace identity | Hodnota | Popis
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | Deklarace "AUD" (cílová skupina) identifikuje příjemce, pro které je požadavek JWT určen (tady Azure AD) [, viz dokument RFC 7519, část 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  V takovém případě je tento příjemce přihlašovacím serverem (login.microsoftonline.com).
exp | 1601519414 | Deklarace "EXP" (čas vypršení platnosti) identifikuje dobu vypršení platnosti nebo po jejímž uplynutí může být požadavek JWT přijat ke zpracování. Viz [dokument RFC 7519, část 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  To umožňuje, aby byl kontrolní výraz použit až do té doby, takže by měl být krátký – 5-10 minut po `nbf` maximálně.  Služba Azure AD v současné době neumísťuje omezení `exp` . 
ISS | ClientID | Deklarace "ISS" (Issuer) identifikuje objekt zabezpečení, který vystavil token JWT, v tomto případě klientská aplikace.  Použijte ID aplikace GUID.
jti | (identifikátor GUID) | Deklarace identity JTI (ID JWT) poskytuje jedinečný identifikátor pro token JWT. Hodnota identifikátoru musí být přiřazena způsobem, který zajišťuje nezanedbatelnou pravděpodobnost, že stejná hodnota bude omylem přiřazena k jinému datovému objektu. Pokud aplikace používá více vystavitelů, musí být kolizi znemožněna mezi hodnotami vytvořenými různými vystaviteli. Hodnota "JTI" je řetězec, který rozlišuje velká a malá písmena. [RFC 7519, část 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
NBF | 1601519114 | Deklarace "NBF" (ne dřív) určuje dobu, po jejímž uplynutí nesmí být požadavek JWT přijat ke zpracování. [RFC 7519, oddíl 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  Použití aktuálního času je vhodné. 
jednotk | ClientID | Deklarace "sub" (Subject) identifikuje předmět tokenu JWT, v tomto případě i ve vaší aplikaci. Použijte stejnou hodnotu jako `iss` . 

### <a name="signature"></a>Podpis

Signatura se vypočítává pomocí certifikátu, jak je popsáno ve [specifikaci JSON web token RFC7519](https://tools.ietf.org/html/rfc7519).

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

Následující řetězec je příkladem kódovaného kontrolního výrazu. Pokud pečlivě prohledáváte, všimnete si tří sekcí oddělených tečkami ( `.` ):

* První oddíl kóduje *hlavičku* .
* Druhá část kóduje *deklarace* (datová část).
* Poslední část je *podpis* vypočítanýý pomocí certifikátů z obsahu prvních dvou částí.

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
3. Klikněte na **Přidat**.
  Po nahrání certifikátu se zobrazí miniatura, datum zahájení a hodnoty vypršení platnosti.

### <a name="updating-the-application-manifest"></a>Aktualizace manifestu aplikace

Po uložení certifikátu je potřeba vypočítat:

- `$base64Thumbprint` -Hodnota hash certifikátu zakódovaná pomocí Base64
- `$base64Value` -Hodnota kódovaná pro nezpracované údaje certifikátu v kódování Base64

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
   
## <a name="using-a-client-assertion"></a>Použití kontrolního výrazu klienta

Kontrolní výrazy klienta se dají použít všude, kde se použije tajný klíč klienta.  Například v [toku autorizačního kódu](v2-oauth2-auth-code-flow.md)můžete předat a `client_secret` prokázat, že požadavek přichází z vaší aplikace. Tento parametr můžete nahradit `client_assertion` parametry a `client_assertion_type` . 

| Parametr | Hodnota | Popis|
|-----------|-------|------------|
|`client_assertion_type`|`urn:ietf:params:oauth:client-assertion-type:jwt-bearer`| Toto je pevná hodnota, která označuje, že používáte přihlašovací údaje certifikátu. |
|`client_assertion`| TOKEN |Toto je token JWT vytvořený výše. |

## <a name="next-steps"></a>Další kroky

[Knihovna MSAL.NET zpracovává tento scénář](msal-net-client-assertions.md) v jednom řádku kódu.

[Aplikace konzoly .NET Core daemon s použitím ukázky kódu Microsoft Identity Platform](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) na GitHubu ukazuje, jak aplikace používá vlastní přihlašovací údaje pro ověřování. Také ukazuje, jak můžete [vytvořit certifikát podepsaný svým držitelem](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) pomocí `New-SelfSignedCertificate` rutiny prostředí PowerShell. Pomocí [skriptů pro vytváření aplikací](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) v ukázkovém úložišti můžete také vytvářet certifikáty, vypočítat kryptografický otisk a tak dále.

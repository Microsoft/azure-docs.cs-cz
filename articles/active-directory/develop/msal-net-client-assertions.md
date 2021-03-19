---
title: Kontrolní výrazy klienta (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o podpoře podepsaných klientských výrazů u důvěrných klientských aplikací v knihovně Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/18/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8fb4ecb8fa8d6938e9afbc77064380b7b213029a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578732"
---
# <a name="confidential-client-assertions"></a>Důvěrné kontrolní výrazy klienta

Aby bylo možné prokázat svoji identitu, důvěrné klientské aplikace zaměňují tajný kód s Azure AD. Tajný klíč může být:
- Tajný kód klienta (heslo aplikace).
- Certifikát, který se používá k vytvoření podepsaného kontrolního výrazu obsahujícího standardní deklarace identity.

Tento tajný klíč může také představovat přímo podepsaný kontrolní výraz.

MSAL.NET má čtyři metody pro poskytnutí přihlašovacích údajů nebo kontrolních výrazů do důvěrné klientské aplikace:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> I když je možné použít `WithClientAssertion()` rozhraní API k získání tokenů pro důvěrného klienta, nedoporučujeme ho používat ve výchozím nastavení, protože je pokročilejší a je navržený tak, aby zpracovávala velmi konkrétní scénáře, které nejsou běžné. Pomocí `.WithCertificate()` rozhraní API to MSAL.NET umožní. Toto rozhraní API nabízí možnost přizpůsobení žádosti o ověření, pokud je to potřeba, ale `.WithCertificate()` pro většinu scénářů ověřování stačí výchozí kontrolní výraz vytvořený nástrojem. Toto rozhraní API je také možné použít jako alternativní řešení v některých situacích, kdy MSAL.NET nepomůže provést operaci podepisování interně.

### <a name="signed-assertions"></a>Podepsané kontrolní výrazy

Podepsaný klientský kontrolní výraz má podobu podepsaného tokenu JWT s datovou částí, která obsahuje požadované deklarace identity ověřování, které jsou v Azure AD zakódované pomocí kódování Base64. Pokud ho chcete použít:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Můžete také použít formulář delegáta, který vám umožní vypočítat kontrolní výraz v čase:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(() => { return GetSignedClientAssertion(); } )
                                          .Build();
```

[Deklarace identity, které očekává služba Azure AD](active-directory-certificate-credentials.md) v podepsaném kontrolním výrazu, jsou:

Typ deklarace identity | Hodnota | Popis
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | Deklarace "AUD" (cílová skupina) identifikuje příjemce, pro které je požadavek JWT určen (tady Azure AD) [, viz dokument RFC 7519, část 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  V takovém případě je tento příjemce přihlašovacím serverem (login.microsoftonline.com).
exp | 1601519414 | Deklarace "EXP" (čas vypršení platnosti) identifikuje dobu vypršení platnosti nebo po jejímž uplynutí může být požadavek JWT přijat ke zpracování. Viz [dokument RFC 7519, část 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  To umožňuje, aby byl kontrolní výraz použit až do té doby, takže by měl být krátký – 5-10 minut po `nbf` maximálně.  Služba Azure AD v současné době neumísťuje omezení `exp` . 
ISS | ClientID | Deklarace "ISS" (Issuer) identifikuje objekt zabezpečení, který vystavil token JWT, v tomto případě klientská aplikace.  Použijte ID aplikace GUID.
jti | (identifikátor GUID) | Deklarace identity JTI (ID JWT) poskytuje jedinečný identifikátor pro token JWT. Hodnota identifikátoru musí být přiřazena způsobem, který zajišťuje nezanedbatelnou pravděpodobnost, že stejná hodnota bude omylem přiřazena k jinému datovému objektu. Pokud aplikace používá více vystavitelů, musí být kolizi znemožněna mezi hodnotami vytvořenými různými vystaviteli. Hodnota "JTI" je řetězec, který rozlišuje velká a malá písmena. [RFC 7519, část 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
NBF | 1601519114 | Deklarace "NBF" (ne dřív) určuje dobu, po jejímž uplynutí nesmí být požadavek JWT přijat ke zpracování. [RFC 7519, oddíl 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  Použití aktuálního času je vhodné. 
jednotk | ClientID | Deklarace "sub" (Subject) identifikuje předmět tokenu JWT, v tomto případě i ve vaší aplikaci. Použijte stejnou hodnotu jako `iss` . 

Tady je příklad, jak vytvořit tyto deklarace identity:

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

Tady je postup, jak vytvořit podepsaný klientský kontrolní výraz:

```csharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(Certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims())));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>Alternativní metoda

Máte také možnost použít [Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) k vytvoření kontrolního výrazu za vás. Kód bude elegantní, jak je znázorněno v následujícím příkladu:

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

Jakmile budete mít podepsaného kontrolního výrazu klienta, můžete ho použít s rozhraními API MSAL, jak je znázorněno níže.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` ve výchozím nastavení vytvoří podepsaný kontrolní výraz obsahující deklarace, které očekává služba Azure AD, a další deklarace identity klientů, které chcete odeslat. Zde je fragment kódu, jak to provést.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Pokud je jedna z deklarací ve slovníku, kterou předáte, stejná jako jedna z povinných deklarací, vezme se v úvahu hodnota další deklarace identity. Přepíše deklarace vypočtené pomocí MSAL.NET.

Pokud chcete poskytnout vlastní deklarace identity, včetně povinných deklarací, které očekává služba Azure AD, předejte `false` `mergeWithDefaultClaims` parametr.

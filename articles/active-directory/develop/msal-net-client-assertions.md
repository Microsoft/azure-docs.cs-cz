---
title: Tvrzení klienta (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informace o podpoře tvrzení podepsaných klientů pro důvěrné klientské aplikace v knihovně Microsoft Authentication Library pro rozhraní .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/18/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8c97387bfd2a362d3bf5a6b8a3252242f061da31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050295"
---
# <a name="confidential-client-assertions"></a>Tvrzení důvěrného klienta

Chcete-li prokázat svou identitu, důvěrné klientské aplikace vyměňovat tajný klíč s Azure AD. Tajemství může být:
- Tajný klíč klienta (heslo aplikace).
- Certifikát, který se používá k vytvoření podepsané kontrolní výraz obsahující standardní deklarace identity.

Tento tajný klíč může být také přímo podepsaný mač.

MSAL.NET má čtyři metody, jak poskytnout důvěrné klientské aplikaci pověření nebo kontrolní výrazy:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> I když je možné `WithClientAssertion()` použít rozhraní API k získání tokenů pro důvěrného klienta, nedoporučujeme jej používat ve výchozím nastavení, protože je pokročilejší a je navržen tak, aby zpracovával velmi specifické scénáře, které nejsou běžné. Použití `.WithCertificate()` rozhraní API umožní MSAL.NET to za vás zpracovat. Toto rozhraní API nabízí možnost přizpůsobit požadavek na ověření `.WithCertificate()` v případě potřeby, ale výchozí kontrolní výraz vytvořený bude stačit pro většinu scénářů ověřování. Toto rozhraní API lze také použít jako řešení v některých scénářích, kde MSAL.NET nepodaří provést operaci podepisování interně.

### <a name="signed-assertions"></a>Podepsaná kontrolní výrazy

Podepsané tvrzení klienta má podobu podepsanéjwt s datovou částí obsahující požadované deklarace ověřování nařízené Azure AD, Base64 kódované. Pokud ho chcete použít:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Deklarace očekávané službou Azure AD jsou:

Typ deklarace identity | Hodnota | Popis
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | Deklarace "aud" (publikum) identifikuje příjemce, pro které je JWT určen (zde Azure AD) Viz [RFC 7519, oddíl 4.1.3]
exp | Čt Červen 27 2019 15:04:17 GMT+0200 (Romance letní čas) | Deklarace "exp" (expirace) identifikuje čas vypršení platnosti, na kterém nebo po kterém Nesmí být JWT přijat ke zpracování. Viz [RFC 7519, oddíl 4.1.4]
Iss | {Id klienta} | Deklarace "iss" (emitent) identifikuje jistinu, který vydal JWT. Zpracování této deklarace je specifické pro aplikaci. Hodnota "iss" je řetězec rozlišující malá a velká písmena obsahující hodnotu StringOrURI. [RFC 7519, oddíl 4.1.1]
jti | (a Guid) | Deklarace "jti" (JWT ID) poskytuje jedinečný identifikátor pro JWT. Hodnota identifikátoru musí být přiřazena způsobem, který zajišťuje, že existuje zanedbatelná pravděpodobnost, že stejná hodnota bude omylem přiřazena jinému datovému objektu; pokud aplikace používá více emitentů, musí být zabráněno kolizím mezi hodnotami vytvořenými i různými emitenty. Tvrzení "jti" lze použít k zabránění přehrání JWT. Hodnota "jti" je řetězec rozlišující malá a velká písmena. [RFC 7519, oddíl 4.1.7]
Nbf | Čt Červen 27 2019 14:54:17 GMT+0200 (Romance letní čas) | Tvrzení "nbf" (ne dříve) označuje dobu, před kterou nesmí být JWT přijat ke zpracování. [RFC 7519, oddíl 4.1.5]
Dílčí | {Id klienta} | Pohledávka "sub" (předmět) identifikuje předmět JWT. Tvrzení v JWT jsou obvykle prohlášení o předmětu. Hodnota předmětu musí být vymezena tak, aby byla místně jedinečná v kontextu vystavittele, nebo globálně jedinečná. Viz [RFC 7519, bod 4.1.2]

Zde je příklad, jak řemeslo tyto nároky:

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

Zde je návod, jak vytvořit tvrzení podepsaného klienta:

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

Máte také možnost pomocí [Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) vytvořit kontrolní výraz pro vás. Kód bude elegantnější, jak je znázorněno v příkladu níže:

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

Jakmile máte tvrzení podepsaného klienta, můžete jej použít s apis MSAL, jak je znázorněno níže.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)`ve výchozím nastavení vytvoří podepsané kontrolní výraz obsahující deklarace očekávané službou Azure AD plus další deklarace klienta, které chcete odeslat. Zde je fragment kódu o tom, jak to udělat.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Pokud je jeden z deklarací ve slovníku, který předáte, stejný jako jeden z povinných nároků, bude zohledněna hodnota dodatečné deklarace. Přepíše deklarace vypočítané MSAL.NET.

Pokud chcete poskytnout vlastní deklarace identity, včetně povinných deklarací očekávaných službou Azure AD, předejte `false` `mergeWithDefaultClaims` parametr.

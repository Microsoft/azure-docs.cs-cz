---
title: Kontrolní výrazy klienta v knihovně Microsoft Authentication Library pro .NET | Azure
description: Přečtěte si o podpoře podepsaných klientských výrazů u důvěrných klientských aplikací v knihovně Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1ea75499334f3f6eb2f5d3c15526067fcef4eb8
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442510"
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

### <a name="signed-assertions"></a>Podepsané kontrolní výrazy

Podepsaný klientský kontrolní výraz má podobu podepsaného tokenu JWT s datovou částí, která obsahuje požadované deklarace identity ověřování, které jsou v Azure AD zakódované pomocí kódování Base64. Pokud ho chcete použít:

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Služba Azure AD očekává deklarace identity:

Typ deklarace identity | Hodnota | Popis
---------- | ---------- | ----------
AUD | https://login.microsoftonline.com/{tenantId}/v2.0 | Deklarace "AUD" (cílová skupina) identifikuje příjemce, pro které je požadavek JWT určen (tady Azure AD), viz [RFC 7519, Section 4.1.3].
oček | Čtvrtek června 27 2019 15:04:17 GMT + 0200 (románské země (letní čas)) | Deklarace "EXP" (čas vypršení platnosti) identifikuje dobu vypršení platnosti nebo po jejímž uplynutí může být požadavek JWT přijat ke zpracování. Viz [RFC 7519, kapitola 4.1.4]
ISS | ClientID | Deklarace identity "ISS" (Issuer) identifikuje objekt zabezpečení, který vystavil token JWT. Zpracování této deklarace identity je specifické pro aplikaci. Hodnota "ISS" je řetězec rozlišující velká a malá písmena obsahující hodnotu StringOrURI. [RFC 7519, oddíl 4.1.1]
jti | (identifikátor GUID) | Deklarace identity JTI (ID JWT) poskytuje jedinečný identifikátor pro token JWT. Hodnota identifikátoru musí být přiřazena způsobem, který zajišťuje nezanedbatelnou pravděpodobnost, že stejná hodnota bude omylem přiřazena k jinému datovému objektu. Pokud aplikace používá více vystavitelů, musí být kolizi znemožněna mezi hodnotami vytvořenými různými vystaviteli. Deklaraci identity JTI lze použít k zabránění opakovanému přehrání tokenu JWT. Hodnota "JTI" je řetězec, který rozlišuje velká a malá písmena. [RFC 7519, část 4.1.7]
nbf | Čtvrtek června 27 2019 14:54:17 GMT + 0200 (románské země (letní čas)) | Deklarace "NBF" (ne dřív) určuje dobu, po jejímž uplynutí nesmí být požadavek JWT přijat ke zpracování. [RFC 7519, oddíl 4.1.5]
sub | ClientID | Deklarace "sub" (Subject) identifikuje předmět tokenu JWT. Deklarace v tokenu JWT jsou obvykle příkazy pro předmět. Hodnota subjektu musí být buď vymezená, aby byla v kontextu vystavitele místně jedinečná, nebo být globálně jedinečná. Viz [RFC 7519, oddíl 4.1.2].

Tady je příklad, jak vytvořit tyto deklarace identity:

```CSharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = "https://login.microsoftonline.com/72f988bf-86f1-41af-hd4m-2d7cd011db47/v2.0";

      string ConfidentialClientID = "61dab2ba-145d-4b1b-8569-bf4b9aed5dhb" //client id
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

```CSharp
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

string GetAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
    X509Certificate2 certificate = ReadCertificate(config.CertificateName);

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
    string SignedAssertion = string.Concat(token, ".", signature);
    return SignedAssertion;
}
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)`ve výchozím nastavení vytvoří podepsaný kontrolní výraz obsahující deklarace, které očekává služba Azure AD, a další deklarace identity klientů, které chcete odeslat. Zde je fragment kódu, jak to provést.

```CSharp
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

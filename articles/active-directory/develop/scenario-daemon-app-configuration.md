---
title: Aplikace démona, která volá webová rozhraní API (konfigurace aplikace) – platforma Microsoftu identity
description: Naučte se vytvářet aplikace démona, která volá webová rozhraní API (konfigurace aplikace).
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 705545fd5167087be1a001c45f58907d6ff225e8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277824"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Aplikace démona, která volá webovou rozhraní API – konfigurace kódu

Naučte se konfigurovat kód pro aplikaci démona, která volá webová rozhraní API.

## <a name="msal-libraries-supporting-daemon-apps"></a>Knihovny MSAL podporující aplikace démona

Knihovny Microsoftu podporující aplikace démona jsou:

  Knihovna MSAL | Popis
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Podporované platformy pro sestavení aplikace démona jsou .NET Framework a platformy .NET Core (ne UWP, Xamarin. iOS a Xamarin. Android, protože tyto platformy slouží k vytváření veřejných klientských aplikací).
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Vývoj v průběhu verze Public Preview
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL. Kompilátor | Vývoj v průběhu verze Public Preview

## <a name="configuration-of-the-authority"></a>Konfigurace autority

Vzhledem k tom, že aplikace démona nepoužívají delegovaná oprávnění, ale oprávnění aplikací, jejich *podporovaný typ účtu* nemůže být *účty v žádném organizačním adresáři a osobních účtech Microsoft (například Skype, Xbox, Outlook.com)* . K udělení souhlasu s aplikací démon pro osobní účty Microsoft se totiž neposkytuje žádný správce tenanta. V organizaci nebo účtech budete muset zvolit *účty* *v libovolné organizaci*.

Proto by autorita zadaná v konfiguraci aplikace měla být přihlášená klientovi (zadáním ID tenanta nebo názvu domény přidruženého k vaší organizaci).

Pokud jste nezávislý výrobce softwaru a chcete poskytnout více tenantů nástroje, můžete použít `organizations`. Mějte ale na paměti, že budete taky muset vysvětlit zákazníkům, jak udělit souhlas správce. Podrobnosti najdete v tématu o [žádosti o souhlas pro celého tenanta](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) . V současné době je také v MSAL omezení, `organizations` které je povoleno pouze v případě, že přihlašovací údaje klienta obsahují tajný klíč aplikace (nikoli certifikát). Viz [MSAL.NET chyby #891](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/891)

## <a name="application-configuration-and-instantiation"></a>Konfigurace a instance aplikace

V knihovnách MSAL se přihlašovací údaje klienta (tajný kód nebo certifikát) předávají jako parametr konstrukce důvěrné klientské aplikace.

> [!IMPORTANT]
> I v případě, že vaše aplikace je Konzolová aplikace spuštěná jako služba, a pokud se jedná o aplikaci démona, musí to být důvěrná klientská aplikace.

### <a name="msalnet"></a>MSAL.NET

Přidejte do své aplikace balíček NuGet [Microsoft. IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) .

Použít obor názvů MSAL.NET

```CSharp
using Microsoft.Identity.Client;
```

Aplikace démona bude prezentována`IConfidentialClientApplication`

```CSharp
IConfidentialClientApplication app;
```

Zde je kód pro sestavení aplikace s tajným klíčem aplikace:

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

Zde je kód pro sestavení aplikace s certifikátem:

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

Místo toho může klientská aplikace v tajných klientech také prokázat svoji identitu pomocí kontrolních výrazů klienta. Tento rozšířený scénář je podrobně popsán v [kontrolním výrazu klienta](msal-net-client-assertions.md) .


### <a name="msalpython"></a>MSAL.Python

```Python
# Create a preferably long-lived app instance which maintains a token cache.

app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache

    )
```

### <a name="msaljava"></a>MSAL. Kompilátor

```Java
PrivateKey key = getPrivateKey();
X509Certificate publicCertificate = getPublicCertificate();

// create clientCredential with public and private key
IClientCredential credential = ClientCredentialFactory.create(key, publicCertificate);

ConfidentialClientApplication cca = ConfidentialClientApplication
  .builder(CLIENT_ID, credential)
  .authority(AUTHORITY_MICROSOFT)
  .build();
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Aplikace démona – získávání tokenů pro aplikaci](./scenario-daemon-acquire-token.md)

---
title: Démon procesu aplikace volání webových rozhraní API (Konfigurace aplikace) – platforma identit Microsoft
description: Zjistěte, jak vytvořit aplikaci pro proces démon, že volání webových rozhraní API (Konfigurace aplikace)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8d377db827a6548c380128624c21f4ae7896aff
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075322"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Démon procesu aplikace, že volání webových rozhraní API – konfigurace kódu

Zjistěte, jak nakonfigurovat kódu pro vaši aplikaci démona, volá webové rozhraní API.

## <a name="msal-libraries-supporting-daemon-apps"></a>Aplikace démonů podpůrné knihovny MSAL

Knihovny Microsoft podporuje aplikace démonů jsou:

  Knihovna MSAL | Popis
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Podporované platformy, jak vytvořit webovou aplikaci démon jsou platformy .NET Framework a .NET Core (UPW Ne, Xamarin.iOS a Xamarin.Android jako těchto platforem se používají k vytváření veřejné klientských aplikací)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Vývoj v průběhu – ve verzi public preview
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Vývoj v průběhu – ve verzi public preview

## <a name="configuration-of-the-authority"></a>Konfigurace autority

Vzhledem k tomu, že aplikace démon nepoužívejte delegovaná oprávnění, ale oprávnění aplikací, jejich *nepodporuje typ účtu* nemůže být *účty v libovolném adresáři organizace a Microsoft osobní účty () například, Skype, Xbox, Outlook.com)*. Ve skutečnosti neexistuje žádný správce tenanta udělit souhlas démon aplikace pro osobní účty Microsoft. Budete muset zvolit *účty v mé organizaci* nebo *účty v jakékoli organizaci*.

Proto autority zadané v konfiguraci aplikace by měla být tenanta ed (určení ID Tenanta nebo název domény přidružený k vaší organizaci). Pokud jste nezávislý výrobce softwaru a chcete poskytnout nástroj pro více tenantů, můžete použít `organizations`. Ale pamatujte, že budete také muset vašim zákazníkům vysvětlují, jak udělit souhlas správce. Zobrazit [požaduje souhlas pro celého tenanta](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) podrobnosti

## <a name="application-configuration-and-instantiation"></a>Konfigurace aplikace a instance

Přihlašovací údaje klienta (tajný klíč nebo certifikát) jsou v knihovnách MSAL předaného jako parametr konstrukce důvěrnému klientovi aplikace.

> [!IMPORTANT]
> I v případě, že vaše aplikace je konzolová aplikace spuštění jako služby, pokud je to aplikace démon ho musí být důvěrný klientské aplikace.

### <a name="msalnet"></a>MSAL.NET

Přidat [Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) balíček NuGet do vaší aplikace.

Pomocí MSAL.NET obor názvů

```CSharp
using Microsoft.Identity.Client;
```

Démon procesu aplikace se zobrazí podle `IConfidentialClientApplication`

```CSharp
IConfidentialClientApplication app;
```

Tady je kód pro vytvoření aplikace s tajný klíč aplikace:

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

Tady je kód pro vytvoření aplikace pomocí certifikátu:

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

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

### <a name="msaljava"></a>MSAL.Java

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
> [Démon procesu aplikace – získávání tokenů pro aplikaci](./scenario-daemon-acquire-token.md)
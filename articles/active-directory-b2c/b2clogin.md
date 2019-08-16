---
title: Nastavte adresy URL pro přesměrování na b2clogin.com-Azure Active Directory B2C
description: Přečtěte si o používání b2clogin.com v adresách URL pro přesměrování pro Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dbc366daac89f44d4b084081590124f81ff9cc9c
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533743"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Nastavte adresy URL pro přesměrování na b2clogin.com pro Azure Active Directory B2C

Když nastavíte poskytovatele identity pro registraci a přihlášení do aplikace Azure Active Directory B2C (Azure AD B2C), musíte zadat adresu URL pro přesměrování. Ve vašich aplikacích a rozhraní API byste už neměli odkazovat na *Login.microsoftonline.com* . Místo toho použijte *b2clogin.com* pro všechny nové aplikace a Migrujte stávající aplikace z *Login.microsoftonline.com* do *b2clogin.com*.

## <a name="benefits-of-b2clogincom"></a>Výhody b2clogin.com

Když jako adresu URL pro přesměrování použijete *b2clogin.com* :

* Místo spotřebované v hlavičce souboru cookie od služby společnosti Microsoft se sníží.
* Vaše adresy URL pro přesměrování už nemusejí zahrnovat odkaz na Microsoft.
* JavaScriptový kód na straně klienta je podporován (aktuálně ve [verzi Preview](user-flow-javascript-overview.md)) v přizpůsobených stránkách. Z důvodu omezení zabezpečení jsou kódy JavaScriptu a prvky formuláře HTML odebrány z vlastních stránek, pokud použijete *Login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Přehled požadovaných změn

Je možné, že budete muset provést několik úprav, abyste mohli migrovat své aplikace na *b2clogin.com*:

* Změňte adresu URL pro přesměrování v aplikacích poskytovatele identity na odkaz na *b2clogin.com*.
* Aktualizujte své aplikace Azure AD B2C tak, aby používaly *b2clogin.com* v uživatelském toku a odkazy na koncový bod tokenu.
* Aktualizujte všechny **Povolené zdroje** , které jste definovali v nastavení CORS pro [přizpůsobení uživatelského rozhraní](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="change-identity-provider-redirect-urls"></a>Změna adres URL pro přesměrování zprostředkovatele identity

Na webu každého zprostředkovatele identity, na kterém jste vytvořili aplikaci, změňte všechny důvěryhodné adresy URL tak, aby se `your-tenant-name.b2clogin.com` přesměrovaly na místo *Login.microsoftonline.com*.

Pro adresy URL pro přesměrování b2clogin.com můžete použít dva formáty. První z nich přináší výhody, proč se "Microsoft" zobrazuje kdekoli v adrese URL pomocí ID tenanta (GUID) místo názvu domény klienta:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

Druhá možnost používá název domény tenanta ve formátu `your-tenant-name.onmicrosoft.com`. Příklad:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Pro oba formáty:

* Nahraďte `{your-tenant-name}` s názvem vašeho tenanta Azure AD B2C.
* Odeberte `/te` , pokud existuje v adrese URL.

## <a name="update-your-applications-and-apis"></a>Aktualizace aplikací a rozhraní API

Kód ve vašich aplikacích a rozhraní API s podporou Azure AD B2C se může na `login.microsoftonline.com` několik míst odkazovat. Kód může mít například odkazy na toky uživatelů a koncové body tokenu. Místo toho aktualizujte následující informace `your-tenant-name.b2clogin.com`:

* Koncový bod autorizace
* Koncový bod tokenu
* Vydavatel tokenu

Například koncový bod autority pro zásady registrace a přihlašování společnosti Contoso by měl být následující:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="validateauthority-property"></a>Vlastnost ValidateAuthority

Pokud používáte [MSAL.NET][msal-dotnet] v2 nebo starší, nastavte `false` vlastnost **ValidateAuthority** na instanci klienta tak, aby umožňovala přesměrování na *b2clogin.com*. Toto nastavení není vyžadováno pro MSAL.NET v3 a novější.

```CSharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

Pokud používáte [MSAL pro JavaScript][msal-js]:

```JavaScript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
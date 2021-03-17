---
title: Migrace aplikací a rozhraní API na b2clogin.com
titleSuffix: Azure AD B2C
description: Přečtěte si o používání b2clogin.com v adresách URL pro přesměrování pro Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 20df5fc3a4d7c392be62df2b7778854d1e2e1cba
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109058"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Nastavte adresy URL pro přesměrování na b2clogin.com pro Azure Active Directory B2C

Když nastavíte poskytovatele identity pro registraci a přihlášení do aplikace Azure Active Directory B2C (Azure AD B2C), musíte zadat adresu URL pro přesměrování. Neměli byste už v aplikacích a rozhraních API odkazovat na *Login.microsoftonline.com* k ověřování uživatelů pomocí Azure AD B2C. Místo toho použijte *b2clogin.com* pro všechny nové aplikace a Migrujte stávající aplikace z *Login.microsoftonline.com* do *b2clogin.com*.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Vyřazení login.microsoftonline.com

**Aktualizace z října 2020:** Rozšiřujeme období odkladu pro klienty, kteří nemůžou splňovat původně ohlášené datum vyřazení ze 4. prosince 2020. K vyřazení login.microsoftonline.com se teď dojde dřív než **14. ledna 2021.**

Na **pozadí**: od 04 do 4. prosince 2019 jsme původně [oznámili](https://azure.microsoft.com/updates/b2c-deprecate-msol/) plánované vyřazení podpory Login.microsoftonline.com v Azure AD B2C dne 04 2020. To poskytuje existujícím klientům jeden (1) rok migrace na b2clogin.com. Noví klienti vytvoření po 04. prosinci 2019 nebudou přijímat požadavky od login.microsoftonline.com. Všechny funkce zůstávají stejné na koncovém bodu b2clogin.com.

Vyřazení login.microsoftonline.com nemá vliv na Azure Active Directory klienty. Touto změnou jsou ovlivněny pouze Azure Active Directory B2C klienti.

## <a name="what-endpoints-does-this-apply-to"></a>Jaké koncové body to platí pro
Přechod na b2clogin.com se vztahuje pouze na koncové body ověřování, které používají zásady Azure AD B2C (uživatelské toky nebo vlastní zásady) k ověřování uživatelů. Tyto koncové body mají `<policy-name>` parametr, který určuje Azure AD B2C zásad, které se mají použít. [Přečtěte si další informace o Azure AD B2Cch zásadách](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 

Tyto koncové body můžou vypadat takto:
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Alternativně `<policy-name>` může být předán jako parametr dotazu:
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/authorize?<b>p=\<policy-name\></b></code>
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/token?<b>p=\<policy-name\></b></code>

> [!IMPORTANT]
> Koncové body, které používají parametr Policy, musí být aktualizované a také [adresy URL pro přesměrování zprostředkovatele identity](#change-identity-provider-redirect-urls).

Někteří Azure AD B2C zákazníci používají sdílené funkce tenantů Azure AD Enterprise, jako je například grantový tok udělení přihlašovacích údajů klienta OAuth 2,0. K těmto funkcím se dostanete pomocí koncových bodů login.microsoftonline.com Azure AD, *které neobsahují parametr zásad*. __Tyto koncové body nejsou ovlivněny__.

## <a name="benefits-of-b2clogincom"></a>Výhody b2clogin.com

Když jako adresu URL pro přesměrování použijete *b2clogin.com* :

* Místo spotřebované v hlavičce souboru cookie od služby společnosti Microsoft se sníží.
* Vaše adresy URL pro přesměrování už nemusejí zahrnovat odkaz na Microsoft.
* JavaScriptový kód na straně klienta je podporován (aktuálně ve [verzi Preview](javascript-and-page-layout.md)) v přizpůsobených stránkách. Z důvodu omezení zabezpečení jsou kódy JavaScriptu a prvky formuláře HTML odebrány z vlastních stránek, pokud použijete *Login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Přehled požadovaných změn

Je možné, že budete muset provést několik úprav, abyste mohli migrovat své aplikace na *b2clogin.com*:

* Změňte adresu URL pro přesměrování v aplikacích poskytovatele identity na odkaz na *b2clogin.com*.
* Aktualizujte své aplikace Azure AD B2C tak, aby používaly *b2clogin.com* v uživatelském toku a odkazy na koncový bod tokenu. To může zahrnovat aktualizaci používání knihovny ověřování, jako je Microsoft Authentication Library (MSAL).
* Aktualizujte všechny **Povolené zdroje** , které jste definovali v nastavení CORS pro [přizpůsobení uživatelského rozhraní](customize-ui-with-html.md).

Starý koncový bod může vypadat takto:
- <b><code>https://login.microsoft.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>

Odpovídající aktualizovaný koncový bod by vypadal takto:
- <code><b>https://\<tenant-name\>.b2clogin.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>


## <a name="change-identity-provider-redirect-urls"></a>Změna adres URL pro přesměrování zprostředkovatele identity

Na webu každého zprostředkovatele identity, na kterém jste vytvořili aplikaci, změňte všechny důvěryhodné adresy URL tak, aby se přesměrovaly na `your-tenant-name.b2clogin.com` místo *Login.microsoftonline.com*.

Pro adresy URL pro přesměrování b2clogin.com můžete použít dva formáty. První z nich přináší výhody, proč se "Microsoft" zobrazuje kdekoli v adrese URL pomocí ID tenanta (GUID) místo názvu domény klienta:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

Druhá možnost používá název domény tenanta ve formátu `your-tenant-name.onmicrosoft.com` . Příklad:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Pro oba formáty:

* Nahraďte `{your-tenant-name}` názvem vašeho tenanta Azure AD B2C.
* Odeberte, `/te` Pokud existuje v adrese URL.

## <a name="update-your-applications-and-apis"></a>Aktualizace aplikací a rozhraní API

Kód ve vašich aplikacích a rozhraní API s podporou Azure AD B2C se může na `login.microsoftonline.com` několik míst odkazovat. Kód může mít například odkazy na toky uživatelů a koncové body tokenu. Místo toho aktualizujte následující informace `your-tenant-name.b2clogin.com` :

* Koncový bod autorizace
* Koncový bod tokenu
* Vydavatel tokenů

Například koncový bod autority pro zásady registrace a přihlašování společnosti Contoso by měl být následující:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Informace o migraci webových aplikací založených na OWIN do b2clogin.com najdete v tématu [migrace webového rozhraní API založeného na Owin na b2clogin.com](multiple-token-endpoints.md).

Informace o migraci rozhraní API služby Azure API Management chráněných pomocí Azure AD B2C najdete v části [migrace do b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) v tématu [zabezpečení rozhraní api služby azure API Management pomocí Azure AD B2C](secure-api-management.md).

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="msalnet-validateauthority-property"></a>Vlastnost MSAL.NET ValidateAuthority

Pokud používáte [MSAL.NET][msal-dotnet] v2 nebo starší, nastavte vlastnost **ValidateAuthority** na `false` instanci klienta tak, aby umožňovala přesměrování na *b2clogin.com*. Nastavení této hodnoty na `false` není vyžadováno pro MSAL.NET v3 a novější.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

### <a name="msal-for-javascript-validateauthority-property"></a>MSAL pro vlastnost validateAuthority JavaScriptu

Pokud používáte [MSAL pro JavaScript][msal-js] v 1.2.2 nebo starší, nastavte vlastnost **validateAuthority** na `false` .

```JavaScript
// MSAL.js v1.2.2 and earlier
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false // Required in MSAL.js v1.2.2 and earlier **ONLY**
  }
);
```

Pokud jste nastavili `validateAuthority: true` v MSAL.js 1.3.0 + (výchozí nastavení), musíte také zadat platného vystavitele tokenu `knownAuthorities` :

```JavaScript
// MSAL.js v1.3.0+
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: true, // Supported in MSAL.js v1.3.0+
    knownAuthorities: ['tenant-name.b2clogin.com'] // Required if validateAuthority: true
  }
);
```

## <a name="next-steps"></a>Další kroky

Informace o migraci webových aplikací založených na OWIN do b2clogin.com najdete v tématu [migrace webového rozhraní API založeného na Owin na b2clogin.com](multiple-token-endpoints.md).

Informace o migraci rozhraní API služby Azure API Management chráněných pomocí Azure AD B2C najdete v části [migrace do b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) v tématu [zabezpečení rozhraní api služby azure API Management pomocí Azure AD B2C](secure-api-management.md).

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md

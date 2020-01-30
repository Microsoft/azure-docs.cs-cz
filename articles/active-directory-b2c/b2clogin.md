---
title: Migrace aplikací a rozhraní API na b2clogin.com
titleSuffix: Azure AD B2C
description: Přečtěte si o používání b2clogin.com v adresách URL pro přesměrování pro Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5c9054daea76675ed621caf1630c509b16743f4e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76836338"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Nastavte adresy URL pro přesměrování na b2clogin.com pro Azure Active Directory B2C

Když nastavíte poskytovatele identity pro registraci a přihlášení do aplikace Azure Active Directory B2C (Azure AD B2C), musíte zadat adresu URL pro přesměrování. Ve vašich aplikacích a rozhraní API byste už neměli odkazovat na *Login.microsoftonline.com* . Místo toho použijte *b2clogin.com* pro všechny nové aplikace a Migrujte stávající aplikace z *Login.microsoftonline.com* do *b2clogin.com*.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Vyřazení login.microsoftonline.com

Od 04. prosince 2019 jsme oznámili plánované vyřazení podpory login.microsoftonline.com v Azure AD B2C dne **04 2020**:

[Azure Active Directory B2C nepoužívá login.microsoftonline.com.](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

Vyřazení login.microsoftonline.com se projeví pro všechny klienty Azure AD B2C v 04. prosince 2020, což poskytuje stávajícím tenantům jeden (1) rok k migraci na b2clogin.com. Noví klienti vytvoření po 04. prosinci 2019 nebudou přijímat požadavky od login.microsoftonline.com. Všechny funkce zůstávají stejné na koncovém bodu b2clogin.com.

Vyřazení login.microsoftonline.com nemá vliv na Azure Active Directory klienty. Touto změnou jsou ovlivněny pouze Azure Active Directory B2C klienti.

## <a name="benefits-of-b2clogincom"></a>Výhody b2clogin.com

Když jako adresu URL pro přesměrování použijete *b2clogin.com* :

* Místo spotřebované v hlavičce souboru cookie od služby společnosti Microsoft se sníží.
* Vaše adresy URL pro přesměrování už nemusejí zahrnovat odkaz na Microsoft.
* JavaScriptový kód na straně klienta je podporován (aktuálně ve [verzi Preview](user-flow-javascript-overview.md)) v přizpůsobených stránkách. Z důvodu omezení zabezpečení jsou kódy JavaScriptu a prvky formuláře HTML odebrány z vlastních stránek, pokud použijete *Login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Přehled požadovaných změn

Je možné, že budete muset provést několik úprav, abyste mohli migrovat své aplikace na *b2clogin.com*:

* Změňte adresu URL pro přesměrování v aplikacích poskytovatele identity na odkaz na *b2clogin.com*.
* Aktualizujte své aplikace Azure AD B2C tak, aby používaly *b2clogin.com* v uživatelském toku a odkazy na koncový bod tokenu.
* Aktualizujte všechny **Povolené zdroje** , které jste definovali v nastavení CORS pro [přizpůsobení uživatelského rozhraní](custom-policy-ui-customization-dynamic.md).

## <a name="change-identity-provider-redirect-urls"></a>Změna adres URL pro přesměrování zprostředkovatele identity

Na webu každého poskytovatele identity, na kterém jste vytvořili aplikaci, změňte všechny důvěryhodné adresy URL tak, aby se přesměrovaly na `your-tenant-name.b2clogin.com` místo na *Login.microsoftonline.com*.

Pro adresy URL pro přesměrování b2clogin.com můžete použít dva formáty. První z nich přináší výhody, proč se "Microsoft" zobrazuje kdekoli v adrese URL pomocí ID tenanta (GUID) místo názvu domény klienta:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

Druhá možnost používá název domény tenanta ve formě `your-tenant-name.onmicrosoft.com`. Příklad:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Pro oba formáty:

* Nahraďte `{your-tenant-name}` s názvem vašeho tenanta Azure AD B2C.
* Pokud v adrese URL existuje, odeberte `/te`.

## <a name="update-your-applications-and-apis"></a>Aktualizace aplikací a rozhraní API

Kód ve vašich aplikacích a rozhraní API s podporou Azure AD B2C se může na několika místech vztahovat na `login.microsoftonline.com`. Kód může mít například odkazy na toky uživatelů a koncové body tokenu. Místo toho aktualizujte následující informace `your-tenant-name.b2clogin.com`:

* koncový bod autorizace
* Koncový bod tokenu
* Vydavatel tokenů

Například koncový bod autority pro zásady registrace a přihlašování společnosti Contoso by měl být následující:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Informace o migraci webových aplikací založených na OWIN do b2clogin.com najdete v tématu [migrace webového rozhraní API založeného na Owin na b2clogin.com](multiple-token-endpoints.md).

Informace o migraci rozhraní API služby Azure API Management chráněných pomocí Azure AD B2C najdete v části [migrace do b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) v tématu [zabezpečení rozhraní api služby azure API Management pomocí Azure AD B2C](secure-api-management.md).

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="validateauthority-property"></a>Vlastnost ValidateAuthority

Pokud používáte [MSAL.NET][msal-dotnet] v2 nebo starší, nastavte vlastnost **ValidateAuthority** tak, aby `false` při vytváření instancí klienta, aby bylo umožněno přesměrování na *b2clogin.com*. Toto nastavení není vyžadováno pro MSAL.NET v3 a novější.

```csharp
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

## <a name="next-steps"></a>Další kroky

Informace o migraci webových aplikací založených na OWIN do b2clogin.com najdete v tématu [migrace webového rozhraní API založeného na Owin na b2clogin.com](multiple-token-endpoints.md).

Informace o migraci rozhraní API služby Azure API Management chráněných pomocí Azure AD B2C najdete v části [migrace do b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) v tématu [zabezpečení rozhraní api služby azure API Management pomocí Azure AD B2C](secure-api-management.md).

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
---
title: Migrace aplikací a api do b2clogin.com
titleSuffix: Azure AD B2C
description: Přečtěte si o používání b2clogin.com v adresách URL přesměrování pro Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 64b440054795670b99a22e37dec7188f3e1cd74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189986"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Nastavení adres URL přesměrování na b2clogin.com pro Službu Azure Active Directory B2C

Když nastavíte poskytovatele identity pro registraci a přihlášení v aplikaci Azure Active Directory B2C (Azure AD B2C), musíte zadat adresu URL přesměrování. Již byste neměli odkazovat na *login.microsoftonline.com* v aplikacích a api. Místo toho použijte *b2clogin.com* pro všechny nové aplikace a migrujte existující aplikace z *login.microsoftonline.com* do *b2clogin.com*.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Odsuzování login.microsoftonline.com

Na 04 Prosinec 2019 jsme oznámili plánované vyřazení login.microsoftonline.com podporu v Azure AD B2C na **04 Prosinec 2020**:

[Služba Azure Active Directory B2C zavrhuje login.microsoftonline.com](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

Vyřazení login.microsoftonline.com vstoupí v platnost pro všechny klienty Azure AD B2C na 04 Prosinec 2020, poskytuje stávající klienti jeden (1) rok migrovat do b2clogin.com. Noví klienti vytvoření po 04 login.microsoftonline.com. Všechny funkce zůstává stejné na koncovém bodu b2clogin.com.

Vyřazení login.microsoftonline.com nemá vliv na klienty služby Azure Active Directory. Tato změna se týká pouze klientů služby Azure Active Directory B2C.

## <a name="benefits-of-b2clogincom"></a>Výhody b2clogin.com

Používáte-li jako adresu URL přesměrování *b2clogin.com:*

* Místo spotřebované v záhlaví souborů cookie službami společnosti Microsoft je sníženo.
* Adresy URL přesměrování již nemusí obsahovat odkaz na společnost Microsoft.
* Kód klienta JavaScriptu je podporován (aktuálně ve [verzi preview)](user-flow-javascript-overview.md)na přizpůsobených stránkách. Z důvodu bezpečnostních omezení jsou javascriptový kód a prvky formuláře HTML odebrány z vlastních stránek, pokud používáte *login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Přehled požadovaných změn

K migraci aplikací do *b2clogin.com*může být nutné provést několik úprav:

* Změňte adresu URL přesměrování v aplikacích poskytovatele identity tak, aby odkazovala *b2clogin.com*.
* Aktualizujte aplikace Azure AD B2C tak, aby používaly *b2clogin.com* v jejich odkazech na tok a token.
* Aktualizujte všechny **povolené počátky,** které jste definovali v nastavení CORS pro [přizpůsobení uživatelského rozhraní](custom-policy-ui-customization.md).

## <a name="change-identity-provider-redirect-urls"></a>Změna adres URL přesměrování zprostředkovatele identity

Na webu každého poskytovatele identity, na kterém jste vytvořili aplikaci, změňte `your-tenant-name.b2clogin.com` všechny důvěryhodné adresy URL tak, aby se naněté místo *login.microsoftonline.com*.

Existují dva formáty, které můžete použít pro b2clogin.com adresy URL přesměrování. První poskytuje výhodu, že "Microsoft" se zobrazí kdekoli v adrese URL pomocí ID klienta (GUID) místo názvu domény klienta:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

Druhá možnost používá název domény klienta `your-tenant-name.onmicrosoft.com`ve formě aplikace . Například:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Pro oba formáty:

* Nahraďte `{your-tenant-name}` se názvem vašeho klienta Azure AD B2C.
* Odebrat, `/te` pokud existuje v adrese URL.

## <a name="update-your-applications-and-apis"></a>Aktualizace aplikací a api

Kód ve vašich aplikacích a rozhraních API s `login.microsoftonline.com` podporou Azure AD B2C může odkazovat na několika místech. Váš kód může mít například odkazy na toky uživatelů a koncové body tokenu. Aktualizujte následující místo `your-tenant-name.b2clogin.com`odkazu :

* Koncový bod autorizace
* Koncový bod tokenu
* Vydavatel tokenu

Například koncový bod autority pro zásady přihlášení a přihlášení společnosti Contoso by nyní byl:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Informace o migraci webových aplikací založených na OWIN na b2clogin.com naleznete v [tématu Migrace webového rozhraní API založeného na OWIN na b2clogin.com](multiple-token-endpoints.md).

Migrace rozhraní API Azure Management API chráněných Azure AD B2C najdete v tématu [Migrace do b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) části Secure a Azure API Management API s Azure [AD B2C](secure-api-management.md).

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="validateauthority-property"></a>Vlastnost ValidateAuthority

Pokud používáte [MSAL.NET][msal-dotnet] v2 nebo starší, nastavte vlastnost `false` **ValidateAuthority** na instanci klienta, aby bylo možné přesměrování *na b2clogin.com*. Toto nastavení není vyžadováno pro MSAL.NET v3 a vyšší.

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

Informace o migraci webových aplikací založených na OWIN na b2clogin.com naleznete v [tématu Migrace webového rozhraní API založeného na OWIN na b2clogin.com](multiple-token-endpoints.md).

Migrace rozhraní API Azure Management API chráněných Azure AD B2C najdete v tématu [Migrace do b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) části Secure a Azure API Management API s Azure [AD B2C](secure-api-management.md).

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md

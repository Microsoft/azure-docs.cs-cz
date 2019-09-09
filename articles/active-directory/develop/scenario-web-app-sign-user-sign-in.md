---
title: Webová aplikace, která přihlašuje uživatele (přihlásit se) – Microsoft Identity Platform
description: Informace o tom, jak vytvořit webovou aplikaci, která přihlašuje uživatele (přihlásit se)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
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
ms.openlocfilehash: 9766b530b4d795d0f35f097de20155cdd17687ca
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70812399"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Webová aplikace, která přihlašuje uživatele – přihlásit se

Naučte se, jak přidat přihlášení k kódu pro vaši webovou aplikaci, která přihlašuje uživatele.

## <a name="sign-in"></a>Přihlášení

Kód, který jsme prozkoumali v [konfiguraci kódu předchozí aplikace](scenario-web-app-sign-user-app-configuration.md) v článku, je vše, co potřebujete k implementaci přihlášení.
Jakmile se uživatel přihlásí do vaší aplikace, pravděpodobně budete chtít povolit, aby se odhlásili. ASP.NET Core zpracovává pro vás registraci.

## <a name="what-sign-out-involves"></a>Co zahrnuje i odhlášení

Odhlášení z webové aplikace je o více než odebrání informací o přihlášeném účtu ze stavu webové aplikace.
Webová aplikace musí také přesměrovat uživatele na koncový bod platformy `logout` Microsoft identity, aby se odhlásily. Když vaše webová aplikace přesměruje uživatele na `logout` koncový bod, tento koncový bod vymaže relaci uživatele z prohlížeče. Pokud vaše aplikace nepřešla do `logout` koncového bodu, uživatel se znovu ověří do vaší aplikace bez zadání přihlašovacích údajů, protože by měl platnou relaci jednotného přihlašování s koncovým bodem Microsoft Identity Platform.

Další informace najdete v části [odeslání žádosti o přihlášení](v2-protocols-oidc.md#send-a-sign-out-request) na [platformě Microsoft Identity Platform a](v2-protocols-oidc.md) v Koncepční dokumentaci k protokolu OpenID Connect.

## <a name="application-registration"></a>Registrace aplikace

Během registrace aplikace jste zaregistrovali **identifikátor URI po odhlášení**. V našem `https://localhost:44321/signout-oidc` kurzu jste se zaregistrovali v poli **Adresa URL pro odhlášení** v části **Upřesnit nastavení** na stránce **ověřování** . Podrobnosti najdete v tématu [Registrace aplikace WebApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp) .

## <a name="aspnet-core-code"></a>Kód ASP.NET Core

### <a name="signout-button"></a>Tlačítko pro odhlášení

Tlačítko Odhlásit se zveřejňuje v `Views\Shared\_LoginPartial.cshtml` a zobrazí se, jenom když je k dispozici ověřený účet (to znamená, že se uživatel dřív přihlásil).

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()`akce`AccountController`

Když stisknete tlačítko **Odhlásit** se na webové aplikaci, aktivuje se `SignOut` akce na `Account` řadiči. V předchozích verzích základních šablon `Account` ASP.NET byl kontroler vložen do webové aplikace, ale to už neplatí, protože je teď součástí samotného ASP.NET Core Frameworku. 

Kód pro `AccountController` je k dispozici v úložišti ASP.NET Core na adrese z [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Řízení účtu:

- Nastaví identifikátor URI pro přesměrování OpenID `/Account/SignedOut` na tak, aby se řadič zavolal zpátky, když Azure AD provedl odhlášení.
- Volání `Signout()`, která umožňují middlewaru OpenIdConnect kontaktovat koncový bod Microsoft identity `logout` Platform, který:

  - Vymaže soubor cookie relace z prohlížeče a
  - Volání nakonec volá zpět **adresu URL pro odhlášení**, která) ve výchozím nastavení zobrazuje [odhlášenou](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) stránku pro zobrazení odhlásilo. html také jako součást ASP.NET Core.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Zachytávání volání `logout` koncového bodu

Middleware ASP.NET Core OpenIdConnect umožňuje vaší aplikaci zachytit volání koncového bodu Microsoft Identity Platform `logout` tím, že poskytuje událost OpenIdConnect s názvem. `OnRedirectToIdentityProviderForSignOut` Webová aplikace ji používá k tomu, aby se zabránilo tomu, aby se dialog pro výběr účtu při odhlášení zobrazoval uživateli. Toto zachycení se provádí v rámci `AddAzureAdV2Authentication` `Microsoft.Identity.Web` opakovaně použitelné knihovny. Viz [StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

```CSharp
public static IServiceCollection AddAzureAdV2Authentication(this IServiceCollection services,
                                                            IConfiguration configuration)
{
    ...
    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
        ...
        options.Authority = options.Authority + "/v2.0/";
        ...
        // Attempt to avoid displaying the select account dialog when signing out
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            var user = context.HttpContext.User;
            context.ProtocolMessage.LoginHint = user.GetLoginHint();
            context.ProtocolMessage.DomainHint = user.GetDomainHint();
            await Task.FromResult(0);
        };
    }
}
```

## <a name="aspnet-code"></a>ASP.NET kód

V ASP.NET se odhlášení spouští z metody odhlašování () na řadiči (např. AccountController). Tato metoda není součástí ASP.NET architektury (na rozdíl od toho, co se stane v ASP.NET Core), a nepoužívá asynchronní a to je důvod:

- pošle výzvu k odhlášení OpenId.
- Vymaže mezipaměť.
- přesměrování na stránku, kterou chce

```CSharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 MsalAppBuilder.ClearUserTokenCache();
 Response.Redirect("/");
}
```

## <a name="protocol"></a>Protocol

Pokud nechcete používat ASP.NET Core ani ASP.NET, můžete se podívat na dokumentaci k protokolu, která je k dispozici v [otevřeném ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přesunout do produkčního prostředí](scenario-web-app-sign-user-production.md)

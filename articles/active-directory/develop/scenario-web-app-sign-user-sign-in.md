---
title: Webové aplikace, který se přihlásí uživatelé (přihlásit se) – platforma identit Microsoft
description: Další informace o vytváření webové aplikace, které přihlásí uživatelé (přihlášení)
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
ms.openlocfilehash: 3fb7fbba7ec48da580d2a630ae51aa20b3307848
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074617"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Webová aplikace s přihlašováním uživatelů – přihlášení

Zjistěte, jak přidat přihlášení na kód vaší webové aplikace, které uživatelé přihlásí.

## <a name="sign-in"></a>přihlášení

Kód, který jsme vytvořili v předchozím článku [konfigurace kódu aplikace](scenario-web-app-sign-user-app-configuration.md) je všechno, co potřebujete k implementaci odhlašování. Jakmile se uživatel přihlásil k aplikaci, budete pravděpodobně chtít povolit odhlášení. ASP.NET core zpracovává odhlášení za vás.

## <a name="what-sign-out-involves"></a>Zahrnuje co Odhlásit se

Odhlášení z webové aplikace se o více než odstranění informace o účtu přihlášeného stavu webové aplikace.
Webové aplikace musí také přesměrovat uživatele na Microsoft identity platform v2.0 `logout` koncový bod se odhlásit. Když vaše webová aplikace přesměruje uživatele na `logout` koncový bod, odstraní tento koncový bod relace uživatele z prohlížeče. Pokud nebyla aplikace Přejít `logout` koncový bod, uživatel by donutit k vaší aplikaci bez nutnosti zadávat své přihlašovací údaje znovu, protože by měli platné jednotné přihlášení relace s koncovým bodem v2.0 platforma Microsoft Identity.

Další informace najdete v tématu [odeslat žádost o odhlášení](v2-protocols-oidc.md#send-a-sign-out-request) tématu [v2.0 Microsoft Identity platform a protokolu OpenID Connect](v2-protocols-oidc.md) rámcové dokumentaci.

## <a name="application-registration"></a>Registrace aplikací

Při registraci aplikace budete jste se zaregistrovali **příspěvku odhlášení URI**. V našem kurzu jste zaregistrovali `https://localhost:44321/signout-oidc` v **odhlašovací adresa URL** pole **Upřesnit nastavení** tématu **ověřování** stránky. Podrobnosti najdete v tématu, [ registraci aplikace webové aplikace](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

## <a name="aspnet-core-code"></a>Kód technologie ASP.NET Core

### <a name="signout-button"></a>Tlačítko pro odhlášení

Tlačítko Odhlásit je přístupný ve `Views\Shared\_LoginPartial.cshtml` a zobrazí jenom po ověření účtu (to znamená, když uživatel se dříve přihlásil).

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

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()` Akce `AccountController`

Stisknutím klávesy **Odhlásit** tlačítko na webové triggery pro aplikace `SignOut` akce `Account` kontroleru. V předchozích verzích šablony ASP.NET core `Account` vložená řadiče s webovou aplikací, ale to už nejsou tak jak je teď součástí samotného rozhraní ASP.NET Core. 

Kód `AccountController` je k dispozici v úložišti ASP.NET core na z [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). Řízení účtů:

- Identifikátor URI pro přesměrování sady s OpenID `/Account/SignedOut` tak, aby kontroleru je volána zpět, když Azure AD byl proveden odhlášení
- Volání `Signout()`, které umožní middlewarem OpenIdConnect, obraťte se na platformě Microsoft identity `logout` koncový bod které:

  - Vymaže souboru cookie relace z prohlížeče, a
  - Volání nakonec volá zpět **odhlašovací adresa URL**, což) ve výchozím nastavení, zobrazí znaménkem si zobrazit stránku [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) také poskytuje jako součást ASP.NET Core.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Zachycení volání `logout` koncového bodu

Umožňuje aplikaci zachytit volání platforma identit Microsoft middlewarem ASP.NET Core OpenIdConnect `logout` koncového bodu zadáním OpenIdConnect událost s názvem `OnRedirectToIdentityProviderForSignOut`. Webová aplikace používá k pokusu o vyhnout dialogovém okně vyberte účet, zobrazí se uživateli při odhlášení. Provádí se v tomto zachycení `AddAzureAdV2Authentication` z `Microsoft.Identity.Web` opakovaně použitelné knihovny. Zobrazit [StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

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

## <a name="aspnet-code"></a>Kódu ASP.NET

V technologii ASP.NET Odhlásit se aktivuje z metody SignOut() řadiči (například AccountController). Tato metoda není součástí architektury ASP.NET (rozporu s co se děje v ASP.NET Core) a nepoužívá asynchronní, a to je důvod, proč ho:

- odešle výzvu odhlašování přes protokol OpenId
- Vymaže mezipaměť
- provede přesměrování na stránku, kterou chce

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

## <a name="protocol"></a>Protocol (Protokol)

Pokud už nechcete používat ASP.NET Core nebo ASP.NET, můžete si prohlédnout protokol dokumentaci, která je dostupná z [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přejít do produkčního prostředí](scenario-web-app-sign-user-production.md)

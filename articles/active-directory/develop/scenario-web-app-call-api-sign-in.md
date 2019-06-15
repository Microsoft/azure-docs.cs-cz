---
title: Webové aplikace, že volání webových rozhraní API (přihlásit se) – platforma identit Microsoft
description: Zjistěte, jak sestavit webovou aplikaci, která volá webové rozhraní API (přihlášení)
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
ms.openlocfilehash: 663cea72eb620217ad5fa8925d3bb00eedbf890c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074557"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Webovou aplikaci, která volá webové rozhraní API – přihlášení

Přidání přihlašování do webové aplikace už znáte. Přečtěte si, že v [webovou aplikaci, že se přihlásí uživatelé – přidání přihlašování](scenario-web-app-sign-user-sign-in.md).

Co se zde liší, je pokud uživatel je přihlášený navýšení kapacity, z této aplikace nebo z libovolné aplikace, které chcete odebrat z mezipaměti tokenů, tokeny, které jsou přidružené k uživateli.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Zachycení zpětné volání po odhlášení – jeden odhlásit

Může zachytávat vaše aplikace po `logout` události, například zrušte záznam tokenu mezipaměti přidružené k účtu, který odhlášení. Uvidíme v druhé části tohoto kurzu (o webové aplikaci volání webového rozhraní API), že webová aplikace uloží přístupové tokeny pro daného uživatele v mezipaměti. Zachycení po `logout` zpětného volání umožňuje webové aplikace k odebrání uživatele z mezipamětí tokenů. Tento mechanismus je znázorněný v `AddMsal()` metoda [StartupHelper.cs L137 143](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L137-L143)

**Odhlašovací adresa Url** , zaregistrovaný pro vaše aplikace umožňuje implementovat jednotné odhlašování. Platforma identit Microsoft `logout` zavolá koncový bod **odhlašovací adresa URL** zaregistrovaný s vaší aplikací. Toto volání se stane, když odhlášení bylo iniciováno z vaší webové aplikace, nebo z jiné webové aplikace nebo prohlížeče. Další informace najdete v tématu [jedním odhlašování](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc#single-sign-out) v rámcové dokumentaci.

```CSharp
public static IServiceCollection AddMsal(this IServiceCollection services, IEnumerable<string> initialScopes)
{
    services.AddTokenAcquisition();

    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
     ...
        // Handling the sign-out: removing the account from MSAL.NET cache
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            // Remove the account from MSAL.NET token cache
            var _tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
            await _tokenAcquisition.RemoveAccount(context);
        };
    });
    return services;
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získání tokenu pro webovou aplikaci](scenario-web-app-call-api-acquire-token.md)

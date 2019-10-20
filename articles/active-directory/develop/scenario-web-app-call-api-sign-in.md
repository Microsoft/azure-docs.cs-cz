---
title: Webová aplikace, která volá webová rozhraní API (přihlášení) – Microsoft Identity Platform
description: Informace o tom, jak vytvořit webovou aplikaci, která volá webová rozhraní API (přihlásit se)
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
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cb0190f76ddce79012a5bf97e2d813f40f9f018
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596366"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Webová aplikace, která volá webová rozhraní API – přihlášení

Už víte, jak přidat přihlášení do vaší webové aplikace. Naučíte se, že ve [webové aplikaci, která přihlašuje uživatele, přidáte přihlášení](scenario-web-app-sign-user-sign-in.md).

To se liší v tom, že když se uživatel odhlásí z této aplikace nebo z jakékoli aplikace, kterou chcete odebrat z mezipaměti tokenů, tokeny přidružené k uživateli.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Zachycení zpětného volání po odhlášení – jednotné odhlášení

Vaše aplikace může zachytit událost po `logout`, například, aby vymazala záznam mezipaměti tokenů přidružený k účtu, který se odhlásil. Webová aplikace bude ukládat tokeny přístupu pro uživatele v mezipaměti. Zachycení po `logout` zpětného volání umožňuje webové aplikaci odebrat uživatele z mezipaměti tokenů.

# <a name="aspnet-coretabaspnetcore"></a>[Jádro ASP.NET](#tab/aspnetcore)

Tento mechanismus je znázorněn v metodě `AddMsal()` [WebAppServiceCollectionExtensions. cs # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)

**Adresa URL pro odhlášení** , kterou jste zaregistrovali pro vaši aplikaci, vám umožní implementovat jednotné odhlašování. Microsoft Identity Platform `logout` Endpoint zavolá **adresu URL pro odhlášení** registrovanou ve vaší aplikaci. K tomuto volání dochází, pokud se odhlášení iniciovalo z vaší webové aplikace nebo z jiné webové aplikace nebo prohlížeče. Další informace najdete v tématu [jednotné odhlašování](v2-protocols-oidc.md#single-sign-out).

```CSharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: removing the account from MSAL.NET cache
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

Kód pro RemoveAccountAsync je k dispozici v [Microsoft. identity. Web/TokenAcquisition. cs # L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Ukázka ASP.NET neodebírá účty z mezipaměti při globálním odhlašování.

# <a name="javatabjava"></a>[Java](#tab/java)

Ukázka Java neodebírá účty z mezipaměti při globálním odhlašování.

# <a name="pythontabpython"></a>[Python](#tab/python)

Ukázka Pythonu neodebírá účty z mezipaměti při globálním odhlašování.

---

## <a name="next-steps"></a>Další kroky

# <a name="aspnet-coretabaspnetcore"></a>[Jádro ASP.NET](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Získání tokenu pro webovou aplikaci](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Získání tokenu pro webovou aplikaci](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Získání tokenu pro webovou aplikaci](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Získání tokenu pro webovou aplikaci](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---

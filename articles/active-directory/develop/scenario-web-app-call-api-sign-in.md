---
title: Odebrání účtů z mezipaměti tokenů při odhlašování – platforma identit Microsoftu | Azure
description: Přečtěte si, jak odebrat účet z mezipaměti tokenů při odhlašování
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
ms.openlocfilehash: ea18538662dc63876a50f52e9e6a8b3fffb3b35a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758866"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Webová aplikace, která volá webová api: Odebrání účtů z mezipaměti tokenů při globálním odhlašování

Zjistili jste, jak přidat přihlášení do webové aplikace ve [webové aplikaci, která se přihlašuje k uživatelům: Přihlášení a odhlášení](scenario-web-app-sign-user-sign-in.md).

Odhlášení se liší u webové aplikace, která volá webové apis. Když se uživatel odhlásí z vaší aplikace nebo z libovolné aplikace, je nutné odebrat tokeny přidružené k tomuto uživateli z mezipaměti tokenů.

## <a name="intercept-the-callback-after-single-sign-out"></a>Zachycení zpětného volání po jednotném odhlášení

Chcete-li vymazat položku mezipaměti tokenu přidruženou k účtu, který se odhlásil, může vaše aplikace zachytit událost po. `logout` Webové aplikace ukládají přístupové tokeny pro každého uživatele v mezipaměti tokenů. Zachycením po `logout` zpětnévolání, vaše webová aplikace můžete odebrat uživatele z mezipaměti.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Pro ASP.NET Core je mechanismus zachycení `AddMsal()` znázorněn v metodě [WebAppServiceCollectionExtensions.cs#L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157).

Adresa URL odhlášení, kterou jste dříve zaregistrovali pro vaši aplikaci, umožňuje implementovat jednotné odhlášení. Koncový bod platformy `logout` identity Microsoftvolá adresu URL odhlášení. K tomuto volání dojde, pokud se odhlášení spustilo z vaší webové aplikace nebo z jiné webové aplikace nebo prohlížeče. Další informace naleznete [v tématu Jednotné odhlášení](v2-protocols-oidc.md#single-sign-out).

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: Remove the account from MSAL.NET cache.
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache.
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

Kód pro `RemoveAccountAsync` je k dispozici od [Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Ukázka ASP.NET neodebere účty z mezipaměti při globálním odhlašování.

# <a name="java"></a>[Java](#tab/java)

Ukázka jazyka Java neodebere účty z mezipaměti při globálním odhlašování.

# <a name="python"></a>[Python](#tab/python)

Ukázka Pythonu neodebere účty z mezipaměti při globálním odhlašování.

---

## <a name="next-steps"></a>Další kroky

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Získání tokenu pro webovou aplikaci](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Získání tokenu pro webovou aplikaci](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Získání tokenu pro webovou aplikaci](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Získání tokenu pro webovou aplikaci](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---

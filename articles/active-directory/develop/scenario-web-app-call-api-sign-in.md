---
title: Odebrání účtů z mezipaměti tokenů při odhlašování – Microsoft Identity Platform | Azure
description: Zjistěte, jak odebrat účet z mezipaměti tokenů při odhlášení.
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
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758866"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Webová aplikace, která volá webová rozhraní API: Odebrání účtů z mezipaměti tokenů při globálním odhlašování

Zjistili jste, jak přidat přihlášení do webové aplikace ve [webové aplikaci, která se přihlásí uživatelům: přihlášení a](scenario-web-app-sign-user-sign-in.md)odhlášení.

Odhlášení se liší u webové aplikace, která volá webová rozhraní API. Když se uživatel odhlásí z vaší aplikace nebo z jakékoli aplikace, musíte tokeny přidružené k tomuto uživateli odebrat z mezipaměti tokenů.

## <a name="intercept-the-callback-after-single-sign-out"></a>Zachytit zpětné volání po jednotném odhlašování

Pokud chcete vymazat položku mezipaměti tokenů přidruženou k účtu, který se odhlásil, může vaše aplikace zachytit po `logout` události. Webové aplikace ukládají přístupové tokeny pro každého uživatele v mezipaměti tokenů. Po zachytávání po zpětném volání `logout` může webová aplikace odebrat uživatele z mezipaměti.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Pro ASP.NET Core je mechanismus zachytávání znázorněn v metodě `AddMsal()` [WebAppServiceCollectionExtensions. cs # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157).

Adresa URL pro odhlášení, kterou jste předtím zaregistrovali pro vaši aplikaci, vám umožní implementovat jednotné odhlašování. Microsoft Identity Platform `logout` Endpoint volá vaši adresu URL pro odhlášení. K tomuto volání dochází, pokud se odhlášení začalo z vaší webové aplikace nebo z jiné webové aplikace nebo prohlížeče. Další informace najdete v tématu [jednotné odhlašování](v2-protocols-oidc.md#single-sign-out).

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

Kód pro `RemoveAccountAsync` je k dispozici v [Microsoft. identity. Web/TokenAcquisition. cs # L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Ukázka ASP.NET neodebírá účty z mezipaměti při globálním odhlašování.

# <a name="javatabjava"></a>[Java](#tab/java)

Ukázka Java neodebírá účty z mezipaměti při globálním odhlašování.

# <a name="pythontabpython"></a>[Python](#tab/python)

Ukázka Pythonu neodebírá účty z mezipaměti při globálním odhlašování.

---

## <a name="next-steps"></a>Další kroky

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

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

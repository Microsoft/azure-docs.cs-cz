---
title: Ověření oborů a webových rozhraní www pro role aplikací | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak vytvořit chráněné webové rozhraní API a nakonfigurovat kód aplikace.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 816a9620a3486b534f9293084b7c4f5b4f748033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768116"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Chráněné webové rozhraní API: Ověření oborů a rolí aplikací

Tento článek popisuje, jak můžete přidat autorizaci do webového rozhraní API. Tato ochrana zajišťuje, že rozhraní API je volána pouze:

- Aplikace jménem uživatelů, kteří mají správné obory.
- Daemon aplikace, které mají správné role aplikace.

> [!NOTE]
> Fragmenty kódu z tohoto článku jsou extrahovány z následujících vzorků, které jsou plně funkční:
>
> - [ASP.NET přírůstkové kurzy core webového rozhraní API](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) na GitHubu
> - [ukázka webového rozhraní API ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Chcete-li chránit ASP.NET nebo ASP.NET základní `[Authorize]` webové rozhraní API, je nutné přidat atribut k jedné z následujících položek:

- Samotný řadič, pokud chcete, aby byly chráněny všechny akce ovladače
- Akce jednotlivých řadičů pro vaše rozhraní API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Ale tato ochrana nestačí. Zaručuje pouze, že ASP.NET a ASP.NET Core ověřit token. Vaše rozhraní API potřebuje ověřit, že token používaný k volání rozhraní API je požadováno s očekávané deklarace identity. Tato tvrzení vyžadují zejména ověření:

- *Obory,* pokud je rozhraní API voláno jménem uživatele.
- *Role aplikace,* pokud rozhraní API lze volat z aplikace daemon.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Ověření oborů v rozhraních API volaných jménem uživatelů

Pokud klientská aplikace volá vaše rozhraní API jménem uživatele, rozhraní API musí požádat o nosný token, který má specifické obory pro rozhraní API. Další informace naleznete v [tématu Konfigurace kódu | Nosné znamení](scenario-protected-web-api-app-configuration.md#bearer-token).

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result.
        ...
    }
...
}
```

Metoda `VerifyUserHasAnyAcceptedScope` provede něco jako následující kroky:

- Ověřte, zda `http://schemas.microsoft.com/identity/claims/scope` existuje `scp`deklarace s názvem nebo .
- Ověřte, zda deklarace má hodnotu, která obsahuje obor očekávaný rozhraním API.

```csharp
    /// <summary>
    /// When applied to a <see cref="HttpContext"/>, verifies that the user authenticated in the
    /// web API has any of the accepted scopes.
    /// If the authenticated user doesn't have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized error with a message noting which scopes are expected in the token.
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to 
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

Předchozí [ukázkový kód](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) je pro ASP.NET Core. Chcete-ASP.NET, `HttpContext.User` stačí `ClaimsPrincipal.Current`nahradit písmenem `"http://schemas.microsoft.com/identity/claims/scope"` a) a nahradit typ deklarace pomocí `"scp"`. Viz také fragment kódu dále v tomto článku.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Ověření rolí aplikací v rozhraních API volaných aplikacemi daemonu

Pokud je vaše webové rozhraní API voláno [aplikací daemon](scenario-daemon-overview.md), tato aplikace by měla vyžadovat oprávnění aplikace k webovému rozhraní API. Jak je znázorněno v [části Vystavení oprávnění aplikací (role aplikace)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles), rozhraní API zpřístupňuje tato oprávnění. Jedním z `access_as_application` příkladů je role aplikace.

Nyní musíte mít vaše rozhraní API ověřit, že `roles` token, který obdrží, obsahuje deklaraci a že tato deklarace má očekávanou hodnotu. Ověřovací kód je podobný kódu, který ověřuje delegovaná oprávnění s tím rozdílem, že akce ovladače testuje role namísto oborů:

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

Metoda `ValidateAppRole` může být něco takového:

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
}
```

Tentokrát je fragment kódu pro ASP.NET. Pro ASP.NET Core, `ClaimsPrincipal.Current` `HttpContext.User`stačí nahradit `"roles"` . a `"http://schemas.microsoft.com/identity/claims/roles"`nahradit název deklarace jako . Viz také fragment kódu dříve v tomto článku.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Přijímání tokenů pouze pro aplikace, pokud by webové rozhraní API mělo být voláno pouze aplikacemi pro daemon

Uživatelé mohou také používat deklarace rolí ve vzorcích přiřazení uživatelů, jak je znázorněno v [tématu Postup: Přidání rolí aplikací do aplikace a jejich přijetí v tokenu](howto-add-app-roles-in-azure-ad-apps.md). Pokud jsou role přiřaditelné oběma rolím, vrácení rolí se změnami umožní aplikacím přihlásit se jako uživatelé a uživatelé se přihlásit jako aplikace. Doporučujeme deklarovat různé role pro uživatele a aplikace, aby se zabránilo této nejasnosti.

Pokud chcete, aby pouze aplikace pro daemon volat webové rozhraní API, přidejte podmínku, že token je token je token pouze pro aplikaci při ověřování role aplikace.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Kontrola inverzní podmínku umožňuje pouze aplikace, které se přihlásí uživatele mj.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přechod k produkčnímu prostředí](scenario-protected-web-api-production.md)

---
title: Ověřit obory a chráněné webové rozhraní API pro obory a role aplikace | Azure
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet chráněné webové rozhraní API a konfigurovat kód vaší aplikace.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a4ee2679da5065ab9e9b02d4ddb313fab75e78f7
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/26/2020
ms.locfileid: "83845131"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Chráněné webové rozhraní API: ověření oborů a rolí aplikací

Tento článek popisuje, jak můžete přidat autorizaci do webového rozhraní API. Tato ochrana zajišťuje, že rozhraní API je voláno pouze pomocí:

- Aplikace jménem uživatelů, kteří mají správné obory
- Démon aplikace, které mají správnou aplikační role

> [!NOTE]
> Fragmenty kódu z tohoto článku jsou extrahovány z následujících ukázek, které jsou plně funkční:
>
> - [Přírůstkový kurz ASP.NET Core webového rozhraní API](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) na GitHubu
> - [Ukázka webového rozhraní API v ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Chcete-li chránit ASP.NET nebo ASP.NET Core webového rozhraní API, je nutné přidat `[Authorize]` atribut do jedné z následujících položek:

- Kontroler, pokud chcete, aby všechny akce kontroleru byly chráněné
- Akce jednotlivého kontroleru pro vaše rozhraní API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Tato ochrana ale není dostatečná. Garantuje jenom ASP.NET a ASP.NET Core ověřit token. Vaše rozhraní API musí ověřit, že token používaný pro volání rozhraní API je požadován s očekávanými deklaracemi. Tyto deklarace identity vyžadují konkrétně ověření:

- *Rozsahy* , pokud je rozhraní API voláno jménem uživatele.
- *Role aplikace* , pokud je možné rozhraní API volat z aplikace typu démon.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Ověřit obory rozhraní API volaných jménem uživatelů

Pokud klientská aplikace volá vaše rozhraní API jménem uživatele, musí rozhraní API požádat o token nosiče, který má konkrétní obory pro rozhraní API. Další informace najdete v tématu [Konfigurace kódu | Nosný token](scenario-protected-web-api-app-configuration.md#bearer-token)

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

`VerifyUserHasAnyAcceptedScope`Metoda dělá podobný postup jako u následujících kroků:

- Ověřte, jestli existuje deklarace identity s názvem `http://schemas.microsoft.com/identity/claims/scope` nebo `scp` .
- Ověřte, že deklarace identity má hodnotu, která obsahuje obor očekávaný rozhraním API.

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

Předchozí [vzorový kód](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) je určen pro ASP.NET Core. V případě ASP.NET jenom nahraďte `HttpContext.User` `ClaimsPrincipal.Current` argumentem a nahraďte typ deklarace `"http://schemas.microsoft.com/identity/claims/scope"` pomocí `"scp"` . Viz také fragment kódu dále v tomto článku.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Ověření aplikačních rolí v rozhraní API volaných aplikacemi démona

Pokud je vaše webové rozhraní API voláno [aplikací démona](scenario-daemon-overview.md), měla by tato aplikace vyžadovat oprávnění aplikace pro vaše webové rozhraní API. Jak je znázorněno v vystavování [oprávnění aplikace (aplikační role)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles), vaše rozhraní API zpřístupňuje taková oprávnění. Jedním z příkladů je `access_as_application` aplikační role.

Teď musíte mít rozhraní API, abyste ověřili, že token, který obdrží, obsahuje `roles` deklaraci identity a že tato deklarace má očekávanou hodnotu. Ověřovací kód je podobný kódu, který ověřuje delegovaná oprávnění s tím rozdílem, že vaše akce kontroleru testuje role namísto oborů:

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

`ValidateAppRole`Metoda může být podobná této:

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

Tentokrát je fragment kódu určen pro ASP.NET. V případě ASP.NET Core stačí nahradit `ClaimsPrincipal.Current` pomocí `HttpContext.User` a nahradit `"roles"` název deklarace pomocí `"http://schemas.microsoft.com/ws/2008/06/identity/claims/role"` . Viz také fragment kódu dříve v tomto článku.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Přijímají se tokeny jenom pro aplikace, pokud by webové rozhraní API mělo volat jenom aplikace typu démon.

Uživatelé můžou ve vzorcích přiřazení uživatelů také používat deklarace identity, jak je znázorněno v tématu [Postupy: Přidání rolí aplikace do aplikace a jejich přijetí v tokenu](howto-add-app-roles-in-azure-ad-apps.md). Pokud se role přiřazují oběma uživatelům, kontrola rolí umožní aplikacím přihlásit se jako uživatelé a uživatelé, aby se přihlásili jako aplikace. Doporučujeme, abyste pro uživatele a aplikace deklarovali různé role, aby nedocházelo k nejasnostem.

Pokud chcete, aby vaše webové rozhraní API volaly jenom aplikace typu démon, přidejte podmínku, která je tokenem jenom pro aplikace při ověřování role aplikace.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Kontrola inverzní podmínky umožňuje pouze aplikacím, které přihlásí uživatele k volání rozhraní API.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přechod k produkčnímu prostředí](scenario-protected-web-api-production.md)

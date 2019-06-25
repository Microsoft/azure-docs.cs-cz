---
title: Chráněné webové rozhraní API – konfigurace kódu aplikace | Azure Active Directory
description: Zjistěte, jak sestavit chráněné webové rozhraní API a konfigurovat kódu vaší aplikace.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: b700825be9a7fe23fe4b50a2d69d4de71f7dc038
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116451"
---
# <a name="protected-web-api---adding-authorization-to-your-api"></a>Chráněné webové rozhraní API – přidání ověřování do vašeho rozhraní API

Tento článek popisuje, jak můžete přidat ověření pro vaše webové rozhraní API. Tato ochrana se zajistí, že je pouze volán:

- aplikace jménem uživatelů s správné obory 
- nebo aplikace démonů s správné aplikační role.

Technologie ASP.NET nebo ASP.NET Core webového rozhraní API, které se mají chránit, bude potřeba přidat `[Authorize]` atribut na:

- samotný Pokud chcete, aby všechny akce daného kontroleru, který se má chránit řadič
- nebo akce jednotlivých kontroleru pro vaše rozhraní API.

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Ale tato ochrana nestačí. Pouze zaručil této technologie ASP.NET nebo ASP.NET Core se ověří token. Chcete-li ověřit, že je token používaný k volání webového rozhraní API byla vyžádána se deklarace identity, která se podle konkrétně potřebuje vaše rozhraní API:

- **obory** Pokud se volá rozhraní API jménem uživatele
- **role aplikace** Pokud rozhraní API může být volána z aplikace démona.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>Ověření oborů v rozhraní API volá jménem uživatelů

Pokud vaše rozhraní API je volán klientskou aplikaci jménem uživatele, pak je potřeba požádat o token nosiče s konkrétní obory pro rozhraní API (viz [konfiguraci kódu | Nosný token](scenario-protected-web-api-app-configuration.md#bearer-token))

```CSharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The Web API will only accept tokens 1) for users, 2) having the `access_as_user` scope for
    /// this API
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result
        ...
    }
...
}
```

`VerifyUserHasAnyAcceptedScope` Metoda byste udělali nějak takto:

- Ověřte, zda je deklarací identity s názvem `http://schemas.microsoft.com/identity/claims/scope` nebo `scp`
- Ověřte, že deklarace identity má hodnotu obsahující obor očekávané rozhraním API.

```CSharp
    /// <summary>
    /// When applied to an <see cref="HttpContext"/>, verifies that the user authenticated in the 
    /// Web API has any of the accepted scopes.
    /// If the authenticated user does not have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized with the message telling which scopes are expected in the token
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

Tento ukázkový kód je pro ASP.NET Core. Pro nahrazení jenom ASP.NET `HttpContext.User` podle `ClaimsPrincipal.Current`a typ deklarace identity `"http://schemas.microsoft.com/identity/claims/scope"` podle `"scp"` (viz také následující fragment kódu)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Ověřuje se, jestli role aplikace v rozhraní API volané aplikace démonů

Pokud vaše webové rozhraní API je volán [démon aplikace](scenario-daemon-overview.md), pak tuto aplikaci potřeba oprávnění aplikace, aby vaše webové rozhraní API. Zaznamenali jsme v [scenario-protected-web-api-app-registration.md#how-to-expose-application-permissions--app-roles-], že vaše rozhraní API zveřejňuje taková oprávnění (například jako `access_as_application` role aplikace).
Teď je potřeba mít svá rozhraní API, ověřte, zda obsahuje token obdržel `roles` deklarací identity a zda tato deklarace identity se očekává, že hodnota. Je podobný kód, který ověřuje delegovaná oprávnění, s výjimkou, že místo pro testování kódu dělat toto ověření `scopes`, testovat vaše akce kontroleru pro `roles`:

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

`ValidateAppRole()` Metoda může být vypadat přibližně takto:

```CSharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case we look for a `role` value of `access_as_application`
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

Tento ukázkový kód je pro technologii ASP.NET. Pro ASP.NET Core, stačí nahradit `ClaimsPrincipal.Current` podle `HttpContext.User` a `"roles"` název pomocí deklarace `"http://schemas.microsoft.com/identity/claims/roles"` (viz výše uvedeném fragmentu kódu)

### <a name="accepting-app-only-tokens-if-the-web-api-should-only-be-called-by-daemon-apps"></a>Pokud webové rozhraní API by měla být volána pouze pomocí aplikace démonů přijímá pouze tokeny aplikací

`roles` Deklarace identity se používá také pro uživatele ve vzorech pro přiřazení uživatele (viz [jak: Přidání rolí aplikace ve vaší aplikaci a přijímat je v tokenu](howto-add-app-roles-in-azure-ad-apps.md)). Kontroluje se tak, aby role vám umožní aplikace přihlásit jako uživatele a naopak, pokud jsou dané role přiřadit k oběma. Doporučujeme mít různé role pro uživatele a aplikace, aby nedošlo k této záměně deklarovaný.

Pokud chcete povolit pouze démon aplikace volat webové rozhraní API, budete chtít přidat podmínku, když ověřujete role aplikace, že váš token je token jen pro aplikace:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid");
string sub = ClaimsPrincipal.Current.FindFirst("sub");
bool isAppOnlyToken = oid == sub;
```

Kontrola stavu inverzní vám umožní jenom aplikace, které uživatele, přihlaste se k volání rozhraní API.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přejít do produkčního prostředí](scenario-protected-web-api-production.md)

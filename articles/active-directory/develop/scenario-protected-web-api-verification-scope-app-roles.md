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
ms.openlocfilehash: 23ff03316a1f9409d4d6e4b7ddf52d0c8cc7a909
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551545"
---
# <a name="protected-web-api-adding-authorization-to-your-api"></a>Chráněné webové rozhraní API: Přidání ověřování do vašeho rozhraní API

Tento článek popisuje, jak můžete přidat ověřování do webového rozhraní API. Tato ochrana zajistí, že se pouze volá rozhraní API:

- Aplikace jménem uživatelů, kteří mají správné rozsahy.
- Aplikace démonů, které mají správné aplikační role.

K ochraně webového rozhraní API ASP.NET/ASP.NET Core, budete muset přidat `[Authorize]` atribut na jednu z těchto:

- Kontroler samostatně, pokud chcete, aby všechny akce daného kontroleru, který se má chránit
- Akce jednotlivých kontroleru pro vaše rozhraní API

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Ale tato ochrana nestačí. Pouze zaručuje, že základní ASP.NET/ASP.NET ověří token. Chcete-li ověřit, že je token používaný k volat vaše webové rozhraní API byla vyžádána se deklarace identity se očekává, že, zejména potřebuje vaše rozhraní API:

- *Obory*, pokud se volá rozhraní API jménem uživatele.
- *Role aplikace*, pokud rozhraní API může být volána z aplikace démona.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>Ověření oborů v rozhraní API volá jménem uživatelů

Pokud vaše rozhraní API je volán klientskou aplikaci jménem uživatele, je potřeba požádat o token nosiče, který obsahuje konkrétní obory pro rozhraní API. (Viz [konfiguraci kódu | Nosný token](scenario-protected-web-api-app-configuration.md#bearer-token).)

```CSharp
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

`VerifyUserHasAnyAcceptedScope` Metoda byste udělali nějak takto:

- Ověřte, zda je deklarace identity s názvem `http://schemas.microsoft.com/identity/claims/scope` nebo `scp`.
- Ověřte, zda se deklarace má hodnotu, která obsahuje obor očekávané rozhraním API.

```CSharp
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

Tento ukázkový kód je pro ASP.NET Core. Pro technologii ASP.NET, stačí nahradit `HttpContext.User` s `ClaimsPrincipal.Current`a nahraďte typ deklarace identity `"http://schemas.microsoft.com/identity/claims/scope"` s `"scp"`. (Viz také dále v tomto článku fragmentu kódu.)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Ověřuje se, jestli role aplikace v rozhraní API volané aplikace démonů

Pokud vaše webové rozhraní API je volán [démon aplikace](scenario-daemon-overview.md), tato aplikace by měla vyžadovat aplikace oprávnění ke své webové rozhraní API. Zaznamenali jsme v [vystavení oprávnění aplikace (role aplikace)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles) , že vaše rozhraní API zveřejňuje taková oprávnění (například `access_as_application` role aplikace).
Teď je potřeba mít svá rozhraní API, ověřte, zda obsahuje token obdržel `roles` deklarace identity a zda tato deklarace identity se očekává, že hodnota. Je podobný kód, který ověřuje delegovaná oprávnění, s výjimkou, že místo pro testování kódu dělat toto ověření `scopes`, testovat vaše akce kontroleru pro `roles`:

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

Tento ukázkový kód je pro technologii ASP.NET. Pro ASP.NET Core, stačí nahradit `ClaimsPrincipal.Current` s `HttpContext.User`a nahraďte `"roles"` deklarace názvu s `"http://schemas.microsoft.com/identity/claims/roles"`. (Viz také fragment kódu výše v tomto článku.)

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Pokud webové rozhraní API by měla být volána pouze pomocí aplikace démonů přijímat tokeny jen pro aplikace

`roles` Deklarace identity se používá také pro uživatele ve vzorech pro přiřazení uživatele. (Viz [jak: Přidání rolí aplikace ve vaší aplikaci a přijímat je v tokenu](howto-add-app-roles-in-azure-ad-apps.md).) Kontroluje se tak, aby role vám umožní aplikace přihlásit jako uživatele a naopak, pokud jsou dané role přiřadit k oběma. Doporučujeme vám, že deklarujete různé role pro uživatele a aplikace, aby se zabránilo potíže.

Pokud chcete povolit pouze aplikace démonů volání webového rozhraní API, přidejte podmínku, když ověřujete role aplikace, že váš token je token jen pro aplikace:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid");
string sub = ClaimsPrincipal.Current.FindFirst("sub");
bool isAppOnlyToken = oid == sub;
```

Kontrola stavu inverzní vám umožní jenom aplikace s možností přihlášení uživatele k volání rozhraní API.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přejít do produkčního prostředí](scenario-protected-web-api-production.md)

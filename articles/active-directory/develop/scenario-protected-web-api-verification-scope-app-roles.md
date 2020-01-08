---
title: Ověřit obory & App role Protected Web API | Azure
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet chráněné webové rozhraní API a konfigurovat kód vaší aplikace.
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
ms.openlocfilehash: 2eb9cdf68bf5103776d50db28e9e6facc89c9278
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423687"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Chráněné webové rozhraní API: ověření oborů a rolí aplikací

Tento článek popisuje, jak můžete přidat autorizaci do webového rozhraní API. Tato ochrana zajišťuje, že rozhraní API je voláno pouze pomocí:

- Aplikace jménem uživatelů, kteří mají správné obory
- Démon aplikace, které mají správnou aplikační role

> [!NOTE]
> Fragmenty kódu z tohoto článku jsou extrahovány z následujících ukázek, které jsou plně funkční
>
> - [Přírůstkový kurz ASP.NET Core webového rozhraní API](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) na GitHubu
> - [Ukázka webového rozhraní API v ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

K ochraně webového rozhraní API ASP.NET/ASP.NET Core budete muset přidat atribut `[Authorize]` v jednom z těchto:

- Samotný kontroler, pokud chcete všechny akce kontroleru chránit
- Akce jednotlivého kontroleru pro vaše rozhraní API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Tato ochrana ale není dostatečná. Garantuje pouze to, že ASP.NET/ASP.NET Core ověří token. Vaše rozhraní API musí ověřit, zda byl token používaný k volání webového rozhraní API požadován s deklaracemi, které očekává, zejména:

- *Obory*, pokud je rozhraní API voláno jménem uživatele.
- *Role aplikace*, pokud je možné rozhraní API volat z aplikace démona.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>Ověřování oborů v rozhraních API volaných jménem uživatelů

Pokud vaše rozhraní API volá klientská aplikace jménem uživatele, musí požádat o nosný token, který má konkrétní obory pro rozhraní API. (Viz [Konfigurace kódu | Nosný token](scenario-protected-web-api-app-configuration.md#bearer-token).)

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

Metoda `VerifyUserHasAnyAcceptedScope` by vypadala nějak takto:

- Ověřte, že existuje deklarace identity s názvem `http://schemas.microsoft.com/identity/claims/scope` nebo `scp`.
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

Tento [vzorový kód](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) je určen pro ASP.NET Core. V případě ASP.NET stačí nahradit `HttpContext.User` `ClaimsPrincipal.Current`a nahradit typ deklarace `"http://schemas.microsoft.com/identity/claims/scope"` `"scp"`. (Viz také fragment kódu dále v tomto článku.)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Ověřování rolí aplikace v rozhraní API volaných aplikacemi démona

Pokud je vaše webové rozhraní API voláno [aplikací démona](scenario-daemon-overview.md), měla by tato aplikace vyžadovat oprávnění aplikace pro vaše webové rozhraní API. Zjistili jsme, že máte oprávnění k vystavování [oprávnění aplikací (aplikační role)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles) , které vaše rozhraní API zpřístupňuje (například role `access_as_application` aplikace).
Teď musíte mít rozhraní API, abyste ověřili, že token, který obdržel, obsahuje `roles` deklaraci identity a že tato deklarace má hodnotu, kterou očekává. Kód, který provádí toto ověření, je podobný kódu, který ověřuje delegovaná oprávnění s tím rozdílem, že místo testování pro `scopes`bude akce kontroleru testována `roles`:

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

Metoda `ValidateAppRole()` může být podobná této:

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

Tentokrát je fragment kódu určen pro ASP.NET. V případě ASP.NET Core stačí nahradit `ClaimsPrincipal.Current` `HttpContext.User`a nahradit název `"roles"` deklarace `"http://schemas.microsoft.com/identity/claims/roles"`. (Viz také fragment kódu dříve v tomto článku.)

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Přijímají se tokeny jenom pro aplikace, pokud by webové rozhraní API mělo volat jenom aplikace typu démon.

Deklarace `roles` se také používá pro uživatele ve vzorcích přiřazování uživatelů. (Viz [Postup: Přidání rolí aplikace do aplikace a jejich přijetí v tokenu](howto-add-app-roles-in-azure-ad-apps.md).) Takže jenom kontrola rolí umožní aplikacím přihlašovat se jako uživatelé a jiným způsobem, pokud se role přiřazují k oběma. Doporučujeme, abyste pro uživatele a aplikace deklarovali různé role, aby nedocházelo k nejasnostem.

Pokud chcete, aby aplikace typu démon mohli volat vaše webové rozhraní API, přidejte podmínku, když ověříte roli aplikace, že se jedná o token jenom pro aplikaci:

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Při kontrole inverzní podmínky budou povoleny pouze aplikace, které přihlásí uživatele k volání rozhraní API.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přesunout do produkčního prostředí](scenario-protected-web-api-production.md)

---
title: Volání zabezpečené ASP.NET webové rozhraní api v Azure Active Directory B2C | Microsoft Docs
description: Sestavení .NET webové aplikace a volání webového rozhraní api pomocí přístupových tokenů Azure Active Directory B2C a OAuth 2.0.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/17/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 39603cf103a8ff2656c76843aeae36b17936d13a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712398"
---
# <a name="azure-ad-b2c-call-a-net-web-api-from-a-net-web-app"></a>Azure AD B2C: Volání webového rozhraní API .NET z webové aplikace .NET

Pomocí Azure AD B2C můžete přidat funkce správy identit výkonné webové aplikace a webové rozhraní API. Tento článek popisuje, jak požádat o přístupové tokeny a zkontrolujte volání z webové aplikace .NET "seznam úkolů".NET webové rozhraní api.

Tento článek nepopisuje, jak implementovat přihlášení, registrace a správy profilů pomocí Azure AD B2C. Zaměřuje se na volání webových rozhraní API po již byl uživatel ověřen. Pokud jste to ještě neudělali, měli byste:

* Začínáme s [webové aplikace .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
* Začínáme s [webové rozhraní api .NET](active-directory-b2c-devquickstarts-api-dotnet.md)

## <a name="prerequisite"></a>Požadavek

K vytvoření webové aplikace, která volá webové rozhraní api, potřebujete:

1. [Vytvoření klienta Azure AD B2C](active-directory-b2c-get-started.md).
2. [Zaregistrovat webového rozhraní api](active-directory-b2c-app-registration.md#register-a-web-api).
3. [Webovou aplikaci zaregistrovat](active-directory-b2c-app-registration.md#register-a-web-app).
4. [Nastavit zásady](active-directory-b2c-reference-policies.md).
5. [Udělení oprávnění aplikace prostřednictvím webu webového rozhraní api](active-directory-b2c-access-tokens.md#publishing-permissions).

> [!IMPORTANT]
> Klientská aplikace a webové rozhraní API musí používat stejný adresář Azure AD B2C.
>

## <a name="download-the-code"></a>Stáhněte si kód

Kód k tomuto kurzu se udržuje na [GitHubu](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Ukázku můžete klonovat spuštěním:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Po stažení ukázkového kódu otevřete soubor Visual Studio .sln, abyste mohli začít. Soubor řešení obsahuje dva projekty: `TaskWebApp` a `TaskService`. `TaskWebApp` je webová aplikace MVC, který uživatel komunikuje. `TaskService` je back-endové webové rozhraní API aplikace, které ukládá seznam úkolů každého uživatele. Tento článek nepopisuje sestavování `TaskWebApp` webovou aplikaci nebo `TaskService` webové rozhraní api. Naučte se vytvářet webové aplikace .NET pomocí Azure AD B2C, najdete v tématu naše [kurz .NET pro webové aplikace](active-directory-b2c-devquickstarts-web-dotnet-susi.md). Naučte se vytvářet rozhraní .NET webové rozhraní API zabezpečené pomocí Azure AD B2C, najdete v tématu naše [kurz webové rozhraní API .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Aktualizace konfigurace Azure AD B2C

Naše ukázka je nakonfigurovaná k použití zásad a ID klienta naše ukázkového tenanta. Pokud chcete použít vlastního klienta:

1. Otevřete `web.config` v projektu `TaskService` a nahraďte následující hodnoty:

    * `ida:Tenant` názvem vašeho tenanta
    * `ida:ClientId` pomocí svého ID aplikace webového rozhraní api
    * `ida:SignUpSignInPolicyId` názvem zásady registrace/přihlášení

2. Otevřete `web.config` v projektu `TaskWebApp` a nahraďte následující hodnoty:

    * `ida:Tenant` názvem vašeho tenanta
    * `ida:ClientId` identifikátorem webového aplikace
    * `ida:ClientSecret` tajným klíčem webové aplikace
    * `ida:SignUpSignInPolicyId` názvem zásady registrace/přihlášení
    * `ida:EditProfilePolicyId` názvem zásady pro úpravu profilu
    * `ida:ResetPasswordPolicyId` názvem zásady pro resetování hesla



## <a name="requesting-and-saving-an-access-token"></a>Požaduje a ukládání token přístupu

### <a name="specify-the-permissions"></a>Zadejte oprávnění

Aby bylo možné volání webového rozhraní API, budete muset ověřit uživatele (pomocí zásad služby registrace-množství nebo přihlášení) a [přijímat přístupový token](active-directory-b2c-access-tokens.md) z Azure AD B2C. Chcete-li získat přístupový token, nejprve musíte zadat chcete přístupový token k udělení oprávnění. Oprávnění jsou určené v `scope` parametr při provádění požadavku `/authorize` koncový bod. Chcete-li například získat přístupový token s oprávnění "číst" prostředků aplikace, kterou je identifikátor ID URI aplikace z `https://contoso.onmicrosoft.com/tasks`, oboru by `https://contoso.onmicrosoft.com/tasks/read`.

K určení oboru ve výběru, otevřete soubor `App_Start\Startup.Auth.cs` a definovat `Scope` proměnné v OpenIdConnectAuthenticationOptions.

```CSharp
// App_Start\Startup.Auth.cs

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ...

            // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
            Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
        }
    );
}
```

### <a name="exchange-the-authorization-code-for-an-access-token"></a>Exchange autorizační kód pro token přístupu

Po dokončení registrace nebo přihlášení zkušeností uživatele s vaší aplikace obdrží autorizační kód z Azure AD B2C. Middleware OWIN OpenID Connect uloží kód, ale nebude exchange pro přístupový token. Můžete použít [MSAL knihovny](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) aby exchange. V našem ukázce jsme nakonfigurovali zpětné volání oznámení do middleware OpenID Connect vždy, když je obdržena autorizační kód. Zpětné volání používáme MSAL exchange kód pro token a uložení tokenu do mezipaměti.

```CSharp
/*
* Callback function when an authorization code is received
*/
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
    // Extract the code from the response notification
    var code = notification.Code;

    var userObjectId = notification.AuthenticationTicket.Identity.FindFirst(ObjectIdElement).Value;
    var authority = String.Format(AadInstance, Tenant, DefaultPolicy);
    var httpContext = notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase;

    // Exchange the code for a token. Make sure to specify the necessary scopes
    ClientCredential cred = new ClientCredential(ClientSecret);
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                            RedirectUri, cred, new NaiveSessionCache(userObjectId, httpContext));
    var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { ReadTasksScope, WriteTasksScope }, code, DefaultPolicy);
}
```

## <a name="calling-the-web-api"></a>Volání webového rozhraní API

Tato část popisuje postup použití tokenu obdržel během registrace-množství nebo přihlášení s Azure AD B2C, aby měli přístup na webové rozhraní API.

### <a name="retrieve-the-saved-token-in-the-controllers"></a>Načtení uložené token v na řadiče

`TasksController` Je zodpovědný za komunikaci s webové rozhraní API a pro odesílání požadavků HTTP do rozhraní API číst, vytvářet a odstraňovat úlohy. Protože rozhraní API je zabezpečená službou Azure AD B2C, musíte nejdřív načíst token, který jste uložili v předchozím kroku.

```CSharp
// Controllers\TasksController.cs

/*
* Uses MSAL to retrieve the token from the cache
*/
private async void acquireToken(String[] scope)
{
    string userObjectID = ClaimsPrincipal.Current.FindFirst(Startup.ObjectIdElement).Value;
    string authority = String.Format(Startup.AadInstance, Startup.Tenant, Startup.DefaultPolicy);

    ClientCredential credential = new ClientCredential(Startup.ClientSecret);

    // Retrieve the token using the provided scopes
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                        Startup.RedirectUri, credential,
                                        new NaiveSessionCache(userObjectID, this.HttpContext));
    AuthenticationResult result = await app.AcquireTokenSilentAsync(scope);

    accessToken = result.Token;
}
```

### <a name="read-tasks-from-the-web-api"></a>Čtení úlohy z webové rozhraní API

Když máte token, abyste ho připojili k HTTP `GET` v požadavku `Authorization` záhlaví bezpečně volat `TaskService`:

```CSharp
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        // Retrieve the token with the specified scopes
        acquireToken(new string[] { Startup.ReadTasksScope });

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, apiEndpoint);

        // Add token to the Authorization header and make the request
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
        HttpResponseMessage response = await client.SendAsync(request);

        // Handle response ...
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Vytvářet a odstraňovat úlohy na webové rozhraní API

Postupujte podle stejného vzoru při odesílání `POST` a `DELETE` požadavky na webové rozhraní API, pomocí MSAL k získání přístupového tokenu z mezipaměti.

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Nakonec sestavte a spusťte obě aplikace. Registrace a přihlášení a vytvořte úkoly pro přihlášeného uživatele. Odhlásit se a přihlaste se jako jiný uživatel. Vytvořte úkoly pro tohoto uživatele. Všimněte si, jak jsou úlohy ukládají pro každého uživatele na volání rozhraní API, protože rozhraní API extrahuje identitu uživatele z tokenu obdrží. Zkuste taky přehrávání s obory. Odeberte oprávnění k "zápisu" a potom zkuste přidat úloha. Ujistěte se odhlásit se při každé změně oboru.


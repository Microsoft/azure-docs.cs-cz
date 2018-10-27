---
title: Volání zabezpečené ASP.NET web api v Azure Active Directory B2C | Dokumentace Microsoftu
description: Vytvoření aplikace .NET Web app a volání webového rozhraní api pomocí přístupových tokenů Azure Active Directory B2C a OAuth 2.0.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 38ad6d8f3746d856d8c0a73520d55fad9d8344e6
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50157917"
---
# <a name="azure-ad-b2c-call-a-net-web-api-from-a-net-web-app"></a>Azure AD B2C: Volání webového rozhraní API .NET z webové aplikace .NET

Pomocí Azure AD B2C, můžete přidat funkce správy identity výkonné do vašich webových aplikacích a webových rozhraní API. Tento článek popisuje, jak požádat o tokeny přístupu a zkontrolujte volání z webové aplikace .NET "seznam úkolů".NET webové rozhraní api.

Tento článek nepopisuje, jak implementovat přihlášení, registrace a správy profilů pomocí Azure AD B2C. Jakmile uživatel je již ověřen, zaměřuje se na volání webových rozhraní API. Pokud jste tak dosud neučinili, měli byste:

* Začněte [webové aplikace .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
* Začněte [webové rozhraní api .NET](active-directory-b2c-devquickstarts-api-dotnet.md)

## <a name="prerequisite"></a>Požadavek

Chcete-li vytvořit webovou aplikaci, která volá webové rozhraní api, musíte:

1. [Vytvoření tenanta Azure AD B2C](active-directory-b2c-get-started.md).
2. [Registrace webové rozhraní api](active-directory-b2c-app-registration.md#register-a-web-api).
3. [Zaregistrovat webovou aplikaci](active-directory-b2c-app-registration.md#register-a-web-app).
4. [Nastavení zásad](active-directory-b2c-reference-policies.md).
5. [Udělit oprávnění k použití webové aplikaci webového rozhraní api](active-directory-b2c-access-tokens.md#publishing-permissions).

> [!IMPORTANT]
> Klientská aplikace a webové rozhraní API musí používat stejný adresář Azure AD B2C.
>

## <a name="download-the-code"></a>Stáhněte si kód

Kód k tomuto kurzu se udržuje na [GitHubu](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Ukázku můžete klonovat spuštěním:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Po stažení ukázkového kódu otevřete soubor Visual Studio .sln, abyste mohli začít. Soubor řešení obsahuje dva projekty: `TaskWebApp` a `TaskService`. `TaskWebApp` je webová aplikace MVC, kterou uživatel komunikuje. `TaskService` je back-endové webové rozhraní API aplikace, které ukládá seznam úkolů každého uživatele. Tento článek nezahrnuje sestavení `TaskWebApp` webovou aplikaci nebo `TaskService` webové rozhraní api. Naučte se vytvářet webové aplikace .NET pomocí Azure AD B2C, najdete v našich [kurz webových aplikací .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md). Informace o sestavení .NET webového rozhraní API zabezpečené pomocí Azure AD B2C, najdete v našich [kurz vývoje webové rozhraní API .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Aktualizace konfigurace Azure AD B2C

Naše ukázka je nakonfigurovaná k použití zásad a ID klienta naše ukázkového tenanta. Pokud chcete použít vlastního tenanta:

1. Otevřete `web.config` v projektu `TaskService` a nahraďte následující hodnoty:

    * `ida:Tenant` názvem vašeho tenanta
    * `ida:ClientId` identifikátorem aplikace webového rozhraní api
    * `ida:SignUpSignInPolicyId` názvem zásady registrace/přihlášení

2. Otevřete `web.config` v projektu `TaskWebApp` a nahraďte následující hodnoty:

    * `ida:Tenant` názvem vašeho tenanta
    * `ida:ClientId` identifikátorem webového aplikace
    * `ida:ClientSecret` tajným klíčem webové aplikace
    * `ida:SignUpSignInPolicyId` názvem zásady registrace/přihlášení
    * `ida:EditProfilePolicyId` názvem zásady pro úpravu profilu
    * `ida:ResetPasswordPolicyId` názvem zásady pro resetování hesla



## <a name="requesting-and-saving-an-access-token"></a>Vyžádání a ukládání přístupového tokenu

### <a name="specify-the-permissions"></a>Zadejte oprávnění

Aby mohl uskutečnit volání webového rozhraní API, je potřeba se přihlásit uživatele (pomocí zásady přihlášení-registrace/přihlášení) a [přijímat přístupový token](active-directory-b2c-access-tokens.md) z Azure AD B2C. Pro získání přístupového tokenu, nejprve musíte zadat byste chtěli přístupový token k udělení oprávnění. Oprávnění jsou určené v `scope` parametr, když vytvoříte žádost o `/authorize` koncového bodu. Například pro získání přístupového tokenu se aplikace prostředků, která obsahuje identifikátor URI ID aplikace oprávnění "číst" `https://contoso.onmicrosoft.com/tasks`, bude rozsah `https://contoso.onmicrosoft.com/tasks/read`.

K zadání oboru v naší ukázce, otevřete soubor `App_Start\Startup.Auth.cs` a definovat `Scope` proměnné v OpenIdConnectAuthenticationOptions.

```CSharp
// App_Start\Startup.Auth.cs

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ...

            // Specify the scope by appending all of the scopes requested into one string (separated by a blank space)
            Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
        }
    );
}
```

### <a name="exchange-the-authorization-code-for-an-access-token"></a>Exchange autorizační kód pro přístupový token

Po dokončení prostředí registrace nebo přihlášení jako uživatel aplikace obdrží autorizační kód z Azure AD B2C. Middleware OWIN OpenID Connect uloží kód, ale nebude výměně pro přístupový token. Můžete použít [knihovna MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) provést výměnu. V naší ukázce jsme nakonfigurovali zpětné volání oznámení do middlewaru OpenID Connect pokaždé, když se obdrží autorizační kód. Při zpětném volání jsme použití MSAL k výměně kód pro token a uložte tokenu do mezipaměti.

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

Tato část popisuje, jak použít token přijatý během přihlášení-registrace/přihlášení pomocí Azure AD B2C, aby měl přístup k webovému rozhraní API.

### <a name="retrieve-the-saved-token-in-the-controllers"></a>Načtení uložené tokenu v kontrolery

`TasksController` Je zodpovídá za komunikaci s webovým rozhraním API a odesílání požadavků HTTP do rozhraní API, číst, vytvářet a odstraňovat úkoly. Protože je rozhraní API zabezpečené pomocí Azure AD B2C, musíte nejdřív načíst token, který jste uložili v předchozím kroku.

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

### <a name="read-tasks-from-the-web-api"></a>Čtení úkolů ve webovém rozhraní API

Když máte token, abyste ho připojili k HTTP `GET` žádosti `Authorization` hlavičky, má bezpečné volat `TaskService`:

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

### <a name="create-and-delete-tasks-on-the-web-api"></a>Vytvářet a odstraňovat úkoly na webové rozhraní API

Postupujte podle stejného vzoru při odeslání `POST` a `DELETE` požadavky do webového rozhraní API s využitím MSAL k získání přístupového tokenu z mezipaměti.

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Nakonec sestavte a spusťte obě aplikace. Registrace a přihlášení a vytvoření úlohy pro přihlášeného uživatele. Odhlaste se a přihlaste se jako jiný uživatel. Vytvořte úkoly pro tohoto uživatele. Všimněte si, jak se úlohy ukládají pro každého uživatele na rozhraní API, protože rozhraní API extrahuje identitu uživatele z tokenu přijme. Zkuste taky přehrávání s obory. Odeberte oprávnění k "write" a potom zkuste přidat úkol. Jenom nezapomeňte odhlásit se při každé změně oboru.


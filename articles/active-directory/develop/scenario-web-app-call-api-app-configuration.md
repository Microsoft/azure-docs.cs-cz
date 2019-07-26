---
title: Webová aplikace, která volá webová rozhraní API (konfigurace kódu) – Microsoft Identity Platform
description: Naučte se, jak vytvořit webovou aplikaci, která volá webová rozhraní API (konfigurace kódu aplikace).
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad995908ff20d123a77b511d127652aa17c4634
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494530"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Webová aplikace, která volá konfiguraci rozhraní Web API – Code

Jak je vidět ve [scénáři přihlášení k webové aplikaci](scenario-web-app-sign-user-overview.md), vzhledem k tom, že podepisování uživatele je delegováno na middleware Open ID Connect (OIDC), chcete se zapojit do procesu OIDC. Způsob, jak to udělat, se liší v závislosti na použitém rozhraní (tady ASP.NET a ASP.NET Core), ale na konci se přihlásíte k odběru událostí middleware OIDC. Princip je následující:

- ASP.NET nebo ASP.NET Core si umožníte, aby si vyžádal autorizační kód. Tímto ASP.NET/ASP.NET Core umožní uživateli přihlásit se a vyjádřit souhlas,
- Přihlásíte se k odběru žádosti o autorizační kód webovou aplikací.
- Po přijetí kódu ověření použijete knihovny MSAL k uplatnění kódu a výsledných přístupových tokenů a k aktualizaci úložiště tokenů v mezipaměti tokenů. Odtud můžete mezipaměť použít v jiných částech aplikace k tichému získání dalších tokenů.

> [!NOTE]
> Fragmenty kódu z tohoto článku se extrahují z následujících ukázek na GitHubu, které jsou plně funkční:
>
> - [Přírůstkový kurz pro ASP.NET Core webovou aplikaci](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)
> - [Ukázka webové aplikace v ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)

## <a name="libraries-supporting-web-app-scenarios"></a>Knihovny podporující scénáře webových aplikací

Knihovny podporující tok autorizačního kódu pro Web Apps jsou:

| Knihovna MSAL | Popis |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Podporované platformy jsou .NET Framework a .NET Core Platforms (ne UWP, Xamarin. iOS a Xamarin. Android, protože tyto platformy slouží k vytváření veřejných klientských aplikací). |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Vývoj v průběhu verze Public Preview |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL. Kompilátor | Vývoj v průběhu verze Public Preview |

## <a name="aspnet-core-configuration"></a>Konfigurace ASP.NET Core

V ASP.NET Core věci se v `Startup.cs` souboru vyskytují. Budete chtít přihlašovat se k odběru `OnAuthorizationCodeReceived` události Open ID Connect a z této události volat MSAL. Metoda `AcquireTokenFromAuthorizationCode` netto, která má vliv na ukládání do mezipaměti tokenů, přístupového tokenu pro požadovaný `scopes`a obnovovací token, který se použije k aktualizaci přístupového tokenu, když se blíží konec platnosti, nebo když se má získat token jménem stejného uživatele , ale pro jiný prostředek.

```CSharp
string[] scopes = new string[]{ "user.read" };
string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };
```

Komentáře v následujícím kódu vám pomůžou pochopit některé z štychů tkaní MSAL.NET a ASP.NET Core. V [přírůstkovém kurzu ASP.NET Core webové aplikace](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph) jsou k dispozici úplné podrobnosti, kapitola 2

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add("offline_access");
   options.Scope.Add("user.read"); // for instance

   // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
   // where it will be usable from Controllers later (through the TokenAcquisition service)
   var handler = options.Events.OnAuthorizationCodeReceived;
   options.Events.OnAuthorizationCodeReceived = async context =>
   {
    // As AcquireTokenByAuthorizationCode is asynchronous we want to tell ASP.NET core
    // that we are handing the code even if it's not done yet, so that it does 
    // not concurrently call the Token endpoint.
    context.HandleCodeRedemption();

    // Call MSAL.NET AcquireTokenByAuthorizationCode
    var application = BuildConfidentialClientApplication(context.HttpContext,
                                                         context.Principal);
    var result = await application.AcquireTokenByAuthorizationCode(scopes.Except(scopesRequestedByMsalNet), context.ProtocolMessage.Code)
                                  .ExecuteAsync();

    // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it
    // and will not send the OAuth 2.0 request in case a further call to
    // AcquireTokenByAuthorizationCodeAsync in the future for incremental consent 
    // (getting a code requesting more scopes)
    // Share the ID Token so that the identity of the user is known in the application (in 
    // HttpContext.User)
    context.HandleCodeRedemption(null, result.IdToken);

    // Call the previous handler if any
    await handler(context);
   };
```

V ASP.NET Core vytváření důvěrných klientských aplikací používá informace, které jsou v objektu HttpContext. To `HttpContext` ví o adrese URL webové aplikace a přihlášeném uživateli ( `ClaimsPrincipal`v nástroji). 

Používá také konfiguraci ASP.NET Core, která má oddíl "AzureAD" a který je vázaný na:

- struktura dat typu [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet) `_applicationOptions`
- instance typu [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) definovaná v ASP.NET Core `Authentication.AzureAD.UI`. `azureAdOptions` Nakonec aplikace potřebuje udržovat mezipaměti tokenů.

```CSharp
/// <summary>
/// Creates an MSAL Confidential client application
/// </summary>
/// <param name="httpContext">HttpContext associated with the OIDC response</param>
/// <param name="claimsPrincipal">Identity for the signed-in user</param>
/// <returns></returns>
private IConfidentialClientApplication BuildConfidentialClientApplication(HttpContext httpContext, ClaimsPrincipal claimsPrincipal)
{
 var request = httpContext.Request;

 // Find the URI of the application)
 string currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _applicationOptions.CallbackPath ?? string.Empty);

 // Updates the authority from the instance (including national clouds) and the tenant
 string authority = $"{azureAdOptions.Instance}{azureAdOptions.TenantId}/";

 // Instantiates the application based on the application options (including the client secret)
 var app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
               .WithRedirectUri(currentUri)
               .WithAuthority(authority)
               .Build();

 // Initialize token cache providers. In the case of Web applications, there must be one
 // token cache per user (here the key of the token cache is in the claimsPrincipal which
 // contains the identity of the signed-in user)
 if (UserTokenCacheProvider != null)
 {
  UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (AppTokenCacheProvider != null)
 {
  AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

Podrobnosti o poskytovatelích mezipaměti tokenů najdete v tématu [ASP.NET Core výukových kurzů pro webové aplikace | Mezipaměti tokenů](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/455d32f09f4f6647b066ebee583f1a708376b12f/2-WebApp-graph-user/2-2-TokenCache)

> [!NOTE]
> `AcquireTokenByAuthorizationCode`skutečně uplatňuje autorizační kód požadovaný službou ASP.NET a získává tokeny, které se přidají do mezipaměti uživatelských tokenů MSAL.NET. Odtud jsou pak použiti v ASP.NET Core řadičích.

## <a name="aspnet-configuration"></a>Konfigurace ASP.NET

Způsob, jakým ASP.NET zpracovává věci, je podobný, s tím rozdílem, že konfigurace OpenIdConnect `OnAuthorizationCodeReceived` a předplatného `App_Start\Startup.Auth.cs` události se v souboru nachází. Najdete tu podobné koncepty, s tím rozdílem, že budete muset zadat RedirectUri v konfiguračním souboru, což je trochu méně robustní:

```CSharp
private void ConfigureAuth(IAppBuilder app)
{
 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

 app.UseCookieAuthentication(new CookieAuthenticationOptions { });

 app.UseOpenIdConnectAuthentication(
 new OpenIdConnectAuthenticationOptions
 {
  Authority = Globals.Authority,
  ClientId = Globals.ClientId,
  RedirectUri = Globals.RedirectUri,
  PostLogoutRedirectUri = Globals.RedirectUri,
  Scope = Globals.BasicSignInScopes, // a basic set of permissions for user sign in & profile access
  TokenValidationParameters = new TokenValidationParameters
  {
   NameClaimType = "name",
  },
  Notifications = new OpenIdConnectAuthenticationNotifications()
  {
   AuthorizationCodeReceived = OnAuthorizationCodeReceived,
  }
 });
}
```

```CSharp
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
{
 // Upon successful sign in, get & cache a token using MSAL
 string userId = context.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
 IConfidentialClientApplication clientapp;
 clientApp = ConfidentialClientApplicationBuilder.Create(Globals.ClientId)
                  .WithClientSecret(Globals.ClientSecret)
                  .WithRedirectUri(Globals.RedirectUri)
                  .WithAuthority(new Uri(Globals.Authority))
                  .Build();

  EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);

 AuthenticationResult result = await clientapp.AcquireTokenByAuthorizationCode(scopes, context.Code)
                                              .ExecuteAsync();
}
```

Místo toho, aby klientská aplikace tajných kódů klienta mohla také prokázat svoji identitu pomocí klientského certifikátu nebo kontrolního výrazu klienta.
Použití kontrolních výrazů klienta je pokročilý scénář, podrobně popisuje [kontrolní výrazy klienta](msal-net-client-assertions.md) .

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>Mezipaměť tokenu MSAL.NET pro webovou aplikaci ASP.NET (jádro)

V části Web Apps (nebo webová rozhraní API ve skutečnosti) se implementace mezipaměti tokenů liší od implementace mezipaměti tokenů desktopových aplikací (které jsou často [založené na souborech](scenario-desktop-acquire-token.md#file-based-token-cache)). Může používat relaci ASP.NET/ASP.NET Core, mezipaměť Redis, databázi nebo úložiště objektů BLOB v Azure. Ve fragmentu kódu výše se jedná o objekt `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` volání metody, které váže službu cache Service. Podrobnosti o tom, co se stane, je nad rámec této příručky scénáře, ale odkazy jsou uvedené níže.

> [!IMPORTANT]
> Velmi důležitou věcí k realizaci je to, že pro webové aplikace a webová rozhraní API by měla existovat jedna mezipaměť tokenů na uživatele (za každý účet). Je nutné serializovat mezipaměť tokenů pro každý účet.

Příklady použití mezipamětí tokenů pro webové aplikace a webová rozhraní API jsou k dispozici v [kurzu ASP.NET Core webové aplikace](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) v [mezipaměti tokenu fáze 2-2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). U implementací se podívejte na následující složku [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) v knihovně [Microsoft-Authentication-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) (ve složce [Microsoft. identity. Client. Extensions. Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) .

## <a name="next-steps"></a>Další postup

V tomto okamžiku, když se uživatel přihlásí k tokenu, je uložen v mezipaměti tokenů. Pojďme se podívat, jak se pak používá v jiných částech webové aplikace.

> [!div class="nextstepaction"]
> [Přihlaste se k webové aplikaci.](scenario-web-app-call-api-sign-in.md)

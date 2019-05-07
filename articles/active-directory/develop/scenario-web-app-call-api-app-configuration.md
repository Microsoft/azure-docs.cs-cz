---
title: Webové aplikace, že volání webových rozhraní API (kód konfigurace) – platforma identit Microsoft
description: Zjistěte, jak sestavit webovou aplikaci, že volání webových rozhraní API (konfigurace kódu aplikace)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95a5e1ed89b6330a0b6a49cb20d8bf0ef3587d48
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074737"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Webové aplikace, že volání webových rozhraní API – konfigurace kódu

Jak je vidět [scénář webové aplikace přihlásí uživatele](scenario-web-app-sign-user-overview.md), že přihlášení uživatele se deleguje na Open ID connect (OIDC) middleware, který chcete hook nahoru v procesu OIDC. Způsob, jak na to se liší v závislosti na rozhraní jste použili (sem technologie ASP.NET a ASP.NET Core), ale nakonec vám budete přihlášení k odběru událostí OIDC middlewaru. Princip je, že:

- Dáme vám technologie ASP.NET nebo ASP.NET core požádat o autorizační kód. Prováděním tímto ASP.NET/ASP.NET core se mohl uživatel přihlásit a vyjádření souhlasu,
- Budete se přihlásit k přijetí autorizační kód webové aplikace.
- Při přijetí kódu ověřování budete používat knihovny MSAL k uplatnění kódu a použitím výsledných tokenů přístupu a aktualizovat úložiště tokeny v mezipaměti tokenů. Odtud mezipaměti umožňuje v ostatních částech aplikace tiše získávat další tokeny.

## <a name="libraries-supporting-web-app-scenarios"></a>Knihovny podporuje scénáře pro webové aplikace

Knihovny podpůrné tok autorizačního kódu pro Web Apps jsou:

| Knihovna MSAL | Popis |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Podporované platformy jsou platformy .NET Framework a .NET Core (UPW Ne, Xamarin.iOS a Xamarin.Android jako těchto platforem se používají k vytváření veřejné klientských aplikací) |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Vývoj v průběhu – ve verzi public preview |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Vývoj v průběhu – ve verzi public preview |

## <a name="aspnet-core-configuration"></a>Konfigurace ASP.NET Core

V ASP.NET Core, stane věcí `Startup.cs` souboru. Budete chtít přihlásit k odběru `OnAuthorizationCodeReceived` otevřít ID připojení událostí a z této události, zavolejte MSAL. Metoda společnosti NET `AcquireTokenFromAuthorizationCode` které má vliv na ukládání v mezipaměti tokenů, přístupový token pro požadované obory a obnovovací token, který se použije na obnovení přístupového tokenu, když se nachází blízko vypršení platnosti nebo získat token jménem stejného uživatele , ale pro jiný prostředek.

Komentáře v kódu níže vám pomůže pochopit některé aspekty složité tkaní MSAL.NET a ASP.NET Core

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add(OidcConstants.ScopeOfflineAccess);
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

V ASP.NET Core sestavení aplikace důvěrnému klientovi používá informace, které jsou v objektu HttpContext. Tato HttpContext ví o adresu URL pro webovou aplikaci a přihlášeného uživatele (v `ClaimsPrincipal`). Využívá také konfigurace ASP.NET Core, který má oddíl "Azure AD", a který je vázán na `_applicationOptions` datové struktury. A konečně aplikace musí udržovat token mezipamětí.

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
 string currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, azureAdOptions.CallbackPath ?? string.Empty);

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
 if (this.UserTokenCacheProvider != null)
 {
  this.UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (this.AppTokenCacheProvider != null)
 {
  this.AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

`AcquireTokenByAuthorizationCode` ve skutečnosti uplatňuje autorizační kód požadavku ASP.NET a získá tokeny, které jsou přidány do MSAL.NET mezipaměť tokenu uživatele. Odtud, jsou pak použita v kontrolery ASP.NET Core.

## <a name="aspnet-configuration"></a>ASP.NET Configuration

Způsob, jak ASP.NET zpracovává věci se podobá, s tím rozdílem, že konfigurace OpenIdConnect a předplatné, které chcete `OnAuthorizationCodeReceived` události dojde v `App_Start\Startup.Auth.cs` souboru. Zjistíte, podobně jako koncepty, s tím rozdílem, že tady bude potřeba zadat v konfiguračním souboru, což je o něco menší RedirectUri robustní:

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
  // We'll inject our own issuer validation logic below.
  ValidateIssuer = false,
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

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>MSAL.NET Token mezipaměti pro aplikace technologie ASP.NET (jader)

Webové aplikace (nebo webové rozhraní API jak fakt), mezipaměť tokenu implementaci se liší od implementace mezipaměť tokenu aplikací klasické pracovní plochy (které jsou často [souborovému](scenario-desktop-acquire-token.md#file-based-token-cache). Může použít relaci ASP.NET/ASP.NET Core nebo mezipaměti redis cache, nebo databáze nebo dokonce úložištěm objektů BLOB Azure. V kódu je fragment kódu nad touto objekt `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` volání metody, která vytvoří vazbu služby cache service. Podrobnosti o co se stane, zde je mimo rámec této příručky scénář, ale odkazy jsou uvedeny níže.

> [!IMPORTANT]
> Velmi důležité si uvědomit, je, že pro webové aplikace a webová rozhraní API, měla by existovat jeden token mezipaměti na uživatele (v rámci účtu). Potřebujete k serializaci mezipamětí tokenů pro jednotlivé účty.

Příklady toho, jak použít token mezipaměti pro webové aplikace a webová rozhraní API jsou k dispozici v [kurz aplikací ASP.NET Core Web](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) ve fázi [mezipaměť tokenu 2-2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). Implementace máte najdete v následující složce [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) v [microsoft ověřování extensions pro dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) knihovny (v [ Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) složky.

## <a name="next-steps"></a>Další postup

V tomto okamžiku Jakmile se uživatel přihlásí token je uložen v tokenu mezipaměti. Podívejme se, jak se pak používají v jiných částech webové aplikace.

> [!div class="nextstepaction"]
> [Přihlaste se do webové aplikace](scenario-web-app-call-api-sign-in.md)

---
title: Migrace na MSAL.NET
titleSuffix: Microsoft identity platform
description: Přečtěte si o rozdílech mezi knihovnou ověřování Microsoftu pro .NET (MSAL.NET) a Azure AD Authentication Library pro .NET (ADAL.NET) a jak migrovat do MSAL.NET.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: cccb886e13482292e8ab9afa2b34bd9dd2c3229b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050305"
---
# <a name="migrating-applications-to-msalnet"></a>Migrace aplikací do MSAL.NET

Knihovna ověřování Microsoft u .NET (MSAL.NET) a Azure AD Authentication Library pro .NET (ADAL.NET) se používají k ověřování entit Azure AD a vyžádání tokenů z Azure AD. Až dosud většina vývojářů spolupracovala s platformou Azure AD pro vývojáře (v1.0) k ověření identit Azure AD (pracovní a školní účty) vyžádáním tokenů pomocí Azure AD Authentication Library (ADAL). Použití msal:

- můžete ověřit širší sadu identit Microsoftu (identity Azure AD a účty Microsoft a sociální a místní účty prostřednictvím Azure AD B2C), protože používá koncový bod platformy identit Microsoftu,
- vaši uživatelé získají nejlepší prostředí pro jednotné přihlašování.
- vaše aplikace může povolit přírůstkový souhlas a podpora podmíněného přístupu je jednodušší
- z inovací.

**MSAL.NET je nyní doporučená knihovna ověření pro použití s platformou identit microsoftu**. V ADAL.NET nebudou implementovány žádné nové funkce. Úsilí je zaměřeno na zlepšení MSAL.

Tento článek popisuje rozdíly mezi knihovnou ověřování Microsoftu pro .NET (MSAL.NET) a Azure AD Authentication Library pro .NET (ADAL.NET) a pomáhá vám migrovat do MSAL.  

## <a name="differences-between-adal-and-msal-apps"></a>Rozdíly mezi aplikacemi ADAL a MSAL

Ve většině případů chcete použít MSAL.NET a koncový bod platformy identit microsoftu, což je nejnovější generace ověřovacích knihoven Microsoftu. Pomocí MSAL.NET získáte tokeny pro uživatele, kteří se přihlašují k vaší aplikaci pomocí Azure AD (pracovní a školní účty), účty Microsoft (osobní) (MSA) nebo Azure AD B2C. 

Pokud jste již obeznámeni s koncovým bodem Azure AD pro vývojáře (v1.0) koncový bod (a ADAL.NET), můžete si přečíst [Co se liší o platformě identit y Microsoft (v2.0) koncový bod?](active-directory-v2-compare.md).

Pokud však aplikace potřebuje přihlásit uživatele staršími verzemi [služby ADFS (ADFS) služby ADAL.NET.](/windows-server/identity/active-directory-federation-services) Další informace naleznete v tématu [Podpora služby ADFS](https://aka.ms/msal-net-adfs-support).

Následující obrázek shrnuje některé rozdíly mezi ![ADAL.NET a MSAL.NET kódu vedle sebe](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>Balíčky NuGet a obory názvů

ADAL.NET je spotřebováván z balíčku [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet. obor názvů, který `Microsoft.IdentityModel.Clients.ActiveDirectory`chcete použít, je .

Chcete-li použít MSAL.NET, budete muset přidat balíček [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet a použít obor `Microsoft.Identity.Client` názvů

### <a name="scopes-not-resources"></a>Obory nejsou prostředky

ADAL.NET získá tokeny pro *prostředky*, ale MSAL.NET získá tokeny pro *obory*. Počet MSAL.NET přepsání AcquireToken vyžaduje parametr`IEnumerable<string> scopes`nazvaný obory( ). Tento parametr je jednoduchý seznam řetězců, které deklarují požadovaná oprávnění a prostředky, které jsou požadovány. Dobře známé obory jsou [obory aplikace Microsoft Graph](/graph/permissions-reference).

Je také možné v MSAL.NET přístup k prostředkům v1.0. Viz podrobnosti v [obory pro v1.0 aplikace](#scopes-for-a-web-api-accepting-v10-tokens). 

### <a name="core-classes"></a>Základní třídy

- ADAL.NET používá [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) jako reprezentaci připojení ke službě TOKEN (Security Token Service) nebo autorizačnímu serveru prostřednictvím autority. Naopak, MSAL.NET je navržen kolem [klientských aplikací](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications). Poskytuje dvě samostatné `PublicClientApplication` třídy: a`ConfidentialClientApplication`

- Získání tokenů: ADAL.NET a MSAL.NET mají`AcquireTokenAsync` stejná volání ověřování ( a `AcquireTokenSilentAsync` pro ADAL.NET a `AcquireTokenInteractive` `AcquireTokenSilent` v MSAL.NET), ale s různými požadovanými parametry. Jeden rozdíl je skutečnost, že v MSAL.NET již nemusíte `ClientID` předávat v vaší aplikace v každém volání AcquireTokenXX. Ve skutečnosti, `ClientID` je nastavenpouze jednou`IPublicClientApplication` `IConfidentialClientApplication`při budování ( nebo ).

### <a name="iaccount-not-iuser"></a>IAccount není IUser

ADAL.NET zmanipuloval uživatele. Uživatel je však člověk nebo softwarový agent, ale může vlastnit/vlastnit/být zodpovědný za jeden nebo více účtů v systému identit Microsoftu (několik účtů Azure AD, Azure AD B2C, osobní účty Microsoft). 

MSAL.NET 2.x nyní definuje koncept účtu (prostřednictvím rozhraní IAccount). Tato změna rozdělení poskytuje správnou sémantiku: skutečnost, že stejný uživatel může mít několik účtů v různých adresářích Azure AD. Také MSAL.NET poskytuje lepší informace ve scénářích hosta, protože jsou k dispozici informace o domácím účtu.

Další informace o rozdílech mezi iUser a IAccount naleznete [v tématu MSAL.NET 2.x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Výjimky

#### <a name="interaction-required-exceptions"></a>Interakce požadované výjimky

MSAL.NET má více explicitní výjimky. Například při tiché ověřování selže v ADAL postup je zachytit `user_interaction_required` výjimku a vyhledejte kód chyby:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == "user_interaction_required")
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

Viz podrobnosti v [doporučený vzor získat token](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) s ADAL.NET

Pomocí MSAL.NET zachytíte, `MsalUiRequiredException` jak je popsáno v [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Zpracování výjimek z nároku na nárok

V ADAL.NET jsou výjimky nároku na výzvu zpracovány následujícím způsobem:

- `AdalClaimChallengeException`je výjimka (odvozená `AdalServiceException`z) vyzdvižená službou v případě, že prostředek vyžaduje od uživatele více deklarací (například dvoufaktorové ověřování). Člen `Claims` obsahuje některé Fragment JSON s deklaracemi, které jsou očekávány.
- Stále v ADAL.NET, veřejná klientská aplikace `AcquireTokenInteractive` přijímající tuto výjimku musí volat přepsání s parametrem deklarace identity. Toto přepsání `AcquireTokenInteractive` se ani nepokouší o přístup do mezipaměti, protože to není nutné. Důvodem je, že token v mezipaměti nemá správné `AdalClaimChallengeException` deklarace identity (jinak by nebyla vyvolána). Proto není třeba se podívat na mezipaměti. Všimněte `ClaimChallengeException` si, že může být přijata v `AcquireTokenInteractive` Rozhraní OBO WebAPI, vzhledem k tomu, že je třeba volat v aplikaci veřejného klienta volání tohoto webového rozhraní API.
- podrobnosti, včetně ukázek, viz Zpracování [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)

V MSAL.NET jsou výjimky nároku na výzvu zpracovány následujícím způsobem:

- Jsou `Claims` se vynořují v . `MsalServiceException`
- Existuje `.WithClaim(claims)` metoda, která se `AcquireTokenInteractive` může vztahovat na tvůrce. 

### <a name="supported-grants"></a>Podporované granty

Ne všechny granty jsou ještě podporovány v MSAL.NET a v2.0 koncový bod. Následuje souhrn porovnání ADAL.NET a MSAL. net podporované granty.

#### <a name="public-client-applications"></a>Veřejné klientské aplikace

Zde jsou granty podporované v ADAL.NET a MSAL.NET pro desktopové a mobilní aplikace

Oprávnění | ADAL.NET | MSAL.NET
----- |----- | -----
Interaktivní | [Interaktivní auth](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Interaktivní získávání tokenů v MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Integrované ověřování systému Windows | [Integrované ověřování v systému Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Integrované ověřování systému Windows](msal-authentication-flows.md#integrated-windows-authentication)
Uživatelské jméno / heslo | [Získávání tokenů s uživatelským jménem a heslem](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Ověřování pomocí uživatelského jména](msal-authentication-flows.md#usernamepassword)
Tok kódu zařízení | [Profil zařízení pro zařízení bez webových prohlížečů](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Tok kódu zařízení](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Důvěrné klientské aplikace

Zde jsou granty podporované v ADAL.NET a MSAL.NET pro webové aplikace, webová api a aplikace daemon:

Typ aplikace | Oprávnění | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
WebApp, Webové rozhraní API, daemon | Pověření klienta | [Toky pověření klienta v ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Toky pověření klienta v MSAL.NET](msal-authentication-flows.md#client-credentials))
Web API | Jménem | [Služba servisních hovorů jménem uživatele s ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [Jménem společnosti v MSAL.NET](msal-authentication-flows.md#on-behalf-of)
Webová aplikace | Auth kód | [Získávání tokenů s autorizačními kódy ve webových aplikacích s ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Získávání tokenů s autorizačními kódy ve webových aplikacích s a MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Trvalost mezipaměti

ADAL.NET umožňuje rozšířit `TokenCache` třídu implementovat požadovanou funkci trvalosti na platformách bez zabezpečeného úložiště (.NET Framework a .NET core) pomocí `BeforeAccess`metody , a `BeforeWrite` metody. Podrobnosti naleznete v tématu [Serializace mezipaměti tokenů v ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

MSAL.NET vytvoří uvěšenou třídu tokenů zapečetěnou třídou a odebere možnost ji prodloužit. Proto vaše implementace trvalosti mezipaměti tokenů musí být ve formě pomocné třídy, která interaguje s uzavřenou mezipaměti tokenů. Tato interakce je popsána v [serializaci mezipaměti tokenů v MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>Označení společného orgánu

V části v1.0, `https://login.microsoftonline.com/common` pokud použijete oprávnění, umožníte uživatelům přihlásit se pomocí libovolného účtu AAD (pro jakoukoli organizaci). Viz [Ověření úřadu v ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Pokud používáte `https://login.microsoftonline.com/common` oprávnění v v2.0, umožníte uživatelům přihlásit se pomocí libovolné organizace AAD nebo osobního účtu Microsoft (MSA). V MSAL.NET, pokud chcete omezit přihlášení k libovolnému účtu AAD (stejné `https://login.microsoftonline.com/organizations`chování jako u ADAL.NET), musíte použít . Podrobnosti naleznete `authority` v parametru v [aplikaci veřejného klienta](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>tokeny v1.0 a v2.0

Existují dvě verze tokenů:
- tokeny v1.0
- tokeny v2.0 

Koncový bod v1.0 (používaný ADAL) vydává pouze tokeny v1.0.

Koncový bod v2.0 (používaný službou MSAL) však vydává verzi tokenu, který přijímá webové rozhraní API. Vlastnost manifestu aplikace webového rozhraní API umožňuje vývojářům zvolit, která verze tokenu je přijata. Viz `accessTokenAcceptedVersion` referenční dokumentace [manifestu aplikace.](reference-app-manifest.md)

Další informace o tokenech v1.0 a v2.0 najdete v [tématu Tokeny přístupu služby Azure Active Directory](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Obory pro webové rozhraní API přijímající tokeny v1.0

Oprávnění OAuth2 jsou obory oprávnění, které aplikace webového rozhraní API (prostředek) v1.0 zpřístupňuje klientským aplikacím. Tyto obory oprávnění mohou být uděleny klientským aplikacím během souhlasu. Podívejte se na část o oauth2Permissions v [manifestu aplikace Azure Active Directory](active-directory-application-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Obory pro vyžádání přístupu ke konkrétním oprávněním OAuth2 aplikace v1.0

Pokud chcete získat tokeny pro aplikaci přijímající tokeny v1.0 (například https://graph.microsoft.com)rozhraní Microsoft Graph `scopes` API, což je , budete muset vytvořit zřetězením požadovaný identifikátor prostředku s požadovaným oprávněním OAuth2 pro tento prostředek.

Chcete-li například získat přístup v názvu uživatele a webového rozhraní `ResourceId`API v1.0, které je identifikátor URI ID aplikace , měli byste použít:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

Pokud chcete číst a psát pomocí MSAL.NET Azure Activehttps://graph.microsoft.com/)Directory pomocí rozhraní Microsoft Graph API ( , vytvoříte seznam oborů, jako je v následujícím fragmentu:

```csharp
ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Upozornění: Pokud máte jedno nebo dvě lomítka v oboru odpovídající v1.0 Web API

Pokud chcete napsat obor odpovídající rozhraní API Správcehttps://management.core.windows.net/)prostředků Azure ( , musíte požádat o následující obor (všimněte si dvou lomítka) 

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Důvodem je, že rozhraní API Správce prostředků`aud`očekává lomítko v jeho deklarace cílové skupiny ( ) a pak je lomítko oddělit název rozhraní API od oboru.

Logika používaná službou Azure AD je následující:
- Pro koncový bod ADAL (v1.0) s přístupovým tokenem v1.0 (jediný možný), aud=resource
- Pro MSAL (v2.0 koncový bod) žádají přístupový token pro prostředek přijetí v2.0 tokeny, aud = prostředek. Appid
- Pro MSAL (v2.0 koncový bod) žádají přístupový token pro prostředek přijetí v1.0 přístupový token (což je případ výše), Azure AD analyzuje požadovanou cílovou skupinu z požadovaného oboru tím, že vše před poslední lomítko a jeho použití jako identifikátor prostředku. Proto pokud\/https: /database.windows.net očekává,https://database.windows.net/že publikum " ", budete muset\/požádat o rozsah https: /database.windows.net//.default. Viz také problém #[747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Prostředek url koncové lomítko je vynechán, což způsobilo selhání sql auth #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Obory pro vyžádání přístupu ke všem oprávněním aplikace v1.0

Například pokud chcete získat token pro všechny statické obory aplikace v1.0, je třeba použít

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Obory, které chcete požádat v případě aplikace toku pověření klienta / daemonu

V případě toku pověření klienta by byl `/.default`také obor, který by měl být . Tento obor říká Azure AD: "všechna oprávnění na úrovni aplikace, které správce souhlasil v registraci aplikace.

## <a name="adal-to-msal-migration"></a>ADAL na MSAL migrace

V ADAL.NET v2. X, byly vystaveny obnovovací tokeny, které umožňují vyvíjet řešení kolem použití těchto `AcquireTokenByRefreshToken` tokenů jejich ukládánído mezipaměti a pomocí metod poskytovaných ADAL 2.x. Některá z těchto řešení byla použita ve scénářích, jako jsou:
* Dlouho běžící služby, které provádějí akce, včetně aktualizace řídicích panelů jménem uživatelů, zatímco uživatelé již nejsou připojeni. 
* WebFarm scénáře pro povolení klienta přenést RT do webové služby (ukládání do mezipaměti se provádí na straně klienta, šifrované cookie a ne na straně serveru)

MSAL.NET nezveřejňuje aktualizační tokeny, z bezpečnostních důvodů: MSAL zpracovává osvěžující tokeny za vás. 

Naštěstí MSAL.NET nyní má rozhraní API, které umožňuje migrovat předchozí obnovovací `IConfidentialClientApplication`tokeny (získané pomocí ADAL) do :

```csharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into 
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration 
/// scenarios where you have a RefreshToken available. 
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint. 
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```
 
Pomocí této metody můžete poskytnout dříve použitý obnovovací token spolu se všemi obory (prostředky), které si přejete. Obnovovací token bude vyměněn za nový a uložen do mezipaměti do aplikace.  

Vzhledem k tomu, že tato metoda je určena pro scénáře, které nejsou typické, není snadno dostupná s `IConfidentialClientApplication` bez prvního obsazení . `IByRefreshToken`

Tento fragment kódu zobrazuje nějaký kód migrace v důvěrné klientské aplikaci. `GetCachedRefreshTokenForSignedInUser`načíst obnovovací token, který byl uložen v některém úložišti předchozí verzí aplikace, která slouží k využití ADAL 2.x. `GetTokenCacheForSignedInUser`deserializuje mezipaměť pro přihlašovaného uživatele (protože důvěrné klientské aplikace by měly mít jednu mezipaměť na uživatele).

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;
         
AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

Zobrazí se přístupový token a id token vrácený ve vašem AuthenticationResult, zatímco váš nový obnovovací token je uložen v mezipaměti.

Tuto metodu můžete také použít pro různé scénáře integrace, kde máte k dispozici obnovovací token.

## <a name="next-steps"></a>Další kroky

Další informace o oborech naleznete v [oborech, oprávněních a souhlasu v koncovém bodě platformy identit microsoftu.](v2-permissions-and-consent.md)

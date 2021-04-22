---
title: Migrace na MSAL.NET
titleSuffix: Microsoft identity platform
description: Přečtěte si o rozdílech mezi Microsoft Authentication Library for .NET (MSAL.NET) a s knihovnou ověřování Azure AD pro .NET (ADAL.NET) a postupem migrace na MSAL.NET.
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
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 0e7dc3540dc54e0563a5ea416510bddb9a41fb65
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861692"
---
# <a name="migrating-applications-to-msalnet"></a>Migrace aplikací do MSAL.NET

K ověřování entit Azure AD a žádosti o tokeny od Azure AD se používají knihovna Microsoft Authentication Library pro .NET (MSAL.NET) i knihovna ověřování Azure AD pro .NET (ADAL.NET). Až do té doby se většina vývojářů pracovala s Azure AD for Developers Platform (verze 1.0) k ověřování identit Azure AD (pracovní a školní účty) tím, že vyžaduje tokeny pomocí služby Azure AD Authentication Library (ADAL). Pomocí MSAL:

- můžete ověřit širší sadu identit Microsoftu (identity Azure AD a účty Microsoft a sociální a místní účty prostřednictvím Azure AD B2C), protože používá platformu Microsoft identity.
- vaši uživatelé získají nejlepší možnosti jednotného přihlašování.
- vaše aplikace může povolit postupný souhlas a podpora podmíněného přístupu je jednodušší.
- přináší vám výhody inovace.

**MSAL.NET nebo Microsoft. identity. Web jsou teď Doporučené knihovny pro ověřování, které se mají používat s platformou Microsoft Identity**. V ADAL.NET se neimplementují žádné nové funkce. Úsilí se zaměřuje na zlepšení MSAL.

Tento článek popisuje rozdíly mezi knihovnou Microsoft Authentication Library for .NET (MSAL.NET) a knihovnou ověřování Azure AD pro .NET (ADAL.NET) a pomáhá migrovat na MSAL.

## <a name="should-you-migrate-to-msalnet-or-to-microsoftidentityweb"></a>Měli byste migrovat na MSAL.NET nebo na Microsoft. identity. Web.

Před prozkoumá v podrobnostech MSAL.NET vs ADAL.NET můžete chtít zjistit, jestli chcete použít MSAL.NET nebo abstrakci vyšší úrovně, jako je [Microsoft. identity. Web.](microsoft-identity-web.md)

Podrobnosti o rozhodovacím stromu níže najdete v článku [Jak používat jenom MSAL.NET? nebo abstrakce vyšší úrovně?](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Is-MSAL.NET-right-for-me%3F)

:::image type="content" source="media/msal-net-migration/decision-diagram.png" alt-text="Blokový diagram vysvětlující, jak zvolit, jestli při migraci z ADAL.NET potřebujete použít MSAL.NET a Microsoft. identity. Web nebo obojí":::

## <a name="differences-between-adal-and-msal-apps"></a>Rozdíly mezi aplikacemi ADAL a MSAL

Ve většině případů chcete používat MSAL.NET a Microsoft Identity Platform, což je nejnovější generace knihoven ověřování od Microsoftu. Pomocí MSAL.NET získáte tokeny pro uživatele, kteří se přihlásí do vaší aplikace pomocí Azure AD (pracovní a školní účty), účtů Microsoft (osobní) (MSA) nebo Azure AD B2C.

Pokud už jste obeznámeni s koncovým bodem Azure AD for Developers (v 1.0) (a ADAL.NET), možná si budete chtít přečíst, [co se liší od platformy Microsoft Identity?](../azuread-dev/azure-ad-endpoint-comparison.md).

Pokud ale vaše aplikace potřebuje přihlašovat uživatele staršími verzemi [Active Directory Federation Services (AD FS) (ADFS)](/windows-server/identity/active-directory-federation-services), musíte i nadále používat ADAL.NET. Další informace najdete v tématu [Podpora služby ADFS](https://aka.ms/msal-net-adfs-support).

Následující obrázek shrnuje některé rozdíly mezi ADAL.NET a MSAL.NET pro souběžný kód aplikace veřejné klientské aplikace. ![](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>Balíčky a obory názvů NuGet

ADAL.NET se spotřebovává z balíčku NuGet [Microsoft. IdentityModel. clients. Active](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) . obor názvů, který se má použít `Microsoft.IdentityModel.Clients.ActiveDirectory` , je.

Pokud chcete použít MSAL.NET, budete muset přidat balíček NuGet [Microsoft. identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) a použít `Microsoft.Identity.Client` obor názvů.

### <a name="scopes-not-resources"></a>Obory nejsou prostředky

ADAL.NET získá tokeny pro *prostředky*, ale MSAL.NET získá tokeny pro *rozsahy*. Řada MSAL.NETch přepsání AcquireToken vyžaduje parametr nazvaný scopes ( `IEnumerable<string> scopes` ). Tento parametr je jednoduchý seznam řetězců, který deklaruje požadovaná oprávnění a požadované prostředky. Známé obory jsou [obory Microsoft Graph](/graph/permissions-reference).

V MSAL.NET je také možné získat přístup k prostředkům v 1.0. Podívejte se na podrobnosti v [oborech pro aplikaci v 1.0](#scopes-for-a-web-api-accepting-v10-tokens).

### <a name="core-classes"></a>Základní třídy

- ADAL.NET používá [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) jako znázornění připojení ke službě tokenů zabezpečení (STS) nebo k autorizačnímu serveru prostřednictvím autority. V opačném případě je MSAL.NET navržený kolem [klientských aplikací](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications). Poskytuje dvě samostatné třídy: `PublicClientApplication` a `ConfidentialClientApplication`

- Získání tokenů: ADAL.NET a MSAL.NET mají stejná ověřovací volání ( `AcquireTokenAsync` a `AcquireTokenSilentAsync` pro ADAL.NET a `AcquireTokenInteractive` a `AcquireTokenSilent` v MSAL.NET), ale vyžadují různé parametry. Jedním rozdílem je skutečnost, že v MSAL.NET už nemusíte předávat do `ClientID` aplikace v každém volání AcquireTokenXX. Ve skutečnosti `ClientID` je při sestavování (nebo) nastaveno pouze jednou `IPublicClientApplication` `IConfidentialClientApplication` .

### <a name="iaccount-not-iuser"></a>IAccount není IUser

ADAL.NET manipulovat s uživateli. Uživatel je však člověk nebo softwarový agent, ale může mít/a být zodpovědný za jeden nebo více účtů v systému Microsoft Identity System (několik účtů Azure AD, Azure AD B2C, osobní účty Microsoft).

MSAL.NET 2. x teď definuje koncept účtu (prostřednictvím rozhraní IAccount). Tato zásadní změna poskytuje správnou sémantiku: skutečnost, že stejný uživatel může mít několik účtů v různých adresářích Azure AD. MSAL.NET také poskytuje lepší informace v rámci scénářů hostů, protože jsou k dispozici informace o domácím účtu.

Další informace o rozdílech mezi IUser a IAccount najdete v tématu [MSAL.NET 2. x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Výjimky

#### <a name="interaction-required-exceptions"></a>Interakce – vyžadované výjimky

MSAL.NET má více explicitních výjimek. Pokud například v knihovně ADAL dojde k chybě tichého ověřování, procedura je zachytit výjimku a vyhledat `user_interaction_required` kód chyby:

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

Získání tokenu pomocí ADAL.NET najdete v podrobnostech o [doporučeném vzoru](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) .

Pomocí MSAL.NET můžete zachytit, `MsalUiRequiredException` jak je popsáno v [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Zpracování výjimek výzvy deklarací identity

V ADAL.NET jsou výjimky výzvy deklarace identity zpracovávány následujícím způsobem:

- `AdalClaimChallengeException` je výjimka (odvozená od), kterou `AdalServiceException` Služba vyvolala v případě, že prostředek vyžaduje více deklarací identity od uživatele (např. pro ověřování instance se dvěma faktory). `Claims`Člen obsahuje některý fragment JSON s deklaracemi, které jsou očekávané.
- V ADAL.NET je nutné, aby aplikace veřejné klienta obdržela tuto výjimku, aby vyvolala `AcquireTokenInteractive` přepsání s parametrem deklarací identity. Toto přepsání se `AcquireTokenInteractive` ani nepokusí použít mezipaměť, protože není nutné. Důvodem je, že token v mezipaměti nemá správné deklarace identity (jinak `AdalClaimChallengeException` by nebyl vyvolán). Proto není nutné pohlížet na mezipaměť. Všimněte si, že je `ClaimChallengeException` možné přijímat v WEBAPI OBO, zatímco `AcquireTokenInteractive` musí být volány ve veřejné klientské aplikaci, která volá toto webové rozhraní API.
- Podrobnosti, včetně ukázek, najdete v tématu zpracování [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception) .

V MSAL.NET jsou výjimky výzvy deklarace identity zpracovávány následujícím způsobem:

- `Claims`Jsou umístěny na povrchu `MsalServiceException` .
- Existuje `.WithClaim(claims)` metoda, která může být použita na `AcquireTokenInteractive` Tvůrce.

### <a name="supported-grants"></a>Podporované granty

V MSAL.NET a koncovém bodu v 2.0 se zatím nepodporují všechny granty. Následuje souhrn porovnání ADAL.NET a MSAL. Podporované podpory netto.

#### <a name="public-client-applications"></a>Veřejné klientské aplikace

Tady jsou granty podporované v ADAL.NET a MSAL.NET pro desktopové a mobilní aplikace.

Oprávnění | ADAL.NET | MSAL.NET
----- |----- | -----
Interaktivní | [Interaktivní ověřování](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Interaktivní získávání tokenů v MSAL.NET](scenario-desktop-acquire-token.md?tabs=dotnet#acquire-a-token-interactively)
Integrované ověřování systému Windows | [Integrované ověřování ve Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Integrované ověřování systému Windows](scenario-desktop-acquire-token.md?tabs=dotnet#integrated-windows-authentication)
Uživatelské jméno a heslo | [Získávají se tokeny s uživatelským jménem a heslem.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Ověřování hesla uživatele](scenario-desktop-acquire-token.md?tabs=dotnet#username-and-password)
Tok kódu zařízení | [Profil zařízení pro zařízení bez webových prohlížečů](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Tok kódu zařízení](scenario-desktop-acquire-token.md?tabs=dotnet#command-line-tool-without-a-web-browser)

#### <a name="confidential-client-applications"></a>Důvěrné klientské aplikace

Tady jsou granty podporované v ADAL.NET, MSAL.NET a Microsoft. identity. Web pro webové aplikace, webová rozhraní API a aplikace démona:

Typ aplikace | Oprávnění | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Webová aplikace, webové rozhraní API, démon | Pověření klienta | [Toky přihlašovacích údajů klienta v ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Toky přihlašovacích údajů klienta v MSAL.NET](scenario-daemon-acquire-token.md?tabs=dotnet#acquiretokenforclient-api)
Webové rozhraní API | Jménem | [Volání služeb jménem uživatele pomocí ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [Jménem v MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/on-behalf-of)
Webová aplikace | Ověřovací kód | [Získání tokenů pomocí autorizačních kódů ve webových aplikacích pomocí ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Získání tokenů pomocí autorizačních kódů u webových aplikací s MSAL.NET](scenario-web-app-call-api-acquire-token.md?tabs=aspnetcore)

### <a name="cache-persistence"></a>Trvalost mezipaměti

ADAL.NET umožňuje zvětšit `TokenCache` třídu pro implementaci požadovaných funkcí trvalosti na platformách bez zabezpečeného úložiště (.NET Framework a .NET Core) pomocí `BeforeAccess` `BeforeWrite` metod a. Podrobnosti najdete v tématu [serializace mezipaměti tokenů v ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

MSAL.NET zpřístupňuje zapečetěnou třídu cache tokenem a odebírá možnost jejich rozšiřování. Proto vaše implementace mezipaměti tokenů musí být ve formě pomocné třídy, která komunikuje s zapečetěnou mezipamětí tokenu. Tato interakce je popsána v tématu [serializace mezipaměti tokenů v MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>Signification společné autority

Pokud v v 1.0 používáte `https://login.microsoftonline.com/common` autoritu, umožníte uživatelům přihlašovat se pomocí libovolného účtu AAD (pro libovolnou organizaci). Viz [ověření autority v ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Pokud použijete `https://login.microsoftonline.com/common` autoritu v 2.0, umožníte uživatelům, aby se přihlásili pomocí libovolné organizace AAD nebo osobního účtu Microsoft (MSA). Pokud v MSAL.NET chcete omezit přihlášení na libovolný účet AAD (stejné chování jako u ADAL.NET), použijte `https://login.microsoftonline.com/organizations` . Podrobnosti najdete v `authority` parametru ve [veřejné klientské aplikaci](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>tokeny v 1.0 a v 2.0

Existují dvě verze tokenů:
- tokeny v 1.0
- tokeny v 2.0

Koncový bod v 1.0 (používaný v ADAL) vysílá jenom tokeny v 1.0.

Koncový bod v 2.0 (používaný v MSAL) ale emituje verzi tokenu, který webové rozhraní API přijímá. Vlastnost manifestu aplikace webového rozhraní API umožňuje vývojářům zvolit, která verze tokenu je přijata. Viz `accessTokenAcceptedVersion` v tématu Referenční dokumentace k [manifestu aplikace](reference-app-manifest.md) .

Další informace o tokenech v 1.0 a v 2.0 najdete v tématu [Azure Active Directory Access tokens](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Obory pro tokeny webového rozhraní API přijímající verze 1.0

Oprávnění OAuth2 jsou obory oprávnění, které aplikace webového rozhraní API (prostředků) v 1.0 zveřejňuje klientským aplikacím. Tyto obory oprávnění se můžou klientským aplikacím udělit během souhlasu. Podívejte se na část o oauth2Permissions v [manifestu aplikace Azure Active Directory](./reference-app-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Obory pro vyžádání přístupu ke konkrétním OAuth2 oprávnění aplikace v 1.0

Pokud chcete získat tokeny pro aplikaci přijímající tokeny v 1.0 (například rozhraní Microsoft Graph API, které je https://graph.microsoft.com) , je nutné vytvořit `scopes` zřetězením požadovaného identifikátoru prostředku s požadovaným oprávněním OAuth2 pro daný prostředek.

Chcete-li například získat přístup k názvu uživatelského rozhraní API pro uživatele a v 1.0, které identifikátor ID aplikace je `ResourceId` , chcete použít:

```csharp
var scopes = new [] { ResourceId+"/user_impersonation" };
```

Pokud chcete číst a zapisovat pomocí MSAL.NET Azure Active Directory pomocí rozhraní Microsoft Graph API ( https://graph.microsoft.com/) , vytvořili byste seznam oborů, jako v následujícím fragmentu kódu:

```csharp
string ResourceId = "https://graph.microsoft.com/"; 
string[] scopes = { ResourceId + "Directory.Read", ResourceId + "Directory.Write" }
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Upozornění: v oboru, který odpovídá webovému rozhraní API v 1.0, by mělo být jedno nebo dvě lomítka

Pokud chcete zapsat obor odpovídající rozhraní Azure Resource Manager API ( https://management.core.windows.net/) , poznamenejte si následující rozsah (Všimněte si dvou lomítek).

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Důvodem je to, že rozhraní Správce prostředků API očekává lomítko v deklaraci identity cílové skupiny ( `aud` ), a pak je lomítko k oddělení názvu rozhraní API z oboru.

Logika, kterou používá služba Azure AD, je následující:
- Koncový bod pro ADAL (v 1.0) s přístupovým tokenem v 1.0 (jediný možný), AUD = Resource
- Pro MSAL (koncový bod verze 2.0) se žádostí o přístupový token pro prostředek, který přijímá tokeny v 2.0, AUD = Resource. Identifikátor
- Pro MSAL (koncový bod verze 2.0), který žádá o přístupový token pro prostředek, který přijímá přístupový token v 1.0 (což je výše uvedený případ), Azure AD analyzuje požadovanou cílovou skupinu z požadovaného oboru tím, že převezme vše před poslední lomítko a použije ho jako identifikátor prostředku. Proto pokud https: \/ /Database.Windows.NET očekává cílovou skupinu " https://database.windows.net/ ", budete muset vyžádat rozsah protokolu https: \/ /Database.Windows.NET//.default. Viz také problém č.[747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): koncové lomítko adresy URL prostředku je vynecháno, což způsobilo selhání ověřování SQL #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Obory pro vyžadování přístupu ke všem oprávněním aplikace v 1.0

Například pokud chcete získat token pro všechny statické obory aplikace v 1.0, je třeba použít

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] { ResourceId+"/.default" };
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Rozsahy, které se mají požádat v případě aplikace Flow/démon přihlašovacích údajů klienta

V případě toku přihlašovacích údajů klienta by měl být taky obor, který se má předat `/.default` . Tento rozsah oznamuje službě Azure AD: "všechna oprávnění na úrovni aplikace, ke kterým správce souhlasí v registraci aplikace.

## <a name="adal-to-msal-migration"></a>MSAL migrace ADAL

V ADAL.NET v2. X, aktualizační tokeny byly vystaveny, což vám umožní vyvíjet řešení kolem používání těchto tokenů jejich ukládáním do mezipaměti a pomocí `AcquireTokenByRefreshToken` metod poskytovaných ADAL 2. x.
Některá z těchto řešení se použila ve scénářích, jako jsou:
* Dlouhodobě běžící služby, které provádí akce, včetně aktualizace řídicích panelů jménem uživatelů, zatímco uživatelé už nebudou připojení.
* Scénáře webfarmu, které umožňují klientovi přenést RT do webové služby (ukládání do mezipaměti probíhá na straně klienta, zašifrovaný soubor cookie a není na straně serveru)

MSAL.NET nevystavuje aktualizace tokenů z důvodů zabezpečení: MSAL zpracovává obnovování tokenů za vás.

Naštěstí MSAL.NET nyní obsahuje rozhraní API, které vám umožní migrovat předchozí obnovovací tokeny (získané pomocí ADAL) do `IConfidentialClientApplication` :

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

Pomocí této metody můžete zadat dříve použitý obnovovací token spolu s libovolnými obory (prostředky), které si přejete. Obnovovací token se vymění pro nový a uložený do mezipaměti do vaší aplikace.

Jelikož je tato metoda určena pro scénáře, které nejsou typické, není k dispozici, není k dispozici, `IConfidentialClientApplication` a to bez jejich prvního přetypování do `IByRefreshToken` .

Tento fragment kódu ukazuje určitý kód migrace v důvěrné klientské aplikaci. `GetCachedRefreshTokenForSignedInUser` Načtěte obnovovací token, který byl uložen v některém úložišti, pomocí předchozí verze aplikace, která použila k využití knihovny ADAL 2. x. `GetTokenCacheForSignedInUser` deserializace mezipaměť přihlášeného uživatele (protože důvěrné klientské aplikace by měly mít jednu mezipaměť na uživatele).

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

V AuthenticationResult se zobrazí přístupový token a token ID, zatímco se nový obnovovací token uloží do mezipaměti.

Tuto metodu můžete použít také pro různé scénáře integrace, kde máte k dispozici obnovovací token.

## <a name="next-steps"></a>Další kroky

Další informace o oborech v [oborech, oprávněních a jejich souhlasu najdete na platformě Microsoft Identity Platform](v2-permissions-and-consent.md) .

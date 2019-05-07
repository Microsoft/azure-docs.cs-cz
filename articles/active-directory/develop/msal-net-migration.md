---
title: Migrace na MSAL.NET | Azure
description: Přečtěte si o rozdílech mezi knihovna Microsoft Authentication Library pro .NET (MSAL.NET) a Azure AD Authentication Library pro .NET (ADAL.NET) a jak migrovat do MSAL.NET.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2576121bfc945b90ce8ec0260ea30ec110e14dd8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138832"
---
# <a name="migrating-applications-to-msalnet"></a>Migrace aplikací do MSAL.NET

Knihovna Microsoft Authentication Library pro .NET (MSAL.NET) i Azure AD Authentication Library pro .NET (ADAL.NET) se používají k ověření služby Azure AD entity a požádat o tokeny ze služby Azure AD. Až dosud Většina vývojářů pracovali s Azure AD pro vývojáře platformu (verze 1.0) k ověření identit Azure AD (pracovní a školní účty) pomocí žádosti o tokeny pomocí Azure AD Authentication Library (ADAL). Teď pomocí MSAL.NET, můžete ověřovat pestřejší škálu identit společnosti Microsoft (identit Azure AD a účty Microsoft a účty sociálních sítí a místní prostřednictvím Azure AD B2C) prostřednictvím koncového bodu Microsoft identity platform. 

Tento článek popisuje, jak si vybrat mezi knihovna Microsoft Authentication Library pro .NET (MSAL.NET) a Azure AD Authentication Library pro .NET (ADAL.NET) a porovná dvě knihovny.  

## <a name="differences-between-adal-and-msal-apps"></a>Rozdíly mezi aplikacemi pro knihovnu ADAL a MSAL
Ve většině případů budete chtít pomocí MSAL.NET a Microsoft identity platform koncový bod, který je nejnovější generace knihovny Microsoft authentication Library. Pomocí MSAL.NET získat tokeny uživatele přihlášení do vaší aplikace s Azure AD (pracovní a školní účty), účty Microsoft (osobní) (MSA) nebo Azure AD B2C. 

Pokud již máte zkušenosti s Azure AD pro vývojáře (verze 1.0) koncového bodu (a ADAL.NET), můžete chtít číst [Čím se liší koncového bodu Microsoft identity platform (v2.0)?](active-directory-v2-compare.md).

Však budete stále muset použít ADAL.NET Pokud vaše aplikace potřebuje k přihlášení uživatelů s předchozími verzemi [Active Directory Federation Services (ADFS)](/windows-server/identity/active-directory-federation-services). Další podrobnosti najdete v tématu [podpory služby AD FS](https://aka.ms/msal-net-adfs-support).

Následující obrázek shrnuje některé rozdíly mezi ADAL.NET a MSAL.NET ![kód vedle sebe](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>Balíčky NuGet a obory názvů

ADAL.NET spotřebované [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) balíček NuGet. obor názvů pro použití se `Microsoft.IdentityModel.Clients.ActiveDirectory`.

Použití MSAL.NET budete muset přidat [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet balení a použít `Microsoft.Identity.Client` obor názvů

### <a name="scopes-not-resources"></a>Obory nejsou prostředky

Získá tokeny pro ADAL.NET *prostředky*, ale MSAL.NET získá tokeny pro *obory*. Počet MSAL.NET AcquireToken přepsání vyžaduje parametr s názvem obory (`IEnumerable<string> scopes`). Tento parametr je jednoduchý seznam řetězců, které deklarují požadovaná oprávnění a prostředky, které jsou požadovány. Jsou dobře známé obory [obory Microsoft Graphu](/graph/permissions-reference).

Je také možné u MSAL.NET pro přístup k prostředkům v1.0. Podrobnosti najdete v [oborů pro aplikaci v1.0](#scopes-for-a-web-api-accepting-v10-tokens). 

### <a name="core-classes"></a>Základní třídy

- Používá ADAL.NET [kontextu AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) jako reprezentace připojení na službu tokenů zabezpečení (STS) nebo autorizační server prostřednictvím autoritu. Naopak MSAL.NET navržené s ohledem na [klientské aplikace](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications). Poskytuje dvě samostatné třídy: `PublicClientApplication` a `ConfidentialClientApplication`

- Získávání tokenů: ADAL.NET a MSAL.NET mají stejné volání ověřování (`AcquireTokenAsync` a `AcquireTokenSilentAsync` pro ADAL.NET, a `AqquireTokenInteractive` a `AcquireTokenSilent` v MSAL.NET), ale s různými parametry vyžaduje. Jedním rozdílem je skutečnost, že v MSAL.NET, už žádné a zajistěte tak předání `ClientID` vaší aplikace v každém AcquireTokenXX volání. Ve skutečnosti `ClientID` je nastavit pouze jednou při sestavování (`IPublicClientApplication` nebo `IConfidentialClientApplication`).

### <a name="iaccount-not-iuser"></a>IAccount není IUser

ADAL.NET manipulovat uživatelů. Uživatel je člověk nebo agent softwaru, ale může mít/vlastní/zodpovídat za jeden nebo více účtů v systému identit společnosti Microsoft (několik Azure AD účty Azure AD B2C, osobní účty Microsoft). 

MSAL.NET 2.x teď definuje pojem účet (prostřednictvím rozhraní IAccount). Tento narušující změně poskytne tak sémantiku vpravo: skutečnost, že stejný uživatel může mít několik účtů v různých Azure AD adresáře. MSAL.NET také poskytuje lepší informace ve scénářích hosta, získáte informace o domácí účtu.

Další informace o rozdílech mezi IUser a IAccount najdete v tématu [MSAL.NET 2.x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Výjimky

#### <a name="interaction-required-exceptions"></a>Součinnosti výjimky

MSAL.NET má více explicitní výjimky. Pokud se nezdaří bezobslužné ověření v ADAL postup je třeba k zachycení výjimky a najděte `user_interaction_required` kód chyby:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == “user_interaction_required”)
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

Podrobnosti najdete v [doporučený model pro získání tokenu](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) s ADAL.NET

Pomocí MSAL.NET catch `MsalUiRequiredException` jak je popsáno v [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Zpracování výjimek challenge deklarace identity

V ADAL.NET deklarace identity výzvu, zpracování výjimek následujícím způsobem:

- `AdalClaimChallengeException` výjimka (odvozená z `AdalServiceException`) vyvolané službu v případě, že prostředku vyžaduje více deklarací z uživatele (například dva faktory ověřování). `Claims` Obsahuje člen s deklarací identity, které se očekává, že některé fragment ve formátu JSON.
- Stále v ADAL.NET, potřebuje volat aplikace veřejným klientem přijetí této výjimky `AcquireTokenInteractive` přepsat s deklarací parametrů. Toto přepsání `AcquireTokenInteractive` i nepokusí se přístupů do mezipaměti, protože není nutné. Důvodem je, že token v mezipaměti nemá správné deklarace identity (jinak `AdalClaimChallengeException` nebude vyvolána). Proto není nutné se podívat na mezipaměť. Všimněte si, že `ClaimChallengeException` může být přijata v WebAPI způsobem OBO, že `AcquireTokenInteractive` musí být volána v aplikace veřejným klientem volání této webové rozhraní API.
- Podrobnosti, včetně ukázek najdete v tématu zpracování [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)

V MSAL.NET deklarace identity výzvu, zpracování výjimek následujícím způsobem:

- `Claims` Jsou prezentované v `MsalServiceException`.
- Je `.WithClaim(claims)` metodu, která můžete použít `AcquireTokenInteractive` Tvůrce. 

### <a name="supported-grants"></a>Podporované uděluje

Ne všechny příspěvky jsou ještě nepodporuje MSAL.NET a koncový bod verze 2.0. Následuje souhrn porovnání ADAL.NET a MSAL. NET je podporována uděluje.

#### <a name="public-client-applications"></a>Veřejné klientské aplikace

Tady jsou uděluje podporovaných ADAL.NET a MSAL.NET pro desktopové i mobilní aplikace

Udělení | ADAL.NET | MSAL.NET
----- |----- | -----
Interaktivní | [Interaktivní ověřování](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Získávání tokenů interaktivně v MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Integrované ověřování systému Windows | [Integrované ověřování Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Ověření integrované Windows](msal-authentication-flows.md#integrated-windows-authentication)
Uživatelské jméno / heslo | [Získávání tokenů pomocí uživatelského jména a hesla](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Ověřování uživatelského jména hesla](msal-authentication-flows.md#usernamepassword)
Tok kódu zařízení | [Profil zařízení pro zařízení bez webových prohlížečů](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Tok kódu při zařízení](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Důvěrné klientské aplikace

Tady jsou uděluje podporovaných ADAL.NET a MSAL.NET pro webové aplikace, webová rozhraní API a proces démon aplikace:

Typ aplikace | Udělení | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Webové aplikace, webové rozhraní API, démona | Přihlašovací údaje klienta | [Toky přihlašovacích údajů klienta v ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Pověření klienta toků MSAL.NET](msal-authentication-flows.md#client-credentials))
Web API | Jménem: | [Komunikace mezi službami volá jménem uživatele s ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [Jménem aplikace v MSAL.NET](msal-authentication-flows.md#on-behalf-of)
Webová aplikace | Ověřování kódu | [Získávání tokenů s autorizační kódy ve službě web apps s ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Získávání tokenů s autorizační kódy ve službě web apps pomocí A MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Zachování v mezipaměti

ADAL.NET umožňuje rozšířit `TokenCache` třídy k implementaci funkcionality požadovanou trvalostí na platformách bez zabezpečeného úložiště (rozhraní .NET Framework a .NET core) s použitím `BeforeAccess`, a `BeforeWrite` metody. Podrobnosti najdete v tématu [Token serializace mezipaměti v ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

MSAL.NET díky mezipaměť tokenu zapečetěnou třídu odebírá rozšíření. Proto implementace trvalost pro mezipaměť tokenu musí být ve formě pomocnou třídu, která komunikuje s mezipamětí tokenů zapečetěné. Tato interakce je popsána v [Token serializace mezipaměti v MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>Označení běžné autority

V v1.0, pokud použijete https://login.microsoftonline.com/common autority, vám umožní uživatelům přihlašovat se pomocí libovolného účtu AAD (pro každou organizaci). Zobrazit [autority ověřování v ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Pokud používáte https://login.microsoftonline.com/common autorita v2.0, vám umožní uživatelům přihlašovat se pomocí jakékoli organizaci AAD nebo osobní účet Microsoft (MSA). V MSAL.NET, pokud chcete omezit přihlášení k libovolnému účtu AAD (stejné chování jako s ADAL.NET), budete muset použít https://login.microsoftonline.com/organizations. Podrobnosti najdete v tématu `authority` parametr [aplikace veřejným klientem](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>tokeny V1.0 a v2.0

Existují dvě verze tokenů:
- Tokeny V1.0
- Tokeny v2.0 

Koncový bod verze 1.0 (používané ADAL) generuje pouze tokeny v1.0.

Koncový bod verze 2.0 (používané MSAL) však vysílá verzi token, který přijímá webového rozhraní API. Vlastnosti manifestu aplikace webového rozhraní API umožňuje vývojářům vybrat, kterou verzi token je přijat. Zobrazit `accessTokenAcceptedVersion` v [manifest aplikace](reference-app-manifest.md) referenční dokumentaci.

Další informace o tokeny v1.0 a v2.0, naleznete v tématu [přístupové tokeny služby Azure Active Directory](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Oborů pro webové rozhraní API přijímat tokeny v1.0

Obory oprávnění, které v1.0 webovou aplikaci s rozhraním API (prostředek) uvádí pro klientské aplikace jsou oprávnění OAuth2. Tyto obory oprávnění může udělit klientské aplikace během souhlas. V části o oauth2Permissions v [manifest aplikace Azure Active Directory](active-directory-application-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Obory požádáte o přístup k určité oprávnění OAuth2 aplikace v1.0

Pokud chcete získat tokeny pro konkrétní obory v1.0 aplikace (například grafu AAD, což je https://graph.windows.net), je potřeba vytvořit `scopes` zřetězením identifikátor požadovaný prostředek se požadované oprávnění OAuth2 pro daný prostředek.

Například pro přístup k názvu uživatele v1.0 webového rozhraní API, což je identifikátor URI ID aplikace `ResourceId`, kterou chcete použít:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

Pokud chcete číst a zapisovat pomocí MSAL.NET Azure Active Directory pomocí rozhraní AAD graph API (https://graph.windows.net/), vytvořili byste seznam oborů jako následující fragment kódu:

```csharp
ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + “Directory.Read”, ResourceID + “Directory.Write”}
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Upozornění: Pokud máte jednu nebo dvě lomítka v oboru odpovídající v1.0 webového rozhraní API

Pokud chcete zadat rozsahu odpovídající rozhraní API Azure Resource Manageru (https://management.core.windows.net/), budete muset požádat o k následujícímu oboru (Poznámka: dvě lomítka) 

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Důvodem je, že rozhraní API Resource Manageru v jeho deklarace identity cílové skupiny očekává lomítko (`aud`), a pak je lomítko pro oddělení název rozhraní API z oboru.

Logikou používanou službou Azure AD je následující:
- Pro koncový bod ADAL (verze 1.0) verze 1.0 přístupového tokenu (jediné možné), aud = prostředků
- Pro požadující přístupového tokenu prostředku přijímat tokeny v2.0, aud MSAL (koncový bod verze 2.0) = prostředků. ID aplikace
- Azure AD pro MSAL (koncový bod verze 2.0) s dotazem přístupového tokenu pro určitý prostředek, který přijímá token přístupu verze 1.0 (což se v případě výše), analyzuje požadovanou cílovou skupinu z požadovaného oboru převzetím všechno dřív než poslední lomítko a používat jej jako identifikátor prostředku. Proto pokud https://database.windows.net očekává, že cílovou skupinou "https://database.windows.net/", budete muset požádat o oboru https://database.windows.net//.default. Viz také vystavovat #[747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Adresa url prostředku koncového lomítka vynecháte, která způsobila chybu při ověřování sql #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Obory požádáte o přístup k všechna oprávnění aplikace v1.0

Například pokud chcete získat token pro všechny statické obory aplikace v1.0, jedna bude třeba použít

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Obory požadovat v případě klienta přihlašovacích údajů tok / démon aplikace

V případě tok přihlašovacích údajů klienta, by také být oboru předat `/.default`. To říká do služby Azure AD: "všechny aplikace úrovně oprávnění, která správce vyjádřil souhlas se v registraci aplikace.

## <a name="adal-to-msal-migration"></a>ADAL MSAL migrace

Ve verzi v2 ADAL.NET. X, obnovovací tokeny byly zpřístupněné, abyste mohli vyvíjet řešení po použití těchto tokenů jejich ukládání do mezipaměti a použitím `AcquireTokenByRefreshToken` metody poskytované objektem ADAL 2.x. Některé z těchto řešení byly použity v situacích, jako:
* Dlouho běžící služby, které dělat, včetně aktualizace řídicích panelů jménem uživatele, že uživatelé už nejste připojení. 
* Webové farmě scénáře pro povolení klientům přinést RT k webové službě (ukládání do mezipaměti se provádí na straně klienta, zašifrovaného souboru cookie a ne na straně serveru)

Nejedná se v případě MSAL.NET, ale jako jsme žádné déle, doporučujeme využívat aktualizace tokeny tímto způsobem z bezpečnostních důvodů. To by ji činilo obtížně migrovat do MSAL 3.x jako rozhraní API neposkytuje způsob, jak předat dříve nakoupený obnovovací tokeny. 

Naštěstí MSAL.NET nyní používá rozhraní API, která umožňuje migrovat vaše předchozí obnovovací tokeny do `IConfidentialClientApplication` 

```CSharp
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
 
Pomocí této metody můžete zadat předchozích obnovovací token spolu s všechny obory (prostředky), vyžadujete. Token obnovení bude vyměněn nové a uložili do mezipaměti do vaší aplikace.  

Protože tato metoda je určena pro scénáře, které nejsou typické, není snadno přístupné pomocí `IConfidentialClientApplication` bez předchozího přetypování na `IByRefreshToken`.

Tento fragment kódu ukazuje některé migrace kódu v aplikaci důvěrnému klientovi. `GetCachedRefreshTokenForSignedInUser` Načtěte token obnovení, která byla uložená v některých úložiště pomocí předchozí verze aplikace, která umožňuje využít knihovny ADAL 2.x. `GetTokenCacheForSignedInUser` deserializuje mezipaměti pro přihlášeného uživatele (jako důvěrné klientské aplikace by měla mít jeden mezipaměti na uživatele).

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

Zobrazí se přístupový token a ID tokenu vrácenému v vaše AuthenticationResult, zatímco nového tokenu obnovení jsou uloženy v mezipaměti.

Tuto metodu můžete také použít pro různé scénáře integrace kde máte obnovovací token, který je k dispozici.

## <a name="next-steps"></a>Další postup

Další informace o oborech v [obory, oprávnění a souhlas v koncovém bodě Microsoft identity platform](v2-permissions-and-consent.md)
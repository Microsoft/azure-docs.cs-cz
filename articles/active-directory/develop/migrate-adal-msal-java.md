---
title: Příručka k migraci ADAL na MSAL pro platformu Java-Microsoft Identity Platform | Azure
description: Naučte se Azure Active Directory migrovat aplikaci MSAL Authentication Library (ADAL) Java do knihovny Microsoft Authentication Library ().
services: active-directory
author: sangonzal
manager: henrikm
editor: twhitney
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/4/2019
ms.author: sagonzal
ms.reviewer: navyari.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bddf787ce2c654da99b16387ae347f51600c8dd
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905521"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Příručka k migraci ADAL do MSAL pro Java

Tento článek popisuje změny, které je třeba provést při migraci aplikace, která používá knihovnu Azure Active Directory Authentication Library (ADAL) k použití knihovny Microsoft Authentication Library (MSAL).

K ověřování entit Azure AD a žádosti o tokeny od Azure AD se používají knihovny Microsoft Authentication Library pro Java (MSAL4J) a Azure AD Authentication Library pro Java (ADAL4J). Předtím, než se teď většina vývojářů pracovala s Azure AD pro vývojáře (verze 1.0) k ověřování identit Azure AD (pracovní a školní účty) tím, že žádá o tokeny pomocí služby Azure AD Authentication Library (ADAL).

MSAL nabízí následující výhody:

- Protože používá novější koncový bod platformy Microsoft identity, můžete ověřit širší sadu identit Microsoftu, jako jsou identity Azure AD, účty Microsoft a sociální a místní účty prostřednictvím Azure AD Business to Consumer (B2C).
- vaši uživatelé získají nejlepší možnosti jednotného přihlašování.
- Vaše aplikace může povolit postupný souhlas a podpora podmíněného přístupu je jednodušší.

MSAL for Java (MSAL4J) je knihovna pro ověřování, kterou doporučujeme používat s platformou Microsoft identity. V ADAL4J se neimplementují žádné nové funkce. Veškeré úsilí, které se dodávají dál, se zaměřuje na zlepšení MSAL.

## <a name="differences"></a>Rozdíly

Pokud jste pracovali s koncovým bodem Azure AD for Developers (v 1.0) (a ADAL4J), můžete si přečíst informace [o tom, co se liší od koncového bodu Microsoft Identity Platform (v 2.0)?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison).

## <a name="scopes-not-resources"></a>Obory nejsou prostředky

ADAL4J získává tokeny pro prostředky, zatímco MSAL4J získává tokeny pro obory. Počet tříd MSAL4J vyžaduje parametr scopes. Tento parametr je seznam řetězců, které deklarují požadovaná oprávnění a požadované prostředky. Ukázkové obory najdete v tématu věnovaném [oborům Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference) .

## <a name="core-classes"></a>Základní třídy

V ADAL4J třída `AuthenticationContext` představuje vaše připojení ke službě tokenů zabezpečení (STS) nebo autorizačnímu serveru prostřednictvím autority. MSAL4J je však navržena kolem klientských aplikací. Poskytuje dvě samostatné třídy: `PublicClientApplication` a `ConfidentialClientApplication` představují klientské aplikace.  Druhý, `ConfidentialClientApplication`, představuje aplikaci, která je navržena tak, aby bezpečně udržovala tajný klíč, jako je například identifikátor aplikace pro aplikaci démon.

Následující tabulka ukazuje, jak se funkce ADAL4J mapují na nové funkce MSAL4J:

| Metoda ADAL4J| Metoda MSAL4J|
|------|-------|
|acquireToken (prostředek řetězce, přihlašovací údaj ClientCredential, AuthenticationCallback zpětné volání) | acquireToken(ClientCredentialParameters)|
|acquireToken (řetězcové prostředky, ClientAssertion kontrolní výraz, AuthenticationCallback zpětné volání)|acquireToken(ClientCredentialParameters)|
|acquireToken (prostředek řetězce, přihlašovací údaj AsymmetricKeyCredential, AuthenticationCallback zpětné volání)|acquireToken(ClientCredentialParameters)|
|acquireToken (prostředek řetězce, řetězec clientId, uživatelské jméno, heslo řetězce, zpětné volání AuthenticationCallback)| acquireToken(UsernamePasswordParameters)|
|acquireToken (řetězec prostředku, řetězec clientId, uživatelské jméno, heslo řetězce = null, zpětné volání AuthenticationCallback)|acquireToken(IntegratedWindowsAuthenticationParameters)|
|acquireToken (řetězcové prostředky, UserAssertion userAssertion, ClientCredential Credential, zpětné volání AuthenticationCallback)| acquireToken(OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode() | acquireToken(AuthorizationCodeParameters) |
| acquireDeviceCode () a acquireTokenByDeviceCode ()| acquireToken(DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount místo IUser

ADAL4J manipulovat s uživateli. I když uživatel představuje jednoho lidského nebo softwarového agenta, může mít jeden nebo více účtů v systému Microsoft identity. Uživatel může například mít několik osobních účtů Azure AD, Azure AD B2C nebo Microsoft.

MSAL4J definuje koncept účtu prostřednictvím rozhraní `IAccount`. Jedná se o zásadní změnu z ADAL4J, ale je to dobrý důvod, protože zachycuje skutečnost, že stejný uživatel může mít několik účtů, a případně i v různých adresářích služby Azure AD. MSAL4J poskytuje lepší informace v hostujících scénářích, protože jsou k dispozici informace o domácím účtu.

## <a name="cache-persistence"></a>Trvalost mezipaměti

ADAL4J nepodporoval mezipaměť tokenů.
MSAL4J přidá [mezipaměť tokenů](msal-acquire-cache-tokens.md) pro zjednodušení správy životností tokenů tím, že automaticky aktualizuje vypršení platnosti tokenů, pokud je to možné, a zabrání uživateli, aby poskytl pověření, pokud je to možné.

## <a name="common-authority"></a>Společná autorita

Pokud v v 1.0 používáte `https://login.microsoftonline.com/common` autoritu, můžou se uživatelé přihlašovat pomocí libovolného účtu Azure Active Directory (AAD) (pro libovolnou organizaci).

Pokud použijete autoritu `https://login.microsoftonline.com/common` v v 2.0, uživatelé se mohou přihlašovat pomocí libovolné organizace AAD nebo i osobní účet společnosti Microsoft (MSA). Pokud chcete omezit přihlášení na libovolný účet AAD, musíte v MSAL4J použít autoritu `https://login.microsoftonline.com/organizations` (což je stejné chování jako u ADAL4J). Chcete-li určit autoritu, nastavte parametr `authority` v metodě [PublicClientApplication. Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) při vytváření vaší `PublicClientApplication` třídy.

## <a name="v10-and-v20-tokens"></a>tokeny v 1.0 a v 2.0

Koncový bod v 1.0 (používaný v ADAL) vysílá jenom tokeny v 1.0.

Koncový bod v 2.0 (používaný v MSAL) může generovat tokeny v 1.0 a v 2.0. Vlastnost manifestu aplikace webového rozhraní API umožňuje vývojářům zvolit, která verze tokenu je přijata. Viz `accessTokenAcceptedVersion` v referenční dokumentaci k [manifestu aplikace](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) .

Další informace o tokenech v 1.0 a v 2.0 najdete v tématu [Azure Active Directory Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).

## <a name="adal-to-msal-migration"></a>MSAL migrace ADAL

V ADAL4J byly k dispozici aktualizační tokeny – což povoluje vývojářům jejich ukládání do mezipaměti. Pak používají `AcquireTokenByRefreshToken()` k povolení řešení, jako je implementace dlouhotrvajících služeb, které aktualizují řídicí panely jménem uživatele, když už uživatel není připojený.

MSAL4J nevystavuje aktualizační tokeny z bezpečnostních důvodů. Místo toho MSAL zpracovává aktualizace tokenů za vás.

MSAL4J má rozhraní API, které umožňuje migrovat obnovovací tokeny, které jste získali pomocí ADAL4j, do ClientApplication: [acquireToken (RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-). Pomocí této metody můžete zadat dříve použitý obnovovací token spolu s libovolnými obory (prostředky), které si přejete. Obnovovací token se vymění pro nové a v mezipaměti pro použití vaší aplikací.

Následující fragment kódu ukazuje určitý kód migrace v důvěrné klientské aplikaci:

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

`IAuthenticationResult` vrátí přístupový token a token ID, zatímco váš nový obnovovací token je uložený v mezipaměti. Aplikace bude nyní obsahovat také IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Chcete-li použít tokeny, které jsou nyní v mezipaměti, zavolejte:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build(); 
IAuthenticationResult result = app.acquireToken(parameters);
```

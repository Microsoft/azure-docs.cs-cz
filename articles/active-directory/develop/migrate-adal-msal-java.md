---
title: Průvodce migrací ADAL to MSAL (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak migrovat aplikaci Java (ADAL) (Azure Active Directory Authentication Library) do Knihovny ověřování Microsoftu (MSAL).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: 7ba845e79074313f0ccf2c066ba016bd72d46efe
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534563"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Průvodce migrací ADAL to MSAL pro Javu

Tento článek upozorňuje na změny, které je třeba provést k migraci aplikace, která používá Azure Active Directory Authentication Library (ADAL) k použití Knihovny ověřování Microsoft (MSAL).

Knihovna ověřování Microsoftu pro Jazyk Java (MSAL4J) a Azure AD Authentication Library pro Jazyk Java (ADAL4J) se používají k ověřování entit Azure AD a vyžádání tokenů z Azure AD. Až dosud většina vývojářů spolupracovala s platformou Azure AD pro vývojáře (v1.0) k ověření identit Azure AD (pracovní ch a školních účtů) vyžádáním tokenů pomocí Azure AD Authentication Library (ADAL).

MSAL nabízí následující výhody:

- Vzhledem k tomu, že používá novější koncový bod platformy identit Microsoftu, můžete ověřit širší sadu identit Microsoftu, jako jsou identity Azure AD, účty Microsoft a sociální a místní účty prostřednictvím Azure AD Business to Consumer (B2C).
- Vaši uživatelé získají nejlepší prostředí pro jednotné přihlašování.
- Vaše aplikace může povolit přírůstkový souhlas a podpora podmíněného přístupu je jednodušší.

MSAL pro Java je auth knihovna doporučujeme použít s platformou identit microsoftu. Žádné nové funkce budou implementovány na ADAL4J. Veškeré úsilí do budoucna je zaměřeno na zlepšení MSAL.

## <a name="differences"></a>Rozdíly

Pokud jste pracovali s koncovým bodem Azure AD pro vývojáře (v1.0) (a ADAL4J), můžete si přečíst [Co se liší o koncovém bodu platformy identit y Microsoftu (v2.0) .](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

## <a name="scopes-not-resources"></a>Obory nejsou prostředky

ADAL4J získává tokeny pro prostředky vzhledem k tomu, MSAL pro Java získává tokeny pro obory. Počet MSAL pro třídy Java vyžadují parametr oborů. Tento parametr je seznam řetězců, které deklarují požadovaná oprávnění a prostředky, které jsou požadovány. Ukázkové obory najdete v [oborech microsoft graphu.](https://docs.microsoft.com/graph/permissions-reference)

## <a name="core-classes"></a>Základní třídy

V ADAL4J `AuthenticationContext` třída představuje vaše připojení ke službě TOKEN (Security Token Service) nebo autorizačnímu serveru prostřednictvím úřadu. MSAL pro Java je však navržen kolem klientských aplikací. Poskytuje dvě samostatné `PublicClientApplication` třídy: a `ConfidentialClientApplication` představují klientské aplikace.  Druhý , `ConfidentialClientApplication`představuje aplikaci, která je navržena tak, aby bezpečně udržovat tajný klíč, jako je například identifikátor aplikace pro aplikaci daemon.

Následující tabulka ukazuje, jak funkce ADAL4J mapují na nové funkce MSAL pro java funkce:

| Metoda ADAL4J| Metoda MSAL4J|
|------|-------|
|acquireToken(String resource, ClientCredential credential, AuthenticationCallback callback.) | acquireToken(ClientCredentialParameters)|
|acquireToken(Prostředek řetězce, kontrolní výraz služby ClientAssertion, zpětné zpětné volání authenticationCallback)|acquireToken(ClientCredentialParameters)|
|acquireToken(Řetězec prostředku, pověření AsymmetricKeyCredential, zpětné volání AuthenticationCallback)|acquireToken(ClientCredentialParameters)|
|acquireToken(String resource, String clientId, String username, String password, AuthenticationCallback callback.)| acquireToken(UsernamePasswordParameters)|
|acquireToken(String resource, String clientId, String username, String password=null, AuthenticationCallback callback.)|acquireToken(IntegratedWindowsAuthenticationParameters)|
|acquireToken(Řetězec prostředku, userAssertion UserAssertion, pověření pověření klienta, zpětné volání AuthenticationCallback)| acquireToken(OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode() | acquireToken(AuthorizationCodeParameters) |
| acquireDeviceCode() a acquireTokenByDeviceCode()| acquireToken(DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IÚčet místo IUser

ADAL4J manipuloval s uživateli. Přestože uživatel představuje jednoho lidského nebo softwarového agenta, může mít jeden nebo více účtů v systému identit společnosti Microsoft. Uživatel může mít například několik účtů Azure AD, Azure AD B2C nebo Microsoft personal.

MSAL pro Java definuje koncept účtu `IAccount` přes rozhraní. Toto je narušující změna z ADAL4J, ale je to dobrý, protože zachycuje skutečnost, že stejný uživatel může mít několik účtů a možná i v různých adresářích Azure AD. MSAL pro Java poskytuje lepší informace ve scénářích hosta, protože jsou k dispozici informace o domácím účtu.

## <a name="cache-persistence"></a>Trvalost mezipaměti

ADAL4J neměl podporu pro mezipaměť tokenů.
MSAL pro Jazyk Java přidá [mezipaměť tokenů](msal-acquire-cache-tokens.md) pro zjednodušení správy životnosti tokenů automaticky aktualizovat tokeny s ukončenou platností, pokud je to možné, a zabránit zbytečné výzvy pro uživatele poskytnout pověření, pokud je to možné.

## <a name="common-authority"></a>Společný úřad

Ve v1.0, pokud `https://login.microsoftonline.com/common` používáte autoritu, uživatelé se mohou přihlásit pomocí libovolného účtu Služby Azure Active Directory (AAD) (pro libovolnou organizaci).

Pokud používáte `https://login.microsoftonline.com/common` autoritu v v2.0, uživatelé se mohou přihlásit pomocí libovolné organizace AAD nebo dokonce osobního účtu Microsoft (MSA). V MSAL pro Java, pokud chcete omezit přihlášení k libovolnému účtu `https://login.microsoftonline.com/organizations` AAD, musíte použít autoritu (což je stejné chování jako u ADAL4J). Chcete-li určit autoritu, nastavte `authority` parametr v metodě [PublicClientApplication.Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) při vytváření `PublicClientApplication` třídy.

## <a name="v10-and-v20-tokens"></a>tokeny v1.0 a v2.0

Koncový bod v1.0 (používaný ADAL) vydává pouze tokeny v1.0.

Koncový bod v2.0 (používaný msal) může vyzařovat tokeny v1.0 a v2.0. Vlastnost manifestu aplikace webového rozhraní API umožňuje vývojářům zvolit, která verze tokenu je přijata. Viz `accessTokenAcceptedVersion` referenční dokumentace [manifestu aplikace.](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)

Další informace o tokenech v1.0 a v2.0 najdete v [tématu Tokeny přístupu služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).

## <a name="adal-to-msal-migration"></a>ADAL na MSAL migrace

V ADAL4J byly vystaveny tokeny aktualizace -- což umožnilo vývojářům ukládat je do mezipaměti. Poté by `AcquireTokenByRefreshToken()` povolovaly řešení, jako je implementace dlouhotrvajících služeb, které aktualizují řídicí panely jménem uživatele, když uživatel již není připojen.

MSAL pro Java nezveřejňuje tokeny aktualizace z bezpečnostních důvodů. Místo toho MSAL zpracovává osvěžující tokeny pro vás.

MSAL pro Jazyk Java má rozhraní API, které umožňuje migrovat obnovovací tokeny, které jste získali pomocí ADAL4j do klientské aplikace: [acquireToken(RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-). Pomocí této metody můžete poskytnout dříve použitý obnovovací token spolu se všemi obory (prostředky), které si přejete. Obnovovací token bude vyměněn za nový a uložen do mezipaměti pro použití vaší aplikací.

Následující fragment kódu zobrazuje nějaký kód migrace v důvěrné klientské aplikaci:

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

Vrátí `IAuthenticationResult` přístupový token a id token, zatímco váš nový obnovovací token je uložen v mezipaměti.
Aplikace bude nyní také obsahovat iÚčet:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Chcete-li použít tokeny, které jsou nyní v mezipaměti, volejte:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build();
IAuthenticationResult result = app.acquireToken(parameters);
```

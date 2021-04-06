---
title: Získat tokeny & cache pomocí knihovny Microsoft Authentication Library (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o získání a ukládání tokenů do mezipaměti pomocí MSAL.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 98ae81626db637f5b0bd6bfe9e294c32293d09e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755061"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Získání a ukládání tokenů do mezipaměti pomocí knihovny Microsoft Authentication Library (MSAL)

[Přístupové tokeny](access-tokens.md) umožňují klientům bezpečně volat webová rozhraní API chráněná Azure. Existuje několik způsobů, jak získat token pomocí knihovny Microsoft Authentication Library (MSAL). Některé vyžadují interakci uživatele prostřednictvím webového prohlížeče, zatímco jiné nevyžadují interakci s uživatelem. Obecně platí, že metoda použitá pro získání tokenu závisí na tom, jestli je aplikace veřejná klientská aplikace, jako je desktopová nebo mobilní aplikace, nebo důvěrná klientská aplikace, jako je webová aplikace, webové rozhraní API nebo aplikace démon.

MSAL uloží token po jeho získání. Před pokusem o získání tokenu jiným způsobem by měl váš kód aplikace nejprve zkusit získat token z mezipaměti tiše.

Můžete také vymazat mezipaměť tokenu, která se dosahuje odebráním účtů z mezipaměti. Tím se ale neodstraní soubor cookie relace, který je v prohlížeči.

## <a name="scopes-when-acquiring-tokens"></a>Obory při získávání tokenů

[Obory](v2-permissions-and-consent.md) jsou oprávnění, která webovým rozhraní API zpřístupňuje, že klientské aplikace můžou požádat o přístup k. Klientské aplikace požadují souhlas uživatele pro tyto obory při vytváření žádostí o ověření, aby získaly tokeny pro přístup k webovým rozhraním API. MSAL umožňuje získat tokeny pro přístup k Azure AD pro vývojáře (v 1.0) a rozhraní Microsoft Identity Platform API. protokol v 2.0 místo prostředků v požadavcích používá obory. Další informace najdete v tématu [porovnání čtení v 1.0 a v 2.0](../azuread-dev/azure-ad-endpoint-comparison.md). Na základě konfigurace webového rozhraní API verze tokenu, kterou přijímá, vrátí koncový bod v 2.0 přístupový token do MSAL.

Některé metody získání tokenu MSAL vyžadují `scopes` parametr. `scopes`Parametr je seznam řetězců, které deklarují požadovaná oprávnění a požadované prostředky. Dobře známé obory jsou [Microsoft Graph oprávnění](/graph/permissions-reference).

V MSAL je také možné získat přístup k prostředkům v 1.0. Další informace najdete v tématu [obory pro aplikaci v 1.0](msal-v1-app-scopes.md).

### <a name="request-scopes-for-a-web-api"></a>Rozsahy žádostí pro webové rozhraní API

Pokud vaše aplikace potřebuje požádat o přístupový token s konkrétními oprávněními pro rozhraní API prostředků, předejte obory obsahující identifikátor URI ID aplikace rozhraní API ve formátu `<app ID URI>/<scope>` .

Příklady hodnot oboru pro různé prostředky:

- Rozhraní Microsoft Graph API: `https://graph.microsoft.com/User.Read`
- Vlastní webové rozhraní API: `api://11111111-1111-1111-1111-111111111111/api.read`

Formát hodnoty oboru se liší v závislosti na prostředku (rozhraní API) přijímajícího přístupového tokenu a `aud` hodnotách deklarace identity, které přijímá.

Pro Microsoft Graph jenom pro rozsah, který se `user.read` mapuje na `https://graph.microsoft.com/User.Read` , a oba formáty oborů se dají použít zaměnitelné.

Některá webová rozhraní API, například rozhraní API pro Azure Resource Manager ( https://management.core.windows.net/) očekávají koncové lomítko (/) v deklaraci identity cílové skupiny ( `aud` ) přístupového tokenu. V takovém případě předejte obor jako `https://management.core.windows.net//user_impersonation` , včetně dvojitého lomítka (//).

Jiná rozhraní API můžou vyžadovat, aby v hodnotě oboru nejsou zahrnuté *žádné schéma ani hostitel* , a očekává se jenom ID aplikace (GUID) a název oboru, například:

`11111111-1111-1111-1111-111111111111/api.read`

> [!TIP]
> Pokud se prostředek pro příjem dat neřídí vaším ovládacím prvkem, možná budete muset vyzkoušet jiné formáty hodnot oboru (například s schématem a bez hostitele), pokud při `401` předávání přístupového tokenu k prostředku dojde k chybě nebo k jiným chybám.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Požádat o dynamické obory pro přírůstkové vyjádření souhlasu

Vzhledem k tomu, že se funkce poskytované vaší aplikací nebo jejich požadavky mění, můžete podle potřeby požádat o další oprávnění pomocí parametru Scope. Tyto *dynamické rozsahy* umožňují uživatelům poskytnout přírůstkový souhlas s obory.

Můžete se třeba přihlásit jako uživatel, ale nejdřív jim odepřít přístup k žádným prostředkům. Později jim můžete dát možnost zobrazit svůj kalendář tím, že požádáte o obor kalendáře v metodě získat token a získat jeho souhlas. Například vyžádáním `https://graph.microsoft.com/User.Read` `https://graph.microsoft.com/Calendar.Read` oborů a.

## <a name="acquiring-tokens-silently-from-the-cache"></a>Tiché získávání tokenů (z mezipaměti)

MSAL udržuje mezipaměť tokenů (nebo dvě mezipaměti pro důvěrné klientské aplikace) a po získání tokenu uloží do mezipaměti. V mnoha případech se při pokusu o tiché získání tokenu získá další token s více rozsahy na základě tokenu v mezipaměti. Je také možné aktualizovat token, když se blíží vypršení platnosti (protože mezipaměť tokenů obsahuje také obnovovací token).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Doporučený vzor volání pro veřejné klientské aplikace

Kód aplikace by se měl nejdřív pokusit o získání tokenu z mezipaměti v tichém režimu. Pokud volání metody vrátí chybu "požadováno UI" nebo výjimku, zkuste získat token jiným způsobem.

Existují dva toky, ale **neměli byste** se pokoušet o tichou získání tokenu:

- [Tok přihlašovacích údajů klienta](msal-authentication-flows.md#client-credentials), který nepoužívá mezipaměť tokenu uživatele, ale mezipaměť tokenu aplikace. Tato metoda postará o ověření mezipaměti tokenu aplikace před odesláním požadavku službě tokenu zabezpečení (STS).
- [Tok autorizačního kódu](msal-authentication-flows.md#authorization-code) ve webových aplikacích, protože uplatňuje kód, který aplikace získala přihlašováním uživatele a který má souhlas s více obory. Vzhledem k tomu, že kód a není účet předán jako parametr, nemůže metoda najít mezipaměť před uplatněním kódu, který vyvolá volání služby.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Doporučený vzor volání ve webových aplikacích s využitím toku autorizačního kódu

U webových aplikací, které používají [tok autorizačního kódu OpenID Connect](v2-protocols-oidc.md), je doporučený vzor v řadičích:

- Vytvořte instanci důvěrné klientské aplikace s mezipamětí tokenu s přizpůsobenou serializací.
- Získání tokenu pomocí toku autorizačního kódu

## <a name="acquiring-tokens"></a>Získávání tokenů

Obecně platí, že metoda získání tokenu závisí na tom, jestli je to veřejný klient nebo důvěrná klientská aplikace.

### <a name="public-client-applications"></a>Veřejné klientské aplikace

Pro veřejné klientské aplikace (desktopové nebo mobilní aplikace):

- Tokeny se často získávají interaktivně, takže se uživatel přihlásí prostřednictvím uživatelského rozhraní nebo automaticky otevíraného okna.
- Může [pro přihlášeného uživatele tiše získat token](msal-authentication-flows.md#integrated-windows-authentication) pomocí integrovaného ověřování systému Windows (IWA/Kerberos), pokud je desktopová aplikace spuštěná na počítači s Windows připojeném k doméně nebo do Azure.
- Může [získat token s uživatelským jménem a heslem](msal-authentication-flows.md#usernamepassword) v klientských aplikacích rozhraní .NET Framework pro stolní počítače (nedoporučuje se). V důvěrných klientských aplikacích nepoužívejte uživatelské jméno a heslo.
- Může získat token prostřednictvím [toku kódu zařízení](msal-authentication-flows.md#device-code) v aplikacích spuštěných na zařízeních, která nemají webový prohlížeč. Uživatel se poskytuje s adresou URL a kódem, který potom přejde do webového prohlížeče na jiném zařízení a vloží kód a přihlásí se. Azure AD pak pošle token zpátky do zařízení bez prohlížeče.

### <a name="confidential-client-applications"></a>Důvěrné klientské aplikace

Pro důvěrné klientské aplikace (webová aplikace, webové rozhraní API nebo aplikace démona, jako je například služba systému Windows):

- Získejte tokeny **pro samotnou aplikaci** , nikoli pro uživatele, pomocí [toku přihlašovacích údajů klienta](msal-authentication-flows.md#client-credentials). Tato technika se dá použít k synchronizaci nástrojů nebo k nástrojům, které obecně zpracovávají uživatele a nikoli konkrétního uživatele.
- K volání rozhraní API jménem uživatele používejte tok spouštěný [jménem](msal-authentication-flows.md#on-behalf-of) webového rozhraní API. Aplikace je identifikována s přihlašovacími údaji klienta, aby získala token na základě kontrolního výrazu uživatele (SAML, například tokenu JWT). Tento tok se používá aplikacemi, které potřebují přístup k prostředkům určitého uživatele v voláních služby-služba.
- Získejte tokeny pomocí [toku autorizačního kódu](msal-authentication-flows.md#authorization-code) ve webových aplikacích poté, co se uživatel přihlásí prostřednictvím adresy URL žádosti o autorizaci. Aplikace OpenID Connect obvykle používá tento mechanismus, který umožňuje uživatelům přihlašovat se pomocí otevřeného ID připojit a pak přistupovat k webovým rozhraním API jménem uživatele.

## <a name="authentication-results"></a>Výsledky ověřování

Když si klient vyžádá přístupový token, Azure AD také vrátí výsledek ověření, který obsahuje metadata o přístupovém tokenu. Tyto informace zahrnují čas vypršení platnosti přístupového tokenu a rozsahy, pro které je platná. Tato data umožňují vaší aplikaci inteligentní ukládání přístupových tokenů do mezipaměti bez nutnosti analyzovat samotný přístupový token. Výsledek ověřování vystavuje:

- [Přístupový token](access-tokens.md) webového rozhraní API pro přístup k prostředkům Tento řetězec je obvykle JWT s kódováním base64, ale klient by nikdy neměl Hledat v přístupovém tokenu. Formát není zaručený, aby zůstal stabilní a mohl by být zašifrovaný pro daný prostředek. Lidé, kteří napisují kód v závislosti na obsahu přístupového tokenu na klientovi, je jedním z nejběžnějších zdrojů chyb a porušení logiky klienta.
- [Token ID](id-tokens.md) pro uživatele (Jwt).
- Vypršení platnosti tokenu, které oznamuje datum a čas, kdy vyprší platnost tokenu.
- ID tenanta obsahuje tenanta, ve kterém se uživatel našel. Pro uživatele typu Host (scénáře Azure AD B2B) je ID tenanta tenant hosta, nikoli jedinečný tenant. Pokud je token dodán jménem uživatele, výsledek ověřování obsahuje také informace o tomto uživateli. U důvěrných toků klienta, kde jsou požadovány tokeny bez uživatele (pro aplikaci), jsou informace o uživateli null.
- Obory, pro které byl token vydán.
- Jedinečné ID uživatele

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>Upřesnit Přístup k tokenům v mezipaměti uživatele v aplikacích a službách na pozadí

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>Další kroky

Několik platforem podporovaných nástrojem MSAL má další informace související s mezipamětí tokenů v dokumentaci k této knihovně platformy. Například:
- [Získání tokenu z mezipaměti tokenů pomocí MSAL.NET](msal-net-acquire-token-silently.md)
- [Jednotné přihlašování s využitím MSAL.js](msal-js-sso.md)
- [Serializace mezipaměti vlastního tokenu v MSAL pro Python](msal-python-token-cache-serialization.md)
- [Serializace mezipaměti vlastního tokenu v MSAL pro Java](msal-java-token-cache-serialization.md)

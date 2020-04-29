---
title: Získat tokeny & cache pomocí MSAL | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o získání a ukládání tokenů do mezipaměti pomocí knihovny Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 647dff9e6401322371ef795a25ca5ced2b517e9c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534580"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Získání a ukládání tokenů do mezipaměti pomocí knihovny Microsoft Authentication Library (MSAL)

[Přístupové tokeny](access-tokens.md) umožňují klientům bezpečně volat webová rozhraní API chráněná Azure. Existuje mnoho způsobů, jak získat token pomocí knihovny Microsoft Authentication Library (MSAL). Některé způsoby vyžadují interakci s uživatelem prostřednictvím webového prohlížeče. Některé nevyžadují interakci s uživatelem. Obecně platí, že způsob získání tokenu závisí na tom, jestli je aplikace veřejná klientská aplikace (desktopová nebo mobilní aplikace), nebo důvěrná klientská aplikace (webová aplikace, webové rozhraní API nebo aplikace démona, jako je například služba systému Windows).

MSAL uloží token po jeho získání.  Kód aplikace by se měl v tichém pokusu získat token (z mezipaměti), nejdřív před získáním tokenu jiným způsobem.

Můžete také vymazat mezipaměť tokenu, která se dosahuje odebráním účtů z mezipaměti. Tím nedojde k odebrání souboru cookie relace, který je v prohlížeči, ale.

## <a name="scopes-when-acquiring-tokens"></a>Obory při získávání tokenů

[Obory](v2-permissions-and-consent.md) jsou oprávnění, která WEBOVÝm rozhraním API zpřístupňuje klientským aplikacím, aby si vyžádali přístup. Klientské aplikace požadují souhlas uživatele pro tyto obory při vytváření žádostí o ověření, aby získaly tokeny pro přístup k webovým rozhraním API. MSAL umožňuje získat tokeny pro přístup k Azure AD pro vývojáře (v 1.0) a rozhraní Microsoft Identity Platform (v 2.0). protokol v 2.0 místo prostředků v požadavcích používá obory. Další informace najdete v tématu [porovnání čtení v 1.0 a v 2.0](active-directory-v2-compare.md). Na základě konfigurace webového rozhraní API verze tokenu, kterou přijímá, vrátí koncový bod v 2.0 přístupový token do MSAL.

Počet metod MSAL získat token vyžaduje parametr *scopes* . Tento parametr je jednoduchý seznam řetězců, který deklaruje požadovaná oprávnění a požadované prostředky. Dobře známé obory jsou [Microsoft Graph oprávnění](/graph/permissions-reference).

V MSAL je také možné získat přístup k prostředkům v 1.0. Další informace najdete v tématu [obory čtení pro aplikaci v 1.0](msal-v1-app-scopes.md).

### <a name="request-specific-scopes-for-a-web-api"></a>Vyžádat konkrétní obory pro webové rozhraní API

Pokud vaše aplikace potřebuje požádat o tokeny s konkrétními oprávněními pro rozhraní API prostředků, budete muset předat obory obsahující identifikátor URI ID aplikace rozhraní API ve spodním formátu: * &lt;obor identifikátoru URI&gt;/&lt;ID aplikace&gt;*

Například obory pro rozhraní Microsoft Graph API:`https://graph.microsoft.com/User.Read`

Například obory pro vlastní webové rozhraní API:`api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

Pro rozhraní Microsoft Graph API se hodnota `user.read` oboru mapuje na `https://graph.microsoft.com/User.Read` Format a dá se použít zaměnitelné.

> [!NOTE]
> Některá webová rozhraní API, jako je napříkladhttps://management.core.windows.net/) rozhraní api pro Azure Resource Manager (očekávají na konci deklarace identity cílové skupiny (AUD) přístupového tokenu. V takovém případě je důležité předat obor jako https://management.core.windows.net//user_impersonation (poznámku s dvojitým lomítkem), aby byl token platný v rozhraní API.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Požádat o dynamické obory pro přírůstkové vyjádření souhlasu

Při sestavování aplikací pomocí verze 1.0 jste museli zaregistrovat úplnou sadu oprávnění (statické obory), které aplikace požaduje, aby uživatel mohl vyjádřit souhlas v době přihlášení. V 2.0 můžete podle potřeby požádat o další oprávnění pomocí parametru Scope. Nazývají se dynamické obory a umožňují uživateli poskytovat přírůstkový souhlas s obory.

Například můžete uživatele nejprve podepsat a odepřít jim jakýkoliv druh přístupu. Později jim můžete dát možnost číst kalendář uživatele tím, že si vyžádáte rozsah kalendáře v metodách získat token a získáte souhlas uživatele.

Například: `https://graph.microsoft.com/User.Read` a`https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Tiché získávání tokenů (z mezipaměti)

MSAL udržuje mezipaměť tokenů (nebo dvě mezipaměti pro důvěrné klientské aplikace) a po získání token uloží do mezipaměti.  V mnoha případech se při pokusu o tiché získání tokenu získá další token s více rozsahy na základě tokenu v mezipaměti. Je také možné aktualizovat token, když se blíží vypršení platnosti (protože mezipaměť tokenů obsahuje také obnovovací token).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Doporučený vzor volání pro veřejné klientské aplikace

Kód aplikace by se měl nejdříve pokusit získat token v tichém (z mezipaměti).  Pokud volání metody vrátí chybu "požadováno UI" nebo výjimku, zkuste získat token jiným způsobem.

Existují však dva toky před tím, **než se pokusíte o** tiché získání tokenu:

- [tok přihlašovacích údajů klienta](msal-authentication-flows.md#client-credentials), který nepoužívá mezipaměť tokenu uživatele, ale mezipaměť tokenu aplikace. Tato metoda postará o ověření této mezipaměti tokenů aplikace před odesláním žádosti službě STS.
- [tok autorizačního kódu](msal-authentication-flows.md#authorization-code) ve webových aplikacích, protože uplatňuje kód, který aplikace získala přihlašováním uživatele, a má souhlas s více obory. Vzhledem k tomu, že je kód předán jako parametr, a nikoli účet, metoda nemůže najít mezipaměť v mezipaměti před uplatněním kódu, který vyžaduje, aby bylo volání služby úspěšné.

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
- Může [získat token s uživatelským jménem a heslem](msal-authentication-flows.md#usernamepassword) v klientských aplikacích rozhraní .NET Framework pro stolní počítače, ale nedoporučuje se to. V důvěrných klientských aplikacích nepoužívejte uživatelské jméno a heslo.
- Může získat token prostřednictvím [toku kódu zařízení](msal-authentication-flows.md#device-code) v aplikacích spuštěných na zařízeních, která nemají webový prohlížeč. Uživatel se poskytuje s adresou URL a kódem, který potom přejde do webového prohlížeče na jiném zařízení a vloží kód a přihlásí se.  Azure AD pak pošle token zpátky do zařízení bez prohlížeče.

### <a name="confidential-client-applications"></a>Důvěrné klientské aplikace

Pro důvěrné klientské aplikace (webová aplikace, webové rozhraní API nebo aplikace démona, jako je například služba systému Windows):
- Získejte tokeny **pro samotnou aplikaci** , nikoli pro uživatele, pomocí [toku přihlašovacích údajů klienta](msal-authentication-flows.md#client-credentials). Dá se použít k synchronizaci nástrojů nebo k nástrojům, které obecně zpracovávají uživatele a nikoli konkrétního uživatele.
- K volání rozhraní API jménem uživatele používejte tok spouštěný [jménem](msal-authentication-flows.md#on-behalf-of) webového rozhraní API. Aplikace je identifikována s přihlašovacími údaji klienta, aby získala token na základě kontrolního výrazu uživatele (například SAML nebo token JWT). Tento tok se používá aplikacemi, které potřebují přístup k prostředkům určitého uživatele v voláních služby-služba.
- Získejte tokeny pomocí [toku autorizačního kódu](msal-authentication-flows.md#authorization-code) ve webových aplikacích poté, co se uživatel přihlásí prostřednictvím adresy URL žádosti o autorizaci. Aplikace OpenID Connect obvykle používá tento mechanismus, který umožňuje uživatelům přihlašovat se pomocí otevřeného ID připojit a pak přistupovat k webovým rozhraním API jménem uživatele.

## <a name="authentication-results"></a>Výsledky ověřování

Když si klient vyžádá přístupový token, Azure AD také vrátí výsledek ověření, který obsahuje metadata o přístupovém tokenu. Tyto informace zahrnují čas vypršení platnosti přístupového tokenu a rozsahy, pro které je platná. Tato data umožňují vaší aplikaci inteligentní ukládání přístupových tokenů do mezipaměti bez nutnosti analyzovat samotný přístupový token.  Výsledek ověřování vystavuje:

- [Přístupový token](access-tokens.md) webového rozhraní API pro přístup k prostředkům Toto je řetězec, obvykle token JWT kódovaný ve formátu base64, ale klient by nikdy neměl být v přístupovém tokenu. Formát není zaručený, aby zůstal stabilní a mohl by být zašifrovaný pro daný prostředek. Lidé, kteří vytvářejí kód v závislosti na obsahu přístupového tokenu na klientovi, je jedním z největších zdrojů chyb a konců klientských logik.
- [Token ID](id-tokens.md) pro uživatele (Toto je token JWT).
- Vypršení platnosti tokenu, které oznamuje datum a čas, kdy vyprší platnost tokenu.
- ID tenanta obsahuje tenanta, ve kterém se uživatel našel. Pro uživatele typu Host (scénáře Azure AD B2B) je ID tenanta tenant hosta, nikoli jedinečný tenant. Pokud je token dodán jménem uživatele, výsledek ověřování obsahuje také informace o tomto uživateli. U důvěrných toků klienta, kde jsou požadovány tokeny bez uživatele (pro aplikaci), jsou informace o uživateli null.
- Obory, pro které byl token vydán.
- Jedinečné ID uživatele

## <a name="next-steps"></a>Další kroky

Pokud používáte MSAL for Java, přečtěte si informace o [serializaci mezipaměti vlastního tokenu v MSAL pro Java](msal-java-token-cache-serialization.md).

Další informace o [zpracování chyb a výjimek](msal-handling-exceptions.md).

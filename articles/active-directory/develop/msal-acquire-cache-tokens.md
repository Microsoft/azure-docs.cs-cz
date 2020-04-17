---
title: Získat tokeny mezipaměti & pomocí msal | Azure
titleSuffix: Microsoft identity platform
description: Další informace o získávání a ukládání tokenů do mezipaměti pomocí knihovny Microsoft Authentication Library (MSAL).
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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534580"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Získání a mezipaměti tokenů pomocí ověřovací knihovny Microsoft (MSAL)

[Přístupové tokeny](access-tokens.md) umožňují klientům bezpečně volat webová api chráněná Azure. Existuje mnoho způsobů, jak získat token pomocí Knihovny ověřování Microsoft (MSAL). Některé způsoby vyžadují interakci uživatele prostřednictvím webového prohlížeče. Některé nevyžadují žádné interakce s uživatelem. Obecně platí, že způsob získání tokenu závisí na tom, zda je aplikace veřejná klientská aplikace (desktopová nebo mobilní aplikace) nebo důvěrná klientská aplikace (webová aplikace, webové rozhraní API nebo aplikace pro daemon, jako je služba Windows).

MSAL ukládá token po jeho získání.  Kód aplikace by se měl pokusit získat token tiše (z mezipaměti), nejprve před získáním tokenu jinými prostředky.

Můžete také vymazat mezipaměť tokenů, které je dosaženo odebráním účtů z mezipaměti. Tím se však neodstraní soubor cookie relace, který je v prohlížeči.

## <a name="scopes-when-acquiring-tokens"></a>Obory při získávání tokenů

[Obory](v2-permissions-and-consent.md) jsou oprávnění, která webové rozhraní API zveřejňuje pro klientské aplikace, ke kterým mohou požádat o přístup. Klientské aplikace požadují souhlas uživatele pro tyto obory při vytváření požadavků na ověření získat tokeny pro přístup k webovým rozhraním API. MSAL umožňuje získat tokeny pro přístup k Azure AD pro vývojáře (v1.0) a Microsoft identity platformy (v2.0) rozhraní API. V2.0 protokol používá obory namísto prostředku v požadavcích. Další informace naleznete [v porovnání v1.0 a v2.0](active-directory-v2-compare.md). Na základě konfigurace webového rozhraní API verze tokenu, kterou přijímá, koncový bod v2.0 vrátí přístupový token do služby MSAL.

Počet metod získání tokenu MSAL vyžaduje parametr *oboru.* Tento parametr je jednoduchý seznam řetězců, které deklarují požadovaná oprávnění a prostředky, které jsou požadovány. Dobře známé obory jsou [oprávnění aplikace Microsoft Graph](/graph/permissions-reference).

Je také možné v MSAL pro přístup k prostředkům v1.0. Další informace naleznete [v scopes pro aplikaci v1.0](msal-v1-app-scopes.md).

### <a name="request-specific-scopes-for-a-web-api"></a>Vyžádání konkrétních oborů pro webové rozhraní API

Když vaše aplikace potřebuje požadovat tokeny s konkrétními oprávněními pro rozhraní API prostředků, budete muset předat obory obsahující identifikátor URI rozhraní API id aplikace v níže uvedeném formátu: * &lt;obor URI identifikátorů&gt;/&lt;ID aplikace&gt;*

Obory pro rozhraní Microsoft Graph API:`https://graph.microsoft.com/User.Read`

Nebo například obory pro vlastní webové rozhraní API:`api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

Pro rozhraní API Microsoft Graph pouze `user.read` obor `https://graph.microsoft.com/User.Read` hodnota mapuje formátování a lze použít zaměnitelně.

> [!NOTE]
> Některá webová rozhraní API,https://management.core.windows.net/) jako je například rozhraní API Azure Resource Manager ( očekávejte koncové '/' v deklaraci cílové skupiny (aud) přístupového tokenu. V tomto případě je důležité předat https://management.core.windows.net//user_impersonation obor jako (všimněte si dvojité lomítko), pro token platný v rozhraní API.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Požadovat dynamické obory pro přírůstkový souhlas

Při vytváření aplikací pomocí v1.0 jste museli zaregistrovat úplnou sadu oprávnění (statické obory) vyžadované aplikací pro uživatele k souhlasu v době přihlášení. Ve v2.0 můžete požádat o další oprávnění podle potřeby pomocí parametru oboru. Tyto obory se nazývají dynamické obory a umožňují uživateli poskytnout přírůstkový souhlas s obory.

Můžete například zpočátku přihlásit uživatele a odepřít mu jakýkoli druh přístupu. Později jim můžete dát možnost číst kalendář uživatele vyžádáním oboru kalendáře v metodách získání tokenu a získat souhlas uživatele.

Například: `https://graph.microsoft.com/User.Read` a`https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Získávání tokenů tiše (z mezipaměti)

MSAL udržuje mezipaměť tokenů (nebo dvě mezipaměti pro důvěrné klientské aplikace) a ukládá token po jeho získání.  V mnoha případech pokus o tiše získat token získá jiný token s více oborů na základě tokenu v mezipaměti. Je také schopen aktualizovat token, když se blíží vypršení platnosti (jako mezipaměť tokenu také obsahuje token aktualizace).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Doporučený způsob volání pro veřejné klientské aplikace

Kód aplikace by se měl pokusit získat token tiše (z mezipaměti), první.  Pokud volání metody vrátí chybu nebo výjimku "Je vyžadováno uj., zkuste získat token jinými prostředky.

Existují však dva toky, před kterými byste se **neměli** pokoušet tiše získat token:

- [tok pověření klienta](msal-authentication-flows.md#client-credentials), který nepoužívá mezipaměť tokenů uživatele, ale mezipaměti tokenů aplikace. Tato metoda se stará o ověření mezipaměti tokenu této aplikace před odesláním požadavku na SLUŽBY STS.
- [tok autorizačního kódu](msal-authentication-flows.md#authorization-code) ve webových aplikacích, protože uplatní kód, který aplikace získala přihlášením uživatele a jejich souhlasem s více obory. Vzhledem k tomu, že kód je předán jako parametr, a nikoli účet, metoda nemůže hledat v mezipaměti před uplatněním kódu, který vyžaduje, stejně, volání služby.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Doporučený způsob volání ve webových aplikacích pomocí toku autorizačního kódu

Pro webové aplikace, které používají [tok autorizačního kódu OpenID Connect](v2-protocols-oidc.md), je doporučeným vzorem v řadičích:

- Vytvořte instanci důvěrné klientské aplikace s mezipamětí tokenů s přizpůsobenou serializací.
- Získání tokenu pomocí toku autorizačního kódu

## <a name="acquiring-tokens"></a>Získání tokenů

Obecně platí, že způsob získání tokenu závisí na tom, zda se jedná o veřejnou klientskou nebo důvěrnou klientskou aplikaci.

### <a name="public-client-applications"></a>Veřejné klientské aplikace

U veřejných klientských aplikací (desktopových nebo mobilních aplikací):
- Často získat tokeny interaktivně, s uživatelem přihlásit prostřednictvím uživatelského rozhraní nebo automaticky otevírané okno.
- Můžete [získat token tiše pro přihlášený uživatel](msal-authentication-flows.md#integrated-windows-authentication) pomocí integrovaného ověřování systému Windows (IWA/Kerberos), pokud desktopová aplikace běží na počítači se systémem Windows připojen k doméně nebo k Azure.
- Může [získat token s uživatelským jménem a heslem](msal-authentication-flows.md#usernamepassword) v klientských aplikacích desktopové klienty rozhraní .NET Framework, ale to se nedoporučuje. Nepoužívejte uživatelské jméno/heslo v důvěrných klientských aplikacích.
- Může získat token prostřednictvím [toku kódu zařízení](msal-authentication-flows.md#device-code) v aplikacích spuštěných na zařízeních, která nemají webový prohlížeč. Uživateli je poskytnuta adresa URL a kód, který poté přejde do webového prohlížeče na jiném zařízení a zadá kód a přihlásí se.  Azure AD pak odešle token zpět do zařízení bez prohlížeče.

### <a name="confidential-client-applications"></a>Důvěrné klientské aplikace

U důvěrných klientských aplikací (webová aplikace, webové rozhraní API nebo daemon aplikace, jako je služba Windows), můžete:
- Získat tokeny **pro samotnou aplikaci** a ne pro uživatele pomocí [toku pověření klienta](msal-authentication-flows.md#client-credentials). To lze použít pro synchronizaci nástrojů nebo nástroje, které zpracovávají uživatele obecně a není konkrétní uživatel.
- Pomocí [toku on-behalf-of](msal-authentication-flows.md#on-behalf-of) pro webové rozhraní API volat rozhraní API jménem uživatele. Aplikace je identifikována s pověřeními klienta za účelem získání tokenu na základě kontrolního výrazu uživatele (SAML například nebo tokenu JWT). Tento tok je používán aplikacemi, které potřebují přístup k prostředkům konkrétního uživatele v volání služby služby.
- Získejte tokeny pomocí [toku autorizačního kódu](msal-authentication-flows.md#authorization-code) ve webových aplikacích poté, co se uživatel přihlásí prostřednictvím adresy URL žádosti o autorizaci. Aplikace OpenID Connect obvykle používá tento mechanismus, který umožňuje uživateli přihlásit se pomocí open ID connect a pak přistupovat k webovým rozhraním API jménem uživatele.

## <a name="authentication-results"></a>Výsledky ověřování

Když váš klient požaduje přístupový token, Azure AD také vrátí výsledek ověřování, který obsahuje některá metadata o přístupový token. Tyto informace zahrnují čas vypršení platnosti přístupového tokenu a obory, pro které je platný. Tato data umožňují vaší aplikaci provést inteligentní ukládání přístupových tokenů do mezipaměti, aniž by bylo třeba analyzovat samotný přístupový token.  Výsledek ověřování zveřejňuje:

- [Přístupový token](access-tokens.md) pro webové rozhraní API pro přístup k prostředkům. Toto je řetězec, obvykle base64 kódované JWT, ale klient by nikdy neměl hledat uvnitř přístupového tokenu. Formát není zaručeno, že zůstane stabilní a může být šifrována pro prostředek. Lidé, kteří píší kód v závislosti na obsahu přístupového tokenu v klientovi, jsou jedním z největších zdrojů chyb a přestávek logiky klienta.
- [ID token](id-tokens.md) pro uživatele (toto je JWT).
- Vypršení platnosti tokenu, který říká datum a čas, kdy vyprší platnost tokenu.
- ID klienta obsahuje klienta, ve kterém byl nalezen uživatel. Pro uživatele typu Host (scénáře Azure AD B2B) je ID klienta klienta, nikoli jedinečným tenantem. Při doručení tokenu jménem uživatele obsahuje výsledek ověřování také informace o tomto uživateli. Pro důvěrné toky klientů, kde jsou požadovány tokeny bez uživatele (pro aplikaci), je tato informace o uživateli null.
- Obory, pro které byl vydán token.
- Jedinečné ID pro uživatele.

## <a name="next-steps"></a>Další kroky

Pokud používáte MSAL pro Jazyk Java, přečtěte si informace o [vlastní serializaci mezipaměti tokenů v MSAL pro Javu](msal-java-token-cache-serialization.md).

Informace o [zpracování chyb a výjimek](msal-handling-exceptions.md).

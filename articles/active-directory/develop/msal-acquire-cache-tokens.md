---
title: Spravovat tokeny (knihovna Microsoft Authentication Library) | Azure
description: Další informace o získávání a ukládání do mezipaměti tokeny pomocí knihovny Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e22f6b8b0ff6874004373eb4a292ad907db2418
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080944"
---
# <a name="acquiring-and-caching-tokens-using-msal"></a>Získávání a ukládání do mezipaměti tokenů s využitím MSAL
[Přístupové tokeny](access-tokens.md) umožňují klientům bezpečně volat webové rozhraní API chráněné službou Azure. Existuje mnoho způsobů, jak získat token pomocí Microsoft Authentication Library (MSAL). Některé způsoby, vyžadují interakcí s uživateli prostřednictvím webového prohlížeče. Některé nevyžaduje žádné akce uživatele. Obecně platí je způsob, jak získat token závisí, pokud je aplikace veřejným klientem (desktopové nebo mobilní aplikace) nebo důvěrnému klientovi aplikace (aplikace webovou aplikaci, webové rozhraní API nebo proces démon jako služba Windows).

Knihovna MSAL ukládá do mezipaměti token po byly získány.  Kód aplikace by měl pokusit získat token tiše (z mezipaměti), nejprve před jinými prostředky získávání tokenu.

Můžete také vymazat mezipaměť tokenu, kterého je dosaženo odstraněním účty z mezipaměti. Soubor cookie relace, která je v prohlížeči, i když nebude odstraněn.

## <a name="scopes-when-acquiring-tokens"></a>Obory při získávání tokenů
[Obory](v2-permissions-and-consent.md) jsou oprávnění, která poskytuje webové rozhraní API pro klientské aplikace požádat o přístup k. Klientské aplikace požádat o souhlas uživatele pro tyto obory při vytváření žádosti o ověření k získání tokenů pro přístup k webové rozhraní API. MSAL umožňuje získat tokeny pro přístup ke službě Azure AD pro vývojáře (verze 1.0) a platforma identit Microsoft (verze 2.0) rozhraní API. protokol v2.0 používá obory místo prostředek v žádosti. Další informace najdete v článku [porovnání v1.0 a v2.0](active-directory-v2-compare.md). Na základě konfigurace webového rozhraní API, která přijímá token verze koncovým bodem v2.0 vrátí přístupový token MSAL.

Počet MSAL získat token metody vyžadují *obory* parametru. Tento parametr je jednoduchý seznam řetězců, které deklarují požadovaná oprávnění a prostředky, které jsou požadovány. Jsou dobře známé obory [oprávnění Microsoft Graphu](/graph/permissions-reference).

Je také MSAL umožňuje přístup k prostředkům v1.0. Další informace najdete v článku [oborů pro aplikaci v1.0](msal-v1-app-scopes.md).

### <a name="request-specific-scopes-for-a-web-api"></a>Požádat o konkrétních oborů pro webové rozhraní API
Pokud vaše aplikace potřebuje požádat o tokeny se specifickými oprávněními prostředku rozhraní API, je potřeba předat obory obsahující aplikaci identifikátor URI ID rozhraní API v následujícím formátu:  *&lt;identifikátor ID URI aplikace&gt; / &lt;oboru&gt;*

Například obory pro rozhraní Microsoft Graph API: `https://graph.microsoft.com/User.Read`

Nebo třeba oborů pro webové rozhraní API: `api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

Pro rozhraní Microsoft Graph API, pouze hodnota rozsahu `user.read` mapuje `https://graph.microsoft.com/User.Read` formátování a můžou používat Zaměnitelně.

> [!NOTE]
> Některá webová rozhraní API, jako je například rozhraní API Azure Resource Manageru (https://management.core.windows.net/) očekávat koncový znak "/" v cílové skupině deklarací identity (aud) přístupového tokenu. V takovém případě je potřeba předat oboru jako https://management.core.windows.net//user_impersonation (Poznámka: dvojité lomítka) pro daný token platný v rozhraní API.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Žádost o dynamické obory pro přírůstkové souhlas
Při sestavování aplikací s použitím verze 1.0, jste museli registrovat úplnou sadu oprávnění (obory statické) vyžadovaného aplikací pro uživatele souhlas v době přihlášení. V verze 2.0 můžete požádat o další oprávnění podle potřeby pomocí parametru oboru. Ty se nazývají dynamické obory a umožní uživateli zadat přírůstkové vyjadřujete souhlas s obory.

Můžete například nejprve přihlásit uživatele a odepřít je jiný druh přístupu. Později můžete přiřadit schopnost číst kalendář uživatele, můžete si vyžádat oboru kalendář pro získání tokenu metody a vyžadovat souhlas uživatele.

Příklad: `https://graph.microsoft.com/User.Read` a `https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Získávání tokenů tiše (z mezipaměti.)
Knihovna MSAL udržuje mezipaměť tokenu (nebo dva mezipaměti pro aplikace důvěrnému klientovi) a ukládá do mezipaměti token po byl získán.  V mnoha případech zkusí tiše získat token se získat další token s více obory na základě tokenu v mezipaměti. Je také schopné aktualizuje token, pokud je to stále blízko vypršení platnosti (jako mezipaměť tokenu obsahuje také obnovovací token).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Doporučené volání vzor pro veřejné klientské aplikace
Kód aplikace by měl pokusit získat token tiše (z mezipaměti), nejprve.  Pokud volání metody vrací "Required uživatelského rozhraní" chybu nebo výjimku, zkuste získávání tokenu jiným způsobem. 

Nicméně existují dva toky před kterou je **by neměla** pokusí tiše získat token:

- [tok pověření klienta](msal-authentication-flows.md#confidential-client), který nepoužívá mezipaměť tokenu uživatele, ale token mezipaměti aplikace. Tato metoda se postará o ověření tuto mezipaměť tokenu aplikace před odesláním požadavku na službu STS.
- [tok autorizačního kódu](msal-authentication-flows.md#authorization-code) ve službě Web Apps, protože uplatňuje kód, který je teď aplikace tak, že uživatel přihlášení a s nimi souhlas pro další obory. Vzhledem k tomu, že kód je předán jako parametr, nikoli účet, metodu nejde Hledat v mezipaměti před uplatňuje kód, který vyžaduje, i přesto, volání služby.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Doporučeného vzoru volání ve službě Web Apps pomocí tok autorizačního kódu 
Pro webové aplikace, které používají [OpenID Connect tok autorizačního kódu](v2-protocols-oidc.md), je doporučený model v řadiče:

- Vytvoření instance důvěrné klientské aplikace s mezipamětí tokenů s vlastní serializace. 
- Získat token použije tok autorizačního kódu

## <a name="acquiring-tokens"></a>Získávání tokenů
Obecně platí způsob získávání tokenu závisí na to, zda je veřejným klientem nebo důvěrné klientské aplikace.

### <a name="public-client-applications"></a>Veřejné klientské aplikace
Pro aplikace veřejným klientem (desktopové nebo mobilní aplikace) můžete:
- Často získejte tokeny interaktivně, uživatele, přihlaste se pomocí uživatelského rozhraní nebo automaticky otevírané okno.
- Můžete [tiše získání tokenu pro přihlášeného uživatele](msal-authentication-flows.md#integrated-windows-authentication) pomocí integrovaného ověřování Windows (IWA/Kerberos), pokud desktopové aplikace běží na počítači Windows připojené k doméně nebo do Azure.
- Můžete [získat token se uživatelské jméno a heslo](msal-authentication-flows.md#usernamepassword) v rozhraní .NET framework klasické pracovní plochy klienta aplikace, ale to nedoporučujeme. Nepoužívejte uživatelského jména a hesla v důvěrné klientské aplikace.
- Můžete získat token prostřednictvím [toku kódu zařízení](msal-authentication-flows.md#device-code) v aplikacích spuštěných na zařízeních, která nemají webový prohlížeč. Uživatel je součástí adresy URL a kód, který pak přejde do webového prohlížeče na jiném zařízení a vloží kód a přihlásí se.  Azure AD pak odešle token zpět do zařízení bez určeného prohlížeče.

### <a name="confidential-client-applications"></a>Důvěrné klientské aplikace 
Pro důvěrné klientské aplikace (webovou aplikaci, webové rozhraní API nebo proces démon aplikací jako služba Windows) můžete:
- Získat tokeny **vlastní aplikace** a není pro uživatele, pomocí [údajů klienta, které tok](msal-authentication-flows.md#confidential-client). To je možné pro synchronizaci nástroje nebo nástroje, které zpracovávají uživatelé obecně a konkrétního uživatele. 
- Použití [tok On-behalf-of](msal-authentication-flows.md#on-behalf-of) webového rozhraní API pro volání rozhraní API jménem uživatele. Aplikace je identifikován pomocí přihlašovacích údajů klienta k získání tokenu podle uživatele kontrolního výrazu (SAML například nebo JWT token). Tento tok používá aplikace, které potřebují přístup k prostředkům ve volání služba služba konkrétního uživatele.
- Získat tokeny pomocí [tok autorizačního kódu](msal-authentication-flows.md#authorization-code) ve službě web apps, jakmile se uživatel přihlásí pomocí autorizace adresa URL požadavku. Aplikace s OpenID Connect se obvykle používají tento mechanismus, který umožňuje uživateli přihlášení pomocí Open ID connect a pak přístup k webovým rozhraním API jménem uživatele.


## <a name="authentication-results"></a>Výsledky ověřování 
Když klient požádá o přístupový token, Azure AD také vrátí výsledek ověřování, který obsahuje některá metadata o přístupový token. Tyto informace zahrnují čas vypršení platnosti přístupového tokenu a oborů, pro které je platný. Tato data umožňuje aplikaci, provedete inteligentního ukládání přístupových tokenů bez nutnosti parsovat přístupový token, samotný.  Výsledek ověřování poskytuje:

- [Přístupový token](access-tokens.md) pro webové rozhraní API pro přístup k prostředkům. Toto je řetězec, obvykle kódováním Base 64 tokenů JWT ale klient by měl vypadat nikdy uvnitř tokenu přístupu. Není zaručeno, že formát zůstat a může být šifrována pro prostředek. Uživatelé psaní kódu v závislosti na token přístup k obsahu na straně klienta je jedním z největších zdroje chyb a zalomení logiky klienta.
- [ID token](id-tokens.md) pro uživatele (to je token JWT).
- Konec platnosti tokenu, který informuje datum a čas, kdy vyprší platnost tokenu.
- ID tenanta obsahuje tenanta, ve kterém byl uživatel nalezen. Pro uživatele typu Host (scénáře B2B ve službě Azure AD) je ID tenanta tenanta hosta, není jedinečný tenanta. Když token se doručí názvu uživatele, výsledek ověřování také obsahuje informace o uživateli. Pro toky důvěrnému klientovi, kde jsou požadovány tokeny bez uživatele (aplikace) tyto informace o uživatelích má hodnotu null.
- Obory, pro které byl token vydán.
- Jedinečné ID pro uživatele.

## <a name="next-steps"></a>Další postup
Další informace o [zpracování chyb a výjimek](msal-handling-exceptions.md). 

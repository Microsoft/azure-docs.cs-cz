---
title: Aplikace webového rozhraní API ve službě Azure Active Directory
description: Popisuje, co jsou aplikace webového rozhraní API a základy toku protokolu, registrace a vypršení platnosti tokenu pro tento typ aplikace.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 9cf5a9c81ca1d7a42a5a8e342dee55f335656c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154419"
---
# <a name="web-api"></a>Web API

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Webové aplikace rozhraní API jsou webové aplikace, které potřebují získat prostředky z webového rozhraní API. V tomto scénáři existují dva typy identit, které webová aplikace můžete použít k ověření a volání webové rozhraní API:

- **Identita aplikace** – tento scénář používá oAuth 2.0 pověření klienta udělit k ověření jako aplikace a přístup k webové rozhraní API. Při použití identity aplikace webové rozhraní API můžete pouze zjistit, že webová aplikace je volání, jako webové rozhraní API neobdrží žádné informace o uživateli. Pokud aplikace obdrží informace o uživateli, bude odeslána prostřednictvím protokolu aplikace a není podepsána službou Azure AD. Webové rozhraní API důvěřuje, že webová aplikace ověřila uživatele. Z tohoto důvodu se tento vzor nazývá důvěryhodný subsystém.
- **Delegovaná identita uživatele** – Tento scénář lze provést dvěma způsoby: OpenID Connect a OAuth 2.0 autorizační kód udělit s důvěrným klientem. Webová aplikace získá přístupový token pro uživatele, který pro webové rozhraní API prokáže, že uživatel byl úspěšně ověřen pro webovou aplikaci a že webová aplikace byla schopna získat identitu delegovaného uživatele pro volání webového rozhraní API. Tento přístupový token je odeslán v požadavku na webové rozhraní API, které autorizuje uživatele a vrátí požadovaný prostředek.

Identita aplikace a delegované typy identit uživatelů jsou popsány v toku níže. Hlavní rozdíl mezi nimi je, že delegovaná identita uživatele musí nejprve získat autorizační kód, než se uživatel může přihlásit a získat přístup k webovému rozhraní API.

## <a name="diagram"></a>Diagram

![Diagram webové aplikace pro webové rozhraní API](./media/authentication-scenarios/web-app-to-web-api.png)

## <a name="protocol-flow"></a>Tok protokolu

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identita aplikace s grantem klientských pověření OAuth 2.0

1. Uživatel je přihlášen k Azure AD ve webové aplikaci (další informace najdete v části **Webové aplikace).**
1. Webová aplikace potřebuje získat přístupový token, aby mohla ověřit webové rozhraní API a načíst požadovaný prostředek. Provede požadavek na koncový bod tokenu služby Azure AD, který poskytuje pověření, ID aplikace a identifikátor URI aplikace webového rozhraní API.
1. Azure AD ověřuje aplikaci a vrátí přístupový token JWT, který se používá k volání webového rozhraní API.
1. Přes protokol HTTPS používá webová aplikace vrácený přístupový token JWT k přidání řetězce JWT s označením "Nosič" v hlavičce autorizace požadavku do webového rozhraní API. Webové rozhraní API pak ověří token JWT a pokud je ověření úspěšné, vrátí požadovaný prostředek.

### <a name="delegated-user-identity-with-openid-connect"></a>Delegovaná identita uživatele s OpenID Connect

1. Uživatel je přihlášen k webové aplikaci pomocí Azure AD (viz část Webový prohlížeč do webové aplikace výše). Pokud uživatel webové aplikace ještě nesouhlasil s povolením webové aplikace volat webové rozhraní API jeho jménem, bude muset souhlasit. Aplikace zobrazí oprávnění, která vyžaduje, a pokud se jedná o oprávnění na úrovni správce, normální uživatel v adresáři nebude moci souhlasit. Tento proces souhlasu se vztahuje pouze na víceklientské aplikace, nikoli na aplikace s jedním tenantem, protože aplikace již bude mít potřebná oprávnění. Když se uživatel přihlásí, webová aplikace obdržela token ID s informacemi o uživateli a také autorizačníkód.
1. Pomocí autorizačního kódu vydaného službou Azure AD odešle webová aplikace požadavek na koncový bod tokenu služby Azure AD, který obsahuje autorizační kód, podrobnosti o klientské aplikaci (ID aplikace a identifikátor URI přesměrování) a požadovaný prostředek (ID aplikace URI pro webové rozhraní API).
1. Autorizační kód a informace o webové aplikaci a webové rozhraní API jsou ověřeny službou Azure AD. Po úspěšném ověření Azure AD vrátí dva tokeny: přístupový token JWT a token aktualizace JWT.
1. Přes protokol HTTPS používá webová aplikace vrácený přístupový token JWT k přidání řetězce JWT s označením "Nosič" v hlavičce autorizace požadavku do webového rozhraní API. Webové rozhraní API pak ověří token JWT a pokud je ověření úspěšné, vrátí požadovaný prostředek.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Delegovaná identita uživatele s udělením autorizačního kódu OAuth 2.0

1. Uživatel je již přihlášen k webové aplikaci, jejíž mechanismus ověřování je nezávislý na Azure AD.
1. Webová aplikace vyžaduje autorizační kód k získání přístupového tokenu, takže vydá požadavek prostřednictvím prohlížeče na koncový bod autorizace služby Azure AD, který poskytuje ID aplikace a přesměrovává identifikátor URI pro webovou aplikaci po úspěšném ověření. Uživatel se přihlásí k Azure AD.
1. Pokud uživatel webové aplikace ještě nesouhlasil s povolením webové aplikace volat webové rozhraní API jeho jménem, bude muset souhlasit. Aplikace zobrazí oprávnění, která vyžaduje, a pokud se jedná o oprávnění na úrovni správce, normální uživatel v adresáři nebude moci souhlasit. Tento souhlas se vztahuje na jednoklientské i víceklientské aplikace. V případě jednoho klienta může správce provést souhlas správce se souhlasem jménem svých uživatelů. To lze provést `Grant Permissions` pomocí tlačítka na [webu Azure Portal](https://portal.azure.com). 
1. Poté, co uživatel souhlasil, webová aplikace obdrží autorizační kód, který potřebuje k získání přístupového tokenu.
1. Pomocí autorizačního kódu vydaného službou Azure AD odešle webová aplikace požadavek na koncový bod tokenu služby Azure AD, který obsahuje autorizační kód, podrobnosti o klientské aplikaci (ID aplikace a identifikátor URI přesměrování) a požadovaný prostředek (ID aplikace URI pro webové rozhraní API).
1. Autorizační kód a informace o webové aplikaci a webové rozhraní API jsou ověřeny službou Azure AD. Po úspěšném ověření Azure AD vrátí dva tokeny: přístupový token JWT a token aktualizace JWT.
1. Přes protokol HTTPS používá webová aplikace vrácený přístupový token JWT k přidání řetězce JWT s označením "Nosič" v hlavičce autorizace požadavku do webového rozhraní API. Webové rozhraní API pak ověří token JWT a pokud je ověření úspěšné, vrátí požadovaný prostředek.

## <a name="code-samples"></a>Ukázky kódů

Podívejte se na ukázky kódu pro scénáře webové aplikace na webové rozhraní API. A často kontrolujte – často se přidávají nové vzorky. Webová [aplikace do webového rozhraní API](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Registrace aplikací

Pokud chcete zaregistrovat aplikaci s koncovým bodem Azure AD v1.0, [přečtěte si část Registrace aplikace](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Jeden tenant – pro identitu aplikace a případy identity delegovaného uživatele webové aplikace a webové rozhraní API musí být registrovány ve stejném adresáři ve službě Azure AD. Webové rozhraní API lze nakonfigurovat tak, aby zpřístupniovalo sadu oprávnění, která se používají k omezení přístupu webové aplikace k jejím prostředkům. Pokud se používá typ identity delegovaného uživatele, musí webová aplikace vybrat požadovaná oprávnění z rozevírací nabídky **Oprávnění k jiným aplikacím** na webu Azure Portal. Tento krok není vyžadován, pokud se používá typ identity aplikace.
* Víceklient – nejprve je webová aplikace nakonfigurována tak, aby označovala oprávnění, která vyžaduje, aby byla funkční. Tento seznam požadovaných oprávnění je zobrazen v dialogovém okně, když uživatel nebo správce v cílovém adresáři udělí souhlas s aplikací, která ji zpřístupní jejich organizaci. Některé aplikace vyžadují pouze oprávnění na úrovni uživatele, se kterými může souhlasit každý uživatel v organizaci. Jiné aplikace vyžadují oprávnění na úrovni správce, s nimiž uživatel v organizaci nemůže souhlasit. Souhlas s aplikacemi, které vyžadují tuto úroveň oprávnění, může udělit souhlas pouze správce adresáře. Když uživatel nebo správce souhlasí, webové aplikace a webové rozhraní API jsou registrovány v jejich adresáři.

## <a name="token-expiration"></a>Vypršení platnosti tokenu

Když webová aplikace používá svůj autorizační kód k získání přístupového tokenu JWT, obdrží také obnovovací token JWT. Po vypršení platnosti přístupového tokenu lze obnovovací token použít k opětovnému ověření uživatele, aniž by bylo nutné, aby se znovu přihlásili. Tento obnovovací token se pak používá k ověření uživatele, což má za následek nový přístupový token a obnovovací token.

## <a name="next-steps"></a>Další kroky

- Další informace o dalších [typech aplikací a scénářích](app-types.md)
- Informace o [základech ověřování](v1-authentication-scenarios.md) Azure AD

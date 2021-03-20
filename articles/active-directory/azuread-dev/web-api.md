---
title: Webové API aplikace v Azure Active Directory
description: Popisuje, jaké aplikace webového rozhraní API jsou a základní informace o toku, registraci a vypršení platnosti tokenu pro tento typ aplikace.
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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154419"
---
# <a name="web-api"></a>Webové rozhraní API

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Webové aplikace API jsou webové aplikace, které potřebují získat prostředky z webového rozhraní API. V tomto scénáři existují dva typy identity, které může webová aplikace použít k ověření a volání webového rozhraní API:

- **Identita aplikace** – tento scénář používá udělení přihlašovacích údajů klienta OAuth 2,0 k ověření jako aplikace a přístup k webovému rozhraní API. Při použití identity aplikace může webové rozhraní API zjistit, že webová aplikace ji volá, protože webové rozhraní API neobdrží žádné informace o uživateli. Pokud aplikace obdrží informace o uživateli, bude zaslána prostřednictvím aplikačního protokolu a není podepsána službou Azure AD. Webové rozhraní API důvěřuje, že webová aplikace ověřila daného uživatele. Z tohoto důvodu se tento model nazývá důvěryhodný podsystém.
- **Identita delegovaného uživatele** – tento scénář je možné provést dvěma způsoby: OpenID Connect a autorizační kód OAuth 2,0 poskytuje důvěrnému klientovi. Webová aplikace získá přístupový token pro uživatele, který prokáže webovému rozhraní API, které uživatel úspěšně ověřil do webové aplikace a že webová aplikace byla schopna získat identitu delegovaného uživatele pro volání webového rozhraní API. Tento přístupový token se pošle v žádosti webovému rozhraní API, které uživatele autorizuje a vrátí požadovaný prostředek.

V toku níže jsou popsány typy identity identity aplikace i delegovaný uživatel. Hlavním rozdílem mezi nimi je, že identita delegovaného uživatele musí nejdřív získat autorizační kód, než se uživatel může přihlásit a získat přístup k webovému rozhraní API.

## <a name="diagram"></a>Diagram

![Diagram webové aplikace do webového rozhraní API](./media/authentication-scenarios/web-app-to-web-api.png)

## <a name="protocol-flow"></a>Tok protokolu

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identita aplikace s udělenými přihlašovacími údaji klienta OAuth 2,0

1. Uživatel je přihlášený k Azure AD ve webové aplikaci (Další informace najdete v části **Web Apps** ).
1. Webová aplikace musí získat přístupový token, aby se mohl ověřit ve webovém rozhraní API a načíst požadovaný prostředek. Vytvoří požadavek na koncový bod tokenu služby Azure AD, který poskytuje přihlašovací údaje, ID aplikace a identifikátor URI ID aplikace webového rozhraní API.
1. Azure AD ověří aplikaci a vrátí přístupový token JWT, který se používá k volání webového rozhraní API.
1. Přes protokol HTTPS používá webová aplikace vrácený přístupový token JWT k přidání řetězce JWT s označením "nosiče" v autorizační hlavičce požadavku do webového rozhraní API. Webové rozhraní API potom ověří token JWT a pokud je ověření úspěšné, vrátí požadovaný prostředek.

### <a name="delegated-user-identity-with-openid-connect"></a>Identita delegovaného uživatele pomocí OpenID Connect

1. Uživatel je přihlášený k webové aplikaci pomocí Azure AD (viz část webový prohlížeč do webové aplikace výše). Pokud uživatel webové aplikace dosud nesouhlasí s tím, že webové aplikaci zavolá webové rozhraní API jménem, bude uživatel muset souhlasit. Aplikace zobrazí požadovaná oprávnění, a pokud má kterákoli z nich oprávnění na úrovni správce, normální uživatel v adresáři nebude moci vyjádřit souhlas. Tento postup souhlasu se vztahuje jenom na víceklientské aplikace, ne na jednotlivé klientské aplikace, protože aplikace už bude mít potřebná oprávnění. Když se uživatel přihlásí, Webová aplikace obdržela token ID s informacemi o uživateli a autorizačním kódem.
1. Pomocí autorizačního kódu vydaného službou Azure AD pošle webová aplikace požadavek na koncový bod tokenu služby Azure AD, který obsahuje autorizační kód, podrobnosti o klientské aplikaci (ID aplikace a identifikátor URI pro přesměrování) a požadovaný prostředek (identifikátor ID aplikace pro webové rozhraní API).
1. Služba Azure AD ověřuje autorizační kód a informace o webové aplikaci a webovém rozhraní API. Po úspěšném ověření vrátí Azure AD dva tokeny: přístupový token JWT a obnovovací token JWT.
1. Přes protokol HTTPS používá webová aplikace vrácený přístupový token JWT k přidání řetězce JWT s označením "nosiče" v autorizační hlavičce požadavku do webového rozhraní API. Webové rozhraní API potom ověří token JWT a pokud je ověření úspěšné, vrátí požadovaný prostředek.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Identita delegovaného uživatele s udělením autorizačního kódu OAuth 2,0

1. Uživatel je již přihlášen k webové aplikaci, jejíž ověřovací mechanismus je nezávislý na službě Azure AD.
1. Webová aplikace vyžaduje autorizační kód pro získání přístupového tokenu, takže vydá požadavek přes prohlížeč do koncového bodu autorizace služby Azure AD a po úspěšném ověření poskytne ID aplikace a identifikátor URI přesměrování pro webovou aplikaci. Uživatel se přihlásí do služby Azure AD.
1. Pokud uživatel webové aplikace dosud nesouhlasí s tím, že webové aplikaci zavolá webové rozhraní API jménem, bude uživatel muset souhlasit. Aplikace zobrazí požadovaná oprávnění, a pokud má kterákoli z nich oprávnění na úrovni správce, normální uživatel v adresáři nebude moci vyjádřit souhlas. Tento souhlas platí pro jednu i víceklientské aplikace. V případě jednoho tenanta může správce provést souhlas správce k souhlasu jménem svých uživatelů. To lze provést pomocí `Grant Permissions` tlačítka v [Azure Portal](https://portal.azure.com). 
1. Poté, co uživatel souhlasí, Webová aplikace obdrží autorizační kód, který potřebuje k získání přístupového tokenu.
1. Pomocí autorizačního kódu vydaného službou Azure AD pošle webová aplikace požadavek na koncový bod tokenu služby Azure AD, který obsahuje autorizační kód, podrobnosti o klientské aplikaci (ID aplikace a identifikátor URI pro přesměrování) a požadovaný prostředek (identifikátor ID aplikace pro webové rozhraní API).
1. Služba Azure AD ověřuje autorizační kód a informace o webové aplikaci a webovém rozhraní API. Po úspěšném ověření vrátí Azure AD dva tokeny: přístupový token JWT a obnovovací token JWT.
1. Přes protokol HTTPS používá webová aplikace vrácený přístupový token JWT k přidání řetězce JWT s označením "nosiče" v autorizační hlavičce požadavku do webového rozhraní API. Webové rozhraní API potom ověří token JWT a pokud je ověření úspěšné, vrátí požadovaný prostředek.

## <a name="code-samples"></a>Ukázky kódů

Podívejte se na ukázky kódu pro scénáře webové aplikace do webového rozhraní API. A znovu se podívejte na časté – nové ukázky se přidávají často. Webová [aplikace do webového rozhraní API](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Registrace aplikace

Pokud chcete zaregistrovat aplikaci s koncovým bodem Azure AD v 1.0, přečtěte si téma [Registrace aplikace](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Jeden tenant – pro případy identity aplikace i delegovaného uživatele musí být webová aplikace a webové rozhraní API zaregistrované ve stejném adresáři v Azure AD. Webové rozhraní API je možné nakonfigurovat tak, aby vystavilo sadu oprávnění, která slouží k omezení přístupu webových aplikací k jejím prostředkům. Pokud se používá typ identity delegovaný uživatel, musí webová aplikace vybrat požadovaná oprávnění z rozevírací nabídky **oprávnění k ostatním aplikacím** v Azure Portal. Tento krok není nutný, pokud se používá typ identity aplikace.
* Multi-tenant – první webová aplikace je nakonfigurovaná tak, aby označovala oprávnění, která vyžaduje, aby byla funkční. Tento seznam požadovaných oprávnění se zobrazí v dialogovém okně, když uživatel nebo správce v cílovém adresáři udělí souhlas s aplikací a zpřístupní ji pro jejich organizaci. Některé aplikace vyžadují oprávnění na úrovni uživatele, ke kterým může udělit každý uživatel v organizaci souhlas. Jiné aplikace vyžadují oprávnění na úrovni správce, kterým uživatel v organizaci nemůže udělit souhlas. Pouze správce adresáře může udělit souhlas aplikacím, které vyžadují tuto úroveň oprávnění. Když uživatel nebo správce souhlasí, Webová aplikace a webové rozhraní API se registrují ve svém adresáři.

## <a name="token-expiration"></a>Vypršení platnosti tokenu

Pokud webová aplikace používá svůj autorizační kód k získání přístupového tokenu JWT, obdrží také obnovovací token JWT. Po vypršení přístupového tokenu se dá obnovovací token použít k opětovnému ověření uživatele, aniž by se museli znovu přihlašovat. Tento obnovovací token se pak použije k ověření uživatele, což vede k vytvoření nového přístupového tokenu a k aktualizaci tokenu.

## <a name="next-steps"></a>Další kroky

- Další informace o dalších [typech a scénářích aplikací](app-types.md)
- Seznamte se se [základy ověřování](v1-authentication-scenarios.md) Azure AD

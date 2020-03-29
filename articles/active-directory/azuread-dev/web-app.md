---
title: Webové aplikace ve službě Azure Active Directory
description: Popisuje, co jsou webové aplikace a základy toku protokolu, registrace a vypršení platnosti tokenu pro tento typ aplikace.
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
ms.openlocfilehash: e4a7fb72d40f5db65e8e30264e9d68b2727749e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154402"
---
# <a name="web-apps"></a>Webové aplikace

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Webové aplikace jsou aplikace, které ověřují uživatele ve webovém prohlížeči webové aplikace. V tomto scénáři webové aplikace přesměruje prohlížeč uživatele k jejich přihlášení do Služby Azure AD. Azure AD vrátí odpověď přihlášení prostřednictvím prohlížeče uživatele, který obsahuje deklarace identity o uživateli v tokenu zabezpečení. Tento scénář podporuje přihlášení pomocí protokolů OpenID Connect, SAML 2.0 a WS-Federation.

## <a name="diagram"></a>Diagram

![Tok ověřování pro prohlížeč do webové aplikace](./media/authentication-scenarios/web-browser-to-web-api.png)

## <a name="protocol-flow"></a>Tok protokolu

1. Když uživatel navštíví aplikaci a potřebuje se přihlásit, jsou přesměrováni prostřednictvím žádosti o přihlášení do koncového bodu ověřování ve službě Azure AD.
1. Uživatel se přihlásí na přihlašovací stránce.
1. Pokud je ověření úspěšné, Azure AD vytvoří ověřovací token a vrátí odpověď přihlášení na adresu URL odpovědi aplikace, která byla nakonfigurovaná na webu Azure Portal. Pro produkční aplikaci by tato adresa URL odpovědi měla být HTTPS. Vrácený token obsahuje deklarace identity o uživateli a azure ad, které jsou vyžadovány aplikací k ověření tokenu.
1. Aplikace ověří token pomocí veřejného podpisového klíče a informace o vystavitelovi, které jsou k dispozici v dokumentu metadat federace pro Azure AD. Poté, co aplikace ověří token, spustí novou relaci s uživatelem. Tato relace umožňuje uživateli přístup k aplikaci, dokud nevyprší její platnost.

## <a name="code-samples"></a>Ukázky kódů

Podívejte se na ukázky kódu pro webovéprohlížeče na scénáře webových aplikací. A často kontrolujte, jak se často přidávají nové vzorky.

## <a name="app-registration"></a>Registrace aplikací

Pokud chcete zaregistrovat webovou aplikaci, [přečtěte si informace o registraci aplikace](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Jeden tenant – pokud vytváříte aplikaci pouze pro vaši organizaci, musí být zaregistrovaná v adresáři vaší společnosti pomocí portálu Azure.
* Víceklient – pokud vytváříte aplikaci, kterou mohou používat uživatelé mimo vaši organizaci, musí být zaregistrována v adresáři vaší společnosti, ale musí být také registrována v adresáři každé organizace, který bude aplikaci používat. Chcete-li, aby vaše aplikace byla k dispozici ve svém adresáři, můžete zahrnout proces registrace pro zákazníky, který jim umožní souhlas s vaší aplikací. Když se zaregistrují pro vaši aplikaci, zobrazí se dialogové okno, které zobrazuje oprávnění, která aplikace vyžaduje, a pak možnost souhlasu. V závislosti na požadovaných oprávněních může být správce v jiné organizaci požádán o udělení souhlasu. Když uživatel nebo správce souhlasí, aplikace je registrována v jejich adresáři.

## <a name="token-expiration"></a>Vypršení platnosti tokenu

Platnost relace uživatele vyprší, když vyprší životnost tokenu vydaného službou Azure AD. Aplikace může v případě potřeby zkrátit toto časové období, například odhlášení uživatelů na základě období nečinnosti. Po vypršení platnosti relace bude uživatel vyzván k novému přihlášení.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [typech aplikací a scénářích](app-types.md)
* Informace o [základech ověřování](v1-authentication-scenarios.md) Azure AD

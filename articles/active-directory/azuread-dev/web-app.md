---
title: Webové aplikace v Azure Active Directory
description: Popisuje, co jsou webové aplikace, a základní informace o toku, registraci a vypršení platnosti tokenu pro tento typ aplikace.
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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154402"
---
# <a name="web-apps"></a>Webové aplikace

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Webové aplikace jsou aplikace, které ověřují uživatele ve webovém prohlížeči na webové aplikace. V tomto scénáři webová aplikace nasměruje prohlížeč uživatele, aby je přihlásil do služby Azure AD. Azure AD vrátí odpověď pro přihlášení prostřednictvím prohlížeče uživatele, který obsahuje deklarace identity uživatele v tokenu zabezpečení. Tento scénář podporuje přihlášení pomocí protokolů OpenID Connect, SAML 2,0 a WS-Federation.

## <a name="diagram"></a>Diagram

![Tok ověřování pro prohlížeč pro webovou aplikaci](./media/authentication-scenarios/web-browser-to-web-api.png)

## <a name="protocol-flow"></a>Tok protokolu

1. Když uživatel navštíví aplikaci a potřebuje se přihlásit, budou přesměrováni prostřednictvím žádosti o přihlášení do koncového bodu ověřování ve službě Azure AD.
1. Uživatel se přihlásí na přihlašovací stránce.
1. Pokud je ověření úspěšné, Azure AD vytvoří ověřovací token a vrátí odpověď na přihlašovací odpověď k adrese URL odpovědi aplikace, která byla nakonfigurovaná v Azure Portal. V případě produkční aplikace by tato adresa URL odpovědi měla být HTTPS. Vrácený token obsahuje deklarace identity uživatele a služby Azure AD, které vyžaduje aplikace k ověření tokenu.
1. Aplikace ověří token pomocí veřejného podpisového klíče a informací vystavitelů, které jsou k dispozici v dokumentu federačních metadat pro službu Azure AD. Poté, co aplikace ověří token, spustí novou relaci s uživatelem. Tato relace umožňuje uživateli přístup k aplikaci až do vypršení platnosti.

## <a name="code-samples"></a>Ukázky kódů

Podívejte se na ukázky kódu pro webový prohlížeč ve scénářích webové aplikace. A pravidelně se podívejte na to, jak se často přidávají nové ukázky.

## <a name="app-registration"></a>Registrace aplikace

Pokud chcete zaregistrovat webovou aplikaci, přečtěte si téma [Registrace aplikace](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Jeden tenant – Pokud vytváříte aplikaci jenom pro vaši organizaci, musí být zaregistrovaná v adresáři vaší společnosti pomocí Azure Portal.
* Vícenásobný tenant – Pokud vytváříte aplikaci, kterou můžou používat uživatelé mimo vaši organizaci, musí být zaregistrované v adresáři vaší společnosti, ale musí být zaregistrované v adresáři každé organizace, který bude aplikaci používat. Aby vaše aplikace byla ve svém adresáři k dispozici, můžete pro zákazníky zahrnout proces registrace, který jim umožní vyjádřit souhlas s vaší aplikací. Při registraci do aplikace se zobrazí dialogové okno, které zobrazuje oprávnění, která aplikace vyžaduje, a pak možnost souhlasu. V závislosti na požadovaných oprávněních může být vyžadováno poskytnutí souhlasu správce v jiné organizaci. Když uživatel nebo správce souhlasí, aplikace se zaregistruje ve svém adresáři.

## <a name="token-expiration"></a>Vypršení platnosti tokenu

Relace uživatele vyprší, jakmile vyprší doba platnosti tokenu vydaného službou Azure AD. V případě potřeby může aplikace zkrátit toto časové období, například odhlášení uživatelů na základě období neaktivity. Po vypršení platnosti relace se uživateli zobrazí výzva k opětovnému přihlášení.

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [typech a scénářích aplikací](app-types.md)
* Seznamte se se [základy ověřování](v1-authentication-scenarios.md) Azure AD

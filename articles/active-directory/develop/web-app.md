---
title: Web apps v Azure Active Directory
description: Popisuje, co jsou webové aplikace a základní informace v protokolu toku, registrace a vypršení platnosti tokenu pro tento typ aplikace.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d15d76f4c16fa89b41ebfc10c9617c4709203d38
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544727"
---
# <a name="web-apps"></a>Webové aplikace

Webové aplikace jsou aplikace, které ověřit uživatele ve webovém prohlížeči na webovou aplikaci. V tomto scénáři webová aplikace přesměruje prohlížeč uživatele pro jejich přihlášení k Azure AD. Azure AD vrátí odpověď přihlášení prostřednictvím webového prohlížeče, který obsahuje deklarace identity o uživateli v tokenu zabezpečení. Tento scénář podporuje přihlašování pomocí protokoly OpenID Connect, SAML 2.0 a WS-Federation.

## <a name="diagram"></a>Diagram

![Tok ověření pro prohlížeče a webové aplikace](./media/authentication-scenarios/web_browser_to_web_api.png)

## <a name="protocol-flow"></a>Protokol toku

1. Když uživatel navštíví aplikace a potřebuje přihlásit, bude přesměrován prostřednictvím přihlašovací požadavek na koncový bod ověřování ve službě Azure AD.
1. Uživatel se přihlásí na přihlašovací stránku.
1. Pokud je ověření úspěšné, Azure AD vytvoří ověřovacího tokenu a vrátí odpověď přihlašovací adresa URL odpovědi aplikace, který byl nakonfigurován na webu Azure Portal. Pro produkční aplikace tato adresa URL odpovědi by mělo být HTTPS. Vrácený token obsahuje deklarace identity o uživateli a Azure AD, které jsou vyžadované aplikací k ověření tokenu.
1. Aplikace ověří daný token pomocí veřejného podpisového klíče a vydavatele informace, které jsou k dispozici v dokumentu federačních metadat pro službu Azure AD. Když aplikace ověří token, spustí novou relaci s uživatelem. Tato relace povolí uživateli přístup k aplikaci do vypršení jeho platnosti.

## <a name="code-samples"></a>Ukázky kódu

Zobrazit ukázky kódu pro webový prohlížeč na web scénářích aplikací. A vrácení zpět často, jak často jsou přidávány nové ukázky.

## <a name="app-registration"></a>Registrace aplikace

Chcete-li zaregistrovat webovou aplikaci, přečtěte si téma [registrace aplikace](quickstart-register-app.md).

* Jednoho tenanta – Pokud vytváříte aplikaci pouze pro vaši organizaci, je nutné jej zaregistrovat v adresáři vaší firmy s využitím webu Azure portal.
* Víceklientské – Pokud vytváříte aplikaci, která je možné uživatele mimo vaši organizaci ho musí být zaregistrovaný v adresáři vaší společnosti, ale také musí být zaregistrovaný v adresáři každé organizace, který bude používat aplikace. K zajištění dostupnosti v jejich adresáři aplikace, můžete zahrnout procesu registrace pro vaše zákazníky, díky kterým souhlas s vaší aplikací. Při registraci pro vaši aplikaci, se zobrazí se dialogové okno zobrazující oprávnění, která aplikace požaduje instalaci a pak možnost vyjádřit souhlas. V závislosti na požadovaných oprávnění může být správcem v druhé organizaci nutné vyjádřit souhlas. Když uživatel nebo správce souhlasí, aplikace je zaregistrovaný v jejich adresáře.

## <a name="token-expiration"></a>Vypršení platnosti tokenu

Relace uživatele vyprší po vypršení platnosti tokenu vydaného službou Azure AD. Aplikace můžete zkrátit toto časové období, v případě potřeby, jako je například odhlašování uživatelů podle určité době nečinnosti. Po vypršení platnosti relace, uživatel se vyzve se znovu přihlásit.

## <a name="next-steps"></a>Další postup

* Další informace o dalších [typy aplikací a scénářů](app-types.md)
* Další informace o službě Azure AD [základy ověřování](authentication-scenarios.md)

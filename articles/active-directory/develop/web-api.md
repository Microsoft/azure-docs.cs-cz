---
title: Webové rozhraní API apps v Azure Active Directory
description: Popisuje, co jsou aplikace webového rozhraní API a základní informace v protokolu toku, registrace a vypršení platnosti tokenu pro tento typ aplikace.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: e4ed0db3a08937c3c8b51e2c8af5e566b59df4c4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970953"
---
# <a name="web-api"></a>Web API

Webové rozhraní API apps jsou webové aplikace, které potřebujete k získání zdroje z webového rozhraní API. V tomto scénáři existují dva typy identit, které webové aplikace můžete použít k ověření a zavolání webového rozhraní API:

- **Identita aplikace** -tento scénář využívá udělování přihlašovacích údajů klienta OAuth 2.0 k ověření jako aplikace a přístup k webovému rozhraní API. Při použití identita aplikace, webové rozhraní API může jenom zjistit, že webová aplikace je volat, jak se webové rozhraní API nepřijímá žádné informace o uživateli. Pokud aplikace obdrží informace o uživateli, se pošle přes protokoly aplikací a není podepsán pomocí Azure AD. Webové rozhraní API důvěřuje, že webová aplikace ověřil daného uživatele. Z tohoto důvodu tento model se nazývá důvěryhodný subsystém.
- **Delegovaná identita uživatele** -tento scénář lze provést dvěma způsoby: OpenID Connect a udělení autorizačního kódu OAuth 2.0 s důvěrnému klientovi. Webová aplikace získá přístupový token pro uživatele, která prokáže vaše oprávnění k webovému rozhraní API, které se uživatel úspěšně ověřen na webovou aplikaci a které webové aplikace se podařilo získat identitu delegovaný uživatel k volání webového rozhraní API. Tento přístupový token se posílá ve požadavek do webového rozhraní API, které opravňují uživatele a vrátí požadovaný prostředek.

Identita aplikace i delegovaný uživatel identity typy jsou popsány v níže toku. Klíčovým rozdílem mezi nimi je, že delegovaný uživatel identity musíte nejprve získat autorizační kód předtím, než uživatel může přihlásit a získat přístup k webovému rozhraní API.

## <a name="diagram"></a>Diagram

![Webové aplikace do diagramu webového rozhraní API](./media/authentication-scenarios/web_app_to_web_api.png)

## <a name="protocol-flow"></a>Protokol toku

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Udělit aplikaci identity pomocí přihlašovacích údajů klienta OAuth 2.0

1. Uživatel je přihlášený do služby Azure AD ve webové aplikaci (viz **webové aplikace** části Další informace).
1. Webová aplikace musí získat přístupový token, takže ho můžete ověřit do webového rozhraní API a získat požadovaný prostředek. Odešle požadavek do služby Azure AD koncový bod tokenu, zadejte přihlašovací údaje, ID aplikace a identifikátor URI ID aplikace webového rozhraní API.
1. Azure AD ověřuje aplikace a vrátí přístupový token JWT, který se používá k volání webového rozhraní API.
1. Přes protokol HTTPS webová aplikace používá vrácené přístupový token JWT přidat token JWT řetězec s označením "Nosiče" v hlavičce autorizace požadavku do webového rozhraní API. Webové rozhraní API pak ověří JWT token a pokud je ověření úspěšné, vrátí požadovaný prostředek.

### <a name="delegated-user-identity-with-openid-connect"></a>Delegovaný uživatel identity s OpenID Connect

1. Uživatel je přihlášený do webové aplikace pomocí Azure AD (viz [webového prohlížeče a webové aplikace](#web-browser-to-web-application) výše). Pokud uživatel webové aplikace nevyjádřil dosud povolení webové aplikace k volání webového rozhraní API na jejím jménem, uživateli muset vyjádřit souhlas. Aplikace se zobrazí oprávnění, která vyžaduje, a pokud některý z těchto oprávnění na úrovni správce, běžného uživatele v adresáři, nebude možné vyjádřit souhlas. Tento proces souhlasu platí jenom pro aplikace s více tenanty, aplikace není jednoho tenanta, protože aplikace se už máte potřebná oprávnění. Když uživatel přihlášen, webové aplikace dostala token ID s informacemi o uživateli, jakož i autorizační kód.
1. Použití autorizační kód vydané službou Azure AD, webová aplikace odešle požadavek na token koncového bodu Azure AD, který obsahuje autorizační kód, podrobnosti o aplikaci klienta (ID aplikace a identifikátor URI pro přesměrování) a požadovaný prostředek (ID aplikace Identifikátor URI pro webové rozhraní API).
1. Autorizační kód a informace o webové aplikace a webového rozhraní API jsou ověření pomocí Azure AD. Po úspěšném ověření Azure AD vrací dva tokeny: přístupový token JWT a aktualizační token JWT.
1. Přes protokol HTTPS webová aplikace používá vrácené přístupový token JWT přidat token JWT řetězec s označením "Nosiče" v hlavičce autorizace požadavku do webového rozhraní API. Webové rozhraní API pak ověří JWT token a pokud je ověření úspěšné, vrátí požadovaný prostředek.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Delegovaný uživatel identitu pomocí udělení autorizačního kódu OAuth 2.0

1. Uživatel je už přihlášení do webové aplikace, jejichž mechanismus ověřování je nezávislý na Azure AD.
1. Webová aplikace vyžaduje autorizační kód k získání přístupového tokenu, takže vydává žádosti přes prohlížeč pro koncový bod autorizace Azure AD, poskytuje ID aplikace a identifikátor URI přesměrování pro webovou aplikaci po úspěšném ověření. Uživatel se přihlásí ke službě Azure AD.
1. Pokud uživatel webové aplikace nevyjádřil dosud povolení webové aplikace k volání webového rozhraní API na jejím jménem, uživateli muset vyjádřit souhlas. Aplikace se zobrazí oprávnění, která vyžaduje, a pokud některý z těchto oprávnění na úrovni správce, běžného uživatele v adresáři, nebude možné vyjádřit souhlas. Toto vyjádření souhlasu se vztahuje na aplikace i s více tenanty. V tomto případě jednoho tenanta můžete provádět správce souhlasu pro vyjádření souhlasu správce jménem uživatele. To lze provést pomocí `Grant Permissions` tlačítko [webu Azure portal](https://portal.azure.com). 
1. Po uživatelem, webová aplikace obdrží autorizační kód potřebný k získání přístupového tokenu.
1. Použití autorizační kód vydané službou Azure AD, webová aplikace odešle požadavek na token koncového bodu Azure AD, který obsahuje autorizační kód, podrobnosti o aplikaci klienta (ID aplikace a identifikátor URI pro přesměrování) a požadovaný prostředek (ID aplikace Identifikátor URI pro webové rozhraní API).
1. Autorizační kód a informace o webové aplikace a webového rozhraní API jsou ověření pomocí Azure AD. Po úspěšném ověření Azure AD vrací dva tokeny: přístupový token JWT a aktualizační token JWT.
1. Přes protokol HTTPS webová aplikace používá vrácené přístupový token JWT přidat token JWT řetězec s označením "Nosiče" v hlavičce autorizace požadavku do webového rozhraní API. Webové rozhraní API pak ověří JWT token a pokud je ověření úspěšné, vrátí požadovaný prostředek.

## <a name="code-samples"></a>Ukázky kódů

Zobrazit ukázky kódu pro webovou aplikaci do scénáře webového rozhraní API. A, vraťte se často – jsou přidávány nové ukázky. Web [aplikace webového rozhraní API](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Registrace aplikace

Registrace aplikace ke koncovému bodu Azure AD v1.0, najdete v článku [registrace aplikace ke koncovému bodu Azure AD v1.0](quickstart-v1-add-azure-ad-app.md).

* Jednoho tenanta – identita aplikace a delegovaný uživatel identity případy, webová aplikace i webové rozhraní API musí být zaregistrovaný ve stejném adresáři, ve službě Azure AD. Webové rozhraní API je možné nakonfigurovat k vystavení sady oprávnění, které slouží k omezení přístupu k webové aplikaci na prostředky. Pokud typ identity delegovaný uživatel musí vybrat požadované oprávnění z webové aplikace **oprávnění k ostatním aplikacím** rozevírací nabídky na webu Azure Portal. Tento krok není povinný, pokud se používá typ identity aplikace.
* Více tenantů-nejprve webové aplikace je nakonfigurovaná k označení oprávnění, která ho, aby byl funkční. Tento seznam požadovaných oprávnění se zobrazí v dialogovém okně, když uživatel nebo správce v cílovém adresáři dává souhlas pro aplikaci, které zpřístupní jejich organizace. Některé aplikace vyžadují jenom oprávnění na úrovni uživatele, které každý uživatel v organizaci můžou udělit souhlas s. Jiné aplikace vyžadují oprávnění na úrovni správce, které nelze vyjádřit souhlas uživatele v organizaci. Pouze správce adresáře lze udělit souhlas aplikacím, které vyžadují tuto úroveň oprávnění. Když uživatel nebo správce souhlasí, webové aplikace a webového rozhraní API jsou obě registrované v jejich adresáře.

## <a name="token-expiration"></a>Vypršení platnosti tokenu

Pokud webová aplikace používá jeho autorizační kód k získání tokenu JWT přístupu, také obdrží obnovovací token JWT. Když vyprší platnost přístupového tokenu, token obnovení je možné donutit uživatele bez nutnosti jejich nutnost znovu se přihlaste k. Tento aktualizační token se pak použije k ověření uživatele, jehož výsledkem nový přístupový token a aktualizační token.

## <a name="next-steps"></a>Další postup

- Další informace o dalších [typy aplikací a scénářů](app-types.md)
- Další informace o službě Azure AD [základy ověřování](authentication-scenarios.md)
---
title: Služba služba aplikace ve službě Azure Active Directory
description: Popisuje, jaké aplikace service to service a základní informace na protokol toku, registrace a vypršení platnosti tokenu pro tento typ aplikace.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0ced89ce97d5f22270d9968fdeb0ddb3fad1e4e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56194984"
---
# <a name="service-to-service-apps"></a>Služba služba aplikace

Služba služba aplikace mohou být aplikace proces démon nebo serveru, kterou je potřeba získat prostředky z webového rozhraní API. Existují dvě dílčí scénáře, které se vztahují k této sekci:

- Démon procesu, kterou je potřeba volat rozhraní API založená na typ udělení přihlašovacích údajů klienta OAuth 2.0

    V tomto scénáři je důležité pochopit pár věcí. Nejprve interakci s uživatelem není možné aplikaci démon, který vyžaduje aplikaci mít svou vlastní identitu. Příklad aplikace démon je úlohu služby batch, nebo službu operační systém běží na pozadí. Tento typ aplikace vyžádá přístupový token pomocí jeho identita aplikace a nabízí ten samý jeho ID aplikace, přihlašovací údaje (heslo nebo certifikát) a aplikace identifikátor URI ID do služby Azure AD. Po úspěšném ověření démona přijímá přístupového tokenu z Azure AD, která se pak použije k volání webového rozhraní API.

- Serverové aplikace (například webové rozhraní API), kterou je potřeba volat webové rozhraní API, založená na specifikace konceptu OAuth 2.0 On-Behalf-Of

    V tomto scénáři Představte si, že uživatel se ověřil na nativní aplikaci a tuto nativní aplikaci je potřeba volat webové rozhraní API. Azure AD vydá přístupový token JWT volání webového rozhraní API. Pokud webové rozhraní API je potřeba volat podřízené webové rozhraní API, můžete tok on-behalf-of pro delegování identity uživatele a pro ověření do druhé úrovně webového rozhraní API.

## <a name="diagram"></a>Diagram

![Proces démon nebo serverová aplikace webové rozhraní API diagramu](./media/authentication-scenarios/daemon_server_app_to_web_api.png)

## <a name="dprotocol-flow"></a>DProtocol toku

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Udělit aplikaci identity pomocí přihlašovacích údajů klienta OAuth 2.0

1. Serverová aplikace musí nejprve ověřování pomocí Azure AD za sebe, bez zásahu člověka jako je například interaktivní dialogu přihlašování. Odešle požadavek do služby Azure AD koncový bod tokenu, zadejte přihlašovací údaje, ID aplikace a identifikátor URI ID aplikace.
1. Azure AD ověřuje aplikace a vrátí přístupový token JWT, který se používá k volání webového rozhraní API.
1. Přes protokol HTTPS webová aplikace používá vrácené přístupový token JWT přidat token JWT řetězec s označením "Nosiče" v hlavičce autorizace požadavku do webového rozhraní API. Webové rozhraní API pak ověří JWT token a pokud je ověření úspěšné, vrátí požadovaný prostředek.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Delegovaný uživatel identitu pomocí specifikace konceptu On-Behalf-Of OAuth 2.0

Viz následující popis toku předpokládá, že uživatel byl ověřen na jinou aplikaci (například nativní aplikace) a jejich identitu uživatele se použil k získání přístupového tokenu do první úrovně webového rozhraní API.

1. Nativní aplikace odesílá přístupového tokenu do první úrovně webového rozhraní API.
1. První úrovně webového rozhraní API odešle požadavek do služby Azure AD koncový bod tokenu, poskytuje jeho ID aplikace a přihlašovací údaje, stejně jako přístupový token uživatele. Kromě toho je žádost odeslána pomocí on_behalf_of parametr, který označuje, webové rozhraní API vyžaduje nové tokeny pro volání podřízené webové rozhraní API jménem původního uživatele.
1. Azure AD ověří, jestli webové rozhraní API první úrovně má oprávnění pro přístup k druhé úrovně webového rozhraní API a ověří žádost vrácení přístupový token JWT, token JWT obnovovací token do první úrovně webového rozhraní API.
1. První úrovně webového rozhraní API přes protokol HTTPS, pak zavolá druhé úrovně webového rozhraní API přidáním řetězec tokenu v autorizační hlavičky v požadavku. První úrovně webového rozhraní API můžete nadále volají druhé úrovně webového rozhraní API jako přístupový token a obnovovací tokeny jsou platné.

## <a name="code-samples"></a>Ukázky kódů

Zobrazit ukázky kódu pro proces démon nebo serverové aplikace na scénáře webového rozhraní API. A vrácení zpět často, jak často jsou přidávány nové ukázky. [Server nebo proces démon aplikace webového rozhraní API](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Registrace aplikace

* Jednoho tenanta – identita aplikace i delegovaný uživatel případech identity proces démon nebo serverové aplikace musí být zaregistrovaný ve stejném adresáři, ve službě Azure AD. Webové rozhraní API je možné nakonfigurovat k vystavení sady oprávnění, které slouží k omezení proces démon nebo serveru pro přístup k jeho prostředkům. Pokud typ identity delegovaný uživatel serverová aplikace musí z rozevírací nabídky "Oprávnění do jiných aplikací" na webu Azure Portal vyberte požadovaná oprávnění. Tento krok není povinný, pokud se používá typ identity aplikace.
* Víceklientské-First, proces démon nebo serveru aplikace je nakonfigurovaná k označení oprávnění, která ho, aby byl funkční. Tento seznam požadovaných oprávnění se zobrazí v dialogovém okně, když uživatel nebo správce v cílovém adresáři dává souhlas pro aplikaci, které zpřístupní jejich organizace. Některé aplikace vyžadují jenom oprávnění na úrovni uživatele, které každý uživatel v organizaci můžou udělit souhlas s. Jiné aplikace vyžadují oprávnění na úrovni správce, které nelze vyjádřit souhlas uživatele v organizaci. Pouze správce adresáře lze udělit souhlas aplikacím, které vyžadují tuto úroveň oprávnění. Když uživatel nebo správce souhlasí, i webové rozhraní API jsou registrovány v jejich adresáře.

## <a name="token-expiration"></a>Vypršení platnosti tokenu

Při první aplikace používá jeho autorizační kód k získání tokenu JWT přístupu, také obdrží obnovovací token JWT. Když vyprší platnost přístupového tokenu, token obnovení je možné znovu ověřovat uživatele bez vás vyzve k zadání přihlašovacích údajů. Tento aktualizační token se pak použije k ověření uživatele, jehož výsledkem nový přístupový token a aktualizační token.

## <a name="next-steps"></a>Další postup

- Další informace o dalších [typy aplikací a scénářů](app-types.md)
- Další informace o službě Azure AD [základy ověřování](authentication-scenarios.md)

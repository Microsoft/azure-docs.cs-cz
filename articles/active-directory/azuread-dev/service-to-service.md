---
title: Aplikace Service-to-Service v Azure Active Directory
description: Popisuje, jaké aplikace Service-to-Service a základní informace o toku, registraci a vypršení platnosti tokenu pro tento typ aplikace.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 179034533d90dbbb6ca362fc6f72996f32873729
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154759"
---
# <a name="service-to-service-apps"></a>Aplikace Service-to-Service

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Aplikace Service-to-Service můžou být démon nebo serverová aplikace, které potřebují získat prostředky z webového rozhraní API. Existují dva dílčí scénáře, které se vztahují k této části:

- Démon, který potřebuje zavolat webové rozhraní API, sestavený na typ udělení přihlašovacích údajů klienta OAuth 2,0

    V tomto scénáři je důležité pochopit několik věcí. První zásah uživatele není možné s aplikací démona, která vyžaduje, aby aplikace měla svou vlastní identitu. Příkladem aplikace démona je dávková úloha nebo služba operačního systému spuštěná na pozadí. Tento typ aplikace požaduje přístupový token pomocí jeho identity aplikace a předvádí jeho ID aplikace, pověření (heslo nebo certifikátu) a identifikátor URI ID aplikace do služby Azure AD. Po úspěšném ověření získá démon přístupový token z Azure AD, který se pak použije k volání webového rozhraní API.

- Serverová aplikace (například webové rozhraní API), která potřebuje volat webové rozhraní API postavené na základě specifikace konceptu OAuth 2,0.

    V tomto scénáři představte, že uživatel ověřil v nativní aplikaci a že tato nativní aplikace musí volat webové rozhraní API. Služba Azure AD vydá přístupový token JWT pro volání webového rozhraní API. Pokud webové rozhraní API potřebuje volat jiné podřízené webové rozhraní API, může k delegování identity uživatele a ověření pro webové rozhraní API druhé úrovně použít tok spouštěný jménem.

## <a name="diagram"></a>Diagram

![Diagram démona nebo serverové aplikace do webového rozhraní API](./media/authentication-scenarios/daemon-server-app-to-web-api.png)

## <a name="protocol-flow"></a>Tok protokolu

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identita aplikace s udělenými přihlašovacími údaji klienta OAuth 2,0

1. Nejdřív se serverová aplikace musí ověřit ve službě Azure AD jako samotnou, a to bez jakékoli lidské interakce, jako je interaktivní přihlašovací dialog. Vytvoří požadavek na koncový bod tokenu služby Azure AD, přičemž poskytne přihlašovací údaje, ID aplikace a identifikátor URI ID aplikace.
1. Azure AD ověří aplikaci a vrátí přístupový token JWT, který se používá k volání webového rozhraní API.
1. Přes protokol HTTPS používá webová aplikace vrácený přístupový token JWT k přidání řetězce JWT s označením "nosiče" v autorizační hlavičce požadavku do webového rozhraní API. Webové rozhraní API potom ověří token JWT a pokud je ověření úspěšné, vrátí požadovaný prostředek.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Identita delegovaného uživatele pomocí specifikace konceptu OAuth 2,0 za jménem

Tok popsaný níže předpokládá, že uživatel byl ověřen v jiné aplikaci (například v nativní aplikaci) a jeho identita uživatele byla použita k získání přístupového tokenu k webovému rozhraní API první vrstvy.

1. Nativní aplikace pošle přístupový token do rozhraní Web API na první vrstvě.
1. První vrstva webového rozhraní API pošle požadavek koncovému bodu tokenu služby Azure AD, který poskytuje ID aplikace a přihlašovací údaje a také přístupový token uživatele. Kromě toho se požadavek pošle s parametrem on_behalf_of, který indikuje, že webové rozhraní API žádá o nové tokeny pro volání webového rozhraní API pro příjem dat jménem původního uživatele.
1. Azure AD ověří, že webové rozhraní API na první vrstvě má oprávnění pro přístup k webovému rozhraní API druhé úrovně a ověří požadavek a vrátí přístupový token JWT a obnovovací token JWT na první vrstvu webového rozhraní API.
1. Webové rozhraní API první vrstvy pak prostřednictvím protokolu HTTPS volá webové rozhraní API druhé vrstvy připojením řetězce tokenu v autorizační hlavičce v žádosti. Webové rozhraní API první vrstvy může dál volat webové rozhraní API druhé úrovně, pokud je přístupový token a aktualizační tokeny platné.

## <a name="code-samples"></a>Ukázky kódů

Podívejte se na ukázky kódu pro procesy démona nebo serverové aplikace do webového rozhraní API: [aplikace serveru nebo démona do webového rozhraní API](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Registrace aplikace

* Jeden tenant – pro případy identity aplikace i delegovaného uživatele musí být démon nebo serverová aplikace zaregistrované ve stejném adresáři v Azure AD. Webové rozhraní API je možné nakonfigurovat tak, aby vystavilo sadu oprávnění, která slouží k omezení přístupu démona nebo serveru k jeho prostředkům. Pokud se používá typ identity delegovaný uživatel, musí serverová aplikace vybrat požadovaná oprávnění. Na stránce **oprávnění rozhraní API** pro registraci aplikace po výběru možnosti **Přidat oprávnění** a zvolení rodiny rozhraní API zvolte **delegovaná oprávnění** a pak vyberte vaše oprávnění. Tento krok není nutný, pokud se používá typ identity aplikace.
* Více tenantů – nejprve je démon nebo serverová aplikace nakonfigurovaná tak, aby označovala oprávnění, která vyžaduje, aby byla funkční. Tento seznam požadovaných oprávnění se zobrazí v dialogovém okně, když uživatel nebo správce v cílovém adresáři udělí souhlas s aplikací a zpřístupní ji pro jejich organizaci. Některé aplikace vyžadují oprávnění na úrovni uživatele, ke kterým může udělit každý uživatel v organizaci souhlas. Jiné aplikace vyžadují oprávnění na úrovni správce, kterým uživatel v organizaci nemůže udělit souhlas. Pouze správce adresáře může udělit souhlas aplikacím, které vyžadují tuto úroveň oprávnění. Když uživatel nebo správce souhlasí, obě webová rozhraní API se zaregistrují ve svém adresáři.

## <a name="token-expiration"></a>Vypršení platnosti tokenu

Když první aplikace použije svůj autorizační kód k získání přístupového tokenu JWT, obdrží také obnovovací token JWT. Po vypršení platnosti přístupového tokenu se dá obnovovací token použít k opětovnému ověření uživatele bez výzvy k zadání přihlašovacích údajů. Tento obnovovací token se pak použije k ověření uživatele, což vede k vytvoření nového přístupového tokenu a k aktualizaci tokenu.

## <a name="next-steps"></a>Další kroky

- Další informace o dalších [typech a scénářích aplikací](app-types.md)
- Seznamte se se [základy ověřování](v1-authentication-scenarios.md) Azure AD

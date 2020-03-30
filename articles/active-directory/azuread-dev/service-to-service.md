---
title: Aplikace pro služby ve službě Azure Active Directory
description: Popisuje, jaké aplikace služby a základy pro tok protokolu, registrace a vypršení platnosti tokenu pro tento typ aplikace.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154759"
---
# <a name="service-to-service-apps"></a>Aplikace pro služby

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Aplikace služby mohou být daemon nebo serverová aplikace, která potřebuje získat prostředky z webového rozhraní API. Existují dva dílčí scénáře, které se vztahují k této části:

- Daemon, který potřebuje volat webové rozhraní API, postavený na oauth 2.0 klienta pověření typu grantu

    V tomto scénáři je důležité pochopit několik věcí. Za prvé, interakce s uživatelem není možné s aplikací daemon, který vyžaduje, aby aplikace mít svou vlastní identitu. Příkladem aplikace daemon je dávková úloha nebo služba operačního systému spuštěná na pozadí. Tento typ aplikace požaduje přístupový token pomocí identity aplikace a předložením id aplikace, přihlašovacích údajů (heslo nebo certifikát) a identifikátoru URI aplikace do služby Azure AD. Po úspěšném ověření daemon obdrží přístupový token z Azure AD, který se pak používá k volání webovérozhraní API.

- Serverová aplikace (například webové rozhraní API), která potřebuje volat webové rozhraní API, postavené na oauth 2.0 on-behalf-of návrh specifikace

    V tomto scénáři si představte, že uživatel byl ověřen v nativní aplikaci a tato nativní aplikace musí volat webové rozhraní API. Azure AD vydává přístupový token JWT pro volání webového rozhraní API. Pokud webové rozhraní API potřebuje volat jiné podřízené webové rozhraní API, můžete použít on-behalf-of flow delegovat identitu uživatele a ověřit na webová rozhraní API druhé vrstvy.

## <a name="diagram"></a>Diagram

![Diagram daemonnebo serverové aplikace do webového rozhraní API](./media/authentication-scenarios/daemon-server-app-to-web-api.png)

## <a name="protocol-flow"></a>Tok protokolu

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identita aplikace s grantem klientských pověření OAuth 2.0

1. Nejprve serverová aplikace potřebuje k ověření pomocí Azure AD jako sám, bez jakékoli lidské interakce, jako je například interaktivní přihlašovací dialog. Provede požadavek na koncový bod tokenu Služby Azure AD, který poskytuje pověření, ID aplikace a identifikátor URI ID aplikace.
1. Azure AD ověřuje aplikaci a vrátí přístupový token JWT, který se používá k volání webového rozhraní API.
1. Přes protokol HTTPS používá webová aplikace vrácený přístupový token JWT k přidání řetězce JWT s označením "Nosič" v hlavičce autorizace požadavku do webového rozhraní API. Webové rozhraní API pak ověří token JWT a pokud je ověření úspěšné, vrátí požadovaný prostředek.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Delegovaná identita uživatele s OAuth 2.0 On-Behalf-Of Návrh specifikace

Tok popsaný níže předpokládá, že uživatel byl ověřen v jiné aplikaci (například nativní aplikace) a jeho identita uživatele byla použita k získání přístupového tokenu k webovému rozhraní API první vrstvy.

1. Nativní aplikace odešle přístupový token do webového rozhraní API první vrstvy.
1. Webové rozhraní API první vrstvy odešle požadavek na koncový bod tokenu služby Azure AD, který poskytuje id a přihlašovací údaje aplikace a přístupový token uživatele. Kromě toho je požadavek odeslán s parametrem on_behalf_of, který označuje, že webové rozhraní API požaduje nové tokeny pro volání následného webového rozhraní API jménem původního uživatele.
1. Azure AD ověří, že webové rozhraní API první vrstvy má oprávnění k přístupu k webovému rozhraní API druhé vrstvy a ověří požadavek, vrací přístupový token JWT a obnovovací token JWT do webového rozhraní API první vrstvy.
1. Přes protokol HTTPS pak webové rozhraní API první vrstvy volá webové rozhraní API druhé vrstvy připojením řetězce tokenu v hlavičce autorizace v požadavku. Webové rozhraní API první vrstvy můžete nadále volat webové rozhraní API druhé vrstvy tak dlouho, dokud přístuptokeny a aktualizovat tokeny jsou platné.

## <a name="code-samples"></a>Ukázky kódů

Podívejte se na ukázky kódu pro scénáře daemon nebo serverové aplikace pro webové rozhraní API: [Server nebo Daemon Aplikace webového rozhraní API](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Registrace aplikací

* Jeden tenant – pro identitu aplikace a případy delegované identity uživatele musí být daemon nebo serverová aplikace registrována ve stejném adresáři ve službě Azure AD. Webové rozhraní API lze nakonfigurovat tak, aby zpřístupňovalo sadu oprávnění, která se používají k omezení přístupu daemonu nebo serveru k jeho prostředkům. Pokud se používá typ identity delegovaného uživatele, musí serverová aplikace vybrat požadovaná oprávnění. Po výběru možnosti **Přidat oprávnění** a výběru rodiny rozhraní API na stránce **Oprávnění rozhraní API** vyberte **delegovaná oprávnění**a pak vyberte svá oprávnění. Tento krok není vyžadován, pokud se používá typ identity aplikace.
* Víceklient – Nejprve je nakonfigurován a nakonfigurován daemon nebo serverová aplikace označující oprávnění, která vyžaduje, aby byla funkční. Tento seznam požadovaných oprávnění je zobrazen v dialogovém okně, když uživatel nebo správce v cílovém adresáři udělí souhlas s aplikací, která ji zpřístupní jejich organizaci. Některé aplikace vyžadují pouze oprávnění na úrovni uživatele, se kterými může souhlasit každý uživatel v organizaci. Jiné aplikace vyžadují oprávnění na úrovni správce, s nimiž uživatel v organizaci nemůže souhlasit. Souhlas s aplikacemi, které vyžadují tuto úroveň oprávnění, může udělit souhlas pouze správce adresáře. Pokud uživatel nebo správce souhlasí, obě webová rozhraní API jsou registrovány v jejich adresáři.

## <a name="token-expiration"></a>Vypršení platnosti tokenu

Když první aplikace používá svůj autorizační kód k získání přístupového tokenu JWT, obdrží také obnovovací token JWT. Po vypršení platnosti přístupového tokenu lze obnovovací token použít k opětovnému ověření uživatele bez zobrazení výzvy k použití pověření. Tento obnovovací token se pak používá k ověření uživatele, což má za následek nový přístupový token a obnovovací token.

## <a name="next-steps"></a>Další kroky

- Další informace o dalších [typech aplikací a scénářích](app-types.md)
- Informace o [základech ověřování](v1-authentication-scenarios.md) Azure AD

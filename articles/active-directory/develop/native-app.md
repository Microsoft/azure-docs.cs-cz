---
title: Nativní aplikace v Azure Active Directory
description: Popisuje, co jsou nativní aplikace a základní informace na protokol toku, registrace a vypršení platnosti tokenu pro tento typ aplikace.
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
ms.openlocfilehash: 4c15890a5693235e8f2554ba8d0fdefc161770f3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165004"
---
# <a name="native-apps"></a>Nativní aplikace

Nativní aplikace jsou aplikace, které volání webového rozhraní API jménem uživatele. Tento scénář je založený na typ udělení kódu autorizace OAuth 2.0 s veřejným klientem, jak je popsáno v části 4.1 [specifikaci OAuth 2.0](https://tools.ietf.org/html/rfc6749). Nativní aplikace získá přístupový token pro uživatele s použitím protokolu OAuth 2.0. Tento přístupový token se pak odešle přes požadavek do webového rozhraní API, které opravňují uživatele a vrátí požadovaný prostředek.

## <a name="diagram"></a>Diagram

![Nativní aplikace přes webové rozhraní API diagramu](./media/authentication-scenarios/native_app_to_web_api.png)

## <a name="protocol-flow"></a>Protokol toku

Pokud používáte knihoven ověřování služby AD, většinu níže popsaných podrobností protokolu jsou zpracovány za vás, jako je například automaticky otevírané okno prohlížeče, ukládání tokenu do mezipaměti a manipulace s tokeny obnovení.

1. Pomocí prohlížeče automaticky otevíraném okně že nativní aplikace odešle požadavek na koncový bod autorizace ve službě Azure AD. Tato žádost obsahuje ID aplikace a identifikátor URI nativní aplikace pro přesměrování, jak je znázorněno na webu Azure portal a identifikátor URI ID aplikace pro webové rozhraní API. Pokud uživatel ještě přihlášeni, zobrazí se výzva k akci
1. Azure AD ověřuje uživatele. Pokud je aplikace s více tenanty a souhlas se vyžaduje souhlas s použitím aplikace, uživatel muset vyjádřit souhlas, pokud se tak již neučinili. Po udělení souhlasu a po úspěšném ověření Azure AD vydá kód odpovědi ověřování. zpět do klientské aplikace identifikátor URI přesměrování.
1. Když Azure AD vydá kód odpovědi zpět na identifikátor URI pro přesměrování ověřování, klientská aplikace přestane interakce prohlížeče a extrahuje autorizační kód z odpovědi. Pomocí tohoto kódu autorizace, klientská aplikace odešle požadavek na token koncového bodu Azure AD, který obsahuje autorizační kód, podrobností o aplikaci klienta (ID aplikace a identifikátor URI pro přesměrování) a požadovaný prostředek (identifikátor ID URI aplikace pro webové rozhraní API).
1. Autorizační kód a informace o klientských aplikací a webového rozhraní API jsou ověření pomocí Azure AD. Po úspěšném ověření Azure AD vrací dva tokeny: přístupový token JWT a aktualizační token JWT. Kromě toho Azure AD vrátí základní informace o uživateli, jako je například jejich zobrazovaného názvu a tenanta ID.
1. Přes protokol HTTPS že klientská aplikace používá vrácené přístupový token JWT přidat token JWT řetězec s označením "Nosiče" v hlavičce autorizace požadavku do webového rozhraní API. Webové rozhraní API pak ověří JWT token a pokud je ověření úspěšné, vrátí požadovaný prostředek.
1. Když vyprší platnost přístupového tokenu, klientská aplikace obdrží chybu, která indikuje, že uživatel musí znovu provést ověření. Pokud má aplikace platné obnovovací token, můžete použít k získání nového tokenu přístupu bez výzvy pro uživatele se znovu přihlásit. Pokud vyprší platnost tokenu obnovení, aplikace bude nutné znovu interaktivně ověření uživatele.

> [!NOTE]
> Aktualizace tokenu vydaného službou Azure AD umožňuje přístup k více prostředkům. Například pokud máte klientské aplikace, který má oprávnění k volání dvě webové rozhraní API, obnovovací token slouží k získání přístupového tokenu pro jiné webové rozhraní API také.

## <a name="code-samples"></a>Ukázky kódů

Pro nativní aplikace pro scénáře webového rozhraní API najdete v ukázkách kódu. A vraťte se často – často přidáváme nové ukázky. [Nativní aplikace pro webové rozhraní API](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>Registrace aplikace

Registrace aplikace ke koncovému bodu Azure AD v1.0, najdete v článku [registrace aplikace ke koncovému bodu Azure AD v1.0](quickstart-v1-add-azure-ad-app.md).

* Jeden tenant - nativní aplikace a webové rozhraní API musí být zaregistrovaný ve stejném adresáři, ve službě Azure AD. Webové rozhraní API je možné nakonfigurovat k vystavení sady oprávnění, které slouží k omezení přístupu k nativní aplikaci na prostředky. Klientská aplikace potom vybere požadovaná oprávnění z rozevírací nabídky "Oprávnění do jiných aplikací" na webu Azure Portal.
* Víceklientské - nejprve nativní aplikace vždy jen registrované v vývojář nebo adresář vydavatele. Za druhé nativní aplikace je nakonfigurovaná k označení oprávnění, která ho, aby byl funkční. Tento seznam požadovaných oprávnění se zobrazí v dialogovém okně, když uživatel nebo správce v cílovém adresáři dává souhlas pro aplikaci, které zpřístupní jejich organizace. Některé aplikace vyžadují jenom oprávnění na úrovni uživatele, které každý uživatel v organizaci můžou udělit souhlas s. Jiné aplikace vyžadují oprávnění na úrovni správce, které nelze vyjádřit souhlas uživatele v organizaci. Pouze správce adresáře lze udělit souhlas aplikacím, které vyžadují tuto úroveň oprávnění. Když uživatel nebo správce souhlasí, pouze webová rozhraní API je zaregistrovaný v jejich adresáře. 

## <a name="token-expiration"></a>Vypršení platnosti tokenu

Když nativní aplikace používá jeho autorizační kód k získání tokenu JWT přístupu, také obdrží obnovovací token JWT. Když vyprší platnost přístupového tokenu, token obnovení je možné opakované ověření uživatele bez nutnosti jejich nutnost znovu přihlásit. Tento aktualizační token se pak použije k ověření uživatele, jehož výsledkem nový přístupový token a aktualizační token.

## <a name="next-steps"></a>Další postup

- Další informace o dalších [typy aplikací a scénářů](app-types.md)
- Další informace o službě Azure AD [základy ověřování](authentication-scenarios.md)

---
title: Nativní aplikace v Azure Active Directory
description: Popisuje, co jsou nativní aplikace a základní informace o toku, registraci a vypršení platnosti tokenu pro tento typ aplikace.
services: active-directory
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
ms.openlocfilehash: 9ecf711f5442b6f21de53d2735ad1c94d7cb6223
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154793"
---
# <a name="native-apps"></a>Nativní aplikace

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Nativní aplikace jsou aplikace, které zavolají webové rozhraní API jménem uživatele. Tento scénář je založený na typu udělení autorizačního kódu OAuth 2,0 s veřejným klientem, jak je popsáno v části 4,1 [specifikace OAuth 2,0](https://tools.ietf.org/html/rfc6749). Nativní aplikace získá přístupový token pro uživatele pomocí protokolu OAuth 2,0. Tento přístupový token se pak pošle v žádosti do webového rozhraní API, které uživatele autorizuje a vrátí požadovaný prostředek.

## <a name="diagram"></a>Diagram

![Diagram nativní aplikace do webového rozhraní API](./media/authentication-scenarios/native-app-to-web-api.png)

## <a name="protocol-flow"></a>Tok protokolu

Pokud používáte knihovny ověřování AD, většina podrobností protokolu popsaných níže se zpracovává za vás, například automaticky otevíraná okna prohlížeče, ukládání tokenů do mezipaměti a zpracování obnovovacích tokenů.

1. Pomocí automaticky otevíraného okna prohlížeče vytvoří nativní aplikace požadavek na koncový bod autorizace ve službě Azure AD. Tato žádost zahrnuje ID aplikace a identifikátor URI přesměrování nativní aplikace, jak je znázorněno v Azure Portal, a identifikátor URI ID aplikace pro webové rozhraní API. Pokud už uživatel není přihlášený, zobrazí se výzva k opětovnému přihlášení.
1. Azure AD ověří uživatele. Pokud se jedná o aplikaci s více klienty a k používání aplikace se vyžaduje souhlas, bude uživatel muset udělit souhlas, pokud už to neudělalo. Po udělení souhlasu a po úspěšném ověření vydá služba Azure AD odpověď autorizačního kódu zpátky do identifikátoru URI přesměrování klientské aplikace.
1. Když Azure AD vystaví odpověď autorizačního kódu zpátky na identifikátor URI přesměrování, klientská aplikace zastaví interakci prohlížeče a z odpovědi vyextrahuje autorizační kód. Pomocí tohoto autorizačního kódu pošle klientská aplikace požadavek na koncový bod tokenu služby Azure AD, který obsahuje autorizační kód, podrobnosti o klientské aplikaci (ID aplikace a identifikátor URI pro přesměrování) a požadovaný prostředek (identifikátor URI ID aplikace pro webové rozhraní API).
1. Služba Azure AD ověřuje autorizační kód a informace o klientské aplikaci a webovém rozhraní API. Po úspěšném ověření vrátí Azure AD dva tokeny: přístupový token JWT a obnovovací token JWT. Kromě toho Azure AD vrátí základní informace o uživateli, jako je jeho zobrazované jméno a ID tenanta.
1. Klientská aplikace přes protokol HTTPS pomocí vráceného přístupového tokenu JWT přidá řetězec JWT s označením "nosiče" v autorizační hlavičce požadavku webovému rozhraní API. Webové rozhraní API potom ověří token JWT a pokud je ověření úspěšné, vrátí požadovaný prostředek.
1. Po vypršení přístupového tokenu obdrží klientská aplikace chybu, která indikuje, že uživatel bude muset provést ověření znovu. Pokud má aplikace platný aktualizační token, dá se použít k získání nového přístupového tokenu bez vyzvání uživatele k opětovnému přihlášení. Pokud platnost tokenu obnovení vyprší, aplikace bude muset znovu interaktivně ověřit uživatele.

> [!NOTE]
> Obnovovací token vydaný službou Azure AD lze použít pro přístup k více prostředkům. Například pokud máte klientskou aplikaci, která má oprávnění volat dvě webová rozhraní API, může být obnovovací token použit také k získání přístupového tokenu k druhému webovému rozhraní API.

## <a name="code-samples"></a>Ukázky kódů

Podívejte se na ukázky kódu pro scénáře nativní aplikace do webového rozhraní API. A znovu se podívejte na časté – přidávajíme nové ukázky často. [Nativní aplikace do webového rozhraní API](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api)

## <a name="app-registration"></a>Registrace aplikace

Pokud chcete zaregistrovat aplikaci s koncovým bodem Azure AD v 1.0, přečtěte si téma [Registrace aplikace](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Jeden tenant – nativní aplikace i webové rozhraní API musí být zaregistrované ve stejném adresáři v Azure AD. Webové rozhraní API je možné nakonfigurovat tak, aby vystavilo sadu oprávnění, která slouží k omezení přístupu nativní aplikace k jejím prostředkům. Klientská aplikace pak vybere požadovaná oprávnění z rozevírací nabídky "oprávnění k ostatním aplikacím" v Azure Portal.
* Multi-tenant – první nativní aplikace se v minulosti jenom zaregistrovala v adresáři pro vývojáře nebo vydavatele. Druhý, nativní aplikace je nakonfigurovaná tak, aby označovala oprávnění, která vyžaduje, aby byla funkční. Tento seznam požadovaných oprávnění se zobrazí v dialogovém okně, když uživatel nebo správce v cílovém adresáři udělí souhlas s aplikací a zpřístupní ji pro jejich organizaci. Některé aplikace vyžadují oprávnění na úrovni uživatele, ke kterým může udělit každý uživatel v organizaci souhlas. Jiné aplikace vyžadují oprávnění na úrovni správce, kterým uživatel v organizaci nemůže udělit souhlas. Pouze správce adresáře může udělit souhlas aplikacím, které vyžadují tuto úroveň oprávnění. Když uživatel nebo správce souhlasí, je ve svém adresáři zaregistrováno pouze webové rozhraní API. 

## <a name="token-expiration"></a>Vypršení platnosti tokenu

Pokud nativní aplikace používá svůj autorizační kód k získání přístupového tokenu JWT, obdrží také obnovovací token JWT. Po vypršení platnosti přístupového tokenu se dá obnovovací token použít k opětovnému ověření uživatele, aniž by se museli znovu přihlašovat. Tento obnovovací token se pak použije k ověření uživatele, což vede k vytvoření nového přístupového tokenu a k aktualizaci tokenu.

## <a name="next-steps"></a>Další kroky

- Další informace o dalších [typech a scénářích aplikací](app-types.md)
- Seznamte se se [základy ověřování](v1-authentication-scenarios.md) Azure AD

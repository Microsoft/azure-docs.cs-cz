---
title: Nativní aplikace ve službě Azure Active Directory
description: Popisuje, co jsou nativní aplikace a základy toku protokolu, registrace a vypršení platnosti tokenu pro tento typ aplikace.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154793"
---
# <a name="native-apps"></a>Nativní aplikace

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Nativní aplikace jsou aplikace, které volají webové rozhraní API jménem uživatele. Tento scénář je postaven na typu udělení autorizačního kódu OAuth 2.0 s veřejným klientem, jak je popsáno v části 4.1 [specifikace OAuth 2.0](https://tools.ietf.org/html/rfc6749). Nativní aplikace získá přístupový token pro uživatele pomocí protokolu OAuth 2.0. Tento přístupový token je pak odeslán v požadavku na webové rozhraní API, které autorizuje uživatele a vrátí požadovaný prostředek.

## <a name="diagram"></a>Diagram

![Nativní aplikace pro diagram webového rozhraní API](./media/authentication-scenarios/native-app-to-web-api.png)

## <a name="protocol-flow"></a>Tok protokolu

Pokud používáte knihovny ověřování služby AD, většina podrobností protokolu popsaných níže jsou zpracovány za vás, jako je například vyskakovací okno prohlížeče, ukládání tokenů do mezipaměti a zpracování aktualizačních tokenů.

1. Pomocí místního okna prohlížeče nativní aplikace provede požadavek na koncový bod autorizace ve službě Azure AD. Tento požadavek zahrnuje ID aplikace a identifikátor URI přesměrování nativní aplikace, jak je znázorněno na portálu Azure, a identifikátor URI id aplikace pro webové rozhraní API. Pokud se uživatel ještě nepřihlásil, zobrazí se výzva k přihlášení znovu.
1. Azure AD ověřuje uživatele. Pokud se jedná o víceklientské aplikace a souhlas je nutné použít aplikaci, uživatel bude muset souhlas, pokud tak již neučinili. Po udělení souhlasu a po úspěšném ověření azure ad vydá odpověď autorizačního kódu zpět na identifikátor URI přesměrování klientské aplikace.
1. Když Azure AD vydá odpověď autorizačního kódu zpět na identifikátor URI přesměrování, klientská aplikace zastaví interakci prohlížeče a extrahuje autorizační kód z odpovědi. Pomocí tohoto autorizačního kódu odešle klientská aplikace požadavek na koncový bod tokenu služby Azure AD, který obsahuje autorizační kód, podrobnosti o klientské aplikaci (ID aplikace a identifikátor URI přesměrování) a požadovaný prostředek (Identifikátor URI aplikace pro rozhraní API).
1. Autorizační kód a informace o klientské aplikaci a webové rozhraní API jsou ověřeny službou Azure AD. Po úspěšném ověření Azure AD vrátí dva tokeny: přístupový token JWT a token aktualizace JWT. Kromě toho Azure AD vrátí základní informace o uživateli, jako je například jeho zobrazované jméno a ID klienta.
1. Přes protokol HTTPS klientská aplikace používá vrácený přístupový token JWT k přidání řetězce JWT s označením "Nosič" v hlavičce autorizace požadavku do webového rozhraní API. Webové rozhraní API pak ověří token JWT a pokud je ověření úspěšné, vrátí požadovaný prostředek.
1. Po vypršení platnosti přístupového tokenu se klientské aplikaci zobrazí chyba, která označuje, že se uživatel musí znovu ověřit. Pokud má aplikace platný obnovovací token, lze ji použít k získání nového přístupového tokenu bez výzvy k opětovnému přihlášení uživatele. Pokud vyprší platnost obnovovacího tokenu, aplikace bude muset znovu interaktivně ověřit uživatele.

> [!NOTE]
> Obnovovací token vydaný službou Azure AD lze použít pro přístup k více prostředkům. Například pokud máte klientskou aplikaci, která má oprávnění k volání dvou webových rozhraní API, obnovovací token lze použít k získání přístupového tokenu do jiného webového rozhraní API také.

## <a name="code-samples"></a>Ukázky kódů

Podívejte se na ukázky kódu pro nativní aplikace pro scénáře webového rozhraní API. A často kontrolujte – často přidáváme nové vzorky. [Nativní aplikace webového rozhraní API](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>Registrace aplikací

Pokud chcete zaregistrovat aplikaci s koncovým bodem Azure AD v1.0, [přečtěte si část Registrace aplikace](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Jeden tenant – nativní aplikace a webové rozhraní API musí být registrovány ve stejném adresáři ve službě Azure AD. Webové rozhraní API lze nakonfigurovat tak, aby zpřístupniovalo sadu oprávnění, která se používají k omezení přístupu nativní aplikace k jejím prostředkům. Klientská aplikace pak vybere požadovaná oprávnění z rozevírací nabídky Oprávnění k jiným aplikacím na webu Azure Portal.
* Víceklient – nejprve nativní aplikace, která je vždy registrována pouze v adresáři vývojáře nebo vydavatele. Za druhé, nativní aplikace je nakonfigurována tak, aby označovala oprávnění, která vyžaduje, aby byla funkční. Tento seznam požadovaných oprávnění je zobrazen v dialogovém okně, když uživatel nebo správce v cílovém adresáři udělí souhlas s aplikací, která ji zpřístupní jejich organizaci. Některé aplikace vyžadují pouze oprávnění na úrovni uživatele, se kterými může souhlasit každý uživatel v organizaci. Jiné aplikace vyžadují oprávnění na úrovni správce, s nimiž uživatel v organizaci nemůže souhlasit. Souhlas s aplikacemi, které vyžadují tuto úroveň oprávnění, může udělit souhlas pouze správce adresáře. Pokud uživatel nebo správce souhlasí, je v jejich adresáři registrováno pouze webové rozhraní API. 

## <a name="token-expiration"></a>Vypršení platnosti tokenu

Když nativní aplikace používá svůj autorizační kód k získání přístupového tokenu JWT, obdrží také obnovovací token JWT. Po vypršení platnosti přístupového tokenu lze obnovovací token použít k opětovnému ověření uživatele, aniž by bylo nutné, aby se znovu přihlásili. Tento obnovovací token se pak používá k ověření uživatele, což má za následek nový přístupový token a obnovovací token.

## <a name="next-steps"></a>Další kroky

- Další informace o dalších [typech aplikací a scénářích](app-types.md)
- Informace o [základech ověřování](v1-authentication-scenarios.md) Azure AD

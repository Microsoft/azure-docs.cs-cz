---
title: Rozhraní pro souhlas Azure AD
titleSuffix: Microsoft identity platform
description: Přečtěte si o rámci pro souhlas ve službě Azure Active Directory a o tom, jak usnadňuje vývoj webových a nativních klientských aplikací pro více klientů.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/30/2018
ms.author: ryanwi
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev
ms.openlocfilehash: cb9441e6ce19094ff72e902cdeea151041ceb963
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161122"
---
# <a name="azure-active-directory-consent-framework"></a>Rozhraní pro souhlas služby Azure Active Directory

Rozhraní pro souhlas Azure Active Directory (Azure AD) usnadňuje vývoj webových a nativních klientských aplikací pro více klientů. Tyto aplikace umožňují přihlášení podle uživatelských účtů z klienta Azure AD, který se liší od toho, kde je aplikace registrována. Mohou také potřebovat přístup k webovým rozhraním API, jako je rozhraní API Microsoft Graph (pro přístup k Azure AD, Intune a službám v Office 365) a další rozhraní API služeb Microsoftu, kromě vlastních webových rozhraní API.

Rámec je založen na uživateli nebo správci, který udělit souhlas s aplikací, která žádá o registraci ve svém adresáři, což může zahrnovat přístup k datům adresáře. Například pokud aplikace webového klienta potřebuje číst informace kalendáře o uživateli z Office 365, tento uživatel je povinen nejprve souhlasit s klientskou aplikací. Po udělení souhlasu bude klientská aplikace moci volat rozhraní Microsoft Graph API jménem uživatele a podle potřeby použít informace kalendáře. [Rozhraní Microsoft Graph API](https://developer.microsoft.com/graph) poskytuje přístup k datům v Office 365 (jako jsou kalendáře a zprávy z Exchange, weby a seznamy ze SharePointu, dokumenty z OneDrivu, poznámkové bloky z OneNotu, úkoly z Planneru a sešity z Excelu), stejně jako uživatelé a skupiny z Azure AD a další datové objekty z dalších cloudových služeb Microsoftu.

Rámec pro souhlas je postaven na OAuth 2.0 a jeho různé toky, jako je například udělení autorizačního kódu a udělení pověření klienta pomocí veřejných nebo důvěrných klientů. Pomocí OAuth 2.0, Azure AD umožňuje vytvářet mnoho různých typů klientských aplikací – například na telefonu, tabletu, serveru nebo webové aplikace – a získat přístup k požadovaným prostředkům.

Další informace o použití rámce pro souhlas s autorizačními granty OAuth2.0 najdete v tématu [Autorizace přístupu k webovým aplikacím pomocí scénářů OAuth 2.0 a Azure AD](v2-oauth2-auth-code-flow.md) and [Authentication pro Azure AD](authentication-scenarios.md). Informace o získání autorizovaného přístupu k Office 365 prostřednictvím Microsoft Graphu najdete v [tématu Ověřování aplikací pomocí Microsoft Graphu](https://developer.microsoft.com/graph/docs/authorization/auth_overview).

## <a name="consent-experience---an-example"></a>Zkušenosti se souhlasem – příklad

Následující kroky ukazují, jak funguje prostředí souhlasu pro vývojáře aplikace i uživatele.

1. Předpokládejme, že máte aplikaci webového klienta, která potřebuje požádat o konkrétní oprávnění pro přístup k prostředku nebo rozhraní API. Dozvíte se, jak tuto konfiguraci provést v další části, ale v podstatě portál Azure se používá k deklarování žádostí o oprávnění v době konfigurace. Stejně jako ostatní nastavení konfigurace se stanou součástí registrace Azure AD aplikace:

    ![Oprávnění k jiným aplikacím](./media/consent-framework/permissions.png)

1. Vezměte v úvahu, že oprávnění aplikace byla aktualizována, aplikace je spuštěna a uživatel se chystá použít poprvé. Nejprve aplikace potřebuje získat autorizační kód z `/authorize` koncového bodu Azure AD. Autorizační kód lze poté použít k získání nového přístupového a obnovovacího tokenu.

1. Pokud uživatel ještě není ověřen, `/authorize` koncový bod Azure AD vyzve uživatele k přihlášení.

    ![Přihlášení uživatele nebo správce ke službě Azure AD](./media/consent-framework/usersignin.png)

1. Po přihlášení uživatele Azure AD určí, pokud uživatel potřebuje zobrazit stránku souhlasu. Toto určení je založeno na tom, zda uživatel (nebo správce jejich organizace) již udělil souhlas aplikace. Pokud souhlas ještě nebyl udělen, Azure AD vyzve uživatele k souhlasu a zobrazí požadovaná oprávnění, která potřebuje k fungování. Sada oprávnění, která se zobrazí v dialogovém okně souhlas, odpovídá oprávněním vybraným v **delegovaných oprávněních** na webu Azure Portal.

    ![Zobrazuje příklad oprávnění zobrazených v dialogovém okně souhlasu.](./media/consent-framework/consent.png)

1. Poté, co uživatel udělí souhlas, autorizační kód je vrácena do vaší aplikace, která je uplatněna získat přístupový token a aktualizovat token. Další informace o tomto toku naleznete [v tématu OAuth 2.0 tok autorizačního kódu](v2-oauth2-auth-code-flow.md).

1. Jako správce můžete také souhlasit s delegovanými oprávněními aplikace jménem všech uživatelů ve vašem tenantovi. Souhlas správce zabrání zobrazení dialogového okna souhlasu pro každého uživatele v tenantovi a na [webu Azure Portal](https://portal.azure.com) to můžou dělat uživatelé s rolí správce. Informace o tom, které role správce mohou souhlasit s delegovanými oprávněními, najdete [v tématu Oprávnění rolí správce ve službě Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

    **Souhlas s delegovanými oprávněními aplikace**

   1. Přejděte na stránku **oprávnění rozhraní API** pro vaši aplikaci.
   1. Klikněte na tlačítko **Udělit souhlas správce.**

      ![Udělit oprávnění pro explicitní souhlas správce](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > Udělení výslovného souhlasu pomocí tlačítka **Udělit oprávnění** je aktuálně vyžadováno pro jednostránkové aplikace (SPA), které používají soubor ADAL.js. V opačném případě se aplikace nezdaří při požadavku na přístupový token.

## <a name="next-steps"></a>Další kroky

* Podívejte [se, jak převést aplikaci na víceklientské aplikace](howto-convert-app-to-be-multi-tenant.md)
* Pro větší hloubku se dozvíte, [jak je souhlas podporován ve vrstvě protokolu OAuth 2.0 během toku udělení autorizačního kódu.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)

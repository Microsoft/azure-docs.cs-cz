---
title: Rozhraní pro vyjádření souhlasu Azure AD
titleSuffix: Microsoft identity platform
description: Přečtěte si o tom, jak Azure Active Directory a jak usnadňuje vývoj webových a nativních klientských aplikací pro více tenantů.
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
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: e9780332ad6279deef63910c7e6ba95e1ccf43bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706128"
---
# <a name="azure-active-directory-consent-framework"></a>Architektura Azure AD pro udělování souhlasu

Rozhraní pro vyjádření souhlasu Azure Active Directory (Azure AD) usnadňuje vývoj webových a nativních klientských aplikací pro více tenantů. Tyto aplikace umožňují přihlášení pomocí uživatelských účtů z klienta Azure AD, které se liší od účtu, ve kterém je aplikace zaregistrovaná. Můžou taky potřebovat přístup k webovým rozhraním API, jako je Microsoft Graph API (pro přístup k Azure AD, Intune a službám v Microsoft 365) a dalších rozhraních API služeb Microsoftu, a to i s vašimi webovými rozhraními API.

Rozhraní je založené na uživateli nebo správci, kteří přistupují k aplikaci, která žádá o registraci ve svém adresáři, což může zahrnovat přístup k datům adresáře. Například pokud Webová klientská aplikace potřebuje číst informace o kalendáři uživatele z Microsoft 365, musí nejprve souhlasit s klientskou aplikací. Po přijetí souhlasu bude klientská aplikace moci volat rozhraní Microsoft Graph API jménem uživatele a podle potřeby použít informace v kalendáři. [Rozhraní Microsoft Graph API](https://developer.microsoft.com/graph) poskytuje přístup k datům v Microsoft 365 (jako jsou kalendáře a zprávy z Exchange, webů a seznamů ze SharePointu, dokumentů z OneDrivu, poznámkových bloků z OneNotu, úkolů z Planneru a sešitů z Excelu) a také uživatelů a skupin z Azure AD a dalších datových objektů z dalších cloudových služeb Microsoftu.

Rozhraní pro vyjádření souhlasu je postavené na OAuth 2,0 a jeho různých tocích, jako je udělení autorizačního kódu a udělení přihlašovacích údajů klienta, pomocí veřejných nebo důvěrných klientů. Díky použití OAuth 2,0 může Azure AD vytvořit mnoho různých typů klientských aplikací – například na telefonu, tabletu, serveru nebo webové aplikaci – a získat přístup k požadovaným prostředkům.

Další informace o používání souhlasu architektury s autorizačními stipendii OAuth 2.0 najdete v tématu [autorizace přístupu k webovým aplikacím pomocí OAuth 2,0 a Azure AD](v2-oauth2-auth-code-flow.md) a [scénářů ověřování pro Azure AD](./authentication-vs-authorization.md). Informace o tom, jak získat autorizovaný přístup k Microsoft 365 prostřednictvím Microsoft Graph, najdete v tématu [ověřování aplikací pomocí Microsoft Graph](/graph/).

## <a name="consent-experience---an-example"></a>Prostředí pro vyjádření souhlasu – příklad

Následující kroky ukazují, jak funguje souhlas pro vývojáře aplikací i pro uživatele.

1. Předpokládejme, že máte aplikaci webového klienta, která potřebuje požádat o konkrétní oprávnění pro přístup k prostředku nebo rozhraní API. Naučíte se, jak tuto konfiguraci provést v následující části, ale v podstatě Azure Portal se používá k deklaraci žádostí o oprávnění v době konfigurace. Podobně jako u jiných nastavení konfigurace se stanou součástí registrace Azure AD v aplikaci:

    ![Oprávnění k ostatním aplikacím](./media/consent-framework/permissions.png)

1. Vezměte v úvahu, že byla aktualizována oprávnění aplikace, aplikace je spuštěná a uživatel je bude v první době používat. Nejprve aplikace potřebuje získat autorizační kód z `/authorize` koncového bodu služby Azure AD. Autorizační kód pak můžete použít k získání nového přístupového a obnovovacího tokenu.

1. Pokud uživatel ještě není ověřený, `/authorize` koncový bod Azure AD vyzve uživatele, aby se přihlásil.

    ![Přihlášení uživatele nebo správce k Azure AD](./media/consent-framework/usersignin.png)

1. Až se uživatel přihlásí, Azure AD určí, jestli se uživatel musí zobrazit na stránce souhlasu. Toto rozhodnutí je založené na tom, jestli uživatel (nebo správce organizace) už udělil souhlas s aplikací. Pokud souhlas ještě nebyl udělen, Azure AD vyzve uživatele k vyjádření souhlasu a zobrazí požadovaná oprávnění, která potřebuje k tomu, aby fungovala. Sada oprávnění, která se zobrazí v dialogovém okně pro vyjádření souhlasu, se shodují s těmi vybranými v **delegovaných oprávněních** v Azure Portal.

    ![Zobrazuje příklad oprávnění zobrazených v dialogovém okně pro vyjádření souhlasu.](./media/consent-framework/consent.png)

1. Jakmile uživatel udělí souhlas, vrátí se do vaší aplikace autorizační kód, který se považuje za získání přístupového tokenu a aktualizačního tokenu. Další informace o tomto toku najdete v tématu [tok autorizačního kódu OAuth 2,0](v2-oauth2-auth-code-flow.md).

1. Jako správce můžete také vyjádřit souhlas s delegovanými oprávněními aplikace jménem všech uživatelů ve vašem tenantovi. Souhlas se správou brání tomu, aby se v dialogovém okně pro každého uživatele v tenantovi zobrazoval dialog a uživatel s rolí správce může provádět [Azure Portal](https://portal.azure.com) . Informace o tom, které role správce můžou souhlasit s delegovanými oprávněními, najdete v tématu [oprávnění role správce ve službě Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

    **Vyjádření souhlasu s delegovanými oprávněními aplikace**

   1. Přejít na stránku **oprávnění rozhraní API** pro vaši aplikaci
   1. Klikněte na tlačítko **udělení souhlasu správce** .

      ![Udělení oprávnění pro explicitní souhlas správce](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > Udělení explicitního souhlasu pomocí tlačítka **udělení oprávnění** je aktuálně vyžadováno pro jednostránkové aplikace (Spa), které používají ADAL.js. V opačném případě dojde k chybě aplikace po vyžádání přístupového tokenu.

## <a name="next-steps"></a>Další kroky

* Podívejte se, [Jak převést aplikaci na více tenantů](howto-convert-app-to-be-multi-tenant.md) .
* Podrobnější informace najdete [v tématu o tom, jak je podpora podporovaná na úrovni protokolu OAuth 2,0 během toku udělení autorizačního kódu.](../azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)
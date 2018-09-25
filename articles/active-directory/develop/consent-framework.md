---
title: Rozhraní pro udělování souhlasu Azure Active Directory
description: Další informace o rozhraní pro udělování souhlasu v Azure Active Directory a jak ho usnadňuje vývoj víceklientské webových a nativních klientských aplikací.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev
ms.openlocfilehash: b01cd43d1c38a9db50937a6cff9b416b8c6acd0e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946472"
---
# <a name="azure-active-directory-consent-framework"></a>Rozhraní pro udělování souhlasu Azure Active Directory

Rozhraní pro udělování souhlasu Azure Active Directory (Azure AD) umožňuje snadno vyvíjet více tenantů webových a nativních klientských aplikací. Tyto aplikace povolit přihlášení podle uživatelské účty z tenanta služby Azure AD, který je jiný než ten, ve kterém aplikace bude zaregistrovaná. Může také potřebují přístup k webové rozhraní API jiných služeb společnosti Microsoft, kromě vlastní webová rozhraní API a rozhraní API, jako je například rozhraní Microsoft Graph API (na přístup k Azure AD, Intune a službám Office 365).

Rozhraní je založené na uživatele nebo správce udělení souhlasu pro aplikaci, která požádá o registrovaný v jejich adresáře, který může zahrnovat přístup k datům adresáře. Například pokud webové klientská aplikace potřebuje číst kalendář informace o uživateli z Office 365, tento uživatel je muset vyjádřit souhlas s klientskou aplikaci nejdřív. Po souhlas, klientská aplikace bude moct volat rozhraní Microsoft Graph API jménem uživatele a podle potřeby použijte informace o kalendáři. [Microsoft Graph API](https://graph.microsoft.io) poskytuje přístup k datům v Office 365 (např. zprávy ze systému Exchange, webům a seznamům ze Sharepointu, dokumenty z Onedrivu, poznámkové bloky z Onenotu, z plánovače úloh a sešity a kalendáře Aplikace Excel), a také uživatelů a skupin ze služby Azure AD a dalších datových objektů z více cloudovým službám Microsoftu.

Rozhraní pro udělování souhlasu je postavená na OAuth 2.0 a jeho různé toky, jako je autorizační kód udělení a klientské přihlašovací údaje, pomocí veřejné nebo důvěrných klientů. S použitím OAuth 2.0, Azure AD umožňuje vytvořit mnoho různých typů klientských aplikací--& gt, jako na telefonu, tabletu, server nebo webové aplikace – a získat přístup k potřebným prostředkům.

Další informace o použití rozhraní pro udělování souhlasu s udělení autorizace OAuth 2.0 najdete v tématu [autorizaci přístupu k webovým aplikacím pomocí OAuth 2.0 a Azure AD](v1-protocols-oauth-code.md) a [scénáře ověřování pro službu Azure AD](authentication-scenarios.md). Informace o tom, jak autorizovaný přístup k Office 365 prostřednictvím Microsoft Graphu najdete v tématu [ověřování aplikací s Microsoft Graphem](https://graph.microsoft.io/docs/authorization/auth_overview).

## <a name="consent-experience---an-example"></a>Prostředí pro vyjádření souhlasu – příklad

Následující kroky ukazují, jak souhlasu prostředí funguje pro aplikace pro vývojáře a uživatele.

1. Předpokládejme, že máte webovou aplikaci klienta, která potřebuje požádat o určitá oprávnění pro přístup k prostředku nebo rozhraní API. Dozvíte jak provést tuto konfiguraci v další části, ale v podstatě na webu Azure portal slouží k deklaraci žádosti oprávnění v době konfigurace. Jako další nastavení konfigurace se stanou součástí registrace služby Azure AD aplikace:

  ![Oprávnění k ostatním aplikacím](./media/quickstart-v1-integrate-apps-with-azure-ad/requiredpermissions.png)

1. Vezměte v úvahu, že oprávnění aplikace se aktualizovaly, je aplikace spuštěna a uživatele je použití poprvé. Nejprve je potřeba získat autorizační kód z Azure AD aplikace `/authorize` koncového bodu. Autorizační kód lze potom získat nový přístupový a obnovovací token.

1. Pokud uživatel ještě není ověřený, Azure AD `/authorize` koncový bod se zobrazí výzva k přihlášení.

  ![Uživatel nebo správce přihlášení ke službě Azure AD](./media/quickstart-v1-integrate-apps-with-azure-ad/usersignin.png)

1. Až se uživatel přihlásil, určí Azure AD, pokud uživatel musí zobrazit stránka pro odsouhlasení podmínek. Toto rozhodnutí je založená na, jestli uživatel (nebo správce ve vaší organizaci) už udělené souhlasu s aplikací. Pokud ještě nebyl udělený souhlas, Azure AD zobrazí výzvu k souhlasu a zobrazí požadovaná oprávnění, je potřeba pracovat. Sada oprávnění, která se zobrazí v dialogovém okně souhlasu odpovídat vybraných v **delegovaná oprávnění** na webu Azure Portal.

  ![Prostředí pro vyjádření souhlasu uživatele](./media/quickstart-v1-integrate-apps-with-azure-ad/consent.png)

1. Jakmile uživatel udělí svůj souhlas, se vrátí autorizační kód do vaší aplikace, který je k získání přístupového tokenu a obnovovací token uplatnit. Další informace o tomto toku, najdete v části [typ aplikace webového rozhraní API](web-api.md).

1. Jako správce může také souhlas delegovaná oprávnění aplikací schválit za všechny uživatele ve vašem tenantovi. Souhlas správce zabraňuje dialogové okno souhlasu zobrazilo pro každého uživatele v tenantovi a to provést [webu Azure portal](https://portal.azure.com) uživatelé s rolí správce. Z **nastavení** stránky pro vaši aplikaci, vyberte **požadovaná oprávnění** a klikněte na **udělit oprávnění** tlačítko.

  ![Udělení oprávnění pro vyjádření souhlasu explicitní správce](./media/quickstart-v1-integrate-apps-with-azure-ad/grantpermissions.png)

  > [!IMPORTANT]
  > Explicitní udělení souhlasu pomocí **udělit oprávnění** je aktuálně vyžaduje pro jednostránkové aplikace (SPA), které používají ADAL.js tlačítko. V opačném případě aplikace selže při vyžádání tokenu přístupu.

## <a name="next-steps"></a>Další postup

* Zobrazit [jak převést aplikaci na více tenantů](howto-convert-app-to-be-multi-tenant.md)
* Větší hloubky, přečtěte si [jak souhlasu se podporuje ve vrstvě protokolu OAuth 2.0 během toku přidělení kódu autorizace.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)

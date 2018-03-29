---
title: 'Azure AD Connect: Bezproblémové jednotné přihlašování | Microsoft Docs'
description: Toto téma popisuje Azure Active Directory (Azure AD) bezproblémové jednotné přihlašování a jak ji umožňuje poskytovat true jednotné přihlašování pro podnikové ploše uživatele uvnitř firemní sítě.
services: active-directory
keywords: Co je Azure AD Connect, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: billmath
ms.openlocfilehash: b1c82727e97b85fae5f315ceb1cd79cfdd111b45
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory bezproblémové jednotné přihlašování

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Co je Azure Active Directory bezproblémové jednotné přihlašování?

Azure Active Directory bezproblémové jednotné přihlašování (Azure AD bezproblémové jednotné přihlašování) automaticky přihlásí uživatele v když jsou v jejich podnikové zařízení připojená k podnikové síti. Když je povolené, uživatelé nepotřebují ve svých hesel a přihlaste se k Azure AD a obvykle i typu v jejich uživatelských jmen. Tato funkce poskytuje uživatelům snadný přístup k vaší cloudové aplikace bez nutnosti jakékoli další místní komponenty.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Bezproblémové jednotného přihlašování je možné kombinovat s buď [synchronizaci hodnoty Hash hesla](active-directory-aadconnectsync-implement-password-synchronization.md) nebo [předávací ověřování](active-directory-aadconnect-pass-through-authentication.md) metody přihlašování.

![Bezproblémové jednotné přihlašování](./media/active-directory-aadconnect-sso/sso1.png)

>[!IMPORTANT]
>Bezproblémové jednotného přihlašování je _není_ použít k Active Directory Federation Services (ADFS).

## <a name="key-benefits"></a>Klíčové výhody

- *Vysoký výkon uživatele*
  - Uživatelé jsou automaticky přihlášeni k místní i cloudové aplikace.
  - Uživatelé nemusí zadávat opakovaně jejich hesla.
- *Snadné nasazení a správě*
  - Žádné další součásti potřebné místní správnou.
  - Funguje s jakékoli metody objektu cloudové ověřování - [synchronizaci hodnoty Hash hesla](active-directory-aadconnectsync-implement-password-synchronization.md) nebo [předávací ověřování](active-directory-aadconnect-pass-through-authentication.md).
  - Můžete se vrátit na některých nebo všech uživatelů pomocí zásad skupiny.
  - Registrace zařízení s Windows 10 s Azure AD bez potřeby jakékoliv infrastruktury služby AD FS. Tato funkce musí používat verze 2.1 nebo novější [připojení k pracovišti klienta](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Označuje funkce

- Přihlašovací uživatelské jméno může být buď místní výchozí uživatelské jméno (`userPrincipalName`) nebo jiný atribut, které jsou nakonfigurované v Azure AD Connect (`Alternate ID`). Pracovní případech i použít, protože používá bezproblémové jednotného přihlašování `securityIdentifier` deklarací identity v lístku protokolu Kerberos k vyhledání odpovídající objekt uživatele ve službě Azure AD.
- Bezproblémové jednotného přihlašování je oportunistické funkce. Pokud z nějakého důvodu selže, přihlašování uživatel přejde zpět regulární chování – tj, uživatel musí zadat svoje heslo na přihlašovací stránce.
- Pokud aplikace (například https://myapps.microsoft.com/contoso.com) předává `domain_hint` (OpenID Connect) nebo `whr` (SAML) parametr - identifikaci vašeho klienta nebo `login_hint` parametr - identifikace uživatele v Azure AD přihlášení požadavku, jsou uživatelé přihlášení se automaticky bez je vstup uživatelská jména a hesla.
- Uživatelé také získají tichou prostředí přihlašování, když aplikace (například https://contoso.sharepoint.com) odešle žádostí o přihlášení k Azure AD nevyužívá dělené tabulky koncové body – to znamená, https://login.microsoftonline.com/contoso.com/<..> nebo https://login.microsoftonline.com/<tenant_ID>/<..> – místo Azure AD společný koncový bod - tedy https://login.microsoftonline.com/common/<...>.
- Odhlásit se podporuje. To umožňuje uživatelům si vybrat jiný účet služby Azure AD přihlásit, místo se automaticky přihlásíte pomocí jednotného přihlašování bezproblémové automaticky.
- Klienti Office 365 (16.0.8730.xxxx a vyšší) jsou podporované pomocí neinteraktivní toku.
- Může být povoleno přes Azure AD Connect.
- Je bezplatné funkce a nepotřebujete žádné placené edice Azure AD pro použití.
- Je podporováno v webovými klienty založené na prohlížeči a klienti Office, které podporují [moderní ověřování](https://aka.ms/modernauthga) na platformách a prohlížeče podporující ověřování protokolu Kerberos:

| OS\Browser |Internet Explorer|Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Ano|Ne|Ano|Ano\*|neuvedeno
|Windows 8.1|Ano|neuvedeno|Ano|Ano\*|neuvedeno
|Windows 8|Ano|neuvedeno|Ano|Ano\*|neuvedeno
|Windows 7|Ano|neuvedeno|Ano|Ano\*|neuvedeno
|Mac OS X|neuvedeno|neuvedeno|Ano\*|Ano\*|Ano\*

\*Vyžaduje [další konfigurace](active-directory-aadconnect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Pro Windows 10, doporučuje se použít [Azure AD Join](../active-directory-azureadjoin-overview.md) zajistit optimální jeden přihlašování v práci s Azure AD.

## <a name="next-steps"></a>Další postup

- [**Rychlý Start** ](active-directory-aadconnect-sso-quick-start.md) – zprovoznění a systémem Azure bezproblémové jednotného přihlašování k AD.
- [**Podrobné technické informace** ](active-directory-aadconnect-sso-how-it-works.md) -pochopit, jak tato funkce funguje.
- [**Nejčastější dotazy** ](active-directory-aadconnect-sso-faq.md) -odpovědi na nejčastější dotazy.
- [**Řešení potíží s** ](active-directory-aadconnect-troubleshoot-sso.md) – zjistěte, jak řešit obvyklé problémy s funkcí.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – pro vyplnění žádosti o nové funkce.

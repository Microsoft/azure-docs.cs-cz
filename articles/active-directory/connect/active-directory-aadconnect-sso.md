---
title: 'Azure AD Connect: Bezproblémové jednotné přihlašování | Dokumentace Microsoftu'
description: Toto téma popisuje Azure Active Directory (Azure AD) bezproblémové jednotné přihlašování a jak umožňuje poskytovat true jednotného přihlašování pro uživatele firemních stolních počítačů v podnikové síti.
services: active-directory
keywords: Co je Azure AD Connect, instalace Active Directory, požadované součásti pro službu Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 41e75fcfd0b88d5c37bb8dd6fcc16b1767b34dba
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285353"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory bezproblémové jednotné přihlašování

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Co je Azure Active Directory bezproblémové jednotné přihlašování?

Azure Active Directory bezproblémové jednotné přihlašování (Azure AD bezproblémové jednotné přihlašování) automaticky přihlásí uživatele v případě, že nejsou na firemní zařízení připojených k podnikové síti. Při povolení uživatele není potřeba zadejte své heslo k přihlášení do služby Azure AD a obvykle, dokonce i ve svých uživatelských jmen zadejte. Tato funkce poskytuje uživatelům snadný přístup k vaší cloudové aplikace bez nutnosti jakékoli další místní komponenty.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Bezproblémové jednotné přihlašování je možné kombinovat s buď [synchronizaci hodnot Hash hesel](active-directory-aadconnectsync-implement-password-hash-synchronization.md) nebo [předávací ověřování](active-directory-aadconnect-pass-through-authentication.md) metod přihlašování. Bezproblémové jednotné přihlašování je _není_ vztahuje k Active Directory Federation Services (ADFS).

![Bezproblémové jednotné přihlašování](./media/active-directory-aadconnect-sso/sso1.png)

>[!IMPORTANT]
>Bezproblémové jednotné přihlašování potřebuje zařízení uživatele bude **připojených k doméně**, ale nemusí pro zařízení, které bude [připojeno k Azure AD](../active-directory-azureadjoin-overview.md).

## <a name="key-benefits"></a>Klíčové výhody

- *Skvělé uživatelské prostředí*
  - Uživatelé jsou automaticky přihlášeni k místním i cloudovým aplikacím.
  - Uživatelé nemají opakovaně zadávat hesla.
- *Snadné nasazení a Správa*
  - Žádné další komponenty potřebné v místním provést tuto práci.
  - Funguje s jakoukoli metodou ověřování cloudu – [synchronizaci hodnot Hash hesel](active-directory-aadconnectsync-implement-password-hash-synchronization.md) nebo [předávací ověřování](active-directory-aadconnect-pass-through-authentication.md).
  - Jde vydávat na některé nebo všechny uživatele pomocí zásad skupiny.
  - Registrace zařízení s Windows 10 v Azure AD bez potřeby jakékoli infrastruktury služby AD FS. Tato funkce vyžaduje, abyste použijte verzi 2.1 nebo novější [klientských připojení k pracovní ploše](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Stručný přehled funkcí

- Přihlašovací uživatelské jméno může být buď místní výchozí uživatelské jméno (`userPrincipalName`) nebo jiný atribut nakonfigurované ve službě Azure AD Connect (`Alternate ID`). Oba použít případy práce, protože používá bezproblémového jednotného přihlašování `securityIdentifier` deklarací identity v lístku protokolu Kerberos k vyhledání odpovídajícího objektu uživatele ve službě Azure AD.
- Bezproblémové jednotné přihlašování je zaměřený na příležitosti funkce. Pokud z nějakého důvodu selže, přihlášení uživatele se vrátí do běžného chování – tj, uživatel musí zadat heslo na přihlašovací stránku.
- Pokud manifest aplikace (například https://myapps.microsoft.com/contoso.com) předává `domain_hint` (OpenID Connect) nebo `whr` (SAML) parametr - identifikaci vašeho tenanta, nebo `login_hint` parametr - ne k identifikaci uživatelů, v jeho služby Azure AD žádost o přihlášení, jsou uživatelé přihlášení se automaticky bez jejich zadávání uživatelských jmen a hesel.
- Uživatelé získají bezobslužné možnosti přihlašování také, pokud manifest aplikace (například https://contoso.sharepoint.com) odešle žádostí o přihlášení k Azure AD bezproblémovým koncové body – to znamená, https://login.microsoftonline.com/contoso.com/<..> nebo https://login.microsoftonline.com/<tenant_ID>/<..> – místo pro běžné koncového bodu Azure AD – to znamená, https://login.microsoftonline.com/common/<...>.
- Odhlaste se podporuje. To umožňuje uživatelům si vybrat jiný účet služby Azure AD se přihlásit pomocí, namísto automaticky Probíhá přihlašování pomocí bezproblémového jednotného přihlašování automaticky.
- Klienty Office 365 Win32 (Outlook, Word, Excel a jiné) s verze 16.0.8730.xxxx a vyšší jsou podporovány při použití toku jako neinteraktivní. Pro OneDrive, budete muset aktivovat [Onedrivu bezobslužné konfiguraci funkce](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) bezobslužné možnosti přihlašování.
- Je možné povolit prostřednictvím služby Azure AD Connect.
- Je funkci a není nutné žádné placené edice Azure AD, aby ho použít.
- Podporováno od webovými klienty založené na prohlížeči a klienti Office, které podporují [moderní ověřování](https://aka.ms/modernauthga) na platformy a prohlížeče podporující ověřování protokolu Kerberos:

| OS\Browser |Internet Explorer|Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Ano|Ne|Ano|Ano\*|neuvedeno
|Windows 8.1|Ano|neuvedeno|Ano|Ano\*|neuvedeno
|Windows 8|Ano|neuvedeno|Ano|Ano\*|neuvedeno
|Windows 7|Ano|neuvedeno|Ano|Ano\*|neuvedeno
|Mac OS X|neuvedeno|neuvedeno|Ano\*|Ano\*|Ano\*

\*Vyžaduje [další konfigurace](active-directory-aadconnect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Pro Windows 10, doporučuje se použít [Azure AD Join](../active-directory-azureadjoin-overview.md) zajistit optimální jednotné přihlašování práci s Azure AD.

## <a name="next-steps"></a>Další postup

- [**Rychlý Start** ](active-directory-aadconnect-sso-quick-start.md) – rychle zprovoznit a systémem Azure bezproblémového jednotného přihlašování AD.
- [**Plán nasazení** ](https://aka.ms/AuthenticationDeploymentPlan) – podrobný postup nasazení plánu.
- [**Podrobné technické informace** ](active-directory-aadconnect-sso-how-it-works.md) -pochopit, jak tato funkce funguje.
- [**Nejčastější dotazy** ](active-directory-aadconnect-sso-faq.md) – odpovědi na nejčastější dotazy.
- [**Řešení potíží s** ](active-directory-aadconnect-troubleshoot-sso.md) – zjistěte, jak vyřešit běžné problémy s funkcí.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – k podání žádostí o nové funkce.


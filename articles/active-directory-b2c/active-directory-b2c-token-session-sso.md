---
title: Relace a jednotné přihlašování – konfigurace – Azure Active Directory B2C | Dokumentace Microsoftu
description: Relace a konfigurace jednotného přihlašování v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 674a20fc96cf5b86219222d746525a3559ae9d09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60315775"
---
# <a name="session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Relace a konfigurace jednotného přihlašování v Azure Active Directory B2C

Tato funkce umožňuje přesné řízení, na [tok jednotlivých uživatelů](active-directory-b2c-reference-policies.md), sady:

- Doby života relace webové aplikace spravované službou Azure AD B2C.
- Jednotné přihlašování (SSO) chování napříč více aplikacemi a toky uživatelů ve vašem tenantovi Azure AD B2C.

## <a name="session-behavior"></a>Chování relace

Azure AD B2C podporuje [ověřovacího protokolu OpenID Connect](active-directory-b2c-reference-oidc.md) pro povolení zabezpečené přihlašování k webovým aplikacím. Chcete-li spravovat relace webové aplikace můžete použít následující vlastnosti:

- **Webová aplikace životnost relace (minuty)** – dobu života souboru cookie relace Azure AD B2C uloženého v prohlížeči uživatele po úspěšném ověření.
    - Výchozí = 1 440 minut.
    - Minimální (včetně) = 15 minut.
    - Maximální (včetně) = 1 440 minut.
- **Časový limit relace webové aplikace** – Pokud je tento přepínač nastavený **absolutní**, že uživatel musí k ověření znovu po době určené **webové aplikace životnost relace (minuty)** uplyne. Pokud tento přepínač nastavený **Hromadná** (výchozí nastavení), uživatel zůstane přihlášený, za předpokladu, uživatel je neustále aktivní ve webové aplikaci.

Tyto případy použití jsou povolené použití těchto vlastností:

- Splnění požadavků na váš obor zabezpečení a dodržování předpisů tím, že nastavíte relace webové aplikace životnosti.
- Vynutit ověřování po stanovené časové období, během interakce uživatele s vysokou úrovní zabezpečení součást webové aplikace. 

Tato nastavení nejsou k dispozici pro toky uživatelů pro resetování hesla.

## <a name="single-sign-on-sso-configuration"></a>Konfigurace jednotného přihlašování (SSO)

Pokud máte více aplikacemi a toky uživatelů ve vašem tenantovi B2C, můžete spravovat uživatelské interakce mezi nimi pomocí **Konfigurace jednotného přihlašování** vlastnost. Vlastnost můžete nastavit na jedno z následujících nastavení:

- **Tenant** – toto nastavení je výchozí nastavení. Použití toto nastavení umožňuje více aplikací a uživatel toků ve vašem tenantovi B2C sdílet stejné uživatelské relaci. Například Jakmile se uživatel přihlásí do aplikace, uživatel může také bezproblémově přihlášení do jiného jeden, farmacie Contoso při přístupu k jeho.
- **Aplikace** – toto nastavení umožňuje udržovat relaci uživatele výhradně pro aplikaci, nezávisle na jiných aplikací. Například pokud chcete uživateli umožní přihlásit k farmacie Contoso (pomocí stejných přihlašovacích údajů), i když už je uživatel přihlášený do nákupního Contoso, jiná aplikace na stejném B2C tenanta. 
- **Zásady** – toto nastavení umožňuje udržovat relaci uživatele výhradně pro tok uživatele, nezávisle na aplikace, které ji používají. Například pokud má uživatel již přihlášení a dokončení kroku více faktoru ověřování (MFA), uživatel může dostat přístup k různým částem vyšší zabezpečení více aplikací tak dlouho, dokud platnost pasu nevyprší relace vázané na tok uživatele.
- **Zakázané** – toto nastavení přinutí spustit prostřednictvím toku celé uživatelské na každé provedení zásad.

Tato nastavení nejsou k dispozici pro toky uživatelů pro resetování hesla. 


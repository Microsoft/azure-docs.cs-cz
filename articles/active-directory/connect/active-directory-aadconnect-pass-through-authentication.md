---
title: 'Azure AD Connect: Předávací ověřování | Microsoft Docs'
description: Tento článek popisuje předávací ověřování Azure Active Directory (Azure AD) a jak umožňuje Azure AD přihlášení pomocí ověřování hesla uživatelů pro místní službu Active Directory.
services: active-directory
keywords: Co je Azure AD Connect předávací ověřování, nainstalovat službu Active Directory, požadovaných součástí pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 9b36014057eb1713b2b056cd203a099c59d9b5d4
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031766"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Přihlášení uživatele pomocí ověřování Azure Active Directory průchozí

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Co je Azure Active Directory předávací ověřování?

Předávací ověřování Azure Active Directory (Azure AD) umožňuje uživatelům přihlásit se k místní i cloudové aplikace přihlašovali stejnými hesly. Tato funkce nabízí uživatelům lepší zkušenosti – jeden menší heslo k mějte na paměti a snižuje náklady na IT helpdesk, protože jsou vaši uživatelé méně pravděpodobné, že zapomenete jak se přihlásit. Když se uživatelé přihlašují pomocí služby Azure AD, ověří tato funkce hesla uživatelů přímo pro vaše místní službu Active Directory.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Tato funkce je alternativa k [synchronizaci hodnoty Hash hesel služby Azure AD](active-directory-aadconnectsync-implement-password-hash-synchronization.md), který poskytuje stejné výhodou cloudové ověřování pro organizace. Zásady zabezpečení a dodržování předpisů v některých organizacích však není povolit tyto organizace k odeslání hesla uživatelů i ve formuláři hash mimo jejich interní hranice. Předávací ověřování je to správné řešení pro takový organizace.

![Azure AD předávací ověřování](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

Zkombinováním předávacího ověřování s [bezproblémové jednotné přihlašování](active-directory-aadconnect-sso.md) funkce. Tímto způsobem, když uživatelé přistupují k aplikace na jejich podnikové počítačů ve vaší podnikové síti nepotřebují k zadání hesla k přihlášení.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Hlavní výhody použití Azure AD předávací ověřování

- *Vysoký výkon uživatele*
  - Uživatelé používat stejné hesla pro přihlášení do místní i cloudové aplikace.
  - Uživatelé stráví méně času rozhovoru s IT helpdesk řešení související s hesly problémy.
  - Uživatelé mohou dokončit [hesla pomocí samoobslužné služby správy](../authentication/active-directory-passwords-overview.md) úloh v cloudu.
- *Snadné nasazení a správě*
  - Není nutné komplexní místní nasazení nebo konfiguraci sítě.
  - Musí právě lightweight agenta být nainstalované na místě.
  - Bez režie na správu. Agent automaticky obdrží vylepšení a opravy chyb.
- *Zabezpečení*
  - Místních hesel nikdy ukládají v cloudu v žádný formulář.
  - Agent umožní pouze odchozí připojení z vaší sítě. Proto je potřeba nainstalovat agenta v hraniční síti, označované také jako zóna DMZ.
  - Chrání vaše uživatelské účty ve spolupráci s bezproblémově [zásady Azure AD podmíněného přístupu](../active-directory-conditional-access-azure-portal.md), včetně vícefaktorového ověřování (MFA) a nástrojem [filtrování útoky hrubou silou heslo](../authentication/howto-password-smart-lockout.md).
- *Vysoce dostupný*
  - Další agenty lze nainstalovat na více serverech místně pro zajištění vysoké dostupnosti žádostí o přihlášení.

## <a name="feature-highlights"></a>Označuje funkce

- Podporuje přihlášení uživatele do všech webových aplikací založené na prohlížeči a do klientské aplikace Microsoft Office, které používají [moderní ověřování](https://aka.ms/modernauthga).
- Uživatelská jména přihlášení může být buď místní výchozí uživatelské jméno (`userPrincipalName`) nebo jiný atribut, které jsou nakonfigurované v Azure AD Connect (označované jako `Alternate ID`).
- Funkci bezproblémově pracuje s [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md) funkce jako je například aplikace Multi-Factor Authentication (MFA) pro zajištění vaši uživatelé.
- Integrovat cloudové [hesla pomocí samoobslužné služby správy](../authentication/active-directory-passwords-overview.md), včetně zpětný zápis hesla pro místní služby Active Directory a ochrana heslem pomocí zakazování nejčastěji používaných hesel.
- Prostředí s více doménovými strukturami jsou podporovány, pokud existují vztahy důvěryhodnosti doménové struktury mezi doménovými strukturami vaší AD a v případě směrování přípon názvů je správně nakonfigurován.
- Je bezplatné funkce a nepotřebujete žádné placené edice Azure AD pro použití.
- Může být povoleno prostřednictvím [Azure AD Connect](active-directory-aadconnect.md).
- Používá lightweight místní agent, který naslouchá a reaguje na požadavky na ověření hesla.
- Instalace více agentů poskytuje vysokou dostupnost žádostí o přihlášení.
- Ho [chrání](../authentication/howto-password-smart-lockout.md) vaše místní účty před hrubou vynutit heslo útoky v cloudu.

## <a name="next-steps"></a>Další postup

- [**Rychlý Start** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) – zprovoznění a systémem Azure AD předávací ověřování.
- [**Inteligentní uzamčení** ](../authentication/howto-password-smart-lockout.md) -možnost konfigurace inteligentní uzamčení na vašeho klienta k ochraně uživatelské účty.
- [**Aktuální omezení** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) – zjistěte, jaké postupy se podporují, a ty, které nejsou.
- [**Podrobné technické informace** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) -pochopit, jak tato funkce funguje.
- [**Nejčastější dotazy** ](active-directory-aadconnect-pass-through-authentication-faq.md) -odpovědi na nejčastější dotazy.
- [**Řešení potíží s** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – zjistěte, jak řešit obvyklé problémy s funkcí.
- [**Podrobné informace zabezpečení** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) -další hluboké technické informace o funkci.
- [**Azure AD bezproblémové SSO** ](active-directory-aadconnect-sso.md) -Další informace o této funkci vzájemně doplňují.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – pro vyplnění žádosti o nové funkce.

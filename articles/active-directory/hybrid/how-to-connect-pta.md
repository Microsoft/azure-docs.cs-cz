---
title: 'Azure AD Connect: předávací ověřování | Microsoft Docs'
description: Tento článek popisuje předávací ověřování v Azure Active Directory (Azure AD) a to, jak umožňuje přihlášení k Azure AD pomocí ověřování hesel uživatelů proti místní službě Active Directory.
services: active-directory
keywords: Co je Azure AD Connect předávací ověřování, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b8f613cb7c75d9dd6af1fcf62f9d484398072c6
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89279461"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Přihlašování uživatelů s využitím předávacího ověřování služby Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Co je Azure Active Directory předávací ověřování?

Předávací ověřování Azure Active Directory (Azure AD) umožňuje uživatelům přihlašovat se pomocí stejných hesel k místním i cloudovým aplikacím. Tato funkce zlepšuje zkušenosti uživatelů, protože si nemusejí pamatovat další heslo, a snižuje náklady na helpdesk IT, protože se snižuje pravděpodobnost, že uživatelé zapomenou, jak se přihlásit. Když se uživatelé přihlásí pomocí Azure AD, tato funkce ověří hesla uživatelů přímo proti místní službě Active Directory.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Tato funkce je alternativou k [synchronizaci hodnot hash hesel služby Azure AD](how-to-connect-password-hash-synchronization.md), která poskytuje stejné výhody cloudového ověřování pro organizace. Nicméně některé organizace, které chtějí vyhovět místním zásadám zabezpečení a hesla služby Active Directory, se můžou rozhodnout použít místo toho předávací ověřování. Přečtěte si [tuto příručku](./choose-ad-authn.md) , kde najdete porovnání různých metod přihlášení do služby Azure AD a jak zvolit správnou metodu přihlašování pro vaši organizaci.

![Předávací ověřování Azure AD](./media/how-to-connect-pta/pta1.png)

Předávací ověřování můžete kombinovat s funkcí [snadného jednotného přihlašování](how-to-connect-sso.md) . Když uživatelé přistupují k aplikacím na svých firemních počítačích v podnikové síti, nemusejí zadávat hesla pro přihlášení.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Klíčové výhody použití předávacího ověřování Azure AD

- *Skvělé uživatelské prostředí*
  - Uživatelé používají stejná hesla pro přihlášení k místním i cloudovým aplikacím.
  - Uživatelé stráví méně času rozhovorem IT helpdesku při řešení potíží souvisejících s heslem.
  - Uživatelé můžou dokončit [samoobslužné úlohy správy hesel](../authentication/concept-sspr-howitworks.md) v cloudu.
- *Snadné nasazení & spravovat*
  - Není potřeba složitá místní nasazení nebo konfigurace sítě.
  - Potřebuje jenom odlehčeného agenta, který se má nainstalovat místně.
  - Žádná režie správy. Agent automaticky přijímá vylepšení a opravy chyb.
- *Zabezpečené*
  - Místní hesla se v jakémkoli formuláři nikdy neukládají v cloudu.
  - Chrání vaše uživatelské účty pomocí [zásad podmíněného přístupu Azure AD](../conditional-access/overview.md), včetně Multi-Factor Authentication (MFA), [blokováním staršího ověřování](../conditional-access/concept-conditional-access-conditions.md) a [filtrováním útoků hrubou silou na hesla](../authentication/howto-password-smart-lockout.md).
  - Agent zpřístupňuje jenom odchozí připojení z vaší sítě. Proto neexistuje žádný požadavek na instalaci agenta do hraniční sítě, označované také jako DMZ.
  - Komunikace mezi agentem a službou Azure AD je zabezpečená pomocí ověřování založeného na certifikátech. Tyto certifikáty se v Azure AD automaticky Obnovují každých několik měsíců.
- *Vysoce dostupné*
  - Další agenty je možné nainstalovat na několik místních serverů, aby se zajistila vysoká dostupnost žádostí o přihlášení.

## <a name="feature-highlights"></a>Zvýraznění funkcí

- Podporuje přihlášení uživatele do všech aplikací založených na webovém prohlížeči a do systém Microsoft Office klientských aplikací, které používají [moderní ověřování](https://aka.ms/modernauthga).
- Přihlašovací jména uživatelů můžou být místní výchozí uživatelské jméno ( `userPrincipalName` ) nebo jiný atribut nakonfigurovaný v Azure AD Connect (známý jako `Alternate ID` ).
- Tato funkce bezproblémově funguje s funkcemi [podmíněného přístupu](../conditional-access/overview.md) , jako je Multi-Factor Authentication (MFA), které vám pomůžou zabezpečit uživatele.
- Je integrovaná s cloudovou [samoobslužnou správou hesel](../authentication/concept-sspr-howitworks.md), včetně zpětného zápisu hesla do místní služby Active Directory a ochrany heslem, a to tak, že se hesla běžně používají.
- Prostředí s více doménovými strukturami se podporují, pokud mezi doménovými strukturami služby AD existují vztahy důvěryhodnosti doménové struktury a pokud je Směrování přípon názvů správně nakonfigurované.
- Je to bezplatná funkce a nepotřebujete žádné placené edice Azure AD, abyste ji mohli používat.
- Dá se povolit prostřednictvím [Azure AD Connect](whatis-hybrid-identity.md).
- Používá odlehčeného místního agenta, který naslouchá a reaguje na požadavky na ověření hesla.
- Instalace více agentů poskytuje vysokou dostupnost žádostí o přihlášení.
- [Chrání](../authentication/howto-password-smart-lockout.md) místní účty proti útokům prostřednictvím hesla hrubou silou v cloudu.

## <a name="next-steps"></a>Další kroky

- [Rychlý Start](how-to-connect-pta-quick-start.md) – Získejte a spusťte předávací ověřování Azure AD.
- [Migrace z AD FS na předávací ověřování](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) – podrobný průvodce migrací z AD FS (nebo jiných federačních technologií) na předávací ověřování.
- [Inteligentní uzamčení](../authentication/howto-password-smart-lockout.md) – umožňuje nakonfigurovat funkci inteligentního uzamčení pro vašeho tenanta, aby se chránily uživatelské účty.
- [Aktuální omezení](how-to-connect-pta-current-limitations.md) – Zjistěte, které scénáře jsou podporovány a které nejsou.
- [Technický obsáhlý podrobně](how-to-connect-pta-how-it-works.md) – pochopení, jak tato funkce funguje.
- [Nejčastější dotazy](how-to-connect-pta-faq.md) – odpovědi na nejčastější dotazy
- [Řešení potíží](tshoot-connect-pass-through-authentication.md) – Naučte se řešit běžné problémy s touto funkcí.
- [Podrobně zabezpečení](how-to-connect-pta-security-deep-dive.md) – další podrobné technické informace o této funkci.
- [Bezproblémové jednotné přihlašování Azure AD](how-to-connect-sso.md) – Přečtěte si další informace o této doplňkové funkci.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – pro nové žádosti o funkce
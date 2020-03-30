---
title: 'Azure AD Connect: Předávací ověřování | Dokumenty společnosti Microsoft'
description: Tento článek popisuje předávací ověřování služby Azure Active Directory (Azure AD) a jak umožňuje přihlášení azure ad ověřením hesel uživatelů proti místnímu službě Active Directory.
services: active-directory
keywords: co je předávací ověřování Azure AD Connect, instalace služby Active Directory, požadované součásti pro Azure AD, Jednotné přihlašování, Jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4b52a3025bfb15e2679709353cebf28254a75c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185506"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Přihlašování uživatelů s využitím předávacího ověřování služby Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Co je předávací ověřování služby Azure Active Directory?

Předávací ověřování Azure Active Directory (Azure AD) umožňuje uživatelům přihlásit se k místním i cloudovým aplikacím pomocí stejných hesel. Tato funkce zlepšuje zkušenosti uživatelů, protože si nemusejí pamatovat další heslo, a snižuje náklady na helpdesk IT, protože se snižuje pravděpodobnost, že uživatelé zapomenou, jak se přihlásit. Když se uživatelé přihlašují pomocí služby Azure AD, tato funkce ověřuje hesla uživatelů přímo proti vaší místní službě Active Directory.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Tato funkce je alternativou k [synchronizaci hash hesel Azure AD](how-to-connect-password-hash-synchronization.md), která poskytuje stejné výhody cloudového ověřování organizacím. Některé organizace, které chtějí vynutit své místní zásady zabezpečení a hesel služby Active Directory, se však mohou rozhodnout použít předávací ověřování. V [této příručce](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) najdete porovnání různých metod přihlášení služby Azure AD a jak zvolit správnou metodu přihlášení pro vaši organizaci.

![Předávací ověřování Azure AD](./media/how-to-connect-pta/pta1.png)

Předávací ověřování můžete kombinovat s funkcí [bezproblémového jednotného přihlašování.](how-to-connect-sso.md) Tímto způsobem, když vaši uživatelé přistupují k aplikacím na svých podnikových počítačích v podnikové síti, nemusí zadávat svá hesla, aby se mohli přihlásit.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Hlavní výhody používání předávacího ověřování Azure AD

- *Skvělé uživatelské prostředí*
  - Uživatelé používají stejná hesla k přihlášení do místních i cloudových aplikací.
  - Uživatelé stráví méně času mluvením s it helpdeskem, který řeší problémy související s hesly.
  - Uživatelé mohou v cloudu dokončit [samoobslužné úlohy správy hesel.](../authentication/active-directory-passwords-overview.md)
- *Snadné nasazení & správa*
  - Není třeba pro komplexní místní nasazení nebo konfiguraci sítě.
  - Potřebuje pouze lehký agent, který má být nainstalován místně.
  - Žádné režijní náklady na správu. Agent automaticky obdrží vylepšení a opravy chyb.
- *Zabezpečení*
  - Místní hesla se nikdy neukládají v cloudu v žádné formě.
  - Chrání vaše uživatelské účty bezproblémovou spoluprací se [zásadami podmíněného přístupu Azure AD](../active-directory-conditional-access-azure-portal.md), včetně vícefaktorového ověřování (MFA), [blokování starších ověřování](../conditional-access/concept-conditional-access-conditions.md) a filtrování útoků [hrubou silou hesla](../authentication/howto-password-smart-lockout.md).
  - Agent provádí pouze odchozí připojení z vaší sítě. Proto neexistuje žádný požadavek na instalaci agenta v hraniční síti, označované také jako DMZ.
  - Komunikace mezi agentem a službou Azure AD je zabezpečená pomocí ověřování na základě certifikátu. Tyto certifikáty se automaticky obnovují každých několik měsíců službou Azure AD.
- *Vysoce dostupné*
  - Další agenti mohou být nainstalováni na více místních serverech, aby poskytovali vysokou dostupnost žádostí o přihlášení.

## <a name="feature-highlights"></a>Zvýraznění funkcí

- Podporuje přihlášení uživatelů ke všem aplikacím založeným na webovém prohlížeči a do klientských aplikací sady Microsoft Office, které používají [moderní ověřování](https://aka.ms/modernauthga).
- Přihlašovací uživatelská jména mohou být místní výchozí uživatelské`userPrincipalName`jméno ( ) nebo jiný atribut `Alternate ID`nakonfigurovaný ve službě Azure AD Connect (označovaný jako ).
- Tato funkce bezproblémově spolupracuje s [funkcemi podmíněného přístupu,](../active-directory-conditional-access-azure-portal.md) jako je vícefaktorové ověřování (MFA), které pomáhají zabezpečit uživatele.
- Integrovaná s cloudovou [samoobslužnou správou hesel](../authentication/active-directory-passwords-overview.md), včetně zpětného zápisu heslem do místní služby Active Directory a ochrany heslem zákazem běžně používaných hesel.
- Prostředí s více doménovými strukturami jsou podporována, pokud mezi doménovými strukturami služby AD existují vztahy důvěryhodnosti doménové struktury a pokud je směrování přípony názvů správně nakonfigurováno.
- Jedná se o bezplatnou funkci a k jeho použití nepotřebujete žádné placené edice Azure AD.
- Lze povolit prostřednictvím [služby Azure AD Connect](whatis-hybrid-identity.md).
- Používá zjednodušeného místního agenta, který naslouchá a reaguje na požadavky na ověření hesla.
- Instalace více agentů poskytuje vysokou dostupnost žádostí o přihlášení.
- [Chrání](../authentication/howto-password-smart-lockout.md) vaše místní účty před útoky hrubou silou hesla v cloudu.

## <a name="next-steps"></a>Další kroky

- [Úvodní příručka](how-to-connect-pta-quick-start.md) – spuštění předávacího ověřování Azure AD.
- [Migrace ze služby AD FS do předávacího ověřování](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) – podrobný průvodce migrací ze služby AD FS (nebo jiných federačních technologií) do předávacího ověřování.
- [Inteligentní uzamčení](../authentication/howto-password-smart-lockout.md) – konfigurace funkce inteligentního uzamčení v tenantovi za účelem ochrany uživatelských účtů.
- [Aktuální omezení](how-to-connect-pta-current-limitations.md) – Zjistěte, které scénáře jsou podporovány a které ne.
- [Technické Deep Dive](how-to-connect-pta-how-it-works.md) - Pochopit, jak tato funkce funguje.
- [Nejčastější dotazy](how-to-connect-pta-faq.md) - Odpovědi na často kladené otázky.
- [Poradce při potížích](tshoot-connect-pass-through-authentication.md) – Přečtěte si, jak vyřešit běžné problémy s funkcí.
- [Zabezpečení Deep Dive](how-to-connect-pta-security-deep-dive.md) - Další podrobné technické informace o funkci.
- [Azure AD bezproblémové jednotné přihlašovací](how-to-connect-sso.md) služby – další informace o této doplňkové funkce.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Pro podání nových žádostí o funkce.

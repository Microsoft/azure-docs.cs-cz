---
title: Běžné zásady podmíněného přístupu – Azure Active Directory
description: Běžně používané zásady podmíněného přístupu pro organizace
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20b699f0672b49dd2f947e0cf00d0ffcef7961e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295303"
---
# <a name="common-conditional-access-policies"></a>Běžné zásady podmíněného přístupu

[Výchozí nastavení zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md) jsou pro některé skvělé, ale mnoho organizací potřebuje větší flexibilitu, než nabízejí. Mnoho z nich například potřebuje možnost vyloučit konkrétní účty, jako je jejich nouzový přístup nebo účty pro správu slohozu, ze zásad podmíněného přístupu, které vyžadují vícefaktorové ověřování. Pro tyto organizace mohou být běžné zásady uvedené v tomto článku použitelné.

![Zásady podmíněného přístupu na webu Azure Portal](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Účty nouzového přístupu

Další informace o účtech pro nouzový přístup a o tom, proč jsou důležité, naleznete v následujících článcích: 

* [Správa účtů pro nouzový přístup ve službě Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Vytvoření odolné strategie správy řízení přístupu pomocí Služby Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Typické zásady nasazené organizacemi

* [Blokovat starší verze ověřování](howto-conditional-access-policy-block-legacy.md)\*
* [Vyžadovat vícefaktorové pomocné sufayeři pro správce](howto-conditional-access-policy-admin-mfa.md)\*
* [Vyžadovat vícefaktorové zabezpečení pro správu Azure](howto-conditional-access-policy-azure-management.md)\*
* [Vyžadovat vícefaktorové faktory pro všechny uživatele](howto-conditional-access-policy-all-users-mfa.md)\*

\*Tyto čtyři zásady při společné konfiguraci by napodobovaly funkce povolené [výchozími hodnotami zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="additional-policies"></a>Další zásady

* [Podmíněný přístup podle rizika (vyžaduje Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Vyžadování důvěryhodného umístění pro registraci MFA](howto-conditional-access-policy-registration.md)
* [Blokování přístupu podle umístění](howto-conditional-access-policy-location.md)
* [Vyžadování zařízení, které splňuje požadavky](howto-conditional-access-policy-compliant-device.md)
* [Blokování přístupu s výjimkou konkrétních aplikací](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>Další kroky

- [Simulujte chování přihlášení pomocí nástroje Co-li podmíněného přístupu.](troubleshoot-conditional-access-what-if.md)

- [Použití režimu pouze pro sestavu pro podmíněný přístup k určení dopadu nových rozhodnutí zásad.](concept-conditional-access-report-only.md)

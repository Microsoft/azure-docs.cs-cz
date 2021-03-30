---
title: Společné zásady podmíněného přístupu – Azure Active Directory
description: Běžně používané zásady podmíněného přístupu pro organizace
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdaaed72b85bc3c0196a8897be311efa5fb714e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366339"
---
# <a name="common-conditional-access-policies"></a>Běžné zásady podmíněného přístupu

[Výchozí nastavení zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md) je skvělé pro některé, ale mnoho organizací potřebuje větší flexibilitu než nabídky. Mnoho organizací například potřebuje možnost vyloučit konkrétní účty, jako jsou jejich nouzový přístup nebo účty pro správu se zábrusem ze zásad podmíněného přístupu, které vyžadují vícefaktorové ověřování. Pro tyto organizace se můžou použít běžné zásady, na které se odkazuje v tomto článku.

![Zásady podmíněného přístupu v Azure Portal](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Účty pro nouzový přístup

Další informace o účtech pro nouzový přístup a o tom, proč jsou důležité, najdete v následujících článcích: 

* [Správa účtů pro nouzový přístup v Azure AD](../roles/security-emergency-access.md)
* [Vytvoření odolné strategie správy řízení přístupu pomocí Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Typické zásady nasazené organizacemi

* [Blokovat starší verze ověřování](howto-conditional-access-policy-block-legacy.md)\*
* [Vyžadovat MFA pro správce](howto-conditional-access-policy-admin-mfa.md)\*
* [Vyžadovat vícefaktorové ověřování pro správu Azure](howto-conditional-access-policy-azure-management.md)\*
* [Vyžadovat MFA pro všechny uživatele](howto-conditional-access-policy-all-users-mfa.md)\*

\* Tyto čtyři zásady se nakonfigurují společně a napodobují funkce, které jsou povolené ve [výchozím nastavení zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="additional-policies"></a>Další zásady

* [Podmíněný přístup na základě rizik přihlašování (vyžaduje Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Podmíněný přístup na základě rizika uživatele (vyžaduje Azure AD Premium P2)](howto-conditional-access-policy-risk-user.md)
* [Vyžadování důvěryhodného umístění pro registraci MFA](howto-conditional-access-policy-registration.md)
* [Blokovat přístup podle umístění](howto-conditional-access-policy-location.md)
* [Vyžadování zařízení, které splňuje požadavky](howto-conditional-access-policy-compliant-device.md)
* [Blokovat přístup kromě konkrétních aplikací](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>Další kroky

- [Simulujte chování při přihlašování pomocí nástroje pro What If podmíněného přístupu.](troubleshoot-conditional-access-what-if.md)

- [Použijte režim pouze pro sestavy pro podmíněný přístup k určení dopadu nových rozhodnutí o zásadách.](concept-conditional-access-report-only.md)

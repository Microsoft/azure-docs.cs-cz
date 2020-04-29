---
title: Společné zásady podmíněného přístupu – Azure Active Directory
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295303"
---
# <a name="common-conditional-access-policies"></a>Běžné zásady podmíněného přístupu

[Výchozí nastavení zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md) je skvělé pro některé, ale mnoho organizací potřebuje větší flexibilitu než nabídky. Mnoho z nich třeba umožňuje vyloučit konkrétní účty, jako jsou jejich nouzový přístup nebo účty pro správu ze skla, od zásad podmíněného přístupu, které vyžadují vícefaktorové ověřování. Pro tyto organizace se můžou použít běžné zásady, na které se odkazuje v tomto článku.

![Zásady podmíněného přístupu v Azure Portal](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Účty pro nouzový přístup

Další informace o účtech pro nouzový přístup a o tom, proč jsou důležité, najdete v následujících článcích: 

* [Správa účtů pro nouzový přístup v Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Vytvoření odolné strategie správy řízení přístupu pomocí Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Typické zásady nasazené organizacemi

* [Blokovat starší verze ověřování](howto-conditional-access-policy-block-legacy.md)\*
* [Vyžadovat MFA pro správce](howto-conditional-access-policy-admin-mfa.md)\*
* [Vyžadovat vícefaktorové ověřování pro správu Azure](howto-conditional-access-policy-azure-management.md)\*
* [Vyžadovat MFA pro všechny uživatele](howto-conditional-access-policy-all-users-mfa.md)\*

\*Tyto čtyři zásady, pokud jsou nakonfigurované společně, by mohly napodobovat funkce aktivované [výchozími hodnotami zabezpečení](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="additional-policies"></a>Další zásady

* [Podmíněný přístup podle rizika (vyžaduje Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Vyžadování důvěryhodného umístění pro registraci MFA](howto-conditional-access-policy-registration.md)
* [Blokování přístupu podle umístění](howto-conditional-access-policy-location.md)
* [Vyžadování zařízení, které splňuje požadavky](howto-conditional-access-policy-compliant-device.md)
* [Blokovat přístup kromě konkrétních aplikací](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>Další kroky

- [Simulujte chování při přihlašování pomocí nástroje pro What If podmíněného přístupu.](troubleshoot-conditional-access-what-if.md)

- [Použijte režim pouze pro sestavy pro podmíněný přístup k určení dopadu nových rozhodnutí o zásadách.](concept-conditional-access-report-only.md)

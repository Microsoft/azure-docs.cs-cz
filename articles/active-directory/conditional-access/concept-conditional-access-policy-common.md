---
title: Společné zásady podmíněného přístupu – Azure Active Directory
description: Běžně používané zásady podmíněného přístupu pro organizace
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf338ad0c555038cd99c3604d69ab80371d6ea22
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74804970"
---
# <a name="common-conditional-access-policies"></a>Běžné zásady podmíněného přístupu

Zásady ochrany základní úrovně jsou skvělé, ale mnoho organizací potřebuje větší flexibilitu než nabídky. Mnoho organizací například potřebuje možnost vyloučit konkrétní účty, jako jsou jejich nouzový přístup nebo účty pro správu se zábrusem ze zásad podmíněného přístupu, které vyžadují vícefaktorové ověřování. Pro tyto organizace se můžou použít běžné zásady, na které se odkazuje v tomto článku.

![Zásady podmíněného přístupu v Azure Portal](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Účty pro nouzový přístup

Další informace o účtech pro nouzový přístup a o tom, proč jsou důležité, najdete v následujících článcích: 

* [Správa účtů pro nouzový přístup v Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Vytvoření odolné strategie správy řízení přístupu pomocí Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Typické zásady nasazené organizacemi

* [Vyžadovat MFA pro správce](howto-conditional-access-policy-admin-mfa.md)
* [Vyžadovat vícefaktorové ověřování pro správu Azure](howto-conditional-access-policy-azure-management.md)
* [Vyžadovat MFA pro všechny uživatele](howto-conditional-access-policy-all-users-mfa.md)
* [Blokovat starší verze ověřování](howto-conditional-access-policy-block-legacy.md)
* [Podmíněný přístup na základě rizik (vyžaduje Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Vyžadovat důvěryhodné umístění pro registraci MFA](howto-conditional-access-policy-registration.md)
* [Blokovat přístup podle umístění](howto-conditional-access-policy-location.md)
* [Vyžadovat vyhovující zařízení](howto-conditional-access-policy-compliant-device.md)

## <a name="next-steps"></a>Další kroky

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)

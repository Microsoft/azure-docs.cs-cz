---
title: Licenční požadavky na používání správy privilegovaných identit – Azure Active Directory | Dokumenty společnosti Microsoft
description: Popisuje licenční požadavky na použití Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70696cdb95fffc1e5faa46ca1b5f2180633ed63a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932324"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Licenční požadavky na používání správy privilegovaných identit

Chcete-li používat Azure Active Directory (Azure AD) Privileged Identity Management (PIM), musí mít adresář platnou licenci. Kromě toho musí být licence přiřazeny správcům a příslušným uživatelům. Tento článek popisuje licenční požadavky na použití správy privilegovaných identit.

## <a name="valid-licenses"></a>Platné licence

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="how-many-licenses-must-you-have"></a>Kolik licencí musíte mít?

Ujistěte se, že váš adresář má alespoň tolik licencí Azure AD Premium P2, jako máte zaměstnance, kteří budou provádět následující úkoly:

- Uživatelé přiřazení k rolím Azure AD spravovaným pomocí PIM
- Uživatelé, kteří mohou schválit nebo odmítnout žádosti o aktivaci v PIM
- Uživatelé přiřazení k roli prostředku Azure s přiřazeními just-in-time nebo direct (časově založenými)  
- Uživatelé přiřazení k revizi přístupu
- Uživatelé, kteří provádějí kontroly přístupu

Licence Azure AD Premium P2 **nejsou** vyžadovány pro následující úkoly:

- Pro uživatele s rolemi globálního správce nebo správce privilegovaných rolí, kteří nastavují PIM, konfigurují zásady, přijímají výstrahy a nastavují kontroly přístupu, nejsou vyžadovány žádné licence.

Další informace o licencích najdete v tématu [Přiřazení nebo odebrání licencí pomocí portálu Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="example-license-scenarios"></a>Příklady scénářů licencí

Zde je několik příkladů licenčních scénářů, které vám pomohou určit počet licencí, které musíte mít.

| Scénář | Výpočet | Počet licencí |
| --- | --- | --- |
| Woodgrove Bank má 10 správců pro různá oddělení a 2 globální administrátory, kteří konfigurují a spravují PIM. Mají nárok na pět administrátorů. | Pět licencí pro administrátory, kteří mají nárok | 5 |
| Graphic Design Institute má 25 administrátorů, z nichž 14 je řízeno prostřednictvím PIM. Aktivace role vyžaduje schválení a v organizaci jsou tři různí uživatelé, kteří mohou schvalovat aktivace. | 14 licencí pro způsobilé role + tři schvalovatelé | 17 |
| Contoso má 50 správců, z nichž 42 je spravováno prostřednictvím PIM. Aktivace role vyžaduje schválení a v organizaci je pět různých uživatelů, kteří mohou schvalovat aktivace. Společnost Contoso také provádí měsíční kontroly uživatelů přiřazených k rolím správce a recenzenti jsou správci uživatelů, z nichž šest není v rolích správců spravovaných PIM. | 42 licencí pro způsobilé role + pět schvalovatelů + šest recenzentů | 53 |

## <a name="what-happens-when-a-license-expires"></a>Co se stane, když vyprší platnost licence?

Pokud vyprší platnost licence Azure AD Premium P2, EMS E5 nebo zkušební verze, funkce správy privilegovaných identit už nebudou ve vašem adresáři dostupné:

- Trvalé přiřazení rolí pro role Azure AD nebude ovlivněna.
- Služba Správa privilegovaných identit na webu Azure Portal, také rutiny rozhraní API graphu a rozhraní prostředí PowerShell s privilegovanou správou identit již nebudou k dispozici uživatelům k aktivaci privilegovaných rolí, správě privilegovaného přístupu nebo provedení přístup k přezkumům privilegovaných rolí.
- Způsobilá přiřazení rolí Azure AD se odeberou, protože uživatelé už nebudou moct aktivovat privilegované role.
- Všechny průběžné kontroly přístupu rolí Azure AD skončí a nastavení konfigurace správy privilegovaných identit budou odebrána.
- Správa privilegovaných identit již nebude odesílat e-maily se změnami přiřazení rolí.

## <a name="next-steps"></a>Další kroky

- [Nasazení technologie Privileged Identity Management](pim-deployment-plan.md)
- [Zahájení využití technologie Privileged Identity Management](pim-getting-started.md)
- [Role, které nelze spravovat ve správě privilegovaných identit](pim-roles.md)

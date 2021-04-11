---
title: Licenční požadavky pro použití Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Popisuje licenční požadavky pro použití Azure AD Privileged Identity Management (PIM).
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
ms.topic: how-to
ms.subservice: pim
ms.date: 04/05/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d0428f73c4df3d97002def03823ff8cced1d647
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552627"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Licenční požadavky pro použití Privileged Identity Management

Aby bylo možné používat Azure Active Directory (Azure AD) Privileged Identity Management (PIM), musí mít adresář platnou licenci. Kromě toho musí být licence přiřazeny správcům a relevantním uživatelům. Tento článek popisuje licenční požadavky pro použití Privileged Identity Management.

## <a name="valid-licenses"></a>Platné licence

Budete muset [!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)] použít PIM a všechna jeho nastavení. V současné době můžete určit rozsah kontroly přístupu u instančních objektů s přístupem k Azure AD a k rolím prostředků Azure (Preview) s aktivní verzí služby Azure Active Directory Premium P2 ve vašem tenantovi. Pro účely obecné dostupnosti této funkce bude finalizace licenční model pro instanční objekty a může vyžadovat další licence.

## <a name="licenses-you-must-have"></a>Licence, které musíte mít

Zajistěte, aby měl váš adresář aspoň tolik licencí Azure AD Premium P2, protože máte zaměstnance, kteří budou provádět následující úlohy:

- Uživatelé přiřazení k Azure AD nebo rolím Azure spravovaným pomocí PIM
- Uživatelé, kteří jsou přiřazeni jako oprávnění členové nebo vlastníci privilegovaných skupin přístupu
- Uživatelé mohou schvalovat nebo odmítat žádosti o aktivaci v PIM
- Uživatelé přiřazení k recenzi přístupu
- Uživatelé, kteří provádějí kontroly přístupu

Licence Azure AD Premium P2 nejsou **požadovány** pro následující úlohy:

- Pro uživatele, kteří nastavili PIM, konfigurují zásady, přijímat výstrahy a nastavují kontroly přístupu, nejsou potřeba žádné licence.

Další informace o licencích najdete v tématu [přiřazení nebo odebrání licencí pomocí portálu Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="example-license-scenarios"></a>Příklady scénářů licencí

Tady je několik ukázkových scénářů licencí, které vám pomůžou určit počet licencí, které musíte mít.

| Scenario | Výpočet | Počet licencí |
| --- | --- | --- |
| Woodgrove banka má 10 správců pro různá oddělení a 2 globální správce, kteří konfigurují a spravují PIM. Poskytují pět správcům nároků. | Pět licencí pro správce, kteří mají nárok | 5 |
| Graphics design Institute má 25 správců, z nichž 14 spravuje PIM. Aktivace role vyžaduje schválení a v organizaci existují tři různé uživatele, kteří můžou schvalovat aktivace. | 14 licencí na opravňující role a tři schvalovatele | 17 |
| Společnost Contoso má 50 správců, které 42 spravuje PIM. Aktivace role vyžaduje schválení a v organizaci existuje pět různých uživatelů, kteří můžou schvalovat aktivace. Společnost Contoso také provádí měsíční přezkoumání uživatelů přiřazených rolím Správce a kontrolorům, kteří nejsou v rolích správce spravovaných pomocí PIM. | 42 licencí na opravňující role + pět schvalovatelů + šest revidujících | 53 |

## <a name="when-a-license-expires"></a>Po vypršení platnosti licence

Pokud vyprší platnost Azure AD Premium P2, EMS E5 nebo zkušební licence, nebudou ve vašem adresáři k dispozici Privileged Identity Management funkce:

- Trvalá přiřazení rolí k rolím Azure AD nebudou mít vliv na.
- Služba Privileged Identity Management v Azure Portal, stejně jako rutiny Graph API a rozhraní PowerShell Privileged Identity Management, již nebude k dispozici pro uživatele, aby mohli aktivovat privilegované role, spravovat privilegovaný přístup nebo provádět kontroly přístupu privilegovaných rolí.
- Budou odebrána oprávněná přiřazení rolí rolí Azure AD, protože uživatelé již nebudou moci aktivovat privilegované role.
- Všechny průběžné kontroly přístupu rolí Azure AD skončí a Privileged Identity Management nastavení konfigurace se odeberou.
- Privileged Identity Management už nebude posílat e-maily při změnách přiřazení role.

## <a name="next-steps"></a>Další kroky

- [Nasazení technologie Privileged Identity Management](pim-deployment-plan.md)
- [Zahájení využití technologie Privileged Identity Management](pim-getting-started.md)
- [Role, které nemůžete spravovat v Privileged Identity Management](pim-roles.md)
- [Vytvoření kontroly přístupu pro role prostředků Azure v PIM](pim-resource-roles-start-access-review.md)
- [Vytvoření kontroly přístupu rolí Azure AD v PIM](pim-how-to-start-security-review.md)

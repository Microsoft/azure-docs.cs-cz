---
title: Konfigurace výstrah zabezpečení pro role prostředků Azure v privilegované správě identit – Azure Active Directory | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat výstrahy zabezpečení pro role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0b938dc808d9b02ad4105d85a5b3125135c51d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023072"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Konfigurace výstrah zabezpečení pro role prostředků Azure ve správě privilegovaných identit

Privilegovaná správa identit (PIM) generuje výstrahy, když je podezřelá nebo nebezpečná aktivita ve vaší organizaci Azure Active Directory (Azure AD). Když se aktivuje výstraha, zobrazí se na stránce Výstrahy.

![Prostředky Azure – upozornění na stránku s upozorněním, úroveň rizika a počet](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Kontrola upozornění

Výběrem výstrahy zobrazíte sestavu se seznamem uživatelů nebo rolí, které výstrahu spustily, spolu s pokyny k nápravě.

![Zpráva s upozorněním zobrazující čas posledního skenování, popis, kroky zmírnění, typ, závažnost, dopad na zabezpečení a postup, jak zabránit dalšímu](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Výstrahy

| Výstrahy | Severity | Trigger | Doporučení |
| --- | --- | --- | --- |
| **K prostředku je přiřazeno příliš mnoho vlastníků.** |Střednědobé používání |Roli vlastníka má příliš mnoho uživatelů. |Zkontrolujte uživatele v seznamu a některé z jejich přiřazení znovu přiřaďte k méně privilegovaným rolím. |
| **K prostředku je přiřazeno příliš mnoho stálých vlastníků.** |Střednědobé používání |K roli je trvale přiřazeno příliš mnoho uživatelů. |Zkontrolujte uživatele v seznamu a znovu přiřaďte některé, aby vyžadovali aktivaci pro použití role. |
| **Byla vytvořena duplicitní role.** |Střednědobé používání |Více rolí má stejná kritéria. |Použijte pouze jednu z těchto rolí. |

### <a name="severity"></a>Severity

- **Vysoká**: Vyžaduje okamžitou akci z důvodu porušení zásad. 
- **Střední**: Nevyžaduje okamžitou akci, ale signalizuje potenciální porušení zásad.
- **Nízká**: Nevyžaduje okamžitou akci, ale navrhuje upřednostňovanou změnu zásad.

## <a name="configure-security-alert-settings"></a>Konfigurace nastavení výstrah zabezpečení

Na stránce Výstrahy přejděte na **Nastavení**.

![Stránka Upozornění se zvýrazněným nastavením](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Přizpůsobte si nastavení různých výstrah tak, aby fungovaly s vaším prostředím a cíli zabezpečení.

![Nastavení stránky pro výstrahu pro povolení a konfiguraci nastavení](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Další kroky

- [Konfigurace nastavení role prostředků Azure ve správě privilegovaných identit](pim-resource-roles-configure-role-settings.md)

---
title: Konfigurace výstrah zabezpečení pro role prostředků Azure v Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Naučte se konfigurovat výstrahy zabezpečení pro role prostředků Azure v Azure AD Privileged Identity Management (PIM).
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023072"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Konfigurace výstrah zabezpečení pro role prostředků Azure v Privileged Identity Management

Privileged Identity Management (PIM) generuje výstrahy v případě podezřelé nebo nebezpečné aktivity v organizaci Azure Active Directory (Azure AD). Když se aktivuje výstraha, zobrazí se na stránce s výstrahami.

![Prostředky Azure – výstrahy výpisu stránky výstrah, úroveň rizika a počet](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Zkontrolovat výstrahy

Vyberte výstrahu, chcete-li zobrazit sestavu se seznamem uživatelů nebo rolí, které výstrahu aktivovaly, spolu s pokyny k nápravě.

![Zpráva výstrahy ukazující čas poslední kontroly, popis, kroky pro zmírnění rizika, typ, závažnost, dopad na zabezpečení a způsob, jakým se má příště zabránit](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Výstrahy

| Výstrahy | Severity | Trigger | Doporučení |
| --- | --- | --- | --- |
| **K prostředku je přiřazeno příliš mnoho vlastníků.** |Střednědobé používání |Role vlastníka má příliš mnoho uživatelů. |Zkontrolujte uživatele v seznamu a znovu je přiřaďte k méně privilegovaným rolím. |
| **K prostředku je přiřazeno příliš mnoho trvalých vlastníků.** |Střednědobé používání |K roli je trvale přiřazeno příliš mnoho uživatelů. |Zkontrolujte uživatele v seznamu a znovu je přiřaďte, aby vyžadovaly aktivaci pro použití role. |
| **Duplicitní role se vytvořila.** |Střednědobé používání |Více rolí má stejné kritérium. |Použijte pouze jednu z těchto rolí. |

### <a name="severity"></a>Severity

- **Vysoká**: vyžaduje okamžitou akci kvůli porušení zásad. 
- **Střední**: nevyžaduje okamžitou akci, ale signalizuje potenciální porušení zásad.
- **Nízká**: nevyžaduje okamžitou akci, ale navrhuje upřednostňovanou změnu zásad.

## <a name="configure-security-alert-settings"></a>Konfigurace nastavení výstrah zabezpečení

Na stránce s výstrahami přejít na **Nastavení**.

![Stránka upozornění s zvýrazněným nastavením](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Přizpůsobení nastavení různých výstrah pro práci s vaším prostředím a cíli zabezpečení

![Stránka nastavení pro výstrahu, která povolí a nakonfiguruje nastavení](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Další kroky

- [Konfigurace nastavení role prostředků Azure v Privileged Identity Management](pim-resource-roles-configure-role-settings.md)

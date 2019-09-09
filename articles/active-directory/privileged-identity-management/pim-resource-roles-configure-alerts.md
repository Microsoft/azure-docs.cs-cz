---
title: Konfigurace výstrah zabezpečení pro role prostředků Azure v PIM-Azure Active Directory | Microsoft Docs
description: Naučte se konfigurovat výstrahy zabezpečení pro role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 177f61392c3e441c891ba1b531301b3dae8c0db2
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804231"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Konfigurace výstrah zabezpečení pro role prostředků Azure v PIM
Azure Active Directory (Azure AD) Privileged Identity Management (PIM) generuje výstrahy v případě podezřelé nebo nebezpečné aktivity ve vašem prostředí. Když se aktivuje výstraha, zobrazí se na stránce s výstrahami. 

![Prostředky Azure – výstrahy výpisu stránky výstrah, úroveň rizika a počet](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Zkontrolovat výstrahy
Vyberte výstrahu, chcete-li zobrazit sestavu se seznamem uživatelů nebo rolí, které výstrahu aktivovaly, spolu s doporučeními k nápravě.

![Zpráva výstrahy ukazující čas poslední kontroly, popis, kroky pro zmírnění rizika, typ, závažnost, dopad na zabezpečení a způsob, jakým se má příště zabránit](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Upozornění
| Výstrahy | severity | Trigger | Doporučení |
| --- | --- | --- | --- |
| **K prostředku je přiřazeno příliš mnoho vlastníků.** |Střední |Role vlastníka má příliš mnoho uživatelů. |Zkontrolujte uživatele v seznamu a znovu je přiřaďte k méně privilegovaným rolím. |
| **K prostředku je přiřazeno příliš mnoho trvalých vlastníků.** |Střední |K roli je trvale přiřazeno příliš mnoho uživatelů. |Zkontrolujte uživatele v seznamu a znovu je přiřaďte, aby vyžadovaly aktivaci pro použití role. |
| **Duplicitní role se vytvořila.** |Střední |Více rolí má stejné kritérium. |Použijte pouze jednu z těchto rolí. |


### <a name="severity"></a>severity
* **Vysoká**: Vyžaduje okamžitou akci kvůli porušení zásad. 
* **Střední**: Nevyžaduje okamžitou akci, ale signalizuje potenciální porušení zásad.
* **Nízká úroveň**: Nevyžaduje okamžitou akci, ale navrhuje upřednostňovanou změnu zásad.

## <a name="configure-security-alert-settings"></a>Konfigurace nastavení výstrah zabezpečení
Na stránce s výstrahami přejít na **Nastavení**.

![Stránka upozornění s zvýrazněným nastavením](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Přizpůsobení nastavení různých výstrah pro práci s vaším prostředím a cíli zabezpečení

![Stránka nastavení pro výstrahu, která povolí a nakonfiguruje nastavení](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Další kroky

- [Konfigurace nastavení role prostředků Azure v PIM](pim-resource-roles-configure-role-settings.md)

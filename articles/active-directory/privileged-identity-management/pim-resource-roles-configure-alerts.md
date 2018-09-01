---
title: Konfigurace výstrah zabezpečení pro role prostředků Azure v PIM | Dokumentace Microsoftu
description: Zjistěte, jak konfigurovat výstrahy zabezpečení pro role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 2fa63cf2fa05f2cde4558f0bea38bfd7f17df3ae
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43342058"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Konfigurace výstrah zabezpečení pro role prostředků Azure v PIM
Privileged Identity Management (PIM) pro prostředky Azure generuje výstrahy, když je aktivita podezřelého nebo nebezpečné ve vašem prostředí. Když se aktivuje upozornění, zobrazí na stránky s upozorněními. 

![Stránky s upozorněními](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Zkontrolujte výstrahy
Výběrem výstrahy zobrazíte sestavu obsahující seznam uživatelů nebo rolí, které aktivuje výstrahu, spolu s Rady nápravu.

![Sestava výstrahy](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Výstrahy
| Výstrahy | Severity | Trigger | Doporučení |
| --- | --- | --- | --- |
| **K prostředku je přiřazeno příliš mnoho vlastníků.** |Střednědobé používání |Příliš mnoho uživatelů mít roli vlastníka. |Projděte si uživatelé ze seznamu a znovu přiřadíte některé méně privilegovaným rolím. |
| **K prostředku je přiřazeno příliš mnoho trvalých vlastníků.** |Střednědobé používání |Příliš mnoho uživatelů jsou trvale přiřazené k roli. |Projděte si uživatelé ze seznamu a u změnit přiřazení některé vyžadují také aktivaci role používat. |
| **Vytvořila se duplicitní role** |Střednědobé používání |Stejná kritéria mají více role. |Použijte pouze jednu z těchto rolí. |


### <a name="severity"></a>Severity
* **Vysoká**: vyžaduje okamžitý zásah kvůli porušení zásad. 
* **Střední**: není potřeba reagovat okamžitě, ale signály potenciální porušení zásad.
* **Nízká**: není potřeba reagovat okamžitě, ale navrhuje Změna upřednostňovaného zásad.

## <a name="configure-security-alert-settings"></a>Konfigurace nastavení výstrah zabezpečení
Ze stránky s upozorněními, přejděte na **nastavení**.
![Nastavení](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

Upravte nastavení na jednotlivých výstrah pro práci s vaším prostředím a cíle zabezpečení.
![Vlastní nastavení](media/azure-pim-resource-rbac/rbac-alert-settings.png)

## <a name="next-steps"></a>Další postup

- [Konfigurace výstrah zabezpečení pro role prostředků Azure v PIM](pim-resource-roles-configure-alerts.md)

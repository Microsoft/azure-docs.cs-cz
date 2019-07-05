---
title: Konfigurace výstrah zabezpečení pro role prostředků Azure v PIM – Azure Active Directory | Dokumentace Microsoftu
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
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7ce8b79644a9ffc9481ba825ec5623a9268983
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476332"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Konfigurace výstrah zabezpečení pro role prostředků Azure v PIM
Privileged Identity Management (PIM) ve Azure Active Directory (Azure AD) generuje výstrahy, když je aktivita podezřelého nebo nebezpečné ve vašem prostředí. Když se aktivuje upozornění, zobrazí na stránky s upozorněními. 

![Prostředky Azure – seznam upozornění, úroveň rizika a počet stránky s upozorněními](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Zkontrolujte výstrahy
Výběrem výstrahy zobrazíte sestavu obsahující seznam uživatelů nebo rolí, které aktivuje výstrahu, spolu s Rady nápravu.

![Sestava výstrahy zobrazuje poslední kontroly času, popis, kroky pro zmírnění rizika, typ, závažnosti, dopad na zabezpečení a jak tomu příště předejít](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Výstrahy
| Výstrahy | severity | Trigger | Doporučení |
| --- | --- | --- | --- |
| **K prostředku je přiřazeno příliš mnoho vlastníků.** |Střední |Příliš mnoho uživatelů mít roli vlastníka. |Projděte si uživatelé ze seznamu a znovu přiřadíte některé méně privilegovaným rolím. |
| **K prostředku je přiřazeno příliš mnoho trvalých vlastníků.** |Střední |Příliš mnoho uživatelů jsou trvale přiřazené k roli. |Projděte si uživatelé ze seznamu a u změnit přiřazení některé vyžadují také aktivaci role používat. |
| **Vytvořila se duplicitní role** |Střední |Stejná kritéria mají více role. |Použijte pouze jednu z těchto rolí. |


### <a name="severity"></a>severity
* **Vysoká**: Vyžaduje okamžitý zásah kvůli porušení zásad. 
* **Střední**: Není potřeba reagovat okamžitě, ale signály potenciální porušení zásad.
* **Nízká**: Není potřeba reagovat okamžitě, ale navrhuje Změna upřednostňovaného zásad.

## <a name="configure-security-alert-settings"></a>Konfigurace nastavení výstrah zabezpečení
Ze stránky s upozorněními, přejděte na **nastavení**.

![Stránky s upozorněními s nastavením se zvýrazněnou](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Upravte nastavení na jednotlivých výstrah pro práci s vaším prostředím a cíle zabezpečení.

![Nastavení stránky výstraze, abyste mohli povolit a konfigurovat nastavení](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Další postup

- [Konfigurace nastavení role prostředků Azure v PIM](pim-resource-roles-configure-role-settings.md)

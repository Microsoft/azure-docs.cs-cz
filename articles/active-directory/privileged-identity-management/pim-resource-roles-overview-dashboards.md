---
title: Řídicí panel prostředků použít k provádění kontroly přístupu v PIM – Azure Active Directory | Dokumentace Microsoftu
description: Popisuje, jak použít řídicí panel prostředků k provádění kontroly přístupu v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5404d1b85821458aedef64b72ae635ea49aa1ff
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602463"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>Řídicí panel prostředků použít k provádění kontroly přístupu v PIM

Prostředek řídicí panel můžete použít k provádění kontroly přístupu v Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Správce zobrazit řídicí panel má tři hlavní komponenty:

- Grafická reprezentace aktivace rolí prostředků.
- Dva grafy zobrazující rozdělení přiřazení rolí podle typu přiřazení.
- Datové oblasti týkající se nové přiřazení role.

![Snímek obrazovky Správce zobrazení řídicího panelu, diagramy a grafy](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Snímek obrazovky Správce zobrazení řídicího panelu, zobrazí data](media/pim-resource-roles-overview-dashboards/role-settings.png)

Grafická reprezentace prostředku Aktivace rolí se věnuje posledních sedmi dnů. Tato data působí na vybraný prostředek a zobrazuje počet aktivací pro nejběžnější role (vlastník, Přispěvatel, správce uživatelských přístupů) a pro všechny role v kombinaci.

Napravo od grafu aktivací dva grafy zobrazují rozdělení přiřazení rolí podle přiřazení typu pro uživatele a skupiny. Hodnota v procentech (nebo naopak), můžete změnit tak, že vyberete Výseč grafu.

Pod grafy se zobrazí počet uživatelů a skupin pomocí nové přiřazení rolí za posledních 30 dní a seznam rolí seřazené podle celkem přiřazení (sestupně).

## <a name="next-steps"></a>Další postup

- [Zahájení kontroly přístupu pro role prostředků Azure v nástroji PIM](pim-resource-roles-start-access-review.md) 

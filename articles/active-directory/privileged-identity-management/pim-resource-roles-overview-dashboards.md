---
title: Řídicí panel prostředků použít k provádění kontroly přístupu – Azure | Dokumentace Microsoftu
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
ms.openlocfilehash: 149b51707989f6f6e0bf81fea9a2971f4823bec3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196544"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review"></a>Řídicí panel prostředků použít k provádění kontroly přístupu

Prostředek řídicí panel můžete použít k provádění kontroly přístupu v Privileged Identity Management (PIM) pro prostředky Azure. Správce zobrazit řídicí panel má tři hlavní komponenty:

- Grafická reprezentace aktivace rolí prostředků.
- Dva grafy zobrazující rozdělení přiřazení rolí podle typu přiřazení.
- Datové oblasti týkající se nové přiřazení role.

![Snímek obrazovky Správce zobrazení řídicího panelu, diagramy a grafy](media/azure-pim-resource-rbac/rbac-overview-top.png)

![Snímek obrazovky Správce zobrazení řídicího panelu, zobrazí data](media/azure-pim-resource-rbac/role-settings.png)

Grafická reprezentace prostředku Aktivace rolí se věnuje posledních sedmi dnů. Tato data působí na vybraný prostředek a zobrazuje počet aktivací pro nejběžnější role (vlastník, Přispěvatel, správce uživatelských přístupů) a pro všechny role v kombinaci.

Napravo od grafu aktivací dva grafy zobrazují rozdělení přiřazení rolí podle přiřazení typu pro uživatele a skupiny. Hodnota v procentech (nebo naopak), můžete změnit tak, že vyberete Výseč grafu.

Pod grafy se zobrazí počet uživatelů a skupin pomocí nové přiřazení rolí za posledních 30 dní a seznam rolí seřazené podle celkem přiřazení (sestupně).

## <a name="next-steps"></a>Další postup

- [Zahájení kontroly přístupu pro role prostředků Azure v PIM](pim-resource-roles-start-access-review.md) 

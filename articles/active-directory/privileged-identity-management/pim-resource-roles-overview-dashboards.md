---
title: Pomocí řídicího panelu prostředků proveďte kontrolu přístupu v PIM-Azure Active Directory | Microsoft Docs
description: Popisuje, jak pomocí řídicího panelu prostředků provádět kontrolu přístupu v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e759ba47c16617aa1783ce6fb0e324aa62ee96d
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804113"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>Provedení kontroly přístupu v PIM pomocí řídicího panelu prostředků

Pomocí řídicího panelu prostředků můžete provádět kontrolu přístupu v Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Řídicí panel zobrazení Správce má tři primární součásti:

- Grafická reprezentace aktivací rolí prostředků.
- Dva grafy, které zobrazují distribuci přiřazení rolí podle typu přiřazení.
- Datová oblast, která se vztahuje k novým přiřazením rolí.

![Snímek obrazovky řídicího panelu zobrazení správce, zobrazení grafů a grafů](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Snímek obrazovky řídicího panelu zobrazení Správce zobrazující seznam dat](media/pim-resource-roles-overview-dashboards/role-settings.png)

Grafické znázornění aktivací rolí prostředků pokrývá posledních sedm dní. Tato data jsou vymezená na vybraný prostředek a zobrazují se aktivace pro nejběžnější role (vlastník, přispěvatel, správce přístupu uživatelů) a pro všechny role v kombinaci.

Napravo od grafu aktivace zobrazuje dva grafy distribuci přiřazení rolí podle typu přiřazení pro uživatele i skupiny. Můžete změnit hodnotu na procento (nebo naopak), a to tak, že vyberete řez grafu.

Pod grafy se v posledních 30 dnech zobrazí počet uživatelů a skupin s novými přiřazeními rolí a seznam rolí seřazený podle celkového počtu přiřazení (v sestupném pořadí).

## <a name="next-steps"></a>Další kroky

- [Zahájení kontroly přístupu pro role prostředků Azure v nástroji PIM](pim-resource-roles-start-access-review.md) 

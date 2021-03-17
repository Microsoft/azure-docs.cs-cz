---
title: Řídicí panely prostředků pro recenze přístupu v PIM – Azure AD | Microsoft Docs
description: Popisuje, jak pomocí řídicího panelu prostředků provádět kontrolu přístupu v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e95eaa5b0e86a7470fc48edc23b2dbfb47e4b10c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84743724"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Pomocí řídicího panelu prostředků proveďte kontrolu přístupu v Privileged Identity Management

Pomocí řídicího panelu prostředků můžete provádět kontrolu přístupu v Privileged Identity Management (PIM). Řídicí panel zobrazení správce v Azure Active Directory (Azure AD) má tři primární součásti:

- Grafická reprezentace aktivací rolí prostředků
- Grafy, které zobrazují distribuci přiřazení rolí podle typu přiřazení
- Datová oblast obsahující informace o nových přiřazeních rolí

![Snímek obrazovky řídicího panelu zobrazení správce, zobrazení grafů a grafů](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Snímek obrazovky řídicího panelu zobrazení Správce zobrazující seznam dat](media/pim-resource-roles-overview-dashboards/role-settings.png)

Grafické znázornění aktivací rolí prostředků pokrývá posledních sedm dní. Tato data jsou vymezená na vybraný prostředek a zobrazují se aktivace pro nejběžnější role (vlastník, přispěvatel, správce přístupu uživatelů) a pro všechny role v kombinaci.

Na jedné straně grafu aktivace zobrazuje dva grafy distribuci přiřazení rolí podle typu přiřazení pro uživatele i skupiny. Můžete změnit hodnotu na procento (nebo naopak), a to tak, že vyberete řez grafu.

Pod grafy jsou uvedeny počty uživatelů a skupin s novými přiřazeními rolí za posledních 30 dní a role seřazené podle celkového počtu přiřazení v sestupném pořadí.

## <a name="next-steps"></a>Další kroky

- [Spuštění kontroly přístupu pro role prostředků Azure v Privileged Identity Management](pim-resource-roles-start-access-review.md)

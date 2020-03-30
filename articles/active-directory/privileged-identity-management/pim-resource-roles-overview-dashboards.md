---
title: Řídicí panely prostředků pro kontroly přístupu v PIM – Azure AD | Dokumenty společnosti Microsoft
description: Popisuje, jak pomocí řídicího panelu prostředků provést kontrolu přístupu v Azure AD Privilegované správy identit (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6affa2ecc8919dabeb6173622b525280ce96bcfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847031"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-privileged-identity-management"></a>Použití řídicího panelu prostředků k provedení kontroly přístupu v privilegované správě identit

Řídicí panel prostředků můžete použít k provedení kontroly přístupu v privilegované správě identit (PIM). Řídicí panel Admin View ve službě Azure Active Directory (Azure AD) má tři primární součásti:

- Grafické znázornění aktivací role prostředku
- Grafy zobrazující rozdělení přiřazení rolí podle typu přiřazení
- Datová oblast obsahující informace pro nová přiřazení rolí

![Snímek obrazovky řídicího panelu Admin View s grafy a grafy](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Snímek obrazovky řídicího panelu Admin View se seznamy dat](media/pim-resource-roles-overview-dashboards/role-settings.png)

Grafické znázornění aktivací role prostředku zahrnuje posledních sedm dní. Tato data jsou vymezena na vybraný prostředek a zobrazují aktivace nejběžnějších rolí (vlastníka, přispěvatele, správce přístupu uživatelů) a všech rolí dohromady.

Na jedné straně grafu aktivace dva grafy zobrazují rozdělení přiřazení rolí podle typu přiřazení pro uživatele i skupiny. Hodnotu můžete změnit na procento (nebo naopak) výběrem výseče grafu.

Pod grafy je uveden počet uživatelů a skupin s novými přiřazeními rolí za posledních 30 dní a role seřazené podle celkového počtu přiřazení v sestupném pořadí.

## <a name="next-steps"></a>Další kroky

- [Spuštění kontroly přístupu pro role prostředků Azure ve správě privilegovaných identit](pim-resource-roles-start-access-review.md)

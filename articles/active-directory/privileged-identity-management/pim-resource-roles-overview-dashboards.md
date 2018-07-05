---
title: 'Přehled: Provádění kontroly přístupu v Privileged Identity Management pro prostředky Azure | Dokumentace Microsoftu'
description: Tento dokument popisuje, jak k provádění kontroly přístupu v PIM pro prostředky Azure.
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
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 9e9053c62f2ead3b6ae7d4ca3c6c38fd1063b8da
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441486"
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



---
title: 'Přehled: Provádět kontrola přístupu k prostředkům Azure v Privileged Identity Management | Microsoft Docs'
description: Tento dokument popisuje, jak provést kontrola přístupu k prostředkům Azure PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: b53ae66d927dfacd1048a6bd0fa9abdb6f2a7862
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751148"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review"></a>Pomocí řídicího panelu prostředků provádět kontrola přístupu

Řídicí panel prostředků můžete provádět v Privileged Identity Management (PIM) kontrola přístupu k prostředkům Azure. Správce zobrazení řídicí panel obsahuje tři hlavní komponenty:

- Grafické znázornění aktivace role prostředků.
- Dva grafy zobrazují rozdělení přiřazení rolí podle typu přiřazení.
- Datové oblasti, která se týkají nové přiřazení role.

![Snímek obrazovky Správce zobrazení řídicího panelu s grafy a grafy](media/azure-pim-resource-rbac/rbac-overview-top.png)

![Snímek obrazovky Správce zobrazení řídicího panelu s seznamy dat](media/azure-pim-resource-rbac/role-settings.png)

Grafické znázornění aktivace role prostředků obsahuje posledních sedmi dnech. Tato data je vymezen na vybrané prostředky a zobrazuje počet aktivací pro nejběžnější role (vlastník, Přispěvatel, správce přístupu uživatelů) a všechny role kombinaci.

Napravo od grafu aktivací dva grafy zobrazují distribuci přiřazení rolí podle typu přiřazení pro uživatele a skupiny. Hodnota v procentech (nebo naopak), můžete změnit tak, že vyberete řez grafu.

Pod grafy zobrazí počet uživatelů a skupin pomocí nové přiřazení role v posledních 30 dnů a seznam rolí, které jsou seřazené podle celkové přiřazení (v sestupném pořadí).



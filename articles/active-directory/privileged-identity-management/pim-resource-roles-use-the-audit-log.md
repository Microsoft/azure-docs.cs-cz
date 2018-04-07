---
title: Privileged Identity Management pro prostředky Azure - prostředků auditu | Microsoft Docs
description: Vysvětluje, jak získat všechny aktivity role pro zobrazení daný prostředek.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: b8fa7d5600c0de8a3319ea4de785281372959937
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---audit"></a>Privileged Identity Management - role prostředků - Audit

Audit prostředků nabízí zobrazení všech aktivit role pro prostředek. Můžete filtrovat pomocí předdefinovaných datum nebo rozsah vlastní informace.
![](media/azure-pim-resource-rbac/rbac-resource-audit.png)

Audit prostředků taky poskytuje rychlý přístup k zobrazení podrobností aktivity uživatele. V části "Auditu typu" Vyberte "Aktivovat". Klikněte na "(aktivita)" a zobrazte jeho akce na prostředky Azure.
![](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>Můj audit

Moje auditu nabízí zobrazení aktivity osobní role uživatele. Můžete filtrovat pomocí předdefinovaných datum nebo rozsah vlastní informace.
![](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Zobrazení aktivace a aktivita prostředku Azure

V případě, že potřebujete najdete v části které akce trvala konkrétního uživatele na různých prostředků, můžete zkontrolovat prostředků Azure aktivity spojené s danou aktivační období (pro oprávněné uživatele). Začněte výběrem uživatele ze zobrazení členů, nebo v seznamu členů v konkrétní roli. Výsledek zobrazí grafické zobrazení akcí uživatele na prostředky Azure podle data a poslední aktivace role v tomto stejné období.

![](media/azure-pim-resource-rbac/rbac-user-details.png)

Výběr specifické role aktivace se zobrazí podrobnosti o aktivaci role a odpovídající aktivity prostředků Azure, které došlo k chybě tento uživatel byl aktivní.

![](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)


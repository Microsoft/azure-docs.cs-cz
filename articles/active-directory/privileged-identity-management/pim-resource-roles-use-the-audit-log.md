---
title: Audit role prostředků pro prostředků Azure pomocí Privileged Identity managementu | Microsoft Docs
description: Vysvětluje, jak získat všechny aktivity role pro zobrazení daný prostředek.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 211b8c69a1462f7efdcb4002269d96d1d5cf2ae6
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233780"
---
# <a name="audit-resource-roles-for-azure-resources-by-using-privileged-identity-management"></a>Role prostředků auditu pro prostředků Azure pomocí Privileged Identity Management 

Audit prostředků nabízí zobrazení všech aktivit role pro prostředek. Můžete filtrovat pomocí předdefinovaných datum nebo rozsah vlastní informace.
![Informace o filtru](media/azure-pim-resource-rbac/rbac-resource-audit.png)

Audit prostředků taky poskytuje rychlý přístup k podrobnostem aktivity uživatele. V části **Audit typ**, vyberte **aktivovat**. Vyberte **(aktivita)** zobrazíte uživatele akce v prostředků Azure.
![Podrobnosti o aktivitě](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![Další podrobnosti o aktivitě](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>Můj audit

Moje auditu nabízí zobrazení aktivity osobní role uživatele. Můžete filtrovat pomocí předdefinovaných datum nebo rozsah vlastní informace.
![Osobní role aktivity](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Zobrazení aktivace a aktivita prostředku Azure

Pokud chcete zjistit, jaké akce trvala konkrétního uživatele v různých prostředků, můžete zkontrolovat prostředků Azure aktivity, který je spojen s dobou dané aktivace. Začněte tím, že výběr uživatele z **členy** zobrazení nebo v seznamu členů v konkrétní roli. Výsledek zobrazí grafické zobrazení akcí uživatele v prostředky Azure podle data. Také ukazuje poslední aktivace role v tomto stejné období.

![Podrobnosti uživatele](media/azure-pim-resource-rbac/rbac-user-details.png)

Výběr specifické role aktivace zobrazuje podrobnosti o aktivaci role a příslušné aktivity prostředků Azure, které došlo k chybě tento uživatel byl aktivní.

![Vyberte aktivace role](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)


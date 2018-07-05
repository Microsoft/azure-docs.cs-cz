---
title: Auditovat role prostředků pro prostředky Azure s použitím Privileged Identity Management | Dokumentace Microsoftu
description: Vysvětluje, jak získat přehled o aktivitu všech rolí daný prostředek.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 2740297337e1de0d041a80c7860324175413c833
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37447709"
---
# <a name="audit-resource-roles-for-azure-resources-by-using-privileged-identity-management"></a>Auditovat role prostředků pro prostředky Azure s použitím Privileged Identity Management 

Audit prostředků poskytuje přehled všech aktivit role pro prostředek. Můžete filtrovat informace pomocí předdefinované datové nebo vlastní rozsah.
![Informace o filtru](media/azure-pim-resource-rbac/rbac-resource-audit.png)

Audit prostředků také poskytuje rychlý přístup k podrobnosti o aktivitě uživatele. V části **typ auditu**vyberte **aktivovat**. Vyberte **(aktivita)** zobrazíte jeho akce v prostředcích Azure.
![Podrobnosti o aktivitě](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![Další podrobnosti o aktivitě](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>Můj audit

Můj audit poskytuje přehled aktivit osobní role uživatele. Můžete filtrovat informace pomocí předdefinované datové nebo vlastní rozsah.
![Aktivita osobní role](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Zobrazit aktivace a aktivita prostředku Azure

Pokud chcete zjistit, jaké akce v různých prostředků provedli konkrétního uživatele, můžete zkontrolovat aktivity prostředků Azure, který je spojen s danou aktivační období. Začněte tím, že výběr uživatele **členy** zobrazení nebo ze seznamu členů v konkrétní roli. Výsledek zobrazí grafické zobrazení akcí uživatele v Azure prostředky podle data. Také ukazuje poslední aktivace rolí za stejné období čas.

![Podrobnosti uživatele](media/azure-pim-resource-rbac/rbac-user-details.png)

Výběrem konkrétní role aktivace zobrazíte podrobnosti o aktivaci role a odpovídající aktivity prostředků Azure, ke které došlo během tento uživatel byl aktivní.

![Vyberte aktivace role](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)


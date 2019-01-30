---
title: Správa přístupu k Azure Site Recovery s řízením přístupu na základě rolí (RBAC) | Dokumentace Microsoftu
description: Tento článek popisuje, jak použít řízení přístupu na základě rolí (RBAC) ke správě přístupu k Azure Site Recovery.
ms.service: site-recovery
ms.date: 11/27/2018
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 5a6ef10f03bb5e108c226fe3d68f1dec67e6164a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218066"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>Správa přístupu k Site Recovery s řízením přístupu na základě rolí (RBAC)

Řízení přístupu na základě role v Azure umožňuje přesnou správu přístupu. Pomocí RBAC, můžete v rámci týmu oddělit zodpovědnosti a udělit pouze konkrétní přístupová oprávnění pro uživatele podle potřeby provádět konkrétní úlohy.

Azure Site Recovery poskytuje 3 vestavěné role řízení operací správy Site Recovery. Další informace o [předdefinovaných rolích Azure RBAC](../role-based-access-control/built-in-roles.md)

* [Přispěvatel Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-contributor) – Tato role má všechna oprávnění potřebná ke správě operací Azure Site Recovery v trezoru služby Recovery Services. Uživatel s touto rolí však nemůže vytvořit ani odstranit trezor služby Recovery Services ani přiřadit přístup jiným uživatelům. Tato role je nejvhodnější pro správce zotavení po havárii, kteří můžou povolit a spravovat zotavení po havárii pro aplikace nebo celé organizace, tak možné.
* [Operátor Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-operator) – Tato role má oprávnění provádět a spravovat operace převzetí služeb při selhání a navrácení služeb po obnovení. Uživatel s touto rolí nelze povolit nebo zakázat replikaci, vytvořte nebo odstraňovat trezory, registrovat novou infrastrukturu ani přiřadit přístupová práva jiným uživatelům. Tato role je nejvhodnější pro operátory zotavení po havárii, kdo může převzetí služeb při selhání virtuálních počítačů nebo procházet aplikace, když pokyn vlastníků aplikací nebo správci IT v situacích simulované nebo skutečné po havárii, jako je například zotavení po Havárii. Po vyřešení havárie, operátor zotavení po Havárii znovunastavením ochrany a navrácení služeb po obnovení virtuálních počítačů.
* [Čtenář Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-reader) – Tato role má oprávnění zobrazit všechny operace správy Site Recovery. Tato role je nejvhodnější pro vedoucí monitorování IT, kteří můžou monitorovat aktuální stav ochrany a vydávat lístky podpory v případě potřeby.

Pokud potřebujete definovat vlastní role pro ještě větší kontrolu, naleznete v tématu Jak [vytvářet vlastní role](../role-based-access-control/custom-roles.md) v Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Oprávnění potřebná k povolení replikace pro nové virtuální počítače
Při vytvoření nového virtuálního počítače se replikují do Azure pomocí Azure Site Recovery, jsou úrovně přístupu přidruženého uživatele ověřena k zajištění, že uživatel má požadovaná oprávnění k použití prostředků Azure, které jsou k dispozici k Site Recovery.

Pokud chcete povolit replikaci pro nový virtuální počítač, musí mít uživatel:
* Oprávnění k vytvoření virtuálního počítače ve vybrané skupině prostředků
* Oprávnění k vytvoření virtuálního počítače ve vybrané virtuální síti
* Oprávnění k zápisu do vybraného účtu úložiště

Uživatel potřebuje následující oprávnění k dokončení replikace z nového virtuálního počítače.

> [!IMPORTANT]
>Ujistěte se, že jsou přidány příslušné oprávnění na modelu nasazení (Resource Manager nebo Classic) používá pro nasazení prostředků.

| **Typ prostředku** | **Nasazení modelu** | **Oprávnění** |
| --- | --- | --- |
| Compute | Resource Manager | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | Classic | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| Síť | Resource Manager | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Classic | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Storage | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Classic | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Skupina prostředků | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Zvažte použití "Přispěvatel virtuálních počítačů" a "Classic Přispěvatel virtuálních počítačů" [předdefinované role](../role-based-access-control/built-in-roles.md) pro Resource Manager a Klasický model nasazení modelů v uvedeném pořadí.

## <a name="next-steps"></a>Další postup
* [Řízení přístupu na základě rolí](../role-based-access-control/role-assignments-portal.md): Začínáme s RBAC na webu Azure Portal.
* Další informace o správě přístupu pomocí:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Na základě rolí řešení potíží s řízením přístupu](../role-based-access-control/troubleshooting.md): Získáte návrhy pro řešení běžných problémů.

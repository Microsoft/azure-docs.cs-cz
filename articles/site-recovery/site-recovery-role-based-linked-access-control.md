---
title: Správa řízení přístupu na základě rolí Azure v Azure Site Recovery
description: Tento článek popisuje, jak použít řízení přístupu na základě rolí (RBAC) ke správě přístupu k obnovení webu Azure.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: ce389f9281b02662f87353f00c9bca92cdf86937
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257573"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>Správa přístupu k obnovení webu pomocí řízení přístupu na základě rolí (RBAC)

Řízení přístupu na základě rolí Azure (RBAC) umožňuje správu jemně odstupňovaného přístupu pro Azure. Pomocí RBAC, můžete oddělit odpovědnosti v rámci vašeho týmu a udělit pouze konkrétní přístupová oprávnění pro uživatele podle potřeby provádět konkrétní úlohy.

Azure Site Recovery poskytuje 3 předdefinované role pro řízení operací správy obnovení lokality. Další informace o [předdefinovaných rolích Azure RBAC](../role-based-access-control/built-in-roles.md)

* [Přispěvatel Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-contributor) – Tato role má všechna oprávnění potřebná ke správě operací Azure Site Recovery v trezoru služby Recovery Services. Uživatel s touto rolí však nemůže vytvořit ani odstranit trezor služby Recovery Services ani přiřadit přístup jiným uživatelům. Tato role je nejvhodnější pro správce zotavení po havárii, kteří mohou povolit a spravovat zotavení po havárii pro aplikace nebo celé organizace, podle okolností.
* [Operátor Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-operator) – Tato role má oprávnění provádět a spravovat operace převzetí služeb při selhání a navrácení služeb po obnovení. Uživatel s touto rolí nemůže povolit nebo zakázat replikaci, vytvořit nebo odstranit trezory, zaregistrovat novou infrastrukturu nebo přiřadit přístupová práva ostatním uživatelům. Tato role je nejvhodnější pro operátora zotavení po havárii, který může převzetí služeb při selhání virtuální počítače nebo aplikace, když pokyn vlastníci aplikací a it správci ve skutečné nebo simulované katastrofické situace, jako je například vrták zotavení po havárii. Po vyřešení katastrofy může operátor ZOTAVENÍ znovu chránit a navrácení služeb po obnovení virtuálních počítačů.
* [Čtenář Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-reader) – Tato role má oprávnění zobrazit všechny operace správy Site Recovery. Tato role je nejvhodnější pro pracovníka monitorování IT, který může sledovat aktuální stav ochrany a v případě potřeby zvýšit lístky podpory.

Pokud chcete definovat své vlastní role pro ještě větší kontrolu, podívejte se, jak [vytvořit vlastní role](../role-based-access-control/custom-roles.md) v Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Oprávnění potřebná k povolení replikace pro nové virtuální počítače
Když se nový virtuální počítač replikuje do Azure pomocí Azure Site Recovery, ověří se úrovně přístupu přidruženého uživatele, aby bylo zajištěno, že uživatel má požadovaná oprávnění k použití prostředků Azure poskytovaných pro site recovery.

Chcete-li povolit replikaci pro nový virtuální počítač, musí mít uživatel:
* Oprávnění k vytvoření virtuálního počítače ve vybrané skupině prostředků
* Oprávnění k vytvoření virtuálního počítače ve vybrané virtuální síti
* Oprávnění k zápisu do vybraného účtu úložiště

Uživatel potřebuje k dokončení replikace nového virtuálního počítače následující oprávnění.

> [!IMPORTANT]
>Ujistěte se, že příslušná oprávnění jsou přidány podle modelu nasazení (Správce prostředků nebo classic) používané pro nasazení prostředků.

> [!NOTE]
> Pokud povolujete replikaci pro virtuální počítač Azure a chcete povolit obnovení lokality ke správě aktualizací, pak při povolení replikace můžete také chtít vytvořit nový účet Automation, v takovém případě budete potřebovat oprávnění k vytvoření účtu automatizace ve stejném předplatné jako trezor.

| **Typ prostředku** | **Model nasazení** | **Oprávnění** |
| --- | --- | --- |
| Služba Compute | Resource Manager | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | Classic | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| Network (Síť) | Resource Manager | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterface/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/podsítě/čtení |
|  |  | Microsoft.Network/virtualNetworks/podsítě/join/action |
|  | Classic | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Úložiště | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Classic | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Skupina prostředků | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Zvažte použití [předdefinovaných rolí](../role-based-access-control/built-in-roles.md) přispěvatele virtuálního počítače a přispěvatele klasického virtuálního počítače pro správce prostředků a modely klasického nasazení.

## <a name="next-steps"></a>Další kroky
* [Řízení přístupu na základě rolí:](../role-based-access-control/role-assignments-portal.md)Začínáme s RBAC na webu Azure Portal.
* Přečtěte si, jak spravovat přístup pomocí:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [ROZHRANÍ API PRO ODPOČINEK](../role-based-access-control/role-assignments-rest.md)
* [Řešení potíží s řízením přístupu na základě](../role-based-access-control/troubleshooting.md)rolí : Získejte návrhy na řešení běžných problémů.

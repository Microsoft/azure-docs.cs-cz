---
title: Správa řízení přístupu na základě role v Azure v Azure Site Recovery
description: Tento článek popisuje, jak použít řízení přístupu na základě role Azure (Azure RBAC) ke správě přístupu k Azure Site Recovery.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: d3e1334f513e8ac587d639758d83ce080c5b4ab9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516898"
---
# <a name="manage-site-recovery-access-with-azure-role-based-access-control-azure-rbac"></a>Správa přístupu k Site Recovery pomocí řízení přístupu na základě role Azure (RBAC)

Řízení přístupu na základě role Azure (Azure RBAC) umožňuje jemně odstupňovanou správu přístupu pro Azure. Pomocí Azure RBAC můžete oddělit zodpovědnosti v rámci svého týmu a k provádění konkrétních úloh udělit uživatelům pouze konkrétní přístupová oprávnění.

Azure Site Recovery poskytuje 3 předdefinované role pro řízení operací správy Site Recovery. Další informace o [předdefinovaných rolích Azure](../role-based-access-control/built-in-roles.md)

* [Přispěvatel Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-contributor) – Tato role má všechna oprávnění potřebná ke správě operací Azure Site Recovery v trezoru služby Recovery Services. Uživatel s touto rolí však nemůže vytvořit ani odstranit trezor služby Recovery Services ani přiřadit přístup jiným uživatelům. Tato role je nejvhodnější pro správce zotavení po havárii, kteří můžou povolit a spravovat zotavení po havárii pro aplikace nebo celé organizace, a to v případě, že je to možné.
* [Operátor Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-operator) – Tato role má oprávnění provádět a spravovat operace převzetí služeb při selhání a navrácení služeb po obnovení. Uživatel s touto rolí nemůže povolit ani zakázat replikaci, vytvořit nebo odstranit trezory, registrovat novou infrastrukturu nebo přiřazovat přístupová práva jiným uživatelům. Tato role je nejvhodnější pro operátor zotavení po havárii, který může převzít služby při selhání virtuálních počítačů nebo aplikací na základě pokynů pro vlastníky aplikací a správce IT ve skutečné nebo simulované situaci, jako je například zotavení po havárii. Po vyřešení havárie může operátor DR znovu nastavit ochranu a navrátit služby po obnovení virtuálních počítačů.
* [Čtenář Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-reader) – Tato role má oprávnění zobrazit všechny operace správy Site Recovery. Tato role je nejvhodnější pro vedoucí monitorování IT, kteří můžou monitorovat aktuální stav ochrany a v případě potřeby vyvolat lístky podpory.

Pokud chcete definovat vlastní role pro ještě více ovládacích prvků, přečtěte si téma Jak [vytvořit vlastní role](../role-based-access-control/custom-roles.md) v Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Oprávnění vyžadovaná k povolení replikace pro nové virtuální počítače
Při replikaci nového virtuálního počítače do Azure pomocí Azure Site Recovery se ověřují úrovně přístupu přidružených uživatelů, aby se zajistilo, že uživatel má požadovaná oprávnění k používání prostředků Azure poskytovaných k Site Recovery.

Aby bylo možné povolit replikaci pro nový virtuální počítač, musí mít uživatel:
* Oprávnění k vytvoření virtuálního počítače ve vybrané skupině prostředků
* Oprávnění k vytvoření virtuálního počítače ve vybrané virtuální síti
* Oprávnění k zápisu do vybraného účtu úložiště

K dokončení replikace nového virtuálního počítače potřebuje uživatel následující oprávnění.

> [!IMPORTANT]
>Zajistěte, aby se příslušná oprávnění přidala za model nasazení (Správce prostředků/Classic), která se používají pro nasazení prostředků.

> [!NOTE]
> Pokud povolujete replikaci pro virtuální počítač Azure a chcete povolit Site Recovery pro správu aktualizací, pak při povolování replikace můžete také vytvořit nový účet Automation. v takovém případě byste ale měli oprávnění vytvořit účet Automation ve stejném předplatném jako trezor.

| **Typ prostředku** | **Model nasazení** | **Oprávnění** |
| --- | --- | --- |
| Compute | Resource Manager | Microsoft. COMPUTE/availabilitySets/Read |
|  |  | Microsoft. COMPUTE/virtualMachines/Read |
|  |  | Microsoft. COMPUTE/virtualMachines/Write |
|  |  | Microsoft. COMPUTE/virtualMachines/DELETE |
|  | Klasický | Microsoft. ClassicCompute/domainNames/Read |
|  |  | Microsoft. ClassicCompute/domainNames/Write |
|  |  | Microsoft. ClassicCompute/domainNames/DELETE |
|  |  | Microsoft. ClassicCompute/virtualMachines/Read |
|  |  | Microsoft. ClassicCompute/virtualMachines/Write |
|  |  | Microsoft. ClassicCompute/virtualMachines/DELETE |
| Síť | Resource Manager | Microsoft. Network/networkInterfaces/Read |
|  |  | Microsoft. Network/networkInterfaces/Write |
|  |  | Microsoft. Network/networkInterfaces/DELETE |
|  |  | Microsoft. Network/networkInterfaces/JOIN/Action |
|  |  | Microsoft. Network/virtualNetworks/Read |
|  |  | Microsoft. Network/virtualNetworks/podsítí/čtení |
|  |  | Microsoft. Network/virtualNetworks/subnets/JOIN/Action |
|  | Klasický | Microsoft. ClassicNetwork/virtualNetworks/Read |
|  |  | Microsoft. ClassicNetwork/virtualNetworks/JOIN/Action |
| Storage | Resource Manager | Microsoft. Storage/storageAccounts/Read |
|  |  | Microsoft. Storage/storageAccounts/klíče listkey/Action |
|  | Klasický | Microsoft. ClassicStorage/storageAccounts/Read |
|  |  | Microsoft. ClassicStorage/storageAccounts/klíče listkey/Action |
| Skupina prostředků | Resource Manager | Microsoft. Resources/nasazení/* |
|  |  | Microsoft. Resources/Subscriptions/resourceGroups/Read |

Zvažte použití [předdefinovaných rolí](../role-based-access-control/built-in-roles.md) Přispěvatel virtuálních počítačů a klasického přispěvatele virtuálních počítačů pro modely nasazení Správce prostředků a Classic.

## <a name="next-steps"></a>Další kroky
* [Řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md): Začínáme s Azure RBAC v Azure Portal.
* Naučte se spravovat přístup pomocí:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Řešení potíží s Azure RBAC](../role-based-access-control/troubleshooting.md): Získejte návrhy na řešení běžných problémů.

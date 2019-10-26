---
title: Správa přístupu Azure Site Recovery pomocí řízení přístupu na základě role (RBAC) | Microsoft Docs
description: Tento článek popisuje, jak pro správu přístupu k Azure Site Recovery použít řízení přístupu na základě role (RBAC).
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 51c0d832a6d6d9b1cd148f765e68cb77c4679819
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72929223"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>Správa přístupu Site Recovery pomocí řízení přístupu na základě role (RBAC)

Řízení přístupu na základě role v Azure umožňuje přesnou správu přístupu. Pomocí RBAC můžete oddělit zodpovědnosti v rámci svého týmu a k provádění konkrétních úloh udělit uživatelům pouze konkrétní přístupová oprávnění.

Azure Site Recovery poskytuje 3 předdefinované role pro řízení operací správy Site Recovery. Další informace o [předdefinovaných rolích Azure RBAC](../role-based-access-control/built-in-roles.md)

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
> Pokud povolujete replikaci pro virtuální počítač Azure a chcete povolit Site Recovery pro správu aktualizací, pak při povolování replikace můžete také vytvořit nový účet Automation. v takovém případě byste měli mít oprávnění vytvořit účet Automation ve stejném i předplatné jako trezor.

| **Typ prostředku** | **Model nasazení** | **Udělen** |
| --- | --- | --- |
| Služby Compute | Správce prostředků | Microsoft. COMPUTE/availabilitySets/Read |
|  |  | Microsoft. COMPUTE/virtualMachines/Read |
|  |  | Microsoft. COMPUTE/virtualMachines/Write |
|  |  | Microsoft. COMPUTE/virtualMachines/DELETE |
|  | Klasické | Microsoft. ClassicCompute/domainNames/Read |
|  |  | Microsoft. ClassicCompute/domainNames/Write |
|  |  | Microsoft. ClassicCompute/domainNames/DELETE |
|  |  | Microsoft. ClassicCompute/virtualMachines/Read |
|  |  | Microsoft. ClassicCompute/virtualMachines/Write |
|  |  | Microsoft. ClassicCompute/virtualMachines/DELETE |
| Síť | Správce prostředků | Microsoft. Network/networkInterfaces/Read |
|  |  | Microsoft. Network/networkInterfaces/Write |
|  |  | Microsoft. Network/networkInterfaces/DELETE |
|  |  | Microsoft. Network/networkInterfaces/JOIN/Action |
|  |  | Microsoft. Network/virtualNetworks/Read |
|  |  | Microsoft. Network/virtualNetworks/podsítí/čtení |
|  |  | Microsoft. Network/virtualNetworks/subnets/JOIN/Action |
|  | Klasické | Microsoft. ClassicNetwork/virtualNetworks/Read |
|  |  | Microsoft. ClassicNetwork/virtualNetworks/JOIN/Action |
| Úložiště | Správce prostředků | Microsoft. Storage/storageAccounts/Read |
|  |  | Microsoft. Storage/storageAccounts/klíče listkey/Action |
|  | Klasické | Microsoft. ClassicStorage/storageAccounts/Read |
|  |  | Microsoft. ClassicStorage/storageAccounts/klíče listkey/Action |
| Skupina prostředků | Správce prostředků | Microsoft. Resources/nasazení/* |
|  |  | Microsoft. Resources/Subscriptions/resourceGroups/Read |

Zvažte použití [předdefinovaných rolí](../role-based-access-control/built-in-roles.md) Přispěvatel virtuálních počítačů a klasického přispěvatele virtuálních počítačů pro modely nasazení Správce prostředků a Classic.

## <a name="next-steps"></a>Další kroky
* [Access Control založené na rolích](../role-based-access-control/role-assignments-portal.md): Začínáme s RBAC v Azure Portal.
* Naučte se spravovat přístup pomocí:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Řešení potíží s Access Control na základě rolí](../role-based-access-control/troubleshooting.md): Získejte návrhy pro řešení běžných problémů.

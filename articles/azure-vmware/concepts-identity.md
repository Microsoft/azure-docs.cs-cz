---
title: Koncepty – identita a přístup
description: Seznamte se s principy identit a přístupu k řešení Azure VMware.
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 07a7ac8093524ef4240b8f7607d649520b9439e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586246"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Koncepty identity řešení Azure VMware

Privátní cloudy řešení Azure VMware jsou zřízené pomocí serveru vCenter a správce NSX-T. Pomocí vCenter můžete spravovat úlohy virtuálních počítačů. Ke správě a rozšiřování sítě privátního cloudu slouží správce NSX-T.

Správa identit a přístupu vCenter používá Buildin oprávnění skupiny CloudAdmin. Správce NSX-T používá omezená oprávnění správce. To je podle povahy spravované služby a zajišťuje, aby se vaše privátní cloudová platforma Upgradoval s nejnovějšími funkcemi a opravami, které se mají očekávat.  Další informace najdete v [článku koncepty pro upgrade privátního cloudu][concepts-upgrades].

## <a name="vcenter-access-and-identity"></a>přístup k vCenter a identita

Skupina vCenter CloudAdmin definuje a poskytuje oprávnění v vCenter. Další možností je poskytnout přístup a identitu prostřednictvím integrace jednotného přihlašování vCenter protokolu vCenter s Azure Active Directory. Tuto integraci povolíte po nasazení privátního cloudu. 

V tabulce jsou uvedena oprávnění **CloudAdmin** a **CloudGlobalAdmin** .

|  Sada oprávnění           | CloudAdmin | CloudGlobalAdmin | Komentář |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Upozornění                  | CloudAdmin uživatel má všechna alarmy oprávnění k alarmům v Compute-ResourcePool a virtuálních počítačích.     |          --        |  -- |
|  Automatické nasazení             |  --  |        --        |  Microsoft provádí správu hostitelů.  |
|  Certifikáty            |  --  |        --       |  Microsoft provádí správu certifikátů.  |
|  Knihovna obsahu         | Uživatel CloudAdmin má oprávnění k vytváření a používání souborů v knihovně obsahu.    |         Povoleno s jednotným přihlašováním.         |  Společnost Microsoft bude distribuovat soubory do knihovny obsahu do hostitelů ESXi.  |
|  Datové centrum              |  --  |        --          |  Microsoft provádí všechna data centra operací.  |
|  Úložiště dat               | Úložiště dat. AllocateSpace, DataStore. Browse, Datastore.Config, DataStore. DeleteFile, DataStore. správy, DataStore. UpdateVirtualMachineMetadata     |    --    |   -- |
|  Správce agenta ESX       |  --  |         --       |  Společnost Microsoft provádí všechny operace.  |
|  Složka                  |  CloudAdmin uživatel má všechna oprávnění ke složkám.     |  --  |  --  |
|  Globální                  |  Global. CancelTask, Global. GlobalTag, Global. Health, Global. LogEvent, Global. ManageCustomFields, Global. ServiceManagers, Global. SetCustomField, Global.SystemTag         |                  |    |
|  Hostitel                    |  Host. HBR. HbrManagement      |        --          |  Microsoft provádí všechny ostatní hostitelské operace.  |
|  InventoryService        |  InventoryService. označování      |        --          |  --  |
|  Síť                 |  Síť. přiřadit    |                  |  Microsoft provádí všechny ostatní síťové operace.  |
|  Oprávnění             |  --  |        --       |  Microsoft provádí všechny operace s oprávněními.  |
|  Úložiště řízené profilem  |  --  |        --       |  Microsoft provádí všechny operace s profilem.  |
|  Prostředek                |  Uživatel CloudAdmin má všechna oprávnění prostředků.        |      --       | --   |
|  Naplánovaná úloha          |  CloudAdmin uživatel má všechna oprávnění ScheduleTask –.   |   --   | -- |
|  Relace                |  Sessions. GlobalMessage, Sessions. ValidateSession      |   --   |  Společnost Microsoft provede všechny ostatní operace s relacemi.  |
|  Zobrazení úložišť           |  StorageViews. View   |        --          |  Microsoft provede všechny ostatní operace zobrazení úložiště (konfigurace služby).  |
|  Úlohy                   |  --  |  --   |  Microsoft spravuje rozšíření, která spravují úlohy.  |
|  vApp                    |  CloudAdmin uživatel má všechna oprávnění vApp.  |  --  |  --  |
|  Virtuální počítač         |  CloudAdmin uživatel má všechna oprávnění VirtualMachine.  |  --  |  --  |
|  vService                |  CloudAdmin uživatel má všechna oprávnění vService.  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>Přístup a identita správce NSX – T

Pro přístup ke Správci NSX-T použijte účet *správce* . Má úplná oprávnění a umožňuje vytvářet a spravovat brány (T1) vrstvy 1 (T1), segmenty (logické přepínače) a všechny služby. Tento účet také poskytuje přístup k bráně NSX-T úrovně 0 (T0). Při provádění takových změn byste měli být vědomi, protože by mohlo dojít ke snížení výkonu sítě nebo přístupu k privátnímu cloudu. Otevřete žádost o podporu v Azure Portal, abyste požádali o změny v bráně NSX-T T0.
  
## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s koncepty přístupu k řešení a identitou Azure VMware, se můžete seznámit s těmito informacemi:

- [Koncepce upgradu privátního cloudu](concepts-upgrades.md).
- [vSphere řízení přístupu na základě role pro řešení Azure VMware](concepts-role-based-access-control.md).
- [Jak povolit prostředek řešení Azure VMware](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md

---
title: Koncepty – identita a přístup
description: Seznamte se s principy identit a přístupu k řešení Azure VMware.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 7127109801d92d2177f6edac3efcaf76ddf217e6
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674654"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Koncepty identity řešení Azure VMware

Při nasazení privátního cloudu se zřídí Server vCenter a správce NSX-T. Pomocí vCenter můžete spravovat úlohy virtuálních počítačů. NSX-T Manager slouží k rozšiřování sítě definované privátním cloudem.

Přístup a Správa identit používají pro správce NSX-T oprávnění skupin CloudAdmin pro vCenter a omezená práva správce. Zajišťuje automatickou inovaci vaší privátní cloudové platformy pomocí nejnovějších funkcí a oprav.  Další informace najdete v [článku koncepty pro upgrade privátního cloudu][concepts-upgrades].

## <a name="vcenter-access-and-identity"></a>přístup k vCenter a identita

Oprávnění v vCenter jsou k dispozici prostřednictvím skupiny CloudAdmin. Tuto skupinu je možné spravovat místně v vCenter nebo prostřednictvím integrace jednotného přihlašování vCenter protokolu vCenter s Azure Active Directory. Po nasazení privátního cloudu jste dostali možnost povolit tuto integraci.

V následující tabulce jsou uvedena oprávnění CloudAdmin a CloudGlobalAdmin.

|  Sada oprávnění           | CloudAdmin | CloudGlobalAdmin | Komentář |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Upozornění                  | CloudAdmin uživatel má všechna alarmy oprávnění k alarmům v Compute-ResourcePool a virtuálních počítačích.     |          --        |  -- |
|  Automatické nasazení             |  --  |        --        |  Microsoft provádí správu hostitelů.  |
|  Certifikáty            |  --  |        --       |  Microsoft provádí správu certifikátů.  |
|  Knihovna obsahu         | Uživatel CloudAdmin má oprávnění k vytváření a používání souborů v knihovně obsahu.    |         Povoleno s jednotným přihlašováním.         |  Společnost Microsoft bude distribuovat soubory do knihovny obsahu do hostitelů ESXi.  |
|  Datacentrum              |  --  |        --          |  Microsoft provádí všechna data centra operací.  |
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

Pro přístup ke Správci NSX-T použijte účet správce. Má úplná oprávnění a umožňuje vytváření a správu směrovačů T1, logických přepínačů a všech služeb. Oprávnění poskytují přístup ke směrovači NSX-T T0. Změna směrovače T0 by mohla způsobit snížení výkonu sítě nebo přístupu k privátnímu cloudu. Otevřete žádost o podporu v Azure Portal, abyste požádali o změny ve směrovači NSX-T T0.
  
## <a name="next-steps"></a>Další kroky

V dalším kroku se dozvíte o [konceptech upgradu privátního cloudu][concepts-upgrades].

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
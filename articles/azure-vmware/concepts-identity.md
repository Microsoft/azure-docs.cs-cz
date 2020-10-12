---
title: Koncepty – identita a přístup
description: Seznamte se s principy identit a přístupu k řešení Azure VMware.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 9d5b7b4c7c2e0d55cffc99a3f371494f40320a16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88750588"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Koncepty identity řešení Azure VMware

Server vCenter a správce NSX-T se zřídí při nasazení privátního cloudu. Pomocí vCenter můžete spravovat úlohy virtuálních počítačů a správce NSX-T a rozšíříte tak síť definovanou v privátním cloudovém softwaru.

Přístup a Správa identit používají pro správce NSX-T oprávnění skupin CloudAdmin pro vCenter a omezená práva správce. Tato zásada zajišťuje, že vaše privátní cloudová platforma se dá upgradovat automaticky. Tím se pravidelně doručí nejnovější funkce a opravy. Další podrobnosti o upgradech privátního cloudu najdete v [článku koncepty pro upgrade privátního cloudu][concepts-upgrades] .

## <a name="vcenter-access-and-identity"></a>přístup k vCenter a identita

Oprávnění v vCenter jsou k dispozici prostřednictvím skupiny CloudAdmin. Tuto skupinu je možné spravovat místně v vCenter nebo prostřednictvím integrace jednotného přihlašování vCenter protokolu vCenter s Azure Active Directory. Po nasazení privátního cloudu jste dostali možnost povolit tuto integraci.

V následující tabulce jsou uvedena oprávnění CloudAdmin a CloudGlobalAdmin.

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

K NSX Manageru se dostanete pomocí účtu Administrator (správce). Tento účet má úplná oprávnění a umožňuje vytváření a správu směrovačů T1, logických přepínačů a všech služeb. Úplná oprávnění v NSX-T také poskytují přístup ke směrovači NSX-T T0. Změna směrovače T0 by mohla způsobit snížení výkonu sítě nebo ztráty přístupu k privátnímu cloudu. Aby se splnily požadavky na podporu, je potřeba, abyste v Azure Portal otevřeli žádost o podporu, abyste mohli požádat o změny ve směrovači NSX-T T0.
  
## <a name="next-steps"></a>Další kroky

V dalším kroku se dozvíte o [konceptech upgradu privátního cloudu][concepts-upgrades].

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
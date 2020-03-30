---
title: Nastavení aplikace pro migraci Azure
description: Zjistěte, jak nastavit zařízení Azure Migrate pro posouzení a migraci virtuálních počítačích VMware.
ms.topic: article
ms.date: 11/18/2019
ms.openlocfilehash: 0447443bb6a642cac09566450ea2d9bb6f6453d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337659"
---
# <a name="set-up-an-azure-migrate-appliance"></a>Nastavení zařízení Azure Migrate

Tento článek shrnuje možnosti nastavení zařízení Azure Migrate. 

Zařízení Migrace Azure je nasazení zařízení místní a používá se v řadě scénářů.

**Scénář** | **Podrobnosti**
--- | ---
Posouzení virtuálních počítačů VMware pomocí azure migrate:vyhodnocení serveru | Zjišťování aplikací a závislostí virtuálních mandisířů<br/><br/> Shromažďujte metadata počítače a metadata výkonu pro hodnocení.
Posouzení virtuálních počítačů Hyper-V pomocí řešení Azure Migrate:Server Assessment | Objevte virtuální aplikace Hyper-V<br/><br/> Shromažďujte metadata počítače a metadata výkonu pro hodnocení.
Posouzení fyzických strojů | Objevte počítače jako fyzické servery<br/><br/> Shromažďujte metadata počítače a metadata výkonu pro hodnocení.
Replikujte virtuální virtuální maje v v oblasti VMware s migrací bez agenta. | Replikujte virtuální počítače VMware, aniž byste na virtuální počítači cokoli nainstalovali, co chcete replikovat.


## <a name="deployment-options"></a>Možnosti nasazení

Zařízení můžete nasadit několika způsoby.

**Scénář** | **Šablony** | **Skript** 
--- | --- | --- | ---
**Posouzení virtuálních měn VMware** | Nasazení se staženou šablonou OVA.<br/><br/> Naučte se, jak nasadit zařízení [pomocí šablony](how-to-set-up-appliance-vmware.md), nebo spustit [kurz hodnocení](tutorial-prepare-vmware.md) a nasadit zařízení se šablonou během kurzu.  | Nasazení pomocí instalačního skriptu prostředí PowerShell.<br/><br/>  [Zkontrolujte](deploy-appliance-script.md) pokyny ke skriptu zařízení.
**Posouzení virtuálních měn Hyper-V** | Nasazení se staženou šablonou Virtuálního pevného disku <br/><br/> Naučte se, jak nasadit zařízení [pomocí šablony](how-to-set-up-appliance-vmware.md), nebo spustit [kurz hodnocení](tutorial-prepare-vmware.md) a nasadit zařízení se šablonou během kurzu. | Nasazení pomocí instalačního skriptu prostředí PowerShell.<br/><br/> [Zkontrolujte](deploy-appliance-script.md) pokyny ke skriptu zařízení. 
**Vyhodnocení fyzických serverů** | Žádná šablona. | Nasazení pomocí instalačního skriptu prostředí PowerShell.<br/><br/> Zkontrolujte [pokyny ke skriptu zařízení](how-to-set-up-appliance-physical.md)nebo spusťte kurz [hodnocení](tutorial-prepare-physical.md)a nasaďte zařízení během kurzu.
**Replikace virtuálních měn VMware (bez agentů)** | Nasazení se staženou šablonou OVA.<br/><br/> Pokud jste už vyhodnotili virtuální chod, které replikujete, tak jste už nastavili zařízení během hodnocení.<br/><br/> Pokud replikujete virtuální počítače VMware bez jejich posouzení, přečtěte si, jak nasadit zařízení pomocí šablony nebo spustit [kurz migrace bez agenta](tutorial-migrate-vmware.md)a nasadit zařízení se šablonou během kurzu. | Nasazení pomocí instalačního skriptu prostředí PowerShell. <br/><br/> [Zkontrolujte](deploy-appliance-script.md) pokyny ke skriptu zařízení. 




## <a name="next-steps"></a>Další kroky

[Zkontrolujte](migrate-appliance.md) požadavky na spotřebiče.
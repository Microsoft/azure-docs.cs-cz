---
title: Výběr možnosti migrace VMware pomocí migrace serveru Azure Migrate | Microsoft Docs
description: Poskytuje přehled možností migrace virtuálních počítačů VMware do Azure pomocí migrace Azure Migrate serveru.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 68026af5b96728ea66fd9e584e67e5e596e690fb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974661"
---
# <a name="select-a-vmware-migration-option"></a>Vybrat možnost migrace VMware

Virtuální počítače VMware můžete migrovat do Azure pomocí nástroje pro migraci Azure Migrate serveru. Tento nástroj nabízí několik možností migrace virtuálních počítačů VMware:

- Migrace pomocí replikace bez agentů. Migrujte virtuální počítače, aniž byste museli instalovat cokoli.
- Migrace s agentem pro replikaci. Nainstalujte na virtuální počítač agenta pro replikaci.




## <a name="compare-migration-methods"></a>Porovnání metod migrace

Tato vybraná porovnání vám pomůžou rozhodnout, kterou metodu použít. Můžete si také projít úplné požadavky na podporu pro migraci bez [agentů](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) a [na základě agentů](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) .

**Nastavení** | **Bez agenta** | **Založené na agentovi**
--- | --- | ---
**Oprávnění Azure** | Potřebujete oprávnění k vytvoření projektu Azure Migrate a k registraci aplikací služby Azure AD vytvořených při nasazení Azure Migrateho zařízení. | V předplatném Azure potřebujete oprávnění Přispěvatel. 
**Současná replikace** | Z vCenter Server lze souběžně replikovat maximálně 100 virtuálních počítačů.<br/> Pokud máte pro migraci více než 50 virtuálních počítačů, vytvořte několik dávek virtuálních počítačů.<br/> Další replikace bude mít vliv na výkon. | není k dispozici
**Nasazení zařízení** | [Zařízení Azure Migrate](migrate-appliance.md) je nasazené místně. | [Zařízení replikace Azure Migrate](migrate-replication-appliance.md) je nasazené místně.
**Site Recovery kompatibilita** | Kompatibility. | Pokud jste pro počítač nastavili replikaci pomocí Site Recovery, nemůžete replikovat pomocí Azure Migrate migrace serveru.
**Cílový disk** | Spravované disky | Spravované disky
**Omezení disku** | Disk s operačním systémem: 2 TB<br/><br/> Datový disk: 4 TB<br/><br/> Maximální počet disků: 60 | Disk s operačním systémem: 2 TB<br/><br/> Datový disk: 8 TB<br/><br/> Maximální počet disků: 63
**Průchozí disky** | Nepodporováno | Podporováno
**Spouštění UEFI** | Nepodporováno | Migrovaný virtuální počítač v Azure se automaticky převede na spouštěcí virtuální počítač se systémem BIOS.<br/><br/> Disk s operačním systémem by měl mít až čtyři oddíly a svazky by měly být naformátované pomocí systému souborů NTFS.


## <a name="deployment-steps-comparison"></a>Porovnání kroků nasazení

Po kontrole omezení se může porozumět postupům při nasazení jednotlivých řešení, které vám pomůžou rozhodnout, kterou možnost zvolit.

**Úkol** | **Podrobnosti** |**Bez agenta** | **Založené na agentovi**
--- | --- | --- | ---
**Posouzení** | Před migrací posuzuje servery.  Posouzení je volitelné. Doporučujeme, abyste počítače vyhodnotili před migrací, ale nemusíte je. <br/><br/> Pro vyhodnocení Azure Migrate nastaví odlehčené zařízení pro zjišťování a vyhodnocení virtuálních počítačů. | Pokud po posouzení spustíte migraci bez agenta, pro migraci bez agenta se použije stejná Azure Migrate zařízení nastavená pro posouzení.  |  Pokud po posouzení spustíte migraci založenou na agentech, zařízení nastavené pro posouzení se během migrace bez agentů nepoužije. Zařízení můžete nechat na místě nebo ho odebrat, pokud nechcete provádět další zjišťování a hodnocení.
**Příprava serverů a virtuálních počítačů VMware pro migraci** | Nakonfigurujte řadu nastavení na serverech VMware a virtuálních počítačích. | Požaduje se | Požaduje se
**Přidání nástroje pro migraci serveru** | Přidejte Nástroj pro migraci Azure Migrate serveru do projektu Azure Migrate. | Požaduje se | Požaduje se
**Nasazení zařízení Azure Migrate** | Nastavte zjednodušené zařízení na virtuálním počítači VMware pro zjišťování a hodnocení virtuálních počítačů. | Požaduje se | Není nutné.
**Instalace služby mobility na virtuální počítače** | Nainstalujte službu mobility na každý virtuální počítač, který chcete replikovat. | Nepožaduje se | Požaduje se
**Nasazení zařízení replikace migrace serveru Azure Migrate** | Nastavení zařízení na virtuálním počítači VMware pro zjišťování virtuálních počítačů a přemostění mezi službou mobility běžícími na virtuálních počítačích a migrací serveru Azure Migrate | Nepožaduje se | Požaduje se
**Replikace virtuálních počítačů**. Povolte replikaci virtuálních počítačů. | Nakonfigurujte nastavení replikace a vyberte virtuální počítače, které se mají replikovat. | Požaduje se | Požaduje se
**Spustit test migrace** | Spusťte test migrace a ujistěte se, že vše funguje podle očekávání. | Požaduje se | Požaduje se
**Spustit úplnou migraci** | Migrujte virtuální počítače. | Požaduje se | Požaduje se




## <a name="next-steps"></a>Další kroky

[Migrujte virtuální počítače VMware](tutorial-migrate-vmware.md) s migrací bez agentů.




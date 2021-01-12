---
title: Výběr možnosti migrace VMware pomocí migrace serveru Azure Migrate
description: Poskytuje přehled možností migrace virtuálních počítačů VMware do Azure pomocí migrace Azure Migrate serveru.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 130fcaacedc8aaea7790f6aa9ca7463b4e378e02
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070707"
---
# <a name="select-a-vmware-migration-option"></a>Vybrat možnost migrace VMware

Virtuální počítače VMware můžete migrovat do Azure pomocí nástroje pro migraci Azure Migrate serveru. Tento nástroj nabízí několik možností migrace virtuálních počítačů VMware:

- Migrace pomocí replikace bez agentů. Migrujte virtuální počítače, aniž byste museli instalovat cokoli.
- Migrace s agentem pro replikaci. Nainstalujte na virtuální počítač agenta pro replikaci.


## <a name="compare-migration-methods"></a>Porovnání metod migrace

Tato vybraná porovnání vám pomůžou rozhodnout, kterou metodu použít. Můžete si také projít úplné požadavky na podporu pro migraci bez [agentů](migrate-support-matrix-vmware-migration.md#agentless-migration) a [na základě agentů](migrate-support-matrix-vmware-migration.md#agent-based-migration) .

**Nastavení** | **Bez agenta** | **Založené na agentovi**
--- | --- | ---
**Oprávnění Azure** | Potřebujete oprávnění k vytvoření projektu Azure Migrate a k registraci aplikací služby Azure AD vytvořených při nasazení Azure Migrateho zařízení. | V předplatném Azure potřebujete oprávnění Přispěvatel. 
**Replikace** | Z vCenter Server lze souběžně replikovat maximálně 300 virtuálních počítačů.<br/> Pokud máte pro migraci více než 50 virtuálních počítačů, vytvořte několik dávek virtuálních počítačů.<br/> Další replikace bude mít vliv na výkon.<br/><br/> Na portálu můžete pro replikaci vybrat až 10 počítačů najednou. Pokud chcete replikovat více počítačů, přidejte je do dávek po 10.| Kapacita replikace se zvyšuje škálováním zařízení replikace.
**Nasazení zařízení** | [Zařízení Azure Migrate](migrate-appliance.md) je nasazené místně. | [Zařízení replikace Azure Migrate](migrate-replication-appliance.md) je nasazené místně.
**Site Recovery kompatibilita** | Kompatibility. | Pokud jste pro počítač nastavili replikaci pomocí Site Recovery, nemůžete replikovat pomocí Azure Migrate migrace serveru.
**Cílový disk** | Spravované disky | Spravované disky
**Omezení disku** | Disk s operačním systémem: 2 TB<br/><br/> Datový disk: 32 TB<br/><br/> Maximální počet disků: 60 | Disk s operačním systémem: 2 TB<br/><br/> Datový disk: 32 TB<br/><br/> Maximální počet disků: 63
**Průchozí disky** | Nepodporováno | Podporováno
**Spouštění UEFI** | Podporuje se. | Podporuje se.

## <a name="compare-deployment-steps"></a>Porovnání kroků nasazení

Po kontrole omezení se může porozumět postupům při nasazení jednotlivých řešení, které vám pomůžou rozhodnout, kterou možnost zvolit.

**Úkol** | **Podrobnosti** |**Bez agenta** | **Založené na agentovi**
--- | --- | --- | ---
**Nasazení zařízení se službou Azure Migrate** | Jednoduché zařízení, které běží na virtuálním počítači VMware.<br/><br/> Zařízení se používá ke zjišťování a hodnocení počítačů a k migraci počítačů pomocí migrace bez agentů. | Povinná hodnota.<br/><br/> Pokud jste už zařízení pro posouzení nastavili, můžete použít stejné zařízení pro migraci bez agenta. | Nevyžadují se.<br/><br/> Pokud jste pro posouzení nastavili zařízení, můžete ho nechat na místě, nebo ho odebrat, pokud jste hotovi s posouzením.
**Použití nástroje pro vyhodnocení serveru** | Vyhodnoťte počítače pomocí nástroje Azure Migrate: Nástroj pro vyhodnocení serveru. | Je možné vyhodnotit počítače před jejich migrací, ale nemusíte je. | Posouzení je volitelné.
**Použití nástroje pro migraci serveru** | Přidejte Nástroj pro migraci Azure Migrate serveru do projektu Azure Migrate. | Povinné | Povinné
**Příprava VMware na migraci** | Nakonfigurujte nastavení na serverech a virtuálních počítačích VMware. | Povinné | Povinné
**Instalace služby mobility na virtuální počítače** | Služba mobility běží na každém virtuálním počítači, který chcete replikovat. | Nevyžadováno | Povinné
**Nasazení zařízení replikace** | [Zařízení replikace](migrate-replication-appliance.md) se používá pro migraci na základě agenta. Připojuje se ke službě mobility spuštěné na virtuálních počítačích a migraci serveru. | Nevyžadováno | Povinné
**Replikace virtuálních počítačů**. Povolte replikaci virtuálních počítačů. | Nakonfigurujte nastavení replikace a vyberte virtuální počítače, které se mají replikovat. | Povinné | Povinné
**Spuštění testu migrace** | Spusťte test migrace a ujistěte se, že vše funguje podle očekávání. | Povinné | Povinné
**Spustit úplnou migraci** | Migrujte virtuální počítače. | Povinné | Povinné



## <a name="next-steps"></a>Další kroky

[Migrujte virtuální počítače VMware](tutorial-migrate-vmware.md) s migrací bez agentů.




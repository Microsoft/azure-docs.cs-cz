---
title: Výběr možnosti migrace VMware pomocí migrace serveru Azure Migrate
description: Poskytuje přehled možností migrace virtuálních počítačů VMware do Azure pomocí migrace Azure Migrate serveru.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 22a0629d50ee8181ffcbfe7dad32ab76fb3e68fd
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714159"
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
**Replikace** | Z vCenter Server lze souběžně replikovat maximálně 500 virtuálních počítačů. Na portálu můžete pro replikaci vybrat až 10 počítačů najednou. Pokud chcete replikovat více počítačů, přidejte je do dávek po 10.| Kapacita replikace se zvyšuje škálováním zařízení replikace.
**Nasazení zařízení** | [Zařízení Azure Migrate](migrate-appliance.md) je nasazené místně. | [Zařízení replikace Azure Migrate](migrate-replication-appliance.md) je nasazené místně.
**Site Recovery kompatibilita** | Kompatibility. | Pokud jste pro počítač nastavili replikaci pomocí Site Recovery, nemůžete replikovat pomocí Azure Migrate migrace serveru.
**Cílový disk** | Spravované disky | Spravované disky
**Omezení disku** | Disk s operačním systémem: 2 TB<br/><br/> Datový disk: 32 TB<br/><br/> Maximální počet disků: 60 | Disk s operačním systémem: 2 TB<br/><br/> Datový disk: 32 TB<br/><br/> Maximální počet disků: 63
**Průchozí disky** | Nepodporováno | Podporováno
**Spouštění UEFI** | Podporuje se. | Podporuje se. 
**Připojení** | Veřejný Internet <br/> ExpressRoute s partnerským vztahem Microsoftu <br/> <br/> [Naučte](./replicate-using-expressroute.md) se používat privátní koncové body pro replikaci prostřednictvím privátního partnerského vztahu ExpressRoute nebo připojení S2S VPN. |Veřejný Internet <br/> ExpressRoute se soukromým partnerským vztahem <br/> ExpressRoute s partnerským vztahem Microsoftu <br/> Site-to-site VPN

## <a name="compare-deployment-steps"></a>Porovnání kroků nasazení

Po kontrole omezení se může porozumět postupům při nasazení jednotlivých řešení, které vám pomůžou rozhodnout, kterou možnost zvolit.

**Úkol** | **Podrobnosti** |**Bez agenta** | **Založené na agentovi**
--- | --- | --- | ---
**Nasazení zařízení se službou Azure Migrate** | Jednoduché zařízení, které běží na virtuálním počítači VMware.<br/><br/> Zařízení se používá ke zjišťování a hodnocení počítačů a k migraci počítačů pomocí migrace bez agentů. | Povinná hodnota.<br/><br/> Pokud jste už zařízení pro posouzení nastavili, můžete použít stejné zařízení pro migraci bez agenta. | Nevyžadují se.<br/><br/> Pokud jste pro posouzení nastavili zařízení, můžete ho nechat na místě, nebo ho odebrat, pokud jste hotovi s posouzením.
**Použití nástroje pro vyhodnocení serveru** | Vyhodnoťte počítače pomocí nástroje Azure Migrate: Nástroj pro vyhodnocení serveru. | Posouzení je volitelné. | Posouzení je volitelné.
**Použití nástroje pro migraci serveru** | Přidejte Nástroj pro migraci Azure Migrate serveru do projektu Azure Migrate. | Vyžadováno | Vyžadováno
**Příprava VMware na migraci** | Nakonfigurujte nastavení na serverech a virtuálních počítačích VMware. | Vyžadováno | Vyžadováno
**Instalace služby mobility na virtuální počítače** | Služba mobility běží na každém virtuálním počítači, který chcete replikovat. | Nevyžadováno | Vyžadováno
**Nasazení zařízení replikace** | [Zařízení replikace](migrate-replication-appliance.md) se používá pro migraci na základě agenta. Připojuje se ke službě mobility spuštěné na virtuálních počítačích a migraci serveru. | Nevyžadováno | Vyžadováno
**Replikace virtuálních počítačů**. Povolte replikaci virtuálních počítačů. | Nakonfigurujte nastavení replikace a vyberte virtuální počítače, které se mají replikovat. | Vyžadováno | Vyžadováno
**Spuštění testu migrace** | Spusťte test migrace a ujistěte se, že vše funguje podle očekávání. | Vyžadováno | Vyžadováno
**Spustit úplnou migraci** | Migrujte virtuální počítače. | Vyžadováno | Vyžadováno



## <a name="next-steps"></a>Další kroky

[Migrujte virtuální počítače VMware](tutorial-migrate-vmware.md) s migrací bez agentů.




---
title: Výběr možnosti migrace vsystému VMware pomocí migrace serveru Azure | Dokumenty společnosti Microsoft
description: Obsahuje přehled možností migrace virtuálních počítačích VMware do Azure pomocí migrace serveru Azure.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 52e7103ea3ebcd83369a866cc3f75b0bf0e889a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028713"
---
# <a name="select-a-vmware-migration-option"></a>Výběr možnosti migrace vmware

Virtuální počítače VMware můžete migrovat do Azure pomocí nástroje Migrace serveru Azure. Tento nástroj nabízí několik možností pro migraci virtuálních zařízení VMware:

- Migrace pomocí replikace bez agenta. Migrujte virtuální počítače, aniž byste na ně museli nic instalovat.
- Migrace s agentem pro replikaci. Nainstalujte agenta na virtuální počítač pro replikaci.




## <a name="compare-migration-methods"></a>Porovnat metody migrace

Pomocí těchto vybraných porovnání můžete rozhodnout, kterou metodu použít. Můžete také zkontrolovat úplné požadavky na podporu pro [migraci bez agenta](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) a [na základě agenta.](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers)

**Nastavení** | **Bez agenta** | **Na základě agenta**
--- | --- | ---
**Oprávnění Azure** | Potřebujete oprávnění k vytvoření projektu Migrace Azure a k registraci aplikací Azure AD vytvořených při nasazení zařízení Azure Migrate. | Potřebujete oprávnění přispěvatele pro předplatné Azure. 
**Současná replikace** | Maximálně 100 virtuálních m můžete současně replikovat ze serveru vCenter.<br/> Pokud máte více než 50 virtuálních počítačů pro migraci, vytvořte více dávek virtuálních počítačů.<br/> Replikace více najednou bude mít vliv na výkon. | Není k dispozici
**Nasazení zařízení** | [Zařízení Azure Migrate](migrate-appliance.md) se nasazuje místně. | [Zařízení Migrace replikace Azure](migrate-replication-appliance.md) se nasazuje místně.
**Kompatibilita obnovení webu** | Kompatibilní. | S migrací serveru Azure se nedá replikovat, pokud jste nastavili replikaci pro počítač pomocí site recovery.
**Cílový disk** | Spravované disky | Spravované disky
**Omezení disku** | Disk s os: 2 TB<br/><br/> Datový disk: 4 TB<br/><br/> Maximální počet disků: 60 | Disk s os: 2 TB<br/><br/> Datový disk: 8 TB<br/><br/> Maximální počet disků: 63
**Průchozí disky** | Nepodporuje se | Podporuje se
**UEFI bota** | Nepodporuje se | Migrovaný virtuální počítač v Azure se automaticky převede na spouštěcí virtuální počítač systému BIOS.<br/><br/> Disk operačního systému by měl mít až čtyři oddíly a svazky by měly být formátovány pomocí systému souborů NTFS.


## <a name="deployment-steps-comparison"></a>Porovnání kroků nasazení

Po kontrole omezení vám pochopení kroků při nasazování jednotlivých řešení může pomoci rozhodnout, kterou možnost zvolit.

**Úkol** | **Podrobnosti** |**Bez agenta** | **Na základě agenta**
--- | --- | --- | ---
**Posouzení** | Vyhodnoťte servery před migrací.  Hodnocení je nepovinné. Doporučujeme, abyste stroje posoudili před jejich migrací, ale nemusíte. <br/><br/> Pro posouzení Azure Migrate nastaví zjednodušené zařízení pro zjišťování a hodnocení virtuálních počítačů. | Pokud po vyhodnocení spustíte migraci bez agenta, stejné zařízení Azure Migrate nastavené pro vyhodnocení se používá pro migraci bez agenta.  |  Pokud spustíte migraci na základě agenta po posouzení, zařízení nastavené pro posouzení se nepoužívá během migrace bez agenta. Přístroj můžete nechat na místě nebo jej vyjmout, pokud nechcete provést další zjišťování a hodnocení.
**Příprava serverů a virtuálních počítačů VMware na migraci** | Nakonfigurujte řadu nastavení na serverech a virtuálních počítačích VMware. | Požaduje se | Požaduje se
**Přidání nástroje pro migraci serveru** | Přidejte nástroj migrace serveru Azure v projektu Migrace Azure. | Požaduje se | Požaduje se
**Nasazení zařízení Azure Migrate** | Nastavte lehké zařízení na virtuálním počítači VMware pro zjišťování a hodnocení virtuálních počítačů. | Požaduje se | Není vyžadováno.
**Instalace služby Mobility na virtuálních počítačích** | Instalace služby Mobility na každý virtuální počítač, který chcete replikovat | Není požadováno | Požaduje se
**Nasazení zařízení replikace migrace serveru Azure** | Nastavení zařízení na virtuálním počítači VMware ke zjišťování virtuálních počítačů a přemostění mezi službou Mobility spuštěnou na virtuálních počítačích a migrací serveru Azure | Není požadováno | Požaduje se
**Replikovat virtuální chod .** Povolte replikaci virtuálních montovny. | Konfigurace nastavení replikace a výběr virtuálních počítače k replikaci | Požaduje se | Požaduje se
**Spuštění testu migrace** | Spusťte testovací migraci a ujistěte se, že vše funguje podle očekávání. | Požaduje se | Požaduje se
**Spuštění úplné migrace** | Migrujte virtuální chod. | Požaduje se | Požaduje se




## <a name="next-steps"></a>Další kroky

[Migrujte virtuální chod v systému VMware](tutorial-migrate-vmware.md) s migrací bez agenta.




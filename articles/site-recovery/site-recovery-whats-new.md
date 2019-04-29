---
title: Co je nového ve službě Azure Site Recovery | Dokumentace Microsoftu
description: Poskytuje souhrnné informace o nové vlastnosti představené ve službě Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: raynew
ms.openlocfilehash: 5ee1328dddb6ae1e1c878384097b0e10aa32feeb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776284"
---
# <a name="whats-new-in-site-recovery"></a>Co je nového ve službě Site Recovery

[Azure Site Recovery](site-recovery-overview.md) služby je aktualizovat a vylepšovat průběžně. Abyste mohli Udržujte si přehled, tento článek poskytuje informace o nejnovější vydané verze, nových funkcích a nový obsah. Tato stránka se aktualizuje v pravidelných intervalech.

Pokud máte návrhy na funkce Site Recovery, budeme rádi [bychom znali váš názor](https://feedback.azure.com/forums/256299-site-recovery).

## <a name="q1-2019"></a>Q1 2019 

### <a name="update-rollup-34-february-2019"></a>Kumulativní aktualizace 34 (února 2019)

[Aktualizovat souhrn 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů Site Recovery a poskytovatelů (jak je uvedeno v kumulativní)
**Opravy problému** | Několik oprav chyb a vylepšení (jak je uvedeno v kumulativní)



### <a name="update-rollup-33-february-2019"></a>Kumulativní aktualizace 33 (února 2019)

[Aktualizovat souhrn 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů Site Recovery a poskytovatelů (jak je uvedeno v kumulativní)
**Opravy problému** | Několik oprav chyb a vylepšení (jak je uvedeno v kumulativní)
**Mapování sítě** | Pro zotavení po havárii virtuálního počítače Azure můžete teď můžete použít libovolné dostupné cílové sítě při povolení replikace. 
**Standard SSD** | Nyní můžete nastavit zotavení po havárii pro virtuální počítače Azure pomocí [disků SSD na úrovni Standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Prostory úložiště s přímým přístupem** | Můžete nastavit zotavení po havárii pro aplikace běžící na virtuálním počítači Azure aplikace s použitím [prostory úložiště – přímé](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) pro zajištění vysoké dostupnosti.
**Systém souborů BRTFS** | Pro virtuální počítače VMware, kromě virtuálních počítačů Azure nepodporuje.<br/><br/> Není podporována, pokud: Dílčí svazek systému souborů BTRFS se změní po povolení replikace, systém souborů je rozdělena na několik disků, nebo pokud BTRFS souborový systém podporuje technologii RAID.



### <a name="update-rollup-32-january-2019"></a>Kumulativní aktualizace 32 (leden 2019)

[Aktualizovat souhrn 31](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů Site Recovery a poskytovatelů (jak je uvedeno v kumulativní)
**Opravy problému** | Několik oprav chyb a vylepšení (jak je uvedeno v kumulativní)
**Zotavení po havárii pro Linux** | **Virtuální počítače Azure**: Pracovní stanice RedHat 6 hodin denně, 7; Podpora pro nové verze jádra pro Ubuntu, Debian a SUSE.<br/><br/> **Virtuální počítače VMware a fyzických serverů**: Red Hat Enterprise Linux 7.6; Pracovní stanice RedHat 6 hodin denně, 7; Oracle Linux 6.10/7.6; také koncepty pro nové verze jádra pro Ubuntu, Debian a SUSE.


### <a name="update-rollup-31-january-2019"></a>Kumulativní aktualizace do 31 (leden 2019)

[Aktualizovat souhrn 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů Site Recovery a poskytovatelů (jak je uvedeno v kumulativní)
**Opravy problému** | Několik oprav chyb a vylepšení (jak je uvedeno v kumulativní)
**Zotavení po havárii pro Linux** | **Virtuální počítače Azure**: Oracle Linux 6.8 a 6.9/7.0; Podpora UEK5 jádrech.<br/><br/> **Virtuální počítače VMware a fyzických serverů**: Oracle Linux 6.8 a 6.9/7.0; Podpora UEK5 jádra.
**Systém souborů BRTFS** | Nepodporuje se pro virtuální počítače Azure.
**LVM** | Byla přidána podpora pro svazky LVM a LVM2.<br/><br/> Je podporován adresáři Boot na diskový oddíl a na svazcích LVM.
**Adresáře** | Podpora započítají pro tyto adresáře seet jako samostatných oddílů nebo systémy souborů, které nejsou na stejném disku, systému: / (root), Boot, / USR, /usr/local, /var, / etc.
**Windows Server 2008** | Byla přidána podpora pro dynamické disky.
**Převzetí služeb při selhání virtuálního počítače VMware** | Vylepšená doba převzetí služeb při selhání pro virtuální počítače VMware, kde storvsc a vsbus nejsou spouštěcí ovladače.
**Podpora rozhraní UEFI** | Virtuální počítače Azure nepodporují typ spuštění UEFI. Místní fyzické servery s UEFI teď můžete migrovat do Azure pomocí Site Recovery. Site Recovery migrovat server převedením typ spuštění BIOS před migrací. Site Recovery dříve podporovaly tento převod pro virtuální počítače pouze. Podpora je k dispozici pro fyzické servery s Windows serverem 2012 nebo novější.
**Virtuální počítače Azure v zónách dostupnosti** | Můžete povolit replikaci do jiné oblasti pro virtuální počítače Azure nasazené v zónách dostupnosti. organizační jednotky teď můžete povolit replikaci pro virtuální počítač Azure a nastavit cíle pro převzetí služeb při selhání do jediné instance virtuálních počítačů, virtuálních počítačů ve skupině dostupnosti nebo virtuální počítač v zóně dostupnosti. Nastavení nebude mít vliv na replikaci. [Čtení](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) oznámení.


## <a name="q4-2018"></a>Q4 2018

### <a name="update-rollup-30-october-2018"></a>Kumulativní aktualizace 30 (října 2018)

[Aktualizovat souhrn 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů Site Recovery a poskytovatelů (jak je uvedeno v kumulativní)
**Opravy problému** | Několik oprav chyb a vylepšení (jak je uvedeno v kumulativní)
**Oblasti podpory** | Podporu obnovení přidat Austrálie – střed 1 a Austrálie – střed 2 na webu.
**Podpora pro šifrování disku** | Byla přidána podpora pro zotavení po havárii virtuálních počítačů Azure, které jsou šifrované pomocí Azure Disk Encryption (ADE) pomocí aplikace Azure AD. [Další informace](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Vyloučení disku** | Neinicializovaných disky jsou teď automaticky vyloučené při replikaci virtuálních počítačů Azure.
**Povolená brána firewall úložiště** | Podpora pro přidání [účty úložiště povolená brána firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Replikace virtuálních počítačů Azure s nespravovanými disky pro účty úložiště. povolená brána firewall do jiné oblasti Azure pro zotavení po havárii.<br/><br/> Účty úložiště povolená brána firewall můžete použít jako cílové účty úložiště pro nespravované disky.<br/><br/> Podporováno pouze pomocí prostředí PowerShell.


### <a name="update-rollup-29-october-2018"></a>Kumulativní aktualizace 29 (října 2018)

[Aktualizovat souhrn 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů Site Recovery a poskytovatelů (jak je uvedeno v kumulativní)
**Opravy problému** | Několik oprav chyb a vylepšení (jak je uvedeno v kumulativní)

### <a name="automatic-updates-for-the-mobility-service-extension"></a>Automatické aktualizace pro rozšíření služby Mobility

Do rozšíření služby Mobility Site Recovery přidat možnost pro automatické aktualizace. Rozšíření služby Mobility se nainstaluje na každý virtuální počítač Azure Site Recovery replikovat. Když povolíte replikaci, je vybrat, jestli chce použít ke správě aktualizací pro rozšíření Site Recovery. Aktualizace nevyžadují restartování virtuálního počítače a nemají vliv na replikaci. [Další informace](azure-to-azure-autoupdate.md).

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>Zotavení po havárii pro virtuální počítače pomocí akcelerovanými síťovými službami

Akcelerované síťové služby Povolit virtualizaci (rozhraní SR-IOV) k virtuálnímu počítači, zvýšení výkonu sítě. Když povolíte replikaci pro virtuální počítač Azure, Site Recovery zjistí, zda je povoleno akcelerované síťové služby. Pokud je, po převzetí služeb při selhání Site Recovery automaticky nakonfiguruje akcelerovanými síťovými službami v replice cílového virtuálního počítače Azure pro obě [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) a [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms). [Další informace](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Cenová Kalkulačka funkcí pro zotavení po havárii virtuálního počítače Azure

Zotavení po havárii pro virtuální počítače Azure se neúčtují licenční náklady na virtuální počítač a náklady na síť a úložiště. Azure poskytuje [cenové kalkulačky](https://aka.ms/a2a-cost-estimator) umožňující zjistit tyto náklady. Site Recovery nyní umožňuje [příklad cen odhad](https://aka.ms/a2a-cost-estimator) , ceny Ukázka nasazení podle třívrstvé aplikace pomocí ze šesti virtuálních počítačů s 12 standardní HDD a 6 Premium SSD disky. Ukázka předpokládají, že máte změn dat z 10 GB a den pro standard a 20 GB pro premium. Pro konkrétní nasazení můžete změnit proměnné odhadnout náklady. Můžete zadat počet virtuálních počítačů, počtu a typu spravované disky a očekávané celkové množství dat změnit rychlost očekává mezi virtuální počítače. Kromě toho můžete použít kompresi faktor odhadnout náklady na šířku pásma. [Čtení](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) oznámení.



## <a name="q3-2018"></a>Q3 2018 


### <a name="update-rollup-28-august-2018"></a>Kumulativní aktualizace 28 (. srpna 2018)

[Aktualizovat souhrn 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů Site Recovery a poskytovatelů (jak je uvedeno v kumulativní)
**Zotavení po havárii pro Linux** | **Virtuální počítače Azure**: Přidání podporovaných pro Red Hat Enterprise Linux 6.10; CentOS 6.10.<br/><br/> **Virtuální počítače VMware**: RedHat Enterprise Linux 6.10; CentOS 6.10.<br/><br/> Virtuálních počítačů s Linuxem, že jsou nyní podporovány použít styl oddílů (GPT tabulka) oddílu GUID v režimu kompatibility se starším systémem BIOS. Zobrazit [nejčastější dotazy k diskům virtuálních počítačů Azure IaaS](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/faq-for-disks) Další informace. 
**Podpora cloudu** | Nepodporuje zotavení po havárii pro virtuální počítače Azure v německém cloudu.
**Zotavení po havárii mezi předplatnými** | Podpora pro replikaci virtuálních počítačů Azure v jedné oblasti do jiné oblasti v jiném předplatném, ve stejném tenantovi Azure Active Directory. [Další informace](https://aka.ms/cross-sub-blog).
**Windows Server 2008** | Podpora pro migraci počítače spuštěný Windows Server 2008 R2 nebo 2008 64-bit a 32-bit.<br/><br/> Migrace pouze (replikace a převzetí služeb při selhání). Navrácení služeb po obnovení se nepodporuje.

### <a name="update-rollup-27-july-2018"></a>S kumulativní aktualizací z 27. (července 2018)

[Aktualizovat souhrn 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů Site Recovery a poskytovatelů (jak je uvedeno v kumulativní)
**Zotavení po havárii pro Linux** | **Virtuální počítače Azure**: Red Hat Enterprise Linux 7.5<br/><br/> **Virtuální počítače VMware a fyzických serverů**: Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12




## <a name="next-steps"></a>Další postup

Informujte o našich aktualizacích na [aktualizace Azure](https://azure.microsoft.com/updates/?product=site-recovery) stránky.




 










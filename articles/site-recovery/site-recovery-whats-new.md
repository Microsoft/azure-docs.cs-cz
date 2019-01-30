---
title: Co je nového ve službě Azure Site Recovery | Dokumentace Microsoftu
description: Poskytuje souhrnné informace o nové vlastnosti představené ve službě Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: raynew
ms.openlocfilehash: 61e66a19b625141c69a9473373d3d5d808e18fde
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211113"
---
# <a name="whats-new-in-site-recovery"></a>Co je nového ve službě Site Recovery

[Azure Site Recovery](site-recovery-overview.md) služby je aktualizovat a vylepšovat průběžně. Abyste mohli Udržujte si přehled, tento článek poskytuje informace o nejnovější vydané verze, nových funkcích a nový obsah. Tato stránka se aktualizuje v pravidelných intervalech.

Pokud máte návrhy na funkce Site Recovery, budeme rádi [bychom znali váš názor](https://feedback.azure.com/forums/256299-site-recovery).

## <a name="q1-2019"></a>Q1 2019

### <a name="linux-support"></a>Podpora Linuxu

[Aktualizovat souhrn 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) poskytuje aktualizace pro agenty Site Recovery a poskytovatele. Aktualizace přidává podporu pro Linux následujícím způsobem:

- **Zotavení po havárii virtuálních počítačů Azure**: Pracovní stanice RedHat 6 hodin denně, 7; nové verze jádra pro Ubuntu, Debian a SUSE.
- **Zotavení po havárii virtuálních počítačů VMware nebo fyzických serverů do Azure**: Red Hat Enterprise Linux 7.6; Pracovní stanice RedHat 6 hodin denně, 7; Oracle Linux 6.10/7.6 nové jádra verze Ubuntu, Debian a SUSE.



## <a name="q4-2018"></a>Q4 2018

## <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Cenová Kalkulačka funkcí pro zotavení po havárii virtuálního počítače Azure

Zotavení po havárii pro virtuální počítače Azure se neúčtují licenční náklady na virtuální počítač a náklady na síť a úložiště. Azure poskytuje [cenové kalkulačky](https://aka.ms/a2a-cost-estimator) umožňující zjistit tyto náklady. Site Recovery nyní umožňuje [příklad cen odhad](https://aka.ms/a2a-cost-estimator) , ceny Ukázka nasazení podle třívrstvé aplikace pomocí ze šesti virtuálních počítačů s 12 standardní HDD a 6 Premium SSD disky. Ukázka předpokládají, že máte změn dat z 10 GB a den pro standard a 20 GB pro premium. Pro konkrétní nasazení můžete změnit proměnné odhadnout náklady. Můžete zadat počet virtuálních počítačů, počtu a typu spravované disky a očekávané celkové množství dat změnit rychlost očekává mezi virtuální počítače. Kromě toho můžete použít kompresi faktor odhadnout náklady na šířku pásma. [Čtení](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) oznámení.

### <a name="support-for-azure-vms-in-zones"></a>Podpora pro virtuální počítače Azure v oblastech

Zóny dostupnosti Azure jsou jedinečná fyzická umístění v rámci oblasti Azure. Každá zóna se skládá z jednoho nebo více datových Center vybavených nezávislým napájením, chlazením a sítí. Teď můžete povolit replikaci pro virtuální počítač Azure a nastavit cíl pro převzetí služeb při selhání do jediné instance virtuálních počítačů, virtuálních počítačů ve skupině dostupnosti nebo virtuální počítač v zóně dostupnosti. Nastavení nebude mít vliv na replikaci. [Čtení](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) oznámení.
 
### <a name="disaster-recovery-for-encrypted-vms"></a>Zotavení po havárii pro šifrované virtuální počítače

Site Recovery podporuje virtuální počítače Azure, které jsou šifrované pomocí Azure Disk Encryption (ADE) pomocí aplikace Azure AD. [Další informace](azure-to-azure-how-to-enable-replication-ade-vms.md).

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>Zotavení po havárii pro virtuální počítače pomocí akcelerovanými síťovými službami

Akcelerované síťové služby Povolit virtualizaci (rozhraní SR-IOV) k virtuálnímu počítači, zvýšení výkonu sítě. Když povolíte replikaci pro virtuální počítač Azure, Site Recovery zjistí, zda je povoleno akcelerované síťové služby. Pokud je, po převzetí služeb při selhání Site Recovery automaticky nakonfiguruje akcelerovanými síťovými službami v replice cílového virtuálního počítače Azure pro obě [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) a [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms). [Další informace](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="automatic-updates-for-the-mobility-service-extension"></a>Automatické aktualizace pro rozšíření služby Mobility

Do rozšíření služby Mobility Site Recovery přidat možnost pro automatické aktualizace. Rozšíření služby Mobility se nainstaluje na každý virtuální počítač Azure Site Recovery replikovat. Když povolíte replikaci, je vybrat, jestli chce použít ke správě aktualizací pro rozšíření Site Recovery. Aktualizace nevyžadují restartování virtuálního počítače a nemají vliv na replikaci. [Další informace](azure-to-azure-autoupdate.md).

### <a name="support-for-standard-ssd-disks"></a>Podpora pro standardní disky SSD

Azure zavedené [standardní Solid stav jednotky SSD (Solid-State Drive)](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd) disky a poskytovat řešení nákladově efektivního úložiště pro aplikace, třeba webové servery, které potřebují zajistit konzistentní výkon, ale nemají vysoké IOPS na disku. Jejich kombinovat elementy standardních disků HDD a premium SSD. Site Recovery poskytuje zotavení po havárii pro virtuální počítače Azure s využitím disku SSD na úrovni Standard. Ve výchozím nastavení typ disku je zachována po převzetí služeb při selhání do cílové oblasti.

### <a name="support-for-azure-storage-firewall"></a>Podpora brány firewall služby Azure storage

Zapnutím pravidla brány firewall pro účet můžete zabezpečit účty Azure storage s konkrétní sadou sítí. Konfigurace účtů úložiště pro odepření provozu z interní sítě a Internetu ve výchozím nastavení a potom udělit přístup pro provoz z konkrétní virtuálních sítí. Site Recovery podporuje replikaci pro virtuální počítače s nespravovanými disky pro účty úložiště. povolená brána firewall do sekundární oblasti. V cílové oblasti, pro nespravované disky můžete vybrat účty úložiště s branami firewall povolena. Můžete taky omezit přístup k účtu úložiště mezipaměti tak, že omezíte přístup k síti jenom síť, ve které se nacházejí zdrojové virtuální počítače. Všimněte si, že je potřeba [povolit přístup](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) pro důvěryhodné služby Microsoftu.

## <a name="q3-2018"></a>Q3 2018 

### <a name="linux-support"></a>Podpora Linuxu

#### <a name="update-rollup-28"></a>Kumulativní aktualizace 28

[Aktualizovat souhrn 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) poskytuje aktualizace pro agenty Site Recovery a poskytovatele. Aktualizace přidává podporu pro Linux následujícím způsobem:

- **Zotavení po havárii virtuálních počítačů Azure**: Red Hat Enterprise Linux 6.10; CentOS 6.10; Virtuálních počítačů s Linuxem, že jsou nyní podporovány použít styl oddílů (GPT tabulka) oddílu GUID v režimu kompatibility se starším systémem BIOS.
- **Zotavení po havárii virtuálních počítačů VMware nebo fyzických serverů do Azure**: Red Hat Enterprise Linux 6.10; CentOS 6.10; Virtuálních počítačů s Linuxem, že jsou nyní podporovány použít styl oddílů (GPT tabulka) oddílu GUID v režimu kompatibility se starším systémem BIOS.

#### <a name="update-rollup-27"></a>S kumulativní aktualizací z 27.

[Aktualizovat souhrn 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) poskytuje aktualizace pro agenty Site Recovery a poskytovatele. Aktualizace přidává podporu pro Linux následujícím způsobem:

- **Zotavení po havárii virtuálních počítačů Azure**: Red Hat Enterprise Linux 7.5
- **Zotavení po havárii virtuálních počítačů VMware nebo fyzických serverů do Azure**: SUSE Linux Enterprise Server 12, Red Hat Enterprise Linux 7.5

### <a name="support-for-azure-vms-running-on-windows-server-2016"></a>Podpora pro virtuální počítače Azure běží na Windows serveru 2016

Virtuální počítače Azure běží na Windows serveru 2016 se dají replikovat mezi oblastmi Azure pomocí Azure Site Recovery.

### <a name="support-for-azure-vms-running-storage-spaces-direct"></a>Podpora pro virtuální počítače Azure s prostory úložiště – přímé

[Prostory úložiště – přímé](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) (k dispozici z Windows serveru 2016 a vyšší) seskupuje disky do fondu úložiště a použije kapacity ve fondu vytvořit prostory úložiště. Prostory úložiště je možné na samostatný virtuální počítač, na [hostovaného clusteru virtuálních počítačů Azure](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) pomocí místního úložiště na každém uzlu clusteru nebo sdíleného úložiště v clusteru.

## <a name="next-steps"></a>Další postup

Informujte o našich aktualizacích na [aktualizace Azure](https://azure.microsoft.com/updates/?product=site-recovery) stránky.



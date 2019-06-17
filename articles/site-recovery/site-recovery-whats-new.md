---
title: Co je nového ve službě Azure Site Recovery | Dokumentace Microsoftu
description: Poskytuje souhrnné informace o nové vlastnosti představené ve službě Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 50e1cb95249f0108430e978ae3ffe23b6edc778d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66418382"
---
# <a name="whats-new-in-site-recovery"></a>Co je nového ve službě Site Recovery

[Azure Site Recovery](site-recovery-overview.md) služby je aktualizovat a vylepšovat průběžně. Abyste mohli Udržujte si přehled, tento článek poskytuje informace o nejnovější vydané verze, nových funkcích a nový obsah. Tato stránka se aktualizuje v pravidelných intervalech.

Pokud máte návrhy na funkce Site Recovery, budeme rádi [bychom znali váš názor](https://feedback.azure.com/forums/256299-site-recovery).


## <a name="updates-march-2019"></a>Aktualizace (2019 dne)

### <a name="update-rollup-35"></a>Kumulativní aktualizace 35

[Aktualizovat souhrn 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů Site Recovery a poskytovatelů (jak je uvedeno v kumulativní)
**Vydání opravy a vylepšení** | Několik oprav chyb a vylepšení (jak je uvedeno v kumulativní)

#### <a name="vmwarephysical-server-disaster-recovery"></a>Zotavení po havárii VMware nebo fyzický server
Nové funkce přidané do této aktualizace.

**Funkce** | **Podrobnosti**
--- | ---
**Spravované disky** | Replikace místních virtuálních počítačů VMware a fyzických serverů je nyní přímo do managed disks v Azure. Místní data se odesílají do účtu úložiště mezipaměti v Azure, a body obnovení jsou vytvářeny v spravované disky v cílovém umístění. Tím se zajistí, že není nutné spravovat více cílových účtů služby storage.
**Konfigurační server** | Site Recovery nyní podporuje konfigurační servery s několika síťovými kartami. Předtím, než konfigurační server zaregistrujete v trezoru, je nutné přidat další adaptéry ke konfiguračnímu serveru virtuálního počítače. Pokud přidáte později, budete muset znovu zaregistrovat server v trezoru.


## <a name="updates-february-2019"></a>Aktualizace (února 2019)

### <a name="update-rollup-34"></a>Kumulativní aktualizace 34 

[Aktualizovat souhrn 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů Site Recovery a poskytovatelů (jak je uvedeno v kumulativní).
**Vydání opravy a vylepšení** | Číslo opravy a vylepšení (jak je uvedeno v kumulativní).


### <a name="update-rollup-33"></a>Kumulativní aktualizace 33 

[Aktualizovat souhrn 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů Site Recovery a poskytovatelů (jak je uvedeno v kumulativní).
**Vydání opravy a vylepšení** | Číslo opravy a vylepšení (jak je uvedeno v kumulativní).


#### <a name="azure-vm-disaster-recovery"></a>Azure pro zotavení po havárii virtuálního počítače 
Nové funkce přidané do této aktualizace.

**Funkce** | **Podrobnosti**
--- | ---
**Mapování sítě** | Pro zotavení po havárii virtuálního počítače Azure můžete teď můžete použít libovolné dostupné cílové sítě při povolení replikace. 
**Standard SSD** | Nyní můžete nastavit zotavení po havárii pro virtuální počítače Azure pomocí [disků SSD na úrovni Standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Prostory úložiště s přímým přístupem** | Můžete nastavit zotavení po havárii pro aplikace běžící na virtuálním počítači Azure aplikace s použitím [prostory úložiště – přímé](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) pro zajištění vysoké dostupnosti.  Použití úložiště prostorů s přímým přístupem (S2D) společně s Site Recovery poskytuje komplexní ochranu úloh virtuálních počítačů Azure. S2D umožňuje hostovat cluster hostů v Azure. To je užitečné, když virtuální počítač hostuje kritické aplikace, například vrstvě SAP ASCS, SQL Server nebo horizontální navýšení kapacity souborového serveru.


#### <a name="vmwarephysical-server-disaster-recovery"></a>Zotavení po havárii VMware nebo fyzický server
Nové funkce přidané do této aktualizace.

**Funkce** | **Podrobnosti**
--- | ---
**Systém souborů Linux BRTFS** | Site Recovery nyní podporuje replikaci virtuálních počítačů VMware pomocí systému souborů BRTFS. Replikace není podporována, pokud:<br/><br/>-BTRFS svazku systému souborů dílčí se změní po povolení replikace.<br/><br/>-Systém souborů je rozdělena na několik disků.<br/><br/>-BTRFS systém souborů podporuje technologii RAID.
**Windows Server 2019** | Byla přidána podpora pro počítače se systémem Windows Server 2019.


## <a name="updates-january-2019"></a>Aktualizace (leden 2019)

### <a name="accelerated-networking-azure-vms"></a>Akcelerované síťové služby (virtuální počítače Azure)

Akcelerované síťové služby Povolit virtualizaci (rozhraní SR-IOV) k virtuálnímu počítači, zvýšení výkonu sítě. Když povolíte replikaci pro virtuální počítač Azure, Site Recovery zjistí, zda je povoleno akcelerované síťové služby. Pokud je, po převzetí služeb při selhání Site Recovery automaticky nakonfiguruje akcelerovanými síťovými službami v replice cílového virtuálního počítače Azure pro obě [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) a [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[Další informace](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Kumulativní aktualizace 32 

[Aktualizovat souhrn 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů Site Recovery a poskytovatelů (jak je uvedeno v kumulativní).
**Vydání opravy a vylepšení** | Číslo opravy a vylepšení (jak je uvedeno v kumulativní).

#### <a name="azure-vm-disaster-recovery"></a>Azure pro zotavení po havárii virtuálního počítače

Nové funkce přidané do této aktualizace.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Přidala se podpora pro pracovní stanice RedHat 6 nebo 7 a nové verze jádra pro Ubuntu, Debian a SUSE.
**Prostory úložiště s přímým přístupem** | Site Recovery podporuje virtuální počítače Azure s využitím úložiště prostorů s přímým přístupem (S2D).

#### <a name="vmware-vmsphysical-servers-replication"></a>Replikace virtuálních počítačů VMware nebo fyzických serverů 
**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Přidala se podpora pro 7.6 Red Hat Enterprise Linux, Red Hat pracovní stanice 6 nebo 7, Oracle Linux 6.10/7.6 a nové verze jádra pro Ubuntu, Debian a SUSE.


### <a name="update-rollup-31"></a>Kumulativní aktualizace 31 

[Aktualizovat souhrn 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů Site Recovery a poskytovatelů (jak je uvedeno v kumulativní).
**Vydání opravy a vylepšení** | Číslo opravy a vylepšení (jak je uvedeno v kumulativní).

#### <a name="vmware-vmsphysical-servers-replication"></a>Replikace virtuálních počítačů VMware nebo fyzických serverů 
Nové funkce přidané do této aktualizace.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Byla přidána podpora pro Oracle Linux 6.8 a 6.9/7.0 a pro UEK5 jádra.
**LVM** | Byla přidána podpora pro svazky LVM a LVM2.<br/><br/> Nově se podporuje adresáři Boot na diskový oddíl a na svazcích LVM.
**Adresáře** | Byla přidána podpora pro tyto adresáře nastavený jako samostatných oddílů nebo systémy souborů, které nejsou na stejném disku, systému:<br/><br/> / (root), Boot, / USR, /usr/local, /var, / etc.
**Windows Server 2008** | Byla přidána podpora pro dynamické disky.
**Převzetí služeb při selhání** | Vylepšená doba převzetí služeb při selhání pro virtuální počítače VMware, kde storvsc a vsbus nejsou spouštěcí ovladače.
**Podpora rozhraní UEFI** | Virtuální počítače Azure nepodporují typ spuštění UEFI. Místní fyzické servery s UEFI teď můžete migrovat do Azure pomocí Site Recovery. Site Recovery migrovat server převedením typ spuštění BIOS před migrací. Site Recovery dříve podporovaly tento převod pro virtuální počítače pouze. Podpora je k dispozici pro fyzické servery s Windows serverem 2012 nebo novější.

#### <a name="azure-vm-disaster-recovery"></a>Azure pro zotavení po havárii virtuálního počítače
Nové funkce přidané do této aktualizace.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Nepodporuje se přidal pro Oracle Linux 6.8 a 6.9/7.0; a pro UEK5 jádra.
**Systém souborů Linux BRTFS** | Nepodporuje se pro virtuální počítače Azure.
**Virtuální počítače Azure v zónách dostupnosti** | Můžete povolit replikaci do jiné oblasti pro virtuální počítače Azure nasazené v zónách dostupnosti. Teď můžete povolit replikaci pro virtuální počítač Azure a nastavit cíl pro převzetí služeb při selhání do jediné instance virtuálních počítačů, virtuálních počítačů ve skupině dostupnosti nebo virtuální počítač v zóně dostupnosti. Nastavení nebude mít vliv na replikaci. [Čtení](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) oznámení.
**Povolená brána firewall úložiště (portál/PowerShell)** | Podpora pro přidání [účty úložiště povolená brána firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Replikace virtuálních počítačů Azure s nespravovanými disky pro účty úložiště. povolená brána firewall do jiné oblasti Azure pro zotavení po havárii.<br/><br/> Účty úložiště povolená brána firewall můžete použít jako cílové účty úložiště pro nespravované disky.<br/><br/> Podporované v portálu a pomocí prostředí PowerShell.

## <a name="updates-december-2018"></a>Aktualizace (prosince 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Automatické aktualizace služby Mobility (virtuální počítače Azure)

Do rozšíření služby Mobility Site Recovery přidat možnost pro automatické aktualizace. Rozšíření služby Mobility se nainstaluje na každý virtuální počítač Azure Site Recovery replikovat. Když povolíte replikaci, je vybrat, jestli chce použít ke správě aktualizací pro rozšíření Site Recovery.

Aktualizace nevyžadují restartování virtuálního počítače a nemají vliv na replikaci. [Další informace](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Cenová Kalkulačka funkcí pro zotavení po havárii virtuálního počítače Azure

Po havárii pro zotavení z virtuálních počítačů Azure se neúčtují licenční náklady na virtuální počítač a náklady na síť a úložiště. Azure poskytuje [cenové kalkulačky](https://aka.ms/a2a-cost-estimator) umožňující zjistit tyto náklady. Site Recovery nyní umožňuje [příklad cen odhad](https://aka.ms/a2a-cost-estimator) , ceny Ukázka nasazení podle třívrstvé aplikace pomocí ze šesti virtuálních počítačů s 12 standardní HDD a 6 Premium SSD disky.

- Ukázka předpokládá změn dat z 10 GB a den pro standard a 20 GB pro premium.
- Pro konkrétní nasazení můžete změnit proměnné odhadnout náklady.
- Můžete zadat počet virtuálních počítačů, počtu a typu spravované disky a očekávané celkové množství dat změnit rychlost očekává mezi virtuální počítače.
- Kromě toho můžete použít kompresi faktor odhadnout náklady na šířku pásma.

[Čtení](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) oznámení.


## <a name="updates-october-2018"></a>Aktualizace (října 2018)

### <a name="update-rollup-30"></a>Kumulativní aktualizace 30 

[Aktualizovat souhrn 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů Site Recovery a poskytovatelů (jak je uvedeno v kumulativní).
**Vydání opravy a vylepšení** | Číslo opravy a vylepšení (jak je uvedeno v kumulativní).

#### <a name="azure-vm-disaster-recovery"></a>Azure pro zotavení po havárii virtuálního počítače
Nové funkce přidané do této aktualizace.

**Funkce** | **Podrobnosti**
--- | ---
**Oblasti podpory** | Podporu obnovení přidat Austrálie – střed 1 a Austrálie – střed 2 na webu.
**Podpora pro šifrování disku** | Byla přidána podpora pro zotavení po havárii virtuálních počítačů Azure, které jsou šifrované pomocí Azure Disk Encryption (ADE) pomocí aplikace Azure AD. [Další informace](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Vyloučení disku** | Neinicializované disky jsou teď automaticky vyloučené při replikaci virtuálních počítačů Azure.
**Povolená brána firewall úložiště (PowerShell)** | Podpora pro přidání [účty úložiště povolená brána firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Replikace virtuálních počítačů Azure s nespravovanými disky pro účty úložiště. povolená brána firewall do jiné oblasti Azure pro zotavení po havárii.<br/><br/> Účty úložiště povolená brána firewall můžete použít jako cílové účty úložiště pro nespravované disky.<br/><br/> Podporováno pouze pomocí prostředí PowerShell.


### <a name="update-rollup-29"></a>Kumulativní aktualizace 29 

[Aktualizovat souhrn 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů Site Recovery a poskytovatelů (jak je uvedeno v kumulativní).
**Vydání opravy a vylepšení** | Číslo opravy a vylepšení (jak je uvedeno v kumulativní).


## <a name="updates-august-2018"></a>Aktualizace (ze srpna 2018)

### <a name="update-rollup-28"></a>Kumulativní aktualizace 28 

[Aktualizovat souhrn 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů Site Recovery a poskytovatelů (jak je uvedeno v kumulativní).
**Vydání opravy a vylepšení** | Číslo opravy a vylepšení (jak je uvedeno v kumulativní).

#### <a name="azure-vms-disaster-recovery"></a>Azure pro zotavení po havárii virtuálních počítačů 
Nové funkce přidané do této aktualizace.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Přidání podporovaných pro Red Hat Enterprise Linux 6.10; CentOS 6.10.<br/><br/>
**Podpora cloudu** | Nepodporuje zotavení po havárii pro virtuální počítače Azure v německém cloudu.
**Zotavení po havárii mezi předplatnými** | Podpora pro replikaci virtuálních počítačů Azure v jedné oblasti do jiné oblasti v jiném předplatném, ve stejném tenantovi Azure Active Directory. [Další informace](https://aka.ms/cross-sub-blog).

#### <a name="vmware-vmphysical-server-disaster-recovery"></a>Zotavení po havárii virtuálního počítače VMware nebo fyzický server 
Nové funkce přidané do této aktualizace.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Byla přidána podpora pro Red Hat Enterprise Linux 6.10, CentOS 6.10.<br/><br/> Virtuálních počítačů s Linuxem, že jsou nyní podporovány použít styl oddílů (GPT tabulka) oddílu GUID v režimu kompatibility se starším systémem BIOS. Zkontrolujte [virtuálních počítačů Azure – nejčastější dotazy](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) Další informace. 
**Zotavení po havárii pro virtuální počítače po dokončení migrace** | Podpora pro povolení zotavení po havárii do sekundární oblasti pro VMware v místním prostředí virtuální počítač migrovat na Azure, aniž by bylo nutné odinstalovat Mobility service na virtuálním počítači před povolením replikace.
**Windows Server 2008** | Podpora pro migraci počítače spuštěný Windows Server 2008 R2 nebo 2008 64-bit a 32-bit.<br/><br/> Migrace pouze (replikace a převzetí služeb při selhání). Navrácení služeb po obnovení se nepodporuje.

## <a name="updates-july-2018"></a>Aktualizace (z července 2018)

### <a name="update-rollup-27-july-2018"></a>S kumulativní aktualizací z 27. (července 2018)

[Aktualizovat souhrn 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace agentů Site Recovery a poskytovatelů (jak je uvedeno v kumulativní).
**Vydání opravy a vylepšení** | Číslo opravy a vylepšení (jak je uvedeno v kumulativní).

#### <a name="azure-vms-disaster-recovery"></a>Azure pro zotavení po havárii virtuálních počítačů 

Nové funkce přidané do této aktualizace.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Byla přidána podpora pro Red Hat Enterprise Linux 7.5.

#### <a name="vmware-vmphysical-server-disaster-recovery"></a>Zotavení po havárii virtuálního počítače VMware nebo fyzický server 

Nové funkce přidané do této aktualizace.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Byla přidána podpora pro Red Hat Enterprise Linux 7.5, operačním systémem SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Další postup

Informujte o našich aktualizacích na [aktualizace Azure](https://azure.microsoft.com/updates/?product=site-recovery) stránky.

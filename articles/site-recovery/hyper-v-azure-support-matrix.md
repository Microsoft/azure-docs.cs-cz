---
title: Podpora pro zotavení po havárii virtuálních počítačů Hyper-V do Azure pomocí Azure Site Recovery
description: Shrnuje podporované součásti a požadavky pro zotavení po havárii virtuálního počítače Hyper-V do Azure pomocí Azure Site Recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 7/14/2020
ms.openlocfilehash: 936950ef5dd2c1c7343e30536ab8836fcd198bd0
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580837"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Matice podpory pro zotavení po havárii místních virtuálních počítačů Hyper-V do Azure

Tento článek shrnuje podporované součásti a nastavení pro zotavení po havárii místních virtuálních počítačů Hyper-V do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

>[!NOTE]
> Site Recovery nepřesouvá ani neukládají zákaznická data mimo cílovou oblast, ve které byly pro zdrojové počítače nastavily zotavení po havárii. Zákazníci si můžou vybrat Recovery Services trezor z jiné oblasti, pokud si si je vyberou. Trezor Recovery Services obsahuje metadata, ale žádná skutečná zákaznická data.

## <a name="supported-scenarios"></a>Podporované scénáře

**Scénář** | **Podrobnosti**
--- | ---
Hyper-V s Virtual Machine Manager <br> <br>| V Azure můžete provést zotavení po havárii pro virtuální počítače, které běží na hostitelích Hyper-V, které jsou spravované v System Center Virtual Machine Managerch prostředcích infrastruktury.<br/><br/> Tento scénář můžete nasadit v Azure Portal nebo pomocí prostředí PowerShell.<br/><br/> Pokud jsou hostitelé Hyper-V spravováni Virtual Machine Manager, můžete také provést zotavení po havárii do sekundární místní lokality. Pokud chcete získat další informace o tomto scénáři, přečtěte si [Tento kurz](hyper-v-vmm-disaster-recovery.md).
Hyper-V bez Virtual Machine Manager | V Azure můžete provést zotavení po havárii pro virtuální počítače, které běží na hostitelích Hyper-V, které nejsou spravované pomocí Virtual Machine Manager.<br/><br/> Tento scénář můžete nasadit v Azure Portal nebo pomocí prostředí PowerShell.

## <a name="on-premises-servers"></a>Místní servery

**Server** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Hyper-V (běžící bez Virtual Machine Manager) |  Windows Server 2019, Windows Server 2016, Windows Server 2012 R2 s nejnovějšími aktualizacemi <br/><br/> **Poznámka:** Instalace jádra serveru těchto operačních systémů je taky podporovaná. | Pokud jste už nakonfigurovali Windows Server 2012 R2 s/nebo SCVMM 2012 R2 s Azure Site Recovery a plánujete upgradovat operační systém, postupujte podle pokynů v [dokumentaci.](upgrade-2012R2-to-2016.md)
Hyper-V (běžící s Virtual Machine Manager) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 <br/><br/> **Poznámka:** Instalace jádra serveru těchto operačních systémů je taky podporovaná.  | Pokud se používá Virtual Machine Manager, hostitelé Windows serveru 2019 by měli být spravováni v Virtual Machine Manager 2019. Podobně by hostitelé systému Windows Server 2016 měli být spravováni v Virtual Machine Manager 2016.

> [!NOTE]
> Zajistěte, aby na místním serveru existovala .NET Framework 4.6.2 nebo vyšší.

## <a name="replicated-vms"></a>Replikované virtuální počítače


Následující tabulka shrnuje podporu virtuálních počítačů. Site Recovery podporuje jakékoli úlohy spuštěné v podporovaném operačním systému.

 **Komponenta** | **Podrobnosti**
--- | ---
Konfigurace virtuálního počítače | Virtuální počítače, které se replikují do Azure, musí splňovat [požadavky Azure](#azure-vm-requirements).
Hostovaný operační systém | Libovolný hostovaný operační systém [podporovaný pro Azure](../cloud-services/cloud-services-guestos-update-matrix.md#family-5-releases)...<br/><br/> Windows Server 2016 nano Server se nepodporuje.


## <a name="vmdisk-management"></a>Správa virtuálních počítačů a disků

**Akce** | **Podrobnosti**
--- | ---
Změna velikosti disku na replikovaném virtuálním počítači s technologií Hyper-V | Nepodporováno Zakažte replikaci, proveďte tuto změnu a pak znovu povolte replikaci pro virtuální počítač.
Přidat disk na replikovaný virtuální počítač Hyper-V | Nepodporováno Zakažte replikaci, proveďte tuto změnu a pak znovu povolte replikaci pro virtuální počítač.

## <a name="hyper-v-network-configuration"></a>Konfigurace sítě Hyper-V

**Komponenta** | **Hyper-V s Virtual Machine Manager** | **Hyper-V bez Virtual Machine Manager**
--- | --- | ---
Síť hostitele: seskupování síťových adaptérů | Yes | Yes
Síť hostitele: síť VLAN | Yes | Yes
Síť hostitele: IPv4 | Yes | Yes
Síť hostitele: IPv6 | No | No
Síť virtuálních počítačů hosta: seskupování síťových adaptérů | No | No
Síť virtuálních počítačů hosta: IPv4 | Yes | Yes
Síť virtuálních počítačů hosta: IPv6 | No | Yes
Host VM Network: statická IP adresa (Windows) | Yes | Yes
Síť virtuálních počítačů hosta: statická IP adresa (Linux) | No | No
Host VM Network: více síťových karet | Yes | Yes
Proxy server https | No | No
Přístup k Site Recovery službě přes soukromé odkazy | Ano. [Další informace](hybrid-how-to-enable-replication-private-endpoints.md). | Ano. [Další informace](hybrid-how-to-enable-replication-private-endpoints.md).




## <a name="azure-vm-network-configuration-after-failover"></a>Konfigurace sítě virtuálních počítačů Azure (po převzetí služeb při selhání)

**Komponenta** | **Hyper-V s Virtual Machine Manager** | **Hyper-V bez Virtual Machine Manager**
--- | --- | ---
Azure ExpressRoute | Yes | Yes
INTERNÍHO nástroje | Yes | Yes
ELB | Yes | Yes
Azure Traffic Manager | Yes | Yes
Více síťových karet | Yes | Yes
Vyhrazená IP adresa | Yes | Yes
IPv4 | Yes | Yes
Zachovat zdrojovou IP adresu | Yes | Yes
Koncové body služby Azure Virtual Network<br/> (bez Azure Storage firewallů) | Yes | Yes
Akcelerované síťové služby | No | No


## <a name="hyper-v-host-storage"></a>Úložiště hostitele technologie Hyper-V

**Storage** | **Hyper-V s Virtual Machine Manager** | **Hyper-V bez Virtual Machine Manager**
--- | --- | --- 
NFS | Není k dispozici | Není k dispozici
SMB 3.0 | Yes | Yes
SÍŤ SAN (ISCSI) | Yes | Yes
Multipath (multi-Path). Testováno pomocí:<br></br> Microsoft DSM, EMC PowerPath 5,7 SP4, EMC PowerPath DSM pro CLARiiON | Yes | Yes

## <a name="hyper-v-vm-guest-storage"></a>Úložiště hostů virtuálních počítačů Hyper-V

**Storage** | **Hyper-V s Virtual Machine Manager** | **Hyper-V bez Virtual Machine Manager**
--- | --- | ---
FORMÁTU | Není k dispozici | Není k dispozici
VHD/VHDX | Yes | Yes
Virtuální počítač generace 2 | Yes | Yes
ROZHRANÍ EFI/UEFI<br></br>Migrovaný virtuální počítač v Azure se automaticky převede na spouštěcí virtuální počítač se systémem BIOS. Na virtuálním počítači by měl běžet jenom Windows Server 2012 a novější. Disk s operačním systémem by měl mít až pět oddílů nebo méně a velikost disku s operačním systémem by měla být menší než 300 GB.| Yes | Yes
Disk sdíleného clusteru | No | No
Zašifrovaný disk | No | No
NFS | Není k dispozici | Není k dispozici
SMB 3.0 | No | No
RDM | Není k dispozici | Není k dispozici
Disk >1 TB | Ano, až 4 095 GB | Ano, až 4 095 GB
Disk: 4K logický a fyzický sektor | Nepodporováno: Obecná 1/fin 2 | Nepodporováno: Obecná 1/fin 2
Disk: 4K fyzický sektor a logický sektor 512-byte | Yes |  Yes
Správa logických svazků (LVM). LVM se podporuje jenom na datových discích. Azure poskytuje jenom jeden disk s operačním systémem. | Yes | Yes
Svazek se zakládaným diskem >1 TB | Yes | Yes
Prostory úložiště | No | No
Hot Add/Remove disk | No | No
Vyloučení disku | Yes | Yes
Multipath (multi-Path) | Yes | Yes

## <a name="azure-storage"></a>Azure Storage

**Komponenta** | **Hyper-V s Virtual Machine Manager** | **Hyper-V bez Virtual Machine Manager**
--- | --- | ---
(Locally redundant storage) Místně redundantní úložiště | Yes | Yes
Geograficky redundantní úložiště | Yes | Yes
Geograficky redundantní úložiště s přístupem pro čtení | Yes | Yes
Zónově redundantní úložiště | No | No
Studené úložiště | No | No
Horké úložiště| No | No
Objekty blob bloku | No | No
Šifrování v klidovém prostředí (SSE)| Yes | Yes
Šifrování v klidovém umístění (CMK) <br></br> (Jenom pro převzetí služeb při selhání pro Managed Disks)| Ano (přes PowerShell AZ 3.3.0 Module a vyšší) | Ano (přes PowerShell AZ 3.3.0 Module a vyšší)
Dvojité šifrování v klidovém umístění <br></br> (Jenom pro převzetí služeb při selhání pro Managed Disks) <br></br> Další informace o podporovaných oblastech pro [Windows](../virtual-machines/disk-encryption.md) a [Linux](../virtual-machines/disk-encryption.md) | Ano (přes PowerShell AZ 3.3.0 Module a vyšší) | Ano (přes PowerShell AZ 3.3.0 Module a vyšší)
Premium Storage | Yes | Yes
Storage úrovně Standard | Yes | Yes
Služba import/export | No | No
Účty Azure Storage s povolenou bránou firewall | Ano. Pro cílové úložiště a mezipaměť. | Ano. Pro cílové úložiště a mezipaměť.
Úprava účtu úložiště | No. Cílový Azure Storage účet se po povolení replikace nedá změnit. Chcete-li upravit, zakázat a znovu povolit zotavení po havárii. | No
Možnost zabezpečeného přenosu | Yes | Yes


## <a name="azure-compute-features"></a>Funkce Azure COMPUTE

**Funkce** | **Hyper-V s Virtual Machine Manager** | **Hyper-V bez Virtual Machine Manager**
--- | --- | ---
Skupiny dostupnosti | Yes | Yes
ZDROJ | Yes | Yes  
Spravované disky | Ano, pro převzetí služeb při selhání.<br/><br/> Navrácení služeb po obnovení ze spravovaných disků se nepodporuje. | Ano, pro převzetí služeb při selhání.<br/><br/> Navrácení služeb po obnovení ze spravovaných disků se nepodporuje.

## <a name="azure-vm-requirements"></a>Požadavky na virtuální počítač Azure

Místní virtuální počítače, které se replikují do Azure, musí splňovat požadavky na virtuální počítače Azure shrnuté v této tabulce.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Hostovaný operační systém | Site Recovery podporuje všechny operační systémy, které [Azure podporuje](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794868(v=ws.10)).  | Nepodporovaná Chyba kontroly požadovaných součástí
Architektura hostovaného operačního systému | 32 – bit (Windows Server 2008)/64-bit | Nepodporovaná Chyba kontroly požadovaných součástí
Velikost disku s operačním systémem | Až 2 048 GB pro virtuální počítače 1. generace.<br/><br/> Až 300 GB pro virtuální počítače 2. generace.  | Nepodporovaná Chyba kontroly požadovaných součástí
Počet disků s operačním systémem | 1 | Nepodporovaná Chyba kontroly požadovaných součástí
Počet datových disků | 16 nebo méně  | Nepodporovaná Chyba kontroly požadovaných součástí
Velikost datového virtuálního pevného disku | Až 4 095 GB | Nepodporovaná Chyba kontroly požadovaných součástí
Síťové adaptéry | Podporuje se více adaptérů |
Sdílený virtuální pevný disk | Nepodporováno | Nepodporovaná Chyba kontroly požadovaných součástí
Disk FC | Nepodporováno | Nepodporovaná Chyba kontroly požadovaných součástí
Formát pevného disku | VHD <br/><br/> DISKU | Při převzetí služeb při selhání do Azure Site Recovery automaticky převede VHDX na VHD. Po navrácení služeb po obnovení do místního nasazení budou virtuální počítače nadále používat formát VHDX.
BitLocker | Nepodporováno | Aby bylo možné povolit replikaci virtuálního počítače, musí být nástroj BitLocker zakázán.
název virtuálního počítače | 1 až 63 znaků. Pouze písmena, číslice a pomlčky. Název virtuálního počítače musí začínat a končit písmenem nebo číslicí. | Aktualizujte hodnotu ve vlastnostech virtuálního počítače v Site Recovery.
Typ virtuálního počítače | 1. generace<br/><br/> Generace 2 – Windows | Virtuální počítače 2. generace s typem disku operačního systému Basic (obsahující jeden nebo dva datové svazky formátované jako VHDX) a jsou podporované méně než 300 GB místa na disku.<br></br>Virtuální počítače se systémem Linux generace 2 nejsou podporovány. [Další informace](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Akce trezoru Recovery Services

**Akce** |  **Hyper-V s nástrojem VMM** | **Hyper-V bez nástroje VMM**
--- | --- | ---
Přesunout trezor mezi skupinami prostředků<br/><br/> V rámci předplatných a mezi nimi | No | No
Přesunutí úložiště, sítě, virtuálních počítačů Azure napříč skupinami prostředků<br/><br/> V rámci předplatných a mezi nimi | No | No

> [!NOTE]
> Když se replikuje Hyper-VMs z místního prostředí do Azure, můžete replikovat jenom na jednoho tenanta AD z jednoho konkrétního prostředí – z lokality Hyper-V nebo z Hyper-V s VMM podle potřeby.


## <a name="provider-and-agent"></a>Zprostředkovatel a agent

Abyste se ujistili, že vaše nasazení je kompatibilní s nastavením v tomto článku, ujistěte se, že používáte nejnovější verzi zprostředkovatele a agenta.

**Název** | **Popis** | **Podrobnosti**
--- | --- | --- 
Poskytovatel Azure Site Recovery | Koordinuje komunikaci mezi místními servery a Azure. <br/><br/> Hyper-V s Virtual Machine Managerem: nainstalováno na serverech Virtual Machine Manager<br/><br/> Hyper-V bez Virtual Machine Manager: instaluje se na hostitele Hyper-V.| Nejnovější verze: 5.1.2700.1 (k dispozici z Azure Portal)<br/><br/> [Nejnovější funkce a opravy](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Agent Microsoft Azure Recovery Services | Koordinuje replikaci mezi virtuálními počítači Hyper-V a Azure.<br/><br/> Nainstalováno na místních serverech Hyper-V (s Virtual Machine Manager nebo bez něj) | Nejnovější agent dostupný z portálu






## <a name="next-steps"></a>Další kroky
Přečtěte si, jak [připravit Azure](tutorial-prepare-azure.md) na zotavení po havárii místních virtuálních počítačů Hyper-V.
---
title: Podpora pro zotavení po havárii virtuálních počítačů Hyper-V do Azure pomocí Azure Site Recovery
description: Shrnuje podporované součásti a požadavky pro zotavení po havárii virtuálního počítače Hyper-V do Azure pomocí Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 1/27/2020
ms.author: raynew
ms.openlocfilehash: da2cc3dade843b1ea207eb4ec5bf33a41a289d7e
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85851480"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Matice podpory pro zotavení po havárii místních virtuálních počítačů Hyper-V do Azure


Tento článek shrnuje podporované součásti a nastavení pro zotavení po havárii místních virtuálních počítačů Hyper-V do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).



## <a name="supported-scenarios"></a>Podporované scénáře

**Scénář** | **Podrobnosti**
--- | ---
Hyper-V s Virtual Machine Manager <br> <br>| V Azure můžete provést zotavení po havárii pro virtuální počítače, které běží na hostitelích Hyper-V, které jsou spravované v System Center Virtual Machine Managerch prostředcích infrastruktury.<br/><br/> Tento scénář můžete nasadit v Azure Portal nebo pomocí prostředí PowerShell.<br/><br/> Pokud jsou hostitelé Hyper-V spravováni Virtual Machine Manager, můžete také provést zotavení po havárii do sekundární místní lokality. Pokud chcete získat další informace o tomto scénáři, přečtěte si [Tento kurz](hyper-v-vmm-disaster-recovery.md).
Hyper-V bez Virtual Machine Manager | V Azure můžete provést zotavení po havárii pro virtuální počítače, které běží na hostitelích Hyper-V, které nejsou spravované pomocí Virtual Machine Manager.<br/><br/> Tento scénář můžete nasadit v Azure Portal nebo pomocí prostředí PowerShell.

## <a name="on-premises-servers"></a>Místní servery

**Server** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Hyper-V (běžící bez Virtual Machine Manager) |  Windows Server 2019, Windows Server 2016, Windows Server 2012 R2 s nejnovějšími aktualizacemi (včetně instalace jádra serveru těchto operačních systémů s výjimkou Windows serveru 2019) | Pokud jste už nakonfigurovali Windows Server 2012 R2 s/nebo SCVMM 2012 R2 s Azure Site Recovery a plánujete upgradovat operační systém, postupujte podle pokynů v [dokumentaci.](upgrade-2012R2-to-2016.md)
Hyper-V (běžící s Virtual Machine Manager) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 (včetně instalace jádra serveru těchto operačních systémů s výjimkou Virtual Machine Manager 2019) | Pokud se používá Virtual Machine Manager, hostitelé Windows serveru 2019 by měli být spravováni v Virtual Machine Manager 2019. Podobně by hostitelé systému Windows Server 2016 měli být spravováni v Virtual Machine Manager 2016.

> [!NOTE]
>
> - Zajistěte, aby na místním serveru existovala .NET Framework 4.6.2 nebo vyšší.
> - Převzetí služeb při selhání a navrácení služeb po obnovení do alternativního umístění nebo původního umístění, které běží s Virtual Machine Manager nebo bez něj, se nepodporuje u verze jádra serveru Windows Server 2019.

## <a name="replicated-vms"></a>Replikované virtuální počítače


Následující tabulka shrnuje podporu virtuálních počítačů. Site Recovery podporuje jakékoli úlohy spuštěné v podporovaném operačním systému.

 **Komponenta** | **Podrobnosti**
--- | ---
Konfigurace virtuálního počítače | Virtuální počítače, které se replikují do Azure, musí splňovat [požadavky Azure](#azure-vm-requirements).
Hostovaný operační systém | Libovolný hostovaný operační systém [podporovaný pro Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases)...<br/><br/> Windows Server 2016 nano Server se nepodporuje.


## <a name="vmdisk-management"></a>Správa virtuálních počítačů a disků

**Akce** | **Podrobnosti**
--- | ---
Změna velikosti disku na replikovaném virtuálním počítači s technologií Hyper-V | Není podporováno. Zakažte replikaci, proveďte tuto změnu a pak znovu povolte replikaci pro virtuální počítač.
Přidat disk na replikovaný virtuální počítač Hyper-V | Není podporováno. Zakažte replikaci, proveďte tuto změnu a pak znovu povolte replikaci pro virtuální počítač.

## <a name="hyper-v-network-configuration"></a>Konfigurace sítě Hyper-V

**Komponenta** | **Hyper-V s Virtual Machine Manager** | **Hyper-V bez Virtual Machine Manager**
--- | --- | ---
Síť hostitele: seskupování síťových adaptérů | Ano | Ano
Síť hostitele: síť VLAN | Ano | Ano
Síť hostitele: IPv4 | Ano | Ano
Síť hostitele: IPv6 | No | No
Síť virtuálních počítačů hosta: seskupování síťových adaptérů | No | No
Síť virtuálních počítačů hosta: IPv4 | Ano | Ano
Síť virtuálních počítačů hosta: IPv6 | No | Yes
Host VM Network: statická IP adresa (Windows) | Ano | Ano
Síť virtuálních počítačů hosta: statická IP adresa (Linux) | No | No
Host VM Network: více síťových karet | Ano | Ano
Proxy server https | No | No



## <a name="azure-vm-network-configuration-after-failover"></a>Konfigurace sítě virtuálních počítačů Azure (po převzetí služeb při selhání)

**Komponenta** | **Hyper-V s Virtual Machine Manager** | **Hyper-V bez Virtual Machine Manager**
--- | --- | ---
Azure ExpressRoute | Ano | Ano
INTERNÍHO nástroje | Ano | Ano
ELB | Ano | Ano
Azure Traffic Manager | Ano | Ano
Více síťových karet | Ano | Ano
Vyhrazená IP adresa | Ano | Ano
IPv4 | Ano | Ano
Zachovat zdrojovou IP adresu | Ano | Ano
Koncové body služby Azure Virtual Network<br/> (bez Azure Storage firewallů) | Ano | Ano
Akcelerované síťové služby | No | No


## <a name="hyper-v-host-storage"></a>Úložiště hostitele technologie Hyper-V

**Storage** | **Hyper-V s Virtual Machine Manager** | **Hyper-V bez Virtual Machine Manager**
--- | --- | --- 
NFS | NA | NA
SMB 3.0 | Ano | Ano
SÍŤ SAN (ISCSI) | Ano | Ano
Multipath (multi-Path). Testováno pomocí:<br></br> Microsoft DSM, EMC PowerPath 5,7 SP4, EMC PowerPath DSM pro CLARiiON | Ano | Ano

## <a name="hyper-v-vm-guest-storage"></a>Úložiště hostů virtuálních počítačů Hyper-V

**Storage** | **Hyper-V s Virtual Machine Manager** | **Hyper-V bez Virtual Machine Manager**
--- | --- | ---
FORMÁTU | NA | NA
VHD/VHDX | Ano | Ano
Virtuální počítač generace 2 | Ano | Ano
ROZHRANÍ EFI/UEFI<br></br>Migrovaný virtuální počítač v Azure se automaticky převede na spouštěcí virtuální počítač se systémem BIOS. Na virtuálním počítači by měl běžet jenom Windows Server 2012 a novější. Disk s operačním systémem by měl mít až pět oddílů nebo méně a velikost disku s operačním systémem by měla být menší než 300 GB.| Ano | Ano
Disk sdíleného clusteru | No | No
Zašifrovaný disk | No | No
NFS | NA | NA
SMB 3.0 | No | No
RDM | NA | NA
Disk >1 TB | Ano, až 4 095 GB | Ano, až 4 095 GB
Disk: 4K logický a fyzický sektor | Nepodporováno: Obecná 1/fin 2 | Nepodporováno: Obecná 1/fin 2
Disk: 4K fyzický sektor a logický sektor 512-byte | Ano |  Ano
Správa logických svazků (LVM). LVM se podporuje jenom na datových discích. Azure poskytuje jenom jeden disk s operačním systémem. | Ano | Ano
Svazek se zakládaným diskem >1 TB | Ano | Ano
Prostory úložiště | No | No
Hot Add/Remove disk | No | No
Vyloučení disku | Ano | Ano
Multipath (multi-Path) | Ano | Ano

## <a name="azure-storage"></a>Azure Storage

**Komponenta** | **Hyper-V s Virtual Machine Manager** | **Hyper-V bez Virtual Machine Manager**
--- | --- | ---
(Locally redundant storage) Místně redundantní úložiště | Ano | Ano
Geograficky redundantní úložiště | Ano | Ano
Geograficky redundantní úložiště s přístupem pro čtení | Ano | Ano
Studené úložiště | No | No
Horké úložiště| No | No
Objekty blob bloku | No | No
Šifrování v klidovém prostředí (SSE)| Ano | Ano
Šifrování v klidovém umístění (CMK) <br></br> (Jenom pro převzetí služeb při selhání pro Managed Disks)| Ano (přes PowerShell AZ 3.3.0 Module a vyšší) | Ano (přes PowerShell AZ 3.3.0 Module a vyšší)
Premium Storage | Ano | Ano
Storage úrovně Standard | Ano | Ano
Služba import/export | No | No
Účty Azure Storage s povolenou bránou firewall | Ano. Pro cílové úložiště a mezipaměť. | Ano. Pro cílové úložiště a mezipaměť.
Úprava účtu úložiště | Ne. Cílový Azure Storage účet se po povolení replikace nedá změnit. Chcete-li upravit, zakázat a znovu povolit zotavení po havárii. | No


## <a name="azure-compute-features"></a>Funkce Azure COMPUTE

**Funkce** | **Hyper-V s Virtual Machine Manager** | **Hyper-V bez Virtual Machine Manager**
--- | --- | ---
Skupiny dostupnosti | Ano | Ano
ZDROJ | Ano | Ano  
Spravované disky | Ano, pro převzetí služeb při selhání.<br/><br/> Navrácení služeb po obnovení ze spravovaných disků se nepodporuje. | Ano, pro převzetí služeb při selhání.<br/><br/> Navrácení služeb po obnovení ze spravovaných disků se nepodporuje.

## <a name="azure-vm-requirements"></a>Požadavky na virtuální počítač Azure

Místní virtuální počítače, které se replikují do Azure, musí splňovat požadavky na virtuální počítače Azure shrnuté v této tabulce.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Hostovaný operační systém | Site Recovery podporuje všechny operační systémy, které [Azure podporuje](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Nepodporovaná Chyba kontroly požadovaných součástí
Architektura hostovaného operačního systému | 32 – bit (Windows Server 2008)/64-bit | Nepodporovaná Chyba kontroly požadovaných součástí
Velikost disku s operačním systémem | Až 2 048 GB pro virtuální počítače 1. generace.<br/><br/> Až 300 GB pro virtuální počítače 2. generace.  | Nepodporovaná Chyba kontroly požadovaných součástí
Počet disků s operačním systémem | 1 | Nepodporovaná Chyba kontroly požadovaných součástí
Počet datových disků | 16 nebo méně  | Nepodporovaná Chyba kontroly požadovaných součástí
Velikost datového virtuálního pevného disku | Až 4 095 GB | Nepodporovaná Chyba kontroly požadovaných součástí
Síťové adaptéry | Podporuje se více adaptérů |
Sdílený virtuální pevný disk | Nepodporuje se | Nepodporovaná Chyba kontroly požadovaných součástí
Disk FC | Nepodporuje se | Nepodporovaná Chyba kontroly požadovaných součástí
Formát pevného disku | VHD <br/><br/> DISKU | Při převzetí služeb při selhání do Azure Site Recovery automaticky převede VHDX na VHD. Po navrácení služeb po obnovení do místního nasazení budou virtuální počítače nadále používat formát VHDX.
BitLocker | Nepodporuje se | Aby bylo možné povolit replikaci virtuálního počítače, musí být nástroj BitLocker zakázán.
název virtuálního počítače | 1 až 63 znaků. Pouze písmena, číslice a pomlčky. Název virtuálního počítače musí začínat a končit písmenem nebo číslicí. | Aktualizujte hodnotu ve vlastnostech virtuálního počítače v Site Recovery.
Typ virtuálního počítače | 1. generace<br/><br/> Generace 2 – Windows | Virtuální počítače 2. generace s typem disku operačního systému Basic (obsahující jeden nebo dva datové svazky formátované jako VHDX) a jsou podporované méně než 300 GB místa na disku.<br></br>Virtuální počítače se systémem Linux generace 2 nejsou podporovány. [Další informace](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Akce trezoru Recovery Services

**Akce** |  **Hyper-V s nástrojem VMM** | **Hyper-V bez nástroje VMM**
--- | --- | ---
Přesunout trezor mezi skupinami prostředků<br/><br/> V rámci předplatných a mezi nimi | No | No
Přesunutí úložiště, sítě, virtuálních počítačů Azure napříč skupinami prostředků<br/><br/> V rámci předplatných a mezi nimi | No | No

> [!NOTE]
> Při replikaci virtuálních počítačů Hyper-v z místního prostředí do Azure můžete replikovat jenom na jednoho tenanta AD z jednoho konkrétního prostředí – z lokality Hyper-V nebo z Hyper-V s VMM podle potřeby.


## <a name="provider-and-agent"></a>Zprostředkovatel a agent

Abyste se ujistili, že vaše nasazení je kompatibilní s nastavením v tomto článku, ujistěte se, že používáte nejnovější verzi zprostředkovatele a agenta.

**Název** | **Popis** | **Podrobnosti**
--- | --- | --- 
Poskytovatel Azure Site Recovery | Koordinuje komunikaci mezi místními servery a Azure. <br/><br/> Hyper-V s Virtual Machine Managerem: nainstalováno na serverech Virtual Machine Manager<br/><br/> Hyper-V bez Virtual Machine Manager: instaluje se na hostitele Hyper-V.| Nejnovější verze: 5.1.2700.1 (k dispozici z Azure Portal)<br/><br/> [Nejnovější funkce a opravy](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Agent Microsoft Azure Recovery Services | Koordinuje replikaci mezi virtuálními počítači Hyper-V a Azure.<br/><br/> Nainstalováno na místních serverech Hyper-V (s Virtual Machine Manager nebo bez něj) | Nejnovější agent dostupný z portálu






## <a name="next-steps"></a>Další kroky
Přečtěte si, jak [připravit Azure](tutorial-prepare-azure.md) na zotavení po havárii místních virtuálních počítačů Hyper-V.

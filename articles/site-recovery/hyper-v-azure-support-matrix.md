---
title: Matici podpory pro replikaci technologie Hyper-V Azure | Microsoft Docs
description: "Poskytuje souhrn podporovaných součásti a požadavky pro replikaci technologie Hyper-V do Azure s Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/06/2018
ms.author: raynew
ms.openlocfilehash: 81983b9287a6b8073724f0cd973929f4b4677d4a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="support-matrix-for-hyper-v-replication-to-azure"></a>Podporu pro replikaci technologie Hyper-V do Azure.


Tento článek obsahuje souhrn podporovaných součásti a nastavení pro zotavení po havárii místní virtuální počítače Hyper-V do Azure, pomocí [Azure Site Recovery](site-recovery-overview.md) služby.


## <a name="supported-scenarios"></a>Podporované scénáře

**Scénář** | **Podrobnosti**
--- | --- 
**Technologie Hyper-V s nástrojem VMM** | Je možné provést zotavení po havárii do Azure pro virtuální počítače spuštěné v hostitelích Hyper-V spravované v rámci prostředí prostředků infrastruktury System Center Virtual Machine Manager (VMM)<br/><br/> Můžete nasadit tento scénář v portálu Azure nebo pomocí prostředí PowerShell.<br/><br/> Pokud hostitele Hyper-V jsou spravovány nástrojem VMM, můžete také provést zotavení po havárii na sekundární místní lokalitu. Čtení [v tomto článku](tutorial-vmm-to-vmm.md) Další informace o tomto scénáři.
**Technologie Hyper-V bez VMM** | Pro virtuální počítače spuštěné v hostitelích technologie Hyper-V, které nejsou spravované přes VMM můžete provést zotavení po havárii do Azure.<br/><br/> Můžete nasadit tento scénář v portálu Azure nebo pomocí prostředí Powershell. 


## <a name="on-premises-servers"></a>Místní servery

**Server** | **Požadavky** | **Podrobnosti**
--- | --- | ---
**Technologie Hyper-V (spouštění bez VMM)** | Windows Server 2016, Windows Server 2012 R2 s nejnovějšími aktualizacemi. | Při konfiguraci lokality Hyper-V v Site Recovery, kombinování hostitele se systémem Windows Server 2016 a 2012 R2 se nepodporuje.<br/><br/> Pro virtuální počítače na hostitele se systémem Windows Server 2016 není podporováno obnovení do alternativního umístění.
**Technologie Hyper-V (s VMM)** | VMM 2016, VMM 2012 R2 | Pokud se používá VMM, by měl spravovat Windows Server 2016 hostitele VMM 2016.<br/><br/> Cloudy VMM, které zkombinuje hostitelů Hyper-V se spuštěným operačním systémem Windows Server 2016 a 2012 R2 není aktuálně podporován.<br/><br/> Prostředí, které zahrnují upgrade existujícího serveru VMM 2012 R2 na 2016 nejsou podporovány.


## <a name="replicated-vms"></a>Replikované virtuální počítače


Následující tabulka shrnuje podporu virtuálních počítačů. Site Recovery podporuje jakékoli úlohy spuštěné na podporovaném operačním systému. 

 **Komponenta** | **Podrobnosti**
--- | ---
Konfigurace virtuálního počítače | Virtuální počítače, které se replikují do Azure, musí splňovat [požadavky pro Azure](#failed-over-azure-vm-requirements).
Hostovaný operační systém | Všechny hostovaný operační systém [nepodporuje v Azure](https://technet.microsoft.com/library/cc794868.aspx).<br/><br/> Windows Server 2016 Nano Server není podporována.




## <a name="hyper-v-network-configuration"></a>Konfigurace sítě technologie Hyper-V

**Komponenta** | **Technologie Hyper-V s nástrojem VMM** | **Technologie Hyper-V bez VMM**
--- | --- | ---
Seskupování síťových adaptérů hostitele síťových Adaptérů sítě: | Ano
Hostitele síť: VLAN | Ano
Hostitele sítě: IPv4 | Ano
Hostitele sítě: IPv6 | Ne
Virtuální počítač hostovaný sítě: seskupování síťových adaptérů | Ne
Virtuální počítač hostovaný sítě: IPv4 | Ano
Virtuální počítač hostovaný sítě: IPv6 | Ne
Virtuální počítač hostovaný sítě: statické IP (Windows) | Ano
Virtuální počítač hostovaný sítě: statické IP (Linux) | Ne
Virtuální počítač hostovaný sítě: více – síťový adaptér | Ano



## <a name="azure-vm-network-configuration-after-failover"></a>Azure konfigurace sítě virtuálních počítačů (po převzetí služeb při selhání)

**Komponenta** | **Technologie Hyper-V s nástrojem VMM** | **Technologie Hyper-V bez VMM**
--- | --- | ---
ExpressRoute | Ano | Ano
Interní nástroj pro vyrovnávání zatížení | Ano | Ano
ELB | Ano | Ano
Traffic Manager | Ano | Ano
Multi-NIC | Ano | Ano
Rezervovaná IP adresa | Ano | Ano
IPv4 | Ano | Ano
Zachovat zdrojové IP adresy | Ano | Ano
Koncové body služby virtuální sítě<br/><br/> (Brány firewall úložiště azure a virtuální sítě) | Ne | Ne


## <a name="hyper-v-host-storage"></a>Úložiště pro hostitele Hyper-V

**Úložiště** | **Technologie Hyper-V s nástrojem VMM** | Technologie Hyper-V bez VMM
--- | --- | --- | ---
NFS | Není k dispozici | Není k dispozici
SMB 3.0 | Ano | Ano
SAN (ISCSI) | Ano | Ano
S více cestami (MPIO). Test se:<br></br> Microsoft DSM EMC PowerPath 5.7 SP4<br/><br/> EMC PowerPath DSM pro CLARiiON | Ano | Ano

## <a name="hyper-v-vm-guest-storage"></a>Úložiště hosta virtuálního počítače technologie Hyper-V

**Úložiště** | **Technologie Hyper-V s nástrojem VMM** | Technologie Hyper-V bez VMM
--- | --- | ---
VMDK | Není k dispozici | Není k dispozici
VHD/VHDX | Ano | Ano
2. generace virtuálních počítačů | Ano | Ano
EFI/UEFI| Ano | Ano
Sdílený disk clusteru | Ne | Ne
Šifrované disku | Ne | Ne
NFS | Není k dispozici | Není k dispozici
SMB 3.0 | Ne | Ne
RDM | Není k dispozici | Není k dispozici
Disk > 1 TB | Ano, až 4095 GB | Ano, až 4095 GB
Disku: logický a fyzický sektor 4 kB | Není podporováno: FIN 1 nebo generace 2 | Není podporováno: FIN 1 nebo generace 2
Disku: 4K logické a fyzického sektoru 512 bajtů | Ano |  Ano
Svazek s prokládané disku > 1 TB<br/><br/> Správa logických LVM svazků | Ano | Ano
Prostory úložiště | Ano | Ano
Přidat nebo odebrat aktivní disku | Ne | Ne
Vyloučení disku | Ano | Ano
S více cestami (MPIO) | Ano | Ano

## <a name="azure-storage"></a>Úložiště Azure

**Komponenta** | **Technologie Hyper-V s nástrojem VMM** | **Hyper-V bez VMM)**
--- | --- | ---
LRS | Ano | Ano
GRS | Ano | Ano
RA-GRS | Ano | Ano
Studeného úložiště | Ne | Ne
Horkého úložiště| Ne | Ne
Objekty blob bloku | Ne | Ne
Šifrování v rest(SSE)| Ano | Ano
Storage úrovně Premium | Ano | Ano
Import a export služby | Ne | Ne
Virtuální síť koncové body služby (brány firewall úložiště Azure a virtuální sítě), na cíl, aby účet úložiště mezipaměti používané pro replikaci dat | Ne | Ne


## <a name="azure-compute-features"></a>Funkce výpočtů Azure

**Funkce** | **Technologie Hyper-V s nástrojem VMM** | **Technologie Hyper-V bez VMM**
--- | --- | ---
Skupiny dostupnosti | Ano | Ano
ROZBOČOVAČE | Ano | Ano  
Managed Disks | Ano, pro převzetí služeb při selhání<br/><br/> Navrácení služeb po obnovení spravovaných disků není podporován. | Ano, pro převzetí služeb při selhání<br/><br/> Navrácení služeb po obnovení spravovaných disků není podporován.

## <a name="azure-vm-requirements"></a>Požadavky virtuálního počítače Azure

Místní virtuální počítače, které se replikují do Azure, musí splňovat požadavky na virtuální počítač Azure souhrnu v této tabulce.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
**Hostovaný operační systém** | Site Recovery podporuje všechny operační systémy, které jsou [nepodporuje v Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Kontrola předpokladů se nezdaří, pokud není podporován.
**Architektura operačního systému hosta** | 64 bitů | Kontrola předpokladů se nezdaří, pokud není podporován.
**Velikost disku s operačním systémem** | Až 2048 GB pro virtuální počítače generace 1.<br/><br/> Až 300 GB pro virtuální počítače generace 2.  | Kontrola předpokladů se nezdaří, pokud není podporován.
**Počet disků s operačním systémem** | 1 | Kontrola předpokladů se nezdaří, pokud není podporován.
**Počet datových disků** | 16 nebo méně  | Kontrola předpokladů se nezdaří, pokud není podporován.
**Velikost datového virtuálního pevného disku** | Až 4 095 GB | Kontrola předpokladů se nezdaří, pokud není podporován.
**Síťové adaptéry** | Podporuje se více adaptérů |
**Sdílený virtuální pevný disk** | Nepodporuje se | Kontrola předpokladů se nezdaří, pokud není podporován.
**Disk FC** | Nepodporuje se | Kontrola předpokladů se nezdaří, pokud není podporován.
**Formát pevného disku** | VHD <br/><br/> VHDX | Při selhání do Azure, Site Recovery automaticky převede na virtuální pevný disk VHDX. Pokud selžou zpět na místní virtuální počítače nadále používat formát VHDX.
**Bitlocker** | Nepodporuje se | Než povolíte replikaci pro virtuální počítač, musí se zakázat nástroj BitLocker.
**Název virtuálního počítače** | 1 až 63 znaků. Pouze písmena, číslice a pomlčky. Název virtuálního počítače musí začínat a končit písmenem nebo číslicí. | Aktualizujte hodnotu ve vlastnostech virtuálního počítače ve službě Site Recovery.
**Typ virtuálního počítače** | 1. generace<br/><br/> Generace 2 – Windows | Virtuální počítače generace 2 se typ disku operačního systému basic (která zahrnuje jednu nebo dvě datové svazky naformátované jako VHDX) a menší než 300 GB místa na disku jsou podporovány.<br></br>Virtuální počítače s Linuxem generace 2 nejsou podporované. [Další informace](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="recovery-services-vault-actions"></a>Trezor služeb zotavení

**Akce** |  **Technologie Hyper-V s nástrojem VMM** | **Technologie Hyper-V bez VMM**
--- | --- | --- 
Přesunutí trezoru rámci skupiny prostředků<br/><br/> V rámci a napříč odběrů | Ne | Ne 
Přesunout úložiště, sítě, virtuální počítače Azure mezi skupinami prostředků<br/><br/> V rámci a napříč odběrů | Ne | Ne 


## <a name="provider-and-agent"></a>Zprostředkovatel a Agent

Pokud chcete mít jistotu, že vaše nasazení je kompatibilní s nastavením v tomto článku, ujistěte se, že používáte nejnovější zprostředkovatel a verze agenta.

**Název** | **Popis** | **Podrobnosti**
--- | --- | --- | --- | ---
**Zprostředkovatele Azure Site Recovery** | Koordinuje komunikaci mezi místními servery a Azure <br/><br/> Technologie Hyper-V s nástrojem VMM: nainstalovat na servery VMM<br/><br/> Technologie Hyper-V bez VMM: nainstalovány na hostitelích technologie Hyper-V| Nejnovější verzi: 5.1.2700.1 (k dispozici z portálu)<br/><br/> [Nejnovější funkce a opravy](https://aka.ms/latest_asr_updates)
**Agenta Microsoft Azure Recovery Services (MARS)** | Koordinuje replikaci mezi virtuální počítače Hyper-V a Azure<br/><br/> Nainstalována na místní servery Hyper-V (s nebo bez VMM) | Nejnovější verze agenta z portálu k dispozici






## <a name="next-steps"></a>Další postup
Zjistěte, jak [Příprava Azure](tutorial-prepare-azure.md) pro zotavení po havárii virtuálních počítačů technologie Hyper-V na místě. 

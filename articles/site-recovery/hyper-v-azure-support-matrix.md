---
title: Matice podpory pro replikaci Hyper-V do Azure | Dokumentace Microsoftu
description: Shrnuje podporované součásti a požadavky pro replikaci Hyper-V do Azure pomocí Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 3204329dc7c9efe2b0ba0ae05d17bc93d51620b4
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37921525"
---
# <a name="support-matrix-for-hyper-v-replication-to-azure"></a>Matice podpory pro replikaci Hyper-V do Azure


Tento článek shrnuje podporované komponenty a nastavení pro zotavení po havárii místních virtuálních počítačů Hyper-V do Azure s použitím [Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Podporované scénáře

**Scénář** | **Podrobnosti**
--- | ---
Technologie Hyper-V s Virtual Machine Manager | Můžete provést zotavení po havárii do Azure pro virtuální počítače spuštěné na hostitelích Hyper-V, které jsou spravované v prostředcích infrastruktury System Center Virtual Machine Manager.<br/><br/> Můžete nasadit tento scénář na webu Azure Portal nebo Powershellu.<br/><br/> Pokud hostitele Hyper-V jsou spravovány službou Virtual Machine Manager, můžete také provést zotavení po havárii do sekundární místní lokality. Další informace o tomto scénáři, [v tomto kurzu](tutorial-vmm-to-vmm.md).
Technologie Hyper-V bez Virtual Machine Manager | Můžete provést zotavení po havárii do Azure pro virtuální počítače spuštěné na hostitelích Hyper-V, která se nespravují ve Virtual Machine Manager.<br/><br/> Můžete nasadit tento scénář na webu Azure Portal nebo Powershellu.


## <a name="on-premises-servers"></a>Místní servery

**Server** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Technologie Hyper-V (bez Virtual Machine Manager spuštěna) | Windows Server 2016 (včetně instalace jádra serveru), Windows Server 2012 R2 s nejnovějšími aktualizacemi | Když konfigurujete ve službě Site Recovery do lokality Hyper-V, kombinování hostitele se systémem Windows Server 2016 a 2012 R2 se nepodporuje.<br/><br/> Pro virtuální počítače na hostitele se systémem Windows Server 2016 není podporováno obnovení do alternativního umístění.
Technologie Hyper-V (spuštěné s Virtual Machine Manager) | Virtual Machine Manager 2012 R2 Virtual Machine Manager 2016 | Pokud se používá Virtual Machine Manager, by měl spravovat hostitele Windows serveru 2016 ve Virtual Machine Manager 2016.<br/><br/> Virtual Machine Manager cloud, který kombinuje hostitele Hyper-V spuštěné na Windows serveru 2016 a 2012 R2 se momentálně nepodporuje.<br/><br/> Prostředí, které zahrnují upgrade existujícího serveru Virtual Machine Manager 2012 R2 na 2016 nejsou podporovány.


## <a name="replicated-vms"></a>Replikované virtuální počítače


Následující tabulka shrnuje podporu virtuálních počítačů. Site Recovery podporuje jakoukoli úlohu spuštěnou na podporovaném operačním systému.

 **Komponenta** | **Podrobnosti**
--- | ---
Konfigurace virtuálního počítače | Virtuální počítače, které se replikují do Azure, musí splňovat [požadavky služby Azure](#failed-over-azure-vm-requirements).
Hostovaný operační systém | Žádné hostovaný operační systém nepodporuje v Azure.<br/><br/> Windows Server 2016 Nano Server není podporována.




## <a name="hyper-v-network-configuration"></a>Konfigurace sítě technologie Hyper-V

**Komponenta** | **Technologie Hyper-V s Virtual Machine Manager** | **Technologie Hyper-V bez Virtual Machine Manager**
--- | --- | ---
Hostitelské sítě: seskupování síťové karty | Ano
Hostitelské sítě: sítě VLAN | Ano
Hostitelské sítě: IPv4 | Ano
Hostitelské sítě: IPv6 | Ne
Síť virtuálních počítačů hosta: seskupování síťové karty | Ne
Síť virtuálních počítačů hosta: IPv4 | Ano
Síť virtuálních počítačů hosta: IPv6 | Ne
Síť virtuálních počítačů hosta: statická IP adresa (Windows) | Ano
Síť virtuálních počítačů hosta: statická IP adresa (Linux) | Ne
Síť virtuálních počítačů hosta: s více síťovými Kartami | Ano



## <a name="azure-vm-network-configuration-after-failover"></a>Konfigurace sítě virtuálního počítače Azure (po převzetí služeb při selhání)

**Komponenta** | **Technologie Hyper-V s Virtual Machine Manager** | **Technologie Hyper-V bez Virtual Machine Manager**
--- | --- | ---
Azure ExpressRoute | Ano | Ano
Interní nástroj pro vyrovnávání zatížení | Ano | Ano
ELB | Ano | Ano
Azure Traffic Manager | Ano | Ano
S více síťovými Kartami | Ano | Ano
Rezervovaná IP adresa | Ano | Ano
IPv4 | Ano | Ano
Zachovat Zdrojová IP adresa | Ano | Ano
Koncové body služby virtuální sítě Azure<br/> (bez brány firewall služby Azure Storage) | Ano | Ano
Akcelerované síťové služby | Ne | Ne


## <a name="hyper-v-host-storage"></a>Úložiště hostitele Hyper-V

**Úložiště** | **Technologie Hyper-V s Virtual Machine Manager** | **Technologie Hyper-V bez Virtual Machine Manager**
--- | --- | --- | ---
Systém souborů NFS | Není k dispozici | Není k dispozici
SMB 3.0 | Ano | Ano
SÍŤ SAN (ISCSI) | Ano | Ano
Více cest (MPIO). Testovat pomocí:<br></br> DSM od společnosti Microsoft, SP4 PowerPath 5.7 EMC<br/><br/> EMC PowerPath DSM pro CLARiiON | Ano | Ano

## <a name="hyper-v-vm-guest-storage"></a>Úložiště hostů virtuálních počítačů Hyper-V

**Úložiště** | **Technologie Hyper-V s Virtual Machine Manager** | **Technologie Hyper-V bez Virtual Machine Manager**
--- | --- | ---
VMDK | Není k dispozici | Není k dispozici
VHD/VHDX | Ano | Ano
2. generace virtuálních počítačů | Ano | Ano
ROZHRANÍM EFI/UEFI| Ano | Ano
Sdílený disk clusteru | Ne | Ne
Šifrovaného disku | Ne | Ne
Systém souborů NFS | Není k dispozici | Není k dispozici
SMB 3.0 | Ne | Ne
RDM | Není k dispozici | Není k dispozici
Disk > 1 TB | Ano, až 4 095 GB | Ano, až 4 095 GB
Disku: logický a fyzický sektor 4 kB | Není podporováno: 1 nebo generace 2. generace | Není podporováno: 1 nebo generace 2. generace
Disku: 4 kB logický a fyzický sektor 512 bajtů | Ano |  Ano
Svazek s prokládané disk > 1 TB<br/><br/> Správa logických svazků (LVM) | Ano | Ano
Prostory úložiště | Ano | Ano
Přidání nebo odebrání horké disku | Ne | Ne
Vyloučení disku | Ano | Ano
Více cest (MPIO) | Ano | Ano

## <a name="azure-storage"></a>Azure Storage

**Komponenta** | **Technologie Hyper-V s Virtual Machine Manager** | **Technologie Hyper-V bez Virtual Machine Manager**
--- | --- | ---
(Locally redundant storage) Místně redundantní úložiště | Ano | Ano
Geograficky redundantní úložiště | Ano | Ano
Geograficky redundantní úložiště jen pro čtení | Ano | Ano
Úložiště Cool | Ne | Ne
Horkého úložiště| Ne | Ne
Objekty blob bloku | Ne | Ne
Šifrování v klidovém stavu (SSE)| Ano | Ano
Premium Storage | Ano | Ano
Služba import/export | Ne | Ne
Pro virtuální sítě na cílový účet úložiště mezipaměti nebo úložiště (sloužící k ukládání dat replikace) nakonfigurované brány firewall služby Azure Storage | Ne | Ne


## <a name="azure-compute-features"></a>Funkce výpočetní prostředky Azure

**Funkce** | **Technologie Hyper-V s Virtual Machine Manager** | **Technologie Hyper-V bez Virtual Machine Manager**
--- | --- | ---
Skupiny dostupnosti | Ano | Ano
CENTRUM | Ano | Ano  
Managed Disks | Ano, pro převzetí služeb při selhání.<br/><br/> Navrácení služeb po obnovení spravovaných disků se nepodporuje. | Ano, pro převzetí služeb při selhání.<br/><br/> Navrácení služeb po obnovení spravovaných disků se nepodporuje.

## <a name="azure-vm-requirements"></a>Požadavky virtuálních počítačů Azure

Místní virtuální počítače, které se replikují do Azure, musí splňovat požadavky virtuálního počítače Azure uvedené v této tabulce.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Hostovaný operační systém | Site Recovery podporuje všechny operační systémy, které jsou [nepodporuje v Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Kontrola předpokladů selže, pokud není podporován.
Architektura operačního systému hosta | 64 bitů | Kontrola předpokladů selže, pokud není podporován.
Velikost disku operačního systému | Až 2 048 GB pro virtuální počítače generace 1.<br/><br/> Až 300 GB pro virtuální počítače generace 2.  | Kontrola předpokladů selže, pokud není podporován.
Počet disků operačního systému | 1 | Kontrola předpokladů selže, pokud není podporován.
Počet datových disků | 16 nebo méně  | Kontrola předpokladů selže, pokud není podporován.
Velikost datového disku virtuálního pevného disku | Až 4 095 GB | Kontrola předpokladů selže, pokud není podporován.
Síťové adaptéry | Podporuje se více adaptérů |
Sdílený virtuální pevný disk | Nepodporuje se | Kontrola předpokladů selže, pokud není podporován.
FC disk | Nepodporuje se | Kontrola předpokladů selže, pokud není podporován.
Formát pevného disku | VIRTUÁLNÍ PEVNÝ DISK <br/><br/> VHDX | Site Recovery při převzetí služeb při selhání do Azure automaticky převede formát VHDX na VHD. Když převezmete služby zpět do místního, virtuální počítače i nadále používat formát VHDX.
BitLocker | Nepodporuje se | Než povolíte replikaci pro virtuální počítač, musí se zakázat nástroj BitLocker.
název virtuálního počítače | 1 až 63 znaků. Pouze písmena, číslice a pomlčky. Název virtuálního počítače musí začínat a končit písmenem nebo číslicí. | Aktualizujte hodnotu ve vlastnostech virtuálního počítače ve službě Site Recovery.
Typ virtuálního počítače | 1. generace<br/><br/> Generace 2 – Windows | Virtuální počítače generace 2 použijte disk operačního systému typu basic (která zahrnuje jednu nebo dvě datové svazky naformátované jako VHDX) a menší než 300 GB místa na disku se nepodporuje.<br></br>Virtuální počítače s Linuxem generace 2 nejsou podporované. [Další informace](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Trezor služeb zotavení

**Akce** |  **Technologie Hyper-V s Virtual Machine Manager** | **Technologie Hyper-V bez Virtual Machine Manager**
--- | --- | ---
Přesun trezoru mezi skupinami prostředků<br/><br/> V rámci a napříč předplatnými | Ne | Ne
Přesunout úložiště, sítě, virtuální počítače Azure mezi skupinami prostředků<br/><br/> V rámci a napříč předplatnými | Ne | Ne


## <a name="provider-and-agent"></a>Zprostředkovatel a agent

Pokud chcete mít jistotu, že vaše nasazení je kompatibilní s nastaveními v tomto článku, ujistěte se, že používáte nejnovější zprostředkovatel a agent verze.

**Název** | **Popis** | **Podrobnosti**
--- | --- | --- | --- | ---
Zprostředkovatel Azure Site Recovery | Koordinuje komunikaci mezi místními servery a Azure <br/><br/> Technologie Hyper-V s Virtual Machine Manager: nainstalovat na servery Virtual Machine Manager<br/><br/> Technologie Hyper-V bez Virtual Machine Manager: nainstalovány na hostitelích Hyper-V| Nejnovější verze: 5.1.2700.1 (k dispozici na webu Azure Portal)<br/><br/> [Nejnovější funkce a opravy](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Agenta služeb zotavení Microsoft Azure | Řídí replikaci mezi virtuálními počítači Hyper-V a Azure<br/><br/> Nainstalovat na místní servery Hyper-V (s nebo bez Virtual Machine Manager) | Nejnovější verzi agenta, které jsou k dispozici z portálu






## <a name="next-steps"></a>Další postup
Zjistěte, jak [Příprava Azure](tutorial-prepare-azure.md) pro zotavení po havárii místních virtuálních počítačů Hyper-V.

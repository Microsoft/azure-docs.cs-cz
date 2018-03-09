---
title: Azure Site Recovery matici podpory pro replikaci do Azure | Microsoft Docs
description: "Shrnuje podporované operační systémy a součásti služby Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/07/2018
ms.author: rajanaki
ms.openlocfilehash: 9c52ef47992474465111f106fc15779cadd825be
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-on-premises-to-azure"></a>Azure Site Recovery matici podpory pro replikaci z místního do Azure


Tento článek shrnuje podporované konfigurace a součásti služby Azure Site Recovery při replikaci a obnovení do Azure. Další informace o požadavcích na Azure Site Recovery najdete v tématu [požadavky](site-recovery-prereq.md).

> [!NOTE]
> Zkontrolujte, že je aktualizovat na nejnovější verzi zprostředkovatele Site Recovery a agenta, abyste dosáhli kompatibilitu s aktualizace v matici podpory.


## <a name="support-for-deployment-options"></a>Podporu pro možnosti nasazení

**Nasazení** | **VMware nebo fyzický server** | **Technologie Hyper-V (s/bez nástroje Virtual Machine Manager)** |
--- | --- | ---
**portál Azure Portal** | Místní virtuální počítače VMware do úložiště Azure s Azure Resource Manager nebo classic úložiště a sítě.<br/><br/> Převzetí služeb při selhání využívající Resource Manager nebo classic virtuálních počítačů. | Místní virtuální počítače Hyper-V do úložiště Azure, s Resource Manager nebo classic úložiště a sítě.<br/><br/> Převzetí služeb při selhání využívající Resource Manager nebo classic virtuálních počítačů.
**Portál Classic** | Pouze v režimu údržby. Nové trezory nelze vytvořit. | Pouze v režimu údržby.
**PowerShell** | Podporováno | Podporováno


## <a name="support-for-datacenter-management-servers"></a>Podpora pro servery pro správu datového centra

### <a name="virtualization-management-entities"></a>Virtualizace správu entity

**Nasazení** | **Podpora**
--- | ---
**Virtuální počítač VMware nebo fyzický server** | vCenter 6.5, 6.0 nebo 5.5
**Technologie Hyper-V (s nástrojem Virtual Machine Manager)** | System Center Virtual Machine Manager 2016 a System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > System Center Virtual Machine Manager 2016 cloudu s směs systému Windows Server 2016 a 2012 R2 hostitele není aktuálně podporován.
  > Konfigurace, které zahrnují upgrade existující SCVMM 2012 R2 na 2016 se aktuálně nepodporuje.
### <a name="host-servers"></a>Hostitelské servery

**Nasazení** | **Podpora**
--- | ---
**Virtuální počítač VMware nebo fyzický server** | vSphere 6.5, 6.0, 5.5
**Technologie Hyper-V (s/bez nástroje Virtual Machine Manager)** | Windows Server 2016, Windows Server 2012 R2 s nejnovějšími aktualizacemi.<br></br>Pokud se používá SCVMM hostitelů Windows Server 2016 se mají spravovat nástrojem SCVMM 2016.


  >[!Note]
  >Technologie Hyper-V lokalitě, která zkombinuje hostitele se systémem Windows Server 2016 a 2012 R2 není aktuálně podporován. Obnovení do alternativního umístění pro virtuální počítače na hostiteli systému Windows Server 2016 se aktuálně nepodporuje.

## <a name="support-for-replicated-machine-os-versions"></a>Podpora pro verze operačního systému replikovaného počítače

Virtuální počítače, které jsou chráněné musí splňovat [požadavky pro Azure](#failed-over-azure-vm-requirements) při replikaci do Azure.
Následující tabulka shrnuje podporu pro replikované operačního systému v různých scénářích nasazení při použití Azure Site Recovery. Tato podpora se vztahuje pro jakoukoli úlohu spuštěnou na uvedených operačního systému.

 **VMware nebo fyzický server** | **Technologie Hyper-V (s/bez VMM)** |
--- | --- |
64bitová verze systému Windows Server 2016 (jádra serveru, serveru pomocí možnosti práce s počítačem)\*, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 s v minimálně SP1<br/><br/> Red Hat Enterprise Linux: 5.2 k 5,11, 6.1 k 6.9 7.0 až 7,4<br/><br/>CentOS: 5.2 k 5,11, 6.1 k 6.9 7.0 až 7,4 <br/><br/>Ubuntu 14.04 LTS server[ (podporované verze jádra)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Ubuntu 16.04 LTS server[ (podporované verze jádra)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4, 6.5 systémem Red Hat kompatibilní jádra nebo nedělitelné Enterprise jádra verze 3 (UEK3) <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>(Upgrade replikaci počítačů z SLES 11 SP3 na SLES 11 SP4 není podporována. V případě upgradu replikovaného počítače z SLES 11SP3 pro SLES 11 SP4, musíte zakázat replikaci a chránit počítač znovu po upgradu.) | Všechny hostovaný operační systém [nepodporuje v Azure](https://technet.microsoft.com/library/cc794868.aspx)

>[!NOTE]
>
> \* Windows Server 2016 Nano Server není podporována.
>
> Na Linuxových distribucích jsou podporovány pouze uložené jádra, které jsou součástí verzi nebo aktualizaci podverze rozdělení.
>
> Upgrady mezi hlavní verze Linux rozdělení na Azure Site Recovery chráněný virtuální počítač VMware nebo fyzický server není podporováno. Při upgradu operačního systému přes hlavní verze (například CentOS 6.* k CentOS 7.*), zakažte replikaci pro počítač, upgrade operačního systému na počítači a potom povolit replikaci znovu.
>


### <a name="supported-ubuntu-kernel-versions-for-vmwarephysical-servers"></a>Podporované verze Ubuntu jádra pro VMware nebo fyzické servery

**Verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
14.04 LTS | 9.10 | 3.13.0-24-Generic k 3.13.0-121-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-Generic k 3.13.0-128-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-91-generic |
14.04 LTS | 9.12 | 3.13.0-24-Generic k 3.13.0-132-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-96-generic |
14.04 LTS | 9.13 | 3.13.0-24-Generic k 3.13.0-137-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-104-generic |
16.04 LTS | 9.10 | 4.4.0-21-Generic k 4.4.0-81-generic,<br/>4.8.0-34-Generic k 4.8.0-56-generic,<br/>4.10.0-14-Generic k 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-Generic k 4.4.0-91-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic k 4.10.0-32-generic |
16.04 LTS | 9.12 | 4.4.0-21-Generic k 4.4.0-96-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic k 4.10.0-35-generic |
16.04 LTS | 9.13 | 4.4.0-21-Generic k 4.4.0-104-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic k 4.10.0-42-generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-linux-vmwarephysical-servers"></a>Podporované souborové systémy a konfigurace úložiště hostovaný v systému Linux (VMware nebo fyzické servery)

Následující systémy souborů a úložiště konfigurace softwaru jsou podporovány pro servery Linux spuštěné v VMware nebo fyzických serverů:
* Systémy souborů: ext3, ext40, XFS
* Správce svazků: LVM2

Zařízení úložiště Paravirtualized (exportovat paravirtualized ovladače zařízení) nejsou podporovány.<br/>
Blokovat více fronty vstupně-výstupní operace zařízení nejsou podporovány.<br/>
Fyzické servery s řadič úložiště HP CCISS nejsou podporovány.<br/>

>[!Note]
> Na servery se systémem Linux následující adresáře (Pokud nastavený jako samostatné oddíly/souboru systems) musí být na stejném disku (disk operačního systému) na zdrojovém serveru: / (kořenová), / Boot, USR, /usr/local, /var, etc; a/Boot by měla být na oddílu disku a nesmí být svazek LVM<br/><br/>
>


## <a name="support-for-network-configuration"></a>Podpora pro konfiguraci sítě
Následující tabulka představuje souhrn podporu konfigurace sítě v různých scénářích nasazení, které používají Azure Site Recovery k replikaci do Azure.

### <a name="host-network-configuration"></a>Konfigurace sítě hostitele

**Konfigurace** | **VMware nebo fyzický server** | **Technologie Hyper-V (s/bez nástroje Virtual Machine Manager)**
--- | --- | ---
Seskupování síťových adaptérů | Ano<br/><br/>Není podporováno při replikaci fyzické počítače| Ano
SÍTĚ VLAN | Ano | Ano
IPv4 | Ano | Ano
IPv6 | Ne | Ne

### <a name="guest-vm-network-configuration"></a>Konfigurace sítě virtuálních počítačů hosta

**Konfigurace** | **VMware nebo fyzický server** | **Technologie Hyper-V (s/bez nástroje Virtual Machine Manager)**
--- | --- | ---
Seskupování síťových adaptérů | Ne | Ne
IPv4 | Ano | Ano
IPv6 | Ne | Ne
Statická IP adresa (Windows) | Ano | Ano
Statická IP adresa (Linux) | Ano <br/><br/>Virtuální počítače jsou nakonfigurované na používání protokolu DHCP na navrácení služeb po obnovení  | Ne
Multi-NIC | Ano | Ano

### <a name="failed-over-azure-vm-network-configuration"></a>Konfigurace sítě virtuálních počítačů Azure při selhání

**Síť Azure** | **VMware nebo fyzický server** | **Technologie Hyper-V (s/bez nástroje Virtual Machine Manager)**
--- | --- | ---
ExpressRoute | Ano | Ano
Interní nástroj pro vyrovnávání zatížení | Ano | Ano
ELB | Ano | Ano
Traffic Manager | Ano | Ano
Multi-NIC | Ano | Ano
Rezervovaná IP adresa | Ano | Ano
IPv4 | Ano | Ano
Zachovat zdrojové IP adresy | Ano | Ano
Virtuální síť koncové body služby (brány firewall úložiště Azure a virtuální sítě) | Ne | Ne


## <a name="support-for-storage"></a>Podpora pro úložiště
Následující tabulka představuje souhrn podporu konfigurace úložiště v různých scénářích nasazení, které používají Azure Site Recovery k replikaci do Azure.

### <a name="host-storage-configuration"></a>Konfigurace úložiště hostitele

**Konfigurace** | **VMware nebo fyzický server** | **Technologie Hyper-V (s/bez nástroje Virtual Machine Manager)**
--- | --- | --- | ---
NFS | Ano pro VMware<br/><br/> Ne pro fyzické servery | neuvedeno
SMB 3.0 | neuvedeno | Ano
SAN (ISCSI) | Ano | Ano
S více cestami (MPIO)<br></br>Testovány s: Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM pro CLARiiON | Ano | Ano

### <a name="guest-or-physical-server-storage-configuration"></a>Host nebo konfigurace úložiště fyzického serveru

**Konfigurace** | **VMware nebo fyzický server** | **Technologie Hyper-V (s/bez nástroje Virtual Machine Manager)**
--- | --- | ---
VMDK | Ano | neuvedeno
VHD/VHDX | neuvedeno | Ano
Fin 2 virtuálních počítačů | neuvedeno | Ano
EFI/UEFI| Migrace do Azure pro Windows Server 2012 a novější VMware pouze ve virtuálních počítačích. </br></br> ** Viz poznámka na konci v tabulce.  | Ano
Sdílený disk clusteru | Ne | Ne
Šifrované disku | Ne | Ne
NFS | Ne | neuvedeno
SMB 3.0 | Ne | Ne
RDM | Ano<br/><br/> Není k dispozici pro fyzické servery | neuvedeno
Disk > 1 TB | Ano<br/><br/>Až 4095 GB | Ano<br/><br/>Až 4095 GB
Disk s velikost fyzického sektoru 4K logické a 4 kB | Ano | Není podporována u virtuálních počítačů generace 1<br/><br/>Není podporován pro virtuální počítače generace 2.
Disk s 4K logické a velikost fyzického sektoru 512 bajtů | Ano |  Ano
Svazek s prokládané disku > 1 TB<br/><br/> Správa logických LVM svazků | Ano | Ano
Prostory úložiště | Ne | Ano
Přidat nebo odebrat aktivní disku | Ne | Ne
Vyloučení disku | Ano | Ano
S více cestami (MPIO) | neuvedeno | Ano

> [!NOTE]
> ** Rozhraní UEFI spouštění virtuálních počítačů VMware s Windows serverem 2012 nebo novější, se dají migrovat na Azure. Platí následující omezení.
> - Pouze migrace do Azure. Navrácení služeb po obnovení k místní lokalitě VMware není podporována.
> - Více než 4 oddíly jsou podporovány na disk operačního systému serveru.
> - Vyžaduje verzi služby Azure Site Recovery Mobility 9.13 nebo novější.
> - Není podporováno pro fyzických serverů.

**Úložiště Azure** | **VMware nebo fyzický server** | **Technologie Hyper-V (s/bez nástroje Virtual Machine Manager)**
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
Virtuální síť koncové body služby (brány firewall úložiště Azure a virtuální sítě) nakonfigurované na úložiště v cíli účet nebo účet úložiště používá k ukládání replikaci dat do mezipaměti | Ne | Ne
Účty úložiště obecné účely V2 (jak horkého a studeného úložiště vrstva) | Ne | Ne


## <a name="support-for-azure-compute-configuration"></a>Podpora pro Azure výpočetní konfigurace

**Výpočetní funkce** | **VMware nebo fyzický server** | **Technologie Hyper-V (s/bez nástroje Virtual Machine Manager)**
--- | --- | ---
Skupiny dostupnosti | Ano | Ano
ROZBOČOVAČE | Ano | Ano  
Managed Disks | Ano | Ano<br/><br/>Navrácení služeb po obnovení místně z virtuálního počítače Azure s spravované disky není aktuálně podporován.

## <a name="failed-over-azure-vm-requirements"></a>Požadavky na virtuální počítač Azure při selhání

Site Recovery můžete nasadit za účelem replikace virtuálních počítačů a fyzických serverů s jakýmkoli operačním systémem podporovaným v Azure. To zahrnuje většinu verzí systému Windows a Linux. Místní, že virtuální počítače, které chcete replikovat, musí odpovídat následující požadavky pro Azure při replikaci do Azure.

**Entity** | **Požadavky** | **Podrobnosti**
--- | --- | ---
**Hostovaný operační systém** | Technologie Hyper-V na Azure replikaci: Site Recovery podporuje všechny operační systémy, které jsou [nepodporuje v Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Pro fyzický server replikace a VMware: Zkontrolujte Windows a Linux [požadavky](site-recovery-vmware-to-azure-classic.md) | Kontrola předpokladů se nezdaří, pokud není podporován.
**Architektura operačního systému hosta** | 64 bitů | Kontrola předpokladů se nezdaří, pokud není podporován
**Velikost disku s operačním systémem** | Pokud replikujete až 2048 GB **virtuální počítače VMware nebo fyzických serverů do Azure**.<br/><br/>Až 2048 GB pro **technologie Hyper-V generace 1** virtuálních počítačů.<br/><br/>Až 300 GB pro **virtuálních počítačů technologie Hyper-V generace 2**.  | Kontrola předpokladů se nezdaří, pokud není podporován
**Počet disků s operačním systémem** | 1 | Kontrola předpokladů se nezdaří, pokud není podporován.
**Počet datových disků** | Pokud 64 nebo méně replikujete **virtuálních počítačů VMware do Azure**; 16 nebo méně Pokud replikujete **virtuálních počítačů Hyper-V do Azure** | Kontrola předpokladů se nezdaří, pokud není podporován
**Velikost datového virtuálního pevného disku** | Až 4 095 GB | Kontrola předpokladů se nezdaří, pokud není podporován
**Síťové adaptéry** | Podporuje se více adaptérů |
**Sdílený virtuální pevný disk** | Nepodporuje se | Kontrola předpokladů se nezdaří, pokud není podporován
**Disk FC** | Nepodporuje se | Kontrola předpokladů se nezdaří, pokud není podporován
**Formát pevného disku** | VHD <br/><br/> VHDX | Přestože Azure v současné době formát VHDX nepodporuje, Site Recovery při převzetí služeb při selhání do Azure automaticky převede formát VHDX na VHD. Pokud selžou zpět na místní virtuální počítače nadále používat formát VHDX.
**Bitlocker** | Nepodporuje se | Než začnete chránit virtuální počítač, musí se zakázat nástroj BitLocker.
**Název virtuálního počítače** | 1 až 63 znaků. Pouze písmena, číslice a pomlčky. Název virtuálního počítače musí začínat a končit písmenem nebo číslicí. | Aktualizujte hodnotu ve vlastnostech virtuálního počítače ve službě Site Recovery.
**Typ virtuálního počítače** | 1. generace<br/><br/> Generace 2 – Windows | Virtuální počítače generace 2 se typ disku operačního systému basic (která zahrnuje jednu nebo dvě datové svazky naformátované jako VHDX) a menší než 300 GB místa na disku jsou podporovány.<br></br>Virtuální počítače s Linuxem generace 2 nejsou podporované. [Další informace](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>Podpora pro akce trezoru služeb zotavení

**Akce** | **VMware nebo fyzický server** | **Technologie Hyper-V (bez Virtual Machine Manager)** | **Technologie Hyper-V (s nástrojem Virtual Machine Manager)**
--- | --- | --- | ---
Přesunutí trezoru rámci skupiny prostředků<br/><br/> V rámci a napříč odběrů | Ne | Ne | Ne
Přesunout úložiště, sítě, virtuální počítače Azure mezi skupinami prostředků<br/><br/> V rámci a napříč odběrů | Ne | Ne | Ne


## <a name="support-for-provider-and-agent"></a>Podpora pro zprostředkovatel a Agent

**Název** | **Popis** | **nejnovější verzi** | **Podrobnosti**
--- | --- | --- | --- | ---
**Zprostředkovatele Azure Site Recovery** | Koordinuje komunikaci mezi místními servery a Azure <br/><br/> Nainstalovat na místní servery nástroje Virtual Machine Manager nebo na servery Hyper-V, pokud neexistuje žádný server nástroje Virtual Machine Manager | 5.1.2700.1 (k dispozici z portálu) | [Nejnovější funkce a opravy](https://aka.ms/latest_asr_updates)
**Azure Site Recovery sjednocený instalační program (VMware do Azure)** | Koordinuje komunikaci mezi místními servery VMware a Azure <br/><br/> Nainstalovat na místní servery VMware | 9.12.4653.1 (k dispozici z portálu) | [Nejnovější funkce a opravy](https://aka.ms/latest_asr_updates)
**Služba mobility** | Koordinuje replikaci mezi místními VMware servery/fyzické servery a Azure nebo sekundární lokality<br/><br/> Nainstalovat na virtuální počítač VMware nebo fyzických serverů, které chcete replikovat  | 9.12.4653.1 (k dispozici z portálu) | [Nejnovější funkce a opravy](https://aka.ms/latest_asr_updates)
**Agenta Microsoft Azure Recovery Services (MARS)** | Koordinuje replikaci mezi virtuální počítače Hyper-V a Azure<br/><br/> Nainstalována na místní servery Hyper-V (s nebo bez serveru Virtual Machine Manager) | Nejnovější verze agenta (k dispozici z portálu) |






## <a name="next-steps"></a>Další postup
[Kontrola požadavků](site-recovery-prereq.md)

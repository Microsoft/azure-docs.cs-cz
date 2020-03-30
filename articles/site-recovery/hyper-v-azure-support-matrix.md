---
title: Podpora zotavení po havárii virtuálních počítačích Hyper-V do Azure pomocí Azure Site Recovery
description: Souhrn podporovaných součástí a požadavků na zotavení po havárii virtuálních počítačích Hyper-V v do Azure s Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 1/27/2020
ms.author: raynew
ms.openlocfilehash: d4409fe61bfe1f0a9fe74171f5b1ec471b9a6a26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258054"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Matice podpory pro zotavení po havárii místních virtuálních počítačích Hyper-V do Azure


Tento článek shrnuje podporované součásti a nastavení pro zotavení po havárii místních virtuálních počítačích Hyper-V do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).



## <a name="supported-scenarios"></a>Podporované scénáře

**Scénář** | **Podrobnosti**
--- | ---
Hyper-V se správcem virtuálních strojů <br> <br>| Obnovení po havárii můžete provést pro Azure for VMs spuštěné na hostitelích Hyper-V, které jsou spravované v prostředků Správce virtuálních strojů System Center.<br/><br/> Tento scénář můžete nasadit na webu Azure Portal nebo pomocí PowerShellu.<br/><br/> Když jsou hostitelé Technologie Hyper-V spravováni správcem virtuálních strojů, můžete také provést zotavení po havárii na sekundárním místním webu. Další informace o tomto scénáři naleznete v [tomto kurzu](hyper-v-vmm-disaster-recovery.md).
Hyper-V bez správce virtuálních strojů | Obnovení po havárii můžete provést v Azure for VM spouštěných na hostitelích Hyper-V, které nespravuje Správce virtuálních strojů.<br/><br/> Tento scénář můžete nasadit na webu Azure Portal nebo pomocí PowerShellu.

## <a name="on-premises-servers"></a>Místní servery

**Server** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Hyper-V (běží bez Správce virtuálních strojů) |  Windows Server 2019, Windows Server 2016 (včetně instalace jádra serveru), Windows Server 2012 R2 s nejnovějšími aktualizacemi | Pokud jste už nakonfigurovali Windows Server 2012 R2 s/nebo SCVMM 2012 R2 s Azure Site Recovery a plánujete upgrade operačního systému, postupujte podle [pokynů.](upgrade-2012R2-to-2016.md) 
Hyper-V (běží s Virtual Machine Manager) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Pokud se používá Virtual Machine Manager, měli by se hostitelé Windows Serveru 2019 spravovat ve Správci virtuálních strojů 2019. Podobně by měli být hostitelé Windows Serveru 2016 spravováni ve Správci virtuálních strojů 2016.<br/><br/> Poznámka: Pro hostitele Windows Server 2019 není podporováno navrácení služeb po selhání do alternativního umístění.


## <a name="replicated-vms"></a>Replikované virtuální hody


Následující tabulka shrnuje podporu virtuálních her. Site Recovery podporuje všechny úlohy spuštěné v podporovaném operačním systému.

 **Komponenta** | **Podrobnosti**
--- | ---
Konfigurace virtuálního počítače | Virtuální počítače, které se replikují do Azure, musí splňovat [požadavky Azure](#azure-vm-requirements).
Hostovaný operační systém | Jakýkoli hostovaný operační systém [podporovaný pro Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases)..<br/><br/> Windows Server 2016 Nano Server není podporován.


## <a name="vmdisk-management"></a>Správa virtuálního počítače/disku

**Akce** | **Podrobnosti**
--- | ---
Změna velikosti disku na replikovaném virtuálním počítači Hyper-VM | Není podporováno. Zakažte replikaci, proveďte změnu a znovu povolte replikaci pro virtuální hod.
Přidání disku do replikovaného virtuálního počítače Hyper-V | Není podporováno. Zakažte replikaci, proveďte změnu a znovu povolte replikaci pro virtuální hod.

## <a name="hyper-v-network-configuration"></a>Konfigurace sítě Hyper-V

**Komponenta** | **Hyper-V se správcem virtuálních strojů** | **Hyper-V bez správce virtuálních strojů**
--- | --- | ---
Hostitelská síť: Spojení síťových sítí | Ano | Ano
Hostitelská síť: VLAN | Ano | Ano
Hostitelská síť: IPv4 | Ano | Ano
Hostitelská síť: IPv6 | Ne | Ne
Síť virtuálních virtuálních připojení pro hosty: Seskazení síťových sítí | Ne | Ne
Síť virtuálních virtuálních připojení hosta: IPv4 | Ano | Ano
Síť virtuálních virtuálních připojení hosta: IPv6 | Ne | Ano
Síť virtuálních počítačů hosta: Statická IP (Windows) | Ano | Ano
Síť virtuálních počítačů hosta: Statická IP (Linux) | Ne | Ne
Síť virtuálních virtuálních připojení pro hosty: Multi-NIC | Ano | Ano



## <a name="azure-vm-network-configuration-after-failover"></a>Konfigurace sítě virtuálního počítače Azure (po převzetí služeb při selhání)

**Komponenta** | **Hyper-V se správcem virtuálních strojů** | **Hyper-V bez správce virtuálních strojů**
--- | --- | ---
Azure ExpressRoute | Ano | Ano
ILB | Ano | Ano
Elb | Ano | Ano
Azure Traffic Manager | Ano | Ano
Více nefunkční chod | Ano | Ano
Vyhrazená ADRESA IP | Ano | Ano
IPv4 | Ano | Ano
Zachovat zdrojovou ADRESU IP | Ano | Ano
Koncové body služby Virtuální sítě Azure<br/> (bez brány firewall azure storage) | Ano | Ano
Akcelerované síťové služby | Ne | Ne


## <a name="hyper-v-host-storage"></a>Hostitelské úložiště Hyper-V

**Úložiště** | **Hyper-V se správcem virtuálních strojů** | **Hyper-V bez správce virtuálních strojů**
--- | --- | --- 
NFS | Není k dispozici | Není k dispozici
SMB 3.0 | Ano | Ano
SAN (ISCSI) | Ano | Ano
Vícecestný (MPIO). Testováno s:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM pro CLARiiON | Ano | Ano

## <a name="hyper-v-vm-guest-storage"></a>Úložiště pro hosty virtuálního videa Hyper-V

**Úložiště** | **Hyper-V se správcem virtuálních strojů** | **Hyper-V bez správce virtuálních strojů**
--- | --- | ---
Vmdk | Není k dispozici | Není k dispozici
VHD/VHDX | Ano | Ano
Virtuální počítač generace 2 | Ano | Ano
EFI/UEFI<br></br>Migrovaný virtuální počítač v Azure se automaticky převede na spouštěcí virtuální počítač systému BIOS. Virtuální ms by měl být spuštěn windows server 2012 a novější pouze. Disk operačního systému by měl mít až pět oddílů nebo méně a velikost disku operačního systému by měla být menší než 300 GB.| Ano | Ano
Sdílený disk clusteru | Ne | Ne
Zašifrovaný disk | Ne | Ne
NFS | Není k dispozici | Není k dispozici
SMB 3.0 | Ne | Ne
Rdm | Není k dispozici | Není k dispozici
Disk>1 TB | Ano, až 4 095 GB | Ano, až 4 095 GB
Disk: 4K logický a fyzický sektor | Není podporováno: Gen 1/Gen 2 | Není podporováno: Gen 1/Gen 2
Disk: 4K logický a 512bajtů fyzický sektor | Ano |  Ano
Logická správa svazků (LVM). LVM je podporováno pouze na datových discích. Azure poskytuje jenom jeden disk operačního systému. | Ano | Ano
Svazek s prokládaným diskem >1 TB | Ano | Ano
Prostory úložiště | Ne | Ne
Aktivní přidat nebo odebrat disk | Ne | Ne
Vyloučení disku | Ano | Ano
Vícecestný (MPIO) | Ano | Ano

## <a name="azure-storage"></a>Azure Storage

**Komponenta** | **Hyper-V se správcem virtuálních strojů** | **Hyper-V bez správce virtuálních strojů**
--- | --- | ---
(Locally redundant storage) Místně redundantní úložiště | Ano | Ano
Geograficky redundantní úložiště | Ano | Ano
Geograficky redundantní úložiště s přístupem pro čtení | Ano | Ano
Chladné úložiště | Ne | Ne
Horké úložiště| Ne | Ne
Objekty blob bloku | Ne | Ne
Šifrování v klidovém stavu (SSE)| Ano | Ano
Šifrování v klidovém stavu (CMK) <br></br> (Pouze pro převzetí služeb při selhání na spravované disky)| Ano (dále přes modul PowerShell Az 3.3.0) | Ano (dále přes modul PowerShell Az 3.3.0)
Premium Storage | Ano | Ano
Služba importu a exportu | Ne | Ne
Účty Azure Storage s povolenou bránou firewall | Ano. Pro cílové úložiště a mezipaměť. | Ano. Pro cílové úložiště a mezipaměť.
Úprava účtu úložiště | Ne. Cílový účet azure storage nelze změnit po povolení replikace. Chcete-li upravit, zakažte a znovu povolte zotavení po havárii. | Ne


## <a name="azure-compute-features"></a>Výpočetní funkce Azure

**Funkce** | **Hyper-V se správcem virtuálních strojů** | **Hyper-V bez správce virtuálních strojů**
--- | --- | ---
Skupiny dostupnosti | Ano | Ano
Rozbočovač | Ano | Ano  
Spravované disky | Ano, za převzetí služeb při selhání.<br/><br/> Navrácení služeb po selhání spravovaných disků není podporováno. | Ano, za převzetí služeb při selhání.<br/><br/> Navrácení služeb po selhání spravovaných disků není podporováno.

## <a name="azure-vm-requirements"></a>Požadavky na virtuální počítač Azure

Místní virtuální počítače, které replikujete do Azure, musí splňovat požadavky na virtuální počítače Azure shrnuté v této tabulce.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Hostovaný operační systém | Site Recovery podporuje všechny operační systémy, které jsou [podporované Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Kontrola požadavků se nezdaří, pokud není podporována.
Architektura hostovaného operačního systému | 32bitový (Windows Server 2008)/64bitový | Kontrola požadavků se nezdaří, pokud není podporována.
Velikost disku s operačním systémem | Až 2 048 GB pro virtuální virtuální připojení generace 1.<br/><br/> Až 300 GB pro virtuální virtuální připojení generace 2.  | Kontrola požadavků se nezdaří, pokud není podporována.
Počet disků s operačním systémem | 1 | Kontrola požadavků se nezdaří, pokud není podporována.
Počet datových disků | 16 nebo méně  | Kontrola požadavků se nezdaří, pokud není podporována.
Velikost datového virtuálního pevného disku | Až 4 095 GB | Kontrola požadavků se nezdaří, pokud není podporována.
Síťové adaptéry | Podporuje se více adaptérů |
Sdílený virtuální pevný disk | Nepodporuje se | Kontrola požadavků se nezdaří, pokud není podporována.
Disk FC | Nepodporuje se | Kontrola požadavků se nezdaří, pokud není podporována.
Formát pevného disku | VHD <br/><br/> VHDX | Obnovení webu automaticky převede VHDX na virtuální pevný disk při převzetí služeb při selhání do Azure. Když se vrátíte do místního prostředí, virtuální počítače nadále používají formát VHDX.
BitLocker | Nepodporuje se | Nástroj BitLocker musí být zakázán před povolením replikace pro virtuální ho.
název virtuálního počítače | 1 až 63 znaků. Pouze písmena, číslice a pomlčky. Název virtuálního počítače musí začínat a končit písmenem nebo číslicí. | Aktualizujte hodnotu ve vlastnostech virtuálního počítače v obnovení webu.
Typ virtuálního počítače | 1. generace<br/><br/> Generace 2 -- Windows | Generace 2 Virtuální počítače s typem disku operačního systému základní (který zahrnuje jeden nebo dva datové svazky formátované jako VHDX) a méně než 300 GB místa na disku jsou podporovány.<br></br>Virtuální počítače Linux Generation 2 nejsou podporované. [Další informace](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Akce trezoru služby Recovery Services

**Akce** |  **Technologie Hyper-V s vmm** | **Hyper-V bez VMM**
--- | --- | ---
Přesunutí trezoru mezi skupinami prostředků<br/><br/> V rámci předplatných i napříč | Ne | Ne
Přesunutí úložiště, sítě a virtuálních počítačích Azure napříč skupinami prostředků<br/><br/> V rámci předplatných i napříč | Ne | Ne

> [!NOTE]
> Při replikaci hyper-virtuálních počítačů z místního do Azure můžete replikovat pouze do jednoho klienta AD z jednoho konkrétního prostředí – web Hyper-V nebo Hyper-V s vmm podle potřeby.


## <a name="provider-and-agent"></a>Zprostředkovatel a agent

Chcete-li se ujistit, že je vaše nasazení kompatibilní s nastavením v tomto článku, ujistěte se, že používáte nejnovější verze zprostředkovatele a agenta.

**Název** | **Popis** | **Podrobnosti**
--- | --- | --- 
Zprostředkovatel obnovení webu Azure | Koordinuje komunikaci mezi místními servery a Azure <br/><br/> Hyper-V s Virtual Machine Manager: Nainstalováno na serverech Virtual Machine Manager<br/><br/> Hyper-V bez správce virtuálních strojů: Nainstalováno na hostitelích Hyper-V| Nejnovější verze: 5.1.2700.1 (k dispozici na webu Azure Portal)<br/><br/> [Nejnovější funkce a opravy](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Agent Microsoft Azure Recovery Services | Koordinuje replikaci mezi virtuálními počítači Hyper-V a Azure<br/><br/> Instalace na místních serverech Hyper-V (s virtual machine managerem nebo bez něj) | Nejnovější agent k dispozici z portálu






## <a name="next-steps"></a>Další kroky
Zjistěte, jak [připravit Azure](tutorial-prepare-azure.md) na zotavení po havárii místních virtuálních počítačích Hyper-V.

---
title: "Matici podpory pro replikaci virtuálních počítačů technologie Hyper-V v nástroji VMM cloudů na sekundární lokalitu s Azure Site Recovery | Microsoft Docs"
description: "Shrnuje podporu pro replikaci virtuálního počítače technologie Hyper-V v cloudech VMM do sekundární lokality s Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: 767b0e76b73c082ddb75374f51700b85272f713e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="support-matrix-for-replication-of-hyper-v-vms-to-a-secondary-site"></a>Podporu pro replikaci virtuálních počítačů technologie Hyper-V do sekundární lokality

Tento článek shrnuje, co je podporováno při použití [Azure Site Recovery](site-recovery-overview.md) služby k replikaci virtuálních počítačů Hyper-V spravované v cloudech System Center Virtual Machine Manager (VMM) do sekundární lokality. Pokud chcete replikovat virtuální počítače Hyper-V do Azure, přečtěte si [této matici podpory](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Můžete provádět pouze replikaci do sekundární lokality při správě hostitele Hyper-V v cloudech VMM.

  

## <a name="host-servers"></a>Hostitelské servery

**Operační systém** | **Podrobnosti**
--- | ---
Windows Server 2012 R2 | Servery musí používat nejnovější aktualizace.
Windows Server 2016 |  Cloudy VMM 2016 s směs systému Windows Server 2016 a 2012 R2 hostitelů nejsou aktuálně podporovány.<br/><br/> Nasazení, které se upgradovaly z System Center 2012 R2 VMM 2012 R2 na System Center 2016 nejsou aktuálně podporovány.


## <a name="replicated-vm-support"></a>Replikovat podporu virtuálních počítačů

Následující tabulka shrnuje podporu operačního systému pro počítače replikovat pomocí Site Recovery. Libovolnou úlohu lze spustit na podporovaný operační systém.

**Verze systému Windows** | **Technologie Hyper-V (s nástrojem VMM)**
--- | ---
Windows Server 2016 | Všechny hostované operační systém [podporovaná technologií Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) na Windows Server 2016 
Windows Server 2012 R2 | Všechny hostované operační systém [podporovaná technologií Hyper-V](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) v systému Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Úložiště počítače Linux

Je možné replikovat jenom počítače se systémem Linux s následující úložiště:

- File system (EXT3, ETX4, ReiserFS, XFS).
- Vícenásobný software zařízení Mapper.
- Správce svazků (LVM2).
- Fyzické servery s HP CCISS řadič úložiště nejsou podporovány.
- Systém souborů ReiserFS je podporována pouze na SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Síťová konfigurace – hostitele nebo hosta virtuálního počítače

**Konfigurace** | **Podporuje se**  
--- | --- 
Host - seskupování síťových adaptérů | Ano 
Host - sítě VLAN | Ano 
Host - IPv4 | Ano 
Host - IPv6 | Ne 
Hosta virtuálního počítače – seskupování síťových adaptérů | Ne
Hosta virtuálního počítače – IPv4 | Ano
Hosta virtuálního počítače – IPv6 | Ne
Virtuální počítač Gues – Windows nebo Linuxem - statická IP adresa | Ano
Hostovaný virtuální počítač – více síťovými Kartami | Ano


## <a name="storage"></a>Úložiště

### <a name="host-storage"></a>Hostování úložiště

**Úložiště (hostitel)** | **Podporuje se**
--- | --- 
NFS | neuvedeno
SMB 3.0 |  Ano
SAN (ISCSI) | Ano
S více cestami (MPIO) | Ano

### <a name="guest-or-physical-server-storage"></a>Host nebo fyzický server úložiště

**Konfigurace** | **Podporuje se**
--- | --- | 
VMDK |  neuvedeno
VHD/VHDX | Ano (až 16 disků)
Fin 2 virtuálních počítačů | Ano
Sdílený disk clusteru | Ne
Šifrované disku | Ne
ROZHRANÍ UEFI| neuvedeno
NFS | Ne
SMB 3.0 | Ne
RDM | neuvedeno
Disk > 1 TB | Ano
Svazek s prokládané disku > 1 TB<br/><br/> LVM | Ano
Prostory úložiště | Ano
Přidat nebo odebrat aktivní disku | Ne
Vyloučení disku | Ano
S více cestami (MPIO) | Ano

## <a name="vaults"></a>trezory

**Akce** | **Podporuje se**
--- | --- 
Přesunutí trezorů v rámci skupiny prostředků (v rámci nebo předplatných) |  Ne
Přesunout úložiště, sítě, virtuální počítače Azure mezi skupinami prostředků (v rámci nebo předplatných) | Ne

## <a name="azure-site-recovery-provider"></a>Zprostředkovatele Azure Site Recovery

Zprostředkovatel koordinuje komunikaci mezi servery VMM. 

**Latest** | **Aktualizace**
--- | --- | --- | --- | ---
5.1.19 ([dostupná z portálu](http://aka.ms/downloaddra) | [Nejnovější funkce a opravy](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Další postup

[Replikace virtuálních počítačů technologie Hyper-V v cloudech VMM do sekundární lokality](tutorial-vmm-to-vmm.md)


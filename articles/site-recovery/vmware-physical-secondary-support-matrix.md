---
title: "Matici podpory pro replikaci virtuálních počítačů VMware nebo fyzických serverů do sekundární lokality VMware s Azure Site Recovery | Microsoft Docs"
description: "Shrnuje podporu replikace VMware nebo fyzický server na sekundární lokalitu s Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: b51a4573ad7a8461b7261f08d94639d2030492d9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="support-matrix-for-replication-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Podporu pro replikaci virtuálních počítačů VMware a fyzické servery do sekundární lokality

Tento článek shrnuje, co je podporováno při použití [Azure Site Recovery](site-recovery-overview.md) služby replikovat virtuální počítače VMware nebo Windows nebo Linuxem fyzických serverů do sekundární lokality VMware.

- Pokud chcete replikovat virtuální počítače VMware nebo fyzických serverů do Azure, přečtěte si [této matici podpory](vmware-physical-azure-support-matrix.md).
- Pokud chcete replikovat virtuální počítače Hyper-V do sekundární lokality, přečtěte si [této matici podpory](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Replikace virtuálních počítačů VMware na místě a fyzických serverů jsou poskytovány InMage Scout. InMage Scout je součástí předplatné služby Azure Site Recovery.


## <a name="host-servers"></a>Hostitelské servery

**Operační systém** | **Podrobnosti**
--- | ---
vCenter server | vCenter 5.5, 6.0 a verze 6.5<br/><br/> Pokud spustíte 6.0 nebo 6.5, Všimněte si, že jsou podporovány pouze verzi 5.5 funkce.


## <a name="replicated-vm-support"></a>Replikovat podporu virtuálních počítačů

Následující tabulka shrnuje podporu operačního systému pro počítače replikovat pomocí Site Recovery. Libovolnou úlohu lze spustit na podporovaný operační systém.

**Operační systém** | **Podrobnosti**
--- | ---
Windows Server | 64bitová verze systému Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 s v minimálně SP1.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 systémem Red Hat kompatibilní jádra nebo nedělitelné Enterprise jádra verze 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


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
NFS | Ano 
SMB 3.0 | neuvedeno 
SAN (ISCSI) | Ano 
S více cestami (MPIO) | Ano 

### <a name="guest-or-physical-server-storage"></a>Host nebo fyzický server úložiště

**Konfigurace** | **Podporuje se** 
--- | --- 
VMDK | Ano 
VHD/VHDX | neuvedeno 
Fin 2 virtuálních počítačů | neuvedeno 
Sdílený disk clusteru | Ano 
Šifrované disku | Ne 
ROZHRANÍ UEFI| Ano 
NFS | Ne 
SMB 3.0 | Ne 
RDM | Ano 
Disk > 1 TB | Ano 
Svazek s prokládané disku > 1 TB<br/><br/> LVM | Ano 
Prostory úložiště | Ne 
Přidat nebo odebrat aktivní disku | Ano 
Vyloučení disku | Ano 
S více cestami (MPIO) | neuvedeno 

## <a name="vaults"></a>trezory

**Akce** | **Podporuje se** 
--- | --- 
Přesunutí trezorů v rámci skupiny prostředků (v rámci nebo předplatných) | Ne 
Přesunout úložiště, sítě, virtuální počítače Azure mezi skupinami prostředků (v rámci nebo předplatných) | Ne 

## <a name="mobility-service-and-updates"></a>Služba mobility a aktualizace

Služba Mobility koordinuje replikaci mezi místními servery VMware nebo fyzických serverů a sekundární lokality. Při nastavování replikace, měli byste si ověřit, že máte nejnovější verzi služby Mobility a další součásti.

**Aktualizace** | **Podrobnosti** 
--- | --- 
Scout aktualizace | [Další informace o a stažení](/vmware-physical-secondary-disaster-recovery.md#updates) nejnovější aktualizace Scout | Scout aktualizace jsou kumulativní.
Aktualizace komponenty | Aktualizace Scout obsahovat aktualizace pro všechny součásti, včetně RX server, konfigurační server, proces a hlavních cílových serverů, vContinuum servery a zdrojové servery, které chcete chránit.<br/><br/> [Další informace](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).


## <a name="next-steps"></a>Další postup

Stažení [InMage Scout uživatelská příručka](https://aka.ms/asr-scout-user-guide)

- [Replikace virtuálních počítačů technologie Hyper-V v cloudech VMM do sekundární lokality](tutorial-vmm-to-vmm.md)
- [Replikace fyzických serverů a virtuálních počítačů VMware do sekundární lokality](tutorial-vmware-to-vmware.md)

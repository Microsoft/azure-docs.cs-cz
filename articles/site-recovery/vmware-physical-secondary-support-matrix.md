---
title: Matice podpory pro zotavení po havárii virtuálních počítačů VMware nebo fyzické servery do sekundární lokality VMware pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Shrnuje podporu pro zotavení po havárii virtuálních počítačů VMware a fyzické servery do sekundární lokality pomocí Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: b03ee4ebf1113fd422d34ebf219461e75c4a5a45
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53970759"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Matice podpory pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do sekundární lokality

Tento článek shrnuje, co je podporováno při použití [Azure Site Recovery](site-recovery-overview.md) služby zotavení po havárii virtuálních počítačů VMware nebo Windows/Linux fyzické servery do sekundární lokality VMware.

- Pokud chcete replikovat virtuální počítače VMware nebo fyzických serverů do Azure, přečtěte si [tento systém podpory replikace z](vmware-physical-azure-support-matrix.md).
- Pokud chcete replikovat virtuální počítače Hyper-V do sekundární lokality, přečtěte si [tento systém podpory replikace z](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Replikace místních virtuálních počítačů VMware a fyzických serverů je poskytována nástroje InMage Scout. Nástroje InMage Scout je zahrnutý v předplatném služby Azure Site Recovery.


## <a name="host-servers"></a>Hostitelské servery

**Operační systém** | **Podrobnosti**
--- | ---
Server vCenter | vCenter 5.5, 6.0 a 6.5<br/><br/> Pokud spustíte 6.0 nebo 6.5, mějte na paměti, že jsou podporovány pouze 5.5 se v této funkce.


## <a name="replicated-vm-support"></a>Replikované podpora virtuálních počítačů

Následující tabulka shrnuje podporu operačního systému pro počítače pomocí služby Site Recovery replikovat. Jakékoli úlohy můžou běžet na podporovaný operační systém.

**Operační systém** | **Podrobnosti**
--- | ---
Windows Server | 64bitová verze Windows serveru 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 s na minimálně SP1.
Linux | Red Hat Enterprise Linux 6.7, 6.8 6.9, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 kompatibilní jádra Red Hat nebo nedělitelné Enterprise jádra verze 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


## <a name="linux-machine-storage"></a>Úložiště počítače Linux

Je možné replikovat jenom počítače s Linuxem pomocí následující úložiště:

- Systém (EXT3 ETX4, ReiserFS, XFS) souborů.
- Mapování více cest softwaru zařízení.
- Správce svazků (LVM2).
- Fyzické servery s HP CCISS řadič úložiště nejsou podporované.
- Systém souborů ReiserFS je podporován pouze v operačním systémem SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Konfigurace sítě - hostitele nebo hosta virtuálního počítače

**Konfigurace** | **Podporuje se**  
--- | --- 
Hostitel – seskupování síťových adaptérů | Ano 
Hostitel – sítě VLAN | Ano 
Hostitel – protokolu IPv4 | Ano 
Hostitel – IPv6 | Ne 
Hosta virtuálního počítače – seskupování síťových adaptérů | Ne
Hosta virtuálního počítače – protokolu IPv4 | Ano
Hosta virtuálního počítače – IPv6 | Ne
Virtuální počítač Gues – Windows/Linux - statická IP adresa | Ano
Hosta virtuálního počítače – s více síťovými Kartami | Ano


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Úložiště hostitele

**Úložiště (hostitel)** | **Podporuje se** 
--- | --- 
Systém souborů NFS | Ano 
SMB 3.0 | neuvedeno 
SÍŤ SAN (ISCSI) | Ano 
Více cest (MPIO) | Ano 

### <a name="guest-or-physical-server-storage"></a>Host nebo fyzický server úložiště

**Konfigurace** | **Podporuje se** 
--- | --- 
VMDK | Ano 
VHD/VHDX | neuvedeno 
Virtuální počítače 2. generace | neuvedeno 
Sdílený disk clusteru | Ano 
Šifrovaného disku | Ne 
ROZHRANÍ UEFI| Ano 
Systém souborů NFS | Ne 
SMB 3.0 | Ne 
RDM | Ano 
Disk > 1 TB | Ano 
Svazek s prokládané disk > 1 TB<br/><br/> LVM | Ano 
Prostory úložiště | Ne 
Přidání nebo odebrání horké disku | Ano 
Vyloučení disku | Ano 
Více cest (MPIO) | neuvedeno 

## <a name="vaults"></a>Trezory

**Akce** | **Podporuje se** 
--- | --- 
Přesun trezorů služby mezi skupinami prostředků (v rámci předplatného ani mezi předplatnými) | Ne 
Přesunout úložiště, sítě, virtuální počítače Azure mezi skupinami prostředků (v rámci předplatného ani mezi předplatnými) | Ne 

## <a name="mobility-service-and-updates"></a>Službu mobility a aktualizace

Služba Mobility koordinuje replikaci mezi místními servery VMware nebo fyzických serverů a sekundární lokality. Při nastavování replikace nezapomeňte, že máte nejnovější verzi služby Mobility a jiných komponent.

**Aktualizace** | **Podrobnosti** 
--- | --- 
Aktualizace řešení Scout | [Další informace o a stáhnout](vmware-physical-secondary-disaster-recovery.md#updates) nejnovější aktualizace Scout | Jsou kumulativní aktualizace Scout.
Aktualizace součástí | Aktualizace Scout zahrnují aktualizace pro všechny součásti, včetně RX serveru, konfiguračního serveru, proces a hlavní cílové servery, servery vContinuum a zdrojových serverů, které chcete chránit.<br/><br/> [Další informace](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).


## <a name="next-steps"></a>Další postup

Stáhněte si [uživatelské příručce nástroje InMage Scout](https://aka.ms/asr-scout-user-guide)

- [Replikace virtuálních počítačů Hyper-V v cloudech VMM do sekundární lokality](tutorial-vmm-to-vmm.md)
- [Replikace fyzických serverů a virtuálních počítačů VMware do sekundární lokality](tutorial-vmware-to-vmware.md)

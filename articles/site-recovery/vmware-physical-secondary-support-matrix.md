---
title: Matice podpory pro zotavení po havárii virtuálních počítačů VMware nebo fyzických serverů na sekundární lokalitu VMware s Azure Site Recovery | Microsoft Docs
description: Shrnuje podporu pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do sekundární lokality s Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 08/22/2019
ms.author: raynew
ms.openlocfilehash: c330afb2c5d315b3d386d1477669f1aab2f6e6f9
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972076"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Matice podpory pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do sekundární lokality

Tento článek shrnuje, co je podporováno při použití služby [Azure Site Recovery](site-recovery-overview.md) k zotavení po havárii virtuálních počítačů VMware nebo fyzických serverů se systémem Windows nebo Linux do sekundární lokality VMware.

- Pokud chcete replikovat virtuální počítače VMware nebo fyzické servery do Azure, přečtěte si [tuto matrici podpory](vmware-physical-azure-support-matrix.md).
- Pokud chcete replikovat virtuální počítače Hyper-V do sekundární lokality, přečtěte si [tuto matrici podpory](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Replikaci místních virtuálních počítačů VMware a fyzických serverů poskytuje InMage Scout. InMage Scout je součástí předplatného služby Azure Site Recovery.


## <a name="host-servers"></a>Hostitelské servery

**Operační systém** | **Podrobnosti**
--- | ---
Server vCenter | vCenter 5,5, 6,0 a 6,5<br/><br/> Pokud spouštíte 6,0 nebo 6,5, počítejte s tím, že jsou podporovány pouze funkce 5,5.


## <a name="replicated-vm-support"></a>Podpora replikovaných virtuálních počítačů

Následující tabulka shrnuje podporu operačních systémů pro počítače replikované pomocí Site Recovery. V podporovaném operačním systému může běžet libovolný pracovní postup.

**Operační systém** | **Podrobnosti**
--- | ---
Windows Server | 64-bit Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 s minimálně SP1.
Linux | Red Hat Enterprise Linux 6,7, 6,8, 6,9, 7,1, 7,2 <br/><br/> CentOS 6,5, 6,6, 6,7, 6,8, 6,9, 7,0, 7,1, 7,2 <br/><br/> Oracle Enterprise Linux 6,4, 6,5, 6,8, na kterých běží jádro kompatibilní s Red Hat, nebo nepřerušené podnikové jádro verze 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


## <a name="linux-machine-storage"></a>Úložiště počítačů se systémem Linux

Replikovat se dají jenom počítače se systémem Linux s následujícím úložištěm:

- Systém souborů (EXT3, ETX4, ReiserFS, XFS).
- Multipath software – Mapovač zařízení.
- Správce svazků (LVM2).
- Fyzické servery s úložištěm řadiče HP CCISS se nepodporují.
- Systém souborů ReiserFS je podporován pouze v SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Konfigurace sítě – virtuální počítač hosta/hosta

**Konfigurace** | **Podporuje se**  
--- | --- 
Seskupování síťových adaptérů hosta | Ano 
Hostitel-síť VLAN | Ano 
Hostitel – IPv4 | Ano 
Hostitel – IPv6 | Ne 
Virtuální počítač hosta – seskupování síťových adaptérů | Ne
Virtuální počítač hosta – IPv4 | Ano
Virtuální počítač hosta – IPv6 | Ne
Virtuální počítač hosta – Windows/Linux – statická IP adresa | Ano
Virtuální počítač hosta – více síťových karet | Ano


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Úložiště hostitele

**Úložiště (hostitel)** | **Podporuje se** 
--- | --- 
NFS | Ano 
SMB 3.0 | Není k dispozici 
SÍŤ SAN (ISCSI) | Ano 
Multipath (multi-Path) | Ano 

### <a name="guest-or-physical-server-storage"></a>Úložiště hostů nebo fyzických serverů

**Konfigurace** | **Podporuje se** 
--- | --- 
VMDK | Ano 
VHD/VHDX | Není k dispozici 
Virtuální počítač 2. generace | Není k dispozici 
Disk sdíleného clusteru | Ano 
Zašifrovaný disk | Ne 
UEFI| Ano 
NFS | Ne 
SMB 3.0 | Ne 
RDM | Ano 
Disk > 1 TB | Ano 
Svazek se zakládaným diskem > 1 TB<br/><br/> LVM | Ano 
Prostory úložiště | Ne 
Hot Add/Remove disk | Ano 
Vyloučení disku | Ano 
Multipath (multi-Path) | Není k dispozici 

## <a name="vaults"></a>Trezory

**Akce** | **Podporuje se** 
--- | --- 
Přesun trezorů mezi skupinami prostředků (v rámci předplatného nebo mezi nimi) | Ne 
Přesunutí úložiště, sítě, virtuálních počítačů Azure napříč skupinami prostředků (v rámci předplatných nebo mezi nimi) | Ne 

## <a name="mobility-service-and-updates"></a>Služba mobility a aktualizace

Služba mobility koordinuje replikaci mezi místními servery VMware nebo fyzickými servery a sekundární lokalitou. Při nastavování replikace byste měli mít jistotu, že máte nejnovější verzi služby mobility, a dalších součástí.

| **Aktualizace** | **Podrobnosti** |
| --- | --- |
|Aktualizace Scout | Aktualizace Scout jsou kumulativní. <br/><br/> Seznamte se s nejnovějšími aktualizacemi Scout [a stáhněte](vmware-physical-secondary-disaster-recovery.md#updates) si je |
|Aktualizace součástí | Aktualizace Scout obsahují aktualizace pro všechny součásti, včetně serveru pro příjem dat, konfiguračního serveru, procesu a hlavních cílových serverů, serverů vContinuum a zdrojových serverů, které chcete chránit.<br/><br/> [Další informace](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>Další kroky

Stáhnout uživatelskou příručku k [InMage Scout](https://aka.ms/asr-scout-user-guide)

- [Replikace virtuálních počítačů Hyper-V v cloudech VMM do sekundární lokality](tutorial-vmm-to-vmm.md)
- [Replikace fyzických serverů a virtuálních počítačů VMware do sekundární lokality](tutorial-vmware-to-vmware.md)

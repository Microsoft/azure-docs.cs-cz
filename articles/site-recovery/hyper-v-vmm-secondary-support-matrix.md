---
title: Podpora zotavení po havárii technologie Hyper-V do sekundární lokality VMM pomocí Azure Site Recovery
description: Shrnuje podporu replikace virtuálních počítačů Hyper-V v cloudech VMM do sekundární lokality s Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: af7baf413c9054ef3e5bf527851ac06c113cdce7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86131165"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Matice podpory pro zotavení po havárii virtuálních počítačů Hyper-V do sekundární lokality

Tento článek shrnuje, co je podporováno při používání služby [Azure Site Recovery](site-recovery-overview.md) k replikaci virtuálních počítačů Hyper-V spravovaných v cloudech System Center Virtual Machine Manager (VMM) do sekundární lokality. Pokud chcete replikovat virtuální počítače Hyper-V do Azure, přečtěte si [tuto matrici podpory](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Replikaci do sekundární lokality můžete provést pouze v případě, že jsou hostitelé Hyper-V spravováni v cloudech VMM.


## <a name="host-servers"></a>Hostitelské servery

**Operační systém** | **Podrobnosti**
--- | ---
Windows Server 2012 R2 | Na serverech musí běžet nejnovější aktualizace.
Windows Server 2016 |  V současnosti se nepodporují cloudy VMM 2016 se směsí Windows Server 2016 a 2012 hostitelé R2.<br/><br/> Nasazení, která se upgradují z System Center 2012 R2 VMM 2012 R2 na System Center 2016, se v tuto chvíli nepodporují.


## <a name="replicated-vm-support"></a>Podpora replikovaných virtuálních počítačů

Následující tabulka shrnuje podporu operačních systémů pro počítače replikované pomocí Site Recovery. V podporovaném operačním systému může běžet libovolný pracovní postup.

**Verze Windows** | **Hyper-V (s VMM)**
--- | ---
Windows Server 2016 | Libovolný hostovaný operační systém [, který podporuje technologie Hyper-v](/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) v systému Windows Server 2016 
Windows Server 2012 R2 | Libovolný hostovaný operační systém [, který podporuje technologie Hyper-v](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) v systému Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Úložiště počítačů se systémem Linux

Replikovat se dají jenom počítače se systémem Linux s následujícím úložištěm:

- Systém souborů (EXT3, ETX4, ReiserFS, XFS).
- Multipath software – Mapovač zařízení.
- Správce svazků (LVM2).
- Fyzické servery s úložištěm řadiče HP CCISS se nepodporují.
- Systém souborů ReiserFS je podporován pouze v SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Konfigurace sítě – virtuální počítač hosta/hosta

**Konfigurace** | **Podporováno**  
--- | --- 
Seskupování síťových adaptérů hosta | Yes 
Hostitel-síť VLAN | Yes 
Hostitel – IPv4 | Yes 
Hostitel – IPv6 | No 
Virtuální počítač hosta – seskupování síťových adaptérů | No
Virtuální počítač hosta – IPv4 | Yes
Virtuální počítač hosta – IPv6 | No
Virtuální počítač hosta – Windows/Linux – statická IP adresa | Yes
Virtuální počítač hosta – více síťových karet | Ano


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Úložiště hostitele

**Úložiště (hostitel)** | **Podporováno**
--- | --- 
NFS | Není k dispozici
SMB 3.0 |  Yes
SÍŤ SAN (ISCSI) | Yes
Multipath (multi-Path) | Yes

### <a name="guest-or-physical-server-storage"></a>Úložiště hostů nebo fyzických serverů

**Konfigurace** | **Podporováno**
--- | --- | 
FORMÁTU |  Není k dispozici
VHD/VHDX | Ano (až 16 disků)
Virtuální počítač 2. generace | Yes
Disk sdíleného clusteru | No
Zašifrovaný disk | No
UEFI| Není k dispozici
NFS | No
SMB 3.0 | No
RDM | Není k dispozici
Disk > 1 TB | Yes
Svazek se zakládaným diskem > 1 TB<br/><br/> LVM | Yes
Prostory úložiště | Yes
Hot Add/Remove disk | No
Vyloučení disku | Yes
Multipath (multi-Path) | Yes

## <a name="vaults"></a>Trezory

**Akce** | **Podporováno**
--- | --- 
Přesun trezorů mezi skupinami prostředků (v rámci předplatného nebo mezi nimi) |  No
Přesunutí úložiště, sítě, virtuálních počítačů Azure napříč skupinami prostředků (v rámci předplatných nebo mezi nimi) | No

## <a name="azure-site-recovery-provider"></a>Poskytovatel Azure Site Recovery

Poskytovatel koordinuje komunikaci mezi servery VMM. 

**Latest (Nejnovější)** | **Aktualizace**
--- | --- 
5.1.19 ([k dispozici z portálu](https://aka.ms/downloaddra) | [Nejnovější funkce a opravy](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Další kroky

[Replikace virtuálních počítačů Hyper-V v cloudech VMM do sekundární lokality](./hyper-v-vmm-disaster-recovery.md)

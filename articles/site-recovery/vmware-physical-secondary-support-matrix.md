---
title: Podpora pro VMware/fyzické zotavení po havárii do sekundární lokality s Azure Site Recovery
description: Shrnuje podporu pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do sekundární lokality s Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: c6f0f48df32db0beb9c0a57982d9bc87b26538d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86135148"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Matice podpory pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do sekundární lokality

Tento článek shrnuje, co je podporováno při použití služby [Azure Site Recovery](site-recovery-overview.md) k zotavení po havárii virtuálních počítačů VMware nebo fyzických serverů se systémem Windows nebo Linux do sekundární lokality VMware.

- Pokud chcete replikovat virtuální počítače VMware nebo fyzické servery do Azure, přečtěte si [tuto matrici podpory](vmware-physical-azure-support-matrix.md).
- Pokud chcete replikovat virtuální počítače Hyper-V do sekundární lokality, přečtěte si [tuto matrici podpory](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Replikaci místních virtuálních počítačů VMware a fyzických serverů poskytuje InMage Scout. InMage Scout je součástí předplatného služby Azure Site Recovery.

## <a name="end-of-support-announcement"></a>Oznámení o ukončení podpory
Scénář Site Recovery pro replikaci mezi místními VMware nebo fyzickými datacentry se blíží konci podpory.

- Od srpna 2018 se scénář nedá nakonfigurovat v Recovery Services trezoru a InMage Scout software nejde stáhnout z trezoru. Existující nasazení budou podporována.
- - Od prosince 31 2020 se scénář nepodporuje.
Stávající partneři mohou připojit nové zákazníky do scénáře až do doby, než skončí podpora.
- Během 2018 a 2019 budou vydány dvě aktualizace:

    - Aktualizace 7: opravuje problémy s konfigurací sítě a dodržováním předpisů a poskytuje podporu TLS 1,2.
    - Aktualizace 8: přidává podporu pro operační systémy Linux RHEL/CentOS 7.3/7.4/7.5 a pro SUSE 12
    - Po aktualizaci 8 nebudou zveřejněny žádné další aktualizace. Pro operační systémy přidané v Update 8 bude omezená podpora oprav hotfix a opravy chyb na základě nejlepšího úsilí.

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
NFS | Yes 
SMB 3.0 | Není k dispozici 
SÍŤ SAN (ISCSI) | Yes 
Multipath (multi-Path) | Yes 

### <a name="guest-or-physical-server-storage"></a>Úložiště hostů nebo fyzických serverů

**Konfigurace** | **Podporováno** 
--- | --- 
FORMÁTU | Yes 
VHD/VHDX | Není k dispozici 
Virtuální počítač 2. generace | Není k dispozici 
Disk sdíleného clusteru | Yes 
Zašifrovaný disk | No 
UEFI| Yes 
NFS | No 
SMB 3.0 | No 
RDM | Yes 
Disk > 1 TB | Yes 
Svazek se zakládaným diskem > 1 TB<br/><br/> LVM | Yes 
Prostory úložiště | No 
Hot Add/Remove disk | Yes 
Vyloučení disku | Yes 
Multipath (multi-Path) | Není k dispozici 

## <a name="vaults"></a>Trezory

**Akce** | **Podporováno** 
--- | --- 
Přesun trezorů mezi skupinami prostředků (v rámci předplatného nebo mezi nimi) | No 
Přesunutí úložiště, sítě, virtuálních počítačů Azure napříč skupinami prostředků (v rámci předplatných nebo mezi nimi) | No 

## <a name="mobility-service-and-updates"></a>Služba mobility a aktualizace

Služba mobility koordinuje replikaci mezi místními servery VMware nebo fyzickými servery a sekundární lokalitou. Při nastavování replikace byste měli mít jistotu, že máte nejnovější verzi služby mobility, a dalších součástí.

| **Aktualizace** | **Podrobnosti** |
| --- | --- |
|Aktualizace Scout | Aktualizace Scout jsou kumulativní. <br/><br/> [Seznamte se s](vmware-physical-secondary-disaster-recovery.md#updates) nejnovějšími aktualizacemi Scout a Stáhněte si je |
|Aktualizace součástí | Aktualizace Scout obsahují aktualizace pro všechny součásti, včetně serveru pro příjem dat, konfiguračního serveru, procesu a hlavních cílových serverů, serverů vContinuum a zdrojových serverů, které chcete chránit.<br/><br/> [Další informace](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>Další kroky

Stáhnout [uživatelskou příručku k InMage Scout](https://aka.ms/asr-scout-user-guide)

- [Replikace virtuálních počítačů Hyper-V v cloudech VMM do sekundární lokality](./hyper-v-vmm-disaster-recovery.md)
- [Replikace fyzických serverů a virtuálních počítačů VMware do sekundární lokality](./vmware-physical-secondary-disaster-recovery.md)

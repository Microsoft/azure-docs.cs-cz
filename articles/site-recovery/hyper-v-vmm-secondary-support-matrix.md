---
title: Podpora zabezpečení zabezpečení matrix-Hyper-V pro sekundární lokalitu VMM pomocí Azure Site Recovery
description: Shrnuje podporu pro replikaci hyper-v virtuálních počítačích v cloudech VMM do sekundární lokality s Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 1126a85ed22ee17879767a93ca75dc76dd04b747
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74132961"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Matice podpory pro zotavení po havárii virtuálních počítačů Hyper-V do sekundární lokality

Tento článek shrnuje, co je podporováno, když používáte službu [Obnovení webu Azure](site-recovery-overview.md) k replikaci virtuálních počítačů Hyper-V spravovaných v cloudech Nástroje pro virtuální počítače (System Center) do sekundární lokality. Pokud chcete replikovat virtuální počítače Hyper-V do Azure, zkontrolujte [tuto matici podpory](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Do sekundární lokality se dá replikovat jenom v případě, že jsou hostitelé Hyper-V spravováni v cloudech VMM.


## <a name="host-servers"></a>Hostitelské servery

**Operační systém** | **Podrobnosti**
--- | ---
Windows Server 2012 R2 | Servery musí mít nejnovější aktualizace.
Windows Server 2016 |  Cloudy VMM 2016 se směsí hostitelů Windows Server 2016 a 2012 R2 nejsou momentálně podporovány.<br/><br/> Nasazení upgradovaná z System Center 2012 R2 VMM 2012 R2 do System Center 2016 nejsou momentálně podporovaná.


## <a name="replicated-vm-support"></a>Podpora replikovaného virtuálního montovny

Následující tabulka shrnuje podporu operačního systému pro počítače replikované pomocí site recovery. V podporovaném operačním systému může být spuštěno jakékoli pracovní vytížení.

**Verze systému Windows** | **Hyper-V (s VMM)**
--- | ---
Windows Server 2016 | Jakýkoli hostovaný operační systém [podporovaný technologieí Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) v systému Windows Server 2016 
Windows Server 2012 R2 | Jakýkoli hostovaný operační systém [podporovaný technologieí Hyper-V](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) v systému Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Úložiště linuxových strojů

Replikovat lze pouze linuxové počítače s následujícím úložištěm:

- Souborový systém (EXT3, ETX4, ReiserFS, XFS).
- Multipath software-zařízení Mapper.
- Správce hlasitosti (LVM2).
- Fyzické servery s úložištěm řadiče HP CCISS nejsou podporovány.
- Souborový systém ReiserFS je podporován pouze na serveru SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Konfigurace sítě – host/hostovaný virtuální počítače

**Konfigurace** | **Podporovány**  
--- | --- 
Hostitel – spojení nic | Ano 
Hostitel – VLAN | Ano 
Hostitel – IPv4 | Ano 
Hostitel – IPv6 | Ne 
Host VM - NIC teaming | Ne
Host VM - IPv4 | Ano
Virtuální virtuální mše hosta – IPv6 | Ne
Host InM - Windows/Linux - Statická IP adresa | Ano
Virtuální virtuální mše hosta – multi-NIC | Ano


## <a name="storage"></a>Úložiště

### <a name="host-storage"></a>Hostitelské úložiště

**Úložiště (hostitel)** | **Podporovány**
--- | --- 
NFS | Není dostupné.
SMB 3.0 |  Ano
SAN (ISCSI) | Ano
Vícecestný (MPIO) | Ano

### <a name="guest-or-physical-server-storage"></a>Úložiště hosta nebo fyzického serveru

**Konfigurace** | **Podporovány**
--- | --- | 
Vmdk |  Není dostupné.
VHD/VHDX | Ano (až 16 disků)
Gen 2 Virtuální virtuální měna | Ano
Sdílený disk clusteru | Ne
Zašifrovaný disk | Ne
Uefi| Není dostupné.
NFS | Ne
SMB 3.0 | Ne
Rdm | Není dostupné.
Disková > 1 TB | Ano
Svazek s prokládaným diskem > 1 TB<br/><br/> Lvm | Ano
Prostory úložiště | Ano
Aktivní přidat nebo odebrat disk | Ne
Vyloučení disku | Ano
Vícecestný (MPIO) | Ano

## <a name="vaults"></a>Trezory

**Akce** | **Podporovány**
--- | --- 
Přesunutí trezorů mezi skupinami prostředků (v rámci předplatných nebo mezi ně) |  Ne
Přesunutí úložiště, sítě a virtuálních počítačích Azure napříč skupinami prostředků (v rámci nebo napříč předplatnými) | Ne

## <a name="azure-site-recovery-provider"></a>Zprostředkovatel obnovení webu Azure

Zprostředkovatel koordinuje komunikaci mezi servery VMM. 

**Nejnovější** | **Aktualizace**
--- | --- 
5.1.19 ([k dispozici na portálu](https://aka.ms/downloaddra) | [Nejnovější funkce a opravy](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Další kroky

[Replikace virtuálních stránek Hyper-V v cloudech VMM do sekundární lokality](tutorial-vmm-to-vmm.md)


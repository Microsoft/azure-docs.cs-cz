---
title: Podpora pro obnovení voblasti VMware/fyzické havárie na sekundární lokalitě pomocí Azure Site Recovery
description: Shrnuje podporu pro zotavení po havárii virtuálních počítačích VMware a fyzických serverů do sekundární lokality pomocí Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: cabd3f7693c6b6b86bf0324bdafdfe1377d1ece8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256793"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Matice podpory pro zotavení po havárii virtuálních virtuálních zařízení VMware a fyzických serverů do sekundární lokality

Tento článek shrnuje, co je podporováno při použití služby [Azure Site Recovery](site-recovery-overview.md) pro zotavení po havárii virtuálních počítačů VMware nebo fyzických serverů Windows/Linux na sekundárním webu VMware.

- Pokud chcete replikovat virtuální počítače VMware nebo fyzické servery do Azure, zkontrolujte [tuto matici podpory](vmware-physical-azure-support-matrix.md).
- Pokud chcete replikovat virtuální servery Hyper-V do sekundární lokality, zkontrolujte [tuto matici podpory](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Replikaci místních virtuálních počítačů VMware a fyzických serverů zajišťuje inmage scout. InMage Scout je součástí předplatného služby Azure Site Recovery.

## <a name="end-of-support-announcement"></a>Oznámení o ukončení podpory
Scénář obnovení lokality pro replikaci mezi místními vmware nebo fyzickými datovými centry dosahuje ukončení podpory.

- Od srpna 2018 nelze scénář nakonfigurovat v trezoru služby Recovery Services a software InMage Scout nelze stáhnout z trezoru. Stávající nasazení budou podporována.
- - prosince 2020 nebude scénář podporován.
Stávající partneři mohou do scénáře zařazovat nové zákazníky, dokud podpora neskončí.
- Během let 2018 a 2019 budou vydány dvě aktualizace:

    - Aktualizace 7: Opravuje problémy s konfigurací sítě a dodržováním předpisů a poskytuje podporu TLS 1.2.
    - Aktualizace 8: Přidává podporu pro operační systémy Linux RHEL/CentOS 7.3/7.4/7.5 a pro SUSE 12
    - Po aktualizaci 8 nebudou vydány žádné další aktualizace. Bude omezená podpora oprav hotfix pro operační systémy přidané v aktualizaci 8 a opravy chyb na základě nejlepšího úsilí.

## <a name="host-servers"></a>Hostitelské servery

**Operační systém** | **Podrobnosti**
--- | ---
Server vCenter | vCentrum 5,5, 6,0 a 6,5<br/><br/> Pokud spustíte 6.0 nebo 6.5, všimněte si, že jsou podporovány pouze 5,5 funkce.


## <a name="replicated-vm-support"></a>Podpora replikovaného virtuálního montovny

Následující tabulka shrnuje podporu operačního systému pro počítače replikované pomocí site recovery. V podporovaném operačním systému může být spuštěno jakékoli pracovní vytížení.

**Operační systém** | **Podrobnosti**
--- | ---
Windows Server | 64bitový Systém Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 s alespoň sp1.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> Centos 6,5, 6,6, 6,7, 6,8, 6,9, 7,0, 7,1, 7,2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 s jádrem kompatibilním s Red Hat nebo unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


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
NFS | Ano 
SMB 3.0 | Není dostupné. 
SAN (ISCSI) | Ano 
Vícecestný (MPIO) | Ano 

### <a name="guest-or-physical-server-storage"></a>Úložiště hosta nebo fyzického serveru

**Konfigurace** | **Podporovány** 
--- | --- 
Vmdk | Ano 
VHD/VHDX | Není dostupné. 
Gen 2 Virtuální virtuální měna | Není dostupné. 
Sdílený disk clusteru | Ano 
Zašifrovaný disk | Ne 
Uefi| Ano 
NFS | Ne 
SMB 3.0 | Ne 
Rdm | Ano 
Disková > 1 TB | Ano 
Svazek s prokládaným diskem > 1 TB<br/><br/> Lvm | Ano 
Prostory úložiště | Ne 
Aktivní přidat nebo odebrat disk | Ano 
Vyloučení disku | Ano 
Vícecestný (MPIO) | Není dostupné. 

## <a name="vaults"></a>Trezory

**Akce** | **Podporovány** 
--- | --- 
Přesunutí trezorů mezi skupinami prostředků (v rámci předplatných nebo mezi ně) | Ne 
Přesunutí úložiště, sítě a virtuálních počítačích Azure napříč skupinami prostředků (v rámci nebo napříč předplatnými) | Ne 

## <a name="mobility-service-and-updates"></a>Služby mobility a aktualizace

Služba Mobility koordinuje replikaci mezi místními servery VMware nebo fyzickými servery a sekundární lokalitou. Při nastavování replikace byste se měli ujistit, že máte nejnovější verzi služby Mobility a dalšísoučásti.

| **Aktualizace** | **Podrobnosti** |
| --- | --- |
|Aktualizace skautů | Aktualizace průzkumu jsou kumulativní. <br/><br/> [Seznamte se s](vmware-physical-secondary-disaster-recovery.md#updates) nejnovějšími aktualizacemi scoutů a stáhněte si je |
|Aktualizace komponent | Aktualizace programu Scout zahrnují aktualizace pro všechny součásti, včetně serveru RX, konfiguračního serveru, cílových serverů procesů a hlavních cílových serverů, serverů vContinuum a zdrojových serverů, které chcete chránit.<br/><br/> [Další informace](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>Další kroky

Stáhněte si [uživatelskou příručku InMage Scout](https://aka.ms/asr-scout-user-guide)

- [Replikace virtuálních stránek Hyper-V v cloudech VMM do sekundární lokality](tutorial-vmm-to-vmm.md)
- [Replikace fyzických serverů a virtuálních počítačů VMware do sekundární lokality](tutorial-vmware-to-vmware.md)

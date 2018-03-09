---
title: "Matici podpory Azure Site Recovery pro virtuální počítače VMware a fyzické servery replikující se do Azure | Microsoft Docs"
description: "Shrnuje podporované operační systémy a komponenty pro replikaci virtuálních počítačů VMware do Azure pomocí Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/07/2018
ms.author: raynew
ms.openlocfilehash: 10d7db60ef584632a45fd7cdc5877461fa45af03
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>Podporu pro VMware a fyzické serveru replikaci do Azure.


Tento článek obsahuje souhrn podporovaných součásti a nastavení pro zotavení po havárii virtuálních počítačů VMware do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Podporované scénáře

**Scénář** | **Podrobnosti**
--- | ---
**Virtuální počítače VMware** | Pro virtuální počítače VMware na místě, můžete provést zotavení po havárii do Azure. Můžete nasadit tento scénář na portálu Azure nebo pomocí prostředí PowerShell.
**Fyzické servery** | Můžete provést zotavení po havárii do Azure pro místní Windows nebo Linuxem fyzických serverů. Můžete nasadit tento scénář v portálu Azure.

## <a name="on-premises-virtualization-servers"></a>Místní virtualizace serverů

**Server** | **Požadavky** | **Podrobnosti**
--- | --- | ---
**VMware** | vCenter Server verze 6.5, 6.0, nebo 5.5 nebo vSphere verze 6.5, 6.0 nebo 5,5 | Doporučujeme vám, že používáte vCenter server.
**Fyzické servery** | neuvedeno


## <a name="replicated-machines"></a>Replikovaných počítačů

Následující tabulka shrnuje podporu replikace pro počítače. Site Recovery podporuje replikaci jakoukoli úlohu spuštěnou na počítači s podporovaným operačním systémem.

**Komponenta** | **Podrobnosti**
--- | ---
Konfigurace počítače | Počítače, které se replikují do Azure, musí splňovat [požadavky pro Azure](#failed-over-azure-vm-requirements).
Operačního systému počítače (Windows) | 64bitová verze systému Windows Server 2016 (jádra serveru, serveru pomocí možnosti práce s počítačem), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 s v minimálně SP1
Operačního systému počítače (Linux) | Red Hat Enterprise Linux: 5.2 k 5,11, 6.1 k 6.9 7.0 až 7,4 <br/><br/>CentOS: 5.2 k 5,11, 6.1 k 6.9 7.0 až 7,4 <br/><br/>Ubuntu 14.04 LTS server[ (podporované verze jádra)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Ubuntu 16.04 LTS server[ (podporované verze jádra)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4, 6.5 systémem Red Hat kompatibilní jádra nebo nedělitelné Enterprise jádra verze 3 (UEK3) <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>(Upgrade replikaci počítačů z SLES 11 SP3 na SLES 11 SP4 není podporována. Pokud replikovaného počítače je upgrade z SLES 11SP3 na SLES 11 SP4, budete muset zakázat replikaci a chránit počítač znovu po upgradu.)

>[!NOTE]
>
> - Na Linuxových distribucích jsou podporovány pouze uložené jádra, které jsou součástí verzi nebo aktualizaci podverze rozdělení.
>
> - Nepodporují se upgrady mezi hlavní verze Linux rozdělení na virtuální počítač chráněný Site Recovery VMware nebo fyzický server. Při upgradu operačního systému přes hlavní verze (například CentOS 6.* k CentOS 7.*), zakažte replikaci pro počítač, upgrade operačního systému na počítači a potom povolit replikaci znovu.
>

### <a name="ubuntu-kernel-versions"></a>Verze Ubuntu jádra


**Podporované verze** | **Verze Azure je služba Site Recovery Mobility** | **Verze jádra** |
--- | --- | --- |
14.04 LTS | 9.10 | 3.13.0-24-Generic k 3.13.0-121-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-Generic k 3.13.0-128-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-91-generic |
14.04 LTS | 9.12 | 3.13.0-24-Generic k 3.13.0-132-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-96-generic |
14.04 LTS | 9.13 | 3.13.0-24-Generic k 3.13.0-137-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-104-generic |
16.04 LTS | 9.10 | 4.4.0-21-Generic k 4.4.0-81-generic,<br/>4.8.0-34-Generic k 4.8.0-56-generic,<br/>4.10.0-14-Generic k 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-Generic k 4.4.0-91-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic k 4.10.0-32-generic |
16.04 LTS | 9.12 | 4.4.0-21-Generic k 4.4.0-96-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic k 4.10.0-35-generic |
16.04 LTS | 9.13 | 4.4.0-21-Generic k 4.4.0-104-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic k 4.10.0-42-generic |

## <a name="linux-file-systemsguest-storage-configurations"></a>Konfigurace úložiště systémy nebo hosta souborový Linux

**Komponenta** | **Podporuje se**
--- | ---
systémy souborů | ext3, ext4, XFS
Správce svazků | LVM2
Zařízení úložiště Paravirtualized | Zařízení exportovaná paravirtualizovanými ovladači se nepodporují.
Blokovat více fronty vstupně-výstupní operace zařízení | Nepodporuje se.
Fyzické servery se řadič úložiště HP CCISS | Nepodporuje se.
Adresáře | Tyto adresáře (Pokud nastavený jako samostatné oddíly/souboru systems) musí být na stejném disku operačního systému na zdrojovém serveru: / (kořenová), / Boot, USR, /usr/local, /var, etc. </br></br> Pokud / (uživatel root) svazek je svazek LVM, pak/Boot musí nacházet na samostatném oddílu na stejném disku a nesmí být svazek LVM.<br/><br/>
|Požadavky na volné místo| 2 GB v oddílu/root <br/>250 MB v instalační složce
XFSv5 | Funkce XFSv5 v systémech souborů s XFS, jako je například metadata kontrolního součtu, jsou podporovány z verze 9.10 služby Mobility a novější. Nástroje xfs_info zkontrolujte tzv XFS pro oddíl. Pokud ftype nastavena na hodnotu 1, funkce XFSv5 jsou používány.



## <a name="network"></a>Síť

**Komponenta** | **Podporuje se**
--- | ---
Seskupování síťových hostitele síťových adaptérů | Pro virtuální počítače VMware podporován. <br/><br/>Není podporováno pro replikaci fyzického počítače.
Hostitelské sítě VLAN | Ano
Hostitelské sítě IPv4 | Ano
Síť hostitele IPv6 | Ne
Seskupování hostů a serveru sítě síťových adaptérů | Ne
Host server sítě IPv4 | Ano
Host server sítě IPv6 | Ne
Host server síti statickou IP adresu (Windows) | Ano
Host server síti statickou IP adresu (Linux) | Ano <br/><br/>Virtuální počítače jsou nakonfigurované pro používání protokolu DHCP na navrácení služeb po obnovení.  
Host server sítě několik síťových adaptérů | Ano


## <a name="azure-vm-network-after-failover"></a>Azure síť virtuálních počítačů (po převzetí služeb při selhání)

**Komponenta** | **Podporuje se**
--- | ---
Azure ExpressRoute | Ano
Interní nástroj pro vyrovnávání zatížení | Ano
ELB | Ano
Azure Traffic Manager | Ano
Multi-NIC | Ano
Vyhrazená IP adresa | Ano
IPv4 | Ano
Zachovat zdrojové IP adresy | Ano
Koncové body služby Azure virtuální sítě<br/><br/> (Brány firewall úložiště azure a virtuální sítě) | Ne

## <a name="storage"></a>Úložiště
**Komponenta** | **Podporuje se**
--- | ---
Hostitele systému souborů NFS | Ano pro VMware<br/><br/> Ne pro fyzických serverů.
Síť SAN (ISCSI) hostitele | Ano
Hostitel s více cestami (MPIO) | Ano, testována s Microsoft DSM EMC PowerPath 5.7 SP4, EMC PowerPath DSM CLARiiON
Guest/server VMDK | Ano
Guest/server EFI/UEFI| Partial (migrace do Azure pro Windows Server 2012 a novější virtuální počítače VMware pouze) </br></br> Viz poznámka na konci v tabulce.
Disk sdíleného clusteru hosta/server | Ne
Šifrované disk hosta/server | Ne
Guest/server NFS | Ne
Host server protokolu SMB 3.0 | Ne
Host server RDM | Ano<br/><br/> Není k dispozici pro fyzické servery
Disk Host server > 1 TB | Ano<br/><br/>Až 4095 GB
Host server na disku s velikost fyzického sektoru 4K logické a 4 kB | Ano
Disk Host server s 4K logické a velikost fyzického sektoru 512 bajtů | Ano
Host server svazek s prokládané disku > 4 TB <br><br/>Správa logických LVM svazků | Ano
Host server - prostory úložiště | Ne
Disk přidat nebo odebrat aktivní hosta/server | Ne
Host server - vyloučení disku | Ano
Host server s více cestami (MPIO) | neuvedeno

> [!NOTE]
> Rozhraní UEFI spouštění virtuálních počítačů VMware s Windows serverem 2012 nebo novější se dají migrovat na Azure. Platí následující omezení:

> - Je podporována pouze migrace do Azure. Navrácení služeb po obnovení k místní lokalitě VMware není podporována.
> - Server by neměla mít více než čtyři oddíly na disk operačního systému.
> - Vyžaduje verzi služby Mobility 9.13 nebo novější.
> - Není podporováno pro fyzických serverů.

## <a name="azure-storage"></a>Azure Storage

**Komponenta** | **Podporuje se**
--- | ---
LRS | Ano
GRS | Ano
RA-GRS | Ano
Studeného úložiště | Ne
Horkého úložiště| Ne
Objekty blob bloku | Ne
Šifrování v klidovém stavu (SSE)| Ano
Storage úrovně Premium | Ano
Import a export služby | Ne
Koncové body služby virtuální sítě<br/><br/> Úložiště brány firewall a virtuální sítě, které jsou nakonfigurované na cílový účet úložiště nebo mezipaměti úložiště (používá se k uložení dat replikace) | Ne
Účty úložiště obecné účely v2 (horká a studená vrstev) | Ne

## <a name="azure-compute"></a>Výpočtů Azure

**Funkce** | **Podporuje se**
--- | ---
Skupiny dostupnosti | Ano
ROZBOČOVAČE | Ano   
Managed Disks | Ano

## <a name="azure-vm-requirements"></a>Požadavky virtuálního počítače Azure

Místní virtuální počítače, které se replikují do Azure, musí splňovat požadavky na virtuální počítač Azure souhrnu v této tabulce.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
**Hostovaný operační systém** | Ověřte [podporované operační systémy](#replicated machines). | Pokud nepodporované Kontrola předpokladů selže.
**Architektura operačního systému hosta** | 64 bitů | Pokud nepodporované Kontrola předpokladů selže.
**Velikost disku s operačním systémem** | Až 2 048 GB | Pokud nepodporované Kontrola předpokladů selže.
**Počet disků s operačním systémem** | 1 | Pokud nepodporované Kontrola předpokladů selže.
**Počet datových disků** | Počet je 64 nebo méně Pokud replikujete *virtuálních počítačů VMware do Azure*. Počet je 16 nebo méně Pokud replikujete *virtuálních počítačů Hyper-V do Azure*. | Pokud nepodporované Kontrola předpokladů selže.
**Velikost datového virtuálního pevného disku** | Až 4095 GB | Pokud nepodporované Kontrola předpokladů selže.
**Síťové adaptéry** | Několik adaptérů jsou podporovány. |
**Sdílený virtuální pevný disk** | Nepodporuje se. | Pokud nepodporované Kontrola předpokladů selže.
**Disk FC** | Nepodporuje se. | Pokud nepodporované Kontrola předpokladů selže.
**Formát pevného disku** | VHD <br/><br/> VHDX | Přestože Azure v současné době formát VHDX nepodporuje, Site Recovery při převzetí služeb při selhání do Azure automaticky převede formát VHDX na VHD. Pokud žádnou zpět na místní, virtuální počítače nadále používat formát VHDX.
**BitLocker** | Nepodporuje se. | Dříve než povolíte replikaci pro počítač, musí se zakázat nástroj BitLocker.
**Název virtuálního počítače** | Od 1 do 63 znaků.<br/><br/> Pouze písmena, číslice a pomlčky.<br/><br/> Název počítače musí začínat a končit písmenem nebo číslicí. | Aktualizujte hodnotu v vlastnosti počítače ve službě Site Recovery.
**Typ virtuálního počítače** | Generace 1.<br/><br/> Generace 2 – Windows. | Virtuální počítače generace 2 se typ disku operačního systému basic (která zahrnuje jednu nebo dvě datové svazky naformátované jako VHDX) a menší než 300 GB místa na disku jsou podporovány.<br></br>Virtuální počítače s Linuxem generace 2 nejsou podporované. Další informace najdete v tématu [zotavení po havárii do Azure rozšířené](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="vault-tasks"></a>Úložiště úlohy

**Akce** | **Podporuje se**
--- | ---
Přesunutí trezoru rámci skupiny prostředků<br/><br/> V rámci a napříč odběrů | Ne
Přesunout úložiště, sítě, virtuální počítače Azure mezi skupinami prostředků<br/><br/> V rámci a napříč odběrů | Ne


## <a name="mobility-service"></a>Služba mobility

**Název** | **Popis** | **nejnovější verzi** | **Podrobnosti**
--- | --- | --- | --- | ---
**Azure Site Recovery sjednocený instalační program** | Koordinuje komunikaci mezi místními servery VMware a Azure <br/><br/> Nainstalovat na místní servery VMware | 9.12.4653.1 (k dispozici z portálu) | [Nejnovější funkce a opravy](https://aka.ms/latest_asr_updates)
**Služba mobility** | Koordinuje replikaci mezi místními VMware servery/fyzické servery a Azure nebo sekundární lokality<br/><br/> Nainstalovat na virtuální počítač VMware nebo fyzických serverů, které chcete replikovat | 9.12.4653.1 (k dispozici z portálu) | [Nejnovější funkce a opravy](https://aka.ms/latest_asr_updates)


## <a name="next-steps"></a>Další postup
[Zjistěte, jak](tutorial-prepare-azure.md) Příprava Azure pro zotavení po havárii virtuálních počítačů VMware.

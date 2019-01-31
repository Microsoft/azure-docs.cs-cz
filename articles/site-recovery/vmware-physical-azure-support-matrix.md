---
title: Matice podpory pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Shrnuje podporované operační systémy a komponenty pro zotavení po havárii virtuálních počítačů VMware a fyzické servery do Azure pomocí Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: raynew
ms.openlocfilehash: b790cbf6c3c9ec04dfdbf0299e337d32e54e9708
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299756"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matice podpory pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure

Tento článek shrnuje podporované komponenty a nastavení pro zotavení po havárii virtuálních počítačů VMware do Azure s použitím [Azure Site Recovery](site-recovery-overview.md).

Chcete-li začít používat Azure Site Recovery s nejjednodušší scénář nasazení, navštivte naši [kurzy](tutorial-prepare-azure.md). Další informace o architektuře Azure Site Recovery [tady](vmware-azure-architecture.md).

## <a name="replication-scenario"></a>Scénáře replikace

**Scénář** | **Podrobnosti**
--- | ---
Virtuální počítače VMware | Replikace místních virtuálních počítačů VMware do Azure. Můžete nasadit tento scénář na webu Azure Portal nebo pomocí [Powershellu](vmware-azure-disaster-recovery-powershell.md).
Fyzické servery | Replikace místních fyzických serverů Windows/Linux do Azure. Můžete nasadit tento scénář na webu Azure Portal.

## <a name="on-premises-virtualization-servers"></a>Na místních serverech virtualizace

**Server** | **Požadavky** | **Podrobnosti**
--- | --- | ---
VMware | vCenter Server 6.7 6.5, 6.0 nebo 5.5 nebo vSphere 6.7, 6.5, 6.0 nebo 5.5 | Doporučujeme vám použít vCenter server.<br/><br/> Doporučujeme vám, že hostitelé vSphere a serverů vCenter jsou umístěny ve stejné síti jako procesový server. Ve výchozím nastavení součásti procesu serveru běží na konfiguračním serveru, bude v síti, ve kterém nastavíte konfigurační server, pokud nastavíte vyhrazené procesní server.
Fyzické | neuvedeno

## <a name="site-recovery-configuration-server"></a>Site Recovery konfiguračního serveru

Konfigurační server je místní počítač, na kterém běží součásti Site Recovery, včetně konfigurační server, procesový server a hlavní cílový server. Pro replikaci VMware nastavíte konfigurační server se všemi požadavky, pomocí šablony OVF k vytvoření virtuálního počítače VMware. Pro replikaci fyzických serverů můžete nastavit konfiguraci počítače serveru ručně.

**Komponenta** | **Požadavky**
--- |---
Procesorová jádra | 8
Paměť RAM | 16 GB
Počet disků | 3 disky<br/><br/> Disky zahrnují disku operačního systému, disk mezipaměti procesového serveru a jednotky pro uchovávání dat pro navrácení služeb po obnovení.
Volné místo na disku | 600 GB místa potřebného pro mezipaměť procesového serveru.
Volné místo na disku | 600 GB místa potřebného pro jednotka pro uchování.
Operační systém  | Windows Server 2012 R2 nebo Windows Server 2016 |
Národní prostředí operačního systému | Angličtina (en-us)
PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") by měly být nainstalovány.
Role Windows Serveru | Nepovolí: <br/> – Active Directory Domain Services <br/>– Internet Information Service <br/> – Hyper-V |
Zásady skupiny| Nepovolí: <br/> -Zabránit přístupu do příkazového řádku. <br/> -Zabránit přístupu k nástrojům pro úpravu registru. <br/> – Logika důvěryhodnosti pro přiložené soubory. <br/> -Zapnutí provádění skriptů. <br/> [Další informace](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Ujistěte se, že jste:<br/><br/> – Dříve existující výchozí web není nutné. <br/> -Aktivovat [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> -Aktivovat [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) nastavení  <br/> -Nemají dříve existující web nebo aplikace naslouchá na portu 443<br/>
Typ NIC | VMXNET3 (Pokud je nasazená jako virtuální počítač VMware)
Typ IP adresy | Statická
Porty | 443 pro Orchestrace řídicího kanálu)<br/>9443 používá pro přenos dat

## <a name="replicated-machines"></a>Replikované počítače

Site Recovery podporuje jakoukoli úlohu spuštěnou na podporovaném počítači replikace.

**Komponenta** | **Podrobnosti**
--- | ---
Nastavení počítače | Počítače, které se replikují do Azure, musí splňovat [požadavky služby Azure](#azure-vm-requirements).
Operační systém Windows | 64bitová verze Windows serveru 2016 (jádro serveru, Server s desktopovým prostředím), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 s na minimálně SP1. </br></br>  [Windows Server 2008 s na minimálně SP2 – 32bitové a 64bitové](migrate-tutorial-windows-server-2008.md) (pouze migrace). </br></br> Windows 2016 Nano Server není podporován.
Operační systém platformy Linux | Red Hat Enterprise Linux: 5.2 na 5,11<b>\*\*</b>, 6.1 k 6.10<b>\*\*</b>, 7.0 pro 7.6 <br/><br/>CentOS: 5.2 na 5,11<b>\*\*</b>, 6.1 k 6.10<b>\*\*</b>, 7.0 pro 7.6 <br/><br/>Server se systémem Ubuntu 14.04 LTS[ (podporované verze jádra)](#ubuntu-kernel-versions)<br/><br/>Server se systémem Ubuntu 16.04 LTS[ (podporované verze jádra)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8[ (podporované verze jádra)](#debian-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 12 SP1, SP2 SP3 [ (podporované verze jádra)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 11 SP3<b>\*\*</b>, operačním systémem SUSE Linux Enterprise Server 11 SP4 * </br></br>Oracle Linux 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, Red Hat kompatibilní jádra nebo nedělitelné Enterprise jádra verze 3 (UEK3) <br/><br/></br>-Upgrade replikované počítače z operačního systému SUSE Linux Enterprise Server 11 SP3 SP4 není podporován. Pokud chcete upgradovat, zakažte replikaci a povolte znovu po dokončení upgradu.</br></br> - [Další informace](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) informace o podpoře pro Linux a opensourcové technologie v Azure. Site Recovery orchestruje převzetí služeb při selhání spuštění servery s Linuxem v Azure. Linux dodavatelů ale může omezit podporu pouze verze distribuce, které se ještě nedostaly ukončenou životností.<br/><br/> – V Linuxových distribucích jsou podporovány pouze uložených jader, které jsou součástí vydání/aktualizace podverze distribuce.<br/><br/> -Upgradu chráněné počítače napříč hlavní Linux není podporována verze distribuce. K upgradu, zakažte replikaci, upgradovat operační systém a pak replikaci zase povolte.<br/><br/> -Servery se systémem Red Hat Enterprise Linux 5,11 5.2 nebo CentOS 5.2 5,11 by měly mít [komponenty služby Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) nainstalována počítačů pro spuštění v Azure.

### <a name="ubuntu-kernel-versions"></a>Ubuntu verze jádra


**Podporované verze** | **Verze služby Azure Site Recovery Mobility Service** | **Verze jádra** |
--- | --- | --- |
14.04 LTS | [9.22][9.22 UR] | 3.13.0-24-Generic k 3.13.0-164-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-140-generic,<br/>4.15.0-1023-Azure k 4.15.0-1036-azure |
14.04 LTS | [9.21][9.21 UR] | 3.13.0-24-Generic k 3.13.0-163-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-140-generic,<br/>4.15.0-1023-Azure k 4.15.0-1035-azure |
14.04 LTS | [9.20][9.20 UR] | 3.13.0-24-Generic k 3.13.0-153-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-138-generic,<br/>4.15.0-1023-Azure k 4.15.0-1025-azure |
14.04 LTS | [9.19][9.19 UR] | 3.13.0-24-Generic k 3.13.0-153-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-131-generic |
|||
16.04 LTS | [9.22][9.22 UR] | 4.4.0-21-Generic k 4.4.0-140-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-45-generic,<br/>4.15.0-13-Generic k 4.15.0-43-generic<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1018-azure <br/>4.15.0-1012-Azure k 4.15.0-1036-azure|
16.04 LTS | [9.21][9.21 UR] | 4.4.0-21-Generic k 4.4.0-140-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-45-generic,<br/>4.15.0-13-Generic k 4.15.0-42-generic<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1018-azure <br/>4.15.0-1012-Azure k 4.15.0-1035-azure|
16.04 LTS | [9.20][9.20 UR] | 4.4.0-21-Generic k 4.4.0-138-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-45-generic,<br/>4.15.0-13-Generic k 4.15.0-38-generic<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1018-azure <br/>4.15.0-1012-Azure k 4.15.0-1025-azure|
16.04 LTS | [9.19][9.19 UR] | 4.4.0-21-Generic k 4.4.0-131-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-45-generic,<br/>4.15.0-13-Generic k 4.15.0-30-generic<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1018-azure <br/>4.15.0-1012-Azure k 4.15.0-1019-azure|
16.04 LTS | [9.18][9.18 UR] | 4.4.0-21-Generic k 4.4.0-130-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-45-generic |

### <a name="debian-kernel-versions"></a>Verze jádra debian


**Podporované verze** | **Verze služby Azure Site Recovery Mobility Service** | **Verze jádra** |
--- | --- | --- |
Debian 7 | [9.18][9.18 UR],[9.19][9.19 UR],[9.20][9.20 UR],[9.21][9.21 UR] | 3.2.0-4-amd64 k 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.20][9.20 UR],[9.21][9.21 UR] | 3.16.0-4-amd64 k 3.16.0-7-amd64 4.9.0-0.bpo.4-amd64 k 4.9.0-0.bpo.8-amd64 |
Debian 8 | [9.19][9.19 UR] | 3.16.0-4-amd64 k 3.16.0-6-amd64 4.9.0-0.bpo.4-amd64 k 4.9.0-0.bpo.7-amd64 |
Debian 8 | [9.18][9.18 UR] | 3.16.0-4-amd64 k 3.16.0-6-amd64 4.9.0-0.bpo.4-amd64 k 4.9.0-0.bpo.6-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>Podporované verze jádra operačního systému SUSE Linux Enterprise Server 12

**Vydání verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | [9.21][9.21 UR] | SP1 3.12.49-11-default k 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default k 3.12.74-60.64.107-default</br></br> 4.4.21-69-default SP2 do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default k 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default k 4.4.156-94.72-default |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | [9.20][9.20 UR] | SP1 3.12.49-11-default k 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default k 3.12.74-60.64.107-default</br></br> 4.4.21-69-default SP2 do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default k 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default k 4.4.156-94.64-default |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | [9.19][9.19 UR] | SP1 3.12.49-11-default k 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default k 3.12.74-60.64.96-default</br></br> 4.4.21-69-default SP2 do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default k 4.4.121-92.85-default</br></br>SP3 4.4.73-5-default k 4.4.140-94.42-default |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | [9.18][9.18 UR] | SP1 3.12.49-11-default k 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default k 3.12.74-60.64.96-default</br></br> 4.4.21-69-default SP2 do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default k 4.4.121-92.85-default</br></br>SP3 4.4.73-5-default k 4.4.138-94.39-default |

## <a name="linux-file-systemsguest-storage"></a>Úložiště systémů/hostů soubor Linuxu

**Komponenta** | **Podporuje se**
--- | ---
Systémy souborů | ext3, ext4, XFS
Správce svazků | Před [9.20 verze](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), <br/> 1. LVM2 je podporována. <br/> 2. LVM je podporována pouze datových disků. <br/> 3. Virtuální počítače Azure mají pouze jedním diskem operačního systému.<br/><br/>Z [9.20 verze](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) LVM a LVM2 a vyšší, podporují.
Paravirtualizovanými úložných zařízení | Zařízení exportovaná paravirtualizovanými ovladači se nepodporují.
Blokovat fronty více vstupně-výstupních operací zařízení | Nepodporuje se.
Fyzické servery s řadič úložiště HP CCISS | Nepodporuje se.
Zásady vytváření názvů zařízení/přípojný bod | Název zařízení nebo název přípojného bodu musí být jedinečné. Zajistěte, aby velká a malá písmena názvy žádné dvě zařízení a přípojné body. </br> Příklad: Pojmenování dvě zařízení stejný virtuální počítač jako *zařízení1* a *zařízení1* není povolený.
Adresáře | Před [9.20 verze](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), <br/> 1. V následujících adresářích (Pokud nastavený jako samostatné oddíly /-systémy souborů) musí být na stejném disku operačního systému na zdrojovém serveru: / (root), Boot, / USR, /usr/local, /var, / etc.</br>2. Boot by měl být na disku oddílu a nesmí být svazku LVM.<br/><br/> Z [9.20 verze](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) a vyšší, nad omezení neplatí. Boot objemu LVM napříč více než jeden disky se nepodporuje.
Spouštěcí adresář | Více spouštěcí disky na virtuálním počítači se nepodporuje. <br/><br/> Počítač bez spouštěcí disk nejde chránit.

Požadavky na místo na bezplatné | 2 GB na oddíl/root <br/><br/> 250 MB v instalační složce XFSv5 | XFSv5 v systémech souborů s XFS, jako je například metadata kontrolního součtu, jsou však podporovány ze služby Mobility verze dále 9.10. Pomocí nástroje xfs_info tzv XFS pro oddíl. Pokud ftype je nastavena na hodnotu 1, funkce XFSv5 jsou používána.

## <a name="vmdisk-management"></a>Správa virtuálních počítačů/disků

**Akce** | **Podrobnosti**
--- | ---
Změna velikosti disku na replikovaný virtuální počítač | Podporuje se.
Přidání disku na replikovaný virtuální počítač | Zakažte replikaci virtuálního počítače, přidejte disk a potom znovu povolit replikaci. Přidání disku na virtuálním počítači replikaci se momentálně nepodporuje.

## <a name="network"></a>Síť

**Komponenta** | **Podporuje se**
--- | ---
Seskupování síťových adaptérů sítě hostitele | Pro virtuální počítače VMware podporován. <br/><br/>Není podporováno pro replikaci fyzických počítačů.
Hostitelské sítě VLAN | Ano.
Hostitelské sítě IPv4 | Ano.
Hostitelské síti protokolu IPv6 | Ne.
Seskupování síťových adaptérů sítě hosta/server | Ne.
Sítě typu Host/server IPv4 | Ano.
Sítě typu Host/server IPv6 | Ne.
Hosta/sítě statická IP adresa serveru (Windows) | Ano.
Hosta/sítě statická IP adresa serveru (Linux) | Ano. <br/><br/>Virtuální počítače jsou nakonfigurované pro používání protokolu DHCP na navrácení služeb po obnovení.
Několik síťových adaptérů sítě hosta/server | Ano.


## <a name="azure-vm-network-after-failover"></a>Síť virtuálních počítačů Azure (po převzetí služeb při selhání)

**Komponenta** | **Podporuje se**
--- | ---
Azure ExpressRoute | Ano
Interní nástroj pro vyrovnávání zatížení | Ano
ELB | Ano
Azure Traffic Manager | Ano
S více síťovými Kartami | Ano
Vyhrazená IP adresa | Ano
IPv4 | Ano
Zachovat Zdrojová IP adresa | Ano
Koncové body služby virtuální sítě Azure<br/> (bez brány firewall služby Azure Storage) | Ano
Akcelerované síťové služby | Ne

## <a name="storage"></a>Storage
**Komponenta** | **Podporuje se**
--- | ---
Dynamický disk | Operace systémový disk musí být základní disk. <br/><br/>Datové disky můžou být dynamických disků
Host NFS | Ano pro VMware<br/><br/> Ne pro fyzické servery
Host SAN (iSCSI/FC) | Ano
Host vSAN | Ano pro VMware<br/><br/> Není k dispozici pro fyzické servery
Hostitel multipath (MPIO Multipath) | Ano, testováním s využitím Microsoft DSM EMC PowerPath 5.7 SP4, EMC PowerPath DSM pro CLARiiON
Svazky virtuálního hostitele (VVols) | Ano pro VMware<br/><br/> Není k dispozici pro fyzické servery
Host/server VMDK | Ano
Host/server rozhraním EFI/UEFI| Partial (migrace dat do Azure pro Windows Server 2012 a novější) <br/><br/> Další informace v poznámce na konci v tabulce
Disk sdíleného clusteru hosta/server | Ne
Šifrované disk Host/server | Ne
Host/server systému souborů NFS | Ne
Host/serveru protokolu SMB 3.0 | Ne
Host/server RDM | Ano<br/><br/> Není k dispozici pro fyzické servery
Disk Host/serveru > 1 TB | Ano<br/><br/>Až 4 095 GB<br/><br/> Disk musí být větší než 1 024 MB.
Host/server na disku s velikost fyzického sektoru 4K logické a 4 kB | Ano
Disk s 4 kB logického typu Host/server a velikost fyzického sektoru 512 bajtů | Ano
Host/server svazek s prokládané disk > 4 TB <br/><br/>Správa logických svazků (LVM)| Ano
Host/server – prostory úložiště | Ne
Disk horké přidávat nebo odebírat hosta/server | Ne
Host/server - vyloučení disku | Ano
Funkce multipath hosta/server (MPIO) | Ne

> [!NOTE]
> Rozhraní UEFI spouštění virtuálních počítačů VMware s Windows serverem 2012 nebo novějším se dají migrovat do Azure. Platí následující omezení:

> - Je podporována pouze migrace do Azure. Navrácení služeb po obnovení do místní lokality VMware se nepodporuje.
> - Server by neměl mít více než čtyři oddíly na disku s operačním systémem.
> - Vyžaduje verzi služby Mobility 9.13 nebo vyšší.

## <a name="azure-storage"></a>Úložiště Azure

**Komponenta** | **Podporuje se**
--- | ---
(Locally redundant storage) Místně redundantní úložiště | Ano
Geograficky redundantní úložiště | Ano
Geograficky redundantní úložiště jen pro čtení | Ano
Úložiště Cool | Ne
Horkého úložiště| Ne
Objekty blob bloku | Ne
Šifrování v klidovém stavu (šifrování služby Storage)| Ano
Premium Storage | Ano
Služba import/export | Ne
Pro virtuální sítě na cílový účet úložiště mezipaměti nebo úložiště (sloužící k ukládání dat replikace) nakonfigurované brány firewall služby Azure Storage | Ne
Účty úložiště pro obecné účely v2 (vrstvy hot i cool) | Ne

## <a name="azure-compute"></a>Výpočetní prostředky Azure

**Funkce** | **Podporuje se**
--- | ---
Skupiny dostupnosti | Ano
HUB | Ano
Managed Disks | Ano

## <a name="azure-vm-requirements"></a>Požadavky virtuálních počítačů Azure

Místní virtuální počítače, které se replikují do Azure, musí splňovat požadavky virtuálního počítače Azure uvedené v této tabulce. Při spuštění kontroly požadavků Site Recovery se nezdaří, pokud nejsou splněny některé požadavky.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Hostovaný operační systém | Ověřte [podporované operační systémy](#replicated-machines) pro replikované počítače. | Kontrola selže, pokud není podporován.
Architektura operačního systému hosta | 64-bit. | Kontrola selže, pokud není podporován.
Velikost disku operačního systému | Až 2 048 GB. | Kontrola selže, pokud není podporován.
Počet disků operačního systému | 1 | Kontrola selže, pokud není podporován.
Počet datových disků | 64 nebo méně. | Kontrola selže, pokud není podporován.
Velikost datového disku | Až 4 095 GB | Kontrola selže, pokud není podporován.
Síťové adaptéry | Podporuje se více adaptérů. |
Sdílený virtuální pevný disk | Nepodporuje se. | Kontrola selže, pokud není podporován.
FC disk | Nepodporuje se. | Kontrola selže, pokud není podporován.
BitLocker | Nepodporuje se. | Dříve než povolíte replikaci pro počítač, musí se zakázat nástroj BitLocker. |
název virtuálního počítače | Od 1 do 63 znaků.<br/><br/> Pouze písmena, číslice a pomlčky.<br/><br/> Název počítače musí začínat a končit písmenem nebo číslicí. |  Aktualizujte hodnotu v vlastnosti počítače ve službě Site Recovery.


## <a name="vault-tasks"></a>Trezor úlohy

**Akce** | **Podporuje se**
--- | ---
Přesun trezoru mezi skupinami prostředků<br/><br/> V rámci a napříč předplatnými | Ne
Přesunout úložiště, sítě, virtuální počítače Azure mezi skupinami prostředků<br/><br/> V rámci a napříč předplatnými | Ne


## <a name="download-latest-azure-site-recovery-components"></a>Stáhněte si nejnovější komponenty Azure Site Recovery

**Název** | **Popis** | **Pokyny ke stažení nejnovější verze**
--- | --- | --- | --- | ---
Konfigurační server | Koordinuje komunikaci mezi servery VMware v místním prostředím a Azure <br/><br/> Nainstalovat na místní servery VMware | Pro novou instalaci, klikněte na tlačítko [tady](vmware-azure-deploy-configuration-server.md). Pro upgrade existující komponentu na nejnovější verzi, klikněte na tlačítko [tady](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server).
Procesový server|Obvykle se instaluje na konfigurační server. Přijímá data replikace; optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování. a odesílá je do služby Azure Storage. Jak vaše nasazení poroste, můžete přidat další, samostatné procesní servery pro zpracování větší objemy přenosů replikace.| Pro novou instalaci, klikněte na tlačítko [tady](vmware-azure-set-up-process-server-scale.md). Pro upgrade existující komponentu na nejnovější verzi, klikněte na tlačítko [tady](vmware-azure-manage-process-server.md#upgrade-a-process-server).
Služba mobility | Koordinuje replikaci mezi servery pro/fyzické servery VMware v místním a Azure nebo sekundární lokality<br/><br/> Nainstalovat na virtuální počítač VMware nebo fyzické servery, které chcete replikovat | Pro novou instalaci, klikněte na tlačítko [tady](vmware-azure-install-mobility-service.md). Pro upgrade existující komponentu na nejnovější verzi, klikněte na tlačítko [tady](vmware-physical-mobility-service-overview.md#update-the-mobility-service).

Další informace o nejnovější funkce a opravy, klikněte na tlačítko [tady](https://aka.ms/ASR_latest_release_notes).


## <a name="next-steps"></a>Další postup
[Zjistěte, jak](tutorial-prepare-azure.md) Příprava Azure pro zotavení po havárii virtuálních počítačů VMware.

[9.22 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery

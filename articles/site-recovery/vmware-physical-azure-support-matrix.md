---
title: Matice podpory pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Shrnuje podporu pro zotavení po havárii virtuálních počítačů VMware a fyzické servery do Azure pomocí Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/07/2019
ms.author: raynew
ms.openlocfilehash: 1d54f2847f7c37631374653bf291aadf091f3328
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805956"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matice podpory pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure

Tento článek shrnuje podporované součástmi a nastavení pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure s využitím [Azure Site Recovery](site-recovery-overview.md).

- [Další informace](vmware-azure-architecture.md) o architektuře pro zotavení po havárii serveru virtuálního počítače VMware/fyzických prostředků.
- Postupujte podle našich [kurzy](tutorial-prepare-azure.md) můžete vyzkoušet na zotavení po havárii.

## <a name="deployment-scenarios"></a>Scénáře nasazení

**Scénář** | **Podrobnosti**
--- | ---
Zotavení po havárii virtuálních počítačů VMware | Replikace místních virtuálních počítačů VMware do Azure. Můžete nasadit tento scénář na webu Azure Portal nebo pomocí [Powershellu](vmware-azure-disaster-recovery-powershell.md).
Zotavení po havárii fyzických serverů | Replikace místních fyzických serverů Windows/Linux do Azure. Můžete nasadit tento scénář na webu Azure Portal.

## <a name="on-premises-virtualization-servers"></a>Na místních serverech virtualizace

**Server** | **Požadavky** | **Podrobnosti**
--- | --- | ---
vCenter Server | Verze 6.7, 6.5, 6.0 nebo 5.5 | Doporučujeme použít vCenter server ve vašem nasazení zotavení po havárii.
hostitelé vSphere | Verze 6.7, 6.5, 6.0 nebo 5.5 | Doporučujeme vám, že hostitelé vSphere a serverů vCenter jsou umístěny ve stejné síti jako procesový server. Ve výchozím nastavení procesový server běží na konfiguračním serveru. [Další informace](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Site Recovery konfiguračního serveru

Konfigurační server je místní počítač, na kterém běží součásti Site Recovery, včetně konfigurační server, procesový server a hlavní cílový server.

- Pro virtuální počítače VMware nastavíte konfigurační server stažením šablony OVF k vytvoření virtuálního počítače VMware.
- Pro fyzické servery nastavení konfigurace počítače serveru ručně.

**Komponenta** | **Požadavky**
--- |---
Procesorová jádra | 8
Paměť RAM | 16 GB
Počet disků | 3 disky<br/><br/> Disky zahrnují disku operačního systému, disk mezipaměti procesového serveru a jednotky pro uchovávání dat pro navrácení služeb po obnovení.
Volné místo na disku | 600 GB volného místa pro mezipaměť procesového serveru.
Volné místo na disku | 600 GB volného místa pro jednotka pro uchování.
Operační systém  | Windows Server 2012 R2 nebo Windows Server 2016 s desktopovým prostředím |
Národní prostředí operačního systému | Angličtina (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Nevyžaduje se pro verzi konfiguračního serveru [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) nebo novější. 
Role Windows Serveru | Nepovolí Active Directory Domain Services; Internetová informační služba (IIS) nebo technologie Hyper-V. 
Zásady skupiny| -Zabránit přístupu do příkazového řádku. <br/> -Zabránit přístupu k nástrojům pro úpravu registru. <br/> – Logika důvěryhodnosti pro přiložené soubory. <br/> -Zapnutí provádění skriptů. <br/> - [Víc se uč](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Ujistěte se, že jste:<br/><br/> – Dříve existující výchozí web není nutné. <br/> -Aktivovat [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> -Aktivovat [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) nastavení  <br/> -Nemají dříve existující web nebo aplikace naslouchá na portu 443<br/>
Typ NIC | VMXNET3 (Pokud je nasazená jako virtuální počítač VMware)
Typ IP adresy | Static
Porty | 443 pro Orchestrace řídicího kanálu<br/>9443 pro přenos dat

## <a name="replicated-machines"></a>Replikované počítače

Site Recovery podporuje jakoukoli úlohu spuštěnou na podporovaném počítači replikace.

**Komponenta** | **Podrobnosti**
--- | ---
Nastavení počítače | Počítače, které se replikují do Azure, musí splňovat [požadavky služby Azure](#azure-vm-requirements).
Úlohám počítače | Site Recovery podporuje jakoukoli úlohu spuštěnou na podporovaném počítači replikace. [Další informace](https://aka.ms/asr_workload).
Windows | – Windows Server 2019 (podporováno od [34 kumulativní aktualizace](https://support.microsoft.com/help/4490016) (verze 9.22 služby Mobility) a vyšší.<br/> – Windows Server 2016 (jádro serveru 64-bit, Server s desktopovým prostředím)<br/> - Windows Server 2012 R2, Windows Server 2012<br/> – Windows Server 2008 R2 s na minimálně SP1.<br/> – Windows Server 2008 64 a 32-bit s na minimálně SP2]. Pro migraci pouze podporováno. [Další informace](migrate-tutorial-windows-server-2008.md).<br/> – Windows 10, Windows 8.1, Windows 8, Windows 7 64-bit (podporováno od [36 kumulativní aktualizace](https://support.microsoft.com/help/4503156) (verze 9.22 služby Mobility a vyšší). Windows 7 RTM se nepodporuje. 
Linux | Je podporován pouze 64bitové verzi systému. 32bitový systém se nepodporuje.<br/><br/>Každý server Linux musí mít [komponenty služby Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) nainstalované. Je vyžadován ke spuštění po testu převzetí služeb při selhání/převzetí služeb serveru v Azure. Pokud služby LIS součásti nechybí, ujistěte se, k instalaci [součásti](https://www.microsoft.com/download/details.aspx?id=55106) než povolíte replikaci pro počítače ke spuštění v Azure. <br/><br/> Site Recovery orchestruje převzetí služeb při selhání spuštění servery s Linuxem v Azure. Linux dodavatelů ale může omezit podporu pouze verze distribuce, které se ještě nedostaly ukončenou životností.<br/><br/> V Linuxových distribucích jsou podporovány pouze uložených jader, které jsou součástí vydání/aktualizace podverze distribuce.<br/><br/> Mezi hlavní Linux není podporována verze distribuční upgrade chráněných počítačů. K upgradu, zakažte replikaci, upgradovat operační systém a pak replikaci zase povolte.<br/><br/> [Další informace](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) informace o podpoře pro Linux a open source technologiemi v Azure.
Linux Red Hat Enterprise | 5.2 na 5,11</b><br/> 6.1 k 6.10</b><br/> 7.0 pro 7.6<br/> <br/> Servery se systémem Red Hat Enterprise Linux 5.2 5,11 & 6.1 6.10 nemají [komponenty služby Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) předinstalovaným. Nezapomeňte nainstalovat [součásti](https://www.microsoft.com/download/details.aspx?id=55106) než povolíte replikaci pro počítače ke spuštění v Azure.
Linux: CentOS | 5.2 na 5,11</b><br/> 6.1 k 6.10</b><br/> 7.0 pro 7.6<br/> <br/> Servery se systémem CentOS 5.2 5,11 & 6.1 6.10 nemají [komponenty služby Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) předinstalovaným. Nezapomeňte nainstalovat [součásti](https://www.microsoft.com/download/details.aspx?id=55106) než povolíte replikaci pro počítače ke spuštění v Azure.
Ubuntu | Server se systémem Ubuntu 14.04 LTS [(zkontrolujte podporované verze jádra)](#ubuntu-kernel-versions)<br/><br/>Server se systémem Ubuntu 16.04 LTS [(zkontrolujte podporované verze jádra)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(zkontrolujte podporované verze jádra)](#debian-kernel-versions)
SUSE Linux | Systém SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(zkontrolujte podporované verze jádra)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> Upgrade replikované počítače z operačního systému SUSE Linux Enterprise Server 11 SP3 SP4 není podporován. Pokud chcete upgradovat, zakažte replikaci a znovu povolit po upgradu.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6<br/><br/> Red Hat kompatibilní jádra nebo nedělitelné Enterprise jádra verze 3, 4 a 5 (UEK3, UEK4 UEK5.) 


### <a name="ubuntu-kernel-versions"></a>Ubuntu verze jádra


**Podporované verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
14.04 LTS | [9.26][9.26 UR]| 3.13.0-24-Generic k 3.13.0-170-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-148-generic,<br/>4.15.0-1023-Azure k 4.15.0-1045-azure |
14.04 LTS | [9.25][9.25 UR]  | 3.13.0-24-Generic k 3.13.0-169-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-146-generic,<br/>4.15.0-1023-Azure k 4.15.0-1042-azure |
14.04 LTS | [9.24][9.24 UR] | 3.13.0-24-Generic k 3.13.0-167-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-143-generic,<br/>4.15.0-1023-Azure k 4.15.0-1040-azure |
14.04 LTS | [9.23][9.23 UR] | 3.13.0-24-Generic k 3.13.0-165-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-142-generic,<br/>4.15.0-1023-Azure k 4.15.0-1037-azure |
|||
16.04 LTS | [9.26][9.26 UR] | 4.4.0-21-Generic k 4.4.0-148-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-45-generic,<br/>4.15.0-13-Generic k 4.15.0-50-generic<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1018-azure <br/>4.15.0-1012-Azure k 4.15.0-1045-azure|
16.04 LTS | [9.25][9.25 UR] | 4.4.0-21-Generic k 4.4.0-146-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-45-generic,<br/>4.15.0-13-Generic k 4.15.0-48-generic<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1018-azure <br/>4.15.0-1012-Azure k 4.15.0-1042-azure|
16.04 LTS | [9.24][9.24 UR] | 4.4.0-21-Generic k 4.4.0-143-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-45-generic,<br/>4.15.0-13-Generic k 4.15.0-46-generic<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1018-azure <br/>4.15.0-1012-Azure k 4.15.0-1040-azure|
16.04 LTS | [9.23][9.23 UR] | 4.4.0-21-Generic k 4.4.0-142-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-45-generic,<br/>4.15.0-13-Generic k 4.15.0-45-generic<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1018-azure <br/>4.15.0-1012-Azure k 4.15.0-1037-azure|

### <a name="debian-kernel-versions"></a>Verze jádra debian


**Podporované verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
Debian 7 | [9.21][9.21 UR], [9.22][9.22 UR],[9.23][9.23 UR], [9,24][9.24 UR]| 3.2.0-4-amd64 k 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.25][9.25 UR] | 3.16.0-4-amd64 k 3.16.0-8-amd64 4.9.0-0.bpo.4-amd64 k 4.9.0-0.bpo.8-amd64 |
Debian 8 | [9.22][9.22 UR],[9.23][9.23 UR], [9,24][9.24 UR] | 3.16.0-4-amd64 k 3.16.0-7-amd64 4.9.0-0.bpo.4-amd64 k 4.9.0-0.bpo.8-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>Podporované verze jádra operačního systému SUSE Linux Enterprise Server 12

**Vydání verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (s aktualizací SP1, SP2, SP3, SP4) | [9.26][9.26 UR] | SP1 3.12.49-11-default k 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default k 3.12.74-60.64.110-default</br></br> 4\.4.21-69-default SP2 do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default k 4.4.121-92.109-default</br></br>SP3 4.4.73-5-default k 4.4.178-94.91-default</br></br>SP3 4.4.138-4.7-azure k 4.4.178-4.28-azure</br></br>SP4 4.12.14-94.41-default k 4.12.14-95.16-default</br>SP4 4.12.14-6.3-azure k 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (s aktualizací SP1, SP2, SP3, SP4) | [9.25][9.25 UR] | SP1 3.12.49-11-default k 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default k 3.12.74-60.64.107-default</br></br> 4\.4.21-69-default SP2 do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default k 4.4.121-92.104-default</br></br>SP3 4.4.73-5-default k 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-azure k 4.4.176-4.25-azure</br></br>SP4 4.12.14-94.41-default k 4.12.14-95.13-default</br>SP4 4.12.14-6.3-azure k 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (s aktualizací SP1, SP2, SP3, SP4) | [9.24][9.24 UR] | SP1 3.12.49-11-default k 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default k 3.12.74-60.64.107-default</br></br> 4\.4.21-69-default SP2 do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default k 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default k 4.4.175-94.79-default</br></br>SP4 4.12.14-94.41-default k 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (s aktualizací SP1, SP2, SP3, SP4) | [9.23][9.23 UR] | SP1 3.12.49-11-default k 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default k 3.12.74-60.64.107-default</br></br> 4\.4.21-69-default SP2 do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default k 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default k 4.4.162-94.69-default</br></br>SP4 4.12.14-94.41-default k 4.12.14-95.6-default |


## <a name="linux-file-systemsguest-storage"></a>Úložiště systémů/hostů soubor Linuxu

**Komponenta** | **Podporuje se**
--- | ---
Systémy souborů | ext3, ext4, XFS
Správce svazků | -LVM je podporována.<br/> - / podporuje spuštění na LVM od [31 kumulativní aktualizace](https://support.microsoft.com/help/4478871/) (verze 9,20 služby Mobility) a vyšší. Nepodporuje se ve starších verzí služby Mobility.<br/> -Více disků s operačním systémem nepodporují.
Paravirtualizovanými úložných zařízení | Zařízení exportovaná paravirtualizovanými ovladači se nepodporují.
Blokovat fronty více vstupně-výstupních operací zařízení | Nepodporuje se.
Fyzické servery s řadič úložiště HP CCISS | Nepodporuje se.
Zásady vytváření názvů zařízení/přípojný bod | Název zařízení nebo název přípojného bodu musí být jedinečné.<br/> Zajistěte, aby velká a malá písmena názvy žádné dvě zařízení a přípojné body. Například názvy zařízení pro stejný virtuální počítač jako *zařízení1* a *zařízení1* se nepodporuje.
Adresáře | Pokud používáte verzi služby Mobility starší než verze 9,20 (vydané v [31 kumulativní aktualizace](https://support.microsoft.com/help/4478871/)), pak platí tato omezení:<br/><br/> -Tyto adresáře (Pokud je nastavení jako samostatné oddíly /-systémy souborů) musí být na stejném disku operačního systému na zdrojovém serveru: / (root), Boot, / USR, /usr/local, /var, / etc.</br> -Boot adresář by měl být na oddílu disku a nesmí být svazku LVM.<br/><br/> Od verze 9,20 a vyšší není tato omezení platí. 
Spouštěcí adresář | -Více spouštěcí disky jsou podporovány na virtuálním počítači <br/> - / není podporované spouštění na svazku LVM napříč více než jeden disk.<br/> – Počítač bez spouštěcí disk nelze replikovat.
Požadavky na volné místo| 2 GB na oddíl/root <br/><br/> 250 MB v instalační složce Nástroje
XFSv5 | Funkce XFSv5 v systémech souborů s XFS, jako je například metadata kontrolního součtu, jsou podporované (verze služby Mobility 9.10 a vyšší).<br/> Pomocí nástroje xfs_info tzv XFS pro oddíl. Pokud `ftype` je nastavena na hodnotu 1, pak XFSv5 funkce se používají.
BTRFS | Podporuje BTRFS od [34 kumulativní aktualizace](https://support.microsoft.com/help/4490016) (verze 9.22 služby Mobility) a vyšší. BTRFS není podporováno, pokud:<br/><br/> -Subvolume BTRFS souboru systému se změní po povolení ochrany.</br> -BTRFS systému souborů je rozdělena na několik disků.</br> -BTRFS systém souborů podporuje technologii RAID.

## <a name="vmdisk-management"></a>Správa virtuálních počítačů/disků

**Akce** | **Podrobnosti**
--- | ---
Změna velikosti disku na replikovaný virtuální počítač | Podporuje se.
Přidání disku na replikovaný virtuální počítač | Nepodporuje se.<br/> Zakažte replikaci virtuálního počítače, přidejte disk a potom znovu povolit replikaci.

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
ILB | Ano
ELB | Ano
Azure Traffic Manager | Ano
S více síťovými Kartami | Ano
Vyhrazená IP adresa | Ano
IPv4 | Ano
Zachovat Zdrojová IP adresa | Ano
Koncové body služby virtuální sítě Azure<br/> | Ano
Urychlení sítě | Ne

## <a name="storage"></a>Storage
**Komponenta** | **Podporuje se**
--- | ---
Dynamický disk | Disk s operačním systémem musí být základní disk. <br/><br/>Datové disky můžou být dynamických disků
Konfigurace disku dockeru | Ne
Host NFS | Ano pro VMware<br/><br/> Ne pro fyzické servery
Host SAN (iSCSI/FC) | Ano
Host vSAN | Ano pro VMware<br/><br/> Není k dispozici pro fyzické servery
Hostitel multipath (MPIO Multipath) | Ano, testováním s využitím Microsoft DSM EMC PowerPath 5.7 SP4, EMC PowerPath DSM pro CLARiiON
Svazky virtuálního hostitele (VVols) | Ano pro VMware<br/><br/> Není k dispozici pro fyzické servery
Host/server VMDK | Ano
Disk sdíleného clusteru hosta/server | Ne
Šifrované disk Host/server | Ne
Host/server systému souborů NFS | Ne
Host/server iSCSI | Ne
Host/serveru protokolu SMB 3.0 | Ne
Host/server RDM | Ano<br/><br/> Není k dispozici pro fyzické servery
Disk Host/serveru > 1 TB | Ano<br/><br/>Až 4 095 GB<br/><br/> Disk musí být větší než 1 024 MB.
Host/server na disku s velikost fyzického sektoru 4K logické a 4 kB | Ne
Disk s 4 kB logického typu Host/server a velikost fyzického sektoru 512 bajtů | Ne
Host/server svazek s prokládané disk > 4 TB <br/><br/>Správa logických svazků (LVM)| Ano
Host/server – prostory úložiště | Ne
Disk horké přidávat nebo odebírat hosta/server | Ne
Host/server - vyloučení disku | Ano
Funkce multipath hosta/server (MPIO) | Ne
Oddílů GPT hosta/server | Pěti oddílů se podporuje od [37 kumulativní aktualizace](https://support.microsoft.com/help/4508614/) (verze 9.25 služby Mobility) a vyšší. Dříve se podporovaly čtyři.
Spuštění EFI/UEFI hosta/server | -Podporovány, pokud používáte verzi služby Mobility 9.13 nebo vyšší.<br/> -Podporované při migraci virtuálních počítačů VMware nebo fyzické servery s Windows serverem 2012 nebo novější do Azure.<br/> Pouze – můžete replikovat virtuální počítače pro migraci. Navrácení služeb po obnovení do místní se nepodporuje.<br/> – Podporované pouze systémem souborů NTFS <br/> – Typ zabezpečené spuštění UEFI se nepodporuje. <br/> -Velikost sektoru disku musí být fyzický sektor o velikosti 512 bajtů.

## <a name="replication-channels"></a>Kanály replikace

|**Typ replikace**   |**Podporuje se**  |
|---------|---------|
|Přenosy dat se sníženou zátěží (ODX)    |       Ne  |
|Offline seeding        |   Ne      |
| Azure Data Box | Ne

## <a name="azure-storage"></a>Úložiště Azure

**Komponenta** | **Podporuje se**
--- | ---
(Locally redundant storage) Místně redundantní úložiště | Ano
Geograficky redundantní úložiště | Ano
Geograficky redundantní úložiště jen pro čtení | Ano
Úložiště Cool | Ne
Horkého úložiště| Ne
Objekty blob bloku | Ne
Šifrování neaktivních (SSE)| Ano
Premium Storage | Ano
Služba import/export | Ne
Bran firewall Azure Storage pro virtuální sítě | Ano.<br/> Konfigurovat na cílový účet úložiště mezipaměti nebo úložiště (sloužící k ukládání dat replikace).
Účty úložiště pro obecné účely v2 (horkou a studenou úrovní) | Ano (transakční náklady jsou výrazně vyšší v2 ve srovnání s V1)

## <a name="azure-compute"></a>Výpočetní prostředky Azure

**Funkce** | **Podporuje se**
--- | ---
Skupiny dostupnosti | Ano
Zóny dostupnosti | Ne
HUB | Ano
Spravované disky | Ano

## <a name="azure-vm-requirements"></a>Požadavky virtuálních počítačů Azure

Pro místní virtuální počítače replikované do Azure musí splňovat požadavky na virtuální počítač Azure uvedené v této tabulce. Kontrola požadavků pro replikaci spuštění Site Recovery je kontrola selže, pokud nejsou splněny některé požadavky.

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

## <a name="churn-limits"></a>Omezení četnosti změn

Následující tabulka obsahuje omezení služby Azure Site Recovery. 
- Tato omezení se zakládají na našich testováních, ale nezahrnuje všechny kombinace vstupně-výstupních operací aplikace.
- Skutečné výsledky se můžou lišit v závislosti na kombinaci vstupně-výstupních operací vaší aplikace.
- Nejlepších výsledků dosáhnete, důrazně doporučujeme, abyste spustili [nástroj Plánovač nasazení](site-recovery-deployment-planner.md)a provádět rozsáhlá testování aplikace pomocí testovací převzetí služeb při selhání získat skutečnou představu o výkonu pro vaši aplikaci.

**Cíl replikace** | **Průměrná velikost vstupně-výstupních operací zdrojového disku** |**Průměrná četnost změn dat zdrojového disku** | **Celková denní četnost změn dat zdrojového disku**
---|---|---|---
Storage úrovně Standard | 8 kB | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 8 kB  | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 16 kB | 4 MB/s |  336 GB na disk
Disk úrovně Premium P10 nebo P15 | 32 kB nebo větší | 8 MB/s | 672 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 8 kB    | 5 MB/s | 421 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 16 kB nebo větší |20 MB/s | 1684 GB na disk


**Četnost změn dat zdroje** | **Maximální omezení**
---|---
Průměrná četnost změn dat na virtuální počítač| 25 MB/s
Četnost změn dat ve špičce napříč všemi disky virtuálního počítače | 54 MB/s
Maximální četnost změn dat za den s podporou procesového serveru | 2 TB

- Toto jsou průměrné hodnoty za předpokladu, že se vstupně-výstupní operace z 30 % překrývají.
- Služba Site Recovery je schopna zpracovávat větší propustnost v závislosti na poměru překrývání, větší velikosti zápisů a skutečného chování vstupně-výstupních operací úloh.
- Tato čísla předpokládají typický backlog přibližně 5 minut. To znamená, že zpracování nahrávaných dat a vytvoření bodu obnovení proběhne do pěti minut od nahrání.

## <a name="vault-tasks"></a>Trezor úlohy

**Akce** | **Podporuje se**
--- | ---
Přesun trezoru mezi skupinami prostředků | Ne
Přesun trezoru v rámci a napříč předplatnými | Ne
Přesunout úložiště, sítě, virtuální počítače Azure mezi skupinami prostředků | Ne
Přesunout úložiště, sítě, virtuální počítače Azure v rámci a napříč předplatnými. | Ne


## <a name="obtain-latest-components"></a>Získat nejnovější komponenty

**Název** | **Popis** | **Podrobnosti**
--- | --- | ---
Konfigurační server | Nainstalovaný místně.<br/> Koordinuje komunikaci mezi místními servery VMware nebo fyzických počítačů a Azure. | - [Další informace o](vmware-physical-azure-config-process-server-overview.md) konfiguračního serveru.<br/> - [Další informace o](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) upgradovat na nejnovější verzi.<br/> - [Další informace o](vmware-azure-deploy-configuration-server.md) nastavení konfiguračního serveru. 
Procesový server | Obvykle se instaluje na konfigurační server.<br/> Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do Azure.<br/> Jak vaše nasazení poroste, můžete přidat další procesní servery pro zpracování větší objemy přenosů replikace. | - [Další informace o](vmware-physical-azure-config-process-server-overview.md) procesový server.<br/> - [Další informace o](vmware-azure-manage-process-server.md#upgrade-a-process-server) upgradovat na nejnovější verzi.<br/> - [Další informace o](vmware-physical-large-deployment.md#set-up-a-process-server) nastavení horizontální navýšení kapacity procesových serverů.
Služba mobility | Nainstalovat na virtuální počítač VMware nebo fyzické servery, které chcete replikovat.<br/> Řídí replikaci mezi servery pro/fyzické servery VMware v místním a Azure.| - [Další informace o](vmware-physical-mobility-service-overview.md) službu Mobility.<br/> - [Další informace o](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) upgradovat na nejnovější verzi.<br/> 



## <a name="next-steps"></a>Další postup
[Zjistěte, jak](tutorial-prepare-azure.md) Příprava Azure pro zotavení po havárii virtuálních počítačů VMware.

[9.26 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery

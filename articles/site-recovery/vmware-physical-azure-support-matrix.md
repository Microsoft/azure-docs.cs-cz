---
title: Matice podpory pro obnovení voblasti VMware/fyzické havárie v azure site recovery
description: Shrnuje podporu pro zotavení po havárii virtuálních počítačích VMware a fyzického serveru do Azure pomocí Azure Site Recovery.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 2/24/2020
ms.author: raynew
ms.openlocfilehash: 05e60c5b008746bbfd72dbe7a2e14b18aa563671
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371388"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matice podpory pro zotavení po havárii virtuálních počítačích VMware a fyzických serverů do Azure

Tento článek shrnuje podporované součásti a nastavení pro zotavení po havárii virtuálních počítačích VMware a fyzických serverů do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

- [Další informace](vmware-azure-architecture.md) o architektuře zotavení po havárii virtuálního vava nebo fyzického serveru VMware.
- Postupujte podle [našich výukových programů](tutorial-prepare-azure.md) a vyzkoušejte zotavení po havárii.

## <a name="deployment-scenarios"></a>Scénáře nasazení

**Scénář** | **Podrobnosti**
--- | ---
Zotavení virtuálních měn VMware po havárii | Replikace místních virtuálních počítačů VMware do Azure. Tento scénář můžete nasadit na webu Azure Portal nebo pomocí [PowerShellu](vmware-azure-disaster-recovery-powershell.md).
Zotavení fyzických serverů po havárii | Replikace místních fyzických serverů Windows/Linux do Azure. Tento scénář můžete nasadit na webu Azure Portal.

## <a name="on-premises-virtualization-servers"></a>Lokální virtualizační servery

**Server** | **Požadavky** | **Podrobnosti**
--- | --- | ---
vCenter Server | Verze 6.7, 6.5, 6.0 nebo 5.5 | Doporučujeme použít server vCenter v nasazení zotavení po havárii.
hostitelé vSphere | Verze 6.7, 6.5, 6.0 nebo 5.5 | Doporučujeme, aby hostitelé vSphere a servery vCenter byly umístěny ve stejné síti jako procesní server. Ve výchozím nastavení je procesní server spuštěn na konfiguračním serveru. [Další informace](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Konfigurační server obnovení sítě

Konfigurační server je místní počítač, který spouští součásti site recovery, včetně konfiguračního serveru, procesního serveru a hlavního cílového serveru.

- Pro virtuální počítače VMware nastavíte konfigurační server stažením šablony OVF a vytvořte virtuální ho dispozitova virtuálního počítače VMware.
- U fyzických serverů nastavíte počítač konfiguračního serveru ručně.

**Komponenta** | **Požadavky**
--- |---
Procesorová jádra | 8
Paměť RAM | 16 GB
Počet disků | 3 disky<br/><br/> Disky zahrnují disk operačního systému, disk mezipaměti procesu a retenční jednotku pro navrácení služeb po selhání.
Volné místo na disku | 600 GB místa pro mezipaměť procesního serveru.
Volné místo na disku | 600 GB místa pro retenční jednotku.
Operační systém  | Windows Server 2012 R2 nebo Windows Server 2016 s desktopovým prostředím <br/><br> Pokud máte v plánu použít vestavěný hlavní cíl tohoto zařízení pro navrácení služeb po selhání, ujistěte se, že verze operačního systému je stejná nebo vyšší než replikované položky.|
Národní prostředí operačního systému | Angličtina (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Není potřeba pro konfigurační server verze [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) nebo novější. 
Role Windows Serveru | Nepovolujte službu Active Directory Domain Services. Internetová informační služba (IIS) nebo technologie Hyper-V. 
Zásady skupiny| - Zabránit přístupu k příkazovému řádku. <br/> - Zabránit přístupu k nástrojům pro úpravu registru. <br/> - Logika důvěryhodnosti pro přílohy souborů. <br/> - Zapněte spuštění skriptu. <br/> - [Další informace](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Ujistěte se, že:<br/><br/> - Nemáte již existující výchozí webové stránky <br/> - Povolit [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - Povolit nastavení [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> - Nemají již existující webové stránky / app poslech na portu 443<br/>
Typ nic | VMXNET3 (při nasazení jako virtuální virtuální měna VMware)
Typ IP adresy | Statická
Porty | 443 používá pro řízení orchestrace kanálu<br/>9443 pro přenos dat

## <a name="replicated-machines"></a>Replikované počítače

Site Recovery podporuje replikaci všech úloh spuštěných na podporovaném počítači.

> [!Note]
> V následující tabulce je uvedena podpora pro počítače se spouštěním systému BIOS. Podpora na počítačích založených na UEFI naleznete v části [Úložiště.](#storage)

**Komponenta** | **Podrobnosti**
--- | ---
Nastavení stroje | Počítače, které se replikují do Azure, musí splňovat [požadavky Azure](#azure-vm-requirements).
Pracovní vytížení počítače | Site Recovery podporuje replikaci všech úloh spuštěných na podporovaném počítači. [Další informace](https://aka.ms/asr_workload).
Windows Server 2019 | Podporováno z [kumulativní aktualizace 34](https://support.microsoft.com/help/4490016) (verze 9.22 služby Mobility) a dále.
64bitový Systém Windows Server 2016 | Podporováno pro jádro serveru, server se zkušenostmi s počítačem.
Windows Server 2012 R2 / Windows Server 2012 | Podporuje se.
Windows Server 2008 R2 s sp1 a dále. | Podporuje se.<br/><br/> Od verze [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) agenta služby Mobility je třeba nainstalovat [aktualizaci zásobníku (SSU)](https://support.microsoft.com/help/4490628) a [sha-2 nainstalovanou](https://support.microsoft.com/help/4474419) v počítačích se systémem Windows 2008 R2 s aktualizací SP1 nebo novější. SHA-1 není podporována od září 2019 a pokud není povoleno podepisování kódu SHA-2, rozšíření agenta se nenainstaluje nebo neupgraduje podle očekávání. Další informace o [upgradu sha-2 a požadavcích](https://aka.ms/SHA-2KB).
Windows Server 2008 s sp2 nebo novější (64bitový/32bitový) |  Podporováno pouze pro migraci. [Další informace](migrate-tutorial-windows-server-2008.md).<br/><br/> Od verze [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) agenta služby Mobility je třeba nainstalovat [aktualizaci zásobníku (SSU)](https://support.microsoft.com/help/4493730) a [sha-2 nainstalovanou](hhttps://support.microsoft.com/help/4474419) v počítačích se systémem Windows 2008 SP2. ISHA-1 není podporována od září 2019 a pokud není povoleno podepisování kódu SHA-2, rozšíření agenta se nenainstaluje nebo neupgraduje podle očekávání. Další informace o [upgradu sha-2 a požadavcích](https://aka.ms/SHA-2KB).
Windows 10, Windows 8.1, Windows 8 | Podporuje se.
Windows 7 s 64bitovou sp1 | Podporováno z [kumulativní aktualizace 36](https://support.microsoft.com/help/4503156) (verze 9.22 služby Mobility) dále. </br></br> Od [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) agenta služby Mobility potřebujete [aktualizaci zásobníku (SSU)](https://support.microsoft.com/help/4490628) a [aktualizaci SHA-2 nainstalovanou](https://support.microsoft.com/help/4474419) v počítačích se systémem Windows 7 SP1.  SHA-1 není podporována od září 2019 a pokud není povoleno podepisování kódu SHA-2, rozšíření agenta se nenainstaluje nebo neupgraduje podle očekávání. Další informace o [upgradu sha-2 a požadavcích](https://aka.ms/SHA-2KB).
Linux | Podporován je pouze 64bitový systém. 32bitový systém není podporován.<br/><br/>Každý linuxový server by měl mít [nainstalovány součásti Linux Integration Services (LIS).](https://www.microsoft.com/download/details.aspx?id=55106) Je nutné spustit server v Azure po testování převzetí služeb při selhání nebo převzetí služeb při selhání. Pokud součásti LIS chybí, ujistěte se, že před povolením replikace pro počítače spustit v Azure. [components](https://www.microsoft.com/download/details.aspx?id=55106) <br/><br/> Site Recovery orchestruje převzetí služeb při selhání ke spuštění serverů Linux v Azure. Dodavatelé Linuxu však mohou omezit podporu pouze na distribuční verze, které nedosáhly konce životnosti.<br/><br/> V linuxových distribucích jsou podporována pouze základní jádra, která jsou součástí distribuce menší verze release/update.<br/><br/> Upgrade chráněných počítačů napříč hlavními distribučními verzemi Linuxu není podporován. Chcete-li inovovat, zakázat replikaci, inovovat operační systém a potom znovu povolit replikaci.<br/><br/> [Přečtěte si další informace](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) o podpoře Linuxu a open source technologie v Azure.
Linux Red Hat Enterprise | 5.2 až 5.11</b><br/> 6,1 až 6,10</b> </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1 <br/> Servery se systémem Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10 nemají předinstalované [komponenty Linux Integration Services (LIS).](https://www.microsoft.com/download/details.aspx?id=55106) Ujistěte se, že nainstalovat [součásti](https://www.microsoft.com/download/details.aspx?id=55106) před povolením replikace pro počítače nastartuje v Azure.
Linux: CentOS | 5.2 až 5.11</b><br/> 6,1 až 6,10</b><br/> 7,0 až 7,6<br/> <br/> 8,0 až 8,1<br/><br/> Servery se systémem CentOS 5.2-5.11 & 6.1-6.10 nemají předinstalované [komponenty Linux Integration Services (LIS).](https://www.microsoft.com/download/details.aspx?id=55106) Ujistěte se, že nainstalovat [součásti](https://www.microsoft.com/download/details.aspx?id=55106) před povolením replikace pro počítače nastartuje v Azure.
Ubuntu | Ubuntu 14.04 LTS server [(recenze podporovaných verzí jádra)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS server [(recenze podporovaných verzí jádra)](#ubuntu-kernel-versions) </br> Ubuntu 18.04 LTS server [(recenze podporovaných verzí jádra)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(recenze podporovaných verzí jádra)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(recenze podporovaných verzí jader)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15, 15 SP1 [(recenze podporovaných verzí jádra)](#suse-linux-enterprise-server-15-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> Upgrade replikovaných počítačů ze SUSE Linux Enterprise Server 11 SP3 na aktualizaci SP4 není podporován. Chcete-li provést upgrade, zakažte replikaci a po obnovení upgradu znovu povolte.
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> Spuštění jádra kompatibilního s Red Hat nebo Unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5) 

> [!Note]
> Pro každou z verzí systému Windows Azure Site Recovery podporuje pouze [sestavení kanálu dlouhodobé údržby (LTSC).](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc)  [Pololetní](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) verze kanálu nejsou v současné době podporovány.

### <a name="ubuntu-kernel-versions"></a>Verze jádra Ubuntu

**Podporovaná verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
14.04 LTS | [9.32][9.32 UR] | 3.13.0-24-generický až 3.13.0-170-generický,<br/>3.16.0-25-generický až 3.16.0-77-generický,<br/>3.19.0-18-generický až 3.19.0-80-generický,<br/>4.2.0-18-generický až 4.2.0-42-generický,<br/>4.4.0-21-generický až 4.4.0-148-generický,<br/>4.15.0-1023-azure až 4.15.0-1045-azure |
14.04 LTS | [9.31][9.31 UR] | 3.13.0-24-generický až 3.13.0-170-generický,<br/>3.16.0-25-generický až 3.16.0-77-generický,<br/>3.19.0-18-generický až 3.19.0-80-generický,<br/>4.2.0-18-generický až 4.2.0-42-generický,<br/>4.4.0-21-generický až 4.4.0-148-generický,<br/>4.15.0-1023-azure až 4.15.0-1045-azure |
14.04 LTS | [9.30][9.30 UR] | 3.13.0-24-generický až 3.13.0-170-generický,<br/>3.16.0-25-generický až 3.16.0-77-generický,<br/>3.19.0-18-generický až 3.19.0-80-generický,<br/>4.2.0-18-generický až 4.2.0-42-generický,<br/>4.4.0-21-generický až 4.4.0-148-generický,<br/>4.15.0-1023-azure až 4.15.0-1045-azure |
14.04 LTS | [9.29][9.29 UR]| 3.13.0-24-generický až 3.13.0-170-generický,<br/>3.16.0-25-generický až 3.16.0-77-generický,<br/>3.19.0-18-generický až 3.19.0-80-generický,<br/>4.2.0-18-generický až 4.2.0-42-generický,<br/>4.4.0-21-generický až 4.4.0-148-generický,<br/>4.15.0-1023-azure až 4.15.0-1045-azure |
|||
16,04 LTS | [9.32][9.32 UR] | 4.4.0-21-generický až 4.4.0-171-generický,<br/>4.8.0-34-generický až 4.8.0-58-generický,<br/>4.10.0-14-generický až 4.10.0-42-generický,<br/>4.11.0-13-generický až 4.11.0-14-generický,<br/>4.13.0-16-generický až 4.13.0-45-generický,<br/>4.15.0-13-generický až 4.15.0-74-generický<br/>4.11.0-1009-azure až 4.11.0-1016-azure,<br/>4.13.0-1005-azure až 4.13.0-1018-azure <br/>4.15.0-1012-azure až 4.15.0-1066-azure|
16,04 LTS | [9.31][9.31 UR] | 4.4.0-21-generický až 4.4.0-170-generický,<br/>4.8.0-34-generický až 4.8.0-58-generický,<br/>4.10.0-14-generický až 4.10.0-42-generický,<br/>4.11.0-13-generický až 4.11.0-14-generický,<br/>4.13.0-16-generický až 4.13.0-45-generický,<br/>4.15.0-13-generický až 4.15.0-72-generický<br/>4.11.0-1009-azure až 4.11.0-1016-azure,<br/>4.13.0-1005-azure až 4.13.0-1018-azure <br/>4.15.0-1012-azure až 4.15.0-1063-azure|
16,04 LTS | [9.30][9.30 UR] | 4.4.0-21-generický až 4.4.0-166-generický,<br/>4.8.0-34-generický až 4.8.0-58-generický,<br/>4.10.0-14-generický až 4.10.0-42-generický,<br/>4.11.0-13-generický až 4.11.0-14-generický,<br/>4.13.0-16-generický až 4.13.0-45-generický,<br/>4.15.0-13-generický až 4.15.0-66-generický<br/>4.11.0-1009-azure až 4.11.0-1016-azure,<br/>4.13.0-1005-azure až 4.13.0-1018-azure <br/>4.15.0-1012-azure až 4.15.0-1061-azure|
16,04 LTS | [9.29][9.29 UR] | 4.4.0-21-generický až 4.4.0-164-generický,<br/>4.8.0-34-generický až 4.8.0-58-generický,<br/>4.10.0-14-generický až 4.10.0-42-generický,<br/>4.11.0-13-generický až 4.11.0-14-generický,<br/>4.13.0-16-generický až 4.13.0-45-generický,<br/>4.15.0-13-generický až 4.15.0-64-generický<br/>4.11.0-1009-azure až 4.11.0-1016-azure,<br/>4.13.0-1005-azure až 4.13.0-1018-azure <br/>4.15.0-1012-azure až 4.15.0-1059-azure|
|||
18.04 LTS | [9.32][9.32 UR]| 4.15.0-20-generický až 4.15.0-74-generický </br> 4.18.0-13-generický až 4.18.0-25-generický </br> 5.0.0-15-generický až 5.0.0-37-generický </br> 5.3.0-19-generický až 5.3.0-24-generický </br> 4.15.0-1009-azure až 4.15.0-1037-azure </br> 4.18.0-1006-azure až 4.18.0-1025-azure </br> 5.0.0-1012-azure až 5.0.0-1028-azure </br> 5.3.0-1007-azure až 5.3.0-1009-azure|
18.04 LTS | [9.31][9.31 UR]| 4.15.0-20-generický až 4.15.0-72-generický </br> 4.18.0-13-generický až 4.18.0-25-generický </br> 5.0.0-15-generický až 5.0.0-37-generický </br> 5.3.0-19-generický až 5.3.0-24-generický </br> 4.15.0-1009-azure až 4.15.0-1037-azure </br> 4.18.0-1006-azure až 4.18.0-1025-azure </br> 5.0.0-1012-azure až 5.0.0-1025-azure </br> 5.3.0-1007-azur|
18.04 LTS | [9.30][9.30 UR] | 4.15.0-20-generický až 4.15.0-66-generický </br> 4.18.0-13-generický až 4.18.0-25-generický </br> 5.0.0-15-generický až 5.0.0-32-generický </br> 4.15.0-1009-azure až 4.15.0-1037-azure </br> 4.18.0-1006-azure až 4.18.0-1025-azure </br> 5.0.0-1012-azure až 5.0.0-1023-azure|
18.04 LTS | [9.29][9.29 UR] | 4.15.0-20-generický až 4.15.0-62-generický </br> 4.18.0-13-generický až 4.18.0-25-generický </br> 5.0.0-15-generický až 5.0.0-27-generický </br> 4.15.0-1009-azure až 4.15.0-1037-azure </br> 4.18.0-1006-azure až 4.18.0-1025-azure </br> 5.0.0-1012-azure až 5.0.0-1018-azure|

### <a name="debian-kernel-versions"></a>Verze jádra Debianu


**Podporovaná verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
Debian 7 | [9.29][9.29 UR], [9.30][9.30 UR], [9.31][9.31 UR], [9.32.][9.32 UR]| 3.2.0-4-amd64 až 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.30][9.30 UR], [9.31][9.31 UR], [9.32.][9.32 UR] | 3.16.0-4-amd64 až 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 až 4.9.0-0.bpo.11-amd64 |
Debian 8 | [9.29][9.29 UR] | 3.16.0-4-amd64 až 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 až 4.9.0-0.bpo.9-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>Podporované verze jádra SUSE Linux Enterprise Server 12

**Vydat** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.32][9.32 UR] | Podporována jsou všechna [jádra SUSE 12 SP1, SP2, SP3, SP4.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azuraž na 4.4.180-4.31-azurový,</br>4.12.14-6.3-azure až 4.12.14-6.34-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.31][9.31 UR] | Podporována jsou všechna [jádra SUSE 12 SP1, SP2, SP3, SP4.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azuraž na 4.4.180-4.31-azurový,</br>4.12.14-6.3-azure až 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.30][9.30 UR] | Podporována jsou všechna [jádra SUSE 12 SP1, SP2, SP3, SP4.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azuraž na 4.4.180-4.31-azurový,</br>4.12.14-6.3-azure až 4.12.14-6.26-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.29][9.29 UR] | Podporována jsou všechna [jádra SUSE 12 SP1, SP2, SP3, SP4.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azuraž na 4.4.180-4.31-azurový,</br>4.12.14-6.3-azure až 4.12.14-6.23-azure  |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>Podporované verze jádra SUSE Linux Enterprise Server 15

**Vydat** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 a 15 SP1 | 9.32 | Podporována jsou všechna [jádra SUSE 15 a 15.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_15)</br></br> 4.12.14-5.5-azure až 4.12.14-8.22-azure |

## <a name="linux-file-systemsguest-storage"></a>Linuxové souborové systémy/úložiště pro hosty

**Komponenta** | **Podporovány**
--- | ---
Systémy souborů | ext3, ext4, XFS, BTRFS (podmínky platné podle této tabulky)
Správce svazků | - LVM je podporováno.<br/> - /boot na LVM je podporován z [kumulativní aktualizace 31](https://support.microsoft.com/help/4478871/) (verze 9.20 služby Mobility) a dále. Není podporována v dřívějších verzích služby Mobility.<br/> - Více disků operačního systému není podporováno.
Paravirtualizovaná úložná zařízení | Zařízení exportovaná paravirtualizovanými ovladači se nepodporují.
Zařízení iO bloku více front | Není podporováno.
Fyzické servery s řadičem úložiště HP CCISS | Není podporováno.
Konvence pojmenování zařízení a přípojného bodu | Název zařízení nebo název přípojného bodu by měl být jedinečný.<br/> Ujistěte se, že žádná dvě zařízení/přípojné body mají názvy rozlišující malá a velká písmena. Například pojmenování zařízení pro stejný virtuální virtuální modul jako *device1* a *Device1* není podporována.
Adresáře | Pokud používáte verzi služby Mobility starší než verze 9.20 (vydané v [kumulativní aktualizaci 31](https://support.microsoft.com/help/4478871/)), platí tato omezení:<br/><br/> - Tyto adresáře (pokud jsou nastaveny jako samostatné oddíly/souborové systémy) musí být na stejném disku operačního systému na zdrojovém serveru: /(root), /boot, /usr, /usr/local, /var, /etc.</br> - /boot adresář by měl být na disku oddílu a nesmí být svazek LVM.<br/><br/> Od verze 9.20 tato omezení neplatí. 
Spouštěcí adresář | - Spouštěcí disky nesmí být ve formátu oddílu GPT. Toto je omezení architektury Azure. Disky GPT jsou podporovány jako datové disky.<br/><br/> Více spouštěcích disků na virtuálním počítači není podporované<br/><br/> - /boot na svazku LVM přes více než jeden disk není podporován.<br/> - Počítač bez spouštěcího disku nelze replikovat.
Požadavky na volné místo| 2 GB na /root oddílu <br/><br/> 250 MB v instalační složce
XFSv5 | Funkce XFSv5 v systémech souborů XFS, jako je například kontrolní součet metadat, jsou podporovány (verze služby Mobility verze 9.10 dále).<br/> Pomocí nástroje xfs_info zkontrolujte superblok XFS pro oddíl. Pokud `ftype` je nastavena na 1, pak XFSv5 funkce jsou používány.
BTRFS | BTRFS je podporován od [kumulativní aktualizace 34](https://support.microsoft.com/help/4490016) (verze 9.22 služby Mobility) a dále. BTRFS není podporován, pokud:<br/><br/> - Subsvazsystému souborů BTRFS se po povolení ochrany změní.</br> - Souborový systém BTRFS je rozložen na více discích.</br> - Souborový systém BTRFS podporuje RAID.

## <a name="vmdisk-management"></a>Správa virtuálního počítače/disku

**Akce** | **Podrobnosti**
--- | ---
Změna velikosti disku na replikovaném virtuálním počítači | Podporováno na zdrojovém virtuálním počítači před převzetím služeb při selhání přímo ve vlastnostech virtuálního počítače. Není nutné zakázat nebo znovu povolit replikaci.<br/><br/> Pokud změníte zdrojový virtuální virtuální ms po převzetí služeb při selhání, změny nejsou zachycení.<br/><br/> Pokud změníte velikost disku na virtuálním počítači Azure po převzetí služeb při selhání, když si poobnovení, site recovery vytvoří nový virtuální počítač s aktualizacemi.
Přidání disku do replikovaného virtuálního počítače | Není podporováno.<br/> Zakažte replikaci virtuálního počítače, přidejte disk a znovu povolte replikaci.

## <a name="network"></a>Network (Síť)

**Komponenta** | **Podporovány**
--- | ---
Seskatování síťových síťových síťových sítí | Podporováno pro virtuální měna VMware. <br/><br/>Není podporováno pro replikaci fyzického počítače.
Hostitelská síť VLAN | Ano.
Hostitelská síť IPv4 | Ano.
IPv6 hostitelské sítě | Ne.
Seskatouje síť ovou síť hosta/serveru | Ne.
Síť hosta/serveru IPv4 | Ano.
Síť hosta/serveru IPv6 | Ne.
Statická IP adresa sítě hosta/serveru (Windows) | Ano.
Statická IP adresa sítě host/server (Linux) | Ano. <br/><br/>Virtuální počítače jsou nakonfigurované tak, aby používaly službu DHCP při navrácení služeb po obnovení.
Síť hosta/serveru více síťových karty | Ano.


## <a name="azure-vm-network-after-failover"></a>Síť virtuálních počítačových sítí Azure (po převzetí služeb při selhání)

**Komponenta** | **Podporovány**
--- | ---
Azure ExpressRoute | Ano
ILB | Ano
Elb | Ano
Azure Traffic Manager | Ano
Více nefunkční chod | Ano
Rezervovaná IP adresa | Ano
IPv4 | Ano
Zachovat zdrojovou ADRESU IP | Ano
Koncové body virtuální síťové služby Azure<br/> | Ano
Urychlení sítě | Ne

## <a name="storage"></a>Úložiště
**Komponenta** | **Podporovány**
--- | ---
Dynamický disk | Disk operačního systému musí být základní disk. <br/><br/>Datové disky mohou být dynamické disky
Konfigurace disku Dockeru | Ne
Hostitelské nfs | Ano pro vmware<br/><br/> Ne pro fyzické servery
Hostitel SAN (iSCSI/FC) | Ano
Hostitel vSAN | Ano pro vmware<br/><br/> Není k tomu k onomu fyzickému serveru
Vícecestná hostitelská cesta (MPIO) | Ano, testováno s Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM pro CLARiiON
Hostitelské virtuální svazky (VVols) | Ano pro vmware<br/><br/> Není k tomu k onomu fyzickému serveru
Host/server VMDK | Ano
Disk sdíleného clusteru hosta/serveru | Ne
Zašifrovaný disk hosta/serveru | Ne
Systém zabezpečení systému zabezpečení hosta/serveru | Ne
Host/server iSCSI | Pro migraci - Ano<br/>Pro zotavení po havárii – ne, iSCSI bude navrácení služeb po obnovení jako připojený disk k virtuálnímu počítače
Host/server SMB 3.0 | Ne
RdM host/server RDM | Ano<br/><br/> Není k tomu k onomu fyzickému serveru
Disk hosta/serveru > 1 TB | Ano, disk musí být větší než 1024 MB.<br/><br/>Až 8 192 GB při replikaci na spravované disky (verze 9.26 dále)<br></br> Až 4 095 GB při replikaci na účty úložiště
Disk hosta/serveru s logickou velikostí 4K a fyzickým sektorem 4k | Ne
Disk hosta/serveru s logickou velikostí 4K a velikostí fyzického sektoru 512 bajtů | Ne
Host/serverový svazek s prokládaným diskem >4 TB <br/><br/>Logická správa svazků (LVM)| Ano
Host/server - Prostory úložiště | Ne
Aktivní přidat nebo odebrat disk pro přidání nebo odebrání hosta/serveru | Ne
Host/server - vyloučit disk | Ano
Multicesta hosta/serveru (MPIO) | Ne
Oddíly GPT hosta/serveru | Pět oddílů je podporováno z [kumulativní aktualizace 37](https://support.microsoft.com/help/4508614/) (verze 9.25 služby Mobility) a dále. Dříve byly podporovány čtyři.
ReFS | Odolný souborový systém je podporován službou Mobility verze 9.23 nebo vyšší
Spouštění EFI/UEFI pro hosty/server | - Podporováno pro Windows Server 2012 nebo novější, SLES 12 SP4 a RHEL 8.0 s agentem mobility verze 9.30 a dále<br/> - Zabezpečený typ spouštění UEFI není podporován. 

## <a name="replication-channels"></a>Replikační kanály

|**Typ replikace**   |**Podporovány**  |
|---------|---------|
|Překládaných datových přenosů (ODX)    |       Ne  |
|Offline secí        |   Ne      |
| Azure Data Box | Ne

## <a name="azure-storage"></a>Úložiště Azure

**Komponenta** | **Podporovány**
--- | ---
(Locally redundant storage) Místně redundantní úložiště | Ano
Geograficky redundantní úložiště | Ano
Geograficky redundantní úložiště s přístupem pro čtení | Ano
Chladné úložiště | Ne
Horké úložiště| Ne
Objekty blob bloku | Ne
Šifrování v klidovém stavu (SSE)| Ano
Šifrování v klidovém stavu (CMK)| Ano (dále přes modul Powershell Az 3.3.0)
Premium Storage | Ano
Služba importu a exportu | Ne
Brány firewall azure storage pro virtuální sítě | Ano.<br/> Nakonfigurováno v cílovém účtu úložiště úložiště nebo mezipaměti (používá se k ukládání dat replikace).
Účty úložiště pro obecné účely v2 (horké a chladné úrovně) | Ano (Transakční náklady jsou podstatně vyšší pro V2 ve srovnání s V1)

## <a name="azure-compute"></a>Výpočetní prostředky Azure

**Funkce** | **Podporovány**
--- | ---
Skupiny dostupnosti | Ano
Zóny dostupnosti | Ne
Rozbočovač | Ano
Spravované disky | Ano

## <a name="azure-vm-requirements"></a>Požadavky na virtuální počítač Azure

Místní virtuální počítače replikované do Azure musí splňovat požadavky virtuálních zařízení Azure shrnuté v této tabulce. Při obnovení webu spustí kontrolu požadavků na replikaci, kontrola se nezdaří, pokud některé požadavky nejsou splněny.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Hostovaný operační systém | Ověřte [podporované operační systémy](#replicated-machines) pro replikované počítače. | Kontrola se nezdaří, pokud není podporována.
Architektura hostovaného operačního systému | 64bitový. | Kontrola se nezdaří, pokud není podporována.
Velikost disku s operačním systémem | Až 2 048 GB. | Kontrola se nezdaří, pokud není podporována.
Počet disků s operačním systémem | 1 | Kontrola se nezdaří, pokud není podporována.
Počet datových disků | 64 nebo méně. | Kontrola se nezdaří, pokud není podporována.
Velikost datového disku | Až 8 192 GB při replikaci na spravovaný disk (verze 9.26 dále)<br></br>Až 4 095 GB při replikaci do účtu úložiště| Kontrola se nezdaří, pokud není podporována.
Síťové adaptéry | Je podporováno více adaptérů. |
Sdílený virtuální pevný disk | Není podporováno. | Kontrola se nezdaří, pokud není podporována.
Disk FC | Není podporováno. | Kontrola se nezdaří, pokud není podporována.
BitLocker | Není podporováno. | Nástroj BitLocker musí být zakázán před povolením replikace pro počítač. |
název virtuálního počítače | Od 1 do 63 znaků.<br/><br/> Pouze písmena, číslice a pomlčky.<br/><br/> Název počítače musí začínat a končit písmenem nebo číslem. |  Aktualizujte hodnotu ve vlastnostech počítače v obnovení webu.

## <a name="resource-group-limits"></a>Omezení skupiny prostředků

Informace o počtu virtuálních počítačů, které lze chránit v rámci jedné skupiny prostředků, naleznete v článku o [omezení předplatného a kvótách](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#resource-group-limits)

## <a name="churn-limits"></a>Omezení konve

Následující tabulka obsahuje omezení služby Azure Site Recovery. 
- Tato omezení jsou založena na našich testech, ale nepokrývají všechny možné kombinace vstupně-va aplikací.
- Skutečné výsledky se můžou lišit v závislosti na kombinaci vstupně-výstupních operací vaší aplikace.
- Chcete-li dosáhnout nejlepších výsledků, důrazně doporučujeme spustit [nástroj Plánovač nasazení](site-recovery-deployment-planner.md)a provést rozsáhlé testování aplikací pomocí testovacích převzetí služeb při selhání, abyste získali skutečný obrázek výkonu pro vaši aplikaci.

**Cíl replikace** | **Průměrná velikost vstupně-výstupních operací zdrojového disku** |**Průměrná četnost změn dat zdrojového disku** | **Celkový počet změn dat zdrojového disku za den**
---|---|---|---
Storage úrovně Standard | 8 kB | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 8 kB  | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 16 kB | 4 MB/s |  336 GB na disk
Disk úrovně Premium P10 nebo P15 | 32 kB nebo větší | 8 MB/s | 672 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 8 kB    | 5 MB/s | 421 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 16 kB nebo větší |20 MB/s | 1684 GB na disk


**Četnost změn dat zdroje** | **Maximální omezení**
---|---
Četnost změn dat ve špičce napříč všemi disky virtuálního počítače | 54 MB/s
Maximální četnost změn dat za den s podporou procesového serveru | 2 TB

- Toto jsou průměrné hodnoty za předpokladu, že se vstupně-výstupní operace z 30 % překrývají.
- Služba Site Recovery je schopna zpracovávat větší propustnost v závislosti na poměru překrývání, větší velikosti zápisů a skutečného chování vstupně-výstupních operací úloh.
- Tato čísla předpokládají typické nevyřízené položky přibližně pět minut. To znamená, že zpracování nahrávaných dat a vytvoření bodu obnovení proběhne do pěti minut od nahrání.

## <a name="vault-tasks"></a>Úkoly trezoru

**Akce** | **Podporovány**
--- | ---
Přesunutí trezoru mezi skupinami prostředků | Ne
Přesunutí trezoru v rámci předplatných i mezi ně | Ne
Přesunutí úložiště, sítě a virtuálních počítačích Azure napříč skupinami prostředků | Ne
Přesuňte úložiště, síť, virtuální počítače Azure v rámci a napříč předplatnými. | Ne


## <a name="obtain-latest-components"></a>Získání nejnovějších komponent

**Název** | **Popis** | **Podrobnosti**
--- | --- | ---
Konfigurační server | Nainstalováno místně.<br/> Koordinuje komunikaci mezi místními servery VMware nebo fyzickými počítači a Azure. | - [Další informace o](vmware-physical-azure-config-process-server-overview.md) konfiguračním serveru.<br/> - [Přečtěte si o](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) upgradu na nejnovější verzi.<br/> - [Přečtěte si informace o](vmware-azure-deploy-configuration-server.md) nastavení konfiguračního serveru. 
Procesní server | Obvykle se instaluje na konfigurační server.<br/> Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do Azure.<br/> Jak vaše nasazení roste, můžete přidat další procesní servery pro zpracování větších svazků replikačního provozu. | - [Další informace o](vmware-physical-azure-config-process-server-overview.md) procesním serveru.<br/> - [Přečtěte si o](vmware-azure-manage-process-server.md#upgrade-a-process-server) upgradu na nejnovější verzi.<br/> - [Přečtěte si o](vmware-physical-large-deployment.md#set-up-a-process-server) nastavení horizontálních procesních serverů škálování.
Služba Mobility | Nainstalováno na virtuálním počítači VMware nebo fyzických serverech, které chcete replikovat.<br/> Koordinuje replikaci mezi místními servery VMware a fyzickými servery a Azure.| - [Přečtěte si o](vmware-physical-mobility-service-overview.md) službě Mobility.<br/> - [Přečtěte si o](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) upgradu na nejnovější verzi.<br/> 



## <a name="next-steps"></a>Další kroky
[Zjistěte, jak](tutorial-prepare-azure.md) připravit Azure na zotavení po havárii virtuálních počítačích VMware.

[9.32 UR]: https://support.microsoft.com/en-in/help/4538187/update-rollup-44-for-azure-site-recovery
[9.31 UR]: https://support.microsoft.com/en-in/help/4537047/update-rollup-43-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery

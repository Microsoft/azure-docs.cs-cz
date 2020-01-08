---
title: Matice podpory pro zotavení po havárii VMware/fyzický v Azure Site Recovery
description: Shrnuje podporu pro zotavení po havárii virtuálních počítačů VMware a fyzického serveru do Azure pomocí Azure Site Recovery.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/23/2019
ms.openlocfilehash: 752f2276f23aa22feac870493e1ea5c1fdc4ca98
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645935"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matice podpory pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure

Tento článek shrnuje podporované součásti a nastavení pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

- [Přečtěte si další informace](vmware-azure-architecture.md) o architektuře virtuálních počítačů VMware nebo fyzického serveru pro zotavení po havárii.
- Pokud si chcete vyzkoušet zotavení po havárii, postupujte podle našich [kurzů](tutorial-prepare-azure.md) .

## <a name="deployment-scenarios"></a>Scénáře nasazení

**Scénář** | **Podrobnosti**
--- | ---
Zotavení virtuálních počítačů VMware po havárii | Replikace místních virtuálních počítačů VMware do Azure. Tento scénář můžete nasadit v Azure Portal nebo pomocí [prostředí PowerShell](vmware-azure-disaster-recovery-powershell.md).
Zotavení po havárii fyzických serverů | Replikace místních fyzických serverů se systémem Windows nebo Linux do Azure. Tento scénář můžete nasadit v Azure Portal.

## <a name="on-premises-virtualization-servers"></a>Místní virtualizační servery

**Server** | **Požadavky** | **Podrobnosti**
--- | --- | ---
vCenter Server | Verze 6,7, 6,5, 6,0 nebo 5,5 | V nasazení zotavení po havárii doporučujeme použít Server vCenter.
vSphere hostitelé | Verze 6,7, 6,5, 6,0 nebo 5,5 | Doporučujeme, aby se hostitelé vSphere a vCenter servery nacházely ve stejné síti jako procesový Server. Ve výchozím nastavení běží procesový Server na konfiguračním serveru. [Další informace](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Site Recovery konfigurační server

Konfigurační server je místní počítač, který spouští součásti Site Recovery, včetně konfiguračního serveru, procesového serveru a hlavního cílového serveru.

- Pro virtuální počítače VMware nastavíte konfigurační server tak, že si stáhnete šablonu OVF a vytvoříte virtuální počítač VMware.
- Pro fyzické servery nastavte počítač konfiguračního serveru ručně.

**Komponenta** | **Požadavky**
--- |---
Procesorová jádra | 8
Paměť RAM | 16 GB
Počet disků | 3 disky<br/><br/> Mezi disky patří disk s operačním systémem, disk mezipaměti procesového serveru a jednotka pro uchovávání pro navrácení služeb po obnovení.
Volné místo na disku | 600 GB místa pro mezipaměť procesového serveru.
Volné místo na disku | 600 GB místa pro jednotku pro uchovávání dat.
Operační systém  | Windows Server 2012 R2 nebo Windows Server 2016 s desktopovým prostředím <br/><br> Pokud plánujete použít vestavěný hlavní cíl tohoto zařízení k navrácení služeb po obnovení, zajistěte, aby byla verze operačního systému stejná nebo vyšší než replikované položky.|
Národní prostředí operačního systému | Angličtina (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Není nutné pro konfigurační server verze [9,14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) nebo novější. 
Role Windows Serveru | Nepovolujte Active Directory Domain Services; Internetová informační služba (IIS) nebo Hyper-V. 
Zásady skupiny| – Zabraňte přístupu k příkazovému řádku. <br/> – Zabraňte přístup k nástrojům pro úpravu registru. <br/> – Logika vztahu důvěryhodnosti pro přílohy souborů. <br/> -Zapnout provádění skriptu. <br/> - další [informace](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Ujistěte se, že:<br/><br/> – Nemáte existující výchozí web. <br/> -Povolit [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> -Povolit nastavení [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> – Už nemusíte na portu 443 naslouchat předem existující web nebo aplikaci.<br/>
Typ síťové karty | VMXNET3 (při nasazení jako virtuální počítač VMware)
Typ IP adresy | Statický
Porty | 443 použito pro orchestraci řídicích kanálů<br/>9443 pro přenos dat

## <a name="replicated-machines"></a>Replikované počítače

Site Recovery podporuje replikaci všech úloh spuštěných v podporovaném počítači.

> [!Note]
> V následující tabulce je uvedena podpora pro počítače se spouštěním systému BIOS. Podporu počítačů založených na UEFI najdete v části [úložiště](#storage) .

**Komponenta** | **Podrobnosti**
--- | ---
Nastavení počítače | Počítače, které se replikují do Azure, musí splňovat [požadavky Azure](#azure-vm-requirements).
Zatížení počítače | Site Recovery podporuje replikaci všech úloh spuštěných v podporovaném počítači. [Další informace](https://aka.ms/asr_workload).
Windows Server 2019 | Podporováno z [kumulativní aktualizace 34](https://support.microsoft.com/help/4490016) (verze 9,22 služby mobility) a vyšší.
Windows Server 2016 64 – bit | Podporováno pro jádro serveru, server s desktopovým prostředím.
Windows Server 2012 R2/Windows Server 2012 | Podporuje se.
Windows Server 2008 R2 s aktualizací SP1 a vyšší. | Podporuje se.<br/><br/> Od agenta služby mobility verze [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) potřebujete aktualizaci [cestou nadřazené (Servicing Stack Update)](https://support.microsoft.com/help/4490628) a [SHA-2](https://support.microsoft.com/help/4474419) nainstalovanou na počítačích se systémem Windows 2008 R2 s aktualizací SP1 nebo novějším. SHA-1 se od září 2019 nepodporuje a pokud podepisování kódu SHA-2 není povolené, rozšíření agenta se nenainstaluje nebo upgraduje podle očekávání. Přečtěte si další informace o [upgradu a požadavcích SHA-2](https://aka.ms/SHA-2KB).
Windows Server 2008 s aktualizací SP2 nebo novější (64bitová/32bitová verze) |  Podporováno pouze pro migraci. [Další informace](migrate-tutorial-windows-server-2008.md).<br/><br/> Od agenta služby mobility verze [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) potřebujete aktualizaci [cestou nadřazené (Servicing Stack Update)](https://support.microsoft.com/help/4493730) a [SHA-2](h https://support.microsoft.com/help/4474419) nainstalovanou na počítačích s Windows 2008 SP2. ISHA-1 se od září 2019 nepodporuje a pokud podepisování kódu SHA-2 není povolené, rozšíření agenta se nenainstaluje nebo upgraduje podle očekávání. Přečtěte si další informace o [upgradu a požadavcích SHA-2](https://aka.ms/SHA-2KB).
Windows 10, Windows 8.1, Windows 8 | Podporuje se.
Windows 7 s aktualizací SP1 64-bit | Podporováno z [kumulativní aktualizace 36](https://support.microsoft.com/help/4503156) (verze 9,22 služby mobility) a vyšší. </br></br> Od agenta služby mobility [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) potřebujete aktualizaci [cestou nadřazené (Servicing Stack Update)](https://support.microsoft.com/help/4490628) a [SHA-2](https://support.microsoft.com/help/4474419) nainstalovanou na počítačích se systémem Windows 7 SP1.  SHA-1 se od září 2019 nepodporuje a pokud podepisování kódu SHA-2 není povolené, rozšíření agenta se nenainstaluje nebo upgraduje podle očekávání. Přečtěte si další informace o [upgradu a požadavcích SHA-2](https://aka.ms/SHA-2KB).
Linux | Podporuje se jenom 64-bit systému. 32 bitový systém není podporován.<br/><br/>Na každém serveru se systémem Linux by měly být nainstalovány [komponenty pro Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) . Po testovacím převzetí služeb při selhání nebo převzetí služeb při selhání se vyžaduje spuštění serveru v Azure. Pokud chybí komponenty aplikace LIS, před povolením replikace pro počítače, které se mají spustit v Azure, zajistěte instalaci těchto [součástí](https://www.microsoft.com/download/details.aspx?id=55106) . <br/><br/> Site Recovery orchestrovat převzetí služeb při selhání, aby se spouštěly servery Linux v Azure. Dodavatelé systému Linux ale můžou omezit podporu jenom na distribuční verze, které nedosáhly konce životního cyklu.<br/><br/> V distribucích systému Linux jsou podporovány pouze základní jádra, která jsou součástí verze distribuce nebo aktualizace dílčí verze distribuce.<br/><br/> Upgrade chráněných počítačů v rámci velkých verzí pro distribuci systému Linux není podporován. Pokud chcete upgradovat, zakažte replikaci, upgradujte operační systém a pak znovu povolte replikaci.<br/><br/> [Přečtěte si další informace](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) o podpoře pro Linux a open source technologie v Azure.
Linux Red Hat Enterprise | 5,2 až 5,11</b><br/> 6,1 až 6,10</b> </br> 7,0, 7,1, 7,2, 7,3, 7,4, [7,5, 7,6](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) , [7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), 8,0 <br/> Servery se systémem Red Hat Enterprise Linux 5.2-5,11 & 6.1-6.10 nemají předem nainstalované [komponenty Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) . Před povolením replikace pro počítače, které se mají spustit v Azure, nezapomeňte nainstalovat tyto [součásti](https://www.microsoft.com/download/details.aspx?id=55106) .
Linux: CentOS | 5,2 až 5,11</b><br/> 6,1 až 6,10</b><br/> 7,0 až 7,6<br/> <br/> Servery, na kterých běží CentOS 5.2-5,11 & 6.1-6.10, nemají předem nainstalované [komponenty pro Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) . Před povolením replikace pro počítače, které se mají spustit v Azure, nezapomeňte nainstalovat tyto [součásti](https://www.microsoft.com/download/details.aspx?id=55106) .
Ubuntu | Server Ubuntu 14,04 LTS [(kontrola podporovaných verzí jádra)](#ubuntu-kernel-versions)<br/><br/>Server Ubuntu 16,04 LTS [(kontrola podporovaných verzí jádra)](#ubuntu-kernel-versions) </br> Server Ubuntu 18,04 LTS [(kontrola podporovaných verzí jádra)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(kontrola podporovaných verzí jádra)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(kontrola podporovaných verzí jádra)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> Upgrade replikovaných počítačů z SUSE Linux Enterprise Server 11 SP3 na SP4 se nepodporuje. Pokud chcete upgradovat, zakažte replikaci a po upgradu ji znovu povolte.
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4 [, 7,5, 7,6, 7,7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> Spuštění jádra kompatibilního s Red Hat nebo nedělitelné podnikové jádro verze 3, 4 & 5 (UEK3, UEK4, UEK5) 

> [!Note]
> Pro každou verzi Windows Azure Site Recovery podporuje jenom sestavení [LTSC (Long-Term Servicing Channel)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) .  [Pololetní verze kanálů](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) v tuto chvíli nejsou aktuálně podporovány.

### <a name="ubuntu-kernel-versions"></a>Verze jádra Ubuntu

**Podporovaná verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
14,04 LTS | [9,31][9.31 UR] | 3.13.0-24 – obecné pro 3.13.0-170 – obecné,<br/>3.16.0-25 – obecný pro 3.16.0-77-Generic,<br/>3.19.0-18 – obecný pro 3.19.0 – 80 – obecné,<br/>4.2.0-18 – obecný pro 4.2.0-42 – obecné,<br/>4.4.0-21 – obecné až 4.4.0 – 148 – obecné,<br/>4.15.0-1023 – Azure do 4.15.0 – 1045 – Azure |
14,04 LTS | [9,30][9.30 UR] | 3.13.0-24 – obecné pro 3.13.0-170 – obecné,<br/>3.16.0-25 – obecný pro 3.16.0-77-Generic,<br/>3.19.0-18 – obecný pro 3.19.0 – 80 – obecné,<br/>4.2.0-18 – obecný pro 4.2.0-42 – obecné,<br/>4.4.0-21 – obecné až 4.4.0 – 148 – obecné,<br/>4.15.0-1023 – Azure do 4.15.0 – 1045 – Azure |
14,04 LTS | [9,29][9.29 UR]| 3.13.0-24 – obecné pro 3.13.0-170 – obecné,<br/>3.16.0-25 – obecný pro 3.16.0-77-Generic,<br/>3.19.0-18 – obecný pro 3.19.0 – 80 – obecné,<br/>4.2.0-18 – obecný pro 4.2.0-42 – obecné,<br/>4.4.0-21 – obecné až 4.4.0 – 148 – obecné,<br/>4.15.0-1023 – Azure do 4.15.0 – 1045 – Azure |
14,04 LTS | [9,28][9.28 UR]| 3.13.0-24 – obecné pro 3.13.0-170 – obecné,<br/>3.16.0-25 – obecný pro 3.16.0-77-Generic,<br/>3.19.0-18 – obecný pro 3.19.0 – 80 – obecné,<br/>4.2.0-18 – obecný pro 4.2.0-42 – obecné,<br/>4.4.0-21 – obecné až 4.4.0 – 148 – obecné,<br/>4.15.0-1023 – Azure do 4.15.0 – 1045 – Azure |
|||
16,04 LTS | [9,31][9.31 UR] | 4.4.0-21 – obecné pro 4.4.0-170 – obecné,<br/>4.8.0-34-obecné až 4.8.0 – 58 – obecné,<br/>4.10.0-14 – obecný pro 4.10.0-42 – obecné,<br/>4.11.0-13 – Obecné pro 4.11.0-14 – obecný,<br/>4.13.0-16 – obecný pro 4.13.0-45 – obecný,<br/>4.15.0-13 – Obecné pro 4.15.0-72 – obecné<br/>4.11.0-1009 – Azure do 4.11.0-1016-Azure<br/>4.13.0 – 1005 – Azure do 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure do 4.15.0-1063 – Azure|
16,04 LTS | [9,30][9.30 UR] | 4.4.0-21 – obecné pro 4.4.0 – 166 – obecné,<br/>4.8.0-34-obecné až 4.8.0 – 58 – obecné,<br/>4.10.0-14 – obecný pro 4.10.0-42 – obecné,<br/>4.11.0-13 – Obecné pro 4.11.0-14 – obecný,<br/>4.13.0-16 – obecný pro 4.13.0-45 – obecný,<br/>4.15.0-13 – Obecné k 4.15.0 – 66 – obecné<br/>4.11.0-1009 – Azure do 4.11.0-1016-Azure<br/>4.13.0 – 1005 – Azure do 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure do 4.15.0-1061 – Azure|
16,04 LTS | [9,29][9.29 UR] | 4.4.0-21 – obecné k 4.4.0-164 – obecné,<br/>4.8.0-34-obecné až 4.8.0 – 58 – obecné,<br/>4.10.0-14 – obecný pro 4.10.0-42 – obecné,<br/>4.11.0-13 – Obecné pro 4.11.0-14 – obecný,<br/>4.13.0-16 – obecný pro 4.13.0-45 – obecný,<br/>4.15.0-13 – Obecné pro 4.15.0-64 – obecné<br/>4.11.0-1009 – Azure do 4.11.0-1016-Azure<br/>4.13.0 – 1005 – Azure do 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure do 4.15.0-1059 – Azure|
16,04 LTS | [9,28][9.28 UR] | 4.4.0-21 – obecný pro 4.4.0 – 159 – obecné,<br/>4.8.0-34-obecné až 4.8.0 – 58 – obecné,<br/>4.10.0-14 – obecný pro 4.10.0-42 – obecné,<br/>4.11.0-13 – Obecné pro 4.11.0-14 – obecný,<br/>4.13.0-16 – obecný pro 4.13.0-45 – obecný,<br/>4.15.0-13 – Obecné k 4.15.0 – 58 – obecné<br/>4.11.0-1009 – Azure do 4.11.0-1016-Azure<br/>4.13.0 – 1005 – Azure do 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure do 4.15.0-1055 – Azure|
|||
18,04 LTS | [9,31][9.31 UR]| 4.15.0-20 – obecný pro 4.15.0-72 – obecné </br> 4.18.0-13 – Obecné pro 4.18.0-25 – obecné </br> 5.0.0-15 – obecný pro 5.0.0-37 – obecné </br> 5.3.0-19 – Obecné k 5.3.0 – 24 – obecné </br> 4.15.0-1009 – Azure do 4.15.0-1037 – Azure </br> 4.18.0-1006 – Azure až 4.18.0 – 1025 – Azure </br> 5.0.0-1012 – Azure do 5.0.0-1025-Azure </br> 5.3.0-1007 – Azure|
18,04 LTS | [9,30][9.30 UR] | 4.15.0-20 – obecný pro 4.15.0-66 – obecné </br> 4.18.0-13 – Obecné pro 4.18.0-25 – obecné </br> 5.0.0-15 – obecný pro 5.0.0 – 32 – obecné </br> 4.15.0-1009 – Azure do 4.15.0-1037 – Azure </br> 4.18.0-1006 – Azure až 4.18.0 – 1025 – Azure </br> 5.0.0-1012 – Azure do 5.0.0-1023-Azure|
18,04 LTS | [9,29][9.29 UR] | 4.15.0-20 – obecný pro 4.15.0-62 – obecné </br> 4.18.0-13 – Obecné pro 4.18.0-25 – obecné </br> 5.0.0-15 – obecné až 5.0.0 – 27 – obecné </br> 4.15.0-1009 – Azure do 4.15.0-1037 – Azure </br> 4.18.0-1006 – Azure až 4.18.0 – 1025 – Azure </br> 5.0.0-1012 – Azure do 5.0.0-1018 – Azure|

### <a name="debian-kernel-versions"></a>Verze jádra Debian


**Podporovaná verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
Debian 7 | [9,28][9.28 UR], [9,29][9.29 UR], [9,30][9.30 UR], [9,31][9.31 UR]| 3.2.0-4-amd64 až 3.2.0-6-AMD64, 3.16.0 -0. BPO. 4 – amd64 |
|||
Debian 8 | [9,30][9.30 UR], [9,31][9.31 UR] | 3.16.0-4-amd64 pro 3.16.0-10-AMD64, 4.9.0 -0. BPO. 4-amd64 na 4.9.0 -0. BPO. 11 – amd64 |
Debian 8 | [9,28][9.28 UR], [9,29][9.29 UR] | 3.16.0-4-amd64 pro 3.16.0-10-AMD64, 4.9.0 -0. BPO. 4-amd64 na 4.9.0 -0. BPO. 9-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 podporovaných verzí jádra

**Vydání** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,31][9.31 UR] | Podporují se všechny [zásoby SUSE 12 SP1, SP2, SP3 a SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) .</br></br> 4.4.138-4.7 – Azure na 4.4.180 – 4.31 – Azure,</br>4.12.14-6.3 – Azure na 4.12.14 – 6.29 – Azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,30][9.30 UR] | Podporují se všechny [zásoby SUSE 12 SP1, SP2, SP3 a SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) .</br></br> 4.4.138-4.7 – Azure na 4.4.180 – 4.31 – Azure,</br>4.12.14-6.3 – Azure na 4.12.14 – 6.26 – Azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,29][9.29 UR] | Podporují se všechny [zásoby SUSE 12 SP1, SP2, SP3 a SP4](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) .</br></br> 4.4.138-4.7 – Azure na 4.4.180 – 4.31 – Azure,</br>4.12.14-6.3 – Azure na 4.12.14 – 6.23 – Azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,28][9.28 UR] | SP1 3.12.49-11 – výchozí hodnota pro 3.12.74-60.64.40 – výchozí</br></br> SP1 (LTSS) 3.12.74-60.64.45-default na 3.12.74-60.64.118 – výchozí</br></br> SP2 4.4.21-69-default až 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-default na 4.4.121-92.117 – výchozí</br></br>SP3 4.4.73-5 – výchozí hodnota pro 4.4.180-94.100 – výchozí</br></br>SP3 4.4.138 – 4.7 – Azure do 4.4.180 – 4.31 – Azure</br></br>SP4 4.12.14-94.41-default on 4.12.14-95.29-default</br>SP4 4.12.14-6.3 – Azure na 4.12.14 – 6.23 – Azure |


## <a name="linux-file-systemsguest-storage"></a>Systémy souborů Linux/hostované úložiště

**Komponenta** | **Podporuje se**
--- | ---
Systémy souborů | ext3, ext4, XFS
Správce svazků | – LVM je podporováno.<br/> –/Boot v LVM se podporuje z [kumulativní aktualizace 31](https://support.microsoft.com/help/4478871/) (verze 9,20 služby mobility) a vyšší. Není podporovaný ve starších verzích služby mobility.<br/> -Více disků s operačním systémem se nepodporuje.
Zařízení úložiště paravirtualizovanými | Zařízení exportovaná paravirtualizovanými ovladači se nepodporují.
Blokové vstupně-výstupní operace s více frontami | Není podporováno.
Fyzické servery s řadičem úložiště HP CCISS | Není podporováno.
Konvence pojmenování zařízení/přípojných bodů | Název zařízení nebo přípojný bod by měl být jedinečný.<br/> Zajistěte, aby v žádném ze dvou zařízení/přípojných bodů nebyly rozlišována velká a malá písmena. Například pojmenování zařízení pro stejný virtuální počítač jako *zařízení1* a *zařízení1* se nepodporuje.
Adresáře | Pokud používáte verzi služby mobility, která je starší než verze 9,20 (vydaná v [kumulativní aktualizaci 31](https://support.microsoft.com/help/4478871/)), platí tato omezení:<br/><br/> – Tyto adresáře (Pokud se nastavují jako samostatné oddíly/souborové systémy) musí být na stejném disku s operačním systémem na zdrojovém serveru:/(root),/Boot,/usr,/usr/local,/var,/etc.</br> – Adresář/Boot by měl být na disku a nesmí být svazkem LVM.<br/><br/> Z verze 9,20 a vyšší se tato omezení nevztahují. 
Spouštěcí adresář | – Spouštěcí disky nesmí být ve formátu oddílu GPT. Toto je omezení architektury Azure. Disky GPT jsou podporovány jako datové disky.<br/><br/> Víc spouštěcích disků na virtuálním počítači se nepodporuje.<br/><br/> –/Boot na svazku LVM na více než jednom disku se nepodporuje.<br/> – Počítač bez spouštěcího disku nejde replikovat.
Požadavky na volné místo| 2 GB na oddílu/root <br/><br/> 250 MB v instalační složce
XFSv5 | XFSv5 funkce v systémech souborů XFS, jako je kontrolní součet metadat, jsou podporované (služba mobility verze 9,10 a vyšší).<br/> K zkontrolování XFS nadbloku oddílu použijte nástroj xfs_info. Pokud je `ftype` nastavené na 1, funkce XFSv5 se používají.
BTRFS | BTRFS se podporuje z [kumulativní aktualizace 34](https://support.microsoft.com/help/4490016) (verze 9,22 služby mobility) a vyšší. BTRFS se nepodporuje v těchto případech:<br/><br/> – Po povolení ochrany se změní dílčí svazek BTRFS systému souborů.</br> – Systém souborů BTRFS je rozložen na více disků.</br> – Systém souborů BTRFS podporuje RAID.

## <a name="vmdisk-management"></a>Správa virtuálních počítačů a disků

**Akce** | **Podrobnosti**
--- | ---
Změna velikosti disku na replikovaném virtuálním počítači | Podporováno ve zdrojovém virtuálním počítači před převzetím služeb při selhání přímo ve vlastnostech virtuálního počítače. Není nutné zakázat nebo znovu povolit replikaci.<br/><br/> Pokud po převzetí služeb při selhání změníte zdrojový virtuální počítač, změny se nezachytí.<br/><br/> Pokud změníte velikost disku na virtuálním počítači Azure po převzetí služeb při selhání, Site Recovery při navrácení služeb po obnovení vytvoří nový virtuální počítač s aktualizacemi.
Přidat disk na replikovaný virtuální počítač | Není podporováno.<br/> Zakažte replikaci pro virtuální počítač, přidejte disk a pak znovu povolte replikaci.

## <a name="network"></a>Network (Síť)

**Komponenta** | **Podporuje se**
--- | ---
Seskupování síťových adaptérů hostitele | Podporováno pro virtuální počítače VMware. <br/><br/>Není podporováno pro replikaci fyzického počítače.
Síť VLAN sítě hostitele | Ano.
Síť IPv4 hostitele | Ano.
Síť IPv6 hostitele | Ne.
Seskupování síťových adaptérů hosta/serveru | Ne.
Síť IPv4 hosta/serveru | Ano.
Síť s protokolem IPv6 Host/Server | Ne.
Statická IP adresa sítě hosta/serveru (Windows) | Ano.
Statická IP adresa sítě hosta nebo serveru (Linux) | Ano. <br/><br/>Virtuální počítače jsou nakonfigurovány na používání protokolu DHCP při navrácení služeb po obnovení.
Síť s více síťovými kartami Host/Server | Ano.


## <a name="azure-vm-network-after-failover"></a>Síť virtuálních počítačů Azure (po převzetí služeb při selhání)

**Komponenta** | **Podporuje se**
--- | ---
Azure ExpressRoute | Ano
ILB | Ano
ELB | Ano
Azure Traffic Manager | Ano
Více síťových karet | Ano
Vyhrazená IP adresa | Ano
IPv4 | Ano
Zachovat zdrojovou IP adresu | Ano
Koncové body služby virtuální sítě Azure<br/> | Ano
Urychlení sítě | Ne

## <a name="storage"></a>Storage
**Komponenta** | **Podporuje se**
--- | ---
Dynamický disk | Disk s operačním systémem musí být základní disk. <br/><br/>Datové disky můžou být dynamické disky.
Konfigurace disku Docker | Ne
Host NFS | Ano pro VMware<br/><br/> Ne pro fyzické servery
SÍŤ SAN hostitele (iSCSI/FC) | Ano
Host vSAN | Ano pro VMware<br/><br/> Není k dispozici pro fyzické servery
Funkce Multipath (MPIO) hostitele | Ano, Testováno pomocí Microsoft DSM, EMC PowerPath 5,7 SP4, EMC PowerPath DSM pro CLARiiON
Virtuální svazky hostitele (VVols) | Ano pro VMware<br/><br/> Není k dispozici pro fyzické servery
VMDK nebo server typu Host | Ano
Disk sdíleného clusteru Host/Server | Ne
Zašifrovaný disk hosta/Server | Ne
Host/Server NFS NFS | Ne
ISCSI Host/Server | Migrace – Ano<br/>V případě zotavení po havárii – technologie iSCSI navrácení služeb po obnovení jako připojeného disku k virtuálnímu počítači.
Host/server SMB 3,0 | Ne
Host/Server – RDM | Ano<br/><br/> Není k dispozici pro fyzické servery
Disk hosta/Server > 1 TB | Ano, disk musí být větší než 1024 MB.<br/><br/>Až 8 192 GB při replikaci do spravovaných disků (9,26 verze a vyšší)<br></br> Až 4 095 GB při replikaci do účtů úložiště
Disk hosta/Server s velikostí logického sektoru 4K a 4k | Ne
Disk hosta/serveru s velikostí logického sektoru 4K a 512-byte | Ne
Svazek typu Host/Server s prokládaným diskem > 4 TB <br/><br/>Správa logických svazků (LVM)| Ano
Host/Server – prostory úložiště | Ne
Host/Server – Hot přidat/odebrat disk | Ne
Host/Server – vyloučit disk | Ano
Funkce Multipath Host/Server (MPIO) | Ne
Oddíly GPT/Server GPT | Z [kumulativní aktualizace 37](https://support.microsoft.com/help/4508614/) (verze 9,25 služby mobility) (verze) a vyšší je podporované pět oddílů. Dříve se podporovaly čtyři.
ReFS | Odolný systém souborů je podporován se službou mobility verze 9,23 nebo vyšší.
Spuštění hosta/serveru EFI/UEFI | – Podporováno pro Windows Server 2012 nebo novější, SLES 12 SP4 a RHEL 8,0 s agentem mobility verze 9,30 a vyšší<br/> -Typ spouštění zabezpečeného rozhraní UEFI není podporován. 

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
Geograficky redundantní úložiště s přístupem pro čtení | Ano
Studené úložiště | Ne
Horké úložiště| Ne
Objekty blob bloku | Ne
Šifrování v Rest (SSE)| Ano
Šifrování v klidovém případě (CMK)| Ne
Premium Storage | Ano
Služba import/export | Ne
Azure Storage brány firewall pro virtuální sítě | Ano.<br/> Nakonfigurováno na cílovém účtu úložiště nebo úložiště mezipaměti (používá se k ukládání dat replikace).
Účty úložiště pro obecné účely v2 (horká a studená úroveň) | Ano (cena za transakce je podstatně vyšší pro V2 v porovnání s V1)

## <a name="azure-compute"></a>Výpočetní prostředí Azure

**Funkce** | **Podporuje se**
--- | ---
Skupiny dostupnosti | Ano
Zóny dostupnosti | Ne
ZDROJ | Ano
Spravované disky | Ano

## <a name="azure-vm-requirements"></a>Požadavky na virtuální počítače Azure

Místní virtuální počítače replikované do Azure musí splňovat požadavky na virtuální počítače Azure shrnuté v této tabulce. Když Site Recovery spustí kontrolu předpokladů pro replikaci, tato akce se nezdaří, pokud nejsou splněné některé z těchto požadavků.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Hostovaný operační systém | Ověřte [podporované operační systémy](#replicated-machines) pro replikované počítače. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Architektura hostovaného operačního systému | 64-bit. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Velikost disku operačního systému | Až 2 048 GB. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Počet disků operačního systému | 1\. místo | Pokud je tato operace Nepodporovaná, ověřte chybu.
Počet datových disků | 64 nebo méně. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Velikost datového disku | Až 8 192 GB při replikaci na spravovaný disk (9,26 verze a vyšší)<br></br>Až 4 095 GB při replikaci do účtu úložiště| Pokud je tato operace Nepodporovaná, ověřte chybu.
Síťové adaptéry | Podporuje se několik adaptérů. |
Sdílené VHD | Není podporováno. | Pokud je tato operace Nepodporovaná, ověřte chybu.
Disk FC | Není podporováno. | Pokud je tato operace Nepodporovaná, ověřte chybu.
BitLocker | Není podporováno. | Před povolením replikace pro počítač musí být BitLocker zakázán. |
název virtuálního počítače | Od 1 do 63 znaků.<br/><br/> Pouze písmena, číslice a pomlčky.<br/><br/> Název počítače musí začínat a končit písmenem nebo číslicí. |  Aktualizujte hodnotu ve vlastnostech počítače v Site Recovery.

## <a name="resource-group-limits"></a>Omezení skupiny prostředků

Informace o počtu virtuálních počítačů, které se dají chránit v rámci jedné skupiny prostředků, najdete v článku [limity a kvóty předplatného](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#resource-group-limits) .

## <a name="churn-limits"></a>Omezení četnosti změn

Následující tabulka obsahuje omezení služby Azure Site Recovery. 
- Tato omezení vycházejí z našich testů, ale nevztahují se na všechny možné kombinace v/v aplikace.
- Skutečné výsledky se můžou lišit v závislosti na kombinaci vstupně-výstupních operací vaší aplikace.
- Pro dosažení nejlepších výsledků důrazně doporučujeme, abyste spustili [nástroj Plánovač nasazení](site-recovery-deployment-planner.md)a prováděli rozsáhlé testování aplikací pomocí testovacího převzetí služeb při selhání, abyste získali skutečný přehled o výkonu vaší aplikace.

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
Četnost změn dat ve špičce napříč všemi disky virtuálního počítače | 54 MB/s
Maximální četnost změn dat za den s podporou procesového serveru | 2 TB

- Toto jsou průměrné hodnoty za předpokladu, že se vstupně-výstupní operace z 30 % překrývají.
- Služba Site Recovery je schopna zpracovávat větší propustnost v závislosti na poměru překrývání, větší velikosti zápisů a skutečného chování vstupně-výstupních operací úloh.
- Tato čísla předpokládají typické nevyřízené položky přibližně pět minut. To znamená, že zpracování nahrávaných dat a vytvoření bodu obnovení proběhne do pěti minut od nahrání.

## <a name="vault-tasks"></a>Úlohy trezoru

**Akce** | **Podporuje se**
--- | ---
Přesunout trezor mezi skupinami prostředků | Ne
Přesun trezoru v rámci předplatných a mezi nimi | Ne
Přesunutí úložiště, sítě, virtuálních počítačů Azure napříč skupinami prostředků | Ne
Přesuňte úložiště, síť, virtuální počítače Azure v rámci i napříč předplatnými. | Ne


## <a name="obtain-latest-components"></a>Získat nejnovější součásti

**Název** | **Popis** | **Podrobnosti**
--- | --- | ---
Konfigurační server | Místně nainstalované.<br/> Koordinuje komunikaci mezi místními servery VMware nebo fyzickými počítači a Azure. | - [informace o](vmware-physical-azure-config-process-server-overview.md) konfiguračním serveru.<br/> - [informace o](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) upgradu na nejnovější verzi.<br/> - se [dozvíte o](vmware-azure-deploy-configuration-server.md) nastavení konfiguračního serveru. 
Procesový Server | Obvykle se instaluje na konfigurační server.<br/> Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování a odesílá je do Azure.<br/> Jak vaše nasazení poroste, můžete přidat další procesové servery, které budou zpracovávat větší objemy replikačních přenosů. | - [informace o](vmware-physical-azure-config-process-server-overview.md) procesovém serveru.<br/> - [informace o](vmware-azure-manage-process-server.md#upgrade-a-process-server) upgradu na nejnovější verzi.<br/> - se [dozvíte, jak](vmware-physical-large-deployment.md#set-up-a-process-server) nastavit procesové servery se škálováním na více instancí.
Mobility Service | Nainstalované na virtuálním počítači VMware nebo na fyzických serverech, které chcete replikovat.<br/> Koordinuje replikaci mezi místními servery VMware a fyzickými servery a Azure.| - [Přečtěte si o](vmware-physical-mobility-service-overview.md) službě mobility.<br/> - [informace o](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) upgradu na nejnovější verzi.<br/> 



## <a name="next-steps"></a>Další kroky
[Přečtěte si, jak](tutorial-prepare-azure.md) připravit Azure na zotavení po havárii virtuálních počítačů VMware.


[9.31 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
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

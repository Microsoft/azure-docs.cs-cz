---
title: Matice podpory pro zotavení po havárii virtuálních počítačů Azure mezi oblastmi Azure a Azure Site Recovery | Microsoft Docs
description: Shrnuje požadavky a podporu pro zotavení po havárii virtuálních počítačů Azure z jedné oblasti do druhé s Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/22/2019
ms.author: raynew
ms.openlocfilehash: 87362b785ecbb17c5a49a8c087b6a70715480877
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679383"
---
# <a name="support-matrix-for-replicating-azure-vms-from-one-region-to-another"></a>Matice podpory pro replikaci virtuálních počítačů Azure z jedné oblasti do jiné

Tento článek shrnuje podporu a požadavky při zotavení po havárii virtuálních počítačů Azure z jedné oblasti Azure do jiné pomocí služby [Azure Site Recovery](site-recovery-overview.md) .


## <a name="deployment-method-support"></a>Podpora metody nasazení

**Nasazení** |  **Podpora**
--- | ---
**Azure Portal** | Podporuje se.
**PowerShell** | Podporuje se. [Víc se uč](azure-to-azure-powershell.md)
**REST API** | Podporuje se.
**Rozhraní příkazového řádku** | Aktuálně není podporováno


## <a name="resource-support"></a>Podpora prostředků

**Akce prostředku** | **Podrobnosti**
--- | --- | ---
**Přesun trezorů mezi skupinami prostředků** | Nepodporuje se
**Přesun prostředků COMPUTE/Storage/sítě mezi skupinami prostředků** | Nepodporuje se.<br/><br/> Pokud po replikaci virtuálního počítače přesunete virtuální počítač nebo přidružené součásti, jako je úložiště nebo síť, musíte pro tento virtuální počítač Zakázat a znovu povolit replikaci.
**Replikace virtuálních počítačů Azure z jednoho předplatného do jiného pro zotavení po havárii** | Podporováno v rámci stejného Azure Active Directory tenanta.
**Migrace virtuálních počítačů napříč oblastmi v rámci podporovaných geografických clusterů (v rámci a napříč předplatnými)** | Podporováno v rámci stejného Azure Active Directory tenanta.
**Migrace virtuálních počítačů v rámci stejné oblasti** | Nepodporuje se.

## <a name="region-support"></a>Podpora oblastí

Virtuální počítače můžete replikovat a obnovovat mezi dvěma oblastmi v rámci stejného geografického clusteru. Geografické clustery jsou definovány pro udržení latence a suverenity dat.


**Geografický cluster** | **Oblasti Azure**
-- | --
Používaný | Kanada – východ, Kanada – střed, Střed USA – jih, Středozápadní USA, Východní USA, Východní USA 2, Západní USA, Západní USA 2, Střed USA, Střed USA – sever
Evropa | Velká Británie – západ, Velká Británie – jih, Severní Evropa, Západní Evropa, Francie – střed, Francie – jih, Jižní Afrika – západ, Jižní Afrika – sever
Asie | Jižní Indie, Střed Indie, Západní Indie, jihovýchodní Asie, Východní Asie, Japonsko – východ, Japonsko – západ, Korea – střed, Korea – jih, střed USA (Spojené arabské emiráty), Spojené arabské emiráty sever
Austrálie   | Austrálie – východ, Austrálie – jihovýchod, Austrálie – střed, Austrálie – střed 2
Azure Government    | US GOV) – Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, US DOD – východ, US DOD – střed 
Německo | Německo – střed, Německo – severovýchod
Čína | Čína – východ, Čína – sever, Čína North2, Čína – východ 2

>[!NOTE]
>
> - Pro oblast **Brazílie – jih**můžete replikovat a přenášet služby při selhání do těchto oblastí: Střed USA – jih, Středozápadní USA, Východní USA, Východní USA 2, Západní USA, Západní USA 2 a Střed USA – sever.
> - Brazílie – jih se dá použít jenom jako zdrojová oblast, ze které se můžou virtuální počítače replikovat pomocí Site Recovery. Nemůže fungovat jako cílová oblast. Důvodem je příčiny potíží s latencí z důvodu zeměpisné vzdálenosti.
> - Můžete pracovat v oblastech, pro které máte odpovídající přístup.
> - Pokud se v oblasti, ve které chcete vytvořit trezor, nezobrazí, ujistěte se, že vaše předplatné má přístup k vytváření prostředků v této oblasti.
> - Pokud při povolování replikace nevidíte oblast v rámci geografického clusteru, ujistěte se, že vaše předplatné má oprávnění k vytváření virtuálních počítačů v této oblasti.



## <a name="cache-storage"></a>Úložiště mezipaměti

Tato tabulka shrnuje podporu účtu úložiště mezipaměti používaného Site Recovery během replikace.

**Nastavení** | **Podpora** | **Podrobnosti**
--- | --- | ---
Účty úložiště pro obecné účely v2 (horká a studená vrstva) | Podporováno | Použití GPv2 se nedoporučuje, protože náklady na transakce pro v2 jsou podstatně vyšší než V1 účty úložiště.
Azure Storage brány firewall pro virtuální sítě  | Podporováno | Pokud používáte účet úložiště mezipaměti s povolenou bránou firewall nebo cílový účet úložiště, zajistěte, abyste [povolili důvěryhodné služby Microsoftu](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).


## <a name="replicated-machine-operating-systems"></a>Operační systémy replikovaných počítačů

Site Recovery podporuje replikaci virtuálních počítačů Azure s operačními systémy uvedenými v této části.

### <a name="windows"></a>Windows

**Operační systém** | **Podrobnosti**
--- | ---
Windows Server. 2019 | Jádro serveru, server s desktopovým prostředím
Windows Server 2016  | Jádro serveru, server s desktopovým prostředím
Windows Server 2012 R2 |
Windows Server 2012 |
Windows Server 2008 R2 | Spuštění verze SP1 nebo novější
Windows 10 (x64) |
Windows 8.1 (x64) |
Windows 8 (x64) |
Windows 7 (x64) | Používání SP1 nebo novější verze (Windows 7 RTM se nepodporuje)

#### <a name="linux"></a>Linux

**Operační systém** | **Podrobnosti**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6  
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6
Ubuntu 14.04 LTS Server | [Podporované verze jádra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS Server | [Podporovaná verze jádra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Ubuntu servery s použitím ověřování a přihlášení pomocí hesla a balíčku Cloud-init ke konfiguraci cloudových virtuálních počítačů můžou mít při převzetí služeb při selhání zakázané přihlášení (v závislosti na konfiguraci cloudinit). Přihlášení založené na heslech na virtuálním počítači můžete znovu povolit tím, že resetujete heslo z nabídky Nastavení > řešení potíží s >em (u virtuálního počítače, u kterého došlo k převzetí služeb při selhání v Azure Portal.
Debian 7 | [Podporované verze jádra](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Podporované verze jádra](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Podporované verze jádra)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> Upgrade replikačních počítačů z aktualizace SP3 na verzi SP4 není podporován. Pokud byl replikovaný počítač upgradován, je nutné zakázat replikaci a po upgradu znovu povolit replikaci.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6<br/><br/> Spuštění jádra kompatibilního s Red Hat nebo nedělitelné podnikové jádro verze 3, 4 & 5 (UEK3, UEK4, UEK5) 


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra Ubuntu pro virtuální počítače Azure

**Předběžné** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
14,04 LTS | 9,27 | 3.13.0-24 – obecné pro 3.13.0-170 – obecné,<br/>3.16.0-25 – obecný pro 3.16.0-77-Generic,<br/>3.19.0-18 – obecný pro 3.19.0 – 80 – obecné,<br/>4.2.0-18 – obecný pro 4.2.0-42 – obecné,<br/>4.4.0-21 – obecné až 4.4.0 – 148 – obecné,<br/>4.15.0-1023 – Azure do 4.15.0 – 1045 – Azure |
14,04 LTS | 9,26 | 3.13.0-24 – obecné pro 3.13.0-170 – obecné,<br/>3.16.0-25 – obecný pro 3.16.0-77-Generic,<br/>3.19.0-18 – obecný pro 3.19.0 – 80 – obecné,<br/>4.2.0-18 – obecný pro 4.2.0-42 – obecné,<br/>4.4.0-21 – obecné až 4.4.0 – 148 – obecné,<br/>4.15.0-1023 – Azure do 4.15.0 – 1045 – Azure |
14,04 LTS | 9,25 | 3.13.0-24 – obecné pro 3.13.0-169 – obecné,<br/>3.16.0-25 – obecný pro 3.16.0-77-Generic,<br/>3.19.0-18 – obecný pro 3.19.0 – 80 – obecné,<br/>4.2.0-18 – obecný pro 4.2.0-42 – obecné,<br/>4.4.0-21 – obecné pro 4.4.0-146 – obecné,<br/>4.15.0-1023 – Azure do 4.15.0 – 1042 – Azure |
14,04 LTS | 9,24 | 3.13.0-24 – obecné až 3.13.0 – 167 – obecné,<br/>3.16.0-25 – obecný pro 3.16.0-77-Generic,<br/>3.19.0-18 – obecný pro 3.19.0 – 80 – obecné,<br/>4.2.0-18 – obecný pro 4.2.0-42 – obecné,<br/>4.4.0-21 – obecné pro 4.4.0-143 – obecné,<br/>4.15.0-1023 – Azure do 4.15.0 – 1040 – Azure |
|||
16,04 LTS | 9,27 | 4.4.0-21 – obecné pro 4.4.0-154 – obecné,<br/>4.8.0-34-obecné až 4.8.0 – 58 – obecné,<br/>4.10.0-14 – obecný pro 4.10.0-42 – obecné<br/>4.11.0-13 – Obecné pro 4.11.0-14 – obecný,<br/>4.13.0-16 – obecný pro 4.13.0-45 – obecný,<br/>4.15.0-13 – Obecné pro 4.15.0-54 – obecné<br/>4.11.0-1009 – Azure do 4.11.0-1016-Azure<br/>4.13.0 – 1005 – Azure do 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure do 4.15.0 – 1050 – Azure|
16,04 LTS | 9,26 | 4.4.0-21 – obecné až 4.4.0 – 148 – obecné,<br/>4.8.0-34-obecné až 4.8.0 – 58 – obecné,<br/>4.10.0-14 – obecný pro 4.10.0-42 – obecné<br/>4.11.0-13 – Obecné pro 4.11.0-14 – obecný,<br/>4.13.0-16 – obecný pro 4.13.0-45 – obecný,<br/>4.15.0-13 – obecný pro 4.15.0-50 – obecné<br/>4.11.0-1009 – Azure do 4.11.0-1016-Azure<br/>4.13.0 – 1005 – Azure do 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure do 4.15.0-1045 – Azure|
16,04 LTS | 9,25 | 4.4.0-21 – obecné pro 4.4.0-146 – obecné,<br/>4.8.0-34-obecné až 4.8.0 – 58 – obecné,<br/>4.10.0-14 – obecný pro 4.10.0-42 – obecné,<br/>4.11.0-13 – Obecné pro 4.11.0-14 – obecný,<br/>4.13.0-16 – obecný pro 4.13.0-45 – obecný,<br/>4.15.0-13 – obecný pro 4.15.0-48 – obecné<br/>4.11.0-1009 – Azure do 4.11.0-1016-Azure<br/>4.13.0 – 1005 – Azure do 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure do 4.15.0-1042 – Azure|
16,04 LTS | 9,24 | 4.4.0-21 – obecné pro 4.4.0-143 – obecné,<br/>4.8.0-34-obecné až 4.8.0 – 58 – obecné,<br/>4.10.0-14 – obecný pro 4.10.0-42 – obecné,<br/>4.11.0-13 – Obecné pro 4.11.0-14 – obecný,<br/>4.13.0-16 – obecný pro 4.13.0-45 – obecný,<br/>4.15.0-13 – Obecné pro 4.15.0-46 – obecné<br/>4.11.0-1009 – Azure do 4.11.0-1016-Azure<br/>4.13.0 – 1005 – Azure do 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure do 4.15.0-1040 – Azure|

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra Debian pro virtuální počítače Azure

**Předběžné** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
Debian 7 | 9.24,9.25,9.26,9.27 | 3.2.0-4-amd64 až 3.2.0-6-AMD64, 3.16.0 -0. BPO. 4 – amd64 |
|||
Debian 8 | 9,27 | 3.16.0-4-amd64 až 3.16.0-9-AMD64, 4.9.0 -0. BPO. 4-amd64 na 4.9.0 -0. BPO. 9-amd64 |
Debian 8 | 9,25, 9,26 | 3.16.0-4-amd64 až 3.16.0-8-AMD64, 4.9.0 -0. BPO. 4-amd64 na 4.9.0 -0. BPO. 8-amd64 |
Debian 8 | 9,24 | 3.16.0-4-amd64 až 3.16.0-7-AMD64, 4.9.0 -0. BPO. 4-amd64 na 4.9.0 -0. BPO. 8 – amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra SUSE Linux Enterprise Server 12 pro virtuální počítače Azure

**Předběžné** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,27 | SP1 3.12.49-11 – výchozí hodnota pro 3.12.74-60.64.40 – výchozí</br></br> SP1 (LTSS) 3.12.74-60.64.45-default na 3.12.74-60.64.115 – výchozí</br></br> SP2 4.4.21-69-default až 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-default na 4.4.121-92.114 – výchozí</br></br>SP3 4.4.73-5 – výchozí hodnota pro 4.4.180-94.97 – výchozí</br></br>SP3 4.4.138 – 4.7 – Azure do 4.4.180 – 4.31 – Azure</br></br>SP4 4.12.14-94.41-default on 4.12.14-95.19-default</br>SP4 4.12.14-6.3 – Azure na 4.12.14 – 6.15 – Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,26 | SP1 3.12.49-11 – výchozí hodnota pro 3.12.74-60.64.40 – výchozí</br></br> SP1 (LTSS) 3.12.74-60.64.45-default na 3.12.74-60.64.110 – výchozí</br></br> SP2 4.4.21-69-default až 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-default na 4.4.121-92.109 – výchozí</br></br>SP3 4.4.73-5 – výchozí hodnota pro 4.4.178-94.91 – výchozí</br></br>SP3 4.4.138 – 4.7 – Azure do 4.4.178 – tedy 4,28 – Azure</br></br>SP4 4.12.14-94.41-default on 4.12.14-95.16-default</br>SP4 4.12.14-6.3 – Azure až 4.12.14-\ \ Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,25 | SP1 3.12.49-11 – výchozí hodnota pro 3.12.74-60.64.40 – výchozí</br></br> SP1 (LTSS) 3.12.74-60.64.45-default na 3.12.74-60.64.107 – výchozí</br></br> SP2 4.4.21-69-default až 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-default na 4.4.121-92.104 – výchozí</br></br>SP3 4.4.73-5 – výchozí hodnota pro 4.4.176-94.88 – výchozí</br></br>SP3 4.4.138 – 4.7 – Azure do 4.4.176 – 4.25 – Azure</br></br>SP4 4.12.14-94.41-default on 4.12.14-95.13-default</br>SP4 4.12.14-6.3 – Azure až 4.12.14-\ \ Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,24 | SP1 3.12.49-11 – výchozí hodnota pro 3.12.74-60.64.40 – výchozí</br></br> SP1 (LTSS) 3.12.74-60.64.45-default na 3.12.74-60.64.107 – výchozí</br></br> SP2 4.4.21-69-default až 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-default na 4.4.121-92.104 – výchozí</br></br>SP3 4.4.73-5 – výchozí hodnota pro 4.4.176-94.88 – výchozí</br></br>SP4 4.12.14-94.41-default on 4.12.14-95.13-default |


## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replikované počítače – systém souborů Linux/úložiště hostů

* Souborové systémy: ext3, EXT4, ReiserFS (jenom SUSE Linux Enterprise Server), XFS, BTRFS
* Správce svazků: LVM2
* Multipath software: Mapovač zařízení


## <a name="replicated-machines---compute-settings"></a>Replikované počítače – nastavení výpočtů

**Nastavení** | **Podpora** | **Podrobnosti**
--- | --- | ---
Size | Všechny velikosti virtuálních počítačů Azure s minimálně 2 jádry procesoru a 1 GB paměti RAM | Ověřte [velikosti virtuálních počítačů Azure](../virtual-machines/windows/sizes.md).
Skupiny dostupnosti | Podporováno | Pokud povolíte replikaci pro virtuální počítač Azure s výchozími možnostmi, vytvoří se skupina dostupnosti automaticky na základě nastavení zdrojové oblasti. Tato nastavení můžete změnit.
Zóny dostupnosti | Podporováno |
Zvýhodněné hybridní využití (centrum) | Podporováno | Pokud má zdrojový virtuální počítač povolenou licenci na rozbočovač, používá testovací převzetí služeb při selhání nebo převzetí služeb při selhání virtuální počítač taky licenci centra.
Škálovací sady virtuálních počítačů | Nepodporuje se |
Image Galerie Azure – Publikováno Microsoftem | Podporováno | Podporováno, pokud virtuální počítač běží v podporovaném operačním systému.
Image Galerie Azure – Publikováno třetí stranou | Podporováno | Podporováno, pokud virtuální počítač běží v podporovaném operačním systému.
Vlastní image – Publikováno třetí stranou | Podporováno | Podporováno, pokud virtuální počítač běží v podporovaném operačním systému.
Virtuální počítače migrované pomocí Site Recovery | Podporováno | Pokud se virtuální počítač VMware nebo fyzický počítač migruje do Azure pomocí Site Recovery, musíte odinstalovat starší verzi služby mobility spuštěnou na počítači a restartovat počítač před tím, než ho budete replikovat do jiné oblasti Azure.
Zásady RBAC | Nepodporuje se | Zásady řízení přístupu na základě role (RBAC) na virtuálních počítačích se nereplikují do virtuálního počítače s podporou převzetí služeb při selhání v cílové oblasti.
Rozšíření | Nepodporuje se | Rozšíření se nereplikují do virtuálního počítače s podporou převzetí služeb při selhání v cílové oblasti. Po převzetí služeb při selhání je potřeba ji nainstalovat ručně.

## <a name="replicated-machines---disk-actions"></a>Replikované počítače – diskové akce

**Akce** | **Podrobnosti**
-- | ---
Změna velikosti disku na replikovaném virtuálním počítači | Podporováno
Přidání disku do replikovaného virtuálního počítače | Podporováno

## <a name="replicated-machines---storage"></a>Replikované počítače – úložiště

Tato tabulka shrnuje podporu pro disk s operačním systémem Azure VM, datový disk a dočasný disk.

- Abyste se vyhnuli problémům s výkonem, je důležité sledovat omezení počtu disků virtuálních počítačů a cíle pro virtuální počítače se systémem [Linux](../virtual-machines/linux/disk-scalability-targets.md) a [Windows](../virtual-machines/windows/disk-scalability-targets.md) .
- Pokud nasadíte s výchozím nastavením, Site Recovery na základě nastavení zdroje automaticky vytvoří disky a účty úložiště.
- Pokud přizpůsobíte, ujistěte se, že dodržujete pokyny.

**Komponenta** | **Podpora** | **Podrobnosti**
--- | --- | ---
Maximální velikost disku s operačním systémem | 2048 GB | [Přečtěte si další informace](../virtual-machines/windows/managed-disks-overview.md) o discích virtuálních počítačů.
Dočasný disk | Nepodporuje se | Dočasný disk je vždy vyloučen z replikace.<br/><br/> Na dočasném disku neukládejte žádná trvalá data. [Další informace](../virtual-machines/windows/managed-disks-overview.md).
Maximální velikost datového disku | 8192 GB pro Managed disks<br></br>4095 GB pro nespravované disky|
Minimální velikost datového disku | Neexistují žádná omezení pro nespravované disky. 2 GB pro spravované disky | 
Maximální počet datových disků | Až 64, v souladu s podporou konkrétní velikosti virtuálního počítače Azure | [Přečtěte si další informace](../virtual-machines/windows/sizes.md) o velikostech virtuálních počítačů.
Rychlost změny datového disku | Maximálně 10 MB/s na disk pro Premium Storage. Maximálně 2 MB/s na disk pro úložiště úrovně Standard. | Pokud je průměrná rychlost změny dat na disku nepřetržitě vyšší než maximální, replikace se nezachytí.<br/><br/>  Pokud je ale maximum překročeno zřídka, replikace může zachytit, ale mírně zpožděné body obnovení.
Datový disk – standardní účet úložiště | Podporováno |
Datový disk – účet Premium Storage | Podporováno | Pokud má virtuální počítač disky se systémem Premium a standardními účty úložiště, můžete pro každý disk vybrat jiný cílový účet úložiště, abyste měli jistotu, že máte stejnou konfiguraci úložiště v cílové oblasti.
Spravovaný disk – Standard | Podporováno v oblastech Azure, ve kterých je podpora Azure Site Recovery podporovaná. |
Spravovaný disk – Premium | Podporováno v oblastech Azure, ve kterých je podpora Azure Site Recovery podporovaná. |
SSD úrovně Standard | Podporováno |
Redundance | LRS a GRS jsou podporovány.<br/><br/> ZRS se nepodporuje.
Studené a horké úložiště | Nepodporuje se | Disky virtuálních počítačů se nepodporují na studeném a horkém úložišti.
Prostory úložiště | Podporováno |
Šifrování v klidovém prostředí (SSE) | Podporováno | Pro účty úložiště je výchozí nastavení SSE.   
Azure Disk Encryption (ADE) pro operační systém Windows | Podporují se virtuální počítače s povoleným [šifrováním pomocí aplikace Azure AD](https://aka.ms/ade-aad-app) . |
Azure Disk Encryption (ADE) pro Linux OS | Nepodporuje se |
Horké přidání | Podporováno | Povolení replikace pro datový disk, který přidáte do replikovaného virtuálního počítače Azure, se podporuje pro virtuální počítače, které používají spravované disky.
Odstranit disk z provozu | Nepodporuje se | Pokud na virtuálním počítači odeberete datový disk, budete muset zakázat replikaci a znovu povolit replikaci pro virtuální počítač.
Vyloučení disku | Pracovníky. Ke konfiguraci je nutné použít [PowerShell](azure-to-azure-exclude-disks.md) . |  Dočasné disky jsou ve výchozím nastavení vyloučené.
Prostory úložiště s přímým přístupem  | Podporováno pro body obnovení konzistentní vzhledem k selhání. Body obnovení konzistentní vzhledem k aplikacím se nepodporují. |
Souborový server se škálováním na více instancí  | Podporováno pro body obnovení konzistentní vzhledem k selhání. Body obnovení konzistentní vzhledem k aplikacím se nepodporují. |
LRS | Podporováno |
GRS | Podporováno |
RA-GRS | Podporováno |
ZRS | Nepodporuje se |
Studené a horké úložiště | Nepodporuje se | Disky virtuálních počítačů se ve studeném a horkém úložišti nepodporují.
Azure Storage brány firewall pro virtuální sítě  | Podporováno | Pokud omezíte přístup k účtu úložiště přes virtuální síť, povolte možnost [Povolit důvěryhodné služby společnosti Microsoft](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Účty úložiště pro obecné účely v2 (horká a studená vrstva) | Ano | Náklady transakce se podstatně zvyšují v porovnání s účty úložiště pro obecné účely v1.

>[!IMPORTANT]
> Abyste se vyhnuli problémům s výkonem, ujistěte se, že je pro virtuální počítače se systémem [Linux](../virtual-machines/linux/disk-scalability-targets.md) nebo [Windows](../virtual-machines/windows/disk-scalability-targets.md) dodržujte škálovatelnost a výkonnostní cíle pro disky virtuálních počítačů. Pokud použijete výchozí nastavení, Site Recovery v závislosti na konfiguraci zdroje vytvoří požadované disky a účty úložiště. Pokud přizpůsobíte a vyberete vlastní nastavení, postupujte podle škálovatelnosti disku a cílů výkonu pro vaše zdrojové virtuální počítače.

## <a name="limits-and-data-change-rates"></a>Limity a míry změny dat

Následující tabulka shrnuje Site Recovery omezení.

- Tato omezení vycházejí z našich testů, ale zjevně nepokrývá všechny možné kombinace I/O aplikací.
- Skutečné výsledky se můžou lišit v závislosti na kombinaci v/v aplikace.
- Existují dvě omezení, která je potřeba zvážit, četnost změn dat na disku a změny dat virtuálních počítačů.
- Pokud například použijete disk Premium P20, jak je popsáno v následující tabulce, Site Recovery může zpracovávat 5 MB změn na disk s maximálním počtem pěti takových disků na jeden virtuální počítač, a to kvůli limitu 25 MB/s celkové četnosti změn na virtuální počítač.

**Cíl úložiště** | **Průměrný počet vstupů a výstupů zdrojového disku** |**Průměrná četnost změn dat zdrojového disku** | **Celková denní četnost změn dat zdrojového disku**
---|---|---|---
Storage úrovně Standard | 8 kB | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 8 kB  | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 16 kB | 4 MB/s |  336 GB na disk
Disk úrovně Premium P10 nebo P15 | 32 kB nebo větší | 8 MB/s | 672 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 8 kB    | 5 MB/s | 421 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 16 kB nebo větší |20 MB/s | 1684 GB na disk

## <a name="replicated-machines---networking"></a>Replikované počítače – sítě
**Nastavení** | **Podpora** | **Podrobnosti**
--- | --- | ---
NIC | Maximální podporovaný počet pro konkrétní velikost virtuálního počítače Azure | Síťové karty se vytvoří, když se virtuální počítač vytvoří během převzetí služeb při selhání.<br/><br/> Počet síťových adaptérů na VIRTUÁLNÍm počítači s podporou převzetí služeb při selhání závisí na počtu síťových adaptérů ve zdrojovém virtuálním počítači, když je replikace povolená. Pokud po povolení replikace přidáte nebo odeberete síťovou kartu, nebude to mít vliv na počet síťových adaptérů v replikovaném virtuálním počítači po převzetí služeb při selhání. Všimněte si také, že pořadí síťových adaptérů po převzetí služeb při selhání není zaručené jako původní pořadí.
Internetový nástroj pro vyrovnávání zatížení | Podporováno | Přidružte předem nakonfigurovaný Nástroj pro vyrovnávání zatížení pomocí skriptu Azure Automation v plánu obnovení.
Interní nástroj pro vyrovnávání zatížení | Podporováno | Přidružte předem nakonfigurovaný Nástroj pro vyrovnávání zatížení pomocí skriptu Azure Automation v plánu obnovení.
Veřejná IP adresa | Podporováno | Přidružte existující veřejnou IP adresu k síťovému rozhraní. Případně vytvořte veřejnou IP adresu a přidružte ji k síťovému rozhraní pomocí skriptu Azure Automation v plánu obnovení.
NSG na síťové kartě | Podporováno | Přidružte NSG k síťovému rozhraní pomocí skriptu Azure Automation v plánu obnovení.
NSG v podsíti | Podporováno | Přidružte NSG k podsíti pomocí skriptu Azure Automation v plánu obnovení.
Rezervovaná (statická) IP adresa | Podporováno | Pokud má síťová karta ve zdrojovém virtuálním počítači statickou IP adresu a cílová podsíť má stejnou IP adresu, je přiřazená k virtuálnímu počítači, u kterého došlo k převzetí služeb při selhání.<br/><br/> Pokud cílová podsíť nemá k dispozici stejnou IP adresu, jedna z dostupných IP adres v podsíti je vyhrazena pro virtuální počítač.<br/><br/> Můžete také zadat pevnou IP adresu a podsíť v nastavení **replikované položky** > **Nastavení** > **výpočetních a síťových** > **síťových rozhraní**.
Dynamická IP adresa | Podporováno | Pokud má síťová karta ve zdroji dynamické přidělování IP adres, síťové rozhraní ve virtuálním počítači převzetí služeb při selhání je ve výchozím nastavení také dynamické.<br/><br/> V případě potřeby ho můžete v případě potřeby upravit na pevně stanovenou IP adresu.
Několik IP adres | Nepodporuje se | Při převzetí služeb při selhání virtuálního počítače, který má síťové rozhraní s více IP adresami, zůstane zachována pouze primární IP adresa síťového adaptéru ve zdrojové oblasti. Pokud chcete přiřadit více IP adres, můžete přidat virtuální počítače do [plánu obnovení](recovery-plan-overview.md) a připojit skript pro přiřazení dalších IP adres k plánu, nebo můžete změnu provést ručně nebo pomocí skriptu po převzetí služeb při selhání. 
Traffic Manager     | Podporováno | Můžete předem nakonfigurovat Traffic Manager tak, aby se provoz směroval do koncového bodu ve zdrojové oblasti v pravidelných intervalech, a v případě převzetí služeb při selhání do koncového bodu v cílové oblasti.
Azure DNS | Podporováno |
Vlastní DNS  | Podporováno |
Neověřený proxy server | Podporováno | [Víc se uč](site-recovery-azure-to-azure-networking-guidance.md)    
Ověřený proxy server | Nepodporuje se | Pokud virtuální počítač používá pro odchozí připojení ověřený proxy server, nedá se replikovat pomocí Azure Site Recovery.    
Připojení VPN typu Site-to-site k místnímu prostředí<br/><br/>(s ExpressRoute nebo bez něj)| Podporováno | Ujistěte se, že jsou udr a skupin zabezpečení sítě nakonfigurované tak, aby se provoz Site Recovery nesměroval do místního prostředí. [Víc se uč](site-recovery-azure-to-azure-networking-guidance.md)    
Připojení virtuální sítě k virtuální síti | Podporováno | [Víc se uč](site-recovery-azure-to-azure-networking-guidance.md)  
Koncové body služby virtuální sítě | Podporováno | Pokud omezíte přístup k virtuální síti k účtům úložiště, ujistěte se, že důvěryhodné služby Microsoftu mají povolený přístup k účtu úložiště.
Akcelerované síťové služby | Podporováno | Na zdrojovém virtuálním počítači musí být povolené urychlené síťové služby. [Další informace](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>Další postup
- Přečtěte si [pokyny k síti](site-recovery-azure-to-azure-networking-guidance.md) pro replikaci virtuálních počítačů Azure.
- Nasazení zotavení po havárii [replikací virtuálních počítačů Azure](site-recovery-azure-to-azure.md).

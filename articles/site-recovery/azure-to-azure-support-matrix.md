---
title: Systém podpory Azure Site Recovery pro zotavení po havárii virtuálních počítačů Azure IaaS mezi oblastmi Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Shrnuje podporované operační systémy a konfigurace pro replikaci virtuálních počítačů Azure (VM) Azure Site Recovery z jedné oblasti do jiného pro potřeby zotavení po havárii.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/12/2018
ms.author: raynew
ms.openlocfilehash: 50f9027301e4a04cd5624deee084429c803e04f4
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342710"
---
# <a name="support-matrix-for-replicating-from-one-azure-region-to-another"></a>Matice podpory pro které se replikují z jedné oblasti Azure do jiné

Tento článek shrnuje podporované konfigurace a komponent, pokud nasazení zotavení po havárii pomocí replikace, převzetí služeb při selhání a obnovení virtuálních počítačů Azure z jedné oblasti Azure do jiného, pomocí [Azure Site Recovery](site-recovery-overview.md) služby.


## <a name="deployment-method-support"></a>Podpora nasazení – metoda

**Metoda nasazení** |  **Podporované / nepodporované**
--- | ---
**Azure Portal** | Podporováno
**PowerShell** | [Replikace z Azure do Azure pomocí Powershellu](azure-to-azure-powershell.md)
**REST API** | Aktuálně se nepodporuje.
**Rozhraní příkazového řádku** | Aktuálně se nepodporuje.


## <a name="resource-support"></a>Podpora prostředků

**Akce prostředku** | **Podrobnosti**
--- | --- | ---
**Přesun trezoru mezi skupinami prostředků** | Nepodporuje se
**Přesunout výpočetní síťi prostředků mezi skupinami prostředků** | Nepodporuje se.<br/><br/> Při přesunutí virtuálního počítače nebo přidružené komponent, jako jsou úložiště nebo sítě po replikaci virtuálního počítače, musíte zakázat a znovu povolte replikaci pro virtuální počítač.
**Replikace virtuálních počítačů Azure z jednoho předplatného do druhého pro zotavení po havárii** | Podporováno ve stejném tenantovi Azure Active Directory.
**Migrace virtuálních počítačů napříč oblastmi v rámci podporovaných geografických clusterů (v rámci a napříč předplatnými)** | Podporováno ve stejném tenantovi Azure Active Directory.
**Migrace virtuálních počítačů ve stejné oblasti** | Nepodporuje se.

## <a name="region-support"></a>Oblasti podpory

Můžete replikovat a obnovovat virtuální počítače mezi všechny dvou oblastí ve stejné zeměpisné clusteru. Geografické clustery jsou definovány dodržujte při tom suverenita a data latence.

**Geografické clusteru** | **Oblasti Azure**
-- | --
Americké | Kanada – východ, Kanada – Jih, střed USA (střed), střed USA – Západ, USA – východ, USA – východ 2, USA – Západ, USA – západ 2, střed USA, USA (střed) – sever
Evropa | Velká Británie – Západ, Velká Británie – Jih, Severní Evropa, západní Evropa, Francie – střed, Francie – jih
Asie | Indie – Jih, střed Indie, jihovýchodní Asie, jihovýchodní Asie, Japonsko – východ, Japonsko – Západ, Korea – střed, Korea – jih
Austrálie   | Austrálie – východ, Austrálie – jihovýchod, Austrálie – střed, Austrálie – střed 2
Azure Government    | Virginie USA (gov), US GOV Iowa, USA (gov) Arizona, US GOV Texas, US DOD – východ, US DOD – střed
Německo | Německo – střed, Německo – severovýchod
Čína | Čína – východ, Čína – sever, Čína North2, Čína – východ 2

>[!NOTE]
>
> Pro oblast Brazílie – jih můžete replikaci a převzetí služeb při selhání pro jeden z následujících akcí: Střed USA – Jih, střed USA – Západ, USA – východ, USA – východ 2, USA – Západ, USA – západ 2 a oblasti střed USA – sever.</br>
> Je třeba poznamenat, Site Recovery je povolena pouze oblasti Brazílie – Jih, který se použije jako zdrojové oblasti, ze které se dají chránit virtuální počítače. Nelze použít jako cíl zotavení po Havárii oblasti pro všechny oblasti Azure, jako jsou střed USA – jih. Důvod je ten latence zjištěnými z důvodu geografické vzdálenosti doporučujeme vybrat oblasti žádné další Ameriky, než Brazílie – jih.  

## <a name="cache-storage"></a>Úložiště mezipaměti

Tato tabulka shrnuje podporu pro účet úložiště mezipaměti, který se používá služba Site Recovery během replikace.

**Nastavení** | **Podpora** | **Podrobnosti**
--- | --- | ---
Obecné účely V2 účty úložiště (horká a studená vrstva přístupu) | Nepodporuje se. | Omezení existuje pro úložiště mezipaměti, protože náklady na transakce pro V2 jsou výrazně vyšší než účtů storage V1.
Pro virtuální sítě brány firewall služby Azure Storage  | Podporováno | Pokud používáte účet úložiště mezipaměti povolena brána firewall nebo cílový účet úložiště, zajistěte, aby ["Povolit důvěryhodné služby Microsoftu"](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).


## <a name="replicated-machine-operating-systems"></a>Operační systémy replikované počítače

Site Recovery podporuje replikaci virtuálních počítačů Azure s operačními systémy, uvedené v této části.

### <a name="windows"></a>Windows

**Operační systém** | **Podrobnosti**
--- | ---
Windows Server 2016  | Jádro serveru, Server s desktopovým prostředím
Windows Server 2012 R2 |
Windows Server 2012 |
Windows Server 2008 R2 | S aktualizací SP1 nebo novější

#### <a name="linux"></a>Linux

**Operační systém** | **Podrobnosti**
--- | ---
Red Hat Enterprise Linux | 6.7 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6  
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6
Ubuntu 14.04 LTS Server | [Verze podporovaných jádra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Server se systémem Ubuntu 16.04 LTS | [Verze podporovaných jádra](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Ubuntu servery s použitím ověřování pomocí hesla a přihlášení a cloud-init balíček ke konfiguraci cloudu virtuálních počítačů, může být deaktivovány převzetí služeb při selhání (v závislosti na konfiguraci cloudinit) přihlášení založené na heslech. Přihlášení pomocí hesla, která může být na virtuálním počítači znovu zapnout resetováním hesla z podpory > Poradce při potížích s > nabídky nastavení (služby se převzaly virtuálního počítače na webu Azure Portal.
Debian 7 | [Verze podporovaných jádra](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Verze podporovaných jádra)](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2 SP3. [Verze podporovaných jádra](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | S AKTUALIZACÍ SP3<br/><br/> Upgrade replikace počítačů z SP3 do SP4 se nepodporuje. Pokud byl upgradován replikovaného počítače, musíte zakázat replikaci a znovu povolte replikaci po upgradu.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5 <br/><br/> Systém Red Hat kompatibilní jádra nebo nedělitelné Enterprise jádra verze 3 (UEK3).


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra Ubuntu pro Azure virtual machines

**Vydání verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
14.04 LTS | 9,20 | 3.13.0-24-Generic k 3.13.0-161-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-138-generic,<br/>4.15.0-1023-Azure k 4.15.0-1030-azure |
14.04 LTS | 9.19 | 3.13.0-24-Generic k 3.13.0-153-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-131-generic |
14.04 LTS | 9.18 | 3.13.0-24-Generic k 3.13.0-151-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-128-generic |
14.04 LTS | 9.17 | 3.13.0-24-Generic k 3.13.0-147-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-124-generic |
|||
16.04 LTS | 9,20 | 4.4.0-21-Generic k 4.4.0-138-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-45-generic,<br/>4.15.0-13-Generic k 4.15.0-38-generic<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1018-azure <br/>4.15.0-1012-Azure k 4.15.0-1030-azure|
16.04 LTS | 9.19 | 4.4.0-21-Generic k 4.4.0-131-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-45-generic,<br/>4.15.0-13-Generic k 4.15.0-30-generic<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1018-azure <br/>4.15.0-1012-Azure k 4.15.0-1019-azure|
16.04 LTS | 9.18 | 4.4.0-21-Generic k 4.4.0-128-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-45-generic,<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1018-azure |
16.04 LTS | 9.17 | 4.4.0-21-Generic k 4.4.0-124-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-41-generic,<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1016-azure |


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra Debian pro Azure virtual machines

**Vydání verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
Debian 7 | 9.17,9.18,9.19,9.20 | 3.2.0-4-amd64 k 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9,20 | 3.16.0-4-amd64 k 3.16.0-7-amd64 4.9.0-0.bpo.4-amd64 k 4.9.0-0.bpo.8-amd64 |
Debian 8 | 9.19 | 3.16.0-4-amd64 k 3.16.0-6-amd64 4.9.0-0.bpo.4-amd64 k 4.9.0-0.bpo.7-amd64 |
Debian 8 | 9.17, 9.18 | 3.16.0-4-amd64 k 3.16.0-6-amd64 4.9.0-0.bpo.4-amd64 k 4.9.0-0.bpo.6-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra operačního systému SUSE Linux Enterprise Server 12 pro virtuální počítače Azure

**Vydání verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | 9,20 | SP1 3.12.49-11-default k 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default k 3.12.74-60.64.107-default</br></br> 4.4.21-69-default SP2 do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default k 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default k 4.4.162-94.69-default |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | 9.19 | SP1 3.12.49-11-default k 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default k 3.12.74-60.64.93-default</br></br> 4.4.21-69-default SP2 do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default k 4.4.121-92.80-default</br></br>SP3 4.4.73-5-default k 4.4.140-94.42-default |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | 9.18 | SP1 3.12.49-11-default k 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default k 3.12.74-60.64.93-default</br></br> 4.4.21-69-default SP2 do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default k 4.4.121-92.80-default</br></br>SP3 4.4.73-5-default k 4.4.138-94.39-default |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | 9.17 | SP1 3.12.49-11-default k 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default k 3.12.74-60.64.88-default</br></br> 4.4.21-69-default SP2 do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default</br></br>SP3 4.4.73-5-default k 4.4.126-94.22-default |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replikované počítače – Linux souboru systému/hostovaného úložiště

* Systémy souborů: ext3 ext4, ReiserFS (pouze operačním systémem Suse Linux Enterprise Server), XFS, BTRFS
* Správce svazků: LVM2
* Software s funkcí Multipath: Mapování zařízení


## <a name="replicated-machines---compute-settings"></a>Replikované počítače – výpočetní nastavení

**Nastavení** | **Podpora** | **Podrobnosti**
--- | --- | ---
Velikost | Libovolná velikost virtuálních počítačů Azure s nejméně 2 jádra procesoru a 1 GB paměti RAM | Ověřte [velikosti virtuálních počítačů Azure](../virtual-machines/windows/sizes.md).
Skupiny dostupnosti | Podporováno | Pokud povolíte replikaci pro virtuální počítač Azure s výchozími možnostmi, skupinu dostupnosti se vytvoří automaticky v závislosti na nastavení zdrojové oblasti. Tato nastavení můžete upravit.
Zóny dostupnosti | Podporováno |  
Program hybrid Use Benefit (HUB) | Podporováno | Pokud zdrojový virtuální počítač má licenci centra povolené, testovací převzetí služeb při selhání nebo převzetí služeb při selhání virtuálního počítače také používá licenci ROZBOČOVAČE.
Škálovací sady virtuálních počítačů | Nepodporuje se |
Publikované Image z Galerie Azure – Microsoft | Podporováno | Podporováno, pokud virtuální počítač běží na podporovaném operačním systému.
Image Galerie Azure - publikované třetích stran | Podporováno | Podporováno, pokud virtuální počítač běží na podporovaném operačním systému.
Vlastní Image - publikované třetích stran | Podporováno | Podporováno, pokud virtuální počítač běží na podporovaném operačním systému.
Migrovat virtuální počítače pomocí služby Site Recovery | Podporováno | Pokud virtuální počítač VMware nebo fyzický počítač byl migrován do Azure pomocí Site Recovery, musíte odinstalovat starší verze služby Mobility na počítači a restartujte ho před replikací do jiné oblasti Azure.

## <a name="replicated-machines---disk-actions"></a>Replikované počítače - disk akce

**Akce** | **Podrobnosti**
-- | ---
Změna velikosti disku na replikovaný virtuální počítač | Podporováno
Přidání disku do replikovaný virtuální počítač | Nepodporuje se.<br/><br/> Potřebujete zakázat replikaci příslušného virtuálního počítače přidejte disk a pak replikaci zase povolte.

## <a name="replicated-machines---storage"></a>Replikované počítače - úložiště

Tato tabulka je automaticky shrnutý podporu pro disk operačního systému virtuálního počítače Azure, datový disk a dočasný disk.

- Je důležité sledovat omezením disku virtuálního počítače a cíle pro [Linux](../virtual-machines/linux/disk-scalability-targets.md) a [Windows](../virtual-machines/windows/disk-scalability-targets.md) virtuálních počítačů, aby se zabránilo problémy s výkonem.
- Pokud provádíte nasazení s výchozím nastavením, Site Recovery automaticky vytvoří disks a účtech úložiště na základě nastavení zdroje.
- Pokud upravíte, nezapomeňte že postupovat podle pokynů.

**Komponenta** | **Podpora** | **Podrobnosti**
--- | --- | ---
Maximální velikost disku operačního systému | 2 048 GB | [Další informace](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms) o discích virtuálních počítačů.
Dočasný disk | Nepodporuje se | Dočasný disk je vždy z replikace vyloučit.<br/><br/> Nemáte žádná trvalá data na dočasném disku. [Další informace](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk).
Maximální velikost datového disku | 4095 GB |
Maximální číslo datového disku | Až 64, v souladu s podporou pro určité velikosti virtuálních počítačů Azure | [Další informace](../virtual-machines/windows/sizes.md) o velikostech virtuálních počítačů.
Četnost změn dat disku | Maximálně 10 MB/s na disku pro premium storage. Maximálně 2 MB/s na disk pro úložiště úrovně Standard. | Pokud Průměrná frekvence změny dat na disku je trvale vyšší než maximální délka, nebude dohnat replikace.<br/><br/>  Pokud se překročí maximální nedojde replikace můžete zachytit, ale může se zobrazit body obnovení mírně zpožděné.
Datový disk - účet úložiště úrovně standard | Podporováno |
Datový disk - účtu služby premium storage | Podporováno | Pokud virtuální počítač obsahuje disky, které jsou rozděleny mezi účty úložiště úrovně standard a premium, můžete vybrat jiný cílový účet úložiště pro každý z disků, zajistěte, že abyste měli stejnou konfiguraci úložiště v cílové oblasti.
Spravovaný disk – standard | Podporované v oblastech Azure, ve kterých je Azure Site Recovery nepodporuje. |  
Spravovaný disk – premium | Podporované v oblastech Azure, ve kterých je Azure Site Recovery nepodporuje. |
SSD úrovně Standard | Nepodporuje se |
Redundance | Jsou podporovány LRS a GRS.<br/><br/> ZRS se nepodporuje.
Úložiště Cool a hot | Nepodporuje se | Disky virtuálních počítačů se nepodporují studené a horké úložiště
Prostory úložiště | Podporováno |         
Šifrování v klidovém stavu (SSE) | Podporováno | SSE je výchozí nastavení u účtů úložiště.   
Azure Disk Encryption (ADE) pro operační systém Windows | Povoleno pro virtuální počítače [šifrování pomocí aplikace Azure AD](https://aka.ms/ade-aad-app) jsou podporovány |
Azure Disk Encryption (ADE) pro operační systém Linux | Nepodporuje se |
Přidání nebo odebrání horké disku | Nepodporuje se | Je-li přidat nebo odebrat datový disk na virtuálním počítači, musíte zakázat replikaci a povolte replikaci pro virtuální počítač znovu.
Vyloučení disku | Nepodporuje se|   Dočasný disk je ve výchozím nastavení vyloučen.
Prostory úložiště s přímým přístupem  | Nepodporuje se|
Souborový Server se Škálováním  | Nepodporuje se|
LRS | Podporováno |
GRS | Podporováno |
RA-GRS | Podporováno |
ZRS | Nepodporuje se |  
Úložiště Cool a Hot | Nepodporuje se | Disky virtuálního počítače nejsou podporovány na studené a horké úložiště
Pro virtuální sítě brány firewall služby Azure Storage  | Podporováno | Pokud pro přístup k virtuální síti se omezení pro účty úložiště, zajistěte, aby ["Povolit důvěryhodné služby Microsoftu"](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Účty úložiště pro obecné účely V2 (i horká a studená úroveň) | Ne | Zvýšit náklady na transakce podstatně porovnání pro obecné účely V1 účty úložiště

>[!IMPORTANT]
> Ujistěte se, že můžete sledovat virtuální počítač disk škálovatelnost a výkonnostní cíle pro [Linux](../virtual-machines/linux/disk-scalability-targets.md) nebo [Windows](../virtual-machines/windows/disk-scalability-targets.md) virtuálních počítačů, aby se zabránilo problémy s výkonem. Pokud budete postupovat podle výchozího nastavení, Site Recovery vytvoří požadované disky a účty úložiště v závislosti na konfiguraci zdroje. Je-li přizpůsobit a vybrat vlastní nastavení, ujistěte se, abyste postupovali podle disku škálovatelnost a výkonnostní cíle pro zdrojové virtuální počítače.

## <a name="azure-site-recovery-limits-to-replicate-data-change-rates"></a>Frekvence změny omezení Azure Site Recovery k replikaci dat.
Následující tabulka obsahuje omezení služby Azure Site Recovery. Tato omezení se zakládají na našich testováních, nemůžou však pokrýt všechny možné kombinace vstupně-výstupních operací aplikace. Skutečné výsledky se můžou lišit v závislosti na kombinaci vstupně-výstupních operací vaší aplikace. Můžeme také nezapomeňte přitom, že existují dvě omezení ke zvážení na disk, četnost změn dat a za virtuální počítač data churn.
Například, když se podíváte na disk úrovně Premium P20 v níže uvedená tabulka, Site Recovery dokáže zpracovat změny 5 MB/s na disk s maximálně pěti těmito disky na virtuální počítač kvůli omezení 25 MB/s celková četnost změn na virtuální počítač.

**Cíl ukládání replikace** | **Průměrná velikost vstupně-výstupních operací zdrojového disku** |**Průměrná četnost změn dat zdrojového disku** | **Celková denní četnost změn dat zdrojového disku**
---|---|---|---
Storage úrovně Standard | 8 kB | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 8 kB  | 2 MB/s | 168 GB na disk
Disk úrovně Premium P10 nebo P15 | 16 kB | 4 MB/s |  336 GB na disk
Disk úrovně Premium P10 nebo P15 | 32 kB nebo větší | 8 MB/s | 672 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 8 kB    | 5 MB/s | 421 GB na disk
Disk úrovně Premium P20 nebo P30 nebo P40 nebo P50 | 16 kB nebo větší |10 MB/s | 842 GB na disk
## <a name="replicated-machines---networking"></a>Replikované počítače - sítě
**Konfigurace** | **Podpora** | **Podrobnosti**
--- | --- | ---
NIC | Maximální počet podporovaný pro určité velikosti virtuálních počítačů Azure | Síťové karty se vytvářejí, když virtuální počítač se vytvoří během převzetí služeb při selhání.<br/><br/> Počet síťových adaptérů na převzetí služeb při selhání virtuálního počítače závisí na počet síťových adaptérů na zdrojovém virtuálním počítači při povolení replikace. Je-li přidat nebo odebrat síťovou kartu po povolení replikace, neovlivní počet síťových adaptérů na replikovanému virtuálnímu počítači po převzetí služeb při selhání.
Internetový nástroj pro vyrovnávání zatížení | Podporováno | Přidružte předkonfigurované zátěže pomocí Azure Automation skriptu v plánu obnovení.
Interní nástroj pro vyrovnávání zatížení | Podporováno | Přidružte předkonfigurované zátěže pomocí Azure Automation skriptu v plánu obnovení.
Veřejná IP adresa | Podporováno | Stávající veřejnou IP adresu přidružit k síťové kartě Vytvoření veřejné IP adresy a přidružte jej k síťovému rozhraní pomocí Azure Automation skriptu v plánu obnovení.
NSG na síťové KARTĚ | Podporováno | Přidružení NSG k síťovému rozhraní pomocí Azure Automation skriptu v plánu obnovení.  
NSG na podsítě | Podporováno | Přidružení skupiny zabezpečení sítě s podsítí pomocí Azure Automation skriptu v plánu obnovení.
Vyhrazená IP adresa (statické) | Podporováno | Pokud má síťový adaptér na zdrojovém virtuálním počítači statickou IP adresu a cílová podsíť má stejnou IP adresu k dispozici, je přiřazen k převzetí virtuálního počítače.<br/><br/> Pokud cílová podsíť nemá stejnou IP adresu k dispozici, některou z dostupných IP adres v podsíti je vyhrazen pro virtuální počítač.<br/><br/> Můžete také určit pevnou IP adresu a podsítě v **replikované položky** > **nastavení** > **výpočty a síť**  >  **Síťová rozhraní**.
Dynamická IP adresa | Podporováno | Pokud má síťový adaptér na zdroj dynamických IP adres, NIC se selhání pro virtuální počítač je dynamický ve výchozím nastavení.<br/><br/> Tuto hodnotu můžete změnit na pevnou IP adresu v případě potřeby.
Traffic Manager     | Podporováno | Traffic Manager vám umožňují předem nakonfigurovat tak, aby provoz se směruje do koncového bodu ve zdrojové oblasti v pravidelných intervalech a ke koncovému bodu v cílové oblasti v případě převzetí služeb při selhání.
Azure DNS | Podporováno |
Vlastní DNS  | Podporováno |    
Neověřené Proxy | Podporováno | Odkazovat na [sítě dokument s pokyny.](site-recovery-azure-to-azure-networking-guidance.md)    
Ověřený proxy server | Nepodporuje se | Pokud virtuální počítač používá ověřený proxy server pro odchozí připojení, nelze replikovat, pomocí Azure Site Recovery.    
Site to Site VPN s místními (s nebo bez ExpressRoute)| Podporováno | Ujistěte se, že trasy definované uživatelem a skupin zabezpečení sítě jsou nakonfigurovány tak, že provoz obnovení lokality se nesměruje na místní. Odkazovat na [sítě dokument s pokyny.](site-recovery-azure-to-azure-networking-guidance.md)  
Připojení mezi virtuálními SÍTĚMI | Podporováno | Odkazovat na [sítě dokument s pokyny.](site-recovery-azure-to-azure-networking-guidance.md)  
Koncové body služby virtuální sítě | Podporováno | Pokud pro přístup k virtuální síti se omezení pro účty úložiště, ujistěte se, že jsou povolena důvěryhodným službám Microsoftu přístup k účtu úložiště.
Akcelerované síťové služby | Podporováno | Akcelerované síťové služby musí být povolené na zdrojovém virtuálním počítači. [Další informace](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>Další postup
- Čtení [sítě pokyny pro replikaci virtuálních počítačů Azure](site-recovery-azure-to-azure-networking-guidance.md).
- Nasazení zotavení po havárii podle [replikaci virtuálních počítačů Azure](site-recovery-azure-to-azure.md).

---
title: Matice podpory Azure Site Recovery pro replikaci z Azure do Azure | Dokumentace Microsoftu
description: Shrnuje podporované operační systémy a konfigurace pro replikaci virtuálních počítačů Azure (VM) Azure Site Recovery z jedné oblasti do jiného pro potřeby zotavení po havárii.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.author: sujayt
ms.openlocfilehash: c2892d51c6eb5e71c0b1af400b78e993742fede0
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173046"
---
# <a name="support-matrix-for-replicating-from-one-azure-region-to-another"></a>Matice podpory pro které se replikují z jedné oblasti Azure do jiné



Tento článek shrnuje podporované konfigurace a komponenty při replikaci a obnovení virtuálních počítačů Azure z jedné oblasti do jiné oblasti pomocí [Azure Site Recovery](site-recovery-overview.md) služby.

## <a name="user-interface-options"></a>Možnosti uživatelského rozhraní

**Uživatelské rozhraní** |  **Podporované / nepodporované**
--- | ---
**Azure Portal** | Podporováno
**PowerShell** | [Replikace z Azure do Azure pomocí Powershellu](azure-to-azure-powershell.md)
**REST API** | Aktuálně se nepodporuje.
**Rozhraní příkazového řádku** | Aktuálně se nepodporuje.


## <a name="resource-support"></a>Podpora prostředků

**Typ přesunu prostředku** | **Podrobnosti** 
--- | --- | ---
**Přesun trezoru mezi skupinami prostředků** | Nepodporuje se<br/><br/> Trezoru služby Recovery services nelze přesouvat mezi skupinami prostředků.
**Přesunout výpočetní síťi prostředků mezi skupinami prostředků** | Nepodporuje se.<br/><br/> Při přesunutí virtuálního počítače nebo přidružené komponent, jako jsou úložiště nebo sítě po replikuje, musíte zakázat replikaci a znovu povolit replikaci pro virtuální počítač.
**Replikace virtuálních počítačů Azure z jednoho předplatného do druhého pro zotavení po havárii** | Nepodporuje se.
**Migrace virtuálních počítačů mezi předplatnými** | Nepodporuje se.
**Migrace virtuálních počítačů ve stejné oblasti** | Nepodporuje se.


## <a name="support-for-replicated-machine-os-versions"></a>Podporu pro replikované počítače verze operačního systému

Níže podpory se dá použít pro jakoukoli úlohu běžící v uvedených operačního systému.

#### <a name="windows"></a>Windows

- Windows Server 2016 (jádro serveru, Server s desktopovým prostředím) *
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 s na minimálně SP1

>[!NOTE]
>
> \* Windows Server 2016 Nano Server není podporována.

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7, 6.8 6.9, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5   
- CentOS 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3,7.4, 7.5
- Ubuntu 14.04 LTS Server [ (podporované verze jádra)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Server se systémem Ubuntu 16.04 LTS [ (podporované verze jádra)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7 [ (podporované verze jádra)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- Debian 8 [ (podporované verze jádra)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- SUSE Linux Enterprise Server 12 SP1, SP2 SP3 [ (podporované verze jádra)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
- SUSE Linux Enterprise Server 11 SP3
- SP4 operačním systémem SUSE Linux Enterprise Server 11
- Oracle Enterprise Linux 6.4, 6.5 kompatibilní jádra Red Hat nebo nedělitelné Enterprise jádra verze 3 (UEK3)

(Upgrade replikace počítačů z SLES 11 SP3 do SLES 11 SP4 se nepodporuje. Pokud replikovaného počítače byl upgradován z SLES 11SP3 na SLES 11 SP4, musíte zakázat replikaci a chránit počítač znovu po upgradu.)

>[!NOTE]
>
> Servery se systémem Ubuntu použití ověřování pomocí hesla a přihlášení a pomocí cloud-init balíček ke konfiguraci cloudové virtuální počítače, může mít založené na heslech přihlašovací jméno zakázané po převzetí služeb při selhání (v závislosti na konfiguraci cloudinit.) Přihlášení pomocí hesla, která může být na virtuálním počítači znovu zapnout resetováním hesla z nabídky nastavení (v části Podpora a řešení potíží části) tohoto přes virtuální počítač na webu Azure portal.

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra Ubuntu pro Azure virtual machines

**Vydání verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
14.04 LTS | 9.18 | 3.13.0-24-Generic k 3.13.0-151-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-128-generic |
14.04 LTS | 9.17 | 3.13.0-24-Generic k 3.13.0-147-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-124-generic |
14.04 LTS | 9.16 | 3.13.0-24-Generic k 3.13.0-144-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-119-generic |
14.04 LTS | 9.15. | 3.13.0-24-Generic k 3.13.0-143-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-116-generic |
|||
16.04 LTS | 9.18 | 4.4.0-21-Generic k 4.4.0-128-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-45-generic,<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1018-azure |
16.04 LTS | 9.17 | 4.4.0-21-Generic k 4.4.0-124-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-41-generic,<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1016-azure |
16.04 LTS | 9.16 | 4.4.0-21-Generic k 4.4.0-119-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-38-generic,<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1012-azure |
16.04 LTS | 9.15. | 4.4.0-21-Generic k 4.4.0-116-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic až 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-37-generic,<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1012-azure |


### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra Debian pro Azure virtual machines

**Vydání verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
Debian 7 | 9.17,9.18 | 3.2.0-4-amd64 k 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 7 | 9.15., 9.16 | 3.2.0-4-amd64 k 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.17, 9.18 | 3.16.0-4-amd64 k 3.16.0-6-amd64 4.9.0-0.bpo.4-amd64 k 4.9.0-0.bpo.6-amd64 |
Debian 8 | 9.15., 9.16 | 3.16.0-4-amd64 k 3.16.0-5-amd64 4.9.0-0.bpo.4-amd64 k 4.9.0-0.bpo.5-amd64 |

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Podporované verze jádra operačního systému SUSE Linux Enterprise Server 12 pro virtuální počítače Azure

**Vydání verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | 9.18 | SP1 3.12.49-11-default k 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default k 3.12.74-60.64.93-default</br></br> 4.4.21-69-default SP2 do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default k 4.4.121-92.80-default</br></br>SP3 4.4.73-5-default k 4.4.138-94.39-default |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | 9.17 | SP1 3.12.49-11-default k 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default k 3.12.74-60.64.88-default</br></br> 4.4.21-69-default SP2 do 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default</br></br>SP3 4.4.73-5-default k 4.4.126-94.22-default |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>Podporované souborové systémy a konfigurace úložiště hosta na virtuálních počítačích Azure s operačním systémem Linux

* Systémy souborů: ext3 ext4, ReiserFS (Suse Linux Enterprise Server pouze), XFS
* Správce svazků: LVM2
* Software s funkcí Multipath: Mapovač zařízení

## <a name="region-support"></a>Oblasti podpory

Můžete replikovat a obnovovat virtuální počítače mezi všechny dvou oblastí ve stejné zeměpisné clusteru.

**Geografické clusteru** | **Oblasti Azure**
-- | --
Americké | Kanada – východ, Kanada – Jih, střed USA (střed), střed USA – Západ, USA – východ, USA – východ 2, USA – Západ, USA – západ 2, střed USA, USA (střed) – sever
Evropa | Velká Británie – Západ, Velká Británie – Jih, Severní Evropa, západní Evropa, Francie – střed, Francie – jih
Asie | Indie – Jih, střed Indie, jihovýchodní Asie, jihovýchodní Asie, Japonsko – východ, Japonsko – Západ, Korea – střed, Korea – jih
Austrálie   | Austrálie – východ, Austrálie – jihovýchod
Azure Government    | Virginie USA (gov), US GOV Iowa, USA (gov) Arizona, US GOV Texas, US DOD – východ, US DOD – střed
Německo | Německo – střed, Německo – severovýchod
Čína | Čína – východ, Čína – sever

>[!NOTE]
>
> Pro oblast Brazílie – jih můžete replikovat a na některý střed USA – Jih, střed USA – Západ, USA – východ, USA – východ 2, USA – Západ, USA – západ 2 a oblasti střed USA – sever převzetí služeb při selhání a navrácení služeb po obnovení.

## <a name="support-for-vmdisk-management"></a>Podpora pro správu virtuálních počítačů/disků

**Akce** | **Podrobnosti**
-- | ---
Změna velikosti disku na replikovaný virtuální počítač | Podporováno
Přidání disku do replikovaný virtuální počítač | Nepodporuje se. Potřebujete zakázat replikaci příslušného virtuálního počítače přidejte disk a pak replikaci zase povolte.


## <a name="support-for-compute-configuration"></a>Podpora pro konfiguraci výpočtů

**Konfigurace** | **Podporované/nepodporované** | **Poznámky**
--- | --- | ---
Velikost | Libovolná velikost virtuálních počítačů Azure s nejméně 2 jádra procesoru a 1 GB paměti RAM | Odkazovat na [velikosti virtuálních počítačů Azure](../virtual-machines/windows/sizes.md)
Skupiny dostupnosti | Podporováno | Pokud použijete výchozí možnost 'Povolit replikaci' kroku portálu, skupinu dostupnosti je automaticky vytvořená v závislosti na konfiguraci zdrojové oblasti. Můžete změnit cíl dostupnosti v "replikovaná položka > Nastavení > výpočty a síť > dostupnosti" kdykoli.
Hybridní použití výhody (HUB) virtuálních počítačů | Podporováno | Pokud zdrojový virtuální počítač má licence HUB povolený, testovací převzetí služeb při selhání nebo převzetí služeb při selhání virtuálního počítače také používá licence ROZBOČOVAČE.
Škálovací sady virtuálních počítačů | Nepodporuje se |
Publikování Microsoft Azure Image z Galerie- | Podporováno | Podporované jako virtuální počítač běží na podporovaném operačním systému službou Site Recovery
Image Galerie Azure - publikované třetích stran | Podporováno | Podporované jako virtuální počítač běží na podporovaném operačním systému službou Site Recovery.
Vlastní Image - publikované třetích stran | Podporováno | Podporované jako virtuální počítač běží na podporovaném operačním systému službou Site Recovery.
Migrovat virtuální počítače pomocí služby Site Recovery | Podporováno | Pokud je, že VMware nebo fyzický počítač migrovat na Azure pomocí Site Recovery, musíte odinstalovat starší verze služby mobility a restartujte ho před replikací do jiné oblasti Azure.

## <a name="support-for-storage-configuration"></a>Podpora pro konfigurace úložiště

**Konfigurace** | **Podporované/nepodporované** | **Poznámky**
--- | --- | ---
Maximální velikost disku operačního systému | 2 048 GB | Odkazovat na [disky, které jsou používány virtuálními počítači.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Velikost maximální datového disku | 4095 GB | Odkazovat na [disky, které jsou používány virtuálními počítači.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Počet datových disků | Podporuje až 64 stejně jako konkrétní velikosti virtuálního počítače Azure | Odkazovat na [velikosti virtuálních počítačů Azure](../virtual-machines/windows/sizes.md)
Dočasný disk | Vždy z replikace vyloučit. | Dočasný disk je vyloučený z replikace vždy. Umístíte by neměl žádné trvalá data na dočasném disku podle doprovodné materiály k Azure. Odkazovat na [dočasný disk na virtuálních počítačích Azure](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk) další podrobnosti.
Četnost změn dat na disku | Maximálně 10 MB/s na disk storage úrovně Premium až 2 MB/s na disk pro úložiště úrovně Standard | Pokud se o míru změn průměr dat na disku přesahuje 10 MB/s (pro Premium) a 2 MB/s (pro úroveň Standard) nepřetržitě, nebude dohnat replikace. Nicméně pokud je občasnému dat shluků a četnost změn dat je větší než 10 MB/s (pro Premium) a 2 MB/s (pro úroveň Standard) nechystáte nějakou dobu se vrátí, replikace se dohnat. V takovém případě může se zobrazit body obnovení mírně zpožděné.
Disky v účtech úložiště úrovně standard | Podporováno |
Disky na účty úložiště úrovně premium | Podporováno | Pokud virtuální počítač obsahuje disky, které jsou rozděleny mezi účty úložiště úrovně standard a premium, můžete vybrat jiný cílový účet úložiště pro každý z disků zajistěte, že abyste měli stejnou konfiguraci úložiště v cílové oblasti
Standard Managed disks | Podporované v oblastech Azure, ve kterých je Azure Site Recovery nepodporuje. |  
Premium Managed disks | Podporované v oblastech Azure, ve kterých je Azure Site Recovery nepodporuje. |
Prostory úložiště | Podporováno |         
Šifrování v klidovém stavu (SSE) | Podporováno | SSE je výchozí nastavení u účtů úložiště.   
Azure Disk Encryption (ADE) | Nepodporuje se |
Přidání nebo odebrání horké disku | Nepodporuje se | Je-li přidat nebo odebrat datový disk na virtuálním počítači, musíte zakázat replikaci a povolte replikaci pro virtuální počítač znovu.
Vyloučení disku | Nepodporuje se|   Dočasný disk je ve výchozím nastavení vyloučen.
Prostory úložiště s přímým přístupem  | Nepodporuje se|
Souborový Server se Škálováním  | Nepodporuje se|
LRS | Podporováno |
GRS | Podporováno |
RA-GRS | Podporováno |
ZRS | Nepodporuje se |  
Úložiště Cool a Hot | Nepodporuje se | Disky virtuálního počítače nejsou podporovány na studené a horké úložiště
Pro virtuální sítě brány firewall služby Azure Storage  | Ne | Umožňuje přístup ke konkrétním virtuálním sítím Azure v používá k ukládání replikovaných dat účtů úložiště mezipaměti není podporována.
Účty úložiště pro obecné účely V2 (i horká a studená úroveň) | Ne | Zvýšit náklady na transakce podstatně porovnání pro obecné účely V1 účty úložiště

>[!IMPORTANT]
> Ujistěte se, že můžete sledovat virtuální počítač disk škálovatelnost a výkonnostní cíle pro [Linux](../virtual-machines/linux/disk-scalability-targets.md) nebo [Windows](../virtual-machines/windows/disk-scalability-targets.md) virtuálních počítačů, aby se zabránilo problémy s výkonem. Pokud budete postupovat podle výchozího nastavení, Site Recovery vytvoří požadované disky a účty úložiště v závislosti na konfiguraci zdroje. Je-li přizpůsobit a vybrat vlastní nastavení, ujistěte se, abyste postupovali podle disku škálovatelnost a výkonnostní cíle pro zdrojové virtuální počítače.

## <a name="support-for-network-configuration"></a>Podpora pro konfiguraci sítě
**Konfigurace** | **Podporované/nepodporované** | **Poznámky**
--- | --- | ---
Síťové rozhraní (NIC) | Až do maximální počet síťových adaptérů podporuje určité velikosti virtuálních počítačů Azure | Síťové karty se vytvářejí, když je virtuální počítač vytvořen jako součást testovacího převzetí služeb při selhání nebo převzetí služeb při selhání. Počet síťových adaptérů na převzetí služeb při selhání virtuálního počítače závisí na počtu síťových karet na zdroj, který se má při povolení replikace virtuální počítač. Pokud budete přidávat nebo odebírat NIC po povolení replikace, neovlivní počet síťové karty na převzetí služeb při selhání virtuálního počítače.
Internetový nástroj pro vyrovnávání zatížení | Podporováno | Je nutné přidružit předem nakonfigurované zátěže pomocí azure automation skriptu v plánu obnovení.
Interní nástroj pro vyrovnávání zatížení | Podporováno | Je nutné přidružit předem nakonfigurované zátěže pomocí azure automation skriptu v plánu obnovení.
Veřejná IP adresa| Podporováno | Budete muset přidružení už existující veřejné IP adresy k síťovému rozhraní nebo vytvořit a přidružit ji k síťovému rozhraní pomocí azure automation skriptu v plánu obnovení.
NSG na síťové karty (Resource Manager)| Podporováno | Je potřeba přidružení NSG k síťové KARTĚ pomocí azure automation skriptu v plánu obnovení.  
Skupina zabezpečení sítě na podsíť (Resource Manager a Classic)| Podporováno | Je potřeba přidružení NSG k podsíti pomocí azure automation skriptu v plánu obnovení.
Skupina zabezpečení sítě na virtuálním počítači (Classic)| Podporováno | Je potřeba přidružení NSG k síťové KARTĚ pomocí azure automation skriptu v plánu obnovení.
Vyhrazené IP (statická IP adresa) / zachovat Zdrojová IP adresa | Podporováno | Pokud má síťový adaptér na zdrojovém virtuálním počítači konfiguraci statické IP adresy a cílová podsíť má stejnou IP adresu k dispozici, přiřadí se k převzetí služeb při selhání virtuálního počítače. Pokud cílová podsíť nemá stejnou IP Adresou, k dispozici, jeden z dostupných IP adres v podsíti je vyhrazený pro tento virtuální počítač. Můžete zadat pevné IP podle vašeho výběru v "replikovaná položka > Nastavení > výpočty a síť > Síťová rozhraní. Můžete vybrat síťového adaptéru a zadejte podsíť a IP podle vašeho výběru.
Zjišťování dynamických IP| Podporováno | Pokud má síťový adaptér na zdrojovém virtuálním počítači dynamickou konfiguraci IP adres, NIC na převzetí služeb virtuálního počítače je dynamický ve výchozím nastavení. Můžete zadat pevné IP podle vašeho výběru v "replikovaná položka > Nastavení > výpočty a síť > Síťová rozhraní. Můžete vybrat síťového adaptéru a zadejte podsíť a IP podle vašeho výběru.
Integrace Traffic Manageru | Podporováno | Můžete předem nakonfigurovat traffic manager tak, že provoz se směruje do koncového bodu ve zdrojové oblasti v pravidelných intervalech a ke koncovému bodu v cílové oblasti v případě převzetí služeb při selhání.
Spravované Azure DNS | Podporováno |
Vlastní DNS  | Podporováno |    
Neověřené Proxy | Podporováno | Odkazovat na [sítě dokument s pokyny.](site-recovery-azure-to-azure-networking-guidance.md)    
Ověřený proxy server | Nepodporuje se | Pokud virtuální počítač používá ověřený proxy server pro odchozí připojení, nelze replikovat, pomocí Azure Site Recovery.    
Site to Site VPN s místními (s nebo bez ExpressRoute)| Podporováno | Ujistěte se, že trasy definované uživatelem a skupin zabezpečení sítě jsou nakonfigurovány tak, že provoz obnovení lokality se nesměruje na místní. Odkazovat na [sítě dokument s pokyny.](site-recovery-azure-to-azure-networking-guidance.md)  
Připojení mezi virtuálními SÍTĚMI | Podporováno | Odkazovat na [sítě dokument s pokyny.](site-recovery-azure-to-azure-networking-guidance.md)  
Koncové body služby virtuální sítě | Podporováno | Bran firewall Azure Storage pro virtuální sítě nejsou podporovány. Umožňuje přístup ke konkrétním virtuálním sítím Azure v používá k ukládání replikovaných dat účtů úložiště mezipaměti není podporována.
Akcelerované síťové služby | Nepodporuje se | Je možné replikovat virtuální počítač s Akcelerovanými síťovými službami povolené, ale převzetí služeb při selhání virtuálního počítače nebude mít Akcelerovanými síťovými službami povolena. Akcelerované síťové služby budou taky zakázané pro zdrojový virtuální počítač na navrácení služeb po obnovení.


## <a name="next-steps"></a>Další postup
- Další informace o [sítě pokyny pro replikaci virtuálních počítačů Azure](site-recovery-azure-to-azure-networking-guidance.md)
- Začněte chránit svoje úlohy pomocí [replikaci virtuálních počítačů Azure](site-recovery-azure-to-azure.md)

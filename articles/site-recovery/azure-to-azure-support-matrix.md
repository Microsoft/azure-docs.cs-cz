---
title: Azure Site Recovery matici podpory pro replikaci z Azure do Azure | Microsoft Docs
description: Shrnuje podporované operační systémy a konfigurace pro virtuální počítače Azure (VM) Azure Site Recovery replikaci z jedné oblasti do jiné pro potřeby zotavení po havárii.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 04/25/2018
ms.author: sujayt
ms.openlocfilehash: d7bfbbe834ac8506b7d12d5748406460df0fe3bc
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="support-matrix-for-replicating-from-one-azure-region-to-another"></a>Podporu pro replikaci z jedné oblasti Azure do jiného


>[!NOTE]
>
> Replikace obnovení lokality pro virtuální počítače Azure je aktuálně ve verzi preview.

Tento článek shrnuje podporované konfigurace a součásti služby Azure Site Recovery při replikaci a obnovení virtuálních počítačů Azure z jedné oblasti do jiné oblasti.

## <a name="user-interface-options"></a>Možnosti uživatelského rozhraní

**Uživatelské rozhraní** |  **Podporované / nepodporované**
--- | ---
**Azure Portal** | Podporováno
**Portál Classic** | Nepodporuje se
**PowerShell** | [Preview](azure-to-azure-powershell.md)
**REST API** | Aktuálně nepodporuje
**Rozhraní příkazového řádku** | Aktuálně nepodporuje


## <a name="resource-move-support"></a>Podpora přesunutí prostředku

**Typ přesunutí prostředku** | **Podporované / nepodporované** | **Poznámky**  
--- | --- | ---
**Přesunutí trezoru rámci skupiny prostředků** | Nepodporuje se |Trezor služeb zotavení nelze přesouvat mezi skupinami prostředků.
**Přesunutí výpočty, úložiště a sítě v rámci skupiny prostředků** | Nepodporuje se |Pokud přesunete po povolení replikace virtuálního počítače (nebo jeho přidružené komponenty, jako je například úložiště a sítě), budete muset zakázat replikaci a zapnout replikaci pro virtuální počítač znovu.



## <a name="support-for-deployment-models"></a>Podpora pro modely nasazení

**Model nasazení** | **Podporované / nepodporované** | **Poznámky**  
--- | --- | ---
**Classic** | Podporováno | Můžete replikovat klasické virtuální počítač a obnovit jako virtuální počítač s classic. Nelze obnovit jako virtuální počítač Resource Manager. Pokud nasadíte klasické virtuální počítač bez připojení k virtuální síti a přímo do oblasti Azure, se nepodporuje.
**Resource Manager** | Podporováno |

>[!NOTE]
>
> 1. Replikace virtuálních počítačů Azure z jedno předplatné do jiného pro scénáře zotavení po havárii není podporována.
> 2. Migrace Azure nepodporuje virtuální počítače ve předplatných.
> 3. Migrace Azure nepodporuje virtuální počítače ve stejné oblasti.
> 4. Migruje virtuální počítače Azure z modelu nasazení Classic do Resource manager model nasazení se nepodporuje.

## <a name="support-for-replicated-machine-os-versions"></a>Podpora pro verze operačního systému replikovaného počítače

Níže podpora je dostupná pro jakoukoli úlohu spuštěnou na uvedených operačního systému.

#### <a name="windows"></a>Windows

- Windows Server 2016 (jádra serveru, Server s desktopovým prostředím) *
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 s v minimálně SP1

>[!NOTE]
>
> \* Windows Server 2016 Nano Server není podporována.

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3,7.4
- CentOS 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3,7.4
- Ubuntu 14.04 LTS Server [ (podporované verze jádra)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Ubuntu 16.04 LTS Server [ (podporované verze jádra)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7 [ (podporované verze jádra)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- Debian 8 [ (podporované verze jádra)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- Oracle Enterprise Linux 6.4, 6.5 systémem Red Hat kompatibilní jádra nebo nedělitelné Enterprise jádra verze 3 (UEK3)
- SUSE Linux Enterprise Server 11 SP3
- SUSE Linux Enterprise Server 11 SP4

(Upgrade replikaci počítačů z SLES 11 SP3 na SLES 11 SP4 není podporována. Pokud replikovaného počítače z SLES 11SP3 byl upgradován na verzi SLES 11 SP4, budete muset zakázat replikaci a chránit počítač znovu po upgradu.)

>[!NOTE]
>
> Ubuntu servery pomocí ověřování pomocí hesla a přihlášení a pomocí balíčku pro cloud init ke konfiguraci cloudu virtuálních počítačů, může mít založené na heslech přihlašovací údaje zakázána po převzetí služeb při selhání (v závislosti na konfiguraci cloudinit.) Resetováním hesla z nabídky nastavení může být založené na heslech přihlášení znovu zapnout na virtuálním počítači (v části Podpora + Poradce při potížích s část) došlo přes virtuální počítač na portálu Azure.

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Podporované verze Ubuntu jádra pro virtuální počítače Azure

**Verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
14.04 LTS | 9.12 | 3.13.0-24-Generic k 3.13.0-132-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-96-generic |
14.04 LTS | 9.13 | 3.13.0-24-Generic k 3.13.0-137-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-104-generic |
14.04 LTS | 9.14 | 3.13.0-24-Generic k 3.13.0-141-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-112-generic |
14.04 LTS | 9.15. | 3.13.0-24-Generic k 3.13.0-143-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-116-generic |
16.04 LTS | 9.12 | 4.4.0-21-Generic k 4.4.0-96-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic k 4.10.0-35-generic |
16.04 LTS | 9.13 | 4.4.0-21-Generic k 4.4.0-104-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic k 4.10.0-42-generic |
16.04 LTS | 9.14 | 4.4.0-21-Generic k 4.4.0-112-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic k 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-32-generic,<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1009-azure |
16.04 LTS | 9.15. | 4.4.0-21-Generic k 4.4.0-116-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic k 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-37-generic,<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1012-azure |


### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Podporované verze Debian jádra pro virtuální počítače Azure

**Verze** | **Verze služby mobility** | **Verze jádra** |
--- | --- | --- |
Debian 7 | 9.14, 9.15. | 3.2.0-4-amd64 k 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 8 | 9.14, 9.15. | 3.16.0-4-amd64 k 3.16.0-5-amd64, 4.9.0-0.bpo.4-amd64 k 4.9.0-0.bpo.5-amd64 |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>Podporované souborové systémy a konfigurace úložiště hosta na virtuálních počítačích Azure spuštěn operační systém Linux.

* Systémy souborů: ext3, ext4, ReiserFS (Suse Linux Enterprise Server pouze), XFS
* Správce svazků: LVM2
* Software s funkcí Multipath: Mapovač zařízení

## <a name="region-support"></a>Oblasti podpory

Můžete replikovat a obnovení virtuálních počítačů mezi všechny dvěma oblastmi v rámci stejné zeměpisné clusteru.

**Zeměpisná clusteru** | **Oblasti Azure**
-- | --
Amerika | Východní Kanada, Kanada centrální, Jižní střed USA, střed USA – Západ, východní USA, Východ USA 2, západ USA, západní USA 2, střed USA, střed USA – sever
Evropa | Spojené království – Západ, Spojené království – Jih, Severní Evropa, západní Evropa, střední, Francie Francie – jih
Asie | Indie – Jih, střed, jihovýchodní Asie, východní Asie, Japonsko – východ, Japonsko – Západ, Korejská – střed, Korejská jih
Austrálie   | Austrálie – východ, Austrálie – jihovýchod
Azure Government    | Virginia verze pro státní správu USA, USA verze pro státní správu Iowa, USA verze pro státní správu Arizona, Texas verze pro státní správu USA, DOD USA – východ, DOD USA – střed
Německo | Německo – střed, Německo – severovýchod
Čína | Východní Čína, severní Čína

>[!NOTE]
>
> Oblasti Brazílie – jih můžete replikaci a převzetí služeb při selhání mezi jihu USA – Západ střední USA, Východ USA, Východ USA 2, západní USA, západní USA 2 a oblastech severní jihu USA a navrácení služeb po obnovení.


## <a name="support-for-compute-configuration"></a>Podpora pro konfiguraci výpočtů

**Konfigurace** | **Podporované/nepodporované** | **Poznámky**
--- | --- | ---
Velikost | Jakékoli velikosti virtuálního počítače Azure s nejméně 2 jádra procesoru a 1 GB paměti RAM | Odkazovat na [velikosti virtuálního počítače Azure](../virtual-machines/windows/sizes.md)
Sady dostupnosti | Podporováno | Pokud použijete výchozí možnost během kroku replikaci povolit portálu, skupina dostupnosti je automaticky vytvořit, podle konfigurace oblast zdroje. Můžete změnit skupinu dostupnosti cíl ' replikované položky > Nastavení > výpočty a síť > skupiny dostupnosti, kdykoli.
Hybridní použití zvýhodnění (ROZBOČOVAČ) virtuálních počítačů | Podporováno | Pokud zdrojový virtuální počítač má licenci ROZBOČOVAČE povolené, testovací převzetí služeb při selhání nebo virtuálního počítače převzetí služeb při selhání také používá licence ROZBOČOVAČE.
Virtual Machine Scale Sets | Nepodporuje se |
Publikovaná Microsoft Azure Galerie obrázků- | Podporováno | Podporovány, pokud virtuální počítač běží na podporovaný operační systém pomocí Site Recovery
Azure Gallery Image - publikovaná třetích stran | Podporováno | Podporovány, pokud virtuální počítač běží na podporovaný operační systém pomocí Site Recovery.
Vlastní Image - publikovaná třetích stran | Podporováno | Podporovány, pokud virtuální počítač běží na podporovaný operační systém pomocí Site Recovery.
Virtuální počítače migrovat pomocí Site Recovery | Podporováno | Pokud je, že VMware nebo fyzický počítač migrovat na Azure pomocí Site Recovery, musíte odinstalovat starší verze služby mobility a restartujte počítač před replikace jiné oblasti Azure.

## <a name="support-for-storage-configuration"></a>Podpora pro konfigurace úložiště

**Konfigurace** | **Podporované/nepodporované** | **Poznámky**
--- | --- | ---
Maximální velikost disku operačního systému | 2 048 GB | Odkazovat na [disky, které jsou používány virtuálními počítači.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Velikost disku maximum dat. | 4095 GB | Odkazovat na [disky, které jsou používány virtuálními počítači.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Počet datových disků | Podporuje až 64 jako konkrétní velikost virtuálního počítače Azure | Odkazovat na [velikosti virtuálního počítače Azure](../virtual-machines/windows/sizes.md)
Dočasné disku | Vždy z replikace vyloučit. | Dočasné disk je vyloučený z replikace vždy. Neměli vložit žádná trvalá data na dočasné disku podle Azure pokyny. Odkazovat na [dočasným diskovým na virtuálních počítačích Azure](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk) další podrobnosti.
Míry změny dat na disku | Nesmí být delší než 10 MB/s na disk pro storage úrovně Premium až 2 MB/s na disk pro standardní úložiště | Pokud se o míru změn průměr dat na disku je nad rámec 10 MB/s (pro Premium) a 2 MB/s (pro Standard) nepřetržitě, nebudou aktualizovány replikace. Ale pokud je shluků příležitostně dat a míry změny dat je větší než 10 MB/s (pro Premium) až 2 MB/s (pro Standard) po určitou dobu a dodává se, replikace budou aktualizovány. V takovém případě může se zobrazit body obnovení mírně zpožděné.
Disky na účty úložiště standard storage | Podporováno |
Disky na prémiové účty úložiště | Podporováno | Pokud virtuální počítač obsahuje disky, které jsou rozloženy účty úložiště standard a premium, můžete vybrat jiný cílový účet úložiště pro každý z disků, zda že máte stejnou konfiguraci úložiště v cílová oblast
Standardní disky spravované | Podporované v oblastech Azure, ve kterých je Azure Site Recovery podporována. Cloud vlády nejsou aktuálně podporovány.  |  
Pro prémiové disky spravované | Podporované v oblastech Azure, ve kterých je Azure Site Recovery podporována. Cloud vlády nejsou aktuálně podporovány. |
Prostory úložiště | Podporováno |         
Šifrování v klidovém stavu (SSE) | Podporováno | Pro účty úložiště mezipaměti a cíle můžete vybrat účet úložiště SSE povolena.     
Azure Disk Encryption (ADE) | Nepodporuje se |
Přidat nebo odebrat aktivní disku | Nepodporuje se | Je-li přidat nebo odebrat datový disk ve virtuálním počítači, musíte zakázat replikaci a zapnout replikaci pro virtuální počítač znovu.
Vyloučení disku | Nepodporuje se|   Ve výchozím nastavení je vyloučen dočasné disku.
Prostory úložiště – přímé  | Nepodporuje se|
Škálovaný souborový Server  | Nepodporuje se|
LRS | Podporováno |
GRS | Podporováno |
RA-GRS | Podporováno |
ZRS | Nepodporuje se |  
Aktivní a studeného úložiště | Nepodporuje se | Disky virtuálního počítače nejsou podporovány na studených a aktivní úložiště
Azure Storage brány firewall pro virtuální sítě  | Ne | Umožňuje přístup ke konkrétní virtuální sítě Azure na účty úložiště mezipaměti používá k ukládání replikovaných dat není podporována.
Účty úložiště obecné účely V2 (jak horkého a studeného úložiště vrstva) | Ne | Nárůst nákladů transakce podstatně porovnává pro obecné účely účty úložiště V1

>[!IMPORTANT]
> Ujistěte se, že zjistíte virtuální počítač disku škálovatelnosti a cílech výkonnosti pro [Linux](../virtual-machines/linux/disk-scalability-targets.md) nebo [Windows](../virtual-machines/windows/disk-scalability-targets.md) virtuální počítače, aby se zabránilo problémům s výkonem. Pokud budete postupovat podle výchozího nastavení, Site Recovery vytvořte požadované disky a účty úložiště na základě konfigurace zdroje. Pokud vlastní nastavení a vyberte vlastní nastavení, ujistěte se, postupujte podle cílů disků škálovatelnost a výkon, a to pro zdrojové virtuální počítače.

## <a name="support-for-network-configuration"></a>Podpora pro konfiguraci sítě
**Konfigurace** | **Podporované/nepodporované** | **Poznámky**
--- | --- | ---
Síťové rozhraní (NIC) | Až do maximální počet síťových adaptérů nepodporuje konkrétní velikost virtuálního počítače Azure | Síťové adaptéry se vytvoří při vytvoření virtuálního počítače jako součást operace převzetí služeb při selhání nebo testovací převzetí služeb při selhání. Počet síťových adaptérů na převzetí služeb při selhání virtuálního počítače závisí na počet síťových adaptérů na zdroj, který má virtuální počítač v době povolení replikace. Pokud jste přidat nebo odebrat síťovou kartu po povolení replikace, neovlivní počet síťový adaptér na převzetí služeb při selhání virtuálního počítače.
Internetový nástroj pro vyrovnávání zatížení | Podporováno | Je nutné přidružit Vyrovnávání zatížení předem nakonfigurovaná pomocí služby azure automation skriptu v plánu obnovení.
Interní nástroj pro vyrovnávání zatížení | Podporováno | Je nutné přidružit Vyrovnávání zatížení předem nakonfigurovaná pomocí služby azure automation skriptu v plánu obnovení.
Veřejná IP adresa| Podporováno | Budete muset přiřadit stávající veřejnou IP adresu na síťový adaptér nebo vytvořit a přidružit na síťový adaptér pomocí služby azure automation skriptu v plánu obnovení.
Skupina NSG na síťovou kartu (Resource Manager)| Podporováno | Je nutné přidružit NSG na síťový adaptér pomocí služby azure automation skriptu v plánu obnovení.  
Skupina NSG na podsítě (Resource Manager a klasický)| Podporováno | Je potřeba přidružení skupiny NSG k podsíti pomocí služby azure automation skriptu v plánu obnovení.
Skupina NSG na virtuálním počítači (klasické)| Podporováno | Je nutné přidružit NSG na síťový adaptér pomocí služby azure automation skriptu v plánu obnovení.
Vyhrazená IP adresa (statickou IP adresu) / zachovat zdrojové IP adresy | Podporováno | Pokud má síťový adaptér na zdrojový virtuální počítač konfiguraci statické IP adresy a cílové podsíti má stejnou IP adresu, k dispozici, je přiřazen k převzetí služeb při selhání virtuálního počítače. Pokud cílové podsíti nemá stejnou IP Adresou, k dispozici, jednu z dostupných IP adres v podsíti je vyhrazený pro tento virtuální počítač. Můžete zadat pevné IP zvoleného v ' replikované položky > Nastavení > výpočty a síť > síťových rozhraní se. Můžete vybrat síťový adaptér a zadejte podsíť a IP podle svého výběru.
Dynamické IP| Podporováno | Pokud má síťový adaptér na zdrojový virtuální počítač konfigurace s dynamickými IP, síťový adaptér na převzetí služeb při selhání virtuálního počítače je také dynamické ve výchozím nastavení. Můžete zadat pevné IP zvoleného v ' replikované položky > Nastavení > výpočty a síť > síťových rozhraní se. Můžete vybrat síťový adaptér a zadejte podsíť a IP podle svého výběru.
Integrace Traffic Manageru | Podporováno | Můžete předkonfigurovat váš správce provozu tak, že provoz se směruje na koncový bod ve zdrojové oblasti v pravidelných intervalech a ke koncovému bodu v cílové oblasti v případě převzetí služeb při selhání.
Spravovat Azure DNS | Podporováno |
Vlastní DNS  | Podporováno |    
Neověřené Proxy | Podporováno | Odkazovat na [sítě pokyny dokumentu.](site-recovery-azure-to-azure-networking-guidance.md)    
Ověřené Proxy | Nepodporuje se | Pokud virtuální počítač používá ověřené proxy pro odchozí připojení, nelze replikovat, pomocí Azure Site Recovery.    
Site to Site VPN s místním (s nebo bez ExpressRoute)| Podporováno | Ujistěte se, zda Nsg a udr jsou nakonfigurovány tak, že webový provoz obnovení není směrované na místní. Odkazovat na [sítě pokyny dokumentu.](site-recovery-azure-to-azure-networking-guidance.md)  
Virtuální síť připojení virtuální sítě | Podporováno | Odkazovat na [sítě pokyny dokumentu.](site-recovery-azure-to-azure-networking-guidance.md)  
Koncové body služby virtuální sítě | Podporováno | Azure Storage brány firewall pro virtuální sítě nejsou podporovány. Umožňuje přístup ke konkrétní virtuální sítě Azure na účty úložiště mezipaměti používá k ukládání replikovaných dat není podporována.
Akcelerované síťové služby | Nepodporuje se | Je možné replikovat virtuální počítač s Accelerated sítě povolené, ale převzetí služeb při selhání virtuálního počítače nebudou mít Accelerated sítě povolené. Zrychlený sítě budou rovněž zakázány pro zdrojového virtuálního počítače na navrácení služeb po obnovení.


## <a name="next-steps"></a>Další postup
- Další informace o [sítě pokyny pro replikaci virtuálních počítačů Azure](site-recovery-azure-to-azure-networking-guidance.md)
- Začněte chránit vaše úlohy [replikace virtuálních počítačů Azure](site-recovery-azure-to-azure.md)

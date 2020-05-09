---
title: SAP HANA konfigurací úložiště virtuálních počítačů Azure | Microsoft Docs
description: Doporučení úložiště pro virtuální počítače, které mají v nich nasazené SAP HANA.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b0d8228586c0e20e4314331339aa2f2c46a38c9a
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792152"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Konfigurace úložiště virtuálních počítačů Azure SAP HANA

Azure poskytuje různé typy úložiště, které jsou vhodné pro virtuální počítače Azure, na kterých běží SAP HANA. **SAP HANA certifikované typy úložiště Azure** , které je možné zvážit pro seznam nasazení SAP HANA, jako je: 

- SSD úrovně Premium Azure  
- [Disky Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Další informace o těchto typech disků najdete v článku [Výběr typu disku](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types) .

Azure nabízí dvě metody nasazení pro virtuální pevné disky v Azure Standard a Premium Storage. Pokud to celkový scénář umožňuje, využijte nasazení služby [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) . 

Seznam typů úložišť a jejich SLA v rámci vstupně-výstupních operací a propustnosti úložiště najdete v [dokumentaci k Azure pro službu Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Nezávisle na zvoleném typu úložiště Azure je nutné, aby systém souborů používaný v tomto úložišti podporoval SAP pro konkrétní operační systém a systém DBMS. [Poznámka k podpoře SAP #405827](https://launchpad.support.sap.com/#/notes/405827) uvádí podporované systémy souborů pro různé operační systémy a databáze, včetně SAP HANA. To platí pro všechny svazky SAP HANA můžou mít přístup ke čtení a zápisu pro libovolný úkol. Při použití systému souborů NFS v Azure pro SAP HANA platí další omezení verze systému souborů NFS, jak je uvedeno dále v tomto článku. 


Minimální SAP HANA certifikované podmínky pro různé typy úložišť: 

- Azure SSD úrovně Premium –/Hana/log se musí ukládat do mezipaměti v Azure [akcelerátor zápisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Svazek/Hana/data může být umístěný na SSD úrovně Premium bez použití Azure Akcelerátor zápisu nebo na disku Ultra.
- Azure Ultra disk alespoň pro svazek/Hana/log. Svazek/Hana/data se dá umístit buď do SSD úrovně Premium bez Akcelerátor zápisu Azure, nebo aby se dosáhlo rychlejšího restartování extrémně disku.
- Svazky **systému souborů NFS v 4.1** nad Azure NetApp Files/Hana/log **a** /Hana/data. Svazek/Hana/Shared může používat protokol NFS v3 nebo NFS verze 4.1. Protokol NFS v 4.1 je povinný pro svazky/Hana/data a/Hana/protokolů.

Některé typy úložišť lze kombinovat. Například je možné umístit/Hana/data do Premium Storage a/Hana/log je možné umístit do úložiště Ultra disk, aby se dosáhlo požadované nízké latence. Pokud ale použijete svazek systému souborů NFS v 4.1 na základě ANF pro/Hana/data, budete muset použít jiný svazek NFS v 4.1 na základě ANF pro/Hana/log.. Použití NFS na ANF pro jeden ze svazků (jako/Hana/data) a Azure Premium Storage nebo Ultra disk pro jiný svazek (jako/Hana/log) se **nepodporuje**.

V místním světě se zřídka postará o subsystémech I/O a jeho schopnosti. Důvodem je, že dodavatel zařízení musí zajistit, aby byly splněny minimální požadavky na úložiště pro SAP HANA. Při sestavování infrastruktury Azure byste si měli být vědomi některých z těchto požadavků na vydaných SAP. Některé z vlastností minimální propustnosti, které jsou požadovány pro SAP, mají za následek nutnost:

- Povolit čtení a zápis v **/Hana/logu** s 250 MB/s a velikosti 1 MB vstupu/výstupu
- Povolit aktivitu čtení minimálně 400 MB/s pro **/Hana/data** pro velikost I/O 16 mb a 64 MB
- Povolit aktivitu zápisu alespoň 250 MB/s pro **/Hana/data** s 16 mb a 64 MB I/O velikosti

Vzhledem k toho, že nízká latence úložiště je pro systémy DBMS velmi kritická, i když DBMS, jako je SAP HANA, udržujte data v paměti. Kritická cesta v úložišti většinou představuje zápisy do protokolu transakcí systémů DBMS. Ale také operace jako psaní úložných bodů nebo načítání dat v paměti po zotavení po havárii můžou být kritické. Proto je **nutné** využívat disky Azure Premium pro **/Hana/data** a **/Hana/log** svazky. Aby bylo možné dosáhnout minimální propustnosti **/Hana/log** a **/Hana/data** podle požadavků SAP, je třeba vytvořit RAID 0 pomocí MDADM nebo LVM na více discích Premium Storage Azure. A používejte svazky RAID jako svazky **/Hana/data** a **/Hana/log** . 

**Doporučení: jako velikost stripe pro RAID 0 doporučujeme použít:**

- 256 KB pro **/Hana/data**
- 32 KB pro **/Hana/log**

> [!IMPORTANT]
> Velikost proložení pro/Hana/data se změnila z předchozích doporučení volajících 64 KB nebo 128 KB 256 na základě zkušeností zákazníků s novějšími verzemi systému Linux. Velikost 256 KB poskytuje poněkud lepší výkon.

> [!NOTE]
> Nemusíte konfigurovat žádnou redundanci pomocí svazků RAID, protože Azure Premium a Storage úrovně Standard udržují tři image VHD. Použití svazku RAID je čistě ke konfiguraci svazků, které poskytují dostatečnou propustnost vstupně-výstupních operací.

Nahromadění řady virtuálních pevných disků Azure pod položkou RAID se accumulative z IOPS a propustnosti úložiště. Pokud tedy zadáte disk RAID 0 nad 3 x P30 disků Azure Premium Storage, měli byste zadat třikrát hodnotu IOPS a trojnásobnou propustnost úložiště jednoho disku Azure Premium Storage P30.

Při změně velikosti nebo rozhodování pro virtuální počítač si taky Pamatujte na propustnost v/v. Celková propustnost úložiště virtuálních počítačů je popsána v článku [paměťově optimalizované velikosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Režim plánovače I/O systému Linux
Linux má několik různých režimů plánování vstupu a výstupu. Běžnými doporučeními pro dodavatele a SAP systému Linux je překonfigurování režimu v/v plánovače u diskových svazků z režimu **MQ-konečný** nebo **kyber** do **NoOp** (non-Queue) nebo **none** for (pro více front). Podrobnosti jsou odkazovány v [#1984787 SAP Note](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Řešení s Premium Storage a Azure Akcelerátor zápisu pro virtuální počítače řady Azure M-Series
Azure Akcelerátor zápisu je funkce, která je k dispozici pro virtuální počítače Azure M-Series výhradně. Jako název uvádíme účel funkce a vylepšit latenci vstupu a výstupu zápisů do Premium Storage Azure. V případě SAP HANA se má Akcelerátor zápisu použít jenom pro svazek **/Hana/log** . Proto jsou **/Hana/data** a **/Hana/log** samostatné svazky s Azure akcelerátor zápisu podporují pouze svazek **/Hana/log** . 

> [!IMPORTANT]
> Pokud používáte Azure Premium Storage, je použití Azure [akcelerátor zápisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) nebo svazku/Hana/log povinné. Akcelerátor zápisu je k dispozici pouze pro virtuální počítače s podporou Premium Storage a řady M-Series a Mv2-Series.

Níže uvedená doporučení pro ukládání do mezipaměti jsou popsána v parametrech vstupu/výstupu SAP HANA, jako je například:

- K datovým souborům HANA se špatně všechny úlohy čtení. Po restartování instance HANA nebo při načtení dat do HANA jsou výjimky velké velikosti I/OS. Dalším případem většího počtu vstupně-výstupních vstupně-výstupních souborů pro čtení dat může být zálohování databáze HANA. V důsledku toho by ukládání do mezipaměti pro čtení většinou nedávalo smysl vzhledem k tomu, že ve většině případů je třeba všechny svazky datových souborů přečíst Kompletně.
- Zápis proti datovým souborům se nachází v shlukech na základě zotavení po havárii HANA úložných bodů a HANA. Zápis úložných bodů je asynchronní a neobsahuje žádné uživatelské transakce. Zápis dat během zotavení po havárii je kritický pro výkon, aby systém mohl rychle reagovat. Zotavení po havárii by ale mělo být spíše výjimečné situace
- Ze souborů pro opětovné provedení služby HANA se špatně žádné čtení. Výjimky jsou velké vstupně-výstupy při provádění záloh protokolů transakcí, zotavení po havárii nebo ve fázi restartování instance HANA.  
- Hlavní zatížení proti souboru protokolu SAP HANA znovu zapisuje. V závislosti na povaze úlohy můžete mít vstupně-výstupní operace až na 4 KB nebo jiné velikosti vstupně-výstupních operací s 1 MBm nebo více. Latence zápisu proti SAP HANA protokolu opětovného provedení je kritická.
- Všechny zápisy musí být trvale uchovány na disku spolehlivě.

**Doporučení: Výsledkem těchto pozorovaných vstupně-výstupních vzorů SAP HANA je ukládání do mezipaměti pro různé svazky pomocí Azure Premium Storage by mělo být nastavené jako:**

- **/Hana/data** – bez ukládání do mezipaměti
- **/Hana/log** – žádné ukládání do mezipaměti – výjimka pro M-a Mv2-Series, kde by měla být povolena akcelerátor zápisu bez mezipaměti pro čtení. 
- mezipaměť pro čtení **/Hana/Shared**

### <a name="production-recommended-storage-solution"></a>Doporučené produkční řešení úložiště

> [!IMPORTANT]
> SAP HANA certifikace pro virtuální počítače Azure M-Series je výhradně ve službě Azure Akcelerátor zápisu pro svazek **/Hana/log** . V důsledku toho se očekává, že produkční scénář SAP HANA nasazení na virtuálních počítačích Azure řady M-Series se pro svazek **/Hana/log** nakonfiguruje s využitím Azure akcelerátor zápisu.  

> [!NOTE]
> V případě produkčních scénářů ověřte, jestli určitý typ virtuálního počítače podporuje SAP HANA SAP v [dokumentaci SAP pro IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Doporučení často překračují minimální požadavky SAP, jak je uvedeno výše v tomto článku. Uvedená doporučení představují kompromis mezi doporučeními velikosti podle SAP a maximální propustností úložiště, kterou poskytují různé typy virtuálních počítačů.

**Doporučení: doporučené konfigurace pro produkční scénáře vypadají jako:**

| Skladová položka virtuálního počítače | Paměť RAM | Max. VSTUPNĚ-VÝSTUPNÍ OPERACE VIRTUÁLNÍHO POČÍTAČE<br /> Propustnost | /hana/data | /hana/log | /hana/shared | Rozsah/root | /usr/sap | Hana a zálohování |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 × P20 | 1 × P20 | 1 × P6 | 1 × P6 |1 × P20 |
| M32ls | 256 GB | 500 MB/s | 3 x P20 | 2 × P20 | 1 × P20 | 1 × P6 | 1 × P6 |1 × P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 × P20 | 1 × P20 | 1 × P6 | 1 × P6 |1 × P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 × P20 | 1 × P30 | 1 × P6 | 1 × P6 |2 × P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 × P20 | 1 × P30 | 1 × P6 | 1 × P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 × P20 | 1 × P30 | 1 × P10 | 1 × P6 | 2 × P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 × P30 | 2 × P20 | 1 × P30 | 1 × P10 | 1 × P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 × P20 | 1 × P30 | 1 × P10 | 1 × P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 × P20 | 1 × P30 | 1 × P10 | 1 × P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 × P20 | 1 × P30 | 1 × P10 | 1 × P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 × P40 | 2 × P20 | 1 × P30 | 1 × P10 | 1 × P6 | 4 x P50 |

Ověřte, zda propustnost úložiště pro různé navrhované svazky splňuje zatížení, které chcete spustit. Pokud zatížení vyžaduje větší objemy pro **/Hana/data** a **/Hana/log**, je potřeba zvýšit počet virtuálních pevných disků Azure Premium Storage. Změna velikosti svazku s více virtuálními disky, než je uvedené, zvyšuje počet IOPS a propustnost vstupně-výstupních operací v rámci omezení typu virtuálního počítače Azure.

Azure Akcelerátor zápisu funguje jenom ve spojení se službou [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Proto musí být alespoň disky Premium Storage Azure tvořící svazek **/Hana/log** nasazené jako spravované disky.

Existují omezení pro Azure Premium Storage VHD na jeden virtuální počítač, která může podporovat Azure Akcelerátor zápisu. Aktuální limity jsou:

- 16 VHD pro virtuální počítač s M128xx a M416xx
- 8 VHD pro virtuální počítač s M64xx a M208xx
- 4 virtuální pevné disky pro virtuální počítač s M32xx

Podrobnější pokyny k tomu, jak povolit Azure Akcelerátor zápisu, najdete v článku [akcelerátor zápisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Podrobnosti a omezení pro Azure Akcelerátor zápisu najdete ve stejné dokumentaci.

**Doporučení: pro disky tvořící/Hana/log svazek je nutné použít Akcelerátor zápisu.**


### <a name="cost-conscious-azure-storage-configuration"></a>Konfigurace Azure Storage vědomosti nákladů
Následující tabulka uvádí konfiguraci typů virtuálních počítačů, které zákazníci používají také k hostování SAP HANA na virtuálních počítačích Azure. K dispozici můžou být některé typy virtuálních počítačů, které nemusí splňovat všechna kritéria pro úložiště SAP HANA nebo nejsou oficiálně podporované SAP HANA pomocí SAP. Ale proto se těmto virtuálním počítačům zdálo udělat v neprodukčních scénářích nějaké možnosti. **/Hana/data** a **/Hana/log** jsou zkombinovány na jeden svazek. V důsledku toho může použití Azure Akcelerátor zápisu znamenat omezení z hlediska IOPS.

> [!NOTE]
> V případě scénářů podporovaných systémem SAP ověřte, zda je určitý typ virtuálního počítače podporován SAP HANA SAP v [dokumentaci SAP pro IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Změna z dřívějších doporučení pro řešení s vědomím nákladů je přechod z [HDD úrovně Standard disků Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) na lepší výkon a spolehlivější [SSD úrovně Standard disky Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) .

Doporučení často překračují minimální požadavky SAP, jak je uvedeno výše v tomto článku. Uvedená doporučení představují kompromis mezi doporučeními velikosti podle SAP a maximální propustností úložiště, kterou poskytují různé typy virtuálních počítačů.

| Skladová položka virtuálního počítače | Paměť RAM | Max. VSTUPNĚ-VÝSTUPNÍ OPERACE VIRTUÁLNÍHO POČÍTAČE<br /> Propustnost | /Hana/data a/Hana/log<br /> prokládaný pomocí LVM nebo MDADM | /hana/shared | Rozsah/root | /usr/sap | Hana a zálohování |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 × E20 | 1 × E6 | 1 × E6 | 1 × E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 × E20 | 1 × E6 | 1 × E6 | 1 × E15 |
| E32v3 | 256 GB | 768 MB/s | 3 x P20 | 1 × E20 | 1 × E6 | 1 × E6 | 1 × E20 |
| E64v3 | 432 GiB | 1 200 MB/s | 3 x P20 | 1 × E20 | 1 × E6 | 1 × E6 | 1 × E30 |
| GS5 | 448 GiB | 2 000 MB/s | 3 x P20 | 1 × E20 | 1 × E6 | 1 × E6 | 1 × E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 × E20 | 1 × E6 | 1 × E6 | 1 × E20 |
| M32ls | 256 GB | 500 MB/s | 3 x P20 | 1 × E20 | 1 × E6 | 1 × E6 | 1 × E20 |
| M64ls | 512 GiB | 1 000 MB/s | 3 x P20 | 1 × E20 | 1 × E6 | 1 × E6 |1 × E30 |
| M64s | 1 000 GiB | 1 000 MB/s | 2 × P30 | 1 × E30 | 1 × E6 | 1 × E6 |2 × E30 |
| M64ms | 1 750 GiB | 1 000 MB/s | 3 x P30 | 1 × E30 | 1 × E6 | 1 × E6 | 3 x E30 |
| M128s | 2 000 GiB | 2 000 MB/s |3 x P30 | 1 × E30 | 1 × E10 | 1 × E6 | 2 × E40 |
| M128ms | 3 800 GiB | 2 000 MB/s | 5 × P30 | 1 × E30 | 1 × E10 | 1 × E6 | 2 × E50 |
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 x P30 | 1 × E30 | 1 × E10 | 1 × E6 |  3 x E40 |
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 4 x P40 | 1 × E30 | 1 × E10 | 1 × E6 |  4 x E40 |
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 4 x P40 | 1 × E30 | 1 × E10 | 1 × E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2 000 MB/s | 8 × P40 | 1 × E30 | 1 × E10 | 1 × E6 |  4 x E50 |


Disky doporučené pro menší typy virtuálních počítačů se 3 x P20 neodesílá svazky týkající se doporučení místa podle dokumentu [White paper k úložišti SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Nicméně možnost zobrazená v tabulce byla provedena za účelem zajištění dostatečné propustnosti disku pro SAP HANA. Pokud budete potřebovat změny ve svazku **/Hana/Backup** , který měl velikost pro uchovávání záloh, které reprezentují dvojnásobek paměti, můžete ho klidně upravit.   
Ověřte, zda propustnost úložiště pro různé navrhované svazky splňuje zatížení, které chcete spustit. Pokud zatížení vyžaduje větší objemy pro **/Hana/data** a **/Hana/log**, je potřeba zvýšit počet virtuálních pevných disků Azure Premium Storage. Změna velikosti svazku s více virtuálními disky, než je uvedené, zvyšuje počet IOPS a propustnost vstupně-výstupních operací v rámci omezení typu virtuálního počítače Azure. 

> [!NOTE]
> Výše uvedené konfigurace by nevyužily [smlouvy SLA pro virtuální počítače Azure s jedním virtuálním počítačem](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) , protože využívají kombinaci Azure Premium Storage a služby Azure Storage úrovně Standard. Výběr byl však vybrán, aby bylo možné optimalizovat náklady. Pro všechny disky, které jsou uvedené jako Azure Storage úrovně Standard (SXX), musíte zvolit Premium Storage, aby konfigurace virtuálního počítače odpovídala smlouvě SLA pro jeden virtuální počítač Azure.


> [!NOTE]
> Uvedená doporučení pro konfiguraci disků cílí na minimální požadavky, které SAP vyjádří ke svým poskytovatelům infrastruktury. V reálných scénářích nasazení a úloh v reálném čase byly zjištěny situace, kdy tato doporučení ještě neposkytovala dostatek možností. Může se jednat o situace, kdy zákazník vyžadoval rychlejší navýšení dat po restartování HANA nebo kde konfigurace zálohování vyžadovala větší šířku pásma úložiště. Jiné případy zahrnují **/Hana/log** , kde pro konkrétní zatížení nepostačují 5000 IOPS. Proto proveďte tato doporučení jako výchozí bod a přizpůsobte je na základě požadavků úlohy.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Konfigurace úložiště Azure Ultra disk pro SAP HANA
Microsoft se v procesu zavádí nový typ úložiště Azure s názvem [Azure Ultra disk](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk). Významný rozdíl mezi službou Azure Storage, která je doposud dostupná a Ultra disk, je, že možnosti disku už nejsou svázané s velikostí disku. Jako zákazník můžete definovat tyto možnosti pro ultra disk:

- Velikost disku v rozsahu od 4 GiB do 65 536 GiB
- Rozsah IOPS od 100 IOPS do 160K IOPS (maximální počet závisí na typech virtuálních počítačů).
- Propustnost úložiště z 300 MB/s do 2 000 MB/s

Ultra disk poskytuje možnost definovat jeden disk, který splňuje vaši velikost, IOPS a rozsah propustnosti disku. Místo používání správců logických svazků, jako je LVM nebo MDADM, na Azure Premium Storage k vytváření svazků, které splňují požadavky na propustnost vstupně-výstupních operací a úložiště. Můžete spouštět kombinaci konfigurací mezi disky Ultra a Premium Storage. V důsledku toho můžete omezit využití disku Ultra disk na/Hana/data a/Hana/log svazky na výkon a další svazky pokrýt pomocí Azure Premium Storage

Dalšími výhodami extrémně disku může být lepší latence čtení v porovnání s Premium Storage. Rychlejší latence čtení může mít výhody, když chcete snížit dobu spuštění HANA a následné načtení dat do paměti. Pokud HANA zapisuje úložných bodů, můžou se taky vycházet z výhod úložiště Ultra disk. Vzhledem k tomu, že Premium Storage disky pro/Hana/data obvykle nejsou Akcelerátor zápisu mezipaměti, latence zápisu pro/Hana/data v Premium Storage v porovnání s Ultra diskem je vyšší. Je možné očekávat, že zápis do uloženého bodu s Ultra diskem je lepší na disku Ultra disk.

> [!IMPORTANT]
> Ultra disk ještě není ve všech oblastech Azure a ještě nepodporují všechny typy virtuálních počítačů uvedené níže. Podrobné informace o dostupnosti Ultra disk a o podporovaných rodinách virtuálních počítačů najdete v článku o [tom, jaké typy disků jsou dostupné v Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Provozní řešení doporučené pro produkční úložiště s čistou konfigurací disků Ultra
V této konfiguraci se svazky/Hana/data a/Hana/log udržují samostatně. Navrhované hodnoty jsou odvozeny mimo klíčové ukazatele výkonu, které SAP musí certifikovat typy virtuálních počítačů pro SAP HANA a konfigurace úložiště podle doporučení v dokumentu [White paper k úložišti SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

Doporučení často překračují minimální požadavky SAP, jak je uvedeno výše v tomto článku. Uvedená doporučení představují kompromis mezi doporučeními velikosti podle SAP a maximální propustností úložiště, kterou poskytují různé typy virtuálních počítačů.

| Skladová položka virtuálního počítače | Paměť RAM | Max. VSTUPNĚ-VÝSTUPNÍ OPERACE VIRTUÁLNÍHO POČÍTAČE<br /> Propustnost | /Hana/data svazek | /Hana/data v/v – propustnost | /Hana/data IOPS | /Hana/log svazek | /Hana/log v/v – propustnost | /Hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1 200 MB/s | 600 GB | 700 MB/s | 7 500 | 512 GB | 500 MB/s  | 2 000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MB/s | 7 500 | 256 GB | 250 MB/s  | 2 000 |
| M32ls | 256 GB | 500 MB/s | 300 GB | 400 MB/s | 7 500 | 256 GB | 250 MB/s  | 2 000 |
| M64ls | 512 GiB | 1 000 MB/s | 600 GB | 600 MB/s | 7 500 | 512 GB | 400 MB/s  | 2,500 |
| M64s | 1 000 GiB | 1 000 MB/s |  1 200 GB | 600 MB/s | 7 500 | 512 GB | 400 MB/s  | 2,500 |
| M64ms | 1 750 GiB | 1 000 MB/s | 2 100 GB | 600 MB/s | 7 500 | 512 GB | 400 MB/s  | 2,500 |
| M128s | 2 000 GiB | 2 000 MB/s |2 400 GB | 1 200 MB/s |9 000 | 512 GB | 800 MB/s  | 3 000 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 4 800 GB | 1200 MB/s |9 000 | 512 GB | 800 MB/s  | 3 000 | 
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 3 500 GB | 1 000 MB/s | 9 000 | 512 GB | 400 MB/s  | 2,500 | 
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 7 200 GB | 1 000 MB/s | 9 000 | 512 GB | 400 MB/s  | 2,500 | 
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 7 200 GB | 1 500 MB/s | 9 000 | 512 GB | 800 MB/s  | 3 000 | 
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 14 400 GB | 1 500 MB/s | 9 000 | 512 GB | 800 MB/s  | 3 000 |   

Uvedené hodnoty mají být výchozím bodem a je nutné je vyhodnotit proti skutečným požadavkům. Výhodou pro Azure Ultra disk je, že hodnoty pro vstupně-výstupní operace a propustnost můžou být přizpůsobené bez nutnosti vypnout virtuální počítač nebo zastavit zatížení, které se v systému používá.   

> [!NOTE]
> Zatím nejsou k dispozici snímky úložiště s úložištěm Ultra disk. Tím se zablokuje použití snímků virtuálních počítačů s Azure Backup službami.

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>Řešení úložiště s důrazem na náklady s čistou konfigurací disků Ultra
V této konfiguraci jsou svazky/Hana/data a/Hana/log na stejném disku. Navrhované hodnoty jsou odvozeny mimo klíčové ukazatele výkonu, které SAP musí certifikovat typy virtuálních počítačů pro SAP HANA a konfigurace úložiště podle doporučení v dokumentu [White paper k úložišti SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). 

Doporučení často překračují minimální požadavky SAP, jak je uvedeno výše v tomto článku. Uvedená doporučení představují kompromis mezi doporučeními velikosti podle SAP a maximální propustností úložiště, kterou poskytují různé typy virtuálních počítačů.

| Skladová položka virtuálního počítače | Paměť RAM | Max. VSTUPNĚ-VÝSTUPNÍ OPERACE VIRTUÁLNÍHO POČÍTAČE<br /> Propustnost | Svazek pro/Hana/data a/log | /Hana/data a propustnost vstupně-výstupních operací protokolu | /Hana/data a log IOPS |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1 200 MB/s | 1 200 GB | 1 200 MB/s | 9 500 | 
| M32ts | 192 GiB | 500 MB/s | 512 GB | 400 MB/s | 9 500 | 
| M32ls | 256 GB | 500 MB/s | 600 GB | 400 MB/s | 9 500 | 
| M64ls | 512 GiB | 1 000 MB/s | 1 100 GB | 900 MB/s | 10 000 | 
| M64s | 1 000 GiB | 1 000 MB/s |  1 700 GB | 900 MB/s | 10 000 | 
| M64ms | 1 750 GiB | 1 000 MB/s | 2 600 GB | 900 MB/s | 10 000 | 
| M128s | 2 000 GiB | 2 000 MB/s |2 900 GB | 1 800 MB/s |12 000 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 5 300 GB | 1 800 MB/s |12 000 |  
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 000 GB | 900 MB/s | 10 000 |  
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 7 700 GB | 900 MB/s | 10 000 | 
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 7 700 GB | 1, 800MBps | 12 000 |  
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 15 000 GB | 1 800 MB/s | 12 000 |    

Uvedené hodnoty mají být výchozím bodem a je nutné je vyhodnotit proti skutečným požadavkům. Výhodou pro Azure Ultra disk je, že hodnoty pro vstupně-výstupní operace a propustnost můžou být přizpůsobené bez nutnosti vypnout virtuální počítač nebo zastavit zatížení, které se v systému používá.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Svazky NFS v 4.1 na Azure NetApp Files
Azure NetApp Files poskytuje nativní sdílené složky systému souborů NFS, které se dají použít pro svazky/Hana/Shared,/Hana/data a/Hana/log. Použití sdílených složek systému souborů NFS založených na ANF pro svazky/Hana/data a/Hana/log vyžaduje použití protokolu NFS v 4.1. Protokol NFS verze 3 není podporován pro použití/Hana/data a/Hana/log svazků při založení sdílených složek na ANF. 

> [!IMPORTANT]
> Protokol NFS v3 implementovaný v Azure NetApp Files **není podporován pro** použití pro/Hana/data a/Hana/log. Použití NFS 4,1 je povinné pro svazky/Hana/data a/Hana/log z funkčního bodu zobrazení. Vzhledem k tomu, že je možné použít pro svazek/Hana/Shared systém souborů NFS v3 nebo NFS verze 4.1, ze funkčního hlediska.

### <a name="important-considerations"></a>Důležité informace
Při zvažování Azure NetApp Files SAP NetWeaver a SAP HANA si pamatujte na následující důležité informace:

- Minimální fond kapacit je 4 TiB.  
- Minimální velikost svazku je 100 GiB
- Azure NetApp Files a všech virtuálních počítačů, kde se Azure NetApp Files svazky připojí, musí být ve stejné oblasti jako Azure Virtual Network nebo ve [virtuálních sítích s partnerským vztahem](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) .  
- Vybraná virtuální síť musí mít podsíť, delegovanou na Azure NetApp Files.
- Propustnost svazku Azure NetApp je funkcí kvóty svazku a úrovně služeb, jak je uvedeno v části [úroveň služby pro Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Při změně velikosti svazků NetApp HANA Azure se ujistěte, že výsledná propustnost splňuje požadavky na systém HANA.  
- Azure NetApp Files nabízí [zásady exportu](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): můžete řídit povolené klienty, typ přístupu (čtení&zápisu, jen pro čtení atd.). 
- Azure NetApp Files funkce zatím nereaguje na zóny. Aktuálně Azure NetApp Files funkce není nasazená ve všech zónách dostupnosti v oblasti Azure. Mějte na paměti, že v některých oblastech Azure máte vliv na potenciální latenci.  
- Je důležité mít virtuální počítače nasazené v těsné blízkosti úložiště Azure NetApp pro nízkou latenci. 
- ID uživatele pro ADM s <b>identifikátorem SID</b>a ID skupiny `sapsys` pro virtuální počítače musí odpovídat konfiguraci v Azure NetApp Files. 

> [!IMPORTANT]
> U SAP HANA úloh je nízká latence kritická. Spolupracujte se zástupcem Microsoftu a zajistěte, aby se virtuální počítače a Azure NetApp Files svazky nasadily v těsné blízkosti.  

> [!IMPORTANT]
> Pokud dojde k neshodě mezi ID uživatele pro ADM se <b>zabezpečením SID</b>a ID `sapsys` skupiny mezi virtuálním počítačem a konfigurací Azure NetApp, zobrazí se oprávnění soubory na svazcích Azure NetApp připojených k virtuálním počítačům `nobody`. Nezapomeňte zadat správné ID uživatele pro ADM s <b>identifikátorem SID</b>a ID skupiny pro `sapsys`, když se při [připojování nového systému do Azure NetApp Files nový systém](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) .

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Určení velikosti databáze HANA v Azure NetApp Files

Propustnost svazku Azure NetApp je funkce velikosti svazku a úrovně služby, jak je uvedeno v části [úroveň služby pro Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Při návrhu infrastruktury pro SAP v Azure byste měli znát minimální požadavky na propustnost úložiště v SAP, která se přeloží na minimální propustnost:

- Povolit čtení a zápis v/Hana/logu s 250 MB/s a velikosti 1 MB vstupu/výstupu  
- Povolit aktivitu čtení minimálně 400 MB/s pro/Hana/data pro velikost I/O 16 MB a 64 MB  
- Povolit aktivitu zápisu alespoň 250 MB/s pro/Hana/data s 16 MB a 64 MB I/O velikosti  

[Omezení propustnosti Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) na 1 TIB kvót:
- Premium Storage vrstva – 64 MiB/s  
- Úroveň úložiště úrovně Ultra – 128 MiB/s  

> [!IMPORTANT]
> Nezávisle na kapacitě, kterou nasazujete na jednom svazku NFS, se očekává, že propustnost stabilní úrovně v rozsahu od 1.2 do 1,4 GB/s (šířka pásma), kterou využívá spotřebitel ve virtuálním počítači. To se musí udělat se základní architekturou nabídky ANF a souvisejícími omezeními pro Linux Sessions pro systém souborů NFS. Údaje o výkonu a propustnosti popsané v článku [výsledky testování testů výkonnosti pro Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/performance-benchmarks-linux) byly provedeny na jednom sdíleném svazku systému souborů NFS s několika klientskými virtuálními počítači a v důsledku více relací. Tento scénář se liší od scénáře, který je v SAP k disměrnému scénáři. Místo měření propustnosti z jednoho virtuálního počítače na svazek systému souborů NFS. hostováno v ANF.

Aby splňovala požadavky na minimální propustnost SAP pro data a protokol a podle pokynů pro `/hana/shared`, Doporučené velikosti by vypadaly takto:

| Svazek | Velikost<br /> Premium Storage úroveň | Velikost<br /> Úroveň úložiště Ultra Storage | Podporovaný protokol NFS |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /hana/shared | Max (512 GB, 1xRAM) na 4 pracovní uzly | Max (512 GB, 1xRAM) na 4 pracovní uzly | V3 nebo v 4.1 |


> [!NOTE]
> Tady uvedená doporučení pro změny velikosti Azure NetApp Files cílí na to, aby splňovala minimální požadavky SAP na jejich poskytovatele infrastruktury. V reálných scénářích nasazení zákazníků a úloh, které nemusí být dostatečné. Tato doporučení použijte jako výchozí bod a přizpůsobte je na základě požadavků konkrétního zatížení.  

Proto byste měli zvážit, jak nasadit podobnou propustnost pro ANF svazky, jak je uvedeno pro úložiště Ultra disk již. Vezměte v úvahu také velikosti pro velikosti uvedené pro svazky pro různé skladové položky virtuálních počítačů, které jsou v tabulkách s Ultra diskem již provedeny.

> [!TIP]
> Můžete znovu nastavit velikost svazků Azure NetApp Files dynamicky bez `unmount` nutnosti svazků, zastavit virtuální počítače nebo zastavit SAP HANA. To umožňuje flexibilitu v závislosti na očekávané i nepředvídatelné propustnosti požadavků.

Dokumentace k nasazení SAP HANA konfigurace škálování na více instancí s pohotovostním uzlem pomocí systému souborů NFS v 4.1, který je hostovaný v ANF, se zveřejňuje v SAP HANA škálování na více instancích [s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse).


## <a name="next-steps"></a>Další kroky
Další informace naleznete v tématu:

- [SAP HANA Průvodce vysokou dostupností pro virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

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
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1b363c9da195794f6356539ffca46101edf431c2
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533876"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Konfigurace úložiště virtuálních počítačů Azure SAP HANA

Azure poskytuje různé typy úložiště, které jsou vhodné pro virtuální počítače Azure, na kterých běží SAP HANA. Typy úložiště Azure, které je možné považovat za seznam SAP HANA nasazení, jako například: 

- SSD úrovně Standard diskové jednotky (SSD)
- Jednotky SSD (Solid-State Drive)
- [Ultra disk](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/disks-enable-ultra-ssd) 

Další informace o těchto typech disků najdete v článku [Výběr typu disku](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types) .

Azure nabízí dvě metody nasazení pro virtuální pevné disky v Azure Standard a Premium Storage. Pokud to celkový scénář umožňuje, využijte nasazení služby [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) . 

Seznam typů úložišť a jejich SLA v rámci vstupně-výstupních operací a propustnosti úložiště najdete v [dokumentaci k Azure pro službu Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

**Základě Použití Azure Premium Storage ve spojení se službou Azure Akcelerátor zápisu a používání služby Azure Managed Disks pro nasazení**

V místním světě se nemuseli starat o vstupně-výstupních subsystémech a jejich schopnostech. Důvodem je, že dodavatel zařízení musí zajistit, aby byly splněny minimální požadavky na úložiště pro SAP HANA. Při sestavování infrastruktury Azure byste si měli být vědomi některých z těchto požadavků. Některé z vlastností minimální propustnosti, které jsou kladeny, mají za následek nutnost:

- Povolit čtení a zápis v **/Hana/logu** s 250 MB/s a velikosti 1 MB vstupu/výstupu
- Povolit aktivitu čtení minimálně 400 MB/s pro **/Hana/data** pro velikost I/O 16 mb a 64 MB
- Povolit aktivitu zápisu alespoň 250 MB/s pro **/Hana/data** s 16 mb a 64 MB I/O velikosti

Vzhledem k toho, že nízká latence úložiště je pro systémy DBMS velmi kritická, i když DBMS, jako je SAP HANA, udržujte data v paměti. Kritická cesta v úložišti většinou představuje zápisy do protokolu transakcí systémů DBMS. Ale také operace jako psaní úložných bodů nebo načítání dat v paměti po zotavení po havárii můžou být kritické. Proto je **nutné** využívat disky Azure Premium pro **/Hana/data** a **/Hana/log** svazky. Aby bylo možné dosáhnout minimální propustnosti **/Hana/log** a **/Hana/data** podle požadavků SAP, je třeba vytvořit RAID 0 pomocí MDADM nebo LVM na více discích Premium Storage Azure. A používejte svazky RAID jako svazky **/Hana/data** a **/Hana/log** . 

**Základě Jako velikost stripe pro RAID 0 doporučujeme použít:**

- 64 KB nebo 128 KB pro **/Hana/data**
- 32 KB pro **/Hana/log**

> [!NOTE]
> Nemusíte konfigurovat žádnou redundanci pomocí svazků RAID, protože Azure Premium a Storage úrovně Standard udržují tři image VHD. Použití svazku RAID je čistě ke konfiguraci svazků, které poskytují dostatečnou propustnost vstupně-výstupních operací.

Nahromadění řady virtuálních pevných disků Azure pod položkou RAID se accumulative z IOPS a propustnosti úložiště. Pokud tedy zadáte disk RAID 0 nad 3 x P30 disků Azure Premium Storage, měli byste zadat třikrát hodnotu IOPS a trojnásobnou propustnost úložiště jednoho disku Azure Premium Storage P30.

Níže uvedená doporučení pro ukládání do mezipaměti jsou popsána v parametrech vstupu/výstupu SAP HANA, jako je například:

- K datovým souborům HANA se špatně všechny úlohy čtení. Po restartování instance HANA nebo při načtení dat do HANA jsou výjimky velké velikosti I/OS. Dalším případem většího počtu vstupně-výstupních vstupně-výstupních souborů pro čtení dat může být zálohování databáze HANA. V důsledku toho by ukládání do mezipaměti pro čtení většinou nedávalo smysl vzhledem k tomu, že ve většině případů je třeba všechny svazky datových souborů přečíst Kompletně.
- Zápis proti datovým souborům se nachází v shlukech na základě zotavení po havárii HANA úložných bodů a HANA. Zápis úložných bodů je asynchronní a neobsahuje žádné uživatelské transakce. Zápis dat během zotavení po havárii je kritický pro výkon, aby systém mohl rychle reagovat. Zotavení po havárii by ale mělo být spíše výjimečné situace
- Ze souborů pro opětovné provedení služby HANA se špatně žádné čtení. Výjimky jsou velké vstupně-výstupy při provádění záloh protokolů transakcí, zotavení po havárii nebo ve fázi restartování instance HANA.  
- Hlavní zatížení proti souboru protokolu SAP HANA znovu zapisuje. V závislosti na povaze úlohy můžete mít vstupně-výstupní operace až na 4 KB nebo jiné velikosti vstupně-výstupních operací s 1 MBm nebo více. Latence zápisu proti SAP HANA protokolu opětovného provedení je kritická.
- Všechny zápisy musí být trvale uchovány na disku spolehlivě.

**Základě V důsledku těchto pozorovaných vstupně-výstupních vzorů SAP HANA je vhodné nastavit ukládání do mezipaměti pro různé svazky pomocí Azure Premium Storage jako:**

- **/Hana/data** – bez ukládání do mezipaměti
- **/Hana/log** -žádné ukládání do mezipaměti – výjimka pro řadu M-Series (viz dále v tomto dokumentu)
- mezipaměť pro čtení **/Hana/Shared**


Při změně velikosti nebo rozhodování pro virtuální počítač si taky Pamatujte na propustnost v/v. Celková propustnost úložiště virtuálních počítačů je popsána v článku [paměťově optimalizované velikosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Režim plánovače I/O systému Linux
Linux má několik různých režimů plánování vstupu a výstupu. Běžnými doporučeními pro dodavatele Linux a SAP je překonfigurování režimu I/O Scheduleru pro svazky disků z režimu **CFQ** do režimu **NoOp** . Podrobnosti jsou odkazovány v [#1984798 SAP Note](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="production-storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Řešení produkčního úložiště s využitím Azure Akcelerátor zápisu pro virtuální počítače Azure řady M-Series
Azure Akcelerátor zápisu je funkce, která se zavádí výhradně pro virtuální počítače Azure M-Series. Jako název uvádíme účel funkce a vylepšit latenci vstupu a výstupu zápisů do Premium Storage Azure. V případě SAP HANA se má Akcelerátor zápisu použít jenom pro svazek **/Hana/log** . Proto jsou **/Hana/data** a **/Hana/log** samostatné svazky s Azure akcelerátor zápisu podporují pouze svazek **/Hana/log** . 

> [!IMPORTANT]
> SAP HANA certifikace pro virtuální počítače Azure M-Series je výhradně ve službě Azure Akcelerátor zápisu pro svazek **/Hana/log** . V důsledku toho se očekává, že produkční scénář SAP HANA nasazení na virtuálních počítačích Azure řady M-Series se pro svazek **/Hana/log** nakonfiguruje s využitím Azure akcelerátor zápisu.  

> [!NOTE]
> V případě produkčních scénářů ověřte, jestli určitý typ virtuálního počítače podporuje SAP HANA SAP v [dokumentaci SAP pro IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Základě Doporučené konfigurace pro produkční scénáře vypadají jako:**

| SKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM | Max. VM I/O<br /> Propustnost | /hana/data | /hana/log | /hana/shared | Rozsah/root | /usr/sap | Hana a zálohování |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Typy virtuálních počítačů s M416xx_v2 ještě nejsou od Microsoftu k dispozici k veřejnému. Ověřte, zda propustnost úložiště pro různé navrhované svazky splňuje zatížení, které chcete spustit. Pokud zatížení vyžaduje větší objemy pro **/Hana/data** a **/Hana/log**, je potřeba zvýšit počet virtuálních pevných disků Azure Premium Storage. Změna velikosti svazku s více virtuálními disky, než je uvedené, zvyšuje počet IOPS a propustnost vstupně-výstupních operací v rámci omezení typu virtuálního počítače Azure.

Azure Akcelerátor zápisu funguje jenom ve spojení se službou [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Proto musí být alespoň disky Premium Storage Azure tvořící svazek **/Hana/log** nasazené jako spravované disky.

Existují omezení pro Azure Premium Storage VHD na jeden virtuální počítač, která může podporovat Azure Akcelerátor zápisu. Aktuální limity jsou:

- 16 VHD pro virtuální počítač s M128xx a M416xx
- 8 VHD pro virtuální počítač s M64xx a M208xx
- 4 virtuální pevné disky pro virtuální počítač s M32xx

Podrobnější pokyny k tomu, jak povolit Azure Akcelerátor zápisu, najdete v článku [akcelerátor zápisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Podrobnosti a omezení pro Azure Akcelerátor zápisu najdete ve stejné dokumentaci.

**Základě Pro disky, které tvoří/Hana/log svazek, je nutné použít Akcelerátor zápisu.**


## <a name="cost-conscious-azure-storage-configuration"></a>Konfigurace Azure Storage vědomosti nákladů
Následující tabulka uvádí konfiguraci typů virtuálních počítačů, které zákazníci používají také k hostování SAP HANA na virtuálních počítačích Azure. K dispozici můžou být některé typy virtuálních počítačů, které nemusí splňovat všechna kritéria pro úložiště SAP HANA nebo nejsou oficiálně podporované SAP HANA pomocí SAP. Ale proto se těmto virtuálním počítačům zdálo udělat v neprodukčních scénářích nějaké možnosti. **/Hana/data** a **/Hana/log** jsou zkombinovány na jeden svazek. V důsledku toho může použití Azure Akcelerátor zápisu znamenat omezení z hlediska IOPS.

> [!NOTE]
> V případě scénářů podporovaných systémem SAP ověřte, zda je určitý typ virtuálního počítače podporován SAP HANA SAP v [dokumentaci SAP pro IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Změna z dřívějších doporučení pro řešení s vědomím nákladů je přechod z [HDD úrovně Standard disků Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) na lepší výkon a spolehlivější [SSD úrovně Standard disky Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) .


| SKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM | Max. VM I/O<br /> Propustnost | /Hana/data a/Hana/log<br /> prokládaný pomocí LVM nebo MDADM | /hana/shared | Rozsah/root | /usr/sap | Hana a zálohování |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


Typy virtuálních počítačů s M416xx_v2 ještě nejsou od Microsoftu k dispozici k veřejnému. Disky doporučené pro menší typy virtuálních počítačů se 3 x P20 neodesílá svazky týkající se doporučení místa podle dokumentu [White paper k úložišti SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Nicméně možnost zobrazená v tabulce byla provedena za účelem zajištění dostatečné propustnosti disku pro SAP HANA. Pokud budete potřebovat změny ve svazku **/Hana/Backup** , který měl velikost pro uchovávání záloh, které reprezentují dvojnásobek paměti, můžete ho klidně upravit.   
Ověřte, zda propustnost úložiště pro různé navrhované svazky splňuje zatížení, které chcete spustit. Pokud zatížení vyžaduje větší objemy pro **/Hana/data** a **/Hana/log**, je potřeba zvýšit počet virtuálních pevných disků Azure Premium Storage. Změna velikosti svazku s více virtuálními disky, než je uvedené, zvyšuje počet IOPS a propustnost vstupně-výstupních operací v rámci omezení typu virtuálního počítače Azure. 

> [!NOTE]
> Výše uvedené konfigurace by nevyužily [smlouvy SLA pro virtuální počítače Azure s jedním virtuálním počítačem](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) , protože využívají kombinaci Azure Premium Storage a služby Azure Storage úrovně Standard. Výběr byl však vybrán, aby bylo možné optimalizovat náklady. Pro všechny disky, které jsou uvedené jako Azure Storage úrovně Standard (SXX), musíte zvolit Premium Storage, aby konfigurace virtuálního počítače odpovídala smlouvě SLA pro jeden virtuální počítač Azure.


> [!NOTE]
> Uvedená doporučení pro konfiguraci disků cílí na minimální požadavky, které SAP vyjádří ke svým poskytovatelům infrastruktury. V reálných scénářích nasazení a úloh v reálném čase byly zjištěny situace, kdy tato doporučení ještě neposkytovala dostatek možností. Může se jednat o situace, kdy zákazník vyžadoval rychlejší navýšení dat po restartování HANA nebo kde konfigurace zálohování vyžadovala větší šířku pásma úložiště. Jiné případy zahrnují **/Hana/log** , kde pro konkrétní zatížení nepostačují 5000 IOPS. Proto proveďte tato doporučení jako výchozí bod a přizpůsobte je na základě požadavků úlohy.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Konfigurace úložiště Azure Ultra disk pro SAP HANA
Microsoft se v procesu zavádí nový typ úložiště Azure s názvem [Azure Ultra disk](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#ultra-disk). Velký rozdíl mezi službou Azure Storage, která je doposud dostupná a Ultra disk, je, že možnosti disku už nejsou vázané na velikost disku. Jako zákazník můžete definovat tyto možnosti pro ultra disk:

- Velikost disku v rozsahu od 4 GiB do 65 536 GiB
- Rozsah IOPS od 100 IOPS do 160K IOPS (maximální počet závisí na typech virtuálních počítačů).
- Propustnost úložiště z 300 MB/s do 2 000 MB/s

Ultra disk poskytuje možnost definovat jeden disk, který splňuje vaši velikost, IOPS a rozsah propustnosti disku. Místo používání správců logických svazků, jako je LVM nebo MDADM, na Azure Premium Storage k vytváření svazků, které splňují požadavky na propustnost vstupně-výstupních operací a úložiště. Můžete spouštět kombinaci konfigurací mezi disky Ultra a Premium Storage. V důsledku toho můžete omezit využití disku Ultra disk na/Hana/data a/Hana/log svazky na výkon a další svazky pokrýt pomocí Azure Premium Storage

> [!IMPORTANT]
> Ultra disk ještě není ve všech oblastech Azure a ještě nepodporují všechny typy virtuálních počítačů. Podrobné informace o dostupnosti Ultra disk a o podporovaných rodinách virtuálních počítačů najdete v článku o [tom, jaké typy disků jsou dostupné v Azure?](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#ultra-disk).

| SKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM | Max. VM I/O<br /> Propustnost | /Hana/data svazek | /Hana/data v/v – propustnost | /Hana/data IOPS | /Hana/log svazek | /Hana/log v/v – propustnost | /Hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1 200 MB/s | 600 GB | 700 MB/s | 7500 | 512 GB | 500 MB/s  | 2000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 500 MB/s | 7500 | 256 GB | 500 MB/s  | 2000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 500 MB/s | 7500 | 256 GB | 500 MB/s  | 2000 |
| M64ls | 512 GiB | 1000 MB/s | 600 GB | 500 MB/s | 7500 | 512 GB | 500 MB/s  | 2000 |
| M64s | 1000 GiB | 1 000 MB/s |  1 200 GB | 500 MB/s | 7500 | 512 GB | 500 MB/s  | 2000 |
| M64ms | 1750 GiB | 1 000 MB/s | 2100 GB | 500 MB/s | 7500 | 512 GB | 500 MB/s  | 2000 |
| M128s | 2000 GiB | 2 000 MB/s |2400 GB | 1200 MB/s |9000 | 512 GB | 800 MB/s  | 2000 | 
| M128ms | 3800 GiB | 2 000 MB/s | 4800 GB | 1200 MB/s |9000 | 512 GB | 800 MB/s  | 2000 | 
| M208s_v2 | 2850 GiB | 1 000 MB/s | 3500 GB | 1000 MB/s | 9000 | 512 GB | 500 MB/s  | 2000 | 
| M208ms_v2 | 5700 GiB | 1 000 MB/s | 7200 GB | 1000 MB/s | 9000 | 512 GB | 500 MB/s  | 2000 | 
| M416s_v2 | 5700 GiB | 2 000 MB/s | 7200 GB | 1500M b/s | 9000 | 512 GB | 800 MB/s  | 2000 | 
| M416ms_v2 | 11400 GiB | 2 000 MB/s | 14400 GB | 1500 MB/s | 9000 | 512 GB | 800 MB/s  | 2000 |   

Typy virtuálních počítačů s M416xx_v2 ještě nejsou od Microsoftu k dispozici k veřejnému. Uvedené hodnoty mají být výchozím bodem a je nutné je vyhodnotit proti skutečným požadavkům. Výhodou pro Azure Ultra disk je, že hodnoty pro vstupně-výstupní operace a propustnost můžou být přizpůsobené bez nutnosti vypnout virtuální počítač nebo zastavit zatížení, které se v systému používá.   

> [!NOTE]
> Zatím nejsou k dispozici snímky úložiště s úložištěm Ultra disk. Tím se zablokuje použití snímků virtuálních počítačů s Azure Backup službami.

## <a name="next-steps"></a>Další postup
Další informace naleznete v tématu:

- [SAP HANA Průvodce vysokou dostupností pro virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
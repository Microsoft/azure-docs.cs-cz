---
title: SAP HANA konfigurací úložiště virtuálních počítačů Azure | Microsoft Docs
description: Doporučení úložiště pro virtuální počítače, které mají v nich nasazené SAP HANA.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure HANA, úložiště Ultra disk, Premium Storage
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/03/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 48d1c04e59d316ac19000f5b890c0cb1c96fb213
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549435"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Konfigurace úložiště virtuálních počítačů Azure SAP HANA

Azure poskytuje různé typy úložiště, které jsou vhodné pro virtuální počítače Azure, na kterých běží SAP HANA. **SAP HANA certifikované typy úložiště Azure** , které je možné zvážit pro seznam nasazení SAP HANA, jako je: 

- Azure Premium SSD nebo Storage úrovně Premium 
- [Disky Ultra](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Další informace o těchto typech disků najdete v článku [Azure Storage typy pro úlohu SAP](./planning-guide-storage.md) a [Výběr typu disku](../../disks-types.md) .

Azure nabízí dvě metody nasazení pro virtuální pevné disky v Azure Standard a Premium Storage. Očekáváme, že využijete [Azure Managed disk](https://azure.microsoft.com/services/managed-disks/) pro nasazení blokového úložiště Azure. 

Seznam typů úložišť a jejich SLA v rámci vstupně-výstupních operací a propustnosti úložiště najdete v [dokumentaci k Azure pro službu Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Nezávisle na zvoleném typu úložiště Azure je nutné, aby systém souborů používaný v tomto úložišti podporoval SAP pro konkrétní operační systém a systém DBMS. [Poznámka k podpoře SAP #2972496](https://launchpad.support.sap.com/#/notes/2972496) uvádí podporované systémy souborů pro různé operační systémy a databáze, včetně SAP HANA. To platí pro všechny svazky SAP HANA můžou mít přístup ke čtení a zápisu pro libovolný úkol. Při použití systému souborů NFS v Azure pro SAP HANA platí další omezení verze systému souborů NFS, jak je uvedeno dále v tomto článku. 


Minimální SAP HANA certifikované podmínky pro různé typy úložišť: 

- Služba Azure Premium Storage podporuje službu Azure Premium Storage – **/hana/log** [akcelerátor zápisu](../../how-to-enable-write-accelerator.md). Svazek **/Hana/data** může být umístěný na Premium Storage bez použití Azure akcelerátor zápisu nebo na disku Ultra.
- Azure Ultra disk alespoň pro svazek **/Hana/log** . Svazek **/Hana/data** můžete umístit do úložiště Premium Storage bez použití Azure akcelerátor zápisu nebo za účelem dosažení rychlejšího restartování Ultra disk
- Svazky **systému souborů NFS v 4.1** nad Azure NetApp Files **/Hana/log a/Hana/data**. Svazek/Hana/Shared může používat protokol NFS v3 nebo NFS verze 4.1.

Některé typy úložišť lze kombinovat. Například je možné umístit **/Hana/data** do služby Premium Storage a **/Hana/log** je možné umístit do úložiště Ultra disk, aby se dosáhlo požadované nízké latence. Pokud používáte svazek založený na ANF pro **/Hana/data**, musí být  **/Hana/log** svazek založen na systému souborů NFS nad ANF. Použití systému souborů NFS nad ANF pro jeden ze svazků (jako je/Hana/data) a Azure Premium Storage nebo Ultra disk pro ostatní svazky (jako **/Hana/log** **) se nepodporuje.**

V místním světě se zřídka postará o subsystémech I/O a jeho schopnosti. Důvodem je, že dodavatel zařízení musí zajistit, aby byly splněny minimální požadavky na úložiště pro SAP HANA. Při sestavování infrastruktury Azure byste si měli být vědomi některých z těchto požadavků na vydaných SAP. Některé z minimálních vlastností propustnosti, které SAP doporučuje, jsou:

- Čtení a zápis v **/Hana/logě** 250 MB/s s 1 MB vstupně-výstupních velikostí
- Aktivita čtení minimálně 400 MB/s pro **/Hana/data** pro velikost I/O 16 mb a 64 MB
- Aktivita zápisu minimálně 250 MB/s pro **/Hana/data** s velikostí 16 mb a 64 MB v/v

Vzhledem k toho, že nízká latence úložiště je pro systémy DBMS velmi kritická, i když DBMS, jako je SAP HANA, udržujte data v paměti. Kritická cesta v úložišti většinou představuje zápisy do protokolu transakcí systémů DBMS. Ale také operace jako psaní úložných bodů nebo načítání dat v paměti po zotavení po havárii můžou být kritické. Proto je **nutné** využívat Azure Premium Storage, Ultra disk nebo ANF pro svazky **/Hana/data** a **/Hana/log** . 


V části výběr konfigurace úložiště pro HANA se můžou zobrazit tyto zásady GUID:

- Rozhodněte o typu úložiště založeném na [typech Azure Storage pro úlohu SAP](./planning-guide-storage.md) a [Vyberte typ disku](../../disks-types.md) .
- Celková propustnost vstupně-výstupních operací virtuálních počítačů a omezení IOPS při změně velikosti nebo rozhodování pro virtuální počítač. Celková propustnost úložiště virtuálního počítače je popsána v článku [paměťově optimalizované velikosti virtuálních počítačů](../../sizes-memory.md) .
- Při rozhodování o konfiguraci úložiště se snažte zůstat pod celkovou propustností virtuálního počítače s konfigurací svazku **/Hana/data** . Zápisem úložných bodů SAP HANA může být agresivní vystavení vstupně-výstupních volání. Při zápisu do uloženého bodu je možné snadno nabídnout až omezení propustnosti **/Hana/data** svazku. Pokud vaše disky, které sestavují svazek **/Hana/data** , mají vyšší propustnost, než umožňuje váš virtuální počítač, můžete se v situacích, kdy propustnost využívané zápisem uloženého bodu, narušovat pomocí propustnosti zápisů protokolů znovu. Situace, která může ovlivnit propustnost aplikace


> [!IMPORTANT]
> Návrhy pro konfigurace úložiště jsou určené jako pokyny, jak začít s. Spuštění úloh a analýza vzorů využití úložiště vám může znamenat, že nepoužíváte celou šířku pásma úložiště nebo IOPS. Můžete zvážit možnost úložiště a pak. V opačném případě vaše zatížení může potřebovat větší propustnost úložiště než navržené s těmito konfiguracemi. V důsledku toho může být nutné nasadit větší kapacitu, IOPS nebo propustnost. V terénu napětí mezi požadovanou kapacitou úložiště, požadovanou latencí úložiště, propustností úložiště a minimální náročnou konfigurací nabízí Azure dostatek různých typů úložiště s různými možnostmi a různé cenové body, které vám umožní najít a upravit správné ohrožení pro vás a vaše úlohy HANA.


## <a name="stripe-sets-versus-sap-hana-data-volume-partitioning"></a>Prokládané sady versus SAP HANA dělení datových svazků
Pomocí služby Azure Premium Storage se můžete setkat s nejlepším poměrem cen a výkonu při rozložení svazku **/Hana/data** nebo **/Hana/log** na několik disků Azure. Místo toho, abyste nasadili větší diskový svazek, který poskytuje další informace o IOPS nebo propustnosti. Zatím to bylo dosaženo LVM a MDADM správců svazků, které jsou součástí systému Linux. Metoda prokládaného disku je v desítkových a dobře známých. Vzhledem k tomu, že tyto prokládané svazky mají přístup k možnostem IOPS nebo propustnosti, které můžete potřebovat, se při správě těchto prokládaných svazků zvyšují složitosti. Zejména v případech, kdy se svazky potřebují rozšířit o kapacitu. Minimálně pro **/Hana/data** představil SAP alternativní metodu, která dosahuje stejného cíle jako prokládání na více discích Azure. Vzhledem k tomu, že SAP HANA 2,0 SPS03, je IndexServer HANA schopný rozkládat svou aktivitu v/v mezi více datovými soubory HANA, které jsou umístěné na různých discích Azure. Výhodou je, že se nemusíte starat o vytváření a správu prokládaného svazku napříč různými disky Azure. SAP HANA funkce dělení datových svazků je podrobně popsána v těchto částech:

- [Příručka pro správce HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [Blog o SAP HANA – dělení datových svazků](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [Poznámka ke SAP #2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [Poznámka ke SAP #2700123](https://launchpad.support.sap.com/#/notes/2700123)

Při čtení podrobností je zřejmé, že využití této funkce v sadách odstínů v nástroji Volume Manager trvá. Také zjistíte, že dělení datových svazků HANA nefunguje jenom pro úložiště bloků Azure, jako je Azure Premium Storage. Tuto funkci můžete použít i pro vyložení napříč sdílenými složkami NFS v případě, že tyto sdílené složky mají omezení pro IOPS nebo propustnost.  


## <a name="linux-io-scheduler-mode"></a>Režim plánovače I/O systému Linux
Linux má několik různých režimů plánování vstupu a výstupu. Běžným doporučením pro dodavatele a SAP systému Linux je překonfigurování režimu I/O Scheduleru pro svazky na disku z režimu **MQ-konečný** nebo **kyber** do **NoOp** (non-) nebo **none** for (pro více front), pokud to ještě neudělaly profily SLES saptune. Podrobnosti jsou odkazovány v: 

- [Poznámka ke SAP #1984787](https://launchpad.support.sap.com/#/notes/1984787)
- [Poznámka ke SAP #2578899](https://launchpad.support.sap.com/#/notes/2578899) 
- [Problém s nastavením NoOp v SLES 12 SP4](https://www.suse.com/support/kb/doc/?id=000019547)

V Red Hat ponechte nastavení stanovená konkrétními profily pro různé aplikace SAP.


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Řešení s využitím služby Premium Storage a Azure Akcelerátor zápisu pro virtuální počítače řady Azure M-Series
Azure Akcelerátor zápisu je funkce, která je k dispozici pro virtuální počítače Azure M-Series výhradně. Jako název uvádíme účel funkce k vylepšení latence v/v zápisu do služby Azure Premium Storage. V případě SAP HANA se má Akcelerátor zápisu použít jenom pro svazek **/Hana/log** . Proto jsou **/Hana/data** a **/Hana/log** samostatné svazky s Azure akcelerátor zápisu podporují pouze svazek **/Hana/log** . 

> [!IMPORTANT]
> Při použití služby Azure Premium Storage je použití Azure [akcelerátor zápisu](../../how-to-enable-write-accelerator.md) pro svazek **/Hana/log** povinné. Akcelerátor zápisu je k dispozici pro služby Premium Storage a řady M-Series a Mv2-Series jenom pro virtuální počítače. Akcelerátor zápisu nepracuje v kombinaci s jinými rodinami virtuálních počítačů Azure, jako je Esv3 nebo Edsv4.

Doporučení pro ukládání do mezipaměti pro disky Azure Premium níže jsou popsány v parametrech v/v pro SAP HANA, jako je:

- K datovým souborům HANA se špatně všechny úlohy čtení. Po restartování instance HANA nebo při načtení dat do HANA jsou výjimky velké velikosti I/OS. Dalším případem většího počtu vstupně-výstupních vstupně-výstupních souborů pro čtení dat může být zálohování databáze HANA. V důsledku toho by ukládání do mezipaměti pro čtení většinou nedávalo smysl vzhledem k tomu, že ve většině případů je třeba všechny svazky datových souborů přečíst Kompletně. 
- Zápis proti datovým souborům se nachází v shlukech na základě zotavení po havárii HANA úložných bodů a HANA. Zápis úložných bodů je asynchronní a neobsahuje žádné uživatelské transakce. Zápis dat během zotavení po havárii je kritický pro výkon, aby systém mohl rychle reagovat. Zotavení po havárii by ale mělo být spíše výjimečné situace
- Ze souborů pro opětovné provedení služby HANA se špatně žádné čtení. Výjimky jsou velké vstupně-výstupy při provádění záloh protokolů transakcí, zotavení po havárii nebo ve fázi restartování instance HANA.  
- Hlavní zatížení proti souboru protokolu SAP HANA znovu zapisuje. V závislosti na povaze úlohy můžete mít vstupně-výstupní operace až na 4 KB nebo jiné velikosti vstupně-výstupních operací s 1 MBm nebo více. Latence zápisu proti SAP HANA protokolu opětovného provedení je kritická.
- Všechny zápisy musí být trvale uchovány na disku spolehlivě.

**Doporučení: Výsledkem těchto pozorovaných vstupně-výstupních vzorů SAP HANA je ukládání do mezipaměti pro různé svazky pomocí služby Azure Premium Storage, jako třeba:**

- **/Hana/data** – žádné ukládání do mezipaměti nebo čtení do mezipaměti
- **/Hana/log** – žádné ukládání do mezipaměti – výjimka pro virtuální počítače M a Mv2-Series, kde by mělo být povoleno Azure akcelerátor zápisu 
- mezipaměť pro čtení **/Hana/Shared**
- **Disk s operačním systémem** – neměňte výchozí ukládání do mezipaměti, které se nastavuje v Azure v době vytváření virtuálního počítače.


Pokud používáte LVM nebo mdadm k sestavování sad Stripe Sets na několika discích Azure Premium, je potřeba definovat velikosti pruhů. Tyto velikosti se liší mezi **/Hana/data** a **/Hana/log**. **Doporučení: jako velikost pruhů se má doporučení použít:**

- 256 KB pro **/Hana/data**
- 64 KB pro **/Hana/log**

> [!NOTE]
> Velikost proložení pro **/Hana/data** se změnila z předchozích doporučení volajících 64 kb nebo 128 KB 256 na základě zkušeností zákazníků s novějšími verzemi systému Linux. Velikost 256 KB poskytuje poněkud lepší výkon. Také jsme změnili doporučení pro svazky velikostí **/Hana/log** z 32 kb na 64 KB, aby bylo možné dosáhnout dostatečné propustnosti s většími velikostmi vstupně-výstupních operací.

> [!NOTE]
> Nemusíte konfigurovat žádnou redundanci pomocí svazků RAID, protože blokové úložiště Azure udržuje tři image virtuálního pevného disku. Použití Stripe sady s disky Azure Premium je čistě ke konfiguraci svazků, které poskytují dostatečný počet IOPS nebo propustnost vstupně-výstupních operací.

Celkový počet virtuálních pevných disků Azure v rámci sady prokládaných svazků je accumulative ze strany IOPS a propustnosti úložiště. Takže pokud zadáte prokládanou sadu napříč více než 3 × P30 disky úložiště Azure Premium, měli byste vám zadat trojnásobku IOPS a trojnásobnou propustnost úložiště jednoho disku P30 služby Azure Premium Storage.

> [!IMPORTANT]
> Pokud používáte LVM nebo mdadm jako správce svazků k vytváření prokládaných sad na více discích Azure Premium, nesmí být tři SAP HANA systémy souborů/data,/log a/Shared vloženy do výchozí nebo kořenové skupiny svazků. Důrazně doporučujeme postupovat podle pokynů pro dodavatele pro Linux, které obvykle vytvářejí jednotlivé skupiny svazků pro/data,/log a/Shared..


### <a name="azure-burst-functionality-for-premium-storage"></a>Funkce Azure Burst pro Premium Storage
Pro disky Azure Premium Storage menší nebo rovny 512 GiB v kapacitě se nabízí funkce shlukování. Přesný způsob, jak funguje shlukování disku, je popsaný v článku o rozložení [disku na disk](../../disk-bursting.md). Po přečtení článku rozumíte konceptu časově rozlišených vstupně-výstupních operací a propustnosti v časech, kdy je zatížení v/v pod nominálními IOPS a propustností disků (podrobnosti o nominální propustnosti najdete v tématu [ceny spravovaného disku](https://azure.microsoft.com/pricing/details/managed-disks/)). Chystáte se rozlišit rozdíl mezi vstupně-výstupními operacemi a propustností mezi aktuálním využitím a jmenovitými hodnotami disku. Počet shluků je omezen na maximálně 30 minut.

V ideálních případech, kde je možné naplánovat tuto funkci shlukování, se pravděpodobně jedná o svazky nebo disky, které obsahují datové soubory pro různé systémy DBMS. U vstupně-výstupních úloh, které jsou na těchto svazcích očekávány, se očekává, že budou vypadat jako v případě malých až středních systémů.

- Nízká až střední zátěž pro čtení, protože data v ideálním případě jsou ukládána do mezipaměti v paměti nebo jako v případě HANA by měla být celá v paměti.
- Shluky zápisu aktivované kontrolními body databáze nebo úložných bodů, které se vydávají pravidelně
- Úlohy zálohování, které se čtou do souvislého streamu v případech, kdy se zálohy neprovádějí pomocí snímků úložiště
- Pro SAP HANA načtení dat do paměti po restartování instance

Obzvláště na menších systémech DBMS, kde vaše úloha zpracovává jenom několik stovek transakcí za sekundu, může tato funkce zvýšit smysl i pro disky nebo svazky, které uchovávají transakce nebo protokol opětovného přihlášení. Očekávaná úloha na disku nebo svazcích vypadá takto:

- Pravidelné zápisy na disk, které jsou závislé na úloze, a povaha úlohy, protože každé potvrzení vydané aplikací je nejspíš aktivovat vstupně-výstupní operace.
- Vyšší zatížení v propustnosti pro případy provozních úloh, jako je vytváření nebo opětovné sestavení indexů
- Čtení shluků při provádění protokolu transakcí nebo zálohování protokolu znovu


### <a name="production-recommended-storage-solution-based-on-azure-premium-storage"></a>Řešení doporučené pro produkční úložiště založené na Azure Premium Storage

> [!IMPORTANT]
> SAP HANA certifikace pro virtuální počítače Azure M-Series je výhradně ve službě Azure Akcelerátor zápisu pro svazek **/Hana/log** . V důsledku toho se očekává, že produkční scénář SAP HANA nasazení na virtuálních počítačích Azure řady M-Series se pro svazek **/Hana/log** nakonfiguruje s využitím Azure akcelerátor zápisu.  

> [!NOTE]
> Ve scénářích, které zahrnují Azure Premium Storage, implementujeme do konfigurace možnosti shlukování. Pokud používáte testovací nástroje pro úložiště bez ohledu na tvar nebo formu, mějte na paměti, jak [funguje Azure Premium disking](../../disk-bursting.md) . Při spuštění testů úložiště dodaných prostřednictvím nástroje SAP HWCCT nebo HCMT neočekáváme, že všechny testy budou kritéria předávat, protože některé testy překročí kredity, které můžete nashromáždit. Hlavně v případě, že všechny testy běží sekvenčně bez přerušení.


> [!NOTE]
> V případě produkčních scénářů ověřte, jestli určitý typ virtuálního počítače podporuje SAP HANA SAP v [dokumentaci SAP pro IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Doporučení: doporučené konfigurace s Azure Premium Storage pro produkční scénáře vypadají jako:**

Konfigurace pro svazek SAP **/Hana/data** :

| Skladová položka virtuálního počítače | Paměť RAM | Max. VSTUPNĚ-VÝSTUPNÍ OPERACE VIRTUÁLNÍHO POČÍTAČE<br /> Propustnost | /hana/data | Zřízená propustnost | Maximální propustnost shluku | IOPS | Shlukový IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MB/s | 4 x P6 | 200 MB/s | 680 MB/s | 960 | 14 000 |
| M32ls | 256 GB | 500 MB/s | 4 x P6 | 200 MB/s | 680 MB/s | 960 | 14 000 |
| M64ls | 512 GiB | 1 000 MB/s | 4 x P10 | 400 MB/s | 680 MB/s | 2 000 | 14 000 |
| M64s | 1 000 GiB | 1 000 MB/s | 4 x P15 | 500 MB/s | 680 MB/s | 4 400 | 14 000 |
| M64ms | 1 750 GiB | 1 000 MB/s | 4 x P20 | 600 MB/s | 680 MB/s | 9 200 | 14 000 |  
| M128s | 2 000 GiB | 2 000 MB/s | 4 x P20 | 600 MB/s | 680 MB/s | 9 200| 14 000 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 4 x P30 | 800 MB/s | bez shlukování | 20 000 | bez shlukování | 
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 x P30 | 800 MB/s | bez shlukování | 20 000| bez shlukování | 
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 4 x P40 | 1 000 MB/s | bez shlukování | 30 000 | bez shlukování |
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 4 x P40 | 1 000 MB/s | bez shlukování | 30 000 | bez shlukování |
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 4 x P50 | 2 000 MB/s | bez shlukování | 30 000 | bez shlukování |


Pro svazek **/Hana/log** . konfigurace by vypadala takto:

| Skladová položka virtuálního počítače | Paměť RAM | Max. VSTUPNĚ-VÝSTUPNÍ OPERACE VIRTUÁLNÍHO POČÍTAČE<br /> Propustnost | **/Hana/log** svazek | Zřízená propustnost | Maximální propustnost shluku | IOPS | Shlukový IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MB/s | 3 x P10 | 300 MB/s | 510 MB/s | 1 500 | 10 500 | 
| M32ls | 256 GB | 500 MB/s | 3 x P10 | 300 MB/s | 510 MB/s | 1 500 | 10 500 | 
| M64ls | 512 GiB | 1 000 MB/s | 3 x P10 | 300 MB/s | 510 MB/s | 1 500 | 10 500 | 
| M64s | 1 000 GiB | 1 000 MB/s | 3 x P15 | 375 MB/s | 510 MB/s | 3 300 | 10 500 | 
| M64ms | 1 750 GiB | 1 000 MB/s | 3 x P15 | 375 MB/s | 510 MB/s | 3 300 | 10 500 |  
| M128s | 2 000 GiB | 2 000 MB/s | 3 x P15 | 375 MB/s | 510 MB/s | 3 300 | 10 500|  
| M128ms | 3 800 GiB | 2 000 MB/s | 3 x P15 | 375 MB/s | 510 MB/s | 3 300 | 10 500 | 
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 3 x P15 | 375 MB/s | 510 MB/s | 3 300 | 10 500 |  
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 3 x P15 | 375 MB/s | 510 MB/s | 3 300 | 10 500 |  
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 3 x P15 | 375 MB/s | 510 MB/s | 3 300 | 10 500 |  
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 3 x P15 | 375 MB/s | 510 MB/s | 3 300 | 10 500 | 


U ostatních svazků by konfigurace vypadala takto:

| Skladová položka virtuálního počítače | Paměť RAM | Max. VSTUPNĚ-VÝSTUPNÍ OPERACE VIRTUÁLNÍHO POČÍTAČE<br /> Propustnost | /hana/shared | Rozsah/root | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 1 × P15 | 1 × P6 | 1 × P6 |
| M32ls | 256 GB | 500 MB/s |  1 × P15 | 1 × P6 | 1 × P6 |
| M64ls | 512 GiB | 1000 MB/s | 1 × P20 | 1 × P6 | 1 × P6 |
| M64s | 1 000 GiB | 1 000 MB/s | 1 × P30 | 1 × P6 | 1 × P6 |
| M64ms | 1 750 GiB | 1 000 MB/s | 1 × P30 | 1 × P6 | 1 × P6 | 
| M128s | 2 000 GiB | 2 000 MB/s | 1 × P30 | 1 × P10 | 1 × P6 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 1 × P30 | 1 × P10 | 1 × P6 |
| M208s_v2 | 2 850 GiB | 1 000 MB/s |  1 × P30 | 1 × P10 | 1 × P6 |
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 1 × P30 | 1 × P10 | 1 × P6 | 
| M416s_v2 | 5 700 GiB | 2 000 MB/s |  1 × P30 | 1 × P10 | 1 × P6 | 
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 1 × P30 | 1 × P10 | 1 × P6 | 


Ověřte, zda propustnost úložiště pro různé navrhované svazky splňuje zatížení, které chcete spustit. Pokud zatížení vyžaduje větší objemy pro **/Hana/data** a **/Hana/log**, je potřeba zvýšit počet virtuálních pevných disků Azure Premium Storage. Změna velikosti svazku s více virtuálními disky, než je uvedené, zvyšuje počet IOPS a propustnost vstupně-výstupních operací v rámci omezení typu virtuálního počítače Azure.

Azure Akcelerátor zápisu funguje jenom ve spojení se službou [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Proto musí být alespoň disky Azure Premium Storage, které tvoří **/Hana/log** svazek, nasazeny jako spravované disky. Podrobnější pokyny a omezení služby Azure Akcelerátor zápisu najdete v článku [akcelerátor zápisu](../../how-to-enable-write-accelerator.md).

Pro virtuální počítače s certifikací HANA řady Azure [Esv3](../../ev3-esv3-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#esv3-series) a [EDSV4](../../edv4-edsv4-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#edsv4-series)je nutné ANF pro **/Hana/data** a **/Hana/log** svazek. Nebo potřebujete místo Azure Premium Storage využívat službu Azure Ultra disk Storage jenom pro svazek **/Hana/log** . V důsledku toho by konfigurace pro **/Hana/data** svazek v Azure Premium Storage vypadala takto:

| Skladová položka virtuálního počítače | Paměť RAM | Max. VSTUPNĚ-VÝSTUPNÍ OPERACE VIRTUÁLNÍHO POČÍTAČE<br /> Propustnost | /hana/data | Zřízená propustnost | Maximální propustnost shluku | IOPS | Shlukový IOPS |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | 160 GiB | 480 MB/s | 3 x P10 | 300 MB/s | 510 MB/s | 1 500 | 10 500 |
| E32ds_v4 | 256 GB | 768 MB/s | 3 x P10 |  300 MB/s | 510 MB/s | 1 500 | 10 500|
| E48ds_v4 | 384 GiB | 1 152 MB/s | 3 x P15 |  375 MB/s |510 MB/s | 3 300  | 10 500 | 
| E64ds_v4 | 504 GiB | 1 200 MB/s | 3 x P15 |  375 MB/s | 510 MB/s | 3 300 | 10 500 | 
| E64s_v3 | 432 GiB | 1 200 MB/s | 3 x P15 |  375 MB/s | 510 MB/s | 3 300 | 10 500 | 

U ostatních svazků, včetně **/Hana/log** na disku Ultra, může konfigurace vypadat takto:

| Skladová položka virtuálního počítače | Paměť RAM | Max. VSTUPNĚ-VÝSTUPNÍ OPERACE VIRTUÁLNÍHO POČÍTAČE<br /> Propustnost | /Hana/log svazek | /Hana/log v/v – propustnost | /Hana/log IOPS | /hana/shared | Rozsah/root | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MB/s | 80 GB | 250 MB/s | 1 800 | 1 × P15 | 1 × P6 | 1 × P6 |
| E32ds_v4 | 256 GB | 768 MB/s | 128 GB | 250 MB/s | 1 800 | 1 × P15 | 1 × P6 | 1 × P6 |
| E48ds_v4 | 384 GiB | 1 152 MB/s | 192 GB | 250 MB/s | 1 800 | 1 × P20 | 1 × P6 | 1 × P6 |
| E64ds_v4 | 504 GiB | 1 200 MB/s | 256 GB | 250 MB/s | 1 800 | 1 × P20 | 1 × P6 | 1 × P6 |
| E64s_v3 | 432 GiB | 1 200 MB/s | 220 GB | 250 MB/s | 1 800 | 1 × P20 | 1 × P6 | 1 × P6 |


## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Konfigurace úložiště Azure Ultra disk pro SAP HANA
Jiný typ úložiště Azure se nazývá [Azure Ultra disk](../../disks-types.md#ultra-disk). Významný rozdíl mezi službou Azure Storage, která je doposud dostupná a Ultra disk, je, že možnosti disku už nejsou svázané s velikostí disku. Jako zákazník můžete definovat tyto možnosti pro ultra disk:

- Velikost disku v rozsahu od 4 GiB do 65 536 GiB
- Rozsah IOPS od 100 IOPS do 160K IOPS (maximální počet závisí na typech virtuálních počítačů).
- Propustnost úložiště z 300 MB/s do 2 000 MB/s

Ultra disk poskytuje možnost definovat jeden disk, který splňuje vaši velikost, IOPS a rozsah propustnosti disku. Místo používání správců logických svazků, jako je LVM nebo MDADM, na Azure Premium Storage, k vytvoření svazků splňujících požadavky na propustnost vstupně-výstupních operací a úložiště. Můžete spouštět kombinaci konfigurací mezi disky Ultra disk a Premium Storage. V důsledku toho můžete omezit využití disku Ultra na výkon, který je kritický pro **/Hana/data** a **/Hana/log** , a pokrýt ostatní svazky Azure Premium Storage.

Dalšími výhodami extrémně disku může být lepší latence čtení v porovnání s Premium Storage. Rychlejší latence čtení může mít výhody, když chcete snížit dobu spuštění HANA a následné načtení dat do paměti. Pokud HANA zapisuje úložných bodů, můžou se taky vycházet z výhod úložiště Ultra disk. 

> [!NOTE]
> Ultra disk ještě není ve všech oblastech Azure a ještě nepodporují všechny typy virtuálních počítačů uvedené níže. Podrobné informace o dostupnosti Ultra disk a o podporovaných rodinách virtuálních počítačů najdete v článku o [tom, jaké typy disků jsou dostupné v Azure?](../../disks-types.md#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Provozní řešení doporučené pro produkční úložiště s čistou konfigurací disků Ultra
V této konfiguraci se svazky **/Hana/data** a **/Hana/log** udržují samostatně. Navrhované hodnoty jsou odvozeny mimo klíčové ukazatele výkonu, které SAP musí certifikovat typy virtuálních počítačů pro SAP HANA a konfigurace úložiště podle doporučení v dokumentu [White paper k úložišti SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

Doporučení často překračují minimální požadavky SAP, jak je uvedeno výše v tomto článku. Uvedená doporučení představují kompromis mezi doporučeními velikosti podle SAP a maximální propustností úložiště, kterou poskytují různé typy virtuálních počítačů.

> [!NOTE]
> Azure Ultra disk vynucuje minimálně 2 IOPS na kapacitu disku za gigabajt.


| Skladová položka virtuálního počítače | Paměť RAM | Max. VSTUPNĚ-VÝSTUPNÍ OPERACE VIRTUÁLNÍHO POČÍTAČE<br /> Propustnost | /Hana/data svazek | /Hana/data v/v – propustnost | /Hana/data IOPS | /Hana/log svazek | /Hana/log v/v – propustnost | /Hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MB/s | 200 GB | 400 MB/s | 2,500 | 80 GB | 250 MB | 1 800 |
| E32ds_v4 | 256 GB | 768 MB/s | 300 GB | 400 MB/s | 2,500 | 128 GB | 250 MB/s | 1 800 |
| E48ds_v4 | 384 GiB | 1152 MB/s | 460 GB | 400 MB/s | 3 000 | 192 GB | 250 MB/s | 1 800 |
| E64ds_v4 | 504 GiB | 1200 MB/s | 610 GB | 400 MB/s | 3 500 |  256 GB | 250 MB/s | 1 800 |
| E64s_v3 | 432 GiB | 1 200 MB/s | 610 GB | 400 MB/s | 3 500 | 220 GB | 250 MB | 1 800 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MB/s | 2,500 | 96 GB | 250 MB/s  | 1 800 |
| M32ls | 256 GB | 500 MB/s | 300 GB | 400 MB/s | 2,500 | 256 GB | 250 MB/s  | 1 800 |
| M64ls | 512 GiB | 1 000 MB/s | 620 GB | 400 MB/s | 3 500 | 256 GB | 250 MB/s  | 1 800 |
| M64s | 1 000 GiB | 1 000 MB/s |  1 200 GB | 600 MB/s | 5 000 | 512 GB | 250 MB/s  | 2,500 |
| M64ms | 1 750 GiB | 1 000 MB/s | 2 100 GB | 600 MB/s | 5 000 | 512 GB | 250 MB/s  | 2,500 |
| M128s | 2 000 GiB | 2 000 MB/s |2 400 GB | 750 MB/s | 7 000 | 512 GB | 250 MB/s  | 2,500 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 4 800 GB | 750 MB/s |9 600 | 512 GB | 250 MB/s  | 2,500 | 
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 3 500 GB | 750 MB/s | 7 000 | 512 GB | 250 MB/s  | 2,500 | 
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 7 200 GB | 750 MB/s | 14 400 | 512 GB | 250 MB/s  | 2,500 | 
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 7 200 GB | 1 000 MB/s | 14 400 | 512 GB | 400 MB/s  | 4 000 | 
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 14 400 GB | 1 500 MB/s | 28 800 | 512 GB | 400 MB/s  | 4 000 |   

**Uvedené hodnoty mají být výchozím bodem a je nutné je vyhodnotit proti skutečným požadavkům.** Výhodou pro Azure Ultra disk je, že hodnoty pro vstupně-výstupní operace a propustnost můžou být přizpůsobené bez nutnosti vypnout virtuální počítač nebo zastavit zatížení, které se v systému používá.   

> [!NOTE]
> Zatím nejsou k dispozici snímky úložiště s úložištěm Ultra disk. Tím se zablokuje použití snímků virtuálních počítačů s Azure Backup službami.


## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Svazky NFS v 4.1 na Azure NetApp Files
Podrobné informace o ANF pro HANA najdete v dokumentu o [svazcích systému souborů NFS v 4.1 Azure NetApp Files pro SAP HANA](./hana-vm-operations-netapp.md)




## <a name="cost-conscious-solution-with-azure-premium-storage"></a>Řešení na vědomí nákladů pomocí služby Azure Premium Storage
Zatím řešení Azure Premium Storage popsané v tomto dokumentu najdete v části [řešení s využitím služby Premium Storage a azure akcelerátor zápisu pro virtuální počítače řady Azure M-Series](#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines) , která se týkají SAP HANA produkčních scénářů. Jedna z vlastností produkčních konfigurací, které podporují, je oddělení svazků pro SAP HANAá data a znovu se přihlaste do dvou různých svazků. Důvodem takového oddělení je to, že charakteristiky zatížení na svazcích se liší. A že u navrhovaných produkčních konfigurací může být potřeba jiný typ ukládání do mezipaměti nebo dokonce jiné typy úložiště bloků Azure. V případě neprodukčních scénářů se některé z doporučení pro produkční systémy nemusí vztahovat na více systémů, které nejsou v produkčním prostředí. V důsledku toho může být Kombinovaný objem dat HANA a protokolu. I když s některými culprits, jako je například, že nesplňuje určitou propustnost nebo klíčové ukazatele výkonu, které jsou požadovány pro produkční systémy. Dalším aspektem snížení nákladů v takových prostředích může být využití [úložiště Azure SSD úrovně Standard](./planning-guide-storage.md#azure-standard-ssd-storage). Mějte na paměti, že volba SSD úrovně Standard nebo HDD úrovně Standard úložiště Azure má dopad na SLA na jeden virtuální počítač, jak je popsáno v článku věnovaném  [smlouvě SLA pro Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines).

Méně nákladná alternativa takových konfigurací by mohla vypadat takto:


| Skladová položka virtuálního počítače | Paměť RAM | Max. VSTUPNĚ-VÝSTUPNÍ OPERACE VIRTUÁLNÍHO POČÍTAČE<br /> Propustnost | /Hana/data a/Hana/log<br /> prokládaný pomocí LVM nebo MDADM | /hana/shared | Rozsah/root | /usr/sap | vyjádření |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 4 x P6 | 1 × E10 | 1 × E6 | 1 × E6 | Nedosáhne méně než 1 MS latence úložiště<sup>1</sup> . |
| E16v3 | 128 GiB | 384 MB/s | 4 x P6 | 1 × E10 | 1 × E6 | 1 × E6 | Typ virtuálního počítače není certifikovaný pro HANA <br /> Nedosáhne méně než 1 MS latence úložiště<sup>1</sup> . |
| M32ts | 192 GiB | 500 MB/s | 3 x P10 | 1 × E15 | 1 × E6 | 1 × E6 | Při použití Akcelerátor zápisu pro kombinované objemy dat a svazků protokolu se omezí frekvence IOPS na 5 000<sup>2</sup> . |
| E20ds_v4 | 160 GiB | 480 MB/s | 4 x P6 | 1 × E15 | 1 × E6 | 1 × E6 | Nedosáhne méně než 1 MS latence úložiště<sup>1</sup> . |
| E32v3 | 256 GB | 768 MB/s | 4 x P10 | 1 × E15 | 1 × E6 | 1 × E6 | Typ virtuálního počítače není certifikovaný pro HANA <br /> Nedosáhne méně než 1 MS latence úložiště<sup>1</sup> . |
| E32ds_v4 | 256 GB | 768 MB/s | 4 x P10 | 1 × E15 | 1 × E6 | 1 × E6 | Nedosáhne méně než 1 MS latence úložiště<sup>1</sup> . |
| M32ls | 256 GB | 500 MB/s | 4 x P10 | 1 × E15 | 1 × E6 | 1 × E6 | Při použití Akcelerátor zápisu pro kombinované objemy dat a svazků protokolu se omezí frekvence IOPS na 5 000<sup>2</sup> . |
| E48ds_v4 | 384 GiB | 1 152 MB/s | 6 × P10 | 1 × E20 | 1 × E6 | 1 × E6 | Nedosáhne méně než 1 MS latence úložiště<sup>1</sup> . |
| E64v3 | 432 GiB | 1 200 MB/s | 6 × P10 | 1 × E20 | 1 × E6 | 1 × E6 | Nedosáhne méně než 1 MS latence úložiště<sup>1</sup> . |
| E64ds_v4 | 504 GiB | 1200 MB/s |  7 x P10 | 1 × E20 | 1 × E6 | 1 × E6 | Nedosáhne méně než 1 MS latence úložiště<sup>1</sup> . |
| M64ls | 512 GiB | 1 000 MB/s | 7 x P10 | 1 × E20 | 1 × E6 | 1 × E6 | Při použití Akcelerátor zápisu pro kombinované objemy dat a svazků protokolu se omezí frekvence IOPS na 10 000<sup>2</sup> . |
| M64s | 1 000 GiB | 1 000 MB/s | 7 x P15 | 1 × E30 | 1 × E6 | 1 × E6 | Při použití Akcelerátor zápisu pro kombinované objemy dat a svazků protokolu se omezí frekvence IOPS na 10 000<sup>2</sup> . |
| M64ms | 1 750 GiB | 1 000 MB/s | 6 × P20 | 1 × E30 | 1 × E6 | 1 × E6 | Při použití Akcelerátor zápisu pro kombinované objemy dat a svazků protokolu se omezí frekvence IOPS na 10 000<sup>2</sup> . |
| M128s | 2 000 GiB | 2 000 MB/s |6 × P20 | 1 × E30 | 1 × E10 | 1 × E6 | Při použití Akcelerátor zápisu pro kombinované objemy dat a svazků protokolu se omezí frekvence IOPS na 20 000<sup>2</sup> . |
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 x P30 | 1 × E30 | 1 × E10 | 1 × E6 | Při použití Akcelerátor zápisu pro kombinované objemy dat a svazků protokolu se omezí frekvence IOPS na 10 000<sup>2</sup> . |
| M128ms | 3 800 GiB | 2 000 MB/s | 5 × P30 | 1 × E30 | 1 × E10 | 1 × E6 | Při použití Akcelerátor zápisu pro kombinované objemy dat a svazků protokolu se omezí frekvence IOPS na 20 000<sup>2</sup> . |
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 4 x P40 | 1 × E30 | 1 × E10 | 1 × E6 | Při použití Akcelerátor zápisu pro kombinované objemy dat a svazků protokolu se omezí frekvence IOPS na 10 000<sup>2</sup> . |
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 4 x P40 | 1 × E30 | 1 × E10 | 1 × E6 | Při použití Akcelerátor zápisu pro kombinované objemy dat a svazků protokolu se omezí frekvence IOPS na 20 000<sup>2</sup> . |
| M416ms_v2 | 11400 GiB | 2 000 MB/s | 7 x P40 | 1 × E30 | 1 × E10 | 1 × E6 | Při použití Akcelerátor zápisu pro kombinované objemy dat a svazků protokolu se omezí frekvence IOPS na 20 000<sup>2</sup> . |


<sup>1</sup> službu [Azure akcelerátor zápisu](../../how-to-enable-write-accelerator.md) nelze použít se rodinou virtuálních počítačů Ev4 a Ev4. V důsledku použití služby Azure Premium Storage nebude latence vstupu/výstupu menší než 1 ms.

<sup>2</sup> rodina virtuálních počítačů podporuje [Azure akcelerátor zápisu](../../how-to-enable-write-accelerator.md), ale je možné, že limit IOPS pro zápis akcelerátoru může omezit možnosti IOPS konfigurace disku.

V případě kombinace dat a svazku protokolu pro SAP HANA by disky vytvářející prokládaný svazek neměly mít povolenou mezipaměť pro čtení nebo mezipaměť pro čtení/zápis.

V seznamu jsou uvedené typy virtuálních počítačů, které nejsou certifikované pro SAP, a to tak, aby se nezobrazovaly jako [SAP HANAý hardwarový adresář](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Připomínky zákazníků bylo, že tyto neuvedené typy virtuálních počítačů byly úspěšně použity pro některé nevýrobní úkoly.


## <a name="next-steps"></a>Další kroky
Další informace naleznete v tématu:

- [SAP HANA Průvodce vysokou dostupností pro virtuální počítače Azure](./sap-hana-availability-overview.md).
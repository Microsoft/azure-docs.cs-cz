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
ms.date: 09/03/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 60947a8138972834f30274715226648d1b2360a1
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89440690"
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
> Nezávisle na zvoleném typu úložiště Azure je nutné, aby systém souborů používaný v tomto úložišti podporoval SAP pro konkrétní operační systém a systém DBMS. [Poznámka k podpoře SAP #405827](https://launchpad.support.sap.com/#/notes/405827) uvádí podporované systémy souborů pro různé operační systémy a databáze, včetně SAP HANA. To platí pro všechny svazky SAP HANA můžou mít přístup ke čtení a zápisu pro libovolný úkol. Při použití systému souborů NFS v Azure pro SAP HANA platí další omezení verze systému souborů NFS, jak je uvedeno dále v tomto článku. 


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
- Pokud používáte službu Azure Premium Storage, jedná se o nejlevnějšíou konfiguraci pomocí správců logických svazků k sestavení sad Stripe Sets pro vytváření svazků **/Hana/data** a **/Hana/log** .

> [!IMPORTANT]
> Návrhy pro konfigurace úložiště jsou určené jako pokyny, jak začít s. Spuštění úloh a analýza vzorů využití úložiště vám může znamenat, že nepoužíváte celou šířku pásma úložiště nebo IOPS. Můžete zvážit možnost úložiště a pak. V opačném případě vaše zatížení může potřebovat větší propustnost úložiště než navržené s těmito konfiguracemi. V důsledku toho může být nutné nasadit větší kapacitu, IOPS nebo propustnost. V terénu napětí mezi požadovanou kapacitou úložiště, požadovanou latencí úložiště, propustností úložiště a minimální náročnou konfigurací nabízí Azure dostatek různých typů úložiště s různými možnostmi a různé cenové body, které vám umožní najít a upravit správné ohrožení pro vás a vaše úlohy HANA.

## <a name="linux-io-scheduler-mode"></a>Režim plánovače I/O systému Linux
Linux má několik různých režimů plánování vstupu a výstupu. Běžnými doporučeními pro dodavatele a SAP systému Linux je překonfigurování režimu v/v plánovače u diskových svazků z režimu **MQ-konečný** nebo **kyber** do **NoOp** (non-Queue) nebo **none** for (pro více front). Podrobnosti jsou odkazovány v [#1984787 SAP Note](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Řešení s využitím služby Premium Storage a Azure Akcelerátor zápisu pro virtuální počítače řady Azure M-Series
Azure Akcelerátor zápisu je funkce, která je k dispozici pro virtuální počítače Azure M-Series výhradně. Jako název uvádíme účel funkce k vylepšení latence v/v zápisu do služby Azure Premium Storage. V případě SAP HANA se má Akcelerátor zápisu použít jenom pro svazek **/Hana/log** . Proto jsou **/Hana/data** a **/Hana/log** samostatné svazky s Azure akcelerátor zápisu podporují pouze svazek **/Hana/log** . 

> [!IMPORTANT]
> Při použití služby Azure Premium Storage je použití Azure [akcelerátor zápisu](../../how-to-enable-write-accelerator.md) pro svazek **/Hana/log** povinné. Akcelerátor zápisu je k dispozici pouze pro virtuální počítače s podporou Premium Storage a řady M-Series a Mv2-Series. Akcelerátor zápisu nepracuje v kombinaci s jinými rodinami virtuálních počítačů Azure, jako je Esv3 nebo Edsv4.

Doporučení pro ukládání do mezipaměti pro disky Azure Premium níže jsou popsány v parametrech v/v pro SAP HANA, jako je:

- K datovým souborům HANA se špatně všechny úlohy čtení. Po restartování instance HANA nebo při načtení dat do HANA jsou výjimky velké velikosti I/OS. Dalším případem většího počtu vstupně-výstupních vstupně-výstupních souborů pro čtení dat může být zálohování databáze HANA. V důsledku toho by ukládání do mezipaměti pro čtení většinou nedávalo smysl vzhledem k tomu, že ve většině případů je třeba všechny svazky datových souborů přečíst Kompletně. 
- Zápis proti datovým souborům se nachází v shlukech na základě zotavení po havárii HANA úložných bodů a HANA. Zápis úložných bodů je asynchronní a neobsahuje žádné uživatelské transakce. Zápis dat během zotavení po havárii je kritický pro výkon, aby systém mohl rychle reagovat. Zotavení po havárii by ale mělo být spíše výjimečné situace
- Ze souborů pro opětovné provedení služby HANA se špatně žádné čtení. Výjimky jsou velké vstupně-výstupy při provádění záloh protokolů transakcí, zotavení po havárii nebo ve fázi restartování instance HANA.  
- Hlavní zatížení proti souboru protokolu SAP HANA znovu zapisuje. V závislosti na povaze úlohy můžete mít vstupně-výstupní operace až na 4 KB nebo jiné velikosti vstupně-výstupních operací s 1 MBm nebo více. Latence zápisu proti SAP HANA protokolu opětovného provedení je kritická.
- Všechny zápisy musí být trvale uchovány na disku spolehlivě.

**Doporučení: Výsledkem těchto pozorovaných vstupně-výstupních vzorů SAP HANA je ukládání do mezipaměti pro různé svazky pomocí služby Azure Premium Storage, jako třeba:**

- **/Hana/data** – žádné ukládání do mezipaměti nebo čtení do mezipaměti
- **/Hana/log** – žádné ukládání do mezipaměti – výjimka pro virtuální počítače řady M a Mv2, kde by mělo být povoleno Azure akcelerátor zápisu 
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
Pro disky Azure Premium Storage menší nebo rovny 512 GiB v kapacitě se nabízí funkce shlukování. Přesný způsob, jak funguje shlukování disku, je popsaný v článku o rozložení [disku na disk](../../linux/disk-bursting.md). Po přečtení článku rozumíte konceptu časově rozlišených vstupně-výstupních operací a propustnosti v časech, kdy je zatížení v/v pod nominálními IOPS a propustností disků (podrobnosti o nominální propustnosti najdete v tématu [ceny spravovaného disku](https://azure.microsoft.com/pricing/details/managed-disks/)). Chystáte se rozlišit rozdíl mezi vstupně-výstupními operacemi a propustností mezi aktuálním využitím a jmenovitými hodnotami disku. Počet shluků je omezen na maximálně 30 minut.

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
> Ve scénářích, které zahrnují Azure Premium Storage, implementujeme do konfigurace možnosti shlukování. Pokud používáte testovací nástroje pro úložiště bez ohledu na tvar nebo formu, mějte na paměti, jak [funguje Azure Premium disking](../../linux/disk-bursting.md) . Při spuštění testů úložiště dodaných prostřednictvím nástroje SAP HWCCT nebo HCMT neočekáváme, že všechny testy budou kritéria předávat, protože některé testy překročí kredity, které můžete nashromáždit. Hlavně v případě, že všechny testy běží sekvenčně bez přerušení.


> [!NOTE]
> V případě produkčních scénářů ověřte, jestli určitý typ virtuálního počítače podporuje SAP HANA SAP v [dokumentaci SAP pro IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Doporučení: doporučené konfigurace s Azure Premium Storage pro produkční scénáře vypadají jako:**

Konfigurace pro svazek SAP **/Hana/data** :

| Skladová položka virtuálního počítače | Paměť RAM | Max. VSTUPNĚ-VÝSTUPNÍ OPERACE VIRTUÁLNÍHO POČÍTAČE<br /> Propustnost | /hana/data | Maximální propustnost shluku | IOPS | Shlukový IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MB/s | 4 x P6 | 680 MB/s | 960 | 14 000 |
| M32ls | 256 GB | 500 MB/s | 4 x P6 | 680 MB/s | 960 | 14 000 |
| M64ls | 512 GiB | 1 000 MB/s | 4 x P10 |  680 MB/s | 2 000 | 14 000 |
| M64s | 1 000 GiB | 1 000 MB/s | 4 x P15 | 680 MB/s | 4 400 | 14 000 |
| M64ms | 1 750 GiB | 1 000 MB/s | 4 x P20 | 680 MB/s | 9 200 | 14 000 |  
| M128s | 2 000 GiB | 2 000 MB/s | 4 x P20 | 680 MB/s | 9 200| 14 000 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 4 x P30 | 800 MB/s (zřízené) | 20 000 | bez shluku | 
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 x P30 | 800 MB/s (zřízené) | 20 000| bez shluku | 
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 4 x P40 | 1 000 MB/s (zřízené) | 25 000 | bez shluku |
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 4 x P40 | 1 000 MB/s (zřízené) | 25 000 | bez shluku |
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 4 x P50 | 2 000 MB/s (zřízené) | 25 000 | bez shluku |


Pro svazek **/Hana/log** . konfigurace by vypadala takto:

| Skladová položka virtuálního počítače | Paměť RAM | Max. VSTUPNĚ-VÝSTUPNÍ OPERACE VIRTUÁLNÍHO POČÍTAČE<br /> Propustnost | **/Hana/log** svazek | Maximální propustnost shluku | IOPS | Shlukový IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MB/s | 3 x P10 | 510 MB/s | 1 500 | 10 500 | 
| M32ls | 256 GB | 500 MB/s | 3 x P10 | 510 MB/s | 1 500 | 10 500 | 
| M64ls | 512 GiB | 1 000 MB/s | 3 x P10 | 510 MB/s | 1 500 | 10 500 | 
| M64s | 1 000 GiB | 1 000 MB/s | 3 x P15 | 510 MB/s | 3 300 | 10 500 | 
| M64ms | 1 750 GiB | 1 000 MB/s | 3 x P15 | 510 MB/s | 3 300 | 10 500 |  
| M128s | 2 000 GiB | 2 000 MB/s | 3 x P15 | 510 MB/s | 3 300 | 10 500|  
| M128ms | 3 800 GiB | 2 000 MB/s | 3 x P15 | 510 MB/s | 3 300 | 10 500 | 
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 3 x P15 | 510 MB/s | 3 300 | 10 500 |  
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 3 x P15 | 510 MB/s | 3 300 | 10 500 |  
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 3 x P15 | 510 MB/s | 3 300 | 10 500 |  
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 3 x P15 | 510 MB/s | 3 300 | 10 500 | 


U ostatních svazků by konfigurace vypadala takto:

| Skladová položka virtuálního počítače | Paměť RAM | Max. VSTUPNĚ-VÝSTUPNÍ OPERACE VIRTUÁLNÍHO POČÍTAČE<br /> Propustnost | /hana/shared | Rozsah/root | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 1 × P20 | 1 × P6 | 1 × P6 |
| M32ls | 256 GB | 500 MB/s |  1 × P20 | 1 × P6 | 1 × P6 |
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

| Skladová položka virtuálního počítače | Paměť RAM | Max. VSTUPNĚ-VÝSTUPNÍ OPERACE VIRTUÁLNÍHO POČÍTAČE<br /> Propustnost | /hana/data | Maximální propustnost shluku | IOPS | Shlukový IOPS |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | 160 GiB | 480 MB/s | 3 x P10 | 510 MB/s | 1 500 | 10 500 |
| E32ds_v4 | 256 GB | 768 MB/s | 3 x P10 |  510 MB/s | 1 500 | 10 500|
| E48ds_v4 | 384 GiB | 1 152 MB/s | 3 x P15 |  510 MB/s | 3 300  | 10 500 | 
| E64ds_v4 | 504 GiB | 1 200 MB/s | 3 x P15 |  510 MB/s | 3 300 | 10 500 | 
| E64s_v3 | 432 GiB | 1 200 MB/s | 3 x P15 |  510 MB/s | 3 300 | 10 500 | 

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
> Ultra disk ještě není ve všech oblastech Azure a ještě nepodporují všechny typy virtuálních počítačů uvedené níže. Podrobné informace o dostupnosti Ultra disk a o podporovaných rodinách virtuálních počítačů najdete v článku o [tom, jaké typy disků jsou dostupné v Azure?](../../windows/disks-types.md#ultra-disk).

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
Azure NetApp Files poskytuje nativní sdílené složky systému souborů NFS, které se dají použít pro svazky **/Hana/Shared**, **/Hana/data**a **/Hana/log** . Použití sdílených složek systému souborů NFS založených na ANF pro svazky **/Hana/data** a **/Hana/log** vyžaduje použití protokolu NFS v 4.1. Protokol NFS verze 3 není podporován pro použití **/Hana/data** a **/Hana/log** svazků při založení sdílených složek na ANF. 

> [!IMPORTANT]
> Protokol NFS v3 implementovaný v Azure NetApp Files **není podporován pro** použití pro **/Hana/data** a **/Hana/log**. Použití NFS 4,1 je povinné pro svazky **/Hana/data** a **/Hana/log** z funkčního bodu zobrazení. Vzhledem k tomu, že je možné použít pro svazek **/Hana/Shared** systém souborů NFS v3 nebo NFS verze 4.1, ze funkčního hlediska.

### <a name="important-considerations"></a>Důležité informace
Při zvažování Azure NetApp Files SAP NetWeaver a SAP HANA si pamatujte na následující důležité informace:

- Minimální fond kapacit je 4 TiB.  
- Minimální velikost svazku je 100 GiB
- Azure NetApp Files a všech virtuálních počítačů, kde se Azure NetApp Files svazky připojí, musí být ve stejné oblasti jako Azure Virtual Network nebo ve [virtuálních sítích s partnerským vztahem](../../../virtual-network/virtual-network-peering-overview.md) .  
- Vybraná virtuální síť musí mít podsíť, delegovanou na Azure NetApp Files.
- Propustnost svazku Azure NetApp je funkcí kvóty svazku a úrovně služeb, jak je uvedeno v části [úroveň služby pro Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Při změně velikosti svazků NetApp HANA Azure se ujistěte, že výsledná propustnost splňuje požadavky na systém HANA.  
- Azure NetApp Files nabízí [zásady exportu](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): můžete řídit povolené klienty, typ přístupu (čtení&zápisu, jen pro čtení atd.). 
- Azure NetApp Files funkce zatím nereaguje na zóny. Aktuálně Azure NetApp Files funkce není nasazená ve všech zónách dostupnosti v oblasti Azure. Mějte na paměti, že v některých oblastech Azure máte vliv na potenciální latenci.  
- Je důležité mít virtuální počítače nasazené v těsné blízkosti úložiště Azure NetApp pro nízkou latenci. 
- ID uživatele pro ADM s <b>identifikátorem SID</b>a ID skupiny pro `sapsys` virtuální počítače musí odpovídat konfiguraci v Azure NetApp Files. 

> [!IMPORTANT]
> U SAP HANA úloh je nízká latence kritická. Spolupracujte se zástupcem Microsoftu a zajistěte, aby se virtuální počítače a Azure NetApp Files svazky nasadily v těsné blízkosti.  

> [!IMPORTANT]
> Pokud dojde k neshodě mezi ID uživatele pro ADM se <b>zabezpečením SID</b>a ID skupiny `sapsys` mezi virtuálním počítačem a konfigurací Azure NetApp, zobrazí se oprávnění soubory na svazcích Azure NetApp připojených k virtuálním počítačům `nobody` . Nezapomeňte zadat správné ID uživatele pro ADM s <b>identifikátorem SID</b>a ID skupiny pro `sapsys` , když se při [připojování nového systému do Azure NetApp Files nový systém](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) .

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Určení velikosti databáze HANA v Azure NetApp Files

Propustnost svazku Azure NetApp je funkce velikosti svazku a úrovně služby, jak je uvedeno v části [úroveň služby pro Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). 

Při návrhu infrastruktury pro SAP v Azure byste měli znát minimální požadavky na propustnost úložiště v SAP, která se přeloží na minimální propustnost:

- Povolit čtení a zápis v **/Hana/logu** s 250 MB/s a velikosti 1 MB vstupu/výstupu  
- Povolit aktivitu čtení minimálně 400 MB/s pro **/Hana/data** pro velikost I/O 16 mb a 64 MB  
- Povolit aktivitu zápisu alespoň 250 MB/s pro **/Hana/data** s 16 mb a 64 MB I/O velikosti  

[Omezení propustnosti Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md) na 1 TIB kvót:
- Úroveň úložiště úrovně Premium – 64 MiB/s  
- Úroveň úložiště úrovně Ultra – 128 MiB/s  

> [!IMPORTANT]
> Nezávisle na kapacitě, kterou nasazujete na jednom svazku NFS, se očekává, že propustnost stabilní úrovně v rozsahu od 1.2 do 1,4 GB/s (šířka pásma), kterou využívá spotřebitel ve virtuálním počítači. To se musí udělat se základní architekturou nabídky ANF a souvisejícími omezeními pro Linux Sessions pro systém souborů NFS. Údaje o výkonu a propustnosti popsané v článku [výsledky testování testů výkonnosti pro Azure NetApp Files](../../../azure-netapp-files/performance-benchmarks-linux.md) byly provedeny na jednom sdíleném svazku systému souborů NFS s několika klientskými virtuálními počítači a v důsledku více relací. Tento scénář se liší od scénáře, který je v SAP k disměrnému scénáři. Místo měření propustnosti z jednoho virtuálního počítače na svazek systému souborů NFS. hostováno v ANF.

Aby splňovala požadavky na minimální propustnost SAP pro data a protokol a podle pokynů pro `/hana/shared` , Doporučené velikosti by vypadaly takto:

| Svazek | Velikost<br /> Premium Storage úroveň | Velikost<br /> Úroveň úložiště Ultra Storage | Podporovaný protokol NFS |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /hana/shared | Max (512 GB, 1xRAM) na 4 pracovní uzly | Max (512 GB, 1xRAM) na 4 pracovní uzly | V3 nebo v 4.1 |


> [!NOTE]
> Tady uvedená doporučení pro změny velikosti Azure NetApp Files cílí na to, aby splňovala minimální požadavky SAP na jejich poskytovatele infrastruktury. V reálných scénářích nasazení zákazníků a úloh, které nemusí být dostatečné. Tato doporučení použijte jako výchozí bod a přizpůsobte je na základě požadavků konkrétního zatížení.  

Proto byste měli zvážit, jak nasadit podobnou propustnost pro ANF svazky, jak je uvedeno pro úložiště Ultra disk již. Vezměte v úvahu také velikosti pro velikosti uvedené pro svazky pro různé skladové položky virtuálních počítačů, které jsou v tabulkách s Ultra diskem již provedeny.

> [!TIP]
> Můžete znovu nastavit velikost svazků Azure NetApp Files dynamicky bez nutnosti `unmount` svazků, zastavit virtuální počítače nebo zastavit SAP HANA. To umožňuje flexibilitu v závislosti na očekávané i nepředvídatelné propustnosti požadavků.

Dokumentace k nasazení SAP HANA konfigurace škálování na více instancí s pohotovostním uzlem pomocí systému souborů NFS v 4.1, který je hostovaný v ANF, se zveřejňuje v SAP HANA škálování na více instancích [s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md).


## <a name="cost-conscious-solution-with-azure-premium-storage"></a>Řešení na vědomí nákladů pomocí služby Azure Premium Storage
Zatím řešení Azure Premium Storage popsané v tomto dokumentu najdete v části [řešení s využitím služby Premium Storage a azure akcelerátor zápisu pro virtuální počítače řady Azure M-Series](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines) , která se týkají SAP HANA produkčních scénářů. Jedna z vlastností produkčních konfigurací, které podporují, je oddělení svazků pro SAP HANAá data a znovu se přihlaste do dvou různých svazků. Důvodem takového oddělení je to, že charakteristiky zatížení na svazcích se liší. A že u navrhovaných produkčních konfigurací může být potřeba jiný typ ukládání do mezipaměti nebo dokonce jiné typy úložiště bloků Azure. Produkční podporované konfigurace pomocí Azure blokového úložiště musí být v souladu s smlouvou [SLA s jedním virtuálním počítačem pro Azure Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/) také.  V případě neprodukčních scénářů se některé z doporučení pro produkční systémy nemusí vztahovat na více systémů, které nejsou v produkčním prostředí. V důsledku toho může být Kombinovaný objem dat HANA a protokolu. I když s některými culprits, jako je například, že nesplňuje určitou propustnost nebo klíčové ukazatele výkonu, které jsou požadovány pro produkční systémy. Dalším aspektem snížení nákladů v takových prostředích může být využití [úložiště Azure SSD úrovně Standard](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide-storage#azure-standard-ssd-storage). I když volba neověřuje [smlouvu SLA pro jeden virtuální počítač pro Azure Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/). 

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

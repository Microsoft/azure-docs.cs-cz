---
title: Konfigurace úložiště virtuálních počítačů SAP HANA Azure | Dokumenty společnosti Microsoft
description: Doporučení úložiště pro virtuální hod, který má SAP HANA nasazené v nich.
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
ms.openlocfilehash: 4b469c098db4f8d90147b491bcb54bd55d326b03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080304"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Konfigurace úložiště virtuálních počítačů Azure SAP HANA

Azure poskytuje různé typy úložiště, které jsou vhodné pro virtuální počítače Azure, které používají SAP HANA. Sap **HANA certifikované typy úložišť Azure,** které lze považovat pro seznam nasazení SAP HANA, jako jsou: 

- Azure Premium SSD  
- [Disky Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Další informace o těchto typech disků naleznete v článku [Výběr typu disku](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Azure nabízí dvě metody nasazení pro virtuální počítače ve standardu Azure a úložiště Premium. Pokud to celkový scénář umožňuje, využijte výhod nasazení [spravovaných disků Azure.](https://azure.microsoft.com/services/managed-disks/) 

Seznam typů úložišť a jejich sla ve službách VOPS a propustnost úložiště najděte [dokumentaci k Azure pro spravované disky](https://azure.microsoft.com/pricing/details/managed-disks/).

> [!IMPORTANT]
> Nezávisle na zvoleném typu úložiště Azure musí být systém souborů, který se používá v tomto úložišti, podporován SAP pro konkrétní operační systém a DBMS. [Poznámka podpory SAP #405827](https://launchpad.support.sap.com/#/notes/405827) uvádí podporované systémy souborů pro různé operační systémy a databáze, včetně SAP HANA. To platí pro všechny svazky SAP HANA může přistupovat pro čtení a zápis pro jakýkoli úkol. Konkrétně pomocí nfs v Azure pro SAP HANA, další omezení verzí systému nfs platí, jak je uvedeno dále v tomto článku 


Minimální podmínky certifikované SAP HANA pro různé typy úložišť jsou: 

- Azure Premium SSD – /hana/log je nutné uložit do mezipaměti s [Azure Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Svazek /hana/data lze umístit na premium SSD bez akcelerátoru zápisu Azure nebo na disk Ultra
- Disk Azure Ultra alespoň pro svazek /hana/log. Svazek /hana/data lze umístit buď na premium SSD bez akcelerátoru zápisu Azure, nebo za účelem rychlejšího restartování ultra disku
- **NFS v4.1** svazky nad Soubory Azure NetApp pro /hana/log **a** /hana/data. Svazek /hana/shared může používat protokol NFS v3 nebo NFS v4.1. Protokol NFS v4.1 je povinný pro svazky /hana/data a/hana/log.

Některé typy úložišť lze kombinovat. Například je možné umístit /hana/data na Premium Storage a /hana/log lze umístit na úložiště ultra disků, aby se získala požadovaná nízká latence. Pokud však používáte svazek nfs v4.1 založený na ANF pro /hana/data, je nutné použít jiný svazek NFS v4.1 založený na ANF pro /hana/log. Použití systému zabezpečení systému sdílení nf nad anf pro jeden z svazků (jako /hana/data) a Azure Premium Storage nebo Ultra disk pro druhý svazek (jako /hana/log) **není podporována**.

V místním světě jste se málokdy museli starat o vstupně-do subsystémy a jeho schopnosti. Důvodem bylo, že dodavatel zařízení potřebné k ujistěte se, že jsou splněny minimální požadavky na úložiště pro SAP HANA. Při vytváření infrastruktury Azure sami, měli byste si být vědomi některé z těchto sap vydané požadavky. Některé z minimální propustnost charakteristiky, které jsou požadovány od SAP, jsou výsledkem nutnosti:

- Povolení čtení a zápisu na **/hana/log** 250 MB/s s velikostmi 1 MB vstupně-va
- Povolení čtení alespoň 400 MB/s pro **/hana/data** pro velikosti 16 MB a 64 MB vstupně-va
- Povolení aktivity zápisu alespoň 250 MB/s pro **/hana/data** s velikostmi 16 MB a 64 MB vstupně-v/o.

Vzhledem k tomu, že nízká latence úložiště je pro systémy DBMS důležitá, i když DBMS, jako je SAP HANA, uchovává data v paměti. Kritická cesta v úložišti je obvykle kolem zápisů transakčníprotokol systémů DBMS. Ale také operace, jako je zápis upočovacích bodů nebo načítání dat v paměti po zotavení po chybě může být důležité. Proto je **povinné** využít disky Azure Premium pro **svazky /hana/data** a **/hana/log.** Chcete-li dosáhnout minimální propustnost **/hana/log** a **/hana/data** podle potřeby sap, je třeba vytvořit RAID 0 pomocí MDADM nebo LVM přes více disků Azure Premium Storage. A použijte svazky RAID jako **svazky /hana/data** a **/hana/log.** 

**Doporučení: Jako velikosti pruhů pro RAID 0 doporučujeme použít:**

- 256 KB pro **/hana/data**
- 32 KB pro **/hana/log**

> [!IMPORTANT]
> Velikost proužek pro /hana/data se změnila z dřívějších doporučení volajících po 64 KB nebo 128 KB na 256 KB na základě zkušeností zákazníků s novějšími verzemi Linuxu. Velikost 256 KB poskytuje o něco lepší výkon

> [!NOTE]
> Není nutné konfigurovat žádnou úroveň redundance pomocí svazků RAID, protože azure premium a standardní úložiště uchovávejte tři bitové kopie virtuálního disku.You don't need to configure any redundancy level using RAID volumes since Azure Premium and Standard storage keep three images of a VHD. Použití svazku RAID je čistě ke konfiguraci svazků, které poskytují dostatečnou vstupně-va.

Akumulační počet Azure VD pod RAID, je kumulativní z viporu a úložiště propustnost straně. Pokud tedy vložíte disky RAID 0 nad 3 x P30 Azure Premium Storage, mělo by vám to poskytnout trojnásobek viops a trojnásobek propustnosti úložiště jednoho disku Azure Premium Storage P30.

Při dimenzování velikosti nebo rozhodování o virtuálním virtuálním ms také mějte na paměti celkovou propustnost vstupně-v.v.V., pokud jde o velikost virtuálního soudu. Celková propustnost úložiště virtuálních počítače je dokumentována v článku [Velikosti virtuálních strojů optimalizované pro paměť](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Režim Plánovač i/O Linuxu
Linux má několik různých režimů plánování vstupně-in a o. Běžné doporučení prostřednictvím dodavatelů Linuxu a SAP je překonfigurovat vstupně-vajatický režim pro diskové svazky z **cfq** režimu do **noop** (non-multiqueue) nebo **žádný** pro (multiqueue) režim. Podrobnosti jsou uvedeny v [SAP Note #1984787](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Řešení s úložištěm Premium a akcelerátorem zápisu Azure pro virtuální počítače Azure řady M
Azure Write Accelerator je funkce, která je dostupná výhradně pro virtuální počítače Azure řady M.Azure Write Accelerator je funkce, která je dostupná výhradně pro virtuální počítače Azure řady M.Azure Write Accelerator is a functionality that is available for Azure M-Series VMs exclusively. Jak název uvádí, účelem funkce je zlepšit latenci vstupně-výstupních zápisů proti úložišti Azure Premium. Pro SAP HANA akcelerátor zápisu má být použit pouze proti svazku **/hana/log.** Proto **/hana/data** a **/hana/log** jsou samostatné svazky s akcelerátorem zápisu Azure, který podporuje pouze svazek **/hana/log.** 

> [!IMPORTANT]
> Při použití Azure Premium Storage je povinné využití [Akcelerátoru zápisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) Azure nebo svazku /hana/log. Akcelerátor zápisu je k dispozici pouze pro prémiové úložiště a virtuální zařízení řady M a Mv2.

Níže uvedená doporučení pro ukládání do mezipaměti předpokládají charakteristiky vstupně-va pro SAP HANA, které uvádějí, jako je:

- Téměř neexistuje žádné čtení zatížení proti datové soubory HANA. Výjimky jsou velké velikosti vstupně-va po restartování instance HANA nebo při načítání dat do HANA. Dalším případem větší čtení vstupně-v a. proti datovým souborům může být zálohy databáze HANA. V důsledku toho čtení ukládání do mezipaměti většinou nemá smysl, protože ve většině případů je třeba zcela číst všechny svazky datových souborů.
- Zápis proti datovým souborům dochází v shluky založené na hana savepoints a hana zotavení po chybě. Zápis upočovacích bodů je asynchronní a nezdržují žádné uživatelské transakce. Zápis dat během zotavení po chybě je rozhodující pro zvýšení výkonu, aby systém opět rychle reagoval. Zotavení po havárii by však mělo být spíše výjimečnými situacemi
- Neexistují téměř žádné čtení z hana znovu soubory. Výjimky jsou velké vstupně-out y při provádění záloh protokolu transakcí, zotavení po chybě nebo ve fázi restartování instance HANA.  
- Hlavní zatížení proti souboru protokolu sap hana je zápisy. V závislosti na povaze pracovního vytížení můžete mít vstupně-blou tak malé, jak 4 KB nebo v jiných případech velikosti vstupně-v.a. 1 MB nebo více. Latence zápisu proti protokolu opakování SAP HANA je kritická pro výkon.
- Všechny zápisy musí být trvalé na disku spolehlivým způsobem

**Doporučení: V důsledku těchto pozorovaných vzorů vstupně-va podle SAP HANA by mělo být ukládání do mezipaměti pro různé svazky pomocí služby Azure Premium Storage nastaveno takto:**

- **/hana/data** - bez ukládání do mezipaměti
- **/hana/log** - žádné ukládání do mezipaměti - výjimka pro řady M a Mv2, kde by měl být povolen akcelerátor zápisu bez čtení ukládání do mezipaměti. 
- **/hana/shared** - čtení ukládání do mezipaměti

### <a name="production-recommended-storage-solution"></a>Doporučené úložné řešení pro výrobu

> [!IMPORTANT]
> Certifikace SAP HANA pro virtuální počítače Řady Azure M je výhradně s akcelerátorem zápisu Azure pro svazek **/hana/log.** V důsledku toho se očekává, že produkční scénář SAP HANA nasazení na virtuálních počítačích Azure řady M se očekává, že nakonfigurovat s Azure Write Accelerator pro **/hana/log** svazku.  

> [!NOTE]
> Pro produkční scénáře zkontrolujte, zda je určitý typ virtuálního uživatele podporován pro SAP HANA SAP v [dokumentaci SAP pro IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Doporučení často překračují minimální požadavky SAP, jak je uvedeno dříve v tomto článku. Uvedená doporučení jsou kompromisem mezi doporučeními velikosti sap a maximální propustností úložiště, kterou poskytují různé typy virtuálních počítačů.

**Doporučení: Doporučené konfigurace pro produkční scénáře vypadají takto:**

| Skladová položka virtuálního počítače | Paměť RAM | Max. VZpěr vstupně-ti a do<br /> Propustnost | /hana/data | /hana/log | /hana/shared | /kořenový svazek | /usr/míza | hana/zálohování |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Zkontrolujte, zda propustnost úložiště pro různé navrhované svazky splňuje zatížení, které chcete spustit. Pokud zatížení vyžaduje vyšší svazky pro **/hana/data** a **/hana/log**, je potřeba zvýšit počet virtuálních disponiárů úložiště Azure Premium. Změna velikosti svazku s více virtuálními počítači, než je uvedeno, zvyšuje propustnost VOPS a V/V v rámci limitů typu virtuálního počítače Azure.

Azure Write Accelerator funguje jenom ve spojení se [spravovanými disky Azure](https://azure.microsoft.com/services/managed-disks/). Takže alespoň disky úložiště Azure Premium, které tvoří svazek **/hana/log,** je třeba nasadit jako spravované disky.

Existují limity virtuálních dispon Azure Premium Storage na virtuální počítač, které můžou být podporované pomocí Akcelerátoru zápisu Azure. Aktuální limity jsou:

- 16 VD pro virtuální měna M128xx a M416xx
- 8 VD pro virtuální měna M64xx a M208xx
- 4 virtuální diody pro virtuální virtuální měna M32xx

Podrobnější pokyny k povolení akcelerátoru zápisu Azure najdete v článku [Akcelerátor zápisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Podrobnosti a omezení pro Azure Write Accelerator najdete ve stejné dokumentaci.

**Doporučení: Akcelerátor zápisu je třeba použít pro disky tvořící svazek /hana/log**


### <a name="cost-conscious-azure-storage-configuration"></a>Konfigurace azure storage s vědomím nákladů
V následující tabulce je uvedena konfigurace typů virtuálních počítačů, které zákazníci používají také k hostování SAP HANA na virtuálních počítačích Azure. Mohou existovat některé typy virtuálních počítačů, které nemusí splňovat všechna minimální kritéria úložiště pro SAP HANA nebo nejsou oficiálně podporovány SAP HANA sap. Ale zatím se zdálo, že tyto virtuální společnosti fungují dobře pro neprodukční scénáře. **/hana/data** a **/hana/log** jsou kombinovány do jednoho svazku. V důsledku toho se využití akcelerátoru zápisu Azure může stát omezením z hlediska viops.

> [!NOTE]
> U scénářů podporovaných SAP zkontrolujte, zda je určitý typ virtuálního uživatele podporován pro SAP HANA SAP v [dokumentaci SAP pro IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Změna oproti dřívějším doporučením pro řešení s vědomím nákladů je přechod z [disků Azure Standard HDD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) na výkonnější a spolehlivější [disky Azure Standard SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)

Doporučení často překračují minimální požadavky SAP, jak je uvedeno dříve v tomto článku. Uvedená doporučení jsou kompromisem mezi doporučeními velikosti sap a maximální propustností úložiště, kterou poskytují různé typy virtuálních počítačů.

| Skladová položka virtuálního počítače | Paměť RAM | Max. VZpěr vstupně-ti a do<br /> Propustnost | /hana/data a /hana/log<br /> pruhované s LVM nebo MDADM | /hana/shared | /kořenový svazek | /usr/míza | hana/zálohování |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1 200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2 000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1 000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1 000 GiB | 1 000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1 750 GiB | 1 000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2 000 GiB | 2 000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3 800 GiB | 2 000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2 000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


Disky doporučené pro menší typy virtuálních počítačů s 3 x P20 nadměrně zvětšují svazky týkající se doporučení místa podle [bílého papíru úložiště SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Nicméně volba zobrazená v tabulce byla provedena tak, aby poskytovala dostatečnou propustnost disku pro SAP HANA. Pokud potřebujete změny svazku **/hana/backup,** který byl dimenzován pro udržování záloh, které představují dvojnásobek objemu paměti, neváhejte a upravte.   
Zkontrolujte, zda propustnost úložiště pro různé navrhované svazky splňuje zatížení, které chcete spustit. Pokud zatížení vyžaduje vyšší svazky pro **/hana/data** a **/hana/log**, je potřeba zvýšit počet virtuálních disponiárů úložiště Azure Premium. Změna velikosti svazku s více virtuálními počítači, než je uvedeno, zvyšuje propustnost VOPS a V/V v rámci limitů typu virtuálního počítače Azure. 

> [!NOTE]
> Výše uvedené konfigurace nebude mít prospěch z [azure virtuálního počítače jediné sla virtuálního počítače,](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) protože používá směs Azure Premium Storage a Azure Standard Storage. Výběr byl však vybrán za účelem optimalizace nákladů. Budete muset zvolit úložiště Premium pro všechny výše uvedené disky, které jsou uvedeny jako Azure Standard Storage (Sxx), aby konfigurace virtuálního počítače kompatibilní s azure jednotného virtuálního počítače SLA.


> [!NOTE]
> Uvedená doporučení pro konfiguraci disku se zaměřují na minimální požadavky, které SAP vyjadřuje vůči poskytovatelům infrastruktury. V reálných nasazeních zákazníků a scénářích pracovního vytížení došlo k situacím, kdy tato doporučení stále neposkytovala dostatečné možnosti. Mohou to být situace, kdy zákazník vyžadoval rychlejší opětovné načtení dat po restartování hana nebo kde konfigurace záloh vyžadovaly vyšší šířku pásma do úložiště. Další případy zahrnuty **/hana/log** kde 5000 VOPS nebyly dostatečné pro konkrétní pracovní vytížení. Vezměte tedy tato doporučení jako výchozí bod a přizpůsobte se na základě požadavků pracovního vytížení.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Konfigurace úložiště Azure Ultra pro SAP HANA
Microsoft právě zavádí nový typ úložiště Azure s názvem [Disk Azure Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk). Významný rozdíl mezi úložištěazure nabízí tak daleko a Ultra disk je, že funkce disku již nejsou vázány na velikost disku. Jako zákazník můžete definovat tyto funkce pro Ultra disk:

- Velikost disku v rozmezí od 4 GiB do 65 536 GiB
- IOPS rozsah od 100 IOPS do 160K IOPS (maximální závisí na typech virtuálních počítačů také)
- Propustnost úložiště od 300 MB/s do 2 000 MB/s

Ultra disk umožňuje definovat jeden disk, který splňuje vaši velikost, IOPS a rozsah propustnost disku. Místo použití logických správců svazků, jako je LVM nebo MDADM nad Azure Premium Storage, k vytváření svazků, které splňují požadavky na iOPS a propustnost úložiště. Můžete spustit konfigurační kombinaci mezi ultra diskem a úložištěm Premium. V důsledku toho můžete omezit využití ultra disku na kritické svazky /hana/data a /hana/log a pokrýt ostatní svazky pomocí služby Azure Premium Storage.

Další výhody ultra disku může být lepší latence čtení ve srovnání s Premium Storage. Rychlejší latence čtení může mít výhody, pokud chcete snížit časy spuštění HANA a následné zatížení dat do paměti. Výhody úložiště disků Ultra lze také cítit, když HANA píše uložené body. Vzhledem k tomu, že disky úložiště Premium pro /hana/data obvykle nejsou uloženy v mezipaměti akcelerátoru zápisu, latence zápisu do /hana/data na úložišti Premium ve srovnání s diskem Ultra je vyšší. Lze očekávat, že ukládání bodů psaní s Ultra disk je lepší výkon na disku Ultra.

> [!IMPORTANT]
> Ultra disk ještě není k dispozici ve všech oblastech Azure a také ještě nepodporuje všechny typy virtuálních počítačů uvedené níže. Podrobné informace o tom, kde je k dispozici ultra disk a které rodiny virtuálních počítačů jsou podporované, najdete v článku [Jaké typy disků jsou dostupné v Azure?](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk).

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Produkční úložné řešení s čistou konfigurací ultra disku
V této konfiguraci uchováte svazky /hana/data a /hana/log samostatně. Navrhované hodnoty jsou odvozeny z kru, které sap má certifikovat typy virtuálních počítačů pro SAP HANA a konfigurace úložiště, jak je doporučeno v [sap TDI úložiště Whitepaper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

Doporučení často překračují minimální požadavky SAP, jak je uvedeno dříve v tomto článku. Uvedená doporučení jsou kompromisem mezi doporučeními velikosti sap a maximální propustností úložiště, kterou poskytují různé typy virtuálních počítačů.

| Skladová položka virtuálního počítače | Paměť RAM | Max. VZpěr vstupně-ti a do<br /> Propustnost | /hana/datový svazek | /hana/datová vstupně-to-v., vodivá propustnost | /hana/vstupně-toops dat | /hana/svazek protokolu | /hana/log I/O propustnost | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1 200 MB/s | 600 GB | 700 mb/s | 7 500 | 512 GB | 500 mb/s  | 2 000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 mb/s | 7 500 | 256 GB | 250 mb/s  | 2 000 |
| M32ls | 256 GB | 500 MB/s | 300 GB | 400 mb/s | 7 500 | 256 GB | 250 mb/s  | 2 000 |
| M64ls | 512 GiB | 1 000 MB/s | 600 GB | 600 mb/s | 7 500 | 512 GB | 400 mb/s  | 2,500 |
| M64s | 1 000 GiB | 1 000 MB/s |  1 200 GB | 600 mb/s | 7 500 | 512 GB | 400 mb/s  | 2,500 |
| M64ms | 1 750 GiB | 1 000 MB/s | 2 100 GB | 600 mb/s | 7 500 | 512 GB | 400 mb/s  | 2,500 |
| M128s | 2 000 GiB | 2 000 MB/s |2 400 GB | 1 200 mb/s |9 000 | 512 GB | 800 mb/s  | 3 000 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 4 800 GB | 1200 mb/s |9 000 | 512 GB | 800 mb/s  | 3 000 | 
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 3 500 GB | 1 000 mb/s | 9 000 | 512 GB | 400 mb/s  | 2,500 | 
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 7 200 GB | 1 000 mb/s | 9 000 | 512 GB | 400 mb/s  | 2,500 | 
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 7 200 GB | 1 500 mb/s | 9 000 | 512 GB | 800 mb/s  | 3 000 | 
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 14 400 GB | 1 500 mb/s | 9 000 | 512 GB | 800 mb/s  | 3 000 |   

Uvedené hodnoty mají být výchozím bodem a je třeba je vyhodnotit podle skutečných požadavků. Výhodou disku Azure Ultra je, že hodnoty pro viopy a propustnost lze upravit bez nutnosti vypnutí virtuálního počítače nebo zastavení úlohy použité v systému.   

> [!NOTE]
> Dosud nejsou k dispozici snímky úložiště s úložištěm Ultra disk. To blokuje využití snímků virtuálních počítačů pomocí služby Azure Backup Services

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>Řešení úložiště s vědomím nákladů s čistou konfigurací disku Ultra
V této konfiguraci můžete svazky /hana/data a /hana/log na stejném disku. Navrhované hodnoty jsou odvozeny z kru, které sap má certifikovat typy virtuálních počítačů pro SAP HANA a konfigurace úložiště, jak je doporučeno v [sap TDI úložiště Whitepaper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). 

Doporučení často překračují minimální požadavky SAP, jak je uvedeno dříve v tomto článku. Uvedená doporučení jsou kompromisem mezi doporučeními velikosti sap a maximální propustností úložiště, kterou poskytují různé typy virtuálních počítačů.

| Skladová položka virtuálního počítače | Paměť RAM | Max. VZpěr vstupně-ti a do<br /> Propustnost | Svazek pro /hana/data a /log | /hana/data a propustnost vstupně-v.I/O protokolu | /hana/data a protokol Ovatidní cípy |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1 200 MB/s | 1 200 GB | 1 200 mb/s | 9,500 | 
| M32ts | 192 GiB | 500 MB/s | 512 GB | 400 mb/s | 9,500 | 
| M32ls | 256 GB | 500 MB/s | 600 GB | 400 mb/s | 9,500 | 
| M64ls | 512 GiB | 1 000 MB/s | 1 100 GB | 900 mb/s | 10 000 | 
| M64s | 1 000 GiB | 1 000 MB/s |  1 700 GB | 900 mb/s | 10 000 | 
| M64ms | 1 750 GiB | 1 000 MB/s | 2 600 GB | 900 mb/s | 10 000 | 
| M128s | 2 000 GiB | 2 000 MB/s |2 900 GB | 1 800 mb/s |12 000 | 
| M128ms | 3 800 GiB | 2 000 MB/s | 5 300 GB | 1 800 mb/s |12 000 |  
| M208s_v2 | 2 850 GiB | 1 000 MB/s | 4 000 GB | 900 mb/s | 10 000 |  
| M208ms_v2 | 5 700 GiB | 1 000 MB/s | 7 700 GB | 900 mb/s | 10 000 | 
| M416s_v2 | 5 700 GiB | 2 000 MB/s | 7 700 GB | 1 800 Mb/s | 12 000 |  
| M416ms_v2 | 11 400 GiB | 2 000 MB/s | 15 000 GB | 1 800 mb/s | 12 000 |    

Uvedené hodnoty mají být výchozím bodem a je třeba je vyhodnotit podle skutečných požadavků. Výhodou disku Azure Ultra je, že hodnoty pro viopy a propustnost lze upravit bez nutnosti vypnutí virtuálního počítače nebo zastavení úlohy použité v systému.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Svazky nfs v4.1 v souborech Azure NetApp
Soubory Azure NetApp poskytuje nativní sdílené složky systému souborů NFS, které lze použít pro svazky /hana/shared, /hana/data a /hana/log. Použití sdílených složek systému zabezpečení systému zabezpečení systému anf pro svazky /hana/data a /hana/log vyžaduje použití protokolu v4.1 NFS. NFS protokol v3 není podporován pro použití svazků /hana/data a /hana/log při zakládání sdílených složek na ANF. 

> [!IMPORTANT]
> Protokol NFS v3 implementovaný v souborech Azure NetApp **není** podporován pro /hana/data a /hana/log. Použití nfs 4.1 je povinné pro svazky /hana/data a /hana/log z funkčního hlediska. Vzhledem k tomu, že pro /hana/sdílený svazek nFS v3 nebo NFS v4.1 protokol lze použít z funkčního hlediska.

### <a name="important-considerations"></a>Důležité informace
Při zvažování soubory Azure NetApp pro SAP Netweaver a SAP HANA, uvědomte si následující důležité aspekty:

- Minimální kapacita bazénu je 4 TiB.  
- Minimální velikost svazku je 100 GiB
- Soubory Azure NetApp a všechny virtuální počítače, kde se připojí svazky souborů Azure NetApp, musí být ve stejné virtuální síti Azure nebo ve [virtuálních sítích s partnerským vztahem](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ve stejné oblasti.  
- Vybraná virtuální síť musí mít podsíť delegovanou na soubory Azure NetApp.
- Propustnost svazku Azure NetApp je funkce kvóty svazku a úrovně služby, jak je popsáno na [úrovni služby pro soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Při dimenzování svazků HANA Azure NetApp se ujistěte, že výsledná propustnost splňuje požadavky systému HANA.  
- Azure NetApp Files nabízí [zásady exportu](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): můžete řídit povolené klienty, typ přístupu (čtení&zápis, jen pro čtení atd.). 
- Funkce Azure NetApp Files ještě není zónová. V současné době azure netapp soubory funkce není nasazenve všech zónách dostupnosti v oblasti Azure. Uvědomte si potenciální důsledky latence v některých oblastech Azure.  
- Je důležité mít virtuální počítače nasazené v těsné blízkosti úložiště Azure NetApp pro nízkou latenci. 
- ID uživatele pro <b>sid</b>adm a `sapsys` ID skupiny pro virtuální počítače musí odpovídat konfiguraci v Azure NetApp Files. 

> [!IMPORTANT]
> Pro úlohy SAP HANA je důležitá nízká latence. Spolupracujte se zástupcem Microsoftu a ujistěte se, že virtuální počítače a svazky Souborů Azure NetApp jsou nasazeny v těsné blízkosti.  

> [!IMPORTANT]
> Pokud existuje neshoda mezi ID uživatele pro <b>sid</b>adm `sapsys` a ID skupiny mezi virtuálním počítačem a konfigurací Azure NetApp, oprávnění pro soubory `nobody`na svazcích Azure NetApp, připojené na virtuálních počítačích, se zobrazí jako . Ujistěte se, že při [nástupu do nového systému](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) do `sapsys`souborů Azure NetApp zadejte správné ID uživatele pro <b>sid</b>adm a ID skupiny pro aplikaci .

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Změna velikosti databáze HANA v souborech Azure NetApp

Propustnost svazku Azure NetApp je funkce velikosti svazku a úrovně služby, jak je popsáno na [úrovni služby pro soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Při navrhování infrastruktury pro SAP v Azure byste si měli být vědomi některých minimálních požadavků na propustnost úložiště od SAP, které se promítají do vlastností minimální propustnost:

- Povolení čtení a zápisu na /hana/log 250 MB/s s velikostmi 1 MB vstupně-va  
- Povolení čtení alespoň 400 MB/s pro /hana/data pro velikosti 16 MB a 64 MB vstupně-va  
- Povolení aktivity zápisu alespoň 250 MB/s pro /hana/data s velikostmi 16 MB a 64 MB vstupně-v/o.  

[Limity propustnost souborů Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) na 1 TiB kvóty svazku jsou:
- Úroveň úložiště Premium – 64 MiB/s  
- Úroveň úložiště Ultra - 128 MiB/s  

> [!IMPORTANT]
> Nezávisle na kapacitě, kterou nasadíte na jeden svazek nfs, propustnost, se očekává, že platovitá v rozsahu 1.2-1.4 GB/s šířky pásma využívá spotřebitel ve virtuálním počítači. To má co do činění s základní architekturou nabídky ANF a souvisejícími limity relace Linuxu kolem NFS. Čísla výkonu a propustnost, jak je popsáno v článku [Výsledky srovnávacího testu výkonu pro soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-benchmarks) byly provedeny na jednom sdíleném svazku systému souborů NFS s více klientskými virtuálními počítači a v důsledku toho s více relacemi. Tento scénář se liší od scénáře, který měříme v SAP. Kde měříme propustnost z jednoho virtuálního virtuálního aplikace proti svazku nfs. hostována na ANF.

Chcete-li splnit požadavky SAP na minimální propustnost `/hana/shared`pro data a protokol a podle pokynů pro , doporučené velikosti by vypadat takto:

| Svazek | Velikost<br /> Úroveň úložiště Premium | Velikost<br /> Úroveň úložiště Ultra | Podporovaný protokol nfs |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared | Max (512 GB, 1xRAM) na 4 pracovní uzly | Max (512 GB, 1xRAM) na 4 pracovní uzly | v3 nebo v4.1 |


> [!NOTE]
> Azure NetApp soubory velikosti doporučení zde jsou zaměřeny na splnění minimální požadavky SAP vyjadřuje vůči jejich poskytovatelům infrastruktury. V reálných nasazeních zákazníků a scénářích úlohtonemusí stačit. Tato doporučení použijte jako výchozí bod a přizpůsobte se na základě požadavků konkrétního pracovního vytížení.  

Proto můžete zvážit nasazení podobné propustnost pro svazky ANF, jak je již uvedeno pro úložiště ultra disků. Zvažte také velikosti pro velikosti uvedené pro svazky pro různé virtuální počítače SKU, jak je již provedeno v tabulkách ultra disk.

> [!TIP]
> Můžete změnit velikost svazků Azure NetApp Files dynamicky, bez nutnosti `unmount` svazků, zastavení virtuálních počítačů nebo zastavení SAP HANA. To umožňuje flexibilitu pro splnění vašich požadavků na propustnost i nepředvídané požadavky na propustnost.

Dokumentace o tom, jak nasadit konfiguraci sap hana horizontálnínavýšení kapacity s pohotovostním uzlem pomocí svazků NFS v4.1, které jsou hostované v ANF, je publikována v [horizontálním navýšení kapacity SAP HANA s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse).


## <a name="next-steps"></a>Další kroky
Další informace naleznete v tématu:

- [SAP HANA Průvodce vysokou dostupností pro virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

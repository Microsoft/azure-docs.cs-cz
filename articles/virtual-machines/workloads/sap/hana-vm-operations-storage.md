---
title: Konfigurace úložiště virtuálních počítačů SAP HANA Azure | Dokumentace Microsoftu
description: Doporučení pro úložiště pro virtuální počítače, které mají nasazené v nich SAP HANA.
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
ms.date: 06/05/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d062b6fff9693d5bda75edd65b8fe88d834eff57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735508"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Konfigurace úložiště virtuálních počítačů Azure SAP HANA

Azure poskytuje různé typy úložišť, které jsou vhodné pro virtuální počítače Azure, které se spouštějí SAP HANA. Typy úložiště Azure, které lze považovat za seznam nasazení SAP HANA jako: 

- Standardní diskové jednotky SSD (Solid-State Drive)
- Premium jednotkami SSD (Solid-State Drive)
- Ultra SSD ve verzi public preview a s aplikacemi SAP produkční není dosud podporován.

Další informace o těchto typů disků nevidí, najdete v článku [vyberte typ disku](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)

Azure nabízí dvě metody nasazení pro virtuální pevné disky na Azure na úrovni Standard a Premium Storage. Pokud celková scénář umožňuje, využít výhod [Azure spravovaný disk](https://azure.microsoft.com/services/managed-disks/) nasazení. 

Seznam typů úložiště a jejich SLA propustnost vstupně-výstupních operací a úložiště, najdete v tématu [dokumentace k Azure pro spravované disky](https://azure.microsoft.com/pricing/details/managed-disks/).

**Doporučení: Použití Azure Premium Storage ve spojení s akcelerátorem zápisu Azure a používat službu Azure Managed Disks pro nasazení**

Ve světě místní zřídka bylo záleží vstupně-výstupních subsystémů a její možnosti. Důvodem bylo, že se na dodavatele zařízení potřeba zajistit, že jsou splněny minimální úložiště pro SAP HANA. Během vytváření infrastruktury Azure sami, měli byste znát některé z těchto požadavků. Některé vlastnosti minimální propustnost, které se zobrazí výzva jsou výsledkem je potřeba:

- Povolit čtení a zápis na **/hana/log** z 250 MB/s s velikostí vstupně-výstupních operací 1 MB
- Povolit čtení aktivity minimálně 400 MB/s pro **/hana/dat** pro vstupně-výstupní operace velikosti 16 MB a 64 MB
- Povolit zápis aktivity alespoň 250 MB/s pro **/hana/dat** s 16 MB a 64 MB. velikost vstupně-výstupních operací

Úložiště s nízkou latencí je uvedený zásadní pro systémy DBMS, dokonce i DBMS, jako je SAP HANA, zachovat data v paměti. Kritická cesta v úložišti je obvykle kolem zápisy protokolu transakce systémů DBMS. Můžete ale také operace, jako je vytváření úložných bodů nebo načítají data v paměti se po obnovení při havárii může být důležité. Proto je **povinné** využívat disky Azure úrovně Premium pro **/hana/data** a **/hana/log** svazky. Abyste dosáhli minimální propustnost **/hana/log** a **/hana/dat** podle potřeby SAP, budete muset sestavit RAID 0 pomocí MDADM nebo LVM přes několik disků Azure Premium Storage. A používat svazky RAID jako **/hana/data** a **/hana/log** svazky. 

**Doporučení: Podle velikosti stripe RAID 0 doporučení je použít:**

- 64 KB nebo 128 KB pro   **/hana/dat**
- 32 KB pro   **/hana/log**

> [!NOTE]
> Nemusíte konfigurovat žádné úroveň redundance pomocí svazky RAID, protože Azure Premium a Standard storage udržují tři Image virtuálního pevného disku. Využití diskového pole RAID svazek je čistě konfigurace svazků, které poskytuje dostatečnou propustnost vstupně-výstupních operací.

Shromažďování počtu virtuálních pevných disků Azure pod RAID, je kumulativní straně propustnost vstupně-výstupních operací a úložiště. Proto když vložíte RAID 0 přes 3 x disky P30 Azure Premium Storage, se vám měl dát třikrát IOPS a třikrát propustnost úložiště jednoho disku Azure Premium Storage P30.

Ukládání do mezipaměti doporučení níže jsou za předpokladu, že vlastnosti vstupně-výstupních operací pro SAP HANA tento seznam jako:

- Existuje téměř jakékoli další úlohy proti HANA datových souborů. Výjimky jsou velké velikosti vstupně-výstupních operací po restartování instance HANA nebo data se načtou do HANA. Další možnost je z větší čtení že vstupně-výstupních operací s datovými soubory mohou být zálohy databáze HANA. V důsledku většinou ukládání do mezipaměti pro čtení nedává smysl, protože ve většině případů, všechny datové svazky souborů je potřeba načíst úplně.
- Zápis proti datových souborů dochází v nárůsty zatížení na základě úložných bodů HANA a obnovení při havárii pro HANA. Zápis úložných bodů je asynchronní a nejsou pojme jakékoli uživatelské transakce. Zápis dat během obnovení při havárii je výkon kritických zajistí systému reagovat rychle znovu. Nicméně obnovení při havárii by měl být raději výjimečné situace
- Existují téměř všechny operace čtení ze souborů znovu HANA. Výjimky jsou velkými vstupy a výstupy při provádění zálohy protokolu transakcí, obnovení při havárii, nebo ve fázi restartování instance HANA.  
- Hlavní zátěž v SAP HANA znovu protokolu jsou zápisy. Závislé na povaze úloh, můžete mít vstupně-výstupních operací malá jako 4 KB nebo v jiných případech vstupně-výstupní operace velikosti nejméně 1 MB. Zapsat latence proti protokolu znovu SAP HANA je důležité výkonu.
- Všechny operace zápisu musí být trvale uloženého na disku spolehlivé způsobem

**Doporučení: V důsledku těchto zjištěné vzory vstupů/výstupů SAP Hana měla by být ukládání do mezipaměti pro jiné svazky za využití Azure Premium Storage nastavena jako:**

- **/ hana/dat** -neexistující ukládání do mezipaměti
- **/ hana/log** – žádné ukládání do mezipaměti - výjimka pro řadu M-Series (viz dále v tomto dokumentu)
- **/ hana/sdílené** – pro čtení, ukládání do mezipaměti


Mějte také celkovou propustnost vstupně-výstupní operace virtuálního počítače při změně velikosti nebo rozhodování o tom, pro virtuální počítač. Celkovou propustnost úložiště virtuálního počítače najdete v článku [paměťově optimalizované velikosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Režim Plánovač vstupně-výstupních operací systému Linux
Linux má několik různých režimech plánování vstupně-výstupních operací. Prostřednictvím Linux dodavateli a SAP doporučuje změna konfigurace vstupně-výstupní režim plánovače pro diskové svazky z **cfq** režimu, aby **noop** režimu. Podrobnosti jsou odkazovány v [1984798 # Poznámka SAP](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="production-storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Produkční řešení úložiště pomocí Azure Write Accelerator pro virtuální počítače Azure řady M-Series
Akcelerátor zápisu Azure je funkce, které je Začínáme zavádět pro virtuální počítače Azure řady M-Series výhradně. Jak uvádí název účelem funkce je zlepšit latenci vstupu/výstupu zápisů ve službě Azure Storage úrovně Premium. Pro SAP HANA, by měla použít u akcelerátorem zápisu **/hana/log** pouze svazku. Proto **/hana/data** a **/hana/log** jsou samostatné svazky s podpůrnými akcelerátor zápisu Azure **/hana/log** pouze svazku. 

> [!IMPORTANT]
> Certifikace SAP HANA pro virtuální počítače Azure řady M-Series je výhradně s Azure Write Accelerator pro **/hana/log** svazku. V důsledku toho se očekává mít nakonfigurovanou Azure Write Accelerator pro produkční scénáře nasazení SAP HANA na virtuálních počítačích Azure řady M-Series **/hana/log** svazku.  

> [!NOTE]
> Pro produkční scénáře, zkontrolujte, zda určitého typu virtuálních počítačů, je podporován pro SAP HANA SAP v [dokumentaci k SAPU pro IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Doporučení: Na doporučenou konfiguraci pro produkční scénáře, vypadat takto:**

| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM | Max. VM I/O<br /> Propustnost | / hana/dat | / hana/log | / hana/sdílené | / root svazku | / usr/sap | Hana/zálohování |
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

Typy virtuálních počítačů M416xx_v2 nejsou dosud k dispozici společností Microsoft veřejnosti. Zkontrolujte, zda splňuje propustnost úložiště pro různé svazky doporučené úlohy, které chcete spustit. Pokud úloha vyžaduje větší svazky pro **/hana/data** a **/hana/log**, je potřeba zvýšit počet virtuálních pevných disků Storage úrovně Premium pro Azure. Pro změnu velikosti svazku s více virtuálními pevnými disky než uvedené zvýšení IOPS a propustnost vstupně-výstupní operace v rámci omezení typu virtuálního počítače Azure.

Azure Write Accelerator pouze funguje ve spojení s [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Proto alespoň na disky Azure Premium Storage tvořící **/hana/log** svazků je nutné nasadit jako spravované disky.

Existují omezení Azure Premium Storage VHD na virtuální počítač, který může podporovat akcelerátor zápisu Azure. Aktuální omezení platí pro:

- 16 virtuálních pevných disků pro virtuální počítač M128xx a M416xx
- 8 virtuálních pevných disků pro virtuální počítač M64xx a M208xx
- 4 virtuální pevné disky pro M32xx virtuálního počítače

Podrobnější pokyny o tom, jak povolit akcelerátor zápisu Azure najdete v článku [akcelerátorem zápisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Podrobnosti a omezení pro akcelerátor zápisu Azure najdete v dokumentaci stejné.

**Doporučení: Je třeba použít akcelerátorem zápisu pro disky, které tvoří /hana/log svazku**


## <a name="cost-conscious-azure-storage-configuration"></a>Nákladů při provádění konfigurace služby Azure Storage
Následující tabulka ukazuje konfiguraci typy virtuálních počítačů, které zákazníci taky využít k hostiteli SAP HANA na virtuálních počítačích Azure. Můžou nastat některé typy virtuálních počítačů, které nemusí splňovat všechna kritéria minimální úložiště pro SAP HANA nebo nejsou oficiálně podporované se SAP HANA SAP. Ale zatím vypadal jako tyto virtuální počítače bez problémů provádět pro neprodukční prostředí. **/Hana/data** a **/hana/log** se spojí dohromady a jeden svazek. V důsledku použití akcelerátor zápisu Azure se může stát omezení z hlediska vstupně-výstupních operací.

> [!NOTE]
> Pro SAP Podporované scénáře, zkontrolujte, zda určitého typu virtuálních počítačů, je podporován pro SAP HANA SAP v [dokumentaci k SAPU pro IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Změna z předchozí doporučení pro řešení při provádění nákladů, je přesunout z [disky Azure HDD standardní](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) provádět lepší a spolehlivější [disků SSD na úrovni Standard Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)


| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM | Max. VM I/O<br /> Propustnost | / hana/dat a/hana/log<br /> prokládané s LVM nebo MDADM | / hana/sdílené | / root svazku | / usr/sap | Hana/zálohování |
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


Typy virtuálních počítačů M416xx_v2 nejsou dosud k dispozici společností Microsoft veřejnosti. Disky, doporučuje se pro typy menších virtuálních počítačů s 3 x P20 oversize svazky týkajících se místa doporučení podle [SAP TDI úložiště dokument White Paper](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Volba se zobrazuje v tabulce však byl proveden poskytuje dostatečnou propustnost disku pro SAP HANA. Pokud potřebujete změny **/hana/záloha** svazek, který se velikostí pro uchování zálohy, které představují dvakrát objem paměti, můžete upravit.   
Zkontrolujte, zda splňuje propustnost úložiště pro různé svazky doporučené úlohy, které chcete spustit. Pokud úloha vyžaduje větší svazky pro **/hana/data** a **/hana/log**, je potřeba zvýšit počet virtuálních pevných disků Storage úrovně Premium pro Azure. Pro změnu velikosti svazku s více virtuálními pevnými disky než uvedené zvýšení IOPS a propustnost vstupně-výstupní operace v rámci omezení typu virtuálního počítače Azure. 

> [!NOTE]
> Konfigurace výše by NIJAK přínosné [jeden virtuální počítač Azure SLA k virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) protože používá kombinaci Azure Premium Storage a Azure Standard Storage. Výběr však byla vybrána k optimalizaci nákladů. Je třeba vybrat Premium Storage pro všechny disky výše uvedený jako úložiště Azure úrovně Standard (Sxx), aby konfigurace virtuálního počítače kompatibilní s Azure SLA jednoho virtuálního počítače.


> [!NOTE]
> Doporučené konfigurace disku uvedeno cílíte minimální požadavky, které SAP vyjadřuje směrem k příslušné infrastrukturu poskytovatele. V nasazení skutečných zákazníků a scénáře úloh situacích došlo k kde tato doporučení stále neposkytl dostatečná možnosti. Toto může nastat situace, kdy zákazník vyžaduje rychlejší opětovné načtení dat po restartu HANA nebo kde zálohování konfigurace vyžaduje větší šířku pásma pro úložiště. Jindy zahrnuté **/hana/log** kde 5000 vstupně-výstupních operací nejsou dostatečná pro konkrétní úlohu. Proto Využijte tato doporučení jako počáteční bod a přizpůsobení podle požadavků zatížení.
>  

## <a name="azure-ultra-ssd-storage-configuration-for-sap-hana"></a>Konfigurace Azure Ultra SSD úložiště pro SAP HANA
Microsoft je právě Představujeme nový typ služby Azure storage volána [Azure Ultra SSD](https://azure.microsoft.com/updates/ultra-ssd-new-azure-managed-disks-offering-for-your-most-latency-sensitive-workloads/). Rozdíl mezi Azure storage nabízí zatím a Ultra SSD je, že velikost disku se už nejsou vázány možnosti disku. Zákazníky můžete definovat tyto možnosti Ultra SSD:

- Velikost disku od 4 GB až 65 536 GiB
- Vstupně-výstupních operací v rozsahu od 100 IOPS 160 kB vstupně-výstupních operací (maximální počet závisí na typy virtuálních počítačů a také)
- Propustnost úložiště z 300 MB/s na 2 000 MB za sekundu

Podrobnosti vyhledat článek [oznamujeme Ultra SSD – další generaci technologie disky Azure (preview)](https://azure.microsoft.com/blog/announcing-ultra-ssd-the-next-generation-of-azure-disks-technology-preview/)

UltraSSD poskytuje možnost definovat jeden disk, který splňuje vaše velikost vstupně-výstupních operací a rozsah propustnost disku. Namísto použití Správce logických svazků jako LVM nebo MDADM nad Azure Premium Storage k vytvoření svazků, které splňují požadavky na propustnost vstupně-výstupních operací a úložiště. Kombinace konfigurace můžete spustit mezi UltraSSD a Storage úrovně Premium. V důsledku toho můžete omezit využití UltraSSD kritické /hana/data výkonu a /hana/log svazky a zahrnují jiných svazků se službou Azure Premium Storage

| SKLADOVOU POLOŽKU VIRTUÁLNÍHO POČÍTAČE | Paměť RAM | Max. VM I/O<br /> Propustnost | objem dat/hana / | propustnost/hana/vstup/výstup dat | / hana/dat vstupně-výstupních operací | svazek s protokolem/hana / | / hana/log vstupně-výstupní propustnost | / hana/log vstupně-výstupních operací |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 500 MB/s | 7500 | 256 GB | 500 MB/s  | 2000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 500 MB/s | 7500 | 256 GB | 500 MB/s  | 2000 |
| M64ls | 512 GiB | 1000 MB/s | 600 GB | 500 MB/s | 7500 | 512 GB | 500 MB/s  | 2000 |
| M64s | 1000 GiB | 1,000 MB/s |  1 200 GB | 500 MB/s | 7500 | 512 GB | 500 MB/s  | 2000 |
| M64ms | 1750 GiB | 1,000 MB/s | 2100 GB | 500 MB/s | 7500 | 512 GB | 500 MB/s  | 2000 |
| M128s | 2000 GiB | 2,000 MB/s |2400 GB | 1200 MB/s |9000 | 512 GB | 800 MBps  | 2000 | 
| M128ms | 3800 GiB | 2,000 MB/s | 4800 GB | 1200 MB/s |9000 | 512 GB | 800 MBps  | 2000 | 
| M208s_v2 | 2850 GiB | 1,000 MB/s | 3500 GB | 1000 Mb/s | 9000 | 512 GB | 500 MB/s  | 2000 | 
| M208ms_v2 | 5700 GiB | 1,000 MB/s | 7200 GB | 1000 Mb/s | 9000 | 512 GB | 500 MB/s  | 2000 | 
| M416s_v2 | 5700 GiB | 2,000 MB/s | 7200 GB | 1 500 milionů b/s | 9000 | 512 GB | 800 MBps  | 2000 | 
| M416ms_v2 | 11400 GiB | 2,000 MB/s | 14400 GB | 1 500 MB/s | 9000 | 512 GB | 800 MBps  | 2000 |   

Typy virtuálních počítačů M416xx_v2 nejsou dosud k dispozici společností Microsoft veřejnosti. Uvedené hodnoty slouží jako výchozí bod a potřebujete má být porovnán s skutečné požadavky. Výhodu služby Azure Ultra SSD je, že hodnoty IOPS a propustnost lze přizpůsobit, aniž byste museli vypnout virtuální počítač nebo zastavení úlohy použity v systému.   

> [!NOTE]
> Snímky úložiště s úložištěm UltraSSD zatím není k dispozici. To blokuje použití snímky virtuálních počítačů pomocí služby Azure Backup

## <a name="next-steps"></a>Další postup
Další informace naleznete v tématu:

- [Příručka pro vysokou dostupnost SAP HANA pro Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
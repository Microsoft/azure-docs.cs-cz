---
title: Výběr typu disku pro virtuální počítače Azure s IaaS – spravované disky
description: Přečtěte si o dostupných typech disků Azure pro virtuální počítače, včetně Ultra disks, Premium SSD, Standard SSD a Standard HDD.
author: roygara
ms.author: rogarana
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1a7e8e71e26af241d16095a5fa1e6a02a7e3d4c2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500763"
---
# <a name="what-disk-types-are-available-in-azure"></a>Jaké typy disků jsou k dispozici v Azure?

Služba Azure Managed disks v současné době nabízí čtyři typy disků. každý typ je zaměřený na konkrétní scénáře zákazníků.

## <a name="disk-comparison"></a>Porovnání disků

V následující tabulce najdete porovnání disků Ultra, Premium Solid-State Drives (SSD), Standard SSD a standardních pevných disků (HDD) pro spravované disky, které vám pomůžou rozhodnout se, co použít.

| Podrobnosti | Disky Ultra | SSD úrovně Premium | SSD úrovně Standard | HDD úrovně Standard |
| ------ | ---------- | ----------- | ------------ | ------------ |
|Typ disku   |SSD   |SSD   |SSD   |HDD   |
|Scénář   |Úlohy náročné na v/v, jako jsou [SAP HANA](workloads/sap/hana-vm-operations-storage.md), databáze nejvyšší úrovně (například SQL, Oracle) a další úlohy náročné na transakce.   |Úlohy v produkčním prostředí a úlohy, u kterých záleží na výkonu   |Webové servery, málo používané podnikové aplikace a vývoj či testování   |Zálohování, úlohy, které nejsou kritické a používají se zřídka   |
|Maximální velikost disku   |65 536 gibibajtů (GiB)    |32 767 GiB    |32 767 GiB   |32 767 GiB   |
|Maximální propustnost   |2 000 MB/s    |900 MB/s   |750 MB/s   |500 MB/s   |
|Maximální počet vstupně-výstupních operací za sekundu   |160 000    |20 000   |6 000   |2 000   |

## <a name="ultra-disk"></a>Disky Ultra

Disky Azure Ultra zajišťují vysokou propustnost, vysoké IOPS a představují konzistentní diskové úložiště s nízkou latencí pro virtuální počítače Azure IaaS. Mezi další výhody prostředí Ultra disks patří schopnost dynamicky měnit výkon disku společně s vašimi úlohami, aniž by bylo potřeba restartovat virtuální počítač. Disky Ultra jsou vhodné pro úlohy náročné na data, jako jsou úlohy SAP HANA, databáze nejvyšší úrovně a úlohy s velkým počtem transakcí. Disky tohoto typu se dají používat jen jako datové disky. Jako disky s operačním systémem doporučujeme používat disky SSD úrovně Premium.

### <a name="performance"></a>Výkon

Když zřizujete disk Ultra, můžete nezávisle konfigurovat kapacitu a výkon disku. Disky Ultra přicházejí v několika pevných velikostech od 4 GiB až 64 TiB a funkce flexibilního modelu konfigurace výkonu, který umožňuje nezávisle konfigurovat vstupně-výstupní operace a propustnost.

Mezi klíčové funkce Ultra diskù patří:

- Kapacita disku: rozsahy disků Ultra jsou v rozsahu 4 GiB až 64 TiB.
- Disk IOPS: disky Ultra podporují limity IOPS 300 IOPS/GiB, až do maximálního počtu 160 kB za sekundu na disk. Abyste dosáhli IOPS, které jste zřídili, zajistěte, aby byl vybraný disk IOPS menší než limit počtu IOPS virtuálních počítačů. Minimální zaručená IOPS na disk je 2 IOPS/GiB, přičemž celkové minimální hodnoty jsou 100 IOPS. Pokud byste například měli 4 GiB disk Ultra, budete mít minimálně 100 IOPS, ale ne osm IOPS.
- Propustnost disku: s Ultra disky je pro každý zřízený IOPS povolený limit propustnosti jednoho disku 256 KiB/s až do maximálního počtu 2000 MB/s na disk (kde MB/s = 10 ^ 6 bajtů za sekundu). Minimální zaručená propustnost na disk je 4KiB/s pro každý zřízený IOPS, přičemž celkové minimální hodnoty jsou 1 MB/s.
- Disky Ultra podporují úpravu atributů výkonu disku (IOPS a propustnosti) za běhu bez odpojení disku od virtuálního počítače. Jakmile se na disku vystavila operace změny velikosti výkonu disku, může trvat až hodinu, než se změna projeví. V průběhu 24 hodin je omezeno čtyři operace změny velikosti výkonu. Je možné, že operace změny velikosti výkonu selže z důvodu nedostatečné kapacity šířky pásma výkonu.

### <a name="disk-size"></a>Velikost disku

|Velikost disku (GiB)  |Limit IOPS  |Limit propustnosti (MB/s)  |
|---------|---------|---------|
|4     |1 200         |300         |
|8     |2 400         |600         |
|16     |4 800         |1 200         |
|32     |9 600         |2 000         |
|64     |19 200         |2 000         |
|128     |38 400         |2 000         |
|256     |76 800         |2 000         |
|512     |153 600         |2 000         |
|1024 – 65536 (velikosti v tomto rozsahu se zvyšují v přírůstcích po 1 TiB)     |160 000         |2 000         |

Disky Ultra jsou navržené tak, aby poskytovaly latence v milisekundách a cílové IOPS a propustnost popsané v předchozí tabulce 99,99% času.

### <a name="ga-scope-and-limitations"></a>Rozsah a omezení GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]


Pokud chcete začít používat disky Ultra, přečtěte si náš článek na předmětu: [použití disků Azure Ultra](disks-enable-ultra-ssd.md).

## <a name="premium-ssd"></a>SSD úrovně Premium

Azure Premium SSD poskytuje podporu vysoce výkonných disků s nízkou latencí pro virtuální počítače a úlohy náročné na vstupně-výstupní operace (v/v). Pokud chcete využít rychlost a výkon disků služby Premium Storage, můžete migrovat stávající disky virtuálních počítačů na Premium SSD. Premium SSD jsou vhodné pro produkční aplikace kritické pro klíčové služby. Prémiová SSD se dá použít jenom u řady virtuálních počítačů, které jsou kompatibilní s Premium Storage.

Další informace o jednotlivých typech a velikostech virtuálních počítačů v Azure pro Windows nebo Linux, včetně toho, které velikosti jsou kompatibilní s úložištěm Premium Storage, najdete v tématu [velikosti virtuálních počítačů v Azure](sizes.md). V tomto článku je potřeba vyhledat všechny jednotlivé velikosti virtuálních počítačů, abyste zjistili, jestli je služba Premium Storage kompatibilní.

### <a name="disk-size"></a>Velikost disku
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

Když zřizujete disk služby Premium Storage na rozdíl od standardního úložiště, zaručujete si kapacitu, IOPS a propustnost tohoto disku. Pokud například vytvoříte P50 disk, Azure zřídí 4 095 GB kapacity úložiště, 7 500 IOPS a propustnost 250 MB/s pro tento disk. Vaše aplikace může využívat celou kapacitu a výkon. SSD úrovně Premium disky jsou navržené tak, aby poskytovaly nízké latence v řádu milisekund a cílové IOPS a propustnost popsané v předchozí tabulce 99,9% času.

## <a name="bursting"></a>Shlukování

SSD úrovně Premium velikosti menší než P30 nyní nabízí shlukování disku a může zvýšit počet IOPS za sekundu na disk až do 3 500 a jejich šířku pásma až do 170 MB/s. Shlukování je automatizované a funguje na základě úvěrového systému. Kredity se automaticky shromažďují v intervalu shlukování, když se provoz na disku nachází pod stanoveným cílem výkonu a kredity se automaticky spotřebovávají při nárůstu provozu po cíli až do maximálního limitu shluku. Maximální limit shluku určuje strop & šířky pásma disku, a to i v případě, že máte k dispozici nárůst kreditů. Shlukování disků poskytuje lepší toleranci proti nepředvídatelným změnám vzorů v/v. Můžete ji nejlépe využít pro spouštění a aplikace disku s operačním systémem s nárazové přenosy.    

Podpora pro rozšíření disků bude ve výchozím nastavení povolená pro nová nasazení použitelných velikostí disků bez nutnosti zásahu uživatele. U stávajících disků s použitelnými velikostmi můžete povolit shlukování pomocí jedné z těchto dvou možností: odpojit a znovu připojit disk nebo zastavit a restartovat připojený virtuální počítač. Pokud je disk připojený k virtuálnímu počítači, který podporuje maximální dobu trvání ve špičce limitu 30 minut, budou se všechny velikosti disků s vyšší úrovní zatížení začínat plným objemem shlukového kreditu. Další informace o tom, jak rozpracovat na discích Azure, najdete v tématu věnovaném [SSD úrovně Premiummu roztržení](./disk-bursting.md). 

### <a name="transactions"></a>Transakce

V případě SSD úrovně Premium je každá vstupně-výstupní operace menší nebo rovna 256 KiB propustnosti považována za jednu vstupně-výstupní operaci. Vstupně-výstupní operace větší než 256 KiB propustnosti se považují za více než více vstupně-výstupních operací o velikosti 256 KiB.

## <a name="standard-ssd"></a>SSD úrovně Standard

Azure Standard SSD je cenově výhodné možnost úložiště optimalizovaná pro úlohy, které vyžadují konzistentní výkon na nižší úrovni IOPS. SSD úrovně Standard nabízí dobré prostředí vstupní úrovně pro ty, kteří chtějí přejít ke cloudu, zejména v případě, že dochází k problémům s odchylkou úloh běžících na vašich řešeních HDD na pracovišti. Ve srovnání se standardem HDD přináší úroveň Standard SSD lepší dostupnost, konzistenci, spolehlivost a latenci. Standardní SSD jsou vhodné pro webové servery, nízkoúrovňové aplikační servery s nízkými IOPS, lehce používané podnikové aplikace a vývojové a testovací úlohy. Jako standard HDD jsou na všech virtuálních počítačích Azure k dispozici standardní SSD.

### <a name="disk-size"></a>Velikost disku
[!INCLUDE [disk-storage-standard-ssd-sizes](../../includes/disk-storage-standard-ssd-sizes.md)]

Standardní SSD jsou navržené tak, aby poskytovaly jednorázové latence milisekund a IOPS a propustnost až do limitů popsaných v předchozí tabulce 99% času. Skutečný počet vstupně-výstupních operací a propustnosti se může lišit v závislosti na vzorech přenosů. Úroveň Standard SSD zajistí jednotnější výkon, než disky HDD s nižší latencí.

### <a name="transactions"></a>Transakce

U standardních SSD se za jednu vstupně-výstupní operaci považuje každá vstupně-výstupní operace menší nebo rovna 256 KiB propustnosti. Vstupně-výstupní operace větší než 256 KiB propustnosti se považují za více než více vstupně-výstupních operací o velikosti 256 KiB. Tyto transakce mají dopad na fakturaci.

## <a name="standard-hdd"></a>HDD úrovně Standard

Azure Standard HDD zajišťuje spolehlivou podporu disků s nízkými náklady pro virtuální počítače, které mají úlohy s necitlivými na latenci. U služby Storage úrovně Standard se data ukládají na jednotky pevného disku (HDD). Latence, IOPS a propustnost HDD úrovně Standard disků se můžou výrazně lišit ve srovnání s disky na bázi SSD. HDD úrovně Standard disky jsou navržené tak, aby poskytovaly latence zápisu v rámci 10ms a latence čtení v 20ms pro většinu vstupně-výstupních operací, ale skutečný výkon se může lišit v závislosti na velikosti vstupně-výstupních operací a způsobu úlohy. Když pracujete s virtuálními počítači, můžete použít standardní disky HDD pro scénáře vývoje a testování a méně důležité úlohy. Standardní HDD jsou dostupné ve všech oblastech Azure a dají se používat se všemi virtuálními počítači Azure.

### <a name="disk-size"></a>Velikost disku
[!INCLUDE [disk-storage-standard-hdd-sizes](../../includes/disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transakce

U standardních HDD se každá vstupně-výstupní operace považuje za jedinou transakci, a to bez ohledu na velikost I/O. Tyto transakce mají dopad na fakturaci.

## <a name="billing"></a>Fakturace

Při použití spravovaných disků platí následující doporučení pro fakturaci:

- Typ disku
- Velikost spravovaného disku
- Snímky
- Přenosy odchozích dat
- Počet transakcí

**Velikost spravovaného disku**: spravované disky se účtují podle zřízené velikosti. Azure namapuje zřízenou velikost (zaokrouhlenou nahoru) na nejbližší nabízenou velikost disku. Podrobnosti o nabízených velikostech disků najdete v předchozích tabulkách. Každý disk se mapuje na podporovanou nabídku velikosti zřízeného disku a účtuje se odpovídajícím způsobem. Pokud jste například zřídili 200 GiB SSD úrovně Standard, namapuje se na nabídku velikost disku E15 (256 GiB). Fakturace za libovolný zřízený disk se účtuje po hodinách za použití měsíčních cen za nabídku úložiště. Pokud jste například zřídili disk E10 a odstranili jste ho po 20 hodinách, bude se vám účtovat částka nabídky E10 na 20 hodin. To je bez ohledu na množství skutečných dat zapsaných na disk.

**Snímky**: snímky se účtují na základě použité velikosti. Pokud třeba vytvoříte snímek spravovaného disku s zřízenou kapacitou 64 GiB a skutečná velikost využitých dat je 10 GiB, bude se snímek fakturovat jenom za využitou velikost dat 10 GiB.

Další informace o snímcích najdete v části věnované snímkům v [přehledu spravovaného disku](managed-disks-overview.md).

**Přenosy odchozích dat**: [odchozí přenosy dat](https://azure.microsoft.com/pricing/details/bandwidth/) (data odcházející z datových center Azure) se účtují podle využití šířky pásma.

**Transakce**: účtuje se počet transakcí, které provedete na standardním spravovaném disku. U standardních SSD se za jednu vstupně-výstupní operaci považuje každá vstupně-výstupní operace menší nebo rovna 256 KiB propustnosti. Vstupně-výstupní operace větší než 256 KiB propustnosti se považují za více než více vstupně-výstupních operací o velikosti 256 KiB. U standardních HDD se každá vstupně-výstupní operace považuje za jedinou transakci, a to bez ohledu na velikost I/O.

Podrobné informace o cenách pro Managed Disks, včetně nákladů na transakce, najdete v článku [Managed disks ceny](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Poplatek za rezervaci virtuálních počítačů Ultra disk

Virtuální počítače Azure mají schopnost určit, jestli jsou kompatibilní s disky Ultra. Virtuální počítač kompatibilní s Ultra disk přiděluje vyhrazenou kapacitu šířky pásma mezi instancemi výpočetního virtuálního počítače a jednotkou škálování úložiště bloku, aby se optimalizoval výkon a snížila latence. Výsledkem přidání této možnosti na virtuální počítač je poplatek za rezervaci, který je uložený jenom v případě, že jste na virtuálním počítači povolili funkci Ultra disk, aniž byste k ní připojili disk Ultra. Pokud je Ultra disk připojený k virtuálnímu počítači kompatibilnímu s Ultra diskem, tento poplatek se nepoužije. Tento poplatek je vázaný na vCPU zřízený na virtuálním počítači. 

> [!Note]
> Pro [omezené velikosti virtuálních počítačů](constrained-vcpu.md)je poplatek za rezervaci založen na skutečném počtu vCPU, nikoli na omezeních jader. U Standard_E32-8s_v3 bude poplatek za rezervaci vycházet z 32 jader. 

Podrobnosti o cenách za Ultra disk najdete na [stránce s cenami za disky Azure](https://azure.microsoft.com/pricing/details/managed-disks/) .

### <a name="azure-disk-reservation"></a>Rezervace disku Azure

Rezervace disku je možnost koupit jeden rok diskového úložiště předem při slevě a snížit tak celkové náklady. Při nákupu rezervovaného disku vyberete určitou SKU disku v cílové oblasti, například 10 P30 (1TiB) Premium SSD v oblasti Východní USA 2 oblast po dobu jednoho roku. Prostředí rezervace se podobá rezervovaným instancím virtuálních počítačů. Můžete začlenit rezervace virtuálních počítačů a disků a maximalizovat tak úspory. V současné době rezervace Azure disks nabízí plán závazku pro SKU úrovně Premium SSD z P30 (1TiB) na P80 (32 TiB) ve všech produkčních oblastech. Další podrobnosti o cenách rezervovaných disků najdete na [stránce s cenami za Azure disks](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="next-steps"></a>Další kroky

Začněte tím, že najdete [ceny Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/) .
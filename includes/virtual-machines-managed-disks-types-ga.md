---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 90cd1d8968963d428eb2d1de4efb458b5f89ca74
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84436013"
---
## <a name="premium-ssd"></a>SSD úrovně Premium

Azure Premium SSD poskytuje podporu vysoce výkonných disků s nízkou latencí pro virtuální počítače a úlohy náročné na vstupně-výstupní operace (v/v). Pokud chcete využít rychlost a výkon disků služby Premium Storage, můžete migrovat stávající disky virtuálních počítačů na Premium SSD. Premium SSD jsou vhodné pro produkční aplikace kritické pro klíčové služby. Prémiová SSD se dá použít jenom u řady virtuálních počítačů, které jsou kompatibilní s Premium Storage.

Další informace o typech a velikostech virtuálních počítačů v Azure pro Windows, včetně toho, které velikosti jsou kompatibilní s úložištěm Premium Storage, najdete v tématu [velikosti virtuálních počítačů s Windows](../articles/virtual-machines/windows/sizes.md). Další informace o typech a velikostech virtuálních počítačů v Azure pro Linux, včetně toho, které velikosti jsou kompatibilní s úložištěm Premium Storage, najdete v tématu [velikosti virtuálních počítačů se systémem Linux](../articles/virtual-machines/linux/sizes.md). V některém z těchto článků je potřeba vyhledat v článku o jednotlivých velikostech virtuálních počítačů, abyste zjistili, jestli je služba Premium Storage kompatibilní.

### <a name="disk-size"></a>Velikost disku
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Když zřizujete disk služby Premium Storage na rozdíl od standardního úložiště, zaručujete si kapacitu, IOPS a propustnost tohoto disku. Pokud například vytvoříte P50 disk, Azure zřídí 4 095 GB kapacity úložiště, 7 500 IOPS a propustnost 250 MB/s pro tento disk. Vaše aplikace může využívat celou kapacitu a výkon. SSD úrovně Premium disky jsou navržené tak, aby poskytovaly nízké latence v řádu milisekund a cílové IOPS a propustnost popsané v předchozí tabulce 99,9% času.

## <a name="bursting"></a>Shlukování

SSD úrovně Premium velikosti menší než P30 nyní nabízí shlukování disku a může zvýšit počet IOPS za sekundu na disk až do 3 500 a jejich šířku pásma až 170 MB/s. Shlukování je automatizované a funguje na základě úvěrového systému. Kredity se automaticky shromažďují v intervalu shlukování, když se provoz na disku nachází pod stanoveným cílem výkonu a kredity se automaticky spotřebovávají při nárůstu provozu po cíli až do maximálního limitu shluku. Maximální limit shluku určuje strop & šířky pásma disku, a to i v případě, že máte k dispozici nárůst kreditů. Shlukování disků poskytuje lepší toleranci proti nepředvídatelným změnám vzorů v/v. Můžete ji nejlépe využít pro spouštění a aplikace disku s operačním systémem s nárazové přenosy.    

Podpora pro rozšíření disků bude ve výchozím nastavení povolená pro nová nasazení použitelných velikostí disků bez nutnosti zásahu uživatele. U stávajících disků s použitelnými velikostmi můžete povolit shlukování pomocí jedné z těchto dvou možností: odpojit a znovu připojit disk nebo zastavit a restartovat připojený virtuální počítač. Pokud je disk připojený k virtuálnímu počítači, který podporuje maximální dobu trvání ve špičce limitu 30 minut, budou se všechny velikosti disků s vyšší úrovní zatížení začínat plným objemem shlukového kreditu. Další informace o tom, jak rozpracovat na discích Azure, najdete v tématu věnovaném [SSD úrovně Premiummu roztržení](../articles/virtual-machines/linux/disk-bursting.md). 

### <a name="transactions"></a>Transakce

V případě SSD úrovně Premium je každá vstupně-výstupní operace menší nebo rovna 256 KiB propustnosti považována za jednu vstupně-výstupní operaci. Vstupně-výstupní operace větší než 256 KiB propustnosti se považují za více než více vstupně-výstupních operací o velikosti 256 KiB.

## <a name="standard-ssd"></a>SSD úrovně Standard

Azure Standard SSD je cenově výhodné možnost úložiště optimalizovaná pro úlohy, které vyžadují konzistentní výkon na nižší úrovni IOPS. SSD úrovně Standard nabízí dobré prostředí vstupní úrovně pro ty, kteří chtějí přejít ke cloudu, zejména v případě, že dochází k problémům s odchylkou úloh běžících na vašich řešeních HDD na pracovišti. Ve srovnání se standardem HDD přináší úroveň Standard SSD lepší dostupnost, konzistenci, spolehlivost a latenci. Standardní SSD jsou vhodné pro webové servery, nízkoúrovňové aplikační servery s nízkými IOPS, lehce používané podnikové aplikace a vývojové a testovací úlohy. Jako standard HDD jsou na všech virtuálních počítačích Azure k dispozici standardní SSD.

### <a name="disk-size"></a>Velikost disku
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Standardní SSD jsou navržené tak, aby poskytovaly jednorázové latence milisekund a IOPS a propustnost až do limitů popsaných v předchozí tabulce 99% času. Skutečný počet vstupně-výstupních operací a propustnosti se může lišit v závislosti na vzorech přenosů. Úroveň Standard SSD zajistí jednotnější výkon, než disky HDD s nižší latencí.

### <a name="transactions"></a>Transakce

U standardních SSD se za jednu vstupně-výstupní operaci považuje každá vstupně-výstupní operace menší nebo rovna 256 KiB propustnosti. Vstupně-výstupní operace větší než 256 KiB propustnosti se považují za více než více vstupně-výstupních operací o velikosti 256 KiB. Tyto transakce mají dopad na fakturaci.

## <a name="standard-hdd"></a>HDD úrovně Standard

Azure Standard HDD zajišťuje spolehlivou podporu disků s nízkými náklady pro virtuální počítače, které mají úlohy s necitlivými na latenci. U služby Storage úrovně Standard se data ukládají na jednotky pevného disku (HDD). Latence, IOPS a propustnost HDD úrovně Standard disků se můžou výrazně lišit ve srovnání s disky na bázi SSD. HDD úrovně Standard disky jsou navržené tak, aby poskytovaly latence zápisu v rámci 10ms a latence čtení v 20ms pro většinu vstupně-výstupních operací, ale skutečný výkon se může lišit v závislosti na velikosti vstupně-výstupních operací a způsobu úlohy. Když pracujete s virtuálními počítači, můžete použít standardní disky HDD pro scénáře vývoje a testování a méně důležité úlohy. Standardní HDD jsou dostupné ve všech oblastech Azure a dají se používat se všemi virtuálními počítači Azure.

### <a name="disk-size"></a>Velikost disku
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

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

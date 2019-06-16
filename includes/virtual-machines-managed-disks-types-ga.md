---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2bc5602011ed64b11b1b8c96b7e69a8d5ee9bf32
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133226"
---
## <a name="premium-ssd"></a>Premium SSD

Azure premium SSD poskytovat podporu vysoce výkonných a s nízkou latencí disků pro virtuální počítače (VM) s vstup/výstup (IO)-náročné úlohy. Pokud chcete využít výhod rychlost a výkon disků premium storage, můžete migrovat existující disky virtuálních počítačů na Premium SSD. SSD disky Premium jsou vhodné pro důležité produkční aplikace. Disky Premium SSD jde použít jenom s řadu virtuálních počítačů, které jsou kompatibilní s úložištěm premium.

Další informace o jednotlivých typů virtuálních počítačů a velikosti v Azure pro Windows, včetně toho, jaké velikosti jsou premium, kompatibilní s úložištěm, naleznete v tématu [velikosti virtuálních počítačů s Windows](../articles/virtual-machines/windows/sizes.md). Další informace o jednotlivých typech virtuálních počítačů a velikosti v Azure pro Linux, které velikosti jsou premium, kompatibilní s úložištěm, včetně naleznete v tématu [velikosti virtuálního počítače s Linuxem](../articles/virtual-machines/linux/sizes.md).

### <a name="disk-size"></a>Velikost disku
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Při zřizování disku úložiště úrovně premium, na rozdíl od úložiště úrovně standard, je zaručena kapacita, IOPS a propustnost tohoto disku. Například pokud vytvoříte P50 disku, Azure mu kapacitu 4 095 GB úložiště, 7500 IOPS a propustnost 250 MB/s pro tento disk. Aplikace můžete použít nebo její část kapacitu a výkon. SSD disky Premium jsou navržené k zajištění nízké latence – milisekund a cílit na vstupně-výstupních operací a propustnosti je popsáno v předchozí tabulce 99,9 % času.

### <a name="transactions"></a>Transakce

Pro úrovně premium SSD každý vstupně-výstupní operace menší než nebo rovna až 256 KiB propustnosti se považuje za jeden vstupně-výstupní operace. Vstupně-výstupní operace větší než 256 KiB propustnosti se považují za více vstupně-výstupních operací, o velikosti 256 KiB.

## <a name="standard-ssd"></a>SSD úrovně Standard

SSD disky Azure standard je možnost a nákladově efektivní služba storage optimalizované pro úlohy, které je třeba zajistit konzistentní výkon na nižších úrovních vstupně-výstupních operací. SSD na úrovni Standard nabízí prostředí dobrý základní úrovně pro ty, kteří chtějí přesunout do cloudu, zejména v případě, že dochází k problémům s odchylkou úloh běžících na řešení pevný disk v místním prostředí. Porovnání na standardní pevné disky, standardní jednotky SSD poskytují lepší dostupnosti, konzistence, spolehlivost a latenci. SSD disky Standard jsou vhodné pro webové servery, s nízkou vstupně-výstupních operací aplikační servery, málo používaná podnikových aplikací a vývojové a testovací úlohy. Podobně jako standardní pevné disky jsou k dispozici na všech virtuálních počítačích Azure standardní disky SSD.

### <a name="disk-size"></a>Velikost disku
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Standardní disky SSD jsou navržené k poskytování latence v řádu milisekund a IOPS a propustnost až do omezení je popsáno v předchozí tabulce 99 % času. Skutečné IOPS a propustnost může někdy lišit v závislosti na vzory přenosů. Standardní disky SSD zajistí konzistentnější výkon než HDD disky s nižší latencí.

### <a name="transactions"></a>Transakce

Pro standardní jednotky SSD každý vstupně-výstupní operace menší než nebo rovna až 256 KiB propustnosti se považuje za jednu vstupně-výstupní operace. Vstupně-výstupní operace větší než 256 KiB propustnosti se považují za více vstupně-výstupních operací, o velikosti 256 KiB. Tyto transakce ovlivnit fakturaci.

## <a name="standard-hdd"></a>Standard HDD

Azure standardní pevné disky poskytovat podporu spolehlivé, úsporné disků pro virtuální počítače běžící úlohu kterému latence nevadí. Se standardním úložiště jsou data uložená na pevných disků (HDD). Porovnání s jednotkami SSD disky může více výrazně lišit latenci, vstupně-výstupních operací a propustnosti standardní HDD disky. Standardní HDD disky jsou navržené tak poskytovat latence zápisu pod 10 ms a latenci pod 20ms pro většinu operací vstupně-výstupních operací čtení, ale skutečný výkon se může lišit v závislosti na velikost a úlohy vzor vstupně-výstupních operací. Při práci s virtuálními počítači, můžete použít standardní HDD disky, pro scénáře vývoje/testování a méně důležité úlohy. Standardní pevné disky jsou dostupné ve všech oblastech Azure a můžou používat všechny virtuální počítače Azure.

### <a name="disk-size"></a>Velikost disku
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transakce

Standardní pevné disky se nepovažuje za každé vstupně-výstupní operace, jako jedna transakce, bez ohledu na velikost vstupně-výstupních operací. Tyto transakce ovlivnit fakturaci.

## <a name="billing"></a>Fakturace

Při použití spravovaných disků, platí následující aspekty fakturace:

- Typ disku
- Velikost spravovaného disku
- Snímky
- Přenosy odchozích dat
- Počet transakcí

**Spravovaná velikost disku**: spravované disky se účtují na zřízená velikost. Azure maps zřízená velikost (zaokrouhleno) na nejbližší velikost nabízený disku. Podrobnosti o velikosti disků nabízejí najdete v předchozích tabulkách. Každý disk mapuje na nabídku velikost zřízeného disku podporované a odpovídajícím způsobem se účtuje. Například pokud jste zřídili 200 GB SSD na úrovni Standard, mapuje se na nabídky velikost disku E15 (256 GB). Za všechny zajišťovaným diskem se fakturuje po hodinách pomocí cenu za měsíc pro nabídku služby Premium Storage. Například pokud zřízené disk s E10 a odstraní ji po 20 hodin, bude se vám účtovat pro nabídky E10 nebo jeho poměrnou část 20 hodin. To je bez ohledu na velikost skutečných dat zapsaných na disk.

**Snímky**: Snímky se účtuje podle velikosti použít. Například pokud vytvoříte snímku spravovaného disku s zřízená kapacita 64 GiB a skutečné používaných dat velikosti 10 GB, snímku se mu fakturuje jenom používaných dat velikosti 10 GB.

---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6740ea320f2d950386da12eb44726e2c826b60a4
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80386083"
---
## <a name="premium-ssd"></a>SSD úrovně Premium

Azure premium SSD poskytují vysoce výkonnou diskovou podporu s nízkou latencí pro virtuální počítače (VM) se vstupními a výstupními úlohami náročnými na vstup a výstup. Chcete-li využít rychlost a výkon disků úložiště premium, můžete migrovat existující disky virtuálních počítačů na disky SSD premium. Prémiové SSD dispozice jsou vhodné pro klíčové produkční aplikace. Ssd disky Premium lze použít pouze s řadami virtuálních počítačů, které jsou kompatibilní s prémiovým úložištěm.

Další informace o jednotlivých typech a velikostech virtuálních počítačů v Azure pro Windows, včetně velikostí, které jsou kompatibilní s prémiovým úložištěm, najdete v [tématu Velikosti virtuálních počítačů s Windows](../articles/virtual-machines/windows/sizes.md). Další informace o jednotlivých typech a velikostech virtuálních počítačů v Azure pro Linux, včetně velikostí, které jsou kompatibilní s prémiovým úložištěm, najdete v [tématu Velikosti virtuálních počítačů v Linuxu](../articles/virtual-machines/linux/sizes.md). Z jednoho z těchto článků je třeba zkontrolovat každý jednotlivý článek velikosti virtuálního počítače k určení, zda je kompatibilní s úložištěm premium.

### <a name="disk-size"></a>Velikost disku
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Při zřizování disk úložiště premium, na rozdíl od standardního úložiště, máte zaručena kapacita, vigbování a propustnost tohoto disku. Například pokud vytvoříte disk P50, Azure zřídí kapacitu úložiště 4 095 GB, 7 500 IOPS a 250 MB/s propustnost pro tento disk. Vaše aplikace může používat celou kapacitu nebo část kapacity a výkonu. Disky Premium SSD jsou navrženy tak, aby poskytovaly nízké jednociferné milisekundové latence a cílové VOPS a propustnost popsané v předchozí tabulce 99,9 % času.

## <a name="bursting"></a>Prasknutí

Premium SSD velikosti menší než P30 nyní nabízejí disk prasknutí a může prasknout jejich IOPS na disk až 3500 a jejich šířku pásma až 170 Mbps. Roztržení je automatizované a funguje na základě kreditního systému. Kredity se automaticky hromadí v intervalu shluků, když je provoz na disku nižší než zřízený cíl výkonu a kredity se automaticky spotřebovávají, když provoz překročí cíl, a to až do maximálního limitu roztržení. Maximální limit shluků definuje strop diskových iOPS & šířku pásma, i když máte shlukkredity, ze kterých můžete spotřebovávat. Roztržení disku poskytuje lepší toleranci na nepředvídatelné změny vzorů vi. Můžete nejlépe využít pro spuštění disku operačního systému a aplikace s špičatý provoz.    

Podpora roztržení disků bude ve výchozím nastavení povolena u nových nasazení příslušných velikostí disků bez nutnosti akce uživatele. Pro existující disky příslušných velikostí můžete povolit přetržení s některou ze dvou možností: odpojit a znovu připojit disk nebo zastavit a restartovat připojený virtuální počítač. Všechny velikosti burst příslušné disk začne s plnou roztržení kreditní kbelík, když je disk připojen k virtuálnímu počítači, který podporuje maximální dobu trvání na vrcholu burst limit 30 minut. Další informace o tom, jak praskování práce na disky Azure, najdete v [tématu Premium SSD bursting](../articles/virtual-machines/linux/disk-bursting.md). 

### <a name="transactions"></a>Transakce

U prémiových ssd disponionů je každá vstupně-va/vprovozná operace menší nebo rovna 256 KiB propustností považována za jednu vstupně-va. Vstupně-v operace větší než 256 KiB propustnost jsou považovány za více V/O velikosti 256 KiB.

## <a name="standard-ssd"></a>SSD úrovně Standard

Standardní ssd disky Azure jsou nákladově efektivní možnost úložiště optimalizovaná pro úlohy, které vyžadují konzistentní výkon na nižších úrovních VOPS. Standardní SSD nabízí dobré vstupní úroveň prostředí pro ty, kteří chtějí přejít do cloudu, zejména pokud máte problémy s odchylkou úloh spuštěných na řešení hdd v místním prostředí. Ve srovnání se standardními pevnými disky poskytují standardní disky SSD lepší dostupnost, konzistenci, spolehlivost a latenci. Standardní ssd disy jsou vhodné pro webové servery, aplikační servery s nízkým iOPS, lehce používané podnikové aplikace a úlohy Pro v/Test. Stejně jako standardní pevné disky jsou standardní disky SSD dostupné na všech virtuálních počítačích Azure.

### <a name="disk-size"></a>Velikost disku
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Standardní ssd dispony jsou navrženy tak, aby poskytovaly jednociferné milisekundové latence a vips a propustnost až do limitů popsaných v předchozí tabulce 99 % času. Skutečné vipony a propustnost se mohou někdy lišit v závislosti na vzorcích provozu. Standardní disky SSD poskytují konzistentnější výkon než disky HDD s nižší latencí.

### <a name="transactions"></a>Transakce

Pro standardní SSD je každá vstupně-vaoperace menší nebo rovna 256 KiB propustností považována za jednu vstupně-va. Vstupně-v operace větší než 256 KiB propustnost jsou považovány za více V/O velikosti 256 KiB. Tyto transakce mají dopad na fakturaci.

## <a name="standard-hdd"></a>HDD úrovně Standard

Standardní pevné disky Azure poskytují spolehlivou a nízkonákladovou diskovou podporu pro virtuální počítače s úlohami necitlivými na latenci. Se standardním úložištěm jsou data uložena na pevných discích (HDD). Latence, IOPS a propustnost disků se standardním pevným diskem se mohou ve srovnání s disky založenými na ssd lišit ve větší míře. Standardní pevné disky jsou navrženy tak, aby poskytovaly latence zápisu pod 10 ms a čtení latence pod 20 ms pro většinu operací vi, ale skutečný výkon se může lišit v závislosti na velikosti videa a struktuře pracovního vytížení. Při práci s virtuálními počítačemi můžete použít standardní disky HDD pro scénáře pro vývoj a testování a méně kritické úlohy. Standardní pevné disky jsou dostupné ve všech oblastech Azure a můžou se používat se všemi virtuálními počítači Azure.

### <a name="disk-size"></a>Velikost disku
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transakce

U standardních pevných disků je každá operace vstupně-operace považována za jednu transakci bez ohledu na velikost vstupně-va. Tyto transakce mají dopad na fakturaci.

## <a name="billing"></a>Fakturace

Při použití spravovaných disků platí následující aspekty fakturace:

- Typ disku
- velikost spravovaného disku
- Snímky
- Odchozí datové přenosy
- Počet transakcí

**Velikost spravovaného disku:** spravované disky se účtují podle zřízené velikosti. Azure mapuje zřízenou velikost (zaokrouhlenou nahoru) na nejbližší nabízenou velikost disku. Podrobnosti o nabízených velikostech disků naleznete v předchozích tabulkách. Každý disk se mapuje na podporovanou nabídku velikosti zřízeného disku a účtuje se odpovídajícím způsobem. Pokud jste například zřídíte 200 GiB Standard SSD, mapuje se na nabídku velikosti disku E15 (256 GiB). Fakturace pro jakýkoli zřízený disk se účtuje poměrně každou hodinu pomocí měsíční ceny pro nabídku úložiště Premium. Pokud jste například zřídit e10 disk a odstranit po 20 hodinách, bude vám účtována e10 nabízí poměrně 20 hodin. To je bez ohledu na množství skutečných dat zapsaných na disk.

**Snímky**: Snímky se účtují na základě použité velikosti. Pokud například vytvoříte snímek spravovaného disku se zřízenou kapacitou 64 Gb a skutečně použitou velikostí dat 10 GB, bude snímek účtován pouze za použitou velikost dat 10 GB.

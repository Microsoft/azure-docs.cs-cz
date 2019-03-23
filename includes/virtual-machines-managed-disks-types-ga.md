---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 88a4110d68dc8aa921d647f90de654d2ebb4e17d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395584"
---
## <a name="premium-ssd"></a>Premium SSD

Azure premium SSD poskytovat podporu vysoce výkonných a s nízkou latencí disků pro virtuální počítače (VM) s vstup/výstup (IO)-náročné úlohy. Pokud chcete využít výhod rychlost a výkon disků premium storage, můžete migrovat existující disky virtuálních počítačů na Premium SSD. SSD disky Premium jsou vhodné pro důležité produkční aplikace.

### <a name="disk-size"></a>Velikost disku
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="standard-ssd"></a>SSD úrovně Standard

SSD disky Azure standard je možnost a nákladově efektivní služba storage optimalizované pro úlohy, které je třeba zajistit konzistentní výkon na nižších úrovních vstupně-výstupních operací. SSD na úrovni Standard nabízí prostředí dobrý základní úrovně pro ty, kteří chtějí přesunout do cloudu, zejména v případě, že dochází k problémům s odchylkou úloh běžících na řešení pevný disk v místním prostředí. Standardní jednotky SSD poskytují lepší dostupnosti, konzistence, spolehlivost a latenci v porovnání s HDD disky. SSD disky Standard jsou vhodné pro webové servery, s nízkou vstupně-výstupních operací aplikační servery, málo používaná podnikových aplikací a vývojové a testovací úlohy.

### <a name="disk-size"></a>Velikost disku
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

## <a name="standard-hdd"></a>Standard HDD

Azure standardní pevné disky poskytovat podporu spolehlivé, úsporné disků pro virtuální počítače běžící úlohu kterému latence nevadí. Také podporuje objekty BLOB, tabulky, fronty a soubory. Se standardním úložiště jsou data uložená na pevných disků (HDD). Při práci s virtuálními počítači, můžete použít standardní disky SSD a HDD, pro scénáře vývoje/testování a méně důležité úlohy. Storage úrovně Standard je k dispozici ve všech oblastech Azure.

### <a name="disk-size"></a>Velikost disku
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

## <a name="billing"></a>Fakturace

Při použití spravovaných disků, platí následující aspekty fakturace:

- Typ disku
- Velikost spravovaného disku
- Snímky
- Přenosy odchozích dat
- Počet transakcí

**Spravovaná velikost disku**: spravované disky se účtují na zřízená velikost. Azure maps zřízená velikost (zaokrouhleno) na nejbližší velikost nabízený disku. Podrobnosti o velikosti disků nabízejí najdete v předchozích tabulkách. Každý disk mapuje na nabídku velikost zřízeného disku podporované a odpovídajícím způsobem se účtuje. Například pokud jste zřídili 200 GB SSD na úrovni Standard, mapuje se na nabídky velikost disku E15 (256 GB). Za všechny zajišťovaným diskem se fakturuje po hodinách pomocí cenu za měsíc pro nabídku služby Premium Storage. Například pokud zřízené disk s E10 a odstraní ji po 20 hodin, bude se vám účtovat pro nabídky E10 nebo jeho poměrnou část 20 hodin. To je bez ohledu na velikost skutečných dat zapsaných na disk.

**Snímky**: Snímky se účtuje podle velikosti použít. Například pokud vytvoříte snímku spravovaného disku s zřízená kapacita 64 GiB a skutečné používaných dat velikosti 10 GB, snímku se mu fakturuje jenom používaných dat velikosti 10 GB.
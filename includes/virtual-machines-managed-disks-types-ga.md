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
ms.openlocfilehash: 0201776914610ddaca50a670fc500156a65cd734
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2019
ms.locfileid: "58124681"
---
## <a name="premium-ssd"></a>Premium SSD

Azure premium SSD poskytovat podporu vysoce výkonných a s nízkou latencí disků pro virtuální počítače (VM) s vstup/výstup (IO)-náročné úlohy. Pokud chcete využít výhod rychlost a výkon disků premium storage, můžete migrovat existující disky virtuálních počítačů na Premium SSD. SSD disky Premium jsou vhodné pro důležité produkční aplikace.

### <a name="disk-size"></a>Velikost disku

Velikosti s hvězdičkou jsou aktuálně ve verzi preview.

| Velikosti úrovně Premium SSD | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 * | P70 * | P80 * |
|-------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Velikost disku v GB | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,095 | 8 192 | 16,384 | 32,767 |
| Vstupně-výstupní operace za sekundu / disk | Až 120 | Až 240 | Až 500 | Až 1100 | Až 2,300 | Až 5 000 | Až 7500 | Až 7500 | Až 12 500 | Až pro 15 000 | Až 20 000 |
| Propustnost / disk | Až pro 25 MiB za sekundu | Až na 50 MiB za sekundu | Až 100 MiB/s | Až 125 MiB za sekundu | Až 150 MiB za sekundu | Až 200 MiB za sekundu | Až 250 MiB za sekundu | Až 250 MiB za sekundu| Až 480 MiB za sekundu | Až 750 MiB za sekundu | Až 750 MiB za sekundu |

## <a name="standard-ssd"></a>SSD úrovně Standard

SSD disky Azure standard je možnost a nákladově efektivní služba storage optimalizované pro úlohy, které je třeba zajistit konzistentní výkon na nižších úrovních vstupně-výstupních operací. SSD na úrovni Standard nabízí prostředí dobrý základní úrovně pro ty, kteří chtějí přesunout do cloudu, zejména v případě, že dochází k problémům s odchylkou úloh běžících na řešení pevný disk v místním prostředí. Standardní jednotky SSD poskytují lepší dostupnosti, konzistence, spolehlivost a latenci v porovnání s HDD disky. SSD disky Standard jsou vhodné pro webové servery, s nízkou vstupně-výstupních operací aplikační servery, málo používaná podnikových aplikací a vývojové a testovací úlohy.

### <a name="disk-size"></a>Velikost disku

Velikosti s hvězdičkou jsou aktuálně ve verzi preview.

| Standardní velikosti SSD | E4 | E6 | E10 | E15 | E20 | E30 | E40 | E50 | E60* | E70* | E80* |
|--------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Velikost disku v GB | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,095 | 8 192 | 16,384 | 32,767 |
| Vstupně-výstupní operace za sekundu / disk | Až 120 | Až 240 | Až 500 | Až 500 | Až 500 | Až 500 | Až 500 | Až 500 | 1 až 300 | Až 2 000 | Až 2 000 |
| Propustnost / disk |  Až pro 25 MiB za sekundu |  Až na 50 MiB za sekundu  |  Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu| Až 300 MiB za sekundu |  Až 500 MiB/s | Až 500 MiB/s |

## <a name="standard-hdd"></a>Standard HDD

Azure standardní pevné disky poskytovat podporu spolehlivé, úsporné disků pro virtuální počítače běžící úlohu kterému latence nevadí. Také podporuje objekty BLOB, tabulky, fronty a soubory. Se standardním úložiště jsou data uložená na pevných disků (HDD). Při práci s virtuálními počítači, můžete použít standardní disky SSD a HDD, pro scénáře vývoje/testování a méně důležité úlohy. Storage úrovně Standard je k dispozici ve všech oblastech Azure.

### <a name="disk-size"></a>Velikost disku

Velikosti s hvězdičkou jsou aktuálně ve verzi preview.

| Disk typu Standard | S4 | S6 | S10 | S15 | S20 | S30 | S40 | S50 | S60* | S70* | S80* |
|--------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Velikost disku v GB | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,095 | 8 192 | 16,384 | 32,767 |
| Vstupně-výstupní operace za sekundu / disk | Až 500 | Až 500 | Až 500 | Až 500 | Až 500 | Až 500 | Až 500 | Až 500 | 1 až 300 | Až 2 000 | Až 2 000 |
| Propustnost / disk | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu| Až 300 MiB za sekundu | Až 500 MiB/s | Až 500 MiB/s |

## <a name="billing"></a>Fakturace

Při použití spravovaných disků, platí následující aspekty fakturace:

- Typ disku
- Velikost spravovaného disku
- Snímky
- Přenosy odchozích dat
- Počet transakcí

**Spravovaná velikost disku**: spravované disky se účtují na zřízená velikost. Azure maps zřízená velikost (zaokrouhleno) na nejbližší velikost nabízený disku. Podrobnosti o velikosti disků nabízejí najdete v předchozích tabulkách. Každý disk mapuje na nabídku velikost zřízeného disku podporované a odpovídajícím způsobem se účtuje. Například pokud jste zřídili 200 GB SSD na úrovni Standard, mapuje se na nabídky velikost disku E15 (256 GB). Za všechny zajišťovaným diskem se fakturuje po hodinách pomocí cenu za měsíc pro nabídku služby Premium Storage. Například pokud zřízené disk s E10 a odstraní ji po 20 hodin, bude se vám účtovat pro nabídky E10 nebo jeho poměrnou část 20 hodin. To je bez ohledu na velikost skutečných dat zapsaných na disk.

**Snímky**: Snímky se účtuje podle velikosti použít. Například pokud vytvoříte snímku spravovaného disku s zřízená kapacita 64 GiB a skutečné používaných dat velikosti 10 GB, snímku se mu fakturuje jenom používaných dat velikosti 10 GB.
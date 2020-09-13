---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 65f6c239f34775efff6a2ea2e399064a7702606a
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663875"
---
![Dokumentace k Dsv3](media/vm-disk-performance/dsv3-documentation.jpg)

Maximální propustnost disku, která není v **mezipaměti** , je výchozím limitem úložiště, který může virtuální počítač zpracovat. Maximální propustnost úložiště **v mezipaměti** je samostatný limit, pokud povolíte ukládání hostitele do mezipaměti. Povolení ukládání do mezipaměti hostitele můžete provést při vytváření virtuálního počítače a připojení disků. Můžete také upravit a zapnout a vypnout ukládání disků do mezipaměti na stávajícím virtuálním počítači.

![Ukládání hostitelů do mezipaměti](media/vm-disk-performance/host-caching.jpg)

Mezipaměť hostitele lze upravit tak, aby odpovídala vašim požadavkům na úlohy pro každý disk. Mezipaměť hostitele můžete nastavit jen pro čtení pro úlohy, které provedou pouze operace čtení a čtení a zápis pro úlohy, které mají rovnováhu na operacích čtení a zápisu. Pokud vaše úloha nedodržuje žádný z těchto vzorů, nebudete moct používat ukládání hostitelů do mezipaměti. 

Pojďme pokračovat s příkladem pro náš Standard_D8s_v3 virtuální počítač. S výjimkou této doby povolíme ukládání hostitelů do mezipaměti pro disky a limit IOPS virtuálního počítače je 16 000 IOPS. Připojeno k virtuálnímu počítači jsou tři podkladové disky P30, které mohou zpracovávat 5 000 IOPS.

Nastavení:
- Standard_D8s_v3 
    - IOPS v mezipaměti: 16 000
    - Neuložené IOPS s mezipamětí: 12 800
- Disk s operačním systémem P30 
    - IOPS: 5 000
    - Ukládání hostitelů do mezipaměti povoleno 
- 2 datové disky P30
    - IOPS: 5 000
    - Ukládání hostitelů do mezipaměti povoleno

![Příklad ukládání hostitele do mezipaměti](media/vm-disk-performance/host-caching-example-without-remote.jpg)

Nyní aplikace, která používá tento Standard_D8s_v3 virtuální počítač s povoleným ukládáním do mezipaměti, vytvoří požadavek na 15 000 IOPS. Tyto požadavky se u každého připojeného disku rozdělují jako 5 000 IOPS a neprojeví se žádné capping výkonu.

## <a name="combined-uncached-and-cached-limits"></a>Kombinované omezení neuložených do mezipaměti a mezipaměti

Limity ukládání do mezipaměti virtuálního počítače jsou oddělené od jejich neuloženého limitu. To znamená, že můžete povolit ukládání do mezipaměti hostitele na discích připojených k virtuálnímu počítači a zároveň Nepovolit ukládání do mezipaměti hostitele na jiných discích, aby virtuální počítače mohly získat celkový počet vstupně-výstupních operací úložiště pro limit uložený v mezipaměti a limit neuloženého v mezipaměti. Podíváme se na příklad, abychom vám pomohli přesvědčit, jak tato omezení pracují dohromady, a budeme pokračovat s konfigurací připojení k virtuálnímu počítači s Standard_D8s_v3 a disky Premium.

Nastavení:
- Standard_D8s_v3 
    - IOPS v mezipaměti: 16 000
    - Neuložené IOPS s mezipamětí: 12 800
- Disk s operačním systémem P30 
    - IOPS: 5 000
    - Ukládání hostitelů do mezipaměti povoleno 
- 2 datové disky P30 × 2
    - IOPS: 5 000
    - Ukládání hostitelů do mezipaměti povoleno
- 2 datové disky P30 × 2
    - IOPS: 5 000
    - Ukládání hostitelů do mezipaměti zakázáno

![Příklad mezipaměti hostitele se vzdáleným úložištěm](media/vm-disk-performance/host-caching-example-with-remote.jpg)

Aplikace spuštěná v Standard_D8s_v3 virtuálním počítači teď vytvoří žádost o 25 000 IOPS. Tento požadavek se u každého přiloženého disku rozděluje jako 5 000 IOPS, kde 3 z těchto disků používá ukládání hostitele do mezipaměti a 2 z těchto disků není. Vzhledem k tomu, že 3 použití mezipaměti hostitele je v rámci limitů uložených v mezipaměti 16 000, jsou tyto požadavky úspěšně dokončeny a nedochází k capping výkonu úložiště. Vzhledem k tomu, že 2 disky, které nepoužívají ukládání hostitelů do mezipaměti, jsou také v rámci 12 800 limitů neukládaných do mezipaměti, jsou tyto požadavky také úspěšně dokončeny a nedochází k capping.

## <a name="metrics-for-disk-performance"></a>Metriky pro výkon disku
Máme metriky v Azure, které poskytují přehled o tom, jak virtuální počítače a disky provádějí. Tyto metriky lze zobrazit vizuálně prostřednictvím Azure Portal nebo je lze načíst prostřednictvím volání rozhraní API. Metriky se počítají v intervalu jedné minuty. K dispozici jsou následující metriky, které vám pomohou získat přehled o výkonu virtuálních počítačů a disku a propustnosti:
- **Hloubka fronty disku s operačním systémem** – Počet aktuálních nezpracovaných vstupně-výstupních požadavků, které čekají na čtení nebo zapisování na disk s operačním systémem.
- **Bajty přečtené z disku s operačním systémem/s** – počet bajtů čtených za sekundu z disku s operačním systémem.
- **Operace čtení disku s operačním systémem/s** – počet vstupních operací, které se z disku s operačním systémem čtou za sekundu.
- **Bajty zápisu na disk s operačním systémem/s** – počet bajtů zapsaných za sekundu z disku s operačním systémem.
- **Operace zápisu na disk s operačním systémem za sekundu** – počet výstupních operací zapsaných za sekundu z disku s operačním systémem.
- **Hloubka fronty datových disků** – Počet aktuálních nezpracovaných vstupně-výstupních požadavků, které čekají na čtení nebo zapisování na datové disky.
- **Bajty čtení z datového disku/s** – počet bajtů čtených za sekundu z datových disků.
- **Operace čtení z datového disku za sekundu** – počet vstupních operací, které se za sekundu čtou z datových disků.
- Bajty zapsané na **datový disk/s** – počet bajtů zapsaných za sekundu z datových disků.
- **Operace zápisu na datový disk/s** – počet výstupních operací zapsaných za sekundu z datových disků.
- **Bajty čtení z disku/s** – celkový počet bajtů čtených za sekundu ze všech disků připojených k virtuálnímu počítači.
- **Operace čtení disku/s** – počet vstupních operací, které jsou čteny za sekundu ze všech disků připojených k virtuálnímu počítači.
- **Bajty zápisu na disk/s** – počet bajtů zapsaných v sekundách ze všech disků připojených k virtuálnímu počítači.
- **Operace zápisu na disk/s** – počet výstupních operací zapsaných v sekundách ze všech disků připojených k virtuálnímu počítači.

## <a name="storage-io-utilization-metrics"></a>Metriky využití v/v úložiště
Metriky, které vám pomůžou diagnostikovat disk v/v capping:
- **Procento využitého počtu vstupně** -výstupních operací vstupně-výstupních operací – procentuální hodnota vypočítaná za vstupně-výstupní operace datového disku dokončena prostřednictvím zřízeného datového pevného disku Pokud je tato hodnota na 100%, bude vaše aplikace spuštěná v/v omezené z limitu IOPS datového disku.
- **Procento využité šířky pásma datového disku** – procento vypočítané propustností datového disku dokončenou při propustnosti zřízeného datového disku. Pokud je tato hodnota na 100%, bude vaše aplikace spuštěná v/v omezené z limitu šířky pásma datového disku.
- **Procento využití IOPS disku s operačním** systémem – procentuální hodnota, kterou vypočítala IOPS disku s operačním systémem, se dokončila v rámci zřízeného IOPS disku operačního systému. Pokud je tato hodnota na 100%, bude vaše aplikace spuštěná v/v omezené z limitu IOPS disku s operačním systémem.
- **Procento využité šířky pásma disku** – procentuální hodnota vypočítaná propustností disku s operačním systémem, která se dokončila v rámci zřízené propustnosti disku s operačním systémem. Pokud je tato hodnota na 100%, bude vaše aplikace spuštěná v/v omezené z limitu šířky pásma disku s operačním systémem.
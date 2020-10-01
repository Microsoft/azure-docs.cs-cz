---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: e5a6dae98e786bf55dc17d8fabe42f84e9927442
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91605902"
---
![Dokumentace k Dsv3](media/vm-disk-performance/dsv3-documentation.jpg)

Maximální propustnost disku, která není v **mezipaměti** , je výchozím limitem úložiště, který může virtuální počítač zpracovat. Maximální propustnost úložiště **v mezipaměti** je samostatný limit, pokud povolíte ukládání hostitele do mezipaměti. Ukládání do mezipaměti hostitele funguje tak, že se úložiště přiblíží k virtuálnímu počítači, který je možné zapsat nebo číst, a to rychle. Velikost úložiště, která je k dispozici pro virtuální počítač pro ukládání do mezipaměti hostitele, je v dokumentaci. Můžete například vidět, že Standard_D8s_v3 se dodává s 200 GiB úložiště mezipaměti.

Povolení ukládání do mezipaměti hostitele můžete provést při vytváření virtuálního počítače a připojení disků. Můžete také upravit a zapnout a vypnout ukládání disků do mezipaměti v existujícím virtuálním počítači.

![Ukládání hostitelů do mezipaměti](media/vm-disk-performance/host-caching.jpg)

Mezipaměť hostitele lze upravit tak, aby odpovídala vašim požadavkům na úlohy pro každý disk. Mezipaměť hostitele můžete nastavit jen pro čtení pro úlohy, které provedou pouze operace čtení a čtení a zápis pro úlohy, které mají rovnováhu na operacích čtení a zápisu. Pokud vaše úloha nedodržuje žádný z těchto vzorů, nedoporučujeme používat ukládání hostitelů do mezipaměti. 

Podíváme se na několik příkladů různých nastavení mezipaměti hostitele a zjistíte, jak to ovlivní tok dat a výkon. V tomto prvním příkladu se podíváme na to, co se stane s požadavky na vstupně-výstupní operace, když je nastavení ukládání do mezipaměti hostitele nastaveno **jen pro čtení**.

Nastavení:
- Standard_D8s_v3 
    - IOPS v mezipaměti: 16 000
    - Neuložené IOPS s mezipamětí: 12 800
- Datový disk P30 
    - IOPS: 5 000
    - **Ukládání hostitele do mezipaměti: jen pro čtení** 

Když je proveden čtení a v mezipaměti jsou k dispozici požadovaná data, mezipaměť vrátí požadovaná data a není nutné číst z disku. Tato čtení se započítávají do limitů uložených v mezipaměti virtuálního počítače.

![Čtení přístupů do mezipaměti hostitele čtení](media/vm-disk-performance/host-caching-read-hit.jpg)

Když je proveden čtení a požadovaná data **nejsou** v mezipaměti k dispozici, požadavek na čtení se pak přesměruje na disk, který je pak nasměruje do mezipaměti i do virtuálního počítače. Tato čtení se započítávají do limitu neuloženého do mezipaměti virtuálního počítače a limitu pro ukládání do mezipaměti virtuálního počítače.

![Neúspěšné čtení z mezipaměti hostitele čtení](media/vm-disk-performance/host-caching-read-miss.jpg)

Po provedení zápisu musí být zápis zapisován do mezipaměti i do disku před tím, než je považován za úplný. Tento zápis se započítává do limitu neuloženého do mezipaměti virtuálního počítače a limitu pro ukládání do mezipaměti virtuálního počítače.

![Čtení zápisu do mezipaměti hostitele](media/vm-disk-performance/host-caching-write.jpg)

V následujícím příkladu se podíváme na to, co se stane s požadavky na vstupně-výstupní operace, když je nastavení mezipaměti hostitele nastaveno na **čtení/zápis**.

Nastavení:
- Standard_D8s_v3 
    - IOPS v mezipaměti: 16 000
    - Neuložené IOPS s mezipamětí: 12 800
- Datový disk P30 
    - IOPS: 5 000
    - **Mezipaměť hostitele: čtení i zápis** 

Čtení jsou zpracovávány stejným způsobem jako jen pro čtení a zápisy jsou pouze ty, které se liší v ukládání do mezipaměti pro čtení a zápis. Při psaní nastavení ukládání do mezipaměti hostitele pro čtení/zápis je nutné zapsat pouze zápis do mezipaměti hostitele, aby bylo možné považovat za kompletní. Zápis pak laxně vytvářená zapsaný na disk jako proces na pozadí. To znamená, že zápisy se budou započítávat do mezipaměti v mezipaměti, když se zapisují do mezipaměti a když se laxně vytvářená zapisuje na disk, který se bude nacházet v neuloženém vstupu do mezipaměti.

![Zápis do mezipaměti pro čtení a zápis hostitele](media/vm-disk-performance/host-caching-read-write.jpg)

Pojďme pokračovat s příkladem pro náš Standard_D8s_v3 virtuální počítač. S výjimkou této doby povolíme ukládání hostitelů do mezipaměti pro disky a limit IOPS virtuálního počítače je 16 000 IOPS. Připojeno k virtuálnímu počítači jsou tři podkladové disky P30, které mohou zpracovávat 5 000 IOPS.

Nastavení:
- Standard_D8s_v3 
    - IOPS v mezipaměti: 16 000
    - Neuložené IOPS s mezipamětí: 12 800
- Disk s operačním systémem P30 
    - IOPS: 5 000
    - Mezipaměť hostitele: čtení i zápis 
- 2 datové disky P30
    - IOPS: 5 000
    - Mezipaměť hostitele: čtení i zápis

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
    - Mezipaměť hostitele: čtení i zápis
- 2 datové disky P30 × 2
    - IOPS: 5 000
    - Mezipaměť hostitele: čtení i zápis
- 2 datové disky P30 × 2
    - IOPS: 5 000
    - Ukládání hostitele do mezipaměti: zakázáno

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

Metriky, které vám pomůžou diagnostikovat virtuální počítač v/v capping:
- **Procento využitých vstupně** -výstupních operací IOPS v mezipaměti – procentuální hodnota vypočtená celkovým IOPS dokončena přes maximální limit počtu IOPS virtuálního počítače v mezipaměti. Pokud je tato hodnota na 100%, bude vaše aplikace spuštěná v/v omezené z limitu IOPS v mezipaměti virtuálního počítače.
- **Procento spotřebované šířky pásma v mezipaměti pro virtuální počítače** – procento vypočítané celkovou propustností disku dokončenou v rámci maximální propustnosti virtuálního počítače v mezipaměti. Pokud je tato hodnota na 100%, bude vaše aplikace spuštěná v/v omezené z limitu šířky pásma v mezipaměti virtuálního počítače.
- **Procento využitých vstupně** -výstupních operací IOPS v mezipaměti – procentuální hodnota vypočítaná celkovým počtem IOPS na virtuálním počítači, která se dokončila v maximálním limitu počtu IOPS virtuálního počítače, který nemá mezipaměť Pokud je tato hodnota na 100%, bude vaše aplikace spuštěná v/v omezené z limitu IOPS bez mezipaměti virtuálního počítače.
- **Procento využité šířky pásma neuložené v mezipaměti** – procentuální hodnota vypočítaná z celkové propustnosti disku na virtuálním počítači, která se dokončila přes maximální propustnost zajištěných virtuálních počítačů. Pokud je tato hodnota na 100%, bude vaše aplikace spuštěná v/v omezené z limitu šířky pásma bez mezipaměti virtuálního počítače.


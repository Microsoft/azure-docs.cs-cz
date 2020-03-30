---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/29/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 871a3edf70690a09d3747703e8bc999dfcce967c
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385176"
---
Pro prémiové Disky SSD je podporováno prasknutí disku. Roztržení je podporováno na všech prémiových ssd diskech velikosti <= 512 GiB (P20 nebo nižší). Tyto velikosti disků podporují prasknutí na základě nejlepšíúsilí a využít kreditní systém pro správu prasknutí. Kredity se hromadí v intervalu shluků vždy, když je provoz na disku nižší než zřízený cíl výkonu pro jejich velikost disku, a spotřebovávají kredity, když provoz přejde za cíl. Provoz disku je sledován proti iOPS a šířku pásma v zřízeném cíli. Roztržení disku neobejde omezení velikosti virtuálního počítače (VM) na viops nebo propustnost.

Shlukování disku je ve výchozím nastavení povoleno u nových nasazení velikostí disků, které jej podporují. Existující velikosti disků, pokud podporují stržení disku, mohou povolit prasknutí některou z následujících metod:

- Odpojte a znovu připojte disk.
- Zastavte a spusťte virtuální hod.

## <a name="burst-states"></a>Zaskakné stavy

Všechny velikosti burst příslušné disk začne s plnou shluk kreditní kbelík, když je disk připojen k virtuálnímu počítači. Maximální doba roztržení je určena velikostí lopaty kreditu burst. Nevyužité kredity můžete hromadit pouze do velikosti kreditu. V libovolném okamžiku může být váš disk praskla kreditní kbelík v jednom z následujících tří stavů: 

- Nabíhání, když provoz disku používá méně než zřízený cíl výkonu. Můžete akumulovat kredit, pokud je provoz na disku mimo cíle viops nebo šířky pásma nebo obojí. Stále můžete hromadit kredity IO, když spotřebováváte plnou šířku pásma disku, naopak.  

- Klesající, když provoz disku používá více než zřízený cíl výkonu. Provoz s hlukem bude nezávisle využívat kredity z viporu nebo šířky pásma. 

- Zbývající konstantní, když provoz disku je přesně na zřízené cíle výkonu. 

Velikosti disků, které poskytují podporu shlukování spolu se specifikacemi shluků, jsou shrnuty v následující tabulce.

## <a name="regional-availability"></a>Regionální dostupnost

Roztržení disku je k dispozici ve všech oblastech veřejného cloudu.

## <a name="disk-sizes"></a>Velikosti disků

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>Ukázkové scénáře

Chcete-li získat lepší představu o tom, jak to funguje, zde je několik příkladů scénářů:

- Jeden běžný scénář, který může těžit z roztržení disku je rychlejší spuštění virtuálního počítače a spuštění aplikace na discích operačního systému. Vezměte virtuální počítač s Linuxem s 8 GiB OS image jako příklad. Pokud používáme disk P2 jako disk operačního systému, zřízený cíl je 120 VOPS a 25 MB/s. Při spuštění virtuálního počítače bude na disku s osovým systémem načítat spouštěcí soubory špička pro čtení. Se zavedením prasknutí můžete číst při maximální rychlosti roztržení 3500 IOPS a 170 MB/s, což urychluje dobu načítání nejméně o 6x. Po spuštění virtuálního počítače je úroveň provozu na disku operačního systému obvykle nízká, protože většina datových operací aplikace bude proti připojeným datovým diskům. Pokud je provoz pod zřízeným cílem, budete hromadit kredity.

- Pokud hostujete prostředí vzdálené virtuální plochy, kdykoli aktivní uživatel spustí aplikaci, jako je AutoCAD, výrazně se zvýší provoz na disk operačního systému. V takovém případě bude shlukový provoz spotřebovávat nahromaděné kredity, což vám umožní jít nad rámec zřízeného cíle a spuštění aplikace mnohem rychleji.

- Disk P1 má zřízený cíl 120 VOPS a 25 MB/s. Pokud skutečný provoz na disku byl 100 IOPS a 20 MB v posledních 1 sekundový interval, pak nevyužité 20 IOs a 5 MB jsou připsány na burst kbelík disku. Kredity v bloku roztržení lze později použít, když provoz překročí zřízený cíl, a to až do maximálního limitu roztržení. Maximální limit shluků definuje strop provozu na disku, i když máte praskla kredity konzumovat z. V tomto případě i v případě, že máte 10 000 iOs v kontejneru kreditu, disk P1 nemůže vydat více než maximální shluk 3 500 IO za sekundu.  

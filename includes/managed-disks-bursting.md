---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7564d40aa9344288c0368818b0b6501dc22a5a27
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705530"
---
Pro prémiové SSD je aktuálně funkce pro vystavování disku ve verzi Preview. Shluking se podporuje u všech velikostí disků SSD úrovně Premium < = 512 GiB (P20 nebo níže). Tyto velikosti disků podporují rozpínání na nejlepší úsilí a využívají systém kreditů ke správě shlukování. Kredity se sčítají v rámci shlukového přenosu vždy, když je přenos disku pod zřízeným cílem výkonu pro velikost disku, a využití kreditů při nárůstu provozu nad rámec cíle. Provoz na disku se sleduje proti vstupně-výstupních operací i šířce pásma v zřízeném cíli.

Při nových nasazení velikosti disků, která ho podporují, je rozšíření disku ve výchozím nastavení povolené. Stávající velikosti disků, pokud podporují shlukování disku, mohou umožňovat roztržení pomocí jedné z následujících metod:

- Odpojte disk a znovu ho připojte.
- Zastavte a spusťte virtuální počítač.

## <a name="burst-states"></a>Stavy shlukování

Pokud je disk připojený k virtuálnímu počítači, zahájí se všechny velikosti virtuálních počítačů, které jsou k dispozici, s plným kreditem shlukového přenosu. Maximální doba navýšení se určuje podle velikosti intervalu shlukového kreditu. Můžete nashromáždit jenom nevyužité kredity až do velikosti tohoto platebního intervalu. V jakémkoli okamžiku může být v jednom z následujících tří stavů kredit shlukování disku: 

- Časově rozlišené, když přenos disku používá méně než zřízený cíl výkonu. Kredit můžete navýšit, pokud je přenos disku nad rámec vstupně-výstupních operací nebo cílů šířky pásma nebo obojího. Kredity v/v můžete i nadále nahromadění, když spotřebováváte celou šířku pásma, naopak.  

- Odmítnutí, pokud provoz disku využívá více než zřízený cíl výkonu. Shlukový přenos nezávisle spotřebovává kredity z IOPS nebo šířky pásma. 

- Zbývající konstanta, pokud je přenos disku přesně v zajištěném cíli výkonnosti. 

Velikosti disků, které poskytují podporu pro shlukování spolu s specifikacemi shluku, jsou shrnuty v následující tabulce.

## <a name="regional-availability"></a>Dostupnost podle oblastí

V současné době je shlukování disků dostupné pouze v Středozápadní USA oblasti.

## <a name="disk-sizes"></a>Velikost disků

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>Ukázkové scénáře

Pokud chcete získat lepší představu o tom, jak to funguje, tady je několik příkladů scénářů:

- Jedním z běžných scénářů, které můžou těžit z rozšíření disku, je rychlejší spouštění virtuálních počítačů a spouštění aplikací na discích s operačním systémem. Jako příklad si povezměte virtuální počítač se systémem Linux s 8 GiB image OS. Pokud jako disk s operačním systémem používáme disk P2, zřízený cíl je 120 IOPS a 25 MB/s. Když se virtuální počítač spustí, načte se na disk s operačním systémem načítání spouštěcích souborů špička. Díky zavedení shlukování můžete číst s maximální rychlostí shluku 3500 IOPS a 170 MB/s, což urychluje dobu načítání nejméně 6x. Po spuštění virtuálního počítače je úroveň provozu na disku s operačním systémem obvykle nízká, protože většina datových operací aplikace bude odpovídat připojeným datovým diskům. Pokud je přenos pod zřízeným cílem, shromáždí se kredity.

- Pokud jste hostitelem vzdáleného prostředí virtuálních ploch, kdykoli aktivní uživatel spustí aplikaci, jako je AutoCAD, dojde k výraznému nárůstu provozu na disk s operačním systémem. V takovém případě bude provoz za provozu využívat nahromaděné kredity, což vám umožní přejít nad rámec zřízeného cíle a spustit aplikaci mnohem rychleji.

- Disk P1 má zřízenou cílovou verzi 120 IOPS a 25 MB/s. Pokud by skutečný provoz na disku byl 100 vstupně-výstupních operací a 20 MB/s za uplynulý interval 1 sekund, pak se nevyužité 20 IOs a 5 MB připisují do shlukového intervalu disku. Kredity v intervalu shlukování lze později použít, pokud přenos přesáhne zřízený cíl až do maximálního limitu shlukování. Maximální limit shluku definuje strop provozu na disku, a to i v případě, že máte kredity nárůstu využití. V takovém případě, a to i v případě, že máte v rámci platebního intervalu 10 000 IOs, nemůže disk P1 vydávat více než maximální počet jednotek 3 500 v/v za sekundu.  

---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 95a78df20f5bed07213dfa3cc2c9b35e283f54e7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
Počítač B-series rodiny umožňuje zvolit, které velikost virtuálního počítače poskytuje nezbytné základní úroveň výkonu pro úlohy, možnost burst výkonu procesoru až o 100 % Intel® Broadwell E5-2673 v4 2.3 GHz nebo procesor Intel® Haswell 2.4 GHz E5-2673 v3 virtuální procesory.

Virtuální počítače B-series jsou ideální pro úlohy, které nemají potřebovat úplný výkon procesoru nepřetržitě, jako jsou webové servery, malé databáze a vývoj a testovací prostředí. Tyto úlohy mají obvykle burstable výkonu požadavky. B-series poskytuje možnost zakoupit velikost virtuálního počítače se základní výkon a instance virtuálního počítače vytvoří kredity při je nižší než jeho základní použití. Když virtuální počítač nahromadění platební, můžete virtuální počítač burst nad účaří použití až o 100 % virtuální procesor, když vaše aplikace vyžaduje vyšší výkon procesoru.

B-series se dodává v následujících šesti velikosti virtuálních počítačů:

| Velikost          | pro virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Základní výkonu procesoru virtuálního počítače | Maximální počet výkonu procesoru virtuálního počítače | Kredity bankovních / hodinu | Maximální počet bankovních kredity |
|---------------|--------|-------------|----------------|--------------------------------|---------------------------|-----------------------|--------------------|
| Standard_B1s  | 1      | 1           | 4              | 10 %                            | 100%                      | 6                     | 144                |
| Standard_B1ms | 1      | 2           | 4              | 20 %                            | 100%                      | 12                    | 288                |
| Standard_B2s  | 2      | 4           | 8              | 40%                            | 200%                      | 24                    | 576                |
| Standard_B2ms | 2      | 8           | 16             | 60%                            | 200%                      | 36                    | 864                |
| Standard_B4ms | 4      | 16          | 32             | 90%                            | 400%                      | 54                    | 1296               |
| Standard_B8ms | 8      | 32          | 64             | 135%                           | 800%                      | 81                    | 1944               |




## <a name="q--a"></a>Dotazy a odpovědi 

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>Otázka: Jak získat 135 % standardních hodnot výkonu z virtuálního počítače?
**A**: 135 % je sdílená mezi 8 virtuálních procesorů na který tvoří velikost virtuálního počítače. Například pokud vaše aplikace používá 4 s 8 jádry pracující na dávkové zpracování a každý z těchto 4 virtuální procesory jsou spuštěné v 30 % využití celkový objem výkonu procesoru virtuálního počítače by roven 120 %.  Znamená, že virtuální počítač by vytváření platební čas podle rozdílů 15 % z základní výkon.  Můžete ale také znamená, že pokud máte kredity, které jsou k dispozici, stejného virtuálního počítače můžete použít 100 % všechny 8 virtuálních procesorů je udělení tohoto virtuálního počítače výkonu procesoru maximální 800 %.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>Otázka: jak můžete monitorovat Moje kredit a spotřeba
**A**: jsme představení 2 nové metriky v následujících týdnech **platební** metrika vám umožní zobrazit kolik kredity má bankovních virtuálního počítače a **ConsumedCredit** metrika se zobrazí kolik procesoru kredity z banky spotřebovala virtuálního počítače.    Bude moct zobrazit tyto metriky z podokna metriky na portálu nebo programově pomocí rozhraní API Azure monitorování.

Další informace o tom, jak získat přístup k datům metriky pro Azure najdete v tématu [přehled metriky v Microsoft Azure](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>Otázka: jak počítají kredity?
**A**: sazby akumulace a využití virtuálního počítače jsou nastavené tak, aby virtuálního počítače s úrovni přesně jeho základní výkon bude mít ani net akumulace nebo spotřeby bursting kredity.  Virtuální počítač bude mít zvýšení kredity vždy, když je spuštěna pod úrovní jeho základní výkon a bude mít snížení kredity vždy, když virtuální počítač je využití procesoru více než úroveň jeho základní výkonu.

**Příklad**: nasadit virtuální počítač pomocí velikosti B1ms své malé čas a návštěvnosti aplikaci databáze. Tato velikost umožňuje Moje aplikace pro použití jako můj směrného plánu, který je.2 kredity za minutu, kterou můžete použít nebo bank až 20 % virtuální procesory. 

Moje aplikace je nyní začátek a konec pracovního Moje zaměstnanci dne 7:00-9:00:00 až 4:00 – 18:00:00. Během dalších 20 hodin dne, Moje aplikace je obvykle v nečinnosti, jenom pomocí 10 % virtuální procesory. Pro dobu mimo špičku I vám 0,2 kredity za minutu, ale jenom využívat kredity 0.l za minutu, aby virtuální počítač bude bankovní.1 x 60 = 6 kredity za hodinu.  20 hodin, které jsem mimo špičku I bude bankovní 120 kredity.  

Během hodiny špičky Moje aplikace zobrazí průměr využití virtuálních procesorů 60 %, I přesto vám 0,2 kredity za minutu, ale I využívat 0,6 kredity za minutu, net náklady na.4 kredity minutu nebo.4 x 60 = 24 kredity za hodinu. Mám 4 hodiny denně špičkové využití, tak náklady na něj 4 x 24 = 96 kredity u mého používání ve špičce.

Pokud I trvat 120 kredity, které I vytvořené mimo špičku a odečtena 96 kredity, které lze použít pro moje špičky, I bankovní další 24 kredity za den, který lze použít pro jiné shluky aktivity.


### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>Otázka: B-Series podporuje úložiště Premium datové disky?
**A**: Ano, všech velikostí B-Series podporují Storage úrovně Premium datových disků.   
    


    

    

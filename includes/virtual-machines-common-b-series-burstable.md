---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 06/25/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 6a3e2034792fdc0a4a8fed7885c7d5ad78ea24d9
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67501222"
---
Řadu virtuálních počítačů B-series umožňuje vybrat velikost virtuálního počítače, ve které vám poskytne potřebné základní úroveň výkonu pro úlohy, možnost burst výkon procesoru až 100 % Intel® Broadwell E5-2673 v4 2.3 GHz nebo procesor Intel® Haswell 2.4 GHz E5-2673 v3 virtuální procesor.

Virtuální počítače řady B-series jsou ideální pro úlohy, které nevyžadují plný výkon procesoru nepřetržitě, jako jsou webové servery, testování konceptů, malé databáze a vývoj prostředí sestavení. Tyto úlohy mají obvykle požadavky na výkon zvládáním výkonových špiček. B-series vám poskytne možnost zakoupení velikost virtuálního počítače se základní výkon a instanci virtuálního počítače vytvoří kredity při je menší než její základní použití. Když virtuální počítač se nashromáždil kredit, můžete převést virtuální počítač nad směrný plán pomocí virtuálního procesoru navýšit až na 100 %, když vaše aplikace vyžaduje vyšší výkon.

B-series je k dispozici ve následujících velikostí virtuálních počítačů:

| Size             | Virtuální procesory  | Paměť: GiB | Dočasné úložiště (SSD): GiB | Základní výkon CPU virtuálního počítače | Maximální výkon CPU virtuálního počítače | Počáteční kredity | Uložené kredity za hodinu | Max. uložených kreditů | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5 %                   | 100 %                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10 %                   | 100 %                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20 %                   | 100 %                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40 %                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60 %                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90 %                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202 %                  | 1200 %                   | 360                   | 121                 | 2909           | 16                                     | 6480 / 75                                 | 4320 / 50                                  | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270 %                  | 1600 %                   | 480                   | 162                 | 3888           | 32                                     | 8640 / 100                                 | 4320 / 50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337 %                  | 2000 %                   | 600                   | 203                 | 4860           | 32                                     | 10800 / 125                                 | 4320 / 50                                 | 8  |

<sup>1</sup> B1ls je podporována pouze v Linuxu

## <a name="workload-example"></a>Příklad pracovního vytížení

Vezměte v úvahu některé aplikace office/rezervace. Aplikace potřebuje nárůsty zatížení procesoru během pracovní doby, ale ne velké množství výpočetního výkonu během mimo špičku. V tomto příkladu úloha vyžaduje 16vCPU virtuální počítač s 64GiB paměti RAM k zajištění efektivní práce.

V tabulce jsou uvedeny hodinové údaje o provozu a graf je vizuální znázornění tento přenos.

B16 vlastnosti:

Maximální počet procesorů perf: 16vCPU * 100 % = % 1 600

Standardní hodnoty: 270 %

![Graf každou hodinu údaje o provozu](./media/virtual-machines-common-b-series-burstable/office-workload.png)

| Scénář | Čas | Využití procesoru (%) | Kredity sbírají<sup>1</sup> | Kredity, které jsou k dispozici |
| --- | --- | --- | --- | --- |
| B16ms nasazení | Nasazení | Nasazení  | 480 (Počáteční kredit) | 480 |
| Žádný provoz | 0:00 | 0 | 162 | 642 |
| Žádný provoz | 1:00 | 0 | 162 | 804 |
| Žádný provoz | 2:00 | 0 | 162 | 966 |
| Žádný provoz | 3:00 | 0 | 162 | 1128 |
| Žádný provoz | 4:00 | 0 | 162 | 1290 |
| Žádný provoz | 5:00 | 0 | 162 | 1452 |
| S nízkým provozem | 6:00 | 270 | 0 | 1452 |
| Zaměstnanci přijdou do office (aplikace potřebuje virtuální procesory 80 %) | 7:00 | 1280 | -606 | 846 |
| Zaměstnanci pokračovat přicházející do kanceláře (aplikace potřebuje virtuální procesory 80 %) | 8:00 | 1280 | -606 | 240 |
| S nízkým provozem | 9:00 | 270 | 0 | 240 |
| S nízkým provozem | 10:00 | 100 | 102 | 342 |
| S nízkým provozem | 11:00 | 50 | 132 | 474 |
| S nízkým provozem | 12:00 | 100 | 102 | 576 |
| S nízkým provozem | 13:00 | 100 | 102 | 678 |
| S nízkým provozem | 14:00 | 50 | 132 | 810 |
| S nízkým provozem | 15:00 | 100 | 102 | 912 |
| S nízkým provozem | 16:00 | 100 | 102 | 1014 |
| Zaměstnanci rezervaci (vCPU aplikace potřebám 100 %) | 17:00 | 1600 | -798 | 216 |
| S nízkým provozem | 18:00 | 270 | 0 | 216 |
| S nízkým provozem | 19:00 | 270 | 0 | 216 |
| S nízkým provozem | 20:00 | 50 | 132 | 348 |
| S nízkým provozem | 21:00 | 50 | 132 | 480 |
| Žádný provoz | 22:00 | 0 | 162 | 642 |
| Žádný provoz | 23:00 | 0 | 162 | 804 |

<sup>1</sup> kredity sbírají/kredity použít za hodinu je ekvivalentní: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes`.  

Hodinová sazba pro D16s_v3, který má 16 virtuálních procesorů a 64 GiB paměti je $0.936 za hodinu (měsíc $673.92) a pro B16ms s 16 virtuálních procesorů a 64 GiB paměti, že je míra 0.794 $ za hodinu (měsíc $547.86). <b> Výsledkem je úspora v 15 %!</b>

## <a name="q--a"></a>Dotazy a odpovědi

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>Otázka: Jak získat 135 % standardních hodnot výkonu z virtuálního počítače?
**A**: 135 % se sdílí mezi 8 virtuálních procesorů, které tvoří velikost virtuálního počítače. Například pokud vaše aplikace používá 4, 8 jader, pracovat na dávkové zpracování a každý z těchto 4 virtuální procesory jsou spuštěny na 30 % využití celkové množství výkonu procesoru virtuálního počítače by rovná 120 %.  To znamená, že váš virtuální počítač by vytváření kredit čas podle rozdílů 15 % z základní výkon.  Můžete ale také to znamená, že pokud máte k dispozici, můžete použít stejný virtuální počítač 100 % všech 8 virtuálních procesorů kredity díky tomu mají tento virtuální počítač výkon procesoru maximální 800 %.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>Otázka: Jak můžu monitorovat své platební zůstatek a spotřebu
**A**: Budeme zavádět 2 nové metriky za pár týdnů, **kredit** metriky vám umožní zobrazit, kolik váš virtuální počítač má bankovních kredity a **ConsumedCredit** metrika se zobrazí váš virtuální počítač má kolik kredity CPU spotřebované zruinovalo.    Bude moct zobrazit tyto metriky z podokna metrik na portálu nebo programově pomocí rozhraní API služby Azure Monitor.

Další informace o tom, jak přistupovat k datům metriky pro Azure najdete v tématu [přehled metrik v Microsoft Azure](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>Otázka: Jak se počítají kredity?
**A**: Sazby akumulací a využití virtuálních počítačů jsou nastavené tak, aby virtuální počítač s úrovni přesně jeho základní výkon bude mít net akumulací ani spotřebu shlukování kredity.  Virtuální počítač bude mít net nárůst kredity pokaždé, když je spuštěn pod úrovní jeho základní výkon a bude mít snížení kredity pokaždé, když virtuální počítač využívá procesor více než její základní výkon úrovně.

**Příklad**:  Mohu nasadit virtuální počítač s použitím velikosti B1ms pro moje čas (krátkodobé používání) a účast databázové aplikace. Tato velikost umožňuje aplikaci používat jako Moje směrného plánu, který je 0,2 kredity za minutu, které můžete použít nebo banka až 20 % virtuální procesor. 

Moje aplikace je zaneprázdněna na začátek a konec pracovní Moje zaměstnanci dne 7:00-9:00:00 až 4:00 – 18:00:00. Při dalších 20 hodin dne, aplikace obvykle je v nečinnosti, jenom pomocí 10 % virtuálního procesoru navýšit. Hodiny mimo špičku, můžu získat kredity 0,2 požadavku za minutu, ale jenom využívat 0.l kredity za minutu, takže virtuální počítač bude bankovní 0,1 x 60 = 6 kredity za hodinu.  20 hodin, které jsem mimo špičku můžu se banky 120 kredity.  

Během špičky aplikaci průměrné využití virtuálních procesorů 60 %, můžu stále získejte kredity 0,2 požadavku za minutu, ale I využívat 0,6 kredity za minutu, s net náklady 0.4 kredity minutu nebo 0.4 x 60 = 24 kredity za hodinu. Mám 4 hodiny denně využití ve špičce, takže to stojí 4 x 24 = 96 kredity pro využití ve špičce.

Pokud trvat 120 kredity, které jsem získal mimo špičku a odečítání 96 kredity, použita pro moje špičky, I bankovních dalších 24 kredity za den, který lze použít pro jiné nárůstům aktivity.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>Otázka: Výpočet kredity shromážděných a použít
**A**: Můžete použít následující vzorec: 

(Základní výkonu procesoru virtuálního počítače – využití procesoru) / 100 = bank kredity nebo použít za minutu

např. ve výše instance směrný plán je 20 %, a pokud použijete 10 % využití CPU se shromažďování (20-10 %) / 100 = 0,1 kredit ve výši za minutu.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>Otázka: B-Series podporuje Storage úrovně Premium datové disky?
**A**: Ano, všech velikostí řady B-Series podporuje datové disky Premium Storage.   
    
### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>Otázka: Proč svůj zbývající kredit nastavena na hodnotu 0 opětovné nasazení nebo zastavit nebo spustit?
**A** : Když je virtuální počítač "REDPLOYED" a virtuální počítač přesune do jiného uzlu, nahromaděné kredit bude ztracena. Pokud virtuální počítač je spuštěna nebo zastavena, ale zůstává na stejném uzlu, virtuální počítač zachová nahromaděné kredit. Pokaždé, když se virtuální počítač spustí novou na uzlu, získá Počáteční kredit, Standard_B8ms je 240 minut.
    
### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>Otázka: Co se stane, když nasadím nepodporované image operačního systému na B1ls?
**A** : B1ls podporuje pouze Linuxové Image a Pokud nasazujete cokoli jiného image operačního systému, nemusí se získat nejlepší prostředí pro zákazníky.

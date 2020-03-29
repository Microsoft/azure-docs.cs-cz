---
title: Roztržení řady B – virtuální počítače Azure
description: Popisuje řady B burstable velikosti virtuálních počítačových virtuálních počítače Azure.
services: virtual-machines
author: ayshakeen
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: ayshak
ms.openlocfilehash: 5121ef8eb3123d830233e91968c69b331d255bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161076"
---
# <a name="b-series-burstable-virtual-machine-sizes"></a>Velikosti virtuálních strojů řady B

Virtuální počítače řady B jsou ideální pro úlohy, které nepotřebují plný výkon procesoru nepřetržitě, jako jsou webové servery, doklady o konceptech, malé databáze a vývojová prostředí sestavení. Tyto úlohy mají obvykle sknutelné požadavky na výkon. Řada B poskytuje možnost zakoupit velikost virtuálního počítače s výkonem podle směrného plánu a instance virtuálního počítače vytváří kredity, když používá méně než jeho směrný plán. Když virtuální počítač nahromaděný kredit, virtuální počítač může prasknout nad směrný plán pomocí až 100 % virtuálního procesoru, když vaše aplikace vyžaduje vyšší výkon procesoru.

Řada B se dodává v následujících velikostech virtuálních počítače:

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Není podporováno

Migrace za provozu: Podporováno

Aktualizace pro zachování paměti: Podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Základní procesor Perf virtuálního počítače | Maximální cpu Perf virtuálního počítače | Počáteční kredity | Uklápěné kredity za hodinu | Maximální bankovní úvěry | Max. datových disků | Maximální propustnost úložiště v mezipaměti a dočasná propustnost úložiště: IOPS/MBps | Maximální propustnost disku bez mezipaměti: IOPS/MBps | Maximální počet síťových karet |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Standard_B1ls<sup>1</sup> | 1  | 0,5 | 4   | 5 %   | 100 %  | 30  | 3   | 72   | 2  | 200/10    | 160/10    | 2  |
| Standard_B1s              | 1  | 1   | 4   | 10 %  | 100 %  | 30  | 6   | 144  | 2  | 400/10    | 320/10    | 2  |
| Standard_B1ms             | 1  | 2   | 4   | 20 %  | 100 %  | 30  | 12  | 288  | 2  | 800/10    | 640/10    | 2  |
| Standard_B2s              | 2  | 4   | 8   | 40 %  | 200 %  | 60  | 24  | 576  | 4  | 1600/15   | 1280/15   | 3  |
| Standard_B2ms             | 2  | 8   | 16  | 60 %  | 200 %  | 60  | 36  | 864  | 4  | 2400/22.5 | 1920/22.5 | 3  |
| Standard_B4ms             | 4  | 16  | 32  | 90 %  | 400%  | 120 | 54  | 1296 | 8  | 3600/35   | 2880/35   | 4  |
| Standard_B8ms             | 8  | 32  | 64  | 135% | 800%  | 240 | 81  | 1944 | 16 | 4320/50   | 4320/50   | 4  |
| Standard_B12ms            | 12 | 48  | 96  | 202% | 1200% | 360 | 121 | 2909 | 16 | 6480/75   | 4320/50   | 6  |
| Standard_B16ms            | 16 | 64  | 128 | 270% | 1600% | 480 | 162 | 3888 | 32 | 8640/100  | 4320/50   | 8  |
| Standard_B20ms            | 20 | 80  | 160 | 337% | 2 000 % | 600 | 203 | 4860 | 32 | 10800/125 | 4320/50   | 8  |

<sup>1</sup> B1ls je podporován pouze na Linuxu

## <a name="workload-example"></a>Příklad pracovnízátěže

Zvažte aplikaci pro vrácení se změnami v kanceláři. Aplikace potřebuje shluky procesoru během pracovní doby, ale ne mnoho výpočetního výkonu během mimo pracovní dobu. V tomto příkladu zatížení vyžaduje 16vCPU virtuální počítač s 64GiB paměti RAM pracovat efektivně.

V tabulce jsou uvedeny hodinové údaje o provozu a graf představuje vizuální znázornění tohoto provozu.

B16 charakteristiky:

Maximální výkon procesoru: 16vCPU * 100% = 1600%

Výchozí hodnota: 270 %

![Graf hodinových provozních dat](./media/b-series-burstable/office-workload.png)

| Scénář | Time | Využití procesoru (%) | Nahromaděné kredity<sup>1</sup> | Dostupné kredity |
| --- | --- | --- | --- | --- |
| Nasazení B16ms | Nasazení | Nasazení  | 480 (počáteční kredity) | 480 |
| Žádný provoz | 0:00 | 0 | 162 | 642 |
| Žádný provoz | 1:00 | 0 | 162 | 804 |
| Žádný provoz | 2:00 | 0 | 162 | 966 |
| Žádný provoz | 3:00 | 0 | 162 | 1128 |
| Žádný provoz | 4:00 | 0 | 162 | 1290 |
| Žádný provoz | 5:00 | 0 | 162 | 1452 |
| Nízký provoz | 6:00 | 270 | 0 | 1452 |
| Zaměstnanci přicházejí do kanceláře (aplikace potřebuje 80% vCPU) | 7:00 | 1280 | -606 | 846 |
| Zaměstnanci i nadále přicházejí do kanceláře (aplikace potřebuje 80% vCPU) | 8:00 | 1280 | -606 | 240 |
| Nízký provoz | 9:00 | 270 | 0 | 240 |
| Nízký provoz | 10:00 | 100 | 102 | 342 |
| Nízký provoz | 11:00 | 50 | 132 | 474 |
| Nízký provoz | 12:00 | 100 | 102 | 576 |
| Nízký provoz | 13:00 | 100 | 102 | 678 |
| Nízký provoz | 14:00 | 50 | 132 | 810 |
| Nízký provoz | 15:00 | 100 | 102 | 912 |
| Nízký provoz | 16:00 | 100 | 102 | 1014 |
| Zaměstnanci odhlášení (aplikace potřebuje 100% vCPU) | 17:00 | 1600 | -798 | 216 |
| Nízký provoz | 18:00 | 270 | 0 | 216 |
| Nízký provoz | 19:00 | 270 | 0 | 216 |
| Nízký provoz | 20:00 | 50 | 132 | 348 |
| Nízký provoz | 21:00 | 50 | 132 | 480 |
| Žádný provoz | 22:00 | 0 | 162 | 642 |
| Žádný provoz | 23:00 | 0 | 162 | 804 |

<sup>1</sup> Nasbíraných kreditů/kreditů použitých za hodinu odpovídá: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes`.  

Pro D16s_v3, která má 16 virtuálních procesorů a 64 GiB paměti, je hodinová sazba $0.936 za hodinu (měsíční $673.92) a pro B16ms s 16 virtuálními procesory a 64 GiB paměti je sazba $0.794 za hodinu (měsíční $547.86). <b>To má za následek 15% úspory!</b>

## <a name="q--a"></a>Otázky a odpovědi

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>Otázka: Jak získat 135 % základní výkon z virtuálního virtuálního virtuálního mě?

**A:** 135 % je sdíleno mezi 8 virtuálních procesorů, které tvoří velikost virtuálního počítače. Například pokud vaše aplikace používá 4 z 8 jader pracujících na dávkové zpracování a každý z těchto 4 virtuálních procesorů běží na 30 % využití celkové množství výkonu procesoru virtuálního počítače by se rovnalo 120 %.  To znamená, že váš virtuální počítač bude vytvářet čas kreditu na základě 15% rozdílu od výkonu podle směrného plánu.  Ale také to znamená, že když máte k dispozici kredity, že stejný virtuální počítače můžete použít 100 % všech 8 virtuálních procesorů, které dávají tomuto virtuálnímu virtuálnímu počítače maximální výkon procesoru 800 %.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>Otázka: Jak mohu sledovat zůstatek a spotřebu kreditu

**A:** Budeme zavedení 2 nové metriky v nadcházejících týdnech, **kreditní** metrika vám umožní zobrazit, kolik kreditů váš virtuální počítač uložila a **ConsumedCredit** metrika ukáže, kolik kreditů CPU váš virtuální počítač spotřebovává z banky.    Tyto metriky budete moct zobrazit z podokna metrik na portálu nebo programově prostřednictvím api azure monitoru.

Další informace o tom, jak získat přístup k datům metrik pro Azure, najdete [v tématu Přehled metrik v Microsoft Azure](../azure-monitor/platform/data-platform.md).

### <a name="q-how-are-credits-accumulated"></a>Otázka: Jak se kredity akumulují?

**A**: Akumulace virtuálních her a spotřeba sazby jsou nastaveny tak, že virtuální ms běží přesně na jeho základní úroveň výkonu nebude mít ani čisté akumulace nebo spotřeby roztržení kreditů.  Virtuální počítače bude mít čisté zvýšení kreditů vždy, když běží pod jeho základní úroveň výkonu a bude mít čistý pokles kreditů vždy, když virtuální počítače využívá procesor u vyšší než jeho základní úroveň výkonu.

**Příklad**: Nasadím virtuální ho s využitím velikosti B1ms pro svou databázovou aplikaci s malým časem a docházkou. Tato velikost umožňuje mé žádosti používat až 20% virtuálního procesoru jako můj směrný plán, což je 0,2 kreditů za minutu, kterou mohu použít nebo banku.

Moje aplikace je zaneprázdněna na začátku a na konci pracovního dne mých zaměstnanců, mezi 7:00-9:00 a 4:00 - 18:00. Během dalších 20 hodin dne je moje aplikace obvykle nečinná, pouze pomocí 10 % virtuálního procesoru. V době mimo špičku získávám 0,2 kreditů za minutu, ale spotřebovávají pouze 0,l kreditů za minutu, takže můj virtuální virtuální virtuální ms bude bankovat 0,1 x 60 = 6 kreditů za hodinu.  Za 20 hodin, kdy jsem mimo špičku, si dám 120 kreditů.  

Během špičky moje aplikace v průměru 60% využití virtuálních procesorů, stále jsem vydělávat 0,2 kreditů za minutu, ale sním 0,6 kreditů za minutu, za čisté náklady na 0,4 kreditů za minutu nebo 0,4 x 60 = 24 kreditů za hodinu. Mám 4 hodiny za den špičkového využití, takže to stojí 4 x 24 = 96 kreditů za mé špičkové využití.

Pokud si vezmu 120 kreditů, které jsem získal mimo špičku, a odečtou 96 kreditů, které jsem použil pro své špičky, mohu využít dalších 24 kreditů za den, které mohu použít pro další výbuchy aktivity.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>Otázka: Jak mohu vypočítat nahromaděné a použité kredity?

**A**: Můžete použít následující vzorec:

(Základní cpu perf virtuálního počítače - využití procesoru) / 100 = Kredity banky nebo použití za minutu

například ve výše uvedeném případě je váš směrný plán 20 % a pokud používáte 10 % procesoru, který hromadíte (20%-10%)/100 = 0,1 kreditu za minutu.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>Otázka: Podporuje b-series datové disky úložiště Premium?

**A:** Ano, všechny velikosti řady B podporují datové disky úložiště Premium.

### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>Otázka: Proč je můj zbývající kredit nastaven na 0 po opětovném nasazení nebo zastavení/spuštění?

**A** : Když je virtuální virtuální město "REDPLOYED" a virtuální ho přesunout do jiného uzlu, nahromaděný kredit je ztracena. Pokud je virtuální počítač zastavený nebo spuštěný, ale zůstane ve stejném uzlu, zachová se nahromaděný kredit. Kdykoli virtuální počítač začne znovu na uzlu, získá počáteční kredit, pro Standard_B8ms je to 240 minut.

### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>Otázka: Co se stane, když nasadím nepodporovanou bitovou kopii operačního systému na B1ls?

**A** : B1ls podporuje pouze linuxové image a pokud nasadíte jinou bitovou kopii operačního systému, nemusíte získat nejlepší zákaznickou zkušenost.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.
---
title: Shluky B-Series – Azure Virtual Machines
description: Popisuje B-série velikostí virtuálních počítačů Azure
services: virtual-machines
ms.subservice: sizes
author: styli365
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: sttsinar
ms.openlocfilehash: dc6706d4ec9090c59d4dd668d2ae1dd3ce7d188a
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928038"
---
# <a name="b-series-burstable-virtual-machine-sizes"></a>Velikosti virtuálních počítačů řady B-Series

Virtuální počítače řady B-Series jsou ideální pro úlohy, které nepotřebují průběžný výkon procesoru, jako jsou webové servery, prokázání konceptů, malých databází a prostředí pro vývoj sestavení. Tyto úlohy mají typicky požadavky na výkon při roztržení. Řada B-Series nabízí možnost zakoupit si velikost virtuálního počítače s využitím základního výkonu, který může vytvořit kredity, pokud používá méně než svůj směrný plán. V případě, že virtuální počítač má nahromaděné kredity, může virtuální počítač nad rámec standardních hodnot zvýšit úroveň pomocí až 100% vCPU, pokud vaše aplikace vyžaduje vyšší výkon procesoru.

Řada B-obsahuje následující velikosti virtuálních počítačů:

[Výpočetní jednotka Azure (ACU)](./acu.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json): liší se *<br>
[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): nepodporováno<br>
[Migrace za provozu](maintenance-and-updates.md): podporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): podporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
<br>
* Virtuální počítače řady B-Series jsou nenáročné, takže ACU čísla se budou lišit v závislosti na úlohách a základním využití.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Základní výkon procesoru virtuálního počítače | Maximální výkon procesoru virtuálního počítače | Počáteční kredity | Kredity v bance za hodinu | Maximální počet bank kreditů | Max. datových disků | Maximální propustnost úložiště v mezipaměti a dočasné úložiště: IOPS/MB/s | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Standard_B1ls<sup>1</sup> | 1  | 0,5 | 4   | 5 %   | 100 %  | 30  | 3   | 72   | 2  | 200/10    | 160/10    | 2  |
| Standard_B1s              | 1  | 1   | 4   | 10 %  | 100 %  | 30  | 6   | 144  | 2  | 400/10    | 320/10    | 2  |
| Standard_B1ms             | 1  | 2   | 4   | 20 %  | 100 %  | 30  | 12  | 288  | 2  | 800/10    | 640/10    | 2  |
| Standard_B2s              | 2  | 4   | 8   | 40 %  | 200 %  | 60  | 24  | 576  | 4  | 1600/15   | 1280/15   | 3  |
| Standard_B2ms             | 2  | 8   | 16  | 60 %  | 200 %  | 60  | 36  | 864  | 4  | 2400/22.5 | 1920 – 22.5 | 3  |
| Standard_B4ms             | 4  | 16  | 32  | 90 %  | 400%  | 120 | 54  | 1296 | 8  | 3600/35   | 2880/35   | 4  |
| Standard_B8ms             | 8  | 32  | 64  | 135% | 800%  | 240 | 81  | 1944 | 16 | 4320/50   | 4320/50   | 4  |
| Standard_B12ms            | 12 | 48  | 96  | 202% | 1200% | 360 | 121 | 2909 | 16 | 6480/75   | 4320/50   | 6  |
| Standard_B16ms            | 16 | 64  | 128 | 270% | 1600% | 480 | 162 | 3888 | 32 | 8640/100  | 4320/50   | 8  |
| Standard_B20ms            | 20 | 80  | 160 | 337% | 2 000 % | 600 | 203 | 4860 | 32 | 10800/125 | 4320/50   | 8  |

<sup>1</sup> B1ls se podporuje jenom na Linux.

## <a name="workload-example"></a>Příklad úlohy

Vezměte v úvahu aplikaci Office pro vrácení se změnami/výstup. Aplikace potřebuje v pracovní době nárůst počtu PROCESORů, ale ne spoustu výpočetní síly v době mimo špičku. V tomto příkladu zatížení vyžaduje 16vCPU virtuální počítač s 64GiB paměti RAM, aby bylo možné efektivně pracovat.

Tabulka zobrazuje data o hodinách přenosů a graf je vizuální reprezentace tohoto provozu.

Charakteristiky B16:

Maximální výkon procesoru: 16vCPU × 100% = 1600%

Směrný plán: 270%

![Graf dat hodinových přenosů](./media/b-series-burstable/office-workload.png)

| Scénář | Čas | Využití CPU (%) | Shromážděné kredity<sup>1</sup> | Dostupné kredity |
| --- | --- | --- | --- | --- |
| Nasazení B16ms | Nasazení | Nasazení  | 480 (počáteční kredity) | 480 |
| Žádný provoz | 0:00 | 0 | 162 | 642 |
| Žádný provoz | 1:00 | 0 | 162 | 804 |
| Žádný provoz | 2:00 | 0 | 162 | 966 |
| Žádný provoz | 3:00 | 0 | 162 | 1128 |
| Žádný provoz | 4:00 | 0 | 162 | 1290 |
| Žádný provoz | 5:00 | 0 | 162 | 1452 |
| Nízká přenosová data | 6:00 | 270 | 0 | 1452 |
| Zaměstnanci přicházejí do Office (aplikace potřebuje 80% vCPU). | 7:00 | 1280 | -606 | 846 |
| Zaměstnanci pokračují v kanceláři (potřebuje aplikace 80% vCPU). | 8:00 | 1280 | -606 | 240 |
| Nízká přenosová data | 9:00 | 270 | 0 | 240 |
| Nízká přenosová data | 10:00 | 100 | 102 | 342 |
| Nízká přenosová data | 11:00 | 50 | 132 | 474 |
| Nízká přenosová data | 12:00 | 100 | 102 | 576 |
| Nízká přenosová data | 13:00 | 100 | 102 | 678 |
| Nízká přenosová data | 14:00 | 50 | 132 | 810 |
| Nízká přenosová data | 15:00 | 100 | 102 | 912 |
| Nízká přenosová data | 16:00 | 100 | 102 | 1014 |
| Zaměstnanci, kteří si rezervovali (požadavky aplikace 100% vCPU) | 17:00 | 1600 | -798 | 216 |
| Nízká přenosová data | 18:00 | 270 | 0 | 216 |
| Nízká přenosová data | 19:00 | 270 | 0 | 216 |
| Nízká přenosová data | 20:00 | 50 | 132 | 348 |
| Nízká přenosová data | 21:00 | 50 | 132 | 480 |
| Žádný provoz | 22:00 | 0 | 162 | 642 |
| Žádný provoz | 23:00 | 0 | 162 | 804 |

<sup>1</sup> kumulovaná kredity/kredity využité za hodinu jsou ekvivalentem: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes` .  

V případě D16s_v3, který má 16 vCPU a 64 GiB paměti, je hodinová sazba $0,936 za hodinu (měsíční $673,92) a pro B16ms s 16 vCPU a 64 GiB paměti je sazba $0,794 za hodinu (měsíční $547,86). <b> Výsledkem je 15% úspora!</b>

## <a name="q--a"></a>Otázky a odpovědi

### <a name="q-what-happens-when-my-credits-run-out"></a>Otázka: co se stane, když kredit skončí?
Odpověď **: po** vyčerpání kreditů se virtuální počítač vrátí k výkonu směrného plánu.

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>Otázka: jak získáte 135% výkonnost směrného plánu z virtuálního počítače?

Odpověď **: 135** % se sdílí mezi 8 vCPU a tvoří velikost virtuálního počítače. Například pokud vaše aplikace používá 4 až 8 jader pracujících na dávkovém zpracování a každý z těchto 4 vCPU běží na 30% využití, celková velikost výkonu procesoru virtuálního počítače by byla rovna 120%.  To znamená, že váš virtuální počítač bude sestavovat kredit na základě 15% rozdílu od vašeho směrného výkonu.  Ale také to znamená, že pokud máte k dispozici kredity, může stejný virtuální počítač použít 100% ze všech 8 vCPUů, takže virtuální počítač má maximální výkon procesoru 800%.

### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>Otázka: Jak můžu monitorovat zůstatek a spotřebu svého kreditu?

**A** Odpověď: metrika **kreditu** vám umožní zobrazit, kolik KREDITů byl váš virtuální počítač v bance, a metrika **CONSUMEDCREDIT** zobrazí počet KREDITů procesoru, které váš virtuální počítač využil od banky.    Tyto metriky budete moct zobrazit z podokna metrik na portálu nebo programově prostřednictvím rozhraní Azure Monitor API.

Další informace o tom, jak získat přístup k datům metrik pro Azure, najdete v tématu [Přehled metrik v Microsoft Azure](../azure-monitor/platform/data-platform.md).

### <a name="q-how-are-credits-accumulated-and-consumed"></a>Otázka: jak se shromažďují a spotřebovávají kredity?

Odpověď: míra akumulace a spotřeby virtuálních počítačů je nastavená **tak,** že virtuální počítač, na kterém běží přesně jeho základní úroveň výkonu, nebude ani kumulace ani spotřeba kreditů.  Virtuální počítač bude mít čisté zvýšení kreditů vždy, když je spuštěný pod základní úrovní výkonu a bude mít čistý pokles v kreditech, kdykoli virtuální počítač využívá procesor větší, než je základní úroveň výkonu.

**Příklad** : nasadím virtuální počítač s využitím B1msé velikosti pro moji malou dobu a aplikaci pro databázi docházky. Tato velikost umožňuje, aby moje aplikace používala až 20% vCPU jako můj směrný plán, což je 0,2 kreditů za minutu, které můžu použít nebo banku.

Moje aplikace je zaneprázdněna na začátku a konci mého pracovního dne zaměstnanců, mezi 7:00-9:00 a 4:00-6:13:00. V průběhu dalších 20 hodin dne je moje aplikace obvykle při nečinnosti, a to pouze pomocí 10% vCPU. V době mimo špičku získáme 0,2 kreditů za minutu, ale spotřebujete jenom 0. l kredity za minutu, takže můj virtuální počítač bude bankou 0,1 x 60 = 6 kreditů za hodinu.  Po dobu 20 hodin, u kterých jsem mimo špičku, budu k dis120 kredity bank.  

Během špičky moje aplikace průměrně 60% využití vCPU, přebírám 0,2 kreditů za minutu, ale 0,6 spotřebovávám kreditů za minutu, a za čistých nákladů 0,4 na 0,4 kreditů za minutu a za hodinu. Počet hodin využití ve špičce je 4 hodiny, takže náklady 4 × 24 = 96 se za využití ve špičce zadarmo.

Pokud mám vynásobené kredity 120, které jsem vybral mimo špičku, a 96 odečíst kredity, které jsme využívali ve špičkách, mám banku dalších 24 kreditů za den, kterou můžu použít pro jiné shluky aktivity.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>Otázka: Jak mohu vypočítat nahromaděné a využité kredity?

Odpověď: můžete použít následující **vzorec:**

(Základní PROCESORový výkon virtuálního počítače – využití procesoru)/100 = banka kreditů nebo použití za minutu

například ve výše uvedeném případě je váš směrný plán 20% a pokud využijete 10% procesoru, který shromažďujete (20%-10%)/100 = 0,1 kredit za minutu.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>Otázka: podporuje B-Series Premium Storage datové disky?

Odpověď **: Ano** , všechny velikosti řady B-Series podporují Premium Storage datových disků.

### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>Otázka: Proč je můj zbývající kredit nastavený na 0 po opětovném nasazení nebo zastavení/spuštění?

Odpověď **: když** je virtuální počítač "REDPLOYED" a virtuální počítač se přesune do jiného uzlu, kumulovaný kredit se ztratí. Pokud je virtuální počítač zastavený nebo spuštěný, ale zůstává ve stejném uzlu, si virtuální počítač zachová Kumulovaný kredit. Pokaždé, když se virtuální počítač na uzlu spustí znovu, získá počáteční kredit za Standard_B8ms je 240.

### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>Otázka: co se stane, když nasadím nepodporovanou bitovou kopii operačního systému na B1ls?

Odpověď **: B1ls** podporuje pouze systémy Linux a pokud nasazujete jinou bitovou kopii operačního systému, nebudete mít nejlepší prostředí pro zákazníky.

## <a name="other-sizes-and-information"></a>Další velikosti a informace

- [Obecné účely](sizes-general.md)
- [Optimalizované pro výpočty](sizes-compute.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)

Cenová kalkulačka: [Cenová Kalkulačka](https://azure.microsoft.com/pricing/calculator/)

Další informace o typech disků: [typy disků](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.

---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 04/11/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 7196a2ea794c1d17a2c55c05accb447d83929972
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64744034"
---
Tato část obsahuje informace o starší generace škálu velikostí virtuálních počítačů. Tyto velikosti jsou stále podporovány, ale nebude dostávat dodatečnou kapacitu. Existují novější nebo alternativní velikostí, které jsou obecně dostupné. Najdete [velikosti pro Windows virtual machines v Azure](../articles/virtual-machines/windows/sizes.md) nebo [velikostí pro virtuální počítače s Linuxem v Azure](../articles/virtual-machines/linux/sizes.md) vybrat virtuální počítač velikosti, které bude nejlépe vyhovovaly vašim požadavkům.  

Další informace o změně velikosti virtuálního počítače s Linuxem najdete v tématu [změnit velikost virtuálního počítače s Linuxem pomocí Azure CLI](../articles/virtual-machines/linux/change-vm-size.md). Pokud používáte virtuální počítače s Windows a dávají přednost používání Powershellu, přečtěte si téma [změnit velikost virtuálního počítače s Windows](../articles/virtual-machines/windows/resize-vm.md).  

<br>

### <a name="basic-a"></a>Basic A  

**Novější doporučená velikost**: [Av2-series](../articles/virtual-machines/windows/sizes-general.md#av2-series)

Premium Storage:  Nepodporuje se

Premium Storage ukládání do mezipaměti:  Nepodporuje se

Velikosti úrovně Basic jsou primárně určeny pro úlohy související s vývojem a další aplikace, pro které se nevyžaduje vyrovnávání zatížení, automatické škálování nebo používání virtuálních počítačů s vysokými nároky na paměť.

|Velikost – Velikost\Název | Virtuální procesory |Memory (Paměť)|Síťové karty (Max.)|Max. velikost dočasného disku |Max. datové disky (1 023 GB)|Max. IOPS (300 na disk)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1×300|
|A1\Basic_A1|1|1,75 GB|2| 40 GB |2|2×300|
|A2\Basic_A2|2|3,5 GB|2| 60 GB|4|4×300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8×300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16×300|

<br>

### <a name="a-series"></a>A-Series  

**Novější doporučená velikost**: [Av2-series](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 50-100

Premium Storage:  Nepodporuje se

Premium Storage ukládání do mezipaměti:  Nepodporuje se

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (HDD): GiB | Max. datových disků | Maximální propustnost datového disku: IOPS | Maximální počet síťových karet / očekávaný šířka pásma (MB/s)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0,768 |20 |1 |1×500 |2 / 100 |
| Standard_A1 |1 |1,75 |70 |2 |2×500 |2 / 500  |
| Standard_A2 |2 |3,5 |135 |4 |4×500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8×500 |2 / 1 000 |
| Standard_A4 |8 |14 |605 |16 |16×500 |4 / 2 000 |
| Standard_A5 |2 |14 |135 |4 |4×500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8×500 |2 / 1 000 |
| Standard_A7 |8 |56 |605 |16 |16×500 |4 / 2 000 |

<sup>1</sup> je velikost the A0 využívá fyzický hardware. Pouze pro tuto konkrétní velikost můžou výkon vašich spuštěných úloh ovlivnit jiná zákaznická nasazení. Relativní výkon je popsaný níže jako očekávaný základ, přičemž se dá očekávat variabilita přibližně 15 procent.

<br>

### <a name="a-series---compute-intensive-instances"></a>A-series – Instance náročné na výpočetní výkon  

**Novější doporučená velikost**: [Av2-series](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 225

Premium Storage:  Nepodporuje se

Premium Storage ukládání do mezipaměti:  Nepodporuje se

Velikosti A8-A11 a H-series se také označují jako *náročné na výpočetní výkon*. Hardware pro hostování těchto velikostí je navržený a optimalizovaný pro úlohy náročné na výpočty a síťový provoz, včetně clusterovaného vysokovýkonného výpočetního prostředí (HPC), například pro modelování a simulace. Řady A8-A11-series používají Intel Xeon E5-2670 @ 2,6 GHz a H-series používá Intel Xeon E5-2667 v3 @ 3,2 GHz.  

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (HDD): GiB | Max. datových disků | Maximální propustnost datového disku: IOPS | Maximální počet síťových karet|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32×500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64 x 500 |4 |
| Standard_A10 |8 |56 |382 |32 |32×500 |2  |
| Standard_A11 |16 |112 |382 |64 |64 x 500 |4 |

<sup>1</sup>pro aplikace MPI vyhrazenou síť back-end RDMA zapnutá síť FDR InfiniBand, která zajišťuje mimořádně nízkou latenci a velkou šířku pásma.  

<br>

### <a name="d-series"></a>D-series  

**Novější doporučená velikost**: [Dv3-series](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Premium Storage:  Nepodporuje se

Premium Storage ukládání do mezipaměti:  Nepodporuje se

| Velikost         | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální počet datových disků nebo propustnost: IOPS | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 4 / 4×500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8×500                         | 2 / 1 000                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16 / 16×500                         | 4 / 2 000                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32 / 32×500                       | 8 / 4 000                     |

<sup>1</sup> řadu virtuálních počítačů můžete spustit na jednom z následujících procesoru: 2.2 GHz Intel E5-2660 v2 Xeon®, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) nebo procesorech 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>D-series – optimalizované z hlediska paměti  

**Novější doporučená velikost**: [Dv3-series](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Premium Storage:  Nepodporuje se

Premium Storage ukládání do mezipaměti:  Nepodporuje se

| Velikost         | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální počet datových disků nebo propustnost: IOPS | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8×500                         | 2 / 1 000                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16×500                         | 4 / 2 000                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32×500                       | 8 / 4 000                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 8000                |

<sup>1</sup> řadu virtuálních počítačů můžete spustit na jednom z následujících procesoru: 2.2 GHz Intel E5-2660 v2 Xeon®, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) nebo procesorech 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series"></a>DS-series  

**Novější doporučená velikost**: [Řada DSv3-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1</sup>

Premium Storage:  Podporováno

Premium Storage ukládání do mezipaměti:  Podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3,5 |7 |4 |4 000 / 32 (43) |3 200 / 32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8 000 / 64 (86) |6 400 / 64 |2 / 1 000 |
| Standard_DS3 |4 |14 |28 |16 |16 000 / 128 (172) |12 800 / 128 |4 / 2 000 |
| Standard_DS4 |8 |28 |56 |32 |32 000 / 256 (344) |25 600 / 256 |8 / 4 000 |

<sup>1</sup> řadu virtuálních počítačů můžete spustit na jednom z následujících procesoru: 2.2 GHz Intel E5-2660 v2 Xeon®, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) nebo procesorech 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS-series – optimalizované z hlediska paměti  

**Novější doporučená velikost**: [Řada DSv3-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1,2</sup>

Premium Storage:  Podporováno

Premium Storage ukládání do mezipaměti:  Podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8 000 / 64 (72) |6 400 / 64 |2 / 1 000 |
| Standard_DS12 |4 |28 |56 |16 |16 000 / 128 (144) |12 800 / 128 |4 / 2 000 |
| Standard_DS13 |8 |56 |112 |32 |32 000 / 256 (288) |25 600 / 256 |8 / 4 000 |
| Standard_DS14 |16 |112 |224 |64 |64 000 / 512 (576) |51 200 / 512 |8 / 8000 |

<sup>1</sup> maximální propustnost disku (IOPS nebo MB/s) s DS-series virtuálních počítačů může být omezená počtem, velikostí a prokládáním připojených disků.  Podrobnosti najdete v tématu [návrh pro vysoký výkon](../articles/virtual-machines/windows/premium-storage-performance.md).   
<sup>2</sup> řadu virtuálních počítačů můžete spustit na jednom z následujících procesoru: 2.2 GHz Intel E5-2660 v2 Xeon®, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) nebo procesorech 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

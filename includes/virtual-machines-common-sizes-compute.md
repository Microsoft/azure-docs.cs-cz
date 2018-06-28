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
ms.openlocfilehash: 638b52edb554b6bddb206943fca42b03bdc69060
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "30845127"
---
<!-- F-series, Fs-series* -->

Výpočetní optimalizované velikosti virtuálních počítačů mají vysoký poměr procesoru k paměti a jsou vhodné pro střední provoz webových serverů, síťových zařízení, procesů batch a aplikační servery. Tento článek obsahuje informace o počtu Vcpu, datové disky a síťové adaptéry, jakož i úložiště propustnost a šířku pásma sítě pro každou velikost v této skupině.

Fsv2-series je založena na procesor Intel® Xeon® platinové 8168 poskytuje funkci základní základní četností 2.7 GHz a maximální turbo jednojádrový četností 3.7 GHz. Intel® AVX-512 pokyny, které jsou nové na škálovatelných procesory Intel, poskytne až 2 X zvýšení výkonu vektoru zpracování úloh na jednoduché i dvojité přesnosti operací s pohyblivou čárkou. Jinými slovy jsou skutečně rychlé pro všechny výpočetní úlohy. 

Za nižší cenu za hodinu seznamu Fsv2-series je hodnota nejlepší v ceníku výkon v Azure portfolií založené na Azure výpočetní jednotky (ACU) na jeden virtuální procesor. 

Řada F-series využívá procesory Intel Xeon® E5-2673 v3 (Haswell) s frekvencí 2,4 GHz, kterou může technologie Intel Turbo Boost 2.0 zvýšit až na 3,1 GHz. Výkon procesoru je tedy stejný jako u virtuálních počítačů Dv2-series.  

Virtuální počítače řady F-series jsou skvělou volbou pro úlohy, které potřebují rychlejší procesory, ale ne tolik paměti nebo dočasného úložiště na virtuální procesor.  Úlohy jako například analýzy, herní servery, webové servery nebo dávkové zpracování můžou řadu F-series s výhodou využít.

Řada Fs-series nabízí všechny výhody řady F-series a navíc službu Premium Storage.

## <a name="fsv2-series-sup1sup"></a>Fsv2-series <sup>1</sup>

ACU: 195-210

| Velikost             | pro virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální počet síťových adaptérů / očekává šířku pásma sítě (MB/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|------------------------------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 (32)                                                             | 2 / 875                                        |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 (64)                                                             | 2 / 1,750                                      |
| Standard_F8s_v2   | 8      | 16          | 64             | 16             | 16000 (128)                                                           | 4 / 3,500                                      |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 (256)                                                           | 4 / 7000                                      |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 (512)                                                           | 8 / 14,000                                     |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 (1024)                                                         | 8 / 28,000                                     |
| Standard_F72s_v2<sup>2, 3</sup> | 72     | 144         | 576            | 32             | 144000 (1520)                                                         | 8 / 30,000                                     |

<sup>1</sup> Fsv2-series Virtuálního počítače funkci technologii Intel® Hyper-Threading

<sup>2</sup> víc než 64 virtuálních procesorů se vyžaduje jedna z těchto podporované hostované operační systémy: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 a Red Hat Enterprise Linux, CentOS 7.3 nebo Oracle Linux 7.3 s LIS 4.2.1

<sup>3</sup> instance je izolovaný hardware vyhrazený pro jednoho zákazníka.

## <a name="fs-series-sup1sup"></a>FS-series <sup>1</sup>

ACU: 210–250

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových adaptérů / očekává šířku pásma sítě (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4 000 / 32 (12) |3 200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8 000 / 64 (24) |6 400 / 96 |2 / 1 500 |
| Standard_F4s |4 |8 |16 |16 |16 000 / 128 (48) |12 800 / 192 |4 / 3 000 |
| Standard_F8s |8 |16 |32 |32 |32 000 / 256 (96) |25 600 / 384 |8 / 6 000 |
| Standard_F16s |16 |32 |64 |64 |64 000 / 512 (192) |51 200 / 768 |8 / 12000 |

MB/s = 10^6 bajtů za sekundu a GiB = 1024^3 bajtů.

<sup>1</sup> propustnost maximální disku (IOPS nebo MB/s) možné pomocí řady Fs virtuálních počítačů může být omezena číslo, velikost a proložení připojené disky.  Podrobnosti viz článek [Premium Storage: Vysoce výkonné úložiště pro virtuální počítače Azure](../articles/virtual-machines/windows/premium-storage.md).


<br>

## <a name="f-series"></a>F-series

ACU: 210–250

| Velikost         | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální propustnost datových disků: IOPS | Maximální počet síťových adaptérů / očekává šířku pásma sítě (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4×500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8×500                         | 2 / 1 500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16×500                         | 4 / 3 000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32×500                       | 8 / 6 000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 12000           |


<br>



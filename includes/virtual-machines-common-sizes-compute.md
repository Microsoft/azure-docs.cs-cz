---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 3bd4adcd6df07aa47312223c559f0e9ed4fc0b14
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51264340"
---
<!-- F-series, Fs-series* -->

COMPUTE optimalizované velikosti virtuálních počítačů mají vysoký poměr procesorů k paměti a jsou vhodné pro webové servery středním provozem, síťová zařízení, dávkové procesy a aplikační servery. Tento článek obsahuje informace o počtu virtuálních procesorů, datové disky a síťové adaptéry, jakož i úložiště propustnost a šířku pásma sítě pro jednotlivé velikosti v této skupině.

Řada Fsv2 je založená na procesorech Intel® Xeon® Platinum 8168, poskytuje funkci dlouhodobě všech základních Turbo rychlost 3.4GHz a frekvenci maximální turbo jednojádrový 3,7 GHz. Intel® AVX-512 pokyny, které jsou nové na procesorech Intel škálovatelné, bude poskytovat až 2 X zvýšení výkonu vektoru zpracování úloh na jednoduché i dvojité přesnosti s plovoucí desetinnou čárkou bodu operace. Jinými slovy je velmi rychlá pro všechny výpočetní úlohy. 

Za nižší ceníkové za každou hodinu je Fsv2-series nejlepší poměr cena – výkon v portfoliu Azure, na Azure Compute jednotek (ACU) na virtuální procesor. 

Řada F-series využívá procesory Intel Xeon® E5-2673 v3 (Haswell) s frekvencí 2,4 GHz, kterou může technologie Intel Turbo Boost 2.0 zvýšit až na 3,1 GHz. Výkon procesoru je tedy stejný jako u virtuálních počítačů Dv2-series.  

Virtuální počítače řady F-series jsou skvělou volbou pro úlohy, které potřebují rychlejší procesory, ale ne tolik paměti nebo dočasného úložiště na virtuální procesor.  Úlohy jako například analýzy, herní servery, webové servery nebo dávkové zpracování můžou řadu F-series s výhodou využít.

Řada Fs-series nabízí všechny výhody řady F-series a navíc službu Premium Storage.

## <a name="fsv2-series-sup1sup"></a>Řada Fsv2 <sup>1</sup>

ACU: 195-210

Storage úrovně Premium: podporováno

Ukládání do mezipaměti služby Premium Storage: podporováno

| Velikost             | virtuálních procesorů | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|------------------------------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 / 31 (32)           | 3200 / 47                | Střední                                       |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 / 63 (64)           | 6400 / 95                | Střední                                       |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000 / 127 (128)        | 12800 / 190              | Vysoký                                           |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 / 255 (256)        | 25600 / 380              | Vysoký                                           |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 / 512 (512)        | 51200 / 750              | Velmi vysoké                                 |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 / 1024 (1024)     | což odpovídá 80 000 v / 1100             | Velmi vysoké                                 |
| Standard_F72s_v2<sup>2, 3</sup> | 72 | 144 | 576         | 32             | 144000 / 1152 (1520)     | což odpovídá 80 000 v / 1100             | Velmi vysoké                                 |


<sup>1</sup> funkcí technologie Intel® Hyper-Threading Fsv2-series Virtuálních počítačů

<sup>2</sup> více než 64 virtuálních procesorů se vyžaduje jednu z těchto podporovaných hostovaných operačních systémů: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 a Red Hat Enterprise Linux, CentOS 7.3 nebo Oracle Linux 7.3 s LIS 4.2.1

<sup>3</sup> instance je izolovaná na hardware vyhrazený pro jediného zákazníka.

## <a name="fs-series-sup1sup"></a>Řada FS-series <sup>1</sup>

ACU: 210–250

Storage úrovně Premium: podporováno

Ukládání do mezipaměti služby Premium Storage: podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4 000 / 32 (12) |3 200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8 000 / 64 (24) |6 400 / 96 |2 / 1 500 |
| Standard_F4s |4 |8 |16 |16 |16 000 / 128 (48) |12 800 / 192 |4 / 3 000 |
| Standard_F8s |8 |16 |32 |32 |32 000 / 256 (96) |25 600 / 384 |8 / 6 000 |
| Standard_F16s |16 |32 |64 |64 |64 000 / 512 (192) |51 200 / 768 |8 / 12000 |

MB/s = 10^6 bajtů za sekundu a GiB = 1024^3 bajtů.

<sup>1</sup> maximální propustnost disku (IOPS nebo MB/s) se Fs-series virtuálních počítačů může být omezená počtem, velikostí a prokládáním připojených disků.  Podrobnosti viz článek [Premium Storage: Vysoce výkonné úložiště pro virtuální počítače Azure](../articles/virtual-machines/windows/premium-storage.md).


<br>

## <a name="f-series"></a>F-series

ACU: 210–250

Storage úrovně Premium: Není podporováno.

Premium Storage ukládání do mezipaměti: Není podporováno

| Velikost         | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální propustnost datových disků: IOPS | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4×500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8×500                         | 2 / 1 500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16×500                         | 4 / 3 000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32×500                       | 8 / 6 000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 12000           |


<br>



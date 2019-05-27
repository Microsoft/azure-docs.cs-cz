---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 05/16/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 40857879826963f9a82cb5864b6980305c522679
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145731"
---
Tato část obsahuje informace o velikostech virtuálních počítačů předchozí generace. Tyto velikosti je stále možné, ale nejsou k dispozici novější generace. 

## <a name="f-series"></a>F-series

Řada F-series využívá procesory Intel Xeon® E5-2673 v3 (Haswell) s frekvencí 2,4 GHz, kterou může technologie Intel Turbo Boost 2.0 zvýšit až na 3,1 GHz. Výkon procesoru je tedy stejný jako u virtuálních počítačů Dv2-series.  

Virtuální počítače řady F-series jsou skvělou volbou pro úlohy, které potřebují rychlejší procesory, ale ne tolik paměti nebo dočasného úložiště na virtuální procesor.  Úlohy jako například analýzy, herní servery, webové servery nebo dávkové zpracování můžou řadu F-series s výhodou využít.

ACU: 210 - 250

Premium Storage:  Nepodporuje se

Premium Storage ukládání do mezipaměti:  Nepodporuje se

| Velikost         | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální počet datových disků nebo propustnost: IOPS | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4×500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8×500                         | 2 / 1 500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16×500                         | 4 / 3 000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32×500                       | 8 / 6 000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 12000           |

## <a name="fs-series-sup1sup"></a>Řada FS-series <sup>1</sup>

Řada Fs-series nabízí všechny výhody řady F-series a navíc službu Premium Storage.

ACU: 210 - 250

Premium Storage:  Podporováno

Premium Storage ukládání do mezipaměti:  Podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4 000 / 32 (12) |3 200 / 48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8 000 / 64 (24) |6 400 / 96 |2 / 1 500 |
| Standard_F4s |4 |8 |16 |16 |16 000 / 128 (48) |12 800 / 192 |4 / 3 000 |
| Standard_F8s |8 |16 |32 |32 |32 000 / 256 (96) |25 600 / 384 |8 / 6 000 |
| Standard_F16s |16 |32 |64 |64 |64 000 / 512 (192) |51 200 / 768 |8 / 12000 |

MB/s = 10^6 bajtů za sekundu a GiB = 1024^3 bajtů.

<sup>1</sup> maximální propustnost disku (IOPS nebo MB/s) se Fs-series virtuálních počítačů může být omezená počtem, velikostí a prokládáním připojených disků.  Podrobnosti najdete v tématu [návrh pro vysoký výkon](../articles/virtual-machines/windows/premium-storage-performance.md).  

## <a name="ls-series"></a>Řada Ls

Řada Ls-series nabízí až 32 virtuálních procesorů a využívá [řadu procesorů Intel® Xeon® E5 v3](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Řada Ls-series má stejný výkon procesoru jako řady G/GS-series a 8 GiB paměti na virtuální procesor.

Řada Ls-series nepodporuje vytváření z místní mezipaměti a zvyšuje dosažitelné vstupně-výstupních operací trvalých datových disků. Vysoká propustnost a IOPS místního disku díky jsou virtuální počítače řady Ls-series ideální pro úložišť typu NoSQL, jako je například Apache Cassandra a MongoDB, které replikujte data napříč několika virtuálních počítačů k přetrvávání v případě selhání jednoho virtuálního počítače.

ACU: 180-240

Premium Storage:  Podporováno

Premium Storage ukládání do mezipaměti:  Nepodporuje se
 
| Velikost          | Virtuální procesory | Paměť (GiB) | Dočasné úložiště (GiB) | Max. datových disků | Maximální propustnost dočasného úložiště (IOPS nebo MB/s) | Maximální propustnost disku bez mezipaměti (IOPS nebo MB/s) | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20,000 / 200 | 5 000 / 125  | 2 / 4,000  | 
| Standard_L8s   | 8  | 64  | 1 388 | 32 | 40,000 / 400 | 10 000 / 250 | 4 / 8,000  | 
| Standard_L16s  | 16 | 128 | 2 807 | 64 | 80,000 / 800 | 20 000 / 500 | 8 / 16,000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5,630 | 64   | 160,000 / 1,600   | 40 000 / 1 000     | 8 / 20,000 | 

Maximální propustnost disku s virtuální počítače řady Ls-series může být omezená podle počtu, velikostí a prokládáním připojených disků. Podrobnosti najdete v tématu [návrh pro vysoký výkon](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> instance je izolovaná na hardware vyhrazený pro jediného zákazníka.

## <a name="nvv2-series-preview"></a>NVv2-series (ve verzi Preview)

**Novější doporučená velikost**: [NVv3-series (ve verzi Preview)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv2-series-preview)

Virtuální počítače řady NVv2 využívají [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU a NVIDIA GRID technologii procesorů Intel Broadwell. Tyto virtuální počítače je určená pro urychlení GPU grafických aplikací a virtuálních ploch, kde chcete zákazníkům vizualizovat svoje data, simulovat výsledky k zobrazení, pracovat na CAD nebo vykreslování a datového proudu obsahu. Tyto virtuální počítače navíc mohou spouštět úlohy v jednoduché přesnosti, jako je kódování a vykreslování. NVv2 virtuálních počítačů podporují službu Premium Storage a dodávají s dvakrát systémové paměti (RAM) ve srovnání s jeho předchůdce NV-series.  

Každý GPU v instancích NVv2 obsahuje licenci mřížky. Tuto licenci získáte flexibilitu používat NV instance jako virtuální pracovní stanice pro jednoho uživatele nebo 25 souběžných uživatelů můžete připojit k virtuálnímu počítači pro scénář virtuální aplikace.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet | Virtuálních pracovních stanic | Virtuální aplikace | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Velikosti Standard A0–A4 při použití rozhraní příkazového řádku a PowerShellu

V klasickém modelu nasazení se některé názvy virtuálních počítačů s prostředím příkazového řádku a PowerShellu trochu liší:

* Standard_A0 je ExtraSmall
* Standard_A1 je Small
* Standard_A2 je Medium
* Standard_A3 je Large
* Standard_A4 je ExtraLarge

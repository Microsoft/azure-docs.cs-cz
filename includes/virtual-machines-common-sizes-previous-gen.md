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
ms.openlocfilehash: cfffc29a467a89416964564b9c55a73cbf77377d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68601312"
---
V této části najdete informace o předchozích generacích velikostí virtuálních počítačů. Tyto velikosti je stále možné používat, ale k dispozici jsou novější generace. 

## <a name="f-series"></a>F-series

Řada F-series využívá procesory Intel Xeon® E5-2673 v3 (Haswell) s frekvencí 2,4 GHz, kterou může technologie Intel Turbo Boost 2.0 zvýšit až na 3,1 GHz. Výkon procesoru je tedy stejný jako u virtuálních počítačů Dv2-series.  

Virtuální počítače řady F-series jsou skvělou volbou pro úlohy, které potřebují rychlejší procesory, ale ne tolik paměti nebo dočasného úložiště na virtuální procesor.  Úlohy jako například analýzy, herní servery, webové servery nebo dávkové zpracování můžou řadu F-series s výhodou využít.

ACU 210 – 250

Premium Storage:  Není podporováno

Ukládání Premium Storage do mezipaměti:  Není podporováno

| Size         | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/čtení MB/s/zápis MB/s | Maximální počet datových disků/propustnost: IOPS | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4×500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8×500                         | 2 / 1 500                     |
| Standard_F4  | 4         | 8           | 64             | 12000 / 187 / 93                                         | 16 / 16×500                         | 4 / 3 000                     |
| Standard_F8  | 8         | 16          | 128            | 24000 / 375 / 187                                        | 32 / 32×500                       | 8 / 6 000                     |
| Standard_F16 | 16        | 32          | 256            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 12000           |

## <a name="fs-series-sup1sup"></a>FS – řada <sup>1</sup>

Řada Fs-series nabízí všechny výhody řady F-series a navíc službu Premium Storage.

ACU 210 – 250

Premium Storage:  Podporováno

Ukládání Premium Storage do mezipaměti:  Podporováno

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4000/32 (12) |3200/48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8000/64 (24) |6400/96 |2 / 1 500 |
| Standard_F4s |4 |8 |16 |16 |16000/128 (48) |12800/192 |4 / 3 000 |
| Standard_F8s |8 |16 |32 |32 |32000/256 (96) |25600 / 384 |8 / 6 000 |
| Standard_F16s |16 |32 |64 |64 |64000 / 512 (192) |51200/768 |8 / 12000 |

MB/s = 10^6 bajtů za sekundu a GiB = 1024^3 bajtů.

<sup>1</sup> maximální propustnost disku (IOPS nebo MB/s), která je možné použít pro virtuální počítač FS, může být omezená počtem, velikostí a prokládáním připojených disků.  Podrobnosti najdete v tématu [navrhování pro vysoký výkon](../articles/virtual-machines/windows/premium-storage-performance.md).  

## <a name="ls-series"></a>Řada Ls

Řada Ls-series nabízí až 32 virtuálních procesorů a využívá [řadu procesorů Intel® Xeon® E5 v3](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Řada Ls-series má stejný výkon procesoru jako řady G/GS-series a 8 GiB paměti na virtuální procesor.

Řada ls-series nepodporuje vytvoření místní mezipaměti, aby bylo možné navýšit IOPS dosažitelné pomocí trvalých datových disků. Vysoká propustnost a IOPS místního disku zpřístupňuje virtuální počítače řady ls-series, které jsou ideální pro NoSQL obchody, jako je Apache Cassandra a MongoDB, které replikují data napříč několika virtuálními počítači, aby se zajistila stálost v případě selhání jednoho virtuálního počítače.

ACU 180-240

Premium Storage:  Podporováno

Ukládání Premium Storage do mezipaměti:  Není podporováno
 
| Size          | Virtuální procesory | Paměť (GiB) | Dočasné úložiště (GiB) | Max. datových disků | Maximální propustnost dočasného úložiště (IOPS/MB/s) | Maximální propustnost disku při neukládání do mezipaměti (IOPS/MB/s) | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20000/200 | 5000/125  | 2 / 4 000  | 
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4 / 8 000  | 
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000 / 800 | 20000 / 500 | 8 / 16 000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5630 | 64   | 160000/1600   | 40000/1000     | 8 / 20 000 | 

Maximální propustnost disku dostupná pro virtuální počítače ls-series může být omezená počtem, velikostí a prokládáním připojených disků. Podrobnosti najdete v tématu [navrhování pro vysoký výkon](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.

## <a name="nvv2-series-preview"></a>NVv2-Series (Preview)

**Doporučení pro novější velikost**: [NVv3-Series (Preview)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv3-series--1)

Virtuální počítače řady NVv2-Series využívají technologii [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU a technologii NVIDIA Grid s procesory Intel Broadwell. Tyto virtuální počítače jsou určené pro akcelerované grafické aplikace GPU a virtuální plochy, kde si zákazníci chtějí vizualizovat svá data, simulovat výsledky pro zobrazení, práci na CAD nebo vykreslování a streamování obsahu. Tyto virtuální počítače navíc mohou spouštět úlohy v jednoduché přesnosti, jako je kódování a vykreslování. Virtuální počítače s NVv2 podporují Premium Storage a přidávají se s dvojnásobnou paměťovou pamětí (RAM) ve srovnání s jejím předchůdcem (řady NV).  

Každý grafický procesor v NVv2 instancích obsahuje licenci na MŘÍŽKu. Tato licence vám poskytne flexibilitu pro použití instance NV jako virtuální pracovní stanice pro jednoho uživatele nebo 25 souběžných uživatelů se může připojit k virtuálnímu počítači pro scénář virtuální aplikace.

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet | Virtuální pracovní stanice | Virtuální aplikace | 
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

---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 08/15/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: a4746a945f1a89c34308a3bd968f6341e0e25ac5
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541448"
---
Tato část poskytuje informace o starších generacích velikostí virtuálních počítačů. Tyto velikosti se pořád podporují, ale nezískají další kapacitu. K dispozici jsou novější nebo alternativní velikosti, které jsou všeobecně dostupné. Chcete-li zvolit velikosti virtuálních počítačů, které budou nejlépe vyhovovat vašim potřebám, přečtěte si informace o velikostech [virtuálních počítačů s Windows v Azure](../articles/virtual-machines/windows/sizes.md) nebo [velikosti pro virtuální počítače se systémem Linux v Azure](../articles/virtual-machines/linux/sizes.md) .  

Další informace o změně velikosti virtuálního počítače se systémem Linux najdete v tématu [Změna velikosti virtuálního počítače se systémem Linux pomocí rozhraní příkazového řádku Azure CLI](../articles/virtual-machines/linux/change-vm-size.md). Pokud používáte virtuální počítače s Windows a dáváte přednost použití PowerShellu, přečtěte si téma [Změna velikosti virtuálního počítače s Windows](../articles/virtual-machines/windows/resize-vm.md).  

<br>

### <a name="basic-a"></a>Basic A  

**Doporučení pro novější velikost**: [Av2-series](../articles/virtual-machines/windows/sizes-general.md#av2-series)

Premium Storage:  Není podporováno

Ukládání Premium Storage do mezipaměti:  Není podporováno

Velikosti úrovně Basic jsou primárně určeny pro úlohy související s vývojem a další aplikace, pro které se nevyžaduje vyrovnávání zatížení, automatické škálování nebo používání virtuálních počítačů s vysokými nároky na paměť.

|Velikost – Velikost\Název | Virtuální procesory |Memory (Paměť)|Síťové karty (Max.)|Max. velikost dočasného disku |Max. datové disky (1023 GB)|Max. IOPS (300 na disk)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1×300|
|A1\Basic_A1|1|1,75 GB|2| 40 GB |2|2×300|
|A2\Basic_A2|2|3,5 GB|2| 60 GB|4|4×300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8×300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16×300|

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Velikosti Standard A0–A4 při použití rozhraní příkazového řádku a PowerShellu

V klasickém modelu nasazení se některé názvy virtuálních počítačů s prostředím příkazového řádku a PowerShellu trochu liší:

* Standard_A0 je ExtraSmall
* Standard_A1 je Small
* Standard_A2 je Medium
* Standard_A3 je Large
* Standard_A4 je ExtraLarge

### <a name="a-series"></a>A-Series  

**Doporučení pro novější velikost**: [Av2-series](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU 50-100

Premium Storage:  Není podporováno

Ukládání Premium Storage do mezipaměti:  Není podporováno

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (HDD): GiB | Max. datových disků | Maximální propustnost datového disku: IOPS | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0,768 |20 |1 |1×500 |2 / 100 |
| Standard_A1 |1 |1,75 |70 |2 |2×500 |2 / 500  |
| Standard_A2 |2 |3,5 |135 |4 |4×500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8×500 |2 / 1 000 |
| Standard_A4 |8 |14 |605 |16 |16×500 |4 / 2 000 |
| Standard_A5 |2 |14 |135 |4 |4×500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8×500 |2 / 1 000 |
| Standard_A7 |8 |56 |605 |16 |16×500 |4 / 2 000 |

<sup>1</sup> velikost a0 se odebírá na fyzickém hardwaru. Pouze pro tuto konkrétní velikost můžou výkon vašich spuštěných úloh ovlivnit jiná zákaznická nasazení. Relativní výkon je popsaný níže jako očekávaný základ, přičemž se dá očekávat variabilita přibližně 15 procent.

<br>

### <a name="a-series---compute-intensive-instances"></a>A-series – Instance náročné na výpočetní výkon  

**Doporučení pro novější velikost**: [Av2-series](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU 225

Premium Storage:  Není podporováno

Ukládání Premium Storage do mezipaměti:  Není podporováno

Velikosti A8-A11 a H-series se také označují jako *náročné na výpočetní výkon*. Hardware pro hostování těchto velikostí je navržený a optimalizovaný pro úlohy náročné na výpočty a síťový provoz, včetně clusterovaného vysokovýkonného výpočetního prostředí (HPC), například pro modelování a simulace. Řady A8-A11-series používají Intel Xeon E5-2670 @ 2,6 GHz a H-series používá Intel Xeon E5-2667 v3 @ 3,2 GHz.  

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (HDD): GiB | Max. datových disků | Maximální propustnost datového disku: IOPS | Maximální počet síťových karet|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32×500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32×500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup> V případě aplikací MPI je v síti FDR InfiniBand povolena vyhrazená síť s back-end službou RDMA, která zajišťuje extrémně nízkou latenci a vysokou šířku pásma.  

<br>

### <a name="d-series"></a>D-series  

**Doporučení pro novější velikost**: [Dv3-series](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU 160-250 <sup>1</sup>

Premium Storage:  Není podporováno

Ukládání Premium Storage do mezipaměti:  Není podporováno

| Size         | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/čtení MB/s/zápis MB/s | Maximální počet datových disků/propustnost: IOPS | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 4 / 4×500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8×500                         | 2 / 1 000                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16 / 16×500                         | 4 / 2 000                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32 / 32×500                       | 8 / 4 000                     |

<sup>1</sup> řada virtuálních počítačů může běžet na jednom z následujících procesorů: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 V3 (Haswell) nebo 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>Řady D-Series – optimalizované pro paměť  

**Doporučení pro novější velikost**: [Dv3-series](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU 160-250 <sup>1</sup>

Premium Storage:  Není podporováno

Ukládání Premium Storage do mezipaměti:  Není podporováno

| Size         | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/čtení MB/s/zápis MB/s | Maximální počet datových disků/propustnost: IOPS | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8×500                         | 2 / 1 000                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16×500                         | 4 / 2 000                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32×500                       | 8 / 4 000                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 8000                |

<sup>1</sup> řada virtuálních počítačů může běžet na jednom z následujících procesorů: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 V3 (Haswell) nebo 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series"></a>DS-series  

**Doporučení pro novější velikost**: [DSv3-Series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU 160-250 <sup>1</sup>

Premium Storage:  Podporováno

Ukládání Premium Storage do mezipaměti:  Podporováno

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3,5 |7 |4 |4 000 / 32 (43) |3 200 / 32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8 000 / 64 (86) |6 400 / 64 |2 / 1 000 |
| Standard_DS3 |4 |14 |28 |16 |16 000 / 128 (172) |12 800 / 128 |4 / 2 000 |
| Standard_DS4 |8 |28 |56 |32 |32 000 / 256 (344) |25 600 / 256 |8 / 4 000 |

<sup>1</sup> řada virtuálních počítačů může běžet na jednom z následujících procesorů: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 V3 (Haswell) nebo 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS-Series – optimalizovaná paměť  

**Doporučení pro novější velikost**: [DSv3-Series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU 160-250 <sup>1, 2</sup>

Premium Storage:  Podporováno

Ukládání Premium Storage do mezipaměti:  Podporováno

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8 000 / 64 (72) |6 400 / 64 |2 / 1 000 |
| Standard_DS12 |4 |28 |56 |16 |16 000 / 128 (144) |12 800 / 128 |4 / 2 000 |
| Standard_DS13 |8 |56 |112 |32 |32 000 / 256 (288) |25 600 / 256 |8 / 4 000 |
| Standard_DS14 |16 |112 |224 |64 |64 000 / 512 (576) |51 200 / 512 |8 / 8000 |

<sup>1</sup> maximální propustnost disku (IOPS nebo MB/s), která je možné použít u virtuálního počítače DS, může být omezená počtem, velikostí a prokládáním připojených disků.  Podrobnosti najdete v tématu [navrhování pro vysoký výkon](../articles/virtual-machines/windows/premium-storage-performance.md).   
<sup>2</sup> řada virtuálních počítačů může běžet na jednom z následujících procesorů: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 V3 (Haswell) nebo 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ls-series"></a>Řada Ls

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

### <a name="gs-series"></a>Řada GS 

ACU 180 – 240 <sup>1</sup>

Premium Storage:  Podporováno

Ukládání Premium Storage do mezipaměti:  Podporováno

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10 000 / 100 (264) |5 000 / 125 |2 / 2 000 |
| Standard_GS2 |4 |56 |112 |16 |20 000 / 200 (528) |10 000 / 250 |2 / 4 000 |
| Standard_GS3 |8 |112 |224 |32 |40 000 / 400 (1 056) |20 000 / 500 |4 / 8 000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80 000 / 800 (2 112) |40 000 / 1 000 |8 / 16 000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160 000 / 1 600 (4 224) |80 000 / 2 000 |8 / 20 000 |

<sup>1</sup> maximální propustnost disku (IOPS nebo MB/s), kterou je možné použít pro virtuální počítač GS Series, může být omezená počtem, velikostí a prokládáním připojených disků. Podrobnosti najdete v tématu [navrhování pro vysoký výkon](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>2</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.

jsou k dispozici <sup>3</sup> omezené základní velikosti.

<br>

### <a name="g-series"></a>G-series

ACU 180 – 240

Premium Storage:  Není podporováno

Ukládání Premium Storage do mezipaměti:  Není podporováno

| Size         | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/čtení MB/s/zápis MB/s | Maximální počet datových disků/propustnost: IOPS | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8 / 8 × 500                       | 2 / 2 000                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 16 / 16 × 500                       | 2 / 4 000                     |
| Standard_G3  | 8         | 112         | 1 536          | 24000 / 375 / 187                                        | 32 / 32 × 500                     | 4 / 8 000                |
| Standard_G4  | 16        | 224         | 3 072          | 48000 / 750 / 375                                        | 64 / 64 × 500                     | 8 / 16 000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6 144          | 96000 / 1500 / 750                                       | 64 / 64 × 500                     | 8 / 20 000           |

<sup>1</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.
<br>

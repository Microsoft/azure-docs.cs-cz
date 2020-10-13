---
title: Velikosti virtuálních počítačů Azure – předchozí generace | Microsoft Docs
description: Uvádí předchozí generace velikostí dostupné pro virtuální počítače v Azure. Uvádí informace o počtu vCPU, datových discích a síťových rozhraních a propustnosti úložiště a šířce pásma sítě pro velikosti v této sérii.
services: virtual-machines
ms.subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/02/2020
ms.author: jushiman
ms.openlocfilehash: 05da5a21fb558b7d936b7569a69578eda06e11cf
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875270"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>Předchozí generace velikostí virtuálních počítačů

V této části najdete informace o předchozích generacích velikostí virtuálních počítačů. Tyto velikosti je stále možné používat, ale k dispozici jsou novější generace.

## <a name="f-series"></a>F-series

Řada F-series využívá procesory Intel Xeon® E5-2673 v3 (Haswell) s frekvencí 2,4 GHz, kterou může technologie Intel Turbo Boost 2.0 zvýšit až na 3,1 GHz. Výkon procesoru je tedy stejný jako u virtuálních počítačů Dv2-series.  

Virtuální počítače řady F-series jsou skvělou volbou pro úlohy, které potřebují rychlejší procesory, ale ne tolik paměti nebo dočasného úložiště na virtuální procesor.  Úlohy jako například analýzy, herní servery, webové servery nebo dávkové zpracování můžou řadu F-series s výhodou využít.

ACU: 210–250

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/čtení MB/s/zápis MB/s | Maximální počet datových disků/propustnost: IOPS | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2  | 16  | 3 000 / 46 / 23    | 4/4x500   | 2/750   |
| Standard_F2  | 2  | 4  | 32  | 6 000 / 93 / 46    | 8/8x500   | 2/1500  |
| Standard_F4  | 4  | 8  | 64  | 12 000 / 187 / 93  | 16/16x500 | 4/3000  |
| Standard_F8  | 8  | 16 | 128 | 24 000 / 375 / 187 | 32/32x500 | 8/6000  |
| Standard_F16 | 16 | 32 | 256 | 48 000 / 750 / 375 | 64/64x500 | 8/12000 |

## <a name="fs-series-sup1sup"></a>FS – řada <sup>1</sup>

Řada Fs-series nabízí všechny výhody řady F-series a navíc službu Premium Storage.

ACU: 210–250

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a dočasné úložiště: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_F1s  | 1  | 2  | 4  | 4  | 4000/32 (12)    | 3200/48   | 2/750   |
| Standard_F2s  | 2  | 4  | 8  | 8  | 8000/64 (24)    | 6400/96   | 2/1500  |
| Standard_F4s  | 4  | 8  | 16 | 16 | 16000/128 (48)  | 12800/192 | 4/3000  |
| Standard_F8s  | 8  | 16 | 32 | 32 | 32000/256 (96)  | 25600/384 | 8/6000  |
| Standard_F16s | 16 | 32 | 64 | 64 | 64000/512 (192) | 51200/768 | 8/12000 |

MB/s = 10^6 bajtů za sekundu a GiB = 1024^3 bajtů.

<sup>1</sup> maximální propustnost disku (IOPS nebo MB/s), která je možné použít pro virtuální počítač FS, může být omezená počtem, velikostí a prokládáním připojených disků.  Podrobnosti najdete v tématu [Návrh pro vysoký výkon](premium-storage-performance.md).

<br>



### <a name="basic-a"></a>Basic A  

**Doporučení pro novější velikost**: [Av2-Series](av2-series.md)

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Velikosti úrovně Basic jsou primárně určeny pro úlohy související s vývojem a další aplikace, pro které se nevyžaduje vyrovnávání zatížení, automatické škálování nebo používání virtuálních počítačů s vysokými nároky na paměť.

| Velikost – Velikost\Název | Virtuální procesory | Paměť|Síťové karty (Max.)| Max. velikost dočasného disku | Max. datové disky (1023 GB)| Max. IOPS (300 na disk) |
|---|---|---|---|---|---|---|
| A0\Basic_A0 | 1 | 768 MB  | 2 | 20 GB  | 1  | 1×300  |
| A1\Basic_A1 | 1 | 1,75 GB | 2 | 40 GB  | 2  | 2×300  |
| A2\Basic_A2 | 2 | 3,5 GB  | 2 | 60 GB  | 4  | 4×300  |
| A3\Basic_A3 | 4 | 7 GB    | 2 | 120 GB | 8  | 8×300  |
| A4\Basic_A4 | 8 | 14 GB   | 2 | 240 GB | 16 | 16×300 |

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Velikosti Standard A0–A4 při použití rozhraní příkazového řádku a PowerShellu

V klasickém modelu nasazení se některé názvy virtuálních počítačů s prostředím příkazového řádku a PowerShellu trochu liší:

* Standard_A0 je ExtraSmall
* Standard_A1 je Small
* Standard_A2 je Medium
* Standard_A3 je Large
* Standard_A4 je ExtraLarge

### <a name="a-series"></a>A-Series  

**Doporučení pro novější velikost**: [Av2-Series](av2-series.md)

ACU: 50–100

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (HDD): GiB | Max. datových disků | Maximální propustnost datového disku: IOPS | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 &nbsp; <sup>1</sup> | 1 | 0,768 | 20 | 1 | 1×500 | 2/100 |
| Standard_A1 | 1 | 1.75 | 70  | 2  | 2×500  | 2/500  |
| Standard_A2 | 2 | 3,5  | 135 | 4  | 4×500  | 2/500  |
| Standard_A3 | 4 | 7    | 285 | 8  | 8×500  | 2/1000 |
| Standard_A4 | 8 | 14   | 605 | 16 | 16×500 | 4/2000 |
| Standard_A5 | 2 | 14   | 135 | 4  | 4×500  | 2/500  |
| Standard_A6 | 4 | 28   | 285 | 8  | 8×500  | 2/1000 |
| Standard_A7 | 8 | 56   | 605 | 16 | 16×500 | 4/2000 |

<sup>1</sup> velikost a0 se odebírá na fyzickém hardwaru. Pouze pro tuto konkrétní velikost můžou výkon vašich spuštěných úloh ovlivnit jiná zákaznická nasazení. Relativní výkon je popsaný níže jako očekávaný základ, přičemž se dá očekávat variabilita přibližně 15 procent.

<br>

### <a name="a-series---compute-intensive-instances"></a>A-series – Instance náročné na výpočetní výkon  

**Doporučení pro novější velikost**: [Av2-Series](av2-series.md)

ACU: 225

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Velikosti A8-A11 a H-series se také označují jako *náročné na výpočetní výkon*. Hardware pro hostování těchto velikostí je navržený a optimalizovaný pro úlohy náročné na výpočty a síťový provoz, včetně clusterovaného vysokovýkonného výpočetního prostředí (HPC), například pro modelování a simulace. Řady A8-A11-series používají Intel Xeon E5-2670 @ 2,6 GHz a H-series používá Intel Xeon E5-2667 v3 @ 3,2 GHz.  

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (HDD): GiB | Max. datových disků | Maximální propustnost datového disku: IOPS | Maximální počet síťových karet|
|---|---|---|---|---|---|---|
| Standard_A8 &nbsp; <sup>1</sup> | 8  | 56  | 382 | 32 | 32×500 | 2 |
| Standard_A9 &nbsp; <sup>1</sup> | 16 | 112 | 382 | 64 | 64x500 | 4 |
| Standard_A10 | 8  | 56  | 382 | 32 | 32×500 | 2 |
| Standard_A11 | 16 | 112 | 382 | 64 | 64x500 | 4 |

<sup>1</sup> V případě aplikací MPI je v síti FDR InfiniBand povolena vyhrazená síť s back-end službou RDMA, která zajišťuje extrémně nízkou latenci a vysokou šířku pásma.  

> [!NOTE]
> Virtuální počítače A8 – A11 jsou plánovány k vyřazení na 3/2021. Důrazně doporučujeme nevytvářet žádné nové virtuální počítače A8 – A11. Migrujte všechny stávající virtuální počítače A8-A11 do novějších a výkonných vysoce výkonných výpočetních počítačů, jako je například H, zaplnění, HC, HBv2, a také obecné účely výpočetních virtuálních počítačů, jako jsou například D, E a F, pro lepší cenový výkon. Další informace najdete v tématu [Průvodce migrací HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

<br>

### <a name="d-series"></a>D-series  

**Doporučení pro novější velikost**: [Dav4-Series](dav4-dasv4-series.md), [dv4-](dv4-dsv4-series.md) Series a [Ddv4-Series](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/čtení MB/s/zápis MB/s | Maximální počet datových disků/propustnost: IOPS | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|
| Standard_D1  | 1 | 3,5 | 50  | 3 000 / 46 / 23    | 4/4x500   | 2/500  |
| Standard_D2  | 2 | 7   | 100 | 6 000 / 93 / 46    | 8/8x500   | 2/1000 |
| Standard_D3  | 4 | 14  | 200 | 12 000 / 187 / 93  | 16/16x500 | 4/2000 |
| Standard_D4  | 8 | 28  | 400 | 24 000 / 375 / 187 | 32/32x500 | 8/4000 |

<sup>1</sup> řada virtuálních počítačů může běžet na jednom z následujících procesorů: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 V3 (Haswell) nebo 2,3 GHz Intel Xeon® E5-2673 v4 (Broadwell).  

<br>

### <a name="d-series---memory-optimized"></a>Řady D-Series – optimalizované pro paměť  

**Doporučení pro novější velikost**: [Dav4-Series](dav4-dasv4-series.md), [dv4-](dv4-dsv4-series.md) Series a [Ddv4-Series](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/čtení MB/s/zápis MB/s | Maximální počet datových disků/propustnost: IOPS | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|
| Standard_D11 | 2  | 14  | 100 | 6 000 / 93 / 46    | 8/8x500   | 2/1000 |
| Standard_D12 | 4  | 28  | 200 | 12 000 / 187 / 93  | 16/16x500 | 4/2000 |
| Standard_D13 | 8  | 56  | 400 | 24 000 / 375 / 187 | 32/32x500 | 8/4000 |
| Standard_D14 | 16 | 112 | 800 | 48 000 / 750 / 375 | 64/64x500 | 8/8000 |

<sup>1</sup> řada virtuálních počítačů může běžet na jednom z následujících procesorů: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 V3 (Haswell) nebo 2,3 GHz Intel Xeon® E5-2673 v4 (Broadwell).  

<br>

## <a name="preview-dc-series"></a>Verze Preview: DC-Series

**Doporučení pro novější velikost**: [DCsv2-Series](dcv2-series.md)

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

DC-Series využívá nejnovější generaci procesorů Intel XEON s 2176G s využitím technologie SGX a s technologií Intel Turbo rozbušky může jít až o 4.7 GHz. 

| Velikost          | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                          | 3200/48                                  | 2 / 1 500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                          | 6400/96                                  | 2 / 3000                                     |

> [!IMPORTANT]
>
> Virtuální počítače DC-Series jsou [virtuální počítače generace 2](./linux/generation-2.md#creating-a-generation-2-vm) a podporují jenom `Gen2` image.


### <a name="ds-series"></a>DS-series  

**Doporučení pro novější velikost**: [Dasv4-Series](dav4-dasv4-series.md), [Dsv4-](dv4-dsv4-series.md) Series a [Ddsv4-Series](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a dočasné úložiště: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_DS1 | 1 | 3,5 | 7  | 4  | 4000/32 (43)    | 3200/32   | 2/500  |
| Standard_DS2 | 2 | 7   | 14 | 8  | 8000/64 (86)    | 6400/64   | 2/1000 |
| Standard_DS3 | 4 | 14  | 28 | 16 | 16000/128 (172) | 12800/128 | 4/2000 |
| Standard_DS4 | 8 | 28  | 56 | 32 | 32000/256 (344) | 25600/256 | 8/4000 |

<sup>1</sup> řada virtuálních počítačů může běžet na jednom z následujících procesorů: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 V3 (Haswell) nebo 2,3 GHz Intel Xeon® E5-2673 v4 (Broadwell).  

<br>

### <a name="ds-series---memory-optimized"></a>DS-Series – optimalizovaná paměť  

**Doporučení pro novější velikost**: [Dasv4-Series](dav4-dasv4-series.md), [Dsv4-](dv4-dsv4-series.md) Series a [Ddsv4-Series](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1, 2</sup>

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a dočasné úložiště: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_DS11 | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/64   | 2/1000 |
| Standard_DS12 | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/128 | 4/2000 |
| Standard_DS13 | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/256 | 8/4000 |
| Standard_DS14 | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/512 | 8/8000 |

<sup>1</sup> maximální propustnost disku (IOPS nebo MB/s), která je možné použít u virtuálního počítače DS, může být omezená počtem, velikostí a prokládáním připojených disků.  Podrobnosti najdete v tématu [Návrh pro vysoký výkon](premium-storage-performance.md).
<sup>2</sup> řada virtuálních počítačů může běžet na jednom z následujících procesorů: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 V3 (Haswell) nebo 2,3 GHz Intel Xeon® E5-2673 v4 (Broadwell).  

<br>

### <a name="ls-series"></a>Řada Ls

**Doporučení pro novější velikost**: [Lsv2-Series](lsv2-series.md)

Řada Ls-series nabízí až 32 virtuálních procesorů a využívá [řadu procesorů Intel® Xeon® E5 v3](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Řada Ls-series má stejný výkon procesoru jako řady G/GS-series a 8 GiB paměti na virtuální procesor.

Řada ls-series nepodporuje vytvoření místní mezipaměti, aby bylo možné navýšit IOPS dosažitelné pomocí trvalých datových disků. Vysoká propustnost a IOPS místního disku zpřístupňuje virtuální počítače řady ls-series, které jsou ideální pro NoSQL obchody, jako je Apache Cassandra a MongoDB, které replikují data napříč několika virtuálními počítači, aby se zajistila stálost v případě selhání jednoho virtuálního počítače.

ACU: 180–240

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

| Velikost | Virtuální procesory | Paměť (GiB) | Dočasné úložiště (GiB) | Max. datových disků | Maximální propustnost dočasného úložiště (IOPS/MB/s) | Maximální propustnost disku při neukládání do mezipaměti (IOPS/MB/s) | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| Standard_L32s &nbsp; <sup>1</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

Maximální propustnost disku dostupná pro virtuální počítače ls-series může být omezená počtem, velikostí a prokládáním připojených disků. Podrobnosti najdete v tématu [Návrh pro vysoký výkon](premium-storage-performance.md).

<sup>1</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.

### <a name="gs-series"></a>Řady GS

**Doporučení pro novější velikost**: [Easv4-Series](eav4-easv4-series.md), [Esv4-Series](ev4-esv4-series.md), [Edsv4-Series](edv4-edsv4-series.md) a [M-Series](m-series.md)

ACU: 180 – 240 <sup>1</sup>

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 | 2 | 28  | 56  | 8  | 10000/100 (264)  | 5000/ 125  | 2/2000 |
| Standard_GS2 | 4 | 56  | 112 | 16 | 20000/200 (528)  | 10000/ 250 | 2/4000 |
| Standard_GS3 | 8 | 112 | 224 | 32 | 40000/400 (1056) | 20000/ 500 | 4/8000 |
| Standard_GS4 &nbsp; <sup>3</sup> | 16 | 224 | 448 | 64 | 80000/800 (2112) | 40000/1000 | 8/16000 |
| Standard_GS5 &nbsp; <sup>2, &nbsp; 3</sup> | 32 | 448 |896 | 64 |160000/1600 (4224) | 80000/2000 | 8/20000 |

<sup>1</sup> maximální propustnost disku (IOPS nebo MB/s), kterou je možné použít pro virtuální počítač GS Series, může být omezená počtem, velikostí a prokládáním připojených disků. Podrobnosti najdete v tématu [Návrh pro vysoký výkon](premium-storage-performance.md).

<sup>2</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.

jsou k dispozici <sup>3</sup> omezené základní velikosti.

<br>

### <a name="g-series"></a>G-series

**Doporučení pro novější velikost**: [Eav4-Series](eav4-easv4-series.md), [Ev4-](ev4-esv4-series.md) Series a [Edv4-](edv4-edsv4-series.md) Series a [M-Series](m-series.md)

ACU: 180–240

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/čtení MB/s/zápis MB/s | Maximální počet datových disků/propustnost: IOPS | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|
| Standard_G1  | 2  | 28  | 384  | 6 000 / 93 / 46    | 8/8x500   | 2/2000  |
| Standard_G2  | 4  | 56  | 768  | 12 000 / 187 / 93  | 16/16x500 | 2/4000  |
| Standard_G3  | 8  | 112 | 1536 | 24 000 / 375 / 187 | 32/32x500 | 4/8000  |
| Standard_G4  | 16 | 224 | 3072 | 48 000 / 750 / 375 | 64/64x500 | 8/16000 |
| Standard_G5 &nbsp; <sup>1</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.
<br>

## <a name="nv-series"></a>Řada NV
**Doporučení pro novější velikost**: [NVv3-Series](nvv3-series.md) a [NVv4-Series](nvv4-series.md)

Virtuální počítače řady NV využívají technologii [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU a technologii NVIDIA Grid pro aplikace s akcelerovanými pracovními plochami a virtuálními plochami, kde zákazníci mohou vizualizovat svá data nebo simulace. Uživatelé mohou vizualizovat své pracovní postupy náročné na grafiku na instancích NV a získat tak vynikající možnosti grafiky a také spouštět úlohy s jednoduchou přesností, jako je například kódování a vykreslování. Virtuální počítače řady NV využívají i procesory Intel Xeon E5-2690 V3 (Haswell).

Každý grafický procesor v instancích NV obsahuje licenci na MŘÍŽKu. Tato licence vám poskytne flexibilitu pro použití instance NV jako virtuální pracovní stanice pro jednoho uživatele nebo 25 souběžných uživatelů se může připojit k virtuálnímu počítači pro scénář virtuální aplikace.

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Migrace za provozu: nepodporováno

Aktualizace pro zachování paměti: nepodporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet | Virtuální pracovní stanice | Virtuální aplikace |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = polovina karty M60.
<br>


## <a name="nvv2-series"></a>NVv2-Series

**Doporučení pro novější velikost**: [NVv3-Series](nvv3-series.md)

Virtuální počítače řady NVv2-Series využívají technologii [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU a technologii NVIDIA Grid s procesory Intel Broadwell. Tyto virtuální počítače jsou určené pro akcelerované grafické aplikace GPU a virtuální plochy, kde si zákazníci chtějí vizualizovat svá data, simulovat výsledky pro zobrazení, práci na CAD nebo vykreslování a streamování obsahu. Tyto virtuální počítače navíc můžou spouštět úlohy s jednoduchou přesností, jako je například kódování a vykreslování. Virtuální počítače s NVv2 podporují Premium Storage a přidávají se s dvojnásobnou paměťovou pamětí (RAM) ve srovnání s jejím předchůdcem (řady NV).  

Každý grafický procesor v NVv2 instancích obsahuje licenci na MŘÍŽKu. Tato licence vám poskytne flexibilitu pro použití instance NV jako virtuální pracovní stanice pro jednoho uživatele nebo 25 souběžných uživatelů se může připojit k virtuálnímu počítači pro scénář virtuální aplikace.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet | Virtuální pracovní stanice | Virtuální aplikace |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

<br>

## <a name="nc-series"></a>Řada NC
**Doporučení k novější velikosti**: [NC T4 V3-Series](nct4-v3-series.md)

Virtuální počítače řady NC-Series jsou napájené kartou [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) a procesorem Intel Xeon E5-2690 V3 (Haswell). Uživatelé můžou zpracovávejte data rychleji tím, že využívají CUDA pro aplikace pro průzkumy energie, simulace havárií, trasování paprsků, hloubkové učení a další. Konfigurace NC24r poskytuje síťové rozhraní s nízkou latencí a vysokou propustností optimalizované pro vysoce provázané úlohy paralelního zpracování.

[Premium Storage](premium-storage-performance.md): nepodporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): nepodporováno<br>
[Migrace za provozu](maintenance-and-updates.md): nepodporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): nepodporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1<br>
<br>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet |
|---|---|---|---|---|---|---|---|
| Standard_NC6    | 6  | 56  | 340  | 1 | 12 | 24 | 1 |
| Standard_NC12   | 12 | 112 | 680  | 2 | 24 | 48 | 2 |
| Standard_NC24   | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = polovina karty K80.

*Podpora RDMA


<br>


## <a name="ncv2-series"></a>Řada NCv2
**Doporučení k novější velikosti**: [NC T4 V3-Series](nct4-v3-series.md) a [NC V100 V3-Series](ncv3-series.md)

Virtuální počítače řady NCv2-Series využívají grafické procesory NVIDIA Tesla P100. Tyto GPU můžou poskytnout více než dvojnásobný výpočetní výkon řady NC-Series. Zákazníci můžou využít těchto aktualizovaných GPU pro tradiční úlohy HPC, jako je modelování zásobníku, sekvence DNA, analýza bílkovin, simulace Monte Carlo a další. Kromě GPU využívají virtuální počítače řady NCv2-Series také procesory Intel Xeon E5-2690 v4 (Broadwell).

Konfigurace NC24rs v2 poskytuje síťové rozhraní s nízkou latencí a vysokou propustností optimalizované pro vysoce provázané úlohy paralelního zpracování.

[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): podporováno<br>
[Migrace za provozu](maintenance-and-updates.md): nepodporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): nepodporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>

> Pro tuto řadu virtuálních počítačů je kvóta vCPU (jádro) ve vašem předplatném zpočátku v každé oblasti nastavená na 0. [Požádejte o zvýšení kvóty vCPU](../azure-portal/supportability/resource-manager-core-quotas-request.md) pro tuto řadu v [dostupné oblasti](https://azure.microsoft.com/regions/services/).
>
| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v2    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v2   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2 * | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = jedna karta P100.

*Podpora RDMA

<br>

## <a name="nd-series"></a>Řada ND
**Doporučení k novější velikosti**: [NDv2-Series](ndv2-series.md) a [NC V100 V3-Series](ncv3-series.md)

Virtuální počítače řady ND-Series jsou nově přidané do rodiny GPU navržené pro AI a pro úlohy s hloubkovým učením. Nabízí vynikající výkon pro školení a odvozování. Instance ND jsou napájené pomocí [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU a procesorů Intel Xeon E5-2690 v4 (Broadwell). Tyto instance poskytují vynikající výkon pro operace s plovoucí desetinnou čárkou s jednoduchou přesností pro úlohy AI s využitím Microsoft Cognitive Toolkit, TensorFlow, Caffe a dalších platforem. Řada ND-Series také nabízí mnohem větší velikost paměti GPU (24 GB), což umožňuje přizpůsobit mnohem větší neuronové modely .NET. Podobně jako řada NC-Series nabízí konfiguraci se sekundární sítí s nízkou latencí a vysokou propustností prostřednictvím RDMA a InfiniBand konektivitu, takže můžete spouštět rozsáhlé školicí úlohy zahrnující mnoho GPU.

[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): podporováno<br>
[Migrace za provozu](maintenance-and-updates.md): nepodporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): nepodporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>

> Pro tuto řadu virtuálních počítačů je kvóta vCPU (jader) na oblast ve vašem předplatném zpočátku nastavená na 0. [Požádejte o zvýšení kvóty vCPU](../azure-portal/supportability/resource-manager-core-quotas-request.md) pro tuto řadu v [dostupné oblasti](https://azure.microsoft.com/regions/services/).
>
| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 24 | 32 | 80000/800 | 8 |
| Standard_ND24rs * | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = jedna karta P40.

*Podpora RDMA

<br>

## <a name="other-sizes"></a>Jiné velikosti

* [Obecné účely](sizes-general.md)
* [Optimalizované pro výpočty](sizes-compute.md)
* [Optimalizované pro paměť](sizes-memory.md)
* [Optimalizované pro úložiště](sizes-storage.md)
* [GPU](sizes-gpu.md)
* [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.

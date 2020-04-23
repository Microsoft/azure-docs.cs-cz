---
title: Velikosti virtuálních počítačů Azure Linux – předchozí generace | Dokumenty společnosti Microsoft
description: Uvádí předchozí generace velikostí dostupných pro virtuální počítače s Linuxem v Azure. Obsahuje informace o počtu virtuálních procesorů, datových disků a síťových síťových karty, jakož i propustnost úložiště a šířku pásma sítě pro velikosti v této řadě.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: jonbeck
ms.openlocfilehash: ac49d2da9d05c9677dbb6f5328874ab3a45ff661
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081536"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>Předchozí generace velikostí virtuálních strojů

Tato část obsahuje informace o předchozích generacích velikostí virtuálních strojů. Tyto velikosti lze stále používat, ale jsou k dispozici novější generace.

## <a name="f-series"></a>F-series

Řada F-series využívá procesory Intel Xeon® E5-2673 v3 (Haswell) s frekvencí 2,4 GHz, kterou může technologie Intel Turbo Boost 2.0 zvýšit až na 3,1 GHz. Výkon procesoru je tedy stejný jako u virtuálních počítačů Dv2-series.  

Virtuální počítače řady F-series jsou skvělou volbou pro úlohy, které potřebují rychlejší procesory, ale ne tolik paměti nebo dočasného úložiště na virtuální procesor.  Úlohy jako například analýzy, herní servery, webové servery nebo dávkové zpracování můžou řadu F-series s výhodou využít.

ACU: 210–250

Úložiště Premium: Není podporováno

Ukládání do mezipaměti úložiště Premium: Není podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/Read MBps/Write MB/B/b/s | Maximální počet datových disků/propustnost: IOPS | Maximální počet síťových připojení/očekávaná šířka pásma sítě (Mb/s) |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2  | 16  | 3 000 / 46 / 23    | 4/4x500   | 2/750   |
| Standard_F2  | 2  | 4  | 32  | 6 000 / 93 / 46    | 8/8x500   | 2/1500  |
| Standard_F4  | 4  | 8  | 64  | 12 000 / 187 / 93  | 16/16x500 | 4/3000  |
| Standard_F8  | 8  | 16 | 128 | 24 000 / 375 / 187 | 32/32x500 | 8/6000  |
| Standard_F16 | 16 | 32 | 256 | 48 000 / 750 / 375 | 64/64x500 | 8/12000 |

## <a name="fs-series-sup1sup"></a>Fs-řada <sup>1</sup>

Řada Fs-series nabízí všechny výhody řady F-series a navíc službu Premium Storage.

ACU: 210–250

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a protemplovaná propustnost úložiště: IOPS/MBps (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS/MBps | Maximální počet síťových připojení/očekávaná šířka pásma sítě (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standard_F1s  | 1  | 2  | 4  | 4  | 4000/32 (12)    | 3200/48   | 2/750   |
| Standard_F2s  | 2  | 4  | 8  | 8  | 8000/64 (24)    | 6400/96   | 2/1500  |
| Standard_F4s  | 4  | 8  | 16 | 16 | 16000/128 (48)  | 12800/192 | 4/3000  |
| Standard_F8s  | 8  | 16 | 32 | 32 | 32000/256 (96)  | 25600/384 | 8/6000  |
| Standard_F16s | 16 | 32 | 64 | 64 | 64000/512 (192) | 51200/768 | 8/12000 |

MB/s = 10^6 bajtů za sekundu a GiB = 1024^3 bajtů.

<sup>1</sup> Maximální propustnost disku (IOPS nebo MBps) možná s virtuálním počítačem řady Fs může být omezena počtem, velikostí a prokládáním připojených disků.  Podrobnosti naleznete v tématu návrh vysokého výkonu pro [Windows](windows/premium-storage-performance.md) nebo [Linux](linux/premium-storage-performance.md).  


## <a name="nvv2-series"></a>Řada NVv2

**Novější velikost doporučení:** [NVv3-série](nvv3-series.md)

Virtuální stroje řady NVv2 jsou poháněny grafickými procesory [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) a technologií NVIDIA GRID s procesory Intel Broadwell. Tyto virtuální počítače jsou určeny pro grafické aplikace s akcelerací GPU a virtuální plochy, kde zákazníci chtějí vizualizovat svá data, simulovat výsledky pro zobrazení, pracovat na CAD nebo vykreslit a streamovat obsah. Kromě toho tyto virtuální počítače můžete spustit jednu přesnost úlohy, jako je kódování a vykreslování. Virtuální počítače NVv2 podporují úložiště Premium storage a jsou dodávány s dvojnásobnou systémovou pamětí (RAM) ve srovnání s předchůdcem řady NV.  

Každý GPU v instancích NVv2 je dodáván s licencí GRID. Tato licence poskytuje flexibilitu používat instanci NV jako virtuální pracovní stanici pro jednoho uživatele nebo 25 souběžných uživatelů se může připojit k virtuálnímu počítači pro scénář virtuální aplikace.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet | Virtuální pracovní stanice | Virtuální aplikace |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="older-generations-of-virtual-machine-sizes"></a>Starší generace velikostí virtuálních strojů

Tato část obsahuje informace o starších generacích velikostí virtuálních strojů. Tyto velikosti jsou stále podporovány, ale neobdrží další kapacitu. Existují novější nebo alternativní velikosti, které jsou obecně k dispozici. Najdete v části [Velikosti pro virtuální počítače s Linuxem v Azure](linux/sizes.md) a vyberte velikosti virtuálních počítačů, které nejlépe vyhovují vašim potřebám.  

Další informace o změně velikosti virtuálního počítače s Linuxem najdete v [tématu Změna velikosti virtuálního počítače s Linuxem](linux/change-vm-size.md).  

<br>

### <a name="basic-a"></a>Basic A  

**Novější velikost doporučení:** [Av2-série](av2-series.md)

Úložiště Premium: Není podporováno

Ukládání do mezipaměti úložiště Premium: Není podporováno

Velikosti úrovně Basic jsou primárně určeny pro úlohy související s vývojem a další aplikace, pro které se nevyžaduje vyrovnávání zatížení, automatické škálování nebo používání virtuálních počítačů s vysokými nároky na paměť.

| Velikost – Velikost\Název | Virtuální procesory | Memory (Paměť)|Síťové karty (Max.)| Max. velikost dočasného disku | Max. datové disky (každý 1023 GB)| Max. IOPS (300 na disk) |
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

**Novější velikost doporučení:** [Av2-série](av2-series.md)

ACU: 50–100

Úložiště Premium: Není podporováno

Ukládání do mezipaměti úložiště Premium: Není podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (HDD): GiB | Max. datových disků | Maximální propustnost datového disku: IOPS | Maximální počet síťových připojení/očekávaná šířka pásma sítě (Mb/s) |
| --- | --- | --- | --- | --- | --- | --- |
| &nbsp;<sup>1.</sup> Standard_A0 | 1 | 0,768 | 20 | 1 | 1×500 | 2/100 |
| Standard_A1 | 1 | 1,75 | 70  | 2  | 2×500  | 2/500  |
| Standard_A2 | 2 | 3,5  | 135 | 4  | 4×500  | 2/500  |
| Standard_A3 | 4 | 7    | 285 | 8  | 8×500  | 2/1000 |
| Standard_A4 | 8 | 14   | 605 | 16 | 16×500 | 4/2000 |
| Standard_A5 | 2 | 14   | 135 | 4  | 4×500  | 2/500  |
| Standard_A6 | 4 | 28   | 285 | 8  | 8×500  | 2/1000 |
| Standard_A7 | 8 | 56   | 605 | 16 | 16×500 | 4/2000 |

<sup>1</sup> Velikost A0 je na fyzickém hardwaru přepsána. Pouze pro tuto konkrétní velikost můžou výkon vašich spuštěných úloh ovlivnit jiná zákaznická nasazení. Relativní výkon je popsaný níže jako očekávaný základ, přičemž se dá očekávat variabilita přibližně 15 procent.

<br>

### <a name="a-series---compute-intensive-instances"></a>A-series – Instance náročné na výpočetní výkon  

**Novější velikost doporučení:** [Av2-série](av2-series.md)

ACU: 225

Úložiště Premium: Není podporováno

Ukládání do mezipaměti úložiště Premium: Není podporováno

Velikosti A8-A11 a H-series se také označují jako *náročné na výpočetní výkon*. Hardware pro hostování těchto velikostí je navržený a optimalizovaný pro úlohy náročné na výpočty a síťový provoz, včetně clusterovaného vysokovýkonného výpočetního prostředí (HPC), například pro modelování a simulace. Řady A8-A11-series používají Intel Xeon E5-2670 @ 2,6 GHz a H-series používá Intel Xeon E5-2667 v3 @ 3,2 GHz.  

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (HDD): GiB | Max. datových disků | Maximální propustnost datového disku: IOPS | Maximální počet síťových karet|
|---|---|---|---|---|---|---|
| Standard_A8&nbsp;<sup>1</sup> | 8  | 56  | 382 | 32 | 32×500 | 2 |
| Standard_A9&nbsp;<sup>1</sup> | 16 | 112 | 382 | 64 | 64x500 | 4 |
| Standard_A10 | 8  | 56  | 382 | 32 | 32×500 | 2 |
| Standard_A11 | 16 | 112 | 382 | 64 | 64x500 | 4 |

<sup>1.</sup> Pro aplikace MPI je vyhrazená síť back-endRD povolena sítí FDR InfiniBand, která poskytuje ultra nízkou latenci a velkou šířku pásma.  

> [!NOTE]
> Virtuální počítače A8 – A11 jsou plánovány na vyřazení do důchodu na 3/2021. Další informace naleznete v [průvodci migrací HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

<br>

### <a name="d-series"></a>D-series  

**Novější velikost doporučení:** [Dv3-série](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Úložiště Premium: Není podporováno

Ukládání do mezipaměti úložiště Premium: Není podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/Read MBps/Write MB/B/b/s | Maximální počet datových disků/propustnost: IOPS | Maximální počet síťových připojení/očekávaná šířka pásma sítě (Mb/s) |
|---|---|---|---|---|---|---|
| Standard_D1  | 1 | 3,5 | 50  | 3 000 / 46 / 23    | 4/4x500   | 2/500  |
| Standard_D2  | 2 | 7   | 100 | 6 000 / 93 / 46    | 8/8x500   | 2/1000 |
| Standard_D3  | 4 | 14  | 200 | 12 000 / 187 / 93  | 16/16x500 | 4/2000 |
| Standard_D4  | 8 | 28  | 400 | 24 000 / 375 / 187 | 32/32x500 | 8/4000 |

<sup>1</sup> Rodina virtuálních zařízení může běžet na jednom z následujících procesorů: 2.2 GHz Intel Xeon® E5-2660 v2, 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) nebo 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>Řada D - optimalizovaná paměť  

**Novější velikost doporučení:** [Dv3-série](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Úložiště Premium: Není podporováno

Ukládání do mezipaměti úložiště Premium: Není podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/Read MBps/Write MB/B/b/s | Maximální počet datových disků/propustnost: IOPS | Maximální počet síťových připojení/očekávaná šířka pásma sítě (Mb/s) |
|---|---|---|---|---|---|---|
| Standard_D11 | 2  | 14  | 100 | 6 000 / 93 / 46    | 8/8x500   | 2/1000 |
| Standard_D12 | 4  | 28  | 200 | 12 000 / 187 / 93  | 16/16x500 | 4/2000 |
| Standard_D13 | 8  | 56  | 400 | 24 000 / 375 / 187 | 32/32x500 | 8/4000 |
| Standard_D14 | 16 | 112 | 800 | 48 000 / 750 / 375 | 64/64x500 | 8/8000 |

<sup>1</sup> Rodina virtuálních zařízení může běžet na jednom z následujících procesorů: 2.2 GHz Intel Xeon® E5-2660 v2, 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) nebo 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

## <a name="preview-dc-series"></a>Náhled: DC-série

**Novější velikost doporučení:** [DCsv2-série](dcv2-series.md)

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Podporováno

Řada DC využívá nejnovější generaci 3,7GHz procesoru Intel XEON E-2176G s technologií SGX a s technologií Intel Turbo Boost může být až 4,7 GHz. 

| Velikost          | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových připojení / Očekávaná šířka pásma sítě (Mb/s) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1 500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |

> [!IMPORTANT]
>
> Virtuální servery řady DC jsou [virtuální mise generace 2](./linux/generation-2.md#creating-a-generation-2-vm) a podporují jenom `Gen2` image.


### <a name="ds-series"></a>DS-series  

**Novější velikost doporučení:** [Dsv3-série](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a protemplovaná propustnost úložiště: IOPS/MBps (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS/MBps | Maximální počet síťových připojení/očekávaná šířka pásma sítě (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standard_DS1 | 1 | 3,5 | 7  | 4  | 4000/32 (43)    | 3200/32   | 2/500  |
| Standard_DS2 | 2 | 7   | 14 | 8  | 8000/64 (86)    | 6400/64   | 2/1000 |
| Standard_DS3 | 4 | 14  | 28 | 16 | 16000/128 (172) | 12800/128 | 4/2000 |
| Standard_DS4 | 8 | 28  | 56 | 32 | 32000/256 (344) | 25600/256 | 8/4000 |

<sup>1</sup> Rodina virtuálních zařízení může běžet na jednom z následujících procesorů: 2.2 GHz Intel Xeon® E5-2660 v2, 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) nebo 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS-series - optimalizovaná paměť  

**Novější velikost doporučení:** [Dsv3-série](dv3-dsv3-series.md)

ACU: 160-250 <sup>1,2</sup>

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost úložiště v mezipaměti a protemplovaná propustnost úložiště: IOPS/MBps (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS/MBps | Maximální počet síťových připojení/očekávaná šířka pásma sítě (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standard_DS11 | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/64   | 2/1000 |
| Standard_DS12 | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/128 | 4/2000 |
| Standard_DS13 | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/256 | 8/4000 |
| Standard_DS14 | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/512 | 8/8000 |

<sup>1</sup> Maximální propustnost disku (IOPS nebo MBPs) možná s virtuálním počítačem řady DS může být omezena počtem, velikostí a prokládáním připojených disků.  Podrobnosti naleznete v tématu návrh vysokého výkonu pro [Windows](windows/premium-storage-performance.md) nebo [Linux](linux/premium-storage-performance.md).
<sup>2</sup> VM Rodina může běžet na jednom z následujících procesorů: 2.2 GHz Intel Xeon® E5-2660 v2, 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) nebo 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ls-series"></a>Řada Ls

Řada Ls-series nabízí až 32 virtuálních procesorů a využívá [řadu procesorů Intel® Xeon® E5 v3](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Řada Ls-series má stejný výkon procesoru jako řady G/GS-series a 8 GiB paměti na virtuální procesor.

Řada Ls nepodporuje vytvoření místní mezipaměti pro zvýšení viposlužby dosažitelné trvalými datovými disky. Vysoká propustnost a viposlužby na místním disku činí virtuální počítače řady Ls ideální pro úložiště NoSQL, jako jsou Apache Cassandra a MongoDB, které replikují data napříč více virtuálními počítači, aby bylo dosaženo trvalosti v případě selhání jednoho virtuálního počítače.

ACU: 180–240

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Není podporováno

| Velikost | Virtuální procesory | Paměť (GiB) | Dočasné skladování (GiB) | Max. datových disků | Maximální propustnost úložiště (IOPS/MBps) | Maximální propustnost disku bez mezipaměti (IOPS/MBps) | Maximální počet síťových připojení/očekávaná šířka pásma sítě (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standard_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| Standard_L32s&nbsp;<sup>1</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

Maximální propustnost disku s virtuálními zařízeními řady Ls může být omezena počtem, velikostí a prokládáním všech připojených disků. Podrobnosti naleznete v tématu návrh vysokého výkonu pro [Windows](windows/premium-storage-performance.md) nebo [Linux](linux/premium-storage-performance.md).

<sup>1</sup> Instance je izolována na hardware vyhrazený pro jednoho zákazníka.

### <a name="gs-series"></a>Řada GS

ACU: 180 - 240 <sup>1</sup>

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS/MBps | Maximální počet síťových připojení/očekávaná šířka pásma sítě (Mb/s) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 | 2 | 28  | 56  | 8  | 10000/100 (264)  | 5000/ 125  | 2/2000 |
| Standard_GS2 | 4 | 56  | 112 | 16 | 20000/200 (528)  | 10000/ 250 | 2/4000 |
| Standard_GS3 | 8 | 112 | 224 | 32 | 40000/400 (1056) | 20000/ 500 | 4/8000 |
| Standard_GS4&nbsp;<sup>3</sup> | 16 | 224 | 448 | 64 | 80000/800 (2112) | 40000/1000 | 8/16000 |
| Standard_GS5&nbsp;<sup>2, 3.&nbsp;</sup> | 32 | 448 |896 | 64 |160000/1600 (4224) | 80000/2000 | 8/20000 |

<sup>1</sup> Maximální propustnost disku (IOPS nebo MB/s) možná u virtuálního počítače řady GS může být omezena počtem, velikostí a prokládáním připojených disků. Podrobnosti naleznete v tématu návrh vysokého výkonu pro [Windows](windows/premium-storage-performance.md) nebo [Linux](linux/premium-storage-performance.md).

<sup>2</sup> Instance je izolována na hardware vyhrazený pro jednoho zákazníka.

<sup>3</sup> K dispozici jsou omezené velikosti jader.

<br>

### <a name="g-series"></a>G-series

ACU: 180–240

Úložiště Premium: Není podporováno

Ukládání do mezipaměti úložiště Premium: Není podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/Read MBps/Write MB/B/b/s | Maximální počet datových disků/propustnost: IOPS | Maximální počet síťových připojení/očekávaná šířka pásma sítě (Mb/s) |
|---|---|---|---|---|---|---|
| Standard_G1  | 2  | 28  | 384  | 6 000 / 93 / 46    | 8/8x500   | 2/2000  |
| Standard_G2  | 4  | 56  | 768  | 12 000 / 187 / 93  | 16/16x500 | 2/4000  |
| Standard_G3  | 8  | 112 | 1536 | 24 000 / 375 / 187 | 32/32x500 | 4/8000  |
| Standard_G4  | 16 | 224 | 3072 | 48 000 / 750 / 375 | 64/64x500 | 8/16000 |
| Standard_G5&nbsp;<sup>1</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> Instance je izolována na hardware vyhrazený pro jednoho zákazníka.
<br>

## <a name="nv-series"></a>Řada NV
**Novější velikost doporučení:** [NVv3-série](nvv3-series.md) a [NVv4-série](nvv4-series.md)

Virtuální stroje řady NV jsou poháněny grafickými procesory [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) a technologií NVIDIA GRID pro aplikace s akcelerací stolních počítačů a virtuální plochy, kde jsou zákazníci schopni vizualizovat svá data nebo simulace. Uživatelé jsou schopni vizualizovat své pracovní postupy náročné na grafiku na instancích NV získat vynikající grafické schopnosti a navíc spustit jednu přesnost úlohy, jako je kódování a vykreslování. Virtuální počítače řady NV jsou také napájeny procesory Intel Xeon E5-2690 v3 (Haswell).

Každý GPU v instancích NV je dodáván s licencí GRID. Tato licence poskytuje flexibilitu používat instanci NV jako virtuální pracovní stanici pro jednoho uživatele nebo 25 souběžných uživatelů se může připojit k virtuálnímu počítači pro scénář virtuální aplikace.

Úložiště Premium: Není podporováno

Ukládání do mezipaměti úložiště Premium: Není podporováno

Migrace za provozu: Není podporována

Aktualizace pro zachování paměti: Není podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet | Virtuální pracovní stanice | Virtuální aplikace |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = polovina karty M60.
<br>

## <a name="other-sizes"></a>Jiné velikosti

* [Obecné účely](sizes-general.md)
* [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
* [Optimalizované z hlediska paměti](sizes-memory.md)
* [Optimalizované z hlediska úložiště](sizes-storage.md)
* [GPU](sizes-gpu.md)
* [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.

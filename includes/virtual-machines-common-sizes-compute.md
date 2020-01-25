---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 16a0fa29c067262f1794528b16abfce662d05605
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76749013"
---
<!-- F-series, Fs-series* -->

Velikosti virtuálních počítačů optimalizované pro výpočty mají vysoký poměr PROCESORů k paměti. Tyto velikosti jsou vhodné pro webové servery se středním provozem, síťová zařízení, dávkové procesy a aplikační servery. Tento článek poskytuje informace o počtu vCPU, datových discích a síťových adaptérů. Obsahuje také informace o propustnosti úložiště a šířce pásma sítě pro každou velikost v tomto seskupení.

Fsv2-Series je založená na procesoru Intel® Xeon® Platinum 8168. Nabízí trvalou veškerou rychlost Turbo, 3,4 GHz a maximální frekvenci s jedním jádrem 3,7 GHz. Pokyny pro procesory Intel® AVX-512 jsou novinkou na škálovatelných procesorech Intel. Tyto pokyny poskytují až dvojnásobné zvýšení výkonu pro úlohy vektorového zpracování na operacích s plovoucí desetinnou čárkou a dvojitou přesností. Jinými slovy jsou skutečně rychlé pro všechny výpočetní úlohy.

Za nižší ceníkové sazby za hodinu je Fsv2-Series nejlepší hodnotou cena – výkon v portfoliu Azure na základě výpočetní jednotky Azure (ACU) na vCPU.

## <a name="fsv2-series-sup1sup"></a>Fsv2-Series <sup>1</sup>

ACU: 195-210

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

| Velikost             | vCPU | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000/31 (32)           | 3200/47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000/63 (64)           | 6400/95                | 2 / 1750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000 / 127 (128)        | 12800/190              | 4 / 3500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000/255 (256)        | 25600 / 380              | 4 / 7000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000/512 (512)        | 51200 / 750              | 8 / 14000              |
| Standard_F48s_v2 | 48     | 96          | 384            | 32             | 96000/768 (768)        | 76800/1100             | 8 / 21000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 / 1024 (1024)     | 80000/1100             | 8 / 28000              |
| Standard_F72s_v2<sup>2,&nbsp;3</sup> | 72 | 144 | 576         | 32             | 144000/1152 (1520)     | 80000/1100             | 8 / 30000              |

<sup>1</sup> virtuální počítače Fsv2-Series jsou součástí technologie Intel® technologie Hyper-Threading.

<sup>2</sup> použití více než 64 vCPU vyžaduje jeden z těchto podporovaných hostovaných operačních systémů:
- Windows Server 2016 nebo novější
- Ubuntu 16,04 LTS nebo novější s vyladěným jádrem Azure (4,15 jádro nebo novější)
- SLES 12 SP2 nebo novější
- RHEL nebo CentOS verze 6,7 až 6,10 s nainstalovaným balíčkem LIS (nebo novějším) od Microsoftu
- RHEL nebo CentOS verze 7,3 s nainstalovaným balíčkem aplikace LIS verze 4.2.1 (nebo novější)
- RHEL nebo CentOS verze 7,6 nebo novější
- Oracle Linux s UEK4 nebo novějším
- Debian 9 s jádrem pro porty, Debian 10 nebo novější
- CoreOS s jádrem 4,14 nebo novějším

<sup>3</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.

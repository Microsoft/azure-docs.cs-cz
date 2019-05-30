---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: ec718449e9f9db3f816d327f3d2483de813d755c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391413"
---
<!-- F-series, Fs-series* -->

COMPUTE optimalizované velikosti virtuálních počítačů mají vysoký poměr procesorů k paměti a jsou vhodné pro webové servery středním provozem, síťová zařízení, dávkové procesy a aplikační servery. Tento článek obsahuje informace o počtu virtuálních procesorů, datové disky a síťové adaptéry, jakož i úložiště propustnost a šířku pásma sítě pro jednotlivé velikosti v této skupině.

Řada Fsv2 je založená na procesorech Intel® Xeon® Platinum 8168, poskytuje funkci dlouhodobě všech základních Turbo rychlost 3.4GHz a frekvenci maximální turbo jednojádrový 3,7 GHz. Intel® AVX-512 pokyny, které jsou nové na procesorech Intel škálovatelné, bude poskytovat až 2 X zvýšení výkonu vektoru zpracování úloh na jednoduché i dvojité přesnosti s plovoucí desetinnou čárkou bodu operace. Jinými slovy je velmi rychlá pro všechny výpočetní úlohy. 

Za nižší ceníkové za každou hodinu je Fsv2-series nejlepší poměr cena – výkon v portfoliu Azure, na Azure Compute jednotek (ACU) na virtuální procesor.

## <a name="fsv2-series-sup1sup"></a>Řada Fsv2 <sup>1</sup>

ACU: 195 - 210

Premium Storage:  Podporováno

Premium Storage ukládání do mezipaměti:  Podporováno

| Velikost             | virtuálních procesorů | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 / 31 (32)           | 3200 / 47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 / 63 (64)           | 6400 / 95                | 2 / 1750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000 / 127 (128)        | 12800 / 190              | 4 / 3500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 / 255 (256)        | 25600 / 380              | 4 / 7000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 / 512 (512)        | 51200 / 750              | 8 / 14000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 / 1024 (1024)     | 80000 / 1100             | 8 / 28000              |
| Standard_F72s_v2<sup>2, 3</sup> | 72 | 144 | 576         | 32             | 144000 / 1152 (1520)     | 80000 / 1100             | 8 / 30000              |


<sup>1</sup> funkcí technologie Intel® Hyper-Threading Fsv2-series Virtuálních počítačů

<sup>2</sup> více než 64 virtuálních procesorů se vyžaduje jednu z těchto podporovaných hostovaných operačních systémů: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 a Red Hat Enterprise Linux, CentOS 7.3 nebo Oracle Linux 7.3 s LIS 4.2.1

<sup>3</sup> instance je izolovaná na hardware vyhrazený pro jediného zákazníka.
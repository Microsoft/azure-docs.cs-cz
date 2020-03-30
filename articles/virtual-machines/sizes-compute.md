---
title: Velikosti virtuálních počítačů Azure – optimalizované pro výpočetní výkon | Dokumenty společnosti Microsoft
description: Uvádí různé velikosti optimalizované pro výpočetní prostředky, které jsou k dispozici pro virtuální počítače v Azure. Uvádí informace o počtu virtuálních procesorů, datových disků a síťových síťových připojení, jakož i propustnost úložiště a šířku pásma sítě pro velikosti v této řadě.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: d709d621341ef14ec158ed5af1c2df4297d66b8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77493628"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>Výpočetní optimalizované velikosti virtuálních strojů

Velikosti virtuálních počítačů optimalizované pro výpočetní výkon mají vysoký poměr procesoru k paměti. Tyto velikosti jsou vhodné pro webové servery se středním provozem, síťová zařízení, dávkové procesy a aplikační servery. Tento článek obsahuje informace o počtu virtuálních procesorů, datových disků a nic. Obsahuje také informace o propustnosti úložiště a šířce pásma sítě pro každou velikost v tomto seskupení.

[Řada Fsv2](fsv2-series.md) je založena na procesoru Intel® Xeon® Platinum 8168. Je vybaven trvalým jádrem Turbo taktovací rychlost 3,4 GHz a maximální jednojádrovou turbo frekvencí 3,7 GHz. Intel® AVX-512 pokyny jsou nové na Intel scalable procesory. Tyto pokyny poskytují až 2x zvýšení výkonu pro úlohy vektorového zpracování na operacích s jednou i dvojitou přesností s plovoucí desetinnou desetinnou desetinnou desetinnou desetinnou desetinnou desetinnou desetinnou desetinnou desetinnou desetinnou desetinnou stou. Jinými slovy, jsou opravdu rychlé pro všechny výpočetní úlohy.

Při nižší ceníkové ceně za hodinu je řada Fsv2 nejlepší hodnotou v cenovém výkonu v portfoliu Azure na základě výpočetní jednotky Azure (ACU) za virtuální procesor.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.
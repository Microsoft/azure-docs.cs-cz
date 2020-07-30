---
title: Velikosti virtuálních počítačů Azure – optimalizované pro výpočty | Microsoft Docs
description: Uvádí různé výpočetní optimalizované velikosti, které jsou dostupné pro virtuální počítače v Azure. Uvádí informace o počtu vCPU, datových discích a síťových adaptérů a propustnosti úložiště a šířce pásma sítě pro velikosti v této sérii.
author: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 37b7e47d4cf70f6f4fb4e6a9364c12bbe69daf9d
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423894"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>Výpočetní optimalizované velikosti virtuálních počítačů

Velikosti virtuálních počítačů optimalizované pro výpočty mají vysoký poměr PROCESORů k paměti. Tyto velikosti jsou vhodné pro webové servery se středním provozem, síťová zařízení, dávkové procesy a aplikační servery. Tento článek poskytuje informace o počtu vCPU, datových discích a síťových adaptérů. Obsahuje také informace o propustnosti úložiště a šířce pásma sítě pro každou velikost v tomto seskupení.

[Fsv2-Series](fsv2-series.md) běží na procesorech Intel® Xeon® Platinum 8272CL (Cascade Lake) a na procesorech Intel® Xeon® Platinum 8168 (Skylake). Nabízí trvalou veškerou rychlost Turbo, 3,4 GHz a maximální frekvenci s jedním jádrem 3,7 GHz. Pokyny pro procesory Intel® AVX-512 jsou novinkou na škálovatelných procesorech Intel. Tyto pokyny poskytují až dvojnásobné zvýšení výkonu pro úlohy vektorového zpracování na operacích s plovoucí desetinnou čárkou a dvojitou přesností. Jinými slovy jsou skutečně rychlé pro všechny výpočetní úlohy.

Za nižší ceníkové sazby za hodinu je Fsv2-Series nejlepší hodnotou cena – výkon v portfoliu Azure na základě výpočetní jednotky Azure (ACU) na vCPU.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.

Další informace o tom, jak Azure vyjmenovává své virtuální počítače, najdete v tématu zásady [vytváření názvů virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machines/vm-naming-conventions).

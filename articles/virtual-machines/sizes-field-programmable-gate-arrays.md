---
title: Velikosti virtuálních počítačů Azure pro pole s programovatelnými poli brány (FPGA)
description: Uvádí různé dostupné velikosti FPGA optimalizované pro virtuální počítače v Azure. Uvádí informace o počtu vCPU, datových discích a síťových rozhraních a propustnosti úložiště a šířce pásma sítě pro velikosti v této sérii.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: vikancha
ms.openlocfilehash: b05c3198f6c3d84f852d9535a3cf0b67f66e01f6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417246"
---
# <a name="fpga-optimized-virtual-machine-sizes"></a>FPGA optimalizované velikosti virtuálních počítačů

FPGA optimalizované velikosti virtuálních počítačů jsou specializované virtuální počítače dostupné s jedním nebo několika FPGA. Tyto velikosti jsou navržené pro úlohy náročné na výpočetní výkon. Tento článek poskytuje informace o počtu a typu FPGA, vCPU, datových disků a síťových karet. Propustnost úložiště a šířka pásma sítě jsou také zahrnuty pro každou velikost v tomto seskupení.

- Velikosti [řady NP-Series](np-series.md) jsou optimalizované pro úlohy, včetně odvození strojového učení, překódování videa a & analýzy databáze. Řada NP-Series využívá akcelerátory Xilinx U250.


## <a name="deployment-considerations"></a>Aspekty nasazování

- Dostupnost virtuálních počítačů řady N-Series najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).

- Virtuální počítače řady N-Series jde nasadit jenom v modelu nasazení Správce prostředků.

- Pokud chcete nasadit více než několik virtuálních počítačů řady N-Series, vezměte v úvahu předplatné s průběžnými platbami nebo jiné možnosti nákupu. Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), můžete použít pouze omezený počet výpočetních jader Azure.

- V rámci vašeho předplatného Azure možná budete muset zvýšit kvótu jader (na oblast) a zvýšit samostatnou kvótu pro jádro NP. Chcete-li požádat o zvýšení kvóty, [otevřete online žádost o zákaznickou podporu](../azure-portal/supportability/how-to-create-azure-support-request.md) zdarma. Výchozí omezení se můžou lišit v závislosti na vaší kategorii předplatného.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované pro výpočty](sizes-compute.md)
- [Výpočetní prostředí GPU s akcelerací](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.

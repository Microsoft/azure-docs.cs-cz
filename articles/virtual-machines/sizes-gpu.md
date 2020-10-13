---
title: Velikosti virtuálních počítačů Azure – GPU | Microsoft Docs
description: Zobrazuje seznam různých velikostí GPU, které jsou dostupné pro virtuální počítače v Azure. Uvádí informace o počtu vCPU, datových discích a síťových rozhraních a propustnosti úložiště a šířce pásma sítě pro velikosti v této sérii.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: fb534ae4a59c9a5c87a7eb7ab268a40b9771fdb2
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876222"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Velikosti virtuálních počítačů optimalizované pro grafické procesory

Velikosti virtuálních počítačů optimalizované pro GPU jsou specializované virtuální počítače, které jsou k dispozici s jedním, více nebo zlomkem GPU. Tyto velikosti jsou navržené pro úlohy náročné na výpočetní výkon, náročné na grafiku a vizualizaci. Tento článek poskytuje informace o počtu a typu GPU, vCPU, datových disků a síťových karet. Propustnost úložiště a šířka pásma sítě jsou také zahrnuty pro každou velikost v tomto seskupení.

- Velikosti řady [NCv3-Series](ncv3-series.md) a [NC T4_v3-Series](nct4-v3-series.md) jsou optimalizované pro aplikace náročné na výpočetní výkon GPU. Mezi příklady patří aplikace a simulace založené na CUDA a OpenCL, AI a obsáhlý Learning. Řada NC V3 V3-Series se zaměřuje na odvozené úlohy, které využívají GPU GPU Tesla T4 a procesor AMD EPYC2 Řím. NCv3-Series se zaměřuje na vysoce výkonné výpočetní úlohy a úlohy AI s grafickým procesorem Tesla V100u NVIDIA.

- Velikost [řady NDv2-Series](ndv2-series.md) se zaměřuje na škálovatelné a škálovatelné aplikace pro výuku s hloubkovým učením. NDv2-Series používá NVIDIA Volta V100 a procesor Intel Xeon Platinum 8168 (Skylake).

- Velikosti [NV-Series](nv-series.md) a [NVv3-Series](nvv3-series.md) jsou optimalizované a navržené pro vzdálené vizualizace, streamování, hry, kódování a VDI, a to pomocí platforem, jako je OpenGL a DirectX. Tyto virtuální počítače jsou řízené grafickým procesorem NVIDIA Tesla M60.

- [NVv4-Series](nvv4-series.md) Velikosti virtuálních počítačů optimalizované a navržené pro VDI a vzdálenou vizualizaci. U rozděleného GPU nabízí NVv4 správnou velikost pro úlohy, které vyžadují menší prostředky GPU. Tyto virtuální počítače jsou řízené grafickým procesorem AMD Radeon Instinct MI25. Virtuální počítače s NVv4 v současné době podporují jenom hostovaný operační systém Windows.

## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

Aby bylo možné využívat možnosti GPU pro virtuální počítače řady Azure N-Series, je nutné nainstalovat ovladače NVIDIA nebo AMD GPU.

- Pro virtuální počítače, které jsou zajištěné grafickými procesory NVIDIA, nainstaluje [rozšíření ovladače NVIDIA GPU](./extensions/hpccompute-gpu-windows.md) příslušné ovladače NVIDIA CUDA nebo Grid. Nainstalujte nebo spravujte rozšíření pomocí Azure Portal nebo nástrojů, jako jsou šablony Azure PowerShell nebo Azure Resource Manager. Podporované operační systémy a kroky nasazení najdete v [dokumentaci k rozšíření ovladače GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) . Obecné informace o rozšířeních virtuálních počítačů najdete v tématu [rozšíření a funkce virtuálních počítačů Azure](./extensions/overview.md).   

   Alternativně můžete ovladače NVIDIA GPU nainstalovat ručně. Další informace najdete v tématu [instalace ovladačů NVIDIA GPU pro virtuální počítače řady n-Series s Windows](./windows/n-series-driver-setup.md) nebo [instalace ovladačů NVIDIA GPU pro virtuální počítače řady n-Series s operačním systémem Linux](./linux/n-series-driver-setup.md) pro podporované operační systémy, ovladače, instalace a postupy ověření.

- Pro virtuální počítače, které jsou založené na technologii AMD GPU, najdete informace v tématu [instalace ovladačů AMD GPU na virtuálních počítačích řady N-Series](./windows/n-series-amd-driver-setup.md) se systémem Windows pro podporované operační systémy, ovladače, instalace a postupy ověření.

## <a name="deployment-considerations"></a>Aspekty nasazování

- Dostupnost virtuálních počítačů řady N-Series najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).

- Virtuální počítače řady N-Series jde nasadit jenom v modelu nasazení Správce prostředků.

- Virtuální počítače řady N-Series se liší v typu Azure Storage podporují jejich disky. Virtuální počítače síťového adaptéru a NV podporují jenom disky virtuálních počítačů, které jsou založené na standardu Disk Storage (HDD). Virtuální počítače NCv2, NCv3, ND, NDv2 a NVv2 podporují pouze disky virtuálních počítačů, které jsou zajištěny pomocí Premium Disk Storage (SSD).

- Pokud chcete nasadit více než několik virtuálních počítačů řady N-Series, vezměte v úvahu předplatné s průběžnými platbami nebo jiné možnosti nákupu. Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), můžete použít pouze omezený počet výpočetních jader Azure.

- V rámci vašeho předplatného Azure možná budete muset zvýšit kvótu jader (na oblast) a zvýšit samostatnou kvótu pro NC, NCv2, NCv3, ND, NDv2, NV nebo NVv2 jádra. Chcete-li požádat o zvýšení kvóty, [otevřete online žádost o zákaznickou podporu](../azure-portal/supportability/how-to-create-azure-support-request.md) zdarma. Výchozí omezení se můžou lišit v závislosti na vaší kategorii předplatného.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované pro výpočty](sizes-compute.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.

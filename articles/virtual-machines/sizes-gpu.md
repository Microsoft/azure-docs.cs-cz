---
title: Velikosti virtuálních počítačů Azure – GPU | Microsoft Docs
description: Zobrazuje seznam různých velikostí GPU, které jsou dostupné pro virtuální počítače v Azure. Uvádí informace o počtu vCPU, datových discích a síťových rozhraních a propustnosti úložiště a šířce pásma sítě pro velikosti v této sérii.
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
ms.openlocfilehash: 3ee7a2ffed9c2c50829f237fdf4c8d3b5a6f1531
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2020
ms.locfileid: "77538768"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Velikosti virtuálních počítačů optimalizované pro GPU

Velikosti virtuálních počítačů optimalizované pro GPU jsou specializované virtuální počítače dostupné s jedním nebo několika grafickými procesory NVIDIA. Tyto velikosti jsou navržené pro úlohy náročné na výpočetní výkon, náročné na grafiku a vizualizaci. Tento článek poskytuje informace o počtu a typu GPU, vCPU, datových disků a síťových karet. Propustnost úložiště a šířka pásma sítě jsou také zahrnuty pro každou velikost v tomto seskupení.

- Velikosti [NC-](nc-series.md)Series, [NCv2-](ncv2-series.md)Series, řady [NCv3-](ncv3-series.md) Series jsou optimalizované pro aplikace a algoritmy náročné na výpočetní výkon a na síť. Mezi příklady patří aplikace a simulace založené na CUDA a OpenCL, AI a obsáhlý Learning. NCv3-Series se zaměřuje na vysoce výkonné výpočetní úlohy s grafickým procesorem NVIDIA Tesla V100. Řada NC-Series používá procesor Intel Xeon E5-2690 V3 V3 (Haswell) a virtuální počítače řady NCv2-Series a NCv3-Series používají procesor Intel Xeon E5-2690 v4 (Broadwell).

- Velikosti řady [ND-Series](nd-series.md)a [NDv2-Series](ndv2-series.md) se zaměřují na scénáře školení a odvozování pro hloubkové učení. Využívají GPU NVIDIA Tesla P40 a procesor Intel Xeon E5-2690 v4 (Broadwell). NDv2-Series používá procesor Intel Xeon Platinum 8168 (Skylake).

- Velikosti [NV-Series](nv-series.md) a [NVv3-Series](nvv3-series.md) jsou optimalizované a navržené pro vzdálené vizualizace, streamování, hry, kódování a VDI, a to pomocí platforem, jako je OpenGL a DirectX. Tyto virtuální počítače jsou řízené grafickým procesorem NVIDIA Tesla M60.

- [NVv4-Series](nvv4-series.md) Velikosti virtuálních počítačů optimalizované a navržené pro VDI a vzdálenou vizualizaci. U rozděleného GPU nabízí NVv4 správnou velikost pro úlohy, které vyžadují menší prostředky GPU. Tyto virtuální počítače jsou řízené grafickým procesorem AMD Radeon Instinct MI25.

## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

Aby bylo možné využít možnosti GPU pro virtuální počítače řady Azure N-Series, je nutné nainstalovat ovladače GPU NVIDIA.

[Rozšíření ovladače NVIDIA GPU](/azure/virtual-machines/extensions/hpccompute-gpu-windows) nainstaluje vhodné ovladače NVIDIA CUDA nebo Grid na virtuální počítač řady N-Series. Nainstalujte nebo spravujte rozšíření pomocí Azure Portal nebo nástrojů, jako jsou šablony Azure PowerShell nebo Azure Resource Manager. Podporované operační systémy a kroky nasazení najdete v [dokumentaci k rozšíření ovladače GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) . Obecné informace o rozšířeních virtuálních počítačů najdete v tématu [rozšíření a funkce virtuálních počítačů Azure](/azure/virtual-machines/extensions/overview).

Pokud se rozhodnete nainstalovat ovladače GPU NVIDIA ručně, přečtěte si téma Instalace ovladače GPU [řady n-Series pro Windows](/azure/virtual-machines/windows/n-series-driver-setup) nebo [n-Series Setup Driver Setup pro Linux](/azure/virtual-machines/linux/n-series-driver-setup) pro podporované operační systémy, ovladače, instalace a postupy ověření.

## <a name="deployment-considerations"></a>Aspekty nasazování

- Dostupnost virtuálních počítačů řady N-Series najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).

- Virtuální počítače řady N-Series jde nasadit jenom v modelu nasazení Správce prostředků.

- Virtuální počítače řady N-Series se liší v typu Azure Storage podporují jejich disky. Virtuální počítače síťového adaptéru a NV podporují jenom disky virtuálních počítačů, které jsou založené na standardu Disk Storage (HDD). Virtuální počítače NCv2, NCv3, ND, NDv2 a NVv2 podporují pouze disky virtuálních počítačů, které jsou zajištěny pomocí Premium Disk Storage (SSD).

- Pokud chcete nasadit více než několik virtuálních počítačů řady N-Series, vezměte v úvahu předplatné s průběžnými platbami nebo jiné možnosti nákupu. Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), můžete použít pouze omezený počet výpočetních jader Azure.

- V rámci vašeho předplatného Azure možná budete muset zvýšit kvótu jader (na oblast) a zvýšit samostatnou kvótu pro NC, NCv2, NCv3, ND, NDv2, NV nebo NVv2 jádra. Chcete-li požádat o zvýšení kvóty, [otevřete online žádost o zákaznickou podporu](/../azure-supportability/how-to-create-azure-support-request.md) zdarma. Výchozí omezení se můžou lišit v závislosti na vaší kategorii předplatného.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.

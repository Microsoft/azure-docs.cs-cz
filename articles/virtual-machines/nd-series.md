---
title: ND-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače řady ND-Series.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 5516e17a74fffd28f432df305dfa7f592644d4a7
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611568"
---
# <a name="nd-series"></a>Řada ND

Virtuální počítače řady ND-Series jsou nově přidané do rodiny GPU navržené pro AI a pro úlohy s hloubkovým učením. Nabízí vynikající výkon pro školení a odvozování. Instance ND jsou napájené pomocí [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU a procesorů Intel Xeon E5-2690 v4 (Broadwell). Tyto instance poskytují vynikající výkon pro operace s plovoucí desetinnou čárkou s jednoduchou přesností pro úlohy AI s využitím Microsoft Cognitive Toolkit, TensorFlow, Caffe a dalších platforem. Řada ND-Series také nabízí mnohem větší velikost paměti GPU (24 GB), což umožňuje přizpůsobit mnohem větší neuronové modely .NET. Podobně jako řada NC-Series nabízí konfiguraci se sekundární sítí s nízkou latencí a vysokou propustností prostřednictvím RDMA a InfiniBand konektivitu, takže můžete spouštět rozsáhlé školicí úlohy zahrnující mnoho GPU.

[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): podporováno<br>
[Migrace za provozu](maintenance-and-updates.md): nepodporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): nepodporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
[Akcelerované síťové služby](../virtual-network/create-vm-accelerated-networking-cli.md): nepodporováno<br>
[Dočasné disky s operačním systémem](ephemeral-os-disks.md): podporované <br>
NVIDIA NVLink Interconnect: nepodporováno<br>

> [!IMPORTANT]
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

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

Aby bylo možné využít možnosti GPU pro virtuální počítače řady Azure N-Series, je nutné nainstalovat ovladače GPU NVIDIA.

[Rozšíření ovladače NVIDIA GPU](./extensions/hpccompute-gpu-windows.md) nainstaluje vhodné ovladače NVIDIA CUDA nebo Grid na virtuální počítač řady N-Series. Nainstalujte nebo spravujte rozšíření pomocí Azure Portal nebo nástrojů, jako jsou šablony Azure PowerShell nebo Azure Resource Manager. Podporované operační systémy a kroky nasazení najdete v [dokumentaci k rozšíření ovladače GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) . Obecné informace o rozšířeních virtuálních počítačů najdete v tématu [rozšíření a funkce virtuálních počítačů Azure](./extensions/overview.md).

Pokud se rozhodnete nainstalovat ovladače GPU NVIDIA ručně, přečtěte si téma Instalace ovladače GPU [řady n-Series pro Windows](./windows/n-series-driver-setup.md) nebo [n-Series Setup Driver Setup pro Linux](./linux/n-series-driver-setup.md) pro podporované operační systémy, ovladače, instalace a postupy ověření.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU.](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.

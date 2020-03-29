---
title: Řada NCv2 – virtuální počítače Azure
description: Specifikace pro virtuální chod řady NCv2.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 3643fbabef08d890ce121d41a9bc1eb40c88459d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273915"
---
# <a name="ncv2-series"></a>Řada NCv2

Virtuální procesory řady NCv2 jsou poháněny grafickými procesory [NVIDIA Tesla P100.](https://www.nvidia.com/data-center/tesla-p100/) Tyto gpu mohou poskytovat více než 2x výpočetní výkon řady NC. Zákazníci mohou využít těchto aktualizovaných grafických procesorů pro tradiční úlohy HPC, jako je modelování nádrží, sekvenování DNA, analýza bílkovin, simulace Monte Carlo a další. Kromě gpu jsou virtuální počítače řady NCv2 také napájeny procesory Intel Xeon E5-2690 v4 (Broadwell).

Konfigurace NC24rs v2 poskytuje síťové rozhraní s nízkou latencí a vysokou propustností optimalizované pro úzce propojené paralelní výpočetní úlohy.

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Podporováno

Migrace za provozu: Není podporována

Aktualizace pro zachování paměti: Není podporováno

> [!IMPORTANT]
> Pro tuto řadu virtuálních počítače je kvóta virtuálních procesorů (jádra) ve vašem předplatném zpočátku nastavena na 0 v každé oblasti. [Požádejte o zvýšení kvóty virtuálního procesoru](../azure-supportability/resource-manager-core-quotas-request.md) pro tuto řadu v [dostupné oblasti](https://azure.microsoft.com/regions/services/).
>
| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální propustnost disku bez mezipaměti: IOPS/MBps | Maximální počet síťových karet |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v2    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v2   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2* | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = jedna karta P100.

*Podpora RDMA

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

Chcete-li využít funkce GPU virtuálních počítačů řady Azure N, musí být nainstalovány ovladače GPU NVIDIA.

[Rozšíření ovladače GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) nainstaluje příslušné ovladače NVIDIA CUDA nebo GRID na virtuální počítač řady N. Nainstalujte nebo spravujte rozšíření pomocí portálu Azure nebo nástrojů, jako jsou šablony Azure PowerShellnebo Azure Resource Manager. Informace o podporovaných operačních systémech a krocích nasazení naleznete v [dokumentaci k rozšíření ovladače GPU NVIDIA.](./extensions/hpccompute-gpu-windows.md) Obecné informace o rozšířeních virtuálních virtuálních zařízení najdete v tématu [Rozšíření a funkce virtuálních strojů Azure](./extensions/overview.md).

Pokud se rozhodnete nainstalovat ovladače GPU NVIDIA ručně, přečtěte si informace [o nastavení ovladače GPU řady N pro Windows](./windows/n-series-driver-setup.md) nebo [n-series pro Linux](./linux/n-series-driver-setup.md) pro podporované operační systémy, ovladače, instalaci a kroky ověření.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.

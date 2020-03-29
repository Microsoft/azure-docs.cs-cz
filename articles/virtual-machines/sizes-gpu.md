---
title: Velikosti virtuálních zařízení Azure – GPU | Dokumenty společnosti Microsoft
description: Uvádí různé velikosti optimalizované pro GPU, které jsou k dispozici pro virtuální počítače v Azure. Obsahuje informace o počtu virtuálních procesorů, datových disků a síťových síťových karty, jakož i propustnost úložiště a šířku pásma sítě pro velikosti v této řadě.
services: virtual-machines
documentationcenter: ''
author: vikancha
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
ms.openlocfilehash: 7e1e0d488844a94bd0be2b91398678e620295729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913578"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Velikosti virtuálních strojů optimalizované pro GPU

Velikosti virtuálních počítačů optimalizované pro GPU jsou specializované virtuální počítače dostupné s jedním nebo více grafickými procesory NVIDIA. Tyto velikosti jsou určeny pro náročné výpočetní, grafické náročné a vizualizační úlohy. Tento článek obsahuje informace o počtu a typu objektů GPU, virtuálních procesorů, datových disků a nic. Pro každou velikost v tomto seskupení jsou zahrnuty také propustnost úložiště a šířka pásma sítě.

- [NC-series](nc-series.md), [NCv2-series](ncv2-series.md), [NCv3-series](ncv3-series.md) velikosti jsou optimalizovány pro aplikace a algoritmy náročné na výpočetní výkon a síť. Některé příklady jsou CUDA a OpenCL založené aplikace a simulace, AI a Deep Learning. Řada NCv3 se zaměřuje na vysoce výkonné výpočetní úlohy s grafickým procesorem Tesla V100 společnosti NVIDIA. Řada NC používá procesor Intel Xeon E5-2690 v3 2.60GHz v3 (Haswell) a virtuální zařízení řady NCv2 a NCv3 používají procesor Intel Xeon E5-2690 v4 (Broadwell).

- [Nd-series](nd-series.md), a [NDv2-série](ndv2-series.md) velikosti jsou zaměřeny na školení a odvození scénáře pro hluboké učení. Používají GPU NVIDIA Tesla P40 a procesor Intel Xeon E5-2690 v4 (Broadwell). Řada NDv2 používá procesor Intel Xeon Platinum 8168 (Skylake).

- [Velikosti řady NV](nv-series.md) a [NVv3](nvv3-series.md) jsou optimalizovány a navrženy pro vzdálené vizualizace, streamování, hraní her, kódování a scénáře VDI pomocí architektur, jako jsou OpenGL a DirectX. Tyto virtuální měny jsou podporovány gpu NVIDIA Tesla M60.

- [Řada NVv4](nvv4-series.md) Velikosti virtuálních počítačových zařízení optimalizované a navržené pro VDI a vzdálenou vizualizaci. S rozdělenými GPU nabízí NVv4 správnou velikost pro úlohy, které vyžadují menší prostředky GPU. Tyto virtuální hry jsou podporovány GPU AMD Radeon Instinct MI25. Virtuální servery NVv4 v současné době podporují pouze hostovaný operační systém Windows.

## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

Chcete-li využít funkce GPU virtuálních počítačů řady Azure N, musí být nainstalovány ovladače GPU NVIDIA.

[Rozšíření ovladače GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) nainstaluje příslušné ovladače NVIDIA CUDA nebo GRID na virtuální počítač řady N. Nainstalujte nebo spravujte rozšíření pomocí portálu Azure nebo nástrojů, jako jsou šablony Azure PowerShellnebo Azure Resource Manager. Informace o podporovaných operačních systémech a krocích nasazení naleznete v [dokumentaci k rozšíření ovladače GPU NVIDIA.](/azure/virtual-machines/extensions/hpccompute-gpu-windows) Obecné informace o rozšířeních virtuálních virtuálních zařízení najdete v tématu [Rozšíření a funkce virtuálních strojů Azure](/azure/virtual-machines/extensions/overview).

Pokud se rozhodnete nainstalovat ovladače GPU NVIDIA ručně, přečtěte si informace [o nastavení ovladače GPU řady N pro Windows](/azure/virtual-machines/windows/n-series-driver-setup) nebo [n-series pro Linux](/azure/virtual-machines/linux/n-series-driver-setup) pro podporované operační systémy, ovladače, instalaci a kroky ověření.

## <a name="deployment-considerations"></a>Aspekty nasazování

- Dostupnost virtuálních aplikací řady N najdete v tématu [Produkty dostupné podle oblasti](https://azure.microsoft.com/regions/services/).

- Virtuální počítače řady N lze nasadit jenom v modelu nasazení Resource Manageru.

- Virtuální počítače řady N se liší v typu azure storage, které podporují pro své disky. Virtuální počítače NC a NV podporují jenom virtuální počítače, které jsou zálohovány standardním diskovým úložištěm (HDD). Virtuální počítače NCv2, NCv3, ND, NDv2 a NVv2 podporují jenom virtuální počítače, které jsou zálohovány úložištěm disků Premium (SSD).

- Pokud chcete nasadit víc než několik virtuálních zařízení řady N, zvažte předplatné s průběžným platbou nebo jiné možnosti nákupu. Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), můžete použít pouze omezený počet výpočetních jader Azure.

- Možná budete muset zvýšit kvótu jader (na oblast) v předplatném Azure a zvýšit samostatnou kvótu pro jádra NC, NCv2, NCv3, ND, NDv2, NV nebo NVv2. Chcete-li požádat o zvýšení kvóty, [otevřete online žádost o zákaznickou podporu](../azure-portal/supportability/how-to-create-azure-support-request.md) zdarma. Výchozí limity se mohou lišit v závislosti na kategorii předplatného.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.

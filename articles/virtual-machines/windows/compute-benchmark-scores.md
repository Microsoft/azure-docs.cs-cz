---
title: Výpočetní srovnávacího testu skóre pro virtuální počítače Windows | Microsoft Docs
description: Porovnání SPECint výpočetní srovnávacího testu skóre pro virtuální počítače Azure se systémem Windows Server
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 69ae72ec-e8be-4e46-a8f0-e744aebb5cc2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cynthn
ms.openlocfilehash: cb77a058e72b8f08cb8974d5ec5088bc498e071d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="compute-benchmark-scores-for-windows-vms"></a>Výpočetní srovnávacího testu skóre pro virtuální počítače Windows
Následující skóre srovnávacího testu SPECInt zobrazit výpočetní výkon pro Azure a vysoce výkonných virtuálních počítačů řazení systémem Windows Server. Výpočetní srovnávacího testu skóre jsou také k dispozici pro [virtuální počítače s Linuxem](../linux/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
> [!NOTE]
> Čísla Linux nedávno aktualizovala a obsahují více komplexní sadu virtuálních počítačů.

## <a name="a-series---compute-intensive"></a>A-series - náročné na výkon
| Velikost | vCPU | Uzly NUMA | Procesor | Spuštění | Základní míra průměr | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |Intel Xeon CPU E5-. 2670 0 @ 2.6 GHz |10 |236.1 |1.1 |
| Standard_A9 |16 |2 |Intel Xeon CPU E5-. 2670 0 @ 2.6 GHz |10 |450.3 |7.0 |
| Standard_A10 |8 |1 |Intel Xeon CPU E5-. 2670 0 @ 2.6 GHz |5 |235.6 |0.9 |
| Standard_A11 |16 |2 |Intel Xeon CPU E5-. 2670 0 @ 2.6 GHz |7 |454.7 |4.8 |

## <a name="dv2-series"></a>Dv2-series
| Velikost | vCPU | Uzly NUMA | Procesor | Spuštění | Základní míra průměr | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |83 |36.6 |2.6 |
| Standard_D2_v2 |2 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |27 |70.0 |3.7 |
| Standard_D3_v2 |4 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |19 |130.5 |4.4 |
| Standard_D4_v2 |8 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |19 |238.1 |5.2 |
| Standard_D5_v2 |16 |2 |Intel Xeon E5-2673 v3 @ 2,4 GHz |14 |460.9 |15.4 |
| Standard_D11_v2 |2 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |19 |70.1 |3.7 |
| Standard_D12_v2 |4 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |2 |132.0 |1.4 |
| Standard_D13_v2 |8 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |17 |235.8 |3.8 |
| Standard_D14_v2 |16 |2 |Intel Xeon E5-2673 v3 @ 2,4 GHz |15 |460.8 |6.5 |

## <a name="g-series-gs-series"></a>G-series, GS-series
| Velikost | vCPU | Uzly NUMA | Procesor | Spuštění | Základní míra průměr | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1, Standard_GS1 |2 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |31 |71.8 |6.5 |
| Standard_G2, Standard_GS2 |4 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |5 |133.4 |13.0 |
| Standard_G3, Standard_GS3 |8 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |6 |242.3 |6.0 |
| Standard_G4, Standard_GS4 |16 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |15 |398.9 |6.0 |
| Standard_G5, Standard_GS5 |32 |2 |Intel Xeon E5-2698B v3 @ 2 GHz |22 |762.8 |3.7 |

## <a name="h-series"></a>H-series
| Velikost | vCPU | Uzly NUMA | Procesor | Spuštění | Základní míra průměr  | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |1 |Intel Xeon E5-. 2667 v3 @ 3,2 GHz |5 |297.4 |0.9 |
| Standard_H16 |16 |2 |Intel Xeon E5-. 2667 v3 @ 3,2 GHz |5 |575.8 |6.8 |
| Standard_H8m |8 |1 |Intel Xeon E5-. 2667 v3 @ 3,2 GHz |5 |297.0 |1.2 |
| Standard_H16m |16 |2 |Intel Xeon E5-. 2667 v3 @ 3,2 GHz |5 |572.2 |3.9 |
| Standard_H16r |16 |2 |Intel Xeon E5-. 2667 v3 @ 3,2 GHz |5 |573.2 |2.9 |
| Standard_H16mr |16 |2 |Intel Xeon E5-. 2667 v3 @ 3,2 GHz |7 |569.6 |2.8 |

## <a name="about-specint"></a>O SPECint
Čísla systému Windows byly počítaný spuštěním [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) v systému Windows Server. SPECint byl spuštěn pomocí možnosti základní rychlost (výsledcích SPECint_rate2006), s jedna kopie za virtuální procesory. SPECint se skládá z 12 samostatné testy, každý spustit třikrát, trvá Medián z každého testu a je k vytvoření složeného skóre vážení. Tyto testy byly spusťte napříč více virtuálními počítači poskytnout průměrné skóre vidět.

## <a name="next-steps"></a>Další postup
* Kapacity úložiště, podrobnosti o disku a další důležité informace pro výběr mezi velikosti virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


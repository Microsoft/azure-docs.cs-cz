---
title: "Výpočetní srovnávacího testu skóre pro virtuální počítače s Linuxem | Microsoft Docs"
description: "Porovnání CoreMark výpočetní srovnávacího testu skóre pro virtuální počítače Azure se systémem Linux"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 93e812c1-79dd-40c5-b97b-aa79f5cd7d76
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cynthn;davberg
ms.openlocfilehash: 1d3db66c54769a3efc91a4e909d9c53fc2650743
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="compute-benchmark-scores-for-linux-vms"></a>Výpočetní srovnávacího testu skóre pro virtuální počítače s Linuxem
Následující srovnávacího testu skóre CoreMark zobrazit výpočetní výkon systémem Ubuntu řazení pro virtuální počítač Azure vysoce výkonné. Výpočetní srovnávacího testu skóre jsou také k dispozici pro [virtuálních počítačů Windows](../windows/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="a-series---compute-intensive"></a>A-series - náročné na výkon
| Velikost | vCPU | Uzly NUMA | Procesor | Spuštění | Iterace za sekundu | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |Intel Xeon CPU E5-. 2670 0 @ 2.6 GHz |179 |110,294 |554 |
| Standard_A9 |16 |2 |Intel Xeon CPU E5-. 2670 0 @ 2.6 GHz |189 |210,816 |2,126 |
| Standard_A10 |8 |1 |Intel Xeon CPU E5-. 2670 0 @ 2.6 GHz |188 |110,025 |1,045 |
| Standard_A11 |16 |2 |Intel Xeon CPU E5-. 2670 0 @ 2.6 GHz |188 |210,727 |2,073 |

## <a name="dv2-series"></a>Dv2-series
| Velikost | vCPU | Uzly NUMA | Procesor | Spuštění | Iterace za sekundu | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |140 |14,852 |780 |
| Standard_D2_v2 |2 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |133 |29,467 |1,863 |
| Standard_D3_v2 |4 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |139 |56,205 |1,167 |
| Standard_D4_v2 |8 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |126 |108,543 |3,446 |
| Standard_D5_v2 |16 |2 |Intel Xeon E5-2673 v3 @ 2,4 GHz |126 |205,332 |9,998 |
| Standard_D11_v2 |2 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |125 |28,598 |1,510 |
| Standard_D12_v2 |4 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |131 |55,673 |1,418 |
| Standard_D13_v2 |8 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |140 |107,986 |3,089 |
| Standard_D14_v2 |16 |2 |Intel Xeon E5-2673 v3 @ 2,4 GHz |140 |208,186 |8,839 |
| Standard_D15_v2 |20 |2 |Intel Xeon E5-2673 v3 @ 2,4 GHz |28 |268,560 |4,667 |

## <a name="f-series"></a>F-series
| Velikost | vCPU | Uzly NUMA | Procesor | Spuštění | Iterace za sekundu | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_F1 |1 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |154 |15,602 |787 |
| Standard_F2 |2 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |126 |29,519 |1,233 |
| Standard_F4 |4 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |147 |58,709 |1,227 |
| Standard_F8 |8 |1 |Intel Xeon E5-2673 v3 @ 2,4 GHz |224 |112,772 |3,006 |
| Standard_F16 |16 |2 |Intel Xeon E5-2673 v3 @ 2,4 GHz |42 |218,571 |5,113 |

## <a name="g-series"></a>G-series
| Velikost | vCPU | Uzly NUMA | Procesor | Spuštění | Iterace za sekundu | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1 |2 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |83 |31,310 |2,891 |
| Standard_G2 |4 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |84 |60,112 |3,537 |
| Standard_G3 |8 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |84 |107,522 |4,537 |
| Standard_G4 |16 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |83 |195,116 |5,024 |
| Standard_G5 |32 |2 |Intel Xeon E5-2698B v3 @ 2 GHz |84 |360,329 |14,212 |

## <a name="gs-series"></a>Řada GS
| Velikost | vCPU | Uzly NUMA | Procesor | Spuštění | Iterace za sekundu | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |84 |28,613 |1,884 |
| Standard_GS2 |4 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |83 |54,348 |3,474 |
| Standard_GS3 |8 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |83 |104,564 |1,834 |
| Standard_GS4 |16 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |84 |194,111 |4,735 |
| Standard_GS5 |32 |2 |Intel Xeon E5-2698B v3 @ 2 GHz |84 |357,396 |16,228 |

## <a name="h-series"></a>H-series
| Velikost | vCPU | Uzly NUMA | Procesor | Spuštění | Iterace za sekundu | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |1 |Intel Xeon E5-. 2667 v3 @ 3,2 GHz |28 |140,782 |2,512 |
| Standard_H16 |16 |2 |Intel Xeon E5-. 2667 v3 @ 3,2 GHz |35 |275,289 |7,110 |
| Standard_H18m |8 |1 |Intel Xeon E5-. 2667 v3 @ 3,2 GHz |28 |139,071 |3,988 |
| Standard_H16m |16 |2 |Intel Xeon E5-. 2667 v3 @ 3,2 GHz |28 |275,988 |6,963 |
| Standard_H16r |16 |2 |Intel Xeon E5-. 2667 v3 @ 3,2 GHz |28 |273,982 |6,069 |
| Standard_H16mr |16 |2 |Intel Xeon E5-. 2667 v3 @ 3,2 GHz |28 |274,523 |5,698 |

## <a name="about-coremark"></a>O CoreMark
Linux čísla byly počítaný spuštěním [CoreMark](http://www.eembc.org/coremark/faq.php) na Ubuntu. CoreMark byl nakonfigurován s počet vláken, počet virtuálních procesorů, a nastavte souběžnosti na PThreads. Cílový počet iterací byla upravena podle očekávaný výkon zajistit runtime aspoň 20 sekund (obvykle mnohem déle). Konečný výsledek představuje počet iterací dokončit rozdělené podle počtu sekund, po které trvalo spuštění testu. Každý test byl spuštěn alespoň sedm časy pro každý virtuální počítač. Testy (s výjimkou pro H series_ byly spuštěné října 2015 na víc virtuálních počítačů v každé oblasti Azure veřejné virtuálního počítače byl v podporované na datum spustit.

## <a name="next-steps"></a>Další postup
* Kapacity úložiště, podrobnosti o disku a další důležité informace pro výběr mezi velikosti virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Ke spouštění skriptů CoreMark na virtuální počítače s Linuxem, stáhněte si [CoreMark pack skriptu](http://download.microsoft.com/download/3/0/5/305A3707-4D3A-4599-9670-AAEB423B4663/AzureCoreMarkScriptPack.zip).


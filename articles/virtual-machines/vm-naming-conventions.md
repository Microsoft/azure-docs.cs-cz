---
title: Zásady vytváření názvů virtuálních počítačů Azure
description: Vysvětluje konvence pojmenování používané pro velikosti virtuálních počítačů Azure.
ms.service: virtual-machines
subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.date: 7/22/2020
ms.author: mimckitt
ms.custom: sttsinar
ms.openlocfilehash: 2059c6f374e4cd5c2518e2fc0ac0da5858b99825
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131714"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Zásady vytváření názvů virtuálních počítačů Azure

Tato stránka popisuje konvence pojmenování používané pro virtuální počítače Azure. Virtuální počítače používají tyto zásady vytváření názvů k označení různých funkcí a specifikací.

## <a name="naming-convention-explanation"></a>Vysvětlení konvence pojmenování

**[Rodina]**  +  **[Subrodina *]**  +  **[počet vCPU]**  +  **[Doplňkové funkce]**  +  **[Typ akcelerátoru *]**  +  **[Verze]**

|Hodnota | Vysvětlení|
|---|---|
| Standardní, základní nebo experimentální | Výchozí hodnota Standard je přiřazená pro všechny velikosti virtuálních počítačů GA. | 
| Rodina | Označuje řadu rodin virtuálních počítačů.| 
| * Sub-Family | Používá se jenom pro specializované rozlišení virtuálních počítačů.|
| počet vCPU| Označuje počet vCPU virtuálního počítače. |
| Doplňkové funkce | Jedno nebo několik malých písmen značí doplňkové funkce, jako například: <br> a = procesor založený na platformě AMD <br> d = disk (místní dočasný disk je přítomen); Toto je pro novější virtuální počítače Azure, viz [Ddv4 a Ddsv4-Series](./ddv4-ddsv4-series.md) . <br> h = podporuje hibernace <br> i = izolovaný <br> l = nedostatek paměti <br> m = náročné na paměť <br> t = malá paměť <br> r = RDMA <br> s = Premium Storage schopný, včetně možného použití [SSD úrovně Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) (Poznámka: Některé novější velikosti bez atributu s mohou i nadále podporovat Premium Storage, například M128, M64 atd.)<br> |
| * Typ akcelerátoru | Označuje typ hardwarového akcelerátoru v SKU specializovaného/GPU. V názvu budou mít v názvu pouze nové specializované SKU/SKU GPU spuštěné z tohoto čtvrtletí 2020. |
| Verze | Označuje verzi řady virtuálních počítačů. |

## <a name="example-breakdown"></a>Příklad rozpisu

**[Rodina]**  +  **[Subrodina *]**  +  **[počet vCPU]**  +  **[Doplňkové funkce]**  +  **[Typ akcelerátoru *]**  +  **[Verze]**

### <a name="example-1-m416ms_v2"></a>Příklad 1: M416ms_v2

|Hodnota | Vysvětlení|
|---|---|
| Rodina | M | 
| počet vCPU | 416 |
| Doplňkové funkce | m = náročné na paměť <br> s = Premium Storage schopný |
| Verze | v2 |

### <a name="example-2-nv16as_v4"></a>Příklad 2: NV16as_v4

|Hodnota | Vysvětlení|
|---|---|
| Rodina | N | 
| Dílčí rodina | V |
| počet vCPU | 16 |
| Doplňkové funkce | a = procesor založený na platformě AMD <br> s = Premium Storage schopný |
| Verze | V4 |

### <a name="example-3-nc4as_t4_v3"></a>Příklad 3: NC4as_T4_v3

|Hodnota | Vysvětlení|
|---|---|
| Rodina | N | 
| Dílčí rodina | C |
| počet vCPU | 4 |
| Doplňkové funkce | a = procesor založený na platformě AMD <br> s = Premium Storage schopný |
| Typ akcelerátoru | T4 |
| Verze | v3 |

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o dostupných [velikostech virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) v Azure. 

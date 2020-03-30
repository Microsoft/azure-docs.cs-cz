---
title: Přehled výpočetní jednotky Azure | Dokumenty společnosti Microsoft
description: Přehled konceptu výpočetních jednotek Azure. ACU poskytuje způsob porovnání výkonu procesoru mezi skum Azure.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: e344d09497a30dec546dfaedd3d78f30c7d214d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535693"
---
# <a name="azure-compute-unit-acu"></a>Výpočetní jednotka Azure (ACU)

Koncept azure compute unit (ACU) poskytuje způsob porovnání výpočetního výkonu (CPU) mezi skladovými jednotkami Azure. To vám umožní snadno zjistit, jaká jednotka SKU nejlépe vyhoví vašim požadavkům na výkon. Jednotka ACU je aktuálně stanovená tak, že malý virtuální počítač (Standard_A1) má 100 ACU a ostatní jednotky SKU jsou pak ohodnoceny podle relativního výsledku standardního srovnávacího testu.

> [!IMPORTANT]
> ACU je jenom informativní údaj. Výsledek v případě konkrétních úloh může být odlišný.
<br>

| Rodina SKU | ACU \ virtuální procesor | vCPU: Jádro |
| --- | --- |---|
| [A0](sizes-previous-gen.md) |50 | 1:1 |
| [A1 – A4](sizes-previous-gen.md) |100 | 1:1 |
| [A5 - A7](sizes-previous-gen.md) |100 | 1:1 |
| [A1_v2 - A8_v2](sizes-general.md) |100 | 1:1 |
| [A2m_v2 - A8m_v2](sizes-general.md) |100 | 1:1 |
| [A8 – A11](sizes-previous-gen.md) |225* | 1:1 |
| [D1 - D14](sizes-previous-gen.md) |160 - 250 | 1:1 |
| [D1_v2 - D15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [DS1 – DS14](sizes-previous-gen.md) |160 - 250 | 1:1 |
| [DS1_v2 - DS15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\* |
| [F2s_v2 - F72s_v2](fsv2-series.md) |195 - 210* | 2:1\*\*\* |
| [F1 - F16](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [F1s - F16s](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [G1 - G5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [GS1 - GS5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [H](h-series.md) |290 - 300* | 1:1 |
| [HB](hb-series.md) |199 - 216** | 1:1 |
| [Hc](hc-series.md) |297 - 315* | 1:1 |
| [L4s - L32s](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [L8s_v2 - L80s_v2](lsv2-series.md) |150 - 175** | 2:1 |
| [M](m-series.md) | 160 - 180 | 2:1\*\*\* |

* ACU používají technologii Intel® Turbo ke zvýšení frekvence procesoru a zvýšení výkonu.  Velikost zvýšení výkonu se může lišit v závislosti na velikosti virtuálního počítače, zatížení a další úlohy spuštěné na stejném hostiteli.
**ACU používají technologii AMD® Boost ke zvýšení frekvence procesoru a zvýšení výkonu.  Velikost zvýšení výkonu se může lišit v závislosti na velikosti virtuálního počítače, zatížení a další úlohy spuštěné na stejném hostiteli.
Hyper-threaded a schopné spustit vnořené virtualizace

Zde jsou odkazy na další informace o různých velikostech:

- [Obecný](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)

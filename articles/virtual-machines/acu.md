---
title: Přehled výpočetní jednotky Azure
description: Přehled konceptu výpočetních jednotek Azure. ACU poskytuje způsob, jak porovnat výkon procesoru napříč SKU Azure.
author: mimckitt
ms.service: virtual-machines
ms.subservice: azure-compute-unit
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.reviewer: davberg
ms.openlocfilehash: 3233695f8a8cc14d70f0c482458e77094dde7622
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969066"
---
# <a name="azure-compute-unit-acu"></a>Výpočetní jednotka Azure (ACU)

Koncept výpočetní jednotky Azure (ACU) poskytuje způsob, jak porovnat výpočetní výkon (CPU) napříč SKU Azure. To vám umožní snadno zjistit, jaká jednotka SKU nejlépe vyhoví vašim požadavkům na výkon. ACU je v současné době standardizovaná na malém (Standard_A1) virtuálním počítači, který se 100, a všechny ostatní SKU pak reprezentují, kolik rychlejší může SKU spustit standardní srovnávací síť.

* ACUs pomocí technologie Intel® Turbo Zvyšte frekvenci procesoru a získáte zvýšení výkonu.  Množství zvýšení výkonu se může lišit v závislosti na velikosti virtuálního počítače, zatížení a dalších úlohách, které běží na stejném hostiteli.

* * ACUs pomocí technologie AMD® zvýšit frekvenci procesoru a zvýšit výkon.  Množství zvýšení výkonu se může lišit v závislosti na velikosti virtuálního počítače, zatížení a dalších úlohách, které běží na stejném hostiteli.

Vlákny s technologií Hyper a schopné spustit vnořenou virtualizaci

Technologie AMD pro souběžné zpracování více vláken

> [!IMPORTANT]
> ACU je jenom informativní údaj. Výsledek v případě konkrétních úloh může být odlišný.
<br>

| Rodina SKU | ACU \ virtuální procesor | vCPU: jádro |
| --- | --- |---|
| [A0](sizes-previous-gen.md) |50 | 1:1 |
| [A1 – A4](sizes-previous-gen.md) |100 | 1:1 |
| [A5 – A7](sizes-previous-gen.md) |100 | 1:1 |
| [A1_v2 – A8_v2](sizes-general.md) |100 | 1:1 |
| [A2m_v2 – A8m_v2](sizes-general.md) |100 | 1:1 |
| [A8 – A11](sizes-previous-gen.md) |225* | 1:1 |
| [B](sizes-b-series-burstable.md) |Různé | 1:1 |
| [D1 – D14](sizes-previous-gen.md) |160 – 250 | 1:1 |
| [D1_v2 – D15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [DS1 – DS14](sizes-previous-gen.md) |160 – 250 | 1:1 |
| [DS1_v2 – DS15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160 – 190 * | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160 – 190 * | 2:1\*\*\* |
| [Dav4](dav4-dasv4-series.md) |230 – 260 * * | 2:1\*\*\*\* |
| [Dasv4](dav4-dasv4-series.md) |230 – 260 * * | 2:1\*\*\*\* |
| [Dv4](dv4-dsv4-series.md) | 195 – 210 | 2:1\*\*\* |
| [Dsv4](dv4-dsv4-series.md) | 195 – 210 | 2:1\*\*\* |
| [Ddv4](ddv4-ddsv4-series.md) | 195 – 210 * | 2:1\*\*\* |
| [Ddsv4](ddv4-ddsv4-series.md) | 195 – 210 * | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160 – 190 * | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160 – 190 * | 2:1\*\*\* |
| [Eav4](eav4-easv4-series.md) |230 – 260 * * | 2:1\*\*\*\* |
| [Easv4](eav4-easv4-series.md) | 230 – 260 * * | 2:1\*\*\*\* |
| [Ev4](ev4-esv4-series.md) | 195 – 210 | 2:1\*\*\* |
| [Esv4](ev4-esv4-series.md) | 195 – 210 | 2:1\*\*\* |
| [Edv4](edv4-edsv4-series.md) | 195 – 210 * | 2:1\*\*\* |
| [Edsv4](edv4-edsv4-series.md) | 195 – 210 * | 2:1\*\*\* |
| [F2s_v2 – F72s_v2](fsv2-series.md) |195 – 210 * | 2:1\*\*\* |
| [F1 – F16 ÚROVNĚ](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [F1s úrovně – F16s úrovně](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [G1-G5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [GS1 – GS5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [Y](h-series.md) |290 - 300* | 1:1 |
| [HB](hb-series.md) |199 – 216 * * | 1:1 |
| [HC](hc-series.md) |297 – 315 * | 1:1 |
| [L4S úrovně – L32s úrovně](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [L8s_v2 – L80s_v2](lsv2-series.md) |150 – 175 * * | 2:1\*\*\*\* |
| [4m](m-series.md) | 160 – 180 | 2:1\*\*\* |
| [NVv4](nvv4-series.md) |230 – 260 * * | 2:1\*\*\*\* |

Tady jsou odkazy na Další informace o různých velikostech:

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro výpočty](sizes-compute.md)
- [Optimalizované z hlediska GPU.](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Optimalizované pro úložiště](sizes-storage.md)

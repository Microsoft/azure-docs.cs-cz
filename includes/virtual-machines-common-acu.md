---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn;davberg
ms.custom: include file
ms.openlocfilehash: 86320af20a5f296358e1fd1d38eb297a8bb1a4bb
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331098"
---
Koncepce sady Azure Compute jednotek (ACU) poskytuje způsob porovnání výkonu (CPU) jednotlivých SKU v Azure. To vám umožní snadno zjistit, jaká jednotka SKU nejlépe vyhoví vašim požadavkům na výkon.  Jednotka ACU je aktuálně stanovená tak, že malý virtuální počítač (Standard_A1) má 100 ACU a ostatní jednotky SKU jsou pak ohodnoceny podle relativního výsledku standardního srovnávacího testu. 

> [!IMPORTANT]
> ACU je jenom informativní údaj.  Výsledek v případě konkrétních úloh může být odlišný. 
> 
> 

<br>

| Rodina SKU | ACU \ virtuální procesor | vCPU: Jádro |
| --- | --- |---|
| [A0](../articles/virtual-machines/windows/sizes-general.md) |50 | 1:1 |
| [A1 – A4](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A5 – A7](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A1_v2 - A8_v2](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A2m_v2 - A8m_v2](../articles/virtual-machines/windows/sizes-general.md) |100 | 1:1 |
| [A8 - A11](../articles/virtual-machines/windows/sizes-hpc.md) |225* | 1:1 |
| [D1 - D14](../articles/virtual-machines/windows/sizes-general.md) |160 | 1:1 |
| [D1_v2 - D15_v2](../articles/virtual-machines/windows/sizes-general.md) |210 - 250* | 1:1 |
| [DS1 - DS14](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160 | 1:1 |
| [DS1_v2 - DS15_v2](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |210-250* | 1:1 |
| [D_v3](../articles/virtual-machines/virtual-machines-windows-sizes-general.md) |160-190* | 2:1\*\*\* |
| [Ds_v3](../articles/virtual-machines/virtual-machines-windows-sizes-general.md) |160-190* | 2:1\*\*\* |
| [E_v3](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160-190* | 2:1\*\*\*|
| [Es_v3](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160-190* | 2:1\*\*\* |
| [F2s_v2 - F72s_v2](../articles/virtual-machines/windows/sizes-compute.md) |195-210* | 2:1\*\*\* |
| [F1 - F16](../articles/virtual-machines/windows/sizes-compute.md) |210-250* | 1:1 |
| [F1s - F16s](../articles/virtual-machines/windows/sizes-compute.md) |210-250* | 1:1 |
| [G1 - G5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 - 240* | 1:1 |
| [GS1 - GS5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 - 240* | 1:1 |
| [H](../articles/virtual-machines/windows/sizes-hpc.md) |290 - 300* | 1:1 |
| [L4s - L32s](../articles/virtual-machines/windows/sizes-storage.md) |180 - 240* | 1:1 |
| [L8s_v2 - L80s_v2](../articles/virtual-machines/windows/sizes-storage.md) |150 - 175** | 2:1 |
| [M](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) | 160-180 | 2:1\*\*\* |

* ACUs pomocí technologie Intel® Turbo zvyšte frekvenci procesoru a poskytnout zvýšení výkonu.  Množství nárůst výkonu může lišit v závislosti na velikosti virtuálního počítače, úlohy a dalších úlohách spuštěných na stejném hostiteli.

** ACUs technologií AMD® Boost můžete zvýšit frekvenci procesoru a poskytnout zvýšení výkonu.  Množství nárůst výkonu může lišit v závislosti na velikosti virtuálního počítače, úlohy a dalších úlohách spuštěných na stejném hostiteli.

S vláken typu Hyper a je schopný spuštění vnořené virtualizace

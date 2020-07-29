---
title: Dv4 a Dsv4-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače s dv4 a Dsv4-Series.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 32520a458a797ee9945603dd86c9efe359e4d1f6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291163"
---
# <a name="dv4-and-dsv4-series"></a>Řada Dv4 a Dsv4

Dv4 a Dsv4-Series běží na &reg; procesorech Intel Xeon &reg; Platinum 8272CL (Cascade Lake) v konfiguraci s vlákny typu Hyper a poskytují lepší polohu pro většinu úloh pro obecné účely. Nabízí trvalou veškerou základní rychlost Turbo 3,4 GHz. 

> [!NOTE]
> Nejčastější dotazy najdete v tématu [velikosti virtuálních počítačů Azure bez místního dočasného disku](azure-vms-no-temp-disk.md).
## <a name="dv4-series"></a>Dv4-Series

Velikosti řady dv4-Series běží na Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake). Velikosti řady dv4-Series nabízejí kombinaci možností vCPU, paměti a vzdáleného úložiště pro většinu produkčních úloh. Virtuální počítače dv4-Series [mají &reg; technologii Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html).

Úložiště vzdálených datových disků se fakturuje odděleně od virtuálních počítačů. Pokud chcete použít disky Premium Storage, použijte velikosti Dsv4. Měřiče cen a účtování pro velikosti Dsv4 jsou stejné jako pro dv4-Series.


> [!IMPORTANT]
> Tyto nové velikosti jsou momentálně pod Public Preview. K těmto dv4 a Dsv4-Series se můžete zaregistrovat [tady](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u). 


ACU: 195-210

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Migrace za provozu: podporováno

Aktualizace pro zachování paměti: podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální počet síťových karet|Očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|
| Standard_D2_v4 | 2 | 8 | Jenom vzdálené úložiště | 4 | 2|1000 |
| Standard_D4_v4 | 4 | 16  | Jenom vzdálené úložiště | 8 | 2|2000 |
| Standard_D8_v4 | 8 | 32 | Jenom vzdálené úložiště | 16 | 4|4000 |
| Standard_D16_v4 | 16 | 64 | Jenom vzdálené úložiště | 32 | 8|8000 |
| Standard_D32_v4 | 32 | 128 | Jenom vzdálené úložiště | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | Jenom vzdálené úložiště | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | Jenom vzdálené úložiště | 32 | 8|30000 |

## <a name="dsv4-series"></a>Dsv4-Series

Velikosti řady Dsv4-Series běží na Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake). Velikosti řady dv4-Series nabízejí kombinaci možností vCPU, paměti a vzdáleného úložiště pro většinu produkčních úloh. Virtuální počítače Dsv4-Series [mají &reg; technologii Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html). Úložiště vzdálených datových disků se fakturuje odděleně od virtuálních počítačů.

> [!IMPORTANT]
> Tyto nové velikosti jsou momentálně pod Public Preview. K těmto dv4 a Dsv4-Series se můžete zaregistrovat [tady](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u). 

ACU: 195-210

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Migrace za provozu: podporováno

Aktualizace pro zachování paměti: podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost v mezipaměti: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku neuloženého v mezipaměti: IOPS/MB/s | Maximální počet síťových karet|Očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | Jenom vzdálené úložiště | 4 | 19000/120 (50) | 3000/48 | 2|1000 |
| Standard_D4s_v4 | 4 | 16 | Jenom vzdálené úložiště | 8 | 38500/242 (100) | 6400/96 | 2|2000 |
| Standard_D8s_v4 | 8 | 32 | Jenom vzdálené úložiště | 16 | 77000/485 (200) | 12800/192 | 4|4000 |
| Standard_D16s_v4 | 16 | 64  | Jenom vzdálené úložiště | 32 | 154000/968 (400) | 25600/384 | 8|8000 |
| Standard_D32s_v4 | 32 | 128 | Jenom vzdálené úložiště | 32 | 308000/1936 (800) | 51200/768 | 8|16000 |
| Standard_D48s_v4 | 48 | 192 | Jenom vzdálené úložiště | 32 | 462000/2904 (1200) | 76800/1152 | 8|24000 |
| Standard_D64s_v4 | 64 | 256 | Jenom vzdálené úložiště | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |

---
title: Skóre srovnávacích testů pro virtuální počítače Azure s Windows
description: Porovnejte skóre SPECint výpočetních testů pro virtuální počítače Azure s Windows serverem.
author: cynthn
ms.service: virtual-machines
ms.subservice: benchmark
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 04/29/2020
ms.author: cynthn
ms.reviewer: davberg
ms.openlocfilehash: c3cd1595f0c760ee8b749a0fedace6b55527bfab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104956192"
---
# <a name="compute-benchmark-scores-for-windows-vms"></a>Hodnocení srovnávacích testů pro virtuální počítače s Windows
Následující skóre srovnávacích testů SPECInt ukazují výpočetní výkon pro vybrané virtuální počítače Azure se systémem Windows Server. Pro [virtuální počítače se systémem Linux](../linux/compute-benchmark-scores.md)jsou k dispozici také hodnocení srovnávacích testů.


## <a name="av2---general-compute"></a>Av2 – obecné výpočetní prostředky

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_A1_v2 | 1 | 1 | Intel (R) Xeon (R) CPU E5-2660 0 @ 2.20 GHz | 12 | 14,2 | 0.3 | 
| Standard_A1_v2 | 1 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 9 | 13.2 | 0.6 | 
| Standard_A1_v2 | 1 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 10 | 14.1 | 0.7 | 
| Standard_A2_v2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2660 0 @ 2.20 GHz | 14 | 28.9 | 0.6 | 
| Standard_A2_v2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 10 | 27,4 | 1.6 | 
| Standard_A2_v2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 17 | 28.9 | 1.8 | 
| Standard_A2m_v2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2660 0 @ 2.20 GHz | 14 | 29,0 | 0,5 | 
| Standard_A2m_v2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 11 | 26.3 | 0.8 | 
| Standard_A2m_v2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 21 | 28.4 | 1.0 | 
| Standard_A4_v2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2660 0 @ 2.20 GHz | 27 | 56,6 | 1.0 | 
| Standard_A4_v2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 13 | 52,8 | 2.0 | 
| Standard_A4_v2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 15 | 52,1 | 4.5 | 
| Standard_A4m_v2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2660 0 @ 2.20 GHz | 17 | 56,4 | 1.8 | 
| Standard_A4m_v2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 6 | 53,4 | 1.9 | 
| Standard_A4m_v2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 23 | 57,1 | 3,6 | 
| Standard_A8_v2 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2660 0 @ 2.20 GHz | 14 | 109,1 | 1.6 | 
| Standard_A8_v2 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 6 | 101,5 | 2,8 | 
| Standard_A8_v2 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 11 | 101,9 | 2.7 | 
| Standard_A8m_v2 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 11 | 101.4 | 1.2 | 
| Standard_A8m_v2 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 10 | 104,5 | 5,1 | 
| Standard_A8m_v2 | 8 | 2 | Intel (R) Xeon (R) CPU E5-2660 0 @ 2.20 GHz | 13 | 111,6 | 2.3 | 


> [!NOTE]
> Virtuální počítače řady Av2-Series se dají nasadit na nejrůznější typy a procesory hardwaru (jak vidíte výše). Virtuální počítače řady Av2-Series mají pro úlohy na vstupní úrovni, jako je vývoj a testování, nejvhodnější konfigurace výkonu a paměti procesoru. Velikost je omezená, aby nabízela poměrně konzistentní výkon procesoru pro běžící instanci bez ohledu na hardware, na kterém je nasazený. software, který využívá výhod konkrétních novějších optimalizací procesorů, ale může výrazně variace v různých typech procesorů.

## <a name="b---burstable"></a>B-Shluková

(Aktualizované 2019-10-23 na 2019-11-03 PBI: 5604451)

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_B1ms | 1 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 9 | 6.3 | 0.2 | 
| Standard_B1ms | 1 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 47 | 6.4 | 0.2 | 
| Standard_B2ms | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 36 | 19.8 | 0.8 | 
| Standard_B2s | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 2 | 13,0 | 0,0 | 
| Standard_B2s | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 29 | 13,0 | 0,5 | 
| Standard_B4ms | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 6 | 27,1 | 1.0 | 
| Standard_B4ms | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 43 | 28.3 | 0.7 | 
| Standard_B8ms | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 3 | 42,0 | 0,0 | 
| Standard_B8ms | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 25 | 41,4 | 0,9 | 
| Standard_B12ms | 12 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 nebo v4 | 19 | 58,9 | 2.3 |
| Standard_B16ms | 16 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 nebo v4 | 18 | 75,4 | 2.1 |
| Standard_B20ms | 20 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 nebo v4| 2 | 90,6 | 1.3 |


>[!NOTE]
> Virtuální počítače řady B-Series jsou pro úlohy s požadavky na nejvyšší výkon. Instance virtuálních počítačů nashromáždí kredity při použití menšího směrného plánu. Když se na virtuálním počítači nashromáždil kredit, může se virtuální počítač nad rámec směrného plánu zvýšit až 100%, aby se splnily krátké požadavky na nárůst zatížení procesoru. Doba nárůstu závisí na dostupných kreditech, které jsou funkcí velikosti a času virtuálního počítače.  
>
> SPEC int je poměrně dlouho běžící test, který obvykle vyčerpá dostupné kredity shlukového přenosu.  Proto se výše uvedená čísla blíží základnímu výkonu virtuálního počítače (i když můžou odrážet určitou dobu shluku mezi běhy).  Pro krátké, shluky, úlohy (typické pro řady B-Series) se obvykle blíží sadě DS v3.

## <a name="dsv3---general-compute--premium-storage"></a>DSv3 – obecné COMPUTE a Premium Storage

(aktualizované 2019-10-23 na 2019-11-03 PBI: 5604451)

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_D2s_v3 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 10 | 40,8 | 2.3 | 
| Standard_D2s_v3 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 52 | 43,3 | 2.1 | 
| Standard_D4s_v3 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 21 | 77,9 | 2,6 | 
| Standard_D4s_v3 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 29 | 82,3 | 2.5 | 
| Standard_D8s_v3 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 7 | 148,3 | 1.9 | 
| Standard_D8s_v3 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 28 | 155,4 | 5.6 | 
| Standard_D16s_v3 | 16 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 3 | 275,7 | 5,1 | 
| Standard_D16s_v3 | 16 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 38 | 298,2 | 4.4 | 
| Standard_D32s_v3 | 32 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 24 | 545,8 | 10,5 | 
| Standard_D32s_v3 | 32 | 2 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 9 | 535,6 | 12,6 | 
| Standard_D32 – 8s_v3 | 8 |  | Intel (R) Xeon (R) CPU E5-2673 V3 nebo v4 | 6 | 166,0 | 8.8 |
| Standard_D32 – 16s_v3 | 16 |  | Intel (R) Xeon (R) CPU E5-2673 V3 nebo v4 | 4 | 300,8 | 6.4 |
| Standard_D48s_v3 | 48 | 2 | Intel (R) Xeon (R) CPU E5-2673 V3 nebo v4 | 1 | 838,0 | 0,0 |
| Standard_D64s_v3 | 64 | 2 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 35 | 1070,6 | 2,4 | 
| Standard_D64 – 16s_v3 | 16 |  | Intel (R) Xeon (R) CPU E5-2673 V3 nebo v4 | 4 | 340,0 | 21.4 |
| Standard_D64 – 32s_v3 | 32 |  | Intel (R) Xeon (R) CPU E5-2673 V3 nebo v4 | 3 | 592,3 | 1.5 |

## <a name="dv3---general-compute"></a>Dv3 – obecné výpočetní prostředky

(aktualizované 2019-10-23 na 2019-11-03 PBI: 5604451)

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_D2_v3 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 10 | 38,6 | 1.8 | 
| Standard_D2_v3 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 24 | 41,8 | 3.3 | 
| Standard_D4_v3 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 17 | 77,8 | 1.3 | 
| Standard_D4_v3 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 45 | 82,7 | 4.5 | 
| Standard_D8_v3 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 9 | 146,7 | 10,4 | 
| Standard_D8_v3 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 27 | 159,9 | 8.3 | 
| Standard_D16_v3 | 16 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 10 | 274,1 | 3.8 | 
| Standard_D16_v3 | 16 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 32 | 300,7 | 8.8 | 
| Standard_D32_v3 | 32 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 24 | 549,3 | 11,1 | 
| Standard_D32_v3 | 32 | 2 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 7 | 538,6 | 9,4 | 
| Standard_D48_v3 | 48 |  |  Intel (R) Xeon (R) CPU E5-2673 V3 nebo v4 | 3 | 839,7 | 14.4 |
| Standard_D64_v3 | 64 | 2 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 32 | 1070,6 | 12.4 | 

## <a name="dsv2---storage-optimized"></a>DSv2 – optimalizované úložiště

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_DS1_v2 | 1 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 12 | 33,0 | 1.1 | 
| Standard_DS1_v2 | 1 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 37 | 33.8 | 2.5 | 
| Standard_DS2_v2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 33 | 63,9 | 1.7 | 
| Standard_DS2_v2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 32 | 66,6 | 4.8 | 
| Standard_DS3_v2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 15 | 125,5 | 3.2 | 
| Standard_DS3_v2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 47 | 130,1 | 4.3 | 
| Standard_DS4_v2 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 23 | 235,7 | 6.6 | 
| Standard_DS4_v2 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 34 | 249,4 | 2,8 | 
| Standard_DS5_v2 | 16 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 11 | 414,9 | 5,1 | 
| Standard_DS5_v2 | 16 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 31 | 470,6 | 5.7 | 
| Standard_DS11_v2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 22 | 66,3 | 2,8 | 
| Standard_DS11_v2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 34 | 64,8 | 2,8 | 
| Standard_DS11 – 1_v2 | 1 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 17 | 33.6 | 1.8 | 
| Standard_DS11 – 1_v2 | 1 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 41 | 36,0 | 1.7 | 
| Standard_DS12_v2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 10 | 126,8 | 2.7 | 
| Standard_DS12_v2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 30 | 127,5 | 3.3 | 
| Standard_DS12 – 1_v2 | 1 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 20 | 33,5 | 1.4 | 
| Standard_DS12 – 1_v2 | 1 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 30 | 34,8 | 2,4 | 
| Standard_DS12 – 2_v2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 17 | 65,5 | 2.3 | 
| Standard_DS12 – 2_v2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 33 | 67,7 | 5,1 | 
| Standard_DS13_v2 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 20 | 234,1 | 7.1 | 
| Standard_DS13_v2 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 23 | 248,0 | 2,2 | 
| Standard_DS13 – 2_v2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 17 | 65,2 | 3.1 | 
| Standard_DS13 – 2_v2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 15 | 72,8 | 3.8 | 
| Standard_DS13 – 4_v2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 24 | 126,1 | 4.3 | 
| Standard_DS13 – 4_v2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 27 | 133,3 | 2,8 | 
| Standard_DS14_v2 | 16 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 22 | 469,5 | 6.9 | 
| Standard_DS14_v2 | 16 | 2 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 16 | 456,6 | 7.3 | 
| Standard_DS14 – 4_v2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 28 | 132,8 | 6.6 | 
| Standard_DS14 – 4_v2 | 4 | 2 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 16 | 125,1 | 4.8 | 
| Standard_DS14 – 8_v2 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 27 | 251,3 | 2,4 | 
| Standard_DS14 – 8_v2 | 8 | 2 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 14 | 247,4 | 10,2 | 
| Standard_DS15_v2 | 20 | 2 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 45 | 546,1 | 10,5 | 

## <a name="dv2---general-compute"></a>Dv2 – obecné výpočetní prostředky

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_D1_v2 | 1 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 30 | 33,5 | 1.7 | 
| Standard_D1_v2 | 1 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 31 | 34.7 | 2.5 | 
| Standard_D2_v2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 18 | 66.0 | 1.8 | 
| Standard_D2_v2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 31 | 69,9 | 5.0 | 
| Standard_D3_v2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 27 | 127,7 | 3.0 | 
| Standard_D3_v2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 27 | 133,4 | 9,1 | 
| Standard_D4_v2 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 15 | 238,7 | 4.4 | 
| Standard_D4_v2 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 36 | 248,9 | 4.8 | 
| Standard_D5_v2 | 16 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 9 | 413,9 | 14.1 | 
| Standard_D5_v2 | 16 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 27 | 470,2 | 8.1 | 
| Standard_D5_v2 | 16 | 2 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 5 | 466,0 | 0,0 | 
| Standard_D11_v2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 22 | 66,4 | 2.9 | 
| Standard_D11_v2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 27 | 69.0 | 6.7 | 
| Standard_D12_v2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 24 | 127,7 | 4,6 | 
| Standard_D12_v2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 20 | 135,9 | 9.3 | 
| Standard_D13_v2 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 16 | 237,4 | 6.6 | 
| Standard_D13_v2 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 28 | 250,2 | 3.8 | 
| Standard_D14_v2 | 16 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 23 | 473,0 | 9,4 | 
| Standard_D14_v2 | 16 | 2 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 17 | 443,9 | 18,8 | 
| Standard_D15_v2 | 20 | 2 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 37 | 558,8 | 8.4 | 

## <a name="esv3---memory-optimized--premium-storage"></a>Esv3 – optimalizované pro paměť a Premium Storage

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_E2s_v3 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 39 | 42,5 | 2,2 | 
| Standard_E4s_v3 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 28 | 81,4 | 3.3 | 
| Standard_E8s_v3 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 29 | 156,3 | 5,1 | 
| Standard_E8 – 2s_v3 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 57 | 41,8 | 2,6 | 
| Standard_E8 – 4s_v3 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 45 | 82,9 | 3.0 | 
| Standard_E16s_v3 | 16 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 31 | 295,7 | 4.5 | 
| Standard_E16 – 4s_v3 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 45 | 82,7 | 3.8 | 
| Standard_E16 – 8s_v3 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 39 | 158,3 | 4.5 | 
| Standard_E20s_v3 | 20 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 27 | 369,7 | 3.2 | 
| Standard_E32s_v3 | 32 | 2 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 31 | 577,9 | 9,4 | 
| Standard_E32 – 8s_v3 | 8 | 2 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 31 | 163,4 | 6.8 | 
| Standard_E32 – 16s_v3 | 16 | 2 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 41 | 307,1 | 8.7 | 
| Standard_E4 – 2s_v3 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 65 | 41,9 | 2,4 | 
| Standard_E64s_v3 | 64 | 2 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 1 | 1080,0 | 0,0 | 
| Standard_E64 – 16s_v3 | 16 | 2 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 3 | 334,3 | 1.5 | 
| Standard_E64 – 32s_v3 | 32 | 2 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 4 | 592,5 | 4.4 | 

## <a name="eisv3---memory-opt--premium-storage-isolated"></a>Eisv3 a opt-Memory opt + Premium Storage (izolovaný režim)

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_E64is_v3 | 64 | 2 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 28 | 1073,9 | 5.7 | 

## <a name="ev3---memory-optimized"></a>Ev3 – optimalizované pro paměť

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_E2_v3 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 41 | 41,2 | 2,4 | 
| Standard_E4_v3 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 43 | 81,4 | 5.3 | 
| Standard_E8_v3 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 39 | 157,4 | 8.1 | 
| Standard_E16_v3 | 16 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 49 | 301,6 | 8.9 | 
| Standard_E20_v3 | 20 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 35 | 371,0 | 6.9 | 
| Standard_E32_v3 | 32 | 2 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 35 | 579,9 | 16,1 | 
| Standard_E64_v3 | 64 | 2 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 31 | 1080,0 | 11.3 | 

## <a name="eiv3---memory-optimized-isolated"></a>Eiv3 – optimalizované pro paměť (izolovaný režim)

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_E64i_v3 | 64 | 2 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 28 | 1081,4 | 11,1 | 

## <a name="fsv2---compute--storage-optimized"></a>Fsv2 – COMPUTE + úložiště – optimalizované

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_F2s_v2 | 2 | 1 | Procesor Intel (R) Xeon (R) Platinum 8168 CPU @ 2.70 GHz | 46 | 56,5 | 2,4 | 
| Standard_F4s_v2 | 4 | 1 | Procesor Intel (R) Xeon (R) Platinum 8168 CPU @ 2.70 GHz | 60 | 110,2 | 4.7 | 
| Standard_F8s_v2 | 8 | 1 | Procesor Intel (R) Xeon (R) Platinum 8168 CPU @ 2.70 GHz | 36 | 215,2 | 5.3 | 
| Standard_F16s_v2 | 16 | 1 | Procesor Intel (R) Xeon (R) Platinum 8168 CPU @ 2.70 GHz | 36 | 409,3 | 15.5 | 
| Standard_F32s_v2 | 32 | 1 | Procesor Intel (R) Xeon (R) Platinum 8168 CPU @ 2.70 GHz | 31 | 760,9 | 16.9 | 
| Standard_F64s_v2 | 64 | 2 | Procesor Intel (R) Xeon (R) Platinum 8168 CPU @ 2.70 GHz | 33 | 1440,9 | 26.0 | 
| Standard_F72s_v2 | 72 | 2 | Procesor Intel (R) Xeon (R) Platinum 8168 CPU @ 2.70 GHz | 29 | 1372,1 | 8.2 | 

## <a name="fs---compute-and-storage-optimized"></a>FS – COMPUTE a úložiště – optimalizované

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_F1s | 1 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 31 | 33.2 | 1.0 | 
| Standard_F1s | 1 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 41 | 35.1 | 2.0 | 
| Standard_F2s | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 18 | 63,7 | 1.8 | 
| Standard_F2s | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 21 | 66,6 | 3.8 | 
| Standard_F4s | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 14 | 128,4 | 2.9 | 
| Standard_F4s | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 25 | 127,7 | 4.5 | 
| Standard_F8s | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 11 | 234,9 | 3.7 | 
| Standard_F8s | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 19 | 251,2 | 4.5 | 
| Standard_F16s | 16 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 9 | 413,9 | 3,6 | 
| Standard_F16s | 16 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 36 | 471,8 | 7,5 | 

## <a name="f---compute-optimized"></a>Optimalizované pro F-COMPUTE

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_F1 | 1 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 15 | 32.8 | 1.8 | 
| Standard_F1 | 1 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 13 | 33,3 | 2.0 | 
| Standard_F2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 27 | 64,9 | 6.0 | 
| Standard_F2 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 21 | 67,8 | 4,9 | 
| Standard_F4 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 18 | 128,4 | 3.3 | 
| Standard_F4 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 32 | 132,1 | 7,8 | 
| Standard_F8 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 17 | 239,4 | 2.3 | 
| Standard_F8 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 25 | 251,2 | 7,0 | 
| Standard_F16 | 16 | 1 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 19 | 424,1 | 8.2 | 
| Standard_F16 | 16 | 1 | Intel (R) Xeon (R) CPU E5-2673 V4 @ 2.30 GHz | 32 | 467,8 | 11,1 | 
| Standard_F16 | 16 | 2 | Intel (R) Xeon (R) CPU E5-2673 V3 @ 2.40 GHz | 6 | 472,3 | 13.2 | 

## <a name="gs---storage-optimized"></a>GS – optimalizované úložiště

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_GS1 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2698B V3 @ 2,00 GHz | 29 | 63,6 | 4.7 | 
| Standard_GS2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2698B V3 @ 2,00 GHz | 29 | 122,3 | 6.9 | 
| Standard_GS3 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2698B V3 @ 2,00 GHz | 31 | 222,4 | 8.1 | 
| Standard_GS4 | 16 | 1 | Intel (R) Xeon (R) CPU E5-2698B V3 @ 2,00 GHz | 31 | 391,4 | 28.6 | 
| Standard_GS4-4 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2698B V3 @ 2,00 GHz | 28 | 127,5 | 5.3 | 
| Standard_GS4-8 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2698B V3 @ 2,00 GHz | 31 | 226,7 | 5.8 | 
| Standard_GS5 | 32 | 2 | Intel (R) Xeon (R) CPU E5-2698B V3 @ 2,00 GHz | 31 | 760,9 | 6,2 | 
| Standard_GS5-8 | 8 | 2 | Intel (R) Xeon (R) CPU E5-2698B V3 @ 2,00 GHz | 31 | 259,5 | 2.7 | 
| Standard_GS5-16 | 16 | 2 | Intel (R) Xeon (R) CPU E5-2698B V3 @ 2,00 GHz | 31 | 447,9 | 4,0 | 

## <a name="g---compute-optimized"></a>Optimalizováno G – COMPUTE
| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_G1 | 2 | 1 | Intel (R) Xeon (R) CPU E5-2698B V3 @ 2,00 GHz | 29 | 64,7 | 9.2 | 
| Standard_G2 | 4 | 1 | Intel (R) Xeon (R) CPU E5-2698B V3 @ 2,00 GHz | 30 | 127,9 | 12,2 | 
| Standard_G3 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2698B V3 @ 2,00 GHz | 30 | 231,7 | 12,6 | 
| Standard_G4 | 16 | 1 | Intel (R) Xeon (R) CPU E5-2698B V3 @ 2,00 GHz | 31 | 400,2 | 3.9 | 
| Standard_G5 | 32 | 2 | Intel (R) Xeon (R) CPU E5-2698B V3 @ 2,00 GHz | 31 | 774,1 | 4.1 | 

## <a name="h---high-performance-compute-hpc"></a>H-High Performance COMPUTE (HPC)

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_H8 | 8 | 1 | Intel (R) Xeon (R) CPU E5-2667 V3 @ 3.20 GHz | 31 | 296,1 | 1.4 | 
| Standard_H8m | 8 | 1 | Intel (R) Xeon (R) CPU E5-2667 V3 @ 3.20 GHz | 34 | 295,1 | 1.5 | 
| Standard_H16 | 16 | 2 | Intel (R) Xeon (R) CPU E5-2667 V3 @ 3.20 GHz | 19 | 563,5 | 4.3 | 
| Standard_H16m | 16 | 2 | Intel (R) Xeon (R) CPU E5-2667 V3 @ 3.20 GHz | 19 | 562,9 | 3.3 | 
| Standard_H16mr | 16 | 2 | Intel (R) Xeon (R) CPU E5-2667 V3 @ 3.20 GHz | 18 | 563,6 | 3.7 | 
| Standard_H16r | 16 | 2 | Intel (R) Xeon (R) CPU E5-2667 V3 @ 3.20 GHz | 17 | 562,2 | 4.2 | 

## <a name="ls---storage-optimized"></a>LS – úložiště optimalizované

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_L4s | 4 | 1 | Intel (R) Xeon (R) CPU E5-2698B V3 @ 2,00 GHz | 29 | 122,7 | 6.6 | 
| Standard_L8s | 8 | 1 | Intel (R) Xeon (R) CPU E5-2698B V3 @ 2,00 GHz | 30 | 223,3 | 7,5 | 
| Standard_L16s | 16 | 1 | Intel (R) Xeon (R) CPU E5-2698B V3 @ 2,00 GHz | 31 | 397,3 | 2.5 | 
| Standard_L32s | 32 | 2 | Intel (R) Xeon (R) CPU E5-2698B V3 @ 2,00 GHz | 31 | 766,1 | 3,5 | 

## <a name="m---memory-optimized"></a>M – optimalizované pro paměť

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_M8 – 2 MS | 2 | 1 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 15 | 42,1 | 2.1 | 
| Standard_M8 – 4ms | 4 | 1 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 13 | 81,6 | 2.9 | 
| Standard_M16 – 4ms | 4 | 1 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 14 | 82,5 | 2.5 | 
| Standard_M16 – 8ms | 8 | 1 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 20 | 157,2 | 6.0 | 
| Standard_M32 – 8ms | 8 | 1 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 18 | 162,5 | 2.1 | 
| Standard_M32 – 16MS | 16 | 1 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 12 | 306,5 | 0,5 | 
| Standard_M64 | 64 | 2 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 11 | 1010,9 | 5.4 | 
| Standard_M64 – 16MS | 16 | 2 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 13 | 316,0 | 2,4 | 
| Standard_M64 – 32ms | 32 | 2 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 12 | 586,8 | 5.4 | 
| Standard_M64m | 64 | 2 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 12 | 1005,5 | 12,3 | 
| Standard_M64ms | 64 | 2 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 12 | 1012,9 | 12,5 | 
| Standard_M64s | 64 | 2 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 12 | 1012,5 | 4.5 | 
| Standard_M128 | 128 | 4 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 11 | 1777,3 | 15.6 | 
| Standard_M128 – 32ms | 32 | 4 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 13 | 620,5 | 2.5 | 
| Standard_M128 – 64ms | 64 | 4 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 12 | 1140,8 | 2.9 | 
| Standard_M128m | 128 | 4 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 12 | 1778,3 | 10.3 | 
| Standard_M128ms | 128 | 4 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 15 | 1780,7 | 18,3 | 
| Standard_M128s | 128 | 4 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 12 | 1775,8 | 11,6 | 
| Standard_M16ms | 16 | 1 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 20 | 293,1 | 11.8 | 
| Standard_M32ls | 32 | 1 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 13 | 535,2 | 4.8 | 
| Standard_M32ms | 32 | 1 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 11 | 534,1 | 4,6 | 
| Standard_M32ms | 32 | 2 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 1 | 589,0 | 0,0 | 
| Standard_M32ts | 32 | 1 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 12 | 538,6 | 3.2 | 
| Standard_M64ls | 64 | 2 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 13 | 1015,2 | 10.0 | 
| Standard_M8ms | 8 | 1 | Intel (R) Xeon (R) CPU E7-8890 V3 @ 2.50 GHz | 13 | 158,2 | 5.5 | 

## <a name="ncsv3---gpu-enabled"></a>NCSv3 – GPU zapnuto

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_NC6s_v3 | 6 | 1 | Intel (R) Xeon (R) CPU E5-2690 V4 @ 2.60 GHz | 6 | 230,2 | 1.6 | 
| Standard_NC12s_v3 | 12 | 1 | Intel (R) Xeon (R) CPU E5-2690 V4 @ 2.60 GHz | 7 | 425,0 | 3,6 | 
| Standard_NC24rs_v3 | 24 | 2 | Intel (R) Xeon (R) CPU E5-2690 V4 @ 2.60 GHz | 2 | 811,0 | 4.2 | 
| Standard_NC24s_v3 | 24 | 2 | Intel (R) Xeon (R) CPU E5-2690 V4 @ 2.60 GHz | 3 | 809,3 | 2.3 | 

## <a name="ncsv2---gpu-enabled"></a>NCSv2 – GPU zapnuto

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_NC6s_v2 | 6 | 1 | Intel (R) Xeon (R) CPU E5-2690 V4 @ 2.60 GHz | 11 | 227,0 | 6,2 | 
| Standard_NC12s_v2 | 12 | 1 | Intel (R) Xeon (R) CPU E5-2690 V4 @ 2.60 GHz | 9 | 427,3 | 1.3 | 
| Standard_NC24rs_v2 | 24 | 2 | Intel (R) Xeon (R) CPU E5-2690 V4 @ 2.60 GHz | 12 | 811,0 | 5.4 | 
| Standard_NC24s_v2 | 24 | 2 | Intel (R) Xeon (R) CPU E5-2690 V4 @ 2.60 GHz | 11 | 811,5 | 4.4 | 

## <a name="nc---gpu-enabled"></a>NC – GPU zapnuto

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_NC6 | 6 | 1 | Intel (R) Xeon (R) CPU E5-2690 V3 @ 2.60 GHz | 27 | 209,6 | 4.4 | 
| Standard_NC12 | 12 | 1 | Intel (R) Xeon (R) CPU E5-2690 V3 @ 2.60 GHz | 28 | 394,4 | 3.8 | 
| Standard_NC24 | 24 | 2 | Intel (R) Xeon (R) CPU E5-2690 V3 @ 2.60 GHz | 28 | 751,7 | 3,5 | 
| Standard_NC24r | 24 | 2 | Intel (R) Xeon (R) CPU E5-2690 V3 @ 2.60 GHz | 27 | 752,9 | 3.4 | 

## <a name="nds--gpu-enabled"></a>NDs – GPU zapnuto

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_ND6s | 6 | 1 | Intel (R) Xeon (R) CPU E5-2690 V4 @ 2.60 GHz | 8 | 230,1 | 1.2 | 
| Standard_ND12s | 12 | 1 | Intel (R) Xeon (R) CPU E5-2690 V4 @ 2.60 GHz | 11 | 426,5 | 1.4 | 
| Standard_ND24rs | 24 | 2 | Intel (R) Xeon (R) CPU E5-2690 V4 @ 2.60 GHz | 10 | 811,4 | 3,5 | 
| Standard_ND24s | 24 | 2 | Intel (R) Xeon (R) CPU E5-2690 V4 @ 2.60 GHz | 11 | 812,6 | 4.4 | 

## <a name="nv---gpu-enabled"></a>NV – GPU – povoleno

| Velikost | Virtuální procesory | Uzly NUMA | Procesor | Běží | Průměrná základní sazba | StdDev | 
| ---- | ----: | ---------: | --- | ---: | ------------: | -----: | 
| Standard_NV6 | 6 | 1 | Intel (R) Xeon (R) CPU E5-2690 V3 @ 2.60 GHz | 28 | 210,5 | 6.1 | 
| Standard_NV12 | 12 | 1 | Intel (R) Xeon (R) CPU E5-2690 V3 @ 2.60 GHz | 28 | 394,5 | 2.3 | 
| Standard_NV24 | 24 | 2 | Intel (R) Xeon (R) CPU E5-2690 V3 @ 2.60 GHz | 26 | 752,2 | 4.4 | 

## <a name="about-specint"></a>O SPECint
Počty oken byly vypočítány spuštěním [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) na Windows serveru. SPECint se spustil pomocí možnosti základní sazba (SPECint_rate2006) s jednou kopií na vCPU. SPECint se skládá z 12 samostatných testů, každý se spouští třikrát a bere medián hodnoty z každého testu a vážením má tvořit složené skóre. Tyto testy byly potom spuštěny napříč několika virtuálními počítači za účelem zadání průměrného počtu zobrazených výsledků.

## <a name="next-steps"></a>Další kroky
* Kapacitu úložiště, podrobnosti o disku a další požadavky pro výběr velikostí virtuálních počítačů najdete v tématu [velikosti virtuálních počítačů](../sizes.md).
---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/22/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: d3e0f2680cbe703edc553ddff310fba2d1ddf77f
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35323812"
---
Paměťově optimalizovaná nabídka velikosti virtuálních počítačů vysoký poměr paměti procesor, který se výborně hodí pro servery relační databáze, středních a velkých mezipaměti a analýzy v paměti. Tento článek obsahuje informace o počtu Vcpu, datové disky a síťové adaptéry, jakož i úložiště propustnost a šířku pásma sítě pro každou velikost v této skupině. 

* M-Series nabízí nejvyšší počet virtuálních procesorů (Vcpu až 128) a největší paměti (až 3.8 TiB) ve všech virtuálních počítačů v cloudu.  Je ideální pro extrémně velké databáze nebo další aplikace, kterým přináší výhody vysoký počet virtuálních procesorů a velké množství paměti.

* Dv2-series, G-series a svými protějšky DSv2 nebo GS jsou ideální pro aplikace, které potřebují rychlejší Vcpu, lepší výkon dočasné úložiště, nebo mít vyšší nároky na paměť.  Nabízejí výkonnou kombinaci pro mnoho podnikových aplikací.


* Virtuální počítače Dv2-series, nástupce původní řady D-series, mají výkonnější procesor. Procesor v Dv2-series je asi o 35 % rychlejší než procesor v D-series. Je založena na nejnovější generace 2.4 v3® GHz Intel Xeon E5-2673 2,4 GHz (Haswell) nebo E5-2673 v4 2.3 GHz (Broadwell) procesorů a s 2.0 technologie Intel Turbo nárůst, můžete přejít až 3.1 GHz. Řada Dv2-series má stejnou konfiguraci paměti a disku jako řada D.

* 2.3 GHz (Broadwell) procesoru Ev3-series funkce E5-2673 v4 v konfiguraci technologie hyper-threaded, poskytuje lepší nabízená hodnota pro nejobecnější účel úlohy a převedení Ev3 do zarovnání s obecné účely virtuální počítače většinu ostatních cloudů.  Paměť se rozšířila (z 7 GiB/virtuální procesory na 8 GiB/virtuální procesory) při omezení diskových a síťových byly upraveny na základě za jádra souladu s přechodem na Hyper-threadingem.  Ev3 je postupujte podle až do velikosti virtuálních počítačů velkého množství paměti D/Dv2 rodiny.

* Výpočtů Azure nabízí velikostí virtuálního počítače, které jsou izolované typu konkrétní hardware a vyhrazenou pro jednoho zákazníka.  Tyto velikosti virtuálního počítače jsou nejvhodnější pro úlohy, které vyžadují vysokou míru izolaci od ostatních zákazníků pro úlohy zahrnující elementy jako dodržování předpisů a zákonné požadavky.  Zákazníci také možné dále rozdělit prostředky těchto virtuálních počítačů izolované pomocí [podporu pro vnořené virtuální počítače Azure](https://azure.microsoft.com/en-us/blog/nested-virtualization-in-azure/).  Podrobnosti viz tabulky řady virtuální počítač pod pro izolované možnosti virtuálního počítače.

## <a name="esv3-series"></a>Esv3-series 

ACU: 160-190 <sup>1</sup>

Instance ESv3-series jsou založené na procesorech 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell), jejichž frekvence se s technologií Intel Turbo Boost 2.0 může zvýšit až na 3,5 GHz, a používají službu Premium Storage. Instance Ev3-series jsou ideální pro podnikové aplikace s vysokými nároky na paměť.


| Velikost             | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových adaptérů / očekává šířku pásma sítě (MB/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4 000 / 32 (50)                                                       | 3 200 / 48                                | 2 / 1,000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8 000 / 64 (100)                                                      | 6 400 / 96                                | 2 / 2,000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16 000 / 128 (200)                                                    | 12 800 / 192                              | 4 / 4,000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32 000 / 256 (400)                                                    | 25 600 / 384                              | 8 / 8,000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64 000 / 512 (800)                                                    | 51 200 / 768                              | 8 / 16,000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128 000 / 1 024 (1 600)                                                   | 80 000 / 1 200                             | 8 / 30,000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128 000 / 1 024 (1 600)                                                   | 80 000 / 1 200                             | 8 / 30,000                             |


<sup>1</sup> Esv3-series Virtuálního počítače funkci technologii Intel® Hyper-Threading.

<sup>2</sup> omezené základní velikostí, které jsou k dispozici.

<sup>3</sup> instance je izolovaný hardware vyhrazený pro jednoho zákazníka.


## <a name="ev3-series"></a>Ev3-series 

ACU: 160-190 <sup>1</sup>

Instance Ev3-series jsou založené na procesorech 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell) a s technologií Intel Turbo Boost 2.0 se jejich frekvence může zvýšit až na 3,5 GHz. Instance Ev3-series jsou ideální pro podnikové aplikace s vysokými nároky na paměť.

Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disky Premium Storage, použijte velikosti ESv3. Měřiče cen a účtování pro velikosti ESv3 jsou stejné jako pro Ev3-series. 


| Velikost            | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Max. počet NIC / Šířka pásma sítě |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3 000 / 46 / 23                                               | 2 / 1,000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6 000 / 93 / 46                                               | 2 / 2,000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12 000 / 187 / 93                                             | 4 / 4,000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24 000 / 375 / 187                                            | 8 / 8,000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48 000 / 750 / 375                                            | 8 / 16,000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96 000 / 1 000 / 500                                           | 8 / 30,000           |
| Standard_E64i_v3&nbsp;<sup>2</sup> | 64        | 432         | 1600           | 32             | 96 000 / 1 000 / 500                                           | 8 / 30,000           |

<sup>1</sup> Ev3-series Virtuálního počítače funkci technologii Intel® Hyper-Threading.

<sup>2</sup> omezené základní velikostí, které jsou k dispozici. 


## <a name="m-series"></a>M-Series 

ACU: 160-180 <sup>1</sup>

| Velikost            | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových adaptérů / očekává šířku pásma sítě (MB/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M64s  | 64   | 1024        | 2 048           | 64             | 80 000 / 800 (6 348)       | 40 000 / 1 000                            | 8 / 16 000          |
| Standard_M64ms  | 64   | 1 792        | 2 048           | 64             | 80 000 / 800 (6 348)       | 40 000 / 1 000                            | 8 / 16 000          |
| Standard_M128s&nbsp;<sup>2,&nbsp;3</sup> | 128  | 2 048        | 4 096           | 64             | 160 000 / 1 600 (12 696) | 80 000 / 2 000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892        | 4 096           | 64             | 160 000 / 1 600 (12 696) | 80 000 / 2 000                            | 8 / 30000          |

<sup>1</sup> funkce M-series Virtuálního počítače s technologií Intel® Hyper-Threading

<sup>2</sup> víc než 64 virtuálních procesorů se vyžaduje jedna z těchto podporované hostované operační systémy: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 a Red Hat Enterprise Linux, CentOS 7.3 nebo Oracle Linux 7.3 s LIS 4.2.1.

<sup>3</sup> omezené základní velikostí, které jsou k dispozici.

<sup>4</sup> instance je izolovaný hardware vyhrazený pro jednoho zákazníka.
<br>

## <a name="gs-series"></a>Řada GS 

ACU: 180-240 <sup>1</sup>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových adaptérů / očekává šířku pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10 000 / 100 (264) |5 000 / 125 |2 / 2 000 |
| Standard_GS2 |4 |56 |112 |16 |20 000 / 200 (528) |10 000 / 250 |2 / 4 000 |
| Standard_GS3 |8 |112 |224 |32 |40 000 / 400 (1 056) |20 000 / 500 |4 / 8 000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80 000 / 800 (2 112) |40 000 / 1 000 |8 / 16 000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160 000 / 1 600 (4 224) |80 000 / 2 000 |8 / 20 000 |

<sup>1</sup> propustnost maximální disku (IOPS nebo MB/s) možné pomocí řady GS virtuálních počítačů může být omezena číslo, velikost a proložení připojené disky. Podrobnosti viz článek [Premium Storage: Vysoce výkonné úložiště pro virtuální počítače Azure](../articles/virtual-machines/windows/premium-storage.md). 

<sup>2</sup> instance je izolovaný hardware vyhrazený pro jednoho zákazníka.

<sup>3</sup> omezené základní velikostí, které jsou k dispozici.

<br>

## <a name="g-series"></a>G-series

ACU: 180–240

| Velikost         | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální propustnost datových disků: IOPS | Maximální počet síťových adaptérů / očekává šířku pásma sítě (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8 / 8 × 500                       | 2 / 2 000                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 16 / 16 × 500                       | 2 / 4 000                     |
| Standard_G3  | 8         | 112         | 1 536          | 24000 / 375 / 187                                        | 32 / 32 × 500                     | 4 / 8 000                |
| Standard_G4  | 16        | 224         | 3 072          | 48000 / 750 / 375                                        | 64 / 64 × 500                     | 8 / 16 000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6 144          | 96000 / 1500 / 750                                       | 64 / 64 × 500                     | 8 / 20 000           |

<sup>1</sup> instance je izolovaný hardware vyhrazený pro jednoho zákazníka.
<br>


## <a name="dsv2-series"></a>DSv2-series 

ACU: 210 – 250 <sup>1</sup>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových adaptérů / očekává šířku pásma sítě (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8 000 / 64 (72) |6 400 / 96 |2 / 1 500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16 000 / 128 (144) |12 800 / 192 |4 / 3 000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32 000 / 256 (288) |25 600 / 384 |8 / 6 000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64 000 / 512 (576) |51 200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80 000 / 640 (720) |64 000 / 960 |8 / 25000&nbsp;<sup>4</sup>


<sup>1</sup> propustnost maximální disku (IOPS nebo MB/s) možné pomocí řady DSv2 virtuálních počítačů může být omezena číslo, velikost a proložení připojené disky.  Podrobnosti viz článek [Premium Storage: Vysoce výkonné úložiště pro virtuální počítače Azure](../articles/virtual-machines/windows/premium-storage.md).

<sup>2</sup> instance je izolovaný hardware vyhrazený pro jednoho zákazníka.

<sup>3</sup> omezené základní velikostí, které jsou k dispozici.

<sup>4</sup> 25000 MB/s se Zrychlený sítěmi.

<br>

## <a name="dv2-series"></a>Dv2-series

ACU: 210–250

| Velikost              | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální propustnost datových disků: IOPS | Maximální počet síťových adaptérů / očekává šířku pásma sítě (MB/s) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8×500                         | 2 / 1 500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16×500                         | 4 / 3 000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32×500                       | 8 / 6 000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1 000          | 60000 / 937 / 468                                        | 64 / 64 x 500                       | 8 / 25 000&nbsp;<sup>2</sup> |

<sup>1</sup> instance je izolovaný hardware vyhrazený pro jednoho zákazníka. 

<sup>2</sup> 25000 MB/s se Zrychlený sítěmi.



<br>




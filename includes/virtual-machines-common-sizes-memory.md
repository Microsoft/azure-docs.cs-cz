---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 97efb82f104742993f7b2fac40a74f4feb9e0b38
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333889"
---
Paměťově optimalizované velikosti nabídky virtuálních počítačů vysoký poměr paměti procesoru, které jsou velmi vhodné pro servery relačních databází, střední a velké mezipaměti a analýzu v paměti. Tento článek obsahuje informace o počtu virtuálních procesorů, datové disky a síťové adaptéry, jakož i úložiště propustnost a šířku pásma sítě pro jednotlivé velikosti v této skupině. 

* Řada M-Series nabízí nejvyšší počet virtuálních procesorů (až 128 virtuálních procesorů) a největší paměť (až do 3,8 TiB) ze všech virtuálních počítačů v cloudu.  Je ideální pro extrémně velké databáze nebo další aplikace, kterým přináší výhody vysoký počet virtuálních procesorů a velké množství paměti.

* Řady Dv2-series, G-series a jejich protějšky DSv2 a GS jsou ideální pro aplikace, které vyžadují rychlejší virtuální procesory, vyšší výkon dočasného úložiště nebo mají vysoké nároky na paměť.  Nabízejí výkonnou kombinaci pro mnoho podnikových aplikací.


* Virtuální počítače Dv2-series, nástupce původní řady D-series, mají výkonnější procesor. Procesor v Dv2-series je asi o 35 % rychlejší než procesor v D-series. Je založen na nejnovější generace 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) 2,4 GHz nebo E5-2673 v4 (Broadwell) 2,3 GHz procesorech a s technologií Intel Turbo Boost Technology 2.0 můžete až na 3,1 GHz. Řada Dv2-series má stejnou konfiguraci paměti a disku jako řada D.

* Ev3-series funkce E5-2673 v4 (Broadwell) 2,3 GHz procesor konfigurace technologie hyper-threaded, poskytuje lepší návrh hodnoty pro většinu obecných úloh účel a přináší Ev3 soulad s virtuálními počítači pro obecné účely většina jiných cloudech.  Paměti došlo k rozbalení (ze 7 GiB/virtuální procesor do 8 GiB/virtuálních procesorů) zatímco disku a sítě limity byly upraveny na základě vázané na jádro, aby bylo v souladu s přechodem na hyperthreadingem.  Ev3 je dále až vysoký poměr paměti velikosti virtuálních počítačů řady D/Dv2.

* Azure Compute nabízí velikosti virtuálních počítačů, které jsou izolované na konkrétní typ hardwaru a vyhrazené pro jednoho zákazníka.  Tyto velikosti virtuálních počítačů jsou nejvhodnější pro úlohy, které vyžadují vysoký stupeň izolace od ostatních zákazníků a kterých se týkají například požadavky na dodržování předpisů a zákonné požadavky.  Zákazníci můžou dál rozdělit prostředky těchto virtuálních počítačů izolované pomocí vybrat [podpora Azure pro vnořených virtuálních počítačích](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Projděte si tabulky virtuálních počítačů řady níže pro izolované možnosti virtuálního počítače.

## <a name="esv3-series"></a>Esv3-series 

ACU: 160-190 <sup>1</sup>

Premium Storage:  Podporováno

Premium Storage ukládání do mezipaměti:  Podporováno

Instance ESv3-series jsou založené na procesorech 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell), jejichž frekvence se s technologií Intel Turbo Boost 2.0 může zvýšit až na 3,5 GHz, a používají službu Premium Storage. Instance Ev3-series jsou ideální pro podnikové aplikace s vysokými nároky na paměť.


| Velikost             | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost v mezipaměti a dočasného úložiště: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MBps | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4 000 / 32 (50)                                                       | 3 200 / 48                                | 2 / 1,000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8 000 / 64 (100)                                                      | 6 400 / 96                                | 2 / 2,000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16 000 / 128 (200)                                                    | 12 800 / 192                              | 4 / 4,000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32 000 / 256 (400)                                                    | 25 600 / 384                              | 8 / 8,000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40,000 / 320 (400)                                                    | 32,000 / 480                              | 8 / 10,000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64 000 / 512 (800)                                                    | 51 200 / 768                              | 8 / 16,000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128 000 / 1 024 (1 600)                                                   | 80 000 / 1 200                             | 8 / 30,000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128 000 / 1 024 (1 600)                                                   | 80 000 / 1 200                             | 8 / 30,000                             |


<sup>1</sup> Esv3-series Virtuálních počítačů vybavené technologií Intel® Hyper-Threading.

<sup>2</sup> core dostupné velikosti omezen.

<sup>3</sup> instance je izolovaná na hardware vyhrazený pro jediného zákazníka.


## <a name="ev3-series"></a>Ev3-series 

ACU: 160 - 190 <sup>1</sup>

Premium Storage:  Nepodporuje se

Premium Storage ukládání do mezipaměti:  Nepodporuje se

Instance Ev3-series jsou založené na procesorech 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell) a s technologií Intel Turbo Boost 2.0 se jejich frekvence může zvýšit až na 3,5 GHz. Instance Ev3-series jsou ideální pro podnikové aplikace s vysokými nároky na paměť.

Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disky Premium Storage, použijte velikosti ESv3. Měřiče cen a účtování pro velikosti ESv3 jsou stejné jako pro Ev3-series. 


| Velikost            | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Max. počet NIC / Šířka pásma sítě |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3 000 / 46 / 23                                               | 2 / 1,000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6 000 / 93 / 46                                               | 2 / 2,000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12 000 / 187 / 93                                             | 4 / 4,000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24 000 / 375 / 187                                            | 8 / 8,000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10,000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48 000 / 750 / 375                                            | 8 / 16,000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96 000 / 1 000 / 500                                           | 8 / 30,000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96 000 / 1 000 / 500                                           | 8 / 30,000           |

<sup>1</sup> Ev3-series Virtuálních počítačů vybavené technologií Intel® Hyper-Threading.

<sup>2</sup> core dostupné velikosti omezen.

<sup>3</sup> instance je izolovaná na hardware vyhrazený pro jediného zákazníka.


## <a name="m-series"></a>M-Series 

ACU: 160-180 <sup>1</sup>

Premium Storage:  Podporováno

Premium Storage ukládání do mezipaměti:  Podporováno

Akcelerátor zápisu:  [Podporuje se](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Velikost            | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost v mezipaměti a dočasného úložiště: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MBps | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218.75 | 256  | 8  | 10,000 / 100 (793)  | 5,000  / 125 | 4 / 2,000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437.5  | 512  | 16 | 20,000 / 200 (1,587) | 10 000 / 250 | 8 / 4,000 |
| Standard_M32ts | 32 | 192    | 1,024 | 32 | 40,000 / 400 (3,174) | 20 000 / 500 | 8 / 8,000 |
| Standard_M32ls | 32 | 256    | 1,024 | 32 | 40,000 / 400 (3,174) | 20 000 / 500 | 8 / 8,000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1,024 | 32 | 40,000 / 400 (3,174) | 20 000 / 500 | 8 / 8,000 |
| Standard_M64s  | 64 | 1,024   | 2,048 | 64 | 80,000 / 800 (6,348)| 40 000 / 1 000 | 8 / 16,000          |
| Standard_M64ls  | 64 | 512    | 2,048 | 64 | 80,000 / 800 (6,348) | 40 000 / 1 000 | 8 / 16,000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1,792 | 2,048 | 64 | 80,000 / 800 (6,348)| 40 000 / 1 000 | 8 / 16,000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2,048        | 4,096  | 64 | 160 000 / 1 600 (12 696) | 80 000 / 2 000                            | 8 / 30,000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3,892  | 4,096 | 64 | 160 000 / 1 600 (12 696) | 80 000 / 2 000                            | 8 / 30,000          |
| Standard_M64   | 64  | 1,024 | 7,168  | 64 | 80,000  / 800  (1,228) | 40 000 / 1 000 | 8 / 16,000 |
| Standard_M64m  | 64  | 1,792 | 7,168  | 64 | 80,000  / 800  (1,228) | 40 000 / 1 000 | 8 / 16,000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2,048 | 14,336 | 64 | 250,000 / 1,600 (2,456) | 80 000 / 2 000 | 8 / 32,000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3,892 | 14,336 | 64 | 250,000 / 1,600 (2,456) | 80 000 / 2 000 | 8 / 32,000 |



<sup>1</sup> funkce virtuálních počítačů řady M-series technologii Intel® Hyper-Threading

<sup>2</sup> více než 64 virtuálních procesorů se vyžaduje jednu z těchto podporovaných hostovaných operačních systémů: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 a Red Hat Enterprise Linux, CentOS 7.3 nebo Oracle Linux 7.3 s LIS 4.2.1.

<sup>3</sup> core dostupné velikosti omezen.

<sup>4</sup> instance je izolovaná na hardware vyhrazený pro jediného zákazníka.
<br>

## <a name="gs-series"></a>Řada GS 

ACU: 180 - 240 <sup>1</sup>

Premium Storage:  Podporováno

Premium Storage ukládání do mezipaměti:  Podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost v mezipaměti a dočasného úložiště: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MBps | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10 000 / 100 (264) |5 000 / 125 |2 / 2 000 |
| Standard_GS2 |4 |56 |112 |16 |20 000 / 200 (528) |10 000 / 250 |2 / 4 000 |
| Standard_GS3 |8 |112 |224 |32 |40 000 / 400 (1 056) |20 000 / 500 |4 / 8 000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80 000 / 800 (2 112) |40 000 / 1 000 |8 / 16 000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160 000 / 1 600 (4 224) |80 000 / 2 000 |8 / 20 000 |

<sup>1</sup> maximální propustnost disku (IOPS nebo MB/s) se GS-series virtuálních počítačů může být omezená počtem, velikostí a prokládáním připojených disků. Podrobnosti najdete v tématu [návrh pro vysoký výkon](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>2</sup> instance je izolovaná na hardware vyhrazený pro jediného zákazníka.

<sup>3</sup> core dostupné velikosti omezen.

<br>

## <a name="g-series"></a>G-series

ACU: 180 - 240

Premium Storage:  Nepodporuje se

Premium Storage ukládání do mezipaměti:  Nepodporuje se

| Velikost         | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální počet datových disků nebo propustnost: IOPS | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8 / 8 × 500                       | 2 / 2 000                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 16 / 16 × 500                       | 2 / 4 000                     |
| Standard_G3  | 8         | 112         | 1 536          | 24000 / 375 / 187                                        | 32 / 32 × 500                     | 4 / 8 000                |
| Standard_G4  | 16        | 224         | 3 072          | 48000 / 750 / 375                                        | 64 / 64 × 500                     | 8 / 16 000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6 144          | 96000 / 1500 / 750                                       | 64 / 64 × 500                     | 8 / 20 000           |

<sup>1</sup> instance je izolovaná na hardware vyhrazený pro jediného zákazníka.
<br>


## <a name="dsv2-series-11-15"></a>DSv2-series 11-15

ACU: 210 - 250 <sup>1</sup>

Premium Storage:  Podporováno

Premium Storage ukládání do mezipaměti:  Podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost v mezipaměti a dočasného úložiště: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MBps | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8 000 / 64 (72) |6 400 / 96 |2 / 1 500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16 000 / 128 (144) |12 800 / 192 |4 / 3 000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32 000 / 256 (288) |25 600 / 384 |8 / 6 000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64 000 / 512 (576) |51 200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80 000 / 640 (720) |64 000 / 960 |8 / 25000&nbsp;<sup>4</sup>


<sup>1</sup> maximální propustnost disku (IOPS nebo MB/s) se DSv2-series virtuálních počítačů může být omezená počtem, velikostí a prokládáním připojených disků.  Podrobnosti najdete v tématu [návrh pro vysoký výkon](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>2</sup> instance je izolovaná na hardware vyhrazený pro jediného zákazníka.

<sup>3</sup> core dostupné velikosti omezen.

<sup>4</sup> 25000 MB/s s Akcelerovanými síťovými službami.

<br>

## <a name="dv2-series-11-15"></a>Řady Dv2-series 11-15

ACU: 210 - 250

Premium Storage:  Nepodporuje se

Premium Storage ukládání do mezipaměti:  Nepodporuje se

| Velikost              | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální počet datových disků nebo propustnost: IOPS | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8×500                         | 2 / 1 500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16×500                         | 4 / 3 000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32×500                       | 8 / 6 000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1 000          | 60000 / 937 / 468                                        | 64 / 64 x 500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> instance je izolovaná na hardware vyhrazený pro jediného zákazníka. 

<sup>2</sup> 25000 MB/s s Akcelerovanými síťovými službami.



<br>




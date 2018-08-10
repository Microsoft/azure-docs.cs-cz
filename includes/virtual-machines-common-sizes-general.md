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
ms.openlocfilehash: 1606b5e38b79cb535108ca747783a05de1fbc982
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40027110"
---
Velikosti virtuálních počítačů pro obecné účely poskytují vyvážený poměr procesorů k paměti. Ideální pro testování a vývoj, malé a střední databáze a webové servery s nízkým a středním provozem. Tento článek obsahuje informace o počtu virtuálních procesorů, datové disky a síťové adaptéry, jakož i propustnost úložiště pro velikosti v této skupině. 

- Virtuální počítače řady Av2-series je možné nasadit na celou řadu typů hardwaru a procesorů. Velikost je omezována v závislosti na použitém hardwaru a nabízí konzistentní výkon procesoru pro spuštěné instance bez ohledu na hardware. Pokud chcete zjistit fyzický hardware, na kterém je virtuální počítač dané velikosti nasazený, zadejte dotaz na virtuální hardware přímo z virtuálního počítače.

- Virtuální počítače Dv2-series, nástupce původní řady D-series, mají výkonnější procesor. Procesor v Dv2-series je asi o 35 % rychlejší než procesor v D-series. Je založen na nejnovější generace Intel Xeon® E5-2673 v3 (Haswell) 2,4 GHz nebo E5-2673 v4 (Broadwell) 2,3 GHz procesorech a s technologií Intel Turbo Boost Technology 2.0 můžete až na 3,1 GHz. Řada Dv2-series má stejnou konfiguraci paměti a disku jako řada D.

- Dv3-series, funkcí procesoru 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) procesor nebo nejnovější procesorech 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell) procesoru v konfiguraci typu hyper, poskytuje lepší návrh hodnoty pro většinu obecných úloh účel.  Paměti došlo k rozbalení (z ~3.5 GiB/virtuální procesor do 4 GB a vCPU) zatímco disku a sítě limity byly upraveny na základě vázané na jádro, aby bylo v souladu s přechodem na hyperthreadingem.  Dv3 už má vysoký poměr paměti velikosti virtuálních počítačů řady D/Dv2, ty se přesunuly na novou řadu Ev3.


## <a name="b-series"></a>B-Series

Storage úrovně Premium: podporováno

Premium Storage ukládání do mezipaměti: Není podporováno

Zvládáním výkonových špiček virtuální počítače řady B-series jsou ideální pro úlohy, které nejsou nutné plný výkon procesoru nepřetržitě, jako jsou webové servery, malé databáze a vývoj a testovací prostředí. Tyto úlohy mají obvykle požadavky na výkon zvládáním výkonových špiček. B-Series poskytuje tyto zákazníkům možnost zakoupení velikost virtuálního počítače s výkonem při provádění základní ceny, umožňující instance virtuálního počítače Vybudujte kredity, když virtuální počítač využívá míň než jeho základní výkon. Když virtuální počítač se nashromáždil kredit, můžete převést virtuální počítač nad základní Virtuálního počítače pomocí až 100 % procesoru, když vaše aplikace vyžaduje vyšší výkon procesoru.


| Velikost             | Virtuální procesory  | Paměť: GiB | Místní SSD: GiB | Základní výkonu jádro | Kredity bankovních / hodina | Maximální počet bankovních kredity | Max. datových disků | Maximální výkon místního disku: IOPS / MB/s | Maximální počet bez mezipaměti výkonu disku: IOPS / MB/s | Maximální počet síťových karet |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1s  | 1           | 1              | 4                          | 10 %                   | 6                  | 144            | 2                                      | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20 %                   | 12                 | 288            | 2                                      | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |


## <a name="dsv3-series-sup1sup"></a>Řada Dsv3-series <sup>1</sup>

ACU: 160–190

Storage úrovně Premium: podporováno

Ukládání do mezipaměti služby Premium Storage: podporováno

Velikosti Dsv3-series jsou založené na procesoru 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) procesor nebo nejnovější procesorech 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell) procesor, který může s technologií Intel Turbo Boost 2.0 dosáhnout frekvence až 3,5 GHz a používají službu premium storage. Velikosti Dsv3-series nabízejí kombinaci virtuálního procesoru, paměti a dočasného úložiště pro většinu produkčních úloh.


| Velikost             | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4 000 / 32 (50)                                                       | 3 200 / 48                                | 2 / 1,000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8 000 / 64 (100)                                                      | 6 400 / 96                                | 2 / 2,000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16 000 / 128 (200)                                                    | 12 800 / 192                              | 4 / 4,000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32 000 / 256 (400)                                                    | 25 600 / 384                              | 8 / 8,000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64 000 / 512 (800)                                                    | 51 200 / 768                              | 8 / 16,000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128,000 / 1024 (1600)                                                    | 80 000 / 1 200                              | 8 / 30,000                                               |

<sup>1</sup> Dsv3-series Virtuálních počítačů procesorech Intel® Hyper-Threading technologie

## <a name="dv3-series-sup1sup"></a>Řada Dv3-series <sup>1</sup>

ACU: 160–190

Storage úrovně Premium: Není podporováno.

Premium Storage ukládání do mezipaměti: Není podporováno

Velikosti Dv3-series jsou založené na procesoru 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) procesor nebo procesorech 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell) procesor, které můžete dosáhnout s technologií Intel Turbo Boost 2.0 dosáhnout frekvence až 3,5 GHz. Velikosti Dv3-series nabízejí kombinaci virtuálního procesoru, paměti a dočasného úložiště pro většinu produkčních úloh.

Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disky Premium Storage, použijte velikosti Dsv3. Měřiče cen a účtování pro velikosti Dsv3 jsou stejné jako pro Dv3-series. 


| Velikost            | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Max. počet NIC / Šířka pásma sítě |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3 000 / 46 / 23                                               | 2 / 1,000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6 000 / 93 / 46                                               | 2 / 2,000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12 000 / 187 / 93                                             | 4 / 4,000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24 000 / 375 / 187                                            | 8 / 8,000                    |
| Standard_D32_v3 | 32        | 128          | 800            | 32             | 48 000 / 750 / 375                                            | 8 / 16,000                             |
| Standard_D64_v3 | 64        | 256          | 1600            | 32             | 96 000 / 1 000 / 500                                            | 8 / 30,000                             |

<sup>1</sup> virtuálních počítačů Dv3-series vybavené technologií Intel® Hyper-Threading


## <a name="dsv2-series"></a>DSv2-series

ACU: 210–250

Storage úrovně Premium: podporováno

Ukládání do mezipaměti služby Premium Storage: podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3,5 |7 |4 |4 000 / 32 (43) |3 200 / 48 |2 / 750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8 000 / 64 (86) |6 400 / 96 |2 / 1 500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16 000 / 128 (172) |12 800 / 192 |4 / 3 000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32 000 / 256 (344) |25 600 / 384 |8 / 6 000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64 000 / 512 (688) |51 200 / 768 |8 / 12000 |



## <a name="dv2-series"></a>Dv2-series

ACU: 210–250

Storage úrovně Premium: Není podporováno.

Premium Storage ukládání do mezipaměti: Není podporováno

| Velikost           | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Max. datových disků | Propustnost: IOPS | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standard_D1_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 4              | 4×500            | 2 / 750                                      |
| Standard_D2_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 8              | 8×500            | 2 / 1 500                                     |
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16             | 16×500           | 4 / 3 000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32             | 32×500           | 8 / 6 000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64             | 64 x 500           | 8 / 12000                                    |


## <a name="av2-series"></a>Av2-series

ACU: 100

Storage úrovně Premium: Není podporováno.

Premium Storage ukládání do mezipaměti: Není podporováno

| Velikost            | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální propustnost datových disků: IOPS | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2×500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4×500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8×500               | 4 / 1 000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16×500             | 8 / 2 000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4×500               | 2 / 500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8×500               | 4 / 1 000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16×500             | 8 / 2 000                     |

<br>






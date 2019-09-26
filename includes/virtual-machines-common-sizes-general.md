---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 5d89feaa5225aea56af86aa7f70d3666994fdaec
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266887"
---
Velikosti virtuálních počítačů pro obecné účely poskytují vyvážený poměr PROCESORů k paměti. Ideální pro testování a vývoj, malé a střední databáze a webové servery s nízkým a středním provozem. Tento článek poskytuje informace o počtu vCPU, datových discích a síťových adaptérů i propustnosti úložiště pro velikosti v tomto seskupení.

- [DC-Series](#dc-series) je řada virtuálních počítačů v Azure, která vám může přispět k ochraně důvěrnosti a integrity dat a kódu při jejich zpracování ve veřejném cloudu. Tyto počítače jsou založené na nejnovější generaci procesorů Intel XEON E-2176G s frekvencí 3,7 GHz a technologií SGX. S technologií Intel Turbo Boost Technology mohou tyto počítače dosáhnout frekvence až 4,7 GHz. Instance řady DC umožňují zákazníkům sestavovat bezpečné aplikace založené na enklávě, aby se při jejich použití chránil kód a data.

- Virtuální počítače řady Av2-Series je možné nasadit na nejrůznější typy hardwaru a procesory. Díky konfiguraci paměti a výkonu procesoru jsou virtuální počítače řady A-Series nejvhodnější pro úlohy základní úrovně, jako je vývoj a testování. Velikost je omezována v závislosti na použitém hardwaru a nabízí konzistentní výkon procesoru pro spuštěné instance bez ohledu na hardware. Pokud chcete zjistit fyzický hardware, na kterém je virtuální počítač dané velikosti nasazený, zadejte dotaz na virtuální hardware přímo z virtuálního počítače.

  Příklady případů použití zahrnují vývojové a testovací servery, webové servery s nízkým provozem, malé až střední databáze, testování konceptů a úložiště kódu.

- Dv2-Series, následný postup pro původní řadu D, nabízí výkonnější procesor a optimální konfiguraci procesoru a paměti, která je vhodná pro většinu produkčních úloh. Procesor v Dv2-series je asi o 35 % rychlejší než procesor v D-series. Je založený na nejnovější generaci procesorů Intel Xeon® E5-2673 V3 2,4 GHz (Haswell) nebo E5-2673 V4 2,3 GHz (Broadwell) a s technologií Intel Turbo 2,0 může jít až o 3,1 GHz. Řada Dv2-series má stejnou konfiguraci paměti a disku jako řada D.

- Sada Dv3-Series obsahuje procesor Intel® Xeon s procesorem 2,4 GHz, Haswell (), nebo nejnovější procesory 2,3 GHz Intel Xeon® E5-2673 v4 (Broadwell) v konfiguraci s více vlákny, které poskytují lepší polohu pro většinu úloh pro obecné účely.  Paměť se rozšířila (od ~ 3,5 GiB/vCPU na 4 GiB/vCPU), zatímco limity disku a sítě se přizpůsobily na základě počtu jader, aby se přerovnaly k přechodu na vlákno.  Dv3 už nemá vysoké velikosti virtuálních počítačů řady D/Dv2, které se přesunuly na novou rodinu Ev3.

  Příklady případů použití řady D-Series zahrnují aplikace na podnikové úrovni, relační databáze, ukládání do mezipaměti v paměti a analýzy.

- Řady da-Series a Dasv3-Series jsou novými velikostmi, které využívají procesor AMD 2.35 EPYC<sup>TM</sup> 7452V v konfiguraci 256 s více vlákny, která vynásobí 8 GB této mezipaměti L3 do každé 8 jader, což zvyšuje možnosti zákazníků na jejich všeobecné používání. zatížení pro účely. Řady da-Series a Dasv3-Series mají stejnou konfiguraci paměti a disku jako řada D & Dsv3-Series.
  
## <a name="b-series"></a>B-Series

Premium Storage:  Podporováno

Ukládání Premium Storage do mezipaměti:  Není podporováno

Virtuální počítače s podporou řady B-Series jsou ideální pro úlohy, které nepotřebují průběžný výkon procesoru, jako jsou webové servery, malé databáze a vývojové a testovací prostředí. Tyto úlohy mají typicky požadavky na výkon při roztržení. Řada B-Series poskytuje zákazníkům možnost zakoupit si velikost virtuálního počítače pomocí cenového plánu s vědomím cen, který umožňuje instanci virtuálního počítače sestavovat kredity, když virtuální počítač využívá méně než základní výkon. Když se na virtuálním počítači nashromáždil kredit, může virtuální počítač zvýšit kapacitu virtuálního počítače pomocí až 100% procesoru, když vaše aplikace vyžaduje vyšší výkon procesoru.

Příklady případů použití zahrnují vývojové a testovací servery, webové servery s nízkým provozem, malé databáze, mikroslužby, servery pro testování konceptů, sestavovací servery.


| Size             | Virtuální procesory  | Paměť: GiB | Dočasné úložiště (SSD): GiB | Základní výkon CPU virtuálního počítače | Maximální výkon CPU virtuálního počítače | Počáteční kredity | Uložené kredity za hodinu | Max. uložených kreditů | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5 %                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10 %                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20 %                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202 %                  | 1200 %                   | 360                   | 121                 | 2909           | 16                                     | 6480/75                                 | 4320 / 50                                 | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270 %                  | 1600 %                   | 480                   | 162                 | 3888           | 32                                     | 8640 / 100                                 | 4320 / 50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337 %                  | 2000 %                   | 600                   | 203                 | 4860           | 32                                     | 10800/125                                 | 4320 / 50                                 | 8  |

<sup>1</sup> B1ls se podporuje jenom na Linux.

## <a name="dsv3-series-sup1sup"></a>Dsv3-Series <sup>1</sup>

ACU 160-190

Premium Storage:  Podporováno

Ukládání Premium Storage do mezipaměti:  Podporováno

Velikosti řady Dsv3-Series jsou založené 2,4 na procesoru Intel Xeon® E5-2673 V3 (Haswell) nebo nejnovějším 2,3 GHz procesory Intel® Xeon s podporou technologie Intel 2,0 Turbo 2673 v4 (Broadwell). Velikosti Dsv3-series nabízejí kombinaci virtuálního procesoru, paměti a dočasného úložiště pro většinu produkčních úloh.


| Size             | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4000/32 (50)                                                       | 3200/48                                | 2 / 1 000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8000 / 64 (100)                                                      | 6400/96                                | 2 / 2 000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16000/128 (200)                                                    | 12800/192                              | 4 / 4000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32000/256 (400)                                                    | 25600 / 384                              | 8 / 8000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64000 / 512 (800)                                                    | 51200/768                              | 8 / 16 000                                               |
| Standard_D48s_v3 | 48     | 192          | 384            | 32             | 96000/768 (1200)                                                    | 76800/1152                               | 8 / 24000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128000 / 1024 (1600)                                                    | 80000/1200                              | 8 / 30000                                               |

<sup>1</sup> Dsv3 – funkce virtuálního počítače řady Intel® technologie Hyper-Threading

## <a name="dasv3-series-preview"></a>Dasv3-Series (Preview)

Premium Storage: Podporováno

Ukládání Premium Storage do mezipaměti: Podporováno

Velikosti řady Dasv3-Series jsou založené na procesoru AMD EPYC<sup>TM</sup> 7452 v 2.35 GHz, který může dosáhnout zvýšení Fmax – z 3.35 GHz a použít Premium Storage. Velikosti řady Dasv3-Series nabízejí kombinaci vCPU, paměti a dočasného úložiště pro většinu produkčních úloh.

Pokud [si chcete zaregistrovat verzi Preview, klikněte sem](http://aka.ms/azureamdpreview).

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB |
|---|---|---|---|
| Standard_D2as_v3  | 2  | 8   | 16  |
| Standard_D4as_v3  | 4  | 16  | 32  |
| Standard_D8as_v3  | 8  | 32  | 64  |
| Standard_D16as_v3 | 16 | 64  | 128 |
| Standard_D32as_v3 | 32 | 128 | 256 |
| Standard_D48as_v3 | 48 | 192 | 384 |
| Standard_D64as_v3 | 64 | 256 | 512 |

## <a name="dv3-series-sup1sup"></a>Dv3-Series <sup>1</sup>

ACU 160-190

Premium Storage:  Není podporováno

Ukládání Premium Storage do mezipaměti:  Není podporováno

Velikosti řady Dv3-Series jsou založené na procesoru Intel® Xeon s procesorem 2,4 GHz, nebo 2,3 GHz procesory Intel Xeon® E5-2673 v4 (Broadwell), které mohou dosáhnout 3,5 GHz pomocí technologie Intel Turbo zvyšovat 2,0. Velikosti Dv3-series nabízejí kombinaci virtuálního procesoru, paměti a dočasného úložiště pro většinu produkčních úloh.

Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disky Premium Storage, použijte velikosti Dsv3. Měřiče cen a účtování pro velikosti Dsv3 jsou stejné jako pro Dv3-series. 


| Velikost            | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště: IOPS/čtení MB/s/zápis MB/s | Max. počet NIC / Šířka pásma sítě |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3 000 / 46 / 23                                               | 2 / 1 000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6 000 / 93 / 46                                               | 2 / 2 000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12 000 / 187 / 93                                             | 4 / 4000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24 000 / 375 / 187                                            | 8 / 8000                    |
| Standard_D32_v3 | 32        | 128         | 800            | 32             | 48 000 / 750 / 375                                            | 8 / 16 000                   |
| Standard_D48_v3 | 48        | 192          | 1200            | 32             | 96 000 / 1 000 / 500                                            | 8 / 24000                             |
| Standard_D64_v3 | 64        | 256         | 1600           | 32             | 96 000 / 1 000 / 500                                           | 8 / 30000                   |

<sup>1</sup> Dv3 – funkce virtuálního počítače řady Intel® technologie Hyper-Threading

## <a name="dav3-series-preview"></a>Dav3-Series (Preview)

Premium Storage: Není podporováno

Ukládání Premium Storage do mezipaměti: Není podporováno

Velikosti řady Dav3-Series jsou založené na procesoru AMD EPYC<sup>TM</sup> 7452 v 2.35 GHz, který může dosáhnout zvýšení Fmax – 3.35 GHz. Velikosti řady Dav3-Series nabízejí kombinaci vCPU, paměti a dočasného úložiště pro většinu produkčních úloh. Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disky Premium Storage, použijte velikosti Dasv3. Měřiče cen a účtování pro velikosti Dasv3 jsou stejné jako pro Dav3-Series.

Pokud [si chcete zaregistrovat verzi Preview, klikněte sem](http://aka.ms/azureamdpreview).

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB |
|---|---|---|---|
| Standard_D2a_v3  | 2  | 8   | 50   |
| Standard_D4a_v3  | 4  | 16  | 100  |
| Standard_D8a_v3  | 8  | 32  | 200  |
| Standard_D16a_v3 | 16 | 64  | 400  |
| Standard_D32a_v3 | 32 | 128 | 800  |
| Standard_D48a_v3 | 48 | 192 | 1200 |
| Standard_D64a_v3 | 64 | 256 | 1600 |

## <a name="dsv2-series"></a>DSv2-series

ACU 210-250

Premium Storage:  Podporováno

Ukládání Premium Storage do mezipaměti:  Podporováno

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3,5 |7 |4 |4000/32 (43) |3200/48 |2 / 750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8000/64 (86) |6400/96 |2 / 1 500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16000 / 128 (172) |12800/192 |4 / 3 000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32000/256 (344) |25600 / 384 |8 / 6 000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64000/512 (688) |51200/768 |8 / 12000 |

## <a name="dv2-series"></a>Dv2-series

ACU 210-250

Premium Storage:  Není podporováno

Ukládání Premium Storage do mezipaměti:  Není podporováno

| Size           | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/čtení MB/s/zápis MB/s | Max. datových disků | Zvyšují IOPS | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standard_D1_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 4              | 4×500            | 2 / 750                                      |
| Standard_D2_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 8              | 8×500            | 2 / 1 500                                     |
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16             | 16×500           | 4 / 3 000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32             | 32×500           | 8 / 6 000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64             | 64x500           | 8 / 12000                                    |

## <a name="av2-series"></a>Av2-series

ACU 100

Premium Storage:  Není podporováno

Ukládání Premium Storage do mezipaměti:  Není podporováno


| Size            | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/čtení MB/s/zápis MB/s | Maximální počet datových disků/propustnost: IOPS | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2×500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4×500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8×500               | 4 / 1 000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16×500             | 8 / 2 000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4×500               | 2 / 500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8×500               | 4 / 1 000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16×500             | 8 / 2 000                     |

## <a name="dc-series"></a>Řada DC

Premium Storage: Podporováno

Ukládání Premium Storage do mezipaměti: Podporováno



| Size          | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                          | 3200/48                                  | 2 / 1 500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                          | 6400/96                                  | 2 / 3000                                     |

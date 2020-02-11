---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: azcspmt;jonbeck;cynthn;joelpell
ms.custom: include file
ms.openlocfilehash: cfb35f6eeae6d74eea8dab96fa336fb6700eb7a7
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77123200"
---
Velikosti virtuálních počítačů pro obecné účely poskytují vyvážený poměr PROCESORů k paměti. Ideální pro testování a vývoj, malé až střední databáze a webové servery s nízkým a středním provozem. Tento článek poskytuje informace o počtu vCPU, datových discích a síťových adaptérů i propustnosti úložiště pro velikosti v tomto seskupení.

- [DC-Series](#dc-series) je řada virtuálních počítačů v Azure, která vám může přispět k ochraně důvěrnosti a integrity dat a kódu při jejich zpracování ve veřejném cloudu. Tyto počítače jsou založené na nejnovější generaci procesorů Intel XEON s 2176G s 3.7 GHz pomocí technologie SGX. Technologie Intel Turbo zvyšuje tyto počítače až na 4.7 GHz. Instance řady DC umožňují zákazníkům vytvářet zabezpečené aplikace založené na enklávy k ochraně kódu a dat, když se používají.

- Virtuální počítače řady Av2-Series je možné nasadit na nejrůznější typy hardwaru a procesory. Virtuální počítače řady a-Series mají pro úlohy na vstupní úrovni, jako je vývoj a testování, nejvhodnější konfigurace výkonu a paměti procesoru. Velikost je omezována v závislosti na použitém hardwaru a nabízí konzistentní výkon procesoru pro spuštěné instance bez ohledu na hardware. Pokud chcete zjistit fyzický hardware, na kterém je virtuální počítač dané velikosti nasazený, zadejte dotaz na virtuální hardware přímo z virtuálního počítače.

  Příklady případů použití zahrnují vývojové a testovací servery, webové servery s nízkým provozem, malé až střední databáze, ověření konceptu a úložiště kódu.

- Dv2-Series, následný postup pro původní řadu D, nabízí výkonnější procesor a optimální konfiguraci procesoru a paměti, která je vhodná pro většinu produkčních úloh. Dv2-Series má přibližně 35% rychlejší než řada D-Series. Dv2-Series běží na procesorech Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) nebo procesory Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) s technologií Intel Turbo 2,0. Řada Dv2-series má stejnou konfiguraci paměti a disku jako řada D.

- Dv3-Series běží na procesorech Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) nebo procesory Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) v konfiguraci s více vlákny, čímž je zajištěno lepší umístění nejdůležitějších hodnot. zatížení pro účely.  Paměť se rozšířila (od ~ 3,5 GiB/vCPU na 4 GiB/vCPU), zatímco limity disku a sítě se přizpůsobily na základě počtu jader, aby se přerovnaly k přechodu na vlákno.  Řady Dv3-Series již nemají virtuální počítače s vysokou velikostí paměti řady D/Dv2-Series byly přesunuty do paměťově optimalizované Ev3-Series pro [systémy Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#ev3-series) a [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#ev3-series).

  Příklady případů použití řady D-Series zahrnují aplikace na podnikové úrovni, relační databáze, ukládání do mezipaměti v paměti a analýzy.

- Řady Dav4-Series a Dasv4-Series jsou novými velikostmi, které využívají procesor AMD 2.35 EPYC<sup>TM</sup> 7452 v konfiguraci s více vlákny, a to s až 256 MB mezipaměti L3, které vynásobí 8 MB této mezipaměti L3 každé 8 jader, a zvyšují možnosti zákazníků pro provozování jejich obecných účelů. Řady Dav4-Series a Dasv4-Series mají stejnou konfiguraci paměti a disku jako řada D & Dsv3-Series.
  
## <a name="b-series"></a>Řady B-Series

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Virtuální počítače s podporou řady B-Series jsou ideální pro úlohy, které nepotřebují průběžný výkon procesoru, jako jsou webové servery, malé databáze a vývojové a testovací prostředí. Tyto úlohy mají typicky požadavky na výkon při roztržení. Řada B-Series poskytuje zákazníkům možnost zakoupit si velikost virtuálního počítače pomocí cenového plánu s vědomím cen, který umožňuje instanci virtuálního počítače sestavovat kredity, když virtuální počítač využívá méně než základní výkon. Když se na virtuálním počítači nashromáždil kredit, může virtuální počítač zvýšit kapacitu virtuálního počítače pomocí až 100% procesoru, když vaše aplikace vyžaduje vyšší výkon procesoru.

Příklady případů použití zahrnují vývojové a testovací servery, webové servery s nízkým provozem, malé databáze, mikroslužby, servery pro testování konceptů, sestavovací servery.


| Velikost             | Virtuální procesory  | Paměť: GiB | Dočasné úložiště (SSD): GiB | Základní výkon procesoru virtuálního počítače | Maximální výkon procesoru virtuálního počítače | Počáteční kredity | Kredity v bance za hodinu | Maximální počet bank kreditů | Max. datových disků | Maximální propustnost úložiště v mezipaměti a dočasné úložiště: IOPS/MB/s | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5 %                   | 100 %                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10 %                   | 100 %                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20 %                   | 100 %                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40 %                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60 %                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202%                  | 1200%                   | 360                   | 121                 | 2909           | 16                                     | 6480/75                                 | 4320 / 50                                 | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270%                  | 1600%                   | 480                   | 162                 | 3888           | 32                                     | 8640 / 100                                 | 4320 / 50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337%                  | 2000%                   | 600                   | 203                 | 4860           | 32                                     | 10800/125                                 | 4320 / 50                                 | 8  |

<sup>1</sup> B1ls se podporuje jenom na Linux.

## <a name="dsv3-series-sup1sup"></a>Dsv3-Series <sup>1</sup>

ACU: 160–190

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Velikosti řady Dsv3-Series běží na procesorech Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) nebo procesory Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) s technologií Intel Turbo 2,0 a používat Premium Storage. Velikosti Dsv3-series nabízejí kombinaci virtuálního procesoru, paměti a dočasného úložiště pro většinu produkčních úloh.


| Velikost             | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4000/32 (50)                                                       | 3200/48                                | 2 / 1 000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8000 / 64 (100)                                                      | 6400/96                                | 2 / 2 000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16000/128 (200)                                                    | 12800/192                              | 4 / 4000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32000/256 (400)                                                    | 25600 / 384                              | 8 / 8000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64000 / 512 (800)                                                    | 51200/768                              | 8 / 16 000                                               |
| Standard_D48s_v3 | 48     | 192          | 384            | 32             | 96000/768 (1200)                                                    | 76800/1152                               | 8 / 24000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128000 / 1024 (1600)                                                    | 80000/1200                              | 8 / 30000                                               |

<sup>1</sup> Dsv3 – funkce virtuálního počítače řady Intel® technologie Hyper-Threading

## <a name="dasv4-series"></a>Dasv4-Series

ACU: 230-260

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Velikosti řady Dasv4-Series jsou založené na procesoru AMD EPYC<sup>TM</sup> 7452 v 2.35 GHz, který může dosáhnout zvýšení maximální frekvence 3.35 GHz a používání jednotky SSD úrovně Premium. Velikosti řady Dasv4-Series nabízejí kombinaci vCPU, paměti a dočasného úložiště pro většinu produkčních úloh.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000/32 (50)|3200/48|2 / 1 000 |
| Standard_D4as_v4|4|16|32|8|8000 / 64 (100)|6400/96|2 / 2 000 |
| Standard_D8as_v4|8|32|64|16|16000/128 (200)|12800/192|4 / 4000 |
| Standard_D16as_v4|16|64|128|32|32000/255 (400)|25600 / 384|8 / 8000 |
| Standard_D32as_v4|32|128|256|32|64000/510 (800)|51200/768|8 / 16 000 |
| Standard_D48as_v4 <sup>**</sup>|48|192|384|32| | | 
| Standard_D64as_v4 <sup>**</sup>|64|256|512|32| | | 
| Standard_D96as_v4 <sup>**</sup>|96|384|768|32| | | 

<sup>**</sup> Tyto velikosti jsou ve verzi Preview.  Pokud máte zájem o vyzkoušení těchto větších velikostí, zaregistrujte se na [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="dv3-series-sup1sup"></a>Dv3-Series <sup>1</sup>

ACU: 160–190

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Velikosti řady Dv3-Series běží na procesorech Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell), nebo procesory Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) s technologií Intel Turbo zvyšovat 2,0. Velikosti Dv3-series nabízejí kombinaci virtuálního procesoru, paměti a dočasného úložiště pro většinu produkčních úloh.

Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disky Premium Storage, použijte velikosti Dsv3. Měřiče cen a účtování pro velikosti Dsv3 jsou stejné jako pro Dv3-series. 


| Velikost            | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální počet síťových karet/šířka pásma sítě (MB/s) |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3 000 / 46 / 23                                               | 2 / 1 000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6 000 / 93 / 46                                               | 2 / 2 000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12 000 / 187 / 93                                             | 4 / 4000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24 000 / 375 / 187                                            | 8 / 8000                    |
| Standard_D32_v3 | 32        | 128         | 800            | 32             | 48 000 / 750 / 375                                            | 8 / 16 000                   |
| Standard_D48_v3 | 48        | 192          | 1200            | 32             | 96 000 / 1 000 / 500                                            | 8 / 24000                             |
| Standard_D64_v3 | 64        | 256         | 1600           | 32             | 96 000 / 1 000 / 500                                           | 8 / 30000                   |

<sup>1</sup> Dv3 – funkce virtuálního počítače řady Intel® technologie Hyper-Threading

## <a name="dav4-series"></a>Dav4-Series

ACU: 230-260

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Velikosti řady Dav4-Series jsou založené na procesoru AMD EPYC<sup>TM</sup> 7452 v 2.35 GHz, který může dosáhnout zvýšení maximální frekvence 3.35 GHz. Velikosti řady Dav4-Series nabízejí kombinaci vCPU, paměti a dočasného úložiště pro většinu produkčních úloh. Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disk SSD úrovně Premium, použijte velikosti Dasv4. Měřiče cen a účtování pro velikosti Dasv4 jsou stejné jako pro Dav4-Series.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 / 1 000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 / 2 000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000 / 187 / 93 | 4 / 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000 / 375 / 187 |8 / 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000 / 750 / 375 |8 / 16 000 |
| Standard_D48a_v4 <sup>**</sup> | 48 | 192| 1200 | 32 | | |
| Standard_D64a_v4 <sup>**</sup> | 64 | 256 | 1600 | 32 | | |
| Standard_D96a_v4 <sup>**</sup> | 96 | 384 | 2400 | 32 | | |

<sup>**</sup> Tyto velikosti jsou ve verzi Preview.  Pokud máte zájem o vyzkoušení těchto větších velikostí, zaregistrujte se na [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="dsv2-series"></a>DSv2-series

ACU: 210–250

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Velikosti řady DSv2-Series běží na procesorech Intel® Xeon® 8171M 2.1 GHz (Skylake) nebo Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) nebo Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) s technologií Intel Turbo 2,0 a používat Premium Storage.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3,5 |7 |4 |4000/32 (43) |3200/48 |2 / 750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8000/64 (86) |6400/96 |2 / 1 500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16000 / 128 (172) |12800/192 |4 / 3 000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32000/256 (344) |25600 / 384 |8 / 6 000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64000/512 (688) |51200/768 |8 / 12000 |

## <a name="dv2-series"></a>Dv2-series

ACU: 210–250

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Velikosti řady DSv2-Series běží na procesorech Intel® Xeon® 8171M 2.1 GHz (Skylake) nebo na procesorech Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) nebo Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) s technologií Intel Turbo zvyšovat 2,0.

| Velikost           | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Max. datových disků | Propustnost: IOPS | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standard_D1_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 4              | 4×500            | 2 / 750                                      |
| Standard_D2_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 8              | 8×500            | 2 / 1 500                                     |
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16             | 16×500           | 4 / 3 000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32             | 32×500           | 8 / 6 000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64             | 64x500           | 8 / 12000                                    |

## <a name="av2-series"></a>Av2-series

ACU: 100

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

| Velikost            | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální propustnost datových disků: IOPS | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2×500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4×500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8×500               | 4 / 1 000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000 / 160 / 80                                          | 16 / 16×500             | 8 / 2 000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000 / 40 / 20                                           | 4 / 4×500               | 2 / 500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000 / 80 / 40                                           | 8 / 8×500               | 4 / 1 000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000 / 160 / 80                                          | 16 / 16×500             | 8 / 2 000                     |

## <a name="dc-series"></a>DC-Series

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno



| Velikost          | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                          | 3200/48                                  | 2 / 1 500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                          | 6400/96                                  | 2 / 3000                                     |

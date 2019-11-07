---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 10/17/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: da47a471ddcf2c083ed127b79e4d3da9713d2ed4
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719073"
---
Paměťově optimalizované velikosti virtuálních počítačů nabízejí vysoký poměr paměti k procesoru, který je skvělý pro servery relačních databází, střední až velké mezipaměti a analýzu v paměti. Tento článek poskytuje informace o počtu vCPU, datových discích a síťových adaptérů i propustnosti úložiště a šířce pásma sítě pro každou velikost v tomto seskupení.

* Ev3-Series nabízí Intel® Xeon® 8171M 2,1 GHz (Skylake) nebo procesory Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) v konfiguraci s více vlákny, které poskytují lepší polohu pro většinu úloh pro obecné účely a přinášejí Ev3 do zarovnání s virtuálními počítači pro obecné účely většiny ostatních cloudů.  Paměť se rozšířila (od 7 GiB/vCPU do 8 GiB/vCPU), zatímco limity disku a sítě se přizpůsobily na základě počtu jader, aby se přerovnaly k přechodu na vlákno.  Ev3 je následná velikost virtuálních počítačů s vysokou pamětí pro řady D/Dv2.

* Řady Eav4-Series a Easv4-Series využívají procesor AMD 2.35 EPYC<sup>TM</sup> 7452 v konfiguraci s více vlákny s až 256mb mezipamětí L3 a zvyšují možnosti spouštění většiny paměťově optimalizovaných úloh.  Řady Eav4-Series a Easv4-Series mají stejnou konfiguraci paměti a disku jako Ev3 & Esv3-Series.

* Mv2-Series nabízí nejvyšší počet vCPU (až 416 vCPU) a největší paměť (až 8,19 TiB) libovolného virtuálního počítače v cloudu. Je ideální pro extrémně velké databáze nebo další aplikace, kterým přináší výhody vysoký počet virtuálních procesorů a velké množství paměti.

* Řada M-Series nabízí vysoký počet vCPU (až 128 vCPU) a velké množství paměti (až 3,8 TiB). Je to také ideální pro extrémně velké databáze nebo jiné aplikace, které využívají vysoké počty vCPU a velké množství paměti.

* Dv2-Series, G-series a protějšky DSv2/GS jsou ideální pro aplikace, které vyžadují rychlejší vCPU, lepší dočasný výkon úložiště nebo mají vyšší nároky na paměť. Nabízejí výkonnou kombinaci pro mnoho podnikových aplikací.

* Virtuální počítače Dv2-series, nástupce původní řady D-series, mají výkonnější procesor. Dv2-Series má přibližně 35% rychlejší než řada D-Series. Běží na Intel® Xeon® 8171M 2,1 GHz (Skylake) nebo Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) nebo procesory Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) a s 2,0 technologií Intel Turbo zvyšovat. Řada Dv2-series má stejnou konfiguraci paměti a disku jako řada D.

* Azure Compute nabízí velikosti virtuálních počítačů, které jsou izolované na konkrétní typ hardwaru a vyhrazené pro jednoho zákazníka.  Tyto velikosti virtuálních počítačů jsou nejvhodnější pro úlohy, které vyžadují vysoký stupeň izolace od ostatních zákazníků a kterých se týkají například požadavky na dodržování předpisů a zákonné požadavky.  Zákazníci si také můžou rozdělit prostředky těchto izolovaných virtuálních počítačů s využitím [podpory Azure pro vnořené virtuální počítače](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Další informace o možnostech izolovaného virtuálního počítače najdete v tabulkách řady virtuálních počítačů.

## <a name="esv3-series"></a>Esv3-Series

ACU: 160-190 <sup>1</sup>

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Instance ESv3-Series obsahují Intel® Xeon® 8171M 2,1 GHz (Skylake) nebo procesory Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) a můžou dosahovat 3,5 GHz pomocí technologie Intel Turbo pro zvýšení úrovně 2,0 a využívat Premium Storage. Instance Ev3-series jsou ideální pro podnikové aplikace s vysokými nároky na paměť.


| Velikost             | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000/32 (50)                                                       | 3200/48                                | 2 / 1 000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000/64 (100)                                                      | 6400/96                                | 2 / 2 000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000/128 (200)                                                    | 12800/192                              | 4 / 4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000/256 (400)                                                    | 25600/384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000/320 (400)                                                    | 32000/480                              | 8 / 10000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64000/512 (800)                                                    | 51200/768                              | 8 / 16 000                             |
| Standard_E48s_v3&nbsp;<sup>2</sup> | 48     | 384         | 768            | 32             | 96000/768 (1200)                                                   | 76800/1152                             | 8 / 24000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000/1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128000/1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |


<sup>1</sup> Esv3-Series je součástí virtuálního počítače Intel® technologie Hyper-Threading.

<sup>2</sup> dostupné základní velikosti.

<sup>3</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.

## <a name="easv4-series"></a>Easv4-Series

ACU: 230-260

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Velikosti řady Easv4-Series jsou založené na procesoru AMD EPYC<sup>TM</sup> 7452 v 2.35 GHz, který může dosáhnout zvýšení maximální frekvence 3.35 GHz a používání jednotky SSD úrovně Premium. Velikosti řady Easv4-Series jsou ideální pro podnikové aplikace náročné na paměť.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50)|3200/48|2 / 1 000 |
| Standard_E4as_v4|4|32|64|8|8000/64 (100)|6400/96|2 / 2 000 |
| Standard_E8as_v4|8|64|128|16|16000/128 (200)|12800/192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400)|25600/384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500)|32000/480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800)|51200/768|8 / 16 000 |
| Standard_E48as_v4 <sup>**</sup> |48|384|768|32|  | | 
| Standard_E64as_v4 <sup>**</sup> |64|512|1024|32| | | 
| Standard_E96as_v4 <sup>**</sup> |96|672|1344|32| | |  

<sup>**</sup>  Tyto velikosti jsou ve verzi Preview. Pokud máte zájem o vyzkoušení těchto větších velikostí, zaregistrujte se na [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="ev3-series"></a>Ev3-series 

ACU: 160 – 190 <sup>1</sup>

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Instance Ev3-Series obsahují procesory Intel® Xeon® 8171M 2,1 GHz (Skylake) nebo procesory Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) a můžou dosahovat 3,5 GHz pomocí technologie Intel Turbo pro zvýšení 2,0. Instance Ev3-series jsou ideální pro podnikové aplikace s vysokými nároky na paměť.

Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disky Premium Storage, použijte velikosti ESv3. Měřiče cen a účtování pro velikosti ESv3 jsou stejné jako pro Ev3-series. 


| Velikost            | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Max. počet NIC / Šířka pásma sítě |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3 000 / 46 / 23                                               | 2 / 1 000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6 000 / 93 / 46                                               | 2 / 2 000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12 000 / 187 / 93                                             | 4 / 4000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24 000 / 375 / 187                                            | 8 / 8000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48 000 / 750 / 375                                            | 8 / 16 000                 |
| Standard_E48_v3 | 48        | 384         | 1200            | 32             | 96 000 / 1 000 / 500                                            | 8 / 24000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96 000 / 1 000 / 500                                           | 8 / 30000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96 000 / 1 000 / 500                                           | 8 / 30000           |

<sup>1</sup> Ev3-Series je součástí virtuálního počítače Intel® technologie Hyper-Threading.

<sup>2</sup> dostupné základní velikosti.

<sup>3</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.

## <a name="eav4-series"></a>Eav4-Series

ACU: 230-260

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Velikosti řady Eav4-Series jsou založené na procesoru AMD EPYC<sup>TM</sup> 7452 v 2.35 GHz, který může dosáhnout zvýšení maximální frekvence 3.35 GHz a používání jednotky SSD úrovně Premium. Velikosti řady Eav4-Series jsou ideální pro podnikové aplikace náročné na paměť. Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disk SSD úrovně Premium, použijte velikosti řady Easv4-Series. Měřiče cen a účtování pro velikosti Easv4 jsou stejné jako pro Eav3-Series.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
| -----|-----|-----|-----|-----|-----|-----|
| Standardní\_E2A\_v4|2|16|50|4|3000 / 46 / 23|2 / 1 000 |
| Standardní\_e4a\_v4|4|32|100|8|6000 / 93 / 46|2 / 2 000 |
| Standardní\_E8a\_v4|8|64|200|16|12000 / 187 / 93|4 / 4000 |
| Standardní\_E16a\_v4|16|128|400|32|24000 / 375 / 187|8 / 8000 |
| Standardní\_E20a\_v4|20|160|500|32|30000/468/234|8 / 10000 |
| Standardní\_E32a\_v4|32|256|800|32|48000 / 750 / 375|8 / 16 000 |
| Standardní\_E48a\_v4 <sup>**</sup> |48|384|1200|32| | |
| Standardní\_E64a\_v4 <sup>**</sup> |64|512|1600|32| | |
| Standardní\_E96a\_v4 <sup>**</sup> |96|672|2400|32| | |

<sup>**</sup>  Tyto velikosti jsou ve verzi Preview.  Pokud máte zájem o vyzkoušení těchto větších velikostí, zaregistrujte se na [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="mv2-series"></a>Řada Mv2

ACU: 188-280<sup>1</sup>

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Akcelerátor zápisu: [podporováno](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

Mv2-Series funkce s vysokou propustností a nízkou latencí běžící na procesorech Intel® Xeon® Platinum 8180M 2,5 GHz (Skylake) se základní frekvencí 2,5 GHz a maximální frekvencí 3,8 GHz. Všechny velikosti virtuálních počítačů řady Mv2-Series můžou využívat trvalé disky úrovně Standard i Premium. Instance Mv2-Series jsou paměťové optimalizované velikosti virtuálních počítačů, které poskytují bezkonkurenční výpočetní výkon pro podporu rozsáhlých databází a úloh s vysokým výkonem, které jsou ideální pro servery relačních databází, velké mezipaměti a v paměti. Analytics.

|Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>2</sup> | 208 | 5700 | 4 096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16 000 |
| Standard_M208s_v2<sup>2</sup> | 208 | 2850 | 4 096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16 000 |
| Standard_M416ms_v2<sup>2, 3</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 / 32000 |
| Standard_M416s_v2<sup>2, 3</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 / 32000 |

<sup>1</sup> Mv2 – funkce virtuálního počítače řady Intel® technologie Hyper-Threading

<sup>2</sup> virtuální počítače řady Mv2-Series jsou pouze 2. generace. Pokud používáte Linux, přečtěte si téma [Podpora pro virtuální počítače generace 2 v Azure](../articles/virtual-machines/linux/generation-2.md) , kde najdete pokyny, jak najít a vybrat obrázek.

<sup>3</sup> pro velikosti M416ms_v2 a M416s_v2 si všimněte, že je k dispozici pouze počáteční podpora pro následující obrázek: "GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 pro aplikace SAP".

## <a name="m-series"></a>M-Series 

ACU: 160-180 <sup>1</sup>

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Velikosti řady M-Series jsou založené na procesorech Intel (R) Xeon (R) Xeon E7-8890 V3 @ 2.50 GHz.   

Akcelerátor zápisu: [podporováno](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Velikost            | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218,75 | 256  | 8  | 10000/100 (793)  | 5000/125 | 4 / 2 000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437,5  | 512  | 16 | 20000/200 (1587) | 10000/250 | 8 / 4 000 |
| Standard_M32ts | 32 | 192    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M32ls | 32 | 256    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M64s  | 64 | 1024   | 2 048 | 64 | 80000/800 (6348)| 40000/1000 | 8 / 16 000          |
| Standard_M64ls  | 64 | 512    | 2 048 | 64 | 80000/800 (6348) | 40000/1000 | 8 / 16 000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1 792 | 2 048 | 64 | 80000/800 (6348)| 40000/1000 | 8 / 16 000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2 048        | 4 096  | 64 | 160000/1600 (12696) | 80000/2000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892  | 4 096 | 64 | 160000/1600 (12696) | 80000/2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1024 | 7168  | 64 | 80000/800 (1228) | 40000/1000 | 8 / 16 000 |
| Standard_M64m  | 64  | 1 792 | 7168  | 64 | 80000/800 (1228) | 40000/1000 | 8 / 16 000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2 048 | 14336 | 64 | 250000/1600 (2456) | 80000/2000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3892 | 14336 | 64 | 250000/1600 (2456) | 80000/2000 | 8 / 32000 |



<sup>1</sup> . funkce virtuálního počítače řady M-series Intel® technologie Hyper-Threading

<sup>2</sup> více než 64 vCPU vyžaduje jeden z těchto podporovaných hostovaných operačních systémů: Windows Server 2016, Ubuntu 16,04 LTS, SLES 12 SP2 a Red Hat Enterprise Linux, CentOS 7,3 nebo Oracle Linux 7,3 s lis 4.2.1.

jsou k dispozici <sup>3</sup> omezené základní velikosti.

<sup>4</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.
<br>


## <a name="dsv2-series-11-15"></a>DSv2-Series 11-15

ACU: 210 – 250 <sup>1</sup>

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Velikosti řady DSv2-Series běží na procesorech Intel® Xeon® 8171M 2,1 GHz (Skylake) nebo Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) nebo procesory Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell).

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000/64 (72) |6400/96 |2 / 1 500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000/128 (144) |12800/192 |4 / 3 000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000/256 (288) |25600/384 |8 / 6 000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000/512 (576) |51200/768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000/640 (720) |64000/960 |8/25000&nbsp;<sup>4</sup>

<sup>1</sup> maximální propustnost disku (IOPS nebo MB/s), která je možné použít u virtuálního počítače DSv2 Series, může být omezená počtem, velikostí a prokládáním připojených disků.  Podrobnosti najdete v tématu [navrhování pro vysoký výkon](../articles/virtual-machines/windows/premium-storage-performance.md).  
<sup>2</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.  
jsou k dispozici <sup>3</sup> omezené základní velikosti.  
<sup>4</sup> 25000 MB/s s akcelerovanými síťovými službami. 

<br>

## <a name="dv2-series-11-15"></a>Dv2-Series 11-15

ACU: 210–250

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Velikosti řady DSv2-Series běží na procesorech Intel® Xeon® 8171M 2,1 GHz (Skylake) nebo Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell) nebo procesory Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell).

| Velikost              | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální propustnost datových disků: IOPS | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8×500                         | 2 / 1 500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16×500                         | 4 / 3 000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32×500                       | 8 / 6 000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1 000          | 60000 / 937 / 468                                        | 64/64x500                       | 8/25000&nbsp;<sup>2</sup> |

<sup>1</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.  
<sup>2</sup> 25000 MB/s s akcelerovanými síťovými službami. 

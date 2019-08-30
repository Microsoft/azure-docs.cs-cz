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
ms.openlocfilehash: c8ffa7b65e4f97fa537299bf542ba377da026064
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70174605"
---
Paměťově optimalizované velikosti virtuálních počítačů nabízejí vysoký poměr paměti k procesoru, který je skvělý pro servery relačních databází, střední až velké mezipaměti a analýzu v paměti. Tento článek poskytuje informace o počtu vCPU, datových discích a síťových adaptérů i propustnosti úložiště a šířce pásma sítě pro každou velikost v tomto seskupení.

* Ev3-Series obsahuje procesor E5-2673 V4 2,3 GHz (Broadwell) v konfiguraci s více vlákny, poskytuje lepší hodnotu pro většinu úloh pro obecné účely a přináší Ev3 k virtuálním počítačům pro obecné účely většiny ostatních cloudů.  Paměť se rozšířila (od 7 GiB/vCPU do 8 GiB/vCPU), zatímco limity disku a sítě se přizpůsobily na základě počtu jader, aby se přerovnaly k přechodu na vlákno.  Ev3 je následná velikost virtuálních počítačů s vysokou pamětí pro řady D/Dv2.

* Řady Eav3-Series a Easv3-Series využívají procesor AMD 2.35 EPYC<sup>TM</sup> 7452V v konfiguraci s více vlákny s až 256mb mezipamětí L3 a zvyšují možnosti spouštění většiny paměťově optimalizovaných úloh.  Řady Eav3-Series a Easv3-Series mají stejnou konfiguraci paměti a disku jako Ev3 & Esv3-Series.

* Mv2-Series nabízí nejvyšší počet vCPU (až 208 vCPU) a největší paměť (až 5,7 TiB) libovolného virtuálního počítače v cloudu. Je ideální pro extrémně velké databáze nebo další aplikace, kterým přináší výhody vysoký počet virtuálních procesorů a velké množství paměti.

* Řada M-Series nabízí vysoký počet vCPU (až 128 vCPU) a velké množství paměti (až 3,8 TiB). Je to také ideální pro extrémně velké databáze nebo jiné aplikace, které využívají vysoké počty vCPU a velké množství paměti.

* Dv2-Series, G-series a protějšky DSv2/GS jsou ideální pro aplikace, které vyžadují rychlejší vCPU, lepší dočasný výkon úložiště nebo mají vyšší nároky na paměť. Nabízejí výkonnou kombinaci pro mnoho podnikových aplikací.

* Virtuální počítače Dv2-series, nástupce původní řady D-series, mají výkonnější procesor. Procesor v Dv2-series je asi o 35 % rychlejší než procesor v D-series. Vychází z nejnovější generace procesorů 2,4 Intel Xeon® E5-2673 V3 2,4 GHz (Haswell) nebo E5-2673 V4 2,3 GHz (Broadwell) a s 2,0 technologií Intel Turbo () může jít až o 3,1 GHz. Řada Dv2-series má stejnou konfiguraci paměti a disku jako řada D.

* Azure Compute nabízí velikosti virtuálních počítačů, které jsou izolované na konkrétní typ hardwaru a vyhrazené pro jednoho zákazníka.  Tyto velikosti virtuálních počítačů jsou nejvhodnější pro úlohy, které vyžadují vysoký stupeň izolace od ostatních zákazníků a kterých se týkají například požadavky na dodržování předpisů a zákonné požadavky.  Zákazníci si také můžou rozdělit prostředky těchto izolovaných virtuálních počítačů s využitím [podpory Azure pro vnořené virtuální počítače](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Další informace o možnostech izolovaného virtuálního počítače najdete v tabulkách řady virtuálních počítačů.

## <a name="esv3-series"></a>Esv3-Series

ACU 160-190 <sup>1</sup>

Premium Storage:  Podporováno

Ukládání Premium Storage do mezipaměti:  Podporováno

Instance ESv3-series jsou založené na procesorech 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell), jejichž frekvence se s technologií Intel Turbo Boost 2.0 může zvýšit až na 3,5 GHz, a používají službu Premium Storage. Instance Ev3-series jsou ideální pro podnikové aplikace s vysokými nároky na paměť.


| Size             | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000/32 (50)                                                       | 3200/48                                | 2 / 1 000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000 / 64 (100)                                                      | 6400/96                                | 2 / 2 000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000/128 (200)                                                    | 12800/192                              | 4 / 4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000/256 (400)                                                    | 25600 / 384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000 / 320 (400)                                                    | 32000/480                              | 8 / 10000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64000 / 512 (800)                                                    | 51200/768                              | 8 / 16 000                             |
| Standard_E48s_v3&nbsp;<sup>2</sup> | 48     | 384         | 768            | 32             | 96000/768 (1200)                                                   | 76800/1152                             | 8 / 24000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |


<sup>1</sup> Esv3-Series je součástí virtuálního počítače Intel® technologie Hyper-Threading.

<sup>2</sup> dostupné základní velikosti.

<sup>3</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.

## <a name="easv3-series"></a>Easv3-Series

Premium Storage: Podporováno

Ukládání Premium Storage do mezipaměti: Podporováno

Velikosti řady Easv3-Series jsou založené na procesoru AMD EPYC<sup>TM</sup> 7452V procesor 2.35 GHz, který může dosáhnout posílených Fmax – v 3.35 GHz a využívat Storage úrovně Premium. Velikosti řady Easv3-Series jsou ideální pro podnikové aplikace náročné na paměť.

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB |
|---|---|---|---|
| Standard_E2as_v3  | 2  | 16  | 32  |
| Standard_E4as_v3  | 4  | 32  | 64  |
| Standard_E8as_v3  | 8  | 64  | 128 |
| Standard_E16as_v3 | 16 | 128 | 256 |
| Standard_E32as_v3 | 32 | 256 | 512 |
| Standard_E48as_v3 | 48 | 384 | 768 |
| Standard_E64as_v3 | 64 | 432 | 864 |

## <a name="ev3-series"></a>Ev3-series 

ACU 160 – 190 <sup>1</sup>

Premium Storage:  Není podporováno

Ukládání Premium Storage do mezipaměti:  Není podporováno

Instance Ev3-series jsou založené na procesorech 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell) a s technologií Intel Turbo Boost 2.0 se jejich frekvence může zvýšit až na 3,5 GHz. Instance Ev3-series jsou ideální pro podnikové aplikace s vysokými nároky na paměť.

Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disky Premium Storage, použijte velikosti ESv3. Měřiče cen a účtování pro velikosti ESv3 jsou stejné jako pro Ev3-series. 


| Size            | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Maximální propustnost dočasného úložiště: IOPS/čtení MB/s/zápis MB/s | Max. počet NIC / Šířka pásma sítě |
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

## <a name="eav3-series"></a>Eav3-Series

Premium Storage: Není podporováno

Ukládání Premium Storage do mezipaměti: Není podporováno

Velikosti řady Eav3-Series jsou založené na procesoru AMD EPYC<sup>TM</sup> 7452V procesor 2.35 GHz, který může dosáhnout posílených Fmax – v 3.35 GHz a využívat Storage úrovně Premium. Velikosti řady Eav3-Series jsou ideální pro podnikové aplikace náročné na paměť. Úložiště datových disků se účtuje nezávisle na virtuálních počítačích. Pokud chcete použít disky Premium Storage, použijte velikosti řady Easv3-Series. Měřiče cen a účtování pro velikosti Easv3 jsou stejné jako pro Eav3-Series.

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB |
|---|---|---|---|---|---|
| Standard_E2a_v3  | 2  | 16  | 50   |
| Standard_E4a_v3  | 4  | 32  | 100  |
| Standard_E8a_v3  | 8  | 64  | 200  |
| Standard_E16a_v3 | 16 | 128 | 400  |
| Standard_E32a_v3 | 32 | 256 | 800  |
| Standard_E48a_v3 | 48 | 384 | 1200 |
| Standard_E64a_v3 | 64 | 432 | 1600 |

## <a name="mv2-series"></a>Řada Mv2

Premium Storage: Podporováno

Ukládání Premium Storage do mezipaměti: Podporováno

Akcelerátor zápisu: [Podporuje se](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

Mv2-Series nabízí vysokou propustnost, nízkou latenci, přímo mapované místní úložiště NVMe běžící na vláknech Intel® Xeon® Platinum 8180M 2,5 GHz (Skylake) se základní frekvencí jádra 2,5 GHz a maximální frekvence Turbo 3,8 GHz. Všechny velikosti virtuálních počítačů řady Mv2-Series můžou využívat trvalé disky úrovně Standard i Premium. Instance Mv2-Series jsou paměťové optimalizované velikosti virtuálních počítačů, které poskytují bezkonkurenční výpočetní výkon pro podporu rozsáhlých databází a úloh s vysokým výkonem, které jsou ideální pro servery relačních databází, velké mezipaměti a v paměti. Analytics. 

|Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>1, 2</sup> | 208 | 5700 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16 000 |
| Standard_M208s_v2<sup>1, 2</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16 000 |

Funkce virtuálního počítače Mv2-Series Intel® technologie Hyper-Threading  

<sup>1</sup> tyto velké virtuální počítače vyžadují jeden z těchto podporovaných Host operačních systémech: Windows Server 2016, Windows Server 2019, SLES 12 SP4, SLES 15.

<sup>2</sup> virtuální počítače řady Mv2-Series jsou pouze [2. generace](/azure/virtual-machines/linux/generation-2) . Pokud používáte Linux, přečtěte si následující část, kde najdete informace o tom, jak najít a vybrat image SUSE Linux.

#### <a name="find-a-suse-image"></a>Najít obrázek SUSE

Chcete-li vybrat příslušnou bitovou kopii systému SUSE Linux v Azure Portal: 

1. V Azure Portal vyberte **vytvořit prostředek** . 
1. Vyhledejte "SUSE SAP" 
1. SLES for SAP generace 2 jsou k dispozici jako buď průběžné platby, nebo využijte vlastní předplatné (BYOS). Ve výsledcích hledání rozbalte požadovanou kategorii obrázku:

    * SUSE Linux Enterprise Server (SLES) pro SAP
    * SUSE Linux Enterprise Server (SLES) pro SAP (BYOS)
    
1. Image SUSE kompatibilní s Mv2-Series mají předponu s názvem `GEN2:`. Pro virtuální počítače řady Mv2-Series jsou k dispozici následující image SUSE:

    * GEN2 SUSE Linux Enterprise Server (SLES) 12 SP4 pro aplikace SAP
    * GEN2 SUSE Linux Enterprise Server (SLES) 15 pro aplikace SAP
    * GEN2 SUSE Linux Enterprise Server (SLES) 12 SP4 pro aplikace SAP (BYOS)
    * GEN2 SUSE Linux Enterprise Server (SLES) 15 pro aplikace SAP (BYOS)

#### <a name="select-a-suse-image-via-azure-cli"></a>Výběr image SUSE přes Azure CLI

Pokud chcete zobrazit seznam aktuálně dostupného SLES pro virtuální počítače Mv2-Series, použijte následující [`az vm image list`](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az-vm-image-list) příkaz:

```azurecli
az vm image list --output table --publisher SUSE --sku gen2 --all
```

Příkaz vypíše aktuálně dostupné virtuální počítače generace 2 dostupné z SUSE pro virtuální počítače řady Mv2-Series. 

Příklad výstupu:

```
Offer          Publisher  Sku          Urn                                        Version
-------------  ---------  -----------  -----------------------------------------  ----------
SLES-SAP       SUSE       gen2-12-sp4  SUSE:SLES-SAP:gen2-12-sp4:2019.05.13       2019.05.13
SLES-SAP       SUSE       gen2-15      SUSE:SLES-SAP:gen2-15:2019.05.13           2019.05.13
SLES-SAP-BYOS  SUSE       gen2-12-sp4  SUSE:SLES-SAP-BYOS:gen2-12-sp4:2019.05.13  2019.05.13
SLES-SAP-BYOS  SUSE       gen2-15      SUSE:SLES-SAP-BYOS:gen2-15:2019.05.13      2019.05.13
```

## <a name="m-series"></a>M-Series 

ACU 160-180 <sup>1</sup>

Premium Storage:  Podporováno

Ukládání Premium Storage do mezipaměti:  Podporováno

Akcelerátor zápisu:  [Podporuje se](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Size            | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218,75 | 256  | 8  | 10000/100 (793)  | 5000  / 125 | 4 / 2 000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437,5  | 512  | 16 | 20000/200 (1587) | 10000/250 | 8 / 4 000 |
| Standard_M32ts | 32 | 192    | 1024 | 32 | 40000/400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ls | 32 | 256    | 1024 | 32 | 40000/400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1024 | 32 | 40000/400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M64s  | 64 | 1024   | 2048 | 64 | 80000/800 (6348)| 40000/1000 | 8 / 16 000          |
| Standard_M64ls  | 64 | 512    | 2048 | 64 | 80000/800 (6348) | 40000/1000 | 8 / 16 000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1 792 | 2048 | 64 | 80000/800 (6348)| 40000/1000 | 8 / 16 000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2 048        | 4 096  | 64 | 160000/1600 (12696) | 80000/2000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892  | 4096 | 64 | 160000/1600 (12696) | 80000/2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1024 | 7168  | 64 | 80000  / 800  (1228) | 40000/1000 | 8 / 16 000 |
| Standard_M64m  | 64  | 1 792 | 7168  | 64 | 80000  / 800  (1228) | 40000/1000 | 8 / 16 000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2048 | 14336 | 64 | 250000/1600 (2456) | 80000/2000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3892 | 14336 | 64 | 250000/1600 (2456) | 80000/2000 | 8 / 32000 |



<sup>1</sup> . funkce virtuálního počítače řady M-series Intel® technologie Hyper-Threading

<sup>2</sup> více než 64 vCPU vyžaduje jednu z těchto podporovaných hostovaných operačních systémech: Windows Server 2016, Ubuntu 16,04 LTS, SLES 12 SP2 a Red Hat Enterprise Linux, CentOS 7,3 nebo Oracle Linux 7,3 s LIS 4.2.1.

jsou k dispozici <sup>3</sup> omezené základní velikosti.

<sup>4</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.
<br>


## <a name="dsv2-series-11-15"></a>DSv2-Series 11-15

ACU 210 - 250 <sup>1</sup>

Premium Storage:  Podporováno

Ukládání Premium Storage do mezipaměti:  Podporováno

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS/MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000/64 (72) |6400/96 |2 / 1 500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000/128 (144) |12800/192 |4 / 3 000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000/256 (288) |25600 / 384 |8 / 6 000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000/512 (576) |51200/768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000/640 (720) |64000/960 |8 / 25000&nbsp;<sup>4</sup>

<sup>1</sup> maximální propustnost disku (IOPS nebo MB/s), která je možné použít u virtuálního počítače DSv2 Series, může být omezená počtem, velikostí a prokládáním připojených disků.  Podrobnosti najdete v tématu [navrhování pro vysoký výkon](../articles/virtual-machines/windows/premium-storage-performance.md).  
<sup>2</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.  
jsou k dispozici <sup>3</sup> omezené základní velikosti.  
<sup>4</sup> 25000 MB/s s akcelerovanými síťovými službami. 

<br>

## <a name="dv2-series-11-15"></a>Dv2-Series 11-15

ACU 210 – 250

Premium Storage:  Není podporováno

Ukládání Premium Storage do mezipaměti:  Není podporováno

| Size              | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS/čtení MB/s/zápis MB/s | Maximální počet datových disků/propustnost: IOPS | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8×500                         | 2 / 1 500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16×500                         | 4 / 3 000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32×500                       | 8 / 6 000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1000          | 60000 / 937 / 468                                        | 64/64x500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> instance je izolovaná na hardwaru vyhrazeném pro jediného zákazníka.  
<sup>2</sup> 25000 MB/s s akcelerovanými síťovými službami. 

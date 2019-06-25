---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/16/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 499b5ac77728bc14aadd5a2ef235e28b6ca83516
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133403"
---
Paměťově optimalizované velikosti nabídky virtuálních počítačů vysoký poměr paměti procesoru, které jsou velmi vhodné pro servery relačních databází, střední a velké mezipaměti a analýzu v paměti. Tento článek obsahuje informace o počtu virtuálních procesorů, datové disky a síťové adaptéry, jakož i úložiště propustnost a šířku pásma sítě pro jednotlivé velikosti v této skupině. 

* Mv2-Series nabízí nejvyšší počet virtuálních procesorů (až 208 virtuálních procesorů) a největší paměť (až 5.7 TiB) ze všech virtuálních počítačů v cloudu. Je ideální pro extrémně velké databáze nebo další aplikace, kterým přináší výhody vysoký počet virtuálních procesorů a velké množství paměti.
 
* Řada M-Series nabízí počet vysokou virtuálních procesorů (až 128 virtuálních procesorů) a velké množství paměti (až do 3,8 TiB). Je taky ideální pro extrémně velké databáze nebo jinými aplikacemi, kterým přináší výhody vysoký počet virtuálních procesorů a velké množství paměti.

* Řady Dv2-series, G-series a jejich protějšky DSv2 a GS jsou ideální pro aplikace, které vyžadují rychlejší virtuální procesory, vyšší výkon dočasného úložiště nebo mají vysoké nároky na paměť. Nabízejí výkonnou kombinaci pro mnoho podnikových aplikací.

* Virtuální počítače Dv2-series, nástupce původní řady D-series, mají výkonnější procesor. Procesor v Dv2-series je asi o 35 % rychlejší než procesor v D-series. Je založen na nejnovější generace 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) 2,4 GHz nebo E5-2673 v4 (Broadwell) 2,3 GHz procesorech a s technologií Intel Turbo Boost Technology 2.0 můžete až na 3,1 GHz. Řada Dv2-series má stejnou konfiguraci paměti a disku jako řada D.

* Ev3-series funkce E5-2673 v4 (Broadwell) 2,3 GHz procesor konfigurace technologie hyper-threaded, poskytuje lepší návrh hodnoty pro většinu obecných úloh účel a přináší Ev3 soulad s virtuálními počítači pro obecné účely většina jiných cloudech.  Paměti došlo k rozbalení (ze 7 GiB/virtuální procesor do 8 GiB/virtuálních procesorů) zatímco disku a sítě limity byly upraveny na základě vázané na jádro, aby bylo v souladu s přechodem na hyperthreadingem.  Ev3 je dále až vysoký poměr paměti velikosti virtuálních počítačů řady D/Dv2.

* Azure Compute nabízí velikosti virtuálních počítačů, které jsou izolované na konkrétní typ hardwaru a vyhrazené pro jednoho zákazníka.  Tyto velikosti virtuálních počítačů jsou nejvhodnější pro úlohy, které vyžadují vysoký stupeň izolace od ostatních zákazníků a kterých se týkají například požadavky na dodržování předpisů a zákonné požadavky.  Zákazníci můžou dál rozdělit prostředky těchto virtuálních počítačů izolované pomocí vybrat [podpora Azure pro vnořených virtuálních počítačích](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Projděte si tabulky virtuálních počítačů řady níže pro izolované možnosti virtuálního počítače.

## <a name="esv3-series"></a>Esv3-series 

ACU: 160-190 <sup>1</sup>

Premium Storage:  Podporováno

Premium Storage ukládání do mezipaměti:  Podporováno

Instance ESv3-series jsou založené na procesorech 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell), jejichž frekvence se s technologií Intel Turbo Boost 2.0 může zvýšit až na 3,5 GHz, a používají službu Premium Storage. Instance Ev3-series jsou ideální pro podnikové aplikace s vysokými nároky na paměť.


| Velikost             | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000 / 32 (50)                                                       | 3200 / 48                                | 2 / 1 000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000 / 64 (100)                                                      | 6400 / 96                                | 2 / 2 000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4 / 4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000 / 256 (400)                                                    | 25600 / 384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000 / 320 (400)                                                    | 32000 / 480                              | 8 / 10000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8 / 16 000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000 / 1200                             | 8 / 30000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000 / 1200                             | 8 / 30000                             |


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
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3 000 / 46 / 23                                               | 2 / 1 000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6 000 / 93 / 46                                               | 2 / 2 000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12 000 / 187 / 93                                             | 4 / 4000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24 000 / 375 / 187                                            | 8 / 8000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48 000 / 750 / 375                                            | 8 / 16 000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96 000 / 1 000 / 500                                           | 8 / 30000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96 000 / 1 000 / 500                                           | 8 / 30000           |

<sup>1</sup> Ev3-series Virtuálních počítačů vybavené technologií Intel® Hyper-Threading.

<sup>2</sup> core dostupné velikosti omezen.

<sup>3</sup> instance je izolovaná na hardware vyhrazený pro jediného zákazníka.


## <a name="mv2-series"></a>Řada Mv2

Premium Storage: Podporováno

Premium Storage ukládání do mezipaměti: Podporováno

Akcelerátor zápisu: [Podporuje se](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

Mv2 řady funkcí vysokou propustnost, nízká latence, namapuje přímo místní úložiště NVMe, které běží na hyper-threaded Intel® Xeon® Platinum procesoru 8180 M 2,5 GHz (Skylake) prostřednictvím všechny základní základní frekvenci 2,5 GHz a frekvenci maximální turbo 3.8 GHz. Všechny velikosti virtuálních počítačů řady Mv2 můžete využívat trvalé disky úrovně standard a premium. Instance Mv2-series jsou optimalizované pro paměť velikosti virtuálních počítačů nabízí bezkonkurenční výpočetní výkon pro podporu velkých databází v paměti a úlohami. nabízí vysoký poměr paměti procesoru, která je ideální pro servery relačních databází, velkými mezipaměťmi a v paměti Analytics. 

|Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>1, 2</sup> | 208 | 5700 | 4 096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16 000 |
| Standard_M208s_v2<sup>1, 2</sup> | 208 | 2850 | 4 096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16 000 |

Funkce technologie Intel® Hyper-Threading Mv2-series Virtuálních počítačů  

<sup>1</sup> tyto velké virtuální počítače vyžadovat jeden z těchto podporovaných hostovaných operačních systémů: Windows Server 2016, Windows Server 2019, SLES 12 SP4, SLES 15.

<sup>2</sup> Mv2-series virtuálních počítačů 2. generace pouze. Pokud používáte Linux, najdete v části Jak najít a vybrat bitovou kopii operačního systému SUSE Linux.

#### <a name="find-a-suse-image"></a>Najít image operačního systému SUSE

Na webu Azure Portal vyberte vhodnou image operačního systému SUSE Linux: 

1. Na webu Azure Portal, vyberte **vytvořit prostředek** 
1. Vyhledejte "SUSE SAP" 
1. SLES pro SAP 2. generace Image jsou dostupné jako buď s průběžnými platbami nebo použít vlastní předplatné (BYOS). Ve výsledcích hledání rozbalte kategorii požadovanou image:

    * SUSE Linux Enterprise Server (SLES) pro SAP
    * SUSE Linux Enterprise Server (SLES) pro SAP (BYOS)
    
1. Image SUSE kompatibilní s Mv2-series mají předponu názvu `GEN2:`. Následující Image operačního systému SUSE jsou k dispozici pro virtuální počítače řady Mv2:

    * GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 pro aplikace SAP
    * GEN2: SUSE Linux Enterprise Server (SLES) 15 pro aplikace SAP
    * GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 pro aplikace SAP (BYOS)
    * GEN2: SUSE Linux Enterprise Server (SLES) 15 pro aplikace SAP (BYOS)

#### <a name="select-a-suse-image-via-azure-cli"></a>Vybrat bitovou kopii operačního systému SUSE prostřednictvím rozhraní příkazového řádku Azure

Chcete-li zobrazit seznam aktuálně dostupných SLES pro SAP image pro virtuální počítače řady Mv2, použijte tuto [ `az vm image list` ](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az-vm-image-list) příkaz:

```azurecli
az vm image list --output table --publisher SUSE --sku gen2 --all
```

Příkaz vstupů tuto chvíli k dispozici virtuální počítače generace 2 k dispozici od společnosti SUSE pro virtuální počítače řady Mv2. 

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

ACU: 160-180 <sup>1</sup>

Premium Storage:  Podporováno

Premium Storage ukládání do mezipaměti:  Podporováno

Akcelerátor zápisu:  [Podporuje se](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Velikost            | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218.75 | 256  | 8  | 10000 / 100 (793)  | 5000  / 125 | 4 / 2 000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437.5  | 512  | 16 | 20000 / 200 (1587) | 10000 / 250 | 8 / 4 000 |
| Standard_M32ts | 32 | 192    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ls | 32 | 256    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M64s  | 64 | 1024   | 2 048 | 64 | 80000 / 800 (6348)| 40000 / 1000 | 8 / 16 000          |
| Standard_M64ls  | 64 | 512    | 2 048 | 64 | 80000 / 800 (6348) | 40000 / 1000 | 8 / 16 000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1 792 | 2 048 | 64 | 80000 / 800 (6348)| 40000 / 1000 | 8 / 16 000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2 048        | 4 096  | 64 | 160000 / 1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892  | 4 096 | 64 | 160000 / 1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1024 | 7168  | 64 | 80000  / 800  (1228) | 40000 / 1000 | 8 / 16 000 |
| Standard_M64m  | 64  | 1 792 | 7168  | 64 | 80000  / 800  (1228) | 40000 / 1000 | 8 / 16 000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2 048 | 14336 | 64 | 250000 / 1600 (2456) | 80000 / 2000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3892 | 14336 | 64 | 250000 / 1600 (2456) | 80000 / 2000 | 8 / 32000 |



<sup>1</sup> funkce virtuálních počítačů řady M-series technologii Intel® Hyper-Threading

<sup>2</sup> více než 64 virtuálních procesorů se vyžaduje jednu z těchto podporovaných hostovaných operačních systémů: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 a Red Hat Enterprise Linux, CentOS 7.3 nebo Oracle Linux 7.3 s LIS 4.2.1.

<sup>3</sup> core dostupné velikosti omezen.

<sup>4</sup> instance je izolovaná na hardware vyhrazený pro jediného zákazníka.
<br>


## <a name="dsv2-series-11-15"></a>DSv2-series 11-15

ACU: 210 - 250 <sup>1</sup>

Premium Storage:  Podporováno

Premium Storage ukládání do mezipaměti:  Podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Max. datových disků | Max. propustnost dočasného úložiště a úložiště v mezipaměti: IOPS / MB/s (velikost mezipaměti v GiB) | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet / očekávaný šířka pásma (MB/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000 / 64 (72) |6400 / 96 |2 / 1 500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000 / 128 (144) |12800 / 192 |4 / 3 000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000 / 256 (288) |25600 / 384 |8 / 6 000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000 / 512 (576) |51200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000 / 640 (720) |64000 / 960 |8 / 25000&nbsp;<sup>4</sup>

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
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1000          | 60000 / 937 / 468                                        | 64 / 64 x 500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> instance je izolovaná na hardware vyhrazený pro jediného zákazníka.  
<sup>2</sup> 25000 MB/s s Akcelerovanými síťovými službami. 

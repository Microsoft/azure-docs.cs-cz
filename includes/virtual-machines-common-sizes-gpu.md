---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 06/11/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 0325fc8cabc43988fb27a307921977b9b487c123
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286294"
---
Velikosti virtuálních počítačů optimalizované pro GPU jsou specializované virtuální počítače dostupné s jedním nebo několika grafickými procesory NVIDIA. Tyto velikosti jsou navržené pro úlohy náročné na výpočetní výkon, náročné na grafiku a vizualizaci. Tento článek poskytuje informace o počtu a typu GPU, vCPU, datových disků a síťových karet. Propustnost úložiště a šířka pásma sítě jsou také zahrnuty pro každou velikost v tomto seskupení.

* Velikosti **NC, NCv2 a NCv3** jsou optimalizované pro aplikace a algoritmy náročné na výpočetní výkon a síťové prostředky. Mezi příklady patří aplikace a simulace založené na CUDA a OpenCL, AI a obsáhlý Learning. NCv3-Series se zaměřuje na vysoce výkonné výpočetní úlohy s grafickým procesorem NVIDIA Tesla V100. Řada NC-Series používá procesor Intel Xeon E5-2690 V3 V3 (Haswell) a virtuální počítače řady NCv2-Series a NCv3-Series používají procesor Intel Xeon E5-2690 v4 (Broadwell).

* **ND a NDv2** Řada ND-Series se zaměřuje na školicí a odvozené scénáře pro obsáhlý Learning. Používá NVIDIA Tesla P40 GPU a procesor Intel Xeon E5-2690 v4 (Broadwell). NDv2-Series používá procesor Intel Xeon Platinum 8168 (Skylake).

* Velikosti **NV a NVv3** jsou optimalizované a navržené pro vzdálené vizualizace, streamování, hraní, kódování a scénáře VDI pomocí platforem, jako je OpenGL a DirectX.  Tyto virtuální počítače jsou řízené grafickým procesorem NVIDIA Tesla M60.

## <a name="nc-series"></a>NC-Series

Premium Storage:  Nepodporuje se

Ukládání Premium Storage do mezipaměti:  Nepodporuje se

Virtuální počítače řady NC-Series jsou napájené kartou [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) a procesorem Intel Xeon E5-2690 V3 (Haswell). Uživatelé můžou zpracovávejte data rychleji tím, že využívají CUDA pro aplikace pro průzkumy energie, simulace havárií, trasování paprsků, hloubkové učení a další. Konfigurace NC24r poskytuje síťové rozhraní s nízkou latencí a vysokou propustností optimalizované pro vysoce provázané úlohy paralelního zpracování.

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = polovina karty K80.

*Podpora RDMA

## <a name="ncv2-series"></a>NCv2-Series

Premium Storage:  Podporováno

Ukládání Premium Storage do mezipaměti:  Podporováno

Virtuální počítače řady NCv2-Series využívají grafické procesory [NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/) . Tyto GPU můžou poskytnout více než dvojnásobný výpočetní výkon řady NC-Series. Zákazníci můžou využít těchto aktualizovaných GPU pro tradiční úlohy HPC, jako je modelování zásobníku, sekvence DNA, analýza bílkovin, simulace Monte Carlo a další. Kromě GPU využívají virtuální počítače řady NCv2-Series také procesory Intel Xeon E5-2690 v4 (Broadwell).

Konfigurace NC24rs v2 poskytuje síťové rozhraní s nízkou latencí a vysokou propustností optimalizované pro vysoce provázané úlohy paralelního zpracování.

> [!IMPORTANT]
> Pro tuto řadu velikostí je kvóta vCPU (jádro) ve vašem předplatném zpočátku v každé oblasti nastavená na 0. [Požádejte o zvýšení kvóty vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) pro tuto rodinu v [dostupné oblasti](https://azure.microsoft.com/regions/services/).
>

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | ---  | ---| --- |
| Standard_NC6s_v2 | 6 |112 | 736 | 1 | 16 | 12 | 20000/ 200 | 4 |
| Standard_NC12s_v2 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2 | 24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 |
| Standard_NC24rs_v2* | 24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 |

1 GPU = jedna karta P100.

*Podpora RDMA

## <a name="ncv3-series"></a>NCv3-series

Premium Storage:  Podporováno

Ukládání Premium Storage do mezipaměti:  Podporováno

Virtuální počítače řady NCv3-Series využívají grafické procesory [NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/) . Tyto GPU můžou poskytnout 1,5 x výpočetní výkon NCv2-Series. Zákazníci můžou využít těchto aktualizovaných GPU pro tradiční úlohy HPC, jako je modelování zásobníku, sekvence DNA, analýza bílkovin, simulace Monte Carlo a další. Konfigurace NC24rs V3 poskytuje síťové rozhraní s nízkou latencí a vysokou propustností optimalizované pro vysoce provázané úlohy paralelního zpracování. Kromě GPU využívají virtuální počítače řady NCv3-Series také procesory Intel Xeon E5-2690 v4 (Broadwell).

> [!IMPORTANT]
> Pro tuto řadu velikostí je kvóta vCPU (jádro) ve vašem předplatném zpočátku v každé oblasti nastavená na 0. [Požádejte o zvýšení kvóty vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) pro tuto rodinu v [dostupné oblasti](https://azure.microsoft.com/regions/services/).
>

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 | 6 |112 | 736 | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3 | 24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 |

1 GPU = jedna karta V100.

*Podpora RDMA

## <a name="ndv2-series-preview"></a>NDv2-Series (Preview)

Premium Storage:  Podporováno

Ukládání Premium Storage do mezipaměti:  Podporováno

InfiniBand Nepodporuje se

Virtuální počítač řady NDv2-Series je novým doplňkem k rodině GPU navržené pro potřeby úloh HPC, AI a Machine Learning. Využívá se 8 NVIDIA Tesla V100 NVLINK propojených GPU a 40 Intel Xeon Platinum 8168 (Skylake) a 672 GiB systémové paměti. Instance řady NDv2-Series poskytují špičkový výkon FP32 a FP64 pro úlohy HPC a AI využívající architektury Cuda, TensorFlow, Pytorch, Caffe a další.

[Zaregistrujte se a získejte přístup k těmto počítačům ve verzi Preview](https://aka.ms/ndv2signup).
<br>

| Size | Virtuální procesory | GPU | Memory (Paměť) | Síťové karty (Max.) | Dočasné úložiště (SSD) GiB | Max. Datové disky | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální šířka pásma sítě | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND40s_v2 | 40 | 8 V100 (NVLink) | 672 GiB | 8 | 2948 | 32 | 80000 / 800 | 24000 MB/s |

## <a name="nd-series"></a>ND-Series

Premium Storage:  Podporováno

Ukládání Premium Storage do mezipaměti:  Podporováno

Virtuální počítače řady ND-Series jsou nově přidané do rodiny GPU navržené pro AI a pro úlohy s hloubkovým učením. Nabízí vynikající výkon pro školení a odvozování. Instance ND jsou napájené pomocí [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU a procesorů Intel Xeon E5-2690 v4 (Broadwell). Tyto instance poskytují vynikající výkon pro operace s plovoucí desetinnou čárkou s jednoduchou přesností pro úlohy AI s využitím Microsoft Cognitive Toolkit, TensorFlow, Caffe a dalších platforem. Řada ND-Series také nabízí mnohem větší paměť GPU (24 GB) a umožňuje zpracovat mnohem větší modely neuronových sítí. Podobně jako řada NC-Series nabízí konfiguraci se sekundární sítí s nízkou latencí a vysokou propustností prostřednictvím RDMA a InfiniBand konektivitu, takže můžete spouštět rozsáhlé školicí úlohy zahrnující mnoho GPU.

> [!IMPORTANT]
> Pro tuto řadu velikostí je kvóta vCPU (jader) na oblast ve vašem předplatném zpočátku nastavená na 0. [Požádejte o zvýšení kvóty vCPU](../articles/azure-supportability/resource-manager-core-quotas-request.md) pro tuto rodinu v [dostupné oblasti](https://azure.microsoft.com/regions/services/).
>

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s | 6 |112 | 736 | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s | 12 |224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 | 
| Standard_ND24s | 24 |448 | 2948 | 4 | 96 | 32 | 80000 / 800 | 8 |
| Standard_ND24rs* | 24 |448 | 2948 | 4 | 96 | 32 | 80000 / 800 | 8 |

1 GPU = jedna karta P40.

*Podpora RDMA

## <a name="nv-series"></a>NV-Series

Premium Storage:  Nepodporuje se

Ukládání Premium Storage do mezipaměti:  Nepodporuje se

Virtuální počítače řady NV využívají technologii [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU a technologii NVIDIA Grid pro aplikace s akcelerovanými pracovními plochami a virtuálními plochami, kde zákazníci mohou vizualizovat svá data nebo simulace. Uživatelé mohou vizualizovat své pracovní postupy náročné na grafiku na instancích NV a získat tak vynikající možnosti grafiky a také spouštět úlohy s jednoduchou přesností, jako je například kódování a vykreslování. Virtuální počítače řady NV využívají i procesory Intel Xeon E5-2690 V3 (Haswell).

Každý grafický procesor v instancích NV obsahuje licenci na MŘÍŽKu. Tato licence vám poskytne flexibilitu pro použití instance NV jako virtuální pracovní stanice pro jednoho uživatele nebo 25 souběžných uživatelů se může připojit k virtuálnímu počítači pro scénář virtuální aplikace.

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet | Virtuální pracovní stanice | Virtuální aplikace |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = polovina karty M60.

## <a name="nvv3-series--sup1sup"></a>NVv3-Series <sup>1</sup>

Premium Storage:  Podporováno

Ukládání Premium Storage do mezipaměti:  Podporováno

Virtuální počítače řady NVv3-Series využívají technologii [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU a technologii NVIDIA Grid s procesory Intel E5-2690 v4 (Broadwell). Tyto virtuální počítače jsou určené pro akcelerované grafické aplikace GPU a virtuální plochy, kde si zákazníci chtějí vizualizovat svá data, simulovat výsledky pro zobrazení, práci na CAD nebo vykreslování a streamování obsahu. Tyto virtuální počítače navíc mohou spouštět úlohy v jednoduché přesnosti, jako je kódování a vykreslování. Virtuální počítače s NVv3 podporují Premium Storage a přidávají se s dvojnásobnou paměťovou pamětí (RAM) ve srovnání s jejím předchůdcem (řady NV).  

Každý grafický procesor v NVv3 instancích obsahuje licenci na MŘÍŽKu. Tato licence vám poskytne flexibilitu pro použití instance NV jako virtuální pracovní stanice pro jednoho uživatele nebo 25 souběžných uživatelů se může připojit k virtuálnímu počítači pro scénář virtuální aplikace.

| Size | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet | Virtuální pracovní stanice | Virtuální aplikace | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV12s_v3 |12 |112 |320 | 1 | 8 | 12 | 20000/200 | 4 | 1 | 25 |
| Standard_NV24s_v3 |24 |224 |640 | 2 | 16 | 24 | 40000/400 | 8 | 2 | 50 |
| Standard_NV48s_v3 |48 |448 |1280 | 4 | 32 | 32 | 80000 / 800 | 8 | 4 | 100 |

1 GPU = polovina karty M60.

<sup>1</sup> NVv3-Series VM – technologie Intel Hyper-Threading

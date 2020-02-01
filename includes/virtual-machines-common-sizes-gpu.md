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
ms.openlocfilehash: 0f15a6a277020d593465fa0aa78fc434413c4545
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76909174"
---
Velikosti virtuálních počítačů optimalizované pro GPU jsou specializované virtuální počítače dostupné s jedním nebo několika grafickými procesory NVIDIA. Tyto velikosti jsou navržené pro úlohy náročné na výpočetní výkon, náročné na grafiku a vizualizaci. Tento článek poskytuje informace o počtu a typu GPU, vCPU, datových disků a síťových karet. Propustnost úložiště a šířka pásma sítě jsou také zahrnuty pro každou velikost v tomto seskupení.

* Velikosti **NC, NCv2 a NCv3** jsou optimalizované pro aplikace a algoritmy náročné na výpočetní výkon a síťové prostředky. Mezi příklady patří aplikace a simulace založené na CUDA a OpenCL, AI a obsáhlý Learning. NCv3-Series se zaměřuje na vysoce výkonné výpočetní úlohy s grafickým procesorem NVIDIA Tesla V100. Řada NC-Series používá procesor Intel Xeon E5-2690 V3 V3 (Haswell) a virtuální počítače řady NCv2-Series a NCv3-Series používají procesor Intel Xeon E5-2690 v4 (Broadwell).

* **ND a NDv2** Řada ND-Series se zaměřuje na školicí a odvozené scénáře pro obsáhlý Learning. Používá NVIDIA Tesla P40 GPU a procesor Intel Xeon E5-2690 v4 (Broadwell). NDv2-Series používá procesor Intel Xeon Platinum 8168 (Skylake).

* Velikosti **NV a NVv3** jsou optimalizované a navržené pro vzdálené vizualizace, streamování, hraní, kódování a scénáře VDI pomocí platforem, jako je OpenGL a DirectX.  Tyto virtuální počítače jsou řízené grafickým procesorem NVIDIA Tesla M60.

* Velikosti **NVv4** jsou optimalizované a navržené pro VDI a vzdálenou vizualizaci. Díky partioned GPU nabízí NVv4 správnou velikost pro úlohy, které vyžadují menší prostředky GPU.  Tyto virtuální počítače jsou řízené grafickým procesorem AMD Radeon Instinct MI25.


## <a name="nc-series"></a>NC-Series

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Virtuální počítače řady NC-Series jsou napájené kartou [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) a procesorem Intel Xeon E5-2690 V3 (Haswell). Uživatelé můžou zpracovávejte data rychleji tím, že využívají CUDA pro aplikace pro průzkumy energie, simulace havárií, trasování paprsků, hloubkové učení a další. Konfigurace NC24r poskytuje síťové rozhraní s nízkou latencí a vysokou propustností optimalizované pro vysoce provázané úlohy paralelního zpracování.

| Velikost | vCPU | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1\. místo | 12 | 24 | 1\. místo |
| Standard_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = polovina karty K80.

*Podpora RDMA

## <a name="ncv2-series"></a>NCv2-Series

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Virtuální počítače řady NCv2-Series využívají grafické procesory [NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/) . Tyto GPU můžou poskytnout více než dvojnásobný výpočetní výkon řady NC-Series. Zákazníci můžou využít těchto aktualizovaných GPU pro tradiční úlohy HPC, jako je modelování zásobníku, sekvence DNA, analýza bílkovin, simulace Monte Carlo a další. Kromě GPU využívají virtuální počítače řady NCv2-Series také procesory Intel Xeon E5-2690 v4 (Broadwell).

Konfigurace NC24rs v2 poskytuje síťové rozhraní s nízkou latencí a vysokou propustností optimalizované pro vysoce provázané úlohy paralelního zpracování.

> [!IMPORTANT]
> Pro tuto řadu velikostí je kvóta vCPU (jádro) ve vašem předplatném zpočátku v každé oblasti nastavená na 0. [Požádejte o zvýšení kvóty vCPU](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) pro tuto rodinu v [dostupné oblasti](https://azure.microsoft.com/regions/services/).
>

| Velikost | vCPU | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | ---  | ---| --- |
| Standard_NC6s_v2 | 6 |112 | 736 | 1\. místo | 16 | 12 | 20000/ 200 | 4 |
| Standard_NC12s_v2 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2 | 24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 |
| Standard_NC24rs_v2* | 24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 |

1 GPU = jedna karta P100.

*Podpora RDMA

## <a name="ncv3-series"></a>NCv3-series

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Virtuální počítače řady NCv3-Series využívají grafické procesory [NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/) . Tyto GPU můžou poskytnout 1,5 x výpočetní výkon NCv2-Series. Zákazníci můžou využít těchto aktualizovaných GPU pro tradiční úlohy HPC, jako je modelování zásobníku, sekvence DNA, analýza bílkovin, simulace Monte Carlo a další. Konfigurace NC24rs V3 poskytuje síťové rozhraní s nízkou latencí a vysokou propustností optimalizované pro vysoce provázané úlohy paralelního zpracování. Kromě GPU využívají virtuální počítače řady NCv3-Series také procesory Intel Xeon E5-2690 v4 (Broadwell).

> [!IMPORTANT]
> Pro tuto řadu velikostí je kvóta vCPU (jádro) ve vašem předplatném zpočátku v každé oblasti nastavená na 0. [Požádejte o zvýšení kvóty vCPU](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) pro tuto rodinu v [dostupné oblasti](https://azure.microsoft.com/regions/services/).
>

| Velikost | vCPU | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 | 6 |112 | 736 | 1\. místo | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3 | 12 |224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3 | 24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 80000 / 800 | 8 |

1 GPU = jedna karta V100.

*Podpora RDMA

## <a name="updated-ndv2-series-preview"></a>Aktualizace NDv2-Series (Preview)

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

InfiniBand: podporované

Virtuální počítač řady NDv2-Series je novým doplňkem k rodině GPU navržené pro potřeby nejnáročnějších úloh AI, strojového učení, simulace a prostředí HPC s podporou GPU. 

NDv2 je napájený pomocí 8 NVIDIA Tesla V100 NVLINK – připojené GPU s 32 GB paměti GPU. Každý virtuální počítač s NDv2 má taky 40 jader Intel Xeon Platinum 8168 (Skylake) 672, které neobsahují GiB systémové paměti. 

Instance NDv2 poskytují vynikající výkon pro úlohy HPC a AI s využitím CUDA výpočetních jader optimalizovaných pro GPU a mnoha nástrojů AI, ML a Analytics, které podporují akceleraci GPU, jako je například TensorFlow, Pytorch, Caffe, RAPIDS a další. rozhraní. 

V kritickém případě je NDv2 sestaven pro výpočetně náročné škálování (což znamená 8 GPU na virtuální počítač) a škálování na více instancí (úlohy vzájemně pracujících více virtuálních počítačů). NDv2 Series teď podporuje 100 gigabitové sítě InfiniBand s EDR back-end, podobně jako dostupné v řadě virtuálních počítačů HPC s vysokým výkonem, aby bylo možné podporovat vysoce výkonné clustery pro paralelní scénáře, včetně distribuovaného školení pro AI a ML. Tato síť back-end podporuje všechny hlavní protokoly InfiniBand, včetně těch, které pracují s knihovnami NCCL2 společnosti NVIDIA, což umožňuje bezproblémové clusteringu GPU.

> Při [povolování InfiniBand](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) na virtuálním počítači s ND40rs_v2 použijte prosím ovladač OFED 4.7-1.0.0.1 Mellanox.

> V důsledku zvýšené paměti GPU vyžaduje nový virtuální počítač ND40rs_v2 použití [virtuálních počítačů generace 2](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) a imagí z Marketplace. 

> [Přihlaste se, abyste si vyžádali předběžný přístup k virtuálnímu počítači NDv2 ve verzi Preview.](https://aka.ms/AzureNDrv2Preview)

> Poznámka: ND40s_v2 o velikosti 16 GB na paměť GPU již není k dispozici pro verzi Preview a byla nahrazena aktualizovaným ND40rs_v2.
<br>

| Velikost | vCPU | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální šířka pásma sítě | Maximální počet síťových karet |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 16 | 32 | 80000 / 800 | 24000 MB/s | 8 |

## <a name="nd-series"></a>ND-Series

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Virtuální počítače řady ND-Series jsou nově přidané do rodiny GPU navržené pro AI a pro úlohy s hloubkovým učením. Nabízí vynikající výkon pro školení a odvozování. Instance ND jsou napájené pomocí [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU a procesorů Intel Xeon E5-2690 v4 (Broadwell). Tyto instance poskytují vynikající výkon pro operace s plovoucí desetinnou čárkou s jednoduchou přesností pro úlohy AI s využitím Microsoft Cognitive Toolkit, TensorFlow, Caffe a dalších platforem. Řada ND-Series také nabízí mnohem větší paměť GPU (24 GB) a umožňuje zpracovat mnohem větší modely neuronových sítí. Podobně jako řada NC-Series nabízí konfiguraci se sekundární sítí s nízkou latencí a vysokou propustností prostřednictvím RDMA a InfiniBand konektivitu, takže můžete spouštět rozsáhlé školicí úlohy zahrnující mnoho GPU.

> [!IMPORTANT]
> Pro tuto řadu velikostí je kvóta vCPU (jader) na oblast ve vašem předplatném zpočátku nastavená na 0. [Požádejte o zvýšení kvóty vCPU](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) pro tuto rodinu v [dostupné oblasti](https://azure.microsoft.com/regions/services/).
>

| Velikost | vCPU | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s | 6 |112 | 736 | 1\. místo | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s | 12 |224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 | 
| Standard_ND24s | 24 |448 | 2948 | 4 | 96 | 32 | 80000 / 800 | 8 |
| Standard_ND24rs * | 24 |448 | 2948 | 4 | 96 | 32 | 80000 / 800 | 8 |

1 GPU = jedna karta P40.

*Podpora RDMA

## <a name="nv-series"></a>NV-Series

Premium Storage: nepodporováno

Ukládání Premium Storage do mezipaměti: nepodporováno

Virtuální počítače řady NV využívají technologii [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU a technologii NVIDIA Grid pro aplikace s akcelerovanými pracovními plochami a virtuálními plochami, kde zákazníci mohou vizualizovat svá data nebo simulace. Uživatelé mohou vizualizovat své pracovní postupy náročné na grafiku na instancích NV a získat tak vynikající možnosti grafiky a také spouštět úlohy s jednoduchou přesností, jako je například kódování a vykreslování. Virtuální počítače řady NV využívají i procesory Intel Xeon E5-2690 V3 (Haswell).

Každý grafický procesor v instancích NV obsahuje licenci na MŘÍŽKu. Tato licence vám poskytne flexibilitu pro použití instance NV jako virtuální pracovní stanice pro jednoho uživatele nebo 25 souběžných uživatelů se může připojit k virtuálnímu počítači pro scénář virtuální aplikace.

| Velikost | vCPU | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet | Virtuální pracovní stanice | Virtuální aplikace |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1\. místo | 8 | 24 | 1\. místo | 1\. místo | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = polovina karty M60.

## <a name="nvv3-series--sup1sup"></a>NVv3-Series <sup>1</sup>

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Virtuální počítače řady NVv3-Series využívají technologii [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU a technologii NVIDIA Grid s procesory Intel E5-2690 v4 (Broadwell). Tyto virtuální počítače jsou určené pro akcelerované grafické aplikace GPU a virtuální plochy, kde si zákazníci chtějí vizualizovat svá data, simulovat výsledky pro zobrazení, práci na CAD nebo vykreslování a streamování obsahu. Tyto virtuální počítače navíc mohou spouštět úlohy v jednoduché přesnosti, jako je kódování a vykreslování. Virtuální počítače s NVv3 podporují Premium Storage a přidávají se s dvojnásobnou paměťovou pamětí (RAM) ve srovnání s jejím předchůdcem (řady NV).  

Každý grafický procesor v NVv3 instancích obsahuje licenci na MŘÍŽKu. Tato licence vám poskytne flexibilitu pro použití instance NV jako virtuální pracovní stanice pro jednoho uživatele nebo 25 souběžných uživatelů se může připojit k virtuálnímu počítači pro scénář virtuální aplikace.

| Velikost | vCPU | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální propustnost disku bez mezipaměti: IOPS / MB/s | Maximální počet síťových karet | Virtuální pracovní stanice | Virtuální aplikace | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV12s_v3 |12 |112 |336 | 1\. místo | 8 | 12 | 20000/200 | 4 | 1\. místo | 25 |
| Standard_NV24s_v3 |24 |224 |672 | 2 | 16 | 24 | 40000/400 | 8 | 2 | 50 |
| Standard_NV48s_v3 |48 |448 |1344 | 4 | 32 | 32 | 80000 / 800 | 8 | 4 | 100 |

1 GPU = polovina karty M60.

<sup>1</sup> NVv3-Series VM – technologie Intel Hyper-Threading

## <a name="nvv4-series-preview--sup1sup"></a>NVv4-Series (Preview) <sup>1</sup>

Premium Storage: podporováno

Ukládání Premium Storage do mezipaměti: podporováno

Virtuální počítače řady NVv4-Series využívají procesory [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU a AMD EPYC 7V12 (Řím). S NVv4-Series Azure zavádí virtuální počítače s částečnými procesory GPU. Vyberte správnou velikost virtuálního počítače pro grafické aplikace s grafickým procesorem (GPU) a virtuální plochy od 1.8. GiB vyrovnávací paměti pro plný grafický procesor s 16 GiB vyrovnávací pamětí snímků. Virtuální počítače s NVv4 v současné době podporují jenom hostovaný operační systém Windows.

[Zaregistrujte se a získejte přístup k těmto počítačům ve verzi Preview](https://aka.ms/nvv4signup).
<br>

| Velikost | vCPU | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | --- | --- | 
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 | 
| Standard_NV32as_v4 |32 |112 |704 | 1\. místo | 16 | 32 | 8 | 



<sup>1</sup> NVv4-Series virtuální počítače s technologií AMD s více vlákny


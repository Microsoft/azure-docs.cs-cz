---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 11/14/2018
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: b6df4ada1aa13e20c7ad52d2b58cdf9c783f9e24
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51716155"
---
GPU optimalizované virtuálních počítačů velikosti jsou specializované virtuální počítače s jedním nebo několika grafickými procesory NVIDIA. Tyto velikosti jsou navržené pro úlohy náročné na výpočetní prostředky, náročné na grafiku a vizualizace. Tento článek obsahuje informace o počtu a typu GPU, virtuálních procesorů, datové disky a síťové adaptéry. Úložiště propustnost a šířku pásma sítě jsou také zahrnuté pro jednotlivé velikosti v této skupině. 

* **Síťový adaptér, NCv2, NCv3, ND a NDv2** velikostí optimalizovaných pro aplikace náročné na výpočetní výkon i síťové prostředky a algoritmy. Některé příklady jsou aplikace pro systém CUDA a OpenCL a simulace, AI a hloubkového učení. Řady NCv3-series se zaměřuje na vysoce výkonné výpočetní úlohy s NVIDIA Tesla V100 GPU.  Řada ND-Series se zaměřuje na scénáře odvozování a trénování pro účely hloubkového učení. Využívá grafické procesory NVIDIA Tesla P40.
* **NV a NVv2** velikosti jsou optimalizované a navržené pro vzdálené vizualizace, streamování, hry, kódování a scénáře VDI s využitím architektur typu OpenGL a DirectX.  Tyto virtuální počítače se zálohují na NVIDIA Tesla M60 GPU.


## <a name="nc-series"></a>NC-Series

Storage úrovně Premium: Není podporováno.

Premium Storage ukládání do mezipaměti: Není podporováno

Virtuální počítače řady NC-series využívají [NVIDIA Tesla K80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf) karty. Uživatel může zpracovávejte prostřednictvím dat rychleji s využitím CUDA pro zkoumání aplikace energie, simulace selhání, ray trasovaných vykreslování, obsáhlý learning a další. Konfiguraci NC24r poskytuje nízkou latenci a vysokou propustnost síťového rozhraní optimalizovaná pro paralelní výpočetní úlohy s provázaností.


| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 8 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 16 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 32 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 32 | 64 | 4 |

1 GPU = polovina karty K80.

*Podpora RDMA

## <a name="ncv2-series"></a>NCv2-Series

Storage úrovně Premium: podporováno

Ukládání do mezipaměti služby Premium Storage: podporováno

Virtuální počítače řady NCv2-series využívají [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf) GPU. Tyto grafické procesory poskytují více než 2 x větší výpočetní výkon řady NC-Series. Zákazníci můžou využít tyto aktualizované grafické procesory pro tradiční úlohy HPC, jako je modelování ložisek, DNA sekvencování, proteinové analýzy, simulace typu Monte Carlo a další. Konfigurace v2 NC24rs poskytuje nízkou latenci a vysokou propustnost síťového rozhraní optimalizovaná pro paralelní výpočetní úlohy s provázaností.

> [!IMPORTANT]
> Pro tuto řadu velikostí kvóty virtuálních procesorů (jader) v rámci vašeho předplatného je zpočátku nastaven 0 v jednotlivých oblastech. [Požádat o zvýšení kvóty virtuálních procesorů](../articles/azure-supportability/resource-manager-core-quotas-request.md) pro tuto řadu v [dostupné oblasti](https://azure.microsoft.com/regions/services/).
>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | ---  | --- |
| Standard_NC6s_v2 |6 |112 | 736 | 1 | 16 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 2948 | 4 | 64 | 32 | 8 |
| Standard_NC24rs_v2* |24 |448 | 2948 | 4 | 64 | 32 | 8 |

1 GPU = jedna karta P100.

*Podpora RDMA

## <a name="ncv3-series"></a>NCv3-series

Storage úrovně Premium: podporováno

Ukládání do mezipaměti služby Premium Storage: podporováno

Virtuální počítače řady NCv3-series využívají [NVIDIA Tesla V100](http://www.nvidia.com/content/PDF/Volta-Datasheet.pdf) GPU. Tyto grafické procesory poskytují výpočetní výkon řady NCv2-series 1, 5násobek. Zákazníci můžou využít tyto aktualizované grafické procesory pro tradiční úlohy HPC, jako je modelování ložisek, DNA sekvencování, proteinové analýzy, simulace typu Monte Carlo a další. Konfigurace v3 NC24rs poskytuje nízkou latenci a vysokou propustnost síťového rozhraní optimalizovaná pro paralelní výpočetní úlohy s provázaností.

> [!IMPORTANT]
> Pro tuto řadu velikostí kvóty virtuálních procesorů (jader) v rámci vašeho předplatného je zpočátku nastaven 0 v jednotlivých oblastech. [Požádat o zvýšení kvóty virtuálních procesorů](../articles/azure-supportability/resource-manager-core-quotas-request.md) pro tuto řadu v [dostupné oblasti](https://azure.microsoft.com/regions/services/).
>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 736 | 1 | 16 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 2948 | 4 | 64 | 32 | 8 | 
| Standard_NC24rs_v3 * |24 |448 | 2948 | 4 | 64 | 32 | 8 |

1 GPU = jedna karta V100.

*Podpora RDMA

## <a name="ndv2-series-preview"></a>NDv2-series (ve verzi Preview)


Storage úrovně Premium: podporováno

Ukládání do mezipaměti služby Premium Storage: podporováno

Infiniband: Není podporováno.


NDv2-series virtuálních počítačů jsou novým rozšířením řady GPU navržený pro potřeby HPC, AI a strojové učení. Využívají 8 vzájemně propojených procesorů GPU NVIDIA Tesla V100 NVLINK, 40 jader Intel Skylake a 672 GiB systémové paměti. Instance řady NDv2-Series poskytují špičkový výkon FP32 a FP64 pro úlohy HPC a AI využívající architektury Cuda, TensorFlow, Pytorch, Caffe a další.

[Zaregistrujte se a získejte přístup k těmto počítačům ve verzi preview](https://aka.ms/ndv2signup).
<br>


| Velikost              | virtuálních procesorů | GPU              | Memory (Paměť)  | Síťové karty (Max.) | Max. Velikost disku           | Max. datové disky (1 023 GB) | Maximální šířka pásma sítě | 
|-------------------|-------------|-------------------|--------|------------------|---------|------------|--------------------------|--------------------|--------------------------------|-----------------------------------------|-----------------------|------------|
| Standard_ND40s_v2 | 40     | 8 V100 (NVlilnk) | 672 GB | 8          | Dočasné 1344 / 2948XIO | 32    | 24 000 MB/s             | 

## <a name="nd-series"></a>ND-Series

Storage úrovně Premium: podporováno

Ukládání do mezipaměti služby Premium Storage: podporováno

Virtuální počítače ND-series jsou novým rozšířením řady GPU určený pro AI a hloubkového učení úlohy. Nabízejí mimořádný výkon por školení a odvozování. Instance ND využívají [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU. Tyto instance poskytují špičkový výkon pro jednoduchou přesnost s plovoucí desetinnou čárkou bodu operace pro úlohy AI využívající Microsoft Cognitive Toolkit, TensorFlow, Caffe a další architektury. Řada ND-Series také nabízí mnohem větší paměť GPU (24 GB) a umožňuje zpracovat mnohem větší modely neuronových sítí. Stejně jako NC-series i řada ND-series nabízí konfiguraci se sekundární síť s nízkou latencí a vysokou propustností přes RDMA a připojení InfiniBand, takže můžete spouštět rozsáhlé školicí úlohy využívající velký počet GPU.

> [!IMPORTANT]
> Pro tuto řadu velikostí je 0 zpočátku nastaven kvóty virtuálních procesorů (jader) na oblast v rámci vašeho předplatného. [Požádat o zvýšení kvóty virtuálních procesorů](../articles/azure-supportability/resource-manager-core-quotas-request.md) pro tuto řadu v [dostupné oblasti](https://azure.microsoft.com/regions/services/).
>

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 736 | 1 | 24 | 12 | 4 |
| Standard_ND12s |12 |224 | 1474 | 2 | 48 | 24 | 8 | 
| Standard_ND24s |24 |448 | 2948 | 4 | 96 | 32 | 8 |
| Standard_ND24rs * |24 |448 | 2948 | 4 | 96 | 32 | 8 |

1 GPU = jedna karta P40.

*Podpora RDMA

## <a name="nv-series"></a>NV-Series

Storage úrovně Premium: Není podporováno.

Premium Storage ukládání do mezipaměti: Není podporováno

Virtuální počítače řad NV využívají [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU a NVIDIA GRID technologie pro desktop accelerated aplikací i virtuálních desktopů kde Zákazníci mají možnost vizualizovat svoje data nebo simulace. Uživatelé budou moci vizualizovat své graficky náročné pracovní postupy na instancích NV na špičkové grafické možnosti a navíc spouštět úlohy s jednoduchou přesností například kódování a vykreslování. 

Každý GPU v instancích NV obsahuje licenci mřížky. Tuto licenci získáte flexibilitu používat NV instance jako virtuální pracovní stanice pro jednoho uživatele nebo 25 souběžných uživatelů můžete připojit k virtuálnímu počítači pro scénář virtuální aplikace.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet | Virtuálních pracovních stanic | Virtuální aplikace | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = polovina karty M60.

## <a name="nvv2-series-preview"></a>NVv2-series (ve verzi Preview)

Storage úrovně Premium: podporováno

Ukládání do mezipaměti služby Premium Storage: podporováno

Virtuální počítače řady NVv2 využívají [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU a NVIDIA GRID technologii procesorů Intel Broadwell. Tyto virtuální počítače je určená pro urychlení GPU grafických aplikací a virtuálních ploch, kde chcete zákazníkům vizualizovat svoje data, simulovat výsledky k zobrazení, pracovat na CAD nebo vykreslování a datového proudu obsahu. Tyto virtuální počítače navíc mohou spouštět úlohy v jednoduché přesnosti, jako je kódování a vykreslování. NVv2 virtuálních počítačů podporují službu Premium Storage a dodávají s dvakrát systémové paměti (RAM) ve srovnání s jeho předchůdce NV-series.  

Každý GPU v instancích NVv2 obsahuje licenci mřížky. Tuto licenci získáte flexibilitu používat NV instance jako virtuální pracovní stanice pro jednoho uživatele nebo 25 souběžných uživatelů můžete připojit k virtuálnímu počítači pro scénář virtuální aplikace.

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet | Virtuálních pracovních stanic | Virtuální aplikace | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |

1 GPU = polovina karty M60.

 

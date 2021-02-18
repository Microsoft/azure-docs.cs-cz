---
title: Konfigurace monitorování GPU pomocí Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje, jak můžete nakonfigurovat monitorování clusterů Kubernetes s použitím uzlů NVIDIA a GPU s povoleným grafickým procesorem AMD pomocí Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 11a9de0872fd3e7589332322b3f8fb855d2c88ec
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100612341"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>Konfigurace monitorování GPU pomocí Azure Monitor pro kontejnery

Počínaje agentem verze *ciprod03022019* služba Azure monitor for Containers Integrated Agents teď podporuje monitorování využití GPU (grafické procesory) na uzlech clusteru Kubernetes s grafickým procesorem a monitorování lusků/kontejnerů požadujících a používání prostředků GPU.

## <a name="supported-gpu-vendors"></a>Podporovaní dodavatelé GPU

Azure Monitor for Containers podporuje monitorování clusterů GPU od následujících dodavatelů GPU:

- [GRAF](https://developer.nvidia.com/kubernetes-gpu)

- [AMD](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Azure Monitor for Containers automaticky spouští monitorování využití GPU na uzlech a GPU, který požaduje lusky a zatížení, shromažďováním následujících metrik v intervalech 60sec a jejich ukládání do tabulky **InsightMetrics** .

>[!NOTE]
>Po zřízení clusteru s uzly GPU zajistěte, aby byl pro spuštění úloh GPU nainstalovaný [ovladač GPU](../../aks/gpu-cluster.md) , jak to vyžaduje AKS. Azure Monitor pro kontejnery shromažďují metriky GPU prostřednictvím ovladače GPU, které běží v uzlu. 

|Název metriky |Dimenze metriky (značky) |Description |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName, gpuId, gpuModel, gpuVendor|Procento času v průběhu minulého ukázkového období (60 sekund), během kterého byl procesor GPU zaneprázdněný nebo aktivně zpracováván pro kontejner. Cyklus cel je číslo mezi 1 a 100. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName |Každý kontejner může určovat omezení jako jeden nebo více GPU. Není možné vyžádat ani omezit zlomek GPU. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName |Každý kontejner může vyžádat jeden nebo více GPU. Není možné vyžádat ani omezit zlomek GPU.|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName, gpuId, gpuModel, gpuVendor |Množství paměti GPU v bajtech, které je možné použít pro konkrétní kontejner. |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, ContainerName, gpuId, gpuModel, gpuVendor |Množství paměti GPU v bajtech, které používá určitý kontejner. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Počet GPU v uzlu, který může používat Kubernetes. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Celkový počet GPU v uzlu. |

## <a name="gpu-performance-charts"></a>Grafy výkonu GPU 

Azure Monitor for containers obsahuje předem nakonfigurované grafy pro metriky uvedené dříve v tabulce jako sešit GPU pro každý cluster. Popis sešitů dostupných pro Azure Monitor kontejnerů najdete [v tématu sešity v Azure monitor for Containers](../insights/container-insights-reports.md) .

## <a name="next-steps"></a>Další kroky

- V tématu [použití GPU pro úlohy náročné na výpočetní výkon ve službě Azure Kubernetes Service](../../aks/gpu-cluster.md) (AKS) se dozvíte, jak nasadit cluster AKS, který obsahuje uzly s podporou GPU.

- Přečtěte si další informace o [SKU virtuálních počítačů optimalizovaných pro GPU v Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Přečtěte si [podporu GPU v Kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) , kde se dozvíte víc o Kubernetes experimentální podpoře pro správu GPU v jednom nebo několika uzlech v clusteru.

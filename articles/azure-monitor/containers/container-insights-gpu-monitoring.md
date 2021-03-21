---
title: Konfigurace monitorování GPU pomocí kontejneru Insights | Microsoft Docs
description: Tento článek popisuje, jak můžete nakonfigurovat monitorování clusterů Kubernetes s využitím uzlů NVIDIA a AMD GPU s podporou kontejneru Insights.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 2958b000ac0dabcd7fddf75a58f553b705a95e9a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731863"
---
# <a name="configure-gpu-monitoring-with-container-insights"></a>Konfigurace monitorování GPU pomocí kontejneru Insights

Počínaje agentem verze *ciprod03022019* podporuje integrovaný agent služby Container Insights monitorovat využití GPU (grafické procesory) na uzlech clusteru Kubernetes s grafickým procesorem a monitorovat lusky/kontejnery požadující a používání prostředků GPU.

## <a name="supported-gpu-vendors"></a>Podporovaní dodavatelé GPU

Container Insights podporuje monitorování clusterů GPU od následujících dodavatelů GPU:

- [GRAF](https://developer.nvidia.com/kubernetes-gpu)

- [AMD](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Služba Container Insights automaticky spouští monitorování využití GPU na uzlech a GPU, který požaduje lusky a zatížení, díky shromažďování následujících metrik v intervalech 60sec a jejich ukládání do tabulky **InsightMetrics** .

>[!NOTE]
>Po zřízení clusteru s uzly GPU zajistěte, aby byl pro spuštění úloh GPU nainstalovaný [ovladač GPU](../../aks/gpu-cluster.md) , jak to vyžaduje AKS. Služby Container Insights shromažďují metriky GPU prostřednictvím ovladače GPU, které běží v uzlu. 

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

Služba Container Insights obsahuje předem nakonfigurované grafy pro metriky uvedené dříve v tabulce jako sešit GPU pro každý cluster. Popis sešitů dostupných pro službu Container Insights najdete [v tématu sešity ve službě Container Insights](../insights/container-insights-reports.md) .

## <a name="next-steps"></a>Další kroky

- V tématu [použití GPU pro úlohy náročné na výpočetní výkon ve službě Azure Kubernetes Service](../../aks/gpu-cluster.md) (AKS) se dozvíte, jak nasadit cluster AKS, který obsahuje uzly s podporou GPU.

- Přečtěte si další informace o [SKU virtuálních počítačů optimalizovaných pro GPU v Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Přečtěte si [podporu GPU v Kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) , kde se dozvíte víc o Kubernetes experimentální podpoře pro správu GPU v jednom nebo několika uzlech v clusteru.

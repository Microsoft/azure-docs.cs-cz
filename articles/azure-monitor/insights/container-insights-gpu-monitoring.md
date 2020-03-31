---
title: Konfigurace monitorování GPU pomocí Azure Monitoru pro kontejnery | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak můžete nakonfigurovat monitorování Kubernetes clustery s NVIDIA a AMD GPU povoleno uzly s Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 958f5ab33edcd280f5673391eba907728f1153c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373307"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>Konfigurace monitorování GPU pomocí Azure Monitoru pro kontejnery

Počínaje verzí *agenta ciprod03022019*, Azure Monitor pro kontejnery integrované agent nyní podporuje monitorování GPU (grafické zpracování jednotky) využití na GPU-aware clusteru uzly a monitorování pods/kontejnery vyžádání a používání prostředků GPU.

## <a name="supported-gpu-vendors"></a>Podporovaní dodavatelé GPU

Azure Monitor pro kontejnery podporuje monitorování clusterů GPU od následujících dodavatelů GPU:

- [Nvidia](https://developer.nvidia.com/kubernetes-gpu)

- [Amd](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Azure Monitor pro kontejnery automaticky spustí monitorování využití GPU na uzlech a GPU požadující pody a úlohy shromažďováním následujících metrik v intervalech 60 sec a jejich uložením v tabulce **InsightMetrics:**

|Název metriky |Metrická dimenze (tagy) |Popis |
|------------|------------------------|------------|
|kontejnerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|Procento času za poslední ukázkové období (60 sekund), během kterého byl gpu zaneprázdněn/aktivně zpracovával pro kontejner. Pracovní cyklus je číslo mezi 1 a 100. |
|kontejnerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Každý kontejner můžete zadat omezení jako jeden nebo více gpu. Není možné požadovat nebo omezit zlomek GPU. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Každý kontejner může požádat o jednu nebo více gpu. Není možné požadovat nebo omezit zlomek GPU.|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Množství paměti GPU v bajtů, které jsou k dispozici pro konkrétní kontejner. |
|containerGpumemoryUsedBajts |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Velikost paměti GPU v bajtů používaných konkrétní kontejner. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Počet GPU v uzlu, který může být použit Kubernetes. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Celkový počet gpu v uzlu. |

## <a name="gpu-performance-charts"></a>Grafy výkonu GPU 

Azure Monitor pro kontejnery zahrnuje předem nakonfigurované grafy pro metriky uvedené výše v tabulce jako sešit GPU pro každý cluster. **GPU uzlu GPU GPU GPU GPU** můžete najít přímo z clusteru AKS výběrem **sešitů** z levého podokna a z rozevíracího seznamu **Zobrazit sešity** v přehledu.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak nasadit cluster AKS, který zahrnuje uzly s podporou GPU, najdete v tématu [Použití gpu pro úlohy náročné na výpočetní výkon ve službě Azure Kubernetes Service](../../aks/gpu-cluster.md) (AKS).

- Další informace o [virtuálních počítačích optimalizovaných pro GPU v Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Projděte si [podporu GPU v Kubernetes,](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) abyste se dozvěděli více o experimentální podpoře Kubernetes pro správu GPU v jednom nebo více uzlech v clusteru.
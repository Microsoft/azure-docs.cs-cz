---
title: Dostupnost prostředků Azure Container Instances
description: Dostupnost výpočetní a paměťové prostředky pro službu Azure Container Instances v různých oblastech Azure.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 05/14/2019
ms.author: danlep
ms.openlocfilehash: 64b60178413e470cc7fe9b3991c6fc29b5a0f860
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794297"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Dostupnost prostředků pro Azure Container Instances v oblastech Azure

Tento článek podrobně popisuje dostupnost výpočetní a paměťové prostředky Azure Container Instances v oblastech Azure. 

Maximální prostředky, které jsou k dispozici pro nasazení jsou hodnoty uvedené [skupinu kontejnerů](container-instances-container-groups.md). Hodnoty jsou aktuální k datu publikování. 

> [!NOTE]
> Skupin kontejnerů vytvořených v rámci těchto omezení prostředků jsou závisí na dostupnosti v rámci oblasti nasazení. V případě velkého zatížení oblasti může při nasazování instancí docházet k selhání. Selhání nasazení zmírnit, zkuste nasazení instancí s nižšími nastavení prostředků, nebo zkuste nasazení později.

Informace o kvótách a další omezení v nasazeních, naleznete v tématu [kvóty a omezení pro Azure Container Instances](container-instances-quotas.md).

## <a name="availability---general"></a>Dostupnost – obecné

Následující oblasti a prostředky jsou k dispozici pro skupiny kontejnerů s Linuxem a [podporované](container-instances-faq.md#what-windows-base-os-images-are-supported) kontejnery založené na Windows serveru 2016.

| Location | Operační systém | Procesor | Paměť (GB) |
| -------- | -- | :---: | :-----------: |
| Kanada – střed, střed Indie, střed USA, východní Asie, východní USA, východní USA 2, Severní Evropa, střední část jihu USA, jihovýchodní Asie, Velká Británie – Jih, USA – západ | Linux | 4 | 16 |
| Západní Evropa, západní USA 2 | Linux | 4 | 14 |
| Austrálie – východ, Japonsko – východ | Linux | 2 | 8 |
| Střed USA – sever, Indie – jih | Linux | 2 | 3,5 |
| Západní Evropa | Windows | 4 | 16 |
| USA – východ, USA – západ | Windows | 4 | 14 |
| Austrálie – východ, Kanada – střed, střed Indie, střed USA, východní Asie, východní USA 2, Japonsko – východ, severní centrální USA, Severní Evropa, střed USA – Jih, Asie – jihovýchod, Indie – Jih, Velká Británie – Jih, USA – západ 2 | Windows | 2 | 3,5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Dostupnost – Windows Server. 2019 LTSC, 1809 nasazení (preview)

Následující oblasti a prostředky jsou k dispozici skupin kontejnerů s kontejnery založené na Windows Server 2019 (preview).

| Location | Operační systém | Procesor | Paměť (GB) |
| -------- | -- | :---: | :-----------: |
| Jihovýchodní Asie, Severní Evropa, západní Evropa, střed USA, východní USA, USA – Západ, USA – západ 2 | Windows | 4 | 16 |
| Východní USA 2 | Windows | 2 | 3,5 |


## <a name="availability---virtual-network-deployment-preview"></a>Dostupnost – nasazení virtuální sítě (preview)

Jsou k dispozici pro skupinu kontejnerů nasazených v těchto oblastech a prostředky [virtuální síť Azure](container-instances-vnet.md) (preview).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Dostupnost – prostředky GPU (preview)

Následující oblasti a prostředky jsou k dispozici pro skupinu kontejnerů nasazen s [GPU prostředky](container-instances-gpu.md) (preview).

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Další postup

Sdělte týmu vědět, pokud chcete zobrazit další oblasti nebo dostupnost zvýšení prostředků v [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Informace o řešení potíží s nasazením instance kontejneru najdete v tématu [řešení problémů s nasazením pomocí služby Azure Container Instances](container-instances-troubleshooting.md).

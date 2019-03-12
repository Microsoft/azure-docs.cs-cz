---
title: Dostupnost prostředků Azure Container Instances
description: Dostupnost výpočetní a paměťové prostředky pro službu Azure Container Instances v různých oblastech Azure.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 03/01/2019
ms.author: danlep
ms.openlocfilehash: 1ca23a95c746139963aa70ed20bb888152fd5cd8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554520"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Dostupnost prostředků pro Azure Container Instances v oblastech Azure

Tento článek podrobně popisuje dostupnost výpočetní a paměťové prostředky Azure Container Instances v oblastech Azure. 

Maximální prostředky, které jsou k dispozici pro nasazení jsou hodnoty uvedené [skupinu kontejnerů](container-instances-container-groups.md). Hodnoty jsou aktuální k datu publikování. Aktuální informace, použijte [seznamu možnosti](/rest/api/container-instances/listcapabilities/listcapabilities) rozhraní API. 

> [!NOTE]
> Skupin kontejnerů vytvořených v rámci těchto omezení prostředků jsou závisí na dostupnosti v rámci oblasti nasazení. V případě velkého zatížení oblasti může při nasazování instancí docházet k selhání. Selhání nasazení zmírnit, zkuste nasazení instancí s nižšími nastavení prostředků, nebo zkuste nasazení později.

Informace o kvótách a další omezení v nasazeních, naleznete v tématu [kvóty a omezení pro Azure Container Instances](container-instances-quotas.md).

## <a name="availability---general"></a>Dostupnost – obecné

| Umístění | Operační systém | Procesor | Paměť (GB) |
| -------- | -- | :---: | :-----------: |
| Kanada – střed, střed USA, východní USA 2, střed USA – jih | Linux | 4 | 16 |
| USA – východ, Severní Evropa, Západní Evropa, Západní USA, Západní USA 2 | Linux | 4 | 14 |
| Japonsko – východ | Linux | 2 | 8 |
| Austrálie – východ, Asie – jihovýchod | Linux | 2 | 7 |
| Střed Indie, východní Asie, severní centrální USA, Jižní Indie | Linux | 2 | 3,5 |
| USA – východ, Západní Evropa, Západní USA | Windows | 4 | 14 |
| Austrálie – východ, Kanada – střed, střed Indie, střed USA, východní Asie, východní USA 2, Japonsko – východ, USA (střed) – sever, Severní Evropa, střed USA – Jih, Indie – Jih, jihovýchodní Asie, USA – západ 2 | Windows | 2 | 3,5 |

## <a name="availability---virtual-network-deployment-preview"></a>Dostupnost – nasazení virtuální sítě (preview)

Jsou k dispozici pro skupinu kontejnerů nasazených v těchto oblastech a prostředky [virtuální síť Azure](container-instances-vnet.md) (preview)

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Dostupnost – prostředky GPU (preview)

Následující oblasti a prostředky jsou k dispozici pro skupinu kontejnerů nasazen s [GPU prostředky](container-instances-gpu.md) (preview)

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Další postup

Sdělte týmu vědět, pokud chcete zobrazit další oblasti nebo dostupnost zvýšení prostředků v [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Informace o řešení potíží s nasazením instance kontejneru najdete v tématu [řešení problémů s nasazením pomocí služby Azure Container Instances](container-instances-troubleshooting.md).

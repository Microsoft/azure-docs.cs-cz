---
title: Dostupnost prostředků Azure Container Instances
description: Dostupnost výpočetních a paměťových prostředků pro službu Azure Container Instances v různých oblastech Azure.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 05/14/2019
ms.author: danlep
ms.openlocfilehash: 24edce511c2d07050db1e77edeae4e587fcd79b0
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172396"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Dostupnost prostředků pro Azure Container Instances v oblastech Azure

Tento článek podrobně popisuje dostupnost Azure Container Instances výpočetních a paměťových prostředků v oblastech Azure. 

Zobrazené hodnoty jsou maximální počet dostupných prostředků na jedno nasazení [skupiny kontejnerů](container-instances-container-groups.md). Hodnoty jsou aktuální v době publikace. 

> [!NOTE]
> Skupiny kontejnerů vytvořené v rámci těchto omezení prostředků podléhají dostupnosti v rámci oblasti nasazení. V případě velkého zatížení oblasti může při nasazování instancí docházet k selhání. Pro zmírnění takového selhání nasazení zkuste nasadit instance s nižšími nastaveními prostředků nebo zkuste nasazení později.

Informace o kvótách a dalších omezeních v nasazeních najdete v tématu [kvóty a omezení pro Azure Container Instances](container-instances-quotas.md).

## <a name="availability---general"></a>Dostupnost – obecné

Pro skupiny kontejnerů se systémem Linux a podporovanými kontejnery Windows Server [](container-instances-faq.md#what-windows-base-os-images-are-supported) 2016 jsou k dispozici následující oblasti a prostředky.

| Location | OS | Procesor | Paměť (GB) |
| -------- | -- | :---: | :-----------: |
| Kanada – střed, Střed Indie, Střed USA, Východní Asie, Východní USA, Východní USA 2, Severní Evropa, Střed USA – jih, jihovýchodní Asie, Velká Británie – jih, Západní USA | Linux | 4 | 16 |
| Západní Evropa Západní USA 2 | Linux | 4 | 14 |
| Austrálie – východ, Japonsko – východ | Linux | 2 | 8 |
| Střed USA – sever Jižní Indie | Linux | 2 | 3,5 |
| Západní Evropa | Windows | 4 | 16 |
| Východní USA Západní USA | Windows | 4 | 14 |
| Austrálie – východ, Kanada – střed, Střed Indie, Střed USA, Východní Asie, Východní USA 2, Japonsko – východ, Střed USA – sever, Severní Evropa, Střed USA – jih, jihovýchodní Asie, Jižní Indie, Velká Británie – jih, Západní USA 2 | Windows | 2 | 3,5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Dostupnost – Windows Server 2019 LTSC, 1809 nasazení (Preview)

Následující oblasti a prostředky jsou k dispozici pro skupiny kontejnerů s kontejnery založenými na Windows serveru 2019 (Preview).

| Location | OS | Procesor | Paměť (GB) |
| -------- | -- | :---: | :-----------: |
| Jihovýchodní Asie, Severní Evropa, Západní Evropa, Střed USA, Východní USA, Západní USA západní USA 2 | Windows | 4 | 16 |
| Východní USA 2 | Windows | 2 | 3,5 |


## <a name="availability---virtual-network-deployment-preview"></a>Dostupnost – nasazení virtuální sítě (Preview)

Pro skupinu kontejnerů nasazené ve [službě Azure Virtual Network](container-instances-vnet.md) (Preview) jsou k dispozici následující oblasti a prostředky.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Dostupnost – prostředky GPU (Preview)

Pro skupinu kontejnerů nasazené s [prostředky GPU](container-instances-gpu.md) (Preview) jsou k dispozici následující oblasti a prostředky.

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Další postup

Nechejte tým informovat, pokud byste chtěli zobrazit další oblasti nebo zvýšit dostupnost prostředků na [aka.MS/ACI/Feedback](https://aka.ms/aci/feedback).

Informace o řešení potíží s nasazením instance kontejneru najdete v tématu [řešení potíží s nasazením pomocí Azure Container Instances](container-instances-troubleshooting.md).

---
title: Dostupnost prostředků podle oblasti
description: Dostupnost výpočetních a paměťových prostředků pro službu Azure Container Instances v různých oblastech Azure.
ms.topic: article
ms.date: 04/27/2020
ms.openlocfilehash: 97baa5199a1803bd967c0b55c846908ea5a2ddcf
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89565425"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Dostupnost prostředků pro Azure Container Instances v oblastech Azure

Tento článek podrobně popisuje dostupnost Azure Container Instances výpočetních, paměťových a úložných prostředků v oblastech Azure a v cílovém operačním systému. 

Zobrazené hodnoty jsou maximální počet dostupných prostředků na jedno nasazení [skupiny kontejnerů](container-instances-container-groups.md). Hodnoty jsou aktuální v době publikace. 

> [!NOTE]
> Skupiny kontejnerů vytvořené v rámci těchto omezení prostředků podléhají dostupnosti v rámci oblasti nasazení. V případě velkého zatížení oblasti může při nasazování instancí docházet k selhání. Pro zmírnění takového selhání nasazení zkuste nasadit instance s nižšími nastaveními prostředků nebo zkuste nasazení později nebo v jiné oblasti s dostupnými prostředky.

Informace o kvótách a dalších omezeních v nasazeních najdete v tématu [kvóty a omezení pro Azure Container Instances](container-instances-quotas.md).

## <a name="availability---general"></a>Dostupnost – obecné

Pro skupiny kontejnerů se systémem Linux a [podporovanými](container-instances-faq.md#what-windows-base-os-images-are-supported) kontejnery systému Windows Server 2016 jsou k dispozici následující oblasti a maximální počet prostředků.

| Oblasti | Operační systém | Max CPU (maximální využití procesoru) | Maximální velikost paměti (GB) | Úložiště (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Brazílie – jih, Kanada – střed, Střed Indie, Střed USA, Východní Asie, Východní USA, Východní USA 2, Severní Evropa, Střed USA – jih, jihovýchodní Asie, Jižní Indie, Velká Británie – jih, Západní Evropa, Západní USA, Západní USA 2 | Linux | 4 | 16 | 50 |
| Austrálie – východ, Japonsko – východ | Linux | 2 | 8 | 50 |
| USA – středosever | Linux | 2 | 3,5 | 50 |
| Brazílie – jih, Japonsko – východ, Západní Evropa | Windows | 4 | 16 | 20 |
| Východní USA Západní USA | Windows | 4 | 14 | 20 |
| Austrálie – východ, Kanada – střed, Střed Indie, Střed USA, Východní Asie, Východní USA 2, Střed USA – sever, Severní Evropa, Střed USA – jih, jihovýchodní Asie, Jižní Indie, Velká Británie – jih, Západní USA 2 | Windows | 2 | 3,5 | 20 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Dostupnost – Windows Server 2019 LTSC, 1809 nasazení (Preview)

Následující oblasti a maximální prostředky jsou k dispozici pro skupiny kontejnerů s kontejnery založenými na Windows serveru 2019 (Preview).

| Oblasti | Operační systém | Max CPU (maximální využití procesoru) | Maximální velikost paměti (GB) | Úložiště (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Austrálie – východ, Brazílie – jih, Kanada – střed, Střed Indie, Střed USA, Východní Asie, Východní USA, Japonsko – východ, Střed USA – sever, Severní Evropa, Střed USA – jih, jihovýchodní Asie, Jižní Indie, Velká Británie – jih, Západní Evropa | Windows | 4 | 16 | 20 |
| Východní USA 2 Západní USA 2 | Windows | 2 | 3,5 | 20 |


## <a name="availability---virtual-network-deployment"></a>Dostupnost – nasazení virtuální sítě

Pro skupinu kontejnerů nasazené ve [službě Azure Virtual Network](container-instances-vnet.md)jsou k dispozici následující oblasti a maximální počet prostředků.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Dostupnost – prostředky GPU (Preview)

Pro skupinu kontejnerů nasazenou s [prostředky GPU](container-instances-gpu.md) (Preview) jsou k dispozici následující oblasti a maximální počet prostředků.

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Další kroky

Nechejte tým informovat, pokud byste chtěli zobrazit další oblasti nebo zvýšit dostupnost prostředků na [aka.MS/ACI/Feedback](https://aka.ms/aci/feedback).

Informace o řešení potíží s nasazením instance kontejneru najdete v tématu [řešení potíží s nasazením pomocí Azure Container Instances](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

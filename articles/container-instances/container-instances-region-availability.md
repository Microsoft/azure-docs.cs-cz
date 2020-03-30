---
title: Dostupnost zdrojů podle oblastí
description: Dostupnost výpočetních prostředků a prostředků paměti pro službu Azure Container Instances v různých oblastech Azure.
ms.topic: article
ms.date: 02/19/2020
ms.author: danlep
ms.openlocfilehash: f429a165fe26cc9fc7aa973231f5a77163feef4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247134"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Dostupnost prostředků pro instance kontejnerů Azure v oblastech Azure

Tento článek podrobně popisuje dostupnost výpočetních, paměťových a úložných prostředků Azure container instance v oblastech Azure a podle cílového operačního systému. 

Uvedené hodnoty jsou maximální dostupné prostředky pro nasazení [skupiny kontejnerů](container-instances-container-groups.md). Hodnoty jsou aktuální v době zveřejnění. 

> [!NOTE]
> Skupiny kontejnerů vytvořené v rámci těchto omezení prostředků závisí na dostupnosti v rámci oblasti nasazení. V případě velkého zatížení oblasti může při nasazování instancí docházet k selhání. Chcete-li zmírnit takové selhání nasazení, zkuste nasadit instance s nižším nastavením prostředků nebo zkuste nasazení později.

Informace o kvótách a dalších omezeních ve vašich nasazeních najdete v [tématu Kvóty a limity pro instance kontejnerů Azure](container-instances-quotas.md).

## <a name="availability---general"></a>Dostupnost – obecné

Následující oblasti a maximální prostředky jsou k dispozici pro skupiny kontejnerů s Linuxem a [podporované](container-instances-faq.md#what-windows-base-os-images-are-supported) kontejnery založené na Windows Serveru 2016.

| Oblasti | Operační systém | Max CPU (maximální využití procesoru) | Maximální paměť (GB) | Úložiště (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Brazílie Jih, Kanada Střední, Střední Indie, Střední USA, Východní Asie, Východní USA, Východní USA 2, Severní Evropa, Jižní Střed USA, Jihovýchodní Asie, Jižní Indie, Velká Británie – jih, Západní Evropa, Západní USA, Západní USA 2 | Linux | 4 | 16 | 50 |
| Austrálie – východ, Japonsko – východ | Linux | 2 | 8 | 50 |
| USA – středosever | Linux | 2 | 3,5 | 50 |
| Brazílie – jih, Japonsko – východ, západní Evropa | Windows | 4 | 16 | 20 |
| Východní USA, Západní USA | Windows | 4 | 14 | 20 |
| Austrálie – východ, Kanada – střed, střední Indie, střední USA, východní Asie, východní USA 2, severní střední USA, severní Evropa, jižní střed USA, jihovýchodní Asie, Jižní Indie, Velká Británie – jih, Západ USA 2 | Windows | 2 | 3,5 | 20 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Dostupnost – nasazení Windows Server 2019 LTSC, 1809 (preview)

Následující oblasti a maximální prostředky jsou k dispozici pro skupiny kontejnerů s kontejnery se systémem Windows Server 2019 (náhled).

| Oblasti | Operační systém | Max CPU (maximální využití procesoru) | Maximální paměť (GB) | Úložiště (GB) |
| -------- | -- | :---: | :-----------: | :---: |
| Austrálie – východ, Brazílie – jih, Kanada – střed, střední Indie, střední USA, východní Asie, východní USA, Japonsko – východ, severní střed USA, severní Evropa, jižní střed USA, jihovýchodní Asie, jižní Indie, Velká Británie – jih, západní Evropa | Windows | 4 | 16 | 20 |
| Východní USA 2, Západ US 2 | Windows | 2 | 3,5 | 20 |


## <a name="availability---virtual-network-deployment"></a>Dostupnost – nasazení virtuální sítě

Následující oblasti a maximální prostředky jsou k dispozici pro skupinu kontejnerů nasazenou ve [virtuální síti Azure](container-instances-vnet.md).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Dostupnost – zdroje GPU (preview)

Následující oblasti a maximální prostředky jsou k dispozici pro skupinu kontejnerů nasazenou s [prostředky GPU](container-instances-gpu.md) (náhled).

> [!IMPORTANT]
> Zdroje GPU jsou k dispozici pouze na vyžádání. Chcete-li požádat o přístup k prostředkům GPU, odešlete [žádost o podporu Azure][azure-support].

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Další kroky

Dejte týmu vědět, zda chcete zobrazit další oblasti nebo zvýšit dostupnost zdrojů na [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Informace o řešení potíží s nasazením instance kontejneru najdete [v tématu Řešení problémů s nasazením s instancemi kontejneru Azure](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
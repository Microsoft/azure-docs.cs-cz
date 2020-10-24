---
title: Dostupnost prostředků podle oblasti
description: Dostupnost výpočetních a paměťových prostředků pro službu Azure Container Instances v různých oblastech Azure.
ms.topic: article
ms.date: 04/27/2020
ms.custom: references_regions
ms.openlocfilehash: 1ed3f50198c0410d9c893fe87523fa214ca03d88
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521454"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Dostupnost prostředků pro Azure Container Instances v oblastech Azure

Tento článek podrobně popisuje dostupnost Azure Container Instances výpočetních, paměťových a úložných prostředků v oblastech Azure a v cílovém operačním systému. Obecný seznam oblastí, které jsou k dispozici pro Azure Container Instances, najdete v tématu [dostupné oblasti](https://azure.microsoft.com/regions/services/).

Zobrazené hodnoty jsou maximální počet dostupných prostředků na jedno nasazení [skupiny kontejnerů](container-instances-container-groups.md). Hodnoty jsou aktuální v době publikace.

> [!NOTE]
> Skupiny kontejnerů vytvořené v rámci těchto omezení prostředků podléhají dostupnosti v rámci oblasti nasazení. V případě velkého zatížení oblasti může při nasazování instancí docházet k selhání. Pro zmírnění takového selhání nasazení zkuste nasadit instance s nižšími nastaveními prostředků nebo zkuste nasazení později nebo v jiné oblasti s dostupnými prostředky.

Informace o kvótách a dalších omezeních v nasazeních najdete v tématu [kvóty a omezení pro Azure Container Instances](container-instances-quotas.md).

## <a name="linux-container-groups"></a>Skupiny kontejnerů Linux

Následující oblasti a maximální prostředky jsou k dispozici pro skupiny kontejnerů s kontejnery Linux v obecných nasazeních, nasazeních [virtuálních sítí Azure](container-instances-vnet.md) a nasazení s využitím [prostředků GPU](container-instances-gpu.md) (Preview).

> [!IMPORTANT]
> Maximální počet prostředků v oblasti se liší v závislosti na vašem nasazení. Například region může mít v nasazení virtuální sítě Azure jiný maximální velikost procesoru a paměti než pro obecné nasazení. Stejná oblast může mít také jinou sadu maximálních hodnot pro nasazení s prostředky GPU. Před zaškrtnutím níže uvedených tabulek ověřte, zda je typ nasazení maximální hodnota ve vaší oblasti.

| Oblast | Max CPU (maximální využití procesoru) | Maximální velikost paměti (GB) | Maximální využití virtuální sítě | Maximální velikost paměti virtuální sítě (GB) | Úložiště (GB) | SKU GPU (Preview) |
| -------- | :---: | :---: | :----: | :-----: | :-------: | :----: |
| Austrálie – východ | 4 | 16 | 4 | 16 | 50 | – |
| Brazil South | 4 | 16 | 2 | 8 | 50 | – |
| Střední Kanada | 4 | 16 | 4 | 16 | 50 | – |
| Indie – střed | 4 | 16 | N/A | N/A | 50 | V100 |
| Střední USA | 4 | 16 | 4 | 16 | 50 | – |
| Východní Asie | 4 | 16 | 4 | 16 | 50 | – |
| USA – východ | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| USA – východ 2 | 4 | 16 | 4 | 16 | 50 | – |
| Japonsko – východ | 2 | 8 | 4 | 16 | 50 | – |
| Jižní Korea – střed | 4 | 16 | N/A | N/A | 50 | – |
| USA – středosever | 2 | 3,5 | 4 | 16 | 50 | – |
| Severní Evropa | 4 | 16 | 4 | 16 | 50 | K80 |
| Středojižní USA | 4 | 16 | 4 | 16 | 50 | – |
| Southeast Asia | 4 | 16 | 4 | 16 | 50 | P100, V100 |
| Indie – jih | 4 | 16 | N/A | N/A | 50 | – |
| Spojené království – jih | 4 | 16 | 4 | 16 | 50 | – |
| USA – středozápad| 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| West Europe | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| USA – západ | 4 | 16 | 2 | 4 | 16| – |
| Západní USA 2 | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |

Pro skupinu kontejnerů nasazenou s [prostředky GPU](container-instances-gpu.md) (Preview) jsou k dispozici následující maximální prostředky.

| SKU GPU | Počet GPU | Max CPU (maximální využití procesoru) | Maximální velikost paměti (GB) | Úložiště (GB) |
| --- | --- | --- | --- | --- |
| K80 | 1 | 6 | 56 | 50 |
| K80 | 2 | 12 | 112 | 50 |
| K80 | 4 | 24 | 224 | 50 |
| P100, V100 | 1 | 6 | 112 | 50 |
| P100, V100 | 2 | 12 | 224 | 50 |
| P100, V100 | 4 | 24 | 448 | 50 |

## <a name="windows-container-groups"></a>Skupiny kontejnerů Windows

Následující oblasti a maximální prostředky jsou k dispozici pro skupiny kontejnerů s [podporovanými a náhledem](container-instances-faq.md#what-windows-base-os-images-are-supported) kontejnerů Windows serveru.

| Oblast | Maximální využití procesoru Windows serveru 2016 | Windows Server 2016 max. paměť (GB) | Windows Server 2019 LTSC Max CPU | Windows Server 2019 LTSC Max paměť (GB) | Úložiště (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Austrálie – východ | 2 | 3,5 | 4 | 16 | 20 |
| Brazil South | 4 | 16 | 4 | 16 | 20 |
| Střední Kanada | 2 | 3,5 | 4 | 16 | 20 |
| Indie – střed | 2 | 3,5 | 4 | 16 | 20 |
| Střední USA | 2 | 3,5 | 4 | 16 | 20 |
| Východní Asie | 2 | 3,5 | 4 | 16 | 20 |
| USA – východ | 2 | 8 | 4 | 16 | 20 |
| USA – východ 2 | 2 | 3,5 | 2 | 3,5 | 20 |
| Francie – střed | 4 | 16 | 4 | 16 | 20 |
| Japonsko – východ | 4 | 16 | 4 | 16 | 20 |
| Jižní Korea – střed | 4 | 16 | 4 | 16 | 20 |
| USA – středosever | 2 | 3,5 | 4 | 16 | 20 |
| Severní Evropa | 2 | 3,5 | 4 | 16 | 20 |
| Středojižní USA | 2 | 3,5 | 4 | 16 | 20 |
| Indie – jih | 2 | 3,5 | 4 | 16 | 20 |
| Southeast Asia | 2 | 3,5 | 4 | 16 | 20 |
| Spojené království – jih | 2 | 3,5 | 4 | 16 | 20 |
| USA – středozápad | 4 | 16 | 4 | 16 | 20 |
| West Europe | 4 | 16 | 4 | 16 | 20 |
| USA – západ | 4 | 14 | N/A | N/A | 20 |
| Západní USA 2 | 2 | 3,5 | 2 | 3,5 | 20 |

## <a name="next-steps"></a>Další kroky

Nechejte tým informovat, pokud byste chtěli zobrazit další oblasti nebo zvýšit dostupnost prostředků na [aka.MS/ACI/Feedback](https://aka.ms/aci/feedback).

Informace o řešení potíží s nasazením instance kontejneru najdete v tématu [řešení potíží s nasazením pomocí Azure Container Instances](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

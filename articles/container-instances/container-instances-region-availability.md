---
title: Dostupnost prostředků podle oblasti
description: Dostupnost výpočetních a paměťových prostředků pro službu Azure Container Instances v různých oblastech Azure.
ms.topic: article
ms.date: 04/27/2020
ms.custom: references_regions
ms.openlocfilehash: e4fbf1023863f9f4c46e6bd2266f72ff2f7d7adc
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395865"
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
| East US | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| USA – východ 2 | 4 | 16 | 4 | 16 | 50 | – |
| Francie – střed | 4 | 16 | 4 | 16 | 50 | – |
| Japan East | 2 | 8 | 4 | 16 | 50 | – |
| Jižní Korea – střed | 4 | 16 | N/A | N/A | 50 | – |
| USA – středosever | 2 | 3,5 | 4 | 16 | 50 | K80, P100, V100 |
| Severní Evropa | 4 | 16 | 4 | 16 | 50 | K80 |
| Středojižní USA | 4 | 16 | 4 | 16 | 50 | – |
| Southeast Asia | 4 | 16 | 4 | 16 | 50 | P100, V100 |
| Indie – jih | 4 | 16 | N/A | N/A | 50 | – |
| Spojené království – jih | 4 | 16 | 4 | 16 | 50 | – |
| USA – středozápad| 4 | 16 | 4 | 16 | 50 | – |
| West Europe | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| USA – západ | 4 | 16 | 4 | 16 | 50 | – |
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

###  <a name="windows-server-2016"></a>Windows Server 2016

> [!NOTE]
> Další informace o hostitelích 1B, 2B a 3B najdete v tématu [Kompatibilita verzí hostitelů a kontejnerů](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility) .

| Oblast | Maximální využití procesoru 1B/2B | Maximální velikost paměti 1B/2B (GB) |Maximální využití procesoru 3B | Maximální velikost paměti 3B (v GB) | Úložiště (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Austrálie – východ | 2 | 8 | 2 | 3,5 | 20 |
| Brazil South | 4 | 16 | 4 | 16 | 20 |
| Střední Kanada | 2 | 3,5 | 2 | 3,5 | 20 |
| Indie – střed | 2 | 3,5 | 2 | 3,5 | 20 |
| Střední USA | 2 | 3,5 | 2 | 3,5 | 20 |
| Východní Asie | 2 | 3,5 | 2 | 3,5 | 20 |
| East US | 4 | 16 | 2 | 8 | 20 |
| USA – východ 2 | 2 | 3,5 | 4 | 16 | 20 |
| Japan East | 4 | 16 | 4 | 16 | 20 |
| Jižní Korea – střed | 4 | 16 | 4 | 16 | 20 |
| USA – středosever | 4 | 16 | 4 | 16 | 20 |
| Severní Evropa | 2 | 3,5 | 2 | 8 | 20 |
| Středojižní USA | 2 | 3,5 | 2 | 3,5 | 20 |
| Southeast Asia | N/A | N/A | 2 | 3,5 | 20 |
| Indie – jih | 2 | 3,5 | 2 | 3,5 | 20 |
| Spojené království – jih | 2 | 8 | 2 | 3,5 | 20 |
| USA – středozápad | 4 | 16 | 4 | 16 | 20 |
| West Europe | 4 | 16 | 4 | 16 | 20 |
| USA – západ | 4 | 16 | 2 | 8 | 20 |
| Západní USA 2 | 2 | 3,5 | 2 | 3,5 | 20 |


### <a name="windows-server-2019-ltsc"></a>Windows Server 2019 LTSC

> [!NOTE]
> Další informace o hostitelích 1B, 2B a 3B najdete v tématu [Kompatibilita verzí hostitelů a kontejnerů](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility) .

| Oblast | Maximální využití procesoru 1B/2B | Maximální velikost paměti 1B/2B (GB) |Maximální využití procesoru 3B | Maximální velikost paměti 3B (v GB) | Úložiště (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Austrálie – východ | 4 | 16 | 4 | 16 | 20 |
| Brazil South | 4 | 16 | 4 | 16 | 20 |
| Střední Kanada | 4 | 16 | 4 | 16 | 20 |
| Indie – střed | 4 | 16 | 4 | 16 | 20 |
| Střední USA | 4 | 16 | 4 | 16 | 20 |
| Východní Asie | 4 | 16 | 4 | 16 | 20 |
| East US | 4 | 16 | 4 | 16 | 20 |
| USA – východ 2 | 2 | 3,5 | 2 | 3,5 | 20 |
| Francie – střed | 4 | 16 | 4 | 16 | 20 |
| Japan East | N/A | Není k dispozici | 4 | 16 | 20 |
| Jižní Korea – střed | 4 | 16 | 4 | 16 | 20 |
| USA – středosever | 4 | 16 | 4 | 16 | 20 |
| Severní Evropa | 4 | 16 | 4 | 16 | 20 |
| Středojižní USA | 4 | 16 | 4 | 16 | 20 |
| Southeast Asia | 4 | 16 | 4 | 16 | 20 |
| Indie – jih | 4 | 16 | 4 | 16 | 20 |
| Spojené království – jih | 4 | 16 | 4 | 16 | 20 |
| USA – středozápad | 4 | 16 | 4 | 16 | 20 |
| West Europe | 4 | 16 | 4 | 16 | 20 |
| USA – západ | 4 | 16 | 4 | 16 | 20 |
| Západní USA 2 | 2 | 8 | 4 | 16 | 20 |

## <a name="next-steps"></a>Další kroky

Nechejte tým informovat, pokud byste chtěli zobrazit další oblasti nebo zvýšit dostupnost prostředků na [aka.MS/ACI/Feedback](https://aka.ms/aci/feedback).

Informace o řešení potíží s nasazením instance kontejneru najdete v tématu [řešení potíží s nasazením pomocí Azure Container Instances](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

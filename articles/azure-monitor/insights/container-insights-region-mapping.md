---
title: Azure Monitor pro mapování oblastí kontejnerů
description: Tento článek popisuje mapování oblastí podporované mezi Azure Monitor pro kontejnery, Pracovní prostor Analýzy protokolů a vlastní metriky.
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: a058f9cac987bb5c7130019f50370c6a176b09ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403428"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapování oblastí podporované službou Azure Monitor pro kontejnery

 Při povolení Azure Monitor pro kontejnery, jsou podporovány pouze určité oblasti pro propojení pracovního prostoru Log Analytics a clusteru AKS a shromažďování vlastnímetriky odeslané do Azure Monitoru.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapování podporovaná službou Log Analytics

Prostředky clusteru AKS nebo pracovní prostor Log Analytics se mohou napěchovat v jiných oblastech a v následující tabulce jsou uvedena naše mapování.

|**Oblast clusteru AKS** | **Oblast pracovního prostoru Analýzy protokolů** |
|-----------------------|------------------------------------|
|**Afrika** | |
|Jižní AfrikaSeverní |WestEurope |
|Jižní AfrikaZápad |WestEurope |
|**Austrálie** | |
|AustrálieVýchod |AustrálieVýchod |
|AustrálieStřední |AustrálieStřední |
|AustrálieCentral2 |AustrálieStřední |
|AustrálieVýchod |AustrálieVýchod |
|**Asie a Tichomoří** | |
|Východní Asie |Východní Asie |
|Jihovýchodní Asie |Jihovýchodní Asie |
|**Brazílie** | |
|BrazílieJižní | SouthCentralUS |
|**Kanada** ||
|KanadaStřední |KanadaStřední |
|KanadaVýchod |KanadaStřední |
|**Evropa** | |
|FranceCentral |FranceCentral |
|FrancieJižní |FranceCentral |
|Severní Evropa |Severní Evropa |
|Spojené královstvíJižní |Spojené královstvíJižní |
|UkZápad |Spojené královstvíJižní |
|WestEurope |WestEurope |
|**Indie** | |
|Střední Indie |Střední Indie |
|Jižní Indie |Střední Indie |
|Západní Indie |Střední Indie |
|**Japonsko** | |
|Japonskovýchod |Japonskovýchod |
|JaponskoZápad |Japonskovýchod |
|**Jižní Korea** | |
|KoreaStřední |KoreaStřední |
|KoreaJižní |KoreaStřední |
|**USA** | |
|Centrální US |Centrální US|
|Východní US |Východní US |
|Východní US2 |Východní US2 |
|Západní US |Západní US |
|Západní US2 |Západní US2 |
|WestCentralUS<sup>1</sup>|VýchodUS<sup>1</sup>|
|USA (Gov) – Virginia |USA (Gov) – Virginia |

<sup>1</sup> Z důvodu omezení kapacity není oblast k dispozici při vytváření nových prostředků. To zahrnuje pracovní prostor Analýzy protokolů. Již existující propojené zdroje v regionu by však měly pokračovat v práci.

## <a name="custom-metrics-supported-regions"></a>Vlastní metriky podporované oblasti

Shromažďování metrik z uzlů a podů služeb Azure Kubernetes Services (AKS) je podporováno pro publikování jako vlastní metriky pouze v [následujících oblastech Azure](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Další kroky

Chcete-li začít sledovat cluster AKS, přečtěte [si, jak povolit Azure Monitor pro kontejnery](container-insights-onboard.md) pochopit požadavky a dostupné metody povolit monitorování.  

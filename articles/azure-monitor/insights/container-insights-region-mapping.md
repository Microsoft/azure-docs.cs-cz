---
title: Mapování oblastí Azure Monitor pro kontejnery
description: Popisuje mapování oblastí podporované mezi Azure Monitor kontejnerů, Log Analytics pracovním prostoru a vlastními metrikami.
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 9e3b8635c70dfdf33b0a062be80c948cd77923cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91272901"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapování oblastí podporované Azure Monitor pro kontejnery

 Při povolování Azure Monitor pro kontejnery jsou podporovány pouze určité oblasti pro propojení pracovního prostoru Log Analytics a clusteru AKS a shromažďování vlastních metrik odeslaných do Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapování podporovaná v pracovním prostoru Log Analytics

Podporované oblasti AKS jsou uvedeny v [produktech, které jsou k dispozici v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service). Pracovní prostor Log Analytics musí být ve stejné oblasti kromě oblastí uvedených v následující tabulce. Podívejte se na [poznámky k verzi AKS](https://github.com/Azure/AKS/releases) pro aktualizace.


|**Oblast clusteru AKS** | **Oblast pracovního prostoru Log Analytics** |
|-----------------------|------------------------------------|
|**Afrika** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Austrálie** | |
|AustraliaCentral2 |AustraliaCentral |
|**Brazílie** | |
|BrazilSouth | SouthCentralUS |
|**Kanada** ||
|CanadaEast |CanadaCentral |
|**Evropa** | |
|FranceSouth |FranceCentral |
|UKWest |UKSouth |
|**Indie** | |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japonsko** | |
|JapanWest |JapanEast |
|**Jižní Korea** | |
|KoreaSouth |KoreaCentral |
|**USA** | |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|


<sup>1</sup> kvůli omezením kapacity není oblast při vytváření nových prostředků dostupná. To zahrnuje Log Analytics pracovní prostor. Stávající propojené prostředky v oblasti ale budou fungovat i nadále.

## <a name="custom-metrics-supported-regions"></a>Vlastní podporované oblasti metrik

Shromažďování metrik z uzlů clusterů Azure Kubernetes Services (AKS) a lusky se podporují pro publikování jako vlastní metriky pouze v následujících [oblastech Azure](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Další kroky

Pokud chcete začít monitorovat cluster AKS, přečtěte si, [Jak povolit Azure monitor kontejnerů](container-insights-onboard.md) pro pochopení požadavků a dostupných metod, které umožňují monitorování.  

---
title: Mapování oblastí v kontejneru Insights
description: Popisuje mapování oblastí podporovaných mezi službami Container Insights, Log Analytics pracovním prostorem a vlastními metrikami.
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: f9e910b1352109608becb82609e85e26d27d2cd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728871"
---
# <a name="region-mappings-supported-by-container-insights"></a>Mapování oblastí podporované službou Container Insights

 Při povolování služby Container Insights se pro propojení Log Analyticsho pracovního prostoru a clusteru AKS podporují jenom některé oblasti a shromažďují se vlastní metriky odeslané do Azure Monitor.

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

Shromažďování metrik z uzlů clusterů Azure Kubernetes Services (AKS) a lusky se podporují pro publikování jako vlastní metriky pouze v následujících [oblastech Azure](../essentials/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Další kroky

Pokud chcete začít monitorovat cluster AKS, přečtěte si, [Jak povolit službě Container Insights](container-insights-onboard.md) pochopit požadavky a dostupné metody pro povolení monitorování.  

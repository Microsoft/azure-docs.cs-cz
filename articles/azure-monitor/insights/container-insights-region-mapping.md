---
title: Azure Monitor pro mapování oblasti kontejnerů
description: Tento článek popisuje mapování oblasti podporovaná mezi Azure Monitor pro kontejnery a pracovní prostor Log Analytics, vlastní metriky.
services: azure-monitor
ms.service: azure-monitor
ms.workload: infrastructure-services
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 481a2a400be4e983e0a2337a200324061494efa1
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518076"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapování oblasti podporované službou Azure Monitor pro kontejnery

 Při povolování monitorování Azure pro kontejnery, pouze určité oblasti jsou podporovány pro propojení pracovního prostoru Log Analytics a AKS cluster a shromažďování vlastní metriky odeslali do Azure monitoru.

## <a name="log-analytics-workspace-supported-mappings"></a>Pracovní prostor log Analytics nepodporuje mapování

Prostředky clusteru AKS nebo pracovní prostor Log Analytics může být v jiných oblastech, přičemž v následující tabulce jsou uvedeny naše mapování.

|**Oblast clusteru AKS** | **Oblasti pracovního prostoru analýzy protokolů** |
|-----------------------|------------------------------------|
|**Afrika** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Austrálie** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**Asie a Tichomoří** | |
|Východní Asie |Východní Asie |
|SoutheastAsia |SoutheastAsia |
|**Brazílie** | |
|BrazilSouth | SouthCentralUS |
|**Canada** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**Evropa** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|UKSouth |UKSouth |
|UKWest |UKSouth |
|WestEurope |WestEurope |
|**Indie** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japonsko** | |
|JapanEast |JapanEast |
|JapanWest |JapanEast |
|**Jižní Korea** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**USA** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|

<sup>1</sup> z důvodu omezení kapacity, není k dispozici v oblasti, při vytváření nových prostředků. To zahrnuje pracovní prostor Log Analytics. Dříve existující propojených prostředků v oblasti však musí i nadále fungovat.

## <a name="custom-metrics-supported-regions"></a>Vlastní metriky podporované oblasti

Shromažďování metrik ze služby Azure Kubernetes (AKS) uzly clusterů a podů jsou podporovány pro publikování jako vlastní metriky v následujících tématech [oblastí Azure](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Další postup

Chcete-li zahájit monitorování clusteru AKS, zkontrolovat [povolení monitorování Azure pro kontejnery](container-insights-onboard.md) vám pomohou pochopit požadavky a dostupné metody, které chcete povolit monitorování.  
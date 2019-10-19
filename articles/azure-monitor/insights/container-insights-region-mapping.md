---
title: Mapování oblastí Azure Monitor pro kontejnery
description: Tento článek popisuje mapování oblastí podporované mezi Azure Monitor pro kontejnery, Log Analytics pracovním prostorem a vlastními metrikami.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.openlocfilehash: f22c62dddf2e38fa2c9471ce98b49a8aa32390b3
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554021"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapování oblastí podporované Azure Monitor pro kontejnery

 Při povolování Azure Monitor pro kontejnery jsou podporovány pouze určité oblasti pro propojení pracovního prostoru Log Analytics a clusteru AKS a shromažďování vlastních metrik odeslaných do Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapování podporovaná v pracovním prostoru Log Analytics

Prostředky clusteru AKS nebo pracovní prostor Log Analytics můžou být v jiných oblastech a v následující tabulce jsou uvedená naše mapování.

|**Oblast clusteru AKS** | **Oblast pracovního prostoru Log Analytics** |
|-----------------------|------------------------------------|
|**Poskytl** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Austrálie** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**Asie a Tichomoří** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**Brazílie** | |
|BrazilSouth | SouthCentralUS |
|**Kanada** ||
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
|USA – Virginie |USA – Virginie |

<sup>1</sup> kvůli omezením kapacity není oblast při vytváření nových prostředků dostupná. To zahrnuje Log Analytics pracovní prostor. Stávající propojené prostředky v oblasti ale budou fungovat i nadále.

## <a name="custom-metrics-supported-regions"></a>Vlastní podporované oblasti metrik

Shromažďování metrik z uzlů clusterů Azure Kubernetes Services (AKS) a lusky se podporují pro publikování jako vlastní metriky pouze v následujících [oblastech Azure](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Další kroky

Pokud chcete začít monitorovat cluster AKS, přečtěte si, [Jak povolit Azure monitor kontejnerů](container-insights-onboard.md) pro pochopení požadavků a dostupných metod, které umožňují monitorování.  

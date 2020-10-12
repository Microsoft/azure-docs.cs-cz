---
title: Konfigurace metrik a protokolů cloudu pro Azure API Management bránu pro samoobslužné hostování | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat metriky a protokoly cloudu pro Azure API Management bránu pro samoobslužné hostování.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: apimpm
ms.openlocfilehash: 3bbfd167e89ae1b5f9b7de1df5fd1cb72c720cb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86254522"
---
# <a name="configure-cloud-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Konfigurace metrik a protokolů cloudu pro Azure API Management bránu pro samoobslužné hostování

Tento článek poskytuje podrobné informace o konfiguraci metrik a protokolů cloudu pro [samoobslužnou bránu](./self-hosted-gateway-overview.md).

Samoobslužná brána musí být přidružená ke službě API Management a vyžaduje odchozí připojení TCP/IP k Azure na portu 443. Brána využívá odchozí připojení k posílání telemetrie do Azure, pokud je to tak nakonfigurované. 

## <a name="metrics"></a>Metriky
Ve výchozím nastavení vychází brána v místním prostředí několik metrik prostřednictvím [Azure monitor](https://azure.microsoft.com/services/monitor/)stejné jako spravovaná brána [v cloudu](api-management-howto-use-azure-monitor.md). 

Funkci lze povolit nebo zakázat pomocí `telemetry.metrics.cloud` klíče v ConfigMap nasazení brány. Níže je uveden přehled dostupných konfigurací:

| Pole  | Výchozí | Description |
| ------------- | ------------- | ------------- |
| telemetrie. metriky. Cloud  | `true` | Povolí protokolování prostřednictvím Azure Monitor. Hodnota může být `true` , `false` . |


Tady je Ukázková konfigurace:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<contoso-gateway-management-endpoint>"
        telemetry.metrics.cloud: "true"
```

Brána pro samoobslužné hostování aktuálně generuje následující metriky prostřednictvím Azure Monitor:

| Metrika  | Popis |
| ------------- | ------------- |
| Žádosti  | Počet požadavků na rozhraní API v období |
| Doba trvání žádostí o bránu | Počet milisekund od chvíle, kdy brána přijala požadavek, do chvíle odeslání úplné odpovědi. |
| Doba trvání požadavků back-endu | Počet milisekund strávený na všech vstupně-výstupních operacích back-endu (připojování, odesílání a příjem bajtů).  |

## <a name="logs"></a>Protokoly

Samoobslužná brána aktuálně neodesílá [diagnostické protokoly](./api-management-howto-use-azure-monitor.md#activity-logs) do cloudu. Nicméně je možné [nakonfigurovat a uchovat protokoly místně](how-to-configure-local-metrics-logs.md) tam, kde je nasazená samoobslužná brána. 

Pokud je brána nasazená ve [službě Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/), můžete povolit [Azure monitor kontejnerů](../azure-monitor/insights/container-insights-overview.md) pro shromažďování protokolů z kontejnerů a jejich zobrazení v Log Analytics. 


## <a name="next-steps"></a>Další kroky

* Další informace o samoobslužné bráně najdete v tématu [Přehled služby Azure API Management v místním prostředí pro samoobslužné hostování](self-hosted-gateway-overview.md) .
* Další informace o [konfiguraci a uchování protokolů místně](how-to-configure-local-metrics-logs.md)

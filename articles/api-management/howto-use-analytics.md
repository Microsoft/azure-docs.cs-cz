---
title: Používání služby API Analytics v Azure API Management | Microsoft Docs
description: Analýza v Azure API Management vám pomůže pochopit a roztřídit využití vašich rozhraní API a výkonu rozhraní API.
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/24/2020
ms.author: apimpm
ms.openlocfilehash: ca7bd70bbf99a6d0079717a7a02328b11528d2e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96841401"
---
# <a name="get-api-analytics-in-azure-api-management"></a>Získání analýzy rozhraní API v Azure API Management

Azure API Management poskytuje integrované analýzy pro vaše rozhraní API. Analyzujte využití a výkon rozhraní API ve vaší instanci API Management napříč několika dimenzemi, včetně:

* Čas
* Geografie
* Rozhraní API
* Operace rozhraní API
* Produkty
* Předplatná
* Uživatelé
* Žádosti

:::image type="content" source="media/howto-use-analytics/analytics-report-portal.png" alt-text="Analýza Timeline na portálu":::

Využijte analýzy pro monitorování na vysoké úrovni a řešení potíží s rozhraními API. Další funkce monitorování, včetně metrik téměř v reálném čase a protokolů prostředků pro diagnostiku a auditování, najdete v tématu [kurz: monitorování publikovaných rozhraní API](api-management-howto-use-azure-monitor.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="analytics---portal"></a>Analytics – portál

Pomocí Azure Portal si můžete prohlédnout analytická data na první pohled vaší instance API Management.

1. V [Azure Portal](https://portal.azure.com)přejděte k instanci API Management. 
1. V nabídce na levé straně v části **monitorování** vyberte **Analytics**.

    :::image type="content" source="media/howto-use-analytics/monitoring-menu-analytics.png" alt-text="Výběr analýzy API Management instance na portálu":::  
1. Vyberte časový rozsah pro data nebo zadejte vlastní časový rozsah.
1. Vyberte kategorii sestav pro analytická data, jako je například **Časová osa**, **zeměpisná** a tak dále.
1. V případě potřeby můžete filtrovat sestavu podle jedné nebo více dalších kategorií.

## <a name="analytics---rest-api"></a>Analýza – REST API

Pomocí operací [sestav](/rest/api/apimanagement/2019-12-01/reports) v API Management REST API můžete načíst a filtrovat analytická data pro instanci API Management.

Dostupné operace vracejí záznamy sestav podle rozhraní API, geografie, operací s rozhraním API, produktem, žádostí, předplatným, časem nebo uživatelem.

## <a name="next-steps"></a>Další kroky

* Úvod k funkcím Azure Monitor v API Management najdete v tématu [kurz: monitorování publikovaných rozhraní API.](api-management-howto-use-azure-monitor.md)
* Podrobné protokolování a sledování HTTP najdete v tématu [monitorování vašich rozhraní API pomocí Azure API Management, Event Hubs a Moesif](api-management-log-to-eventhub-sample.md).
* Přečtěte si o integraci služby [azure API Management s využitím azure Application Insights](api-management-howto-app-insights.md).
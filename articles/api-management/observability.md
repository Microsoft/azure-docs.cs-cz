---
title: Pozorování v Azure API Management | Microsoft Docs
description: Přehled všech možností pozorování v Azure API Management.
services: api-management
documentationcenter: ''
author: begim
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2020
ms.author: apimpm
ms.openlocfilehash: 1ebebed465952bbb5d3e8f82ae1c7776c441c6b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87096739"
---
# <a name="observability-in-azure-api-management"></a>Pozorování v Azure API Management

Pozorování je schopnost pochopit vnitřní stav systému z dat, která vytváří, a schopnost prozkoumat tato data a zodpovědět otázky ohledně toho, co se stalo a proč. 

Azure API Management pomáhá organizacím centralizovat správu všech rozhraní API. Vzhledem k tomu, že slouží jako jediný bod vstupu všech přenosů rozhraní API, je ideálním místem pro sledování rozhraní API. 

## <a name="observability-tools"></a>Nástroje pro pozorování

Následující tabulka shrnuje všechny nástroje podporované nástrojem API Management ke sledování rozhraní API, které jsou užitečné pro jeden nebo více scénářů:

| Nástroj        | Užitečné pro    | Prodleva dat | Uchovávání | Vzorkování | Datový druh | Povoleno|
|:------------- |:-------------|:---- |:----|:---- |:--- |:---- 
| **[Kontrola rozhraní API](api-management-howto-api-inspector.md)** | Testování a ladění | Okamžitě | Poslední 100 trasování | Zapnuto na žádost | Trasování požadavků | Vždy
| **Integrované analýzy** | Sledování a vytváření sestav | V řádu minut | Doba platnosti | 100 % | Sestavy a protokoly | Vždy |
| **[Metriky Azure Monitoru](api-management-howto-use-azure-monitor.md)** | Sledování a vytváření sestav | V řádu minut | 93 dní (upgrade pro rozšíření) | 100 % | Metriky | Vždy |
| **[Protokoly služby Azure Monitor](api-management-howto-use-azure-monitor.md)** | Vytváření sestav, monitorování a ladění | V řádu minut | 31 dní/5 GB (upgrade pro rozšíření) | 100% (nastavitelný) | Protokoly | Volitelné |
| **[Application Insights Azure](api-management-howto-app-insights.md)** | Vytváření sestav, monitorování a ladění | Sekundy | 90 dní/5 GB (upgrade pro rozšíření) | Vlastní | Protokoly, metriky | Volitelné |
| **[Protokolování prostřednictvím centra událostí Azure](api-management-howto-log-event-hubs.md)** | Vlastní scénáře | Sekundy | Spravované uživatelem | Vlastní | Vlastní | Volitelné |

### <a name="self-hosted-gateway"></a>Brána pro samoobslužné hostování

Všechny výše uvedené nástroje jsou podporovány spravovanou bránou v cloudu. [Brána](self-hosted-gateway-overview.md) v místním prostředí aktuálně neodesílá diagnostické protokoly do Azure monitor. Nicméně je možné nakonfigurovat a uchovat protokoly místně tam, kde je nasazená samoobslužná brána. Další informace najdete v tématu [Konfigurace metrik a protokolů cloudu pro samoobslužnou bránu](how-to-configure-cloud-metrics-logs.md) a [konfiguraci místních metrik a protokolů pro samoobslužnou bránu](how-to-configure-local-metrics-logs.md).

## <a name="next-steps"></a>Další kroky

* [Pokud se chcete dozvědět víc o API Management, postupujte podle těchto kurzů.](import-and-publish.md)

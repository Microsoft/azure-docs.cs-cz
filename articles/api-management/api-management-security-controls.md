---
title: Řízení zabezpečení pro Azure API Management
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení API Management
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: 7f5fe404c93b7db22444b9dad97a0d3474c33a16
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257074"
---
# <a name="security-controls-for-api-management"></a>Ovládací prvky zabezpečení pro API Management

Tento článek popisuje ovládací prvky zabezpečení integrované do API Management.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Podpora koncového bodu služby| Ne | |  |
| Podpora vkládání virtuální sítě| Ano | |  |
| Izolace sítě a podpora brány firewall| Ano | Používání skupin zabezpečení sítě (NSG) a Azure Application Gateway (nebo jiného softwarového zařízení). |  |
| Podpora vynuceného tunelování| Ano | Sítě Azure poskytují vynucené tunelové propojení. |  |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | | |
| Protokolování a audit roviny řízení a správy| Ano | [Protokoly aktivit Azure Monitor](../azure-monitor/platform/activity-logs-overview.md) | |
| Protokolování a audit roviny dat| Ano | [Azure monitor diagnostické protokoly](../azure-monitor/platform/resource-logs-overview.md) a (volitelně) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  | |


## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Ověřování| Ano | |  |
| Authorization| Ano | |  |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Šifrování na straně serveru v klidovém umístění: Klíče spravované společností Microsoft | Ano | Citlivá data, jako jsou certifikáty, klíče a hodnoty tajného klíče, jsou šifrovány pomocí klíčů pro instanci služby spravované službou. |  |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ne | Všechny šifrovací klíče jsou na instanci služby a jsou spravované službou. |  |
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno | |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Připojení k [rychlé trase](../expressroute/index.yml) a síti VNet zajišťuje [síť Azure](../virtual-network/index.yml). |  |
| Zašifrovaná volání rozhraní API| Ano | Volání roviny správy se provádí prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml) přes TLS. Vyžaduje se platný webový token JSON (JWT).  Volání roviny dat je možné zabezpečit pomocí protokolu TLS a jednoho z podporovaných mechanismů ověřování (například klientský certifikát nebo token JWT). |   |
 |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Používání [sady Resource Kit pro Azure API Management DevOps](https://aka.ms/apimdevops) |  |

## <a name="vulnerability-scans-false-positives"></a>Ohrožení zabezpečení kontroluje falešně pozitivní výsledky.

Tato část se týká běžných chyb zabezpečení, které neovlivňují Azure API Management.

| Chyba zabezpečení               | Popis                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed je zranitelnost při implementaci rozšíření TLS SessionTicket, které najdete v některých produktech F5. Umožňuje únik ("vykrvení") až 31 bajtů dat z neinicializované paměti. To je způsobeno tím, že přeplňování zásobníku TLS předává ID relace předané z klienta s daty, aby byla 32 bitů dlouhá. |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
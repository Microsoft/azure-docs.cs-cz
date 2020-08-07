---
title: Řízení zabezpečení pro Azure API Management
description: Projděte si kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení API Management. Mezi ně patří ovládací prvky sítě, identita a ochrana dat.
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: a147179f7b55e43379b3c3fa3a7a0767cc97b198
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87902571"
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
| Protokolování a audit roviny řízení a správy| Ano | [Protokoly aktivit Azure Monitor](../azure-monitor/platform/platform-logs-overview.md) | |
| Protokolování a audit roviny dat| Ano | [Azure monitor diagnostické protokoly](../azure-monitor/platform/platform-logs-overview.md) a (volitelně) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  | |


## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Authentication| Ano | |  |
| Autorizace| Ano | |  |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Ano | Citlivá data, jako jsou certifikáty, klíče a hodnoty tajného klíče, jsou šifrovány pomocí klíčů pro instanci služby spravované službou. |  |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ne | Všechny šifrovací klíče jsou na instanci služby a jsou spravované službou. |  |
| Šifrování na úrovni sloupce (Azure Data Services)| – | |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Připojení k [rychlé trase](../expressroute/index.yml) a síti VNet zajišťuje [síť Azure](../virtual-network/index.yml). |  |
| Zašifrovaná volání rozhraní API| Ano | Volání roviny správy se provádí prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml) přes TLS. Vyžaduje se platný webový token JSON (JWT).  Volání roviny dat je možné zabezpečit pomocí protokolu TLS a jednoho z podporovaných mechanismů ověřování (například klientský certifikát nebo token JWT). |   |
 |

## <a name="configuration-management"></a>Správa konfigurací

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Používání [sady Resource Kit pro Azure API Management DevOps](https://aka.ms/apimdevops) |  |

## <a name="vulnerability-scans-false-positives"></a>Ohrožení zabezpečení kontroluje falešně pozitivní výsledky.

Tato část se týká běžných chyb zabezpečení, které neovlivňují Azure API Management.

| Ohrožení zabezpečení               | Popis                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed je zranitelnost při implementaci rozšíření TLS SessionTicket, které najdete v některých produktech F5. Umožňuje únik ("vykrvení") až 31 bajtů dat z neinicializované paměti. To je způsobeno tím, že přeplňování zásobníku TLS předává ID relace předané z klienta s daty, aby byla 32 bitů dlouhá. |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
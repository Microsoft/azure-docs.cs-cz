---
title: 'Azure ExpressRoute: řízení zabezpečení'
description: Seznamte se s ovládacími prvky zabezpečení ve službě Azure ExpressRoute, což jsou kvality nebo funkce, které umožňují zabránit, zjišťovat a reagovat na ohrožení zabezpečení.
services: expressroute
ms.service: expressroute
author: duongau
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: duau
ms.openlocfilehash: 24057de44f3d28df96bcb93e89af9c3afa6fa3c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89394903"
---
# <a name="security-controls-for-azure-expressroute"></a>Ovládací prvky zabezpečení pro Azure ExpressRoute

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Není k dispozici |  |
| Podpora vkládání virtuální sítě| Není k dispozici | |
| Izolace sítě a podpora brány firewall| Yes | Každý zákazník je obsažen ve své vlastní doméně směrování a je připojen k jeho vlastní virtuální síti. |
| Podpora vynuceného tunelování| Není k dispozici | Přes Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Yes | Viz [ExpressRoute monitoring, metriky a výstrahy](expressroute-monitoring-metrics-alerts.md).|
| Protokolování a audit roviny řízení a správy| Yes |  |
| Protokolování a audit roviny dat| No |   |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Yes | Účet služby pro bránu Microsoft (GWM) (Controller); Přístup JIT (just in time) pro vývoj a OP. |
| Autorizace|  Yes |Účet služby pro bránu Microsoft (GWM) (Controller); Přístup JIT (just in time) pro vývoj a OP. |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft |  Není k dispozici | ExpressRoute neukládá zákaznická data. |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Není k dispozici |  |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování VNet-VNet)| No | |
| Zašifrovaná volání rozhraní API| Yes | Prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml) a HTTPS. |


## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Yes | Přes poskytovatele síťových prostředků (NRP). |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
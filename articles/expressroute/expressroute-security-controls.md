---
title: 'Azure ExpressRoute: řízení zabezpečení'
description: Seznamte se s ovládacími prvky zabezpečení ve službě Azure ExpressRoute, což jsou kvality nebo funkce, které umožňují zabránit, zjišťovat a reagovat na ohrožení zabezpečení.
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 6a4589d1aa768548f2ae9ffa01f289c823c0b2e5
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192422"
---
# <a name="security-controls-for-azure-expressroute"></a>Ovládací prvky zabezpečení pro Azure ExpressRoute

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| – |  |
| Podpora vkládání virtuální sítě| – | |
| Izolace sítě a podpora brány firewall| Ano | Každý zákazník je obsažen ve své vlastní doméně směrování a je připojen k jeho vlastní virtuální síti. |
| Podpora vynuceného tunelování| – | Přes Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Viz [ExpressRoute monitoring, metriky a výstrahy](expressroute-monitoring-metrics-alerts.md).|
| Protokolování a audit roviny řízení a správy| Ano |  |
| Protokolování a audit roviny dat| Ne |   |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Účet služby pro bránu Microsoft (GWM) (Controller); Přístup JIT (just in time) pro vývoj a OP. |
| Autorizace|  Ano |Účet služby pro bránu Microsoft (GWM) (Controller); Přístup JIT (just in time) pro vývoj a OP. |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft |  – | ExpressRoute neukládá zákaznická data. |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | – |  |
| Šifrování na úrovni sloupce (Azure Data Services)| – | |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ne | |
| Zašifrovaná volání rozhraní API| Ano | Prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml) a HTTPS. |


## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Přes poskytovatele síťových prostředků (NRP). |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
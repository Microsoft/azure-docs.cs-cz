---
title: Ovládací prvky zabezpečení pro Azure ExpressRoute
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Azure ExpressRoute
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ab73ba6dd4b78d3cd0be5f4c7f7a502e5c58e08
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886773"
---
# <a name="security-controls-for-azure-expressroute"></a>Ovládací prvky zabezpečení pro Azure ExpressRoute

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Není k dispozici |  |
| Podpora vkládání virtuální sítě| Není k dispozici | |
| Izolace sítě a podpora brány firewall| Ano | Každý zákazník je obsažen ve své vlastní doméně směrování a je připojen k jeho vlastní virtuální síti. |
| Podpora vynuceného tunelování| Není k dispozici | Přes Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Viz [ExpressRoute monitoring, metriky a výstrahy](expressroute-monitoring-metrics-alerts.md).|
| Protokolování a audit roviny řízení a správy| Ano |  |
| Protokolování a audit roviny dat| Ne |   |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Účet služby pro bránu Microsoft (GWM) (Controller); Přístup JIT (just in time) pro vývoj a OP. |
| Authorization|  Ano |Účet služby pro bránu Microsoft (GWM) (Controller); Přístup JIT (just in time) pro vývoj a OP. |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: Klíče spravované společností Microsoft |  Není k dispozici | ExpressRoute neukládá zákaznická data. |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Není k dispozici |  |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ne | |
| Zašifrovaná volání rozhraní API| Ano | Prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml) a HTTPS. |


## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Přes poskytovatele síťových prostředků (NRP). |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
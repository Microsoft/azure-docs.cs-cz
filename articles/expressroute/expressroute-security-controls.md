---
title: 'Azure ExpressRoute: Ovládací prvky zabezpečení'
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Azure ExpressRoute
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74079935"
---
# <a name="security-controls-for-azure-expressroute"></a>Ovládací prvky zabezpečení pro Azure ExpressRoute

Tento článek dokumentuje ovládací prvky zabezpečení integrované do Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Síť)

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Není dostupné. |  |
| Podpora vstřikování virtuální sítě| Není dostupné. | |
| Podpora izolace sítě a brány firewall| Ano | Každý zákazník je obsažen ve své vlastní doméně směrování a promažený do vlastní virtuální sítě. |
| Podpora vynuceného tunelování| Není dostupné. | prostřednictvím protokolu hraniční brány (BGP). |

## <a name="monitoring--logging"></a>Sledování & protokolování

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (analýza protokolů, přehledy aplikací atd.)| Ano | Viz [Monitorování ExpressRoute, metriky a výstrahy](expressroute-monitoring-metrics-alerts.md).|
| Protokolování a audit roviny řízení a správy| Ano |  |
| Protokolování a audit roviny dat| Ne |   |

## <a name="identity"></a>Identita

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | účet služby pro bránu pro Microsoft (řadič); Právě v čase (JIT) přístup pro Dev a OP. |
| Autorizace|  Ano |účet služby pro bránu pro Microsoft (řadič); Právě v čase (JIT) přístup pro Dev a OP. |

## <a name="data-protection"></a>Ochrana dat

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém stavu: Klíče spravované společností Microsoft |  Není dostupné. | ExpressRoute neukládá zákaznická data. |
| Šifrování na straně serveru v klidovém stavu: klíče spravované zákazníkem (BYOK) | Není dostupné. |  |
| Šifrování na úrovni sloupců (Azure Data Services)| Není dostupné. | |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ne | |
| Zašifrovaná volání rozhraní API| Ano | Prostřednictvím [Správce prostředků Azure](../azure-resource-manager/index.yml) a HTTPS. |


## <a name="configuration-management"></a>Správa konfigurace

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (správa verzí konfigurace atd.)| Ano | Prostřednictvím poskytovatele síťových prostředků (NRP). |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných ovládacích prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
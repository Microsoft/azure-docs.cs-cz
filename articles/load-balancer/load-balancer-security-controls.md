---
title: Ovládací prvky zabezpečení pro Azure Load Balancer
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Load Balancer
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "74214898"
---
# <a name="security-controls-for-azure-load-balancer"></a>Ovládací prvky zabezpečení pro Azure Load Balancer

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Není k dispozici | |
| Podpora vkládání virtuální sítě| Není k dispozici | |
| Izolace sítě a podpora brány firewall| Není k dispozici |  |
| Podpora vynuceného tunelování| Není k dispozici | |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Yes | [Load Balancer veřejné základní](load-balancer-monitor-log.md)najdete v tématu protokoly Azure monitor. |
| Protokolování a audit roviny řízení a správy| Yes | [Load Balancer veřejné základní](load-balancer-monitor-log.md)najdete v tématu protokoly Azure monitor. |
| Protokolování a audit roviny dat | Není k dispozici |  |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Není k dispozici |  |
| Autorizace| Není k dispozici |  |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Není k dispozici | |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Není k dispozici | |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Není k dispozici | |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Yes | Prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Není k dispozici |  | 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
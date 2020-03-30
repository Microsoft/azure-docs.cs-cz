---
title: Ovládací prvky zabezpečení pro Azure Load Balancer
description: Kontrolní seznam bezpečnostních kontrol pro vyhodnocení vykladače zatížení
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74214898"
---
# <a name="security-controls-for-azure-load-balancer"></a>Ovládací prvky zabezpečení pro Azure Load Balancer

Tento článek dokumentuje ovládací prvky zabezpečení integrované do Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Síť)

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Není dostupné. | |
| Podpora vstřikování virtuální sítě| Není dostupné. | |
| Podpora izolace sítě a brány firewall| Není dostupné. |  |
| Podpora vynuceného tunelování| Není dostupné. | |

## <a name="monitoring--logging"></a>Sledování & protokolování

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (analýza protokolů, přehledy aplikací atd.)| Ano | Viz [protokoly Azure Monitor pro veřejné základní vyrovnávání zatížení](load-balancer-monitor-log.md). |
| Protokolování a audit roviny řízení a správy| Ano | Viz [protokoly Azure Monitor pro veřejné základní vyrovnávání zatížení](load-balancer-monitor-log.md). |
| Protokolování a audit roviny dat | Není dostupné. |  |

## <a name="identity"></a>Identita

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Není dostupné. |  |
| Autorizace| Není dostupné. |  |

## <a name="data-protection"></a>Ochrana dat

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém stavu: Klíče spravované společností Microsoft | Není dostupné. | |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování ve virtuální síti a šifrování virtuální sítě)| Není dostupné. | |
| Šifrování na straně serveru v klidovém stavu: klíče spravované zákazníkem (BYOK) | Není dostupné. | |
| Šifrování na úrovni sloupců (Azure Data Services)| Není dostupné. | |
| Zašifrovaná volání rozhraní API| Ano | Prostřednictvím [Správce prostředků Azure](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Správa konfigurace

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (správa verzí konfigurace atd.)| Není dostupné. |  | 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných ovládacích prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
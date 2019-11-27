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
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214898"
---
# <a name="security-controls-for-azure-load-balancer"></a>Ovládací prvky zabezpečení pro Azure Load Balancer

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Síť)

| Řízení zabezpečení | Ano/Ne | Poznámky: |
|---|---|--|
| Podpora koncového bodu služby| neuvedeno | |
| Podpora vkládání virtuální sítě| neuvedeno | |
| Izolace sítě a podpora brány firewall| neuvedeno |  |
| Podpora vynuceného tunelování| neuvedeno | |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky:|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | [Load Balancer veřejné základní](load-balancer-monitor-log.md)najdete v tématu protokoly Azure monitor. |
| Protokolování a audit roviny řízení a správy| Ano | [Load Balancer veřejné základní](load-balancer-monitor-log.md)najdete v tématu protokoly Azure monitor. |
| Protokolování a audit roviny dat | neuvedeno |  |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky:|
|---|---|--|
| Ověřování| neuvedeno |  |
| Autorizace| neuvedeno |  |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky: |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | neuvedeno | |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| neuvedeno | |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | neuvedeno | |
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno | |
| Zašifrovaná volání rozhraní API| Ano | Prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky:|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| neuvedeno |  | 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
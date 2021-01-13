---
title: Ovládací prvky zabezpečení pro Azure Relay
description: V tomto článku najdete seznam předdefinovaných ovládacích prvků zabezpečení pro vyhodnocení Azure Relay.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 5d55026bfb6e3d6fe955a540b7596a85707398d6
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133340"
---
# <a name="security-controls-for-azure-relay"></a>Ovládací prvky zabezpečení pro Azure Relay

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Podpora privátních koncových bodů| No |  |   |
| Izolace sítě a podpora brány firewall| No |  |   |
| Podpora vynuceného tunelování| Není k dispozici | Relay je tunelové propojení TLS  |   |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Yes | |   |
| Protokolování a audit roviny řízení a správy| Yes | Prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Protokolování a audit roviny dat| Yes | Úspěch nebo neúspěch připojení a chyby a protokolování  |   |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Ověřování| Yes | Přes SAS. | [Azure Relay ověřování a autorizace](relay-authentication-and-authorization.md) |
| Autorizace|  Yes | Přes SAS. | [Azure Relay ověřování a autorizace](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft |  Není k dispozici | Relay je webový soket a neuchovává data. |   |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | No | Používá pouze certifikáty Microsoft TLS.  |   |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |   |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování VNet-VNet)| Yes | Služba vyžaduje protokol TLS. |   |
| Zašifrovaná volání rozhraní API| Yes | HTTPS. |


## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Yes | Prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
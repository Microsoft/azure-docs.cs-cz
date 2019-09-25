---
title: Řízení zabezpečení pro Azure Service Bus Relay
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Azure Service Bus Relay
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 7f4e2a31673905a7e28d1dbb5520650aefc6f368
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219981"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Řízení zabezpečení pro Azure Service Bus Relay

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Service Bus Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Podpora koncového bodu služby| Ne |  |   |
| Izolace sítě a podpora brány firewall| Ne |  |   |
| Podpora vynuceného tunelování| neuvedeno | Relay je tunelové propojení TLS  |   |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | |   |
| Protokolování a audit roviny řízení a správy| Ano | Prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Protokolování a audit roviny dat| Ano | Úspěch nebo neúspěch připojení a chyby a protokolování  |   |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Ověřování| Ano | Via SAS. | [Azure Relay ověřování a autorizace](relay-authentication-and-authorization.md) |
| Authorization|  Ano | Via SAS. | [Azure Relay ověřování a autorizace](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Šifrování na straně serveru v klidovém umístění: Klíče spravované společností Microsoft |  neuvedeno | Relay je webový soket a neuchovává data. |   |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ne | Používá pouze certifikáty Microsoft TLS.  |   |
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno | |   |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Služba vyžaduje protokol TLS. |   |
| Zašifrovaná volání rozhraní API| Ano | HTTPS. |


## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
---
title: Ovládací prvky zabezpečení pro Azure Relay
description: V tomto článku najdete seznam předdefinovaných ovládacích prvků zabezpečení pro vyhodnocení Azure Relay.
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: f8165d994e998af4f15cd6aa2fd08b75191b8b64
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211458"
---
# <a name="security-controls-for-azure-relay"></a>Ovládací prvky zabezpečení pro Azure Relay

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Podpora koncového bodu služby| Ne |  |   |
| Izolace sítě a podpora brány firewall| Ne |  |   |
| Podpora vynuceného tunelování| – | Relay je tunelové propojení TLS  |   |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | |   |
| Protokolování a audit roviny řízení a správy| Ano | Prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Protokolování a audit roviny dat| Ano | Úspěch nebo neúspěch připojení a chyby a protokolování  |   |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Authentication| Ano | Přes SAS. | [Azure Relay ověřování a autorizace](relay-authentication-and-authorization.md) |
| Autorizace|  Ano | Přes SAS. | [Azure Relay ověřování a autorizace](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft |  – | Relay je webový soket a neuchovává data. |   |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ne | Používá pouze certifikáty Microsoft TLS.  |   |
| Šifrování na úrovni sloupce (Azure Data Services)| – | |   |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Služba vyžaduje protokol TLS. |   |
| Zašifrovaná volání rozhraní API| Ano | HTTPS. |


## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
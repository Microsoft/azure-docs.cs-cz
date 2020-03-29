---
title: Ovládací prvky zabezpečení pro přenos služby Azure Service Bus Relay
description: Tento článek obsahuje kontrolní seznam integrovaných ovládacích prvků zabezpečení pro vyhodnocení azure service bus relay.
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 28d3ba14aa7769ac4f3fc22bd2b5bd7acd30557c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514013"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Ovládací prvky zabezpečení pro přenos služby Azure Service Bus Relay

Tento článek dokumentuje ovládací prvky zabezpečení integrované do Azure Service Bus Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Síť)

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Podpora koncového bodu služby| Ne |  |   |
| Podpora izolace sítě a brány firewall| Ne |  |   |
| Podpora vynuceného tunelování| Není dostupné. | Relé je tunel TLS  |   |

## <a name="monitoring--logging"></a>Sledování & protokolování

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora monitorování Azure (analýza protokolů, přehledy aplikací atd.)| Ano | |   |
| Protokolování a audit roviny řízení a správy| Ano | Prostřednictvím [Správce prostředků Azure](../azure-resource-manager/index.yml). |   |
| Protokolování a audit roviny dat| Ano | Připojení úspěch / neúspěch a chyby a přihlášen.  |   |

## <a name="identity"></a>Identita

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Ověřování| Ano | Prostřednictvím SAS. | [Ověřování a autorizace Azure Relay](relay-authentication-and-authorization.md) |
| Autorizace|  Ano | Prostřednictvím SAS. | [Ověřování a autorizace Azure Relay](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Ochrana dat

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Šifrování na straně serveru v klidovém stavu: Klíče spravované společností Microsoft |  Není dostupné. | Relé je webový soket a neuchovává data. |   |
| Šifrování na straně serveru v klidovém stavu: klíče spravované zákazníkem (BYOK) | Ne | Používá pouze certifikáty TLS společnosti Microsoft.  |   |
| Šifrování na úrovni sloupců (Azure Data Services)| Není dostupné. | |   |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Služba vyžaduje TLS. |   |
| Zašifrovaná volání rozhraní API| Ano | HTTPS. |


## <a name="configuration-management"></a>Správa konfigurace

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora správy konfigurace (správa verzí konfigurace atd.)| Ano | Prostřednictvím [Správce prostředků Azure](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných ovládacích prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
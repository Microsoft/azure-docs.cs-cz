---
title: Řízení zabezpečení pro Azure Event Hubs
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Event Hubs Azure
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 277a745d26961ed509258d5423fc3c0da9b79a24
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219410"
---
# <a name="security-controls-for-azure-event-hubs"></a>Řízení zabezpečení pro Azure Event Hubs

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Event Hubs.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Podpora koncového bodu služby| Ano |  |  |
| Podpora vkládání virtuální sítě| Ne | |  |
| Izolace sítě a podpora brány firewall| Ano |  |  |
| Podpora vynuceného tunelování| Ne |  |  |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | |  |
| Protokolování a audit roviny řízení a správy| Ano |  |  |
| Protokolování a audit roviny dat| Ano |   |  |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Ověřování| Ano | | [Autorizace přístupu k Azure Event Hubs](authorize-access-event-hubs.md), [autorizace přístupu k prostředkům Event Hubs pomocí Azure Active Directory](authorize-access-azure-active-directory.md), [autorizace přístupu k prostředkům Event Hubs pomocí sdílených přístupových podpisů](authorize-access-shared-access-signature.md) |
| Authorization|  Ano | | [Ověřování spravované identity pomocí Azure Active Directory pro přístup k prostředkům Event Hubs](authenticate-managed-identity.md), [ověření aplikace pomocí Azure Active Directory pro přístup](authenticate-application.md)k prostředkům Event Hubs, [ověření přístupu k Event Hubs prostředkům pomocí sdílené přístupové podpisy (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Šifrování na straně serveru v klidovém umístění: Klíče spravované společností Microsoft |  Ano | |  |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ne |  |  |
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno | |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | |  |
| Zašifrovaná volání rozhraní API| Ano |  |  |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | |  |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).

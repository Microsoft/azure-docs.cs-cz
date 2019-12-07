---
title: Řízení zabezpečení pro zasílání zpráv Azure Service Bus
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Azure Service Bus zasílání zpráv
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: d0118f815a2ceb149c62363fa334c16d28c6d615
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894409"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Řízení zabezpečení pro zasílání zpráv Azure Service Bus

Tento článek popisuje ovládací prvky zabezpečení integrované v Azure Service Bus zasílání zpráv.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Podpora koncového bodu služby| Ano (jenom úroveň Premium) | Koncové body služby virtuální sítě se podporují jenom pro [Service Bus úrovně Premium](service-bus-premium-messaging.md) . |  |
| Podpora vkládání virtuální sítě| Ne | |  |
| Izolace sítě a podpora brány firewall| Ano (jenom úroveň Premium) |  |  |
| Podpora vynuceného tunelování| Ne |  |  |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Podporováno prostřednictvím [Azure monitor a výstrah](service-bus-metrics-azure-monitor.md). |  |
| Protokolování a audit roviny řízení a správy| Ano | K dispozici jsou protokoly operací.  | [Protokoly diagnostiky Service Bus](service-bus-diagnostic-logs.md) |
| Protokolování a audit roviny dat| Ne |  |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Ověření| Ano | Spravované prostřednictvím [Azure Active Directory identita spravované služby](service-bus-managed-service-identity.md)| [Service Bus ověřování a autorizaci](service-bus-authentication-and-authorization.md). |
| Autorizace| Ano | Podporuje autorizaci pomocí [RBAC](authenticate-application.md) a tokenu SAS. | [Service Bus ověřování a autorizaci](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft |  Ano, pokud je ve výchozím nastavení zapnuté šifrování na straně serveru. |  |  |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ano. Aktuálně ve verzi Preview | Klíč spravovaný zákazníkem ve službě Azure webrecovery se dá použít k šifrování dat v Service Busm oboru názvů v klidovém umístění. | [Konfigurace klíčů spravovaných zákazníkem pro šifrování Azure Service Bus dat v klidovém formátu pomocí Azure Portal (Preview)](configure-customer-managed-key.md)  |
| Šifrování na úrovni sloupce (Azure Data Services)| Nevztahuje se | |   |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Podporuje standardní mechanismus HTTPS/TLS. |   |
| Zašifrovaná volání rozhraní API| Ano | Volání rozhraní API se provádí prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml) a HTTPS. |   |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Podporuje správu verzí poskytovatele prostředků prostřednictvím [rozhraní Azure Resource Manager API](/rest/api/resources/).|   |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).

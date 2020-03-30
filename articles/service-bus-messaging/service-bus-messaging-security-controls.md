---
title: Ovládací prvky zabezpečení pro zasílání zpráv služby Azure Service Bus
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení zasílání zpráv služby Azure Service Bus
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: af119ef026b70fcb4a56b4f823d20c0e9eddddc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75903249"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Ovládací prvky zabezpečení pro zasílání zpráv služby Azure Service Bus

Tento článek dokumentuje ovládací prvky zabezpečení integrované do zasílání zpráv azure service bus.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Síť)

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Podpora koncového bodu služby| Ano (pouze úroveň Premium) | Koncové body služby Virtuální sítě jsou podporované jenom pro [úroveň Service Bus Premium.](service-bus-premium-messaging.md) |  |
| Podpora vstřikování virtuální sítě| Ne | |  |
| Podpora izolace sítě a brány firewall| Ano (pouze úroveň Premium) |  |  |
| Podpora vynuceného tunelování| Ne |  |  |

## <a name="monitoring--logging"></a>Sledování & protokolování

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora monitorování Azure (analýza protokolů, přehledy aplikací atd.)| Ano | Podporované prostřednictvím [Azure Monitor a výstrahy](service-bus-metrics-azure-monitor.md). |  |
| Protokolování a audit roviny řízení a správy| Ano | Provozní protokoly jsou k dispozici.  | [Diagnostické protokoly sběrnice service bus](service-bus-diagnostic-logs.md) |
| Protokolování a audit roviny dat| Ne |  |

## <a name="identity"></a>Identita

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Ověřování| Ano | Správa prostřednictvím [identity spravované služby Azure Active Directory](service-bus-managed-service-identity.md).| [Ověřování a autorizace služby Service Bus](service-bus-authentication-and-authorization.md). |
| Autorizace| Ano | Podporuje autorizaci prostřednictvím [tokenu RBAC](authenticate-application.md) a SAS. | [Ověřování a autorizace služby Service Bus](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Ochrana dat

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky | Dokumentace |
|---|---|--|--|
| Šifrování na straně serveru v klidovém stavu: Klíče spravované společností Microsoft |  Ano, pro šifrování na straně serveru ve výchozím nastavení. |  |  |
| Šifrování na straně serveru v klidovém stavu: klíče spravované zákazníkem (BYOK) | Ano. | Klíč spravovaný zákazníkem v Azure KeyVault lze použít k šifrování dat v oboru názvů service bus v klidovém stavu. | [Konfigurace klíčů spravovaných zákazníky pro šifrování dat Azure Service Bus v klidovém stavu pomocí portálu Azure](configure-customer-managed-key.md)  |
| Šifrování na úrovni sloupců (Azure Data Services)| Není dostupné. | |   |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Podporuje standardní mechanismus HTTPS/TLS. |   |
| Zašifrovaná volání rozhraní API| Ano | Volání rozhraní API se uskutečují prostřednictvím [Správce prostředků Azure](../azure-resource-manager/index.yml) a protokolu HTTPS. |   |

## <a name="configuration-management"></a>Správa konfigurace

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky| Dokumentace |
|---|---|--|--|
| Podpora správy konfigurace (správa verzí konfigurace atd.)| Ano | Podporuje správu verzí zprostředkovatele prostředků prostřednictvím [rozhraní API Azure Resource Manager](/rest/api/resources/).|   |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných ovládacích prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).

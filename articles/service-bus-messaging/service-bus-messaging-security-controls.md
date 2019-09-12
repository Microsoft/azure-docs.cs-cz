---
title: Řízení zabezpečení pro zasílání zpráv Azure Service Bus
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Azure Service Bus zasílání zpráv
services: service-bus-messaging
ms.service: service-bus-messaging
author: axisc
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: aschhab
ms.openlocfilehash: ac931ac7cf6c8609019b120388e1b6ca836f72b8
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886550"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Řízení zabezpečení pro zasílání zpráv Azure Service Bus

Tento článek popisuje ovládací prvky zabezpečení integrované v Azure Service Bus zasílání zpráv.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano (jenom úroveň Premium) | Koncové body služby virtuální sítě se podporují jenom pro [Service Bus úrovně Premium](service-bus-premium-messaging.md) . |
| Podpora vkládání virtuální sítě| Ne | |
| Izolace sítě a podpora brány firewall| Ano (jenom úroveň Premium) |  |
| Podpora vynuceného tunelování| Ne |  |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Podporováno prostřednictvím [Azure monitor a výstrah](service-bus-metrics-azure-monitor.md). |
| Protokolování a audit roviny řízení a správy| Ano | Jsou k dispozici protokoly operací. viz [protokoly diagnostiky Service Bus](service-bus-diagnostic-logs.md).  |
| Protokolování a audit roviny dat| Ne |  |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Spravované prostřednictvím [Azure Active Directory identita spravované služby](service-bus-managed-service-identity.md); viz [Service Bus ověřování a autorizace](service-bus-authentication-and-authorization.md).|
| Authorization| Ano | Podporuje autorizaci pomocí [RBAC](authenticate-application.md) a tokenu SAS; viz [Service Bus ověřování a autorizace](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: Klíče spravované společností Microsoft |  Ano, pokud je ve výchozím nastavení zapnuté šifrování na straně serveru. | Spravované klíče zákazníka a BYOK ještě nejsou podporované. Šifrování na straně klienta je zodpovědností klienta. |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ne |   |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Podporuje standardní mechanismus HTTPS/TLS. |
| Zašifrovaná volání rozhraní API| Ano | Volání rozhraní API se provádí prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml) a HTTPS. |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Podporuje správu verzí poskytovatele prostředků prostřednictvím [rozhraní Azure Resource Manager API](/rest/api/resources/).|

## <a name="next-steps"></a>Další postup

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
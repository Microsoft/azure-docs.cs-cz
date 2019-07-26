---
title: Atributy zabezpečení pro zasílání zpráv Azure Service Bus
description: Kontrolní seznam atributů zabezpečení pro vyhodnocení Azure Service Bus zasílání zpráv
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e1d6e041b47a261b549fb8b608cf09d0d6362dd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443900"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Atributy zabezpečení pro zasílání zpráv Azure Service Bus

Tento článek popisuje atributy zabezpečení integrované do Azure Service Bus zasílání zpráv.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování)|  Ano, pokud je ve výchozím nastavení zapnuté šifrování na straně serveru. | Spravované klíče zákazníka a BYOK ještě nejsou podporované. Šifrování na straně klienta je zodpovědností klienta. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Podporuje standardní mechanismus HTTPS/TLS. |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ne |   |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Ano | Volání rozhraní API se provádí prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml) a HTTPS. |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano (jenom úroveň Premium) | Koncové body služby virtuální sítě se podporují jenom pro [Service Bus úrovně Premium](service-bus-premium-messaging.md) . |
| Podpora vkládání virtuální sítě| Ne | |
| Izolace sítě a podpora brány firewall| Ano (jenom úroveň Premium) |  |
| Podpora vynuceného tunelování| Ne |  |

## <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Podporováno prostřednictvím [Azure monitor a výstrah](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Spravované prostřednictvím [Azure Active Directory identita spravované služby](service-bus-managed-service-identity.md); viz [Service Bus ověřování a autorizace](service-bus-authentication-and-authorization.md).|
| Authorization| Ano | Podporuje autorizaci pomocí [RBAC](service-bus-role-based-access-control.md) (Preview) a tokenu SAS; viz [Service Bus ověřování a autorizace](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | Jsou k dispozici protokoly operací. viz [protokoly diagnostiky Service Bus](service-bus-diagnostic-logs.md).  |
| Protokolování a audit roviny dat| Ne |  |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Podporuje správu verzí poskytovatele prostředků prostřednictvím [rozhraní Azure Resource Manager API](/rest/api/resources/).|

---
title: Běžné atributy zabezpečení pro Azure zasílání zpráv Service Bus
description: Kontrolní seznam společné atributy zabezpečení za vaše rozhodnutí vyzkoušet Azure zasílání zpráv Service Bus
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: d68ffe6561da6a23c288dfabd1d3eb6b34099bb3
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003109"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Atributy zabezpečení pro Azure zasílání zpráv Service Bus

Tento článek popisuje atributy zabezpečení integrované do Azure zasílání zpráv Service Bus.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>|  Ano pro serverové šifrování neaktivních ve výchozím nastavení. | Spravovaná zákazníkem klíče a BYOK se zatím nepodporují. Šifrování na straně klienta je odpovědností klienta |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | Podporuje standardní mechanismus HTTPS/TLS. |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ne |   |
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno | |
| Šifrované volání rozhraní API| Ano | Volání rozhraní API se provádějí přes [Azure Resource Manageru](../azure-resource-manager/index.yml) a HTTPS. |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano (pouze úroveň Premium) | Koncové body služby virtuální sítě jsou podporovány pro [úroveň služby Service Bus Premium](service-bus-premium-messaging.md) pouze. |
| Vkládání podpory virtuálních sítí| Ne | |
| Izolace sítě a podporu funkce brány firewall| Ano (pouze úroveň Premium) |  |
| Vynucené tunelování podpory| Ne |  |

## <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Podporováno prostřednictvím [Azure Monitor a výstrahy](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Spravovat prostřednictvím [Azure Active Directory identitu spravované služby](service-bus-managed-service-identity.md); viz [ověřování a autorizace Service Bus](service-bus-authentication-and-authorization.md).|
| Autorizace| Ano | Podporuje ověřování prostřednictvím [RBAC](service-bus-role-based-access-control.md) (Preview) a tokenu SAS, viz [ověřování a autorizace Service Bus](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano | Protokoly operací jsou k dispozici. Zobrazit [diagnostické protokoly služby Service Bus](service-bus-diagnostic-logs.md).  |
| Protokolování roviny dat a auditu| Ne |  |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano | Podporuje správu verzí zprostředkovatele prostředků prostřednictvím [API Azure Resource Manageru](/rest/api/resources/).|

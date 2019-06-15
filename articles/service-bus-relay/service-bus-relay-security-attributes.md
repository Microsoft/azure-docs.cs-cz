---
title: Běžné atributy zabezpečení pro Azure Service Bus Relay
description: Kontrolní seznam společné atributy zabezpečení za vaše rozhodnutí vyzkoušet Azure Service Bus Relay
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: d8ce3c995e8e0f20ed6d694f481cc5fc9fde4fa7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66000152"
---
# <a name="security-attributes-for-azure-service-bus-relay"></a>Atributy zabezpečení pro Azure Service Bus Relay

Tento článek popisuje atributy zabezpečení integrované do Azure Service Bus Relay.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>|  neuvedeno | Předávání přes webové sokety je a není zachována data. |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | Služba vyžaduje protokol TLS. |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ne | Používá pouze certifikáty Microsoft TLS.  |
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno | |
| Šifrované volání rozhraní API| Ano | HTTPS. |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne |  |
| Izolace sítě a podporu funkce brány firewall| Ne |  |
| Vynucené tunelování podpory| neuvedeno | Relay je tunelu TLS  |

## <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Via SAS. |
| Autorizace|  Ano | Via SAS. |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano | Prostřednictvím [Azure Resource Manageru](../azure-resource-manager/index.yml). |
| Protokolování roviny dat a auditu| Ano | Připojení úspěch nebo selhání a chyby a protokoluje.  |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano | Prostřednictvím [Azure Resource Manageru](../azure-resource-manager/index.yml).|

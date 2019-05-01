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
ms.openlocfilehash: f8827ac290393c9f394c3b13149555a1a2aa6df9
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927489"
---
# <a name="common-security-attributes-for-azure-service-bus-relay"></a>Běžné atributy zabezpečení pro Azure Service Bus Relay

Zabezpečení je integrované do všech oblastí služby Azure. Tento článek popisuje běžné atributy zabezpečení integrované do Azure Service Bus Relay.

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

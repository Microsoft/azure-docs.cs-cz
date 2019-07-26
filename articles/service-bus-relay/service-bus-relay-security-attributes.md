---
title: Atributy zabezpečení pro Azure Service Bus Relay
description: Kontrolní seznam atributů zabezpečení pro vyhodnocení Azure Service Bus Relay
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 2047f64914d4a286e6de38b7b2c8524d98eba562
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443883"
---
# <a name="security-attributes-for-azure-service-bus-relay"></a>Atributy zabezpečení pro Azure Service Bus Relay

Tento článek popisuje atributy zabezpečení integrované do Azure Service Bus Relay.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování)|  Není k dispozici | Relay je webový soket a neuchovává data. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Služba vyžaduje protokol TLS. |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ne | Používá pouze certifikáty Microsoft TLS.  |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Ano | HTTPS. |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne |  |
| Izolace sítě a podpora brány firewall| Ne |  |
| Podpora vynuceného tunelování| Není k dispozici | Relay je tunelové propojení TLS  |

## <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Via SAS. |
| Authorization|  Ano | Via SAS. |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | Prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml). |
| Protokolování a audit roviny dat| Ano | Úspěch nebo neúspěch připojení a chyby a protokolování  |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml).|

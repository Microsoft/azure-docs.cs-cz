---
title: Atributy zabezpečení pro Azure API Management
description: Kontrolní seznam atributů zabezpečení pro vyhodnocení API Management
services: api-management
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 2049e2349e3a25ebd4d3f4db19ec47bbaeb067de
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442280"
---
# <a name="security-attributes-for-api-management"></a>Atributy zabezpečení pro API Management

Tento článek popisuje atributy zabezpečení integrované do API Management.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování)| Ano (pouze šifrování na straně služby) | Citlivá data, jako jsou certifikáty, klíče a hodnoty tajného klíče, jsou šifrovány pomocí klíčů pro instanci služby spravované službou. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Připojení k [rychlé trase](../expressroute/index.yml) a síti VNet zajišťuje [síť Azure](../virtual-network/index.yml). |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ne | Všechny šifrovací klíče jsou na instanci služby a jsou spravované službou. |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Ano | Volání roviny správy se provádí prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml) přes TLS. Vyžaduje se platný webový token JSON (JWT).  Volání roviny dat je možné zabezpečit pomocí protokolu TLS a jednoho z podporovaných mechanismů ověřování (například klientský certifikát nebo token JWT).
 |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne | |
| Podpora vkládání virtuální sítě| Ano | |
| Izolace sítě a podpora brány firewall| Ano | Používání skupin zabezpečení sítě (NSG) a Azure Application Gateway (nebo jiného softwarového zařízení). |
| Podpora vynuceného tunelování| Ano | Sítě Azure poskytují vynucené tunelové propojení. |

## <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | |
| Authorization| Ano | |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | [Protokoly aktivit Azure Monitor](../azure-monitor/platform/activity-logs-overview.md) |
| Protokolování a audit roviny dat| Ano | [Azure monitor diagnostické protokoly](../azure-monitor/platform/diagnostic-logs-overview.md) a (volitelně) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Používání [sady Resource Kit pro Azure API Management DevOps](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>Ohrožení zabezpečení kontroluje falešně pozitivní výsledky.

Tato část se týká běžných chyb zabezpečení, které neovlivňují Azure API Management.

| Chyba zabezpečení               | Popis                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed je zranitelnost při implementaci rozšíření TLS SessionTicket, které najdete v některých produktech F5. Umožňuje únik ("vykrvení") až 31 bajtů dat z neinicializované paměti. To je způsobeno tím, že přeplňování zásobníku TLS předává ID relace předané z klienta s daty, aby byla 32 bitů dlouhá. |

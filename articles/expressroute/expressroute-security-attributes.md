---
title: Atributy zabezpečení pro Azure ExpressRoute
description: Kontrolní seznam atributů zabezpečení pro vyhodnocení Azure ExpressRoute
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: c9a46497c18b99ad7774036fd92e63d024b47045
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442202"
---
# <a name="security-attributes-for-azure-expressroute"></a>Atributy zabezpečení pro Azure ExpressRoute

Tento článek popisuje atributy zabezpečení integrované do Azure ExpressRoute.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování)|  Není k dispozici | ExpressRoute neukládá zákaznická data. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ne | |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Není k dispozici |  |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Ano | Prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml) a HTTPS. |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Není k dispozici |  |
| Podpora vkládání virtuální sítě| Není k dispozici | |
| Izolace sítě a podpora brány firewall| Ano | Každý zákazník je obsažen ve své vlastní doméně směrování a je připojen k jeho vlastní virtuální síti. |
| Podpora vynuceného tunelování| Není k dispozici | Přes Border Gateway Protocol (BGP). |

## <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Viz [ExpressRoute monitoring, metriky a výstrahy](expressroute-monitoring-metrics-alerts.md).|

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Účet služby pro bránu Microsoft (GWM) (Controller); Přístup JIT (just in time) pro vývoj a OP. |
| Authorization|  Ano |Účet služby pro bránu Microsoft (GWM) (Controller); Přístup JIT (just in time) pro vývoj a OP. |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky| 
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano |  |
| Protokolování a audit roviny dat| Ne |   |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Přes poskytovatele síťových prostředků (NRP). |

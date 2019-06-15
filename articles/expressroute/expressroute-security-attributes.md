---
title: Běžné atributy zabezpečení pro Azure ExpressRoute
description: Kontrolní seznam společné atributy zabezpečení za vaše rozhodnutí vyzkoušet Azure ExpressRoute
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: d6156715fb87831d465197fd8eec59d245221e48
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083272"
---
# <a name="common-security-attributes-for-azure-expressroute"></a>Běžné atributy zabezpečení pro Azure ExpressRoute

Zabezpečení je integrované do všech oblastí služby Azure. Tento článek popisuje běžné atributy zabezpečení integrované do Azure ExpressRoute.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>|  neuvedeno | ExpressRoute neukládá data zákazníků. |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ne | |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| neuvedeno |  |
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno | |
| Šifrované volání rozhraní API| Ano | Prostřednictvím [Azure Resource Manageru](../azure-resource-manager/index.yml) a HTTPS. |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| neuvedeno |  |
| vkládání podpory virtuálních sítí| neuvedeno | |
| Izolace sítě a podporu funkce brány firewall| Ano | Každý zákazník je součástí vlastní doménu směrování a tunelovým propojením do své vlastní virtuální sítě |
| Vynucené tunelování podpory| neuvedeno | Prostřednictvím Border Gateway Protocol (BGP). |

## <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Zobrazit [ExpressRoute monitorování, výstrahy a metriky](expressroute-monitoring-metrics-alerts.md).|

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Účet služby pro bránu pro Microsoft (GWM) (správce); Za běhu (JIT) přístup pro účely vývoje a OP. |
| Autorizace|  Ano |Účet služby pro bránu pro Microsoft (GWM) (správce); Za běhu (JIT) přístup pro účely vývoje a OP. |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky| 
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano |  |
| Protokolování roviny dat a auditu| Ne |   |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano | Prostřednictvím poskytovatele prostředků sítě (NRP). |

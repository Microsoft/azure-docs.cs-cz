---
title: Atributy zabezpečení pro Azure VPN Gateway
description: Kontrolní seznam atributů zabezpečení pro vyhodnocení VPN Gateway Azure
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: c355f70975f441609304a4c9ee2ead75f0e0ce25
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444585"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Atributy zabezpečení pro Azure VPN Gateway

Tento článek popisuje atributy zabezpečení integrované do Azure VPN Gateway.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Není k dispozici | Zákaznická data služby VPN Gateway neukládají zákaznická data |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Brána VPN Gateway zašifruje pakety zákazníků mezi bránami Azure VPN a místními zařízeními VPN (P2S) zákazníků. Brány VPN také podporují šifrování VNet-to-VNet. |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ne | Předem sdílené klíče zadané zákazníkem jsou zašifrované v klidovém stavu. ale zatím není integrovaná do CMK. |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Ano | Prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml) a https  |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Není k dispozici | |
| Podpora vkládání virtuální sítě| Není k dispozici | . |
| Izolace sítě a podpora brány firewall| Ano | Brány VPN jsou vyhrazené instance virtuálních počítačů pro jednotlivé zákazníky Virtual Network  |
| Podpora vynuceného tunelování| Ano |  |

## <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Viz [Azure monitor diagnostické protokoly/výstrahy](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure monitor metriky/výstrahy](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) pro správu služby a konfiguraci služby Azure VPN Gateway. |
| Authorization| Ano | Umožňuje autorizaci pomocí [RBAC](../role-based-access-control/overview.md). |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | Azure Resource Manager protokolu aktivit. |
| Protokolování a audit roviny dat | Ano | [Protokoly diagnostiky Azure monitor](../azure-resource-manager/resource-group-audit.md) pro protokolování a AUDITOVÁNÍ připojení VPN. |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | V případě operací správy se stav konfigurace služby Azure VPN Gateway dá exportovat jako šablona Azure Resource Manager a v průběhu času se pořídí verze. | 
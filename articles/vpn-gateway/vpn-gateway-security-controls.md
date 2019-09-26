---
title: Řízení zabezpečení pro Azure VPN Gateway
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení VPN Gateway Azure
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 1babb892063da6d460ea2bc4c567da954731956f
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886488"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Řízení zabezpečení pro Azure VPN Gateway

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure VPN Gateway.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Není k dispozici | |
| Podpora vkládání virtuální sítě| Není k dispozici | |
| Izolace sítě a podpora brány firewall| Ano | Brány VPN jsou vyhrazené instance virtuálních počítačů pro jednotlivé zákazníky Virtual Network  |
| Podpora vynuceného tunelování| Ano |  |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Viz [Azure monitor diagnostické protokoly/výstrahy](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure monitor metriky/výstrahy](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Protokolování a audit roviny řízení a správy| Ano | Azure Resource Manager protokolu aktivit. |
| Protokolování a audit roviny dat | Ano | [Protokoly diagnostiky Azure monitor](../azure-resource-manager/resource-group-audit.md) pro protokolování a AUDITOVÁNÍ připojení VPN. |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) pro správu služby a konfiguraci služby Azure VPN Gateway. |
| Authorization| Ano | Umožňuje autorizaci pomocí [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: Klíče spravované společností Microsoft | Není k dispozici | Zákaznická data služby VPN Gateway neukládají zákaznická data |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Brána VPN Gateway zašifruje pakety zákazníků mezi bránami Azure VPN a místními zařízeními VPN (P2S) zákazníků. Brány VPN také podporují šifrování VNet-to-VNet. |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ne | Předem sdílené klíče zadané zákazníkem jsou zašifrované v klidovém stavu. ale zatím není integrovaná do CMK. |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Ano | Prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml) a https  |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | V případě operací správy se stav konfigurace služby Azure VPN Gateway dá exportovat jako šablona Azure Resource Manager a v průběhu času se pořídí verze. | 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
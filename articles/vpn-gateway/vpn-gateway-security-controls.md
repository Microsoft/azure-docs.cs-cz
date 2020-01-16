---
title: Řízení zabezpečení pro Azure VPN Gateway
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení VPN Gateway Azure
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: cdf616b29a93e786ef26af83b5d3b3541f94d67c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75972275"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Řízení zabezpečení pro Azure VPN Gateway

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure VPN Gateway.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Síť)

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Nevztahuje se | |
| Podpora vkládání virtuální sítě| Nevztahuje se | |
| Izolace sítě a podpora brány firewall| Ano | Brány VPN jsou vyhrazené instance virtuálních počítačů pro jednotlivé zákazníky Virtual Network  |
| Podpora vynuceného tunelování| Ano |  |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Viz [Azure monitor diagnostické protokoly/výstrahy](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure monitor metriky/výstrahy](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Protokolování a audit roviny řízení a správy| Ano | Azure Resource Manager protokolu aktivit. |
| Protokolování a audit roviny dat | Ano | [Protokoly diagnostiky Azure monitor](../azure-resource-manager/management/view-activity-logs.md) pro protokolování a AUDITOVÁNÍ připojení VPN. |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověření| Ano | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) pro správu služby a konfiguraci služby Azure VPN Gateway. |
| Autorizace| Ano | Umožňuje autorizaci pomocí [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Nevztahuje se | Zákaznická data služby VPN Gateway neukládají zákaznická data |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Brána VPN Gateway zašifruje pakety zákazníků mezi bránami Azure VPN a místními zařízeními VPN (P2S) zákazníků. Brány VPN také podporují šifrování VNet-to-VNet. |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ne | Předem sdílené klíče zadané zákazníkem jsou zašifrované v klidovém stavu. ale zatím není integrovaná do CMK. |
| Šifrování na úrovni sloupce (Azure Data Services)| Nevztahuje se | |
| Zašifrovaná volání rozhraní API| Ano | Prostřednictvím [Azure Resource Manager](../azure-resource-manager/index.yml) a https  |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | V případě operací správy se stav konfigurace služby Azure VPN Gateway dá exportovat jako šablona Azure Resource Manager a v průběhu času se pořídí verze. |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).

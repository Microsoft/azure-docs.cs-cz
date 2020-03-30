---
title: Ovládací prvky zabezpečení pro bránu Azure VPN Gateway
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení brány Azure VPN Gateway
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: cdf616b29a93e786ef26af83b5d3b3541f94d67c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972275"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Ovládací prvky zabezpečení pro bránu Azure VPN Gateway

Tento článek dokumentuje ovládací prvky zabezpečení integrované do brány Azure VPN Gateway.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Síť)

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Není dostupné. | |
| Podpora vstřikování virtuální sítě| Není dostupné. | |
| Podpora izolace sítě a brány firewall| Ano | Brány VPN jsou vyhrazené instance virtuálních počítačen pro každého zákazníka virtuální sítě  |
| Podpora vynuceného tunelování| Ano |  |

## <a name="monitoring--logging"></a>Sledování & protokolování

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (analýza protokolů, přehledy aplikací atd.)| Ano | Viz [Protokoly diagnostiky/výstrahy](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor metriky/výstrahy.](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)  |
| Protokolování a audit roviny řízení a správy| Ano | Protokol aktivit Správce prostředků Azure. |
| Protokolování a audit roviny dat | Ano | [Diagnostické protokoly monitorování Azure](../azure-resource-manager/management/view-activity-logs.md) pro protokolování připojení k síti VPN a auditování. |

## <a name="identity"></a>Identita

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) pro správu služby a konfiguraci brány Azure VPN. |
| Autorizace| Ano | Autorizace podpory prostřednictvím [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Ochrana dat

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém stavu: Klíče spravované společností Microsoft | Není dostupné. | Vpn brána tranzitu zákaznická data, neukládá zákaznická data |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování ve virtuální síti a šifrování virtuální sítě)| Ano | Brána VPN šifruje klientské pakety mezi branami Azure VPN a místními zařízeními VPN (S2S) nebo klienty VPN (P2S) zákazníky. Brány VPN také podporují šifrování virtuální sítě na virtuální síť. |
| Šifrování na straně serveru v klidovém stavu: klíče spravované zákazníkem (BYOK) | Ne | Předsdílené klíče zadané zákazníkem jsou v klidovém stavu zašifrovány. ale ještě není integrován s CMK. |
| Šifrování na úrovni sloupců (Azure Data Services)| Není dostupné. | |
| Zašifrovaná volání rozhraní API| Ano | Prostřednictvím [Správce prostředků Azure](../azure-resource-manager/index.yml) a HTTPS  |

## <a name="configuration-management"></a>Správa konfigurace

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (správa verzí konfigurace atd.)| Ano | Pro operace správy lze stav konfigurace brány Azure VPN exportovat jako šablonu Azure Resource Manager u verze v průběhu času. |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných ovládacích prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).

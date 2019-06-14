---
title: Atributy zabezpečení pro Azure VPN Gateway
description: Kontrolní seznam zabezpečení atributy za vaše rozhodnutí vyzkoušet Azure VPN Gateway
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e58e7b3f77d9bb673e300aa60ad07ca9dba5153
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083129"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Atributy zabezpečení pro Azure VPN Gateway

Tento článek popisuje běžné atributy zabezpečení integrované do Azure VPN Gateway.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | neuvedeno | Zákaznická data přenosu brány sítě VPN, neukládá data zákazníků |
| Šifrování během přenosu (například šifrování ExpressRoute ve virtuální síti a šifrováním virtuálními sítěmi)| Ano | Brána VPN šifrování zákazníka paketů mezi bránami Azure VPN Gateway a zákazníka místní zařízení VPN (S2S) nebo klienti VPN (P2S). Brány VPN se také podporují šifrování připojení typu VNet-to-VNet. |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ne | Zákazníkem zadaný předsdílené klíče se šifrují při nečinnosti; ale není součástí CMK ještě. |
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno | |
| Šifrované volání rozhraní API| Ano | Prostřednictvím [Azure Resource Manageru](../azure-resource-manager/index.yml) a HTTPS  |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| neuvedeno | |
| Vkládání podpory virtuálních sítí| neuvedeno | . |
| Izolace sítě a Firewalling podpory| Ano | VPN Gateway je vyhrazené instance virtuálních počítačů pro každého zákazníka virtuální sítě  |
| Vynucené tunelování podpory| Ano |  |

## <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Zobrazit [protokoly diagnostiky Azure Monitor nebo výstrahy](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor/upozornění metrik](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) pro správu služby a konfiguraci brány Azure VPN. |
| Autorizace| Ano | Podporu ověřování přes [RBAC](../role-based-access-control/overview.md). |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano | Protokol aktivit Azure Resource Manageru. |
| Protokolování roviny dat a auditu | Ano | [Diagnostické protokoly Azure monitoru](../azure-resource-manager/resource-group-audit.md) pro připojení k síti VPN, protokolování a auditování. |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano | Pro operace správy stavu konfiguraci služby Azure VPN gateway lze exportovat jako šablonu Azure Resource Manageru a systémovou správou verzí v čase. | 
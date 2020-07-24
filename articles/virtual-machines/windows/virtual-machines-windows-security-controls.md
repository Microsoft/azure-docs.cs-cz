---
title: Řízení zabezpečení pro Azure Windows Virtual Machines
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Windows Virtual Machines Azure
ms.service: virtual-machines
ms.subservice: security
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 6ab6133faef4a6c7a8eb929e5f4cd1840e971a99
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088338"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Ovládací prvky zabezpečení pro Windows Virtual Machines

Tento článek popisuje ovládací prvky zabezpečení integrované do Windows Virtual Machines.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Yes | |
| Podpora vkládání virtuální sítě| Yes | |
| Izolace sítě a podpora brány firewall| Yes |  |
| Podpora vynuceného tunelování| Yes | Další informace najdete v tématu [Konfigurace vynuceného tunelování pomocí modelu nasazení Azure Resource Manager](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Yes | [Monitorujte a aktualizujte virtuální počítač s Windows v Azure](./tutorial-monitor.md). |
| Protokolování a audit roviny řízení a správy| Yes |  |
| Protokolování a audit roviny dat | No |  |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Yes |  |
| Autorizace| Yes |  |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Yes | Viz [šifrování virtuálních disků na virtuálním počítači s Windows](./disk-encryption-overview.md). |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Yes | Azure Virtual Machines podporuje šifrování [ExpressRoute](../../expressroute/index.yml) a virtuální sítě. Viz [šifrování v přenosech na virtuálních počítačích](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms). |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Yes | Klíče spravované zákazníkem jsou podporovaným scénářem šifrování Azure; viz [Přehled šifrování Azure](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms).|
| Šifrování na úrovni sloupce (Azure Data Services)| – | |
| Zašifrovaná volání rozhraní API| Yes | Přes HTTPS a TLS. |



## <a name="configuration-management"></a>Správa konfigurací

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano |  | 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../../security/fundamentals/security-controls.md).

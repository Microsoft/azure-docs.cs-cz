---
title: Řízení zabezpečení pro Azure Linux Virtual Machines – Linux
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Linux Virtual Machines Azure
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 132f696dd1298384c302eeadc264e857d1edff96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87080059"
---
# <a name="security-controls-for-linux-virtual-machines"></a>Ovládací prvky zabezpečení pro Linux Virtual Machines

Tento článek popisuje ovládací prvky zabezpečení integrované do Linux Virtual Machines.

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
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Yes | Viz [monitorování a aktualizace virtuálního počítače se systémem Linux v Azure](./tutorial-monitor.md). |
| Protokolování a audit roviny řízení a správy| Yes |  |
| Protokolování a audit roviny dat | No |  |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Yes |  |
| Autorizace| Yes |  |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Yes | Viz [Azure Disk Encryption pro virtuální počítače se systémem Linux](disk-encryption-overview.md). |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování VNet-VNet)| Yes | Azure Virtual Machines podporuje šifrování [ExpressRoute](../../expressroute/index.yml) a virtuální sítě. Viz [šifrování v přenosech na virtuálních počítačích](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms). |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Yes | Klíče spravované zákazníkem jsou podporovaným scénářem šifrování Azure; viz [Přehled šifrování Azure](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms).|
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Yes | Přes HTTPS a TLS. |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano |  | 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../../security/fundamentals/security-controls.md).

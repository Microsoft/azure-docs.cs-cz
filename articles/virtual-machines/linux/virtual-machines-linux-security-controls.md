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
ms.openlocfilehash: 300acaf4a9d2a11ef107e19df99452c909257d54
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77190577"
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
| Podpora vynuceného tunelování| Yes | Další informace najdete v tématu [Konfigurace vynuceného tunelování pomocí modelu nasazení Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Yes | Viz [monitorování a aktualizace virtuálního počítače se systémem Linux v Azure](/azure/virtual-machines/linux/tutorial-monitoring). |
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
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Yes | Azure Virtual Machines podporuje šifrování [ExpressRoute](/azure/expressroute) a virtuální sítě. Viz [šifrování v přenosech na virtuálních počítačích](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Yes | Klíče spravované zákazníkem jsou podporovaným scénářem šifrování Azure; viz [Přehled šifrování Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Yes | Přes HTTPS a TLS. |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano |  | 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../../security/fundamentals/security-controls.md).

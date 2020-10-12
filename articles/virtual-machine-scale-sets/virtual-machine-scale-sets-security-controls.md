---
title: Řízení zabezpečení pro Azure Virtual Machine Scale Sets
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Virtual Machine Scale Sets Azure
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: security
ms.date: 09/05/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 4480ad425d9a3953fd5779f99d27b5b6b037e61e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87029412"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Řízení zabezpečení pro Azure Virtual Machine Scale Sets

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Virtual Machine Scale Sets.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Yes | |
| Podpora vkládání virtuální sítě| Yes | |
| Izolace sítě a podpora brány firewall| Yes |  |
| Podpora vynuceného tunelování| Yes | Další informace najdete v tématu [Konfigurace vynuceného tunelování pomocí modelu nasazení Azure Resource Manager](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Yes | Podívejte se na téma [monitorování a aktualizace virtuálního počítače se systémem Linux v Azure](../virtual-machines/linux/tutorial-monitor.md) a [monitorování a aktualizace virtuálního počítače s Windows v Azure](../virtual-machines/windows/tutorial-monitor.md). |
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
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Yes | Virtual Machine Scale Sets najdete v tématu [Azure Disk Encryption](disk-encryption-overview.md). |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování VNet-VNet)| Yes | Azure Virtual Machines podporuje šifrování [ExpressRoute](../expressroute/index.yml) a virtuální sítě. Viz [šifrování v přenosech na virtuálních počítačích](../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms). |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Yes | Klíče spravované zákazníkem jsou podporovaným scénářem šifrování Azure; Viz téma [Azure Disk Encryption Virtual Machine Scale Sets](disk-encryption-overview.md)|
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Yes | Přes HTTPS a TLS. |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano |  | 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).

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
ms.openlocfilehash: 030e2c23d68a3fbbc96dd7591583cb27b650d011
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83200017"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Řízení zabezpečení pro Azure Virtual Machine Scale Sets

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Virtual Machine Scale Sets.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | |
| Podpora vkládání virtuální sítě| Ano | |
| Izolace sítě a podpora brány firewall| Ano |  |
| Podpora vynuceného tunelování| Ano | Další informace najdete v tématu [Konfigurace vynuceného tunelování pomocí modelu nasazení Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Podívejte se na téma [monitorování a aktualizace virtuálního počítače se systémem Linux v Azure](/azure/virtual-machines/linux/tutorial-monitoring) a [monitorování a aktualizace virtuálního počítače s Windows v Azure](/azure/virtual-machines/windows/tutorial-monitoring). |
| Protokolování a audit roviny řízení a správy| Ano |  |
| Protokolování a audit roviny dat | Ne |  |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano |  |
| Autorizace| Ano |  |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Ano | Virtual Machine Scale Sets najdete v tématu [Azure Disk Encryption](disk-encryption-overview.md). |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Azure Virtual Machines podporuje šifrování [ExpressRoute](/azure/expressroute) a virtuální sítě. Viz [šifrování v přenosech na virtuálních počítačích](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ano | Klíče spravované zákazníkem jsou podporovaným scénářem šifrování Azure; Viz téma [Azure Disk Encryption Virtual Machine Scale Sets](disk-encryption-overview.md)|
| Šifrování na úrovni sloupce (Azure Data Services)| – | |
| Zašifrovaná volání rozhraní API| Ano | Přes HTTPS a TLS. |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano |  | 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).

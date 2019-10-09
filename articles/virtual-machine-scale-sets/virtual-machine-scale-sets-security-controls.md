---
title: Řízení zabezpečení pro Azure Virtual Machine Scale Sets
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení Virtual Machine Scale Sets Azure
services: virtual-machine-scale-sets
ms.service: virtual-machine-scale-sets
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 7d7e69e8ad0c5b14ac7ed8b941a7949f4f675812
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176776"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Řízení zabezpečení pro Azure Virtual Machine Scale Sets

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure Virtual Machine Scale Sets.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sítě

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
| Ověřování| Ano |  |
| Autorizace| Ano |  |

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Ano | Podívejte [se, jak zašifrovat virtuální počítač se systémem Linux v Azure](/azure/virtual-machines/linux/disk-encryption-linux) a [Šifrovat virtuální disky na](/azure/virtual-machines/windows/encrypt-disks)virtuálním počítači s Windows. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Azure Virtual Machines podporuje šifrování [ExpressRoute](/azure/expressroute) a virtuální sítě. Viz [šifrování v přenosech na virtuálních počítačích](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ano | Klíče spravované zákazníkem jsou podporovaným scénářem šifrování Azure; viz [Přehled šifrování Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Ano | Přes HTTPS a SSL. |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano |  | 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
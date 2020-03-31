---
title: Ovládací prvky zabezpečení pro škálovací sady virtuálních strojů Azure
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení škálovacích sad virtuálních strojů Azure
ms.service: virtual-machine-scale-sets
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 4007f4adeee065fe32492d3bd16f3a06d24e7d96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190610"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Ovládací prvky zabezpečení pro škálovací sady virtuálních strojů Azure

Tento článek dokumentuje ovládací prvky zabezpečení integrované do škálovacích sad virtuálních strojů Azure.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Síť)

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | |
| Podpora vstřikování virtuální sítě| Ano | |
| Podpora izolace sítě a brány firewall| Ano |  |
| Podpora vynuceného tunelování| Ano | Viz [Konfigurace vynuceného tunelového propojení pomocí modelu nasazení Azure Resource Manageru](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Sledování & protokolování

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (analýza protokolů, přehledy aplikací atd.)| Ano | Viz [Sledování a aktualizace virtuálního počítače SIP v Azure](/azure/virtual-machines/linux/tutorial-monitoring) a Monitorování a aktualizace [virtuálního počítače s Windows v Azure](/azure/virtual-machines/windows/tutorial-monitoring). |
| Protokolování a audit roviny řízení a správy| Ano |  |
| Protokolování a audit roviny dat | Ne |  |

## <a name="identity"></a>Identita

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano |  |
| Autorizace| Ano |  |

## <a name="data-protection"></a>Ochrana dat

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém stavu: Klíče spravované společností Microsoft | Ano | Viz [Azure Disk Encryption for Virtual Machine Scale Sets](disk-encryption-overview.md). |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování ve virtuální síti a šifrování virtuální sítě)| Ano | Virtuální počítače Azure podporují šifrování [ExpressRoute](/azure/expressroute) a Virtuální sítě. Viz [Šifrování na cestě ve virtuálních discích](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Šifrování na straně serveru v klidovém stavu: klíče spravované zákazníkem (BYOK) | Ano | Klíče spravované zákazníkem je podporovaný scénář šifrování Azure; Viz [Šifrování disku Azure pro škálovací sady virtuálních počítačů](disk-encryption-overview.md)|
| Šifrování na úrovni sloupců (Azure Data Services)| Není dostupné. | |
| Zašifrovaná volání rozhraní API| Ano | Prostřednictvím protokolů HTTPS a TLS. |

## <a name="configuration-management"></a>Správa konfigurace

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (správa verzí konfigurace atd.)| Ano |  | 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných ovládacích prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).

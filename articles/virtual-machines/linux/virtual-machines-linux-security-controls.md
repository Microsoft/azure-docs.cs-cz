---
title: Ovládací prvky zabezpečení pro virtuální počítače Azure Linux – Linux
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení virtuálních počítačů Azure Linux
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 300acaf4a9d2a11ef107e19df99452c909257d54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190577"
---
# <a name="security-controls-for-linux-virtual-machines"></a>Ovládací prvky zabezpečení pro virtuální počítače s Linuxem

Tento článek dokumentuje ovládací prvky zabezpečení integrované do virtuálních počítačů Linux.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

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
| Podpora monitorování Azure (analýza protokolů, přehledy aplikací atd.)| Ano | Viz [Sledování a aktualizace virtuálního počítače SIP v Azure](/azure/virtual-machines/linux/tutorial-monitoring). |
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
| Šifrování na straně serveru v klidovém stavu: Klíče spravované společností Microsoft | Ano | Viz [Azure Disk Encryption for Linux VMs](disk-encryption-overview.md). |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování ve virtuální síti a šifrování virtuální sítě)| Ano | Virtuální počítače Azure podporují šifrování [ExpressRoute](/azure/expressroute) a Virtuální sítě. Viz [Šifrování na cestě ve virtuálních discích](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Šifrování na straně serveru v klidovém stavu: klíče spravované zákazníkem (BYOK) | Ano | Klíče spravované zákazníkem je podporovaný scénář šifrování Azure; viz [Přehled šifrování Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Šifrování na úrovni sloupců (Azure Data Services)| Není dostupné. | |
| Zašifrovaná volání rozhraní API| Ano | Prostřednictvím protokolů HTTPS a TLS. |

## <a name="configuration-management"></a>Správa konfigurace

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (správa verzí konfigurace atd.)| Ano |  | 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných ovládacích prvcích zabezpečení napříč službami Azure](../../security/fundamentals/security-controls.md).

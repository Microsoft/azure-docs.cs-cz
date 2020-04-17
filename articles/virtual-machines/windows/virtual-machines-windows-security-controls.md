---
title: Ovládací prvky zabezpečení pro virtuální počítače Azure Windows
description: Kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení virtuálních počítačů Azure Windows
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 569c989e8876bb7213d1469c7a70da99aa0b1e9c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455338"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Ovládací prvky zabezpečení pro virtuální počítače s Windows

Tento článek dokumentuje ovládací prvky zabezpečení integrované do virtuálních počítačů Windows.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | |
| Podpora vstřikování virtuální sítě| Ano | |
| Podpora izolace sítě a brány firewall| Ano |  |
| Podpora vynuceného tunelování| Ano | Viz [Konfigurace vynuceného tunelového propojení pomocí modelu nasazení Azure Resource Manageru](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Sledování & protokolování

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (analýza protokolů, přehledy aplikací atd.)| Ano | [Monitorování a aktualizace virtuálního počítače s Windows v Azure](tutorial-monitoring.md). |
| Protokolování a audit roviny řízení a správy| Ano |  |
| Protokolování a audit roviny dat | Ne |  |

## <a name="identity"></a>Identita

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano |  |
| Autorizace| Ano |  |

## <a name="data-protection"></a>Ochrana dat

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování na straně serveru v klidovém stavu: Klíče spravované společností Microsoft | Ano | Viz [Šifrování virtuálních disků na virtuálním počítači s Windows](/azure/virtual-machines/windows/disk-encryption-overview). |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování ve virtuální síti a šifrování virtuální sítě)| Ano | Virtuální počítače Azure podporují šifrování [ExpressRoute](/azure/expressroute) a Virtuální sítě. Viz [Šifrování na cestě ve virtuálních discích](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Šifrování na straně serveru v klidovém stavu: klíče spravované zákazníkem (BYOK) | Ano | Klíče spravované zákazníkem je podporovaný scénář šifrování Azure; viz [Přehled šifrování Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Šifrování na úrovni sloupců (Azure Data Services)| – | |
| Zašifrovaná volání rozhraní API| Ano | Prostřednictvím protokolů HTTPS a TLS. |



## <a name="configuration-management"></a>Správa konfigurace

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (správa verzí konfigurace atd.)| Ano |  | 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných ovládacích prvcích zabezpečení napříč službami Azure](../../security/fundamentals/security-controls.md).

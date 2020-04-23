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
ms.openlocfilehash: ac1ed9ac25d65d0391175fc6d43b48048da74926
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101582"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Ovládací prvky zabezpečení pro Windows Virtual Machines

Tento článek popisuje ovládací prvky zabezpečení integrované do Windows Virtual Machines.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

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
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | [Monitorujte a aktualizujte virtuální počítač s Windows v Azure](tutorial-monitoring.md). |
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
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Ano | Viz [šifrování virtuálních disků na virtuálním počítači s Windows](/azure/virtual-machines/windows/disk-encryption-overview). |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Azure Virtual Machines podporuje šifrování [ExpressRoute](/azure/expressroute) a virtuální sítě. Viz [šifrování v přenosech na virtuálních počítačích](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Ano | Klíče spravované zákazníkem jsou podporovaným scénářem šifrování Azure; viz [Přehled šifrování Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Šifrování na úrovni sloupce (Azure Data Services)| – | |
| Zašifrovaná volání rozhraní API| Ano | Přes HTTPS a TLS. |



## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano |  | 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../../security/fundamentals/security-controls.md).

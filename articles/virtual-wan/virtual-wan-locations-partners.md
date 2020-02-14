---
title: Azure Virtual WAN partneři a umístění | Microsoft Docs
description: Tento článek obsahuje seznam partnerů Azure Virtual WAN a umístění centra.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 68379fd2334e517df4011c05bb32543f7ce4a173
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190409"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtuální partneři sítě WAN a umístění virtuálních rozbočovačů

Tento článek poskytuje informace o oblastech a partnerech podpor Virtual WAN pro připojení k virtuálnímu rozbočovači.

Azure Virtual WAN je síťová služba poskytující optimalizované a automatizované možnosti propojení jednotlivých poboček prostřednictvím Azure. Virtuální síť WAN umožňuje připojit a nakonfigurovat zařízení v pobočkách tak, aby komunikovaly přes Azure. Tato možnost se dá provést ručně nebo pomocí zařízení poskytovatele prostřednictvím virtuálního partnera WAN. Používání partnerských zařízení umožňuje snadné použití, zjednodušení připojení a správu konfigurace.

Připojení z místního zařízení se vytváří automatizovaným způsobem pro virtuální rozbočovač. Virtuální rozbočovač je virtuální síť spravovaná Microsoftem. Rozbočovač obsahuje různé koncové body služby, které umožňují připojení z vaší místní sítě (vpnsite). Můžete mít jenom jedno centrum na oblast.

## <a name="automation"></a>Automatizace od partnerů pro připojení

Zařízení, která se připojují k Azure Virtual WAN, mají vestavěnou automatizaci připojení. Tato možnost se obvykle nastavuje v uživatelském rozhraní pro správu zařízení (nebo ekvivalentní), která nastavuje připojení a správu konfigurací mezi zařízením sítě VPN na koncový bod VPN virtuálního rozbočovače Azure (Brána sítě VPN).

Následující automatizace na nejvyšší úrovni se nastavuje v konzole zařízení/centra pro správu:

* Správná oprávnění pro zařízení pro přístup ke skupině prostředků Azure Virtual WAN
* Nahrání firemního zařízení do Azure Virtual WAN
* Automatické stažení informací o připojení Azure
* Konfigurace místního zařízení pobočky 

Někteří partneři pro připojení můžou automatizovat automatizaci, aby zahrnovali vytvoření virtuální sítě a VPN Gateway virtuálního rozbočovače Azure. Pokud chcete získat další informace o automatizaci, přečtěte si téma [pokyny pro automatizaci virtuálních sítí WAN](virtual-wan-configure-automation-providers.md).

## <a name="partners"></a>Připojení prostřednictvím partnerů

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Následující partneři jsou plánované pro náš budoucí plán: Arista, Aruba HPE, Cisco Systems, F5 Networks, Open Systems, Oracle SD-WAN, SharpLink a VMWare Velocloud.

## <a name="locations"></a>Polohy

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v [nejčastějších dotazech k virtuální síti WAN](virtual-wan-faq.md).

* Další informace o tom, jak automatizovat připojení ke službě Azure Virtual WAN, najdete v tématu [pokyny pro automatizaci virtuálních sítí WAN](virtual-wan-configure-automation-providers.md).

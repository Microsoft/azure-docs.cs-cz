---
title: Umístění partnerů Azure Virtual WAN | Microsoft Docs
description: Tento článek obsahuje seznam partnerů Azure Virtual WAN a umístění centra.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: 985823e5aa806244596277c982cc129554d890f2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489064"
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

Někteří partneři pro připojení můžou automatizovat automatizaci, aby zahrnovali vytvoření virtuální sítě a VPN Gateway virtuálního rozbočovače Azure. Pokud chcete získat další informace o automatizaci, přečtěte si téma [Konfigurace automatizace – partneři sítě WAN](virtual-wan-configure-automation-providers.md).

## <a name="partners"></a>Připojení prostřednictvím partnerů

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Následující partneři jsou plánované pro náš budoucí plán: Arista, F5 Networks, Velocloud.

## <a name="locations"></a>Polohy

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v [nejčastějších dotazech k virtuální síti WAN](virtual-wan-faq.md).

* Další informace o tom, jak automatizovat připojení ke službě Azure Virtual WAN, najdete v tématu [virtuální partneři sítě WAN – jak automatizovat](virtual-wan-configure-automation-providers.md).

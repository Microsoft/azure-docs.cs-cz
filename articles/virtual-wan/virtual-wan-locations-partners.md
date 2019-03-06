---
title: Partneři Azure virtuální sítě WAN umístění | Dokumentace Microsoftu
description: Tento článek obsahuje seznam partnerů Azure virtuální sítě WAN a umístění centra.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: f38cd0565b2e90fe0803d8e815c622e22e954a18
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57409699"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtuální sítě WAN partneři a virtuální rozbočovač umístění

Tento článek obsahuje informace o virtuální sítě WAN podporované oblasti a partneři pro připojení do virtuální rozbočovač.

Azure Virtual WAN je síťová služba poskytující optimalizované a automatizované možnosti propojení jednotlivých poboček prostřednictvím Azure. Virtuální síť WAN umožňuje připojit a nakonfigurovat zařízení v pobočkách tak, aby komunikovaly přes Azure. To můžete udělat buď ručně, nebo pomocí poskytovatele zařízení prostřednictvím virtuální sítě WAN partnera. Pomocí partnerských zařízení umožňuje že snadné použití, zjednodušení připojení a správa konfigurace.

V automatizovaný způsob, jak chcete virtuální rozbočovač se naváže spojení z místního zařízení. Virtuální rozbočovač je spravovaná Microsoftem virtuální sítě. Rozbočovač obsahuje různé koncové body služby, které umožňují připojení z vaší místní sítě (vpnsite). V jedné oblasti můžete mít jenom jedno centrum.

## <a name="automation"></a>Automatizace od partnerů připojení

Zařízení, která připojení k Azure virtuální sítě WAN mají integrovanou automatizaci pro připojení. To se obvykle nastavuje nahoru v Správa zařízení uživatelského rozhraní (nebo ekvivalentní), která nastaví připojení a konfigurace správy mezi větve zařízení VPN pro koncový bod Azure virtuální centrum, virtuální privátní sítě (Brána pro síť VPN).

Následující základní služby automation je nastavení v centru konzoly a správy zařízení:

* Příslušná oprávnění pro zařízení pro přístup ke skupině prostředků virtuální sítě WAN Azure
* Nahrávání zařízení větev do Azure virtuální síť WAN
* Automatické stahování informací o připojení k Azure
* Konfigurace zařízení místní větve 

Někteří partneři připojení může prodloužit automatizace pro patří vytvoření Azure Virtual Hub VNet a brány VPN. Pokud chcete získat další informace o automatizaci, naleznete v tématu [konfigurace Automation – WAN partneři](virtual-wan-configure-automation-providers.md).

## <a name="partners"></a>Možnosti připojení prostřednictvím partnerů

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

## <a name="locations"></a>umístění

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Další postup

* Další informace o virtuální sítě WAN, najdete v článku [virtuální sítě WAN nejčastější dotazy k](virtual-wan-faq.md).

* Další informace o tom, jak automatizovat připojení k Azure virtuální sítě WAN, naleznete v tématu [virtuální sítě WAN partneři – jak automatizovat](virtual-wan-configure-automation-providers.md).

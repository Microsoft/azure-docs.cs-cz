---
title: Partneři a umístění virtuální sítě Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje seznam partnerů Azure Virtual WAN a umístění rozbočovače.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 3976f4114df6222d34f19e2bb44fd05d53057321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123263"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtuální partneři WAN a umístění virtuálních rozbočovačů

Tento článek obsahuje informace o oblastech podporovaných virtuální sítí WAN a partnerech pro připojení do virtuálního rozbočovače.

Azure Virtual WAN je síťová služba poskytující optimalizované a automatizované možnosti propojení jednotlivých poboček prostřednictvím Azure. Virtuální síť WAN umožňuje připojit a nakonfigurovat zařízení v pobočkách tak, aby komunikovaly přes Azure. To lze provést buď ručně, nebo pomocí zprostředkovatelských zařízení prostřednictvím partnera virtual WAN. Použití partnerských zařízení umožňuje snadné použití, zjednodušení připojení a správu konfigurace.

Připojení z místního zařízení je vytvořeno automatizovaným způsobem do virtuálního centra. Virtuální rozbočovač je virtuální síť spravovaná společností Microsoft. Rozbočovač obsahuje různé koncové body služby, které umožňují připojení z vaší místní sítě (vpnsite). Můžete mít pouze jeden rozbočovač na oblast.

## <a name="automation-from-connectivity-partners"></a><a name="automation"></a>Automatizace od partnerů zajišťujích připojení

Zařízení, která se připojují k Virtuální síti Azure, mají integrovanou automatizaci pro připojení. To se obvykle nastavuje v uzdu pro správu zařízení (nebo ekvivalentní), který nastavuje správu připojení a konfigurace mezi pobočkou zařízení VPN na koncový bod VPN virtuálního rozbočovače (brána VPN) virtuálního centra.

V centru konzoly/správy zařízení je nastavena následující automatizace na vysoké úrovni:

* Příslušná oprávnění pro zařízení pro přístup ke skupině prostředků Azure Virtual WAN
* Nahrávání pobočkového zařízení do virtuální sítě Azure WAN
* Automatické stahování informací o připojení Azure
* Konfigurace místního pobočkového zařízení 

Někteří partneři připojení mohou rozšířit automatizaci tak, aby zahrnovala vytvoření virtuální sítě Azure Virtual Hub a brány VPN. Pokud se chcete dozvědět více o automatizaci, [přečtěte si informace o automatizaci pro partnery virtuální sítě WAN](virtual-wan-configure-automation-providers.md).

## <a name="connectivity-through-partners"></a><a name="partners"></a>Konektivita prostřednictvím partnerů

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Následující partneři jsou uvedeni v našem plánu pro blízkou budoucnost: 128 Technologies, Arista, Aruba HPE, Cisco Systems, F5 Networks, Open Systems, Oracle SD-WAN, SharpLink a VMWare Velocloud.

## <a name="locations"></a><a name="locations"></a>Umístění

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální paměti WAN naleznete v [nejčastějších dotazech k virtuální wan .](virtual-wan-faq.md)

* Další informace o automatizaci připojení k virtuální síti Azure najdete v [tématu Pokyny pro automatizaci pro partnery virtuální sítě WAN](virtual-wan-configure-automation-providers.md).

---
title: O službě Azure Route Server (Preview) podporuje ExpressRoute a Azure VPN
description: Přečtěte si, jak server tras Azure komunikuje s ExpressRoute a branami Azure VPN.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 6e588c7c0381c6825bcf75cbbe28a1dd6b865940
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679464"
---
# <a name="about-azure-route-server-preview-support-for-expressroute-and-azure-vpn"></a>Podpora Azure Route serveru (Preview) pro ExpressRoute a Azure VPN

Server Azure Route podporuje nejen virtuální síťová zařízení třetích stran (síťové virtuální zařízení) spuštěná v Azure, ale zároveň se bez problémů integruje s ExpressRoute a branami Azure VPN. Nemusíte konfigurovat ani spravovat vytváření partnerských vztahů protokolu BGP mezi bránou a serverem tras Azure. Můžete povolit směrování mezi bránou a serverem Azure Route pomocí jednoduché [změny konfigurace](quickstart-configure-route-server-powershell.md#route-exchange).

> [!IMPORTANT]
> Služba Azure Route Server (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-it-work"></a>Jak to funguje?

Když nasadíte směrovací server Azure spolu s bránou ExpressRoute a síťové virtuální zařízení ve virtuální síti ve výchozím nastavení, služba Azure Route Server nešíří trasy, které obdrží od brány síťové virtuální zařízení a ExpressRoute mezi sebou. Jakmile povolíte výměnu tras, ExpressRoute a síťové virtuální zařízení se budou vzájemně učit trasy.

Například v následujícím diagramu:

* Zařízení SDWAN dostane z Azure Route serveru trasu od "on-Prem 2", která je připojená k ExpressRoute, spolu s trasou virtuální sítě.

* Brána ExpressRoute přijme trasu od "on-Prem 1", která je připojená k zařízení SDWAN, spolu s trasou virtuální sítě z Azure Route serveru.

    ![Diagram znázorňující ExpressRoute nakonfigurovaný s směrovacím serverem](./media/expressroute-vpn-support/expressroute-with-route-server.png)

Zařízení SDWAN můžete také nahradit bránou Azure VPN. Vzhledem k tomu, že Azure VPN Gateway a ExpressRoute jsou plně spravovány, stačí, když zapnete trasu pro směrování, aby dvě místní sítě vzájemně komunikovaly.

> [!IMPORTANT] 
> Brána Azure VPN Gateway musí být nakonfigurovaná v režimu [**aktivní-aktivní**](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) .
>

![Diagram znázorňující ExpressRoute a bránu VPN, která je nakonfigurovaná pomocí serveru směrování.](./media/expressroute-vpn-support/expressroute-and-vpn-with-route-server.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [serveru Azure Route](route-server-faq.md).
- Přečtěte si, jak [nakonfigurovat server směrování Azure](quickstart-configure-route-server-powershell.md).
- Přečtěte si další informace o [koexistenci Azure ExpressRoute a Azure VPN](../expressroute/expressroute-howto-coexist-resource-manager.md).

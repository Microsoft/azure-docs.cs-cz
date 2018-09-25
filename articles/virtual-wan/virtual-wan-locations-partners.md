---
title: Partneři Azure virtuální sítě WAN umístění | Dokumentace Microsoftu
description: Tento článek obsahuje seznam partnerů Azure virtuální sítě WAN a umístění centra
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: 76542392522ff14642ed7ccc6de8ed543d66513f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984989"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtuální sítě WAN partneři a virtuální rozbočovač umístění

Tento článek obsahuje informace o virtuální sítě WAN podporované oblasti a upřednostňovanou partneři pro připojení do virtuální rozbočovač.

Azure Virtual WAN je síťová služba poskytující optimalizované a automatizované možnosti propojení jednotlivých poboček prostřednictvím Azure. Virtuální síť WAN umožňuje připojit a nakonfigurovat zařízení v pobočkách tak, aby komunikovaly přes Azure. To můžete udělat buď ručně, nebo pomocí upřednostňované poskytovatele zařízení prostřednictvím virtuální sítě WAN upřednostňovaným partnerem. Použití zařízení s upřednostňovaným partnerem umožňuje že snadné použití, zjednodušení připojení a správa konfigurace. V automatizovaný způsob, jak chcete virtuální rozbočovač se naváže spojení z místního zařízení. Virtuální rozbočovač je spravovaná Microsoftem virtuální sítě. Rozbočovač obsahuje různé koncové body služby, které umožňují připojení z vaší místní sítě (vpnsite). V jedné oblasti můžete mít jenom jedno centrum.

## <a name="regions"></a>Oblasti

Seznam oblastí podporovaných a k dispozici jsou následující:

|Geopolitická oblast | Oblast Azure|
|---|---|
|Severní Amerika | Východní USA, Západní USA, Východní USA 2, Západní USA 2, Střed USA, Střed USA – jih, Střed USA – sever, Střed USA – západ, Střední Kanada, Východní Kanada |
|Jižní Amerika |Brazílie – jih |
| Evropa | Francie – střed, Francie – jih, Severní Evropa, Západní Evropa, Velká Británie – západ, Velká Británie – jih |
| Asie | Východní Asie, Jihovýchodní Asie |
| Japonsko  | Japonsko – západ, Japonsko – východ |
| Austrálie | Austrálie – jihovýchod, Austrálie – východ | 
| Vlády Austrálie | Austrálie – střed, Austrálie – střed 2 |
| Indie | Indie – západ, Indie – střed, Indie – jih |
| Jižní Korea | Jižní Korea – střed, Jižní Korea – jih | 

## <a name="automation-from-connectivity-partners"></a>Automatizace od partnerů připojení

Tato část popisuje základní podrobnosti automation z poskytovatelé připojení.

Zařízení, která připojení k Azure virtuální sítě WAN mají integrovanou automatizaci pro připojení. To se obvykle nastavuje nahoru v Správa zařízení uživatelského rozhraní (nebo ekvivalentní), která nastaví připojení a konfigurace správy mezi větve zařízení VPN pro koncový bod Azure virtuální centrum, virtuální privátní sítě (Brána pro síť VPN).

Následující základní služby automation je nastavení v centru konzoly a správy zařízení:

* Příslušná oprávnění pro zařízení pro přístup ke skupině prostředků virtuální sítě WAN Azure
* Nahrávání zařízení větev do Azure virtuální síť WAN
* Automatické stahování informací o připojení k Azure 
* Konfigurace zařízení místní větve 

Někteří partneři připojení může prodloužit automatizace pro patří vytvoření Azure Virtual Hub VNet a brány VPN. Pokud chcete získat další informace o automatizaci, naleznete v tématu [konfigurace Automation – WAN partneři](virtual-wan-configure-automation-providers.md).

## <a name="connectivity-through-preferred-partners"></a>Možnosti připojení prostřednictvím upřednostňovaných partnerů

Pokud váš partner větev zařízení není uvedené v následující části, prosím kontaktujte poskytovatele větev zařízení a potom kliknul kontaktujte nás na e-mailem azurevirtualwan@microsoft.com.

Můžete zkontrolovat následující odkazy, abyste získali další informace o službách nabízených upřednostňované partnery. 

|Upřednostňované partnerů|
|---|
|[Barracuda Networks](https://www.barracuda.com/AzurevWAN)|
| [Check Point](https://www.checkpoint.com/solutions/microsoft-azure-virtual-wan/) |
| [Citrix](https://www.citrix.com/global-partners/microsoft/sd-wan-for-azure-virtual-wan.html)|
|[Netfoundry](https://netfoundry.io/solutions/netfoundry-for-microsoft-azure-virtual-wan/)|
|[Palo Alto Networks](https://researchcenter.paloaltonetworks.com/2018/09/azure-vwan-integration/) |
|[Riverbed technologie](https://www.riverbed.com/go/steelconnect-azurewan.html)|
|[128 technologie](https://www.128technology.com/partners/azure) |

## <a name="next-steps"></a>Další postup

Další informace o virtuální sítě WAN, najdete v článku [virtuální sítě WAN nejčastější dotazy k](virtual-wan-faq.md).

Další informace o tom, jak automatizovat připojení k Azure virtuální sítě WAN, naleznete v tématu [virtuální sítě WAN partneři – jak automatizovat](virtual-wan-configure-automation-providers.md).

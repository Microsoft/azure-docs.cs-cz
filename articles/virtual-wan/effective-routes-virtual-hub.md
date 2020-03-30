---
title: 'Zobrazení efektivních tras virtuálního rozbočovače: Virtuální wan Azure | Dokumenty společnosti Microsoft'
description: Efektivní trasy pro virtuální centrum ve virtuální matné wan
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515847"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>Zobrazení efektivních tras virtuálního rozbočovače

Všechny trasy centra Virtual WAN můžete zobrazit na webu Azure Portal. Chcete-li zobrazit trasy, přejděte do virtuálního rozbočovače a vyberte **možnost Směrování -> Zobrazit efektivní trasy**.

## <a name="understanding-routes"></a><a name="understand"></a>Principy tras

Následující příklad vám pomůže lépe pochopit, jak virtuální směrování SÍTĚ WAN vypadá.

V tomto příkladu máme virtuální WAN se třemi rozbočovači. První uzel se nachází v oblasti východníCH USA, druhé centrum je v oblasti západní Evropy a třetí uzel je v oblasti západních USA. Ve virtuální síti WAN jsou propojeny všechny rozbočovače. V tomto příkladu budeme předpokládat, že centra Východní USA a Západní Evropa mají připojení z místních větví (paprsky) a virtuálních sítí (paprsky).

Mluvil virtuální sítě Azure (10.4.0.0/16) s virtuálním síťovým zařízením (10.4.0.6) je dále partnerský vztah k virtuální síti (10.5.0.0/16). Další [informace](#abouthubroute) o tabulce tras rozbočovače naleznete dále v tomto článku.

V tomto příkladu také předpokládáme, že pobočka 1 pro západní Evropu je propojena s centrem Východní USA a také s centrem západní Evropy. Okruh ExpressRoute ve východní části USA spojuje pobočku 2 s centrem Východní USA.

![Diagramu](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>Zobrazení efektivních tras

Když na portálu vyberete možnost Zobrazit efektivní trasy, vytvoří se výstup zobrazený v [tabulce tras rozbočovače](#routetable) pro centrum USA.

Chcete-li to uvést do perspektivy, první řádek znamená, že centrum East US se naučilo trasu 10.20.1.0/24 (větev 1) kvůli připojení typu směrování VPN *Next* ("Next hop" VPN Gateway Instance0 IP 10.1.0.6, Instance1 IP 10.1.0.7). *Směrování počátečních* bodů na ID zdroje. *Cesta AS* označuje cestu AS pro větev 1.

### <a name="hub-route-table"></a><a name="routetable"></a>Tabulka tras rozbočovače

Pomocí posuvníku v dolní části tabulky zobrazte "Cestu AS".

| **Předpona** |  **Typ dalšího segmentu** | **Další směrování** |  **Počátek trasy** |**Cesta AS** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|Síť VPN |10.1.0.6, 10.1.0.7| /subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/expressRouteGateways/4444a6ac74e4d855555-eastus-gw|21000|
|10.23.1.0/24| Síť VPN |10.1.0.6, 10.1.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw|23000|
|10.4.0.0/16|Připojení k virtuální síti| Připojení k odkazu |  |  |
|10.5.0.0/16| IP adresa| 10.4.0.6|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|0.0.0.0/0| IP adresa| `<Azure Firewall IP>` |/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|10.22.1.0/16| Vzdálený rozbočovač|10.8.0.6, 10.8.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_| 4848-22000 |
|10.9.0.0/16| Vzdálený rozbočovač|  Připojení k odkazu |/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_1| |

>[!NOTE]
> Pokud by centra východní CHVUT a západní Evropa nekomunikovaly mezi sebou v topologii příkladu, trasa se naučila (10.9.0.0/16) by neexistovala. Centra inzerují pouze sítě, které jsou k nim přímo připojeny.
>

## <a name="additional-information"></a><a name="additional"></a>Další informace

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>O tabulce tras rozbočovače

Můžete vytvořit virtuální trasu rozbočovače a použít ji na směrovací tabulku virtuálního rozbočovače. V uvedené tabulce můžete použít více tras. To vám umožní nastavit trasu pro cílovou virtuální síť prostřednictvím IP adresy (obvykle síťové virtuální zařízení (NVA) ve virtuální síti s paprskem). Další informace o sítích pro připojení k sítím a sítě naleznete v [tématu Směrování provozu z virtuálního rozbočovače do sítě nva](virtual-wan-route-table-portal.md).

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>O výchozí trase (0.0.0.0/0)

Virtuální rozbočovač má možnost šířit naučenou výchozí trasu do virtuální sítě, sítě VPN pro lokalitu a připojení ExpressRoute, pokud je příznak "Povoleno" v připojení. Tento příznak je viditelný při úpravě připojení virtuální sítě, připojení VPN nebo připojení ExpressRoute. 'EnableInternetSecurity' je ve výchozím nastavení vždy false na připojení hub virtuální sítě, ExpressRoute a VPN.

Výchozí trasa nepochází z virtuálního rozbočovače WAN. Výchozí trasa je rozšířena, pokud se již dozvěděl virtuální wan rozbočovač v důsledku nasazení brány firewall v rozbočovači, nebo pokud jiná připojená lokalita má vynucené tunelové propojení povoleno.

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v tématu [Přehled služby Virtual WAN](virtual-wan-about.md).
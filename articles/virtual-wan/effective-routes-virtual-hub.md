---
title: 'Zobrazení efektivních tras virtuálního rozbočovače: Azure Virtual WAN | Microsoft Docs'
description: Vyhledáte efektivní trasy k virtuálnímu rozbočovači v Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: cherylmc
ms.openlocfilehash: 7f0785f8ce2528d0c17a8aca520212df931bf394
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84343366"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>Zobrazení efektivních tras virtuálního rozbočovače

Všechny trasy k virtuálnímu rozbočovači WAN můžete zobrazit v Azure Portal. Pokud chcete zobrazit trasy, přejděte do virtuálního centra a pak vyberte **Směrování-> zobrazit efektivní trasy**.

## <a name="understanding-routes"></a><a name="understand"></a>Principy tras

Následující příklad vám pomůže lépe pochopit, jak se bude zobrazovat směrování virtuální sítě WAN.

V tomto příkladu máme virtuální síť WAN se třemi rozbočovači. První centrum je v oblasti Východní USA, druhý rozbočovač je v oblasti Západní Evropa a třetí centrum je v oblasti Západní USA. Ve virtuální síti WAN jsou všechna centra propojena. V tomto příkladu budeme předpokládat, že centra Východní USA a Západní Evropa mají připojení z místních větví (paprsky) a virtuálních sítí Azure (paprsky).

Koncová síť Azure (10.4.0.0/16) s virtuálním síťovým zařízením (10.4.0.6) je dále partnerského vztahu k virtuální síti (10.5.0.0/16). Další informace o tabulce směrování centra najdete v části [Další informace](#abouthubroute) dále v tomto článku.

V tomto příkladu předpokládáme, že Západní Evropa větev 1 je připojená k centru Východní USA a také k rozbočovači Západní Evropa. Okruh ExpressRoute v Východní USA připojuje větev 2 k centru Východní USA.

![znázorňuje](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>Zobrazit efektivní trasy

Když na portálu vyberete Zobrazit efektivní trasy, vytvoří se výstup zobrazený v [tabulce směrování centra](#routetable) pro centrum východní USA.

V perspektivě to první řádek znamená, že centrum Východní USA zjistilo trasu 10.20.1.0/24 (větev 1) z důvodu připojení *typu dalšího segmentu směrování* sítě VPN (' Next hop ' VPN Gateway Instance0 IP 10.1.0.6, položku instance1 IP 10.1.0.7). *Počátek směrování* odkazuje na ID prostředku. *Jako cesta* označuje cestu pro větev 1.

### <a name="hub-route-table"></a><a name="routetable"></a>Tabulka směrování centra

Pomocí posuvníku v dolní části tabulky zobrazte "AS Path".

| **Předpona** |  **Typ dalšího segmentu** | **Další směrování** |  **Původ trasy** |**JAKO cesta** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|Síť VPN |10.1.0.6, 10.1.0.7| /Subscriptions/ `<sub>` /ResourceGroups/ `<rg>` /providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-GW| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/Subscriptions/ `<sub>` /ResourceGroups/ `<rg>` /providers/Microsoft.Network/expressRouteGateways/4444a6ac74e4d85555-eastus-GW|21000|
|10.23.1.0/24| Síť VPN |10.1.0.6, 10.1.0.7|/Subscriptions/ `<sub>` /ResourceGroups/ `<rg>` /providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-GW|23000|
|10.4.0.0/16|Virtual Network připojení| Odkaz na linku |  |  |
|10.5.0.0/16| IP adresa| 10.4.0.6|/Subscriptions/ `<sub>` /ResourceGroups/ `<rg>` /providers/Microsoft.Network/virtualhubs/easthub_1/routetables/table_1| |
|0.0.0.0/0| IP adresa| `<Azure Firewall IP>` |/Subscriptions/ `<sub>` /ResourceGroups/ `<rg>` /providers/Microsoft.Network/virtualhubs/easthub_1/routetables/table_1| |
|10.22.1.0/16| Vzdálené centrum|10.8.0.6, 10.8.0.7|/Subscriptions/ `<sub>` /ResourceGroups/ `<rg>` /providers/Microsoft.Network/virtualhubs/westhub_| 4848-22000 |
|10.9.0.0/16| Vzdálené centrum|  Odkaz na linku |/Subscriptions/ `<sub>` /ResourceGroups/ `<rg>` /providers/Microsoft.Network/virtualhubs/westhub_1| |

>[!NOTE]
> Pokud Východní USA a centra Západní Evropa vzájemně nekomunikovala v příkladu topologie, získaná trasa (10.9.0.0/16) neexistuje. Rozbočovače inzerují jenom sítě, které jsou k nim přímo připojené.
>

## <a name="additional-information"></a><a name="additional"></a>Další informace

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>Tabulka směrování centra

Můžete vytvořit trasu virtuálního rozbočovače a použít ji pro směrovací tabulku virtuálního rozbočovače. V uvedené tabulce můžete použít více tras. To vám umožní nastavit trasu pro cílovou virtuální síť prostřednictvím IP adresy (obvykle síťové virtuální zařízení (síťové virtuální zařízení) ve virtuální síti paprsků). Další informace o síťová virtuální zařízení najdete v tématu [směrování provozu z virtuálního rozbočovače do síťové virtuální zařízení](virtual-wan-route-table-portal.md). Pamatujte na to, že tyto trasy nebudou zobrazeny v efektivní tabulce směrování. Tabulka efektivní trasy obsahuje jenom předpony pro místní a vzdálené centra plus připojené Virtual Network adresní prostor a trasy získané prostřednictvím protokolu BGP.

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>Výchozí trasa (0.0.0.0/0)

Virtuální rozbočovač má schopnost rozšířit získanou výchozí trasu na virtuální síť, síť VPN typu Site-to-site a připojení ExpressRoute, pokud je příznak povoleno u připojení. Tento příznak se zobrazí při úpravě připojení k virtuální síti, připojení k síti VPN nebo připojení ExpressRoute. Ve výchozím nastavení je v připojeních virtuální sítě, ExpressRoute a VPN typu "EnableInternetSecurity" vždy false.

Výchozí trasa nepochází do virtuálního centra WAN. Výchozí trasa je šířena v případě, že ji již služba Virtual WAN hub vyvolala v důsledku nasazení brány firewall v centru nebo v případě, že je povoleno vynucené tunelování na jiném připojeném serveru.

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v tématu [Přehled služby Virtual WAN](virtual-wan-about.md).

---
title: 'Zobrazení efektivních tras virtuálního rozbočovače: Azure Virtual WAN | Microsoft Docs'
description: Postup zobrazení efektivních tras pro virtuální rozbočovač ve službě Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ad487aa7492abcf02988c78ccfa2ba7cd4798249
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983652"
---
# <a name="view-virtual-hub-effective-routes"></a>Zobrazení efektivních tras virtuálního centra

Všechny trasy vašeho virtuálního rozbočovače WAN můžete zobrazit v Azure Portal. Tento článek vás provede kroky k zobrazení efektivních tras. Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>Výběr připojení nebo směrovacích tabulek

1. Přejděte do svého virtuálního centra a pak vyberte **Směrování**. Na stránce směrování vyberte **platné trasy**.
1. Z rozevíracího seznamu můžete vybrat **směrovací tabulku**. Pokud nevidíte možnost směrovací tabulky, znamená to, že v tomto virtuálním centru nemáte nastavenou vlastní nebo výchozí směrovací tabulku.

## <a name="view-output"></a><a name="output"></a>Zobrazit výstup

Výstup stránky zobrazuje následující pole:

* **Prefix**: Předpona adresy známá k aktuální entitě (informace o směrovači virtuálního rozbočovače)
* **Typ dalšího segmentu směrování**: může být Virtual Network připojení, VPN_S2S_Gateway, ExpressRouteGateway, vzdáleného rozbočovače nebo Azure firewall.
* **Další segment směrování**: Toto je odkaz na ID prostředku dalšího segmentu směrování, nebo se k tomu, že se zobrazí odkaz na aktuální centrum, jednoduše zobrazuje odkaz.
* **Původ**: ID prostředku zdroje směrování.
* **Jako cesta**: atribut BGP jako (autonomní systém) cesta uvádí všechny hodnoty, které je třeba procházet, aby se dosáhlo místa, odkud je inzerována předpona, ke které je připojena cesta.

### <a name="example"></a><a name="example"></a>Příklad

Hodnoty v následující ukázkové tabulce naznačují, že připojení nebo směrovací tabulka virtuálního rozbočovače se naučila trasa 10.2.0.0/24 (předpona větve). Zjistila se trasa z důvodu **typu dalšího segmentu směrování sítě VPN** VPN_S2S_GATEWAY s ID prostředku **dalšího segmentu směrování** VPN Gateway. Body **původu trasují** k ID prostředku, který pochází z brány VPN/tabulky nebo připojení směrování. **Jako cesta** označuje cestu pro větev.

Pomocí posuvníku v dolní části tabulky zobrazte "AS Path".

| **Předpona** |  **Typ dalšího segmentu směrování** | **Další směrování** |  **Původ trasy** |**JAKO cesta** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |/Subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw|/Subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw| 20000|

**Odůvodněn**

* Pokud se ve výstupu **získat efektivní trasy** zobrazí hodnota 0.0.0.0/0, znamená to, že trasa existuje v jedné z směrovacích tabulek. Pokud se ale tato trasa nastavila pro Internet, pro připojení se vyžaduje další příznak **"enableInternetSecurity": true** . V případě, že je příznak "enableInternetSecurity" u připojení "NEPRAVDA", nebude cesta platná v síťovém adaptéru virtuálního počítače ukazovat.

* Pole **rozšířit výchozí trasu** se zobrazuje na portálu Azure Virtual WAN při úpravě připojení k virtuální síti, připojení k síti VPN nebo připojení ExpressRoute. Toto pole indikuje příznak **enableInternetSecurity** , který je vždycky ve výchozím nastavení false pro připojení EXPRESSROUTE a VPN, ale pro připojení k virtuální síti je nastavená hodnota true.

* Pokud se při zobrazení efektivních tras na síťové kartě virtuálního počítače zobrazí další segment směrování jako Virtual Network brána, která implikuje směrovač virtuálního rozbočovače v případě, že je virtuální počítač připojený k virtuálnímu rozbočovači sítě WAN.

* Zobrazit efektivní trasy pro tabulku směrování virtuálního rozbočovače se naplní jenom v případě, že má virtuální rozbočovač k němu připojený aspoň jeden typ připojení (VPN/ER/VNET).

## <a name="next-steps"></a>Další kroky

* Další informace o službě Virtual WAN najdete v tématu [Přehled služby Virtual WAN](virtual-wan-about.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

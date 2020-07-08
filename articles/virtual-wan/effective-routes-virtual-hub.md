---
title: 'Zobrazení efektivních tras virtuálního rozbočovače: Azure Virtual WAN | Microsoft Docs'
description: Postup zobrazení efektivních tras pro virtuální rozbočovač ve službě Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 20cdc55b474034480392f9dfb05b20ad25df6939
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037762"
---
# <a name="view-virtual-hub-effective-routes"></a>Zobrazení efektivních tras virtuálního centra

Všechny trasy vašeho virtuálního rozbočovače WAN můžete zobrazit v Azure Portal. Tento článek vás provede kroky k zobrazení efektivních tras. Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

> [!NOTE]
> Na Azure Portal mohou být některé z těchto funkcí stále zavedené a nejsou k dispozici až do týdne od 3. srpna. 
>

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>Výběr připojení nebo směrovacích tabulek

1. Přejděte do svého virtuálního centra a pak vyberte **Směrování**. Na stránce směrování vyberte **platné trasy**.
1. Z rozevíracího seznamu můžete vybrat **Typ připojení** nebo **směrovací tabulku**. Pokud nevidíte možnost směrovací tabulky, znamená to, že v tomto virtuálním centru nemáte nastavenou vlastní nebo výchozí směrovací tabulku.
1. Z rozevíracího seznamu pro **tabulky připojení/směrování**můžete vybrat z následujících položek:

   * Virtual Network připojení
   * Připojení k síti VPN
   * Připojení ExpressRoute
   * Připojení Point-to-site
   * Tabulka směrování

   :::image type="content" source="./media/effective-routes-virtual-hub/routing.png" alt-text="Směrování":::

## <a name="view-output"></a><a name="output"></a>Zobrazit výstup

Výstup stránky zobrazuje následující pole:

* **Prefix**: Předpona adresy známá aktuální entitě.
* **Typ dalšího segmentu směrování**: může být Virtual Network připojení, VPN_S2S_Gateway, ExpressRouteGateway, vzdáleného rozbočovače nebo Azure firewall.
* **Další segment směrování**: Jedná se o IP adresu, nebo jenom v případě, že se na odkaz ukáže aktuální centrum.
* **Původ**: ID prostředku zdroje směrování.
* **Jako cesta**: atribut BGP jako (autonomní systém) cesta uvádí všechny hodnoty, které je třeba procházet, aby se dosáhlo místa, odkud je inzerována předpona, ke které je připojena cesta.

### <a name="example"></a><a name="example"></a>Případě

Hodnoty v následující ukázkové tabulce naznačují, že připojení nebo směrovací tabulka virtuálního rozbočovače se naučila trasa 10.2.0.0/24 (předpona větve). Zjistila se trasa z důvodu **typu dalšího segmentu směrování sítě VPN** VPN_S2S_GATEWAY s ID prostředku **dalšího segmentu směrování** VPN Gateway. Body **původu trasují** k ID prostředku, který pochází z brány VPN/tabulky nebo připojení směrování. **Jako cesta** označuje cestu pro větev.

Pomocí posuvníku v dolní části tabulky zobrazte "AS Path".

| **Předpona** |  **Typ dalšího segmentu** | **Další směrování** |  **Původ trasy** |**JAKO cesta** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |10.1.0.6, 10.1.0.7|/Subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw| 20000|

**Odůvodněn**

* Pokud se ve výstupu **získat efektivní trasy** zobrazí hodnota 0.0.0.0/0, znamená to, že trasa existuje v jedné z směrovacích tabulek. Pokud se ale tato trasa nastavila pro Internet, pro připojení se vyžaduje další příznak **"enableInternetSecurity": true** . V případě, že je příznak "enableInternetSecurity" u připojení "NEPRAVDA", nebude cesta platná v síťovém adaptéru virtuálního počítače ukazovat.

* Pole **rozšířit výchozí trasu** se zobrazuje na portálu Azure Virtual WAN při úpravě připojení k virtuální síti, připojení k síti VPN nebo připojení ExpressRoute. Toto pole indikuje příznak **enableInternetSecurity** , který je vždycky ve výchozím nastavení false pro připojení EXPRESSROUTE a VPN, ale pro připojení k virtuální síti je nastavená hodnota true.

## <a name="next-steps"></a>Další kroky

* Další informace o službě Virtual WAN najdete v tématu [Přehled služby Virtual WAN](virtual-wan-about.md).
* Další informace o směrování virtuálních rozbočovačů najdete v tématu [o směrování virtuálního rozbočovače](about-virtual-hub-routing.md).

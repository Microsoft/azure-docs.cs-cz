---
title: 'Přidání více připojení brány VPN site-to-site do virtuální sítě: Portál Azure'
description: Přidání připojení S2S pro více lokalit do brány VPN, která má existující připojení
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 7b438f2b966dc43d41b91a138b39193d230d5546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779684"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Přidání připojení site-to-site do virtuální sítě s existujícím připojením brány VPN

> [!div class="op_single_selector"]
> * [Portál Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (Classic)](vpn-gateway-multi-site.md)
>
> 

Tento článek vám pomůže přidat připojení site-to-site (S2S) k bráně VPN, která má existující připojení pomocí portálu Azure. Tento typ připojení se často označuje jako konfigurace "více sítí". Připojení S2S můžete přidat k virtuální síti, která už má připojení S2S, připojení point-to-site nebo připojení k virtuální síti. Při přidávání připojení existují určitá omezení. Před zahájením konfigurace ověřte oddíl [Před zahájením](#before) v tomto článku. 

Tento článek se vztahuje na virtuální sítě Správce prostředků, které mají bránu VPN RouteBased. Tyto kroky se nevztahují na nové konfigurace koexistujícího připojení ExpressRoute/Site-to-Site. Pokud však pouze přidáváte nové připojení VPN k již existující koexistující konfiguraci, můžete použít tyto kroky. Informace o koexistujících připojeních naleznete v [tématu ExpressRoute/S2S.](../expressroute/expressroute-howto-coexist-resource-manager.md)

### <a name="deployment-models-and-methods"></a>Modely a metody nasazení
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Tuto tabulku aktualizujeme, jakmile budou pro tuto konfiguraci k dispozici nové články a další nástroje. Když je k dispozici článek, odkazujeme přímo na něj z této tabulky.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before-you-begin"></a><a name="before"></a>Než začnete
Ověřte následující položky:

* Nekonfigurujete novou koexistující konfiguraci ExpressRoute a brány VPN.
* Máte virtuální síť, která byla vytvořena pomocí modelu nasazení Resource Manager s existujícípřipojení.
* Brána virtuální sítě pro vaši virtuální síť je RouteBased. Pokud máte bránu VPN založené na zásadách, musíte odstranit bránu virtuální sítě a vytvořit novou bránu VPN jako RouteBased.
* Žádný z rozsahů adres se nepřekrývá pro žádnou virtuální síť, ke které se tato virtuální síť připojuje.
* Máte kompatibilní zařízení VPN a někoho, kdo je schopen jej nakonfigurovat. Viz [Informace o zařízeních VPN](vpn-gateway-about-vpn-devices.md). Pokud nevíte, jak nakonfigurovat zařízení VPN, nebo neznáte rozsahy IP adres v konfiguraci vaší místní sítě, budete se muset spojit s někým, kdo vám s tím pomůže.
* Máte externě čelí veřejné IP adresy pro vaše zařízení VPN. Tato IP adresa nesmí být umístěná za překladem adres (NAT).

## <a name="part-1---configure-a-connection"></a><a name="part1"></a>Část 1 - Konfigurace připojení
1. V prohlížeči přejděte na portál [Azure Portal](https://portal.azure.com) a v případě potřeby se přihlaste pomocí účtu Azure.
2. Klikněte na **Všechny prostředky** a vyhledejte **bránu virtuální sítě** ze seznamu prostředků a klikněte na ni.
3. Na stránce **Brána virtuální sítě** klikněte na **Připojení**.
   
    ![Stránka Připojení](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Stránka Připojení")<br>
4. Na stránce **Připojení** klikněte na **+Přidat**.
   
    ![Tlačítko Přidat připojení](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Tlačítko Přidat připojení")<br>
5. Na stránce **Přidat připojení** vyplňte následující pole:
   
   * **Název:** Název, který chcete přidělit webu, ke kterém vytváříte připojení.
   * **Typ připojení:** Vyberte **možnost Site-to-site (IPsec).**
     
     ![Přidat stránku připojení](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Přidat stránku připojení")<br>

## <a name="part-2---add-a-local-network-gateway"></a><a name="part2"></a>Část 2 – Přidání brány místní sítě
1. Klepněte na **položku Brána místní sítě:** ***Zvolte bránu místní sítě***. Otevře se stránka **Zvolit bránu místní sítě.**
   
    ![Výběr brány místní sítě](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Výběr brány místní sítě")<br>
2. Kliknutím na **Vytvořit nový** otevřete stránku **Vytvořit bránu místní sítě.**
   
    ![Vytvořit stránku brány místní sítě](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Vytvoření brány místní sítě")<br>
3. Na stránce **Vytvořit bránu místní sítě** vyplňte následující pole:
   
   * **Název:** Název, který chcete přidělit prostředku brány místní sítě.
   * **IP adresa:** Veřejná IP adresa zařízení VPN na webu, ke kterému se chcete připojit.
   * **Adresní prostor:** Adresní prostor, který chcete směrovat do nové lokality místní sítě.
4. Klepnutím na **tlačítko OK** na stránce **Vytvořit bránu místní sítě** uložte změny.

## <a name="part-3---add-the-shared-key-and-create-the-connection"></a><a name="part3"></a>Část 3 - Přidání sdíleného klíče a vytvoření připojení
1. Na stránce **Přidat připojení** přidejte sdílený klíč, který chcete použít k vytvoření připojení. Můžete buď získat sdílený klíč ze zařízení VPN, nebo si ho vytvořit tady a pak nakonfigurovat zařízení VPN tak, aby používalo stejný sdílený klíč. Důležité je, že klíče jsou úplně stejné.
   
    ![Sdílený klíč](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Sdílený klíč")<br>
2. V dolní části stránky vytvořte připojení kliknutím na **OK.**

## <a name="part-4---verify-the-vpn-connection"></a><a name="part4"></a>Část 4 - Ověření připojení VPN


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Další kroky

Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v [cestě učení virtuálních počítačů.](/learn/paths/deploy-a-website-with-azure-virtual-machines/)

---
title: 'Přidání několika připojení VPN brány Site-to-Site k virtuální síti: portál Azure: Resource Manager | Dokumentace Microsoftu'
description: Přidání připojení S2S více lokalit, který má existující připojení brány VPN
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: f3e8b165-f20a-42ab-afbb-bf60974bb4b1
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: a814834be3225764c3b6f237bd515ca087f975a7
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52873117"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Přidat připojení Site-to-Site k virtuální síti s existujícím připojením brány VPN

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (Classic)](vpn-gateway-multi-site.md)
>
> 

V tomto článku vám pomůžou přidat připojení Site-to-Site (S2S) k bráně VPN, který má existující připojení pomocí webu Azure portal. Tento typ připojení se často označuje jako "s více lokalitami" konfigurace. Můžete přidat připojení S2S k virtuální síti, který už má připojení S2S, připojení Point-to-Site a připojení VNet-to-VNet. Při přidávání připojení existují určitá omezení. Zkontrolujte [před zahájením](#before) části v tomto článku ověřit před zahájením konfigurace. 

Tento článek se týká k virtuálním sítím Resource Manageru, mít bránu VPN typu RouteBased. Tyto kroky nevztahují na konfigurace současně existujících připojení ExpressRoute a Site-to-Site. Zobrazit [společně používaných připojení typu ExpressRoute a S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) informace o existujících připojení.

### <a name="deployment-models-and-methods"></a>Modely a metody nasazení
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Tuto tabulku aktualizujeme přidáváním nových článků a dalších nástrojů bude k dispozici pro tuto konfiguraci. Když je článek k dispozici, odkaz na něj přímo z této tabulky.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>Než začnete
Zkontrolujte následující položky:

* Nejsou vytvoření současně existujících připojení ExpressRoute a S2S.
* Máte virtuální síť, která byla vytvořena pomocí modelu nasazení Resource Manageru s existujícím připojením.
* Je Brána virtuální sítě pro vaši virtuální síť typu RouteBased. Pokud máte bránu sítě VPN PolicyBased, musíte odstranit bránu virtuální sítě a vytvořit novou bránu sítě VPN jako RouteBased.
* Žádné rozsahy adres překrytí pro všechny virtuální sítě, která tuto virtuální síť se připojuje k.
* Máte kompatibilní zařízení VPN a někoho, kdo jej umí nakonfigurovat. Viz [Informace o zařízeních VPN](vpn-gateway-about-vpn-devices.md). Pokud nevíte, jak nakonfigurovat zařízení VPN, nebo neznáte rozsahy IP adres v konfiguraci vaší místní sítě, budete se muset spojit s někým, kdo vám s tím pomůže.
* Máte veřejnou IP adresu pro vaše zařízení VPN. Tato IP adresa nesmí být umístěná za překladem adres (NAT).

## <a name="part1"></a>Část 1 – konfigurace připojení
1. V prohlížeči přejděte na portál [Azure Portal](http://portal.azure.com) a v případě potřeby se přihlaste pomocí účtu Azure.
2. Klikněte na tlačítko **všechny prostředky** a vyhledejte vaši **brány virtuální sítě** ze seznamu prostředků a klikněte na něj.
3. Na **Brána virtuální sítě** klikněte na **připojení**.
   
    ![Stránka Připojení](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Stránka Připojení")<br>
4. Na **připojení** klikněte na **+ přidat**.
   
    ![Tlačítko Přidat připojení](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "tlačítko Přidat připojení")<br>
5. Na **přidat připojení** stránce, vyplňte následující pole:
   
   * **Název:** názvu, kterou chcete přiřadit k lokalitě vytváříte připojení.
   * **Typ připojení:** vyberte **Site-to-site (IPsec)**.
     
     ![Stránka Přidat připojení](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "stránku přidat připojení")<br>

## <a name="part2"></a>Část 2 – přidání brány místní sítě
1. Klikněte na tlačítko **bránu místní sítě** ***zvolit bránu místní sítě***. Tím se otevře **vybrat bránu místní sítě** stránky.
   
    ![Zvolit bránu místní sítě](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "vybrat bránu místní sítě")<br>
2. Klikněte na tlačítko **vytvořit nový** otevřít **vytvořit bránu místní sítě** stránky.
   
    ![Stránka pro vytvoření místní síťové brány](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "vytvořit bránu místní sítě")<br>
3. Na **vytvořit bránu místní sítě** stránce, vyplňte následující pole:
   
   * **Název:** názvu, kterou chcete přidělit k prostředku brány místní sítě.
   * **IP adresa:** veřejnou IP adresu zařízení VPN na lokalitu, která se chcete připojit.
   * **Adresní prostor:** adresní prostor, který chcete směrovat do nové místní síťové lokality.
4. Klikněte na tlačítko **OK** na **vytvořit bránu místní sítě** stránky a uložte změny.

## <a name="part3"></a>Část 3 – Přidání sdíleného klíče a vytvoření připojení
1. Na **přidat připojení** stránky, přidá se sdílený klíč, který chcete použít k vytvoření připojení. Můžete získat sdílený klíč z vašeho zařízení VPN, nebo si ho vytvořit tady a pak nakonfigurovat zařízení VPN použijte stejný sdílený klíč. Důležité je, že klíče jsou stejné.
   
    ![Sdílený klíč](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Sdílený klíč")<br>
2. V dolní části stránky klikněte na tlačítko **OK** k vytvoření připojení.

## <a name="part4"></a>Část 4 – ověření připojení VPN


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Další postup

Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Najdete v článku [virtuálních počítačů naučná stezka](/learn/paths/deploy-a-website-with-azure-virtual-machines/) Další informace.

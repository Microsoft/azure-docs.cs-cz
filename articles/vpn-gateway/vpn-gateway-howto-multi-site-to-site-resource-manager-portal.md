---
title: 'Přidání více připojení Site-to-site k virtuální síti VPN Gateway: Azure Portal: Správce prostředků | Microsoft Docs'
description: Přidání připojení S2S s více lokalitami k bráně VPN, která má existující připojení
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: d2c32fd35bbc6de1f010013c40a06af69052d3f5
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244623"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Přidání připojení typu Site-to-site k virtuální síti s existujícím připojením služby VPN Gateway

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (Classic)](vpn-gateway-multi-site.md)
>
> 

Tento článek vám pomůže přidat připojení typu Site-to-Site (S2S) k bráně VPN, která má existující připojení pomocí Azure Portal. Tento typ připojení se často označuje jako konfigurace s více lokalitami. Můžete přidat připojení S2S k virtuální síti, která už má připojení S2S, připojení typu Point-to-site nebo připojení typu VNet-to-VNet. Při přidávání připojení platí určitá omezení. Před zahájením konfigurace zkontrolujte, jestli je v tomto článku oddíl [než začnete](#before) . 

Tento článek se týká Správce prostředků virtuální sítě, která má RouteBased bránu VPN. Tyto kroky se nevztahují na nové konfigurace souběžného připojení ExpressRoute/site-to-site. Pokud však přidáváte pouze nové připojení VPN k již existující konfiguraci s jednou konfigurací současně, můžete použít tento postup. Informace o současných připojeních najdete v tématu [koexistující připojení ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) .

### <a name="deployment-models-and-methods"></a>Modely a metody nasazení
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Tuto tabulku aktualizujeme, protože nové články a další nástroje budou k dispozici pro tuto konfiguraci. Když je článek k dispozici, odkazujeme přímo na něj z této tabulky.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>Než začnete
Ověřte následující položky:

* Nekonfigurujete novou koexistující konfiguraci ExpressRoute a VPN Gateway.
* Máte virtuální síť, která byla vytvořena pomocí modelu nasazení Správce prostředků s existujícím připojením.
* Brána virtuální sítě pro vaši virtuální síť je RouteBased. Pokud máte bránu VPN PolicyBased, musíte bránu virtuální sítě odstranit a vytvořit novou bránu VPN jako RouteBased.
* Žádný z rozsahů adres se nepřekrývá pro žádný z virtuální sítě, ke kterým se tato virtuální síť připojuje.
* Máte kompatibilní zařízení VPN a někoho, kdo ho může nakonfigurovat. Informace najdete v tématu [informace o zařízeních VPN](vpn-gateway-about-vpn-devices.md). Pokud nejste obeznámeni s konfigurací zařízení VPN nebo neznáte rozsahy IP adres v konfiguraci vaší místní sítě, budete se muset spojit s někým, kdo vám poskytne tyto podrobnosti.
* Máte veřejnou IP adresu pro vaše zařízení VPN. Tato IP adresa nemůže být umístěná za překladem adres (NAT).

## <a name="part1"></a>Část 1 – Konfigurace připojení
1. V prohlížeči přejděte na [Azure Portal](https://portal.azure.com) a v případě potřeby se přihlaste pomocí svého účtu Azure.
2. Klikněte na **všechny prostředky** a vyhledejte **bránu virtuální sítě** ze seznamu prostředků a klikněte na ni.
3. Na stránce **Brána virtuální sítě** klikněte na **připojení**.
   
    ![](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Stránka") připojení stránky připojení<br>
4. Na stránce **připojení** klikněte na **+ Přidat**.
   
    Tlačítko ![Přidat připojení]– tlačítko(./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Přidat připojení")<br>
5. Na stránce **Přidat připojení** vyplňte následující pole:
   
   * **Název:** Název, který chcete přidělit k lokalitě, ke které vytváříte připojení.
   * **Typ připojení:** Vyberte **site-to-Site (IPSec)** .
     
     Stránka ![Přidat]připojení –(./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Přidat připojení")<br>

## <a name="part2"></a>Část 2 – Přidání brány místní sítě
1. Klikněte na **Brána místní sítě** ***zvolit bránu místní sítě***. Tím se otevře stránka **Vybrat bránu místní sítě** .
   
    ![Zvolit bránu místní sítě](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "zvolit bránu místní sítě")<br>
2. Kliknutím na **vytvořit nový** otevřete stránku **vytvořit bránu místní sítě** .
   
    ![Stránka vytvořit bránu místní sítě](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "vytvořit bránu místní sítě")<br>
3. Na stránce **vytvořit bránu místní sítě** vyplňte následující pole:
   
   * **Název:** Název, který chcete přidělit prostředku brány místní sítě.
   * **IP adresa:** Veřejná IP adresa zařízení VPN na lokalitě, ke které se chcete připojit.
   * **Adresní prostor:** Adresní prostor, který chcete směrovat do nové místní síťové lokality.
4. Uložte změny kliknutím na tlačítko **OK** na stránce **vytvořit bránu místní sítě** .

## <a name="part3"></a>Část 3 – přidání sdíleného klíče a vytvoření připojení
1. Na stránce **Přidat připojení** přidejte sdílený klíč, který chcete použít k vytvoření připojení. Můžete buď získat sdílený klíč ze zařízení VPN, nebo ho vytvořit tady a potom nakonfigurovat zařízení VPN tak, aby používalo stejný sdílený klíč. Důležité je, že klíče jsou přesně stejné.
   
    ![](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Sdílený klíč") sdíleného klíče<br>
2. V dolní části stránky vytvořte připojení kliknutím na tlačítko **OK** .

## <a name="part4"></a>Část 4 – ověření připojení VPN


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Další kroky

Po dokončení připojení můžete virtuální počítače přidat do svých virtuálních sítí. Další informace najdete v tématu věnovaném [cestě k virtuálním počítačům](/learn/paths/deploy-a-website-with-azure-virtual-machines/) .

---
title: 'Přidání více připojení Site-to-site k síti VPN Gateway do virtuální sítě: Azure Portal'
description: Přidání připojení S2S s více lokalitami k bráně VPN, která má existující připojení
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: 92d39b0d39511571701fd092f641cb8ca3ae42c7
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890144"
---
# <a name="add-additional-s2s-connections-to-a-vnet-azure-portal"></a>Přidání dalších připojení S2S k virtuální síti: Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (Classic)](vpn-gateway-multi-site.md)
>

Tento článek vám pomůže přidat další připojení typu Site-to-Site (S2S) k bráně VPN, která má existující připojení. Tato architektura se často označuje jako konfigurace s více lokalitami. Můžete přidat připojení S2S k virtuální síti, která už má připojení S2S, připojení typu Point-to-site nebo připojení typu VNet-to-VNet. Při přidávání připojení platí určitá omezení. Před zahájením konfigurace si přečtěte část [požadavky](#before) v tomto článku.

Tento článek se týká Správce prostředků virtuální sítě, která má RouteBased bránu VPN. Tyto kroky se nevztahují na nové konfigurace souběžného připojení ExpressRoute/site-to-site. Pokud však přidáváte pouze nové připojení VPN k již existující konfiguraci s jednou konfigurací současně, můžete použít tento postup. Informace o současných připojeních najdete v tématu [koexistující připojení ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) .

## <a name="prerequisites"></a><a name="before"></a>Předpoklady

Ověřte následující položky:

* Nekonfigurujete novou koexistující konfiguraci ExpressRoute a VPN Gateway.
* Máte virtuální síť, která byla vytvořena pomocí modelu nasazení Správce prostředků s existujícím připojením.
* Brána virtuální sítě pro vaši virtuální síť je RouteBased. Pokud máte bránu VPN PolicyBased, musíte bránu virtuální sítě odstranit a vytvořit novou bránu VPN jako RouteBased.
* Žádný z rozsahů adres se nepřekrývá pro žádný z virtuální sítě, ke kterým se tato virtuální síť připojuje.
* Máte kompatibilní zařízení VPN a někoho, kdo ho může nakonfigurovat. Viz [Informace o zařízeních VPN](vpn-gateway-about-vpn-devices.md). Pokud nevíte, jak nakonfigurovat zařízení VPN, nebo neznáte rozsahy IP adres v konfiguraci vaší místní sítě, budete se muset spojit s někým, kdo vám s tím pomůže.
* Máte veřejnou IP adresu pro vaše zařízení VPN.

## <a name="configure-a-connection"></a><a name="configure"></a>Konfigurace připojení

1. V prohlížeči přejděte na portál [Azure Portal](https://portal.azure.com) a v případě potřeby se přihlaste pomocí účtu Azure.
1. Vyberte **všechny prostředky** a vyhledejte **bránu virtuální sítě** ze seznamu prostředků a vyberte ji.
1. Na stránce **Brána virtuální sítě** vyberte **připojení** .

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connections.png" alt-text="Připojení brány VPN Gateway":::
1. Na stránce **připojení** vyberte **+ Přidat** .
1. Tím otevřete stránku **Přidat připojení** .

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/add-connection.png" alt-text="Připojení brány VPN Gateway":::
1. Na stránce **Přidat připojení** vyplňte následující pole:

   * **Název:** Název, který chcete přidělit k lokalitě, ke které vytváříte připojení.
   * **Typ připojení:** Vyberte **site-to-Site (IPSec)** .

## <a name="add-a-local-network-gateway"></a><a name="local"></a>Přidat bránu místní sítě

1. V poli **Brána místní sítě** vyberte **_zvolit bránu místní sítě_*_. Otevře se okno _* zvolit bránu místní sítě** .
1. Výběrem **+ vytvořit nové** otevřete stránku **vytvořit bránu místní sítě** .

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/create-local-network-gateway.png" alt-text="Připojení brány VPN Gateway":::
1. Na stránce **vytvořit bránu místní sítě** vyplňte následující pole:

   * **Název:** Název, který chcete přidělit prostředku brány místní sítě.
   * **Koncový bod:** Veřejná IP adresa zařízení VPN na lokalitě, ke které se chcete připojit, nebo plně kvalifikovaný název domény koncového bodu.
   * **Adresní prostor:** Adresní prostor, který chcete směrovat do nové místní síťové lokality.
1. V případě, že chcete změny uložit, vyberte na stránce **vytvořit bránu místní sítě** možnost **OK** .

## <a name="add-the-shared-key"></a><a name="part3"></a>Přidat sdílený klíč

1. Po vytvoření brány místní sítě se vraťte na stránku **Přidat připojení** .
1. Vyplňte zbývající pole. Pro **sdílený klíč (PSK)** můžete buď získat sdílený klíč ze zařízení VPN, nebo ho vytvořit tady a potom nakonfigurovat zařízení VPN tak, aby používalo stejný sdílený klíč. Důležité je, že klíče jsou přesně stejné.

## <a name="create-the-connection"></a><a name="create"></a>Vytvoření připojení

1. V dolní části stránky vyberte **OK** a vytvořte připojení. Připojení začne okamžitě vytvářet.
1. Jakmile se připojení dokončí, můžete si ho prohlédnout a ověřit.

## <a name="view-and-verify-the-vpn-connection"></a><a name="verify"></a>Zobrazení a ověření připojení k síti VPN

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="next-steps"></a>Další kroky

Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v tématu věnovaném [studijním cestám virtuálních počítačů](/learn/paths/deploy-a-website-with-azure-virtual-machines/).

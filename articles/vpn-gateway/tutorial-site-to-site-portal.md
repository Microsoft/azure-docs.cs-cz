---
title: 'Kurz – připojení místní sítě k virtuální síti: Azure Portal'
description: Pomocí portálu můžete vytvořit připojení typu Site-to-Site VPN Gateway IPsec z místní sítě k virtuální síti Azure přes veřejný Internet.
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/04/2020
ms.openlocfilehash: ccb43c3e7efb9289450ad9a71c003f54e5362b66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945206"
---
# <a name="tutorial-create-a-site-to-site-connection-in-the-azure-portal"></a>Kurz: vytvoření připojení typu Site-to-site v Azure Portal

Brány VPN Azure poskytují propojení různých míst mezi zákazníkem a Azure. V tomto kurzu se dozvíte, jak pomocí Azure Portal vytvořit připojení brány VPN typu Site-to-Site z místní sítě k virtuální síti. Tuto konfiguraci můžete také vytvořit pomocí [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) nebo rozhraní příkazového [řádku Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md).

:::image type="content" source="./media/tutorial-site-to-site-portal/diagram.png" alt-text="Diagram připojení VPN Gateway typu Site-to-Site mezi různými místy":::

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě
> * Vytvoření brány VPN
> * Vytvoření brány místní sítě
> * Vytvoření připojení VPN
> * Ověření připojení
> * Připojení k virtuálnímu počítači

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. Pokud ho ještě nemáte, [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Ujistěte se, že máte kompatibilní zařízení VPN a někoho, kdo jej umí nakonfigurovat. Další informace o kompatibilních zařízeních VPN a konfiguraci zařízení najdete v tématu [Informace o zařízeních VPN](vpn-gateway-about-vpn-devices.md).
* Ověřte, že máte veřejnou IPv4 adresu pro vaše zařízení VPN.
* Pokud neznáte rozsahy IP adres v konfiguraci vaší místní sítě, budete se muset spojit s někým, kdo vám s tím pomůže. Při vytváření této konfigurace musíte zadat předpony rozsahu IP adres, které bude Azure směrovat do vašeho místního umístění. Žádná z podsítí vaší místní sítě se nesmí překrývat s podsítěmi virtuální sítě, ke kterým se chcete připojit.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Vytvoření virtuální sítě

Pomocí následujících hodnot vytvořte virtuální síť (VNet):

* **Skupina prostředků:** TestRG1
* **Název:** VNet1
* **Oblast:** (US) východní USA
* **Adresní prostor IPv4:** 10.1.0.0/16
* **Název podsítě:** FrontEnd
* **Adresní prostor podsítě:** 10.1.0.0/24

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>Vytvoření brány VPN

V tomto kroku vytvoříte bránu virtuální sítě pro svou virtuální síť. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány.

### <a name="about-the-gateway-subnet"></a>O podsíti brány

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="create-the-gateway"></a>Vytvoření brány

Vytvořte bránu VPN pomocí následujících hodnot:

* **Název:** VNet1GW
* **Oblast:** Východní USA
* **Typ brány:** S2S
* **Typ sítě VPN:** Založené na trasách
* **SKU:** VpnGw1
* **Generování:** Generation1
* **Virtuální síť:** VNet1
* **Rozsah adres podsítě brány:** 10.1.255.0/27
* **Veřejná IP adresa:** Vytvořit nový
* **Název veřejné IP adresy:** VNet1GWpip
* **Povolit režim aktivní-aktivní:** Zabezpečen
* **Konfigurace protokolu BGP:** Zabezpečen

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="view-the-public-ip-address"></a><a name="view"></a>Zobrazení veřejné IP adresy

Veřejnou IP adresu brány si můžete zobrazit na stránce **Přehled** pro vaši bránu.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Stránka přehledu":::

Chcete-li zobrazit další informace o objektu veřejné IP adresy, klikněte na odkaz název/IP adresa vedle **veřejné IP adresy**.

## <a name="create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>Vytvoření brány místní sítě

Brána místní sítě je konkrétní objekt, který představuje vaše místní umístění (Web) pro účely směrování. Pro toto umístění určíte název, podle kterého na něj bude Azure odkazovat, a pak zadáte IP adresu místního zařízení VPN, ke kterému vytvoříte připojení. Zadáte také předpony IP adres, které se budou přes bránu VPN směrovat do zařízení VPN. Předpony adres, které zadáte, jsou předpony ve vaší místní síti. Pokud se změní vaše místní síť nebo potřebujete změnit veřejnou IP adresu pro zařízení VPN, můžete hodnoty snadno aktualizovat později.

Vytvořte bránu místní sítě pomocí následujících hodnot:

* **Název:** Site1
* **Skupina prostředků:** TestRG1
* **Umístění:** Východní USA

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="configure-your-vpn-device"></a><a name="VPNDevice"></a>Konfigurace zařízení VPN

Připojení Site-to-Site k místní síti vyžadují zařízení VPN. V tomto kroku nakonfigurujete zařízení VPN. Při konfiguraci zařízení VPN budete potřebovat následující hodnoty:

* Sdílený klíč. Jedná se o stejný sdílený klíč, který zadáváte při vytváření připojení VPN Site-to-Site. V našich ukázkách používáme základní sdílený klíč. Doporučujeme, abyste pro použití vygenerovali složitější klíč.
* Veřejnou IP adresu vaší brány virtuální sítě. Veřejnou IP adresu můžete zobrazit pomocí webu Azure Portal, PowerShellu nebo rozhraní příkazového řádku. Pokud chcete zjistit veřejnou IP adresu vaší brány VPN pomocí Azure Portal, přejděte na **brány virtuální sítě** a potom vyberte název brány.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="create-a-vpn-connection"></a><a name="CreateConnection"></a>Vytvoření připojení VPN

Vytvořte připojení VPN typu Site-to-Site mezi bránou virtuální sítě a místním zařízením VPN.

Vytvořte připojení pomocí následujících hodnot:

* **Název brány místní sítě:** Site1
* **Název připojení:** VNet1toSite1
* **Sdílený klíč:** V tomto příkladu použijeme abc123. Můžete ale použít cokoli, co je kompatibilní s hardwarem sítě VPN. Důležité je, že si tyto hodnoty odpovídají na obou stranách připojení.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="verify-the-vpn-connection"></a><a name="VerifyConnection"></a>Ověření připojení VPN

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="connect-to-a-virtual-machine"></a><a name="connectVM"></a>Připojení k virtuálnímu počítači

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="optional-steps"></a>Volitelné kroky

### <a name="add-additional-connections-to-the-gateway"></a><a name="addconnect"></a>Přidání dalších připojení k bráně

Další připojení můžete přidat za předpokladu, že se žádné z adresních prostorů mezi připojeními nepřekrývají.

1. Pokud chcete přidat další připojení, přejděte k bráně VPN a pak vyberte **připojení** . tím otevřete stránku připojení.
1. Vyberte **+ Přidat** a přidejte své připojení. Upravit typ připojení tak, aby odpovídal typu VNet-to-VNet (pokud se připojujete k jiné bráně VNet), nebo Site-to-Site.
1. Pokud se připojujete pomocí typu Site-to-Site a ještě jste nevytvořili bránu místní sítě pro lokalitu, ke které se chcete připojit, můžete vytvořit novou.
1. Zadejte sdílený klíč, který chcete použít, a pak vyberte **OK** a vytvořte připojení.

### <a name="resize-a-gateway-sku"></a><a name="resize"></a>Změna velikosti SKU brány

Existují konkrétní pravidla týkající se změny velikosti a změny SKU brány. V této části změníme velikost SKU. Další informace najdete v tématu [nastavení brány – Změna velikosti a změny SKU](vpn-gateway-about-vpn-gateway-settings.md#resizechange).

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

### <a name="reset-a-gateway"></a><a name="reset"></a>Resetování brány

Resetování brány Azure VPN je užitečné v případě ztráty připojení VPN mezi lokalitami na jednom nebo více tunelech VPN typu Site-to-Site. V této situaci vaše místní zařízení VPN fungují správně, ale nejsou schopná vytvořit tunelová propojení prostřednictvím protokolu IPsec s branami Azure VPN.

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="additional-configuration-considerations"></a><a name="additional"></a>Další pokyny ke konfiguraci

Konfigurace S2S je možné přizpůsobit různými způsoby. Další informace najdete v následujících článcích:

* Informace o protokolu BGP najdete v tématech [Přehled protokolu BGP](vpn-gateway-bgp-overview.md) a [Postup při konfiguraci protokolu BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Informace o vynuceném tunelování najdete v tématu [Informace o vynuceném tunelování](vpn-gateway-forced-tunneling-rm.md).
* Informace o vysoce dostupných připojeních typu aktivní-aktivní najdete v tématu [Připojení s vysokou dostupností mezi jednotlivými místy a VNet-to-VNet](vpn-gateway-highlyavailable.md).
* Informace o postupu při omezení síťového provozu směřujícího do prostředků ve virtuální síti najdete v tématu [Zabezpečení sítě](../virtual-network/network-security-groups-overview.md).
* Informace o tom, jak Azure směruje provoz mezi Azure, místním prostředím a internetovými prostředky, najdete v tématu [Směrování provozu virtuální sítě](../virtual-network/virtual-networks-udr-overview.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat nebo se k dalšímu kurzu chcete vrátit, odstraňte tyto prostředky pomocí následujících kroků:

1. Do **vyhledávacího** pole v horní části portálu zadejte název vaší skupiny prostředků a vyberte ho z výsledků hledání.

1. Vyberte **Odstranit skupinu prostředků**.

1. Zadejte skupinu prostředků pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Po nakonfigurování připojení S2S můžete přidat připojení P2S ke stejné bráně.

> [!div class="nextstepaction"]
> [Připojení VPN typu Point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

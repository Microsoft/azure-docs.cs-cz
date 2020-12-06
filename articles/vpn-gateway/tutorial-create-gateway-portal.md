---
title: 'Kurz – vytvoření a Správa VPN Gateway: Azure Portal'
description: V tomto kurzu se dozvíte, jak vytvořit, nasadit a spravovat VPN Gateway Azure pomocí portálu.
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 933b71d75eacdca015a38524870f25a345e76d22
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746161"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-azure-portal"></a>Kurz: vytvoření a Správa služby VPN Gateway pomocí Azure Portal

Brány VPN Azure poskytují propojení různých míst mezi zákazníkem a Azure. Tento kurz se zabývá základními položkami nasazení brány VPN Azure, jako je vytvoření a správa brány VPN. Bránu můžete také vytvořit pomocí rozhraní příkazového [řádku Azure](create-routebased-vpn-gateway-cli.md) nebo [Azure PowerShell](create-routebased-vpn-gateway-powershell.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě
> * Vytvoření brány VPN
> * Zobrazení veřejné IP adresy brány
> * Změna velikosti služby VPN Gateway (Změna velikosti SKU)
> * Resetování brány VPN

Následující diagram ukazuje virtuální síť a bránu VPN vytvořené v rámci tohoto kurzu.

:::image type="content" source="./media/tutorial-create-gateway-portal/gateway-diagram.png" alt-text="Diagram virtuální sítě a brány VPN":::

## <a name="prerequisites"></a>Předpoklady

Účet Azure s aktivním předplatným. Pokud ho ještě nemáte, [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť s použitím následujících hodnot:

* **Skupina prostředků:** TestRG1
* **Název:** VNet1
* **Oblast:** (US) východní USA
* **Adresní prostor IPv4:** 10.1.0.0/16
* **Název podsítě:** FrontEnd
* **Adresní prostor podsítě:** 10.1.0.0/24

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>Vytvoření brány VPN

V tomto kroku vytvoříte bránu virtuální sítě pro svou virtuální síť. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány.

Vytvořte bránu virtuální sítě pomocí následujících hodnot:

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

## <a name="view-the-public-ip-address"></a><a name="view"></a>Zobrazení veřejné IP adresy

Veřejnou IP adresu brány si můžete zobrazit na stránce **Přehled** pro vaši bránu.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Stránka Přehled":::

Chcete-li zobrazit další informace o objektu veřejné IP adresy, klikněte na odkaz název/IP adresa vedle **veřejné IP adresy**.

## <a name="resize-a-gateway-sku"></a><a name="resize"></a>Změna velikosti SKU brány

Existují konkrétní pravidla týkající se změny velikosti a změny SKU brány. V této části změníme velikost SKU. Další informace najdete v tématu [nastavení brány – Změna velikosti a změny SKU](vpn-gateway-about-vpn-gateway-settings.md#resizechange).

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

## <a name="reset-a-gateway"></a><a name="reset"></a>Resetování brány

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat nebo se k dalšímu kurzu chcete vrátit, odstraňte tyto prostředky pomocí následujících kroků:

1. Do **vyhledávacího** pole v horní části portálu zadejte název vaší skupiny prostředků a vyberte ho z výsledků hledání.

1. Vyberte **Odstranit skupinu prostředků**.

1. Zadejte skupinu prostředků pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Jakmile budete mít bránu VPN, můžete nakonfigurovat připojení. Články níže vám pomůžou vytvořit několik nejběžnějších konfigurací:

> [!div class="nextstepaction"]
> [Připojení VPN typu Site-to-site](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

> [!div class="nextstepaction"]
> [Připojení VPN typu Point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

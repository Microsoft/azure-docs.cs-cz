---
title: 'Připojení místní sítě k virtuální síti Azure: síť VPN typu Site-to-site: portál'
description: Vytvořte připojení Site-to-Site VPN Gateway s protokolem IPsec z místní sítě k virtuální síti Azure přes veřejný Internet pomocí portálu.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/22/2020
ms.author: cherylmc
ms.openlocfilehash: 10cc97692e43cb46bd26597317b7f05ae6e19f83
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657189"
---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Vytvoření připojení typu Site-to-Site na webu Azure Portal

Tento článek ukazuje, jak pomocí webu Azure Portal vytvořit připojení brány VPN typu Site-to-Site z místní sítě k virtuální síti. Postupy v tomto článku se týkají modelu nasazení Resource Manager. Tuto konfiguraci můžete vytvořit také pomocí jiného nástroje nasazení nebo pro jiný model nasazení, a to výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Rozhraní příkazového řádku](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-site-to-site-classic-portal.md)
>

Připojení brány VPN typu Site-to-Site slouží k připojení místní sítě k virtuální síti Azure přes tunel VPN IPsec/IKE (IKEv1 nebo IKEv2). Tento typ připojení vyžaduje místní zařízení VPN, které má přiřazenou veřejnou IP adresu. Další informace o bránách VPN najdete v tématu [Informace o službě VPN Gateway](vpn-gateway-about-vpngateways.md).

:::image type="content" source="./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png" alt-text="Diagram připojení VPN Gateway typu Site-to-Site mezi různými místy":::

## <a name="prerequisites"></a>Požadavky

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

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

## <a name="how-to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Jak se připojit k virtuálnímu počítači

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Resetování brány VPN

Resetování brány Azure VPN je užitečné v případě ztráty připojení VPN mezi lokalitami na jednom nebo více tunelech VPN typu Site-to-Site. V této situaci vaše místní zařízení VPN fungují správně, ale nejsou schopná vytvořit tunelová propojení prostřednictvím protokolu IPsec s branami Azure VPN. Pokyny najdete v tématu [Resetování brány VPN](reset-gateway.md).

## <a name="how-to-change-a-gateway-sku-resize-a-gateway"></a><a name="resize"></a>Změna skladové položky brány (změna velikosti brány)

Postup pro změnu skladové položky brány najdete v tématu popisujícím [Skladové položky brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="how-to-add-an-additional-connection-to-a-vpn-gateway"></a><a name="addconnect"></a>Postup přidání dalšího připojení k bráně VPN

Další připojení můžete přidat za předpokladu, že se žádné z adresních prostorů mezi připojeními nepřekrývají.

1. Pokud chcete přidat další připojení, přejděte k bráně VPN a pak vyberte **připojení** . tím otevřete stránku připojení.
1. Vyberte **+ Přidat** a přidejte své připojení. Upravit typ připojení tak, aby odpovídal typu VNet-to-VNet (pokud se připojujete k jiné bráně VNet), nebo Site-to-Site.
1. Pokud se připojujete pomocí typu Site-to-Site a ještě jste nevytvořili bránu místní sítě pro lokalitu, ke které se chcete připojit, můžete vytvořit novou.
1. Zadejte sdílený klíč, který chcete použít, a pak vyberte **OK** a vytvořte připojení.

## <a name="next-steps"></a>Další kroky

* Informace o protokolu BGP najdete v tématech [Přehled protokolu BGP](vpn-gateway-bgp-overview.md) a [Postup při konfiguraci protokolu BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Informace o vynuceném tunelování najdete v tématu [Informace o vynuceném tunelování](vpn-gateway-forced-tunneling-rm.md).
* Informace o vysoce dostupných připojeních typu aktivní-aktivní najdete v tématu [Připojení s vysokou dostupností mezi jednotlivými místy a VNet-to-VNet](vpn-gateway-highlyavailable.md).
* Informace o postupu při omezení síťového provozu směřujícího do prostředků ve virtuální síti najdete v tématu [Zabezpečení sítě](../virtual-network/network-security-groups-overview.md).
* Informace o tom, jak Azure směruje provoz mezi Azure, místním prostředím a internetovými prostředky, najdete v tématu [Směrování provozu virtuální sítě](../virtual-network/virtual-networks-udr-overview.md).
* Informace o vytvoření připojení VPN typu Site-to-Site pomocí šablony Azure Resource Manageru najdete v tématu [Vytvoření připojení VPN typu Site-to-Site](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Informace o vytvoření připojení VPN typu VNet-to-VNet pomocí šablony Azure Resource Manager najdete v tématu [nasazení HBA pro geografickou replikaci](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).
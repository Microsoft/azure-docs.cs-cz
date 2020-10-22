---
title: 'Připojení místní sítě k virtuální síti Azure: síť VPN typu Site-to-site: portál'
description: Vytvořte připojení Site-to-Site VPN Gateway s protokolem IPsec z místní sítě k virtuální síti Azure přes veřejný Internet pomocí portálu.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/10/2020
ms.author: cherylmc
ms.openlocfilehash: bbfd00af62f783b0f5122ddb59b921c1ad9e7019
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92331222"
---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Vytvoření připojení typu Site-to-Site na webu Azure Portal

Tento článek ukazuje, jak pomocí webu Azure Portal vytvořit připojení brány VPN typu Site-to-Site z místní sítě k virtuální síti. Postupy v tomto článku se týkají modelu nasazení Resource Manager. Tuto konfiguraci můžete vytvořit také pomocí jiného nástroje nasazení nebo pro jiný model nasazení, a to výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Rozhraní příkazového řádku](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Připojení brány VPN typu Site-to-Site slouží k připojení místní sítě k virtuální síti Azure přes tunel VPN IPsec/IKE (IKEv1 nebo IKEv2). Tento typ připojení vyžaduje místní zařízení VPN, které má přiřazenou veřejnou IP adresu. Další informace o bránách VPN najdete v tématu [Informace o službě VPN Gateway](vpn-gateway-about-vpngateways.md).

![Diagram připojení VPN Gateway typu Site-to-Site mezi různými místy](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* Ujistěte se, že máte kompatibilní zařízení VPN a někoho, kdo jej umí nakonfigurovat. Další informace o kompatibilních zařízeních VPN a konfiguraci zařízení najdete v tématu [Informace o zařízeních VPN](vpn-gateway-about-vpn-devices.md).
* Ověřte, že máte veřejnou IPv4 adresu pro vaše zařízení VPN.
* Pokud neznáte rozsahy IP adres v konfiguraci vaší místní sítě, budete se muset spojit s někým, kdo vám s tím pomůže. Při vytváření této konfigurace musíte zadat předpony rozsahu IP adres, které bude Azure směrovat do vašeho místního umístění. Žádná z podsítí vaší místní sítě se nesmí překrývat s podsítěmi virtuální sítě, ke kterým se chcete připojit. 

### <a name="example-values"></a><a name="values"></a>Příklady hodnot

V příkladech v tomto článku se používají následující hodnoty. Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku. Další celkové informace o nastavení VPN Gateway najdete v tématu [Informace o nastavení služby VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).

* **Název virtuální sítě:** VNet1
* **Adresní prostor:** 10.1.0.0/16
* **Předplatné:** Vyberte předplatné, které chcete použít.
* **Skupina prostředků:** TestRG1
* **Oblast:** Východní USA
* **Podsíť:** Front-end: 10.1.0.0/24, Back-end: 10.1.1.0/24 (volitelné pro toto cvičení)
* **Rozsah adres podsítě brány:** 10.1.255.0/27
* **Název brány virtuální sítě:** VNet1GW
* **Název veřejné IP adresy:** VNet1GWpip
* **Typ sítě VPN:** Založené na trasách
* **Typ připojení:** Síť Site-to-Site (IPsec)
* **Typ brány:** S2S
* **Název brány místní sítě:** Site1
* **Název připojení:** VNet1toSite1
* **Sdílený klíč:** V tomto příkladu použijeme abc123. Můžete ale použít cokoli, co je kompatibilní s hardwarem sítě VPN. Důležité je, že si tyto hodnoty odpovídají na obou stranách připojení.

## <a name="1-create-a-virtual-network"></a><a name="CreatVNet"></a>1. vytvoření virtuální sítě

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="2-create-the-vpn-gateway"></a><a name="VNetGateway"></a>2. Vytvoření brány VPN

V tomto kroku vytvoříte bránu virtuální sítě pro svou virtuální síť. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="example-settings"></a>Příklad nastavení

* **Podrobnosti Instance > oblasti:** Východní USA
* **Virtuální síť Virtual Network >:** VNet1
* **Podrobnosti Instance > název:** VNet1GW
* **Podrobnosti Instance > typ brány:** S2S
* **Podrobnosti Instance > typ sítě VPN:** Založené na trasách
* **Rozsah adres podsítě Virtual Network > brány:** 10.1.255.0/27
* **Veřejná IP adresa > název veřejné IP adresy:** VNet1GWpip

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]


## <a name="3-create-the-local-network-gateway"></a><a name="LocalNetworkGateway"></a>3. Vytvoření brány místní sítě

Brána místní sítě je konkrétní objekt, který představuje vaše místní umístění (Web) pro účely směrování. Pro toto umístění určíte název, podle kterého na něj bude Azure odkazovat, a pak zadáte IP adresu místního zařízení VPN, ke kterému vytvoříte připojení. Zadáte také předpony IP adres, které se budou přes bránu VPN směrovat do zařízení VPN. Předpony adres, které zadáte, jsou předpony ve vaší místní síti. Pokud se změní vaše místní síť nebo potřebujete změnit veřejnou IP adresu pro zařízení VPN, můžete hodnoty snadno aktualizovat později.

**Příklady hodnot**

* **Název:** Site1
* **Skupina prostředků:** TestRG1
* **Umístění:** Východní USA


[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="4-configure-your-vpn-device"></a><a name="VPNDevice"></a>4. konfigurace zařízení VPN

Připojení Site-to-Site k místní síti vyžadují zařízení VPN. V tomto kroku nakonfigurujete zařízení VPN. Při konfiguraci zařízení VPN potřebujete následující:

- Sdílený klíč. Jedná se o stejný sdílený klíč, který zadáváte při vytváření připojení VPN Site-to-Site. V našich ukázkách používáme základní sdílený klíč. Doporučujeme, abyste pro použití vygenerovali složitější klíč.
- Veřejnou IP adresu vaší brány virtuální sítě. Veřejnou IP adresu můžete zobrazit pomocí webu Azure Portal, PowerShellu nebo rozhraní příkazového řádku. Pokud chcete zjistit veřejnou IP adresu brány VPN pomocí webu Azure Portal, přejděte na **Brány virtuální sítě** a klikněte na název brány.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="5-create-the-vpn-connection"></a><a name="CreateConnection"></a>5. vytvoření připojení k síti VPN

Vytvořte připojení VPN typu Site-to-Site mezi bránou virtuální sítě a místním zařízením VPN.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="6-verify-the-vpn-connection"></a><a name="VerifyConnection"></a>6. Ověřte připojení VPN.

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Připojení k virtuálnímu počítači

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Resetování brány VPN

Resetování brány Azure VPN je užitečné v případě ztráty připojení VPN mezi lokalitami na jednom nebo více tunelech VPN typu Site-to-Site. V této situaci vaše místní zařízení VPN fungují správně, ale nejsou schopná vytvořit tunelová propojení prostřednictvím protokolu IPsec s branami Azure VPN. Pokyny najdete v tématu [Resetování brány VPN](vpn-gateway-resetgw-classic.md).

## <a name="how-to-change-a-gateway-sku-resize-a-gateway"></a><a name="resize"></a>Změna skladové položky brány (změna velikosti brány)

Postup pro změnu skladové položky brány najdete v tématu popisujícím [Skladové položky brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="how-to-add-an-additional-connection-to-a-vpn-gateway"></a><a name="addconnect"></a>Postup přidání dalšího připojení k bráně VPN

Další připojení můžete přidat za předpokladu, že se žádné z adresních prostorů mezi připojeními nepřekrývají.

1. Pokud chcete přidat další připojení, přejděte k bráně VPN a potom kliknutím na **Připojení** otevřete stránku Připojení.
2. Kliknutím na **+Přidat** přidáte vaše připojení. Upravit typ připojení tak, aby odpovídal typu VNet-to-VNet (pokud se připojujete k jiné bráně VNet), nebo Site-to-Site.
3. Pokud se připojujete pomocí typu Site-to-Site a ještě jste nevytvořili bránu místní sítě pro lokalitu, ke které se chcete připojit, můžete vytvořit novou.
4. Zadejte sdílený klíč, který chcete použít, a potom kliknutím na **OK** vytvořte připojení.

## <a name="next-steps"></a>Další kroky

* Informace o protokolu BGP najdete v tématech [Přehled protokolu BGP](vpn-gateway-bgp-overview.md) a [Postup při konfiguraci protokolu BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Informace o vynuceném tunelování najdete v tématu [Informace o vynuceném tunelování](vpn-gateway-forced-tunneling-rm.md).
* Informace o vysoce dostupných připojeních typu aktivní-aktivní najdete v tématu [Připojení s vysokou dostupností mezi jednotlivými místy a VNet-to-VNet](vpn-gateway-highlyavailable.md).
* Informace o postupu při omezení síťového provozu směřujícího do prostředků ve virtuální síti najdete v tématu [Zabezpečení sítě](../virtual-network/security-overview.md).
* Informace o tom, jak Azure směruje provoz mezi Azure, místním prostředím a internetovými prostředky, najdete v tématu [Směrování provozu virtuální sítě](../virtual-network/virtual-networks-udr-overview.md).
* Informace o vytvoření připojení VPN typu Site-to-Site pomocí šablony Azure Resource Manageru najdete v tématu [Vytvoření připojení VPN typu Site-to-Site](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Informace o vytvoření připojení VPN typu VNet-to-VNet pomocí šablony Azure Resource Manageru najdete v tématu [Nasazení geografické replikace HBase](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).

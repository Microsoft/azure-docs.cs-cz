---
title: 'Konfigurace připojení typu VNet-to-VNet VPN Gateway: Azure Portal'
description: Vytvořte připojení brány VPN mezi virtuálními sítěmi pomocí Resource Manageru a webu Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/19/2020
ms.author: cherylmc
ms.openlocfilehash: 723d93b9a5e986501278bdee35835cfa0c234711
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555840"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Konfigurace připojení brány VPN typu VNet-to-VNet pomocí Azure Portal

Tento článek vám pomůže propojit virtuální sítě (virtuální sítě) pomocí typu připojení VNet-to-VNet. Virtuální sítě mohou být v různých oblastech a z různých předplatných. Když připojíte virtuální sítě z různých předplatných, nemusíte být přidružení ke stejnému tenantovi Active Directory. 

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet-vnet-diagram.png" alt-text="Diagram virtuální sítě k virtuální síti":::

Kroky v tomto článku se týkají modelu nasazení Azure Resource Manager a používají Azure Portal. Tuto konfiguraci můžete vytvořit pomocí jiného nástroje nebo modelu nasazení pomocí možností, které jsou popsány v následujících článcích:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Propojení různých modelů nasazení – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Propojení různých modelů nasazení – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>Informace o propojování virtuálních sítí

Následující oddíly popisují různé způsoby připojení virtuálních sítí.

### <a name="vnet-to-vnet"></a>VNet-to-VNet

Konfigurace připojení typu VNet-to-VNet je jednoduchý způsob, jak připojit virtuální sítě. Když připojíte virtuální síť k jiné virtuální síti s typem připojení typu VNet-to-VNet (VNet2VNet), je to podobné jako vytvoření připojení typu Site-to-site protokolu IPsec k místnímu umístění. Oba typy připojení používají bránu VPN k poskytnutí zabezpečeného tunelového propojení s protokolem IPsec/IKE a fungují stejně jako při komunikaci. Liší se ale v tom, jak je nakonfigurovaná brána místní sítě. 

Při vytváření připojení typu VNet-to-VNet se automaticky vytvoří a naplní adresní prostor brány místní sítě. Pokud aktualizujete adresní prostor pro jednu virtuální síť, druhá virtuální síť automaticky směruje do aktualizovaného adresního prostoru. Obvykle je rychlejší a jednodušší vytvořit připojení typu VNet-to-VNet než připojení typu Site-to-site.

### <a name="site-to-site-ipsec"></a>Site-to-Site (IPsec)

Pokud pracujete se složitou konfigurací sítě, můžete raději připojit virtuální sítě pomocí [připojení typu Site-to-site](vpn-gateway-howto-site-to-site-resource-manager-portal.md) . Když budete postupovat podle pokynů protokolu IPsec pro lokalitu, vytvoříte a nakonfigurujete brány místní sítě ručně. Brána místní sítě pro každou virtuální síť zpracovává jiné virtuální sítě jako místní síť. Tyto kroky umožňují zadat další adresní prostory pro bránu místní sítě pro směrování provozu. Pokud se adresní prostor pro virtuální síť změní, musíte ručně aktualizovat odpovídající bránu místní sítě.

### <a name="vnet-peering"></a>Partnerské vztahy virtuálních sítí

Virtuální sítě můžete také připojit pomocí partnerského vztahu virtuálních sítí. VNet peering nepoužívá bránu VPN a má odlišná omezení. Kromě toho [ceny pro VNET Peering](https://azure.microsoft.com/pricing/details/virtual-network) se vypočítávají odlišně než [ceny pro VNet-to-VNet VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway). Další informace najdete v tématu [Partnerské vztahy virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Proč vytvářet připojení typu VNet-to-VNet?

Virtuální sítě můžete chtít propojit pomocí připojení typu VNet-to-VNet z následujících důvodů:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Geografická redundance a geografická přítomnost mezi oblastmi

* Můžete nastavit svou vlastní geografickou replikaci nebo synchronizaci se zabezpečeným připojením bez přechodu přes internetové koncové body.
* Pomocí Azure Traffic Manager a Azure Load Balancer můžete nastavit úlohu s vysokou dostupností s geografickou redundancí napříč několika oblastmi Azure. Ve více oblastech Azure můžete například nastavit SQL Server skupiny dostupnosti Always On.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Regionální vícevrstvé aplikace s hranicemi izolace nebo správy

* V rámci stejné oblasti můžete nastavit vícevrstvé aplikace s několika virtuálními sítěmi, které jsou propojeny společně s požadavky na izolaci nebo správu.

Komunikaci typu VNet-to-VNet můžete kombinovat s konfiguracemi s více servery. Tyto konfigurace umožňují vytvářet síťové topologie, které kombinují připojení mezi různými místy pomocí připojení mezi virtuálními sítěmi, jak je znázorněno v následujícím diagramu:

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections-diagram.png" alt-text="Diagram připojení virtuální sítě":::

V tomto článku se dozvíte, jak připojit virtuální sítě pomocí typu připojení VNet-to-VNet. Pokud budete postupovat podle těchto kroků jako cvičení, můžete použít následující příklady hodnot nastavení. V tomto příkladu jsou virtuální sítě ve stejném předplatném, ale v různých skupinách prostředků. Pokud jsou vaše virtuální sítě v různých předplatných, nelze vytvořit připojení na portálu. Místo toho použijte [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) nebo [CLI](vpn-gateway-howto-vnet-vnet-cli.md) . Další informace o připojeních VNet-to-VNet najdete v tématu [Nejčastější dotazy k VNet-to-VNet](#vnet-to-vnet-faq).

### <a name="example-settings"></a>Příklad nastavení

**Hodnoty pro VNet1:**

* **Nastavení virtuální sítě**
  * **Název** : VNet1
  * **Adresní prostor** : 10.1.0.0/16
  * **Předplatné** : vyberte předplatné, které chcete použít.
  * **Skupina prostředků** : TestRG1
  * **Umístění** : východní USA
  * **Podsíť**
    * **Název** : front-end
    * **Rozsah adres** : 10.1.0.0/24

* **Nastavení brány virtuální sítě**
  * **Název** : VNet1GW
  * **Skupina prostředků** : východní USA
  * **Generování** : generace 1
  * **Typ brány** : Vyberte **VPN**.
  * **Typ sítě VPN** : vyberte **směrování * založené na**.
  * **SKU** : VpnGw1
  * **Virtuální síť** : VNet1
  * **Rozsah adres podsítě brány** : 10.1.255.0/27
  * **Veřejná IP adresa** : vytvořit nový
  * **Název veřejné IP adresy** : VNet1GWpip

* **Připojení**
  * **Název** : VNet1toVNet4
  * **Sdílený klíč** : sdílený klíč můžete vytvořit sami. Když vytvoříte připojení mezi virtuální sítě, hodnoty se musí shodovat. Pro toto cvičení použijte abc123.

**Hodnoty pro VNet4:**

* **Nastavení virtuální sítě**
  * **Název** : VNet4
  * **Adresní prostor** : 10.41.0.0/16
  * **Předplatné** : vyberte předplatné, které chcete použít.
  * **Skupina prostředků** : TestRG4
  * **Umístění** : západní USA
  * **Podsíť**
  * **Název** : front-end
  * **Rozsah adres** : 10.41.0.0/24

* **Nastavení brány virtuální sítě**
  * **Název** : VNet4GW
  * **Skupina prostředků** : západní USA
  * **Generování** : generace 1
  * **Typ brány** : Vyberte **VPN**.
  * **Typ sítě VPN** : vyberte **směrování založené na trasách**.
  * **SKU** : VpnGw1
  * **Virtuální síť** : VNet4
  * **Rozsah adres podsítě brány** : 10.41.255.0/27
  * **Veřejná IP adresa** : vytvořit nový
  * **Název veřejné IP adresy** : VNet4GWpip

* **Připojení**
  * **Název** : VNet4toVNet1
  * **Sdílený klíč** : sdílený klíč můžete vytvořit sami. Když vytvoříte připojení mezi virtuální sítě, hodnoty se musí shodovat. Pro toto cvičení použijte abc123.

## <a name="create-and-configure-vnet1"></a>Vytvoření a konfigurace VNet1

Pokud již máte virtuální síť vytvořenou, ověřte, zda jsou nastavení kompatibilní s vaším návrhem brány VPN. Věnujte zvláštní pozornost všem podsítím, které by se mohly překrývat s jinými sítěmi. Pokud máte překrývající se podsítě, vaše připojení nebude fungovat správně.

### <a name="to-create-a-virtual-network"></a>Chcete-li vytvořit virtuální síť

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vnet1-gateway"></a>Vytvoření brány VNet1

V tomto kroku vytvoříte bránu virtuální sítě pro svou virtuální síť. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány. Pokud vytváříte tuto konfiguraci jako cvičení, přečtěte si [Příklad nastavení](#example-settings).

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>Chcete-li vytvořit bránu virtuální sítě

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>Vytvoření a konfigurace VNet4

Po nakonfigurování VNet1 vytvořte VNet4 a VNet4 bránu tak, že zopakujete předchozí kroky a nahradíte hodnoty hodnotami VNet4. Před konfigurací VNet4 nemusíte čekat, dokud se nedokončí vytváření brány virtuální sítě pro VNet1. Pokud používáte vlastní hodnoty, ujistěte se, že se adresní prostory nepřekrývají s žádnými virtuální sítě, ke kterým se chcete připojit.

## <a name="configure-the-vnet1-gateway-connection"></a>Konfigurace připojení brány VNet1

Po dokončení bran virtuální sítě pro VNet1 i VNet4 můžete vytvořit připojení brány virtuální sítě. V této části vytvoříte připojení z VNet1 do VNet4. Tyto kroky fungují pouze u virtuálních sítí ve stejném předplatném. Pokud jsou vaše virtuální sítě v různých předplatných, musíte k vytvoření připojení použít [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) . Pokud se ale vaše virtuální sítě nacházejí v různých skupinách prostředků ve stejném předplatném, můžete je propojit pomocí portálu.

1. V Azure Portal vyberte **všechny prostředky** , do vyhledávacího pole zadejte *Brána virtuální sítě* a potom přejděte k bráně virtuální sítě pro vaši virtuální síť. Například **VNet1GW**. Vyberte bránu, abyste otevřeli stránku **brány virtuální sítě** .
1. Na stránce Brána otevřete **Nastavení->připojení**. Pak vyberte **+ Přidat**.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png" alt-text="Stránka připojení":::
1. Otevře se stránka **Přidat připojení** .

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-vnet4.png" alt-text="Přidat připojení":::

   Na stránce **Přidat připojení** vyplňte hodnoty pro vaše připojení:

   * **Název** : zadejte název připojení. Například *VNet1toVNet4*.

   * **Typ připojení** : v rozevíracím seznamu vyberte **VNet-to-VNet** .

   * **První Brána virtuální sítě** : hodnota tohoto pole se vyplní automaticky, protože vytváříte připojení ze zadané brány virtuální sítě.

   * **Druhá Brána virtuální sítě** : Toto pole je branou virtuální sítě virtuální sítě, ke které chcete vytvořit připojení. Vyberte vybrat **jinou bránu virtuální sítě** a otevřete stránku **Vybrat bránu virtuální sítě** .

      :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/choose.png" alt-text="Výběr brány":::

     * Prohlédněte si brány virtuálních sítí uvedené na této stránce. Všimněte si, že jsou uvedené pouze brány virtuálních sítí v rámci vašeho předplatného. Pokud se chcete připojit k bráně virtuální sítě, která není ve vašem předplatném, použijte [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

     * Vyberte bránu virtuální sítě, ke které se chcete připojit.

   * **Shared Key (PSK)** : do tohoto pole zadejte sdílený klíč pro vaše připojení. Tento klíč si můžete vygenerovat nebo vytvořit sami. V případě připojení typu Site-to-site je klíč, který použijete, stejný pro vaše místní zařízení a připojení brány virtuální sítě. Tento koncept je podobný tomuto: s tím rozdílem, že místo připojení k zařízení VPN se připojujete k jiné bráně virtuální sítě.
1. Výběrem **OK** uložte změny.

## <a name="configure-the-vnet4-gateway-connection"></a>Konfigurace připojení brány VNet4

Potom vytvořte připojení z VNet4 k VNet1. Na portálu vyhledejte bránu virtuální sítě přidruženou k VNet4. Postupujte podle kroků v předchozí části a nahraďte hodnoty pro vytvoření připojení z VNet4 k VNet1. Ujistěte se, že používáte stejný sdílený klíč.

## <a name="verify-your-connections"></a>Zkontrolujte svá připojení

1. Vyhledejte bránu virtuální sítě v Azure Portal. 
1. Na stránce **Brána virtuální sítě** vyberte **připojení** , aby se zobrazila stránka **připojení** pro bránu virtuální sítě. Po navázání spojení uvidíte hodnoty **stavu** se změní na **připojeno**.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png" alt-text="Ověřit připojení":::
1. Ve sloupci **název** vyberte jedno z připojení pro zobrazení dalších informací. Po zahájení toku dat uvidíte hodnoty pro **data v** a **data odchozí**.

   :::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png" alt-text="Snímek obrazovky znázorňující skupinu prostředků s hodnotami pro data v a z dat":::

## <a name="add-additional-connections"></a>Přidat další připojení

Pokud chcete přidat další připojení, přejděte k bráně virtuální sítě, ze které chcete vytvořit připojení, a pak vyberte **připojení**. Můžete vytvořit další připojení VNet-to-VNet nebo vytvořit připojení IPsec Site-to-Site k místnímu umístění. Nezapomeňte upravit **Typ připojení** tak, aby odpovídal typu připojení, které chcete vytvořit. Než vytvoříte další připojení, ověřte, že se adresní prostor pro virtuální síť nepřekrývá s žádným z adresních prostorů, ke kterým se chcete připojit. Postup vytvoření připojení Site-to-Site najdete v tématu [Vytvoření připojení typu Site-to-Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>Nejčastější dotazy týkající se propojení VNet-to-VNet

Projděte si Nejčastější dotazy, kde najdete další informace o propojeních VNet-to-VNet.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak můžete omezit síťový provoz na prostředky ve virtuální síti, najdete v tématu [zabezpečení sítě](../virtual-network/security-overview.md).

* Informace o tom, jak Azure směruje provoz mezi Azure, místním prostředím a internetovými prostředky, najdete v tématu [Směrování provozu virtuální sítě](../virtual-network/virtual-networks-udr-overview.md).

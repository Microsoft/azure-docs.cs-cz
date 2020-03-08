---
title: 'Konfigurace připojení typu VNet-to-VNet VPN Gateway: Azure Portal'
description: Vytvořte připojení brány VPN mezi virtuálními sítěmi pomocí Resource Manageru a webu Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: cherylmc
ms.openlocfilehash: 3d91203253c08acdaa159fc70f7a34fa7fca20c8
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674162"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Konfigurace připojení brány VPN typu VNet-to-VNet pomocí webu Azure portal

Tento článek vám pomůže propojit virtuální sítě (Vnet) s použitím typu připojení VNet-to-VNet. Virtuální sítě může být v různých oblastech a z různých předplatných. Při propojení virtuálních sítí z různých předplatných, předplatná nemusí být přidružená stejnému tenantu Active Directory. 

![Diagram v2v](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

Kroky v tomto článku se týkají modelu nasazení Azure Resource Manageru a pomocí webu Azure portal. Tuto konfiguraci můžete vytvořit pomocí jiného nástroje nasazení nebo model s použitím možností, které jsou popsány v následujících článcích:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Propojení různých modelů nasazení – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Propojení různých modelů nasazení – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>Informace o propojení virtuálních sítí

Následující části popisují různé způsoby, jak propojit virtuální sítě.

### <a name="vnet-to-vnet"></a>VNet-to-VNet

Konfigurace připojení typu VNet-to-VNet je jednoduchý způsob, jak propojit virtuální sítě. Při připojení virtuální sítě s jinou virtuální sítí s typem připojení VNet-to-VNet (VNet2VNet) je podobné jako vytvoření připojení Site-to-Site IPsec k místnímu umístění. Oba typy připojení využívají bránu VPN k poskytování zabezpečeného tunelového propojení pomocí protokolu IPsec/IKE a komunikují stejným způsobem. Ale liší se ve způsobu, jak nakonfigurovat bránu místní sítě. 

Při vytváření připojení typu VNet-to-VNet je automaticky vytvořen a vyplní adresní prostor místní síťové brány. Pokud aktualizujete adresní prostor pro jednu virtuální síť, druhá virtuální síť se automaticky směruje do aktualizovaného adresního prostoru. To je obvykle rychlejší a snazší vytvářet připojení typu VNet-to-VNet připojení než připojení Site-to-Site.

### <a name="site-to-site-ipsec"></a>Site-to-Site (IPsec)

Pokud pracujete se složitou konfigurací sítě, můžete raději připojit virtuální sítě pomocí [připojení typu Site-to-site](vpn-gateway-howto-site-to-site-resource-manager-portal.md) . Když budete postupovat podle kroků Site-to-Site IPsec, vytvoříte a nakonfigurujete brány místní sítě ručně. Brána místní sítě pro každou virtuální síť zpracovává jiné virtuální sítě jako místní síť. Tyto kroky umožňují zadat další adresní prostory pro bránu místní sítě pro směrování provozu. Pokud se adresní prostor pro virtuální síť změní, musíte ručně aktualizovat odpovídající bránu místní sítě.

### <a name="vnet-peering"></a>Partnerské vztahy virtuálních sítí

Vaše virtuální sítě můžete připojit také pomocí VNet peering. VNet peering nepoužívá bránu sítě VPN a má jiná omezení. Kromě toho [ceny pro VNET Peering](https://azure.microsoft.com/pricing/details/virtual-network) se vypočítávají odlišně než [ceny pro VNet-to-VNet VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway). Další informace najdete v tématu [Partnerské vztahy virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Proč vytvářet připojení typu VNet-to-VNet?

Může být vhodné k propojení virtuálních sítí s použitím připojení typu VNet-to-VNet z následujících důvodů:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Geografická redundance a geografická přítomnost mezi oblastmi

  * Můžete nastavit vlastní geografickou replikaci nebo synchronizaci se zabezpečeným připojením bez nutnosti prostřednictvím internetových koncových bodů.
  * S Azure Traffic Manageru a služby Azure Load Balancer můžete nastavit úlohy s vysokou dostupností s geografickou redundancí nad několika oblastmi Azure. Můžete například nastavit skupiny dostupnosti AlwaysOn SQL serveru ve víc oblastech Azure.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Regionální vícevrstvé aplikace s izolací nebo hranice správy.

  * V rámci stejné oblasti můžete nastavit vícevrstvé aplikace s více virtuálními sítěmi propojenými z důvodu izolace nebo požadavků na správu.

Komunikaci typu VNet-to-VNet můžete kombinovat s konfiguracemi s více servery. Tato konfigurace umožňuje vytvářet topologie sítí, které kombinují připojení mezi lokalitami s připojením mezi virtuálními sítěmi, jak je znázorněno v následujícím diagramu:

![O připojeních](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Informace o připojeních")

Tento článek ukazuje, jak propojit virtuální sítě s použitím typu připojení VNet-to-VNet. Pokud jste provedli následující kroky jako cvičení, můžete použít následující ukázkové hodnoty nastavení. V tomto příkladu jsou virtuální sítě ve stejném předplatném, ale v různých skupinách prostředků. Pokud jsou vaše virtuální sítě v různých předplatných, nelze vytvořit připojení na portálu. Místo toho použijte [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) nebo [CLI](vpn-gateway-howto-vnet-vnet-cli.md) . Další informace o připojeních VNet-to-VNet najdete v tématu [Nejčastější dotazy k VNet-to-VNet](#vnet-to-vnet-faq).

### <a name="example-settings"></a>Příklad nastavení

**Hodnoty pro VNet1:**

- **Nastavení virtuální sítě**
    - **Název**: VNet1
    - **Adresní prostor**: 10.1.0.0/16
    - **Předplatné**: vyberte předplatné, které chcete použít.
    - **Skupina prostředků**: TestRG1
    - **Umístění**: východní USA
    - **Podsíť**
        - **Název**: front-end
        - **Rozsah adres**: 10.1.0.0/24
    - **Podsíť brány**:
        - **Název**: *GatewaySubnet* je vyplněný.
        - **Rozsah adres**: 10.1.255.0/27

- **Nastavení brány virtuální sítě**
    - **Název**: VNet1GW
    - **Typ brány**: Vyberte **VPN**.
    - **Typ sítě VPN**: vyberte **směrování založené na trasách**.
    - **SKU**: vyberte SKU brány, kterou chcete použít.
    - **Název veřejné IP adresy**: VNet1GWpip
    - **Vázán**
       - **Název**: VNet1toVNet4
       - **Sdílený klíč**: sdílený klíč můžete vytvořit sami. Když vytvoříte připojení mezi virtuálními sítěmi, hodnoty musí odpovídat. Pro toto cvičení použijte abc123.

**Hodnoty pro VNet4:**

- **Nastavení virtuální sítě**
   - **Název**: VNet4
   - **Adresní prostor**: 10.41.0.0/16
   - **Předplatné**: vyberte předplatné, které chcete použít.
   - **Skupina prostředků**: TestRG4
   - **Umístění**: západní USA
   - **Podsíť** 
      - **Název**: front-end
      - **Rozsah adres**: 10.41.0.0/24
   - **GatewaySubnet** 
      - **Název**: *GatewaySubnet* je vyplněný.
      - **Rozsah adres**: 10.41.255.0/27

- **Nastavení brány virtuální sítě** 
    - **Název**: VNet4GW
    - **Typ brány**: Vyberte **VPN**.
    - **Typ sítě VPN**: vyberte **směrování založené na trasách**.
    - **SKU**: vyberte SKU brány, kterou chcete použít.
    - **Název veřejné IP adresy**: VNet4GWpip
    - **Vázán** 
       - **Název**: VNet4toVNet1
       - **Sdílený klíč**: sdílený klíč můžete vytvořit sami. Když vytvoříte připojení mezi virtuálními sítěmi, hodnoty musí odpovídat. Pro toto cvičení použijte abc123.

## <a name="create-and-configure-vnet1"></a>Vytvoření a konfigurace VNet1
Pokud již máte virtuální síť vytvořenou, ověřte, zda jsou nastavení kompatibilní s vaším návrhem brány VPN. Věnujte zvláštní pozornost všem podsítím, které by se mohly překrývat s jinými sítěmi. Vaše připojení nebude fungovat správně, pokud máte překrývající se podsítí.

### <a name="to-create-a-virtual-network"></a>Chcete-li vytvořit virtuální síť
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vnet1-gateway"></a>Vytvoření brány VNet1
V tomto kroku vytvoříte bránu virtuální sítě pro svou virtuální síť. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány. Pokud vytváříte tuto konfiguraci jako cvičení, přečtěte si [Příklad nastavení](#example-settings).

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>Chcete-li vytvořit bránu virtuální sítě
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>Vytvoření a konfigurace VNet4
Po nakonfigurování VNet1 vytvořte VNet4 a VNet4 bránu tak, že zopakujete předchozí kroky a nahradíte hodnoty hodnotami VNet4. Před konfigurací VNet4 nemusíte čekat, dokud se nedokončí vytváření brány virtuální sítě pro VNet1. Pokud používáte vlastní hodnoty, ujistěte se, že se že adresní prostory nepřekrývaly s žádnou z virtuálních sítí, ke kterému chcete připojit.

## <a name="configure-the-vnet1-gateway-connection"></a>Konfigurace připojení brány VNet1
Po dokončení bran virtuální sítě pro VNet1 i VNet4 můžete vytvořit připojení brány virtuální sítě. V této části vytvoříte připojení z VNet1 do VNet4. Tyto kroky fungují pouze u virtuálních sítí ve stejném předplatném. Pokud jsou vaše virtuální sítě v různých předplatných, musíte k vytvoření připojení použít [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) . Ale pokud jsou vaše virtuální sítě v různých skupinách prostředků ve stejném předplatném, můžete je propojit pomocí portálu.

1. V Azure Portal vyberte **všechny prostředky**, do vyhledávacího pole zadejte *Brána virtuální sítě* a potom přejděte k bráně virtuální sítě pro vaši virtuální síť. Například **VNet1GW**. Vyberte bránu, abyste otevřeli stránku **brány virtuální sítě** . V části **Nastavení**vyberte **připojení**.

   ![Stránka připojení](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png "Stránka připojení")
2. Výběrem **+ Přidat** otevřete stránku **Přidat připojení** .

   ![Přidat připojení](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-vnet4-connection.png "Přidat připojení")
3. Na stránce **Přidat připojení** vyplňte hodnoty pro vaše připojení:

   - **Název**: zadejte název připojení. Například *VNet1toVNet4*.

   - **Typ připojení**: v rozevíracím seznamu vyberte **VNet-to-VNet** .

   - **První Brána virtuální sítě**: hodnota tohoto pole se vyplní automaticky, protože vytváříte připojení ze zadané brány virtuální sítě.

   - **Druhá Brána virtuální sítě**: Toto pole je branou virtuální sítě virtuální sítě, ke které chcete vytvořit připojení. Vyberte vybrat **jinou bránu virtuální sítě** a otevřete stránku **Vybrat bránu virtuální sítě** .

     - Prohlédněte si brány virtuálních sítí uvedené na této stránce. Všimněte si, že jsou uvedené pouze brány virtuálních sítí v rámci vašeho předplatného. Pokud se chcete připojit k bráně virtuální sítě, která není ve vašem předplatném, použijte [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

     - Vyberte bránu virtuální sítě, ke kterému chcete připojit.

     - **Shared Key (PSK)** : do tohoto pole zadejte sdílený klíč pro vaše připojení. Tento klíč si můžete vygenerovat nebo vytvořit sami. Klíč, který používáte v připojení site-to-site, je stejný pro místní zařízení i pro připojení brány virtuální sítě. Tady platí podobný, akorát se místo připojování k zařízení VPN, se připojujete k jiné bráně virtuální sítě.
    
4. Kliknutím na **OK** uložte změny.

## <a name="configure-the-vnet4-gateway-connection"></a>Konfigurace připojení brány VNet4
Potom vytvořte připojení z VNet4 k VNet1. Na portálu vyhledejte bránu virtuální sítě přidruženou k VNet4. Postupujte podle kroků v předchozí části a nahraďte hodnoty pro vytvoření připojení z VNet4 k VNet1. Ujistěte se, že používáte stejný sdílený klíč.

## <a name="verify-your-connections"></a>Zkontrolujte svá připojení

1. Vyhledejte bránu virtuální sítě na webu Azure Portal. 
2. Na stránce **Brána virtuální sítě** vyberte **připojení** , aby se zobrazila stránka **připojení** pro bránu virtuální sítě. Po navázání spojení uvidíte hodnoty **stavu** se změní na **připojeno**.

   ![Ověřit připojení](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png "Ověřit připojení")
3. Ve sloupci **název** vyberte jedno z připojení pro zobrazení dalších informací. Po zahájení toku dat uvidíte hodnoty pro **data v** a **data odchozí**.

   ![Stav](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png "Status")

## <a name="add-additional-connections"></a>Přidat další připojení

Pokud chcete přidat další připojení, přejděte k bráně virtuální sítě, ze které chcete vytvořit připojení, a pak vyberte **připojení**. Můžete vytvořit další připojení VNet-to-VNet nebo vytvořit připojení IPsec Site-to-Site k místnímu umístění. Nezapomeňte upravit **Typ připojení** tak, aby odpovídal typu připojení, které chcete vytvořit. Před vytvořením dalších připojení ověřte, že se adresní prostor vaší virtuální sítě nepřekrývá s žádným z adresních prostorů, které chcete připojit k. Postup vytvoření připojení Site-to-Site najdete v tématu [Vytvoření připojení typu Site-to-Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>Nejčastější dotazy týkající se propojení VNet-to-VNet
Projděte si Nejčastější dotazy, kde najdete další informace o propojeních VNet-to-VNet.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Další kroky

Informace o tom, jak můžete omezit síťový provoz na prostředky ve virtuální síti, najdete v tématu [zabezpečení sítě](../virtual-network/security-overview.md).

Informace o tom, jak Azure směruje provoz mezi Azure, místním prostředím a internetovými prostředky, najdete v tématu [Směrování provozu virtuální sítě](../virtual-network/virtual-networks-udr-overview.md).

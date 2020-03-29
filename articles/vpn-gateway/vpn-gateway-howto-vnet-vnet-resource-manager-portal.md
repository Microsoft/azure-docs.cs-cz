---
title: 'Konfigurace připojení brány VPN virtuální sítě k virtuální síti: Portál Azure'
description: Vytvořte připojení brány VPN mezi virtuálními sítěmi pomocí Resource Manageru a webu Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: cherylmc
ms.openlocfilehash: 3d91203253c08acdaa159fc70f7a34fa7fca20c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78674162"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Konfigurace připojení brány VPN virtuální sítě k virtuální síti pomocí portálu Azure

Tento článek vám pomůže připojit virtuální sítě (Virtuální sítě) pomocí typu připojení virtuální sítě k virtuální síti. Virtuální sítě mohou být v různých oblastech a z různých předplatných. Když připojíte virtuální sítě z různých předplatných, nemusí být předplatná přidružena ke stejnému klientovi služby Active Directory. 

![Diagram v2v](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

Kroky v tomto článku platí pro model nasazení Azure Resource Manager a používat portál Azure. Tuto konfiguraci můžete vytvořit pomocí jiného nástroje nebo modelu nasazení pomocí možností popsaných v následujících článcích:

> [!div class="op_single_selector"]
> * [Portál Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Propojení různých modelů nasazení – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Propojení různých modelů nasazení – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>Informace o propojování virtuálních sítí

Následující části popisují různé způsoby připojení virtuálních sítí.

### <a name="vnet-to-vnet"></a>VNet-to-VNet

Konfigurace připojení virtuální sítě k virtuální síti je jednoduchý způsob připojení virtuálních sítí. Když připojíte virtuální síť k jiné virtuální síti s typem připojení virtuální sítě k virtuální síti (VNet2VNet), je to podobné jako vytvoření připojení IPsec lokality k webu k místnímu umístění. Oba typy připojení používají bránu VPN k zajištění zabezpečeného tunelu s protokolem IPsec/IKE a fungují stejným způsobem při komunikaci. Liší se však ve způsobu konfigurace brány místní sítě. 

Když vytvoříte připojení virtuální sítě k virtuální síti, adresář brány místní sítě se automaticky vytvoří a naplní. Pokud aktualizujete adresní prostor pro jednu virtuální síť, druhá virtuální síť se automaticky přesměruje do aktualizovaného adresního prostoru. Obvykle je rychlejší a jednodušší vytvořit připojení virtuální sítě k virtuální síti než připojení site-to-site.

### <a name="site-to-site-ipsec"></a>Site-to-Site (IPsec)

Pokud pracujete s komplikovanou konfigurací sítě, můžete raději připojit virtuální sítě pomocí [připojení site-to-site](vpn-gateway-howto-site-to-site-resource-manager-portal.md) místo. Pokud se řídíte kroky IPsec mezi webovými servery, vytvoříte a nakonfigurujete brány místní sítě ručně. Brána místní sítě pro každou virtuální síť zpracovává jiné virtuální sítě jako místní síť. Tyto kroky umožňují zadat další adresní prostory pro bránu místní sítě pro směrování provozu. Pokud se změní adresní prostor pro virtuální síť, musíte ručně aktualizovat odpovídající bránu místní sítě.

### <a name="vnet-peering"></a>Partnerské vztahy virtuálních sítí

Virtuální sítě můžete taky připojit pomocí partnerského vztahu virtuální sítě. Partnerský vztah virtuální sítě nepoužívá bránu VPN a má různá omezení. Kromě toho [ceny pro VNET Peering](https://azure.microsoft.com/pricing/details/virtual-network) se vypočítávají odlišně než [ceny pro VNet-to-VNet VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway). Další informace najdete v tématu [Partnerské vztahy virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Proč vytvářet připojení typu VNet-to-VNet?

Virtuální sítě můžete chtít připojit pomocí připojení virtuální sítě k virtuální síti z následujících důvodů:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Geografická redundance a geografická přítomnost mezi oblastmi

  * Můžete nastavit vlastní geografickou replikaci nebo synchronizaci se zabezpečeným připojením, aniž byste museli přejíždět koncové body směřující k Internetu.
  * S Azure Traffic Manager a Azure Load Balancer můžete nastavit vysoce dostupné úlohy s geografickou redundancí ve více oblastech Azure. Můžete například nastavit sql server vždy na skupiny dostupnosti ve více oblastech Azure.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Regionální vícevrstvé aplikace s izolovanými nebo administrativními hranicemi

  * V rámci stejné oblasti můžete nastavit vícevrstvé aplikace s více virtuálními sítěmi, které jsou propojeny z důvodu izolace nebo požadavků na správu.

Komunikaci typu VNet-to-VNet můžete kombinovat s konfiguracemi s více servery. Tyto konfigurace umožňují vytvořit topologie sítě, které kombinují připojení mezi místními prostory s připojením mezi virtuálními sítěmi, jak je znázorněno na následujícím diagramu:

![Informace o připojeních](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Informace o připojeních")

Tento článek ukazuje, jak připojit virtuální sítě pomocí typu připojení virtuální sítě k virtuální síti. Pokud budete postupovat podle těchto kroků jako cvičení, můžete použít následující příklad nastavení hodnoty. V tomto příkladu jsou virtuální sítě ve stejném předplatném, ale v různých skupinách prostředků. Pokud jsou vaše virtuální sítě v různých předplatných, nelze vytvořit připojení na portálu. Místo toho použijte [Prostředí PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) nebo [CLI.](vpn-gateway-howto-vnet-vnet-cli.md) Další informace o připojeních virtuální sítě k virtuální síti najdete v tématu [Nejčastější dotazy k virtuální síti.](#vnet-to-vnet-faq)

### <a name="example-settings"></a>Příklad nastavení

**Hodnoty pro virtuální síť 1:**

- **Nastavení virtuální sítě**
    - **Název**: VNet1
    - **Adresní prostor**: 10.1.0.0/16
    - **Předplatné**: Vyberte předplatné, které chcete použít.
    - **Skupina prostředků**: TestRG1
    - **Umístění**: Východní USA
    - **Podsíť**
        - **Název**: FrontEnd
        - **Rozsah adres**: 10.1.0.0/24
    - **Podsíť brány**:
        - **Název**: *GatewaySubnet* je automaticky vyplněn
        - **Rozsah adres**: 10.1.255.0/27

- **Nastavení brány virtuální sítě**
    - **Název**: VNet1GW
    - **Typ brány**: Vyberte **VPN**.
    - **Typ VPN**: Vyberte **možnost Route-based**.
    - **Skladová položka**: Vyberte skladovou položku brány, kterou chcete použít.
    - **Název veřejné IP adresy**: VNet1GWpip
    - **Připojení**
       - **Název**: VNet1toVNet4
       - **Sdílený klíč**: Sdílený klíč můžete vytvořit sami. Při vytváření připojení mezi virtuálními sítěmi, hodnoty musí odpovídat. Pro toto cvičení použijte abc123.

**Hodnoty pro virtuální síť 4:**

- **Nastavení virtuální sítě**
   - **Název**: VNet4
   - **Adresní prostor**: 10.41.0.0/16
   - **Předplatné**: Vyberte předplatné, které chcete použít.
   - **Skupina prostředků**: TestRG4
   - **Umístění**: Západní USA
   - **Podsíť** 
      - **Název**: FrontEnd
      - **Rozsah adres**: 10.41.0.0/24
   - **GatewaySubnet** 
      - **Název**: *GatewaySubnet* je automaticky vyplněn
      - **Rozsah adres**: 10.41.255.0/27

- **Nastavení brány virtuální sítě** 
    - **Název**: VNet4GW
    - **Typ brány**: Vyberte **VPN**.
    - **Typ VPN**: Vyberte **možnost Route-based**.
    - **Skladová položka**: Vyberte skladovou položku brány, kterou chcete použít.
    - **Název veřejné IP adresy**: VNet4GWpip
    - **Připojení** 
       - **Název**: VNet4toVNet1
       - **Sdílený klíč**: Sdílený klíč můžete vytvořit sami. Při vytváření připojení mezi virtuálními sítěmi, hodnoty musí odpovídat. Pro toto cvičení použijte abc123.

## <a name="create-and-configure-vnet1"></a>Vytvoření a konfigurace virtuální sítě1
Pokud již máte virtuální síť vytvořenou, ověřte, zda jsou nastavení kompatibilní s vaším návrhem brány VPN. Věnujte zvláštní pozornost všem podsítím, které by se mohly překrývat s jinými sítěmi. Připojení nebude fungovat správně, pokud máte překrývající se podsítě.

### <a name="to-create-a-virtual-network"></a>Chcete-li vytvořit virtuální síť
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vnet1-gateway"></a>Vytvoření brány Virtuální sítě 1
V tomto kroku vytvoříte bránu virtuální sítě pro svou virtuální síť. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány. Pokud tuto konfiguraci vytváříte jako cvičení, přečtěte si [téma Příklad nastavení](#example-settings).

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>Chcete-li vytvořit bránu virtuální sítě
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>Vytvoření a konfigurace sítě VNet4
Po konfiguraci sítě VNet1 vytvořte bránu VNet4 a Brána VNet4 opakováním předchozích kroků a nahrazením hodnot hodnotami VNet4. Před konfigurací sítě VNet4 nemusíte čekat, až bude brána virtuální sítě pro Virtuální síť 1 dokončena. Pokud používáte vlastní hodnoty, ujistěte se, že adresní prostory se nepřekrývají s žádným virtuálním i virtuálním připojením, ke kterým se chcete připojit.

## <a name="configure-the-vnet1-gateway-connection"></a>Konfigurace připojení brány Sítě Virtuální sítě 1
Po dokončení brány virtuální sítě pro virtuální síť 1 i Virtuální síť4 můžete vytvořit připojení brány virtuální sítě. V této části vytvoříte připojení z VNet1 do VNet4. Tyto kroky fungují pouze u virtuálních sítí ve stejném předplatném. Pokud vaše virtuální sítě jsou v různých předplatných, musíte použít [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) k připojení. Pokud jsou však vaše virtuální sítě v různých skupinách prostředků ve stejném předplatném, můžete je připojit pomocí portálu.

1. Na webu Azure Portal vyberte **Všechny prostředky**, do vyhledávacího pole zadejte *bránu virtuální sítě* a přejděte na bránu virtuální sítě pro virtuální síť. Například **VNet1GW**. Vyberte bránu a otevřete stránku **brány virtuální sítě.** V části **Nastavení**vyberte **Možnost Připojení**.

   ![Stránka Připojení](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png "Stránka Připojení")
2. Výběrem **možnosti +Přidat** otevřete stránku **Přidat připojení.**

   ![Přidat připojení](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-vnet4-connection.png "Přidání připojení")
3. Na stránce **Přidat připojení** vyplňte hodnoty připojení:

   - **Název**: Zadejte název připojení. Například *VNet1toVNet4*.

   - **Typ připojení**: V rozevíracím programu vyberte **virtuální síť k virtuální síti.**

   - **První brána virtuální sítě**: Tato hodnota pole se automaticky vyplňuje, protože vytváříte toto připojení ze zadané brány virtuální sítě.

   - **Druhá brána virtuální sítě**: Toto pole je brána virtuální sítě, ke které chcete vytvořit připojení. Výběrem **možnosti Zvolit jinou bránu virtuální sítě** otevřete stránku **Zvolit bránu virtuální sítě.**

     - Prohlédněte si brány virtuálních sítí uvedené na této stránce. Všimněte si, že jsou uvedené pouze brány virtuálních sítí v rámci vašeho předplatného. Pokud se chcete připojit k bráně virtuální sítě, která není ve vašem předplatném, použijte [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

     - Vyberte bránu virtuální sítě, ke které se chcete připojit.

     - **Sdílený klíč (PSK)**: Do tohoto pole zadejte sdílený klíč pro připojení. Tento klíč si můžete vygenerovat nebo vytvořit sami. V připojení site-to-site je klíč, který používáte, stejný pro místní zařízení a připojení brány virtuální sítě. Koncept je zde podobný, kromě toho, že se připojujete k jiné bráně virtuální sítě, místo toho, abyste se připojovali k zařízení VPN.
    
4. Výběrem **OK** uložte změny.

## <a name="configure-the-vnet4-gateway-connection"></a>Konfigurace připojení brány Sítě VNet4
Dále vytvořte připojení z Virtuální sítě 4 na virtuální síť 1. Na portálu vyhledejte bránu virtuální sítě přidruženou k síti VNet4. Postupujte podle kroků z předchozí části a nahraďte hodnoty a vytvořte připojení z Virtuální sítě 4 na virtuální síť 1. Ujistěte se, že používáte stejný sdílený klíč.

## <a name="verify-your-connections"></a>Zkontrolujte svá připojení

1. Vyhledejte bránu virtuální sítě na webu Azure Portal. 
2. Na stránce **Brána virtuální sítě** vyberte **Připojení,** chcete-li zobrazit stránku **Připojení** pro bránu virtuální sítě. Po navázání připojení se hodnoty **stavu** změní na **Připojeno**.

   ![Ověření připojení](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png "Ověření připojení")
3. Ve sloupci **Název** vyberte jedno z připojení, chcete-li zobrazit další informace. Když data začnou proudit, uvidíte hodnoty pro **Data dovnitř** a **Data out**.

   ![Stav](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png "Status")

## <a name="add-additional-connections"></a>Přidání dalších připojení

Pokud chcete přidat další připojení, přejděte na bránu virtuální sítě, ze které chcete připojení vytvořit, a pak vyberte **Připojení**. Můžete vytvořit další připojení VNet-to-VNet nebo vytvořit připojení IPsec Site-to-Site k místnímu umístění. Nezapomeňte upravit **Typ připojení** tak, aby odpovídal typu připojení, které chcete vytvořit. Před vytvořením dalších připojení ověřte, zda se adresní prostor pro vaši virtuální síť nepřekrývá s žádným adresním prostorem, ke kterému se chcete připojit. Postup vytvoření připojení Site-to-Site najdete v tématu [Vytvoření připojení typu Site-to-Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>Nejčastější dotazy týkající se propojení VNet-to-VNet
Projděte si Nejčastější dotazy, kde najdete další informace o propojeních VNet-to-VNet.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Další kroky

Informace o omezení síťového provozu na prostředky ve virtuální síti naleznete v tématu [Zabezpečení sítě](../virtual-network/security-overview.md).

Informace o tom, jak Azure směruje provoz mezi Azure, místním prostředím a internetovými prostředky, najdete v tématu [Směrování provozu virtuální sítě](../virtual-network/virtual-networks-udr-overview.md).

---
title: Konfigurace připojení brány VPN typu VNet-to-VNet pomocí webu Azure portal | Dokumentace Microsoftu
description: Vytvořte připojení brány VPN mezi virtuálními sítěmi pomocí Resource Manageru a webu Azure Portal.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2018
ms.author: cherylmc
ms.openlocfilehash: 245710d1ae3e7af4db5286a5db985fe35549ad3a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53098766"
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

Pokud pracujete se složitou konfigurací sítě, dáte možná přednost připojení virtuální sítě pomocí [připojení Site-to-Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md) místo. Když budete postupovat podle kroků Site-to-Site IPsec, vytvoříte a nakonfigurujete brány místní sítě ručně. Brána místní sítě pro každou virtuální síť zpracovává jiné virtuální sítě jako místní síť. Tyto kroky umožňují zadat další adresní prostory pro bránu místní sítě pro směrování provozu. Pokud se adresní prostor pro virtuální síť změní, musíte ručně aktualizovat odpovídající bránu místní sítě.

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

![Informace o připojeních](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Informace o připojeních")

Tento článek ukazuje, jak propojit virtuální sítě s použitím typu připojení VNet-to-VNet. Pokud jste provedli následující kroky jako cvičení, můžete použít následující ukázkové hodnoty nastavení. V tomto příkladu jsou virtuální sítě ve stejném předplatném, ale v různých skupinách prostředků. Pokud jsou vaše virtuální sítě v různých předplatných, nelze vytvořit připojení na portálu. Použití [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) nebo [rozhraní příkazového řádku](vpn-gateway-howto-vnet-vnet-cli.md) místo. Další informace o připojení VNet-to-VNet najdete v tématu [nejčastější dotazy týkající se připojení typu VNet-to-VNet](#vnet-to-vnet-faq).

### <a name="example-settings"></a>Příklad nastavení

**Hodnoty pro virtuální síť TestVNet1:**

- **Nastavení virtuální sítě**
    - **Název**: Zadejte *virtuální sítě TestVNet1*.
    - **Adresní prostor**: Zadejte *10.11.0.0/16*.
    - **Předplatné**: Vyberte předplatné, které chcete použít.
    - **Skupina prostředků**: Zadejte *TestRG1*.
    - **Umístění**: vyberte **USA – východ**.
    - **Podsíť**
        - **Název**: Zadejte *front-endu*.
        - **Rozsah adres**: Zadejte *10.11.0.0/24*.
    - **Podsíť brány**:
        - **Název**: *GatewaySubnet* je autofilled.
        - **Rozsah adres**: Zadejte *10.11.255.0/27*.
    - **DNS server**: vyberte **vlastní** a zadejte IP adresu serveru DNS.

- **Nastavení brány virtuální sítě** 
    - **Název**: Zadejte *TestVNet1GW*.
    - **Typ brány**: Vyberte **VPN**.
    - **Typ sítě VPN**: vyberte **založené na trasách**.
    - **Skladová položka**: vybrat bránu SKU, kterou chcete použít.
    - **Název veřejné IP adresy**: Zadejte *TestVNet1GWIP*
    - **připojení** 
       - **Název**: Zadejte *TestVNet1toTestVNet4*.
       - **Sdílený klíč**: Zadejte *abc123*. Sdílený klíč můžete vytvořit sami. Když vytvoříte připojení mezi virtuálními sítěmi, hodnoty musí odpovídat.

**Hodnoty pro virtuální síť TestVNet4:**

- **Nastavení virtuální sítě**
   - **Název**: Zadejte *virtuální sítě TestVNet4*.
   - **Adresní prostor**: Zadejte *10.41.0.0/16*.
   - **Předplatné**: Vyberte předplatné, které chcete použít.
   - **Skupina prostředků**: Zadejte *TestRG4*.
   - **Umístění**: vyberte **USA – západ**.
   - **Podsíť** 
      - **Název**: Zadejte *front-endu*.
      - **Rozsah adres**: Zadejte *10.41.0.0/24*.
   - **Podsíť brány** 
      - **Název**: *GatewaySubnet* je autofilled.
      - **Rozsah adres**: Zadejte *10.41.255.0/27*.
   - **DNS server**: vyberte **vlastní** a zadejte IP adresu serveru DNS.

- **Nastavení brány virtuální sítě** 
    - **Název**: Zadejte *TestVNet4GW*.
    - **Typ brány**: Vyberte **VPN**.
    - **Typ sítě VPN**: vyberte **založené na trasách**.
    - **Skladová položka**: vybrat bránu SKU, kterou chcete použít.
    - **Název veřejné IP adresy**: Zadejte *TestVNet4GWIP*.
    - **připojení** 
       - **Název**: Zadejte *TestVNet4toTestVNet1*.
       - **Sdílený klíč**: Zadejte *abc123*. Sdílený klíč můžete vytvořit sami. Když vytvoříte připojení mezi virtuálními sítěmi, hodnoty musí odpovídat.

## <a name="create-and-configure-testvnet1"></a>Vytvoření a konfigurace virtuální sítě TestVNet1
Pokud již máte virtuální síť vytvořenou, ověřte, zda jsou nastavení kompatibilní s vaším návrhem brány VPN. Věnujte zvláštní pozornost všem podsítím, které by se mohly překrývat s jinými sítěmi. Vaše připojení nebude fungovat správně, pokud máte překrývající se podsítí. Jakmile vaše virtuální síť nakonfigurována se správným nastavením, můžete začít s kroky v [určení serveru DNS](#dns) oddílu.

### <a name="to-create-a-virtual-network"></a>Chcete-li vytvořit virtuální síť
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="add-additional-address-space-and-create-subnets"></a>Přidání dalšího adresního prostoru a vytvoření podsítí
Po vytvoření virtuální sítě můžete přidat další adresní prostor a vytvořit podsítě.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="create-a-gateway-subnet"></a>Vytvoření podsítě brány
Než vytvoříte bránu virtuální sítě pro vaši virtuální síť, nejprve musíte vytvořit podsíť brány. Podsíť brány obsahuje IP adresy, které používá brána virtuální sítě. Pokud je to možné je nejlepší vytvořit podsíť brány s použitím bloku CIDR/28 nebo velikost/27 k dispozici dostatek IP adres pro plnění požadavků na další konfiguraci v budoucnu.

Pokud vytváříte tuto konfiguraci jako cvičení, použijte tyto [příklad nastavení](#example-settings) při vytváření podsítě brány.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Chcete-li vytvořit podsíť brány
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="specify-a-dns-server-optional"></a>Určení serveru DNS (volitelné)
DNS není povinné pro připojení VNet-to-VNet. Pokud chcete umožnit překlad adres IP pro prostředky, které jsou nasazené do vaší virtuální sítě, určení serveru DNS. Toto nastavení umožňuje určit server DNS, který chcete použít pro překlad IP adres pro tuto virtuální síť. Nevytvoří DNS server.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="create-a-virtual-network-gateway"></a>Vytvoření brány virtuální sítě
V tomto kroku vytvoříte bránu virtuální sítě pro svou virtuální síť. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány. Pokud vytváříte tuto konfiguraci jako cvičení, najdete v článku [příklad nastavení](#example-settings).

### <a name="to-create-a-virtual-network-gateway"></a>Chcete-li vytvořit bránu virtuální sítě
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="create-and-configure-testvnet4"></a>Vytvoření a konfigurace virtuální sítě TestVNet4
Po konfiguraci virtuální sítě TestVNet1 vytvořte virtuální síť TestVNet4 opakováním předchozích kroků a nahrazením hodnot za hodnoty virtuální sítě TestVNet4 hodnoty. Nemusíte čekat na dokončení vytváření před konfigurací virtuální sítě TestVNet4 brány virtuální sítě pro virtuální síť TestVNet1. Pokud používáte vlastní hodnoty, ujistěte se, že se že adresní prostory nepřekrývaly s žádnou z virtuálních sítí, ke kterému chcete připojit.

## <a name="configure-the-testvnet1-gateway-connection"></a>Konfigurace připojení brány TestVNet1
Po dokončení vytváření bran virtuálních sítí pro TestVNet1 a TestVNet4 můžete vytvořit připojení bran virtuálních sítí. V této části vytvoříte připojení z VNet1 do VNet4. Tyto kroky fungují pouze u virtuálních sítí ve stejném předplatném. Pokud jsou vaše virtuální sítě v různých předplatných, je nutné použít [Powershellu](vpn-gateway-vnet-vnet-rm-ps.md) připojení. Ale pokud jsou vaše virtuální sítě v různých skupinách prostředků ve stejném předplatném, můžete je propojit pomocí portálu.

1. Na webu Azure Portal, vyberte **všechny prostředky**, zadejte *brány virtuální sítě* do vyhledávacího pole a potom přejděte k bráně virtuální sítě pro vaši virtuální síť. Například **TestVNet1GW**. Vyberte ji a otevřete **Brána virtuální sítě** stránky.

  ![Stránka Připojení](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "Stránka Připojení")
2. V části **nastavení**vyberte **připojení**a pak vyberte **přidat** otevřít **přidat připojení** stránky.

  ![Přidání připojení](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "Přidání připojení")
3. Na **přidat připojení** stránky, zadejte hodnoty pro připojení:

   - **Název**: Zadejte název vašeho připojení. Například *TestVNet1toTestVNet4*.

   - **Typ připojení**: vyberte **připojení typu VNet-to-VNet** z rozevíracího seznamu.

   - **První Brána virtuální sítě**: Tato hodnota pole se automaticky vyplní, protože toto připojení vytváříte ze zadané virtuální sítě brány.

   - **Druhá Brána virtuální sítě**: Toto pole je Brána virtuální sítě, kterou chcete vytvořit připojení k virtuální síti. Vyberte **vybrat jinou bránu virtuální sítě** otevřít **vybrat bránu virtuální sítě** stránky.

    - Prohlédněte si brány virtuálních sítí uvedené na této stránce. Všimněte si, že jsou uvedené pouze brány virtuálních sítí v rámci vašeho předplatného. Pokud se chcete připojit k bráně virtuální sítě, který není ve vašem předplatném, použijte [Powershellu](vpn-gateway-vnet-vnet-rm-ps.md).

    - Vyberte bránu virtuální sítě, ke kterému chcete připojit.

    - **Sdílený klíč (PSK)**: do tohoto pole zadejte sdílený klíč pro vaše připojení. Tento klíč si můžete vygenerovat nebo vytvořit sami. Klíč, který používáte v připojení site-to-site, je stejný pro místní zařízení i pro připojení brány virtuální sítě. Tady platí podobný, akorát se místo připojování k zařízení VPN, se připojujete k jiné bráně virtuální sítě.
    
4. Vyberte **OK** uložte provedené změny.

## <a name="configure-the-testvnet4-gateway-connection"></a>Konfigurace připojení brány TestVNet4
Dále vytvoříte připojení z virtuální sítě TestVNet4 k virtuální síti TestVNet1. Na portálu vyhledejte bránu virtuální sítě přidruženou k TestVNet4. Postupujte podle kroků v předchozí části a nahraďte hodnoty TestVNet4 pro vytvoření připojení hodnotami TestVNet1. Ujistěte se, že používáte stejný sdílený klíč.

## <a name="verify-your-connections"></a>Zkontrolujte svá připojení

Vyhledejte bránu virtuální sítě na webu Azure Portal. Na **Brána virtuální sítě** stránce **připojení** zobrazíte **připojení** stránky pro bránu virtuální sítě. Po připojení se naváže, zobrazí se vám **stav** hodnoty změnit na **Succeeded** a **připojeno**. Vyberte připojení otevřete **Essentials** stránce a zobrazí se další informace.

![Úspěšné](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Úspěšné")

Po zahájení toku dat zobrazí hodnoty pro **Data v** a **výstupní Data**.

![Základy](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Základy")

## <a name="add-additional-connections"></a>Přidat další připojení

Pokud chcete přidat další připojení, přejděte do brány virtuální sítě, ze kterého chcete vytvořit připojení a pak vyberte **připojení**. Můžete vytvořit další připojení VNet-to-VNet nebo vytvořit připojení IPsec Site-to-Site k místnímu umístění. Nezapomeňte upravit **Typ připojení** tak, aby odpovídal typu připojení, které chcete vytvořit. Před vytvořením dalších připojení ověřte, že se adresní prostor vaší virtuální sítě nepřekrývá s žádným z adresních prostorů, které chcete připojit k. Postup vytvoření připojení Site-to-Site najdete v tématu [Vytvoření připojení typu Site-to-Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>Nejčastější dotazy týkající se propojení VNet-to-VNet
Projděte si Nejčastější dotazy, kde najdete další informace o propojeních VNet-to-VNet.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Další postup

Informace o postupu při omezení síťového provozu do prostředků ve virtuální síti najdete v tématu [zabezpečení sítě](../virtual-network/security-overview.md).

Informace o tom, jak Azure směruje provoz mezi Azure, místním prostředím a internetovými prostředky, najdete v tématu [Směrování provozu virtuální sítě](../virtual-network/virtual-networks-udr-overview.md).

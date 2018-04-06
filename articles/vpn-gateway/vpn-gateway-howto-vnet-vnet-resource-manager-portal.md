---
title: 'Připojení virtuální sítě Azure k jiné virtuální síti: Portál | Dokumentace Microsoftu'
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
ms.date: 03/21/2018
ms.author: cherylmc
ms.openlocfilehash: a95fdf078baa35eb9ca8ade462223b204be77d8e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-the-azure-portal"></a>Konfigurace propojení brány VPN typu VNet-to-VNet pomocí webu Azure Portal

Tento článek vám pomůže propojit virtuální sítě s použitím typu připojení VNet-to-VNet. Virtuální sítě se můžou nacházet ve stejné oblasti nebo v různých oblastech a můžou patřit do stejného předplatného nebo do různých předplatných. Pokud připojujete virtuální sítě z různých předplatných, tato předplatná nemusí být přidružená ke stejnému tenantovi Active Directory. 

Postupy v tomto článku se týkají modelu nasazení Resource Manager a používají Azure Portal. Tuto konfiguraci můžete vytvořit také pomocí jiného nástroje nasazení nebo pro jiný model nasazení, a to výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Propojení různých modelů nasazení – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Propojení různých modelů nasazení – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Diagram v2v](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

## <a name="about"></a>Informace o propojování virtuálních sítí

Existuje více způsobů propojení virtuálních sítí. Následující oddíly popisují různé způsoby připojení virtuálních sítí.

### <a name="vnet-to-vnet"></a>VNet-to-VNet

Konfigurace připojení VNet-to-VNet je dobrým způsobem, jak snadno připojit virtuální sítě. Propojení virtuální sítě s jinou virtuální sítí s použitím typu připojení VNet-to-VNet je podobné jako vytvoření připojení Site-to-Site IPsec k místnímu umístění. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE a oba komunikují stejným způsobem. Rozdílem mezi těmito typy připojení je způsob konfigurace místní síťové brány. Při vytváření připojení typu VNet-to-VNet se nezobrazí adresní prostor místní síťové brány. Vytvoří a naplní se automaticky. Pokud aktualizujete adresní prostor pro jednu virtuální síť, druhá virtuální síť bude automaticky znát trasu do aktualizovaného adresního prostoru. Vytvoření připojení VNet-to-VNet je obvykle rychlejší a snazší než vytvoření připojení Site-to-Site mezi virtuálními sítěmi.

### <a name="site-to-site-ipsec"></a>Site-to-Site (IPsec)

Pokud pracujete se složitou konfigurací sítě, můžete chtít vaše virtuální sítě raději připojit pomocí kroků [Site-to-Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Při použití kroků Site-to-Site (IPsec) vytvoříte a nakonfigurujete brány místní sítě ručně. Brána místní sítě pro každou virtuální síť zpracovává jiné virtuální sítě jako místní síť. Ten vám umožní pro místní síťovou bránu zadat další adresní prostor pro směrování provozu. Pokud se adresní prostor pro virtuální síť změní, musíte aktualizovat odpovídající bránu místní sítě tak, aby změnu odrážela. Nedojde k automatické aktualizaci.

### <a name="vnet-peering"></a>Partnerské vztahy virtuálních sítí

Můžete chtít zvážit připojení vašich virtuálních sítí pomocí VNET Peering. VNET Peering nepoužívá bránu sítě VPN a má jiná omezení. Kromě toho [ceny pro VNET Peering](https://azure.microsoft.com/pricing/details/virtual-network) se vypočítávají odlišně než [ceny pro VNet-to-VNet VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway). Další informace najdete v tématu [Partnerské vztahy virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).

## <a name="why"></a>Proč vytvářet připojení typu VNet-to-VNet?

Virtuální sítě může být vhodné propojit pomocí připojení VNet-to-VNet z následujících důvodů:

* **Geografická redundance napříč oblastmi a geografická přítomnost**

  * Můžete nastavit vlastní geografickou replikaci nebo synchronizaci se zabezpečeným připojením bez procházení koncovými body připojenými k internetu.
  * Pomocí Azure Traffic Manageru a služby Load Balancer je možné vytvářet úlohy s vysokou dostupností s geografickou redundancí nad několika oblastmi Azure. Jedním z důležitých příkladů je nastavení technologie SQL Always On se skupinami dostupnosti nad několika oblastmi Azure.
* **Regionální vícevrstvé aplikace s izolací nebo administrativní hranicí**

  * V rámci stejné oblasti můžete vytvářet vícevrstvé aplikace s několika virtuálními sítěmi propojenými z důvodu izolace nebo požadavků na správu.

Komunikaci typu VNet-to-VNet můžete kombinovat s konfiguracemi s více servery. Díky tomu je možné vytvářet topologie sítí, ve kterých se používá propojování více míst i propojování virtuálních sítí, jak je znázorněno v následujícím schématu:

![Informace o připojeních](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Informace o připojeních")

Tento článek vám pomůže propojit virtuální sítě s použitím typu připojení VNet-to-VNet. Používáte-li tyto kroky jako cvičení, můžete použít ukázkové hodnoty nastavení. V tomto příkladu jsou virtuální sítě ve stejném předplatném, ale v různých skupinách prostředků. Pokud jsou vaše virtuální sítě v různých předplatných, nelze vytvořit připojení na portálu. Můžete použít [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) nebo [rozhraní příkazového řádku](vpn-gateway-howto-vnet-vnet-cli.md). Další informace o propojeních VNet-to-VNet najdete v části [Nejčastější dotazy týkající se propojení VNet-to-VNet](#faq) na konci tohoto článku.

### <a name="values"></a>Příklady nastavení

**Hodnoty pro virtuální síť TestVNet1:**

* Název virtuální sítě: TestVNet1
* Adresní prostor: 10.11.0.0/16
* Předplatné: Vyberte předplatné, které chcete použít.
* Skupina prostředků: TestRG1
* Umístění: Východní USA
* Název podsítě: FrontEnd
* Rozsah adres podsítě: 10.11.0.0/24
* Název podsítě brány: GatewaySubnet (na portálu se sám vyplní)
* Rozsah adres podsítě brány: 10.11.255.0/27
* Server DNS: Použijte IP adresu svého serveru DNS.
* Název brány virtuální sítě: TestVNet1GW
* Typ brány: Síť VPN
* Typ sítě VPN: Založená na trasách
* SKU: Vyberte skladovou jednotku (SKU) brány, kterou chcete použít.
* Název veřejné IP adresy: TestVNet1GWIP
* Název připojení: TestVNet1toTestVNet4
* Sdílený klíč: Sdílený klíč si můžete vytvořit sami. V tomto příkladu použijeme abc123. Důležité je, aby se při vytváření propojení virtuálních sítí tato hodnota shodovala.

**Hodnoty pro virtuální síť TestVNet4:**

* Název virtuální sítě: TestVNet4
* Adresní prostor: 10.41.0.0/16
* Předplatné: Vyberte předplatné, které chcete použít.
* Skupina prostředků: TestRG4
* Umístění: Západní USA
* Název podsítě: FrontEnd
* Rozsah adres podsítě: 10.41.0.0/24
* Název podsítě brány: GatewaySubnet (na portálu se sám vyplní)
* Rozsah adres podsítě brány: 10.41.255.0/27
* Server DNS: Použijte IP adresu svého serveru DNS.
* Název brány virtuální sítě: TestVNet4GW
* Typ brány: Síť VPN
* Typ sítě VPN: Založená na trasách
* SKU: Vyberte skladovou jednotku (SKU) brány, kterou chcete použít.
* Název veřejné IP adresy: TestVNet4GWIP
* Název připojení: TestVNet1toTestVNet4
* Sdílený klíč: Sdílený klíč si můžete vytvořit sami. V tomto příkladu použijeme abc123. Důležité je, aby se při vytváření propojení virtuálních sítí tato hodnota shodovala.

## <a name="CreatVNet"></a>1. Vytvoření a konfigurace virtuální sítě TestVNet1
Pokud již máte virtuální síť vytvořenou, ověřte, zda jsou nastavení kompatibilní s vaším návrhem brány VPN. Věnujte zvláštní pozornost všem podsítím, které by se mohly překrývat s jinými sítěmi. Pokud se podsítě překrývají, připojení nebude fungovat správně. Pokud je vaše virtuální síť nakonfigurována se správným nastavením, můžete začít s kroky v oddílu [Určení serveru DNS](#dns).

### <a name="to-create-a-virtual-network"></a>Chcete-li vytvořit virtuální síť
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="subnets"></a>2. Přidání dalšího adresního prostoru a vytvoření podsítí
Po vytvoření virtuální sítě můžete přidat další adresní prostor a vytvořit podsítě.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. Vytvoření podsítě brány
Před vytvoření brány virtuální sítě pro vaši virtuální síť je nejprve potřeba vytvořit podsíť brány. Podsíť brány obsahuje IP adresy, které používá brána virtuální sítě. Pokud je to možné, je nejlepší vytvořit podsíť brány s použitím bloku CIDR /28 nebo /27, aby byl k dispozici dostatek IP adres pro plnění dalších požadavků na konfiguraci v budoucnu.

Pokud vytváříte tuto konfiguraci jako cvičení, při vytváření podsítě brány použijte tyto [příklady nastavení](#values).

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Chcete-li vytvořit podsíť brány
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>4. Určení serveru DNS (volitelné)
DNS není pro připojení VNet-to-VNet vyžadováno. Pokud ale chcete umožnit překlad IP adres pro prostředky nasazované do vaší virtuální sítě, měli byste určit server DNS. Toto nastavení umožňuje určit server DNS, který chcete použít pro překlad IP adres pro tuto virtuální síť. Neslouží k vytvoření serveru DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. Vytvoření brány virtuální sítě
V tomto kroku vytvoříte bránu virtuální sítě pro svou virtuální síť. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány. Pokud vytváříte tuto konfiguraci jako cvičení, můžete použít tyto [příklady nastavení](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Chcete-li vytvořit bránu virtuální sítě
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="CreateTestVNet4"></a>6. Vytvoření a konfigurace virtuální sítě TestVNet4
Po konfiguraci virtuální sítě TestVNet1 vytvořte virtuální síť TestVNet4 opakováním předchozích kroků a nahrazením hodnot za hodnoty virtuální sítě TestVNet4. Není nutné s konfigurací virtuální sítě TestVNet4 čekat na dokončení vytváření brány virtuální sítě pro TestVNet1. Pokud používáte vlastní hodnoty, zajistěte, aby se adresní prostory nepřekrývaly s žádnou z virtuálních sítí, ke kterým se chcete připojit.

## <a name="TestVNet1Connection"></a>7. Konfigurace připojení brány TestVNet1
Po dokončení vytváření bran virtuálních sítí pro TestVNet1 a TestVNet4 můžete vytvořit připojení bran virtuálních sítí. V této části vytvoříte připojení z VNet1 do VNet4. Tyto kroky fungují pouze u virtuálních sítí ve stejném předplatném. Pokud jsou vaše virtuální sítě v různých předplatných, musíte k vytvoření propojení použít PowerShell. Podrobnosti najdete v článku o [PowerShellu](vpn-gateway-vnet-vnet-rm-ps.md). Pokud ale vaše virtuální sítě jsou v různých skupinách prostředků ve stejném předplatném, můžete je propojit pomocí portálu.

1. V části **Všechny prostředky** přejděte do brány virtuální sítě pro vaši virtuální síť. Například **TestVNet1GW**. Kliknutím na **TestVNet1GW** otevřete stránku brány virtuální sítě.

  ![Stránka Připojení](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "Stránka Připojení")
2. Kliknutím na **+Přidat** otevřete stránku **Přidat připojení**.

  ![Přidání připojení](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "Přidání připojení")
3. Na stránce **Přidat připojení** do pole pro název zadejte název vašeho připojení. Například **TestVNet1toTestVNet4**.
4. V rozevíracím seznamu v poli **Typ připojení** vyberte **VNet-to-VNet**.
5. Pole **První brána virtuální sítě** je vyplněné automaticky, protože toto připojení vytváříte ze zadané brány virtuální sítě.
6. Pole **Druhá brána virtuální sítě** představuje bránu virtuální sítě, ke které chcete vytvořit připojení. Kliknutím na **Vybrat jinou bránu virtuální sítě** otevřete stránku **Vybrat bránu virtuální sítě**.
7. Prohlédněte si brány virtuálních sítí uvedené na této stránce. Všimněte si, že jsou uvedené pouze brány virtuálních sítí v rámci vašeho předplatného. Pokud se chcete připojit k bráně virtuální sítě, která není ve vašem předplatném, použijte k tomu [článek k prostředí PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).
8. Klikněte na bránu virtuální sítě, ke které se chcete připojit.
9. Do pole **Sdílený klíč** zadejte sdílený klíč pro vaše připojení. Tento klíč si můžete vygenerovat nebo vytvořit sami. V připojení typu Site-to-Site by použitý klíč byl úplně stejný pro místní zařízení i pro připojení brány virtuální sítě. Tady platí podobný přístup, akorát se místo připojování k zařízení VPN připojujete k další bráně virtuální sítě.
10. Změny uložíte kliknutím na **OK** v dolní části stránky.

## <a name="TestVNet4Connection"></a>8. Konfigurace připojení brány TestVNet4
Dále vytvoříte připojení z virtuální sítě TestVNet4 k virtuální síti TestVNet1. Na portálu vyhledejte bránu virtuální sítě přidruženou k TestVNet4. Postupujte podle kroků v předchozí části a nahraďte hodnoty TestVNet4 pro vytvoření připojení hodnotami TestVNet1. Ujistěte se, že používáte stejný sdílený klíč.

## <a name="VerifyConnection"></a>9. Zkontrolujte svá připojení

Vyhledejte bránu virtuální sítě na portálu. Na stránce brány virtuální sítě klikněte na **Připojení**. Zobrazí se stránka připojení pro bránu virtuální sítě. Jakmile se připojení naváže, hodnoty stavu se změní na **Úspěch** a **Připojeno**. Dvojím kliknutím na připojení můžete otevřít stránku **Základy** a zobrazit další informace.

![Úspěšné](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Úspěšné")

Po zahájení toku dat zobrazí hodnoty pro vstupní a výstupní data.

![Základy](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Základy")

## <a name="to-add-additional-connections"></a>Přidání dalších připojení

Pokud chcete přidat další připojení, přejděte k bráně virtuální sítě, ze které chcete vytvořit připojení, a potom klikněte na **Připojení**. Můžete vytvořit další připojení VNet-to-VNet nebo vytvořit připojení IPsec Site-to-Site k místnímu umístění. Nezapomeňte upravit **Typ připojení** tak, aby odpovídal typu připojení, které chcete vytvořit. Před vytvořením dalších připojení ověřte, že se adresní prostor vaší virtuální sítě nepřekrývá se žádnými adresními prostory, ke kterým se chcete připojit. Postup vytvoření připojení Site-to-Site najdete v tématu [Vytvoření připojení typu Site-to-Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="faq"></a>Nejčastější dotazy týkající se propojení VNet-to-VNet
Projděte si Nejčastější dotazy, kde najdete další informace o propojeních VNet-to-VNet.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Další kroky

V tématu [Zabezpečení sítě](../virtual-network/security-overview.md) najdete informace o postupu při omezení síťového provozu směřujícího do prostředků ve virtuální síti.

Informace o tom, jak Azure směruje provoz mezi Azure, místním prostředím a internetovými prostředky, najdete v tématu [Směrování provozu virtuální sítě](../virtual-network/virtual-networks-udr-overview.md).

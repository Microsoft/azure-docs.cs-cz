---
title: 'Vytvoření brány VPN založené na trasách: Azure portal | Dokumentace Microsoftu'
description: Rychle vytvořte založené na směrování VPN Gateway pomocí webu Azure portal
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: cherylmc
ms.openlocfilehash: fe05ab36f971105cf72342b8df5e2a82de7fc2b8
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391789"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Vytvoření trasové brány VPN pomocí webu Azure portal

Tento článek vám pomůže rychle vytvořit založené na směrování Azure VPN gateway pomocí webu Azure portal.  Brány VPN se používá při vytváření připojení VPN k místní síti. Bránu sítě VPN můžete použít také k propojení virtuálních sítí. 

Kroky v tomto článku se vytvoří virtuální síť, podsíť, podsítě brány a bránu VPN založenou na směrování (brány virtuální sítě). Po dokončení vytvoření brány můžete pak vytvořit připojení. Tyto kroky vyžadují předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="vnet"></a>Vytvoření virtuální sítě

1. V prohlížeči přejděte na web [Azure Portal](http://portal.azure.com) a přihlaste se pomocí svého účtu Azure.
2. Klikněte na **Vytvořit prostředek**. Do pole **Hledat na Marketplace** zadejte text „Virtuální síť“. Ve vráceném seznamu vyhledejte položku **Virtuální síť** a kliknutím otevřete stránku **Virtuální síť**.
3. U dolního okraje stránky virtuální síť v **vybrat model nasazení** seznamu, ověřte, že **Resource Manageru** vybraný z rozevíracího seznamu a potom klikněte na tlačítko **vytvořit**. Tím se otevře **vytvořit virtuální síť** stránky.
4. Na stránce **Vytvořit virtuální síť** nakonfigurujte nastavení virtuální sítě. Po vyplnění polí se červený vykřičník změní na zelenou značku zaškrtnutí, pokud jsou znaky zadané do pole platné. Použijte následující hodnoty:

  - **Název**: virtuální sítě TestVNet1
  - **Adresní prostor**: 10.1.0.0/16
  - **Předplatné**: Ověřte, zda je v předplatném uvedeným ten, který chcete použít. Předplatná můžete měnit prostřednictvím rozevíracího seznamu.
  - **Skupina prostředků**: TestRG1
  - **Umístění**: USA – východ
  - **Podsíť**: front-endu
  - **Rozsah adres**: 10.1.0.0/24

  ![Stránka pro vytvoření virtuální sítě](./media/create-routebased-vpn-gateway-portal/create-virtual-network.png "Stránka pro vytvoření virtuální sítě")
5. Po zadání hodnoty, vyberte **připnout na řídicí panel** snadno najít virtuální síť na řídicím panelu a potom klikněte na **vytvořit**. Po kliknutí na tlačítko **vytvořit**, se zobrazí na dlaždici na řídicím panelu, který zobrazuje postup vaší virtuální sítě. Obsah dlaždice se v průběhu vytváření sítě VNet mění.

## <a name="gwsubnet"></a>Přidání podsítě brány

Podsíť brány obsahuje vyhrazené IP adresy, které používají služby brány virtuální sítě. Vytvořte podsíť brány.

1. Na portálu přejděte na virtuální síť, pro kterou chcete vytvořit bránu virtuální sítě.
2. Na stránce vaší virtuální sítě klikněte na tlačítko **podsítě** rozbalte **ze sítě VNet1 - podsítě** stránky.
3. Klikněte na tlačítko **+ podsíť brány** v horní části stránky otevřete **přidat podsíť** stránky.

  ![Přidání podsítě brány](./media/create-routebased-vpn-gateway-portal/gateway-subnet.png "Přidání podsítě brány")
4. **Název** pro podsítě se automaticky vyplní požadovaná hodnota "GatewaySubnet". Upravte automaticky vyplněné **rozsah adres** hodnoty tak, aby odpovídala následující hodnoty:

  **Rozsah adres (blok CIDR)**: 10.1.255.0/27

  ![Přidání podsítě brány](./media/create-routebased-vpn-gateway-portal/add-gateway-subnet.png "Přidání podsítě brány")
5. Chcete-li vytvořit podsíť brány, klikněte na tlačítko **OK** v dolní části stránky.

## <a name="gwvalues"></a>Konfigurace nastavení brány

1. Na levé straně stránky portálu klikněte na tlačítko **+ vytvořit prostředek** a typ "Brána virtuální sítě' do vyhledávacího pole, pak se stisknutím klávesy **Enter**. V části **Výsledky** vyhledejte položku **Brána virtuální sítě** a klikněte na ni.
2. V dolní části stránky "Brána virtuální sítě", klikněte na položku **vytvořit** otevřít **vytvořit bránu virtuální sítě** stránky.
3. Na stránce **Vytvořit bránu virtuální sítě** zadejte hodnoty pro příslušnou bránu virtuální sítě.

  - **Název**: Vnet1GW
  - **Typ brány**: síť VPN 
  - **Typ sítě VPN**: založené na trasách
  - **SKU**: VpnGw1
  - **Umístění**: USA – východ
  - **Virtuální síť**: klikněte na tlačítko **virtuální sítě nebo zvolte virtuální síť** otevřít **zvolit virtuální síť** stránky. Vyberte **ze sítě VNet1**.

  ![Konfigurace nastavení brány](./media/create-routebased-vpn-gateway-portal/configure-gateway.png "nakonfigurovat nastavení brány")

## <a name="pip"></a>Vytvoření veřejné IP adresy

Brána VPN musí mít dynamicky přidělené veřejné IP adresy. Při vytváření připojení k bráně VPN, toto je IP adresa vašeho místního zařízení připojeného k.

1. Vyberte **konfigurace první IP adresa, vytvořit konfiguraci IP adresy brány** k vyžádání veřejné IP adresy.

  ![První konfigurace IP adresy](./media/create-routebased-vpn-gateway-portal/add-public-ip-address.png "konfigurace první IP adresy")
2. Na **stránku zvolit veřejnou IP**, klikněte na tlačítko **+ vytvořit novou** otevřít **vytvoření veřejné IP adresy** stránky.
3. Nakonfigurujte nastavení s použitím následujících hodnot:

  - **Název**: **VNet1GWIP**
  - **Skladová položka**: **základní**

  ![Vytvoření veřejné IP adresy](./media/create-routebased-vpn-gateway-portal/public-ip-address-name.png "Vytvořit PIP")
4. Klikněte na tlačítko **OK** v dolní části této stránky uložte provedené změny.

## <a name="creategw"></a>Vytvoření brány VPN

1. Ověřte nastavení na **vytvořit bránu virtuální sítě** stránky. V případě potřeby upravte hodnoty.

  ![Vytvořit bránu VPN](./media/create-routebased-vpn-gateway-portal/create-vpn-gateway.png "vytvořit VPN gateway")
2. Klikněte na tlačítko **vytvořit** v dolní části stránky.

Po kliknutí na **vytvořit**, nastavení se ověří a **nasazení brány virtuální sítě** zobrazí dlaždice na řídicím panelu. Brány VPN může trvat až 45 minut. K zobrazení stavu dokončení může být nutné obnovit stránku portálu.

## <a name="viewgw"></a>Zobrazení brány sítě VPN

1. Po vytvoření brány, přejděte na portálu do sítě VNet1. Brána VPN se zobrazí na stránce Přehled jako připojené zařízení.

  ![Připojená zařízení](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "připojená zařízení")

2. V seznamu zařízení, klikněte na tlačítko **VNet1GW** zobrazíte další informace.

  ![Brána VPN zobrazení](./media/create-routebased-vpn-gateway-portal/view-gateway.png "zobrazení VPN gateway")

## <a name="next-steps"></a>Další postup

Po dokončení vytváření brány můžete vytvořit připojení mezi virtuální sítí a jiné virtuální síti. Nebo vytvořte připojení mezi virtuální sítí a místního umístění.

> [!div class="nextstepaction"]
> [Vytvoření připojení site-to-site](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Vytvoření připojení point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Vytvoření připojení k jiné virtuální síti](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)

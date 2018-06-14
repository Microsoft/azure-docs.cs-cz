---
title: 'Vytvořit bránu sítě VPN založené na trasách: portálu Azure | Microsoft Docs'
description: Rychle vytvořte pomocí portálu Azure brána sítě VPN založené na směrování
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
ms.openlocfilehash: 550f655f6eac5a114636978255578eb3753e0d4b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30918128"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Vytvořit pomocí portálu Azure brána sítě VPN založené na směrování

Tento článek vám pomůže rychle vytvořit pomocí portálu Azure brány Azure VPN založené na trasách.  Brána sítě VPN se používá při vytváření připojení VPN k síti na pracovišti. Můžete také použít bránu VPN propojení virtuálních sítí. 

Kroky v tomto článku vytvořte virtuální síť, podsíť, podsíť brány a bránu VPN založené na směrování (brány virtuální sítě). Po dokončení vytvoření brány můžete vytvořit připojení. Tyto kroky vyžadují předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="vnet"></a>Vytvoření virtuální sítě

1. V prohlížeči přejděte na web [Azure Portal](http://portal.azure.com) a přihlaste se pomocí svého účtu Azure.
2. Klikněte na **Vytvořit prostředek**. Do pole **Hledat na Marketplace** zadejte text „Virtuální síť“. Ve vráceném seznamu vyhledejte položku **Virtuální síť** a kliknutím otevřete stránku **Virtuální síť**.
3. U dolního okraje stránky virtuální sítě z **vybrat model nasazení** seznamu, ověřte, že **Resource Manager** je vybraný z rozevíracího seznamu a pak klikněte na tlačítko **vytvořit**. Tím se otevře **vytvořit virtuální síť** stránky.
4. Na stránce **Vytvořit virtuální síť** nakonfigurujte nastavení virtuální sítě. Po vyplnění polí se červený vykřičník změní na zelenou značku zaškrtnutí, pokud jsou znaky zadané do pole platné. Použijte následující hodnoty:

  - **Název**: TestVNet1
  - **Adresní prostor**: 10.1.0.0/16
  - **Předplatné**: Ověřte, že uvedeného odběru je ten, který chcete použít. Předplatná můžete měnit prostřednictvím rozevíracího seznamu.
  - **Skupina prostředků**: TestRG1
  - **Umístění**: východní USA
  - **Podsíť**: front-endu
  - **Rozsah adres**: 10.1.0.0/24

  ![Stránka pro vytvoření virtuální sítě](./media/create-routebased-vpn-gateway-portal/create-virtual-network.png "Stránka pro vytvoření virtuální sítě")
5. Po zadání hodnoty, vyberte **připnout na řídicí panel** snadno najít virtuální síť na řídicím panelu a potom klikněte na **vytvořit**. Po kliknutí na **vytvořit**, zobrazí dlaždice na řídicím panelu, který průběh vaší virtuální sítě. Obsah dlaždice se v průběhu vytváření sítě VNet mění.

## <a name="gwsubnet"></a>Přidat podsíť brány

Podsíť brány obsahuje rezervovaných adres IP, které používají služby brány virtuální sítě. Vytvořte podsíť brány.

1. Na portálu přejděte na virtuální síť, pro kterou chcete vytvořit bránu virtuální sítě.
2. Na stránce virtuální sítě, klikněte na tlačítko **podsítě** rozbalte **VNet1 - podsítě** stránky.
3. Klikněte na tlačítko **+ podsíť brány** v horní části otevřete **přidat podsíť** stránky.

  ![Přidání podsítě brány](./media/create-routebased-vpn-gateway-portal/add-gateway-subnet.png "Přidání podsítě brány")
4. **Název** pro podsíť je automaticky vyplněno požadovaná hodnota "GatewaySubnet". Upravit automaticky vyplněné **rozsahu adres** hodnoty tak, aby odpovídala následující hodnoty:

  **Rozsah (blok CIDR) adres**: 10.1.255.0/27

  ![Přidání podsítě brány](./media/create-routebased-vpn-gateway-portal/gateway-subnet.png "Přidání podsítě brány")
5. Chcete-li vytvořit podsíť brány, klikněte na tlačítko **OK** v dolní části stránky.

## <a name="gwvalues"></a>Konfigurace nastavení brány

1. Na levé straně stránky portálu, klikněte na tlačítko **+ vytvořit prostředek** a typu 'brány virtuální sítě, do vyhledávacího pole, pak stiskněte klávesu **Enter**. V části **Výsledky** vyhledejte položku **Brána virtuální sítě** a klikněte na ni.
2. V dolní části stránky "Brána virtuální sítě", klikněte na položku **vytvořit** otevřete **vytvořit bránu virtuální sítě** stránky.
3. Na stránce **Vytvořit bránu virtuální sítě** zadejte hodnoty pro příslušnou bránu virtuální sítě.

  - **Název**: Vnet1GW
  - **Typ brány**: sítě VPN 
  - **Typ sítě VPN**: založené na směrování
  - **SKU**: VpnGw1
  - **Umístění**: východní USA
  - **Virtuální síť**: klikněte na tlačítko **virtuální sítě nebo zvolte virtuální síť** otevřete **vyberte virtuální síť** stránky. Vyberte **VNet1**.

  ![Konfigurace nastavení brány](./media/create-routebased-vpn-gateway-portal/configure-gateway.png "nakonfigurovat nastavení brány")

## <a name="pip"></a>Vytvoření veřejné IP adresy

Brána sítě VPN, musí mít dynamicky přidělené veřejnou IP adresu. Při vytváření připojení k bráně VPN toto je IP adresa, která vaše místní zařízení připojuje.

1. Vyberte **konfigurace IP brány vytvořit konfigurace první IP** na vyžádání veřejné IP adresy.

  ![První konfigurace protokolu IP](./media/create-routebased-vpn-gateway-portal/add-public-ip-address.png "konfigurace první IP adresy")
2. Na **veřejné IP stránce zvolte**, klikněte na tlačítko **+ vytvořit nový** otevřete **vytvoření veřejné IP adresy** stránky.
3. Nastavení konfigurace s následujícími hodnotami:

  - **Název**: **VNet1GWIP**
  - **Skladová položka**: **základní**

  ![Vytvoření veřejné IP adresy](./media/create-routebased-vpn-gateway-portal/public-ip-address-name.png "Vytvořit PIP")
4. Klikněte na tlačítko **OK** v dolní části této stránky a uložit provedené změny.

## <a name="creategw"></a>Vytvoření brány VPN

1. Ověřte nastavení na **vytvořit bránu virtuální sítě** stránky. V případě potřeby upravte hodnoty.

  ![Vytvořit bránu VPN](./media/create-routebased-vpn-gateway-portal/create-vpn-gateway.png "vytvořit VPN gateway")
2. Klikněte na tlačítko **vytvořit** v dolní části stránky.

Po kliknutí na tlačítko **vytvořit**, nastavení se ověří a **nasazení virtuální síťová brána** se zobrazí dlaždice na řídicím panelu. Brána sítě VPN může trvat až 45 minut. K zobrazení stavu dokončení může být nutné obnovit stránku portálu.

## <a name="viewgw"></a>Zobrazení služby VPN gateway

1. Po vytvoření brány, přejděte do VNet1 na portálu. Brány sítě VPN se zobrazí na stránce Přehled jako připojené zařízení.

  ![Připojená zařízení](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "připojená zařízení")

2. V seznamu zařízení, klikněte na tlačítko **VNet1GW** zobrazíte další informace.

  ![Brána sítě VPN zobrazení](./media/create-routebased-vpn-gateway-portal/view-gateway.png "brány VPN zobrazení")

## <a name="next-steps"></a>Další postup

Po dokončení vytvoření brány můžete vytvořit připojení mezi virtuální sítí a jiné virtuální síti. Nebo vytvořte připojení mezi virtuální sítí a místní umístění.

> [!div class="nextstepaction"]
> [Umožňuje vytvořit připojení site-to-site](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Umožňuje vytvořit připojení point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Umožňuje vytvořit připojení k jiné virtuální síti](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
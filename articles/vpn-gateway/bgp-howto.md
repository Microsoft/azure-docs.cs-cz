---
title: 'Konfigurace protokolu BGP pro VPN Gateway: portál'
titleSuffix: Azure VPN Gateway
description: Tento článek vás provede postupem konfigurace protokolu BGP s Azure VPN Gateway pomocí prostředí PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: db19b1ae017fa7981747b0e7b4c82e97efc61ed3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878880"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways"></a>Postup konfigurace protokolu BGP u bran Azure VPN Gateway

Tento článek vás provede kroky k povolení protokolu BGP pro připojení VPN typu Site-to-Site (S2S) a připojení typu VNet-to-VNet pomocí Azure Portal.

## <a name="about-bgp"></a><a name="about"></a>Informace o protokolu BGP

BGP je standardní směrovací protokol, na internetu běžně používaný k výměně informací o směrování a dostupnosti mezi dvěma nebo více sítěmi. BGP umožňuje branám Azure VPN a místním zařízením VPN, která se označují jako partneři protokolu BGP nebo sousedními sítěmi, vyměnit trasy, které budou informovat obě brány o dostupnosti a dosažitelnosti těchto předpon, aby procházely branami nebo směrovači. Protokol BGP také umožňuje směrování přenosu mezi více sítěmi pomocí šíření tras, které brána s protokolem BGP zjistí od jednoho partnerského uzlu protokolu BGP, do všech dalších partnerských uzlů protokolu BGP.

Další informace o výhodách protokolu BGP a porozumění technickým požadavkům a doporučeních k použití protokolu BGP najdete v tématu [Přehled protokolu BGP se službou Azure VPN Gateway](vpn-gateway-bgp-overview.md).

## <a name="getting-started"></a>Začínáme

Jednotlivé části tohoto článku vám pomůžou vytvořit základní stavební blok pro povolení protokolu BGP v připojení k síti. Pokud dokončíte všechny tři části, sestavíte topologii, jak je znázorněno v diagramu 1.

**Diagram 1**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="Diagram znázorňující architekturu a nastavení sítě" border="false":::

Dohromady můžete kombinovat části a vytvořit tak složitější síť s více segmenty směrování, která vyhovuje vašim potřebám.

### <a name="prerequisites"></a>Předpoklady

Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

## <a name="part-1-configure-bgp-on-the-virtual-network-gateway"></a><a name ="config"></a>Část 1: Konfigurace protokolu BGP v bráně virtuální sítě

V této části vytvoříte a nakonfigurujete virtuální síť, vytvoříte a nakonfigurujete bránu virtuální sítě s parametry BGP a získáte IP adresu partnerského uzlu protokolu BGP Azure. Diagram 2 znázorňuje nastavení konfigurace, která se použijí při práci s kroky v této části.

**Diagram 2**

:::image type="content" source="./media/bgp-howto/bgp-gateway.png" alt-text="Diagram znázorňující nastavení pro bránu virtuální sítě" border="false":::

### <a name="1-create-and-configure-testvnet1"></a>1. vytvoření a konfigurace virtuální sítě testvnet1

V tomto kroku vytvoříte a nakonfigurujete virtuální sítě testvnet1. Pomocí kroků v [kurzu Vytvoření brány](./tutorial-create-gateway-portal.md) vytvořte a nakonfigurujte službu Azure Virtual Network a bránu VPN. Použijte referenční nastavení na snímcích obrazovky níže.

* Virtuální síť:

   :::image type="content" source="./media/bgp-howto/testvnet-1.png" alt-text="Virtuální sítě testvnet1 s odpovídajícími předponami adres":::

* Podsítě:

   :::image type="content" source="./media/bgp-howto/testvnet-1-subnets.png" alt-text="Virtuální sítě testvnet1 podsítě":::

### <a name="2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. Vytvoření brány VPN Gateway pro virtuální sítě testvnet1 s parametry BGP

V tomto kroku vytvoříte bránu VPN s odpovídajícími parametry protokolu BGP.

1. V Azure Portal přejděte na prostředek **Virtual Network brány** z webu Marketplace a vyberte **vytvořit**.

1. Zadejte parametry, jak je znázorněno níže:

   :::image type="content" source="./media/bgp-howto/create-gateway-1.png" alt-text="Vytvořit VNG1":::

1. V části zvýrazněná **Konfigurace protokolu BGP** na stránce nakonfigurujte následující nastavení:

   :::image type="content" source="./media/bgp-howto/create-gateway-1-bgp.png" alt-text="Konfigurace protokolu BGP":::

   * Vyberte možnost **Konfigurovat protokol BGP**  -  **povoleno** pro zobrazení oddílu konfigurace protokolu BGP.

   * Vyplňte číslo ASN (autonomní systém).

   * Pole **IP adresy protokolu BGP APIPA Azure** je volitelné. Pokud vaše místní zařízení VPN používají pro protokol BGP adresu APIPa, musíte vybrat adresu z rozsahu adres APIPa vyhrazené Azure pro VPN, který je od **169.254.21.0** do **169.254.22.255**. V tomto příkladu se používá 169.254.21.11.

   * Pokud vytváříte bránu VPN typu aktivní-aktivní, v části BGP se zobrazí další **druhá vlastní IP adresa protokolu BGP APIPA v Azure**. Zadejte jinou adresu z povoleného rozsahu APIPa (**169.254.21.0** to **169.254.22.255**).

   > [!IMPORTANT]
   >
   > * Ve výchozím nastavení Azure přiřadí privátní IP adresu z rozsahu předpony GatewaySubnet automaticky jako IP adresu BGP Azure na bráně Azure VPN. Vlastní IP adresa APIPa Azure je nutná v případě, že místní zařízení VPN používají adresu APIPa (169.254.0.1 až 169.254.255.254) jako IP adresu protokolu BGP. Azure VPN Gateway zvolí vlastní adresu APIPa, pokud odpovídající prostředek místní síťové brány (místní síť) má adresu APIPa jako IP adresa partnerského uzlu protokolu BGP. Pokud brána místní sítě používá běžnou IP adresu (ne APIPa), Azure VPN Gateway se vrátí k privátní IP adrese z rozsahu GatewaySubnet.
   >
   > * Adresy protokolu BGP APIPa se nesmí překrývat mezi místními zařízeními VPN a všemi připojenými branami Azure VPN.
   >

1. Vyberte možnost **zkontrolovat + vytvořit** a spusťte ověřování. Po úspěšném ověření vyberte **vytvořit** a nasaďte bránu VPN. Pro úplné vytvoření a nasazení brány může trvat až 45 minut. Stav nasazení můžete zobrazit na stránce Přehled pro vaši bránu.

### <a name="3-obtain-the-azure-bgp-peer-ip-addresses"></a>3. Získejte IP adresy partnerského uzlu protokolu BGP Azure.

Po vytvoření brány můžete získat IP adresy partnerského uzlu BGP v bráně Azure VPN. Tyto adresy jsou potřebné ke konfiguraci místních zařízení VPN pro vytváření relací protokolu BGP s bránou Azure VPN Gateway.

1. Přejděte do prostředku brány virtuální sítě a výběrem stránky **Konfigurace** zobrazte informace o konfiguraci protokolu BGP, jak je znázorněno na následujícím snímku obrazovky. Na této stránce můžete zobrazit všechny informace o konfiguraci protokolu BGP v bráně Azure VPN Gateway: ASN, veřejnou IP adresu a odpovídající IP adresy partnerského uzlu protokolu BGP na straně Azure (výchozí a APIPa).

   :::image type="content" source="./media/bgp-howto/vnet-1-gw-bgp.png" alt-text="Brána BGP":::

1. Na stránce **Konfigurace** můžete provést následující změny konfigurace:

   * V případě potřeby můžete aktualizovat adresu ASN nebo IP adresu protokolu BGP APIPa.
   * Pokud máte bránu VPN typu aktivní-aktivní, na této stránce se zobrazí tato stránka s IP adresami veřejné IP adresy, výchozí a APIPa protokolu BGP druhé instance služby Azure VPN Gateway.

1. Pokud jste provedli nějaké změny, vyberte **Save (Uložit** ) a potvrďte změny ve vaší bráně Azure VPN.

## <a name="part-2-configure-bgp-on-cross-premises-s2s-connections"></a><a name ="crosspremises"></a>Část 2: Konfigurace protokolu BGP u připojení S2S mezi místními lokalitami

K navázání připojení mezi různými místy musíte vytvořit *bránu místní sítě* , která bude představovat vaše místní zařízení VPN, a *připojení* k připojení brány VPN k bráně místní sítě, jak je vysvětleno v tématu [vytvoření připojení typu Site-to-site](./tutorial-site-to-site-portal.md). Tento článek obsahuje další vlastnosti, které jsou potřeba k určení parametrů konfigurace protokolu BGP.

**Diagram 3**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises.png" alt-text="Diagram znázorňující protokol IPsec" border="false":::

### <a name="1-configure-bgp-on-the-local-network-gateway"></a>1. konfigurace protokolu BGP v bráně místní sítě

V tomto kroku nakonfigurujete protokol BGP v bráně místní sítě. Jako příklad použijte následující snímek obrazovky. Snímek obrazovky zobrazuje bránu místní sítě (site5) s parametry určenými v diagramu 3.

:::image type="content" source="./media/bgp-howto/create-local-bgp.png" alt-text="Konfigurace protokolu BGP pro bránu místní sítě":::

#### <a name="important-configuration-considerations"></a>Důležité informace o konfiguraci

* ASN a IP adresa partnerského uzlu protokolu BGP se musí shodovat s konfigurací vaší místní sítě VPN.
* **Adresní prostor** můžete ponechat prázdný jenom v případě, že k připojení k této síti používáte protokol BGP. Azure VPN Gateway interně přidá trasu IP adresy partnerského uzlu protokolu BGP k odpovídajícímu tunelu IPsec. Pokud **nepoužíváte protokol BGP** mezi službou Azure VPN Gateway a touto konkrétní sítí, **musíte** zadat seznam platných předpon adres pro **adresní prostor**.
* Volitelně můžete v případě potřeby použít **IP adresu APIPA** (169.254. x. x) jako místní IP adresu partnerského uzlu BGP. Musíte ale taky zadat IP adresu APIPa, jak je popsáno výše v tomto článku pro vaši bránu Azure VPN Gateway, jinak nelze pro toto připojení vytvořit relaci protokolu BGP.
* Během vytváření brány místní sítě můžete zadat informace o konfiguraci protokolu BGP nebo můžete přidat nebo změnit konfiguraci protokolu BGP ze stránky **Konfigurace** prostředku brány místní sítě.

**Příklad**

V tomto příkladu se jako místní IP adresa partnerského uzlu BGP používá adresa APIPa (169.254.100.1):

:::image type="content" source="./media/bgp-howto/local-apipa.png" alt-text="APIPa a BGP brány místní sítě":::

### <a name="2-configure-a-s2s-connection-with-bgp-enabled"></a>2. Konfigurace připojení S2S s povoleným protokolem BGP

V tomto kroku vytvoříte nové připojení s povoleným protokolem BGP. Pokud už máte připojení a chcete na něm povolit protokol BGP, můžete [aktualizovat existující připojení](#update).

#### <a name="to-create-a-connection-with-bgp-enabled"></a>Vytvoření připojení s povoleným protokolem BGP

Pokud chcete vytvořit nové připojení s povoleným protokolem BGP, zadejte na stránce **Přidat připojení** hodnoty a pak zaškrtněte políčko **Povolit protokol BGP** , aby se pro toto připojení povolil protokol BGP. Výběrem možnosti **OK** vytvořte připojení.

:::image type="content" source="./media/bgp-howto/ipsec-connection-bgp.png" alt-text="Připojení přes protokol IPsec mezi místními místy pomocí protokolu BGP":::

#### <a name="to-update-an-existing-connection"></a><a name ="update"></a>Aktualizace existujícího připojení

Pokud chcete změnit možnost protokolu BGP u připojení, přejděte na stránku **Konfigurace** prostředku připojení a pak přepněte možnost **protokolu BGP** tak, jak je zvýrazněno v následujícím příkladu. Výběrem **Uložit** uložte změny.

:::image type="content" source="./media/bgp-howto/update-bgp.png" alt-text="Aktualizace protokolu BGP pro připojení":::

## <a name="part-3-configure-bgp-on-vnet-to-vnet-connections"></a><a name ="v2v"></a>Část 3: Konfigurace protokolu BGP u připojení VNet-to-VNet

Postup povolení nebo zakázání protokolu BGP u připojení typu VNet-to-VNet je stejný jako u kroků S2S v [části 2](#crosspremises). Protokol BGP můžete při vytváření připojení povolit nebo aktualizovat konfiguraci u stávajícího připojení VNet-to-VNet.

>[!NOTE] 
>Připojení typu VNet-to-VNet bez protokolu BGP omezí komunikaci jenom na dvě připojená virtuální sítě. Povolte protokol BGP, aby povoloval přenosové možnosti směrování pro další připojení S2S nebo VNet-to-VNet těchto dvou virtuální sítě.
>

V případě kontextu, který odkazuje na **diagram 4**, by se protokol BGP mezi TestVNet2 a virtuální sítě testvnet1, TestVNet2 se nedozvěděl o trasách pro místní síť, site5 a proto nemohl komunikovat s lokalitou 5. Po povolení protokolu BGP, jak je znázorněno v diagramu 4, budou moci všechny tři sítě komunikovat přes připojení IPsec a VNet-to-VNet.

**Diagram 4**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="Diagram znázorňující úplnou síť" border="false":::

## <a name="next-steps"></a>Další kroky

Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/windows/quick-create-portal.md).
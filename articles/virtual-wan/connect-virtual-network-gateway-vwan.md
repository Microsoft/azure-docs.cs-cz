---
title: Připojení brány virtuální sítě k virtuální síti Azure
description: Tento článek vám pomůže připojit bránu virtuální sítě Azure k bráně Azure Virtual WAN VPN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: 688183bc07aa14d5e5df182d7de0897cec93f0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066235"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>Připojení brány VPN (brána virtuální sítě) k virtuální síti WAN

Tento článek vám pomůže nastavit připojení z brány Azure VPN (brána virtuální sítě) do virtuální sítě Azure (brána VPN). Vytvoření připojení z brány VPN (brána virtuální sítě) k virtuální síti WAN (brána VPN) je podobné nastavení připojení k virtuální síti WAN z lokalit VPN pobočky.

Abychom minimalizovali možné nejasnosti mezi dvěma funkcemi, předvedeme bránu s názvem funkce, na kterou odkazujeme. Například brána virtuální sítě VPN gateway a brána Virtuální SÍTĚ WAN.

## <a name="before-you-begin"></a>Než začnete

Než začnete, vytvořte následující zdroje:

Informace o službě Azure Virtual WAN

* [Vytvořte virtuální wan](virtual-wan-site-to-site-portal.md#openvwan).
* [Vytvořte rozbočovač](virtual-wan-site-to-site-portal.md#hub). Virtuální rozbočovač obsahuje bránu Virtuální SÍTĚ VPN.

Azure Virtual Network

* Vytvořte virtuální síť bez bran virtuální sítě. Ověřte, zda se žádná z podsítí místních sítí nepřekrývá s virtuálními sítěmi, ke kterým se chcete připojit. Pokud chcete vytvořit virtuální síť na webu Azure Portal, přečtěte si [úvodní příručku](../virtual-network/quick-create-portal.md).

## <a name="1-create-an-azure-virtual-network-gateway"></a><a name="vnetgw"></a>1. Vytvoření brány virtuální sítě Azure

Vytvořte bránu virtuální sítě brány brány brány VPN gateway pro vaši virtuální síť v aktivním aktivním režimu pro vaši virtuální síť. Při vytváření brány můžete buď použít existující veřejné IP adresy pro dvě instance brány, nebo můžete vytvořit nové veřejné IP adresy. Tyto veřejné IP adresy se používají při nastavování virtuálních sítí WAN. Další informace o aktivním aktivním režimu naleznete v [tématu Konfigurace připojení aktivní aktivní](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway).

### <a name="active-active-mode-setting"></a><a name="active-active"></a>Nastavení aktivního aktivního režimu

![aktivní-aktivní](./media/connect-virtual-network-gateway-vwan/active.png "aktivní-aktivní")

### <a name="bgp-setting"></a><a name="BGP"></a>Nastavení Protokolu BGP

Protokol ASN protokolu BGP nemůže být 65515. 66515 bude používat Virtuální WAN Azure.

![BGP](./media/connect-virtual-network-gateway-vwan/bgp.png "Bgp")

### <a name="public-ip-addresses"></a><a name="pip"></a>Veřejné IP adresy

Po vytvoření brány přejděte na stránku **Vlastnosti.** Vlastnosti a nastavení konfigurace budou podobné následujícímu příkladu. Všimněte si dvou veřejných IP adres, které se používají pro bránu.

![Vlastnosti](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. Vytvořte virtuální sítě WAN VPN

Chcete-li vytvořit weby virtuální sítě VPN, přejděte na virtuální síť WAN a v části **Připojení**vyberte **servery VPN**. V této části vytvoříte dvě virtuální sítě VPN, které odpovídají bránám virtuální sítě, které jste vytvořili v předchozí části.

1. Vyberte **možnost +Vytvořit web**.
2. Na stránce **Vytvořit weby VPN** zadejte následující hodnoty:

   * **Region** – (stejná oblast jako brána virtuální sítě Azure VPN Gateway)
   * **Dodavatel zařízení** – zadejte dodavatele zařízení (libovolný název).
   * **Soukromý adresní prostor** - (Zadejte hodnotu, nebo ponechat prázdné, pokud je povoleno BGP)
   * **Border Gateway Protocol** – (Nastavit **povolení,** pokud má brána BGP brány Azure VPN Gateway povolenou protokol BGP)
   * **Připojení k rozbočovačům** (Vyberte rozbočovač, který jste vytvořili v požadavcích z rozevíracího portálu)
3. V části **Odkazy**zadejte tyto hodnoty:

   * **Název zprostředkovatele** – zadejte název odkazu a název zprostředkovatele (libovolný název)
   * **Rychlost** - rychlost (libovolné číslo)
   * **IP adresa** - Zadejte IP adresu (stejně jako první veřejná IP adresa zobrazená pod vlastnostmi brány virtuální sítě (VPN Gateway)IP Address - Enter IP address (same as the first public IP address shown under the (VPN Gateway) virtual network gateway properties)
   * **Adresa Protokolu BGP** a **adresa ASN** – adresa protokolu BGP a asn. Ty musí být stejné jako jedna z IP adres druhé strany Protokolu BGP a ASN z brány virtuální sítě brány VPN Gateway, kterou jste nakonfigurovali v [kroku 1](#vnetgw).
4. Chcete-li vytvořit web, zkontrolujte a vyberte **Potvrdit.**
5. Opakováním předchozích kroků vytvořte druhou lokalitu tak, aby odpovídala druhé instanci brány virtuální sítě brány brány VPN Gateway. Budete mít stejné nastavení, s výjimkou použití druhé veřejné IP adresy a druhé IP adresy BGP z konfigurace BRÁNY VPN.
6. Nyní máte dva weby úspěšně zřízené a můžete přejít k další části ke stažení konfiguračních souborů.

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. Stáhněte si konfigurační soubory VPN

V této části stáhnete konfigurační soubor VPN pro každý web, který jste vytvořili v předchozí části.

1. V horní části stránky Virtuální síť **VPN** vyberte **web**a pak vyberte **možnost Stáhnout konfiguraci VPN mezi lokalitami**. Azure vytvoří konfigurační soubor s nastavením.

   ![stáhnout konfigurační soubor](./media/connect-virtual-network-gateway-vwan/download.png "stáhnout")
2. Stáhněte a otevřete konfigurační soubor.
3. Opakujte tyto kroky pro druhý web. Jakmile budete mít oba konfigurační soubory otevřené, můžete přejít k další části.

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. Vytvoření brány místní sítě

V této části vytvoříte dvě místní síťové brány Azure VPN Gateway. Konfigurační soubory z předchozího kroku obsahují nastavení konfigurace brány. Tato nastavení slouží k vytvoření a konfiguraci místních síťových bran azure brány VPN Gateway.

1. Pomocí těchto nastavení vytvořte bránu místní sítě. Informace o vytvoření brány místní sítě brány VPN gateway naleznete v článku [Vytvoření brány místní sítě](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway).

   * **IP adresa** – Použijte ip adresu Instance0 zobrazenou pro *konfiguraci brány* z konfiguračního souboru.
   * **Protokol BGP** – Pokud je připojení přes protokol BGP, vyberte **konfigurovat nastavení protokolu BGP** a zadejte asn '65515'. Zadejte IP adresu partnerské strany Protokolu BGP. Pro *konfiguraci brány* z konfiguračního souboru použijte adresu Instance0 BgpPeeringAddresses.
   * **Předplatné, skupina prostředků a umístění** jsou stejné jako pro centrum Virtuální WAN.
2. Zkontrolujte a vytvořte bránu místní sítě. Brána místní sítě by měla vypadat podobně jako v tomto příkladu.

   ![stáhnout konfigurační soubor](./media/connect-virtual-network-gateway-vwan/lng1.png "instance0")
3. Opakujte tyto kroky k vytvoření jiné brány místní sítě, ale tentokrát použijte hodnoty Instance1 namísto hodnot Instance0 z konfiguračního souboru.

   ![stáhnout konfigurační soubor](./media/connect-virtual-network-gateway-vwan/lng2.png "instance1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. Vytvoření připojení

V této části vytvoříte připojení mezi bránou místní sítě brány VPN a bránou virtuální sítě. Postup vytvoření připojení brány VPN naleznete v [tématu Konfigurace připojení](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection).

1. Na portálu přejděte na bránu virtuální sítě a klikněte na **Připojení**. V horní části okna Připojení klikněte na **+ Přidat** a otevřete stránku **Přidat připojení**.
2. Na stránce **Přidat připojení** nakonfigurujte pro připojení následující hodnoty:

   * **Název**: Zadejte název připojení.
   * **Typ připojení:** Vybrat **možnost Site-to-site (IPSec)**
   * **Brána virtuální sítě**: Tato hodnota je pevně daná, protože se připojujete z této brány.
   * **Brána místní sítě:** Toto připojení připojí bránu virtuální sítě k bráně místní sítě. Zvolte jednu z bran místní sítě, které jste vytvořili dříve.
   * **Sdílený klíč:** Zadejte sdílený klíč.
   * **Protokol IKE:** Zvolte protokol IKE.
   * **Protokol BGP:** Zvolte **Povolit protokol BGP,** pokud je připojení přes BGP.
3. Vytvořte připojení kliknutím na **OK**.
4. Připojení si můžete zobrazit na stránce **Připojení** brány virtuální sítě.

   ![Připojení](./media/connect-virtual-network-gateway-vwan/connect.png "připojení")
5. Opakováním předchozích kroků vytvořte druhé připojení. Pro druhé připojení vyberte jinou bránu místní sítě, kterou jste vytvořili.

## <a name="6-test-connections"></a><a name="test"></a>6. Zkušební spojení

Připojení můžete otestovat vytvořením dvou virtuálních počítačů, jednoho na straně brány virtuální sítě brány VPN Gateway a jednoho ve virtuální síti pro virtuální síť WAN a následným příkazem ping na dva virtuální počítače.

1. Vytvořte virtuální počítač ve virtuální síti (Test1-VNet) pro Azure VPN Gateway (Test1-VNG). Nevytvářejte virtuální počítač v GatewaySubnet.
2. Vytvořte jinou virtuální síť pro připojení k virtuální síti WAN. Vytvořte virtuální počítač v podsíti této virtuální sítě. Tato virtuální síť nemůže obsahovat žádné brány virtuální sítě. Virtuální síť můžete rychle vytvořit pomocí kroků prostředí PowerShell v článku [o připojení mezi lokalitami.](virtual-wan-site-to-site-portal.md#vnet) Nezapomeňte změnit hodnoty před spuštěním rutiny.
3. Připojte virtuální síť k rozbočovači Virtuální WAN. Na stránce virtuální sítě WAN vyberte **Připojení virtuální sítě**a potom **+Přidat připojení**. Na stránce **Add connection** (Přidat připojení) zadejte údaje do následujících polí:

    * **Connection name** (Název připojení) – zadejte název připojení.
    * **Hubs** (Rozbočovače) – vyberte rozbočovač, který chcete k tomuto připojení přidružit.
    * **Subscription** (Předplatné) – ověřte předplatné.
    * **Virtual network** (Virtuální síť) – vyberte virtuální síť, kterou chcete připojit k tomuto rozbočovači. Virtuální síť nesmí mít existující bránu virtuální sítě.
4. Chcete-li vytvořit připojení k virtuální síti, klepněte na tlačítko **OK.**
5. Připojení je teď nastavené mezi virtuálními zařízeními. Měli byste být schopni ping jeden virtuální počítače z druhého, pokud neexistují žádné brány firewall nebo jiné zásady, které blokují komunikaci.

## <a name="next-steps"></a>Další kroky

Postup konfigurace vlastní chod ipsec, najdete v [tématu Konfigurace vlastní zásady Protokolu IPsec pro virtuální síť WAN](virtual-wan-custom-ipsec-portal.md).
Další informace o virtuální wan, najdete [v tématu o Azure Virtual WAN](virtual-wan-about.md) a Azure Virtual WAN [FAQ](virtual-wan-faq.md).
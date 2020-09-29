---
title: Připojení brány virtuální sítě k virtuální síti WAN Azure
description: Tento článek vám pomůže připojit bránu virtuální sítě Azure k bráně VPN Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 29f5b549bd5f5dbc421487739bb1eb8c7f120bb0
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91441029"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>Připojit VPN Gateway (bránu virtuální sítě) k virtuální síti WAN

Tento článek vám pomůže nastavit připojení z Azure VPN Gateway (bránu virtuální sítě) na virtuální síť WAN Azure (VPN Gateway). Vytvoření připojení z VPN Gateway (Brána virtuální sítě) k virtuální síti WAN (Brána sítě VPN) se podobá nastavení připojení k virtuální síti WAN z lokalit sítě VPN.

Aby nedošlo k nejasnostem mezi dvěma funkcemi, vytvoříme bránu s názvem funkce, na kterou odkazujeme. Například VPN Gateway bránu virtuální sítě a bránu VPN Virtual WAN.

## <a name="before-you-begin"></a>Než začnete

Než začnete, vytvořte následující prostředky:

Azure Virtual WAN

* [Vytvořte virtuální síť WAN](virtual-wan-site-to-site-portal.md#openvwan).
* [Vytvořte centrum](virtual-wan-site-to-site-portal.md#hub). Virtuální rozbočovač obsahuje virtuální síť WAN VPN Gateway.

Azure Virtual Network

* Vytvořte virtuální síť bez bran virtuální sítě. Ověřte, že se žádná z podsítí místních sítí nepřekrývá s virtuálními sítěmi, ke kterým se chcete připojit. Pokud chcete vytvořit virtuální síť v Azure Portal, přečtěte si [rychlý Start](../virtual-network/quick-create-portal.md).

## <a name="1-create-a-vpn-gateway-virtual-network-gateway"></a><a name="vnetgw"></a>1. Vytvoření brány virtuální sítě VPN Gateway

Vytvořte bránu virtuální sítě **VPN Gateway** v režimu aktivní-aktivní pro vaši virtuální síť. Když vytvoříte bránu, můžete použít stávající veřejné IP adresy pro tyto dvě instance brány nebo můžete vytvořit nové veřejné IP adresy. Tyto veřejné IP adresy budete používat při nastavování virtuálních sítí WAN. Další informace o branách VPN a postupech konfigurace aktivních – aktivní najdete v tématu [Konfigurace bran VPN typu aktivní-aktivní](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway).

### <a name="active-active-mode-setting"></a><a name="active-active"></a>Nastavení režimu aktivní – aktivní

Na stránce **Konfigurace** brány virtuální sítě Povolte režim aktivní-aktivní.

![aktivní – aktivní](./media/connect-virtual-network-gateway-vwan/active.png "aktivní-aktivní")

### <a name="bgp-setting"></a><a name="BGP"></a>Nastavení protokolu BGP

Na stránce **Konfigurace** brány virtuální sítě můžete nakonfigurovat **ASN protokolu BGP**. Změňte ASN protokolu BGP. ASN protokolu BGP nemůže být 65515. 66515 bude využívat Azure Virtual WAN.

![Snímek obrazovky se stránkou konfigurace brány virtuální sítě s vybraným nastavením ASN protokolu BGP.](./media/connect-virtual-network-gateway-vwan/bgp.png "#a0")

### <a name="public-ip-addresses"></a><a name="pip"></a>Veřejné IP adresy

Po vytvoření brány přejděte na stránku **vlastností** . Vlastnosti a nastavení konfigurace budou podobné jako v následujícím příkladu. Všimněte si dvou veřejných IP adres, které se používají pro bránu.

![vlastnosti](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. Vytvoření virtuálních sítí VPN sítě WAN

Pokud chcete vytvořit virtuální sítě sítě VPN, přejděte k virtuální síti WAN a v části **připojení**vyberte **sítě VPN**. V této části vytvoříte dvě virtuální sítě VPN sítě WAN, které odpovídají branám virtuální sítě, které jste vytvořili v předchozí části.

1. Vyberte **+ vytvořit web**.
2. Na stránce **vytvořit weby sítě VPN** zadejte následující hodnoty:

   * **Oblast** – stejná oblast jako Brána virtuální sítě Azure VPN Gateway.
   * **Dodavatel zařízení** – zadejte dodavatele zařízení (libovolný název).
   * **Privátní adresní prostor** – zadejte hodnotu nebo nechte pole prázdné, pokud je protokol BGP povolený.
   * **Border Gateway Protocol** – nastavte na **Povolit** , pokud má brána virtuální sítě Azure VPN Gateway povolený protokol BGP.
   * **Připojit k rozbočovačům** – vyberte rozbočovač, který jste vytvořili v části požadavky, z rozevíracího seznamu. Pokud se nezobrazuje rozbočovač, ověřte, že jste pro svůj rozbočovač vytvořili bránu VPN typu Site-to-site.
3. V části **odkazy**zadejte následující hodnoty:

   * **Název poskytovatele** – zadejte název odkazu a název poskytovatele (libovolný název).
   * **Rychlost** (libovolné číslo).
   * **IP adresa** – zadejte IP adresu (totéž jako první veřejná IP adresa zobrazená ve vlastnostech brány virtuální sítě (VPN Gateway)).
   * **Adresa BGP** a **ASN** -adresa BGP a ASN. Musí se shodovat s jednou z IP adres partnerských uzlů BGP a číslem ASN z brány VPN Gateway virtuální sítě, kterou jste nakonfigurovali v [kroku 1](#vnetgw).
4. Zkontrolujte a vyberte **Potvrdit** pro vytvoření webu.
5. Zopakováním předchozích kroků vytvořte druhou lokalitu, která bude odpovídat druhé instanci VPN Gateway brány virtuální sítě. Zachováte stejné nastavení, s výjimkou druhé veřejné IP adresy a druhé IP adresy partnerského uzlu protokolu BGP z konfigurace VPN Gateway.
6. Nyní máte úspěšné zřízení dvou webů a můžete přejít k další části stažení konfiguračních souborů.

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. Stáhněte si konfigurační soubory sítě VPN.

V této části si stáhnete konfigurační soubor sítě VPN pro každý z webů, které jste vytvořili v předchozí části.

1. V horní části stránky **sítě VPN** virtuálních sítí WAN vyberte **lokalitu**a pak vyberte možnost **Stáhnout konfiguraci sítě VPN typu Site-to-site**. Azure vytvoří konfigurační soubor s nastavením.

   ![Snímek obrazovky zobrazující stránku VPN Sites s vybranou akcí stáhnout konfiguraci site-to-Site VPN](./media/connect-virtual-network-gateway-vwan/download.png "stáhnout")
2. Stáhněte a otevřete konfigurační soubor.
3. Opakujte tento postup pro druhý Web. Jakmile budete mít oba konfigurační soubory otevřené, můžete přejít k další části.

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. vytvoření bran místní sítě

V této části vytvoříte dvě brány místní sítě Azure VPN Gateway. Konfigurační soubory z předchozího kroku obsahují nastavení konfigurace brány. Pomocí těchto nastavení můžete vytvořit a nakonfigurovat brány místní sítě Azure VPN Gateway.

1. Pomocí těchto nastavení vytvořte bránu místní sítě. Informace o tom, jak vytvořit VPN Gateway bránu místní sítě, najdete v článku VPN Gateway [Vytvoření brány místní sítě](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway).

   * **IP adresa** – použijte IP adresu Instance0 zobrazenou pro *gatewayconfiguration* z konfiguračního souboru.
   * **BGP** – Pokud je připojení přes protokol BGP, vyberte **Konfigurovat nastavení protokolu BGP** a zadejte číslo ASN ' 65515 '. Zadejte IP adresu partnerského uzlu protokolu BGP. Pro *gatewayconfiguration* z konfiguračního souboru použijte Instance0 BgpPeeringAddresses.
   * **Předplatné, skupina prostředků a umístění** jsou stejné jako pro virtuální síť WAN.
2. Zkontrolujte a vytvořte bránu místní sítě. Brána místní sítě by měla vypadat podobně jako v tomto příkladu.

   ![Snímek obrazovky zobrazující stránku konfigurace s zvýrazněnou IP adresou a vybranou možností konfigurovat nastavení protokolu BGP](./media/connect-virtual-network-gateway-vwan/lng1.png "instance0")
3. Zopakováním těchto kroků vytvořte jinou bránu místní sítě, ale tentokrát použijte hodnoty ' položku instance1 ' namísto hodnot ' Instance0 ' z konfiguračního souboru.

   ![stažení konfiguračního souboru](./media/connect-virtual-network-gateway-vwan/lng2.png "položku instance1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. vytvoření připojení

V této části vytvoříte připojení mezi VPN Gateway brány místní sítě a bránu virtuální sítě. Postup vytvoření připojení VPN Gateway najdete v tématu [Konfigurace připojení](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection).

1. Na portálu přejděte na bránu virtuální sítě a klikněte na **připojení**. V horní části okna Připojení klikněte na **+ Přidat** a otevřete stránku **Přidat připojení**.
2. Na stránce **Přidat připojení** nakonfigurujte pro připojení následující hodnoty:

   * **Název**: Zadejte název připojení.
   * **Typ připojení:** Vyberte **site-to-Site (IPSec)** .
   * **Brána virtuální sítě**: Tato hodnota je pevně daná, protože se připojujete z této brány.
   * **Brána místní sítě:** Toto připojení připojí bránu virtuální sítě k bráně místní sítě. Vyberte jednu z bran místní sítě, kterou jste vytvořili dříve.
   * **Sdílený klíč:** Zadejte sdílený klíč.
   * **Protokol IKE:** Vyberte protokol IKE.
3. Vytvořte připojení kliknutím na **OK**.
4. Připojení si můžete zobrazit na stránce **Připojení** brány virtuální sítě.

   ![Připojení](./media/connect-virtual-network-gateway-vwan/connect.png "připojení")
5. Zopakováním předchozích kroků vytvořte druhé připojení. Pro druhé připojení vyberte jinou bránu místní sítě, kterou jste vytvořili.
6. Pokud jsou připojení přes protokol BGP, po vytvoření připojení přejděte k připojení a vyberte **Konfigurace**. Na stránce **Konfigurace** pro protokol **BGP**vyberte **povoleno**. Pak klikněte na **Uložit**. Opakujte pro druhé připojení.

## <a name="6-test-connections"></a><a name="test"></a>6. testování připojení

Připojení můžete otestovat tak, že vytvoříte dva virtuální počítače, jednu na straně VPN Gateway brány virtuální sítě a jednu ve virtuální síti pro virtuální síť WAN a pak otestujete oba virtuální počítače.

1. Vytvořte virtuální počítač ve virtuální síti (Test1-VNet) pro Azure VPN Gateway (Test1-VNG). Nevytvářejte virtuální počítač v GatewaySubnet.
2. Vytvořte další virtuální síť pro připojení k virtuální síti WAN. Vytvoří virtuální počítač v podsíti této virtuální sítě. Tato virtuální síť nemůže obsahovat žádné brány virtuální sítě. Virtuální síť můžete rychle vytvořit pomocí kroků PowerShellu v článku [připojení typu Site-to-site](virtual-wan-site-to-site-portal.md#vnet) . Nezapomeňte změnit hodnoty před spuštěním rutin.
3. Připojte virtuální síť k virtuálnímu centru WAN. Na stránce pro virtuální síť WAN vyberte připojení k **virtuální síti**a potom **+ Přidat připojení**. Na stránce **Add connection** (Přidat připojení) zadejte údaje do následujících polí:

    * **Connection name** (Název připojení) – zadejte název připojení.
    * **Hubs** (Rozbočovače) – vyberte rozbočovač, který chcete k tomuto připojení přidružit.
    * **Subscription** (Předplatné) – ověřte předplatné.
    * **Virtual network** (Virtuální síť) – vyberte virtuální síť, kterou chcete připojit k tomuto rozbočovači. Virtuální síť nesmí mít existující bránu virtuální sítě.
4. Kliknutím na tlačítko **OK** vytvořte připojení k virtuální síti.
5. Připojení se teď nastavuje mezi virtuálními počítači. Měli byste být schopni testovat jeden virtuální počítač z druhé, pokud neexistují žádné brány firewall nebo jiné zásady, které tuto komunikaci blokují.

## <a name="next-steps"></a>Další kroky

Postup konfigurace vlastní zásady protokolu IPsec najdete v tématu [Konfigurace vlastní zásady IPSec pro virtuální síť WAN](virtual-wan-custom-ipsec-portal.md).
Další informace o virtuální síti WAN najdete v tématu [o Azure Virtual WAN](virtual-wan-about.md) a [nejčastějších dotazech k Azure Virtual WAN](virtual-wan-faq.md).

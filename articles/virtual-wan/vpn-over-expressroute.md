---
title: 'Konfigurace šifrování ExpressRoute: IPsec přes ExpressRoute pro virtuální síť Azure'
description: V tomto kurzu se dozvíte, jak pomocí virtuální sítě Azure WAN vytvořit připojení VPN mezi lokalitami přes soukromý partnerský vztah ExpressRoute.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: b1e6305d142530ab19849f61f12a122d0c6434aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059303"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>Šifrování ExpressRoute: IPsec přes ExpressRoute pro virtuální WAN

Tento článek ukazuje, jak pomocí azure virtuální sítě WAN vytvořit připojení VPN IPsec/IKE z místní sítě do Azure přes privátní partnerský vztah okruhu Azure ExpressRoute. Tato technika může zajistit šifrovaný přenos mezi místními sítěmi a virtuálními sítěmi Azure přes ExpressRoute, aniž by bylo možné přejít přes veřejný internet nebo pomocí veřejných IP adres.

## <a name="topology-and-routing"></a>Topologie a směrování

Následující diagram znázorňuje příklad připojení vpn přes soukromý partnerský vztah ExpressRoute:

![VPN přes ExpressRoute](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

Diagram znázorňuje síť v rámci místní sítě připojené k bráně VPN centra Azure přes soukromý partnerský vztah ExpressRoute. Zřízení konektivity je jednoduché:

1. Navázání připojení ExpressRoute s okruhem ExpressRoute a privátním partnerským partnerem.
2. Nastolit připojení VPN, jak je popsáno v tomto článku.

Důležitým aspektem této konfigurace je směrování mezi místními sítěmi a Azure přes cesty ExpressRoute i VPN.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Provoz z místních sítí do Azure

Pro provoz z místních sítí do Azure se předpony Azure (včetně virtuálního rozbočovače a všech virtuálních sítí s paprskem připojených k rozbočovači) inzerují prostřednictvím protokolu BGP privátního partnerského vztahu ExpressRoute i protokolu BGP sítě VPN. Výsledkem jsou dvě síťové trasy (cesty) směrem k Azure z místních sítí:

- Jeden přes cestu chráněnou protokolem IPsec
- Jeden přímo přes ExpressRoute *bez* ochrany IPsec 

Chcete-li použít šifrování pro komunikaci, musíte se ujistit, že pro síť připojenou k síti VPN v diagramu jsou trasy Azure přes místní bránu VPN upřednostňovány před přímou cestou ExpressRoute.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Provoz z Azure do místních sítí

Stejný požadavek platí pro provoz z Azure do místních sítí. Chcete-li zajistit, aby cesta IPsec byla upřednostňována před přímou cestou ExpressRoute (bez iPsec), máte dvě možnosti:

- Inzerujte konkrétnější předpony v relaci protokolu BGP sítě VPN pro síť připojenou k síti VPN. Můžete inzerovat větší rozsah, který zahrnuje síť připojenou k síti VPN prostřednictvím soukromého partnerského vztahu ExpressRoute a pak konkrétnější rozsahy v relaci protokolu BGP sítě VPN. Můžete například inzerovat 10.0.0.0/16 přes ExpressRoute a 10.0.1.0/24 přes VPN.

- Inzerujte nesouvislé předpony pro VPN a ExpressRoute. Pokud jsou oblasti sítě připojené k síti VPN nesouvislé od jiných připojených sítí ExpressRoute, můžete inzerovat předpony v relacích VPN a ExpressRoute BGP. Můžete například inzerovat 10.0.0.0/24 přes ExpressRoute a 10.0.1.0/24 přes VPN.

V obou těchto příkladech Azure odešle provoz na 10.0.1.0/24 přes připojení VPN, nikoli přímo přes ExpressRoute bez ochrany VPN.

> [!WARNING]
> Pokud inzerujete *stejné* předpony přes připojení ExpressRoute i VPN, Azure použije cestu ExpressRoute přímo bez ochrany VPN.
>

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. Vytvoření virtuální sítě WAN a rozbočovače s bránami

Před pokračováním musí být na místě následující prostředky Azure a odpovídající místní konfigurace:

- Virtuální WAN Azure
- Virtuální rozbočovač WAN s [bránou ExpressRoute](virtual-wan-expressroute-portal.md) a [bránou VPN](virtual-wan-site-to-site-portal.md)

Postup vytvoření virtuální sítě WAN azure a rozbočovače s přidružením ExpressRoute najdete v [tématu Vytvoření přidružení ExpressRoute pomocí azure virtuální sítě WAN](virtual-wan-expressroute-portal.md). Postup vytvoření brány VPN ve virtuální síti WAN najdete v [tématu Vytvoření připojení mezi lokalitami pomocí virtuální sítě Azure .](virtual-wan-site-to-site-portal.md)

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. Vytvoření webu pro místní síť

Prostředek webu je stejný jako weby VPN bez expressroute pro virtuální síť WAN. IP adresa místního zařízení VPN teď může být buď privátní IP adresa, nebo veřejná IP adresa v místní síti dosažitelná prostřednictvím soukromého partnerského vztahu ExpressRoute vytvořeného v kroku 1.

> [!NOTE]
> IP adresa pro místní zařízení VPN *musí* být součástí předpon adres inzerovaných do virtuálního centra WAN prostřednictvím privátního partnerského vztahu Azure ExpressRoute.
>

1. Přejděte ve svém prohlížeči na portál Azure. 
1. Vyberte vytvořenou wan. Na stránce WAN vyberte v části **Připojení** **položku Weby VPN**.
1. Na stránce **Weby VPN** vyberte **+Vytvořit web**.
1. Na stránce **Create site** (Vytvořit lokalitu) zadejte údaje do následujících polí:
   * **Předplatné**: Ověřte předplatné.
   * **Skupina prostředků**: Vyberte nebo vytvořte skupinu prostředků, kterou chcete použít.
   * **Oblast:** Zadejte oblast Azure pro prostředek lokality VPN.
   * **Název**: Zadejte název, pod kterým chcete odkazovat na místní web.
   * **Dodavatel zařízení**: Zadejte dodavatele místního zařízení VPN.
   * **Border Gateway Protocol**: Pokud vaše místní síť používá protokol BGP, vyberte možnost Povolit.
   * **Soukromý adresní prostor**: Zadejte adresní prostor IP, který se nachází na místním webu. Provoz určený pro tento adresní prostor je směrován do místní sítě prostřednictvím brány VPN.
   * **Rozbočovače**: Vyberte jeden nebo více rozbočovačů pro připojení tohoto webu VPN. Vybrané rozbočovače musí mít již vytvořené brány VPN.
1. Vyberte **další: Odkazy >** pro nastavení propojení VPN:
   * **Název odkazu**: Název, podle kterého chcete odkazovat na toto připojení.
   * **Název poskytovatele**: Název poskytovatele internetových služeb pro tento web. Pro místní síť ExpressRoute je to název poskytovatele služeb ExpressRoute.
   * **Rychlost**: Rychlost internetového odkazu nebo okruhu ExpressRoute.
   * **IP adresa**: Veřejná IP adresa zařízení VPN, které se nachází na místním webu. Nebo pro ExpressRoute v místním prostředí je to privátní IP adresa zařízení VPN přes ExpressRoute.

   Pokud je bgp povoleno, bude se vztahovat na všechna připojení vytvořená pro tento web v Azure. Konfigurace protokolu BGP ve virtuální síti WAN je ekvivalentní konfiguraci protokolu BGP v bráně Azure VPN. 
   
   Vaše místní adresa partnerské sítě BGP *se nesmí* shodovat s IP adresou vaší sítě VPN do zařízení nebo adresního prostoru virtuální sítě webu VPN. Pro ip adresu partnera Protokolu BGP použijte na zařízení VPN jinou ADRESU IP. Může se jednat o adresu přiřazenou rozhraní zpětné smyčky v zařízení. Však *nemůže* být APIPA (169.254.* x*. *x*) adresu. Zadejte tuto adresu v odpovídající bráně místní sítě, která představuje umístění. Požadavky protokolu BGP najdete [v tématu O protokolu BGP s bránou Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

1. Vyberte **další: Kontrola + vytvoření >** pro kontrolu hodnot nastavení a vytvoření webu VPN. Pokud jste vybrali **centra** pro připojení, bude připojení navázáno mezi místní sítí a bránou VPN.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. Aktualizace nastavení připojení VPN pro použití ExpressRoute

Po vytvoření webu VPN a připojení k rozbočovači nakonfigurujte připojení takto pro použití soukromého partnerského vztahu ExpressRoute:

1. Vraťte se na stránku prostředků virtuální sítě WAN a vyberte prostředek rozbočovače. Nebo přejděte z webu VPN do připojeného rozbočovače.
1. V části **Připojení**vyberte **možnost VPN (Site-to-Site).**
1. Vyberte tři tečky (**...**) na webu VPN přes ExpressRoute a vyberte **Upravit připojení VPN k tomuto rozbočovači**.
1. V **případě použití privátní IP adresy Azure**vyberte **Ano**. Toto nastavení konfiguruje bránu VPN v rozbočovači tak, aby místo veřejných IP adres používala privátní IP adresy v rozsahu adres rozbočovače v bráně. Tím zajistíte, že provoz z místní sítě prochází cestsoukromé partnerský vztah ExpressRoute spíše než pomocí veřejného internetu pro toto připojení VPN. Následující snímek obrazovky ukazuje nastavení.

   ![Nastavení pro použití privátní IP adresy pro připojení VPN](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
1. Vyberte **Uložit**.

Po uložení změn bude brána VPN v centru používat privátní IP adresy v bráně VPN k navázání připojení IPsec/IKE s místním zařízením VPN přes ExpressRoute.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. Získejte privátní IP adresy pro bránu VPN v rozbočovači

Stáhněte si konfiguraci zařízení VPN a získejte privátní IP adresy brány VPN v centru. Tyto adresy potřebujete ke konfiguraci místního zařízení VPN.

1. Na stránce centra vyberte **vpn (site-to-site)** v části **Připojení**.
1. V horní části stránky **Přehled** vyberte **Stáhnout konfiguraci VPN**. 

   Azure vytvoří účet úložiště ve skupině prostředků "microsoft-network-[location]," kde je *umístění* umístění WAN. Po použití konfigurace na vaše zařízení VPN můžete tento účet úložiště odstranit.
1. Po vytvoření souboru vyberte odkaz, který chcete stáhnout.
1. Použijte konfiguraci ve svém zařízení VPN.

### <a name="vpn-device-configuration-file"></a>Konfigurační soubor zařízení VPN

Konfigurační soubor zařízení obsahuje nastavení, která se mají použít při konfiguraci místního zařízení VPN. Při prohlížení souboru si všimněte následujících informací:

* **vpnSiteConfiguration**: Tato část označuje podrobnosti o zařízení nastavené jako web, který se připojuje k virtuální síti WAN. Obsahuje název a veřejnou IP adresu pobočkového zařízení.
* **vpnSiteConnections**: Tato část obsahuje informace o následujících nastaveních:

    * Adresní prostor virtuální sítě virtuálního rozbočovače.<br/>Příklad:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * Adresní prostor virtuálních sítí, které jsou připojeny k rozbočovači.<br>Příklad:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * IP adresy brány VPN virtuálního rozbočovače. Vzhledem k tomu, že každé připojení brány VPN se skládá ze dvou tunelových propojení v konfiguraci aktivní a aktivní, zobrazí se v tomto souboru obě ADRESY IP. V tomto příkladu `Instance0` `Instance1` se zobrazí a pro každý web a jsou to soukromé IP adresy namísto veřejných IP adres.<br>Příklad:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * Podrobnosti o konfiguraci pro připojení brány VPN, jako je například BGP a předsdílený klíč. Předsdílený klíč se automaticky vygeneruje za vás. Připojení na stránce **Přehled** můžete vždy upravit pro vlastní předsdílený klíč.
  
### <a name="example-device-configuration-file"></a>Příklad konfiguračního souboru zařízení

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>Konfigurace zařízení VPN

Pokud potřebujete pokyny ke konfiguraci zařízení, můžete použít pokyny na [stránce se skripty konfigurace zařízení VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts), pokud vezmete v úvahu následující upozornění:

* Pokyny na stránce zařízení VPN nejsou napsány pro virtuální síť WAN. Virtuální hodnoty WAN z konfiguračního souboru však můžete použít k ruční konfiguraci zařízení VPN. 
* Ke stažení zařízení konfigurační skripty, které jsou pro bránu VPN nefungují pro virtuální WAN, protože konfigurace je odlišná.
* Nová virtuální WAN může podporovat iKEv1 a IKEv2.
* Virtuální síť WAN může používat pouze zařízení VPN založená na trasách a pokyny zařízení.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. Zobrazení virtuální sítě WAN

1. Přejděte na virtuální WAN.
1. Na stránce **Přehled** představuje každý bod na mapě rozbočovač.
1. V části **Rozbočovače a připojení** můžete zobrazit stav připojení rozbočovače, webu, oblasti a sítě VPN. Můžete také zobrazit bajty dovnitř a ven.

## <a name="7-monitor-a-connection"></a><a name="connectmon"></a>7. Sledování připojení

Vytvořte připojení pro monitorování komunikace mezi virtuálním počítačem (VM) Azure a vzdálenou lokalitou. Informace o tom, jak nastavit monitorování připojení, najdete v článku [Monitorování síťové komunikace](~/articles/network-watcher/connection-monitor.md). Zdrojové pole je IP virtuálního počítače v Azure a cílová IP adresa je IP lokality.

## <a name="8-clean-up-resources"></a><a name="cleanup"></a>8. Vyčistit zdroje

Pokud již tyto prostředky nepotřebujete, můžete pomocí [skupiny prostředků a](/powershell/module/az.resources/remove-azresourcegroup) všech prostředků, které obsahuje, odebrat skupinu prostředků. Spusťte následující příkaz Prostředí `myResourceGroup` PowerShell a nahraďte název skupiny prostředků:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

Tento článek vám pomůže vytvořit připojení VPN přes soukromý partnerský vztah ExpressRoute pomocí virtuální sítě WAN. Další informace o virtuální masce WAN a souvisejících funkcích najdete v [přehledu virtuální sítě WAN](virtual-wan-about.md).

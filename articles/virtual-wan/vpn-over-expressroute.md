---
title: 'Konfigurace šifrování ExpressRoute: IPsec over ExpressRoute pro Azure Virtual WAN'
description: V tomto kurzu se naučíte, jak pomocí Azure Virtual WAN vytvořit připojení VPN typu Site-to-site přes privátní partnerský vztah ExpressRoute.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: bbce84ad917da71ab363b20f3aef9da79ed3f2b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91827982"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>Šifrování ExpressRoute: protokol IPsec over ExpressRoute pro virtuální síť WAN

V tomto článku se dozvíte, jak pomocí Azure Virtual WAN navázat připojení VPN IPsec/IKE z vaší místní sítě k Azure prostřednictvím privátního partnerského vztahu okruhu Azure ExpressRoute. Tato technika může zajistit zašifrovaný přenos mezi místními sítěmi a virtuálními sítěmi Azure přes ExpressRoute, aniž by se museli přenášet přes veřejný Internet nebo pomocí veřejných IP adres.

## <a name="topology-and-routing"></a>Topologie a směrování

Následující diagram ukazuje příklad připojení VPN přes privátní partnerský vztah ExpressRoute:

:::image type="content" source="./media/vpn-over-expressroute/vwan-vpn-over-er.png" alt-text="VPN přes ExpressRoute":::

Diagram zobrazuje síť v místní síti připojené ke službě Azure hub VPN Gateway přes privátní partnerský vztah ExpressRoute. Vytváření připojení je jednoduché:

1. Navažte připojení ExpressRoute pomocí okruhu ExpressRoute a privátního partnerského vztahu.
2. Vytvořte připojení VPN, jak je popsáno v tomto článku.

Důležitým aspektem této konfigurace je směrování mezi místními sítěmi a Azure prostřednictvím cest ExpressRoute a VPN.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Provoz z místních sítí do Azure

V případě provozu z místních sítí do Azure jsou předpony Azure (včetně virtuálního rozbočovače a všech virtuálních sítí připojených k rozbočovači) inzerovány prostřednictvím protokolu BGP privátního partnerského vztahu ExpressRoute a protokolu BGP VPN. Výsledkem je dvě síťové trasy (cesty) směrem k Azure z místních sítí:

- Jedna přes cestu chráněnou protokolem IPsec
- Jedno přímo přes ExpressRoute *bez* ochrany IPsec 

Pokud chcete pro komunikaci použít šifrování, musíte se ujistit, že je v diagramu připojená k síti VPN, trasy Azure přes místní bránu VPN jsou upřednostňovány přes přímou cestu ExpressRoute.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Provoz z Azure do místních sítí

Stejný požadavek se vztahuje na provoz z Azure do místních sítí. Chcete-li zajistit, aby byla cesta protokolu IPsec upřednostňována přes přímou cestu ExpressRoute (bez protokolu IPsec), máte dvě možnosti:

- Inzerovat konkrétnější předpony v relaci protokolu BGP sítě VPN pro síť připojenou k síti VPN. Můžete inzerovat větší rozsah, který zahrnuje síť připojenou k síti VPN přes privátní partnerský vztah ExpressRoute, a pak v relaci protokolu BGP sítě VPN více konkrétních rozsahů. Můžete například inzerovat 10.0.0.0/16 přes ExpressRoute a 10.0.1.0/24 prostřednictvím sítě VPN.

- Inzerovat nesouvislé předpony pro VPN a ExpressRoute. Pokud jsou síťové rozsahy připojené k síti VPN nesouvislé z jiných sítí propojených s ExpressRoute, můžete předpony inzerovat v relacích VPN a ExpressRoute BGP. Můžete například inzerovat 10.0.0.0/24 přes ExpressRoute a 10.0.1.0/24 prostřednictvím sítě VPN.

V obou těchto příkladech pošle Azure provoz do 10.0.1.0/24 přes připojení VPN, nikoli přímo přes ExpressRoute bez ochrany VPN.

> [!WARNING]
> Pokud budete *stejné* předpony inzerovat přes ExpressRoute i připojení VPN, Azure použije cestu ExpressRoute přímo bez ochrany VPN.
>

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. vytvoření virtuální sítě WAN a centra s branami

Následující prostředky Azure a odpovídající místní konfigurace musí být zavedeny, než budete pokračovat:

- Virtuální síť WAN Azure
- Virtuální centrum WAN s [bránou ExpressRoute](virtual-wan-expressroute-portal.md) a [bránou VPN](virtual-wan-site-to-site-portal.md)

Postup vytvoření virtuální sítě Azure a centra s přidružením ExpressRoute najdete v tématu [vytvoření přidružení ExpressRoute pomocí Azure Virtual WAN](virtual-wan-expressroute-portal.md). Postup vytvoření brány VPN ve virtuální síti WAN najdete v tématu [vytvoření připojení typu Site-to-site pomocí Azure Virtual WAN](virtual-wan-site-to-site-portal.md).

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. vytvoření webu pro místní síť

Prostředek sítě je stejný jako lokalita VPN bez ExpressRoute pro virtuální síť WAN. IP adresa místního zařízení VPN teď může být privátní IP adresa nebo veřejná IP adresa v místní síti dosažitelná prostřednictvím privátního partnerského vztahu ExpressRoute vytvořeného v kroku 1.

> [!NOTE]
> IP adresa pro místní zařízení VPN *musí* být součástí předpon adres inzerovaných k virtuálnímu centru WAN prostřednictvím privátního partnerského vztahu Azure ExpressRoute.
>

1. V prohlížeči přejdete na Azure Portal. 
1. Vyberte centrum, které jste vytvořili. Na stránce rozbočovač virtuální sítě WAN v části **připojení**vyberte sítě **VPN**.
1. Na stránce sítě **VPN** vyberte **+ vytvořit lokalitu**.
1. Na stránce **Create site** (Vytvořit lokalitu) zadejte údaje do následujících polí:
   * **Předplatné**: Ověřte předplatné.
   * **Skupina prostředků**: vyberte nebo vytvořte skupinu prostředků, kterou chcete použít.
   * **Oblast**: zadejte oblast Azure pro prostředek sítě VPN.
   * **Název**: zadejte název, podle kterého chcete odkazovat na místní lokalitu.
   * **Dodavatel zařízení**: zadejte dodavatele místního zařízení VPN.
   * **Border Gateway Protocol**: Pokud vaše místní síť používá protokol BGP, vyberte Enable (Povolit).
   * **Privátní adresní prostor**: zadejte adresní prostor IP adres umístěný ve vaší místní lokalitě. Provoz určený pro tento adresní prostor je směrován do místní sítě přes bránu VPN.
   * **Centra**: vyberte aspoň jedno rozbočovače, který chcete připojit k tomuto webu VPN. Vybraná centra musí mít už vytvořené brány VPN Gateway.
1. Vyberte **Další: odkazy >** pro nastavení připojení VPN:
   * **Název odkazu**: název, podle kterého chcete na toto připojení odkazovat.
   * **Název zprostředkovatele**: název poskytovatele internetových služeb pro tuto lokalitu. V případě ExpressRoute místní sítě se jedná o název poskytovatele služby ExpressRoute.
   * **Rychlost**: rychlost propojení internetových služeb nebo okruhu ExpressRoute.
   * **IP adresa**: veřejná IP adresa zařízení VPN, která se nachází na vaší místní lokalitě. Nebo pro místní ExpressRoute je to privátní IP adresa zařízení VPN prostřednictvím ExpressRoute.

   Pokud je protokol BGP povolený, bude platit pro všechna připojení vytvořená pro tento web v Azure. Konfigurace protokolu BGP ve virtuální síti WAN je rovnocenná konfiguraci protokolu BGP v bráně Azure VPN. 
   
   Vaše místní adresa partnerského uzlu BGP *nesmí* být SHODNÁ s IP adresou vaší sítě VPN k zařízení nebo adresnímu prostoru virtuální sítě sítě VPN. Pro IP adresu partnerského uzlu BGP použijte jinou IP adresu na zařízení VPN. Může se jednat o adresu přiřazenou rozhraní zpětné smyčky v zařízení. Nejedná se *can't* však o APIPA (169,254).* x*. *x*) adresa. Zadejte tuto adresu v odpovídající bráně místní sítě, která představuje umístění. Požadavky protokolu BGP najdete v tématu [informace o protokolu BGP s Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

1. Vyberte **Další: Zkontrolujte a vytvořte >** a zkontrolujte hodnoty nastavení a vytvořte lokalitu VPN. Pokud jste vybrali **rozbočovače** k připojení, připojení se vytvoří mezi místní sítí a bránou VPN centra.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. aktualizujte nastavení připojení VPN tak, aby používalo ExpressRoute.

Po vytvoření lokality VPN a připojení k centru použijte následující postup ke konfiguraci připojení pro použití privátního partnerského vztahu ExpressRoute:

1. Vraťte se na stránku prostředku virtuální sítě WAN a vyberte prostředek centra. Nebo přejděte z sítě VPN do připojeného centra.

   :::image type="content" source="./media/vpn-over-expressroute/hub-selection.png" alt-text="VPN přes ExpressRoute":::
1. V části **připojení**vyberte **VPN (site-to-site)**.

   :::image type="content" source="./media/vpn-over-expressroute/vpn-select.png" alt-text="VPN přes ExpressRoute":::
1. Vyberte tři tečky (**...**) na webu VPN přes ExpressRoute a vyberte **Upravit připojení VPN k tomuto centru**.

   :::image type="content" source="./media/vpn-over-expressroute/config-menu.png" alt-text="VPN přes ExpressRoute":::
1. Pro možnost **použít privátní IP adresu Azure**vyberte **Ano**. Nastavení nakonfiguruje bránu VPN centra pro použití privátních IP adres v rámci rozsahu adres centra v bráně pro toto připojení místo veřejných IP adres. Tím se zajistí, že přenos z místní sítě projde cesty privátního partnerského vztahu ExpressRoute místo použití veřejného Internetu pro toto připojení k síti VPN. Následující snímek obrazovky ukazuje toto nastavení:

   :::image type="content" source="./media/vpn-over-expressroute/vpn-link-configuration.png" alt-text="VPN přes ExpressRoute" border="false":::
1. Vyberte **Uložit**.

Po uložení změn budou brány VPN centra používat privátní IP adresy v bráně VPN k navázání připojení IPsec/IKE k místnímu zařízení VPN přes ExpressRoute.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. Získejte privátní IP adresy pro bránu VPN centra.

Stáhněte si konfiguraci zařízení VPN a získejte privátní IP adresy brány VPN centra. Tyto adresy budete potřebovat ke konfiguraci místního zařízení VPN.

1. Na stránce centra vyberte v části **připojení**možnost **VPN (site-to-site)** .
1. V horní části stránky **Přehled** vyberte **Stáhnout konfiguraci sítě VPN**. 

   Azure vytvoří účet úložiště ve skupině prostředků Microsoft-Network-[location], kde *umístění* je umístění sítě WAN. Po použití konfigurace na zařízení VPN můžete tento účet úložiště odstranit.
1. Po vytvoření souboru vyberte odkaz pro stažení.
1. Použijte konfiguraci ve svém zařízení VPN.

### <a name="vpn-device-configuration-file"></a>Konfigurační soubor zařízení VPN

Konfigurační soubor zařízení obsahuje nastavení, která se použijí při konfiguraci místního zařízení VPN. Při prohlížení souboru si všimněte následujících informací:

* **vpnSiteConfiguration**: Tato část označuje podrobnosti o zařízení, které se nastavily jako lokalita, která se připojuje k virtuální síti WAN. Obsahuje název a veřejnou IP adresu zařízení pobočky.
* **vpnSiteConnections**: v této části najdete informace o následujících nastaveních:

    * Adresní prostor virtuální sítě virtuálního centra<br/>Příklad:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * Adresní prostor virtuálních sítí, které jsou připojené k rozbočovači.<br>Příklad:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * IP adresy brány VPN virtuálního rozbočovače. Vzhledem k tomu, že se každé připojení brány VPN skládá ze dvou tunelů v konfiguraci aktivní-aktivní, zobrazí se obě IP adresy uvedené v tomto souboru. V tomto příkladu vidíte `Instance0` a `Instance1` pro každou lokalitu a místo veřejných IP adres se jedná o privátní IP adresy.<br>Příklad:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * Podrobnosti konfigurace pro připojení brány VPN, například protokol BGP a předsdílený klíč. Předsdílený klíč se automaticky vygeneruje za vás. Připojení můžete vždy upravit na stránce **Přehled** pro vlastní předsdílený klíč.
  
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

* Pokyny na stránce zařízení VPN nejsou zapsány pro virtuální síť WAN. K ruční konfiguraci zařízení VPN ale můžete použít hodnoty virtuální sítě WAN z konfiguračního souboru. 
* Skripty pro konfiguraci zařízení ke stažení, které jsou pro bránu VPN, nefungují pro virtuální síť WAN, protože konfigurace se liší.
* Nová virtuální síť WAN podporuje jak IKEv1, tak IKEv2.
* Virtuální síť WAN může používat jenom zařízení VPN založená na trasách a pokyny pro zařízení.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. Prohlédněte si virtuální síť WAN

1. Přejít na virtuální síť WAN.
1. Na stránce **Přehled** představuje každý bod na mapě rozbočovač.
1. V části **centra a připojení** můžete zobrazit stav připojení k centru, lokalitám, oblastem a sítím VPN. Můžete také zobrazit bajty v nebo v.

## <a name="6-monitor-a-connection"></a><a name="connectmon"></a>6. monitorování připojení

Vytvořte připojení pro monitorování komunikace mezi virtuálním počítačem Azure a vzdáleným webem. Informace o tom, jak nastavit monitorování připojení, najdete v článku [Monitorování síťové komunikace](~/articles/network-watcher/connection-monitor.md). Pole zdroj je IP adresa virtuálního počítače v Azure a cílová IP adresa je adresa IP lokality.

## <a name="7-clean-up-resources"></a><a name="cleanup"></a>7. vyčištění prostředků

Pokud už tyto prostředky nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít [příkaz Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) . Spusťte následující příkaz prostředí PowerShell a nahraďte `myResourceGroup` názvem vaší skupiny prostředků:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

Tento článek vám pomůže vytvořit připojení VPN prostřednictvím privátního partnerského vztahu ExpressRoute pomocí virtuální sítě WAN. Další informace o virtuální síti WAN a souvisejících funkcích najdete v tématu [Přehled virtuálních sítí WAN](virtual-wan-about.md).

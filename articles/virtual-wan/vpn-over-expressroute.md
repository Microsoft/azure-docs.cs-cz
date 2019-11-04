---
title: Vytvoření připojení VPN typu Site-to-site přes privátní partnerský vztah ExpressRoute ve službě Azure Virtual WAN | Microsoft Docs
description: V tomto kurzu se naučíte, jak pomocí Azure Virtual WAN vytvořit připojení VPN typu Site-to-site přes privátní partnerský vztah ExpressRoute.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 10/11/2019
ms.author: cherylmc
Customer intent: I want to connect my on-premises networks to my VNets using S2S VPN connection over my ExpressRoute private peering using Azure Virtual WAN.
ms.openlocfilehash: 6272d6fe6f8c35c06a8121e10be2dd5a2e5512a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515028"
---
# <a name="create-a-site-to-site-vpn-connection-over-expressroute-private-peering-using-azure-virtual-wan"></a>Vytvoření připojení VPN typu Site-to-site přes privátní partnerský vztah ExpressRoute pomocí Azure Virtual WAN

V tomto článku se dozvíte, jak pomocí virtuální sítě WAN navázat připojení VPN IPsec/IKE z vaší místní sítě k Azure prostřednictvím privátního partnerského vztahu okruhu ExpressRoute. To může zajistit zašifrovaný přenos mezi místními sítěmi a virtuálními sítěmi Azure přes ExpressRoute, a to bez toho, aby se prochází veřejným internetem nebo pomocí veřejných IP adres.

## <a name="topology-and-routing"></a>Topologie a směrování

Následující diagram ukazuje příklad připojení VPN přes ExpressRoute Private peering:

![VPN přes ExpressRoute](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

Diagram zobrazuje síť v místní síti připojené ke službě Azure hub VPN Gateway přes privátní partnerský vztah ExpressRoute. Vytváření připojení je jednoduché:

1. Navázání připojení ExpressRoute pomocí okruhu ExpressRoute a privátního partnerského vztahu
2. Navažte připojení VPN, jak je popsané v tomto dokumentu.

Důležitým aspektem této konfigurace je směrování mezi místními sítěmi a Azure prostřednictvím cest ExpressRoute a VPN.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Provoz z místních sítí do Azure

V případě provozu z místních sítí do Azure budou předpony Azure (včetně virtuálního rozbočovače a všech virtuálních sítí připojených k rozbočovači) inzerovány prostřednictvím protokolu BGP privátního partnerského vztahu ExpressRoute a protokolu BGP sítě VPN. Výsledkem bude, že budou dvě síťové trasy (cesty) směrem k Azure z místních sítí. jednu přes cestu chráněnou protokolem IPsec a jednu přímo přes ExpressRoute **bez** ochrany IPsec. Abyste se ujistili, že se pro komunikaci používá šifrování, musíte se ujistit, že je v diagramu síť připojená k síti VPN, trasy Azure přes místní bránu VPN jsou upřednostňovány přes přímou cestu ExpressRoute.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Provoz z Azure do místních sítí

Stejný požadavek se vztahuje na provoz z Azure do místních sítí. Chcete-li zajistit, aby byla cesta protokolu IPsec upřednostňována přes přímou cestu ExpressRoute (bez protokolu IPsec), máte dvě možnosti:

- Inzerovat konkrétnější předpony v relaci protokolu BGP sítě VPN pro síť připojenou k síti VPN. Můžete inzerovat větší rozsah zahrnující "síť připojenou k síti VPN" prostřednictvím privátního partnerského vztahu ExpressRoute a potom v relaci protokolu BGP VPN více konkrétních rozsahů. Můžete například inzerovat 10.0.0.0/16 přes ExpressRoute a 10.0.1.0/24 prostřednictvím sítě VPN.

- Inzerovat nesouvislé předpony pro VPN a ExpressRoute. Pokud jsou rozsahy sítě připojené k síti VPN nesouvislé z jiné ExpressRoute připojené sítě, můžete předpony inzerovat v relacích VPN a ExpressRoute BGP. Můžete například inzerovat 10.0.0.0/24 přes ExpressRoute a 10.0.1.0/24 prostřednictvím sítě VPN.

V obou těchto příkladech pošle Azure provoz do 10.0.1.0/24 přes připojení VPN, nikoli přímo přes ExpressRoute bez ochrany VPN.

> [!WARNING]
> Pokud budete **stejné** předpony inzerovat přes ExpressRoute i připojení VPN, Azure **použije cestu ExpressRoute přímo bez ochrany VPN**.
>

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. vytvoření virtuální sítě WAN a centra s branami

Než budete pokračovat, je potřeba, abyste měli k dismístě následující prostředky Azure a odpovídající místní konfigurace:

1. Virtuální síť WAN Azure
2. Virtuální centrum WAN s [bránou ExpressRoute](virtual-wan-expressroute-portal.md) a [bránou VPN](virtual-wan-site-to-site-portal.md)

Postup vytvoření služby Azure Virtual WAN a centra s přidružením ExpressRoute najdete v tématu [vytvoření přidružení ExpressRoute pomocí Azure Virtual](virtual-wan-expressroute-portal.md) WAN a [vytvoření připojení typu Site-to-site pomocí Azure Virtual WAN](virtual-wan-site-to-site-portal.md) , kde najdete postup vytvoření sítě VPN. brána ve virtuální síti WAN.

## <a name="site"></a>2. vytvoření webu pro místní síť

Prostředek sítě je stejný jako u virtuálních sítí WAN, které nejsou ExpressRoute servery VPN. Klíčovým aspektem je, že IP adresa místního zařízení VPN teď může být buď privátní IP adresa, nebo veřejná IP adresa v místní síti dosažitelná prostřednictvím privátního partnerského vztahu ExpressRoute vytvořeného v kroku 1.

> [!NOTE]
> Místní IP adresa zařízení VPN musí být součástí předpon adres inzerovaných k virtuálnímu centru WAN prostřednictvím privátního partnerského vztahu Azure ExpressRoute.
>

1. V prohlížeči přejděte na Azure Portal. Klikněte na síť WAN, kterou jste vytvořili. Na stránce WAN v části **připojení**klikněte na **sítě VPN** a otevřete stránku sítě VPN.

2. Na stránce **Lokality VPN** klikněte na **+Vytvořit lokalitu**.

3. Na stránce **Create site** (Vytvořit lokalitu) zadejte údaje do následujících polí:

   * **Subscription** (Předplatné) – ověřte předplatné.
   * **Skupina prostředků** – vyberte nebo vytvořte skupinu prostředků, kterou chcete použít.
   * **Oblast** – oblast Azure pro prostředek sítě VPN.
   * **Název** – název, podle kterého chcete odkazovat na místní lokalitu.
   * **Dodavatel zařízení** – dodavatel místního zařízení VPN.
   * **Border Gateway Protocol** – vyberte povolit, pokud vaše místní síť používá protokol BGP.
   * **Private address space** (Privátní adresní prostor) – prostor IP adres, který se nachází v místní lokalitě. Provoz určený pro tento adresní prostor je směrován do místní sítě přes bránu VPN.
   * **Centra** – vyberte jedno nebo více Center pro připojení tohoto serveru VPN. Vybraná centra musí mít už vytvořené brány VPN Gateway.

4. Klikněte na **Další: odkazy >** pro nastavení připojení VPN:

   * **Název propojení** – název, podle kterého chcete na toto připojení odkazovat.
   * **Název poskytovatele** – název poskytovatele internetových služeb pro tuto lokalitu. V případě ExpressRoute místní sítě název poskytovatele služby ExpressRoute.
   * **Rychlost** – rychlost propojení internetových služeb nebo okruhu ExpressRoute.
   * **IP adresa** – veřejná IP adresa zařízení VPN, která se nachází na vaší místní lokalitě. Nebo, v případě ExpressRoute v místním prostředí, soukromou IP adresu zařízení VPN prostřednictvím ExpressRoute.

   Pokud je protokol BGP povolený, bude platit pro všechna připojení vytvořená pro tento web v Azure. Konfigurace protokolu BGP ve virtuální síti WAN je rovnocenná konfiguraci protokolu BGP v bráně Azure VPN. Vaše místní adresa partnerského uzlu BGP *nesmí* být SHODNÁ s IP adresou vaší sítě VPN a adresním prostorem virtuální sítě VPN. Pro IP adresu partnerského uzlu BGP použijte jinou IP adresu na zařízení VPN. Může se jednat o adresu přiřazenou rozhraní zpětné smyčky v zařízení. Nejedná se však o APIPA (169,254). *x*. *x*) adresa. Adresu zadejte v odpovídající bráně místní sítě reprezentující umístění. Požadavky protokolu BGP najdete v tématu [informace o protokolu BGP s Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

5. Klikněte na **Další: zkontrolovat + vytvořit >** a zkontrolujte hodnoty nastavení a vytvořte lokalitu VPN. Pokud jste vybrali **rozbočovače** k připojení, připojení se vytvoří mezi místní sítí a bránou VPN centra.

## <a name="hub"></a>3. aktualizujte nastavení připojení VPN tak, aby používalo ExpressRoute.

Po vytvoření lokality VPN a připojení k centru použijte následující postup ke konfiguraci připojení pro použití privátního partnerského vztahu ExpressRoute:

1. Vraťte se na stránku prostředku virtuální sítě WAN a klikněte na prostředek centra. Nebo přejděte z sítě VPN do připojeného centra.

2. V části **připojení**klikněte na **VPN (site-to-site)** .

3. Klikněte na "..." na webu VPN přes ExpressRoute a vyberte**Upravit připojení VPN k tomuto centru**.

4. V části**použít privátní IP adresu Azure**vyberte Ano. Nastavení nakonfiguruje bránu VPN centra pro použití privátních IP adres v rámci rozsahu adres centra v bráně pro toto připojení místo veřejných IP adres. Tím se zajistí, že přenos z místní sítě projde cesty privátního partnerského vztahu ExpressRoute místo použití veřejného Internetu pro toto připojení k síti VPN. Následující snímek obrazovky ukazuje okno nastavení.

   ![Nastavení připojení VPN](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
5. Klikněte na **Uložit**.

Po uložení bude Brána VPN centra používat privátní IP adresy v bráně VPN k navázání připojení IPsec/IKE k místnímu zařízení VPN přes ExpressRoute.

## <a name="associate"></a>4. získání privátních IP adres centra VPN Gateway

Stáhněte si konfiguraci zařízení VPN, abyste získali privátní IP adresy brány VPN centra. Ty jsou potřeba ke konfiguraci místního zařízení VPN.

1. Na stránce centra klikněte v části **připojení** na **síť VPN (site-to-site)** .

2. V horní části stránky přehled klikněte na **Stáhnout konfiguraci sítě VPN**. Azure vytvoří účet úložiště ve skupině prostředků ' Microsoft-Network-[location] ', kde umístění je umístění sítě WAN. Až tuto konfiguraci použijete ve svých zařízeních VPN, můžete tento účet úložiště odstranit.

3. Jakmile se dokončí vytváření souboru, můžete ho kliknutím na odkaz stáhnout.

4. Použijte konfiguraci ve svém zařízení VPN.

### <a name="understanding-the-vpn-device-configuration-file"></a>Vysvětlení konfiguračního souboru zařízení VPN

Konfigurační soubor zařízení obsahuje nastavení, které se má použít při konfiguraci místního zařízení VPN. Při prohlížení souboru si všimněte následujících informací:

* **vpnSiteConfiguration** – tato část udává podrobnosti o zařízení nastaveném jako lokalita, která se připojuje k virtuální síti WAN. Obsahuje název a veřejnou IP adresu zařízení pobočky.
* **vpnSiteConnections –** V této části najdete informace o následujících nastaveních:

    * **Adresní prostor** virtuální sítě virtuálních rozbočovačů<br/>Příklad:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * **Adresní prostor** virtuálních sítí připojených k rozbočovači<br>Příklad:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * **IP adresy** brány sítě VPN virtuálního rozbočovače. Vzhledem k tomu, že každé připojení vpngateway se skládá ze dvou tunelů v konfiguraci aktivní-aktivní, zobrazí se obě IP adresy uvedené v tomto souboru. V tomto příkladu se zobrazí "Instance0" a "položku instance1" pro každou lokalitu a jsou privátními IP adresami namísto veřejných IP adres.<br>Příklad:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * **Podrobnosti konfigurace připojení Vpngateway** , jako je protokol BGP, předsdílený klíč atd. PSK je předsdílený klíč, který se automaticky vygeneruje za vás. V případě vlastního předsdíleného klíče můžete připojení upravit na stránce Overview (Přehled).
  
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

* Pokyny na stránce zařízení VPN nejsou určené pro službu Virtual WAN, můžete ale použít hodnoty služby Virtual WAN z konfiguračního souboru a nakonfigurovat zařízení VPN ručně. 
* Skripty konfigurace zařízení ke stažení, které jsou určené pro službu VPN Gateway, pro službu Virtual WAN nefungují, protože se konfigurace liší.
* Nová služba Virtual WAN může podporovat protokol IKEv1 i IKEv2.
* Virtual WAN smí používat jenom zařízení VPN a pokyny pro zařízení založené na trasách.

## <a name="viewwan"></a>5. Prohlédněte si virtuální síť WAN

1. Přejděte na virtuální síť WAN.

2. Na stránce Overview (Přehled) každý bod na mapě představuje jeden rozbočovač. Podržením ukazatele na některém z těchto bodů zobrazíte souhrn stavu rozbočovače.

3. V části Hubs and connections (Rozbočovače a připojení) můžete zjistit stav rozbočovače, lokalitu, oblast, stav připojení VPN a přijaté a odeslané bajty.

## <a name="viewhealth"></a>6. zobrazení stavu prostředku

1. Přejděte na svoji síť WAN.

2. Na stránce sítě WAN v části **SUPPORT + Troubleshooting** (Podpora a řešení potíží) klikněte na **Health** (Stav) a prohlédněte si stav svého prostředku.

## <a name="connectmon"></a>7. monitorování připojení

Vytvořte připojení pro monitorování komunikace mezi virtuálním počítačem Azure a vzdálenou lokalitou. Informace o tom, jak nastavit monitorování připojení, najdete v článku [Monitorování síťové komunikace](~/articles/network-watcher/connection-monitor.md). Do pole zdroje zadejte IP adresu virtuálního počítače v Azure a cílovou IP adresou je IP adresa lokality.

## <a name="cleanup"></a>8. vyčištění prostředků

Pokud už tyto prostředky nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít [příkaz Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) . Položku myResourceGroup nahraďte názvem vaší skupiny prostředků a spusťte následující příkaz PowerShellu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

Tento článek vám pomůže vytvořit připojení VPN přes privátní partnerský vztah ExpressRoute pomocí virtuální sítě WAN. Další informace o virtuální síti WAN a dalších souvisejících funkcích najdete na stránce s [přehledem virtuální sítě WAN](virtual-wan-about.md) .

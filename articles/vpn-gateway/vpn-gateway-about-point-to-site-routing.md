---
title: 'Brána Azure VPN: O směrování P2S'
description: Tento článek vám pomůže pochopit, jak se směrování VPN z bodu na web chová.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: anzaman
ms.openlocfilehash: 4821f2eb694a36cf0570008b3e62ce39999c58d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239754"
---
# <a name="about-point-to-site-vpn-routing"></a>Směrování VPN typu Point-to-Site

Tento článek vám pomůže pochopit, jak se směrování VPN azure point-to-site chová. Chování směrování virtuální sítě P2S závisí na klientském ošetřovaném systému, protokolu používaném pro připojení VPN a vzájemném připojení virtuálních sítí.

Azure aktuálně podporuje dva protokoly pro vzdálený přístup, IKEv2 a SSTP. IKEv2 je podporován v mnoha klientských operačních systémech včetně Windows, Linux, MacOS, Android a iOS. SSTP je podporována pouze v systému Windows. Pokud provedete změnu topologie sítě a máte klienty VPN systému Windows, musí být balíček klienta VPN pro klienty systému Windows stažen a znovu nainstalován, aby byly změny použity na klienta.

> [!NOTE]
> Tento článek se vztahuje pouze na IKEv2.
>

## <a name="about-the-diagrams"></a><a name="diagrams"></a>O diagramech

Existuje celá řada různých diagramů v tomto článku. Každá část zobrazuje jinou topologii nebo konfiguraci. Pro účely tohoto článku připojení Site-to-Site (S2S) a VNet-to-VNet fungují stejným způsobem, protože obě jsou tunely IPsec. Všechny brány VPN v tomto článku jsou založeny na trasách.

## <a name="one-isolated-vnet"></a><a name="isolatedvnet"></a>Jedna izolovaná virtuální síť

Připojení brány VPN typu point-to-site v tomto příkladu je pro virtuální síť, která není připojená nebo partnerský vztah s jinou virtuální sítí (VNet1). V tomto příkladu mohou klienti přistupovat k síti VNet1.

![izolované směrování virtuální sítě](./media/vpn-gateway-about-point-to-site-routing/1.jpg "izolované směrování virtuální sítě")

### <a name="address-space"></a>Adresní prostor

* Virtuální síť1: 10.1.0.0/16

### <a name="routes-added"></a>Přidané trasy

* Trasy přidané do klientů se systémem Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané ke klientům, kteří nejsou se systémem Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti systému Windows mají přístup k síti VNet1

* Klienti, kteří nejsou systémy Windows, mají přístup k síti VNet1

## <a name="multiple-peered-vnets"></a><a name="multipeered"></a>Více partnerských virtuálních sítí

V tomto příkladu je připojení brány VPN point-to-site pro virtuální síť 1. VNet1 je partnerský s Virtuální síť2. Virtuální síť 2 je partnerský vztah s Virtuální sítí 3. VNet1 je partnerský s VNet4. Neexistuje žádný přímý partnerský vztah mezi virtuální sítí 1 a virtuální sítí 3. VNet1 má "Povolit průchod brány" a VNet2 a VNet4 mají povoleno "Používat vzdálené brány".

Klienti používající Systém Windows mohou přistupovat k přímo partnerským virtuálním sítím, ale klient VPN musí být znovu stažen, pokud jsou provedeny změny partnerského vztahu virtuální sítě nebo topologie sítě. Klienti, kteří nejsou systémy Windows, mají přístup k přímo partnerským virtuálním sítím. Přístup není přenositý a je omezen pouze na přímo partnerské virtuální sítě.

![více partnerských virtuálních sítí](./media/vpn-gateway-about-point-to-site-routing/2.jpg "více partnerských virtuálních sítí")

### <a name="address-space"></a>Adresní prostor:

* Virtuální síť1: 10.1.0.0/16

* Virtuální síť 2: 10.2.0.0/16

* Vnet3: 10.3.0.0/16

* Virtuální síť 4: 10.4.0.0/16

### <a name="routes-added"></a>Přidané trasy

* Trasy přidané ke klientům se systémem Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Trasy přidané ke klientům, kteří nejsou se systémem Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti systému Windows mohou přistupovat k sítím VNet1, VNet2 a VNet4, ale klient VPN musí být znovu stažen, aby se změny topologie projevily.

* Klienti, kteří nejsou systémy Windows, mají přístup k sítím VNet1, VNet2 a VNet4.

## <a name="multiple-vnets-connected-using-an-s2s-vpn"></a><a name="multis2s"></a>Více virtuálních sítí připojených pomocí S2S VPN

V tomto příkladu je připojení brány VPN point-to-site pro virtuální síť 1. VNet1 se připojí k Síti VNet2 pomocí připojení VPN site-to-site. VNet2 je připojený k Síti VNet3 pomocí připojení VPN site-to-site. Neexistuje žádné přímé partnerský vztah nebo připojení VPN mezi sítěmi Site-to-Site mezi virtuální sítí 1 a virtuální sítí3. Všechna připojení mezi lokalitami nejsou spuštěna protokol BGP pro směrování.

Klienti používající systém Windows nebo jiný podporovaný operační systém mají přístup pouze k virtuální síti 1. Pro přístup k dalším virtuálním sítím musí být použito protokol BGP.

![více virtuálních sítí a S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "více virtuálních sítí a S2S")

### <a name="address-space"></a>Adresní prostor

* Virtuální síť1: 10.1.0.0/16

* Virtuální síť 2: 10.2.0.0/16

* Vnet3: 10.3.0.0/16

### <a name="routes-added"></a>Přidané trasy

* Trasy přidané do klientů se systémem Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané ke klientům, kteří nejsou s Windows: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti systému Windows mají přístup pouze k virtuální síti 1.

* Klienti, kteří nejsou systémy Windows, mají přístup pouze k virtuální síti 1.

## <a name="multiple-vnets-connected-using-an-s2s-vpn-bgp"></a><a name="multis2sbgp"></a>Více virtuálních sítí připojených pomocí S2S VPN (BGP)

V tomto příkladu je připojení brány VPN point-to-site pro virtuální síť 1. VNet1 se připojí k Síti VNet2 pomocí připojení VPN site-to-site. VNet2 je připojený k Síti VNet3 pomocí připojení VPN site-to-site. Neexistuje žádné přímé partnerský vztah nebo připojení VPN mezi sítěmi Site-to-Site mezi virtuální sítí 1 a virtuální sítí3. Všechna připojení mezi sítěmi jsou spuštěna pro směrování protokolu BGP.

Klienti používající Systém Windows nebo jiný podporovaný operační systém mají přístup ke všem virtuálním sítím, které jsou připojené pomocí připojení VPN site-to-site, ale trasy k připojeným virtuálním sítím musí být ručně přidány ke klientům Windows.

![více virtuálních sítí a S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "více virtuálních sítí a S2S BGP")

### <a name="address-space"></a>Adresní prostor

* Virtuální síť1: 10.1.0.0/16

* Virtuální síť 2: 10.2.0.0/16

* Vnet3: 10.3.0.0/16

### <a name="routes-added"></a>Přidané trasy

* Trasy přidané ke klientům se systémem Windows: 10.1.0.0/16

* Trasy přidané ke klientům, kteří nejsou s Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti systému Windows mohou přistupovat k virtuální síti 1, Virtuální síti 2 a virtuální síti 3, ale trasy na virtuální síť 2 a virtuální síť 3 bude nutné přidat ručně.

* Klienti, kteří nejsou systémy Windows, mají přístup k sítím VNet1, VNet2 a VNet3.

## <a name="one-vnet-and-a-branch-office"></a><a name="vnetbranch"></a>Jedna virtuální síť a pobočka

V tomto příkladu je připojení brány VPN point-to-site pro virtuální síť 1. VNet1 není připojen/ partnerský vztah s jinou virtuální sítí, ale je připojen k místní lokalitě prostřednictvím připojení VPN site-to-site, který není spuštěn BGP.

K síti VNet1 mají přístup jenom klienti systému Windows a klienti, kteří nejsou systémy Windows.

![směrování pomocí virtuální sítě a pobočky](./media/vpn-gateway-about-point-to-site-routing/5.jpg "směrování pomocí virtuální sítě a pobočky")

### <a name="address-space"></a>Adresní prostor

* Virtuální síť1: 10.1.0.0/16

* Místo1: 10.101.0.0/16

### <a name="routes-added"></a>Přidané trasy

* Trasy přidané do klientů se systémem Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané ke klientům, kteří nejsou s Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti systému Windows mají přístup pouze k virtuální síti 1.

* Klienti, kteří nejsou systémy Windows, mají přístup pouze k virtuální síti 1.

## <a name="one-vnet-and-a-branch-office-bgp"></a><a name="vnetbranchbgp"></a>Jedna virtuální síť a pobočka (BGP)

V tomto příkladu je připojení brány VPN point-to-site pro virtuální síť 1. VNet1 není připojen nebo partnerský vztah s jinou virtuální sítí, ale je připojen k místní lokalitě (Site1) prostřednictvím sítě-site vpn připojení běží BGP.

Klienti systému Windows mají přístup k virtuální síti a pobočce (Site1), ale trasy do sítě Site1 musí být ručně přidány do klienta. Klienti, kteří nejsou systémy Windows, mají přístup k virtuální síti i k místní pobočce.

![jedna virtuální síť a pobočka (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "jedna virtuální síť a pobočka")

### <a name="address-space"></a>Adresní prostor

* Virtuální síť1: 10.1.0.0/16

* Místo1: 10.101.0.0/16

### <a name="routes-added"></a>Přidané trasy

* Trasy přidané do klientů se systémem Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané ke klientům, kteří nejsou s Windows: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti systému Windows mají přístup k sítím VNet1 a Site1, ale trasy do sítě Site1 bude nutné přidat ručně.

* Klienti, kteří nejsou systémy Windows, mají přístup k sítím VNet1 a Site1.


## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office"></a><a name="multivnets2sbranch"></a>Více virtuálních sítí připojených pomocí S2S a pobočky

V tomto příkladu je připojení brány VPN point-to-site pro virtuální síť 1. VNet1 se připojí k Síti VNet2 pomocí připojení VPN site-to-site. VNet2 je připojený k Síti VNet3 pomocí připojení VPN site-to-site. Neexistuje žádný přímý partnerský vztah nebo tunel vpn mezi sítěmi VNet1 a VNet3. VNet3 je připojený k pobočce (Site1) pomocí připojení VPN site-to-site. Všechna připojení VPN nejsou spuštěna protokolem BGP.

Všichni klienti mají přístup pouze k virtuální síti 1.

![multi-VNet S2S a pobočka](./media/vpn-gateway-about-point-to-site-routing/7.jpg "multi-VNet S2S a pobočka")

### <a name="address-space"></a>Adresní prostor

* Virtuální síť1: 10.1.0.0/16

* Virtuální síť 2: 10.2.0.0/16

* Vnet3: 10.3.0.0/16

* Místo1: 10.101.0.0/16

### <a name="routes-added"></a>Přidané trasy

* Trasy přidané do klientů se systémem Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané ke klientům, kteří nejsou v systému Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti systému Windows mají přístup pouze k síti VNet1.

* Klienti, kteří nejsou systémy Windows, mají přístup pouze k virtuální síti 1.

## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office-bgp"></a><a name="multivnets2sbranchbgp"></a>Více virtuálních sítí připojených pomocí S2S a pobočky (BGP)

V tomto příkladu je připojení brány VPN point-to-site pro virtuální síť 1. VNet1 se připojí k Síti VNet2 pomocí připojení VPN site-to-site. VNet2 je připojený k Síti VNet3 pomocí připojení VPN site-to-site. Neexistuje žádný přímý partnerský vztah nebo tunel vpn mezi sítěmi VNet1 a VNet3. VNet3 je připojený k pobočce (Site1) pomocí připojení VPN site-to-site. Všechna připojení VPN jsou spuštěna protokol BGP.

Klienti používající Systém Windows mohou přistupovat k virtuálním sítím a sítím, které jsou připojené pomocí připojení VPN mezi lokalitami, ale trasy do virtuální sítě 2, VNetu3 a Sítě1 musí být ručně přidány do klienta. Klienti, kteří nejsou systémy Windows, mají přístup k virtuálním sítím a sítím, které jsou připojené pomocí připojení VPN mezi lokalitami bez ručního zásahu. Přístup je přenositý a klienti mají přístup k prostředkům ve všech připojených virtuálních sítích a lokalitách (místních).

![multi-VNet S2S a pobočka](./media/vpn-gateway-about-point-to-site-routing/8.jpg "multi-VNet S2S a pobočka")

### <a name="address-space"></a>Adresní prostor

* Virtuální síť1: 10.1.0.0/16

* Virtuální síť 2: 10.2.0.0/16

* Vnet3: 10.3.0.0/16

* Místo1: 10.101.0.0/16

### <a name="routes-added"></a>Přidané trasy

* Trasy přidané do klientů se systémem Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané ke klientům, kteří nejsou v systému Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti systému Windows mohou přistupovat k sítím VNet1, VNet2, VNet3 a Site1, ale trasy na Virtuální síť 2, Virtuální síť 3 a lokalitu1 musí být ručně přidány do klienta.

* Klienti, kteří nejsou systémy Windows, mají přístup k sítím VNet1, Vnet2, VNet3 a Site1.

## <a name="next-steps"></a>Další kroky

K zahájení vytváření P2S VPN najdete [v tématu Vytvoření p2s VPN pomocí portálu Azure.](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

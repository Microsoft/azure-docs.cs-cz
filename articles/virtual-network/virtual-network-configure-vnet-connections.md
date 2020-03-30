---
title: Konfigurace a ověření připojení virtuální sítě nebo sítě VPN
description: Podrobné pokyny ke konfiguraci a ověření různých nasazení Azure VPN a virtuálních sítí
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: dddf402455292e19bf0fcda3c50d9ce10d5888d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099061"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>Konfigurace a ověření připojení virtuální sítě nebo sítě VPN

Tento návod obsahuje podrobné pokyny ke konfiguraci a ověření různých nasazení Azure VPN a virtuálních sítí. Scénáře zahrnují tranzitní směrování, připojení mezi sítěmi, protokol BGP (Border Gateway Protocol), připojení pro více sítí a připojení mezi koncovými místy.

Brány Azure VPN umožňují flexibilitu při uspořádání téměř jakéhokoli druhu topologie připojené virtuální sítě v Azure. Můžete například připojit virtuální sítě:

- Napříč regiony.
- Mezi typy virtuálních sítí (Azure Resource Manager versus klasické).
- V rámci Azure nebo v místním hybridním prostředí.
- V různých předplatných. 

## <a name="network-to-network-vpn-connection"></a>Připojení VPN mezi sítěmi

Připojení virtuální sítě k jiné virtuální síti (síť-síť) prostřednictvím sítě VPN je podobné připojení virtuální sítě k umístění v místním webu. Oba typy připojení používají bránu VPN k zajištění zabezpečeného tunelového propojení přes protokoly IPsec a Protokol IKE. Virtuální sítě se můžou nacházet ve stejné oblasti nebo v různých oblastech a můžou patřit do stejného předplatného nebo do různých předplatných.

![Připojení k síti k síti s iPsec](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Pokud vaše virtuální sítě jsou ve stejné oblasti, můžete zvážit jejich připojení pomocí partnerského vztahu virtuální sítě. Partnerský vztah virtuální sítě nepoužívá bránu VPN. Zvyšuje propustnost a snižuje latenci. Pokud chcete nakonfigurovat připojení partnerského vztahu virtuální sítě, vyberte **Konfigurovat a ověřte partnerský vztah virtuální sítě**.

Pokud vaše virtuální sítě byly vytvořeny prostřednictvím modelu nasazení Azure Resource Manger, vyberte **Konfigurovat a ověřit virtuální síť Správce prostředků na připojení virtuální sítě Správce prostředků** pro konfiguraci připojení VPN.

Pokud jedna z virtuálních sítí byla vytvořena prostřednictvím modelu klasického nasazení Azure a druhá byla vytvořena prostřednictvím Správce prostředků, vyberte **Konfigurovat a ověřit klasickou virtuální síť na připojení virtuální sítě Správce prostředků** pro konfiguraci připojení VPN.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>Konfigurace partnerského vztahu virtuální sítě pro dvě virtuální sítě ve stejné oblasti

Než začnete implementovat a konfigurovat partnerský vztah virtuální sítě Azure, ujistěte se, že splňujete následující požadavky:

* Partnerské virtuální sítě musí existovat ve stejné oblasti Azure.
* Partnerské virtuální sítě musí mít IP adresní prostory, které se nepřekrývají.
* Partnerský vztah virtuálních sítí se navazuje mezi dvěma virtuálními sítěmi. Neexistuje žádný odvozený přenositý vztah mezi partnerskými vztahy. Například pokud vneta je partnerský vztah s VNetB a VNetB je peered s VNetC, VNetA *není* peered s VNetC.

Když splníte požadavky, můžete postupovat podle [kurzu: Připojení virtuálních sítí s partnerským vztahem virtuální sítě pomocí portálu Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) k vytvoření a konfiguraci partnerského vztahu.

Chcete-li zkontrolovat konfiguraci partnerského vztahu, použijte následující metodu:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí účtu, který má potřebné [role a oprávnění](virtual-network-manage-peering.md#permissions).
2. Do pole, které obsahuje text **Hledat prostředky** v horní části portálu, zadejte **virtuální sítě**. Když se ve výsledcích hledání zobrazí **virtuální sítě,** vyberte je.
3. V okně **Virtuální sítě,** které se zobrazí, vyberte virtuální síť, pro kterou chcete vytvořit partnerský vztah.
4. V podokně, které se zobrazí pro virtuální síť, vyberte **partnerské partnerské partnerské partnery** v části **Nastavení.**
5. Vyberte partnerský vztah a zobrazte výsledky konfigurace.

![Výběry pro kontrolu konfigurace partnerského vztahu virtuální sítě](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Pro Azure PowerShell spusťte příkaz [Get-AzureRmVirtualNetworkPeering,](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) abyste získali partnerský vztah virtuální sítě. Tady je příklad:

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>Připojení virtuální sítě Resource Manager k jiné virtuální síti Resource Manageru

Můžete nakonfigurovat připojení z jedné virtuální sítě Resource Manager u jiné virtuální sítě Resource Manager přímo. Nebo můžete nakonfigurovat připojení pomocí protokolu IPsec.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>Konfigurace připojení VPN mezi virtuálními sítěmi Resource Manageru

Pokud chcete nakonfigurovat připojení mezi virtuálními sítěmi Resource Manageru bez protokolu IPsec, přečtěte si informace [o konfiguraci připojení brány VPN mezi sítěmi pomocí portálu Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

Pokud chcete nakonfigurovat připojení s Protokolem IPsec mezi dvěma virtuálními sítěmi Správce prostředků, postupujte podle kroků 1 až 5 v [části Vytvoření připojení k webu na webu na portálu Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) pro každou virtuální síť.

> [!Note]
> Tyto kroky fungují pouze pro virtuální sítě ve stejném předplatném. Pokud vaše virtuální sítě jsou v různých předplatných, musíte použít PowerShell k připojení. Podrobnosti najdete v článku o [PowerShellu](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps).

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>Ověření připojení VPN mezi virtuálními sítěmi Resource Manageru

![Klasické připojení virtuální sítě k virtuální síti Azure Resource Manager](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Chcete-li zkontrolovat, zda je připojení VPN správně nakonfigurováno, postupujte podle těchto pokynů.

> [!Note] 
> Čísla za součástmi virtuální sítě v těchto krocích odpovídají číslům v předchozím diagramu.

1. Ujistěte se, že v připojených virtuálních sítích nejsou žádné překrývající se adresní prostory.
2. Ověřte, že rozsah adres pro virtuální síť Azure Resource Manager (1) je přesně definován v instanci **objektu Připojení** (4).
3. Ověřte, že rozsah adres pro virtuální síť Azure Resource Manager (6) je přesně definován v instanci **objektu Připojení** (3).
4. Ověřte, zda se předsdílené klíče shodují s objekty připojení.
5. Ověřte, že virtuální síťová brána Azure Resource Manager VIP (2) je přesně definovaná v instanci **objektu Připojení** (4).
6. Ověřte, že virtuální síťová brána Azure Resource Manager VIP (5) je přesně definovaná v instanci **objektu Připojení** (3).

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Připojení klasické virtuální sítě k virtuální síti Správce prostředků

Můžete vytvořit připojení mezi virtuálními sítěmi, které jsou v různých předplatných a v různých oblastech. Můžete také připojit virtuální sítě, které již mají připojení k místním sítím, pokud jste nakonfigurovali typ brány jako na základě trasy.

Pokud chcete nakonfigurovat připojení mezi klasickou virtuální sítí a virtuální sítí Resource Manager, přečtěte si informace [o připojení virtuálních sítí z různých modelů nasazení pomocí portálu Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal).

![Klasické připojení virtuální sítě k virtuální síti Azure Resource Manager](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Pokud chcete zkontrolovat konfiguraci při připojení klasické virtuální sítě k virtuální síti Azure Resource Manager, postupujte podle těchto pokynů.

> [!Note] 
> Čísla za součástmi virtuální sítě v těchto krocích odpovídají číslům v předchozím diagramu. 

1. Ujistěte se, že v připojených virtuálních sítích nejsou žádné překrývající se adresní prostory.
2. Ověřte, že rozsah adres pro virtuální síť Azure Resource Manager (6) je přesně definován v klasické definici místní sítě (3).
3. Ověřte, že rozsah adres pro klasickou virtuální síť (1) je přesně definován v instanci **objektu připojení** Azure Resource Manager (4).
4. Ověřte, že virtuální virtuální síť brána VIP (2) je přesně definována v instanci **objektu připojení** Azure Resource Manager (4).
5. Ověřte, že brána virtuální sítě Azure Resource Manager (5) je přesně definovaná v klasické instanci **Definice místní sítě** (3).
6. Ověřte, zda se předsdílené klíče shodují v obou připojených virtuálních sítích:
   - Klasická virtuální síť: **Definice místní sítě** (3)
   - Virtuální síť Azure Resource Manager: **Objekt připojení** (4)

## <a name="create-a-point-to-site-vpn-connection"></a>Vytvoření připojení VPN z bodu na místo

Konfigurace point-to-site *(P2S* v následujícím diagramu) umožňuje vytvořit zabezpečené připojení z jednotlivých klientských počítačů do virtuální sítě. Připojení bodu k webu jsou užitečná, pokud se chcete připojit k virtuální síti ze vzdáleného umístění, například z domova nebo z konference. Jsou také užitečné, když máte jen několik klientů, kteří se potřebují připojit k virtuální síti. 

Připojení VPN point-to-site je zahájeno z klientského počítače prostřednictvím nativního klienta SÍTĚ WINDOWS. Připojovaní klienti používají certifikáty k ověření.

![Připojení typu Point-to-Site](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

Připojení z bodu na místo nevyžadují zařízení VPN. Vytvářejí připojení VPN přes protokol SSTP (SSTP) (Secure Socket Tunneling Protocol). Připojení bodu k síti můžete připojit k virtuální síti pomocí různých nástrojů pro nasazení a modelů nasazení:

* [Konfigurace připojení z bodu na místo k virtuální síti pomocí portálu Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Konfigurace připojení z bodu na místo k virtuální síti pomocí portálu Azure (klasický)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [Konfigurace připojení z bodu na místo k virtuální síti pomocí prostředí PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connection"></a>Ověření připojení z bodu na pracoviště

Článek [Řešení potíží: Problémy s připojením z bodu na místo Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) procházejí běžnými problémy s připojením bodu k webu.

## <a name="create-a-multisite-vpn-connection"></a>Vytvoření připojení VPN pro více pracmísti

Připojení site-to-site *(S2S* v následujícím diagramu) můžete přidat do virtuální sítě, která již má připojení site-to-site, připojení z bodu do sítě nebo připojení k síti do sítě. Tento druh připojení se často nazývá konfigurace *více pracovitých sítí.* 

![Připojení pro více pracmísti](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Azure v současné době používá dva modely nasazení: Resource Manager a Classic. Tyto dva modely nejsou zcela kompatibilní s sebou. Chcete-li nakonfigurovat připojení k více sítím s různými modely, přečtěte si následující články:

* [Přidání připojení mezi lokalitami do virtuální sítě s existujícím připojením brány VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Přidání připojení site-to-site do virtuální sítě s existujícím připojením brány VPN (klasické)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> Kroky v těchto článcích se nevztahují na Azure ExpressRoute a konfigurace koexistující připojení site-to-site. Další informace naleznete v tématu [ExpressRoute a koexistující připojení mezi lokalitami](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

## <a name="configure-transit-routing"></a>Konfigurace směrování tranzitu

Tranzitní směrování je specifický scénář směrování, ve kterém připojíte více sítí v topologii daisy-chain. Toto směrování umožňuje prostředkům ve virtuálních sítích na obou koncích řetězce vzájemnou komunikaci prostřednictvím virtuálních sítí mezi nimi. Bez směrování tranzitu se sítě nebo zařízení, která jsou partnerem prostřednictvím rozbočovače, nemohou vzájemně oslovit.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>Konfigurace tranzitního směrování v připojení bodu k síti

Představte si scénář, ve kterém chcete nakonfigurovat připojení VPN mezi sítí site-to-site mezi VNetA a VNetB. Chcete také nakonfigurovat vpn bodu na lokalitu pro klienta pro připojení k bráně VNetA. Potom chcete povolit směrování tranzitu pro klienty point-to-site pro připojení k Virtuální síti, která prochází VNetA. 

Tento scénář je podporován, když je povoleno bgp na síti VPN mezi vneta a virtuální sítě. Další informace naleznete v [tématu O směrování vpn z bodu na pracoviště](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing).

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>Konfigurace tranzitního směrování v připojení ExpressRoute

Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes vyhrazené soukromé připojení zajišťované poskytovatelem připojení. Pomocí ExpressRoute může vytvořit připojení ke cloudovým službám Microsoftu, jako je například Microsoft Azure, Office 365 nebo Dynamics 365. Další informace najdete v článku [Přehled ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Připojení soukromého partnerského vztahu ExpressRoute k virtuálním sítím Azure](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> Doporučujeme, aby, pokud VNetA a VNetB jsou ve stejné geopolitické oblasti, [propojit obě virtuální sítě na okruh ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) namísto konfigurace směrování tranzitu. Pokud se vaše virtuální sítě nacházejí v různých geopolitických oblastech, můžete je také propojit přímo s okruhem, pokud máte [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium). 

Pokud máte ExpressRoute a koexistenci mezi lokalitami, tranzitní směrování není podporováno. Další informace naleznete [v tématu Konfigurace expressroute a webu k webu pomocí prostředí PowerShell](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

Pokud jste povolili ExpressRoute pro připojení místních sítí k virtuální síti Azure, můžete povolit partnerský vztah mezi virtuálními sítěmi, kde chcete mít tranzitní směrování. Chcete-li místním sítím povolit připojení ke vzdálené virtuální síti, je nutné nakonfigurovat [partnerský vztah virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity). 

> [!Note]
> Partnerský vztah virtuální sítě je k dispozici pouze pro virtuální sítě ve stejné oblasti.

Chcete-li zkontrolovat, zda jste nakonfigurovali směrování tranzitu pro partnerský vztah virtuální sítě, postupujte podle následujících pokynů:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí účtu, který má potřebné [role a oprávnění](virtual-network-manage-peering.md#permissions).
2. [Vytvořte partnerský vztah mezi Virtuální maa a Virtuální sítí,](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) jak je znázorněno v předchozím diagramu. 
3. V podokně, které se zobrazí pro virtuální síť, vyberte **partnerské partnerské partnerské partnery** v části **Nastavení.**
4. Vyberte partnerský vztah, který chcete zobrazit. Potom vyberte **Konfigurace,** chcete-li ověřit, že jste povolili **povolení přenosu brány** v síti VNetA připojené k okruhu ExpressRoute, a použít **vzdálenou bránu** ve vzdálené síti VNetB, která není připojena k okruhu ExpressRoute.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>Konfigurace směrování tranzitu v připojení partnerského vztahu virtuální sítě

Když je mezi virtuálními sítěmi navázán partnerský vztah, můžete také v partnerské virtuální síti nakonfigurovat bránu, která bude sloužit jako tranzitní bod pro místní síť. Informace o konfiguraci tranzitní trasy v partnerské síti virtuální sítě naleznete v tématu [Připojení mezi sítěmi](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json).

> [!Note]
> Přenos brány není podporován ve vztahu partnerského vztahu mezi virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení. Obě virtuální sítě ve vztahu partnerského vztahu musí být vytvořeny prostřednictvím Správce prostředků pro přechod brány do práce.

Chcete-li zkontrolovat, zda jste nakonfigurovali tranzitní trasu pro partnerský vztah virtuální sítě, postupujte podle následujících pokynů:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí účtu, který má potřebné [role a oprávnění](virtual-network-manage-peering.md#permissions).
2. Do pole, které obsahuje text **Hledat prostředky** v horní části portálu, zadejte **virtuální sítě**. Když se ve výsledcích hledání zobrazí **virtuální sítě,** vyberte je.
3. V okně **Virtuální sítě,** které se zobrazí, vyberte virtuální síť, pro kterou chcete zkontrolovat nastavení partnerského vztahu.
4. V podokně, které se zobrazí pro vybranou virtuální síť, vyberte **partnerské partnerské partnery** v části **Nastavení.**
5. Vyberte partnerský vztah, který chcete zobrazit. Ověřte, zda jste povolili **povolit přenos brány** a **použít vzdálené brány** v části **Konfigurace**.

![Výběrpro kontrolu, že jste nakonfigurovali tranzitní trasu pro partnerský vztah virtuální sítě](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>Konfigurace směrování tranzitu v připojení mezi sítěmi

Chcete-li nakonfigurovat směrování přenosu mezi virtuálními sítěmi, musíte povolit protokol BGP ve všech zprostředkujících připojeních mezi sítěmi pomocí modelu nasazení Resource Manager a prostředí PowerShell. Pokyny najdete v tématu [Jak nakonfigurovat protokol BGP v branách Azure VPN pomocí PowerShellu](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Přenos ový provoz přes brány Azure VPN je možný prostřednictvím klasického modelu nasazení, ale to závisí na staticky definovaných adresových prostorech v konfiguračním souboru sítě. BGP ještě není podporována s virtuálními sítěmi Azure a bránami VPN prostřednictvím klasického modelu nasazení. Bez protokolu BGP je ruční definování tranzitních adresových prostorů náchylné k chybám a nedoporučujeme je.

> [!Note]
> Klasická připojení mezi sítěmi nakonfigurujete pomocí klasického portálu Azure nebo pomocí konfiguračního souboru sítě na klasickém portálu. Klasickou virtuální síť nelze vytvořit ani upravit prostřednictvím modelu nasazení Azure Resource Manageru nebo portálu Azure. Další informace o tranzitním směrování pro klasické virtuální sítě naleznete v [blogu microsoft developer](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>Konfigurace směrování tranzitu v připojení mezi lokalitami

Chcete-li nakonfigurovat směrování tranzitu mezi místní sítí a virtuální sítí s připojením mezi lokalitami, musíte povolit protokol BGP ve všech zprostředkujících připojeních mezi lokalitami pomocí modelu nasazení Resource Manageru a prostředí PowerShell. Pokyny [najdete v tématu Jak nakonfigurovat protokol BGP v branách Azure VPN pomocí PowerShellu.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)

Přenos ový provoz přes brány Azure VPN je možný prostřednictvím klasického modelu nasazení, ale to závisí na staticky definovaných adresových prostorech v konfiguračním souboru sítě. BGP ještě není podporována s virtuálními sítěmi Azure a bránami VPN prostřednictvím klasického modelu nasazení. Bez protokolu BGP je ruční definování tranzitních adresových prostorů náchylné k chybám a nedoporučujeme je.

> [!Note]
> Klasická připojení mezi lokalitami nakonfigurujete pomocí klasického portálu Azure nebo pomocí konfiguračního souboru sítě na klasickém portálu. Klasickou virtuální síť nelze vytvořit ani upravit prostřednictvím modelu nasazení Azure Resource Manageru nebo portálu Azure. Další informace o tranzitním směrování pro klasické virtuální sítě naleznete v [blogu microsoft developer](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

## <a name="configure-bgp-for-a-vpn-gateway"></a>Konfigurace BGP pro bránu VPN

Protokol BGP je standardní směrovací protokol používaný v Síti Internet k výměně informací o směrování a dosažitelnosti mezi dvěma nebo více sítěmi. Při použití protokolu BGP v kontextu virtuálních sítí Azure, umožňuje brány Azure VPN a místní zařízení VPN, známé jako partnery Protokolu BGP nebo sousedy. Vyměňují si "trasy", které budou informovat obě brány o dostupnosti a dosažitelnosti pro tyto předpony, aby procházely branami nebo směrovači. 

Protokol BGP může také povolit směrování tranzitu mezi více sítěmi šířením tras, které se brána Protokolu BGP učí z jednoho partnera Protokolu BGP do všech ostatních partnerů protokolu BGP. Další informace najdete [v tématu Přehled protokolu BGP s bránou Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview).

### <a name="configure-bgp-for-a-vpn-connection"></a>Konfigurace protokolu BGP pro připojení VPN

Pokud chcete nakonfigurovat připojení VPN, které používá protokol BGP, přečtěte si informace o [konfiguraci protokolu BGP v branách Azure VPN pomocí prostředí PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Povolte protokol BGP na bráně virtuální sítě vytvořením čísla autonomního systému (AS). Základní brány nepodporují protokol BGP. Pokud chcete zkontrolovat skladovou položku brány, přejděte do části **Přehled** okna **brány VPN** na webu Azure Portal. Pokud je vaše skladová položka **základní**, budete muset změnit skladovou položku (viz [Změna velikosti brány](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)) na **VpnGw1**. 

Kontrola skladové položky způsobí 20 až 30 minut prostojů. Jakmile má brána správnou skladovou položku, můžete přidat číslo AS pomocí příkazu Nastavení [AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) PowerShell. Po konfiguraci čísla AS bude automaticky poskytnuta ip adresa druhé strany Protokolu BGP pro bránu.

Je nutné ručně `LocalNetworkGateway` zadat číslo AS a druhou adresu protokolu BGP. Hodnoty `ASN` a `-BgpPeeringAddress` můžete nastavit pomocí příkazu [New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) nebo [Set-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) PowerShell. Některá čísla AS jsou vyhrazena pro Azure a nemůžete je použít, jak je popsáno v [části O Protokolu BGP s azure vpn gateway](../vpn-gateway/vpn-gateway-bgp-overview.md#faq).

Objekt připojení musí mít povoleno protokol BGP. Hodnotu `-EnableBGP` můžete nastavit `$True` na [new-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) nebo [Set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0).

### <a name="validate-the-bgp-configuration"></a>Ověření konfigurace protokolu BGP

Chcete-li zkontrolovat, zda je protokol `get-AzureRmVirtualNetworkGateway` BGP správně nakonfigurován, můžete spustit příkazy a `get-AzureRmLocalNetworkGateway` příkazy. Pak si všimnete, BGP-související `BgpSettingsText` výstup v části. Například:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>Vytvoření vysoce dostupného aktivního/aktivního připojení VPN

Hlavní rozdíly mezi aktivní/aktivní a aktivní/pohotovostní brány jsou:

* Je nutné vytvořit dvě konfigurace IP brány se dvěma veřejnými IP adresami.
* Je nutné nastavit **příznak EnableActiveActiveFeature.**
* Skladová položka brány musí být **VpnGw1**, **VpnGw2**nebo **VpnGw3**.

Chcete-li dosáhnout vysoké dostupnosti pro připojení mezi místními a síťovými připojeními, měli byste nasadit více bran VPN a vytvořit více paralelních připojení mezi vašimi sítěmi a Azure. Přehled možností připojení a topologie naleznete [v tématu Vysoce dostupné připojení mezi místními a síťovými připojeními .](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)

Chcete-li vytvořit aktivní/aktivní připojení mezi místními a síťovými sítěmi, postupujte podle pokynů v části [Konfigurace aktivních/aktivních připojení S2S VPN s branami Azure VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) a nakonfigurujte bránu Azure VPN v aktivním/aktivním režimu.

> [!Note]  
> * Přidáte-li adresy do brány místní sítě pro aktivní/aktivní režim s podporou protokolu BGP, *přidejte pouze adresy /32 partnerů protokolu BGP*. Pokud přidáte další adresy, budou považovány za statické trasy a mají přednost před trasami Protokolu BGP.
> * Musíte použít různá čísla AS protokolu BGP pro místní sítě, které se připojují k Azure. (Pokud jsou stejné, budete muset změnit číslo AS virtuální sítě, pokud vaše místní zařízení VPN již používá ASN k partnerovi s jinými sousedy Protokolu BGP.)

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>Změna typu brány Azure VPN po nasazení

Typ brány virtuální sítě Azure nelze změnit z webu založeného na zásadách na přímo založený na směrování nebo přímo jiným způsobem. Nejprve musíte odstranit bránu. Poté nebude ip adresa a předsdílený klíč zachovány. Pak můžete vytvořit novou bránu požadovaného typu. 

Chcete-li bránu odstranit a vytvořit, postupujte takto:

1. Odstraňte všechna připojení přidružená k původní bráně.
2. Odstraňte bránu pomocí portálu Azure, PowerShellu nebo klasického PowerShellu: 
   * [Odstranění brány virtuální sítě pomocí portálu Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [Odstranění brány virtuální sítě pomocí PowerShellu](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [Odstranění brány virtuální sítě pomocí PowerShellu (klasické)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. Podle pokynů v [části Vytvoření brány VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway) vytvořte novou bránu požadovaného typu a dokončete nastavení SÍTĚ VPN.

> [!Note]
> Tento proces bude trvat přibližně 60 minut.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s připojením mezi virtuálními počítači Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)


---
title: Konfigurace a ověření připojení k virtuální síti nebo k síti VPN
description: Podrobné pokyny pro konfiguraci a ověření různých nasazení Azure VPN a virtuálních sítí
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
ms.openlocfilehash: d9a87eca6a6c66d116817ced0f534a75033d48b9
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221472"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>Konfigurace a ověření připojení k virtuální síti nebo k síti VPN

Tento návod poskytuje podrobné pokyny ke konfiguraci a ověření různých nasazení Azure VPN a virtuálních sítí. Mezi scénáře patří směrování přenosu, připojení k síti, Border Gateway Protocol (BGP), připojení k více lokalitám a připojení Point-to-site.

Azure VPN Gateway umožňují flexibilitu při uspořádávání téměř jakéhokoli druhu připojené virtuální síťové topologie v Azure. Můžete například propojit virtuální sítě:

- Napříč oblastmi.
- Mezi typy virtuální sítě (Azure Resource Manager vs Classic).
- V Azure nebo v místním hybridním prostředí.
- V různých předplatných. 

## <a name="network-to-network-vpn-connection"></a>Připojení VPN typu síť-síť

Připojení virtuální sítě k jiné virtuální síti přes síť VPN je podobné jako připojení virtuální sítě k místnímu umístění lokality. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec a IKE. Virtuální sítě se můžou nacházet ve stejné oblasti nebo v různých oblastech a můžou patřit do stejného předplatného nebo do různých předplatných.

![Připojení k síti pomocí protokolu IPsec](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Pokud jsou vaše virtuální sítě ve stejné oblasti, možná budete chtít zvážit jejich připojení pomocí partnerského vztahu virtuálních sítí. Partnerské vztahy virtuálních sítí nepoužívají bránu VPN. Zvyšuje propustnost a snižuje latenci. Pokud chcete nakonfigurovat připojení partnerského vztahu virtuální sítě, vyberte **Konfigurovat a ověřit partnerský vztah VNet**.

Pokud se vaše virtuální sítě vytvořily prostřednictvím modelu nasazení Azure Resource Manageru, vyberte **Konfigurovat a ověřit správce prostředků virtuální síť pro připojení správce prostředků VNet** ke konfiguraci připojení VPN.

Pokud se jedna z virtuálních sítí vytvořila prostřednictvím modelu nasazení Azure Classic a druhá se vytvořila prostřednictvím Správce prostředků, vyberte **Konfigurovat a ověřit klasickou virtuální síť pro připojení správce prostředků VNet** , abyste mohli nakonfigurovat připojení k síti VPN.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>Konfigurace partnerského vztahu virtuálních sítí pro dvě virtuální sítě ve stejné oblasti

Než začnete s implementací a konfigurací partnerského vztahu virtuálních sítí Azure, ujistěte se, že splňujete následující požadavky:

* Partnerské virtuální sítě musí existovat ve stejné oblasti Azure.
* Virtuální sítě s partnerským vztahem musí mít adresní prostory IP adres, které se nepřekrývají.
* Partnerský vztah virtuálních sítí se navazuje mezi dvěma virtuálními sítěmi. V rámci partnerských vztahů neexistuje žádný odvozený přenosový vztah. Například pokud má partnerském partnerský vztah s VNetB a VNetB má partnerský vztah s sítí vnetc, partnerském *není partnerským vztahem* s sítí vnetc.

Pokud splňujete požadavky, můžete postupovat podle [kurzu: propojení virtuálních sítí s virtuálními partnerskými vztahy virtuálních sítí pomocí Azure Portal](./tutorial-connect-virtual-networks-portal.md) k vytvoření a konfiguraci partnerského vztahu.

Chcete-li ověřit konfiguraci partnerského vztahu, použijte následující metodu:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu, který má potřebné [role a oprávnění](virtual-network-manage-peering.md#permissions).
2. Do pole, které obsahuje **prostředky vyhledávání** textu v horní části portálu, zadejte **virtuální sítě**. Pokud se ve výsledcích hledání zobrazí **virtuální sítě** , vyberte ji.
3. V okně **virtuální sítě** , které se zobrazí, vyberte virtuální síť, pro kterou chcete vytvořit partnerský vztah.
4. V podokně, které se zobrazí pro virtuální síť, vyberte **partnerské vztahy** v části **Nastavení** .
5. Vyberte partnerský vztah a zobrazte výsledky konfigurace.

![Výběry pro kontrolu konfigurace partnerského vztahu virtuálních sítí](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Pro Azure PowerShell spuštěním příkazu [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) Získejte partnerský vztah virtuální sítě. Tady je příklad:

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

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>Připojení virtuální sítě Správce prostředků k jiné virtuální síti Správce prostředků

Připojení z jedné Správce prostředků virtuální sítě k jiné virtuální síti Správce prostředků můžete nakonfigurovat přímo. Můžete také nakonfigurovat připojení pomocí protokolu IPsec.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>Konfigurace připojení VPN mezi Správce prostředkůmi virtuálními sítěmi

Pokud chcete nakonfigurovat připojení mezi Správce prostředkůmi virtuálními sítěmi bez protokolu IPsec, přečtěte si téma [Konfigurace připojení brány sítě VPN typu síť-síť pomocí Azure Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

Pokud chcete nakonfigurovat připojení pomocí protokolu IPsec mezi dvěma Správce prostředkůmi virtuálními sítěmi, postupujte podle kroků 1 až 5 v [části Vytvoření připojení typu Site-to-site v Azure Portal](../vpn-gateway/tutorial-site-to-site-portal.md) pro každou virtuální síť.

> [!Note]
> Tyto kroky fungují jenom pro virtuální sítě ve stejném předplatném. Pokud jsou vaše virtuální sítě v různých předplatných, musíte k vytvoření připojení použít PowerShell. Podrobnosti najdete v článku o [PowerShellu](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>Ověření připojení VPN mezi Správce prostředkůmi virtuálními sítěmi

![Připojení k virtuální síti s Azure Resource Managerm Classic](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Pokud chcete ověřit, že je připojení k síti VPN správně nakonfigurované, postupujte podle těchto pokynů.

> [!Note] 
> Čísla po součástech virtuální sítě v těchto krocích odpovídají číslům v předchozím diagramu.

1. Ujistěte se, že v propojených virtuálních sítích nejsou žádné překrývající se adresní prostory.
2. Ověřte, zda je rozsah adres pro Azure Resource Manager virtuální síť (1) přesně definován v instanci **objektu připojení** (4).
3. Ověřte, zda je rozsah adres pro Azure Resource Manager virtuální síť (6) přesně definován v instanci **objektu připojení** (3).
4. Ověřte, zda jsou předsdílené klíče spárovány s objekty připojení.
5. Ověřte, že virtuální IP adresa brány Azure Resource Manager virtuální sítě (2) je přesně definovaná v instanci **objektu připojení** (4).
6. Ověřte, že virtuální IP adresa brány Azure Resource Manager virtuální sítě (5) je přesně definovaná v instanci **objektu připojení** (3).

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Připojení klasické virtuální sítě k Správce prostředků virtuální síti

Můžete vytvořit propojení mezi virtuálními sítěmi, které jsou v různých předplatných a v různých oblastech. Můžete také propojit virtuální sítě, které již mají připojení k místním sítím, pokud jste nakonfigurovali typ brány jako založený na trasách.

Pokud chcete nakonfigurovat připojení mezi klasickými virtuálními sítěmi a Správce prostředků virtuální sítí, přečtěte si téma [propojení virtuálních sítí z různých modelů nasazení pomocí Azure Portal](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

![Diagram, který zobrazuje připojení klasické virtuální sítě k virtuální síti Azure Resource Manager.](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Pokud chcete ověřit konfiguraci při připojení klasické virtuální sítě k virtuální síti Azure Resource Manager, postupujte podle těchto pokynů.

> [!Note] 
> Čísla po součástech virtuální sítě v těchto krocích odpovídají číslům v předchozím diagramu. 

1. Ujistěte se, že v propojených virtuálních sítích nejsou žádné překrývající se adresní prostory.
2. Ověřte, zda je rozsah adres pro Azure Resource Manager virtuální síť (6) přesně definován v definici klasické místní sítě (3).
3. Ověřte, zda je rozsah adres pro klasickou virtuální síť (1) přesně definován v instanci **objektu Azure Resource Manager Connection** instance (4).
4. Ověřte, že virtuální IP adresa brány Classic Virtual Network (2) je přesně definovaná v instanci **objektu Azure Resource Manager Connection** instance (4).
5. Ověřte, jestli je brána virtuální sítě Azure Resource Manager (5) přesně definovaná v instanci **definice klasické místní sítě** (3).
6. Ověřte, jestli jsou předsdílené klíče v obou připojených virtuálních sítích stejné:
   - Klasická virtuální síť: **definice místní sítě** (3)
   - Azure Resource Manager Virtual Network: **objekt Connection** (4)

## <a name="create-a-point-to-site-vpn-connection"></a>Vytvoření připojení VPN typu point-to-site

Konfigurace Point-to-Site (*P2S* v následujícím diagramu) umožňuje vytvořit zabezpečené připojení z jednotlivého klientského počítače k virtuální síti. Připojení typu Point-to-site jsou užitečná, když se chcete připojit k virtuální síti ze vzdáleného umístění, například z domova nebo z konference. Jsou také užitečné, pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti. 

Připojení VPN typu Point-to-site se spouští z klientského počítače pomocí nativního klienta VPN systému Windows. Připojovaní klienti používají certifikáty k ověření.

![Připojení typu Point-to-Site](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

Připojení typu Point-to-site nevyžadují zařízení VPN. Vytvářejí připojení VPN prostřednictvím protokolu SSTP (Secure Socket Tunneling Protocol). Připojení typu Point-to-site k virtuální síti můžete propojit pomocí různých nástrojů nasazení a modelů nasazení:

* [Konfigurace připojení typu Point-to-site k virtuální síti pomocí Azure Portal](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Konfigurace připojení typu Point-to-site k virtuální síti pomocí Azure Portal (Classic)](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)
* [Konfigurace připojení typu Point-to-site k virtuální síti pomocí PowerShellu](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="validate-your-point-to-site-connection"></a>Ověření připojení Point-to-site

[Řešení potíží s řešením: problémy s připojením Point-to-site](../vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) se týkají běžných problémů s připojením Point-to-site.

## <a name="create-a-multisite-vpn-connection"></a>Vytvoření připojení VPN ve víc lokalitách

Můžete přidat připojení typu Site-to-Site (*S2S* v následujícím diagramu) k virtuální síti, která již má připojení typu Site-to-site, připojení typu Point-to-site nebo připojení typu síť-síť. Tento druh připojení se často označuje jako konfigurace ve *více lokalitách* . 

![Připojení ve více lokalitách](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Azure v současné době používá dva modely nasazení: Resource Manager a Classic. Tyto dva modely nejsou navzájem zcela kompatibilní. Pokud chcete nakonfigurovat připojení ve více lokalitách s různými modely, přečtěte si následující články:

* [Přidání připojení typu Site-to-site k virtuální síti s existujícím připojením služby VPN Gateway](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
* [Přidání připojení typu Site-to-site k virtuální síti s existujícím připojením brány VPN (Classic)](../vpn-gateway/vpn-gateway-multi-site.md)

> [!Note]
> Kroky v těchto článcích se nevztahují na služby Azure ExpressRoute a souběžně existující konfigurace připojení mezi lokalitami. Další informace najdete v tématu [ExpressRoute a společně existující připojení mezi lokalitami](../expressroute/expressroute-howto-coexist-resource-manager.md).

## <a name="configure-transit-routing"></a>Konfigurace směrování přenosu

Směrování provozu je konkrétní scénář směrování, ve kterém propojíte více sítí ve vysoce řetězové topologii. Díky tomuto směrování mohou prostředky ve virtuálních sítích na obou koncích řetězce komunikovat mezi sebou mezi virtuálními sítěmi. Bez průjezdního směrování se nedokáže navázat sítě nebo zařízení, která jsou v partnerském vztahu přes rozbočovač.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>Konfigurace směrování přenosu v připojení typu Point-to-site

Představte si scénář, ve kterém chcete nakonfigurovat připojení VPN typu Site-to-site mezi partnerském a VNetB. Také chcete nakonfigurovat síť VPN typu Point-to-site, aby se klient mohl připojit k bráně partnerském. Pak budete chtít povolit směrování přenosu pro klienty Point-to-site pro připojení k VNetB, které projde prostřednictvím partnerském. 

Tento scénář je podporován, je-li na síti VPN typu Site-to-site mezi partnerském a VNetB povolen protokol BGP. Další informace najdete v tématu [Směrování sítě VPN typu Point-to-site](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md).

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>Konfigurace směrování přenosu v připojení ExpressRoute

Azure ExpressRoute umožňuje rozšířit vaše místní sítě do cloudu Microsoftu přes vyhrazené soukromé připojení zajišťované poskytovatelem připojení. V ExpressRoute můžete vytvořit připojení ke cloudovým službám, jako je Microsoft Azure, Microsoft 365 a Dynamics 365. Další informace najdete v článku [Přehled ExpressRoute](../expressroute/expressroute-introduction.md).

![ExpressRoute připojení privátního partnerského vztahu k virtuálním sítím Azure](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> Doporučujeme, aby v případě, že jsou partnerském a VNetB ve stejné geopolitické oblasti, [propojíte oba virtuální sítě s okruhem ExpressRoute](../expressroute/expressroute-howto-linkvnet-arm.md) , nikoli konfigurací směrování přenosu. Pokud jsou vaše virtuální sítě v různých geopolitických oblastech, můžete je také propojit se svým okruhem přímo, pokud máte [ExpressRoute Premium](../expressroute/expressroute-faqs.md#expressroute-premium). 

Pokud máte ExpressRoute a koexistence mezi lokalitami, směrování přenosu se nepodporuje. Další informace najdete v tématu [Konfigurace ExpressRoute a Site-to-site pomocí prostředí PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md).

Pokud jste povolili ExpressRoute připojit své místní sítě k virtuální síti Azure, můžete povolit partnerský vztah mezi virtuálními sítěmi, ve kterých chcete směrovat směrování. Pokud chcete, aby se vaše místní sítě připojovaly k vzdálené virtuální síti, musíte nakonfigurovat [partnerský vztah virtuálních sítí](./virtual-network-peering-overview.md#gateways-and-on-premises-connectivity). 

> [!Note]
> Partnerský vztah virtuálních sítí je dostupný jenom pro virtuální sítě ve stejné oblasti.

Pokud chcete ověřit, jestli jste nakonfigurovali směrování přenosu pro partnerský vztah virtuálních sítí, postupujte podle těchto pokynů:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu, který má potřebné [role a oprávnění](virtual-network-manage-peering.md#permissions).
2. [Vytvořte partnerský vztah mezi partnerském a VNetB](./tutorial-connect-virtual-networks-portal.md) , jak je znázorněno na dřívějším diagramu. 
3. V podokně, které se zobrazí pro virtuální síť, vyberte **partnerské vztahy** v části **Nastavení** .
4. Vyberte partnerský vztah, který chcete zobrazit. Pak vyberte **Konfigurace** a ověřte, že jste povolili **přenos brány** v partnerském síti připojené k okruhu ExpressRoute a **použijte vzdálenou bránu** ve vzdálené VNetB síti, která není připojená k okruhu ExpressRoute.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>Konfigurace směrování přenosu v připojeních partnerského vztahu virtuální sítě

Když je mezi virtuálními sítěmi navázán partnerský vztah, můžete také v partnerské virtuální síti nakonfigurovat bránu, která bude sloužit jako tranzitní bod pro místní síť. Postup konfigurace přenosové trasy v partnerském vztahu virtuálních sítí najdete v tématu [připojení typu síť-síť](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!Note]
> Přenos brány se nepodporuje v relaci partnerských vztahů mezi virtuálními sítěmi vytvořenými prostřednictvím různých modelů nasazení. Obě virtuální sítě ve vztahu partnerských vztahů se musí vytvořit prostřednictvím Správce prostředků, aby bylo možné pracovat s přenosem brány.

Pokud chcete ověřit, jestli jste nakonfigurovali přenosovou trasu pro partnerský vztah virtuálních sítí, postupujte podle těchto pokynů:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu, který má potřebné [role a oprávnění](virtual-network-manage-peering.md#permissions).
2. Do pole, které obsahuje **prostředky vyhledávání** textu v horní části portálu, zadejte **virtuální sítě**. Pokud se ve výsledcích hledání zobrazí **virtuální sítě** , vyberte ji.
3. V okně **virtuální sítě** , které se zobrazí, vyberte virtuální síť, pro kterou chcete zjistit nastavení partnerského vztahu.
4. V podokně, které se zobrazí u vybrané virtuální sítě, vyberte v části **Nastavení** možnost **partnerské vztahy** .
5. Vyberte partnerský vztah, který chcete zobrazit. Ověřte, že jste povolili možnost **Povolit přenos brány** a **používat vzdálené brány** v části **Konfigurace**.

![Výběry pro kontrolu, že jste nakonfigurovali přenosovou trasu pro partnerský vztah virtuálních sítí](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>Konfigurace směrování přenosu v síťovém připojení

Pokud chcete nakonfigurovat směrování provozu mezi virtuálními sítěmi, musíte povolit protokol BGP u všech zprostředkujících připojení k síti přes síť pomocí modelu nasazení Správce prostředků a PowerShellu. Pokyny najdete v tématu [Postup konfigurace protokolu BGP u bran Azure VPN Gateway pomocí PowerShellu](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md).

Přenos provozu prostřednictvím bran Azure VPN je možný prostřednictvím modelu nasazení Classic, ale spoléhá se na staticky definované adresní prostory v souboru konfigurace sítě. Protokol BGP se zatím nepodporuje u virtuálních sítí Azure a bran sítě VPN prostřednictvím modelu nasazení Classic. Bez protokolu BGP je ruční definování adresních prostorů pro přenos dat náchylné k chybám a nedoporučujeme ji.

> [!Note]
> Klasická připojení typu síť k síti můžete nakonfigurovat pomocí portálu Azure Classic nebo pomocí konfiguračního souboru sítě na portálu Classic. Klasickou virtuální síť nelze vytvořit ani upravit pomocí modelu nasazení Azure Resource Manager ani Azure Portal. Další informace o přenosu směrování pro klasické virtuální sítě najdete na [blogu Microsoftu pro vývojáře](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1).

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>Konfigurace směrování přenosu v rámci připojení typu Site-to-site

Pokud chcete nakonfigurovat směrování provozu mezi vaší místní sítí a virtuální sítí s připojením typu Site-to-site, musíte povolit protokol BGP u všech zprostředkujících připojení typu Site-to-site pomocí modelu nasazení Správce prostředků a PowerShellu. Pokyny najdete v tématu [Postup konfigurace protokolu BGP u bran Azure VPN Gateway pomocí PowerShellu](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) .

Přenos provozu prostřednictvím bran Azure VPN je možný prostřednictvím modelu nasazení Classic, ale spoléhá se na staticky definované adresní prostory v souboru konfigurace sítě. Protokol BGP se zatím nepodporuje u virtuálních sítí Azure a bran sítě VPN prostřednictvím modelu nasazení Classic. Bez protokolu BGP je ruční definování adresních prostorů pro přenos dat náchylné k chybám a nedoporučujeme ji.

> [!Note]
> Klasické připojení typu Site-to-site můžete nakonfigurovat pomocí portálu Azure Classic nebo pomocí konfiguračního souboru sítě na portálu Classic. Klasickou virtuální síť nelze vytvořit ani upravit pomocí modelu nasazení Azure Resource Manager ani Azure Portal. Další informace o přenosu směrování pro klasické virtuální sítě najdete na [blogu Microsoftu pro vývojáře](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1).

## <a name="configure-bgp-for-a-vpn-gateway"></a>Konfigurace BGP pro bránu VPN

BGP je standardní směrovací protokol používaný na internetu k výměně informací o směrování a dostupnosti mezi dvěma nebo více sítěmi. Když se protokol BGP používá v kontextu Azure Virtual Networks, umožňuje brány Azure VPN a místní zařízení VPN, která se označují jako partneři protokolu BGP nebo sousední partneři. Vyměňují "trasy", které budou informovat obě brány o dostupnosti a dosažitelnosti těchto předpon, aby procházeli branami nebo směrovači. 

Protokol BGP může také povolit směrování přenosu mezi více sítěmi tím, že šíří trasy, které brána BGP zjišťuje z jednoho partnerského uzlu protokolu BGP až po všechny ostatní partnerské uzly protokolu BGP. Další informace najdete v tématu [Přehled protokolu BGP s Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="configure-bgp-for-a-vpn-connection"></a>Konfigurace protokolu BGP pro připojení VPN

Pokud chcete nakonfigurovat připojení VPN, které používá protokol BGP, přečtěte si téma [Postup konfigurace protokolu BGP u bran Azure VPN pomocí PowerShellu](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md).

Povolte protokol BGP v bráně virtuální sítě vytvořením autonomního systému (AS) pro něj. Základní brány nepodporují protokol BGP. Chcete-li zjistit SKLADOVOU položku brány, v Azure Portal v okně **VPN Gateway** v části **Přehled** . Pokud je vaše SKU **základní**, musíte změnit skladovou položku (viz [Změna velikosti brány](/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)) na **VpnGw1**. 

Kontrola SKU bude trvat 20 až 30 minut výpadků. Jakmile brána obsahuje správnou SKLADOVOU položku, můžete přidat jako číslo pomocí rutiny [set-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) prostředí PowerShell rutiny. Po nakonfigurování AS Number bude pro bránu automaticky poskytována IP adresa partnerského uzlu protokolu BGP.

Musíte ručně zadat `LocalNetworkGateway` číslo as a partnerská adresa BGP. Hodnoty a můžete nastavit `ASN` `-BgpPeeringAddress` pomocí rutiny [New-AzureRmLocalNetworkGateway](/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) nebo [set-AzureRmLocalNetworkGateway](/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) prostředí PowerShell rutiny. Některá čísla AS jsou vyhrazena pro Azure a nelze je použít, jak je popsáno v [tématu o protokolu BGP s Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md#faq).

Objekt připojení musí mít povolený protokol BGP. Hodnotu můžete nastavit `-EnableBGP` na `$True` pomocí [New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) nebo [set-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0).

### <a name="validate-the-bgp-configuration"></a>Ověření konfigurace protokolu BGP

Pokud chcete zjistit, jestli je protokol BGP správně nakonfigurovaný, můžete spustit rutinu `get-AzureRmVirtualNetworkGateway` a `get-AzureRmLocalNetworkGateway` rutin. Pak si všimnete výstupu souvisejícího s protokolem BGP v `BgpSettingsText` části. Příklad:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>Vytvoření vysoce dostupného připojení VPN typu aktivní/aktivní

Mezi hlavní rozdíly mezi branami aktivní/aktivní a aktivní/pohotovostní patří:

* Musíte vytvořit dvě konfigurace protokolu IP brány se dvěma veřejnými IP adresami.
* Je nutné nastavit příznak **EnableActiveActiveFeature** .
* SKU brány musí být **VpnGw1**, **VpnGw2** nebo **VpnGw3**.

Abyste dosáhli vysoké dostupnosti pro připojení mezi různými místy a mezi lokalitami, měli byste nasadit víc bran VPN a navázat víc paralelních připojení mezi vašimi sítěmi a Azure. Přehled možností připojení a topologie najdete v tématu s [vysokou dostupností mezi místy a připojením k](../vpn-gateway/vpn-gateway-highlyavailable.md)síti.

Pokud chcete vytvořit připojení typu aktivní/aktivní pro více míst a síťovou síť, postupujte podle pokynů v tématu [Konfigurace připojení VPN typu aktivní/aktivní pro S2S pomocí brány VPN Azure](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) a nakonfigurujte bránu Azure VPN v režimu aktivní/aktivní.

> [!Note]  
> * Když přidáváte adresy do brány místní sítě pro režim aktivní/aktivní s povoleným protokolem BGP, *přidejte pouze adresy/32 partnerských uzlů protokolu BGP*. Pokud přidáte více adres, budou považovány za statické trasy a budou mít přednost před trasami protokolu BGP.
> * Pro místní sítě, které se připojují k Azure, musíte použít jinou službu BGP jako čísla. (Pokud jsou stejné, musíte změnit svou virtuální síť jako číslo, pokud vaše místní zařízení VPN už používá ASN k partnerským uzlům s jinými sousedními uzly protokolu BGP.)

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>Změna typu brány VPN Azure po nasazení

Nemůžete změnit typ brány virtuální sítě Azure ze zásad na základě založené na směrování nebo jiným způsobem přímo. Nejdřív musíte bránu odstranit. Pak se zachová IP adresa a předsdílený klíč. Pak můžete vytvořit novou bránu požadovaného typu. 

Pokud chcete odstranit a vytvořit bránu, použijte následující postup:

1. Odstraňte všechna připojení přidružená k původní bráně.
2. Odstraňte bránu pomocí Azure Portal, PowerShellu nebo klasického prostředí PowerShell: 
   * [Odstranění brány virtuální sítě pomocí Azure Portal](../vpn-gateway/vpn-gateway-delete-vnet-gateway-portal.md)
   * [Odstranění brány virtuální sítě pomocí PowerShellu](../vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell.md)
   * [Odstranění brány virtuální sítě pomocí PowerShellu (Classic)](../vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell.md)
3. Postupujte podle kroků v části [Vytvoření brány VPN](../vpn-gateway/tutorial-site-to-site-portal.md#VNetGateway) a vytvořte novou bránu požadovaného typu a dokončete nastavení sítě VPN.

> [!Note]
> Tento proces bude trvat přibližně 60 minut.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s připojením mezi virtuálními počítači Azure](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)
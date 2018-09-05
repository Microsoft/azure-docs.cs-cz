---
title: Směrování provozu virtuální sítě Azure | Dokumentace Microsoftu
description: Zjistěte, jak Azure směruje provoz virtuální sítě a jak můžete směrování Azure přizpůsobit.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2017
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: b206d93d7c72f5d8ff3dd3baa277cd0db33ba583
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42811909"
---
# <a name="virtual-network-traffic-routing"></a>Směrování provozu virtuální sítě

Zjistěte, jak Azure směruje provoz mezi Azure, místním prostředím a internetovými prostředky. Azure pro každou podsíť v rámci virtuální sítě Azure automaticky vytvoří směrovací tabulku a přidá do ní výchozí systémové trasy. Další informace o virtuálních sítích a podsítích najdete v tématu [Přehled virtuálních sítí](virtual-networks-overview.md). Některé systémové trasy Azure můžete přepsat [vlastními trasami](#custom-routes) a do směrovacích tabulek můžete přidat další vlastní trasy. Azure směruje odchozí provoz z podsítě na základě tras ve směrovací tabulce dané podsítě.

## <a name="system-routes"></a>Systémové trasy

Azure automaticky vytvoří systémové trasy a přiřadí je ke každé podsíti ve virtuální síti. Systémové trasy nemůžete vytvořit ani odebrat, ale některé z nich můžete přepsat [vlastními trasami](#custom-routes). Azure vytvoří výchozí systémové trasy pro každou podsíť, a pokud použijete konkrétní možnosti Azure, přidá další [volitelné výchozí trasy](#optional-default-routes) do konkrétních podsítí, nebo do všech podsítí.

### <a name="default"></a>Výchozí

Každá trasa obsahuje předponu adresy a typ dalšího segmentu směrování. Při odeslání odchozího provozu z podsítě na IP adresu v rozsahu předpony adresy nějaké trasy použije Azure trasu obsahující danou předponu. Zjistěte, [jak Azure vybírá trasu](#how-azure-selects-a-route) v případě, že několik tras obsahuje stejné předpony nebo překrývající se předpony. Kdykoli se vytvoří virtuální síť, Azure pro všechny podsítě v rámci této virtuální sítě automaticky vytvoří následující výchozí systémové trasy:


|Zdroj |Předpony adres                                        |Typ dalšího segmentu  |
|-------|---------                                               |---------      |
|Výchozí|Jedinečné pro virtuální síť                           |Virtuální síť|
|Výchozí|0.0.0.0/0                                               |Internet       |
|Výchozí|10.0.0.0/8                                              |Žádný           |
|Výchozí|172.16.0.0/12                                           |Žádný           |
|Výchozí|192.168.0.0/16                                          |Žádný           |
|Výchozí|100.64.0.0/10                                           |Žádný           |

Typy dalších segmentů směrování uvedené v předchozí tabulce představují způsob, jakým Azure směruje provoz určený pro uvedenou předponu adresy. Následuje vysvětlení jednotlivých typů dalších segmentů směrování:

- **Virtuální síť:** Směruje provoz v mezích rozsahů adres v rámci [adresního prostoru](manage-virtual-network.md#add-or-remove-an-address-range) virtuální sítě. Azure vytvoří trasy s předponami adres, které odpovídají jednotlivým rozsahům adres definovaným v rámci adresního prostoru virtuální sítě. Pokud je v adresním prostoru virtuální sítě definováno více rozsahů adres, Azure pro každý z nich vytvoří samostatnou trasu. Azure automaticky směruje provoz mezi podsítěmi pomocí tras vytvořených pro jednotlivé rozsahy adres. Azure pro směrování provozu mezi podsítěmi nevyžaduje definování bran. Přestože virtuální síť obsahuje podsítě a každá podsíť má definovaný rozsah adres, Azure *nevytvoří* výchozí trasy pro rozsahy adres podsítě, protože každý rozsah adres podsítě je v rámci rozsahu adres adresního prostoru virtuální sítě.

- **Internet:** Směruje provoz určený předponou adresy do internetu. Výchozí systémová trasa určuje předponu adresy 0.0.0.0/0. Pokud nepřepíšete výchozí trasy Azure, až na jednu výjimku směruje Azure provoz pro všechny adresy, které nejsou určené rozsahem adres v rámci nějaké virtuální sítě, do internetu. Pokud je cílová adresa adresou některé ze služeb Azure, místo směrování provozu do internetu směruje Azure provoz přes páteřní síť Azure přímo do služby. Provoz mezi službami Azure neprochází přes internet, a to bez ohledu na to, ve které oblasti Azure existuje virtuální síť nebo ve které oblasti Azure je nasazená instance služby Azure. Výchozí systémovou trasu Azure pro předponu adresy 0.0.0.0/0 není možné přepsat [vlastní trasou](#custom-routes).

- **Žádný:** Provoz směrovaný na další segment směrování typu **Žádný** se zahodí, a nesměruje se mimo podsíť. Azure automaticky vytvoří výchozí trasy pro následující předpony adres:
    - **10.0.0.0/8, 172.16.0.0/12 a 192.168.0.0/16:** Rezervované pro soukromé použití v RFC 1918.
    - **100.64.0.0/10:** Rezervovaná v RFC 6598.

    Pokud přiřadíte jakýkoli z předchozích rozsahů adres v rámci adresního prostoru virtuální sítě, Azure automaticky změní typ dalšího segmentu směrování pro příslušnou trasu z **Žádný** na **Virtuální síť**. Pokud přiřadíte rozsah adres k adresnímu prostoru virtuální sítě, který zahrnuje jednu ze čtyř vyhrazených předpon adres (ale není stejný), Azure odebere trasu pro danou předponu a přidá trasu pro předponu adresy, kterou jste přidali, s typem dalšího segmentu směrování **Virtuální síť**.

### <a name="optional-default-routes"></a>Volitelné výchozí trasy

Azure přidá další výchozí systémové trasy pro různé možnosti Azure. Provede to však jenom v případě, že tyto možnosti povolíte. V závislosti na možnosti přidá Azure volitelné výchozí trasy buď do konkrétních podsítí v rámci virtuální sítě, nebo do všech podsítí v rámci virtuální sítě. Azure může po povolení různých možností přidat následující dodatečné systémové trasy a typy dalších segmentů směrování:

|Zdroj                 |Předpony adres                       |Typ dalšího segmentu|Podsíť v rámci virtuální sítě, ke které je trasa přidaná|
|-----                  |----                                   |---------                    |--------|
|Výchozí                |Jedinečné pro virtuální síť, například 10.1.0.0/16|Partnerské vztahy virtuálních sítí                 |Vše|
|Brána virtuální sítě|Předpony inzerované z místního prostředí přes protokol BGP nebo nakonfigurované v místní síťové bráně     |Brána virtuální sítě      |Vše|
|Výchozí                |Několik                               |VirtualNetworkServiceEndpoint|Pouze podsíť, pro kterou je povolený koncový bod služby.|

- **Partnerský vztah virtuálních sítí (VNet):** Když mezi dvěma virtuálními sítěmi vytvoříte partnerský vztah, přidá se trasa pro každý rozsah adres v rámci adresního prostoru obou virtuálních sítí, pro které je partnerský vztah vytvořený. Další informace o [partnerském vztahu virtuálních sítí](virtual-network-peering-overview.md).  
- **Brána virtuální sítě:** Po přidání brány virtuální sítě do virtuální sítě se přidá jedna nebo více tras s uvedeným typem dalšího segmentu směrování *Brána virtuální sítě*. Zdrojem je také *brána virtuální sítě*, protože brána přidá trasy do podsítě. Pokud si vaše místní síťová brána vyměňuje trasy protokolu [BGP](#border-gateway-protocol) (Border Gateway Protocol) s bránou virtuální sítě Azure, přidá se trasa pro každou trasu rozšířenou z místní síťové brány. Doporučuje se shrnout místní trasy do největších možných rozsahů adres, aby se do brány virtuální sítě Azure šířilo co nejméně tras. Počet tras, které můžete rozšířit do brány virtuální sítě Azure, je omezený. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).
- **VirtualNetworkServiceEndpoint:** Když pro určité služby povolíte koncový bod služby, Azure do směrovací tabulky přidá veřejné IP adresy těchto služeb. Koncové body služby se povolují pro jednotlivé podsítě v rámci virtuální sítě, takže se trasa přidá pouze do směrovací tabulky podsítě, pro kterou je koncový bod služby povolený. Veřejné IP adresy služeb Azure se pravidelně mění. Azure při změně adres spravuje adresy ve směrovací tabulce automaticky. Další informace o [koncových bodech služby virtuální sítě](virtual-network-service-endpoints-overview.md) a službách, pro které můžete koncové body služby vytvořit. 

> [!NOTE]
> Typy dalších segmentů směrování **Partnerský vztah virtuálních sítí** a **VirtualNetworkServiceEndpoint** se přidají pouze do směrovacích tabulek podsítí v rámci virtuálních sítí vytvořených prostřednictvím modelu nasazení Azure Resource Manager. Tyto typy dalších segmentů směrování se nepřidají do směrovacích tabulek přidružených k podsítím virtuálních sítí vytvořeným prostřednictvím modelu nasazení Classic. Další informace o [modelech nasazení](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure.

## <a name="custom-routes"></a>Vlastní trasy

Vlastní trasy můžete vytvořit buď vytvořením tras [definovaných uživatelem](#user-defined), nebo výměnou tras [protokolu BGP](#border-gateway-protocol) (Border Gateway Protocol) mezi místní síťovou bránou a bránou virtuální sítě Azure. 
 
### <a name="user-defined"></a>Definované uživatelem

V Azure můžete vytvořit vlastní trasy neboli trasy definované uživatelem za účelem přepsání výchozích systémových tras Azure nebo přidání dalších tras do směrovací tabulky podsítě. V Azure vytvoříte směrovací tabulku a pak ji přidružíte k žádné nebo několika podsítím virtuální sítě. Každá podsíť může mít přidruženou žádnou nebo jednu směrovací tabulku. Další informace o maximálním počtu tras, které můžete přidat do směrovací tabulky, a maximálním počtu směrovacích tabulek definovaných uživatelem, které můžete vytvořit pro jedno předplatné Azure, najdete v tématu věnovaném [omezením Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Pokud vytvoříte směrovací tabulku a přidružíte ji k podsíti, trasy v ní se sloučí s výchozími trasami, které Azure přidá k podsíti ve výchozím nastavení, nebo je přepíší.

Při vytváření trasy definované uživatelem můžete zadat následující typy dalších segmentů směrování:

- **Virtuální zařízení:** Virtuální zařízení je virtuální počítač, na kterém je obvykle spuštěná síťová aplikace, jako je například brána firewall. Další informace o různých předkonfigurovaných virtuálních síťových zařízeních, která můžete nasadit ve virtuální síti, najdete na webu [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Při vytváření trasy s typem segmentu směrování **Virtuální zařízení** zadáváte také IP adresu dalšího segmentu směrování. Tato IP adresa může být:

    - [Privátní IP adresa](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) síťového rozhraní připojeného k virtuálnímu počítači. Pro každé síťové rozhraní připojené k virtuálnímu počítači, který předává provoz na jinou adresu než svou vlastní, musí být povolená možnost Azure *Povolit předávání IP*. Toto nastavení pro síťové rozhraní zakáže kontrolu zdroje a cíle, kterou provádí Azure. Další informace o [povolení předávání IP pro síťové rozhraní](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Přestože je možnost *Povolit předávání IP* nastavením Azure, možná bude nutné povolit předávání IP také v operačním systému virtuálního počítače, aby zařízení předávalo provoz mezi privátními IP adresami přiřazenými k síťovým rozhraním Azure. Pokud zařízení musí směrovat provoz na veřejnou IP adresu, musí buď předávat provoz přes proxy, nebo přeložit privátní IP adresu zdroje na vlastní privátní IP adresu, kterou pak Azure před odesláním provozu do internetu přeloží na veřejnou IP adresu. Informace o určení požadovaných nastavení v rámci virtuálního počítače najdete v dokumentaci k vašemu operačnímu systému nebo síťové aplikaci. Vysvětlení odchozích připojení v Azure najdete v tématu [Principy odchozích připojení](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

    > [!NOTE]
    > Virtuální zařízení nasaďte do jiné podsítě, než ve které jsou nasazené prostředky, které se přes toto virtuální zařízení směrují. Nasazení virtuálního zařízení do stejné podsítě a následné použití směrovací tabulky na podsíť, která směruje provoz přes toto virtuální zařízení, může mít za následek smyčky směrování, kdy provoz nikdy neopustí podsíť.

    - Privátní IP adresa [interního nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure. Nástroj pro vyrovnávání zatížení se často používá jako součást [strategie pro dosažení vysoké dostupnosti virtuálních síťových zařízení](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).

    Můžete definovat trasu s předponou adresy 0.0.0.0/0 a typem dalšího segmentu směrování Virtuální zařízení a tím umožnit zařízení kontrolovat provoz a určit, jestli ho předat, nebo zahodit. Pokud máte v úmyslu vytvořit trasu definovanou uživatelem, která obsahuje předponu adresy 0.0.0.0/0, přečtěte si nejprve část [Předpona adresy 0.0.0.0/0](#default-route).

- **Brána virtuální sítě:** Určete, kdy chcete směrovat provoz určený pro konkrétní předpony adres do brány virtuální sítě. Brána virtuální sítě musí být vytvořená s typem **VPN**. V trase definované uživatelem nemůžete zadat bránu virtuální sítě vytvořenou s typem **ExpressRoute**, protože v případě ExpressRoute musíte pro vlastní trasy použít protokol [BGP](#border-gateway-protocol-routes). Můžete definovat trasu, která směruje provoz určený pro předponu adresy 0.0.0.0/0 do brány virtuální sítě [založené na trasách](../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#vpntype). V místním prostředí můžete mít zařízení, které kontroluje provoz a určuje, jestli ho předat, nebo zahodit. Pokud máte v úmyslu vytvořit trasu definovanou uživatelem pro předponu adresy 0.0.0.0/0, přečtěte si nejprve část [Předpona adresy 0.0.0.0/0](#default-route). Pokud jste [povolili protokol BGP pro bránu virtuální sítě VPN](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), místo konfigurace trasy definované uživatelem pro předponu adresy 0.0.0.0/0 můžete trasu s předponou 0.0.0.0/0 inzerovat přes protokol BGP.
- **Žádný:** Určete, kdy chcete zahodit provoz určený pro předponu adresy, a nepředávat ho do cíle. Pokud jste nějakou možnost plně nenakonfigurovali, Azure může pro některé volitelné systémové trasy uvést typ *Žádný*. Pokud se například typ *Žádný* zobrazí jako **IP adresa dalšího segmentu směrování** s **Typem dalšího segmentu směrování** *Brána virtuální sítě* nebo *Virtuální zařízení*, může být důvodem zastavení nebo neúplná konfigurace zařízení. Azure vytvoří [výchozí systémové trasy](#default) pro vyhrazené předpony adres s typem dalšího segmentu směrování **Žádný**.
- **Virtuální síť:** Určete, kdy chcete přepsat výchozí směrování v rámci virtuální sítě. V části [Příklad směrování](#routing-example) najdete příklad, proč byste mohli vytvořit trasu s typem segmentu směrování **Virtuální síť**.
- **Internet:** Určete, kdy chcete explicitně směrovat provoz směřující na předponu adresy do internetu, nebo jestli chcete provoz určený pro služby Azure s veřejnými IP adresami zachovat v páteřní síti Azure.

V trasách definovaných uživatelem nemůžete jako typ dalšího segmentu směrování zadat **Partnerský vztah virtuálních sítí** ani **VirtualNetworkServiceEndpoint**. Trasy s typy dalších segmentů směrování **Partnerský vztah virtuálních sítí** nebo **VirtualNetworkServiceEndpoint** vytvoří Azure pouze v případě, že nakonfigurujete partnerský vztah virtuálních sítí nebo koncový bod služby.

**Typy dalších segmentů směrování napříč nástroji Azure**

Zobrazené a odkazované názvy typů dalších segmentů směrování se liší na webu Azure Portal a v nástrojích příkazového řádku a v modelech nasazení Azure Resource Manager a Classic. Následující tabulka uvádí názvy používané k odkazování na jednotlivé typy dalších segmentů směrování v různých nástrojích a [modelech nasazení](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json):

|Typ dalšího segmentu                   |Azure CLI 2.0 a PowerShell (Resource Manager) |Azure CLI 1.0 a PowerShell (Classic)|
|-------------                   |---------                                       |-----|
|Brána virtuální sítě         |VirtualNetworkGateway                           |VPNGateway|
|Virtuální síť                 |VNetLocal                                       |VNETLocal (není dostupné v CLI 1.0 v režimu asm)|
|Internet                        |Internet                                        |Internet (není dostupné v CLI 1.0 v režimu asm)|
|Virtuální zařízení               |VirtualAppliance                                |VirtualAppliance|
|Žádný                            |Žádný                                            |Null (není dostupné v CLI 1.0 v režimu asm)|
|Partnerské vztahy virtuálních sítí         |Partnerské vztahy virtuálních sítí                                    |Neuvedeno|
|Koncový bod služby virtuální sítě|VirtualNetworkServiceEndpoint                   |Neuvedeno|

### <a name="border-gateway-protocol"></a>Protokol BGP (Border Gateway Protocol)

Místní síťová brána si může vyměňovat trasy s bránou virtuální sítě Azure pomocí protokolu BGP (Border Gateway Protocol). Použití protokolu BGP s bránou virtuální sítě Azure závisí na typu, který jste vybrali při vytváření brány. Pokud jste vybrali typ:

- **ExpressRoute:** Musíte použít protokol BGP k inzerování místních tras do hraničního směrovače Microsoftu. Pokud jste bránu virtuální sítě nasadili s typem ExpressRoute, nemůžete vytvářet trasy definované uživatelem pro vynucení provozu do brány virtuální sítě ExpressRoute. Pro vynucení provozu z ExpressRoute třeba do virtuálního síťového zařízení můžete použít trasy definované uživatelem.
- **VPN:** Volitelně můžete použít protokol BGP. Podrobnosti najdete v tématu [Protokol BGP s připojeními VPN typu Site-to-Site](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Pokud vyměňujete trasy s Azure pomocí protokolu BGP, do směrovací tabulky všech podsítí ve virtuální síti se přidá samostatná trasa pro každou inzerovanou předponu. Trasa se přidá s uvedeným zdrojem a typem dalšího segmentu směrování *Brána virtuální sítě*. 

Šíření tras BGP je možné v podsíti zakázat pomocí vlastnosti ve směrovací tabulce. Pokud vyměňujete trasy s Azure pomocí protokolu BGP, do směrovací tabulky všech podsítí se zakázaným šířením BGP se trasy nepřidají. Možnosti připojení u připojení VPN zajišťují [vlastní trasy](#custom-routes) s typem dalšího segmentu směrování *Brána virtuální sítě*. Podrobnosti najdete v tématu popisujícím, [jak zakázat šíření tras BGP](manage-route-table.md#create-a-route-table).

## <a name="how-azure-selects-a-route"></a>Jak Azure vybírá trasu

Při odeslání odchozího provozu z podsítě vybere Azure trasu na základě cílové IP adresy pomocí algoritmu shody nejdelších předpon. Směrovací tabulka například obsahuje dvě trasy: Jedna trasa určuje předponu adresy 10.0.0.0/24, zatímco druhá trasa určuje předponu adresy 10.0.0.0/16. Azure směruje provoz určený pro adresu 10.0.0.5 na typ dalšího segmentu směrování určený v trase s předponou adresy 10.0.0.0/24, protože předpona 10.0.0.0/24 je delší než 10.0.0.0/16, přestože adresa 10.0.0.5 je v rozsahu obou předpon adres. Azure směruje provoz směřující na adresu 10.0.1.5 na typ dalšího segmentu směrování určený v trase s předponou adresy 10.0.0.0/16, protože adresa 10.0.1.5 není zahrnutá v předponě adresy 10.0.0.0/24, a proto je předpona adresy 10.0.0.0/16 nejdelší odpovídající předpona.

Pokud několik tras obsahuje stejnou předponu adresy, Azure vybere typ trasy na základě následující priority:

1. Trasa definovaná uživatelem
2. Trasa protokolu BGP
3. Systémová trasa

> [!NOTE]
> Pro provoz související s virtuální sítí, partnerskými vztahy virtuálních sítí nebo koncovými body služby virtuální sítě doporučujeme používat systémové trasy, i když jsou trasy protokolu BGP konkrétnější.

Směrovací tabulka obsahuje například následující trasy:


|Zdroj   |Předpony adres  |Typ dalšího segmentu           |
|---------|---------         |-------                 |
|Výchozí  | 0.0.0.0/0        |Internet                |
|Uživatel     | 0.0.0.0/0        |Brána virtuální sítě |

Pokud je provoz určený pro IP adresu mimo předpony adres jakýchkoli jiných tras ve směrovací tabulce, Azure vybere trasu se zdrojem **Uživatel**, protože trasy definované uživatelem mají vyšší prioritu než výchozí systémové trasy.

V části [Příklad směrování](#routing-example) najdete komplexní směrovací tabulku s vysvětlením tras, které obsahuje.

## <a name="default-route"></a>Předpona adresy 0.0.0.0/0

Trasa s předponou adresy 0.0.0.0/0 dává Azure pokyn, jak směrovat provoz určený pro IP adresu, která není v rozsahu předpony adresy žádné jiné trasy ve směrovací tabulce příslušné podsítě. Při vytvoření podsítě vytvoří Azure [výchozí](#default) trasu k předponě adresy 0.0.0.0/0 s typem dalšího segmentu směrování **Internet**. Pokud tuto trasu nepřepíšete, Azure bude veškerý provoz směřující na IP adresy, které nejsou zahrnuté v předponě adresy žádné jiné trasy, směrovat do internetu. Výjimkou je provoz směřující na veřejné IP adresy služeb Azure, který zůstane na páteřní síti Azure, a nesměruje se do internetu. Pokud tuto trasu přepíšete [vlastní](#custom-routes) trasou, provoz směřující na adresy, které nejsou zahrnuté v předponách adres žádné jiné trasy ve směrovací tabulce, se bude odesílat na virtuální síťové zařízení nebo do brány virtuální sítě v závislosti na tom, který cíl zadáte ve vlastní trase.

Když přepíšete předponu adresy 0.0.0.0/0, kromě přenosu odchozího provozu z podsítě přes bránu virtuální sítě nebo virtuální zařízení dojde u výchozího směrování Azure k následujícím změnám: 

- Azure odesílá veškerý provoz na typ dalšího segmentu směrování určený v trase, včetně provozu určeného pro veřejné IP adresy služeb Azure. Pokud je typ dalšího segmentu směrování pro trasu s předponou adresy 0.0.0.0/0 **Internet**, provoz z podsítě směřující na veřejné IP adresy služeb Azure nikdy neopustí páteřní síť Azure, a to bez ohledu na oblast Azure, ve které virtuální síť nebo prostředek služby Azure existuje. Pokud však vytvoříte trasu definovanou uživatelem nebo trasu protokolu BGP s typem dalšího segmentu směrování **Brána virtuální sítě** nebo **Virtuální zařízení**, veškerý provoz včetně provozu odeslaného na veřejné IP adresy služeb Azure, pro které jste nepovolili [koncové body služby](virtual-network-service-endpoints-overview.md), se odesílá na typ dalšího segmentu směrování určený v této trase. Pokud jste pro nějakou službu povolili koncový bod služby, provoz do této služby se nesměruje na typ dalšího segmentu směrování v trase s předponou adresy 0.0.0.0/0, protože předpony adres pro tuto službu jsou určené v trase, kterou Azure vytvoří při povolení koncového bodu služby, a jsou delší než 0.0.0.0/0.
- Už nemáte přímý přístup k prostředkům v podsíti z internetu. K prostředkům v podsítí můžete z internetu přistupovat nepřímo, pokud příchozí provoz před dosažením prostředku ve virtuální síti prochází zařízením určeným typem dalšího segmentu směrování pro trasu s předponou adresy 0.0.0.0/0. Pokud trasa obsahuje pro typ dalšího segmentu směrování následující hodnoty:
    - **Virtuální zařízení:** Zařízení musí:
        - Být přístupné z internetu
        - Mít přiřazenou veřejnou IP adresu
        - Nemít přidružené pravidlo skupiny zabezpečení sítě, které by bránilo komunikaci se zařízením
        - Neodepírat komunikaci
        - Být schopné překládat a předávat síťové adresy nebo předávat provoz přes proxy do cílového prostředku v podsíti a vracet provoz zpět do internetu. 
    - **Brána virtuální sítě:** Pokud je brána bránou virtuální sítě ExpressRoute, místní zařízení připojené k internetu musí být schopné překládat a předávat síťové adresy nebo předávat provoz přes proxy do cílového prostředku v podsíti přes [soukromý partnerský vztah](../expressroute/expressroute-circuit-peerings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-private-peering) ExpressRoute. 

Pokud je vaše virtuální síť připojená k Azure VPN gateway, nepřidružujte k [podsíti brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) směrovací tabulku, která má směrování s cílem 0.0.0.0/0. Mohli byste tím bráně znemožnit správné fungování. Podrobnosti najdete v otázce *Proč jsou některé porty brány VPN otevřené?* v [nejčastějších dotazech ke službě VPN Gateway](../vpn-gateway/vpn-gateway-vpn-faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#why-are-certain-ports-opened-on-my-vpn-gateway).

V tématech [DMZ mezi Azure a místním datovým centrem](/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid?toc=%2fazure%2fvirtual-network%2ftoc.json) a [DMZ mezi Azure a internetem](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2fazure%2fvirtual-network%2ftoc.json) najdete podrobné informace k implementaci při použití bran virtuálních sítí a virtuálních zařízení mezi internetem a Azure.

## <a name="routing-example"></a>Příklad směrování

Pro objasnění konceptů v tomto článku následující části popisují:
- Scénář s požadavky
- Vlastní trasy potřebné ke splnění těchto požadavků
- Směrovací tabulku pro jednu podsíť, která obsahuje výchozí a vlastní trasy potřebné ke splnění těchto požadavků

> [!NOTE]
> Tento příklad nemá představovat doporučenou implementaci ani implementaci podle osvědčených postupů. Jeho účelem je spíše objasnění konceptů v tomto článku.

### <a name="requirements"></a>Požadavky

1. Implementace dvou virtuálních sítí ve stejné oblasti Azure a povolení komunikace prostředků mezi těmito virtuálními sítěmi.
2. Povolení zabezpečené komunikace místní sítě s oběma virtuálními sítěmi přes internet prostřednictvím tunelu VPN. *Alternativně můžete použít připojení ExpressRoute, ale v tomto příkladu se používá připojení VPN.*
3. Pro jednu podsíť v jedné virtuální síti:
 
    - Vynucení toku veškerého odchozího provozu z podsítě, kromě provozu do služby Azure Storage a provozu v rámci podsítě, přes virtuální síťové zařízení pro účely zkoumání a protokolování.
    - Nezkoumání provozu mezi privátními IP adresami v rámci podsítě, ale umožnění přímého toku provozu mezi všemi prostředky. 
    - Zahození veškerého odchozího provozu určeného pro druhou virtuální síť.
    - Povolení toku odchozího provozu do úložiště Azure přímo do úložiště bez vynucování jeho průchodu přes virtuální síťové zařízení.

4. Povolení veškerého provozu mezi všemi ostatními podsítěmi a virtuálními sítěmi.

### <a name="implementation"></a>Implementace

Následující obrázek ukazuje implementaci prostřednictvím modelu nasazení Azure Resource Manager, která splňuje předchozí požadavky:

![Diagram sítě](./media/virtual-networks-udr-overview/routing-example.png)

Šipky ukazují tok provozu. 

### <a name="route-tables"></a>Směrovací tabulky

#### <a name="subnet1"></a>Podsíť Subnet1

Směrovací tabulka pro podsíť *Subnet1* na obrázku obsahuje následující trasy:

|ID  |Zdroj |Stav  |Předpony adres    |Typ dalšího segmentu          |IP adresa dalšího segmentu směrování|Název trasy definované uživatelem| 
|----|-------|-------|------              |-------                |--------           |--------      |
|1   |Výchozí|Neplatný|10.0.0.0/16         |Virtuální síť        |                   |              |
|2   |Uživatel   |Aktivní |10.0.0.0/16         |Virtuální zařízení      |10.0.100.4         |Within-VNet1  |
|3   |Uživatel   |Aktivní |10.0.0.0/24         |Virtuální síť        |                   |Within-Subnet1|
|4   |Výchozí|Neplatný|10.1.0.0/16         |Partnerské vztahy virtuálních sítí           |                   |              |
|5   |Výchozí|Neplatný|10.2.0.0/16         |Partnerské vztahy virtuálních sítí           |                   |              |
|6   |Uživatel   |Aktivní |10.1.0.0/16         |Žádný                   |                   |ToVNet2-1-Drop|
|7   |Uživatel   |Aktivní |10.2.0.0/16         |Žádný                   |                   |ToVNet2-2-Drop|
|8   |Výchozí|Neplatný|10.10.0.0/16        |Brána virtuální sítě|[X.X.X.X]          |              |
|9   |Uživatel   |Aktivní |10.10.0.0/16        |Virtuální zařízení      |10.0.100.4         |To-On-Prem    |
|10  |Výchozí|Aktivní |[X.X.X.X]           |VirtualNetworkServiceEndpoint    |         |              |
|11  |Výchozí|Neplatný|0.0.0.0/0           |Internet|              |                   |              |
|12  |Uživatel   |Aktivní |0.0.0.0/0           |Virtuální zařízení      |10.0.100.4         |Default-NVA   |

Následuje vysvětlení jednotlivých ID tras:

1. Platforma Azure tuto trasu automaticky přidala pro všechny podsítě v rámci virtuální sítě *Virtual-network-1*, protože 10.0.0.0/16 je jediný rozsah adres definovaný v adresním prostoru pro tuto virtuální síť. Kdyby nebyla vytvořená trasa definovaná uživatelem s ID 2, provoz odeslaný na jakoukoli adresu v rozsahu 10.0.0.1 až 10.0.255.254 by se směroval v rámci virtuální sítě, protože předpona je delší než 0.0.0.0/0 a není v rozsahu předpon adres žádné jiné trasy. Platforma Azure automaticky změnila stav z *Aktivní* na *Neplatný* při přidání trasy definované uživatelem s ID 2, protože má stejnou předponu jako výchozí trasa a trasy definované uživatelem přepisují výchozí trasy. Stav této trasy je stále *Aktivní* pro podsíť *Subnet2*, protože směrovací tabulka, ve které je trasa definovaná uživatelem s ID 2, není přidružená k podsíti *Subnet2*.
2. Platforma Azure tuto trasu přidala při přidružení trasy definované uživatelem pro předponu adresy 10.0.0.0/16 k podsíti *Subnet1* ve virtuální síti *Virtual-network-1*. Trasa definovaná uživatelem určuje 10.0.100.4 jako IP adresu virtuálního zařízení, protože tato adresa je privátní IP adresa přidružená k virtuálnímu počítači virtuálního zařízení. Směrovací tabulka, ve které tato trasa existuje, není přidružená k podsíti *Subnet2*, takže se trasa nezobrazí ve směrovací tabulce pro podsíť *Subnet2*. Tato trasa přepisuje výchozí trasu pro předponu 10.0.0.0/16 (ID 1), která automaticky směrovala provoz určený pro adresy 10.0.0.1 a 10.0.255.254 v rámci virtuální sítě přes typ dalšího segmentu směrování této virtuální sítě. Tato trasa existuje kvůli splnění [požadavku](#requirements) 3 na vynucení průchodu veškerého odchozího provozu přes virtuální zařízení.
3. Platforma Azure tuto trasu přidala při přidružení trasy definované uživatelem pro předponu adresy 10.0.0.0/24 k podsíti *Subnet1*. Provoz určený pro adresy v rozsahu 10.0.0.1 až 10.0.0.254 zůstává v rámci podsítě, a nesměruje se do virtuálního zařízení určeného v předchozím pravidle (ID 2), protože má delší předponu než trasa s ID 2. Tato trasa nebyla přidružená k podsíti *Subnet2*, takže se nezobrazí ve směrovací tabulce pro podsíť *Subnet2*. Tato trasa ve výsledku přepíše trasu s ID 2 pro provoz v rámci podsítě *Subnet1*. Tato trasa existuje kvůli splnění [požadavku](#requirements) 3.
4. Platforma Azure automaticky přidala trasy s ID 4 a 5 pro všechny podsítě v rámci virtuální sítě *Virtual-network-1* při vytvoření partnerského vztahu této virtuální sítě s virtuální sítí *Virtual-network-2*. Virtuální síť *Virtual-network-2* obsahuje ve svém adresním prostoru dva rozsahy adres, 10.1.0.0/16 a 10.2.0.0/16, takže platforma Azure přidala trasu pro oba rozsahy. Kdyby nebyly vytvořené trasy definované uživatelem s ID 6 a 7, provoz odeslaný na jakoukoli adresu v rozsahu 10.1.0.1 až 10.1.255.254 a 10.2.0.1 až 10.2.255.254 by se směroval do partnerské virtuální sítě, protože předpona je delší než 0.0.0.0/0 a není v rozsahu předpon adres žádné jiné trasy. Platforma Azure automaticky změnila stav z *Aktivní* na *Neplatný* při přidání tras s ID 6 a 7, protože mají stejné předpony jako trasy s ID 4 a 5 a trasy definované uživatelem přepisují výchozí trasy. Stav tras s ID 4 a 5 je stále *Aktivní* pro podsíť *Subnet2*, protože směrovací tabulka, ve které jsou trasy definované uživatelem s ID 4 a 5, není přidružená k podsíti *Subnet2*. Partnerský vztah virtuálních sítí byl vytvořený kvůli splnění [požadavku](#requirements) 1.
5. Stejné vysvětlení jako u ID 4.
6. Platforma Azure přidala tuto trasu a trasu s ID 7 při přiřazení tras definovaných uživatelem pro předpony adres 10.1.0.0/16 a 10.2.0.0/16 k podsíti *Subnet1*. Provoz určený pro adresy v rozsahu 10.1.0.1 až 10.1.255.254 a 10.2.0.1 až 10.2.255.254 Azure zahodí, a nesměruje ho do partnerské virtuální sítě, protože trasy definované uživatelem přepisují výchozí trasy. Trasy nejsou přidružené k podsíti *Subnet2*, takže se nezobrazí ve směrovací tabulce pro podsíť *Subnet2*. Trasy přepisují trasy s ID 4 a 5 pro odchozí provoz z podsítě *Subnet1*. Trasy s ID 6 a 7 existují kvůli splnění [požadavku](#requirements) 3 na zahození provozu směřujícího do druhé virtuální sítě.
7. Stejné vysvětlení jako u ID 6.
8. Platforma Azure automaticky přidala tuto trasu pro všechny podsítě v rámci virtuální sítě *Virtual-network-1* při vytvoření brány virtuální sítě typu VPN v této virtuální síti. Platforma Azure přidala veřejnou IP adresu brány virtuální sítě do směrovací tabulky. Provoz odeslaný na jakoukoli adresu v rozsahu 10.10.0.1 až 10.10.255.254 se směruje do brány virtuální sítě. Předpona je delší než 0.0.0.0/0 a není v rozsahu předpon adres žádné jiné trasy. Brána virtuální sítě byla vytvořená kvůli splnění [požadavku](#requirements) 2.
9. Platforma Azure tuto trasu přidala při přidání trasy definované uživatelem pro předponu adresy 10.10.0.0/16 do směrovací tabulky přidružené k podsíti *Subnet1*. Tato trasa přepisuje trasu s ID 8. Trasa odesílá veškerý provoz určený pro místní síť do NVA pro kontrolu, a nesměruje provoz přímo v místním prostředí. Tato trasa byla vytvořená kvůli splnění [požadavku](#requirements) 3.
10. Platforma Azure tuto trasu automaticky přidala do podsítě při povolení koncového bodu služby Azure pro tuto podsíť. Azure směruje provoz z této podsítě na veřejnou IP adresu služby přes síťovou infrastrukturu Azure. Předpona je delší než 0.0.0.0/0 a není v rozsahu předpon adres žádné jiné trasy. Koncový bod služby byl vytvořený kvůli splnění [požadavku](#requirements) 3 na povolení toku provozu určeného pro službu Azure Storage přímo do služby Azure Storage.
11. Platforma Azure tuto trasu automaticky přidala do směrovací tabulky všech podsítí v rámci virtuálních sítí *Virtual-network-1* a *Virtual-network-2*. Předpona adresy 0.0.0.0/0 je nejkratší předpona. Veškerý provoz odeslaný na adresy s delší předponou adresy se směruje podle jiných tras. Azure ve výchozím nastavení směruje veškerý provoz určený pro jiné adresy, než jsou adresy zadané v některé z ostatních tras, do internetu. Platforma Azure automaticky změnila stav z *Aktivní* na *Neplatný* pro podsíť *Subnet1* při přidružení trasy definované uživatelem pro předponu adresy 0.0.0.0/0 (ID 12) k této podsíti. Stav této trasy je stále *Aktivní* pro všechny ostatní podsítě v rámci obou virtuálních sítí, protože trasa není přidružená k žádné jiné podsíti v rámci žádné jiné virtuální sítě.
12. Platforma Azure tuto trasu přidala při přidružení trasy definované uživatelem pro předponu adresy 0.0.0.0/0 k podsíti *Subnet1*. Trasa definovaná uživatelem určuje 10.0.100.4 jako IP adresu virtuálního zařízení. Tato trasa není přidružená k podsíti *Subnet2*, takže se nezobrazí ve směrovací tabulce pro podsíť *Subnet2*. Veškerý provoz pro jakoukoli adresu, která není zahrnutá v předponách adres žádné jiné trasy, se odesílá do virtuálního zařízení. Přidáním této trasy se změnil stav výchozí trasy pro předponu adresy 0.0.0.0/0 (ID 11) z *Aktivní* na *Neplatný* pro podsíť *Subnet1*, protože trasa definovaná uživatelem přepisuje výchozí trasu. Tato trasa existuje kvůli splnění [požadavku](#requirements) 3.

#### <a name="subnet2"></a>Podsíť Subnet2

Směrovací tabulka pro podsíť *Subnet2* na obrázku obsahuje následující trasy:

|Zdroj  |Stav  |Předpony adres    |Typ dalšího segmentu             |IP adresa dalšího segmentu směrování|
|------- |-------|------              |-------                   |--------           
|Výchozí |Aktivní |10.0.0.0/16         |Virtuální síť           |                   |
|Výchozí |Aktivní |10.1.0.0/16         |Partnerské vztahy virtuálních sítí              |                   |
|Výchozí |Aktivní |10.2.0.0/16         |Partnerské vztahy virtuálních sítí              |                   |
|Výchozí |Aktivní |10.10.0.0/16        |Brána virtuální sítě   |[X.X.X.X]          |
|Výchozí |Aktivní |0.0.0.0/0           |Internet                  |                   |
|Výchozí |Aktivní |10.0.0.0/8          |Žádný                      |                   |
|Výchozí |Aktivní |100.64.0.0/10       |Žádný                      |                   |
|Výchozí |Aktivní |172.16.0.0/12       |Žádný                      |                   |
|Výchozí |Aktivní |192.168.0.0/16      |Žádný                      |                   |

Směrovací tabulka pro podsíť *Subnet2* obsahuje všechny výchozí trasy a volitelné partnerské vztahy virtuálních sítí, které vytvořila platforma Azure, a volitelné trasy brány virtuální sítě. Platforma Azure přidala volitelné trasy do všech podsítí ve virtuální síti při přidání brány a partnerského vztahu do virtuální sítě. Platforma Azure odebrala trasy pro předpony adres 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 a 100.64.0.0/10 ze směrovací tabulky podsítě *Subnet1* při přidání trasy definované uživatelem pro předponu adresy 0.0.0.0/0 do podsítě *Subnet1*.  

## <a name="next-steps"></a>Další kroky

- [Vytvoření směrovací tabulky definované uživatelem s trasami a virtuálními síťovými zařízeními](tutorial-create-route-table-portal.md)
- [Konfigurace protokolu BGP pro Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Použití protokolu BGP s ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#route-aggregation-and-prefix-limits)
- [Zobrazení všech tras pro podsíť](diagnose-network-routing-problem.md). Směrovací tabulka definovaná uživatelem zobrazuje pouze trasy definované uživatelem, a ne výchozí trasy ani trasy protokolu BGP pro podsíť. Při zobrazení všech tras se zobrazí výchozí trasy, trasy protokolu BGP a trasy definované uživatelem pro podsíť, ve které je síťové rozhraní.
- [Určení typu dalšího segmentu směrování](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) mezi virtuálním počítačem a cílovou IP adresou. Funkce dalšího segmentu směrování ve službě Azure Network Watcher umožňuje určit, jestli provoz odchází z podsítě a směruje se tam, kam by podle vás měl.

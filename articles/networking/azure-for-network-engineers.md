---
title: 'Azure ExpressRoute: technici pro sítě v Azure'
description: Tato stránka vysvětluje tradičním síťovým technikům, jak fungují sítě v Azure.
documentationcenter: na
services: networking
author: osamaz
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/25/2020
ms.author: osamaz
ms.openlocfilehash: 9a22e58e4407897fb9418cae0ba9f32408cda8e1
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234201"
---
# <a name="azure-for-network-engineers"></a>Azure pro odborníky na sítě
Jako standardní síťový inženýr jste se zabývali fyzickými prostředky, jako jsou směrovače, přepínače, kabely, brány firewall k vytváření infrastruktury. V logické vrstvě, kterou jste nakonfigurovali virtuální síť LAN (VLAN), protokol STP (Spanning Tree Protocol), směrovací protokoly (RIP, OSPF, BGP). Síť jste spravovali pomocí nástrojů pro správu a CLI. Sítě v cloudu jsou odlišné, pokud jsou koncové body sítě logické a používání směrovacích protokolů je minimální. Budete pracovat s rozhraním API Azure Resource Manager, Azure CLI a PowerShellu pro konfiguraci a správu prostředků v Azure. Cestu k síti zahájíte v cloudu tím, že budete rozumět základním klientům sítě Azure. 
## <a name="virtual-network"></a>Virtuální síť
Při navrhování sítě od nejnižší úrovně si shromáždíte některé základní informace. Tyto informace mohou být počtem hostitelů, síťových zařízení, počtu podsítí, směrování mezi podsítěmi, izolovanými doménami, jako jsou například sítě VLAN. Tyto informace pomáhají při změně velikosti sítě a zabezpečení zařízení a také vytváření architektury pro podporu aplikací a služeb.

Když plánujete nasazení aplikací a služeb v Azure, začnete tím, že vytvoříte logickou hranici v Azure, která se nazývá virtuální síť. Tato virtuální síť je podobají na hranici fyzické sítě. Vzhledem k tomu, že se jedná o virtuální síť, nepotřebujete fyzické lovná zařízení, ale budete muset naplánovat logické entity, jako jsou IP adresy, podsítě IP, směrování a zásady.

Když vytvoříte virtuální síť v Azure, je předem nakonfigurovaná s rozsahem IP adres (10.0.0.0/16). Tento rozsah není vyřešen, můžete definovat vlastní rozsah IP adres. Můžete definovat rozsah adres IPv4 i IPv6. Rozsahy IP adres definované pro virtuální síť nejsou inzerovány do Internetu. Z rozsahu IP adres můžete vytvořit více podsítí. Tyto podsítě budou použity k přiřazování IP adres k rozhraním virtuální sítě (virtuální síťové adaptéry). První čtyři IP adresy z každé podsítě jsou vyhrazené a nedají se použít pro přidělování IP adres. Ve veřejném cloudu neexistuje koncept sítí VLAN. Můžete ale vytvořit izolaci v rámci virtuální sítě na základě definovaných podsítí.

Můžete vytvořit jednu velkou podsíť, která zahrnuje všechny adresní prostory virtuální sítě, nebo se rozhodnout vytvořit několik podsítí. Pokud ale používáte bránu virtuální sítě, Azure vyžaduje, abyste vytvořili podsíť s názvem "podsíť brány". Azure bude tuto podsíť používat k přiřazování IP adres branám virtuální sítě. 

## <a name="ip-allocation"></a>Přidělení IP adresy

Když přiřadíte IP adresu hostiteli, ve skutečnosti přiřadíte IP adresu síťové kartě (NIC). Síťovému rozhraní v Azure můžete přiřadit dva typy IP adres:

- Veřejné IP adresy – slouží ke komunikaci příchozího a odchozího (bez překladu adres (NAT)) s internetem a dalšími prostředky Azure, které nejsou připojené k virtuální síti. Přiřazení veřejné IP adresy síťovému rozhraní je volitelné. Veřejné IP adresy patří do adresního prostoru IP adres Microsoftu.
- Privátní IP adresy – slouží ke komunikaci v rámci virtuální sítě, místní sítě a Internetu (s překladem adres (NAT)). Adresní prostor IP adres, který definujete ve virtuální síti, se považuje za soukromý, i když nakonfigurujete adresní prostor veřejných IP adres. Společnost Microsoft tento prostor neinzeruje k Internetu. Virtuálnímu počítači je nutné přiřadit alespoň jeden privátní IP adresu.

Stejně jako u fyzických hostitelů nebo zařízení existují dva způsoby, jak přidělit IP adresy prostředku – dynamické nebo statické. V Azure je výchozí metoda přidělení dynamická, kde se IP adresa přidělí při vytváření virtuálního počítače nebo spuštění zastaveného virtuálního počítače. Když tento virtuální počítač zastavíte nebo odstraníte, IP adresa se uvolní. Pokud chcete zajistit, aby IP adresa virtuálního počítače zůstala stejná, můžete explicitně nastavit statickou metodu přidělování. V takovém případě se IP adresa přiřadí okamžitě. Uvolní se, jenom když virtuální počítač odstraníte nebo změníte jeho metodu přidělování na dynamickou. 

Privátní IP adresy se přiřazují z podsítí, které jste definovali v rámci virtuální sítě. Pro virtuální počítač vyberte podsíť pro přidělování IP adres. Pokud virtuální počítač obsahuje více síťových adaptérů, můžete pro každou síťovou kartu vybrat jinou podsíť.

## <a name="routing"></a>Směrování
Když vytvoříte virtuální síť, Azure vytvoří směrovací tabulku pro vaši síť. Tato směrovací tabulka obsahuje následující typy tras.
- Systémové trasy
- Výchozí trasy podsítě
- Trasy z jiných virtuálních sítí
- Trasy protokolu BGP
- Trasy koncového bodu služby
- Trasy definované uživatelem (UDR)

Při prvním vytvoření virtuální sítě bez definování podsítí vytvoří Azure položky směrování ve směrovací tabulce. Tyto trasy se nazývají systémové trasy. V tomto umístění jsou definovány systémové trasy. Tyto trasy nemůžete změnit. Pomocí konfigurace udr ale můžete přepsat systémové trasy.

Když vytvoříte jednu nebo více podsítí v rámci virtuální sítě, Azure vytvoří ve směrovací tabulce výchozí záznamy, které umožní komunikaci mezi těmito podsítěmi v rámci virtuální sítě. Tyto trasy je možné upravit pomocí statických tras, které jsou uživatelsky definovanými trasami (UDR) v Azure.

Při vytváření partnerského vztahu virtuální sítě mezi dvěma virtuálními sítěmi se přidá trasa pro každý rozsah adres v rámci adresního prostoru každé virtuální sítě, pro který se vytvoří partnerský vztah.

Pokud vaše místní síťová brána naměňuje trasy protokolu BGP (Border Gateway Protocol) s bránou virtuální sítě Azure, přidá se trasa pro každou trasu rozšířenou z místní síťové brány. Tyto trasy se zobrazí ve směrovací tabulce jako trasy protokolu BGP.

Veřejné IP adresy pro určité služby se do směrovací tabulky přidá pomocí Azure při povolení koncového bodu služby pro službu. Koncové body služby jsou povolené pro jednotlivé podsítě v rámci virtuální sítě. Při povolení koncového bodu služby je trasa přidána pouze do směrovací tabulky pro podsíť, která patří do této služby. Azure při změně adres spravuje adresy ve směrovací tabulce automaticky.

Trasy definované uživatelem se označují také jako vlastní trasy. V Azure vytvoříte UDR, abyste přepsali výchozí systémové trasy Azure nebo aby se do směrovací tabulky podsítě přidaly další trasy. V Azure vytvoříte směrovací tabulku a potom přiřadíte směrovací tabulku k podsítím virtuální sítě.

Když ve směrovací tabulce máte konkurenční záznamy, Azure vybere další segment směrování na základě nejdelší shody předpony podobné tradičním směrovačům. Pokud však existuje více položek dalšího směrování se stejnou předponou adresy, Azure vybere trasy v následujícím pořadí.
1. Trasy definované uživatelem (UDR)
1. Trasy protokolu BGP
1. Systémové trasy

## <a name="security"></a>Zabezpečení

Pomocí skupin zabezpečení sítě můžete filtrovat síťový provoz do a z prostředků ve virtuální síti. Můžete také použít síťová virtuální zařízení (síťové virtuální zařízení), například Azure Firewall nebo brány firewall od jiných dodavatelů. Můžete řídit, jak Azure směruje provoz z podsítí. Můžete také omezit, kdo ve vaší organizaci může pracovat s prostředky ve virtuálních sítích.

Skupina zabezpečení sítě (NSG) obsahuje seznam pravidel seznamu řízení přístupu (ACL), která povolují nebo zamítají síťový provoz pro podsítě, síťová rozhraní nebo oboje. Skupiny NSG můžou být přidružené buď k podsítím, nebo k jednotlivým síťovým rozhraním připojeným k podsíti. Pokud je skupina zabezpečení sítě přidružená k podsíti, pravidla seznamu ACL platí pro všechny virtuální počítače v této podsíti. Provoz směřující do konkrétního síťového rozhraní se navíc dá omezit tím, že se přímo k tomuto síťovému rozhraní přidruží skupina NSG.

Skupiny NSG obsahují dvě sady pravidel: příchozí a odchozí. Priorita pravidla musí být v rámci jednotlivých sad jedinečná. Každé pravidlo má vlastnosti, které popisují protokol, zdrojový a cílový rozsah portů, předpony adres, směr přenosu, prioritu a typ přístupu. Všechny skupiny NSG obsahují sadu výchozích pravidel. Výchozí pravidla se nedají odstranit, ale protože je jim přiřazená nejnižší priorita, dají se přepsat pravidly, která vytvoříte.

## <a name="verification"></a>Ověření
### <a name="routes-in-virtual-network"></a>Trasy ve virtuální síti
Kombinace tras, které vytvoříte, výchozích tras Azure a všech tras, které se šíří z vaší místní sítě prostřednictvím služby Azure VPN Gateway (Pokud je vaše virtuální síť připojená k vaší místní síti) prostřednictvím protokolu BGP (Border Gateway Protocol), jsou platné trasy pro všechna síťová rozhraní v podsíti. Tyto efektivní trasy můžete zobrazit tak, že přejdete na kartu NIC prostřednictvím portálu, PowerShellu nebo rozhraní příkazového řádku.
### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)
Platná pravidla zabezpečení používaná pro síťové rozhraní jsou agregace pravidel, která existují v NSG přidružených k síťovému rozhraní, a v podsíti, ve které se síťové rozhraní nachází. Všechna platná pravidla zabezpečení můžete zobrazit z skupin zabezpečení sítě, která se aplikují na síťová rozhraní vašeho virtuálního počítače, a to tak, že přejdete na síťovou kartu prostřednictvím portálu, PowerShellu nebo rozhraní příkazového řádku.

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o službě [Virtual Network][VNet].

Přečtěte si o [Směrování virtuální sítě][vnet-routing].

Seznamte se se [skupinami zabezpečení sítě][network-security].

<!--Link References-->
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[vnet-routing]: ../virtual-network/virtual-networks-udr-overview.md
[network-security]: ../virtual-network/network-security-groups-overview.md
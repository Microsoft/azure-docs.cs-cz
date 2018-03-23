---
title: Partnerské vztahy virtuálních sítí v Azure | Dokumentace Microsoftu
description: Seznamte se s partnerskými vztahy virtuálních sítí v Azure.
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: narayan;anavin
ms.openlocfilehash: 23281067021dd6e4b8959fe73f3c8a11a651d9d2
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="virtual-network-peering"></a>Partnerské vztahy virtuálních sítí

Partnerské vztahy virtuálních sítí umožňují bezproblémové propojení dvou [virtuálních sítí](virtual-networks-overview.md) Azure. Po navázání partnerského vztahu se virtuální sítě pro účely připojení jeví jako jedna síť. Provoz mezi virtuálními počítači v partnerských virtuálních sítích je směrován přes páteřní infrastrukturu Microsoftu v zásadě stejně jako mezi virtuálními počítači ve stejné virtuální síti pouze přes *privátní* IP adresy. 

Mezi výhody použití partnerských vztahů virtuálních sítí patří:

* Provoz mezi partnerskými virtuálními sítěmi je privátní. Provoz mezi virtuálními sítěmi probíhá na páteřní síti Microsoftu. Komunikace mezi partnerskými virtuálními sítěmi nevyžaduje veřejný internet, brány ani šifrování.
* Nízká latence a velká šířka pásma při propojení prostředků v různých virtuálních sítích.
* Možnost komunikace prostředků v jedné virtuální síti s prostředky v jiné virtuální síti, jakmile je vytvořený partnerský vztah těchto virtuálních sítí.
* Možnost přenášet data mezi předplatnými Azure, modely nasazení a oblastmi Azure (Preview).
* Možnost navázání partnerského vztahu mezi virtuálními sítěmi vytvořenými prostřednictvím modelu nasazení Azure Resource Manager, nebo mezi jednou virtuální sítí vytvořenou prostřednictvím modelu nasazení Resource Manager a druhou vytvořenou prostřednictvím modelu nasazení Classic. Další informace o modelech nasazení Azure najdete v článku [Vysvětlení modelů nasazení Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Žádný výpadek prostředků ve virtuálních sítích při vytváření partnerského vztahu ani po jeho vytvoření.

## <a name="requirements-constraints"></a>Požadavky a omezení

* Vytváření partnerských vztahů virtuálních sítí ve stejné oblasti je všeobecně dostupné. Virtuální sítě partnerských vztahů v různých oblastech je v současné době ve verzi Preview pro oblasti USA – středozápad, Střední Kanada, USA – západ 2, Korea – jih, Velká Británie – jih, Velká Británie – západ, Východní Kanada, Indie – jih, Indie – střed a Indie – západ. Před vytvořením partnerského vztahu virtuálních sítí v různých oblastech musíte nejprve [zaregistrovat své předplatné](tutorial-connect-virtual-networks-powershell.md#register) pro verzi Preview. Pokud nedokončíte registraci verze Preview, nebude pokus o vytvoření partnerského vztahu virtuálních sítí v různých oblastech úspěšný.
    > [!WARNING]
    > Partnerské vztahy virtuálních sítí vytvořené mezi oblastmi nemusí dosahovat stejné úrovně dostupnosti a spolehlivosti jako partnerské vztahy ve verzi všeobecné dostupnosti. Partnerské vztahy virtuálních sítí můžou mít omezené možnosti a nemusí být dostupné ve všech oblastech Azure. Nejaktuálnější oznámení o dostupnosti a stavu této funkce najdete na stránce [Aktualizace služby Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

* Adresní prostory IP adres partnerských virtuálních sítí se nesmí překrývat.
* Rozsahy adres nelze do adresních prostorů virtuálních sítí přidávat ani je z něj odstraňovat, jakmile je mezi dvěma virtuálními sítěmi vytvořen partnerský vztah. Pokud potřebujete přidat rozsahy adres do adresního prostoru partnerské virtuální sítě, musíte odebrat partnerský vztah, přidat adresní prostor a pak znovu přidat partnerský vztah.
* Partnerský vztah virtuálních sítí se navazuje mezi dvěma virtuálními sítěmi. Nevzniká žádný odvozený tranzitivní vztah přes partnerské vztahy. Pokud je například virtuální síť A v partnerském vztahu s virtuální sítí B a virtuální síť B v partnerském vztahu s virtuální sítí C, pak virtuální síť A *není* v partnerském vztahu s virtuální sítí C.
* Partnerský vztah lze navázat mezi virtuálními sítěmi ve dvou různých předplatných za předpokladu, že navázání tohoto vztahu u obou předplatných autorizuje uživatel s oprávněním správce (viz [konkrétní oprávnění](create-peering-different-deployment-models-subscriptions.md#permissions)) a že předplatná jsou přidružená ke stejnému tenantovi Azure Active Directory. K propojení virtuálních sítí v předplatných přidružených k různým tenantům Active Directory můžete použít [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V).
* Mezi virtuálními sítěmi lze navázat partnerský vztah, pokud jsou obě vytvořené prostřednictvím modelu nasazení Resource Manager, nebo pokud je jedna virtuální síť vytvořena prostřednictvím modelu nasazení Resource Manager a druhá prostřednictvím modelu nasazení Classic. Nejde ale navázat partnerský vztah mezi virtuálními sítěmi vytvořenými prostřednictvím modelu nasazení Classic. K propojení virtuálních sítí vytvořených prostřednictvím modelu nasazení Classic můžete použít službu [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V).
* Ačkoli komunikace mezi virtuálními počítači ve virtuálních sítích propojených partnerským vztahem nemá žádná další omezení šířky pásma, stále platí omezení šířky pásma na základě velikosti virtuálního počítače. Další informace o maximální šířce pásma pro různé velikosti virtuálních počítačů najdete v článcích o velikosti virtuálních počítačů pro [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

     ![Základní partnerské vztahy virtuálních sítí](./media/virtual-networks-peering-overview/figure03.png)

## <a name="connectivity"></a>Připojení

Po navázání partnerského vztahu mezi virtuálními sítěmi se prostředky v některé z virtuálních sítí můžou přímo připojit k prostředkům v partnerské virtuální síti.

Latence sítě mezi virtuálními počítači v partnerských virtuálních sítích ve stejné oblasti je stejná jako latence v rámci jedné virtuální sítě. Propustnost sítě závisí na šířce pásma, která je pro daný virtuální počítač povolená na základě jeho velikosti. S partnerským vztahem zde nejsou žádná další omezení týkající se šířky pásma.

Provoz mezi virtuálními počítači v partnerských virtuálních sítích je směrován přímo přes páteřní infrastrukturu Microsoftu, ne prostřednictvím brány ani přes veřejný internet.

Virtuální počítače ve virtuální síti mají přístup k internímu nástroji pro vyrovnávání zatížení v partnerské virtuální síti ve stejné oblasti. Podpora interního nástroje pro vyrovnávání zatížení se nevztahuje na virtuální sítě v globálním partnerském vztahu (Preview). Všeobecně dostupná verze globálních partnerských vztahů virtuálních sítí bude zahrnovat podporu interního nástroje pro vyrovnávání zatížení.

Skupiny zabezpečení sítě je možné v obou virtuálních sítích v případě potřeby použít k blokování přístupu do jiných virtuálních sítí nebo podsítí.
Při konfiguraci partnerského vztahu virtuálních sítí můžete otevřít nebo zavřít pravidla skupiny zabezpečení sítě mezi virtuálními sítěmi. Pokud otevřete úplné propojení mezi partnerskými virtuálními sítěmi (výchozí volba), můžete pomocí skupin zabezpečení sítě u konkrétních podsítí nebo virtuálních počítačů blokovat nebo odepřít specifický přístup. Další informace o skupinách zabezpečení sítě najdete v tématu [Přehled skupin zabezpečení sítě](virtual-networks-nsg.md).

## <a name="service-chaining"></a>Řetězení služeb

Pokud chcete povolit řetězení služeb, nakonfigurujte uživatelsky definované trasy, které míří k virtuálním počítačům v partnerských virtuálních sítích (jako IP adresy *dalšího směrování*) nebo k jejich branám. Řetězení služeb umožňuje směrovat přenos dat z jedné virtuální sítě do virtuálního zařízení nebo brány virtuální sítě v partnerské virtuální síti prostřednictvím uživatelsky definovaných tras.

Hvězdicové sítě můžete nasadit, když centrální virtuální síť hostuje součásti infrastruktury, jako je virtuální síťové zařízení nebo služba VPN Gateway. Všechny uvedené virtuální sítě pak můžou vytvořit partnerský vztah s centrální virtuální sítí. Provoz v centrální virtuální síti probíhá přes virtuální síťové zařízení nebo brány VPN. 

Partnerské vztahy virtuálních sítí umožňují, aby další segment směrování v uživatelsky definované trase byl IP adresou virtuálního počítače v partnerské virtuální síti nebo branou VPN. K vytvoření trasy mezi virtuálními sítěmi ale nemůžete použít uživatelsky definovanou trasu, ve které jako další typ segmentu směrování použijete bránu ExpressRoute. Další informace o uživatelsky definovaných trasách najdete v [přehledu uživatelsky definovaných tras](virtual-networks-udr-overview.md#user-defined). Informace o vytvoření hvězdicové síťové topologie najdete v tématu věnovaném [hvězdicové síťové topologii](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering).

## <a name="gateways-and-on-premises-connectivity"></a>Brány a místní připojení

Každá virtuální síť, bez ohledu na to, jestli má navázaný partnerský vztah s jinou virtuální sítí, může mít stále svou vlastní bránu a používat ji pro připojení k místní síti. Můžete také nakonfigurovat [připojení mezi virtuálními sítěmi](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pomocí bran, a to i v situaci, kdy jsou tyto virtuální sítě v partnerském vztahu.

Když jsou nakonfigurovány obě možnosti pro propojení virtuálních sítí, přenos dat mezi těmito sítěmi probíhá s použitím konfigurace partnerského vztahu (t.j. přes páteřní síť Azure).

Když je mezi virtuálními sítěmi navázán partnerský vztah ve stejné oblasti, můžete také v partnerské virtuální síti nakonfigurovat bránu, která bude sloužit jako tranzitní bod pro místní síť. V tomto případě virtuální síť, která používá vzdálenou bránu, nemůže mít svou vlastní bránu. Virtuální síť může mít pouze jednu bránu. Může jí být buď místní brána, nebo vzdálená brána (v partnerské virtuální síti), jak ukazuje následující obrázek:

![přenos dat při partnerských vztazích virtuálních sítí](./media/virtual-networks-peering-overview/figure04.png)

Průchod branou není podporován v případě, že jde o partnerský vztah mezi virtuálními sítěmi vytvořenými pomocí různých modelů nasazení nebo v různých oblastech. Pokud chcete používat průchod branou, je nutné, aby byly obě partnerské virtuální sítě vytvořené pomocí modelu nasazení Resource Manager a ve stejné oblasti. Virtuální sítě v globálním partnerském vztahu aktuálně průchod branou nepodporují.

Po nastavení partnerského vztahu mezi virtuálními sítěmi sdílejícími jedno připojení Azure ExpressRoute přenos dat prochází skrze partnerský vztah (tj. prostřednictvím páteřní sítě Azure). K připojení k místnímu okruhu lze v obou sítích nadále používat místní brány. Alternativním postupem je použití sdílené brány a konfigurace průchodu pro místní připojení.

## <a name="permissions"></a>Oprávnění

Navázání partnerského vztahu virtuální sítě je privilegovaná operace. Jde o samostatnou funkci v rámci oboru názvů VirtualNetworks. Uživatel může dostat specifická práva k autorizaci partnerských vztahů. Uživatel, který má přístup pro čtení a zápis do virtuální sítě, zdědí tato práva automaticky.

Uživatel, který je buď správcem nebo privilegovaným uživatelem pro navázání partnerských vztahů, může iniciovat operaci navázání partnerství s jinou virtuální sítí. Minimální úroveň požadovaných oprávnění je Přispěvatel sítě. Pokud i na druhé straně existuje odpovídající požadavek a jsou splněny i další podmínky, partnerský vztah bude navázán.

Pokud jste například navazovali partnerský vztah mezi virtuálními sítěmi moje_virtuální_síť_A a moje_virtuální_síť_B, váš účet musí mít pro každou virtuální síť přiřazenou minimálně následující roli nebo oprávnění:

|Virtuální síť|Model nasazení|Role|Oprávnění|
|---|---|---|---|
|moje_virtuální_síť_A|Resource Manager|[Přispěvatel sítě](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Classic|[Přispěvatel klasických sítí](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|neuvedeno|
|moje_virtuální_síť_B|Resource Manager|[Přispěvatel sítě](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Classic|[Přispěvatel klasických sítí](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

## <a name="monitor"></a>Monitorování

Pokud navazujete partnerský vztah mezi dvěma virtuálními sítěmi vytvořenými prostřednictvím Resource Manageru, v partnerském vztahu musí být nakonfigurován partnerský vztah pro každou virtuální síť. Stav partnerského propojení můžete monitorovat. Partnerský vztah může být v jednom z následujících stavů:

* **Zahájeno:** Stav se zobrazí po vytvoření partnerského připojení z první virtuální sítě do druhé.
* **Připojeno:** Stav se zobrazí po vytvoření partnerského připojení z druhé virtuální sítě do první. Stav připojení první virtuální sítě se změní ze *Zahájeno* na *Připojeno*. Partnerský vztah virtuálních sítí není úspěšně navázán, dokud stav partnerského vztahu obou virtuálních sítí není *Připojeno*.
* **Odpojeno:** Stav se zobrazí, pokud po navázání partnerského vztahu mezi dvěma virtuálními sítěmi dojde k odstranění partnerského vztahu jedné virtuální sítě k druhé.

## <a name="troubleshoot"></a>Řešení potíží

Pokud chcete partnerský vztah virtuální sítě potvrdit, musíte [zkontrolovat platné trasy](virtual-network-routes-troubleshoot-portal.md) síťového rozhraní v každé podsíti virtuální sítě. Pokud partnerský vztah virtuální sítě existuje, mají všechny podsítě virtuální sítě trasy s typem dalšího přechodu *VNet peering*, a to u každého adresního prostoru v každé partnerské virtuální síti.

K řešení problémů s připojením k virtuálnímu počítači v partnerské virtuální síti také můžete použít [kontrolu připojení](../network-watcher/network-watcher-connectivity-portal.md) ve službě Network Watcher. Kontrola připojení zobrazí směrování provozu od síťového rozhraní zdrojového virtuálního počítače k síťovému rozhraní cílového virtuálního počítače.

## <a name="limits"></a>Omezení

Existují omezení týkající se počtu partnerských vztahů, které lze vytvořit pro jednu virtuální síť. Podrobnosti najdete v tématu [Omezení pro sítě v Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="pricing"></a>Ceny

Za příchozí a výchozí přenos využívající partnerské propojení virtuálních sítí se účtuje nominální poplatek. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Další kroky

* Dokončete kurz o partnerském vztahu virtuálních sítí. Partnerský vztah virtuálních sítí se vytváří mezi virtuálními sítěmi vytvořenými prostřednictvím stejného modelu nebo různých modelů nasazení, které existují ve stejném předplatném nebo různých předplatných. Dokončete kurz pro jeden z následujících scénářů:

    |Model nasazení Azure  | Předplatné  |
    |---------|---------|
    |Obě Resource Manager |[Stejné](tutorial-connect-virtual-networks-portal.md)|
    | |[Různé](create-peering-different-subscriptions.md)|
    |Jedna Resource Manager, druhá Classic     |[Stejné](create-peering-different-deployment-models.md)|
    | |[Různé](create-peering-different-deployment-models-subscriptions.md)|

* Zjistěte, jak vytvořit [hvězdicovou síťovou topologii](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering).
* Prostudujte si všechna [nastavení partnerského vztahu virtuálních sítí a jejich změny](virtual-network-manage-peering.md).

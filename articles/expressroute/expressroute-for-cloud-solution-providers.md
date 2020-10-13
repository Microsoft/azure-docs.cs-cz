---
title: ExpressRoute pro poskytovatele Cloud Solution Provider – Azure | Microsoft Docs
description: Tento článek poskytuje informace pro poskytovatele cloudových řešení, které chtějí začlenit služby Azure a ExpressRoute do jejich nabídek.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 17b8fc3824fb1c7e6cfcfc3d4333dc226b51724d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653634"
---
# <a name="expressroute-for-cloud-solution-providers-csp"></a>Azure ExpressRoute pro poskytovatele Cloud Solution Provider
Společnost Microsoft poskytuje pro tradiční prodejce a distributory (poskytovatele Cloud Solution Provider) hyperškálovatelné služby, aby mohli pro vaše zákazníky rychle zřizovat nové služby a řešení bez nutnosti investovat do vývoje těchto nových služeb. Aby měl poskytovatel Cloud Solution Provider (CSP) možnosti spravovat tyto služby přímo, poskytuje společnost Microsoft programy a rozhraní API umožňující poskytovateli CSP spravovat prostředky Microsoft Azure za své zákazníky. Jeden z těchto prostředků je ExpressRoute. ExpressRoute umožňuje poskytovateli CSP připojovat prostředky zákazníků ke službám Azure. ExpressRoute je vysokorychlostní propojení privátních komunikací se službami v Azure. 

ExpressRoute se skládá z páru okruhů pro zajištění vysoké dostupnosti, které jsou připojeny k předplatným jednoho zákazníka a nemohou být sdíleny více zákazníky. Každý okruh by měl být ukončen v různých směrovačích, aby se zajistila vysoká dostupnost.

> [!NOTE]
> V ExpressRoute se uplatňují omezení šířky pásma a počtu připojení, což znamená, že velké nebo složité implementace budou vyžadovat více okruhů ExpressRoute pro jednoho zákazníka.
> 
> 

Množství služeb Microsoft Azure, které můžete nabídnout zákazníkům, se stále zvyšuje. ExpressRoute pomáhá a vašim zákazníkům využívat tyto služby tím, že poskytuje vysokorychlostní přístup s nízkou latencí pro Microsoft Azure prostředí.

## <a name="microsoft-azure-management"></a>Správa Microsoft Azure
Společnost Microsoft poskytuje CSP s rozhraními API pro správu předplatných zákazníků Azure tím, že umožňuje programovou integraci s vlastními systémy správy služeb. Podporované možnosti správy najdete [tady](https://msdn.microsoft.com/library/partnercenter/dn974944.aspx).

## <a name="microsoft-azure-resource-management"></a>Správa prostředků Microsoft Azure
Způsob správy předplatného závisí na vaší smlouvě se zákazníkem. Poskytovatel CSP může přímo spravovat vytváření a správu prostředků, případně si může zákazník ponechat kontrolu nad předplatným Microsoft Azure a vytvářet prostředky Azure podle potřeby. Pokud zákazník spravuje vytváření prostředků v rámci předplatného Microsoft Azure, bude používat jeden ze dvou modelů: "*Connect-through*" nebo "*Direct-to*" (přímý) model. Tyto modely jsou podrobně popsány v následujících oddílech.  

### <a name="connect-through-model"></a>Model s nepřímým připojením
![Diagram, který zobrazuje model "Connect-through".](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

V případě modelu s nepřímým připojením poskytovatel CSP vytvoří přímé připojení mezi vaším datovým centrem a předplatným Azure zákazníka. Přímé připojení se vytvoří pomocí ExpressRoute, které propojuje vaši síť s Azure. Pak se zákazník připojí k vaší síti. Tento scénář vyžaduje, aby zákazník při přístupu ke službám Azure procházel sítí poskytovatele CSP. 

Pokud má váš zákazník jiná předplatná Azure, která nespravujete vy, používá veřejný Internet nebo vlastní privátní připojení pro připojení k těmto službám zřízeným v rámci předplatného, které není CSP. 

U CSP, který spravuje služby Azure, se předpokládá, že zprostředkovatel CSP má dřív zřízené úložiště zákaznických identit, které by se pak replikoval do Azure Active Directory pro správu předplatného CSP prostřednictvím správy (ADMINISTRATE). Mezi klíčové ovladače pro tento scénář patří situace, kdy má daný partner nebo poskytovatel služeb vytvořen vztah se zákazníkem, zákazník aktuálně spotřebovává služby poskytovatele nebo partner chce poskytnout kombinaci řešení hostovaných poskytovatelem a hostovaného Azure, aby poskytoval flexibilitu a vyřešila výzvy zákazníků, které nemůžou být splněné samotným zprostředkovatelem CSP. Tento model je znázorněn na následujícím **obrázku** .

![Diagram, který zobrazuje podrobný scénář pro model "Connect-through".](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Model s přímým připojením
![Diagram, který zobrazuje model "připojit k".](./media/expressroute-for-cloud-solution-providers/connect-to.png)

V případě modelu s přímým připojením vytvoří poskytovatel služby přímé připojení mezi datovým centrem zákazníka předplatným Azure, které je zřizováno poskytovatelem CSP, s použitím ExpressRoute prostřednictvím sítě zákazníka.

> [!NOTE]
> Pro ExpressRoute zákazník potřebuje vytvořit a spravovat okruh ExpressRoute.  
> 
> 

Tento scénář připojení vyžaduje, aby se zákazník připojil přímo přes zákaznickou síť, aby mohl získat přístup k předplatnému Azure spravovanému CSP, a to pomocí přímého síťového připojení, které se vytvoří, je vlastněné a spravované zákazníkem zcela nebo částečně. Pro tyto zákazníky se předpokládá, že zprostředkovatel aktuálně nemá vytvořené úložiště zákaznických identit, a poskytovatel by mu pomohl při replikaci svého současného úložiště identifikace do Azure Active Directory ke správě svého předplatného prostřednictvím ADMINISTRATE. Mezi klíčové předpoklady pro tento scénář patří situace, kdy má příslušný partner nebo poskytovatel služeb vytvořen vztah se zákazníkem a zákazník aktuálně spotřebovává služby poskytovatele nebo partner chce poskytovat služby založené výhradně na řešeních hostovaných v Azure bez nutnosti používat existující datové centrum nebo infrastrukturu poskytovatele.

![Diagram, který zobrazuje podrobný scénář pro model "připojení k".](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

Volby mezi těmito dvěma možnostmi jsou založené na potřebách vašeho zákazníka a vaší aktuální potřebě poskytování služeb Azure. Podrobnosti o těchto modelech a přidruženém řízení přístupu na základě role, sítích a vzorech návrhu identity jsou uvedeny na následujících odkazech:

* **Řízení přístupu na základě role v Azure (Azure RBAC)** – RBAC je založené na Azure Active Directory.  Další informace o službě Azure RBAC najdete [tady](../role-based-access-control/role-assignments-portal.md).
* **Sítě** – Obsahuje informace k různým tématům týkající se sítí v Microsoft Azure.
* **Azure Active Directory (Azure AD)** – Azure AD poskytuje správu identit pro Microsoft Azure a aplikace SaaS třetích stran. Další informace o Azure AD najdete [tady](https://azure.microsoft.com/documentation/services/active-directory/).  

## <a name="network-speeds"></a>Rychlost sítě
ExpressRoute podporuje rychlost sítě od 50 MB do 10 GB/s. Díky tomu mohou zákazníci zakoupit šířku pásma sítě potřebnou pro konkrétní jedinečné prostředí.

> [!NOTE]
> Šířku pásma sítě lze podle potřeby zvětšit bez přerušení komunikace, ovšem snížení rychlosti sítě vyžaduje zrušení okruhu a jeho opětné vytvoření s nižší rychlostí sítě.  
> 
> 

ExpressRoute podporuje připojení více virtuálních sítí k jednomu okruhu ExpressRoute, aby se vysokorychlostní připojení lépe využívala. Jeden okruh ExpressRoute může být sdílen více předplatnými Azure, která vlastní tentýž zákazník.

## <a name="configuring-expressroute"></a>Konfigurace ExpressRoute
ExpressRoute je možné konfigurovat tak, aby byly podporovány tři typy provozu ([domény směrování](#expressroute-routing-domains)) na jednom okruhu ExpressRoute. Tento provoz je rozdělen na partnerský vztah Microsoftu, veřejný partnerský vztah Azure a privátní partnerský vztah. Můžete zvolit odesílání provozu jednoho nebo všech typů prostřednictvím jednoho okruhu ExpressRoute nebo můžete použít více okruhů ExpressRoute v závislosti na velikosti okruhu ExpressRoute a na izolaci požadované zákazníkem. Stav zabezpečení zákazníka, nemusí umožňovat využívání stejného okruhu pro veřejný i privátní provoz.

### <a name="connect-through-model"></a>Model s nepřímým připojením
V konfiguraci pro připojení budete odpovědni za veškerou síťovou podporu pro propojení vašich prostředků Datacenter vašich zákazníků s předplatnými hostovanými v Azure. Každý z vašich zákazníků, který chce využívat možnosti Azure, bude potřebovat vlastní připojení ExpressRoute, které budete spravovat vy. Budete používat tytéž metody, které by pro zajištění okruhu ExpressRoute používal zákazník. Postupujte podle stejných kroků uvedených v článku [Pracovní postupy ExpressRoute](expressroute-workflows.md) ohledně zřizování okruhů a stavů okruhů. Pak nakonfigurujte trasy protokolu BGP (Border Gateway Protocol) tak, aby mohl být řízen provoz mezi místní sítí a virtuální sítí Azure.

### <a name="connect-to-model"></a>Model s přímým připojením
V případě konfigurace s přímým připojením již zákazník má vytvořeno připojení k Azure nebo vytvoří připojení k poskytovateli internetových služeb s cílem propojit ExpressRoute z vlastního datového centra zákazníka přímo s Azure, a nikoli s vaším datovým centrem. Zákazník zahájí proces zřizování podle kroků uvedených výše pro model s nepřímým připojením. Po vytvoření okruhu bude zákazník muset nakonfigurovat místní směrovače tak, aby měly přístup do vaší sítě i do virtuálních sítí Azure.

Můžete být nápomocni při nastavování připojení a konfiguraci tras tak, aby prostředky ve vašich datových centrech mohly komunikovat s prostředky klienta ve vašem datovém centru nebo s prostředky hostovanými v Azure.

## <a name="expressroute-routing-domains"></a>Domény směrování ExpressRoute
ExpressRoute nabízí tři domény směrování: veřejnou, privátní partnerský vztah Microsoftu. Každá z domén směrování je nakonfigurovaná se stejnými směrovači v konfiguraci aktivní-aktivní pro zajištění vysoké dostupnosti. Podrobnější informace o doménách směrování ExpressRoute najdete [tady](expressroute-circuit-peerings.md).

Můžete definovat vlastní filtry tras a povolit pouze trasy, které chcete povolit nebo které potřebujete. Další informace a postup při provádění těchto změn najdete v článku [Vytvoření a úprava směrování pro okruh ExpressRoute s použitím prostředí PowerShell](expressroute-howto-routing-classic.md), který obsahuje podrobnější informace o filtrech směrování.

> [!NOTE]
> Připojení s partnerským vztahem Microsoft nebo veřejným partnerským vztahem musí používat veřejnou IP adresu, kterou vlastní zákazník nebo poskytovatel CSP, a musí dodržovat veškerá definovaná pravidla. Další informace najdete na stránce [Požadavky služby ExpressRoute](expressroute-prerequisites.md).  
> 
> 

## <a name="routing"></a>Směrování
ExpressRoute se připojuje k sítím Azure prostřednictvím brány virtuální sítě Azure. Brány sítě poskytují směrování pro virtuální sítě Azure.

Při vytváření virtuálních sítí Azure se pro virtuální síť vytvoří také výchozí směrovací tabulka, podle které se směruje provoz do podsítí virtuální sítě a z nich. Pokud výchozí směrovací tabulka pro příslušné řešení nedostačuje, je možné vytvořit vlastní trasy pro směrování odchozího provozu do vlastních zařízení nebo blokovat trasy do konkrétních podsítí či externích sítí.

### <a name="default-routing"></a>Výchozí směrování
Výchozí směrovací tabulka obsahuje následující trasy:

* Směrování v rámci podsítě
* Mezi podsítěmi v rámci virtuální sítě
* Do internetu
* Mezi virtuálními sítěmi s použitím brány VPN
* Z virtuální sítě do místní sítě s použitím brány VPN nebo brány ExpressRoute

![Diagram, který zobrazuje výchozí možnosti směrování.](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>Směrování definované uživatelem (UDR)
Trasy definované uživatelem umožňují řízení odchozího provozu z přiřazené podsítě do jiných podsítí ve virtuální síti nebo prostřednictvím některé z ostatních předdefinovaných bran (ExpressRoute; internet nebo VPN). Výchozí systémovou tabulku směrování je možné směrovací tabulkou definovanou uživatelem. V takovém případě se výchozí směrovací tabulka nahradí vlastními trasami. V případě směrování definovaného uživatelem mohou zákazníci vytvářet konkrétní trasy do zařízení, jako jsou brány firewall nebo zařízení pro detekci narušení, či blokovat přístup ke konkrétním podsítím z podsítě, která je hostitelem trasy definované uživatelem. Přehled tras User-Defined najdete [tady](../virtual-network/virtual-networks-udr-overview.md). 

## <a name="security"></a>Zabezpečení
V závislosti na používaném modelu (přímé připojení nebo nepřímé připojení) zákazník definuje zásady zabezpečení ve své virtuální síti nebo předá požadavky na zásady zabezpečení poskytovateli CSP, aby je pro příslušné virtuální sítě definoval. Je možné definovat následující kritéria zabezpečení:

1. **Izolace zákazníka** – Platforma Azure poskytuje možnost izolace zákazníka tím, že ID zákazníka a informace o virtuální síti ukládá v zabezpečené databázi, která se používá k zapouzdření veškerého provozu zákazníka v tunelových propojeních GRE.
2. Pravidla **skupina zabezpečení sítě (NSG)** slouží k definování povoleného příchozího a odchozího provozu pro podsítě v rámci virtuálních sítí v Azure. Ve výchozím nastavení obsahuje NSG pravidla blokování pro blokování provozu z Internetu do virtuální sítě a povolení pravidel pro provoz v rámci virtuální sítě. Další informace o skupinách zabezpečení sítě najdete [tady](https://azure.microsoft.com/blog/network-security-groups/).
3. **Vynutit tunelové propojení** – Tato možnost slouží k tomu, aby byl provoz z Azure do internetu přesměrován prostřednictvím připojení ExpressRoute do místního datového centra. Další informace o vynuceném tunelovém propojení najdete [tady](expressroute-routing.md#advertising-default-routes).  
4. **Šifrování** – Přestože jsou okruhy ExpressRoute vyhrazeny pro konkrétního zákazníka, je možné, že dojde k prolomení zabezpečení u poskytovatele sítě a útočník pak bude moci zkoumat paketový provoz. Tuto možnost lze eliminovat tím, že zákazník nebo poskytovatel CSP bude provoz prostřednictvím připojení šifrovat definováním zásad režimu tunelového propojení protokolu IPSec pro veškerý provoz mezi místními prostředky a prostředky Azure (informace najdete výše v části týkající se volitelného režimu tunelového propojení s protokolem IPSec pro zákazníka 1 na obrázku 5: Zabezpečení ExpressRoute). Druhou možností je použití zařízení brány firewall v každém koncovém bodu okruhu ExpressRoute. To bude vyžadovat instalaci dalších virtuálních počítačů nebo zařízení brány firewall třetích stran na obou koncích pro šifrování provozu přes okruh ExpressRoute.

![alternativní text](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>Další kroky
Služba poskytovatele Cloud Solution Provider nabízí možnost zvýšení hodnoty pro zákazníky bez nutnosti nákupu nákladné infrastruktury a schopností a zachování vaší pozice primárního poskytovatele outsourcingu. Bezproblémovou integraci s Microsoft Azure je možné zajistit prostřednictvím rozhraní API CSP. Díky tomu lze správu Microsoft Azure integrovat s vašimi stávajícími strukturami správy.  

Další informace najdete prostřednictvím následujících odkazů:

[Program Azure in Cloud Solution Provider](https://docs.microsoft.com/azure/cloud-solution-provider).  
[Připravte se na jednání jako poskytovatel Cloud Solution Provider](https://partner.microsoft.com/solutions/cloud-reseller-pre-launch).  
[Prostředky pro poskytovatele Microsoft Cloud Solution Provider](https://partner.microsoft.com/solutions/cloud-reseller-resources).

---
title: Azure ExpressRoute pro poskytovatele Cloud Solution Provider | Dokumentace Microsoftu
description: Tento článek obsahuje informace pro poskytovatele cloudových služeb, kteří do svých nabídek chtějí začlenit služby Azure a ExpressRoute.
documentationcenter: na
services: expressroute
author: richcar
manager: carmonm
editor: ''
ms.assetid: f6c5f8ee-40ba-41a1-ae31-67669ca419a6
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: richcar
ms.openlocfilehash: 9991da5d5ab6f43cc8c5ebf1672d7da0f1ad061e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31405700"
---
# <a name="expressroute-for-cloud-solution-providers-csp"></a>Azure ExpressRoute pro poskytovatele Cloud Solution Provider
Společnost Microsoft poskytuje pro tradiční prodejce a distributory (poskytovatele Cloud Solution Provider) hyperškálovatelné služby, aby mohli pro vaše zákazníky rychle zřizovat nové služby a řešení bez nutnosti investovat do vývoje těchto nových služeb. Aby měl poskytovatel Cloud Solution Provider (CSP) možnosti spravovat tyto služby přímo, poskytuje společnost Microsoft programy a rozhraní API umožňující poskytovateli CSP spravovat prostředky Microsoft Azure za své zákazníky. Jeden z těchto prostředků je ExpressRoute. ExpressRoute umožňuje poskytovateli CSP připojovat prostředky zákazníků ke službám Azure. ExpressRoute je vysokorychlostní propojení se službami v Azure, které zajišťuje privátní komunikaci. 

ExpressRoute sestává z dvojice okruhů pro zajištění vysoké dostupnosti, které jsou připojeny k předplatným jednoho zákazníka a nemůže je sdílet více zákazníků. Každý okruh by měl být ukončen v různých směrovačích, aby se zajistila vysoká dostupnost.

> [!NOTE]
> V ExpressRoute se uplatňují omezení šířky pásma a počtu připojení, což znamená, že velké nebo složité implementace budou vyžadovat více okruhů ExpressRoute pro jednoho zákazníka.
> 
> 

Množství služeb Microsoft Azure, které můžete nabídnout zákazníkům, se stále zvyšuje. ExpressRoute pomáhá vám a vašim zákazníkům využívat výhody těchto služeb zajišťováním vysokorychlostního přístupu s nízkou latencí k prostředí Microsoft Azure.

## <a name="microsoft-azure-management"></a>Správa Microsoft Azure
Společnost Microsoft nabízí poskytovatelům CSP rozhraní API pro správu předplatných Azure zákazníků, čímž umožňuje programovou integraci s vlastními systémy správy služeb. Podporované možnosti správy najdete [tady](https://msdn.microsoft.com/library/partnercenter/dn974944.aspx).

## <a name="microsoft-azure-resource-management"></a>Správa prostředků Microsoft Azure
Způsob správy předplatného závisí na vaší smlouvě se zákazníkem. Poskytovatel CSP může přímo spravovat vytváření a správu prostředků, případně si může zákazník ponechat kontrolu nad předplatným Microsoft Azure a vytvářet prostředky Azure podle potřeby. Pokud zákazník spravuje vytváření prostředků v rámci svého předplatného Microsoft Azure, bude používat jeden ze dvou modelů: „*Connect-Through*“ (nepřímé připojení) nebo „*Direct-To*“ (přímé připojení). Tyto modely jsou podrobně popsány v následujících oddílech.  

### <a name="connect-through-model"></a>Model s nepřímým připojením
![alternativní text](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

V případě modelu s nepřímým připojením poskytovatel CSP vytvoří přímé připojení mezi vaším datovým centrem a předplatným Azure zákazníka. Přímé připojení se vytvoří pomocí ExpressRoute, které propojuje vaši síť s Azure. Pak se zákazník připojí k vaší síti. Tento scénář vyžaduje, aby zákazník při přístupu ke službám Azure procházel sítí poskytovatele CSP. 

Pokud má zákazník i jiná předplatná Azure, která nespravujete vy, může pro připojení ke službám zřizovaným v rámci předplatného mimo příslušného poskytovatele CSP využívat veřejný internet nebo vlastní privátní připojení. 

V případě poskytovatele CSP, který spravuje služby Azure, se předpokládá, že vytvořil úložiště identit zákazníka, které se poté replikuje do služby Azure Active Directory, v níž probíhá správa příslušného předplatného poskytovatele CSP prostřednictvím funkce AOBO (Administrate-On-Behalf-Of). Mezi klíčové předpoklady pro tento scénář patří situace, kdy má příslušný partner nebo poskytovatel služeb vytvořen vztah se zákazníkem a zákazník aktuálně spotřebovává služby poskytovatele nebo partner chce poskytovat kombinaci řešení hostovaných u poskytovatele a hostovaných v Azure s cílem zajistit flexibilitu a reagovat na výzvy zákazníka, které poskytovatel CSP sám nedokáže splnit. Tento model je znázorněn na následujícím **obrázku**.

![alternativní text](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Model s přímým připojením
![alternativní text](./media/expressroute-for-cloud-solution-providers/connect-to.png)

V případě modelu s přímým připojením vytvoří poskytovatel služby přímé připojení mezi datovým centrem zákazníka předplatným Azure, které je zřizováno poskytovatelem CSP, s použitím ExpressRoute prostřednictvím sítě zákazníka.

> [!NOTE]
> Pro ExpressRoute zákazník potřebuje vytvořit a spravovat okruh ExpressRoute.  
> 
> 

Tento scénář připojení vyžaduje, aby se zákazník připojil přímo prostřednictvím své sítě. Pak může využívat přístup k předplatnému Azure spravovanému poskytovatele CSP s použitím přímého síťového připojení, které je zcela nebo částečně vytvořeno, vlastněno a spravováno zákazníkem. U těchto zákazníků se předpokládá, že poskytovatel nemá vytvořeno úložiště identit zákazníka a že bude zákazníkovi nápomocen při replikaci jeho aktuálního úložiště identit do služby Azure Active Directory s cílem spravovat předplatná prostřednictvím funkce AOBO. Mezi klíčové předpoklady pro tento scénář patří situace, kdy má příslušný partner nebo poskytovatel služeb vytvořen vztah se zákazníkem a zákazník aktuálně spotřebovává služby poskytovatele nebo partner chce poskytovat služby založené výhradně na řešeních hostovaných v Azure bez nutnosti používat existující datové centrum nebo infrastrukturu poskytovatele.

![alternativní text](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

Volba mezi těmito dvěma možnostmi závisí na potřebách zákazníka a na vaší aktuální potřebě poskytování služeb Azure. Podrobnosti o těchto modelech a přidruženém řízení přístupu na základě role, sítích a vzorech návrhu identity jsou uvedeny na následujících odkazech:

* **Řízení přístupu na základě role (RBAC)** – Funkce RBAC je založena na službě Azure Active Directory.  Další informace o funkci Azure RBAC najdete [tady](../role-based-access-control/role-assignments-portal.md).
* **Sítě** – Obsahuje informace k různým tématům týkající se sítí v Microsoft Azure.
* **Azure Active Directory (Azure AD)** – Azure AD poskytuje správu identit pro Microsoft Azure a aplikace SaaS jiných dodavatelů. Další informace o Azure AD najdete [tady](https://azure.microsoft.com/documentation/services/active-directory/).  

## <a name="network-speeds"></a>Rychlost sítě
ExpressRoute podporuje rychlost sítě 50 Mb/s až 10 Gb/s. Díky tomu mohou zákazníci zakoupit šířku pásma sítě potřebnou pro konkrétní jedinečné prostředí.

> [!NOTE]
> Šířku pásma sítě lze podle potřeby zvětšit bez přerušení komunikace, ovšem snížení rychlosti sítě vyžaduje zrušení okruhu a jeho opětné vytvoření s nižší rychlostí sítě.  
> 
> 

ExpressRoute podporuje připojení více virtuálních sítí k jednomu okruhu ExpressRoute, aby se vysokorychlostní připojení lépe využívala. Jeden okruh ExpressRoute může být sdílen více předplatnými Azure, která vlastní tentýž zákazník.

## <a name="configuring-expressroute"></a>Konfigurace ExpressRoute
ExpressRoute je možné konfigurovat tak, aby byly podporovány tři typy provozu ([domény směrování](#ExpressRoute-routing-domains)) na jednom okruhu ExpressRoute. Tento provoz je rozdělen na partnerský vztah Microsoftu, veřejný partnerský vztah Azure a privátní partnerský vztah. Můžete zvolit odesílání provozu jednoho nebo všech typů prostřednictvím jednoho okruhu ExpressRoute nebo můžete použít více okruhů ExpressRoute v závislosti na velikosti okruhu ExpressRoute a na izolaci požadované zákazníkem. Stav zabezpečení zákazníka, nemusí umožňovat využívání stejného okruhu pro veřejný i privátní provoz.

### <a name="connect-through-model"></a>Model s nepřímým připojením
V případě konfigurace s nepřímým připojením budete odpovídat za podchycení veškerých aspektů sítí při připojování prostředků datových center zákazníků k předplatným hostovaným v Azure. Každý z vašich zákazníků, který chce využívat možnosti Azure, bude potřebovat vlastní připojení ExpressRoute, které budete spravovat vy. Budete používat tytéž metody, které by pro zajištění okruhu ExpressRoute používal zákazník. Postupujte podle stejných kroků uvedených v článku [Pracovní postupy ExpressRoute](expressroute-workflows.md) ohledně zřizování okruhů a stavů okruhů. Pak nakonfigurujte trasy protokolu BGP (Border Gateway Protocol) tak, aby mohl být řízen provoz mezi místní sítí a virtuální sítí Azure.

### <a name="connect-to-model"></a>Model s přímým připojením
V případě konfigurace s přímým připojením již zákazník má vytvořeno připojení k Azure nebo vytvoří připojení k poskytovateli internetových služeb s cílem propojit ExpressRoute z vlastního datového centra zákazníka přímo s Azure, a nikoli s vaším datovým centrem. Zákazník zahájí proces zřizování podle kroků uvedených výše pro model s nepřímým připojením. Po vytvoření okruhu bude zákazník muset nakonfigurovat místní směrovače tak, aby měly přístup do vaší sítě i do virtuálních sítí Azure.

Můžete být nápomocni při nastavování připojení a konfiguraci tras tak, aby prostředky ve vašich datových centrech mohly komunikovat s prostředky klienta ve vašem datovém centru nebo s prostředky hostovanými v Azure.

## <a name="expressroute-routing-domains"></a>Domény směrování ExpressRoute
ExpressRoute nabízí tři domény směrování: veřejnou, privátní partnerský vztah Microsoftu. Každá z domén směrování je konfigurována s použitím stejných směrovačů v rámci konfigurace aktivní-aktivní pro zajištění vysoké dostupnosti. Podrobnější informace o doménách směrování ExpressRoute najdete [tady](expressroute-circuit-peerings.md).

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

![alternativní text](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>Směrování definované uživatelem (UDR)
Trasy definované uživatelem umožňují řízení odchozího provozu z přiřazené podsítě do jiných podsítí ve virtuální síti nebo prostřednictvím některé z ostatních předdefinovaných bran (ExpressRoute; internet nebo VPN). Výchozí systémovou tabulku směrování je možné směrovací tabulkou definovanou uživatelem. V takovém případě se výchozí směrovací tabulka nahradí vlastními trasami. V případě směrování definovaného uživatelem mohou zákazníci vytvářet konkrétní trasy do zařízení, jako jsou brány firewall nebo zařízení pro detekci narušení, či blokovat přístup ke konkrétním podsítím z podsítě, která je hostitelem trasy definované uživatelem. Přehled tras definovaných uživatelem najdete [tady](../virtual-network/virtual-networks-udr-overview.md). 

## <a name="security"></a>Zabezpečení
V závislosti na používaném modelu (přímé připojení nebo nepřímé připojení) zákazník definuje zásady zabezpečení ve své virtuální síti nebo předá požadavky na zásady zabezpečení poskytovateli CSP, aby je pro příslušné virtuální sítě definoval. Je možné definovat následující kritéria zabezpečení:

1. **Izolace zákazníka** – Platforma Azure poskytuje možnost izolace zákazníka tím, že ID zákazníka a informace o virtuální síti ukládá v zabezpečené databázi, která se používá k zapouzdření veškerého provozu zákazníka v tunelových propojeních GRE.
2. Pravidla **skupina zabezpečení sítě (NSG)** slouží k definování povoleného příchozího a odchozího provozu pro podsítě v rámci virtuálních sítí v Azure. Ve výchozím nastavení skupiny zabezpečení sítě obsahují pravidla Blokovat pro blokování provozu z internetu do virtuální sítě a pravidla Povolit pro provoz v rámci virtuální sítě. Další informace o skupinách zabezpečení sítě najdete [tady](https://azure.microsoft.com/blog/network-security-groups/).
3. **Vynutit tunelové propojení** – Tato možnost slouží k tomu, aby byl provoz z Azure do internetu přesměrován prostřednictvím připojení ExpressRoute do místního datového centra. Další informace o vynuceném tunelovém propojení najdete [tady](expressroute-routing.md#advertising-default-routes).  
4. **Šifrování** – Přestože jsou okruhy ExpressRoute vyhrazeny pro konkrétního zákazníka, je možné, že dojde k prolomení zabezpečení u poskytovatele sítě a útočník pak bude moci zkoumat paketový provoz. Tuto možnost lze eliminovat tím, že zákazník nebo poskytovatel CSP bude provoz prostřednictvím připojení šifrovat definováním zásad režimu tunelového propojení protokolu IPSec pro veškerý provoz mezi místními prostředky a prostředky Azure (informace najdete výše v části týkající se volitelného režimu tunelového propojení s protokolem IPSec pro zákazníka 1 na obrázku 5: Zabezpečení ExpressRoute). Druhou možností je použití zařízení brány firewall v každém koncovém bodu okruhu ExpressRoute. Tento postup bude vyžadovat instalaci dalších virtuálních počítačů či zařízení brány firewall jiných dodavatelů na obou koncích, aby se mohl šifrovat provoz v rámci okruhu ExpressRoute.

![alternativní text](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>Další kroky
Služba poskytovatele Cloud Solution Provider nabízí možnost zvýšení hodnoty pro zákazníky bez nutnosti nákupu nákladné infrastruktury a schopností a zachování vaší pozice primárního poskytovatele outsourcingu. Bezproblémovou integraci s Microsoft Azure je možné zajistit prostřednictvím rozhraní API CSP. Díky tomu lze správu Microsoft Azure integrovat s vašimi stávajícími strukturami správy.  

Další informace najdete prostřednictvím následujících odkazů:

[Program Azure in Cloud Solution Provider](https://docs.microsoft.com/azure/cloud-solution-provider).  
[Připravte se na jednání jako poskytovatel Cloud Solution Provider](https://partner.microsoft.com/en-us/solutions/cloud-reseller-pre-launch).  
[Prostředky pro poskytovatele Microsoft Cloud Solution Provider](https://partner.microsoft.com/en-us/solutions/cloud-reseller-resources).

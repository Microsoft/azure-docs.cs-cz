---
title: 'Optimalizace směrování - okruhy ExpressRoute: Azure | Dokumentace Microsoftu'
description: Tato stránka obsahuje podrobné informace o tom, jak optimalizovat směrování, pokud máte více než jeden okruh ExpressRoute, který poskytuje připojení mezi Microsoftem a vaší podnikovou sítí.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 65c23b05cfcb623f8e2870df813f5516b3039d5c
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140927"
---
# <a name="optimize-expressroute-routing"></a>Optimalizace směrování ExpressRoute
Pokud máte víc okruhů ExpressRoute, máte více než jednu cestu, jak se připojit k Microsoftu. V důsledku toho může dojít k neoptimálnímu směrování, to znamená, že přenosy dat mezi vaší sítí a Microsoftem mohou použít delší cestu. Čím delší je síťová cesta, tím větší je latence. Latence má přímý vliv na výkon aplikací a činnost koncového uživatele. Tento článek popíše tento problém a vysvětlí možnosti optimalizace směrování pomocí standardních technologií směrování.

## <a name="suboptimal-routing-from-customer-to-microsoft"></a>Neoptimální směrování od zákazníka do Microsoftu
Podívejme se zblízka na problém směrování na příkladu. Představte si, že máte dvě pobočky v USA, jednu v Los Angeles a jednu v New Yorku. Vaše pobočky jsou připojené k síti WAN, což může být buď vaše páteřní síti, nebo virtuální privátní síť IP poskytovatele služeb. Máte dva okruhy ExpressRoute, jeden v oblasti USA – západ a druhý v oblasti USA – východ, které jsou také připojené k síti WAN. Zjevně máte dvě cesty, jak se připojit k síti Microsoftu. Nyní si představte, že máte nasazení Azure (například Azure App Service) v oblasti USA – západ i USA – východ. Vaším záměrem je připojit vaše uživatele z Los Angeles k Azure USA – západ a uživatele z New Yorku k Azure USA – východ, protože správce služby inzeruje, že uživatelé v každé pobočce přistupují k blízkým službám Azure, aby byla činnost optimální. Tento plán funguje dobře pro uživatele na východním pobřeží, ale bohužel ne pro uživatele na západním pobřeží. Příčina problému je následující. V každém okruhu ExpressRoute vám inzerujeme jak předponu v Azure USA – východ (23.100.0.0/16), tak i předponu v Azure USA – západ (13.100.0.0/16). Pokud nevíte, která předpona je ze které oblasti, nejste schopni s nimi zacházet odlišným způsobem. Vaše síť WAN si může myslet, že obě předpony jsou blíž oblasti USA – východ než USA – západ, a proto směruje uživatele z obou poboček přes okruh ExpressRoute v oblasti USA – východ. Díky tomu budete mít v pobočce v Los Angeles mnoho nespokojených uživatelů.

![Případ 1 ExpressRoute – Problém: Neoptimální směrování od zákazníka do Microsoftu](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>Řešení: Použití komunit protokolu BGP
Abyste optimalizovali směrování pro uživatele obou poboček, musíte vědět, která předpona je z oblasti Azure USA – západ a která z Azure USA – východ. Tyto informace kódujeme pomocí [hodnot komunity protokolu BGP](expressroute-routing.md). Přiřadili jsme každé oblasti Azure jedinečnou hodnotu komunity protokolu BGP, např. 12076:51004 pro USA – východ, 12076:51006 pro USA – západ. Teď, když už víte, které předpona je z které oblasti Azure, můžete nakonfigurovat, který okruh ExpressRoute se bude upřednostňovat. Vzhledem k tomu, že k výměně informací o směrování používáme protokol BGP, můžete použít k ovlivnění směrování hodnotu Local Preference protokolu BGP. V našem příkladu můžete přiřadit vyšší hodnotu Local Preference pro 13.100.0.0/16 v oblasti USA – západ než v oblasti USA – východ a obdobně vyšší hodnotu Local Preference pro 23.100.0.0/16 v oblasti USA – východ než USA – západ. Tato konfigurace zajistí, že když jsou k dispozici obě cesty do Microsoftu, uživatelé v Los Angeles použijí pro připojení k Azure USA – západ okruh ExpressRoute v oblasti USA – západ, zatímco uživatelé v New Yorku použijí pro připojení k Azure USA – východ okruh ExpressRoute v oblasti USA – východ. Směrování je optimalizované na obou stranách. 

![Případ 1 ExpressRoute – Řešení: Použití komunit protokolu BGP](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

> [!NOTE]
> Stejnou techniku, použití hodnoty Local Preference, můžete použít na směrování od zákazníka do virtuální sítě Azure. Neuvádíme hodnotu komunity protokolu BGP mezi předpony inzerované z Azure do vaší sítě. Ale vzhledem k tomu, že víte, které nasazení virtuální sítě je blízko které kanceláři, můžete odpovídajícím způsobem nakonfigurovat směrovače, aby preferovali jeden okruh ExpressRoute před jiným.
>
>

## <a name="suboptimal-routing-from-microsoft-to-customer"></a>Neoptimální směrování od Microsoftu k zákazníkovi
Zde je další příklad, kdy připojení z Microsoftu používá delší cestu pro přístup do vaší sítě. V tomto případě používáte místní servery Exchange a Exchange Online v [hybridním prostředí](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx). Vaše pobočky jsou připojené k síti WAN. Inzerujete předpony vašich místních serverů v obou vašich pobočkách Microsoftu prostřednictvím dvou okruhů ExpressRoute. Exchange Online bude inicializovat připojení k místním serverům v případech, jako je například přenesení poštovní schránky. Bohužel připojení k pobočce v Los Angeles se směruje na okruh ExpressRoute v oblasti USA – východ, aby se pak celý obsah přenášel zpátky na západní pobřeží. Příčina problému je podobná té předchozí. Bez jakékoli pomoci namůže síť Microsoftu zjistit, která předpona zákazníka je blíž oblasti USA – východ a která je blíž oblasti USA – západ. Tak se stane, že se pro pobočku v Los Angeles zvolí špatná cesta.

![Případ 2 ExpressRoute – Neoptimální směrování od Microsoftu k zákazníkovi](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>Řešení: Použití předřazení AS PATH
Existují dvě řešení problému. První z nich je, že budete jednoduše inzerovat vaši místní předponu pro pobočku v Los Angeles, 177.2.0.0/31, v okruhu ExpressRoute v oblasti USA – západ a místní předponu pro pobočku v New Yorku, 177.2.0.2/31, v okruhu ExpressRoute v oblasti USA – východ. Výsledkem je jenom jedna cesta pro Microsoft, jak se připojit k jednotlivým pobočkám. Nedochází k nejednoznačnosti a směrování je optimalizované. V tomto návrhu je potřeba se zamyslet nad strategií převzetí služeb při selhání. V případě, že cesta do Microsoftu prostřednictvím okruhu ExpressRoute se přeruší, budete potřebovat zajistit, že Exchange Online se může pořád připojit k vašim místním serverům. 

Druhým řešením je, že budete nadále inzerovat obě předpony v obou okruzích ExpressRoute a kromě toho nám dáte vědět, která předpona je blíž ke které z poboček. Protože podporujeme předřazení protokolu BGP AS PATH, můžete konfigurovat cestu AS PATH pro vaši předponu a ovlivnit směrování. V tomto příkladu lze prodloužit AS PATH pro 172.2.0.0/31 v oblasti USA – východ tak, abychom pro přenos dat určený pro tuto předponu preferovali okruh ExpressRoute v oblasti USA – západ (protože naše síť si bude myslet, že cesta k této předponě je přes západ kratší). Obdobně lze prodloužit AS PATH pro 172.2.0.2/31 v oblasti USA – západ, abychom preferovali okruh ExpressRoute v oblasti USA – východ. Směrování je optimalizované pro obě pobočky. Pokud v tomto návrhu jeden okruh ExpressRoute není funkční, Exchange Online se s vámi pořád může spojit prostřednictvím jiného okruhu ExpressRoute a vaší sítě WAN. 

> [!IMPORTANT]
> Odebereme soukromá čísla AS v AS PATH pro předpony přijaté v partnerském vztahu Microsoftu. Musíte připojit veřejná čísla AS do AS PATH, abyste ovlivnili směrování pro partnerský vztah Microsoftu.
> 
> 

![Případ 2 ExpressRoute – Řešení: Použití předřazení AS PATH](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!NOTE]
> Přestože jsou zde uvedené příklady určené pro partnerské vztahy Microsoftu a veřejné partnerské vztahy, stejné možnosti jsou podporovány i pro soukromé partnerské vztahy. Předřazení AS PATH navíc funguje v rámci jednoho okruhu ExpressRoute a tak ovlivňuje výběr primární a sekundární cesty.
> 
> 

## <a name="suboptimal-routing-between-virtual-networks"></a>Neoptimální směrování mezi virtuálními sítěmi
Pomocí ExpressRoute můžete povolit komunikaci mezi dvěma virtuálními sítěmi jejich propojením k okruhu ExpressRoute. Pokud je propojíte k více okruhům ExpressRoute, mezi virtuálními sítěmi může dojít k neoptimálnímu směrování. Zvažte následující příklad. Máte dva okruhy ExpressRoute, jeden v oblasti USA – západ a druhý v oblasti USA – východ. V každé oblasti máte dvě virtuální sítě. Webové servery jsou nasazené v jedné virtuální síti a aplikační servery v druhé. Kvůli redundanci propojíte dvě virtuální sítě v každé oblasti k místnímu okruhu ExpressRoute i ke vzdálenému okruhu ExpressRoute. Jak je vidět níže, z každé virtuální sítě existují dvě cesty do druhé virtuální sítě. Virtuální sítě nevědí, který okruh ExpressRoute je místní a který je vzdálený. V důsledku toho při směrování ECMP (Equal-Cost-Multi-Path) kvůli vyrovnávání zatížení mezi virtuálními sítěmi můžou některé toky přenosů použít delší cestu a být směrované na vzdálený okruh ExpressRoute.

![Případ 3 ExpressRoute – Neoptimální směrování mezi virtuálními sítěmi](./media/expressroute-optimize-routing/expressroute-case3-problem.png)

### <a name="solution-assign-a-high-weight-to-local-connection"></a>Řešení: Přiřazení vysoké váhy místnímu připojení
Řešení je jednoduché. Protože víte, kde se virtuální sítě a obvody nacházejí, můžete nám říct, kterou cestu má každá virtuální síť preferovat. Speciálně v tomto příkladu přiřaďte vyšší váhu místnímu připojení před vzdáleným připojením (viz [příklad konfigurace](expressroute-howto-linkvnet-arm.md#modify-a-virtual-network-connection)). Když virtuální síť obdrží předponu jiné virtuální sítě na více připojeních, dá při odeslání provozu určeného pro tuto předponu přednost připojení v nejvyšší váhou.

![Případ 3 ExpressRoute – Řešení: Přiřazení vysoké váhy místnímu připojení](./media/expressroute-optimize-routing/expressroute-case3-solution.png)

> [!NOTE]
> Pokud máte víc okruhů ExpressRoute, můžete také ovlivnit směrování z virtuální sítě do místní sítě tím, že nakonfigurujete váhy připojení místo použití techniky předřazení AS PATH, která je popsaná v druhém scénáři výše. Pro každou předponu se při rozhodování o způsobu odeslání provozu vždy podíváme na váhy připojení dřív, než na délku cesty AS PATH.
>
>

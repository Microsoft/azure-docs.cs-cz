---
title: Koncepty – udržitelné softwarové inženýry ve službě Azure Kubernetes Services (AKS)
description: Přečtěte si o udržitelném vývojovém softwaru ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 2457de7bdaa94a6e2269515fafe6689d44960625
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984978"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Udržitelné principy softwarového inženýrství ve službě Azure Kubernetes (AKS)

Zásady udržitelného nastavování softwaru představují sadu kompetencí, které vám pomůžou definovat, sestavovat a spouštět udržitelné aplikace. Celkovým cílem je omezit nároky na uhlíku všech aspektů aplikace. V článku [Principy. zelený projekt][principles-green] je přehled zásad udržitelného softwarového inženýrství.

Je důležité si uvědomit o udržitelném vývojovém softwaru, protože se jedná o posun v prioritách a zaměřuje se na něj. V mnoha případech je software navržený a spuštěný způsobem, který se zaměřuje na vysoký výkon a nízkou latenci. Udržitelný vývoj softwaru se zaměřuje na snížení co nejvíc emisí uhlíku. V některých případech může použití udržitelného principu softwarového inženýrství poskytovat rychlejší výkon nebo nižší latenci, například snížením celkového síťového provozu. V ostatních případech může snížení emisí uhlíku způsobit pomalejší výkon nebo vyšší latenci, například zpoždění úloh s nízkou prioritou. Než se rozhodnete, že se na vaši aplikaci budou uplatňovat udržitelné zásady softwarového inženýrství, Projděte si priority, potřeby a kompromisy vaší aplikace.

## <a name="measure-and-optimize"></a>Měření a optimalizace

Pokud chcete snížit nároky na uhlíku svých clusterů AKS, potřebujete pochopit, jak se používají prostředky clusteru. [Azure monitor][azure-monitor] poskytuje podrobné informace o využití prostředků clusteru, jako je například paměť a využití procesoru. Tato data vám můžou pomoct informovat vaše rozhodnutí o tom, aby se snížilo nároky na uhlíku clusteru a aby se projevily změny. Můžete také nainstalovat [kalkulačku pro udržitelnost Microsoftu][sustainability-calculator] , abyste viděli nároky na všechny vaše prostředky Azure.

## <a name="increase-resource-utilization"></a>Zvýšení využití prostředků

Jedním z možností, jak snížit nároky na uhlík, je snížit množství času nečinnosti pro výpočetní prostředky. Omezení doby nečinnosti zahrnuje zvýšení využití výpočetních prostředků. Pokud jste například měli čtyři uzly v clusteru, každý spuštěný v 50% kapacitě, všechny čtyři uzly mají 50% nevyužité kapacity nečinné. Pokud jste cluster snížili na tři uzly, pak stejné zatížení způsobí, že vaše tři uzly budou běžet na 67% kapacitě, čímž se sníží nevyužitá kapacita na 33% na každém uzlu a zvýší se vaše využití.

> [!IMPORTANT]
> Při zvažování provádění změn prostředků v clusteru ověřte, že vaše [fondy systému][system-pools] mají dostatek prostředků, aby bylo možné udržet stabilitu základních systémových součástí clusteru. Nikdy nezmenšujte prostředky clusteru do bodu, ve kterém se cluster může stát nestabilním.

Po kontrole využití clusteru zvažte použití funkcí nabízených [více fondy uzlů][multiple-node-pools]. [Velikost uzlů][node-sizing] můžete použít k definování fondů uzlů s konkrétními profily procesoru a paměti, což vám umožní přizpůsobit své uzly potřebám vašich úloh. Přizpůsobení velikosti uzlů vašim potřebám vám může pomáhat při spouštění několika uzlů při vyšším využití. Můžete taky nakonfigurovat, jak se cluster [škáluje][scale] , a použít [horizontální automatické škálování pod automatickým škálováním][scale-horizontal] a automatické škálování [clusteru][scale-auto] a automaticky škálovat cluster na základě vaší konfigurace. Řízení škály clusterů vám umožní udržovat všechny uzly běžící s vysokým využitím a přitom udržovat změny v zatížení vašeho clusteru. V případech, kdy je zatížení odolné vůči náhlým přerušením nebo ukončením, můžete použít [fondy bodů][spot-pools] a využít tak výhod nečinné kapacity v Azure. Například fondy přímých přenosů můžou fungovat dobře pro dávkové úlohy nebo vývojové prostředí.

Zvýšení využití může také snížit nadbytečné uzly, což snižuje spotřebu energie využívané [rezervacemi prostředků na jednotlivých uzlech][resource-reservations].

Přečtěte si také *požadavky* na procesor a paměť a *Omezení* v manifestech Kubernetes vašich aplikací. Když tyto hodnoty snížíte pro paměť a procesor, k dispozici je více paměti a CPU pro spouštění dalších úloh. Když spouštíte více úloh s nižšími nároky na procesor a paměť, váš cluster se bude trochu přidělovat, což zvyšuje vaše využití. Když snižujete kapacitu procesoru a paměti pro vaše aplikace, může se chování vašich aplikací snížit nebo nestabilní, pokud nastavíte tyto hodnoty příliš nízké. Před změnou *požadavků* a *omezení*procesoru a paměti zvažte spuštění některých testů srovnávacích testů, abyste zjistili, jestli jsou tyto hodnoty správně nastavené. Kromě toho nikdy nezmenšujte tyto hodnoty do bodu, kdy se vaše aplikace stane nestabilní.

## <a name="reduce-network-travel"></a>Snížení síťové cesty

Omezením požadavků na vzdálenost a odpovědí do clusteru a z nich je potřeba cestovat obvykle ke snížení spotřeby elektřiny síťovými zařízeními a snížení emisí z uhlíku. Po kontrole provozu v síti zvažte vytvoření clusterů [v oblastech][regions] blíže ke zdroji síťového provozu. [Azure Traffic Manager][azure-traffic-manager] můžete použít také k usnadnění směrování provozu do nejbližšího clusteru a [skupin umístění blízkosti][proiximity-placement-groups] , které vám pomůžou snížit vzdálenost mezi prostředky Azure.

> [!IMPORTANT]
> Při zvažování provádění změn v sítích vašeho clusteru nikdy nesnižte síťové zatížení za cenu požadavků na úlohy. Například použití [zón dostupnosti][availability-zones] způsobí větší síťové cestování v clusteru, ale použití této funkce může být nezbytné pro zpracování požadavků na úlohy.

## <a name="demand-shaping"></a>Tvarování poptávky

Pokud je to možné, zvažte možnost posunout poptávku na prostředky clusteru na časy nebo oblasti, kde můžete využít nadbytečnou kapacitu. Zvažte například změnu času nebo oblasti, která má dávková úloha spustit nebo použít [fondy bodových barev][spot-pools]. Zvažte také refaktoring vaší aplikace, aby používala frontu k odložení spuštěné úlohy, které nepotřebují okamžité zpracování.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o funkcích AKS uvedených v tomto článku:

* [Více fondů uzlů][multiple-node-pools]
* [Velikost uzlu][node-sizing]
* [Škálování clusteru][scale]
* [Horizontální automatické škálování podů][scale-horizontal]
* [Automatické škálování clusteru][scale-auto]
* [Fondy přímých barev][spot-pools]
* [Systémové fondy][system-pools]
* [Rezervace prostředků][resource-reservations]
* [Skupiny umístění bezkontaktní komunikace][proiximity-placement-groups]
* [Zóny dostupnosti][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/insights/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principles-green]: https://principles.green/
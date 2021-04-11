---
title: Koncepty – udržitelné softwarové inženýry ve službě Azure Kubernetes Services (AKS)
description: Přečtěte si o udržitelném vývojovém softwaru ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/29/2021
ms.openlocfilehash: c43c65dfa2f3930510bd59aaa24c798525bd691b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011487"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>Udržitelné principy softwarového inženýrství ve službě Azure Kubernetes (AKS)

Zásady udržitelného nastavování softwaru představují sadu kompetencí, které vám pomůžou definovat, sestavovat a spouštět udržitelné aplikace. Celkovým cílem je omezit nároky na uhlíku všech aspektů aplikace. [Zásady udržitelného řízení softwaru][principles-sse] mají přehled o zásadách udržitelného softwarového inženýrství.

Udržitelný Software Engineering je posun v prioritách a zaměřuje se na něj. V mnoha případech se jedná o způsob, jakým je nejdůležitější software navržený, a je spuštěný vysoký výkon a nízká latence. Mezitím se trvale udržitelné softwarové technologie zaměřuje na snížení co nejvíc emisí uhlíku. Rozmyslete si:

* Použití trvalých principů softwarového inženýrství vám může poskytnout rychlejší výkon nebo nižší latenci, například snížením celkového síťového provozu. 
* Snížení emisí uhlíku může způsobit pomalejší výkon nebo vyšší latenci, například zpoždění úloh s nízkou prioritou. 

Než začnete používat udržitelné zásady softwarového inženýrství pro vaši aplikaci, Projděte si priority, potřeby a kompromisy vaší aplikace.

## <a name="measure-and-optimize"></a>Měření a optimalizace

Pokud chcete snížit nároky na uhlíku svých clusterů AKS, potřebujete pochopit, jak se používají prostředky clusteru. [Azure monitor][azure-monitor] poskytuje podrobné informace o využití prostředků clusteru, jako je například paměť a využití procesoru. Tato data informují vaše rozhodnutí o tom, aby se snížilo množství uhlíku vašeho clusteru a sledovalo účinek vašich změn. 

Můžete také nainstalovat [kalkulačku pro udržitelnost Microsoftu][sustainability-calculator] , abyste viděli nároky na všechny vaše prostředky Azure.

## <a name="increase-resource-utilization"></a>Zvýšení využití prostředků

Jedním ze způsobů, jak snížit nároky na uhlík, je zkrátit dobu nečinnosti. Omezení doby nečinnosti zahrnuje zvýšení využití výpočetních prostředků. Například:
1. V clusteru jste měli čtyři uzly, z nichž každý běží v 50% kapacitě. To znamená, že všechny čtyři uzly mají 50% nevyužité kapacity zbývající nečinné. 
1. Cluster jste snížili na tři uzly, každý spuštěný v 67% kapacitě se stejnou úlohou. Úspěšně jste snížili nevyužitou kapacitu na 33% na každém uzlu a zvýšili vaše využití.

> [!IMPORTANT]
> Když zvažujete změnu prostředků v clusteru, ověřte, že [fondy systému][system-pools] mají dostatek prostředků, aby se zajistila stabilita základních komponent systému vašeho clusteru. **Nikdy** nezmenšujte prostředky clusteru do bodu, ve kterém se cluster může stát nestabilním.

Po kontrole využití clusteru zvažte použití funkcí nabízených [více fondy uzlů][multiple-node-pools]: 

* Velikost uzlu

    Použijte [změnu velikosti uzlů][node-sizing] k definování fondů uzlů s konkrétními profily procesoru a paměti, což vám umožní přizpůsobit své uzly potřebám vašich úloh. Změnou velikosti uzlů na potřeby úloh můžete spustit několik uzlů s vyšším využitím. 

* Škálování clusteru

    Nakonfigurujte, jak se cluster [škáluje][scale]. Pomocí automatického [horizontálního][scale-horizontal] navýšení a automatického škálování [clusteru][scale-auto] můžete automaticky škálovat svůj cluster na základě konfigurace. Můžete určit, jak se cluster škáluje, aby se všechny uzly používaly při vysokém využití a zároveň zůstaly synchronizované se změnami v zatížení vašeho clusteru. 

* Fondy přímých barev

    V případech, kdy je zatížení odolné vůči náhlým přerušením nebo ukončením, můžete použít [fondy bodů][spot-pools]. Fondy bodových barev využívají v Azure kapacitu nečinné kapacity. Například fondy přímých přenosů můžou fungovat dobře pro dávkové úlohy nebo vývojové prostředí.

> [!NOTE]
>Zvýšení využití může také snížit nadbytečné uzly, což snižuje spotřebu energie využívané [rezervacemi prostředků na jednotlivých uzlech][resource-reservations].

Nakonec zkontrolujte *požadavky* na procesor a paměť a *Omezení* v manifestech Kubernetes vašich aplikací. 
* Po snížení velikosti paměti a procesoru jsou clusterům k dispozici více paměti a CPU pro spouštění dalších úloh. 
* Když spouštíte více úloh s nižšími nároky na procesor a paměť, váš cluster se bude trochu přidělovat, což zvyšuje vaše využití. 

Při snižování kapacity procesoru a paměti pro vaše aplikace se chování vašich aplikací může snížit nebo nestabilní, pokud nastavíte hodnoty CPU a paměti příliš nízké. Před změnou *požadavků* a *omezení* procesoru a paměti Spusťte některé testy srovnávacích testů a ověřte, zda jsou hodnoty správně nastaveny. Tyto hodnoty nikdy nesnižte na bod nestability aplikace.

## <a name="reduce-network-travel"></a>Snížení síťové cesty

Omezením požadavků a odpovědí na cestách a z vašeho clusteru můžete snížit emise uhlíku a spotřebu elektřiny pomocí síťových zařízení. Po kontrole provozu v síti zvažte vytvoření clusterů [v oblastech][regions] blíže ke zdroji síťového provozu. [Azure Traffic Manager][azure-traffic-manager] můžete použít ke směrování provozu do nejbližšího clusteru a [skupin umístění blízkosti][proiximity-placement-groups] a ke snížení vzdálenosti mezi prostředky Azure.

> [!IMPORTANT]
> Při zvažování provádění změn v sítích vašeho clusteru nikdy nesnižte síťové zatížení za cenu požadavků na úlohy. Například při použití [zón dostupnosti][availability-zones] dojde v clusteru k většímu zatížení sítě a zóny dostupnosti mohou být nezbytné pro zpracování požadavků na úlohy.

## <a name="demand-shaping"></a>Tvarování poptávky

Pokud je to možné, zvažte možnost posunout poptávku na prostředky clusteru na časy nebo oblasti, kde můžete využít nadbytečnou kapacitu. Zvažte například:
* Změna času nebo oblasti, kterou má úloha dávky běžet.
* Používání [fondů bodů][spot-pools]. 
* Refaktoring aplikace pro použití fronty k odložení spuštěné úlohy, které nepotřebují okamžité zpracování.

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
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md
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
[principles-sse]: https://docs.microsoft.com/learn/modules/sustainable-software-engineering-overview/
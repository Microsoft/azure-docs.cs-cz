---
title: Představení správce prostředků clusteru Service Fabric | Microsoft Docs
description: Úvod do Service Fabric clusteru správce prostředků.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: f25a422385abfcdb7020eb7477c0ae2ee55cd8fb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210592"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Představení správce prostředků clusteru Service Fabric
Tradičně Správa systémy IT nebo online služby určené vyhradit konkrétní fyzické nebo virtuální počítače pro tyto konkrétní služby nebo systémy. Služby byly navržen jako vrstev. By "web" vrstvu a vrstvu "data" nebo "úložiště". Aplikace by měla mít zasílání zpráv úrovně, kde plynoucích požadavky a odhlašování, a také sada počítačů vyhrazený pro ukládání do mezipaměti. Jednotlivé typy úlohy nebo vrstvě měl konkrétních počítačů, které jsou vyhrazené pro jeho: databáze získali vyhrazené, webové servery a několika několik počítačů. Pokud konkrétní typ zatížení způsobená na počítače, které bylo na spuštění příliš aktivní, pak jste přidali další počítače pomocí této stejnou konfiguraci do této vrstvy. Ale ne všechny úlohy může tak snadno škálovat – zejména s datovou vrstvu obvykle nahradíte počítače, které mají větší počítače. Snadné. Pokud na počítači se nezdařilo, spustili část celkového aplikace na nižší kapacitu, dokud tento počítač může obnovit. Stále poměrně snadno (Pokud je to nutně zábavné).

Teď ale na světě architektury služby a software se změnila. Je dnes běžné, že aplikace přijaly návrh Škálováním na více systémů. Vytváření aplikací s kontejnery nebo mikroslužeb (nebo obě) je běžné. Teď když stále může mít pouze několik počítačů, neběží jenom jednu instanci zatížení. Budou pravděpodobně i spuštěna několik různých úloh ve stejnou dobu. Nyní máte desítek různých typů služeb (none využívání úplné počítač za prostředky), případně stovky různých instancí těchto služeb. Každé pojmenované instance má jeden nebo více instancí nebo replik pro vysokou dostupnost (HA). V závislosti na velikosti těchto zatížení, a jak jsou zaneprázdněny může najít sami se stovkami nebo tisíci počítačů. 

Náhle Správa prostředí není tak jednoduché, správu několika počítačů, které jsou vyhrazené pro jednotlivé typy úloh. Vaše servery jsou virtuální a už nebude mít názvy (Přepnuli jste mindsets z [mazlíčků k zvířat](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) po všech). Konfigurace je menší o počítačích a informace o službách, sami. Hardware, který je vyhrazen pro jednu instanci zatížení je do značné míry věcí minulosti. Služby, sami staly malé distribuovaných systémů, které jsou rozmístěny v několika menší části komoditním hardwaru.

Vzhledem k tomu, že vaše aplikace není nadále řadu monoliths rozloženy několik vrstev, nyní máte mnoho více kombinací řešení. Kdo rozhodne, co můžete spustit typy úloh, na které hardwaru nebo kolik? Jaké úlohy dobré fungování i na stejném hardwaru, a který konfliktu? Kdy je počítač přestane dolů jak víte, co byla spuštěna existuje na tomto počítači? Kdo má na starosti a ujistěte se, že úlohy spustí znovu spustit? Počkejte (virtuální)? počítače opět online nebo úlohy automaticky převzetí služeb při selhání na jiné počítače a zachovat systémem? Je potřeba lidského zásahu? Co upgrady pro toto prostředí?

Jako vývojáři a operátory týkajících se v tomto prostředí vytvoříme má pomoci se správou této složitost. Náborové binge a při pokusu o překonávají složitost s uživateli, je pravděpodobně není pravé odpovědi, takže co můžeme udělat?

## <a name="introducing-orchestrators"></a>Představení orchestrators
"Orchestrator" je obecný termín pro softwarového produktu, který pomáhá správcům spravovat tyto typy prostředí. Orchestrators jsou komponenty, které trvat v žádostech o jako "Nastavit jako pět kopií této služby v mé prostředí." Se pokusí provést v prostředí shodovat s požadovaným stavem, bez ohledu na to, co se stane.

Orchestrators (ne člověka) jsou co provést akci při selhání na počítači nebo zatížení ukončí neočekávané z nějakého důvodu. Většina orchestrators více než jen nakládat s chybou. Další funkce, které spravujete nová nasazení, upgrady zpracování a týkající se spotřeby prostředků a zásad správného řízení. Všechny orchestrators jsou zásadně o údržbu některé požadovaný stav konfigurace v prostředí. Chcete upozornit orchestrator mají a jej provést lifting náročné. Všechny příklady orchestrators jsou programu Aurora nad Mesos, Docker Datacenter nebo Docker Swarm, Kubernetes a Service Fabric. Tyto orchestrators jsou se aktivně vyvinuté tak, aby vyhovovaly skutečné úlohy v produkčním prostředí. 

## <a name="orchestration-as-a-service"></a>Orchestration jako služby
Správce prostředků clusteru je součástí systému, který zpracovává orchestration v Service Fabric. Úlohy správce prostředků clusteru je rozdělit do tří částí:

1. Vynucení pravidla
2. Optimalizace prostředí
3. Pomoc s jinými procesy

Jak funguje správce prostředků clusteru najdete v následujícím videu Microsoft Virtual Academy: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=d4tka66yC_5706218965">
<img src="./media/service-fabric-cluster-resource-manager-introduction/ConceptsAndDemoVid.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="what-it-isnt"></a>Co není
V aplikacích vrstvy tradiční N, je vždy [nástroj pro vyrovnávání zatížení](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Obvykle se to nástroj pro vyrovnávání zatížení sítě (NLB) nebo aplikaci zatížení vyrovnávání (ALB) v závislosti na tom, kde ji byla ve sadu síťových protokolů. Některé nástroje pro vyrovnávání zatížení jsou založené na hardwaru jako je F5 Big-IP nabídky, jiné jsou na základě softwaru jako je Microsoft je služba Vyrovnávání zatížení sítě. V jiných prostředích může se zobrazit něco jako HAProxy, nginx, Istio nebo Envoy v této roli. V případě architektur se tyto úlohy služby Vyrovnávání zatížení je zajistit Bezstavová zatížení přijímat (přibližně) stejné množství práce. Strategie pro vyrovnávání zatížení rozmanitých. Některé nástroje pro vyrovnávání byste odesílali každý jiný volání na jiný server. Ostatní zadat Připnutí/dlouhodobost relace. Pokročilejší vyrovnávání používat ke směrování na základě jejího očekávané náklady a aktuálního zatížení počítače volání vlastní operaci načtení odhad nebo generování sestav.

Nástroje pro vyrovnávání sítě nebo zprávy směrovače se pokusila o Ujistěte se, že zůstal zhruba vyrovnáváním vrstvě web nebo worker. Strategie pro datové vrstvy vyrovnávání měla jiné a závislé na mechanizmus pro ukládání dat. Datová vrstva vyrovnávání účinné horizontálního dělení dat, ukládání do mezipaměti, spravované zobrazení, uložené procedury a další mechanismy daného obchodu.

Některé z těchto strategií jsou zajímavé, správce prostředků clusteru Service Fabric není nic jako nástroj pro vyrovnávání zatížení sítě nebo mezipaměti. Nástroj pro vyrovnávání zatížení sítě vyrovnává frontends tak, že se provoz mezi frontends. Správce prostředků clusteru Service Fabric má jinou strategii. Zásadně, Service Fabric přesune *služby* kde udělat nejvhodnější, byla očekávána provoz, nebo podle zatížení. Například je může přesunout služby do uzlů, které jsou aktuálně studenou, protože nejsou služby, které jsou to množství práce. Uzly může být studené vzhledem k tomu, že byly odstraněny nebo přesunout jinam služby, které nebyly nalezeny. Například může správce prostředků clusteru služby od počítače s také přesunout. Možná se tento počítač je budou upgradovány, nebo je z důvodu Špička při spotřebě přetížené se služby spuštěné na něm. Alernatively, může mít vyšší požadavky na služby prostředků. V důsledku nejsou k dispozici dostatečné prostředky na tomto počítači spusťte jej. 

Vzhledem k tomu, že správce prostředků clusteru je zodpovědná za přesunutí služby kolem, obsahuje sadu různé funkce ve srovnání s by najít ve službě Vyrovnávání zatížení sítě. Toto je nástroje pro vyrovnávání zatížení sítě přinášejí síťové přenosy k umístění služby již, i když toto umístění není ideální pro spouštění samotnou službu. Správce prostředků clusteru Service Fabric aktivuje se podstatně liší strategie pro zajištění, že jsou efektivně využité prostředky v clusteru.

## <a name="next-steps"></a>Další postup
- Informace o architektuře a informačního toku v rámci správce prostředků clusteru, podívejte se na [v tomto článku ](service-fabric-cluster-resource-manager-architecture.md)
- Správce prostředků clusteru má mnoho možností pro popis clusteru. Chcete-li získat další informace o metriky, projděte si tento článek na [popisující cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- Další informace o konfiguraci služby [Další informace o konfiguraci služby](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Metriky se, jak správce prostředků služby Fabric clusteru spravuje využívání a kapacity v clusteru. Další informace o metriky a způsob jejich konfigurace rezervaci [v tomto článku](service-fabric-cluster-resource-manager-metrics.md)
- Správce prostředků clusteru pracuje s možností správy Service Fabric. Chcete-li získat další informace o této integrace, přečtěte si [v tomto článku](service-fabric-cluster-resource-manager-management-integration.md)
- Chcete-li zjistit, o tom, jak správce prostředků clusteru spravuje a vyrovnává zatížení v clusteru, podívejte se na článek na [Vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md)

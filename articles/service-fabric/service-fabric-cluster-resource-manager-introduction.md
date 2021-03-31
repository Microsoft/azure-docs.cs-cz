---
title: Představujeme Cluster Service Fabric Správce prostředků
description: Přečtěte si o Správce prostředků Service Fabric clusteru, způsobech správy orchestrace služeb vaší aplikace.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: da9205f5d95eaf1b4dc655ee727ab8a4fe90893d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "75563322"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Představení služby Service Fabric cluster Resource Manager
Tradičně spravující IT systémy nebo online služby znamenaly vyhradit konkrétní fyzické nebo virtuální počítače pro tyto konkrétní služby nebo systémy. Služby byly navrženy jako vrstvy. Měla by existovat "Webová" vrstva a vrstva "data" nebo "úložiště". Aplikace by měly vrstvu pro zasílání zpráv, kde jsou požadavky navrstveny, a také sada počítačů vyhrazených pro ukládání do mezipaměti. Pro každou vrstvu nebo typ úlohy jsou vyhrazené konkrétní počítače: databáze má k ní vyhrazené několik počítačů, přičemž webové servery jsou několik. Pokud konkrétní typ úlohy způsobil, že počítače, na kterých bylo spuštěno, běželo příliš horko, Přidali jste do této vrstvy více počítačů se stejnou konfigurací. Avšak ne všechny úlohy je možné škálovat tak snadno – zejména s datovou vrstvou, která by obvykle nahradila počítače většími počítači. Snadné. Pokud počítač selhal, Tato část celkové aplikace běžela s nižší kapacitou, dokud se počítač nepovedlo obnovit. Pořád je poměrně snadné (pokud to není nutně zábavné).

Nyní se však změnila architektura služeb a softwaru. Je běžnější, že aplikace přijaly návrh škálování na více instancí. Sestavování aplikací pomocí kontejnerů nebo mikroslužeb (nebo obou) je běžné. Teď pořád máte jenom několik počítačů, ale neběží jenom jedna instance úlohy. Mohou dokonce současně běžet více různých úloh. Nyní máte spoustu různých typů služeb (žádné využívání prostředků v plném počítači), možná stovky různých instancí těchto služeb. Každá pojmenovaná instance má jednu nebo více instancí nebo replik pro vysokou dostupnost (HA). V závislosti na velikosti těchto úloh a způsobu jejich vytížení se můžete setkat se stovkami nebo tisíci počítačů. 

Náhlé řízení prostředí není tak jednoduché jako správa několika počítačů, které jsou vyhrazeny pro jednotlivé typy úloh. Vaše servery jsou virtuální a už neobsahují (Přepnuli jste mindsets z [domácích na skot](https://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) po všech). Konfigurace je méně o počítačích a další informace o samotných službách. Hardware vyhrazený pro jednu instanci úlohy je z velké části věcí v minulosti. Samotné služby se staly malými distribuovanými systémy, které zahrnují více menších druhů komoditních hardwaru.

Vzhledem k tomu, že vaše aplikace už není řadou monolitů, která je rozdělená mezi několik vrstev, máte teď spoustu dalších kombinací, se kterými se můžete vypořádat. Kdo se rozhodne, jaké typy úloh se můžou spouštět, na kterých hardwaru nebo kolik jich se dá dělat? Které úlohy fungují na stejném hardwaru dobře a které jsou v konfliktu? Když počítač přestane fungovat, jak zjistíte, co bylo na tomto počítači spuštěno? Kdo má za následek, že se úloha spouští znovu? Čekáte se, až se počítač (Virtual?) vrátí zpátky nebo když se vaše úlohy automaticky převezmou na jiné počítače a budou fungovat i nadále? Je vyžadován lidský zásah? Jaké jsou inovace v tomto prostředí?

V případě vývojářů a operátorů, které se týkají tohoto prostředí, chceme pomáhat se správou této složitosti. Najímání Bingu a pokus o skrytí složitosti s lidmi se pravděpodobně nejedná o správnou odpověď, takže co máme?

## <a name="introducing-orchestrators"></a>Představujeme orchestrace
"Orchestrator" je obecný termín pro softwarový software, který správcům pomáhá tyto typy prostředí spravovat. Orchestrace jsou komponenty, které se v žádostech přebírají jako "chtěl bych pět kopií této služby spuštěných v mém prostředí". Snaží se, aby se prostředí shodovalo s požadovaným stavem, bez ohledu na to, co se stane.

Orchestrace (ne lidé) jsou to, co dělat v případě, že dojde k chybě počítače nebo ukončení úlohy z nějakého neočekávaného důvodu. Většina orchestrací vychází jenom při selhání. Další funkce, které spravují nová nasazení, zpracovávají upgrady a řeší spotřebu prostředků a zásady správného řízení. Všechny orchestrace jsou zásadní pro udržení některých požadovaných stavů konfigurace v prostředí. Chcete mít přehled o tom, co chcete, aby produkt Orchestrator byl náročný a měl by se jednat o těžké zvedání. Aurora na Mesos, Docker datacentra/Docker Swarm, Kubernetes a Service Fabric jsou všechny příklady Orchestration. Tyto orchestrace se aktivně vyvinuly tak, aby splňovaly potřeby reálných úloh v produkčních prostředích. 

## <a name="orchestration-as-a-service"></a>Orchestrace jako služba
Cluster Správce prostředků je systémová součást, která zpracovává orchestraci v Service Fabric. Úloha Správce prostředků clusteru je rozdělená na tři části:

1. Vynucování pravidel
2. Optimalizace prostředí
3. Pomoc s ostatními procesy

### <a name="what-it-isnt"></a>Co to není
V tradičních vícevrstvých aplikacích je vždy [Load Balancer](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Obvykle se jedná o Load Balancer sítě (NLB) nebo aplikace Load Balancer (ALB) v závislosti na tom, kde se v zásobníku sítě nacházela. Některé nástroje pro vyrovnávání zatížení jsou založené na hardwaru, jako je například nabídka F5's Big-IP, jiné jsou softwarové, jako je například vyrovnávání zatížení společnosti Microsoft. V jiných prostředích se v této roli může zobrazit něco jako HAProxy, Nginx, Istio nebo zástupné. V těchto architekturách je úlohou vyrovnávání zatížení, aby nestavové úlohy dostaly (přibližně) stejné množství práce. Strategie pro vyrovnání zatížení jsou proměnlivé. Některé nástroje pro vyrovnávání zatížení odesílají každé jiné volání na jiný server. Ostatní dodávají připnutí/vytrvalost relace. Pokročilejší nástroje pro vyrovnávání zatížení využívají skutečný odhad zátěže nebo vytváření sestav ke směrování volání na základě očekávaných nákladů a aktuálního zatížení počítače.

Nástroje pro vyrovnávání zatížení sítě nebo směrovače zpráv se pokusily zajistit, aby byla vrstva webu/pracovního procesu zhruba vyvážená. Strategie pro vyrovnávání datové vrstvy se lišily a závisejí na mechanismu úložiště dat. Vyvážení datové vrstvy na horizontálního dělení dat, ukládání do mezipaměti, spravovaná zobrazení, uložené procedury a další mechanismy specifické pro úložiště.

I když některé z těchto strategií jsou zajímavé, Správce prostředků Service Fabric clusteru není cokoli, jako je třeba síťový nástroj pro vyrovnávání zatížení nebo mezipaměť. Load Balancer sítě vyrovnává frontu pomocí rozprostření provozu napříč frontami. Správce prostředků Service Fabric clusteru má jinou strategii. V podstatě Service Fabric přesouvá *služby* na místo, kde se nejvíce vydávají, očekává se provoz nebo zatížení, které se má sledovat. Například může přesunout služby na uzly, které jsou v současné době studené, protože služby, které neprovádí hodně práce. Uzly mohou být studené, protože služby, které byly přítomny, byly odstraněny nebo přesunuty jinde. Dalším příkladem může být Správce prostředků clusteru přesunout službu mimo počítač. Možná se počítač nebude upgradovat, nebo je přetížený kvůli špičkě ve spotřebě, které služby, které jsou v něm spuštěné. Případně se můžou zvýšit nároky na prostředky služby. V důsledku toho není k dispozici dostatek prostředků pro tento počítač, aby bylo možné pokračovat v jeho spuštění. 

Vzhledem k tomu, že Správce prostředků clusteru zodpovídá za přesun služeb kolem, obsahuje různé sady funkcí v porovnání s tím, co byste našli v nástroji pro vyrovnávání zatížení sítě. Důvodem je to, že nástroje pro vyrovnávání zatížení sítě dodávají síťový provoz do umístění, kde již služby existují, i v případě, že toto umístění není ideální pro provozování samotné služby. Cluster Service Fabric Správce prostředků využívá zásadní různé strategie pro zajištění efektivního využívání prostředků v clusteru.

## <a name="next-steps"></a>Další kroky
- Informace o architektuře a toku informací Správce prostředků clusteru najdete v [tomto článku](service-fabric-cluster-resource-manager-architecture.md) .
- Správce prostředků clusteru má mnoho možností pro popis clusteru. Další informace o metrikách najdete v tomto článku [popisujícím cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md) .
- Další informace o konfiguraci služeb najdete v informacích [o konfiguraci služeb](service-fabric-cluster-resource-manager-configure-services.md) .
- Metriky představují způsob, jakým Správce prostředků clusteru Service Fabric spravuje spotřebu a kapacitu v clusteru. Další informace o metrikách a o tom, jak je nakonfigurovat, najdete v [tomto článku](service-fabric-cluster-resource-manager-metrics.md) .
- Cluster Správce prostředků pracuje s možnostmi správy Service Fabric. Pokud chcete získat další informace o této integraci, přečtěte si [Tento článek](service-fabric-cluster-resource-manager-management-integration.md) .
- Informace o tom, jak cluster Správce prostředků spravuje a vyrovnává zatížení v clusteru, najdete v článku o [Vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md) .

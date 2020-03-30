---
title: Představujeme Správce prostředků clusteru Service Fabric
description: Další informace o Service Fabric Cluster Resource Manager, způsob, jak spravovat orchestraci služeb vaší aplikace.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: da9205f5d95eaf1b4dc655ee727ab8a4fe90893d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563322"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Představujeme správce prostředků clusteru Service Fabric
Tradičně správa IT systémů nebo online služeb znamenala věnovat konkrétní fyzické nebo virtuální počítače těmto konkrétním službám nebo systémům. Služby byly navrženy jako úrovně. K dispozici by byla vrstva "web" a úroveň "data" nebo "úložiště". Aplikace by mít úroveň zasílání zpráv, kde požadavky tekly dovnitř a ven, stejně jako sada počítačů určených pro ukládání do mezipaměti. Každá úroveň nebo typ pracovního vytížení měl specifické počítače vyhrazené pro něj: databáze má několik počítačů věnovaných k němu, webové servery několik. Pokud určitý typ úlohy způsobil, že počítače, na kterých byl spuštěn příliš horký, pak jste přidali další počítače se stejnou konfigurací do této vrstvy. Však ne všechny úlohy lze škálovat tak snadno – zejména s datovou vrstvou byste obvykle nahradit počítače s většími počítači. Snadné. Pokud se počítač nezdařil, běžela tato část celkové aplikace s nižší kapacitou, dokud nebylo možné počítač obnovit. Stále poměrně snadné (ne-li nutně zábavné).

Nyní se však svět služeb a softwarové architektury změnil. Je běžnější, že aplikace přijaly horizontální navýšení kapacity návrhu. Vytváření aplikací s kontejnery nebo mikroslužeb (nebo obojí) je běžné. Nyní, i když stále můžete mít jen několik počítačů, nejsou spuštěny pouze jednu instanci úlohy. Mohou dokonce spouštět více různých úloh současně. Nyní máte desítky různých typů služeb (žádný spotřebovává plné prostředky počítače), možná stovky různých instancí těchto služeb. Každá pojmenovaná instance má jednu nebo více instancí nebo replik pro vysokou dostupnost (HA). V závislosti na velikosti těchto úloh a jejich zaneprázdněnosti se můžete ocitnout se stovkami nebo tisíci počítačů. 

Najednou správa vašeho prostředí není tak jednoduché jako správa několika počítačů určených pro jednotlivé typy úloh. Vaše servery jsou virtuální a již nemají jména (jste přešel myšlení z [domácích zvířat na dobytek](https://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) po všem). Konfigurace je méně o strojích a více o samotných službách. Hardware, který je vyhrazen pro jednu instanci úlohy je do značné míry minulostí. Služby samy o sobě se staly malými distribuovanými systémy, které pokrývají několik menších kusů komoditního hardwaru.

Vzhledem k tomu, že vaše aplikace už není řada monolitů rozprostřených na několika úrovních, máte teď mnohem víc kombinací, se kterými se můžete vypořádat. Kdo rozhoduje o tom, jaké typy úloh lze spustit na kterém hardwaru nebo kolik? Které úlohy fungují dobře na stejném hardwaru a který konflikt? Když se stroj zhasne, jak víte, co tam na tom stroji běželo? Kdo má na starosti, aby se ujistil, že pracovní vytížení začne běžet znovu? Čekáte na návrat (virtuálního?) počítače nebo vaše úlohy automaticky přejdou na jiné počítače a budou spuštěny? Je nutná lidská intervence? A co upgrady v tomto prostředí?

Jako vývojáři a operátoři zabývající se v tomto prostředí, budeme chtít pomoci při správě této složitosti. Najímání flám a snaží se skrýt složitost s lidmi, pravděpodobně není správná odpověď, tak co budeme dělat?

## <a name="introducing-orchestrators"></a>Představujeme orchestrátory
"Orchestrator" je obecný termín pro část softwaru, který pomáhá správcům spravovat tyto typy prostředí. Orchestrators jsou součásti, které berou v požadavcích, jako je "Chtěl bych pět kopií této služby běží v mém prostředí." Snaží se, aby prostředí odpovídalo požadovanému stavu, bez ohledu na to, co se stane.

Orchestrators (ne lidé) jsou co přijmout opatření při selhání počítače nebo pracovní vytížení ukončí z nějakého neočekávaného důvodu. Většina orchestrátorů dělá víc, než že se jen vyrovnává s neúspěchem. Další funkce, které mají, jsou správa nových nasazení, zpracování upgradů a řešení spotřeby prostředků a zásad správného řízení. Všechny orchestrátory jsou zásadně o zachování některé požadovaný stav konfigurace v prostředí. Chcete být schopni říct orchestrátor, co chcete, a nechat to udělat těžkou práci. Aurora na vrcholu Mesos, Docker Datacenter/Docker Swarm, Kubernetes a Service Fabric jsou všechny příklady orchestrátorů. Tyto orchestrátory jsou aktivně vyvíjeny tak, aby vyhovovaly potřebám skutečné hospo- 

## <a name="orchestration-as-a-service"></a>Orchestrace jako služba
Správce prostředků clusteru je systémová součást, která zpracovává orchestraci v service fabric. Úloha Správce prostředků clusteru je rozdělena do tří částí:

1. Vynucování pravidel
2. Optimalizace prostředí
3. Pomoc s jinými procesy

### <a name="what-it-isnt"></a>Co to není
V tradičních aplikacích úrovně N je vždy [balancer zatížení](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Obvykle se jednalo o nástroje vyrovnávání zatížení sítě (NLB) nebo nástroje pro vyrovnávání zatížení aplikace (ALB) v závislosti na tom, kde seděl v síťovém zásobníku. Některé nástroje pro vyrovnávání zatížení jsou hardwarové, jako je nabídka BigIP společnosti F5, jiné jsou založené na softwaru, jako je služba NLB společnosti Microsoft. V jiných prostředích se v této roli může zobrazit něco jako HAProxy, nginx, Istio nebo Envoy. V těchto architekturách je úlohou vyrovnávání zatížení zajistit, aby bezstavové úlohy přijímalo (zhruba) stejné množství práce. Strategie pro vyrovnávání zatížení se lišily. Některé vyvažovače by odeslat každý jiný hovor na jiný server. Jiní za předpokladu, relace připnutí / lepivost. Pokročilejší vyvažovače používají odhad skutečného zatížení nebo vykazování ke směrování volání na základě očekávaných nákladů a aktuálního zatížení stroje.

Síťové vyvažovače nebo směrovače zpráv se snažily zajistit, aby webová/pracovní vrstva zůstala zhruba vyvážená. Strategie pro vyvážení datové vrstvy byly různé a závisely na mechanismu ukládání dat. Vyvažování datové vrstvy spoléhal na ukládání dat, ukládání do mezipaměti, spravované zobrazení, uložené procedury a další mechanismy specifické pro úložiště.

Zatímco některé z těchto strategií jsou zajímavé, Správce prostředků clusteru Service Fabric není nic jako nástroje pro vyrovnávání zatížení sítě nebo mezipaměti. Frontendy vyrovnává vyvažování zatížení sítě rozložením provozu mezi frontendy. Správce prostředků clusteru Service Fabric má jinou strategii. V zásadě Service Fabric přesune *služby* tam, kde mají největší smysl, očekává provoz nebo zatížení následovat. Například může přesunout služby do uzlů, které jsou aktuálně studené, protože služby, které jsou tam nedělají mnoho práce. Uzly mohou být studené, protože služby, které byly přítomny byly odstraněny nebo přesunuty jinam. Jako další příklad správce prostředků clusteru může také přesunout službu od počítače. Možná, že počítač se chystá být upgradován, nebo je přetížena z důvodu špičky spotřeby služby spuštěné na něm. Případně může zvýšit požadavky na prostředky služby. V důsledku toho nejsou v tomto počítači dostatečné prostředky, aby jej bylo pokračovat v jeho spuštění. 

Vzhledem k tomu, že Správce prostředků clusteru je zodpovědný za přesun služeb, obsahuje jinou sadu funkcí ve srovnání s tím, co byste našli v síťovém nástroje pro vyrovnávání zatížení. Důvodem je, že síťové vyrovnávání zatížení doručují síťový provoz tam, kde již služby jsou, i když toto umístění není ideální pro spuštění samotné služby. Správce prostředků clusteru Service Fabric používá zásadně odlišné strategie pro zajištění, že prostředky v clusteru jsou efektivně využívány.

## <a name="next-steps"></a>Další kroky
- Informace o architektuře a toku informací ve Správci prostředků clusteru naleznete v [tomto článku](service-fabric-cluster-resource-manager-architecture.md)
- Správce prostředků clusteru má mnoho možností pro popis clusteru. Další informace o metrikách najdete v tomto článku [o popisu clusteru Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- Další informace o konfiguraci služeb [naleznete v informacích o konfiguraci služeb](service-fabric-cluster-resource-manager-configure-services.md)
- Metriky jsou způsob, jakým je manger prostředků clusteru Service Fabric spravuje spotřebu a kapacitu v clusteru. Další informace o metrikách a jejich konfiguraci najdete v [tomto článku](service-fabric-cluster-resource-manager-metrics.md)
- Správce prostředků clusteru pracuje s možnostmi správy Service Fabric. Chcete-li se dozvědět více o této integraci, přečtěte si [tento článek](service-fabric-cluster-resource-manager-management-integration.md)
- Informace o tom, jak Správce prostředků clusteru spravuje a vyrovnává zatížení clusteru, naleznete v článku o [vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md)

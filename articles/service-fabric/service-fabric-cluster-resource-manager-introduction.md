---
title: Úvod do Service Fabric Cluster Resource Manager | Dokumentace Microsoftu
description: Úvod do Service Fabric Cluster Resource Manager.
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
ms.openlocfilehash: f3f8cf88268498d20651eab40eb655313180cadc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203195"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Úvod do Service Fabric cluster resource Manageru
Tradičně Správa informačních systémů nebo online služby určené vyhradíte konkrétní fyzické nebo virtuální počítače pro tyto konkrétní služby nebo systémy. Služby byla navržena jako úrovně. By být "web" a "data" nebo "úložiště" vrstvu. Aplikace by měla mít zasílání zpráv úrovně, kde požadavky byly převedeny dovnitř a ven a také sadu počítačů vyhrazený pro ukládání do mezipaměti. Každou vrstvu nebo typu úlohy konkrétního počítače, které jsou vyhrazené pro to měl: databáze je teď několik počítačů dedicated, webové servery v pár. Je-li konkrétní typ zatížení počítače, ve kterém se k spuštění příliš horké, pak jste přidali další počítače pomocí této stejnou konfiguraci na dané úrovni. Ale ne všechny úlohy může tak snadno škálovat – zejména s datovou vrstvou obvykle nahradíte počítače s větší počítače. Snadné. Pokud na počítači se nezdařilo, část celkové aplikace nižší kapacitu spuštěné, dokud se počítač může obnovit. Stále poměrně snadné (Pokud není nutně zábavné).

Teď ale ve světě služby a softwarovou architekturu byl změněn. Je běžné, že aplikace, si osvojila návrh pro horizontální navýšení kapacity. Vytváření aplikací s kontejnery nebo mikroslužbami (nebo obojí) je běžné. Nyní když pouze několik počítačů mohou mít i nadále, neběží jenom jednu instanci úlohy. Se možná ještě běží více různých úloh ve stejnou dobu. Teď máte desítkách různých typů služeb (žádné využívání úplné počítač za prostředky), případně stovky různých instancí těchto služeb. Každá pojmenované instance má jeden nebo více instancí nebo replik pro vysokou dostupnost (HA). V závislosti na velikosti těchto pracovních postupů a jak jsou zaneprázdněny, může být pro vás sami stovky nebo tisíce počítačů. 

Náhle správě prostředí není tak jednoduchá jako správa několika počítače vyhrazené pro samostatné typy úloh. Vaše servery jsou virtuální a už nebude mít názvy (jste přešli mindsets z [domácí zvířata, aby zvířat](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) po všech). Konfigurace je menší o počítače a další informace o vlastních služeb. Hardware, který je vyhrazen pro jednu instanci úlohy je do značné míry věcí minulosti. Vlastních služeb se staly malé distribuovaných systémů, které jsou rozmístěny v několika menších komoditním hardwaru.

Vzhledem k tomu, že vaše aplikace není nadále řadu monolitů rozděleny mezi několik vrstev, teď mají mnoho více kombinací řešit. Který rozhodne, jaké typy úloh můžete spustit na hardwaru, nebo kolik? Úlohy, které mohl dobře fungovat na stejném hardwaru, a které jsou v konfliktu? Když se počítač dostane dolů jak na to víte, co bylo spuštěné na daném počítači? Kdo má na starosti a ujistěte se, že úlohy znovu spustí? Čekání (virtuální)? počítače k téhle akci vrátit nebo úlohy automaticky převzetí služeb při selhání do dalších počítačů a nechat běžet –? Je vyžadováno zásahu člověka? Co o upgradech v tomto prostředí?

Jako vývojáři a operátoři pracující v tomto prostředí chceme chcete pomoci se správou Tato složitost. Náborovou binge pokus o překonávají složitost s uživateli, pravděpodobně není správná odpověď, tak co můžeme udělat?

## <a name="introducing-orchestrators"></a>Představujeme orchestrátorů
"Orchestrator" je obecný termín pro softwarového produktu, který pomáhá správcům spravovat tyto typy prostředí. Orchestrátory jsou komponenty, které provést v požadavcích jako "Chci pět kopií této služby spuštěné v Moje prostředí." Se pokusí provést prostředí odpovídá požadovanému stavu, bez ohledu na to, co se stane.

Orchestrátory (ne lidé) jsou co když selže na počítači nebo úlohu ukončí neočekávané z nějakého důvodu provést akci. Většina orchestrátorů víc než jenom řešit selhání. Další funkce, které mají správu nových nasazení, zpracování upgradů a týkající se spotřeby prostředků a zásad správného řízení. Všemi orchestrátory jsou v podstatě o správě některých požadovaný stav konfigurace v prostředí. Chcete dát orchestrátor chcete a jeho proveďte těžké úlohy, jako. Programu Aurora nad Mesos, Docker Datacenter nebo Docker Swarm, Kubernetes a Service Fabric jsou všechny příklady orchestrátorů. Tyto orchestrátorů jsou aktivně vyvinutý podle potřeb skutečné úlohy v produkčním prostředí. 

## <a name="orchestration-as-a-service"></a>Orchestrace jako služba
Cluster Resource Manager je součásti systému, která zajišťuje orchestraci v Service Fabric. Cluster Resource Manageru úlohy je rozdělit do tří částí:

1. Vynucení pravidel
2. Optimalizace prostředí
3. Pomoc s dalšími procesy

### <a name="what-it-isnt"></a>Co ne
V tradičních aplikací vrstvy N, je vždy [nástroje pro vyrovnávání zatížení](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Obvykle to bylo nástroje pro vyrovnávání zatížení sítě (NLB) nebo aplikaci zatížení nástroje pro vyrovnávání (ALB) v závislosti na tom, kde byla ve sadu síťových protokolů. Některé nástroje pro vyrovnávání zatížení jsou založeny na hardwaru jako je F5 Big-IP nabídky, jiné jsou na základě softwaru jako je Microsoft na Vyrovnávání zatížení sítě. V jiných prostředích může se zobrazit něco jako HAProxy, serveru nginx, Istio nebo Envoy v této roli. V tyto architektury je práce pro vyrovnávání zatížení k zajištění, že Bezstavová zatížení přijímat (přibližně) stejné množství práce. Strategie pro vyrovnávání zatížení proměnlivá. Některé nástroje pro vyrovnávání bude posílat každé jiné volání na jiný server. Ostatní poskytuje Připnutí/dlouhodobost relace. Rozšířené nástroje pro vyrovnávání použít vlastní operaci načtení odhad nebo vytváření sestav pro směrování na základě jeho očekávané náklady a aktuální zatížení počítače volání.

Nástroje pro vyrovnávání síťové nebo zprávy směrovače se pokusil Ujistěte se, že úroveň web/worker zůstala zhruba vyvážené. Strategie pro datovou vrstvu služby Vyrovnávání byly závislé na mechanismem úložiště dat a jiné. Vyrovnávání v datové vrstvě spoléhal na horizontální dělení dat, ukládání do mezipaměti, spravované zobrazení, uložené procedury a další mechanismy konkrétní úložiště.

I když některé z těchto strategií zajímavé, Service Fabric Cluster Resource Manageru není nic jako nástroj pro vyrovnávání zatížení sítě nebo mezipaměť. Nástroj pro vyrovnávání zatížení sítě vyrovnává front-endů tím, že rozprostírá provoz mezi front-endů. Service Fabric Cluster Resource Manager má jinou strategii. V podstatě, Service Fabric přesune *služby* kde smysl nejvíce, očekává se provoz nebo načíst dodržovat. Například to může být přesunutí služeb do uzlů, které jsou aktuálně studenou, protože služby, které se nacházejí se tím množství práce. Uzlů může být úplné, protože byly odstraněny nebo přesunuty jinde služby, které byly k dispozici. Další příklad – Cluster Resource Manager by mohla také přesunout služby od počítače s. Možná je na počítači budou upgradovány, nebo je přetížena kvůli nárůst využití služeb běžících na něj. Alternativně může zvýšily požadavky na prostředky služby. V důsledku nejsou k dispozici dostatečné prostředky na tomto počítači bude moct být spuštěná ho. 

Vzhledem k tomu, že Cluster Resource Manager je zodpovědný za přesun služby kolem, obsahuje různé funkce set v porovnání s byste našli v nástroj pro vyrovnávání zatížení sítě. Je to proto, že nástroje pro vyrovnávání zatížení sítě doručovat síťový provoz ve kterém už jsou služby, i v případě, že umístění není ideální pro provoz samotné služby. Service Fabric Cluster Resource Manager používá fundamentálně odlišný způsob strategie pro zajištění, že jsou efektivní využívání prostředků v clusteru.

## <a name="next-steps"></a>Další postup
- Informace o architektuře a informačního toku v Cluster Resource Manager, projděte si [v tomto článku ](service-fabric-cluster-resource-manager-architecture.md)
- Cluster Resource Manager má mnoho možností pro popis clusteru. Další informace o metrikách, prohlédněte si tento článek na [popisující cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- Další informace o konfiguraci služby [informace o konfiguraci služby](service-fabric-cluster-resource-manager-configure-services.md)
- Metriky se, jak spravuje správce prostředků Service Fabric Cluster využití a kapacitu v clusteru. Další informace o metriky a způsob jejich konfigurace rezervace [v tomto článku](service-fabric-cluster-resource-manager-metrics.md)
- Cluster Resource Manager spolupracuje s možností správy Service Fabric. Přečtěte si další informace o této integraci, přečtěte si téma [v tomto článku](service-fabric-cluster-resource-manager-management-integration.md)
- Přečtěte si o tom, jak Cluster Resource Manager spravuje a vyrovnává zatížení v clusteru, přečtěte si článek na [Vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md)

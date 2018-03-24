---
title: Časté otázky týkající se služby Microsoft Azure Service Fabric | Microsoft Docs
description: Nejčastější dotazy ohledně Service Fabric a jejich odpovědi
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: ''
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: chackdan
ms.openlocfilehash: 0ecce5581e8f14a02ad0ad618a226f4671e92f4b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="commonly-asked-service-fabric-questions"></a>Často kladené otázky Service Fabric

Existuje mnoho nejčastější dotazy týkající se co můžete udělat Service Fabric a jak se má použít. Tento dokument popisuje mnoho z těchto běžné otázky a odpovědi.

## <a name="cluster-setup-and-management"></a>Instalace a správy

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Můžete vytvořit cluster, který zahrnuje několik oblastí Azure nebo vlastní datových centrech?

Ano. 

Základní technologie clusteringu Service Fabric lze kombinovat počítače, které běží kdekoliv na světě tak dlouho, dokud mají připojení k síti mezi sebou. Však vytváření a spouštění takového clusteru může být složité.

Pokud vás zajímá v tomto scénáři, doporučujeme vám získat v kontaktu buď prostřednictvím [Service Fabric Githubu problémy seznamu](https://github.com/azure/service-fabric-issues) nebo vaším zástupcem podporu, aby bylo možné získat další pokyny. Service Fabric tým pracuje na poskytnout další přehlednost, pokyny a doporučení pro tento scénář. 

Pár věcí k uvážení: 

1. Prostředek clusteru Service Fabric v Azure je regionální v současné době jsou sady škálování virtuálního počítače, zda je cluster založený na. To znamená, že místní selhání může ztratit schopnost spravovat cluster pomocí Azure Resource Manager nebo portálu Azure. To může dojít, i když cluster zůstane spuštěný a bude moct pracovat s ním přímo. Kromě toho Azure ještě dnes nenabízí možnost používat jedné virtuální sítě, které lze použít v oblastech. To znamená, že cluster více oblasti v Azure vyžaduje buď [veřejné IP adresy pro každý virtuální počítač ve Škálovací sady virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) nebo [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md). Tyto možnosti sítě mají různé dopady na náklady, výkon, a při návrhu aplikace některé míry, proto opatrní analýzy a plánování je vyžadována před stojící až takovém prostředí.
2. Údržby, správu, a monitorování těchto počítačů se může stát složitá, zejména v případě, že rozloženy _typy_ prostředí, například mezi zprostředkovatelé jiný cloud, nebo mezi místními prostředky a Azure. Musí být dbát na to, že upgrady, monitorování, správy a diagnostiky jsou pochopeny pro cluster a aplikace před spuštěním úlohy v produkčním prostředí v takovém prostředí. Pokud již máte zkušenosti řešení těchto problémů v Azure nebo v rámci vlastní datových center, je pravděpodobné, že tyto stejné řešení může být použitá při vytváření nebo spuštění clusteru Service Fabric. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Service Fabric uzly automaticky přijímat aktualizace operačního systému?

Není v současné době ale toto je také běžné požadavek, který Azure v úmyslu dodat.

Do té doby máme [zadané aplikace](service-fabric-patch-orchestration-application.md) , zůstat v operačních systémech pod uzly Service Fabric opravený a aktuální.

Výzva s aktualizacemi operačního systému je, že obvykle vyžadují restart počítače, což vede k dočasné dostupnosti ztráty. Samostatně, který se nejedná o problém, protože Service Fabric automaticky přesměrování provozu pro tyto služby do dalších uzlů. Pokud nejsou aktualizace operačního systému koordinované napříč clusterem, existuje však možností mnoho uzly přejděte dolů najednou. Takové souběžných restartování může způsobit ztrátu dokončení dostupnosti pro službu nebo v minimálně u konkrétního oddílu (pro stavové služby).

V budoucnu plánujeme podporu zásadu aktualizace operačního systému, která je plně automatizované a koordinované napříč doménami aktualizace, zajistíte, že se zachová dostupnosti, i když restartování počítače a jiné neočekávané chyby.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Můžete použít sady škálování velký virtuální počítač v clusteru Moje SF? 

**Krátkodobých odpovědí** – ne. 

**Dlouho zodpovědět** – Přestože sady škálování velký virtuální počítač umožňují škálování virtuálního počítače škálovat instance až 1 000 virtuálních počítačů sady, učiní tak pomocí skupin umístění (PGs). Domén selhání (FDs) a aktualizace domény (UDs) jsou jenom konzistentní v rámci umístění skupiny Service fabric používá FDs a UDs rozhodnutí umístění instancí služby repliky nebo služby. Vzhledem k tomu, že je porovnatelný z hlediska pouze v rámci skupiny umístění FDs a UDs, nemůžete ji použít SF. Například pokud má VM1 v so1 topologii FD = 0 a VM9 v SO2 má topologii FD = 4, neznamená, VM1 a virtuálního počítače 2 se nacházejí ve dvou různých stojany hardwaru, proto SF nelze použít hodnoty FD v takovém případě při rozhodování o umístění.

Další problémy s škálovací sady virtuálních počítačů velké aktuálně neexistují, jako jsou nedostatečná úroveň 4 načíst vyrovnávání podporu. Naleznete v tématu [podrobnosti o velké škálování sady](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Co je minimální velikost clusteru Service Fabric? Proč nemůže být menší?

Minimální podporovaná velikost pro cluster Service Fabric spuštění úlohy v produkčním prostředí je pět uzlů. Pro scénáře vývoje/testování podporujeme clustery tři uzly.

Tyto minimálních existovat, protože cluster Service Fabric spouští sadu stavová systémových služeb, včetně názvů služby a Správce převzetí služeb při selhání. Tyto služby, které sledují jaké služby jsou nasazené do clusteru a kde se aktuálně hostuje, závisí na silnou konzistenci. Tento silnou konzistenci zase, závisí na schopnost získat *kvora* jakékoliv dané aktualizace stavu těchto služeb, kde kvorum představuje striktní většinou replik (N/2 + 1) pro danou službu.

S na pozadí Podívejme se na některé konfigurace možné clusteru:

**Jeden uzel**: tuto možnost nenabízí vysokou dostupnost od ztrátu jeden uzel, pro z jakéhokoli důvodu znamená ztrátu celý cluster.

**Dva uzly**: kvora pro službu nasadit mezi dva uzly (N = 2) je 2 (2/2 + 1 = 2). Při ztrátě jednu repliku není možné vytvořit kvora. Vzhledem k tomu, že provádění aktualizace služby vyžaduje dočasně trvá dolů repliku, nejedná užitečné konfigurace.

**Tři uzly**: s tři uzly (N = 3), požadavek na vytvoření kvora je stále dva uzly (3/2 + 1 = 2). To znamená, že můžete ztratit jednotlivých uzlu a udržení kvora.

Konfigurace třemi uzly clusteru je podporována pro vývojové a testovací vzhledem k tomu, že můžete provádět upgrade a zůstanou platné i po selhání jednotlivých uzlů, dokud není nastat současně. Pro úlohy v produkčním prostředí musíte být odolné vůči takové současné selhání, takže potřebujete pět uzlů.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Je možné vypnout Moje clusteru v noci nebo víkendů abyste ušetřili náklady?

Obecně to možné není. Service Fabric ukládá stav na místní dočasné disků, což znamená, že pokud se virtuální počítač přesune do jiného hostitele, data nepřesouvá s ním. V běžném provozu, který se nejedná o problém nový uzel budou přeneseny aktuální jinými uzly. Pokud zastavíte všechny uzly a je restartovat později, existuje však významné možnost, že většina uzlů spustit na nového hostitele a zkontrolujte systém nemůže provést zotavení.

Pokud chcete vytvořit clustery pro testování vašich aplikací před nasazením, doporučujeme dynamicky vytvořte tyto clustery jako součást vaší [nepřetržité integrace/průběžné kanálu nasazení](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Jak mohu provést upgrade operačního systému (například ze systému Windows Server 2012 na Windows Server 2016)?

Při pracujeme na vylepšené uživatelské prostředí, v současné době jste zodpovědní za upgradu. Je nutné upgradovat bitovou kopii operačního systému na virtuální počítače z jednoho clusteru virtuálních počítačů současně. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Můžete šifrovat připojené datové disky v typu uzlu clusteru (škálovací sadu virtuálních počítačů)?
Ano.  Další informace najdete v tématu [vytvořit cluster s disky připojené data](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks), [šifrování disků (PowerShell)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md), a [šifrování disků (CLI)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-cli.md).

 
## <a name="application-design"></a>Návrh aplikace

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Co je nejlepší způsob, jak dotaz na data napříč oddíly spolehlivé kolekce?

Spolehlivé kolekce jsou obvykle [oddílů](service-fabric-concepts-partitioning.md) povolit škálování pro lepší výkon a propustnost. To znamená, že může být stavu pro danou službu rozloženy desítky či stovky počítačů. K provádění operací přes tento úplné datové sady, máte několik možností:

- Vytvoření služby, který se dotazuje všechny oddíly jinou službu stáhnout požadovaná data.
- Vytvoření služby, který může přijímat data z všechny oddíly z jiné služby.
- Pravidelně nabízet data z každé služby k externím obchodu. Tento přístup je jenom vhodné v případě, že právě prováděné dotazy nejsou součástí základní obchodní logiky.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Co je nejlepší způsob, jak dotaz na data napříč Moje aktéři?

Aktéři jsou navrženy tak, aby nezávislé jednotky stavu a výpočty, a nedoporučuje se provádět široké dotazy stavu objektu actor v době běhu. Pokud máte třeba dotazu mezi úplnou sadu stavu objektu actor, je třeba zvážit buď:

- Nahraďte vaše služby objektu actor stavová spolehlivé služby tak, aby se počet síťových požadavků ke shromažďování všech dat ze počet aktéři počet oddílů ve službě.
- Navrhování vaší subjektům v externím obchodu pro snazší dotazování pravidelně nabízená jejich stavu. Jako výše, tento přístup je pouze navíc nefungovalo, pokud nejsou potřeba pro vaše modul runtime chování právě prováděné dotazy.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Kolik dat můžete ukládat v kolekci spolehlivé?

Spolehlivé služby jsou obvykle rozdělena na oddíly, takže částku, kterou můžete uložit omezen pouze podle počtu počítačů, které máte v clusteru a množství paměti k dispozici v těchto počítačích.

Jako příklad předpokládejme, že máme spolehlivé kolekce ve službě se 100 oddíly a 3 repliky, ukládání objektů, které Průměrná velikost 1 kb. Nyní předpokládejme, že máte cluster 10 počítače s 16gb paměti na jeden počítač. Pro jednoduchost a chcete buďte konzervativní, předpokládají, že operační systém a systémových služeb, modulu runtime Service Fabric a vašim službám využívat 6gb paměti, pak 10gb, které jsou k dispozici na počítač, nebo 100 gb pro cluster.

Pamatujte, že každý objekt musí být uložené tři časy (primární a dvě repliky), byste měli dostatek paměti pro přibližně 35 miliónů objektů v kolekci, při fungování v celém rozsahu. Doporučujeme však probíhá odolné vůči souběžných ztrátě domény selhání a upgradu doménu, která představuje asi 1/3 kapacitu a by snížit počet na zhruba miliónů 23.

Všimněte si, že tento výpočet také předpokládá:

- Že distribuce dat mezi oddílů je přibližně uniform nebo že jste se generování sestav metriky zatížení správce prostředků clusteru. Ve výchozím nastavení načte Service Fabric vyrovnávání na základě počtu repliky. V předchozím příkladu, který by uveďte 10 primární repliky a 20 sekundární repliky na každém uzlu v clusteru. Že to funguje dobře pro zatížení, která je rovnoměrně rozdělené mezi oddílů. Pokud není zatížení i, je nutné, aby mohli Resource Manager společně pack menší repliky a povolit větší repliky spotřebuje další paměť na jednotlivých uzlech hlásit zatížení.

- Zda příslušné spolehlivé služby je pouze jeden ukládání stavu v clusteru. Vzhledem k tomu, že do clusteru, abyste mohli nasadit víc služeb, je potřeba mít prostředky s vědomím, že každý musí spouštět a spravovat stav.

- Aby samotného clusteru není rostoucí nebo zmenšení. Pokud přidáte více počítačů, Service Fabric se znovu vyvážit repliky využívat dodatečnou kapacitu, dokud počet počítačů převyšuje počet oddílů ve vaší služby, protože některé replice nemůžou zahrnovat počítače. Naopak pokud zmenšit velikost clusteru odebráním počítače, repliky jsou podrobněji mnoha funkcemi a mít menší celkové kapacity.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Kolik dat můžete ukládat do objektu actor?

Stejně jako u spolehlivé služby množství dat, které můžete ukládat do služby objektu actor je omezen pouze celkového místa na disku a dostupné paměti mezi uzly v clusteru. Jednotlivé aktéři jsou však co nejúčinnější, pokud se používá k zapouzdření malé množství stavu a přidružené obchodní logiku. Obecně platí musí mít jednotlivé objektu actor stavu, který se měří v kilobajtech.

## <a name="other-questions"></a>Další otázky

### <a name="how-does-service-fabric-relate-to-containers"></a>Jak Service Fabric souvisí do kontejnerů?

Kontejnery nabízejí jednoduchý způsob, jak balíček služeb a jejich závislosti tak, aby se spustit konzistentně ve všech prostředích a mohou pracovat izolovaným způsobem na jednom počítači. Service Fabric nabízí způsob, jak nasadit a spravovat služby, včetně [služby, které byly zabaleny v kontejneru](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Plánujete open-source Service Fabric

Máme open source částí Service Fabric ([framework spolehlivé služby](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [spolehlivé aktéři framework](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [integrace knihovny ASP.NET Core](https://github.com/Azure/service-fabric-aspnetcore), [ Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer), a [Service Fabric rozhraní příkazového řádku](https://github.com/Azure/service-fabric-cli)) na webu GitHub a přijímat příspěvky ze strany komunity na těchto projekty. 

Jsme [nedávno vydal](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/) že plánujeme open-source modulu runtime Service Fabric. V tomto okamžiku máme [úložiště Service Fabric](https://github.com/Microsoft/service-fabric/) až na Githubu s Linuxem sestavení a otestování nástroje, což znamená, můžete naklonujte úložiště, sestavení Service Fabric pro Linux, základní testy, otevřete problémy a odeslání žádosti o přijetí změn. Tvrdě pracujeme získat prostředí pro sestavení nemigrují přes taky společně s kompletní CI prostředí systému Windows.

Postupujte podle [Service Fabric blog](https://blogs.msdn.microsoft.com/azureservicefabric/) další podrobnosti, jak se budou oznámeny.

## <a name="next-steps"></a>Další postup

- [Další informace o klíčových konceptech Service Fabric a osvědčené postupy](https://mva.microsoft.com/en-us/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965)

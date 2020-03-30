---
title: Škálování clusteru Azure Service Fabric
description: Další informace o škálování clusterů Azure Service Fabric dovnitř nebo ven a nahoru nebo dolů. Jako požadavky aplikace změnit, tak může Service Fabric clustery.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: atsenthi
ms.openlocfilehash: 9dd60a5898b648215fc8b26e49a706a7b19dfeeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258691"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Škálování clusterů Azure Service Fabric
Cluster Service Fabric je síťová sada virtuálních nebo fyzických počítačů, do kterých jsou vaše mikroslužby nasazeny a spravovány. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Clustery mohou obsahovat potenciálně tisíce uzlů. Po vytvoření clusteru Service Fabric můžete škálovat cluster vodorovně (změnit počet uzlů) nebo svisle (změnit prostředky uzlů).  Cluster můžete kdykoli škálovat, i když jsou v clusteru spuštěny úlohy.  Při škálování clusteru se automaticky škálují také vaše aplikace.

Proč škálovat cluster? Požadavky aplikací se v průběhu času mění.  Možná budete muset zvýšit prostředky clusteru, aby bylo možné splnit zvýšené zatížení aplikací nebo síťový provoz nebo snížit prostředky clusteru při poklesu poptávky.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Změna velikosti a zvětšování velikosti nebo vodorovné škálování
Změní počet uzlů v clusteru.  Jakmile se nové uzly připojí ke clusteru, [Správce prostředků clusteru](service-fabric-cluster-resource-manager-introduction.md) přesune do nich služby, které snižují zatížení existujících uzlů.  Můžete také snížit počet uzlů, pokud prostředky clusteru nejsou používány efektivně.  Jako uzly opustit clusteru, služby přesunout z těchto uzlů a zvýšení zatížení na zbývající uzly.  Snížení počtu uzlů v clusteru spuštěném v Azure vám může ušetřit peníze, protože platíte za počet virtuálních počítačů, které používáte, a ne za zatížení těchto virtuálních počítačů.  

- Výhody: Nekonečné měřítko, teoreticky.  Pokud je vaše aplikace určena pro škálovatelnost, můžete povolit neomezený růst přidáním dalších uzlů.  Nástroje v cloudových prostředích usnadňují přidávání nebo odevzdání uzlů, takže je snadné upravit kapacitu a platit jenom za prostředky, které používáte.  
- Nevýhody: Aplikace musí být [navrženy pro škálovatelnost](service-fabric-concepts-scalability.md).  Aplikační databáze a trvalost může vyžadovat další architektonické práce škálovat také.  [Spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md) ve stavových službách Service Fabric však usnadňují škálování dat aplikací.

Škálovací sady virtuálních počítačů jsou výpočetní prostředek Azure, který můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definován v clusteru Azure, je [nastaven jako samostatná škálovací sada](service-fabric-cluster-nodetypes.md). Každý typ uzlu pak lze škálovat nebo zmenšovat nezávisle, mají různé sady portů otevřené a může mít různé metriky kapacity. 

Při škálování clusteru Azure mějte na paměti následující pokyny:
- primární typy uzlů, ve kterých běží produkční úlohy, by měly mít vždy pět nebo více uzlů.
- neprimární typy uzlů se spuštěnou stavovou produkční úlohou by měly mít vždy pět nebo více uzlů.
- neprimární typy uzlů, ve kterých běží bezstavové produkční úlohy, by měly mít vždy dva nebo více uzlů.
- Jakýkoli typ uzlu [úrovně odolnosti](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) zlata nebo stříbra by měl mít vždy pět nebo více uzlů.
- Neodstraňujte náhodné instance/uzly virtuálních počítače z typu uzlu, vždy používejte funkci škálování škálování virtuálních strojů. Odstranění náhodných instancí virtuálních počítače může nepříznivě ovlivnit schopnost systémů správně vyvažovat zatížení.
- Pokud používáte pravidla automatického škálování, nastavte pravidla tak, aby se změna měřítka (odebrání instancí virtuálních zařízení) provádí po jednom uzlu. Škálování více než jednu instanci najednou není bezpečné.

Vzhledem k tomu, že typy uzlů Service Fabric v clusteru se shodly na škálovacích sadách virtuálních počítačů v back-endu, můžete [nastavit pravidla automatického škálování nebo ručně škálovat](service-fabric-cluster-scale-up-down.md) škálovací sadu typu uzlu/virtuálního počítače.

### <a name="programmatic-scaling"></a>Programové škálování
V mnoha scénářích [škálování clusteru ručně nebo s pravidly automatického škálování](service-fabric-cluster-scale-up-down.md) jsou dobrá řešení. Pro pokročilejší scénáře však nemusí být vhodné. Potenciální nevýhody těchto přístupů zahrnují:

- Ruční škálování vyžaduje přihlášení a explicitní požadavek na operace škálování. Pokud škálování operace jsou vyžadovány často nebo v nepředvídatelných časech, tento přístup nemusí být dobrým řešením.
- Když pravidla automatického škálování odeberou instanci ze škálovací sady virtuálních strojů, automaticky neodeberou znalosti o tomto uzlu z přidruženého clusteru Service Fabric, pokud typ uzlu nemá úroveň odolnosti silver nebo gold. Vzhledem k tomu, že pravidla automatického škálování fungují na úrovni škálovací sady (nikoli na úrovni Service Fabric), pravidla automatického škálování mohou odebrat uzly Service Fabric bez řádného vypnutí. Toto hrubé odstranění uzlu opustí 'ghost' Service Fabric uzel stavu po operacích škálování. Jednotlivec (nebo služba) bude muset pravidelně čistit odstraněný stav uzlu v clusteru Service Fabric.
- Typ uzlu s úrovní odolnosti zlaťáků nebo stříbra automaticky vyčistí odstraněné uzly, takže není potřeba žádné další čištění.
- Přestože existuje [mnoho metrik podporovaných pravidly](../azure-monitor/platform/autoscale-common-metrics.md) automatického škálování, je stále omezená sada. Pokud váš scénář volá škálování na základě některé metriky, které nejsou zahrnuty v této sadě, pak pravidla automatického škálování nemusí být dobrou volbou.

Jak byste měli přistupovat service fabric škálování závisí na scénáři. Pokud škálování je neobvyklé, možnost přidat nebo odebrat uzly ručně je pravděpodobně dostačující. Pro složitější scénáře pravidla automatického škálování a sady SDK, které vystavují možnost škálování programově, nabízejí výkonné alternativy.

Existují azure api, která umožňují aplikacím programově pracovat se sadami škálování virtuálních strojů a clustery Service Fabric. Pokud existující možnosti automatického škálování nefungují pro váš scénář, tato api umožňují implementovat vlastní logiku škálování. 

Jedním z přístupů k implementaci této "domácí" funkce automatickéškálování je přidat novou bezstavovou službu do aplikace Service Fabric pro správu operací škálování. Vytvoření vlastní škálovací služby poskytuje nejvyšší stupeň kontroly a přizpůsobitelnosti oproti chování škálování vaší aplikace. To může být užitečné pro scénáře, které vyžadují přesnou kontrolu nad tím, kdy a jak se aplikace škáluje dovnitř nebo ven. Tento ovládací prvek však přichází s kompromisem složitosti kódu. Použití tohoto přístupu znamená, že je třeba vlastnit škálování kódu, který je netriviální. V rámci `RunAsync` metody služby může sada aktivačních událostí určit, zda je požadováno škálování (včetně kontroly parametrů, jako je maximální velikost clusteru a přebíjecí doba škálování).   

Rozhraní API používané pro interakce škálovací sady virtuálních strojů (ke kontrole aktuálního počtu instancí virtuálních strojů a k jeho úpravě) je [plynulá knihovna Azure Management Compute library](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). Plynulá výpočetní knihovna poskytuje snadno použitelné rozhraní API pro interakci s škálovacími sadami virtuálních strojů.  Chcete-li pracovat se samotným clusterem Service Fabric, použijte [system.fabric.fabricclient](/dotnet/api/system.fabric.fabricclient).

Kód škálování nemusí spustit jako služba v clusteru škálovat, ačkoli. Obě `IAzure` `FabricClient` a můžete se vzdáleně připojit ke svým přidruženým prostředkům Azure, takže škálovací služba může být snadno konzolová aplikace nebo služba systému Windows spuštěná mimo aplikaci Service Fabric.

Na základě těchto omezení můžete chtít [implementovat více přizpůsobené modely automatickéškálování](service-fabric-cluster-programmatic-scaling.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Změna velikosti nahoru a dolů nebo vertikální škálování 
Změní prostředky (procesor, paměť nebo úložiště) uzlů v clusteru.
- Výhody: Architektura softwaru a aplikací zůstává stejná.
- Nevýhody: Konečné měřítko, protože existuje limit, o kolik můžete zvýšit prostředky na jednotlivých uzlech. Prostoje, protože budete muset převést fyzické nebo virtuální počítače do režimu offline, abyste mohli přidat nebo odebrat prostředky.

Škálovací sady virtuálních počítačů jsou výpočetní prostředek Azure, který můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definován v clusteru Azure, je [nastaven jako samostatná škálovací sada](service-fabric-cluster-nodetypes.md). Každý typ uzlu lze pak spravovat samostatně.  Škálování typu uzlu nahoru nebo dolů zahrnuje změnu skladové položky instancí virtuálního počítače v škálovací sadě. 

> [!WARNING]
> Doporučujeme neměnit skladovou položku virtuálního měn typu škálovací sady nebo uzlu, pokud není spuštěna s [odolností stříbra nebo větší](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Změna velikosti skladové položky virtuálního počítače je operace infrastruktury na místě destruktivní pro data. Bez určité schopnosti zpoždění nebo sledování této změny je možné, že operace může způsobit ztrátu dat pro stavové služby nebo způsobit jiné nepředvídané provozní problémy, a to i pro bezstavové úlohy. 
>

Při škálování clusteru Azure mějte na paměti následující pokyny:
- Pokud škálování dolů typu primárníuzel, nikdy byste neměli škálovat dolů více, než co umožňuje [úroveň spolehlivosti.](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)

Proces škálování typu uzlu nahoru nebo dolů se liší v závislosti na tom, zda se jedná o neprimární nebo primární typ uzlu.

### <a name="scaling-non-primary-node-types"></a>Škálování typů neprimárních uzlů
Vytvořte nový typ uzlu s prostředky, které potřebujete.  Aktualizujte omezení umístění spuštěných služeb tak, aby zahrnovala nový typ uzlu.  Postupně (jeden po druhém) snížit počet instancí starého typu uzlu počet instance na nulu tak, aby nebyla ovlivněna spolehlivost clusteru.  Služby budou postupně migrovat na nový typ uzlu jako starý typ uzlu je vyřazenz provozu.

### <a name="scaling-the-primary-node-type"></a>Změna velikosti typu primárního uzlu
Doporučujeme neměnit skladovou položku virtuálního řezu primárního typu uzlu. Pokud potřebujete větší kapacitu clusteru, doporučujeme přidat další instance. 

Pokud to není možné, můžete vytvořit nový cluster a [obnovit stav aplikace](service-fabric-reliable-services-backup-restore.md) (pokud je to možné) ze starého clusteru. Není nutné obnovit žádný stav systémové služby, jsou znovu vytvořeny při nasazení aplikací do nového clusteru. Pokud jste právě spouštěli bezstavové aplikace v clusteru, pak vše, co provedete, je nasazení aplikací do nového clusteru, nemáte co obnovit. Pokud se rozhodnete přejít na nepodporovanou trasu a chcete změnit skladovou položku virtuálního počítače, proveďte změny definice modelu škálovací sady virtuálních strojů tak, aby odrážela novou skladovou položku. Pokud váš cluster má pouze jeden typ uzlu, ujistěte se, že všechny vaše stavové aplikace reagovat na všechny [události životního cyklu repliky služby](service-fabric-reliable-services-lifecycle.md) (jako replika v sestavení je zablokovaný) včas a že vaše replika služby obnovení doba trvání je menší než pět minut (pro úroveň odolnosti Silver). 

## <a name="next-steps"></a>Další kroky
* Informace o [škálovatelnosti aplikací](service-fabric-concepts-scalability.md).
* [Škálování clusteru Azure dovnitř nebo ven](service-fabric-tutorial-scale-cluster.md).
* [Škálování clusteru Azure programově](service-fabric-cluster-programmatic-scaling.md) pomocí plynulé Azure compute SDK.
* [Škálování samostatného clusteru dovnitř nebo ven](service-fabric-cluster-windows-server-add-remove-nodes.md).


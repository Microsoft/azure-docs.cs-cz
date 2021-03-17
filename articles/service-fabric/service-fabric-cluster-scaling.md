---
title: Škálování clusteru Azure Service Fabric
description: Přečtěte si, jak škálovat nebo snížit kapacitu clusterů Azure Service Fabric. V případě změny požadavků aplikace může Service Fabric clustery.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: atsenthi
ms.openlocfilehash: 610c43f64f9073aefe8008473209039122cf36d7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591786"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Škálování clusterů Azure Service Fabric
Cluster Service Fabric je sada virtuálních nebo fyzických počítačů připojených k síti, do kterých se vaše mikroslužby nasazují a spravují. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Clustery můžou obsahovat potenciálně tisíce uzlů. Po vytvoření clusteru Service Fabric můžete škálovat cluster vodorovně (změnit počet uzlů) nebo vertikálně (změnit prostředky uzlů).  Cluster můžete škálovat kdykoli, a to i v případě, že úlohy běží v clusteru.  I když se cluster škáluje, vaše aplikace se automaticky škálují.

Proč škálovat cluster? Aplikace vyžaduje změnu v průběhu času.  Možná budete muset zvýšit prostředky clusteru tak, aby splňovaly zvýšené zatížení aplikace nebo síťový provoz nebo snížili prostředky clusteru, když na vyžádání klesne požadavek.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Horizontální navýšení a zmenšení nebo horizontální škálování
Změní počet uzlů v clusteru.  Jakmile se nové uzly připojí ke clusteru, Správce prostředků k nim [cluster](service-fabric-cluster-resource-manager-introduction.md) přesune služby, které snižují zatížení stávajících uzlů.  Můžete také snížit počet uzlů, pokud se prostředky clusteru nepoužívají efektivně.  Když uzly opustí cluster, služby se z těchto uzlů pohybují a zatížení zbývajících uzlů se zvýší.  Snížení počtu uzlů v clusteru spuštěném v Azure vám může ušetřit peníze, protože platíte za počet virtuálních počítačů, které používáte, a ne na těchto virtuálních počítačích.  

- Výhody: nekonečná škála teoreticky.  Pokud je vaše aplikace navržena pro škálovatelnost, můžete povolit bez omezení nárůst přidáním dalších uzlů.  Nástroje v cloudovém prostředí usnadňují přidávání a odebírání uzlů, takže je možné snadno upravit kapacitu a platíte jenom za prostředky, které využijete.  
- Nevýhody: aplikace musí být [navržené pro škálovatelnost](service-fabric-concepts-scalability.md).  Aplikační databáze a trvalosti mohou vyžadovat i další strukturální práci pro škálování.  [Spolehlivé kolekce](service-fabric-reliable-services-reliable-collections.md) v Service Fabric stavové služby, ale výrazně usnadňují škálování dat aplikací.

Služby Virtual Machine Scale Sets jsou výpočetním prostředkem Azure, který můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definovaný v clusteru Azure, je [nastavený jako samostatná sada škálování](service-fabric-cluster-nodetypes.md). Jednotlivé typy uzlů se pak dají škálovat nezávisle, mají otevřené různé sady portů a můžou mít různé metriky kapacity. 

Při škálování clusteru Azure mějte na paměti následující pokyny:
- typy primárních uzlů, na kterých běží produkční úlohy, by měly mít vždycky pět nebo více uzlů.
- neprimární typy uzlů, na kterých běží stavová provozní zatížení, by měly mít vždycky pět nebo více uzlů.
- neprimární typy uzlů, na kterých běží Bezstavová provozní zatížení, by měly mít vždycky dva nebo více uzlů.
- Každý typ uzlu [úrovně trvanlivosti](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) Gold nebo stříbrného by měl mít vždy pět nebo více uzlů.
- Neodstraňujte náhodné instance virtuálních počítačů nebo uzly z typu uzlu, vždy použijte funkci škálování sady virtuálních počítačů s měřítkem. Odstranění náhodných instancí virtuálních počítačů může negativně ovlivnit schopnost systémů správně vyrovnávat zatížení.
- Pokud používáte pravidla automatického škálování, nastavte pravidla tak, aby se škálování (odebírání instancí virtuálních počítačů) provádělo vždy v jednom uzlu. Horizontální navýšení kapacity více než jedné instance není bezpečné.

Vzhledem k tomu, že typy uzlů Service Fabric v clusteru tvoří služby Virtual Machine Scale Sets v back-endu, můžete [nastavit pravidla automatického škálování nebo ručně škálovat](service-fabric-cluster-scale-in-out.md) jednotlivé typy uzlů nebo sady škálování virtuálních počítačů.

### <a name="programmatic-scaling"></a>Programové škálování
V mnoha scénářích je vhodné škálovat [cluster ručně nebo s pravidly automatického škálování](service-fabric-cluster-scale-in-out.md) jsou dobrá řešení. U pokročilejších scénářů ale nemusí být správně vyhovující. K potenciálním nevýhodám těchto přístupů patří:

- Ruční škálování vyžaduje, abyste se přihlásili a explicitně vyžadovali operace škálování. Pokud se operace škálování vyžadují často nebo v nepředvídatelných časech, tento přístup nemusí být dobrým řešením.
- Když pravidla automatického škálování odeberou instanci ze sady škálování virtuálních počítačů, automaticky neodstraní znalosti tohoto uzlu z přidruženého clusteru Service Fabric, pokud typ uzlu nemá úroveň odolnosti stříbrného nebo zlata. Vzhledem k tomu, že pravidla automatického škálování fungují na úrovni sady škálování (místo na úrovni Service Fabric), pravidla automatického škálování můžou odebrat Service Fabric uzlů, aniž by je bylo možné řádně vypnout. Tento hrubé uzel po dokončení operací škálování ponechá stav "Ghost" Service Fabric uzlu. Jednotlivec (nebo služba) by musel pravidelně vyčistit odebraný stav uzlu v clusteru Service Fabric.
- Typ uzlu s úrovní trvanlivosti Gold nebo stříbrné automaticky čistí odebrané uzly, takže není potřeba žádné další čištění.
- I když pravidla automatického škálování podporují [mnoho metrik](../azure-monitor/autoscale/autoscale-common-metrics.md) , je stále omezená sada. Pokud váš scénář volá škálování na základě některé metriky, která není pokrytá v dané sadě, nemusejí být pravidla automatického škálování vhodná pro možnost.

Způsob přístupu Service Fabric škálování závisí na vašem scénáři. Pokud je škálování Neběžné, je pravděpodobně dostačující možnost Přidat nebo odebrat uzly ručně. V případě složitějších scénářů automaticky Škálujte pravidla a sady SDK, které vystavují schopnost škálování programově nabízet výkonné alternativy.

Existují rozhraní API Azure, která aplikacím umožňují programově pracovat se službou Virtual Machine Scale Sets a Service Fabricmi clustery. Pokud existující možnosti automatického škálování nefungují pro váš scénář, tato rozhraní API umožňují implementovat vlastní logiku škálování. 

Jedním z možností implementace této "automatického škálování" na domovské straně je přidání nové bezstavové služby do aplikace Service Fabric pro správu operací škálování. Vytvoření vlastní služby škálování poskytuje nejvyšší úroveň řízení a úprav chování škálování vaší aplikace. To může být užitečné pro scénáře, které vyžadují přesnou kontrolu nad tím, kdy nebo jak se aplikace škáluje. Tento ovládací prvek však přináší kompromisy proti složitosti kódu. Použití tohoto přístupu znamená, že potřebujete vlastní škálování kódu, který není triviální. V rámci metody služby `RunAsync` může sada aktivačních událostí určit, jestli se vyžaduje škálování (včetně parametrů kontroly, jako je maximální velikost clusteru a škálování cooldowns).   

Rozhraní API, které se používá pro interakce sady škálování virtuálních počítačů (obojí pro kontrolu aktuálního počtu instancí virtuálních počítačů a jejich úpravu), je [výpočetní knihovna pro správu služby Azure Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). COMPUTE COMPUTE Library nabízí snadno použitelné rozhraní API pro interakci se sadami škálování virtuálních počítačů.  Pokud chcete komunikovat s Service Fabric samotným clusterem, použijte [System. Fabric. FabricClient](/dotnet/api/system.fabric.fabricclient).

Kód škálování nemusí být spuštěn jako služba v clusteru, aby se mohl škálovat, i když. `IAzure`A `FabricClient` můžou se vzdáleně připojit ke svým přidruženým prostředkům Azure, takže služba škálování může snadno být Konzolová aplikace nebo služba systému Windows spuštěná mimo Service Fabric aplikace.

Na základě těchto omezení možná budete chtít [implementovat lépe přizpůsobené modely automatického škálování](service-fabric-cluster-programmatic-scaling.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Vertikální škálování a svislé škálování 
Změní prostředky (CPU, paměť nebo úložiště) uzlů v clusteru.
- Výhody: architektura softwaru a aplikací zůstává stejná.
- Nevýhody: omezené škálování, protože existuje omezení, kolik můžete zvýšit množství prostředků na jednotlivých uzlech. Výpadky, protože budete muset přebírat fyzické nebo virtuální počítače offline, aby bylo možné přidat nebo odebrat prostředky.

Služby Virtual Machine Scale Sets jsou výpočetním prostředkem Azure, který můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definovaný v clusteru Azure, je [nastavený jako samostatná sada škálování](service-fabric-cluster-nodetypes.md). Každý typ uzlu se pak dá spravovat samostatně.  Škálování typu uzlu směrem nahoru nebo dolů zahrnuje přidání nového typu uzlu (s aktualizovanou SKU virtuálního počítače) a odebrání starého typu uzlu.

Při škálování clusteru Azure mějte na paměti následující pokyny:
- Pokud bude horizontální navýšení kapacity typu primárního uzlu, neměli byste ho nikdy škálovat více než to, co umožňuje [úroveň spolehlivosti](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) .

Proces škálování typu uzlu nahoru nebo dolů se liší v závislosti na tom, zda se jedná o typ, který není primární, nebo primární uzel.

### <a name="scaling-non-primary-node-types"></a>Škálování typů bez primárních uzlů
Vytvořte nový typ uzlu s prostředky, které potřebujete.  Aktualizujte omezení umístění spuštěných služeb tak, aby zahrnovala nový typ uzlu.  Postupně (po jednom) snižte počet instancí starého typu instance na hodnotu nula, aby se neovlivnila spolehlivost clusteru.  Služby se postupně migrují na nový typ uzlu, protože starý typ uzlu je vyřazený z provozu.

### <a name="scaling-the-primary-node-type"></a>Škálování typu primárního uzlu
Nasaďte nový typ primárního uzlu s aktualizovanou SKU virtuálního počítače a pak v jednom okamžiku zakažte původní instance primárního typu uzlu, aby se systémové služby migrovali do nové sady škálování. Ověřte, že cluster a nové uzly jsou v pořádku, a pak odeberte původní sadu škálování a stav uzlu pro odstraněné uzly.

Pokud to možné není, můžete vytvořit nový cluster a [Obnovit stav aplikace](service-fabric-reliable-services-backup-restore.md) (Pokud je k dispozici) z původního clusteru. Nemusíte obnovovat žádný stav systémové služby, při nasazení aplikací do nového clusteru se znovu vytvoří. Pokud jste ve svém clusteru právě spustili bezstavové aplikace, pak všechny vaše aplikace nasadíte do nového clusteru, takže nemusíte nic obnovovat.

## <a name="next-steps"></a>Další kroky
* Přečtěte si o [škálovatelnosti aplikací](service-fabric-concepts-scalability.md).
* Horizontální navýšení nebo navýšení [kapacity clusteru Azure](service-fabric-tutorial-scale-cluster.md)
* [Škálujte cluster Azure pomocí programu](service-fabric-cluster-programmatic-scaling.md) Fluent Azure COMPUTE SDK.
* Horizontální navýšení [nebo zmenšení kapacity samostatného clusteru](service-fabric-cluster-windows-server-add-remove-nodes.md)


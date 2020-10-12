---
title: Škálovatelnost služeb Service Fabric Services
description: Přečtěte si o škálování v Azure Service Fabric a různých technikách používaných k škálování aplikací.
author: masnider
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: cb5820849fb34e232a07d610e1cedeb40c0fcfba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89005322"
---
# <a name="scaling-in-service-fabric"></a>Škálování v Service Fabric
Azure Service Fabric usnadňuje vytváření škálovatelných aplikací tím, že spravuje služby, oddíly a repliky na uzlech clusteru. Spouštění mnoha úloh na stejném hardwaru umožňuje maximální využití prostředků, ale také nabízí flexibilitu při volbě škálování úloh. Tento video pro kanál 9 popisuje, jak můžete vytvářet škálovatelné aplikace mikroslužeb:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

Škálování v Service Fabric se provádí několika různými způsoby:

1. Škálování vytvořením nebo odebráním bezstavových instancí služby
2. Škálování vytvořením nebo odebráním nových pojmenovaných služeb
3. Škálování vytvořením nebo odebráním nových instancí s názvem aplikace
4. Škálování pomocí dělených služeb
5. Škálování přidáváním a odebíráním uzlů z clusteru 
6. Škálování pomocí Správce prostředků metriky clusterů

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Škálování vytvořením nebo odebráním bezstavových instancí služby
Jedním z nejjednodušších způsobů, jak škálovat v rámci Service Fabric fungují bez bezstavových služeb. Když vytvoříte bezstavovou službu, získáte možnost definovat `InstanceCount` . `InstanceCount` definuje, kolik spuštěných kopií kódu této služby se vytvoří při spuštění služby. Řekněme například, že cluster obsahuje 100 uzlů. Řekněme také, že je služba vytvořená s `InstanceCount` 10. Během běhu by tyto 10 běžící kopie kódu mohly být příliš zaneprázdněné (nebo nemusí být dostatečně zaneprázdněné). Jedním ze způsobů, jak tuto úlohu škálovat, je změna počtu instancí. Například část kódu pro monitorování nebo správu může změnit stávající počet instancí na 50 nebo na 5 v závislosti na tom, zda zatížení musí na základě zatížení nebo na základě zatížení škálovat. 

C#:

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Prostředí

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Použití dynamického počtu instancí
Service Fabric pro bezstavové služby konkrétně nabízí automatický způsob, jak změnit počet instancí. Díky tomu je možné službu dynamicky škálovat s počtem uzlů, které jsou k dispozici. Způsob, jak se vyjádřit k tomuto chování, je nastavení počtu instancí =-1. InstanceCount =-1 je pokyn Service Fabric, který říká "spuštění této bezstavové služby na všech uzlech". Pokud se počet uzlů změní, Service Fabric automaticky změní počet instancí tak, aby odpovídal, aby služba běžela na všech platných uzlech. 

C#:

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

Prostředí

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Škálování vytvořením nebo odebráním nových pojmenovaných služeb
Pojmenovaná instance služby je konkrétní instance typu služby (viz [Service Fabric životní cyklus aplikací](service-fabric-application-lifecycle.md)) v rámci některé pojmenované instance aplikace v clusteru. 

Nově pojmenované instance služby je možné vytvořit (nebo odebrat), protože služby jsou zaneprázdněné nebo menší. To umožňuje, aby se požadavky rozšířily mezi další instance služby, což obvykle umožňuje zatížení stávajících služeb. Při vytváření služeb Cluster Service Fabric Správce prostředků umístí služby v clusteru distribuovaným způsobem. Přesná rozhodnutí se řídí [metrikami](service-fabric-cluster-resource-manager-metrics.md) v clusteru a dalšími pravidly umístění. Služby je možné vytvořit několika různými způsoby, ale nejběžnější jsou buď prostřednictvím akcí správy, jako je volaná osoba [`New-ServiceFabricService`](/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps) nebo voláním kódu [`CreateServiceAsync`](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet) . `CreateServiceAsync` lze dokonce volat v rámci jiných služeb spuštěných v clusteru.

Dynamické vytváření služeb se dá použít v nejrůznějších scénářích a je to běžný vzor. Zvažte například stavovou službu, která představuje konkrétní pracovní postup. Volání, která představují práci, se budou zobrazovat až k této službě a tato služba bude provádět kroky tohoto pracovního postupu a zaznamenat průběh. 

Jak provedete tuto konkrétní škálu služeb? Služba může mít více tenantů v některém formuláři a přijímat volání a odkázat na mnoho různých instancí stejného pracovního postupu najednou. To však může udělat složitější kód, protože se teď musí zabývat spoustou různých instancí stejného pracovního postupu, a to vše v různých fázích a od různých zákazníků. Také zpracování více pracovních postupů ve stejnou dobu neřeší problém škálování. Důvodem je, že v určitém okamžiku bude tato služba spotřebovávat příliš mnoho prostředků, aby se vešly na konkrétní počítač. Mnoho služeb, které nejsou pro tento model sestavené, na prvním místě, taky dochází k potížím kvůli nějakému kritickému kritickému bodu nebo zpomalení kódu. Tyto typy problémů způsobují nefunkčnost služby, i když je počet souběžných pracovních postupů, které sledování sleduje, větší.  

Řešením je vytvořit instanci této služby pro každou jinou instanci pracovního postupu, kterou chcete sledovat. Toto je skvělý vzor a funguje bez stavu nebo stav služby. Aby tento model fungoval, obvykle se jedná o jinou službu, která funguje jako "služba Správce úloh". Úkolem této služby je přijímat požadavky a směrovat tyto požadavky na jiné služby. Správce může dynamicky vytvořit instanci služby úlohy, když obdrží zprávu, a pak předávat požadavky na tyto služby. Služba Správce může také přijímat zpětná volání, když daná služba pracovního postupu dokončí svou úlohu. Když správce obdrží tato zpětná volání, může odstranit tuto instanci služby pracovního postupu nebo ji nechat v případě, že je očekáváno více volání. 

Rozšířené verze tohoto typu správce mohou dokonce vytvořit fondy služeb, které spravuje. Fond pomáhá zajistit, že pokud se nová žádost dostane do, nemusí počkat, až se služba vytočí. Místo toho může správce vybrat pouze službu pracovního postupu, která není aktuálně zaneprázdněná z fondu, nebo se dá náhodně směrovat. Udržování dostupného fondu služeb zajišťuje rychlejší zpracování nových požadavků, protože je méně pravděpodobný, že požadavek čeká na vyřízení nové služby. Vytváření nových služeb je rychlé, ale ne bezplatné nebo okamžité. Tento fond pomáhá minimalizovat dobu, po kterou musí požadavek čekat, než se obsluhuje. Často se vám zobrazí tento model manažer a fond, pokud se v něm nejvíce vyskytují doby odezvy. Zařazení požadavku do fronty a vytvoření služby na pozadí a jejich _předání na_ je také oblíbený vzor správce, který vytváří a odstraňuje služby na základě určitého sledování množství práce, které služba aktuálně čeká. 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Škálování vytvořením nebo odebráním nových instancí s názvem aplikace
Vytváření a odstraňování instancí celé aplikace se podobá vzorům vytváření a odstraňování služeb. Pro tento model je k dispozici některá služba manažera, která provádí rozhodnutí na základě požadavků, které vidí, a informací, které přijímá od ostatních služeb v rámci clusteru. 

Kdy by se měl místo vytvoření nové instance pojmenované služby v některé z již existujících aplikací vytvořit nová pojmenovaná aplikace? Existuje několik případů:

  * Nová instance aplikace je určena pro zákazníky, jejichž kód musí běžet s určitou konkrétní identitou nebo nastavením zabezpečení.
    * Service Fabric umožňuje definovat různé balíčky kódu, které se mají spustit pod konkrétními identitami. Aby bylo možné spustit stejný balíček kódu pod různými identitami, musí být aktivace provedena v různých instancích aplikace. Vezměte v úvahu případ, kdy jste nasadili stávající úlohy zákazníka. Ty mohou být spuštěny pod určitou identitou, takže můžete monitorovat a řídit jejich přístup k jiným prostředkům, jako jsou vzdálené databáze nebo jiné systémy. V takovém případě, když se nový zákazník zaregistruje, pravděpodobně nebudete chtít aktivovat kód ve stejném kontextu (místo procesu). I když to může být obtížnější, aby kód služby fungoval v kontextu konkrétní identity. Obvykle musíte mít více zabezpečení, izolaci a kód správy identit. Namísto použití různých pojmenovaných instancí služby v rámci stejné instance aplikace a tedy stejného prostoru procesu můžete použít různé pojmenované Service Fabric instance aplikace. To usnadňuje definování různých kontextů identity.
  * Nová instance aplikace slouží také jako způsob konfigurace.
    * Ve výchozím nastavení se všechny pojmenované instance služby konkrétního typu služby v rámci instance aplikace spustí ve stejném procesu na daném uzlu. To znamená, že zatímco každou instanci služby můžete nakonfigurovat odlišně, je to tak komplikované. Služby musí mít nějaký token, který používají k vyhledání konfigurace v rámci konfiguračního balíčku. Obvykle je to pouze název služby. To funguje dobře, ale Couples konfiguraci na názvy jednotlivých instancí služby v rámci dané instance aplikace. To může být matoucí a obtížné spravovat, protože konfigurace je obvykle artefaktem doby návrhu s hodnotami specifickými pro instanci aplikace. Vytváření dalších služeb vždy znamená více upgradů aplikace, aby bylo možné změnit informace v konfiguračních balíčcích nebo nasadit nové, aby nové služby mohly vyhledat konkrétní informace. Často je snazší vytvořit celou novou pojmenovanou instanci aplikace. Pak můžete použít parametry aplikace k nastavení libovolné konfigurace, která je pro služby nezbytná. Tímto způsobem mohou všechny služby, které jsou vytvořeny v rámci této pojmenované instance aplikace, dědit konkrétní nastavení konfigurace. Například namísto použití jednoho konfiguračního souboru s nastavením a přizpůsobením pro každého zákazníka, jako jsou tajná klíčová a zákaznická omezení, byste měli pro každého zákazníka s tímto nastavením přepsat jinou instanci aplikace. 
  * Nová aplikace slouží jako hranice upgradu.
    * V rámci Service Fabric slouží jako hranice pro upgrade různé pojmenované instance aplikace. Upgrade jedné pojmenované instance aplikace nebude mít vliv na kód, na kterém je spuštěná jiná pojmenovaná instance aplikace. Různé aplikace ukončí provoz různých verzí stejného kódu na stejných uzlech. To může být faktor, pokud potřebujete udělat rozhodnutí o škálování, protože si můžete vybrat, jestli se má nový kód řídit stejnými upgrady jako jiná služba nebo ne. Řekněme například, že se volání dorazí na službu manažera, která zodpovídá za to, že se budou škálovat konkrétní úlohy zákazníka tím, že se dynamicky vytvoří a odstraní služby. V tomto případě je však volání pro úlohu přidruženou k _novému_ zákazníkovi. Většina zákazníků, jako je izolace od sebe navzájem, není pouze pro výše uvedené důvody zabezpečení a konfigurace, protože poskytuje větší flexibilitu při spuštění konkrétních verzí softwaru a výběr při jejich upgradu. Můžete také vytvořit novou instanci aplikace a vytvořit službu. stačí k dalšímu rozdělení množství služeb, na které se bude upgradovat jiný upgrade. Samostatné instance aplikace poskytují větší členitost při provádění upgradů aplikace a také umožňují testování/B a nasazení Blue/zelený. 
  * Existující instance aplikace je plná.
    * V Service Fabric je [kapacita aplikace](service-fabric-cluster-resource-manager-application-groups.md) koncept, který můžete použít k řízení množství prostředků dostupných pro konkrétní instance aplikace. Například se můžete rozhodnout, že daná služba musí mít vytvořenou jinou instanci, aby mohla škálovat. Tato instance aplikace je ale pro určitou metriku mimo kapacitu. Pokud by měl tomuto konkrétnímu zákazníkovi nebo úlohám být stále přiděleno více prostředků, můžete buď zvýšit stávající kapacitu této aplikace, nebo vytvořit novou aplikaci. 

## <a name="scaling-at-the-partition-level"></a>Škálování na úrovni oddílu
Service Fabric podporuje dělení. Dělení rozdělí službu do několika logických a fyzických sekcí, z nichž každý funguje nezávisle. To je užitečné u stavových služeb, protože žádná jedna sada replik nesmí zpracovávat všechna volání a manipulovat se všemi stavy najednou. [Přehled dělení](service-fabric-concepts-partitioning.md) poskytuje informace o typech podporovaných schémat dělení. Repliky jednotlivých oddílů jsou rozloženy mezi uzly v clusteru a distribuují se zatížení této služby a zajišťují, že žádná služba jako celek nebo oddíl není v jednom bodě selhání. 

Vezměte v úvahu službu, která používá schéma dělení na oddíly s nízkým klíčem 0, vysokým klíčem 99 a počtem oddílů 4. V clusteru se třemi uzly může být služba rozložená se čtyřmi replikami, které sdílejí prostředky na jednotlivých uzlech, jak je znázorněno zde:

<center>

![Rozložení oddílů se třemi uzly](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Pokud zvýšíte počet uzlů, Service Fabric přesunou některé z existujících replik. Řekněme například, že počet uzlů se zvyšuje na čtyři a repliky se znovu distribuují. Teď služba má teď na každém uzlu spuštěné tři repliky, z nichž každá patří do různých oddílů. To umožňuje lepší využití prostředků, protože nový uzel není studen. Obvykle vylepšuje výkon, protože každá služba má k dispozici více prostředků.

<center>

![Rozložení oddílů se čtyřmi uzly](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Škálování pomocí Service Fabric Správce prostředků clusteru a metrik
[Metrikami](service-fabric-cluster-resource-manager-metrics.md) je způsob, jakým služby vyjadřují spotřebu prostředků Service Fabric. Použití metrik poskytuje clusteru Správce prostředků příležitost k reorganizaci a optimalizaci rozložení clusteru. V clusteru může být například dostatek prostředků, ale nemusí být přidělena službám, které aktuálně pracují. Použití metrik umožňuje Správce prostředků clusteru reorganizovat cluster, aby bylo zajištěno, že služby budou mít přístup k dostupným prostředkům. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Škálování přidáváním a odebíráním uzlů z clusteru 
Další možností pro škálování pomocí Service Fabric je změna velikosti clusteru. Změna velikosti clusteru znamená přidání nebo odebrání uzlů pro jeden nebo více typů uzlů v clusteru. Zvažte například případ, kdy jsou všechny uzly v clusteru horké. To znamená, že prostředky clusteru jsou skoro spotřebovány. V takovém případě je nejlepším způsobem, jak škálovat, přidání dalších uzlů do clusteru. Jakmile se nové uzly připojí ke clusteru, Cluster Service Fabric Správce prostředků přesune služby na ně, což vede k menšímu celkovému zatížení u stávajících uzlů. U bezstavových služeb s počtem instancí =-1 se automaticky vytvoří více instancí služby. To umožňuje, aby některá volání byla přesunuta z existujících uzlů do nových uzlů. 

Další informace najdete v tématu [škálování clusteru](service-fabric-cluster-scaling.md).

## <a name="choosing-a-platform"></a>Výběr platformy

Kvůli rozdílům v implementaci mezi operačními systémy může být použití Service Fabric se systémem Windows nebo Linux důležitou součástí škálování aplikace. Jednou z možných bariér je to, jak se provádí dvoufázové protokolování. Service Fabric v systému Windows používá ovladač jádra pro protokol jednoho počítače, který je sdílen mezi replikami stavové služby. Tento protokol se vyváží přibližně o 8 GB. Linux na druhé straně používá pracovní protokol 256 MB pro každou repliku, takže je méně ideální pro aplikace, které chtějí maximalizovat počet nenáročných replik služby spuštěných v daném uzlu. Tyto rozdíly v požadavcích na dočasné úložiště by mohly potenciálně informovat o požadované platformě nasazení clusteru Service Fabric.

## <a name="putting-it-all-together"></a>Spojení všech součástí dohromady
Pojďme pořizovat všechny nápady, které tady probereme, a promluvit si příklad. Vezměte v úvahu následující službu: Pokoušíte se vytvořit službu, která funguje jako adresář, a podržet se na jména a kontaktní údaje. 

Hned nahoru máte spoustu otázek, které se týkají škálování: kolik uživatelů budete mít? Kolik kontaktů budou jednotliví uživatelé ukládat? Při prvním navýšení vaší služby je obtížné se pokusit vše nahlásit. Řekněme, že jste přešli na jednu statickou službu s konkrétním počtem oddílů. Důsledky vybírání chybného počtu oddílů můžou způsobit problémy s škálováním později. Podobně i v případě, že vyberete správný počet, nebudete mít k dispozici všechny informace, které potřebujete. Například musíte také určit velikost clusteru předem, a to jak v závislosti na počtu uzlů, tak i na jejich velikosti. Je obvykle obtížné odhadnout, kolik prostředků bude služba spotřebovávat během své životnosti. Může být také obtížné znát čas, který se ve službě skutečně zobrazuje. Například může lidé přidat a odebrat své kontakty pouze první věc ráno, nebo je možné je v průběhu dne distribuovat rovnoměrně. Na základě toho může být nutné horizontální navýšení kapacity a dynamické navýšení kapacity. Možná se naučíte, jak předpovídat, když budete potřebovat horizontální navýšení kapacity a navýšení, ale buď budete chtít reagovat na změnu spotřeby prostředků vaší službou. To může zahrnovat změnu velikosti clusteru, aby bylo možné poskytnout více prostředků při reorganizaci používání stávajících prostředků, a to není dostatečné. 

Ale proč se dokonce snažím vybrat schéma jednoho oddílu pro všechny uživatele? Proč omezit na jednu službu a jeden statický cluster? Skutečná situace je obvykle dynamičtější. 

Při sestavování škály zvažte následující dynamický vzor. Možná budete muset přizpůsobit svou situaci:

1. Místo toho, abyste se snažili vybrat schéma vytváření oddílů pro všechny uživatele, vytvořte službu správce.
2. Úkolem služby manažera je podívat se na informace o zákaznících při registraci služby. V závislosti na těchto informacích vytvoří služba Správce instanci vaší _skutečné_ služby úložiště kontaktů _jenom pro daného zákazníka_. Pokud vyžadují konkrétní konfiguraci, izolaci nebo upgrady, můžete se také rozhodnout pro tohoto zákazníka aktivovat instanci aplikace. 

Tento vzor dynamického vytváření přináší mnoho výhod:

  - Nepokoušíte se odhadnout správný počet oddílů pro všechny uživatele nahoru nebo vytvořit jednu službu, která má nekonečně škálovatelnou škálovatelnost. 
  - Různí uživatelé nemusí mít stejný počet oddílů, počet replik, omezení umístění, metriky, výchozí zatížení, názvy služeb, nastavení DNS nebo jakékoli jiné vlastnosti zadané na úrovni služby nebo aplikace. 
  - Získáte další segmentaci dat. Každý zákazník má svou vlastní kopii služby.
    - Každou zákaznickou službu je možné nakonfigurovat odlišně a udělit více nebo méně prostředků, a to podle toho, co je potřeba, podle jejich očekávaného měřítka.
      - Řekněme například, že zákazník zaplatil za "zlatou" úroveň – může získat více replik nebo větší počet oddílů a potenciálně prostředky vyhrazené pro své služby prostřednictvím metrik a kapacity aplikací.
      - Nebo označují, že jsou k dispozici informace indikující, že počet kontaktů, které potřebovali, byl "malý", získá jenom několik oddílů nebo by se mohl do sdíleného fondu služeb umístit i s ostatními zákazníky.
  - Nepoužíváte spoustu instancí služby nebo replik, zatímco čekáte na zobrazení zákazníků.
  - Pokud zákazník odejde z provozu, je odebrání informací z vaší služby snadné, protože správce odstraní tuto službu nebo aplikaci, kterou vytvořil.

## <a name="next-steps"></a>Další kroky
Další informace o Service Fabric konceptech najdete v následujících článcích:

* [Dostupnost služeb Service Fabric Services](service-fabric-availability-services.md)
* [Dělení Service Fabric služeb](service-fabric-concepts-partitioning.md)

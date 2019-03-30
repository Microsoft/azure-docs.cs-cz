---
title: Škálovatelnost služeb Service Fabric | Dokumentace Microsoftu
description: Popisuje postup škálování služby Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 14a7389fe562b5f3206b81411d2224257051c636
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666643"
---
# <a name="scaling-in-service-fabric"></a>Škálování v Service Fabric
Azure Service Fabric umožňuje snadno vytvářet škálovatelné aplikace tím, že spravuje služby, oddíly a repliky na uzlech clusteru. Mnoho úloh na stejném hardwaru umožňuje využití maximální prostředků, ale také poskytuje flexibilitu z hlediska zvoleném způsobu škálovat úlohy. Toto video na Channel 9 popisuje, jak se dají vytvářet škálovatelné mikroslužbových aplikací:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

Škálování v Service Fabric je lze provést několika různými způsoby:

1. Škálování vytvořením nebo odebíráním instancí bezstavové služby
2. Škálování vytvořením nebo nové odebírání s názvem služby
3. Škálování vytvořením nebo nové odebírání pojmenované instance aplikace
4. Škálování oddílů služby
5. Škálování podle přidávání a odebírání uzlů z clusteru 
6. Škálování pomocí Cluster Resource Manageru metriky

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Škálování vytvořením nebo odebíráním instancí bezstavové služby
Jeden z nejjednodušších způsobů škálování v Service Fabric funguje s bezstavové služby. Když vytvoříte bezstavovou službu, získáte možnost definovat `InstanceCount`. `InstanceCount` Určuje, kolik kopií spuštěné této služby v kódu jsou vytvořeny při spuštění služby. Řekněme například, že je 100 uzlů v clusteru. Také Předpokládejme, že služba je vytvořená pomocí `InstanceCount` 10. Za běhu těchto 10 běžící kopie kód všechny stát příliš zaneprázdněn a nemůže (nebo může není dostatečně zaneprázdněn). Chcete-li změnit počet instancí, které je jeden způsob, jak škálovat úlohy. Například některá část kódu, sledování a správu můžete změnit stávající počet instancí, 50 nebo 5, v závislosti na tom, jestli zatížení je potřeba horizontální snížení nebo navýšení kapacity na základě zatížení. 

C#:

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell:

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Použití dynamické počet instancí
Konkrétně pro bezstavové služby Service Fabric nabízí automatické způsob, jak změnit počet instancí. To umožňuje službě dynamicky škálovat počet uzlů, které jsou k dispozici. Způsob, jak začít používat toto chování je nastavit počet instancí = -1. InstanceCount = -1 je instrukce do Service Fabric s textem "Spustit tuto bezstavovou službu na všech uzlech." Pokud se změní z počtu uzlů, Service Fabric automaticky změní počet instancí tak, aby odpovídaly, zajištění, že služba běží na všech uzlech platný. 

C#:

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Škálování vytvořením nebo nové odebírání s názvem služby
Pojmenované instanci služby je konkrétní instance daného typu služby (viz [životního cyklu aplikace Service Fabric](service-fabric-application-lifecycle.md)) v rámci některé instance s názvem aplikace v clusteru. 

Nové instance s názvem služby můžou vytvořit (nebo odebrání) jako služba stane více nebo méně zaneprázdněn. To umožňuje požadavky na možné rozdělit do více instancí služby, obvykle umožňuje na stávající služby ke snížení zatížení. Při vytváření služby Service Fabric Cluster Resource Manager umístí služeb v clusteru distribuované způsobem. Přesné rozhodnutí, která se vztahují [metriky](service-fabric-cluster-resource-manager-metrics.md) v clusteru a další pravidla pro umístění. Služby lze vytvořit několika různými způsoby, ale nejčastěji používané jsou buď prostřednictvím akce správy, jako je někdo volání [ `New-ServiceFabricService` ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps), nebo kód volal [ `CreateServiceAsync` ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet). `CreateServiceAsync` Můžete dokonce možné volat v rámci další služby spuštěné v clusteru.

Vytváření služby dynamicky lze použít v všechny možné druhy scénářů a je běžný vzor. Představte si třeba stavové služby, který představuje určitý pracovní postup. Volání představující práci se chystáte zobrazí pro tuto službu a tato služba bude provádět kroky pro tento průběh pracovního postupu a záznam. 

Jak by provedete škálování této konkrétní služby? Služba může být více tenantů v nějaké podobě a přijímat volání a aktivovat kroky pro mnoho různých instancí stejného pracovního postupu všechny najednou. Však, který může provádět kód složitější, protože teď má se starat o mnoho různých instancí stejného pracovního postupu, všechno v různých fázích a z různých zákazníků. Navíc zpracování více pracovních postupů ve stejnou dobu škálování problém nevyřeší. Je to proto, že v určitém okamžiku tato služba bude využívat příliš mnoho prostředků a nevejde se na konkrétní počítač. Mnoho služeb není pro tento model sestavený na prvním místě i nastat potíže kvůli některé vlastní kritickým bodem nebo zpomalení ve svém kódu. Tyto druhy problémů, že bude služba nechcete pracovat tak dobře, když počet souběžných pracovních postupů, které je sledování získá větší.  

Řešení je k vytvoření instance této služby pro každou jinou instanci pracovního postupu, který chcete sledovat. To je skvělé model a funguje, zda je Bezstavová nebo stavová služba. Pro tohoto modelu chcete pracovat je obvykle jiné službě, která funguje jako "Správce úloh služby". Úloha této služby je pro příjem požadavků a směrovat požadavky do dalších služeb. Správce může dynamicky vytvářet instance služby pracovního vytížení, pokud obdrží zprávu a pak předejte u požadavků na tyto služby. Manager service může také přijímat zpětná volání, po dokončení jeho úlohy služby daného pracovního postupu. Přijetí tato zpětná volání správce ji může odstranit tuto instanci služby pracovního postupu nebo necháte, pokud se očekává, že další volání. 

Pokročilé verze tohoto typu správce, můžete také vytvořit fondy služby, které spravuje. Fond pomáhá zajistit, že při příchodu novou žádost o to nemusí čekat na zprovoznění služby. Místo toho je správce můžete pouze výběr služby pracovního postupu, který není aktuálně zaneprázdněn z fondu nebo směrovat náhodně. Udržování fondu služeb dostupných díky zpracování nových žádostí o rychleji, protože je méně pravděpodobné, že žádost musí počkat na novou službu, chcete-li být spuštěné. Vytváření nových služeb je rychlé, ale není zdarma nebo okamžité. Fond pomáhá minimalizovat čas, kdy má požadavek se má čekat před probíhá údržba. Tento správce a vzor fondu zobrazí často po dobu odezvy, na kterých záleží nejvíce. Služba Řízení front žádosti a vytvoření služby na pozadí a _pak_ předáním je také oblíbené správce vzor, jako je vytváření a odstraňování služby podle některých sledování množství práce tuto službu momentálně má čekající na vyřízení . 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Škálování vytvořením nebo nové odebírání pojmenované instance aplikace
Vytváření a odstraňování instance celá aplikace je podobný vzorec, podle kterého vytváření a odstraňování služby. Pro tento model je některé Správce služby, který provádí rozhodnutí na základě požadavků, které se zobrazují a informace, že přijímá od ostatních služeb v clusteru. 

Kdy je vhodné vytvořit novou instanci s názvem aplikace používat místo vytvoření nové instance služby s názvem v některé již existující aplikaci? Existuje několik případů:

  * Nová instance aplikace je pro zákazníky, jejíž kód je potřeba spustit některé konkrétní identit nebo nastavení zabezpečení.
    * Service Fabric umožňuje definovat balíčky odlišný kód ke spuštění v rámci konkrétní identity. Pokud chcete spustit balíček kódu v rámci různých identit, aktivace musí se provést v různé instance aplikace. Představte si případ, kdy máte stávající zákazník úloh nasazených. Ty mohou být spuštěné pod určitou identitou, vám umožní monitorovat a řídit jeho přístup k jiné prostředky, jako jsou vzdálené databáze nebo jiných systémů. V takovém případě Pokud nový zákazník zaregistruje, pravděpodobně nechcete aktivujte svůj kód ve stejném kontextu (procesního prostoru). Přestože lze to ztěžuje kódu služby tak, aby fungoval v kontextu konkrétní identitu. Obvykle potřebujete další zabezpečení, izolace a kód pro správu identit. Místo použití různých pojmenované instance služby v rámci stejné instance aplikace a proto stejný proces místa, můžete použít různé pojmenované instance aplikace Service Fabric. To usnadňuje definování kontextů jiná identita.
  * Nová instance aplikace slouží také jako způsob konfigurace
    * Ve výchozím nastavení všechny instance služby s názvem typu konkrétní služby v rámci instancí aplikace poběží v rámci stejného procesu v daném uzlu. To znamená, že když každá instance služby můžete nakonfigurovat různě, takový postup je složitější. Služby musíte mít některé token, které používají pro vyhledání jejich konfigurace v rámci konfiguračního balíčku. To je obvykle pouze název služby. To funguje správně, ale jeho páry v odstupu konfiguraci na názvy jednotlivých služeb pojmenované instance v rámci této instance aplikace. To může být matoucí a náročná na správu, protože konfigurace je obvykle artefaktem návrhu dobu s určitými hodnotami instance aplikace. Vytvoření další služby vždy znamená více upgradů aplikací, chcete-li změnit informace v rámci balíčky konfigurace nebo k nasazení nové tak, aby nové služby můžete vyhledat jejich konkrétní informace. Často je snazší vytvářet zcela novou aplikaci s názvem instance. Pak můžete použít parametry aplikace nastavení jakékoli konfigurace je nezbytná pro služby. Tímto způsobem všechny služby, které jsou vytvořeny v rámci, které s názvem instance aplikace může zdědit nastavení konkrétní konfiguraci. Například namísto toho, aby jeden konfigurační soubor s nastavením a vlastní nastavení pro každý zákazník, jako je například tajné kódy, nebo za omezení prostředků na zákazníka, by místo toho máte jinou aplikaci instanci pro každého zákazníka s těmito nastaveními přepsat. 
  * Nová aplikace slouží jako hranice upgradu
    * V Service Fabric různé pojmenované instance aplikace slouží jako hranice pro upgrade. Upgrade jedné instance s názvem aplikace neovlivní kód, který je spuštěna jiná instance s názvem aplikace. Různé aplikace skončí s různými verzemi stejného kódu na stejné uzly. Pokud je třeba provést škálování rozhodnutí, protože můžete zvolit, jestli se nový kód postupujte podle stejného upgrady jako jiné služby v nebo Ne, může to být faktor. Řekněme například, že volání dorazí na správce služby, která odpovídá škálovat úlohy pro konkrétní zákazníky díky vytváření a odstraňování služby dynamicky. V tomto případě však volání je pro zatížení související s _nové_ zákazníka. Většina zákazníků jako navzájem izolované nejen z důvodů zabezpečení a konfigurace uvedených výše, ale protože nabízí větší flexibilitu z hlediska s konkrétní verzí softwaru a zvolíte, když se upgraduje. Může také vytvořit novou instanci aplikace a vytvořte službu existuje jednoduše do oddílu dalších množství služby, které bude touch jeden upgradu. Instance samostatné aplikace poskytují větší členitost při provádění upgrady aplikací a také povolit A / B testování a modrozelené nasazení. 
  * Existující instance aplikace je plná
    * V Service Fabric [kapacity aplikace](service-fabric-cluster-resource-manager-application-groups.md) je koncept, můžete použít k řízení množství prostředků, které jsou k dispozici pro konkrétní aplikaci instance. Například můžete rozhodnout, že uvedená služba musí mít jiná instance vytvořené ke škálování služby. Tato instance aplikace však je mimo kapacitu pro určité metriky. Pokud tohoto konkrétního zákazníka nebo úloha stále mají udělit další prostředky, pak vám může zvýšit existující kapacitu pro danou aplikaci nebo vytvořte novou aplikaci. 

## <a name="scaling-at-the-partition-level"></a>Škálování na úrovni oddílu
Service Fabric podporuje dělení. Dělení služby rozdělí na několik logické a fyzické oddíly, z nichž každý pracuje nezávisle na sobě. To je užitečný v případě stavové služby, protože nikdo sady replik musí zpracovat všechna volání a manipulaci se všemi stavu najednou. [Dělení přehled](service-fabric-concepts-partitioning.md) poskytuje informace o typech schémata dělení, které jsou podporovány. Repliky každý oddíl jsou rozdělené mezi uzly v clusteru, distribuci zatížení tuto službu a zajištění, že žádná služba jako celek ani oddíl má jediný bod selhání. 

Vezměte v úvahu služba, která používá ranged schéma rozdělení oddílů s dolní klíč 0, vysoká hodnota klíče 99 a počet oddílů 4. V třemi uzly clusteru může služba rozloží s čtyři replik, které sdílejí prostředky na každém uzlu, jak je znázorněno zde:

<center>

![Rozložení oddílů se třemi uzly](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Pokud zvýšíte počet uzlů, Service Fabric se přesunout některé z existujících replik. Například Řekněme Dejme tomu, že počet uzlů zvýšení čtyři, tak i repliky získat znovu distribuovat. Služba teď má teď tří replik spuštěných v jednotlivých uzlech, každý patřící do různých oddílů. To umožňuje lepší využití prostředků, protože nový uzel není úplné. Obvykle také zlepšuje výkon jako každá služba má k dispozici více prostředků.

<center>

![Rozložení oddílů se čtyřmi uzly](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Škálování s využitím Service Fabric Cluster Resource Manager a metriky
[Metriky](service-fabric-cluster-resource-manager-metrics.md) se, jak služby express jejich spotřeba prostředků se Service Fabric. Cluster Resource Manager pomocí metrik dává příležitost uspořádat a optimalizovat rozložení clusteru. Například může být spousta prostředků v clusteru, ale nemusí být přiděleny ke službám, které aktuálně provádějí práci. Použití metrik umožňuje Cluster Resource Manageru pro změnu uspořádání clusteru a ujistěte se, že služby mají přístup k prostředkům, k dispozici. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Škálování podle přidávání a odebírání uzlů z clusteru 
Další možností pro škálování s využitím Service Fabric je ke změně velikosti clusteru. Změna velikosti clusteru znamená přidávání nebo odebírání uzlů pro jeden nebo více typy uzlů v clusteru. Zvažte například případ, ve kterém jsou všechny uzly v clusteru hot. To znamená, že prostředky clusteru téměř všechny využívat. Přidání dalších uzlů do clusteru v tomto případě je nejlepší způsob, jak škálovat. Po nových uzlů připojit ke clusteru Service Fabric Cluster Resource Manager přejde služeb, což vede k méně celková zátěž existujících uzlů. Bezstavové služby se počet instancí = -1, další služby, automaticky se vytvářejí instance. To umožňuje několik volání pro přesun z existujících uzlů do nových uzlů. 

Další informace najdete v tématu [škálování clusterů](service-fabric-cluster-scaling.md).

## <a name="putting-it-all-together"></a>Spojení všech součástí dohromady
Pojďme se na všechny nápady, které jsme probrali tady a komunikovat prostřednictvím příklad. Vezměte v úvahu následující služby: Pokoušíte se vytvořit službu, která funguje jako adresář, udržuje názvy a kontaktní údaje. 

Přímo před jeho zahájením máte spoustu pro otázky týkající se škálování: Počet uživatelů, kteří budou mít? Kolik kontakty se každý uživatel uložit? To vše na lámete když jsou připraveni instalace služby poprvé je obtížné. Řekněme, že se chystáte přejděte s jednou službou statické s počtem konkrétního oddílu. Důsledky výběr počtu oddílů nesprávné může vést k problémů škálování později. Podobně i v případě, že vyberete správný počet, nemusí mít všechny informace budete potřebovat. Například máte také rozhodnout velikost clusteru ještě před zahájením, z hlediska počtu uzlů a jejich velikosti. Je obvykle těžké předpovědět, kolik prostředků služba bude využívat za dobu života. Také může být obtížné vědět předem, provoz vzor, který služba zobrazí ve skutečnosti. Možná uživatelé přidávat a odebírat si kontakty pouze první thing ráno nebo možná ji jsou rovnoměrně v průběhu dne. Na základě toho, které možná budete muset dynamicky vertikálně navýšení nebo snížení kapacity. Možná se můžete naučit předvídá, jestli budete muset vertikálně navýšení nebo snížení kapacity, ale v obou případech pravděpodobně budete muset reagovat na měnící se spotřeby prostředků vaší službou. To může zahrnovat změny velikosti clusteru, aby bylo možné poskytnout další prostředky při reorganizace použití existujících prostředků není k dispozici dostatek. 

Ale Proč se ani nepokusí k výběru jednoho oddílu schéma pro všechny uživatele? Proč omezit pouze na jednu službu a jedna statická clusteru? Reálné situace je obvykle dynamičtější. 

Při sestavování pro škálování, vezměte v úvahu následující dynamický vzorek. Je třeba ji přizpůsobili své situaci:

1. Namísto pokusu o vyberte schéma rozdělení oddílů pro každého ještě před zahájením, vytvářejte "manager service".
2. Úlohy ve službě správce můžete se podívat na informace o zákaznících při registraci pro vaši službu. V závislosti na těchto informací ve službě správce vytvořit instanci vaší _skutečné_ službu storage kontakt _jenom pro tohoto zákazníka_. Pokud se vyžadují konkrétní konfiguraci, izolaci nebo upgrady, můžete také rozhodnout zprovoznění instance aplikace pro tohoto zákazníka. 

Tato dynamické vytváření vzorku řadu výhod:

  - Nepokoušíte odhadnout počet správné oddílů pro všechny uživatele ještě před zahájením nebo vytvoření jedné služby, který je neomezeně škálovatelné všechny sama o sobě. 
  - Různí uživatelé nemusí mít stejný počet oddílů, počet replik, omezení umístění, metriky, načítání výchozí, názvy služeb, nastavení dns nebo všechny ostatní vlastnosti zadaný na úrovni služby nebo aplikace. 
  - Můžete získat další data segmentace. Každý zákazník má své vlastní kopii služby
    - Každý zákazník služby můžete nakonfigurovat různě a uděleno více nebo méně prostředků s více nebo méně oddílů nebo repliky podle potřeby podle jejich očekávaného rozsahu.
      - Například Řekněme, že zákazník platili za "Zlatá" vrstva - může dostanou více replik nebo větší počet oddílů a potenciálně prostředků vyhrazených ke službám prostřednictvím kapacity metriky a aplikace.
      - Nebo Řekněme, že se za předpokladu, že informace o tom počet kontakty, které v případě potřeby zapíná se "Malá" – pouze několik oddílů byste získali, nebo dokonce možné zařadit do fondu sdílených služeb s jinými zákazníky.
  - Zatímco čekáte pro zákazníky, kteří se zobrazí nespouštíte spoustu instance služby ani repliky
  - Pokud zákazník jenom na chvíli, jeho informace o odebrání služby je stejně jednoduché jako správci odstranit tuto službu nebo aplikaci, která je vytvořená.

## <a name="next-steps"></a>Další postup
Další informace o konceptech Service Fabric najdete v následujících článcích:

* [Dostupnost služeb Service Fabric](service-fabric-availability-services.md)
* [Dělení služeb Service Fabric](service-fabric-concepts-partitioning.md)

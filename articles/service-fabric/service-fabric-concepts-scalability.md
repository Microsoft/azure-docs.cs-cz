---
title: Škálovatelnost služeb Service Fabric
description: Další informace o škálování ve službě Azure Fabric a různé techniky používané pro škálování aplikací.
author: masnider
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: masnider
ms.openlocfilehash: 17827342b67d37d9fbeb56654824e004367823ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282559"
---
# <a name="scaling-in-service-fabric"></a>Změna velikosti v service fabric
Azure Service Fabric usnadňuje vytváření škálovatelných aplikací pomocí správy služeb, oddílů a replik na uzlech clusteru. Spuštění mnoha úloh na stejném hardwaru umožňuje maximální využití prostředků, ale také poskytuje flexibilitu, pokud jde o způsob škálování úloh. Toto video kanálu 9 popisuje, jak můžete vytvářet škálovatelné aplikace mikroslužeb:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

Škálování ve službě Fabric se provádí několika různými způsoby:

1. Škálování vytvořením nebo odebráním bezstavových instancí služby
2. Škálování vytvořením nebo odebráním nových pojmenovaných služeb
3. Změna měřítka vytvořením nebo odebráním nových instancí pojmenovaných aplikací
4. Škálování pomocí dělených služeb
5. Změna velikosti přidáním a odebráním uzlů z clusteru 
6. Škálování pomocí metrik Správce prostředků clusteru

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Škálování vytvořením nebo odebráním bezstavových instancí služby
Jeden z nejjednodušších způsobů škálování v rámci Service Fabric pracuje s bezstavové služby. Při vytváření bezstavové služby, máte možnost `InstanceCount`definovat . `InstanceCount`definuje, kolik spuštěných kopií kódu této služby je vytvořeno při spuštění služby. Řekněme například, že v clusteru je 100 uzlů. Řekněme také, že služba je `InstanceCount` vytvořena s 10. Během běhu těchto 10 spuštěných kopií kódu může být příliš zaneprázdněno (nebo nemusí být dostatečně zaneprázdněno). Jedním ze způsobů škálování tohoto pracovního vytížení je změnit počet instancí. Například některé části monitorování nebo management kód můžete změnit existující počet instancí na 50 nebo 5, v závislosti na tom, zda zatížení je třeba škálovat v nebo mimo na základě zatížení. 

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
### <a name="using-dynamic-instance-count"></a>Použití dynamického počtu instancí
Speciálně pro bezstavové služby Service Fabric nabízí automatický způsob, jak změnit počet instancí. To umožňuje službě dynamicky škálovat s počtem uzlů, které jsou k dispozici. Způsob, jak se přihlásit do tohoto chování je nastavit počet instancí = -1. InstanceCount = -1 je instrukce service fabric, který říká: "Spustit tuto bezstavovou službu na každém uzlu." Pokud se změní počet uzlů, Service Fabric automaticky změní počet instancí tak, aby odpovídaly, zajištění, že služba je spuštěna na všech platných uzlů. 

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

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Škálování vytvořením nebo odebráním nových pojmenovaných služeb
Instance pojmenované služby je konkrétní instance typu služby (viz [Životní cyklus aplikace Service Fabric)](service-fabric-application-lifecycle.md)v rámci některé instance pojmenované aplikace v clusteru. 

Nové instance pojmenované služby lze vytvořit (nebo odebrat), protože služby jsou více či méně zaneprázdněné. To umožňuje požadavky, které mají být rozloženy mezi více instancí služby, obvykle umožňuje zatížení stávajících služeb snížit. Při vytváření služeb umístí Správce prostředků clusteru Service Fabric služby služby do clusteru distribuovaným způsobem. Přesná rozhodnutí se řídí [metrikami](service-fabric-cluster-resource-manager-metrics.md) v clusteru a dalšími pravidly umístění. Služby lze vytvořit několika různými způsoby, ale nejběžnější jsou [`New-ServiceFabricService`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)buď prostřednictvím [`CreateServiceAsync`](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet)akcí pro správu, jako je volání uživatele , nebo volání kódu . `CreateServiceAsync`lze dokonce volat z jiných služeb spuštěných v clusteru.

Dynamické vytváření služeb lze použít ve všech typech scénářů a je běžný vzor. Zvažte například stavovou službu, která představuje konkrétní pracovní postup. Volání představující práci se zobrazí do této služby a tato služba se chystá provést kroky k tomuto pracovnímu postupu a zaznamenat průběh. 

Jak byste vytvořili tuto konkrétní škálu služeb? Služba může být víceklientské v nějaké formě a přijímat hovory a zahájit kroky pro mnoho různých instancí stejného pracovního postupu najednou. To však může kód zkomplikovat, protože nyní se musí starat o mnoho různých instancí stejného pracovního postupu, a to vše v různých fázích a od různých zákazníků. Také zpracování více pracovních postupů současně nevyřeší problém škálování. Důvodem je, že v určitém okamžiku tato služba bude spotřebovávat příliš mnoho prostředků, aby se vešly na konkrétní počítač. Mnoho služeb, které nejsou vytvořeny pro tento vzor v první řadě také potíže z důvodu některých vlastní choulek nebo zpomalení v jejich kódu. Tyto typy problémů způsobit, že služba nebude fungovat také při počtu souběžných pracovních postupů, které je sledování získá větší.  

Řešením je vytvořit instanci této služby pro každou jinou instanci pracovního postupu, který chcete sledovat. To to je skvělý vzor a funguje bez stavové nebo stavové. Pro tento vzor pracovat, je obvykle jinou službu, která funguje jako "služba Správce úloh". Úkolem této služby je přijímat požadavky a směrovat tyto požadavky do jiných služeb. Správce může dynamicky vytvořit instanci služby pracovního vytížení, když obdrží zprávu a pak předat požadavky na tyto služby. Služba správce může také přijímat zpětná volání, když daná služba pracovního postupu dokončí svou úlohu. Když správce obdrží tato zpětná volání, může odstranit tuto instanci služby pracovního postupu nebo ji ponechat, pokud se očekává více volání. 

Rozšířené verze tohoto typu správce můžete dokonce vytvořit fondy služeb, které spravuje. Fond pomáhá zajistit, že když přijde nový požadavek, nemusí čekat na to, až se služba rozběhne. Místo toho může správce vybrat službu pracovního postupu, která není aktuálně zaneprázdněna z fondu, nebo směrovat náhodně. Udržování fondu služeb k dispozici umožňuje zpracování nových požadavků rychleji, protože je méně pravděpodobné, že požadavek musí čekat na novou službu, která má být převrácena. Vytváření nových služeb je rychlé, ale ne zdarma nebo okamžité. Fond pomáhá minimalizovat dobu, po kterou musí požadavek čekat před obsluhou. Často se zobrazí tento vzor správce a fondu, když záleží nejvíce na časech odezvy. Řazení požadavků do fronty a vytvoření služby na pozadí a _její předání_ je také oblíbeným vzorem správce, stejně jako vytváření a mazání služeb na základě určitého sledování množství práce, kterou služba aktuálně čeká na vyřízení. 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Změna měřítka vytvořením nebo odebráním nových instancí pojmenovaných aplikací
Vytváření a odstranění celých instancí aplikace je podobné vzoru vytváření a mazání služeb. Pro tento vzor je některé služby správce, který dělá rozhodnutí na základě požadavků, které je vidět a informace, které přijímá z jiných služeb uvnitř clusteru. 

Kdy by mělo být místo vytváření nových instancí pojmenované služby použito vytvoření nové instance pojmenované služby v některých již existujících aplikacích? Je tu pár případů:

  * Nová instance aplikace je pro zákazníka, jehož kód musí být spuštěn pod určitou konkrétní identitou nebo nastavením zabezpečení.
    * Service Fabric umožňuje definovat různé balíčky kódu spustit pod konkrétní identity. Chcete-li spustit stejný balíček kódu pod různými identitami, aktivace musí dojít v různých instancích aplikace. Zvažte případ, kdy máte nasazené úlohy existujícího zákazníka. Ty mohou být spuštěny pod určitou identitou, takže můžete sledovat a řídit jejich přístup k jiným prostředkům, jako jsou vzdálené databáze nebo jiné systémy. V takovém případě, když se zaregistruje nový zákazník, pravděpodobně nechcete aktivovat jeho kód ve stejném kontextu (procesní prostor). I když byste mohli, to ztěžuje kód služby jednat v kontextu určité identity. Obvykle musí mít více zabezpečení, izolace a kód pro správu identit. Namísto použití různých pojmenovaných instancí služby v rámci stejné instance aplikace a tím i stejného prostoru procesu můžete použít různé instance aplikace s názvem Service Fabric. To usnadňuje definování různých kontextů identity.
  * Nová instance aplikace slouží také jako prostředek konfigurace
    * Ve výchozím nastavení budou všechny instance pojmenované služby určitého typu služby v rámci instance aplikace spuštěny ve stejném procesu v daném uzlu. To znamená, že zatímco můžete nakonfigurovat každou instanci služby jinak, je to složité. Služby musí mít nějaký token, který používají k vyhledat jejich config v rámci konfigurační balíček. Obvykle je to jen název služby. To funguje dobře, ale spárovat konfiguraci s názvy jednotlivých pojmenovaných instancí služby v rámci této instance aplikace. To může být matoucí a těžko spravovat, protože konfigurace je obvykle artefakt čas návrhu s hodnotami specifické pro instanci aplikace. Vytvoření více služeb vždy znamená další upgrady aplikací pro změnu informací v rámci balíčků konfigurace nebo nasazení nových, aby nové služby mohly vyhledat jejich konkrétní informace. Často je jednodušší vytvořit zcela novou instanci pojmenované aplikace. Pak můžete použít parametry aplikace k nastavení libovolné konfigurace, která je nezbytná pro služby. Tímto způsobem všechny služby, které jsou vytvořeny v rámci této instance pojmenované aplikace může dědit konkrétní nastavení konfigurace. Například místo jednoho konfiguračního souboru s nastavením a přizpůsobením pro každého zákazníka, například tajných kódů nebo omezení prostředků zákazníka, byste místo toho měli pro každého zákazníka jinou instanci aplikace s tímto nastavením Přepsání. 
  * Nová aplikace slouží jako hranice upgradu
    * V rámci Service Fabric, různé pojmenované instance aplikace slouží jako hranice pro upgrade. Inovace jedné instance pojmenované aplikace nebude mít vliv na kód, který je spuštěn jinou instanci pojmenované aplikace. Různé aplikace skončí spuštěním různých verzí stejného kódu ve stejných uzlech. To může být faktor, když potřebujete provést rozhodnutí škálování, protože můžete zvolit, zda nový kód by měl následovat stejné upgrady jako jiné služby, nebo ne. Řekněme například, že volání dorazí do služby správce, která je zodpovědná za škálování úloh konkrétního zákazníka dynamickým vytvářením a odstraňováním služeb. V tomto případě je však volání pro úlohy přidružené k _novému_ zákazníkovi. Většina zákazníků je ráda izolována nejen z důvodů zabezpečení a konfigurace uvedených dříve, ale proto, že poskytuje větší flexibilitu, pokud jde o spuštění konkrétních verzí softwaru a výběr při upgradu. Můžete také vytvořit novou instanci aplikace a vytvořit službu tam jednoduše k dalšímu rozdělení množství vašich služeb, které jeden upgrade bude dotýkat. Samostatné instance aplikace poskytují větší rozlišovací schopnost při provádění upgradů aplikací a také umožňují testování A/B a modré/zelené nasazení. 
  * Existující instance aplikace je plná
    * V Service Fabric, [kapacita aplikace](service-fabric-cluster-resource-manager-application-groups.md) je koncept, který můžete použít k řízení množství prostředků, které jsou k dispozici pro konkrétní instance aplikace. Můžete se například rozhodnout, že daná služba musí mít jinou instanci vytvořenou pro škálování. Tato instance aplikace je však mimo kapacitu pro určitou metriku. Pokud by tomuto konkrétnímu zákazníkovi nebo pracovnímu vytížení mělo být stále přiděleno více prostředků, můžete buď zvýšit stávající kapacitu pro tuto aplikaci, nebo vytvořit novou aplikaci. 

## <a name="scaling-at-the-partition-level"></a>Změna velikosti na úrovni oddílu
Service Fabric podporuje dělení. Dělení rozdělí službu do několika logických a fyzických částí, z nichž každý pracuje nezávisle. To je užitečné u stavových služeb, protože žádná sada replik nemá ke zpracování všech volání a manipulaci se všemi stavy najednou. [Přehled dělení](service-fabric-concepts-partitioning.md) poskytuje informace o typech schémat dělení, které jsou podporovány. Repliky každého oddílu jsou rozloženy mezi uzly v clusteru, distribuci zatížení této služby a zajištění, že služba jako celek nebo jakýkoli oddíl má jeden bod selhání. 

Zvažte službu, která používá schéma dělení v rozsahu s nízkým klíčem 0, vysokým klíčem 99 a počtem oddílů 4. V clusteru se třemi uzlovými daty může být služba rozložena se čtyřmi replikami, které sdílejí prostředky v každém uzlu, jak je znázorněno zde:

<center>

![Rozložení oddílu se třemi uzly](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Pokud zvýšíte počet uzlů, Service Fabric přesune některé existující repliky tam. Řekněme například, že počet uzlů se zvýší na čtyři a repliky získat redistributed. Nyní služba má nyní tři repliky spuštěné na každém uzlu, z nichž každá patří do různých oddílů. To umožňuje lepší využití prostředků, protože nový uzel není zastudena. Obvykle také zlepšuje výkon jako každá služba má k dispozici více prostředků.

<center>

![Rozložení oddílu se čtyřmi uzly](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Škálování pomocí Správce prostředků clusteru prostředků clusteru Service Fabric a metriky
[Metriky](service-fabric-cluster-resource-manager-metrics.md) jsou způsob, jakým služby vyjadřují spotřebu prostředků service fabric. Použití metrik dává Správci prostředků clusteru příležitost změnit uspořádání a optimalizaci rozložení clusteru. Například může být spousta prostředků v clusteru, ale nemusí být přiděleny služby, které jsou aktuálně práci. Použití metrik umožňuje Správci prostředků clusteru změnit uspořádání clusteru a zajistit tak, aby služby měly přístup k dostupným prostředkům. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Změna velikosti přidáním a odebráním uzlů z clusteru 
Další možností pro škálování pomocí Service Fabric je změna velikosti clusteru. Změna velikosti clusteru znamená přidání nebo odebrání uzlů pro jeden nebo více typů uzlů v clusteru. Zvažte například případ, kdy jsou všechny uzly v clusteru horké. To znamená, že prostředky clusteru jsou téměř všechny spotřebované. V tomto případě přidání dalšíu uzly do clusteru je nejlepší způsob, jak škálovat. Jakmile se nové uzly připojí ke clusteru, Správce prostředků clusteru Service Fabric přesune do nich služby, což vede k menšímu celkovému zatížení existujících uzlů. Pro bezstavové služby s počtem instancí = -1 jsou automaticky vytvořeny další instance služby. To umožňuje některé volání přesunout z existujících uzlů do nových uzlů. 

Další informace naleznete v [tématu škálování clusteru](service-fabric-cluster-scaling.md).

## <a name="choosing-a-platform"></a>Výběr platformy

Vzhledem k rozdílům v implementaci mezi operačními systémy může být důležitou součástí škálování aplikace možnost použití service fabric u Windows nebo Linuxu. Jednou z potenciálních překážek je způsob provádění fázovaného protokolování. Service Fabric v systému Windows používá ovladač jádra pro protokol jednoho počítače, sdílený mezi replikami stavové služby. Tento protokol váží asi 8 GB. Linux, na druhé straně používá 256 MB pracovní protokol pro každou repliku, takže je méně ideální pro aplikace, které chtějí maximalizovat počet zjednodušené repliky služby spuštěné na daném uzlu. Tyto rozdíly v požadavcích na dočasné úložiště by mohly potenciálně informovat požadovanou platformu pro nasazení clusteru Service Fabric.

## <a name="putting-it-all-together"></a>Spojení všech součástí dohromady
Vezměme si všechny nápady, které jsme diskutovali zde a mluvit prostřednictvím příkladu. Zvažte následující službu: pokoušíte se vytvořit službu, která funguje jako adresář, a uchováváte jména a kontaktní informace. 

Přímo vpředu máte spoustu otázek týkajících se rozsahu: Kolik uživatelů budete mít? Kolik kontaktů bude každý uživatel ukládat? Snažit se přijít na to všechno, když jste vstávání své služby poprvé, je obtížné. Řekněme, že jste se chystali jít s jedinou statickou službou s konkrétním počtem oddílů. Důsledky výběru počet nesprávný oddíl může způsobit, že budete mít problémy škálování později. Podobně, i když si vyberete správný počet, nemusíte mít všechny informace, které potřebujete. Například musíte také rozhodnout o velikosti clusteru dopředu, a to jak z hlediska počtu uzlů a jejich velikosti. Obvykle je těžké předpovědět, kolik prostředků služba spotřebuje během své životnosti. To může být také těžké vědět dopředu dopravní vzor, který služba skutečně vidí. Například, možná lidé přidat a odstranit své kontakty pouze první věc, kterou ráno, nebo možná je to distribuovánrovnoměrně v průběhu dne. Na základě toho možná budete muset horizontální navýšení kapacity a dynamicky. Možná se můžete naučit předvídat, kdy budete muset horizontální navýšení kapacity a dovnitř, ale tak jako tak budete pravděpodobně muset reagovat na měnící se spotřebu zdrojů vaší službou. To může zahrnovat změnu velikosti clusteru za účelem poskytnutí více prostředků při reorganizaci využití existujících prostředků nestačí. 

Ale proč se dokonce snaží vybrat jeden oddíl schéma pro všechny uživatele? Proč se omezovat na jednu službu a jeden statický cluster? Skutečná situace je obvykle dynamičtější. 

Při vytváření měřítka zvažte následující dynamický vzor. Možná budete muset přizpůsobit své situaci:

1. Místo toho, aby se snaží vybrat schéma dělení pro každého dopředu, vybudovat "manažer služby".
2. Úkolem služby manažera je podívat se na informace o zákaznících, když se zaregistrují k vaší službě. V závislosti na těchto informacích pak služba správce vytvoří instanci _skutečné_ služby kontaktního úložiště _pouze pro tohoto zákazníka_. Pokud vyžadují konkrétní konfiguraci, izolaci nebo upgrady, můžete se také rozhodnout pro spuštění instance aplikace pro tohoto zákazníka. 

Tento model dynamického vytváření mnoho výhod:

  - Nepokoušíte se odhadnout správný počet oddílů pro všechny uživatele předem nebo vytvořit jednu službu, která je nekonečně škálovatelná sama o sobě. 
  - Různí uživatelé nemusí mít stejný počet oddílů, počet replik, omezení umístění, metriky, výchozí zatížení, názvy služeb, nastavení DNS nebo některou z dalších vlastností zadaných na úrovni služby nebo aplikace. 
  - Získáte další segmentaci dat. Každý zákazník má vlastní kopii služby
    - Každý zákaznický servis lze nakonfigurovat odlišně a udělena více nebo méně prostředků, s více nebo méně oddílů nebo replikpodle potřeby na základě jejich očekávané škálování.
      - Řekněme například, že zákazník zaplatil za úroveň "Gold" – mohl by získat více replik nebo větší počet oddílů a potenciálně prostředky vyhrazené pro jejich služby prostřednictvím metrik a kapacit aplikací.
      - Nebo říkají, že poskytli informace o počtu kontaktů, které potřebovali, byl "Malý" - dostali by jen několik oddílů nebo by mohli být dokonce zařazeni do sdíleného fondu služeb s ostatními zákazníky.
  - Nespouštět spoustu instancí nebo replik služby, zatímco čekáte, až se zákazníci zobrazí.
  - Pokud zákazník někdy odejde, odebrání jeho informací z vaší služby je stejně jednoduché jako odstranění této služby nebo aplikace, kterou vytvořil, správcem.

## <a name="next-steps"></a>Další kroky
Další informace o konceptech service fabric naleznete v následujících článcích:

* [Dostupnost služeb Service Fabric](service-fabric-availability-services.md)
* [Služby partitioning Service Fabric](service-fabric-concepts-partitioning.md)

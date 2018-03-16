---
title: "Škálovatelnost služby Service Fabric | Microsoft Docs"
description: "Popisuje postup škálování služby Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: aeda1184610398c0445238ea2e7ccbea866ed418
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="scaling-in-service-fabric"></a>Nastavení velikosti v Service Fabric
Azure Service Fabric lze snadno vytvářet škálovatelné aplikace pomocí správy služeb, oddíly a repliky na uzlech clusteru. Spuštění řadu úloh na stejném hardwaru umožňuje využití maximální prostředků, ale také poskytuje flexibilitu z hlediska jak rozhodnete škálování vašich úloh. Toto video Channel 9 popisuje, jak mohou vytvářet škálovatelné mikroslužeb aplikace:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

Nastavení velikosti v Service Fabric se provádí několika různými způsoby:

1. Škálování vytvořením nebo odebrání instance bezstavové služby
2. Škálování podle vytváření nebo odebírání nové služby s názvem
3. Škálování podle vytváření nebo odebírání nové pojmenované instance aplikace
4. Škálování pomocí oddílů služby
5. Škálování podle přidávání a odebírání uzlů z clusteru 
6. Pomocí Správce prostředků clusteru metriky škálování

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Škálování vytvořením nebo odebrání instance bezstavové služby
Jedním z nejjednodušší způsobů škálování v Service Fabric funguje s bezstavové služby. Když vytvoříte bezstavové služby, zobrazí se možnost definovat `InstanceCount`. `InstanceCount` Určuje, kolik kopií spuštěného kódu této služby se vytvoří při spuštění služby. Řekněme například, že jsou 100 uzly v clusteru. Dále předpokládejme, že je služba vytvořena s `InstanceCount` 10. Během doby běhu tyto 10 spuštěná kopie kódu by se mohly všechny stát příliš zaneprázdněn (nebo může není dostatečně zaneprázdněn). Jedním ze způsobů škálování této úlohy je možné změnit počet instancí. Například některé část kódu, monitorování nebo správu můžete změnit stávající počet instancí, 50, nebo 5, v závislosti na tom, jestli zatížení potřeba škálování příchozí nebo odchozí podle zatížení. 

C#:

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Prostředí PowerShell:

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Použití dynamické počet instancí
Konkrétně pro bezstavové služby Service Fabric nabízí automatické způsob, jak změnit počet instancí. To umožňuje službě dynamicky škálování počtu uzlů, které jsou k dispozici. Způsobem, který se přidá do toto chování je nastavit počet instancí = -1. InstanceCount = -1 je instrukce pro Service Fabric, která říká "Spustit tento bezstavové služby na všech uzlech." Pokud se změní počet uzlů, Service Fabric automaticky změní počet instancí pro shodu, zajistíte, že služba běží na všech uzlech, platný. 

C#:

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

Prostředí PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Škálování podle vytváření nebo odebírání nové služby s názvem
Instance s názvem služby je konkrétní instanci typu služby (viz [životního cyklu aplikace Service Fabric](service-fabric-application-lifecycle.md)) v rámci některé instance s názvem aplikace v clusteru. 

Nové služby s názvem instance může vytvořit (nebo odstranit) jako služby stát více nebo méně zaneprázdněn. To umožňuje, aby možné rozdělit do více instancí služby, obvykle umožňuje zatížení na stávající služby, aby se snížila požadavky. Při vytváření služby, správce prostředků clusteru Service Fabric umístí služby v clusteru, distribuované způsobem. Přesný rozhodnutí, která se řídí [metriky](service-fabric-cluster-resource-manager-metrics.md) v clusteru a další pravidla pro umístění. Služby můžete vytvořit několik různých způsobů, ale většina běžných jsou buď prostřednictvím akce správy, jako je někdo volání [ `New-ServiceFabricService` ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps), nebo kód volání [ `CreateServiceAsync` ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet). `CreateServiceAsync` Můžete dokonce volat z v rámci jiných služeb spuštěných v clusteru.

Vytváření služby dynamicky mohou být používány nejrůznějším scénářům a je běžný vzor. Představte si třeba stavové služby, který představuje určitý pracovní postup. Volání představující práci se chystáte objeví k této službě a tato služba bude provést kroky pro tento pracovní postup a záznam průběh. 

By se tak tohoto měřítka konkrétní službu? Služba může být více klientů v některé formuláře a přijímat volání a všechny najednou ji kroky pro velký počet různých instancí stejného pracovního postupu. Ale, který můžete nastavit kód složitější, protože teď má si dělat starosti velký počet různých instancí stejného pracovního postupu, všechny v různých fázích a z různých zákazníků. Navíc zpracování více pracovních postupů ve stejnou dobu škálování problém nevyřeší. Je to proto, že v určitém okamžiku bude tuto službu využívat příliš mnoho prostředků pro konkrétní počítač. Mnoho služeb není vytvořené pro tento vzor nejprve taky nastat potíže z důvodu některých vyplývajících problémové místo nebo zpomalení v svůj kód. Tyto typy problémů způsobit službu tak, aby fungovat tak dobře, když počet souběžných pracovních postupů, které je sledování získá větší.  

Řešení je k vytvoření instance této služby pro všechny různé instance pracovního postupu, které chcete sledovat. Toto je skvělým vzor a funguje, zda je služba bezstavové nebo stateful. Pro tento vzor pro práci je obvykle jiné službě, která funguje jako "Služba Správce úloh". Úloha této služby je přijímat požadavky a tyto požadavky směrování do dalších služeb. Správce můžete dynamicky vytvořit instance služby zatížení, když obdrží zprávu a pak předejte u požadavků do těchto služeb. Služba Správce může zobrazit i zpětná volání po dokončení úlohy služby daného pracovního procesu. Když správce obdrží tato zpětná volání mohla odstranit tuto instanci služby pracovního postupu, nebo necháte, pokud se očekává více volání. 

Pokročilé verze tohoto typu správce můžete dokonce vytvářet fondy služeb, které spravuje. Fondu pomáhá zajistit, že při přijetí nového požadavku nemá čekání na službu, kterou chcete začne pracovat. Místo toho správce můžete právě vyberte služby pracovního postupu, který není aktuálně zaneprázdněn z fondu, nebo náhodně trasy. Zachování fond dostupnost služeb díky nové žádosti o zpracování rychlejší, vzhledem k tomu, že je méně pravděpodobné, že žádost má čekat na se spouštějí novou službu. Vytvoření nové služby je rychlý, ale není volné nebo okamžitou. Fond se může minimalizovat dobu, kterou má požadavek se má čekat před probíhá údržba. Tento správce a fondu vzor zobrazí často při odezvy vás nejvíc. Vytváření služby na pozadí a zpracování požadavku a _pak_ předávání také oblíbených manager vzor, jako je vytváření a odstraňování služeb aplikace založené na některé sledování množství práce, že služba aktuálně má čekající na vyřízení . 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Škálování podle vytváření nebo odebírání nové pojmenované instance aplikace
Vytváření a odstraňování instancí celou aplikaci je podobná vzor vytváření a odstraňování služeb aplikace. U tohoto vzoru se některé manager service, která je zajistit, že rozhodnutí, na základě požadavků, které se zobrazuje a informace, že přijímá z jiných služeb v clusteru. 

Při vytvoření nové instance s názvem aplikace použít místo vytvoření nové instance s názvem služby v některé stávající aplikaci? Existuje několik případů:

  * Nová instance aplikace je pro zákazníka, jejíž kód je potřeba spustit pod některé konkrétní identity nebo nastavení zabezpečení.
    * Service Fabric umožňuje definovat balíčky různý kód pro spuštění v konkrétní identity. Aby bylo možné spustit balíček kódu pod různými identitami, aktivace nutné dochází k výskytu instancí jinou aplikaci. Představte si případ, kdy máte stávající zákazník úlohy nasazené. To může být spuštěna konkrétní identitou, vám umožní monitorovat a kontrolovat jejich přístup k jiným prostředkům, jako jsou třeba vzdálené databáze nebo jinými systémy. V takovém případě pokud nové zákazník zaregistruje, pravděpodobně nechcete aktivovat svůj kód v rámci stejné (proces místa). Když vám může tento ztěžuje kódu služby tak, aby fungoval v kontextu konkrétní identity. Obvykle musíte mít další zabezpečení, izolace a kódu správy identit. Místo použití různých s názvem instance služby v rámci stejné instance aplikace a proto stejný proces místa, můžete použít různé instance s názvem aplikace Service Fabric. To usnadňuje definovat různé identity kontexty.
  * Nová instance aplikace slouží taky jako způsob konfigurace
    * Ve výchozím nastavení všechny instance služby s názvem typu konkrétní služby v rámci instance aplikace se spustí v rámci jednoho procesu v daném uzlu. To znamená, že každá instance služby nakonfigurovat různě, takový postup je složitá. Musí mít některé token, které používají pro vyhledání jejich konfigurace v rámci konfigurace balíčku. Obvykle je to právě název služby. To funguje bez problémů, ale jeho páry v odstupu konfigurace na názvy instancí jednotlivých s názvem služby v rámci této instance aplikace. To může být matoucí a obtížné spravovat vzhledem k tomu, že konfigurace je obvykle artefaktem návrhu času s konkrétními hodnotami instanci aplikace. Vytvoření další služby vždy znamená více upgradů aplikací a změnit informace v rámci konfigurace balíčky nebo pro nasazení nové tak, aby nové služby můžete vyhledat jejich konkrétní informace. Často je snazší vytvářet zcela nový instanci s názvem aplikace. Potom parametry aplikace můžete nastavit libovolnou konfigurace je nezbytná pro služby. Tímto způsobem všechny služby, které jsou vytvořené v rámci které pojmenovanou instanci aplikace lze dědit nastavení konkrétní konfigurace. Například místo nutnosti jednom konfiguračním souboru s nastavením a vlastní nastavení pro každou zákazníka, jako je například tajných klíčů nebo za zákazníka omezení prostředků, byste místo toho máte instanci jinou aplikaci pro každého zákazníka a to s těmito nastaveními přepsat. 
  * Nová aplikace slouží jako hranice upgradu
    * V rámci Service Fabric jinou aplikaci s názvem instance slouží jako hranice pro upgrade. Upgrade jedné instance s názvem aplikace nebudou mít vliv na kód, který je spuštěna jiná instance aplikace s názvem. Různé aplikace dojdete k s různými verzemi stejného kódu na stejném uzlu. To může být faktorem, když potřebujete učinit rozhodnutí škálování, protože můžete vybrat, zda nový kód postupujte stejným upgrady jako jiná služba nebo ne. Řekněme například, že volání dorazí na službu správce, který je zodpovědný za vytváření a odstraňování služeb aplikace dynamicky škálování úloh konkrétního zákazníka. V takovém případě však volání je pro zatížení přidružené _nové_ zákazníka. Většina zákazníků jako navzájem izolované nejen z důvodů zabezpečení a konfigurace uvedených výše, ale protože poskytuje větší flexibilitu z hlediska spuštění konkrétních verzí softwaru a výběr při získání upgradovat. Také můžete vytvořit novou instanci aplikace a vytvořte službu existuje jednoduše tak, aby další oddíl vaší služby, které bude touch jakéhokoli jeden upgradu. Instance samostatné aplikace poskytují větší členitost při provádění upgradu aplikace a také povolit A / B testování a modrá nebo zelená nasazení. 
  * Existující instance aplikace je plná
    * V Service Fabric [kapacity aplikace](service-fabric-cluster-resource-manager-application-groups.md) je konceptu, které můžete použít k řízení množství prostředků, které jsou k dispozici pro konkrétní aplikaci instance. Například může rozhodnout, že uvedená služba musí mít jiná instance vytvořeny za účelem škálování. Tato instance aplikace je však mimo kapacity pro určité metriky. Pokud tento konkrétní zákazník nebo zatížení stále udělení více prostředků, pak můžete buď zvýšit existující kapacitu pro tuto aplikaci nebo vytvořte novou aplikaci. 

## <a name="scaling-at-the-partition-level"></a>Škálování na úrovni oddílu
Dělení na oddíly podporuje Service Fabric. Vytváření oddílů rozdělí služby do několika oddílů logické a fyzické, z nichž každý pracuje nezávisle. To je užitečný v případě stavové služby, protože žádná sada replik má ke zpracování všechna volání a stavu všechny najednou upravit. [Dělení přehled](service-fabric-concepts-partitioning.md) poskytuje informace o typech rozdělení schémat, které jsou podporovány. Repliky každý oddíl jsou rozprostřeny mezi uzly v clusteru, distribuci zatížení tuto službu a zajistíte, že ani služby jako celek nebo oddíl má jediný bod selhání. 

Zvažte službu, která používá ranged schéma rozdělení oddílů s nízkou klíč 0, vysoká hodnota klíče 99 a počet oddílů na 4. V tři uzly clusteru může být nastíněny službu s čtyři replikami, které sdílejí prostředky na každém uzlu, jak je vidět tady:

<center>
![Rozložení oddílů s tři uzly](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Pokud zvýšíte počet uzlů, Service Fabric přesune některé z existujících replik existuje. Například můžeme například počet uzlů zvýší na čtyři, tak i repliky získat znovu distribuovat. Služba teď má teď tři repliky spuštěna na každém uzlu, každý patřící do různých oddílů. To umožňuje lepší využití prostředků, vzhledem k tomu, že není cold nový uzel. Obvykle se také zlepšuje výkon jako každá služba má dostupné další prostředky.

<center>
![Rozložení oddílů se čtyřmi uzly](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Škálování pomocí Správce prostředků clusteru Service Fabric a metriky
[Metriky](service-fabric-cluster-resource-manager-metrics.md) jsou jak služby express jejich spotřeba prostředků do Service Fabric. Správce prostředků clusteru pomocí metriky dává příležitost reorganizovat a optimalizovat rozložení clusteru. Například může být dost prostředků v clusteru, ale nemusí být přidělené k službám, které aktuálně pracujeme. Použití metriky umožňuje Správce prostředků clusteru reorganizace clusteru zajistěte, aby služby měly přístup k dostupné prostředky. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Škálování podle přidávání a odebírání uzlů z clusteru 
Další možností škálování s Service Fabric je ke změně velikosti clusteru. Změna velikosti clusteru znamená přidávání a odebírání uzlů pro jeden nebo více typy uzlů v clusteru. Například Představte si případ, kde jsou všechny uzly v clusteru aktivní. To znamená, zda jsou prostředky clusteru téměř všechny využívat. Přidání více uzlů do clusteru v tomto případě je nejlepší způsob, jak škálování. Po připojení ke clusteru nové uzly Správce prostředků clusteru Service Fabric přesune služby, výsledkem je menší celkové zatížení na existujícím uzlům. Pro bezstavové služby se počet instancí = -1, další služby, jsou automaticky vytvořeny instance. To umožňuje některá volání pro přesun z existujících uzlů do nových uzlů. 

Přidávání a odebírání uzlů do clusteru se dá udělat pomocí modulu prostředí PowerShell služby infrastruktury Azure Resource Manager.

```posh
Add-AzureRmServiceFabricNode -ResourceGroupName $resourceGroupName -Name $clusterResourceName -NodeType $nodeTypeName  -NumberOfNodesToAdd 5 
Remove-AzureRmServiceFabricNode -ResourceGroupName $resourceGroupName -Name $clusterResourceName -NodeType $nodeTypeName -NumberOfNodesToRemove 5
```

## <a name="putting-it-all-together"></a>Třeba umisťovat všechny společně
Podívejme se na všechny nápady, které jsme probrali sem a komunikovat prostřednictvím příklad. Vezměte v úvahu následující služby: Pokoušíte se vytvořit službu, která funguje jako adresáře, která uchovává názvy a kontaktní informace. 

Právo předem máte spoustu otázky související s Škálováním: počet uživatelů, kteří budou mít? Kolik kontakty bude každý uživatel uložit? Při pokusu o to všechny odhlašování obrázek když jsou stálého instalace služby poprvé je obtížné. Řekněme, že se chystáte přejděte jedinou statické službou s počtem konkrétního oddílu. Důsledky výběr nesprávný počet může vést k mít problémy škálování později. Podobně i v případě, že vyberete správné počet, který nemusí mít všechny informace, které potřebujete. Například máte také rozhodnout velikost clusteru předem, jak z hlediska počtu uzlů a jejich velikost. Je obvykle snadno předpovědět, kolik prostředků služby bude spotřebovat během celé jeho životnosti. Může být také obtížné vědět předem vzor provoz, který služba zobrazí ve skutečnosti. Například možná osoby přidávat a odebírat jejich kontakty pouze první thing ráno, nebo možná ho jsou rovnoměrně v průběhu dne. Na základě toho, které možná budete muset škálovat v a dynamicky. Možná další k předpovědi budete muset škálovat v a, ale v obou případech pravděpodobně budete muset reagovat na měnící se spotřeby prostředků ve službě. To může zahrnovat Změna velikosti clusteru, aby bylo možné poskytovat další prostředky, když reorganizace použití existujících prostředků není dost. 

Ale proč i zkuste vybrat jednoho oddílu schéma pro všechny uživatele? Proč omezit pouze na jednu službu a jeden cluster statické? Skutečné situace je obvykle dynamičtější. 

Při sestavování škálování, zvažte následující dynamické vzor. Budete muset přizpůsobit pro vaši situaci:

1. Namísto pokusu o vyberte schéma rozdělení oddílů pro každého předem vytvořte "manager service".
2. Úlohy služby správce je prohlédnout informace o zákazníkovi při registraci pro vaši službu. Pak v závislosti na tyto informace službu správce vytvořit instanci vaše _skutečné_ kontaktujte úložiště služby _jen pro tohoto zákazníka_. Pokud vyžadují konkrétní konfigurací, izolaci nebo upgrady, můžete taky rozhodnout začne pracovat instance aplikace pro tohoto zákazníka. 

Tato dynamického vytváření vzor řadu výhod:

  - Nejsou pokusu uhodnout správný počet pro všechny uživatele předem nebo vytvořit jednu službu, která je nekonečnou škálovatelné všechny sama o sobě. 
  - Různé uživatele nemusí mít stejný počet oddílů, počet replik, omezení umístění, metriky, načítání výchozí, názvy služeb, nastavení dns nebo jakékoli jiné vlastnosti zadané pro úroveň služby nebo aplikace. 
  - Získáte segmentace další data. Každý zákazník má své vlastní kopii služby
    - Každý zákazník služby můžete nakonfigurována jinak než a udělit více nebo méně prostředků s více nebo méně oddíly nebo repliky podle potřeby podle jejich očekávaný rozsah.
      - Například, že zákazník placené pro úroveň "Zlatá" - může získají další repliky nebo větší počet oddílů a potenciálně prostředky vyhrazený ke službám prostřednictvím kapacity metriky a aplikace.
      - Nebo se stát, že poskytnou informace o tom, počet kontakty, které budou potřebné byla "Malá" - by získat pouze několik oddílů, nebo dokonce možné zařadit do fondu sdílené služby s ostatními zákazníky.
  - Zatímco čekáte pro zákazníky objeví nespustíte spoustu instance služby nebo repliky
  - Pokud zákazník někdy ponechá, pak odebrání své informace ze služby je jednoduše tak, že má správce odstranit této služby nebo aplikace, která ji vytvořila.

## <a name="next-steps"></a>Další kroky
Další informace o konceptech Service Fabric najdete v následujících článcích:

* [Dostupnost služeb Service Fabric](service-fabric-availability-services.md)
* [Vytváření oddílů služby Service Fabric](service-fabric-concepts-partitioning.md)

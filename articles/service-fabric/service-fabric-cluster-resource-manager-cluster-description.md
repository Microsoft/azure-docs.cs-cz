---
title: Popis clusteru pomocí Správce prostředků clusteru
description: Popište cluster Service Fabric zadáním domén selhání, upgradovacích domén, vlastností uzlů a kapacit uzlů pro Správce prostředků clusteru.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7142e3f9aaa25e7ba327194c04ad6a9b5f4e3ad1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258769"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Popište cluster Service Fabric pomocí Správce prostředků clusteru
Funkce Správce prostředků clusteru azure service fabric poskytuje několik mechanismů pro popis clusteru:

* Domény selhání
* Upgradovat domény
* Vlastnosti uzlu
* Kapacity uzlů

Za běhu používá Správce prostředků clusteru tyto informace k zajištění vysoké dostupnosti služeb spuštěných v clusteru. Při vynucování těchto důležitých pravidel se také pokusí optimalizovat spotřebu prostředků v rámci clusteru.

## <a name="fault-domains"></a>Domény selhání
Doména selhání je jakákoli oblast koordinovaného selhání. Jeden počítač je doména selhání. To může selhat na jeho vlastní z různých důvodů, od výpadků napájení řídit selhání na špatné NIC firmware. 

Počítače připojené ke stejnému přepínači Ethernet jsou ve stejné doméně selhání. Stejně tak stroje, které sdílejí jediný zdroj energie nebo na jednom místě. 

Vzhledem k tomu, že je přirozené, že se hardwarové chyby překrývají, jsou domény selhání ze své podstaty hierarchické. Jsou reprezentovány jako identifikátory URI v service fabric.

Je důležité, aby domény selhání jsou nastaveny správně, protože Service Fabric používá tyto informace bezpečně umístit služby. Service Fabric nechce umístit služby tak, že ztráta domény selhání (způsobené selháním některé součásti) způsobí, že služba přejít dolů. 

V prostředí Azure Service Fabric používá informace o doméně selhání poskytované prostředí správně nakonfigurovat uzly v clusteru vaším jménem. Pro samostatné instance Service Fabric jsou domény selhání definovány v době, kdy je cluster nastaven. 

> [!WARNING]
> Je důležité, aby informace o doméně selhání poskytované Service Fabric je přesné. Řekněme například, že uzly clusteru Service Fabric běží uvnitř 10 virtuálních počítačů, spuštěných na 5 fyzických hostitelích. V tomto případě, i když existuje 10 virtuálních počítačů, existuje pouze 5 různých domén selhání (nejvyšší úrovně). Sdílení stejného fyzického hostitele způsobí, že virtuální servery budou sdílet stejnou kořenovou doménu selhání, protože virtuální chod dochází ke koordinovanému selhání, pokud se jejich fyzický hostitel nezdaří.  
>
> Service Fabric očekává, že doména selhání uzlu nezmění. Jiné mechanismy zajištění vysoké dostupnosti virtuálních aplikací, jako jsou [například VIRTUÁLNÍ CHAP](https://technet.microsoft.com/library/cc967323.aspx), může způsobit konflikty s Service Fabric. Tyto mechanismy používají transparentní migraci virtuálních hostitelů z jednoho hostitele na druhého. Nejsou překonfigurovat nebo upozornit spuštěný kód uvnitř virtuálního počítače. Jako takové nejsou *podporovány* jako prostředí pro spouštění clusterů Service Fabric. 
>
> Service Fabric by měla být jedinou používanou technologií s vysokou dostupností. Mechanismy, jako je migrace živého virtuálního ms a sans, nejsou nutné. Pokud tyto mechanismy se používají ve spojení s Service Fabric, _snižují_ dostupnost a spolehlivost aplikací. Důvodem je, že zavádějí další složitost, přidávají centralizované zdroje selhání a používají strategie spolehlivosti a dostupnosti, které jsou v konfliktu s strategiemi v Service Fabric. 
>
>

V následující grafice vybarvíme všechny entity, které přispívají k doménám selhání, a vypíšeme seznam všech různých domén selhání, které z toho vyplývají. V tomto příkladu máme datová centra ("DC"), racky ("R") a čepele ("B"). Pokud každý okno obsahuje více než jeden virtuální počítač, může být další vrstva v hierarchii domény selhání.

<center>

![Uzly uspořádané pomocí domén selhání][Image1]
</center>

Během běhu aplikace Service Fabric Cluster Resource Resource Manager zvažuje domény selhání v clusteru a plány rozložení. Stavové repliky nebo instance bezstavové pro službu jsou distribuovány tak, aby byly v samostatných doménách selhání. Distribuce služby mezi doménami selhání zajišťuje, že dostupnost služby není ohrožena, pokud dojde k selhání domény selhání na libovolné úrovni hierarchie.

Správce prostředků clusteru se nestará o to, kolik vrstev je v hierarchii domény selhání. Snaží se zajistit, že ztráta jedné části hierarchie nemá vliv na služby spuštěné v ní. 

Je nejlepší, pokud stejný počet uzlů je na každé úrovni hloubky v hierarchii domény selhání. Pokud je "strom" domén selhání v clusteru nevyvážený, je pro Správce prostředků clusteru těžší zjistit nejlepší přidělení služeb. Nevyvážené rozložení domén selhání znamená, že ztráta některých domén ovlivňuje dostupnost služeb více než jiné domény. V důsledku toho je Správce prostředků clusteru rozpolcený mezi dvěma cíli: 

* Chce používat stroje v této "těžké" doméně tím, že umístí služby na ně. 
* Chce umístit služby do jiných domén, aby ztráta domény nezpůsobila problémy. 

Jak vypadají nevyvážené domény? Následující diagram znázorňuje dvě různá rozložení clusteru. V prvním příkladu jsou uzly rovnoměrně rozloženy mezi doménami selhání. V druhém příkladu má jedna doména selhání mnohem více uzlů než ostatní domény selhání. 

<center>

![Dvě různá rozložení clusteru][Image2]
</center>

V Azure je pro vás spravována volba domény selhání, která obsahuje uzel. Ale v závislosti na počtu uzlů, které zřídíte, můžete stále skončit s doménami selhání, které mají více uzlů v nich než v jiných. 

Řekněme například, že máte pět domén selhání v clusteru, ale zřízení sedm uzlů pro typ uzlu (**NodeType**). V tomto případě první dvě domény selhání skončit s více uzlů. Pokud budete pokračovat v nasazování více **nodetype** instancí pouze s několika instancemi, problém se zhorší. Z tohoto důvodu doporučujeme, aby počet uzlů v každém typu uzlu je násobkem počtu domén selhání.

## <a name="upgrade-domains"></a>Upgradovat domény
Další funkcí, která pomáhá Správci prostředků clusteru Service Fabric porozumět rozložení clusteru, jsou další funkce, která pomáhá Správci prostředků clusteru Service Fabric pochopit rozložení clusteru. Upgradovací domény definují sady uzlů, které jsou upgradovány současně. Upgradovací domény pomáhají Správci prostředků clusteru pochopit a organizovat operace správy, jako jsou upgrady.

Upgradovací domény jsou hodně jako domény selhání, ale s několika klíčovými rozdíly. Za prvé, oblasti koordinovaných selhání hardwaru definují domény selhání. Upgradovací domény jsou naopak definovány zásadami. Můžete se rozhodnout, kolik chcete, místo toho, aby prostředí diktovat číslo. Můžete mít tolik upgradovacích domén jako uzly. Dalším rozdílem mezi doménami selhání a upgradovacími doménami je, že upgradovací domény nejsou hierarchické. Místo toho jsou spíše jako jednoduchá značka. 

Následující diagram znázorňuje tři upgradovací domény prokládané ve třech doménách selhání. Zobrazuje také jedno možné umístění pro tři různé repliky stavové služby, kde každá skončí v různých doménách selhání a upgradu. Toto umístění umožňuje ztrátu domény selhání, zatímco uprostřed upgradu služby a stále mají jednu kopii kódu a dat.  

<center>

![Umístění s doménami poruchy a upgradu][Image3]
</center>

Existují výhody a nevýhody, které mají velký počet upgradovacích domén. Další upgradovací domény znamenají, že každý krok upgradu je podrobnější a ovlivňuje menší počet uzlů nebo služeb. Méně služeb se musí pohybovat najednou, zavedení méně konve do systému. To má tendenci zlepšit spolehlivost, protože méně služby je ovlivněno jakýmkoli problémem zavedeným během upgradu. Další upgradovací domény také znamenají, že potřebujete méně dostupné vyrovnávací paměti na jiných uzlech pro zpracování dopadu upgradu. 

Například pokud máte pět upgradovacích domén, uzly v každé z nich zpracovávají zhruba 20 procent návštěvnosti. Pokud potřebujete sundat tuto doménu upgradu pro upgrade, toto zatížení obvykle musí někam jít. Vzhledem k tomu, že máte čtyři zbývající upgradovací domény, každá musí mít prostor pro přibližně 5 procent celkového provozu. Další upgradovací domény znamenají, že potřebujete méně vyrovnávací paměti na uzlech v clusteru. 

Zvažte, zda jste místo toho měli 10 upgradovacích domén. V takovém případě by každá doména upgradu zpracovávala pouze asi 10 procent celkového provozu. Když upgrade projde clusterem, každá doména bude muset mít místo pouze pro asi 1,1 procenta celkového provozu. Další upgradovací domény obecně umožňují spouštět uzly při vyšším využití, protože potřebujete méně rezervované kapacity. Totéž platí pro domény selhání.  

Nevýhodou mnoha upgradovacích domén je, že upgrady mají tendenci trvat déle. Service Fabric čeká krátkou dobu po dokončení domény upgradu a provádí kontroly před zahájením upgradu další. Tato zpoždění umožňují zjištění problémů zavedených upgradem před pokračováním upgradu. Kompromis je přijatelný, protože zabraňuje tomu, aby chybné změny ovlivnily příliš velkou část služby najednou.

Přítomnost příliš málo upgradovacídomény má mnoho negativních vedlejších účinků. Zatímco každá upgradovací doména je mimo rozsah a upgraduje se, velká část celkové kapacity není k dispozici. Máte-li například pouze tři domény upgradu, snižujete současně přibližně jednu třetinu celkové kapacity služby nebo clusteru. Mít tolik služby dolů najednou není žádoucí, protože potřebujete dostatek kapacity ve zbytku clusteru ke zpracování úlohy. Udržování této vyrovnávací paměti znamená, že během normálního provozu jsou tyto uzly méně načteny, než by byly jinak. Tím se zvýší náklady na spuštění služby.

Neexistuje žádné skutečné omezení celkového počtu domén selhání nebo upgradu v prostředí nebo omezení jejich překrytí. Existují však společné vzory:

- Domény selhání a upgradovací domény mapované 1:1
- Jedna upgradovací doména na uzel (fyzická nebo virtuální instance operačního systému)
- Model "pruhované" nebo "matice", kde domény selhání a upgradovací domény tvoří matici se stroji, které obvykle běží po diagonálech

<center>

![Rozložení domén selhání a upgradu][Image4]
</center>

Neexistuje žádná nejlepší odpověď, pro které rozložení si vybrat. Každý má klady a zápory. Například model 1FD:1UD lze snadno nastavit. Model jedné upgradovací domény na model uzlu se nejvíce podobá tomu, na co jsou lidé zvyklí. Během upgradů je každý uzel aktualizován nezávisle. To je podobné tomu, jak malé sady strojů byly modernizovány ručně v minulosti.

Nejběžnějším modelem je matice FD/UD, kde domény selhání a upgradovací domény tvoří tabulku a uzly jsou umístěny podél úhlopříčky. Toto je model používaný ve výchozím nastavení v clusterech Service Fabric v Azure. Pro clustery s mnoha uzly, všechno skončí vypadat jako hustý vzorec matice.

> [!NOTE]
> Clustery Service Fabric hostované v Azure nepodporují změnu výchozí strategie. Toto přizpůsobení nabízejí pouze samostatné clustery.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Omezení domény a upgradu a výsledné chování
### <a name="default-approach"></a>Výchozí přístup
Ve výchozím nastavení udržuje Správce prostředků clusteru služby vyvážené napříč doménami selhání a upgradu. To je modelováno jako [omezení](service-fabric-cluster-resource-manager-management-integration.md). Omezení pro chyby a upgrade domény uvádí: "Pro daný oddíl služby by nikdy neměl být větší rozdíl než jeden v počtu objektů služby (instance služby bez stavů nebo repliky stavové služby) mezi dvěma doménami na stejné stejné úroveň hierarchie."

Řekněme, že toto omezení poskytuje záruku "maximální hod.". Omezení pro selhání a upgrade domény zabraňuje určité pohyby nebo uspořádání, které porušují pravidlo.

Řekněme například, že máme cluster se šesti uzly, nakonfigurovaný s pěti doménami selhání a pěti upgradovacími doménami.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **Ud2** | | |N3 | | |
| **Ud3** | | | |N4 | |
| **UD4** | | | | |N5 |

Nyní řekněme, že vytvoříme službu s **TargetReplicaSetSize** (nebo pro bezstavové služby **InstanceCount)** hodnota pět. Repliky přistávají na N1-N5. Ve skutečnosti, N6 se nikdy nepoužívá bez ohledu na to, kolik služeb, jako je tento, které vytvoříte. Ale proč? Podívejme se na rozdíl mezi aktuálnírozložení a co by se stalo, kdyby N6 je vybrán.

Zde je rozložení, které jsme dostali, a celkový počet replik na doménu poruchy a upgradu:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **Ud2** | | |R3 | | |1 |
| **Ud3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Toto rozložení je vyvážené z hlediska uzlů na doménu selhání a upgradovací doménu. Je také vyvážená z hlediska počtu replik na chybu a upgradu domény. Každá doména má stejný počet uzlů a stejný počet replik.

Nyní se podívejme na to, co by se stalo, kdybychom použili N6 místo N2. Jak by pak byly repliky distribuovány?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **Ud2** | | |R2 | | |1 |
| **Ud3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Toto rozložení porušuje naši definici záruky "maximální rozdíl" pro omezení domény selhání. FD0 má dvě repliky, zatímco FD1 má nulu. Rozdíl mezi FD0 a FD1 je celkem dva, což je větší než maximální rozdíl jednoho. Vzhledem k tomu, že omezení je porušeno, Správce prostředků clusteru toto uspořádání neumožňuje. Podobně, pokud jsme si vybrali N2 a N6 (místo N1 a N2), dostali bychom:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **Ud2** | | |R2 | | |1 |
| **Ud3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Toto rozložení je vyvážené z hlediska domén selhání. Ale teď porušuje omezení domény upgradu, protože UD0 má nulové repliky a UD1 má dvě. Toto rozložení je také neplatné a správce prostředků clusteru ho nevyskladní.

Tento přístup k distribuci stavových replik nebo bezstavových instancí poskytuje nejlepší možnou odolnost proti chybám. Pokud jedna doména přejde dolů, minimální počet replik/instancí dojde ke ztrátě. 

Na druhou stranu tento přístup může být příliš přísné a neumožňuje clusteru využívat všechny prostředky. Pro určité konfigurace clusteru nelze použít určité uzly. To může způsobit Service Fabric neumísťovat služby, výsledkem jsou varovné zprávy. V předchozím příkladu nelze použít některé uzly clusteru (N6 v příkladu). I v případě, že jste do tohoto clusteru přidali uzly (N7-N10), repliky/instance by byly umístěny pouze na N1–N5 z důvodu omezení domén selhání a upgradu. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **Ud2** | |N7 |N3 | | |
| **Ud3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>Alternativní přístup

Správce prostředků clusteru podporuje jinou verzi omezení pro domény selhání a upgradu. Umožňuje umístění a zároveň zaručuje minimální úroveň bezpečnosti. Alternativní omezení lze uvést takto: "Pro daný oddíl služby by měla distribuce repliky mezi doménami zajistit, že oddíl neutrpí ztrátu kvora." Řekněme, že toto omezení poskytuje záruku "bezpečného kvora". 

> [!NOTE]
> Pro stavové služby definujeme *ztrátu kvora* v situaci, kdy většina replik oddílu je současně mimo provoz. Například pokud **TargetReplicaSetSize** je pět, sada libovolné tři repliky představuje kvorum. Podobně pokud **TargetReplicaSetSize** je šest, čtyři repliky jsou nezbytné pro kvorum. V obou případech více než dvě repliky může být dolů ve stejnou dobu, pokud oddíl chce pokračovat v normálním fungování. 
>
> Pro službu bez státní příslušnosti neexistuje nic takového jako *ztráta kvora*. Služby bez státní příslušnosti nadále fungovat normálně i v případě, že většina instancí jít dolů ve stejnou dobu. Takže se zaměříme na stavové služby ve zbytku tohoto článku.
>

Vraťme se k předchozímu příkladu. S "kvorum bezpečné" verze omezení, všechny tři rozložení by byla platná. I v případě, že FD0 se nezdařilo ve druhém rozložení nebo UD1 se nezdařilo ve třetím rozložení, oddíl by stále mít kvorum. (Většina replik by stále nahoru.) S touto verzí omezení, N6 lze téměř vždy využít.

Přístup "bezpečné kvora" poskytuje větší flexibilitu než přístup "maximální rozdíl". Důvodem je, že je snazší najít repliky distribuce, které jsou platné v téměř všechny topologie clusteru. Tento přístup však nemůže zaručit nejlepší vlastnosti odolnosti proti chybám, protože některé chyby jsou horší než jiné. 

V nejhorším případě může dojít ke ztrátě většiny replik selháním jedné domény a jedné další repliky. Například místo tří selhání, které jsou nutné ke ztrátě kvora s pěti replikami nebo instancemi, můžete nyní ztratit většinu s pouze dvěma selháními. 

### <a name="adaptive-approach"></a>Adaptivní přístup
Vzhledem k tomu, že oba přístupy mají silné a slabé stránky, zavedli jsme adaptivní přístup, který kombinuje tyto dvě strategie.

> [!NOTE]
> Toto je výchozí chování začínající service fabric verze 6.2. 
> 
> Adaptivní přístup používá logiku "maximální rozdíl" ve výchozím nastavení a přepne na logiku "kvorum bezpečné" pouze v případě potřeby. Správce prostředků clusteru automaticky zjistí, která strategie je nezbytná, a to tak, že se podíváte na konfiguraci clusteru a služeb.
> 
> Správce prostředků clusteru by měl používat logiku "na základě kvora" pro službu obě tyto podmínky jsou splněny:
>
> * **TargetReplicaSetSize** pro službu je dělitelný počtem domén selhání a počtem upgradovacích domén.
> * Počet uzlů je menší nebo roven počtu domén selhání vynásobeným počtem upgradovacích domén.
>
> Mějte na paměti, že Správce prostředků clusteru bude používat tento přístup pro bezstavové i stavové služby, i když ztráta kvora není relevantní pro bezstavové služby.

Vraťme se k předchozímu příkladu a předpokládejme, že cluster má nyní osm uzlů. Cluster je stále nakonfigurován s pěti doménami selhání a pěti upgradovacími doménami a hodnota **TargetReplicaSetSize** služby hostované v tomto clusteru zůstane pět. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **Ud2** | |N7 |N3 | | |
| **Ud3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Vzhledem k tomu, že jsou splněny všechny nezbytné podmínky, bude Správce prostředků clusteru při distribuci služby používat logiku založené na kvoru. To umožňuje použití N6-N8. Jedna možná distribuce služeb v tomto případě může vypadat takto:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **Ud2** | |R3 |R4 | | |2 |
| **Ud3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Pokud je hodnota **TargetReplicaSetSize** vaší služby snížena na čtyři (například), správce prostředků clusteru si této změny všimne. Bude pokračovat pomocí logiky "maximální rozdíl", protože **TargetReplicaSetSize** již nelze rozdělit podle počtu domén selhání a upgradu domén. V důsledku toho dojde k určité přesuny replik distribuovat zbývající čtyři repliky na uzly N1-N5. Tímto způsobem není porušena verze "maximální rozdíl" domény selhání a logiky upgradu domény. 

V předchozím rozložení pokud **targetReplicaSetSize** hodnota je pět a N1 je odebrán z clusteru, počet upgradu domén se rovná čtyři. Správce prostředků clusteru opět začne používat logiku "maximální rozdíl", protože počet upgradovacích domén již nerozděluje hodnotu **TargetReplicaSetSize** služby. V důsledku toho replika R1, při znovu vytvoření, musí přistát na N4 tak, aby omezení pro selhání a upgrade domény není porušena.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |Není dostupné. |Není dostupné. |Není dostupné. |Není dostupné. |Není dostupné. |Není dostupné. |
| **UD1** |R2 | | | | |1 |
| **Ud2** | |R3 |R4 | | |2 |
| **Ud3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Konfigurace domén selhání a upgradu
V nasazení azure hostované service fabric nasazení, domény selhání a upgradu domény jsou definovány automaticky. Service Fabric vyzvedává a používá informace o prostředí z Azure.

Pokud vytváříte vlastní cluster (nebo chcete spustit určitou topologii ve vývoji), můžete zadat doménu selhání a upgradovat informace o doméně sami. V tomto příkladu definujeme cluster místního vývoje s devíti uzlovými daty, který zahrnuje tři datová centra (každá se třemi racky). Tento cluster má také tři upgradovací domény prokládané v těchto třech datových centrech. Tady je příklad konfigurace v souboru ClusterManifest.xml:

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one box/one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

Tento příklad používá soubor ClusterConfig.json pro samostatná nasazení:

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Když definujete clustery prostřednictvím Azure Resource Manager, Azure přiřadí domény selhání a upgradovací domény. Definice typů uzlů a škálovacích sad virtuálních počítačů v šabloně Azure Resource Manager tedy neobsahuje informace o doméně selhání nebo upgradovací doméně.
>

## <a name="node-properties-and-placement-constraints"></a>Vlastnosti uzlů a omezení umístění
Někdy (ve skutečnosti většinu času) budete chtít zajistit, aby určité úlohy běžet pouze na určité typy uzlů v clusteru. Některé úlohy mohou například vyžadovat gpu nebo ssd disy a jiné nemusí. 

Skvělým příkladem cílení hardwaru na konkrétní úlohy je téměř každá architektura n-tier. Některé počítače slouží jako front-end nebo API obsluhující straně aplikace a jsou vystaveny klientům nebo internetu. Různé počítače, často s různými hardwarovými prostředky, zpracovávají práci výpočetních nebo úložných vrstev. Ty obvykle _nejsou_ přímo vystaveny klientům nebo internetu. 

Service Fabric očekává, že v některých případech konkrétní úlohy může být nutné spustit na konkrétní konfigurace hardwaru. Například:

* Existující n-vrstvá aplikace byla "zrušena a přesunuta" do prostředí Service Fabric.
* Úloha musí být spuštěna na konkrétním hardwaru z důvodů výkonu, škálování nebo izolace zabezpečení.
* Úloha by měla být izolována od jiných úloh z důvodů spotřebou zásad nebo prostředků.

Pro podporu těchto druhů konfigurací service fabric obsahuje značky, které můžete použít na uzly. Tyto značky se nazývají *vlastnosti uzlu*. *Omezení umístění* jsou příkazy připojené k jednotlivým službám, které vyberete pro jeden nebo více vlastností uzlu. Omezení umístění definují, kde by měly být služby spuštěny. Sada omezení je rozšiřitelná. Jakýkoli pár klíč/hodnota může fungovat. 

<center>

![Různé úlohy pro rozložení clusteru][Image5]
</center>

### <a name="built-in-node-properties"></a>Vlastnosti předdefinovaných uzlů
Service Fabric definuje některé výchozí vlastnosti uzlu, které lze použít automaticky, takže je nemusíte definovat. Výchozí vlastnosti definované v každém uzlu jsou **NodeType** a **NodeName**. 

Můžete například napsat omezení umístění `"(NodeType == NodeType03)"`jako . **NodeType** je běžně používaná vlastnost. Je to užitečné, protože odpovídá 1:1 s typem stroje. Každý typ počítače odpovídá typu úlohy v tradiční n-vrstvé aplikace.

<center>

![Omezení umístění a vlastnosti uzlu][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>Omezení umístění a syntaxe vlastnosti uzlu 
Hodnota zadaná ve vlastnosti uzlu může být řetězec, logická hodnota nebo podepsána dlouho. Příkaz ve službě se nazývá *omezení* umístění, protože omezuje, kde může být služba spuštěna v clusteru. Omezení může být libovolný logický příkaz, který pracuje s vlastnostmi uzlu v clusteru. Platné voliče v těchto logických příkazech jsou:

* Podmíněné kontroly pro vytváření konkrétních příkazů:

  | Příkaz | Syntaxe |
  | --- |:---:|
  | "rovná se" | "==" |
  | "není rovno" | "!=" |
  | "větší než" | ">" |
  | "větší než nebo rovno" | ">=" |
  | "méně než" | "<" |
  | "menší nebo rovno" | "<=" |

* Logické příkazy pro seskupování a logické operace:

  | Příkaz | Syntaxe |
  | --- |:---:|
  | "a" | "&&" |
  | "nebo" | "&#124;&#124;" |
  | "ne" | "!" |
  | "skupina jako jediné prohlášení" | "()" |

Zde jsou některé příklady základních příkazů omezení:

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Pouze uzly, kde celkový příkaz omezení umístění vyhodnotí na "True" může mít službu umístěna na něj. Uzly, které nemají definovanou vlastnost, neodpovídají žádnému omezení umístění, které vlastnost obsahuje.

Řekněme, že následující vlastnosti uzlu byly definovány pro typ uzlu v ClusterManifest.xml:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

Následující příklad ukazuje vlastnosti uzlu definované pomocí ClusterConfig.json pro samostatná nasazení nebo Template.json pro clustery hostované v Azure. 

> [!NOTE]
> V šabloně Azure Resource Manager typ uzlu je obvykle parametrizovaný. Bude vypadat `"[parameters('vmNodeType1Name')]"` spíše než NodeType01.
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

Omezení umístění *služby* můžete vytvořit pro službu následujícím způsobem:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// Add other required ServiceDescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Pokud jsou platné všechny uzly NodeType01, můžete také vybrat `"(NodeType == NodeType01)"`tento typ uzlu s omezením .

Omezení umístění služby lze dynamicky aktualizovat za běhu. Pokud potřebujete, můžete přesunout službu v clusteru, přidat a odebrat požadavky a tak dále. Service Fabric zajišťuje, že služba zůstane nahoru a k dispozici i v případě, že tyto typy změn jsou provedeny.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Omezení umístění jsou určena pro každou instanci pojmenované služby. Aktualizace vždy místo (přepsat) co bylo dříve zadáno.

Definice clusteru definuje vlastnosti na uzlu. Změna vlastností uzlu vyžaduje upgrade na konfiguraci clusteru. Inovace vlastností uzlu vyžaduje, aby se každý ovlivněný uzel restartoval, aby mohl vykazovat nové vlastnosti. Service Fabric spravuje tyto postupné upgrady.

## <a name="describing-and-managing-cluster-resources"></a>Popis a správa prostředků clusteru
Jednou z nejdůležitějších úloh každého orchestrátoru je pomoci spravovat spotřebu prostředků v clusteru. Správa prostředků clusteru může znamenat několik různých věcí. 

Za prvé, je zajištěno, že stroje nejsou přetížené. To znamená, že se ujistěte, že počítače nespouštějí více služeb, než zvládnou. 

Za druhé, je vyvažování a optimalizace, které jsou rozhodující pro efektivní provoz služeb. Nákladově efektivní nabídky služeb nebo služby citlivé na výkon nemohou dovolit, aby některé uzly byly horké, zatímco jiné jsou studené. Aktivní uzly vedou k tvrzení o prostředku a nízký výkon. Studené uzly představují plýtvání zdroji a zvýšené náklady. 

Service Fabric představuje prostředky jako *metriky*. Metriky jsou všechny logické nebo fyzické prostředky, které chcete popsat Service Fabric. Příklady metriky jsou "WorkQueueDepth" nebo "MemoryInMb." Informace o fyzických prostředcích, které service fabric může řídit na uzlech, naleznete v [tématu zásadsprávného řízení prostředků](service-fabric-resource-governance.md). Informace o výchozích metrikách používaných Správcem prostředků clusteru a o konfiguraci vlastních metrik naleznete v [tomto článku](service-fabric-cluster-resource-manager-metrics.md).

Metriky se liší od omezení umístění a vlastností uzlu. Vlastnosti uzlu jsou statické popisovače samotných uzlů. Metriky popisují prostředky, které mají uzly a které služby spotřebovávají při spuštění na uzlu. Vlastnost uzlu může být **HasSSD** a může být nastavena na hodnotu true nebo false. Množství místa, které je k dispozici na tomto ssd disku a kolik je spotřebováno službami by metrika jako "DriveSpaceInMb." 

Stejně jako u omezení umístění a vlastností uzlu, Správce prostředků clusteru Service Fabric nerozumí tomu, co znamenají názvy metrik. Metrické názvy jsou jen řetězce. Je vhodné deklarovat jednotky jako součást názvů metrik, které vytvoříte, když mohou být nejednoznačné.

## <a name="capacity"></a>Kapacita
Pokud jste vypnuli veškeré *vyvažování*prostředků , správce prostředků clusteru Service Fabric by stále zajistil, že žádný uzel nepřejde přes jeho kapacitu. Správa přetečení kapacity je možné, pokud cluster je příliš plný nebo zatížení je větší než jakýkoli uzel. Kapacita je další *omezení,* které Správce prostředků clusteru používá k pochopení, kolik prostředků uzel má. Zbývající kapacita je také sledována pro cluster jako celek. 

Kapacita i spotřeba na úrovni služby jsou vyjádřeny metrikami. Metrika může být například "ClientConnections" a uzel může mít kapacitu pro "ClientConnections" 32,768. Jiné uzly mohou mít další omezení. Služba spuštěná na tomto uzlu může říct, že aktuálně spotřebovává 32 256 metriky ClientConnections.

Během běhu správce prostředků clusteru sleduje zbývající kapacitu v clusteru a na uzlech. Chcete-li sledovat kapacitu, Správce prostředků clusteru odečte využití každé služby od kapacity uzlu, kde je služba spuštěna. Pomocí těchto informací může Správce prostředků clusteru zjistit, kam umístit nebo přesunout repliky, aby uzly nepřekročily kapacitu.

<center>

![Uzly clusteru a kapacita][Image7]
</center>

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

Můžete zobrazit kapacity definované v manifestu clusteru. Tady je příklad pro ClusterManifest.xml:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Tady je příklad kapacit definovaných pomocí ClusterConfig.json pro samostatná nasazení nebo Template.json pro clustery hostované v Azure: 

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

Zatížení služby se často dynamicky mění. Řekněme, že replika zatížení "ClientConnections" změnil z 1 024 na 2 048. Uzel, který byl spuštěn na pak měl kapacitu pouze 512 zbývající pro tuto metriku. Nyní je umístění repliky nebo instance neplatné, protože v tomto uzlu není dostatek místa. Správce prostředků clusteru musí získat uzel zpět pod kapacitu. Snižuje zatížení uzlu, který je přes kapacitu přesunutím jedné nebo více replik nebo instancí z tohoto uzlu do jiných uzlů. 

Správce prostředků clusteru se snaží minimalizovat náklady na přesunutí replik. Můžete se dozvědět více o [nákladech na pohyb](service-fabric-cluster-resource-manager-movement-cost.md) a o [strategiích a pravidlech vyvažování](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Kapacita clusteru
Jak Správce prostředků clusteru Service Fabric chrání celý cluster před přílišnou úplnou shlukovou infrastrukturou? S dynamickým zatížením toho moc nezmůže. Služby mohou mít špičku zatížení nezávisle na akcích, které provádí Správce prostředků clusteru. Výsledkem je, že váš cluster s dostatkem prostoru pro svět dnes může být poddimenzovaný, pokud zítra dojde k prudkému nárůstu. 

Ovládací prvky ve Správci prostředků clusteru pomáhají předcházet problémům. První věc, kterou můžete udělat, je zabránit vytváření nových úloh, které by způsobily clusteru, aby se stal plný.

Řekněme, že vytvoříte bezstavovou službu a má k ní přidruženo určité zatížení. Služba se stará o metriku "DiskSpaceInMb". Služba bude využívat pět jednotek "DiskSpaceInMb" pro každou instanci služby. Chcete vytvořit tři instance služby. To znamená, že potřebujete 15 jednotek "DiskSpaceInMb", abyste byli přítomni v clusteru, abyste dokonce vytvořili tyto instance služby.

Správce prostředků clusteru průběžně vypočítává kapacitu a spotřebu každé metriky, aby mohl určit zbývající kapacitu v clusteru. Pokud není dostatek místa, Správce prostředků clusteru odmítne volání k vytvoření služby.

Vzhledem k tomu, že požadavek je pouze, že 15 jednotek bude k dispozici, můžete přidělit tento prostor mnoha různými způsoby. Například může existovat jedna zbývající jednotka kapacity na 15 různých uzlech nebo tři zbývající jednotky kapacity na pěti různých uzlech. Pokud Správce prostředků clusteru můžete změnit uspořádání věci tak, že jsou k dispozici pět jednotek na třech uzlech, umístí službu. Změna uspořádání clusteru je obvykle možné, pokud cluster je téměř plná nebo existující služby nelze konsolidovat z nějakého důvodu.

## <a name="buffered-capacity"></a>Kapacita ve vyrovnávací paměti
Kapacita ve vyrovnávací paměti je další funkcí Správce prostředků clusteru. Umožňuje rezervaci některé části celkové kapacity uzlu. Tato vyrovnávací paměť kapacity se používá pouze k umístění služeb během upgradů a selhání uzlu. 

Kapacita ve vyrovnávací paměti je určena globálně pro metriku pro všechny uzly. Hodnota, kterou vyberete pro rezervovanou kapacitu, je funkcí počtu domén selhání a upgradu, které máte v clusteru. Další domény poruchy a upgradu znamenají, že můžete vybrat nižší číslo pro kapacitu ve vyrovnávací paměti. Pokud máte více domén, můžete očekávat, že menší množství clusteru nebude k dispozici během upgradů a selhání. Zadání kapacity ve vyrovnávací paměti má smysl pouze v případě, že jste také zadali kapacitu uzlu pro metriku.

Tady je příklad, jak určit kapacitu ve vyrovnávací paměti v souboru ClusterManifest.xml:

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

Tady je příklad, jak určit kapacitu ve vyrovnávací paměti pomocí clusteru ClusterConfig.json pro samostatná nasazení nebo Template.json pro clustery hostované v Azure:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

Vytvoření nových služeb se nezdaří, pokud cluster uprostřed kapacity pro metriku. Zabránění vytvoření nových služeb pro zachování vyrovnávací paměti zajišťuje, že upgrady a selhání nezpůsobí, že uzly přejít kapacitu. Kapacita ve vyrovnávací paměti je volitelná, ale doporučujeme ji v každém clusteru, který definuje kapacitu pro metriku.

Správce prostředků clusteru zveřejňuje tyto informace o zatížení. Pro každou metriku tyto informace zahrnují: 
- Nastavení kapacity ve vyrovnávací paměti.
- Celková kapacita.
- Aktuální spotřeba.
- Zda je každá metrika považována za vyváženou nebo ne.
- Statistiky o směrodatné odchylce.
- Uzly, které mají nejvíce a nejméně zatížení.  
  
Následující kód ukazuje příklad tohoto výstupu:

```PowerShell
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>Další kroky
* Informace o architektuře a toku informací ve Správci prostředků clusteru naleznete v [tématu Přehled architektury Správce prostředků clusteru](service-fabric-cluster-resource-manager-architecture.md).
* Definování metrikdefragmentace je jedním ze způsobů, jak konsolidovat zatížení uzlů namísto jeho rozprostření. Informace o konfiguraci defragmentace naleznete v [tématu Defragmentace metrik a zatížení v service fabric](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Začněte od začátku a [získejte úvod do Správce prostředků clusteru Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
* Informace o tom, jak Správce prostředků clusteru spravuje a vyrovnává zatížení clusteru, naleznete v [tématu Vyrovnávání clusteru Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png

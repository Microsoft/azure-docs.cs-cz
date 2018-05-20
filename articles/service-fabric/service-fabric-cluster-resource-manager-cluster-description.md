---
title: Popis clusteru clusteru Resource Manager | Microsoft Docs
description: Cluster Service Fabric popisující zadáním domén selhání, Upgrade domény, vlastnosti uzlu a uzel kapacity pro správce prostředků clusteru.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 13ee238580d645f3e727090bc0e0275b36bdb225
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="describing-a-service-fabric-cluster"></a>Popisující service fabric cluster
Služba Fabric clusteru Resource Manager poskytuje několik mechanismů pro popis clusteru. Během doby běhu správce prostředků clusteru používá tyto informace k zajištění vysoké dostupnosti se služby spuštěné v clusteru. Při vynucování tyto důležité pravidla, je taky automatický pokus o optimalizovat spotřeby prostředků v rámci clusteru.

## <a name="key-concepts"></a>Klíčové koncepty
Správce prostředků clusteru podporuje několik funkcí, které popisují clusteru:

* Domén selhání
* Domén upgradu
* Vlastnosti uzlu
* Uzel kapacity

## <a name="fault-domains"></a>Domény selhání
Domény selhání je všechny oblasti koordinované selhání. Jeden počítač je domény selhání (protože ho může selhat v jeho vlastní pro různé příčiny selhání power napájení na jednotce selhání chybný firmwaru síťový adaptér). Počítače připojené ke stejnému přepínači sítě Ethernet jsou ve stejné doméně selhání, jako jsou počítače sdílení jednoho zdroje napájení nebo na jednom místě. Vzhledem k tomu, že je přirozené pro chyby hardwaru na překrývají, jsou ze své podstaty hierarchická domén selhání a jsou reprezentovány jako identifikátory URI v Service Fabric.

Je důležité, aby domén selhání jsou správně nastaveny vzhledem k tomu, že Service Fabric používá tuto informaci k bezpečně umístění služby. Service Fabric nechce umístit služby tak, aby způsobí ztrátu domény selhání (způsobené selhání některé komponenty) služba přejděte. Ve službě Azure prostředí Service Fabric používá poskytnuté prostředím informace o doméně selhání správně nakonfigurovat uzly v clusteru vaším jménem. Pro samostatnou službu prostředků infrastruktury domén selhání jsou definovány v čase, který je nastavený clusteru 

> [!WARNING]
> Je důležité, aby doména selhání informací uvedených na Service Fabric přesná. Například předpokládejme, že uzly clusteru Service Fabric běží uvnitř 10 virtuálních počítačů běžících na pět fyzických hostitelích. V takovém případě i když se 10 virtuálních počítačů, existují jenom 5 různých (nejvyšší úrovně) poruch domén. Sdílení na stejném fyzickém hostiteli způsobí, že virtuální počítače sdílet stejné kořenové domény selhání vzhledem k tomu, že virtuální počítače zaznamenat koordinované selhání, pokud se nezdaří jejich fyzického hostitele.  
>
> Service Fabric očekává domény selhání uzlu nechcete změnit. Další mechanismy pro zajištění vysoké dostupnosti virtuálních počítačů, jako [HA – VMs](https://technet.microsoft.com/library/cc967323.aspx) mohou způsobit konflikty s Service Fabric, které používají transparentní migraci virtuálních počítačů z jednoho hostitele na druhého. Tyto mechanismy nezadávejte překonfigurovat nebo oznámit kód spuštěný ve virtuálním počítači. Jako takový jsou **nepodporuje** jako prostředí pro spuštění Service Fabric clusterů. Service Fabric musí být používané technologie pouze vysokou dostupnost. Mechanismy, jako je migrace za provozu virtuálního počítače, sítě SAN, nebo jiné nejsou potřebné. Pokud se používá ve spojení s Service Fabric, tyto mechanismy _snížit_ aplikace dostupnost a spolehlivost vzhledem k tomu, že zavést další složitosti, přidejte centralizované zdroje selhání a využívat spolehlivost a strategie dostupnosti, které je v konfliktu s těmi, která v Service Fabric. 
>
>

Na následujícím obrázku jsme barva všech entit, které seznam všech různé chyby domén, které způsobit a přispívat do domény selhání. V tomto příkladu máme datových centrech (dále jen "řadiče domény"), stojany ("R") a okna ("B"). V opačném případě pokud každý okno obsahuje více než jeden virtuální počítač, může dojít další úroveň v hierarchii domény selhání.

<center>
![Uzly uspořádané prostřednictvím domén selhání][Image1]
</center>

Během doby běhu správce prostředků clusteru Service Fabric zvažuje domén selhání v clusteru a plány rozložení. Stavová repliky nebo bezstavové instance pro danou službu se distribuují tak, aby byly v samostatných doménách selhání. Distribuce služby napříč doménami selhání zajistí, že nedojde k ohrožení dostupnost služby při selhání doména selhání na libovolné úrovni hierarchie.

Správce prostředků clusteru Service Fabric není v potaz, kolik vrstvy, které jsou v hierarchii domény selhání. Ale pokusí se ujistěte, že ke ztrátě všech jednu část hierarchie nebude mít vliv na běžící v ní. 

Je nejvhodnější při stejný počet uzlů na každé úrovni hloubka v hierarchii domény selhání. Pokud je "stromu" domén selhání nevyváženou v clusteru, obtížnější pro Resource Manager clusteru a pokuste se zjistit doporučené přidělení služeb. Imbalanced rozložení domén selhání znamenají, že ke ztrátě některých domén dopadu na dostupnost služeb víc než jiných doménách. V důsledku toho odpojí správce prostředků clusteru mezi dva cíle: chce použít na počítače v této doméně "těžká" na jejich umístění služby a chce umístit služby v jiných doménách tak, aby ztrátě domény není způsobit problémy. 

Co imbalanced domén vypadat? V následujícím diagramu ukážeme dvě rozložení jiného clusteru. V prvním příkladu jsou rovnoměrně rozloženy uzly na domén selhání. V druhém příkladu má jeden doména selhání mnoho více uzlů, než v jiných doménách selhání. 

<center>
![Dvě různé clusteru rozložení][Image2]
</center>

V Azure výběr z nich obsahuje doména selhání uzlu je spravovaná. Ale v závislosti na počtu uzlů, které můžete zřídit můžete stále skončili s domén selhání s více uzly v nich než jiné. Předpokládejme například že máte pět domén selhání v clusteru, ale zřídit sedmi uzlů pro daný typ uzlu. V takovém případě první dva domén selhání skončili s více uzly. Pokud budete pokračovat v nasazování další NodeTypes s pouze několik instancí, získá zhoršení problém. Z toho důvodu, který má doporučeno počet uzlů v každém uzlu typ je násobkem počet domén selhání.

## <a name="upgrade-domains"></a>Domén upgradu
Upgradu domény jsou další funkce, která pomáhá pochopit rozložení clusteru správce prostředků clusteru Service Fabric. Domén upgradu definovat sadu uzlů, které jsou upgradovány na stejnou dobu. Domén upgradu pomoct pochopit a orchestraci management operace, jako je upgrady správce prostředků clusteru.

Upgradu domény jsou mnohem jako domén selhání, ale s několik hlavní rozdíly. Nejprve oblasti selhání koordinované hardwaru definování domén selhání. Zásady jsou definované upgradu domény, na druhé straně. Bude docházet k rozhodování o tom, kolik chcete, místo ho se závisí na prostředí. Můžete mít tolik upgradu domény jako uzly. Další rozdíl mezi domén selhání a upgradu domény je, že Upgrade domény nejsou hierarchické. Místo toho se více podobají prostřednictvím jednoduchých značek. 

Následující diagram znázorňuje, že jsou tři domény upgradu rozdělené mezi tři domény selhání. Také ukazuje jeden možné umístění pro tři různé repliky stavové služby, kde každý skončilo v různých selhání a upgradu domény. Toto umístění umožňuje ztrátu domény selhání při uprostřed upgrade služby a ještě jednu kopii kód a data.  

<center>
![Umístění s doménách selhání a upgradu][Image3]
</center>

Existují výhody a nevýhody na situaci, kdy velký počet domén upgradu. Další domény upgradu znamená každého kroku upgradu je podrobnější a ovlivňuje menší počet uzlů nebo služby. V důsledku toho méně služby mají přesunout současně, představení méně změn do systému. To může zvýšit spolehlivost, protože méně služby je ovlivněn jakýkoli problém zavedená během upgradu. Další domény upgradu také znamená, je nutné, aby méně dostupné vyrovnávací paměti na jiných uzlech pro zpracování dopad upgradu. Například pokud máte pět upgradu domén, uzly v každé jsou zpracování zhruba 20 % z provozu. Pokud potřebujete vypnout upgradu domény pro účely upgradu, aby byla zátěž obvykle musí někde přejděte. Vzhledem k tomu, že máte čtyři zbývající domény upgradu, každý musí mít dostatek místa pro přibližně 5 % celkového provozu. Další domény upgradu znamená, že budete potřebovat méně vyrovnávací paměť v uzlech v clusteru. Zvažte například, pokud jste měli 10 upgradu domén místo. V takovém případě každý UD by pouze zpracovávat přibližně 10 % celkového provozu. Při upgradu kroky prostřednictvím clusteru, třeba, aby měl prostor pro přibližně 1.1 % celkového provozu pouze každou doménu. Další domény upgradu obecně umožňuje spouštět uzly s vyšší využití, vzhledem k tomu, že budete potřebovat méně rezervované kapacity. Totéž platí pro domény selhání.  

Nevýhodou, že máte velký počet domén upgradu je, že upgrady mívají trvá déle. Service Fabric čeká krátké době po doména Upgrade a provádí kontroly před zahájením upgradu na další stránku. Tyto zpoždění povolit zjišťuje problémy, které jsou zavedené službou upgrade před upgradem. Kompromis je přijatelné, protože chybný změny brání by to ovlivnilo příliš mnoho služby najednou.

Příliš málo upgradu domény má mnoho záporné vedlejší účinky – při každé jednotlivé upgradu domény je mimo provoz a upgraduje velkou část celkové kapacity není k dispozici. Například pokud máte pouze tři domény upgradu můžete trvá dolů o 1/3 celkové služby nebo kapacity clusteru současně. Značná část služby najednou s dolů není žádoucí, vzhledem k tomu, že budete muset mít dostatečnou kapacitu ve zbývající části vašeho clusteru tak umožní zpracovávat zatížení. Zachování této vyrovnávací paměti znamená, že při běžném provozu tyto uzly jsou menší načíst, než by byly jinak. Tím se zvyšuje náklady na provozování vaší služby.

Neexistuje žádné skutečné omezení celkového počtu selhání nebo upgradu domény v prostředí nebo omezení toho, jak se překrývají. Ale nutné dodat, existuje několik běžných vzorů:

- Domén selhání a upgradu domény namapovat 1:1
- Jednu doménu upgradu na uzel (fyzické nebo virtuální instance operačního systému)
- Model "prokládané" nebo "matice", kde domén selhání a upgradu domény formuláři pomocí počítače, které obvykle běží dolů úhlopříčkami matici

<center>
![Selhání a upgradu domény rozložení][Image4]
</center>

Není definován že žádný nejvhodnější odpovědět, jaké rozložení byste měli zvolit, každá z nich má některé výhody a nevýhody. 1FD:1UD model je například jednoduché nastavení. 1 upgradu domény na uzlu modelu je většina jako osoby, které se používají k. Během upgradu se aktualizuje každý uzel nezávisle. Toto je podobná jak malého měla sady počítačů upgradovány ručně v minulosti. 

Nejběžnější model je FD/UD matice, kde FDs a UDs vytvoří tabulku a uzly jsou umístěny společně diagonálních spouštění. Jedná se o model používá ve výchozím nastavení v clusterů Service Fabric v Azure. U clusterů s uzly mnoho všechno, co skončilo vyhledávání jako vzoru hustých matice výše.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Selhání a upgradu domény omezení a výsledné chování
### <a name="default-approach"></a>*Výchozí přístup*
Ve výchozím nastavení udržuje správce prostředků clusteru služby rovnoměrně rozdělen mezi selhání a upgradu domény. To je modelovaná jako [omezení](service-fabric-cluster-resource-manager-management-integration.md). Omezení stavy selhání a upgradu domény: "pro danou službu oddíl, měla by nikdy existovat rozdíl větší než 1 v počet objektů služby (instance bezstavové služby nebo stavové služby repliky) mezi dvěma doménami na stejné úrovni hierarchie". Řekněme, že toto omezení poskytuje záruku "maximální rozdíl". Omezení selhání a upgradu domény zabraňuje určené přesune nebo uspořádání, které porušují pravidlo uvedené výše. 

Podívejme se na jedním z příkladů. Řekněme, že se musí na cluster se šesti uzlů, nakonfigurovaný s pěti domén selhání a pět upgradu domény.

|  | FD0 | FD1 | FD2: | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

*Konfigurace 1*

Nyní Řekněme, že jsme pěti vytvořit službu s TargetReplicaSetSize (nebo bezstavové služby na InstanceCount). Repliky zobrazovat N1 N5. Ve skutečnosti N6 se nikdy nepoužívá bez ohledu na to, kolik služby, jako to, které vytvoříte. Ale proč? Podívejme se na rozdíl mezi aktuální rozložení a co by mohlo dojít, pokud je zvoleno N6.

Tady je My rozložení a celkového počtu replik na selhání a upgradu domény:

|  | FD0 | FD1 | FD2: | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Rozložení 1*


Toto rozložení jsou rovnoměrně z hlediska uzlů na doméně selhání a upgradu domény. Toto pravidlo jsou rovnoměrně také z hlediska počtu replik na selhání a upgradu domény. Každá doména má stejný počet uzlů a stejný počet replik.

Teď se podíváme, co by se stalo jsme použití N6 místo N2. Jak by repliky distribuována pak?

|  | FD0 | FD1 | FD2: | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

*Rozložení 2*


Toto rozložení porušuje naše definice záruky "maximální rozdíl" pro omezení domény selhání. FD0 má dvě repliky, zatímco FD1 obsahuje nula, a tím rozdíl mezi FD0 a FD1 celkem dva týdny, která je větší než maximální rozdíl jednoho. Vzhledem k tomu, že porušení omezení správce prostředků clusteru není povoleno toto uspořádání. Podobně pokud jsme zachyceny N2 a N6 (namísto N1 a N2) nám se získat:

|  | FD0 | FD1 | FD2: | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Rozložení 3*


Toto rozložení jsou rovnoměrně z hlediska domén selhání. Ale nyní ho porušuje omezení upgradu domény protože UD0 má nulové repliky, zatímco UD1 má dva. Toto rozložení proto je neplatná a nebude být zachyceny pomocí Správce prostředků clusteru.

Tento přístup k distribuci stavová repliky nebo bezstavové instancí poskytuje nejlepší možný odolnost proti chybám. V situaci, kdy jednu doménu přestane fungovat, minimální počet replik nebo instancí je ztraceny. 

Tento přístup na druhé straně může být příliš přísné a nepovolíte využívat všechny prostředky clusteru. Některé uzly nelze použít pro některé konfigurace clusteru. To může vést k Service Fabric není umístění vaší služby, což vede k zprávy upozornění. V předchozím příkladu některé uzly clusteru nelze použít (N6 v tomto příkladu). I v případě, že by přidat uzly do clusteru (N7 – N10), by replik nebo instancí umístit jenom na N1 – N5 z důvodu selhání a upgradu domény omezení. 

|  | FD0 | FD1 | FD2: | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

*Konfigurace 2*


### <a name="alternative-approach"></a>*Alternativní způsob*

Správce prostředků clusteru podporuje jinou verzi aplikace selhání a upgradu domény omezení, které umožňuje, aby umísťování při stále zaručení minimální úroveň zabezpečení. Alternativní omezení selhání a upgradu domény může být uvedeno, následujícím způsobem: "Pro danou službu oddíl repliky distribuční napříč doménami se ujistěte, oddíl nebude vyskytovat ztrátě kvora". Řekněme, že toto omezení poskytuje záruku "kvora bezpečné". 

> [!NOTE]
>Pro stavové služby, jsme definovali *ztrátě kvora* v situaci, když jsou většinou replik oddílu dolů ve stejnou dobu. Například pokud TargetReplicaSetSize pět, představuje sadu všechny tři repliky kvora. Podobně pokud TargetReplicaSetSize 6, čtyři repliky jsou nezbytné pro kvorum. Více než dvě repliky můžete v obou případech být mimo provoz ve stejnou dobu, pokud chce oddíl nebude fungovat normálně. Bezstavové služby není k dispozici žádná taková věc jako *ztrátě kvora* jako bezstavové služby conitnue k functionate normálně, i když většina instancí přejděte ve stejnou dobu. Proto se zaměříme na stavové služby ve zbývající části textu.
>

Přejděte zpět do předchozí příklad. Verze "kvora bezpečné" omezení by všechny tři dané rozložení platný. Je to proto, že i v případě, že by selhání FD0 v druhém rozložení a UD1 v třetí rozložení, oddílu by ještě kvora (většinou jejích replik by být stále nahoru). S touto verzí omezení N6 může téměř vždy je možné využít.

"Kvora bezpečné" přístup přináší více flexibility než přístup "maximální rozdíl", jako je snazší najít distribuce repliky, které jsou platné v téměř jakoukoli topologie clusteru. Však tento přístup nemůže zaručit nejlepší odolnost proti chybám charakteristiky protože několika selháními horší než jiné. V nejhorším scénářem případu by mohly být ztraceny s chybou jednu doménu a jedna další replika většinou replik. Například místo nich vyžaduje ke ztrátě kvora s 5 replik nebo instancí 3 selhání, můžete nyní ztratit většinou se ještě dvě selhání. 

### <a name="adaptive-approach"></a>*Adaptivní přístup*
Protože přístupy mají silné a slabé stránky, zavedli jsme adaptivní přístup, který kombinuje tyto dvě strategie.

> [!NOTE]
>To bude výchozí chování počínaje Service Fabric verze 6.2. 
>
Adaptivní přístup ve výchozím nastavení používá logiku "maximální rozdíl" a přepínače na logice "kvora bezpečné" pouze v případě potřeby. Správce prostředků clusteru automaticky hodnoty si strategii, kterou je nutné prohlížením konfiguraci clusteru a služby. Pro danou službu: *Pokud dělitelná počet domén selhání a počet domén upgradu TargetReplicaSetSize **a** počet uzlů je menší než nebo rovno (počet domén selhání) * (na počet domén upgradu), Cluster Resource Manager by měla využívat "kvora na základě" logiku pro tuto službu.* Berte v úvahu, že správce prostředků clusteru bude tuto metodu použijte pro bezstavové a stavové služby, bez ohledu ztrátě kvora nebude relevantní pro bezstavové služby.

Přejděte zpět do předchozí příklad a předpokládají, že cluster má nyní 8 uzlů (cluster je nakonfigurovaný pořád pět domén selhání a pět upgradu domén a TargetReplicaSetSize služby hostované na tento cluster zůstane pět). 

|  | FD0 | FD1 | FD2: | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

*Konfigurace 3*

Vzhledem k tomu, že všechny nezbytné podmínky jsou splněny, bude správce prostředků clusteru využít logice "na základě kvora" v distribuci službu. To umožňuje použití N6 – N8. K jednomu distribučnímu možné služby v takovém případě může vypadat podobně jako:

|  | FD0 | FD1 | FD2: | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

*Rozložení 4*

Pokud vaše služba TargetReplicaSetSize je snížen na čtyři (například), bude správce prostředků clusteru Všimněte si, že změny a obnovit pomocí "maximální rozdíl" logiku, protože už není dělitelné počet FDs a UDs TargetReplicaSetSize. Některé repliky pohybů v důsledku toho dojde k distribuci zbývající čtyři repliky na uzlech N1 N5 tak, aby "maximální rozdíl" verze logiku domény selhání a upgradu domény není došlo k porušení. 

Vyhledávání zpět na čtvrtou rozložení a TargetReplicaSetSize pět. Pokud je z clusteru odebrán N1, počet domén upgradu dřívější čtyři. Správce prostředků clusteru znovu spustí pomocí "maximální rozdíl" logiku, počet UDs není rozdělení rovnoměrně TargetReplicaSetSize služby už. V důsledku toho musí zobrazovat N4 tak, aby selhání a omezení domény upgradu není došlo k porušení repliky R1, když znovu sestaven.

|  | FD0 | FD1 | FD2: | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |neuvedeno |neuvedeno |neuvedeno |neuvedeno |neuvedeno |neuvedeno |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Rozložení 5*

## <a name="configuring-fault-and-upgrade-domains"></a>Konfigurace selhání a upgradu domény
Definování domén selhání a upgradu domény se provádí automaticky v Azure hostovaná nasazení Service Fabric. Service Fabric převezme a používá informace o prostředí z Azure.

Pokud vytváříte vlastní cluster (nebo chcete spustit konkrétní topologie vývojem), je informace o doméně selhání a upgradu domény můžete zadat sami. V tomto příkladu jsme definovali místní vývojový cluster devět uzlu, který zahrnuje tři "datových centrech" (každý má tři stojany). Tento cluster má také tři domény upgradu rozdělená mezi tyto tři datových centrech. Zde je příklad konfigurace: 

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
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

pomocí souboru ClusterConfig.json pro samostatné nasazení

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
> Při definování clusterů pomocí Azure Resource Manager, jsou přiřazeny domén selhání a upgradu domény Azure. Definice typů uzlů a sady škálování virtuálního počítače do šablony Azure Resource Manager tedy nezahrnuje doména selhání nebo informace o upgradu domény.
>

## <a name="node-properties-and-placement-constraints"></a>Vlastnosti uzlu a omezení umístění
V některých případech (v faktu, ve většině případů) budete chtít zajistit, že určité úlohy spustit pouze v určitých typů uzlů v clusteru. Pro některé zatížení může například vyžadovat grafickými procesory nebo SSD a jiné nikoli. Skvělé příkladem cílení na hardware pro konkrétní úlohy je téměř každé n vrstvá architektura odhlašování došlo. Některé počítače sloužit jako front-endu nebo obsluhující straně aplikace API a jsou viditelné na klienty nebo internet. Různé počítače, často se různé hardwarové prostředky, zpracování pracovní vrstev výpočtů nebo úložiště. Obvykle se jedná o _není_ přímo vystavený pro klienty nebo Internetu. Service Fabric očekává, že existují případy, kdy je potřeba spustit na konkrétní hardwarové konfigurace konkrétní úlohy. Příklad:

* stávající vícevrstvé aplikace byla "zrušeno a zapuštěno" do prostředí Service Fabric
* zatížení chce, aby běžela na konkrétní hardware pro výkon, škálování nebo z důvodu izolace zabezpečení
* Úloha by měla být izolované od ostatních úloh pro zásady nebo prostředek spotřebu důvody

Pro podporu těchto nastavení neovlivní konfigurace, Service Fabric má první třídy představu o značky, které lze použít na uzly. Tyto značky se nazývají **vlastnosti uzlu**. **Omezení umístění** jsou příkazy, které jsou připojené k jednotlivé služby, které vyberte jeden nebo více vlastností uzlu. Omezení umístění definovat, které by měly běžet služby. Sadu omezení je rozšiřitelný – může fungovat všechny dvojice klíč/hodnota. 

<center>
![Různé rozložení zatížení clusteru][Image5]
</center>

### <a name="built-in-node-properties"></a>Součástí vlastnosti uzlu
Service Fabric definuje některé výchozí uzel vlastnosti, které lze použít automaticky bez nutnosti definovat uživatele. Výchozí vlastnosti definované v každém uzlu jsou **NodeType** a **NodeName**. Můžete napsat tak například omezení umístění jako `"(NodeType == NodeType03)"`. Obecně našli jsme, že NodeType bude jeden z nejvíc běžně používá vlastnosti. Je vhodné vzhledem k tomu, že odpovídá 1:1 s typem počítače. Každý typ počítače odpovídá typu úlohy v tradiční n vrstvá aplikaci.

<center>
![Omezení umístění a vlastnosti uzlu][Image6]
</center>

## <a name="placement-constraint-and-node-property-syntax"></a>Omezení umístění a syntaxe vlastnosti uzlu 
Hodnota zadaná ve vlastnosti uzlu může být řetězec, bool, nebo podepsané dlouho. Příkaz na službu se označuje jako umístění *omezení* vzhledem k tomu, že se omezí, kde se služba může běžet v clusteru. Omezení může být jakékoli Boolean příkaz, který funguje ve vlastnostech jiného uzlu v clusteru. Jsou platné selektory v těchto příkazech logická hodnota:

1) Podmíněné kontroly pro vytvoření konkrétní příkazy

| Výraz | Syntaxe |
| --- |:---:|
| "rovno" | "==" |
| "není rovno" | "!=" |
| "větší než" | ">" |
| "větší než nebo rovno" | ">=" |
| "menší než" | "<" |
| "menší než nebo rovno" | "<=" |

2) Logická hodnota příkazy pro seskupování a logické operace

| Výraz | Syntaxe |
| --- |:---:|
| "a" | "&&" |
| "nebo" | "&#124;&#124;" |
| "Ne" | "!" |
| "skupiny jako jediný příkaz" | "()" |

Zde jsou některé příklady příkazů základní omezení.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Službu umístit na něm může mít pouze uzly, které vyhodnotí příkaz celkové omezení umístění na hodnotu "True". Uzly, které nemají vlastnost definovanou se neshodují žádné omezení umístění obsahující tuto vlastnost.

Řekněme, že následující vlastnosti uzlu byly definovány pro daný uzel typu:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

pomocí souboru ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery. 

> [!NOTE]
> V šabloně Azure Resource Manager je obvykle parametry typ uzlu. Může vypadat třeba "[parameters('vmNodeType1Name')]" místo "NodeType01".
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

Můžete vytvořit umístění služby *omezení* pro službu, jako následujícím způsobem:

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Prostředí PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Pokud jsou všechny uzly NodeType01 platné, můžete také vybrat daný typ uzlu s omezením v atributu "(NodeType == NodeType01)".

Jedním z nástrojů věcí o omezení umístění služby je, aby bylo možné aktualizovat dynamicky za běhu. Takže pokud budete potřebovat, abyste můžete pohyb služby v clusteru, přidávat a odebírat požadavky atd. Service Fabric postará zajistit, že službu zůstává nahoru a k dispozici i v případě, že jsou vytvářeny těchto typů změn.

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Prostředí PowerShell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Pro všechny různé služby s názvem instance jsou zadány omezení umístění. Aktualizace vždy provést místa (Přepsat) byl dříve zadán.

Definice clusteru definuje vlastnosti na uzlu. Změna vlastnosti uzlu vyžaduje s upgradem konfigurace clusteru. Upgrade vlastnosti uzlu vyžaduje každý ovlivněných uzel restartuje, aby se jeho nové vlastnosti sestav. Tyto vrácení upgradů Service Fabric spravuje.

## <a name="describing-and-managing-cluster-resources"></a>Popisující a Správa prostředků clusteru
Jedním z nejdůležitějších úlohy žádné orchestrator je ke správě spotřeby prostředků v clusteru. Správa prostředků clusteru může zahrnovat několik různých věcí. Nejprve existuje zajišťuje, že počítače nejsou přetížené. To znamená, a ujistěte se, že počítače nejsou spuštěné další služby, než se může zpracovat. Druhou je, že vyrovnávání a optimalizace, které jsou kritická pro efektivní spuštění služby. Některé uzly je aktivní, zatímco jiné jsou cold nemůže povolit náklady nabídky služeb citlivé platné nebo výkonu. Aktivní uzly vést k sporu prostředků a nízký výkon a cold uzly představují nevyužité zdroje a vyšší náklady. 

Service Fabric představuje prostředky jako `Metrics`. Metriky jsou logickou nebo fyzickou prostředku, který chcete popisují do Service Fabric. Příklady metrik věci jako "WorkQueueDepth" nebo "MemoryInMb". Informace o fyzické prostředky, které můžou řídit Service Fabric na uzlech naleznete v tématu [zásad správného řízení prostředků](service-fabric-resource-governance.md). Informace o konfiguraci vlastní metriky a jejich použití naleznete v tématu [v tomto článku](service-fabric-cluster-resource-manager-metrics.md)

Metriky se liší od umísťováním omezení a vlastnosti uzlu. Vlastnosti uzlu jsou statické popisovače v samotných uzlech. Metriky popisují prostředky, které mají uzly a že využívat služby při spuštění na uzlu. Vlastnost uzlu může být "HasSSD" a může být nastavena na hodnotu true nebo false. Množství místa, které jsou k dispozici v této SSD a kolik je využívána služby by metrika jako "DriveSpaceInMb". 

Je důležité si uvědomit, že stejně jako omezení umístění a vlastnosti uzlu, správce prostředků clusteru Service Fabric nerozumí co znamená názvy metriky. Metriky názvy jsou jenom řetězce. Je dobrým zvykem deklarovat jednotky jako součást metriky názvy, které vytvoříte, když může být nejednoznačný.

## <a name="capacity"></a>Kapacita
Pokud jste měli vypnuté všechny prostředků *vyrovnávání*, správce prostředků clusteru Service Fabric stále zajistí, že žádný uzel skončila přes své kapacity. Správa přetečení kapacity je možné, pokud je příliš plná clusteru nebo zatížení je větší než libovolného uzlu. Kapacita je jiné *omezení* že zjistit, kolik prostředků a uzel se používá správce prostředků clusteru. Zbývající kapacity jsou také sledovány clusteru jako celek. Kapacita a spotřeba na úrovni služby jsou vyjádřeny z hlediska metriky. Tak například Metrika může být "ClientConnections" a daný uzel je pravděpodobně kapacity pro "ClientConnections" z 32768. Další uzly může mít jiné omezení některé služby spuštěné v tomto uzlu můžete například ho aktuálně spotřebovává 32256 metrika "ClientConnections".

Během doby běhu správce prostředků clusteru sleduje zbývající kapacity v clusteru a na uzlech. Aby bylo možné sledovat kapacity odečítá správce prostředků clusteru využití každé služby z uzlu kapacity, kde je služba spuštěna. Pomocí těchto informací může správce prostředků clusteru Service Fabric rozmyslete si, kam umístit nebo přesunout repliky tak, aby uzly si projít kapacity.

<center>
![Uzly clusteru a kapacity][Image7]
</center>

C#:

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

Prostředí PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

Můžete zjistit kapacity definované v manifestu clusteru:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

pomocí souboru ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery. 

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

Běžně služby načíst dynamicky změny. Řekněme, že zatížení repliky "ClientConnections" změnil z hodnoty 1 024 na 2048, ale na uzel, který byl spuštěn na pak pouze byl 512 kapacita zbývající pro tuto metriku. Nyní repliky nebo instance umístění je neplatná, protože v tomto uzlu není dostatek místa. Správce prostředků clusteru má nové a získat uzel zpět níže kapacity. Snižuje zatížení na uzlu, který je nad kapacity přesunutím jeden nebo víc replik nebo instancí z tohoto uzlu do dalších uzlů. Při přesunu repliky, pokusí se správce prostředků clusteru minimalizovat náklady na těchto pohybů. Náklady na přesunutí popsané v [v tomto článku](service-fabric-cluster-resource-manager-movement-cost.md) a informace o správce prostředků clusteru je vyrovnává strategie a pravidla je popsaný [zde](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Kapacita clusteru
Jak tedy správce prostředků clusteru Service Fabric zachovat celkové clusteru z příliš zaplnění? Dobře s dynamického zatížení, není k dispozici mnoho ho provést. Služby mohou mít jejich zatížení Špička nezávisle na akce prováděné pomocí Správce prostředků clusteru. Cluster s dostatek prostoru dnes v důsledku toho může být underpowered, když jste se famous zítra. Ale nutné dodat, nejsou některé ovládací prvky, které jsou v zaručená, aby se zabránilo problémům. První věcí, kterou by bylo možné službu je bránit vytvoření nové úlohy, které by způsobily clusteru plná.

Řekněme, že vytvoření bezstavové služby a obsahuje některé zatížení s ním spojená. Řekněme, že služba záleží metrika "DiskSpaceInMb". Dále předpokládejme, že se bude používat pět jednotky "DiskSpaceInMb" pro všechny instance služby. Chcete vytvořit tři instancí služby. Výborně! To znamená, že potřebujeme 15 jednotky "DiskSpaceInMb" nacházet v clusteru, abychom vás mohli i tak nebude moci vytvořit tyto instance služby. Správce prostředků clusteru průběžně vypočítá kapacitu a využití každé metriky tak může zjistit, zbývající kapacity v clusteru. Pokud není k dispozici dostatek místa, správce prostředků clusteru odmítne volání služby vytvořit.

Vzhledem k tomu, že požadavek je pouze to, že existuje možné 15 jednotky, může být tento prostor přiděleno mnoha různými způsoby. Například může existovat jednu jednotku zbývající kapacity v 15 rozdílných uzlech, nebo tři zbývající jednotky kapacity na pět různých uzlech. Pokud správce prostředků clusteru můžete přeuspořádat věcí, takže na tři uzly je k dispozici pět jednotky, umístí službu. Změna uspořádání clusteru je obvykle možné, pokud je téměř plná clusteru, nebo z nějakého důvodu nelze konsolidovat stávající služby.

## <a name="buffered-capacity"></a>Kapacita ve vyrovnávací paměti
Kapacita ve vyrovnávací paměti je jiné funkce služby Správce prostředků clusteru. To umožňuje rezervace některé část celkové kapacity uzlu. Této kapacity vyrovnávací paměti se používá pouze k umístění služby během upgradu a selhání uzlu. Kapacita ve vyrovnávací paměti je zadán globálně za Metrika pro všechny uzly. Hodnota, kterou vyberete pro rezervované kapacity je funkce, počtu selhání a upgradu domén, které máte v clusteru. Další selhání a upgradu domény znamená, můžete si vybrat nižší číslo pro vaše kapacita ve vyrovnávací paměti. Pokud máte více domén, můžete očekávat snížení objemu clusteru a mohli být k dispozici během upgradu a selhání. Zadání uložená do vyrovnávací paměti kapacity má smysl pouze, pokud zadáte uzlu kapacity pro metriku.

Tady je příklad toho, jak určit kapacitu ve vyrovnávací paměti:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

pomocí souboru ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery:

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

Vytvoření nové služby selže, když clusteru je mimo ve vyrovnávací paměti kapacity pro metriku. Brání vytvoření nových služeb, aby byla zachována vyrovnávací paměti zajišťuje upgrady a selhání nezpůsobí uzly si projít kapacity. Kapacita ve vyrovnávací paměti je nepovinný, ale doporučuje se v jakékoli clusteru, který definuje kapacity pro metriku.

Správce prostředků clusteru zpřístupní tyto informace zatížení. Pro jednotlivé metriky tyto informace zahrnují: 
  - Nastavení kapacity ve vyrovnávací paměti
  - Celková kapacita
  - aktuální spotřeba
  - jestli je považován za jednotlivé metriky vyrovnáváním nebo ne
  - Statistika týkající se směrodatné odchylky
  - uzly, které mají nejvíce a minimálně zatížení  
  
Níže vidíte příklad tento výstup:

```posh
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

## <a name="next-steps"></a>Další postup
* Informace o architektuře a informačního toku v rámci správce prostředků clusteru, podívejte se na [v tomto článku ](service-fabric-cluster-resource-manager-architecture.md)
* Definování defragmentace metriky je jeden způsob, jak konsolidovat zatížení na uzlech místo rozšíří ho. Naučte se konfigurovat defragmentace, najdete v tématu [v tomto článku](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Začít od začátku a [získejte Úvod do Service Fabric clusteru správce prostředků](service-fabric-cluster-resource-manager-introduction.md)
* Chcete-li zjistit, o tom, jak správce prostředků clusteru spravuje a vyrovnává zatížení v clusteru, podívejte se na článek na [Vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png

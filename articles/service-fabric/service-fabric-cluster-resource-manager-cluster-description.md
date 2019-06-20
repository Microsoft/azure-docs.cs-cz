---
title: Popis clusteru pomocí Cluster Resource Manager | Dokumentace Microsoftu
description: Popisuje cluster Service Fabric s zadání domén selhání, upgradovacích domén, vlastnosti uzlu a kapacity uzlů pro Cluster Resource Manageru.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 22ccb21a208bbe8e825bff9f7602bfca05990816
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271645"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Popis clusteru Service Fabric pomocí Cluster Resource Manageru
Funkce Správce prostředků clusteru Azure Service Fabric poskytuje několik mechanismů pro popis clusteru:

* Domény selhání
* Upgradovací domény
* Vlastnosti uzlu
* Uzel kapacity

Cluster Resource Manageru za běhu, používá tyto informace k zajištění vysoké dostupnosti služby spuštěné v clusteru. Při vynucování tyto důležité pravidla, také pokusu o optimalizaci spotřeby prostředků v rámci clusteru.

## <a name="fault-domains"></a>Domény selhání
Doména selhání je všechny plochy koordinované selhání. Jeden počítač se doména selhání. Na svůj vlastní z různých důvodů se z power selhání napájení a selhání disku chybné NIC firmwaru může selhat. 

Počítače připojené ke stejnému přepínači sítě Ethernet, jsou ve stejné doméně selhání. Proto jsou počítače, které sdílejí jeden zdroj napájení nebo na jednom místě. 

Protože je přirozené pro hardwarových chyb můžete překrývat, jsou ze své podstaty hierarchické domén selhání. Už jsou reprezentovány jako identifikátory URI v Service Fabric.

Je důležité, že domén selhání jsou správně nastavené protože Service Fabric pomocí těchto informací k bezpečné umístění služby. Service Fabric nechce k umístění služby tak, aby ke ztrátě doména selhání (způsobila selhání některé komponenty) způsobí, že služba přejdete. 

V prostředí Azure Service Fabric používá informace o doméně selhání poskytovaných prostředím správnou konfiguraci uzlů v clusteru vaším jménem. Pro samostatné instance Service Fabric jsou definovány domén selhání v době, která je nastavená clusteru. 

> [!WARNING]
> Je důležité, že je přesné informace o doméně selhání k dispozici do Service Fabric. Například Řekněme, že uzly clusteru Service Fabric běží uvnitř 10 virtuálních počítačů běžících na 5 fyzických hostitelích. V takovém případě i když je 10 virtuálních počítačů, existují jenom 5 různých (nejvyšší úrovně) domén selhání. Sdílení na stejném fyzickém hostiteli způsobí virtuální počítače, které sdílejí stejnou kořenovou doménu selhání, protože virtuální počítače selhat koordinované Pokud se nezdaří jejich fyzickém hostiteli.  
>
> Service Fabric očekává, že doména selhání uzlu nebudou je moci měnit. Jiné mechanismy, jako třeba zajistit vysokou dostupnost virtuálních počítačů, [vysokou dostupnost virtuálních počítačů](https://technet.microsoft.com/library/cc967323.aspx), může způsobit, že je v konfliktu s využitím Service Fabric. Tyto mechanismy pomocí transparentní migrace virtuálních počítačů z jednoho hostitele do druhého. Jejich nejsou opakovaně konfigurovat nebo oznámit spuštění kódu v tomto virtuálním počítači. V důsledku toho jsou *nepodporuje* jako prostředí pro Service Fabric s clustery. 
>
> Service Fabric by měl být používané technologie pouze vysokou dostupnost. Mechanismů, jako jsou živé migrace virtuálních počítačů a sítě SAN nejsou nutné. Pokud tyto mechanismy se používají ve spojení s využitím Service Fabric, že _snížit_ aplikace dostupnost a spolehlivost. Důvodem je, že jejich zavést další složitosti, přidejte centralizované zdroje selhání a použití strategie spolehlivostí a dostupností, které jsou v konfliktu s těmi v Service Fabric. 
>
>

Na následujícím obrázku jsme barva všech entit, které přispívají k domén selhání a seznam všech různých domén selhání, které vyplývají. V tomto příkladu máme datová centra ("řadiče domény"), stojany ("R") a v oknech ("B"). Pokud každé okno obsahuje více než jeden virtuální počítač, může být další úroveň v hierarchii domény selhání.

<center>

![Uzly uspořádané prostřednictvím domén selhání][Image1]
</center>

Za běhu Service Fabric Cluster Resource Manager bere v úvahu domén selhání v clusteru a plány rozložení. Stavové replik nebo instancí bezstavové služby se distribuují tak, aby v samostatných doménách selhání. Služba distribuce napříč doménami selhání zajišťuje, že dostupnost služby není ohrožení selhání doména selhání na libovolné úrovni hierarchie.

Cluster Resource Manageru není pro vás počet vrstev jsou v hierarchii domény selhání. Pokusí se ujistěte, že ztráta jakékoli jedné části hierarchie nemá vliv na služby spuštěné v ní. 

Je vhodné, pokud je stejný počet uzlů na všech úrovních hloubka v hierarchii domény selhání. Pokud ve vašem clusteru je nevyvážené uvozovky "stromu" domén selhání, je těžší pro Cluster Resource Manager zjistit nejlepší přidělení služeb. Rozložení domény selhání imbalanced znamená, že ztráta některé domény má vliv na dostupnost služeb víc než jiných domén. V důsledku toho se odpojí Cluster Resource Manageru se mezi dva cíle: 

* Chce použít na počítačích v této doméně "heavy" tak, že služby na ně. 
* Chce umístí služeb v jiných doménách, aby nezpůsobí ztrátu domény problémy. 

Jak imbalanced domén vypadat? Následující diagram znázorňuje dvě rozložení jiného clusteru. V prvním příkladu se uzly rovnoměrně distribuovaných napříč doménami selhání. V druhém příkladu jednu doménu selhání má mnoho dalších uzlů, než v jiných doménách selhání. 

<center>

![Dvě různé clusteru rozložení][Image2]
</center>

V Azure volba, která poruch doména obsahuje uzel spravuje za vás. Ale v závislosti na počtu uzlů, které zřizujete, můžete stále skončit s doménami selhání, které mají více uzlů v nich než v jiných. 

Řekněme například, že máte 5 domén selhání v clusteru, ale zřízení sedm uzlů typu uzlu (**NodeType**). V takovém případě první dvěma doménami selhání skončit s více uzly. Pokud budete chtít nasadit další **NodeType** instance s pouze několika instancí, získá horší problém. Z tohoto důvodu doporučujeme vám, že počet uzlů v každém typu uzlu je násobkem počet domén selhání.

## <a name="upgrade-domains"></a>Upgradovací domény
Upgradovací domény jsou další funkce, která pomáhá Service Fabric Cluster Resource Manager Principy rozložení clusteru. Upgradovací domény definovat sady uzlů, které budou upgradovány ve stejnou dobu. Upgradovací domény pomáhají Cluster Resource Manageru, pochopit a orchestrovat operace správy, jako je inovace.

Upgradovací domény jsou mnohem jako domén selhání, ale s několik klíčových rozdílů. Nejprve definujte oblasti selhání hardwaru koordinované domén selhání. Upgradovacích domén, na druhé straně se definice zásady. Dostanete se rozhodnout, kolik chcete, místo určovat počet prostředí. Můžete mít libovolný počet upgradovacích domén, stejně jako uzly. Další rozdíl mezi doménami selhání a upgradovacích doménách je, že nejsou hierarchické upgradovacích domén. Místo toho jsou spíše jednoduchých značek. 

Následující diagram znázorňuje tři upgradovacích domén, které jsou rozdělená mezi tři domény selhání. Také ukazuje jeden možné umístění pro tří různých replik stavové služby, kde každý skončilo v různých chybových nebo upgradovacích doménách. Toto umístění umožňuje ztráty doménu selhání při uprostřed upgrade služby a ještě jednu kopii kódu a data.  

<center>

![Chybových nebo upgradovacích umístění s doménami][Image3]
</center>

Existují výhody a nevýhody mají velký počet upgradovacích domén. Více upgradovacích doménách znamenají jednotlivé kroky upgradu je podrobnější a ovlivňuje menší počet uzlů nebo služby. Méně musí přesunout najednou, Představujeme menší změny do systému. To může zlepšit spolehlivost, protože tolik služby je ovlivněna jakýkoli problém zavedené během upgradu. Více upgradovacích doménách také znamená, že potřebujete míň dostupné vyrovnávací paměti na jiných uzlech pro zpracování dopad upgradu. 

Například pokud máte pět upgradovacích domén, uzly v každém zvládají přibližně 20 procent společností z žebříčku provozu. Pokud potřebujete vypnout této upgradovací doména pro účely upgradu, obvykle potřeba někde přejděte zátěž. Protože máte čtyři zbývající upgradovacích domén, každý musí mít místo pro o 5 procent celkového provozu. Více upgradovacích doménách znamená, že budete potřebovat méně vyrovnávací paměti na uzlech v clusteru. 

Zvažte, pokud místo toho máte 10 upgradovacích domén. V takovém případě by být každý upgradovací doména zpracování přibližně 10 % z celkového provozu. Při upgradu prochází clusteru, třeba, aby měl místo pro pouze 1.1 procento celkového provozu každou doménu. Více upgradovacích domén obvykle umožňují provozovat uzly na vyšší využití, protože je budete potřebovat méně záložní kapacitu. Totéž platí pro domén selhání.  

Nevýhodou s mnoha upgradovacích domén je, že upgrady obvykle trvá déle. Service Fabric počká krátkou dobu, po dokončení upgradu domény a provádí kontroly před zahájením upgradu dalším objektem. Tato zpoždění povolit rozpoznání problémy spojené s uvedením upgradu před provedením upgradu. Kompromis představuje to je přijatelné, protože zabraňuje chybný změny výstrahy neovlivňovaly příliš velkou část služby najednou.

Přítomnost moc malý počet upgradovacích domén má mnoho záporné vedlejší účinky. I když každá doména upgradu je dolů a upgraduje, velkou část celkové kapacity není k dispozici. Například pokud máte pouze tři upgradovacích domén, přenášíte dolů o jednu třetinu služby nebo kapacita clusteru současně. Tolik služby najednou s dolů není žádoucí, protože budete potřebovat dostatečnou kapacitu pro zpracování úlohy ve zbývající části vašeho clusteru. Udržování že znamená, že vyrovnávací paměti při normálním provozu, tyto uzly jsou menší načtená, než by se jinak. Tím se zvyšuje náklady na provozování vaší služby.

Neexistuje žádné skutečné omezení celkového počtu domén selhání nebo upgradu v prostředí, nebo omezení na tom, jak se překrývají. Existuje ale obvyklé scénáře:

- Domény selhání a upgradovacích doménách namapované 1:1
- Jednu upgradovací doménu na jeden uzel (fyzický nebo virtuální instance operačního systému)
- Modelu "prokládané" nebo "Přehled", kde doménami selhání a upgradovacích doménách formulář se počítače, které obvykle běží dolů šikmé rozdělení matice

<center>

![Rozložení chybových nebo upgradovacích doménách][Image4]
</center>

Neexistuje žádný nejlepší odpověď pro jaké rozložení byste měli zvolit. Každá obsahuje výhody a nevýhody. Například 1FD:1UD modelu je snadné nastavení. Model jednu upgradovací doménu za uzel model se nejvíc jako názory lidí se používají k. Během upgradu každý uzel aktualizován nezávisle na sobě. To se podobá jak malý byly sady počítačů upgradovány ručně v minulosti.

Nejběžnější vzor je FD/ud stejný matice, kde doménami selhání a upgradovacích doménách formu tabulku a uzly jsou umístěny spuštění po diagonální. Jedná se o model používá ve výchozím nastavení na clusterech Service Fabric v Azure. U clusterů s mnoha uzly všechno, co končí vypadá jako vzor hustému matice.

> [!NOTE]
> Clustery Service Fabric hostované v Azure nepodporují, změna výchozí strategie. Pouze samostatné clustery nabízí tohoto vlastního nastavení.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Omezení domény selhání a upgradu a výsledné chování
### <a name="default-approach"></a>Výchozí přístup
Ve výchozím nastavení Cluster Resource Manageru udržuje služby vyvažují mezi chybových nebo upgradovacích doménách. To je modelovaná jako [omezení](service-fabric-cluster-resource-manager-management-integration.md). Omezení pro chybových nebo upgradovacích doménách stavy: "Pro oddíl dané služby by nikdy existovat rozdíl větší než jedna v počet objektů služeb (Bezstavová služba instancí nebo replik pro stavové služby) mezi dvěma doménami na stejné úrovni hierarchie."

Řekněme, že toto omezení poskytuje záruku "maximální rozdíl". Omezení pro chybových nebo upgradovacích doménách brání určitých operací přesunutí nebo ujednání, které pravidlo porušují.

Například Řekněme, že máme cluster s uzly šesti, nakonfigurovaný s pěti doménami selhání a pěti upgradovacích domén.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Nyní Řekněme, že se nám vytvořit službu pomocí **TargetReplicaSetSize** (nebo pro bezstavovou službu **InstanceCount**) hodnotu 5. Repliky se objevil na N1 N5. Ve skutečnosti N6 se nikdy nepoužívá bez ohledu na to, kolik služby, jako jsou to, které vytvoříte. Ale proč? Podívejme se na rozdíl mezi aktuální rozložení a co by mohlo dojít, pokud je zvolená N6.

Tady je rozložení, které nám dává a celkového počtu replik chybových nebo upgradovacích doménu:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Toto rozložení je balanced z hlediska uzlů na doména selhání a upgradovací domény. To je také balanced z hlediska počtu replik chybových nebo upgradovacích doménu. Každá doména má stejný počet uzlů a stejný počet replik.

Teď se podívejme se na co by mohlo dojít, pokud jsme použili N6 místo N2. Jak by tyto repliky distribuovat pak?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Toto rozložení v rozporu s naší definicí záruku "maximální rozdíl" omezení domény selhání. FD0 má dvě repliky, zatímco FD1 má nula. Rozdíl mezi FD0 a FD1 je celkem 2, což je větší než maximální rozdíl jednoho. Protože porušení omezení neumožňuje toto uspořádání Cluster Resource Manageru. Podobně pokud výběru N2 a N6 (namísto N1 a N2), dostali bychom:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Toto rozložení je balanced z hlediska domén selhání. Ale nyní porušuje omezení upgradovací doméně, protože UD0 má nulovou replik a UD1 má dvě. Toto rozložení je neplatné a nebudou odebrány pomocí Cluster Resource Manageru.

Tento přístup k distribuci replik stavových nebo bezstavových instancí poskytuje nejlepší možné odolnosti proti chybám. Pokud jedna doména ocitne mimo provoz, dojde ke ztrátě minimální počet replik nebo instancí. 

Tento přístup na druhé straně může být příliš přísné a, aby mohli využívat všechny prostředky clusteru. Některé uzly nelze použít pro některé konfigurace clusteru. To může způsobit Service Fabric není umístit vaše služby, což vede k upozornění. V předchozím příkladu, některé z uzlů clusteru nesmí být použita (N6 v příkladu). I v případě, že jste přidali uzly do tohoto clusteru (N7 N10), replik a instancí by použít pouze u N1 – N5 z důvodu omezení chybových nebo upgradovacích doménách. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>Alternativním přístupem

Cluster Resource Manager podporuje jinou verzi omezení pro chybových nebo upgradovacích doménách. Umístění je možné při zůstává zaručena minimální úroveň zabezpečení. Alternativní omezení může být uvedeno následujícím způsobem: "Pro určitou službu oddíl, distribuce replik napříč doménami zajistil, že oddíl nezpůsobuje žádné ztrátě kvora." Řekněme, že toto omezení poskytuje záruku "kvora bezpečné". 

> [!NOTE]
> Pro stavové služby definujeme *ztráty kvora* v situaci, když většinou replik oddílů jsou vypnuté ve stejnou dobu. Například pokud **TargetReplicaSetSize** je pět, představuje sadu všechny tři repliky kvora. Podobně pokud **TargetReplicaSetSize** je šest čtyři repliky jsou nezbytné pro kvorum. Více než dvě repliky můžou v obou případech být mimo provoz ve stejnou dobu, pokud chce oddílu dál normálně fungovat. 
>
> Pro bezstavovou službu, je i *ztráty kvora*. Bezstavové služby dál normálně fungovat i v případě, že většina instancí, přestanou fungovat ve stejnou dobu. Zaměříme se tedy na stavové služby ve zbývající části tohoto článku.
>

Přejděte zpět do předchozího příkladu. S verzí "kvora bezpečné" omezení by všechna tři rozložení platný. I v případě selhání FD0 v druhé rozložení nebo UD1 selhání v třetí rozložení, oddílu by stále mít kvorum. (Většinou repliky stále bude nahoru.) V této verzi omezení můžete téměř vždy používat N6.

"Kvora bezpečné" přístup přináší více flexibility než "maximální rozdíl" přístup. Důvodem je, že je snazší najít distribuce repliky, které jsou platné v téměř jakékoli Clusterová topologie. Však tento přístup nemůže zaručit nejlepší odolnost proti chybám charakteristiky vzhledem k tomu, že některé chyby jsou horší než jiné. 

V nejhorším případě velká může dojít ke ztrátě kvůli chybě jednu doménu a jedna další replika většinou replik. Například místo nich vyžaduje ke ztrátě kvora s pěti replik nebo instancí třech selháních, můžete nyní ztratit většinou s chybami jen dva. 

### <a name="adaptive-approach"></a>Adaptivní přístup
Vzhledem k tomu, že oba přístupy mít silné a slabé stránky, jsme naši nabídku doplnili adaptivní přístup, který kombinuje těchto dvou strategií.

> [!NOTE]
> Toto je výchozí chování, spouští se s platformou Service Fabric verze 6.2. 
> 
> Adaptivní přístup ve výchozím nastavení používá "maximální rozdíl" logika a přepne do logiky "kvora bezpečné" pouze v případě potřeby. Cluster Resource Manageru se automaticky zjistí strategii, kterou je nutné zobrazením konfiguraci clusteru a služeb.
> 
> Cluster Resource Manageru by měl pomocí logiky "na základě kvora" pro službu oba z těchto podmínek jsou splněny:
>
> * **TargetReplicaSetSize** pro službu je rovnoměrně dělitelné podle počtu domén selhání a počet upgradovacích domén.
> * Počet uzlů je menší než nebo rovno počtu domén selhání vynásobené počet upgradovacích domén.
>
> Berte v úvahu, že Cluster Resource Manager bude tuto metodu použijte pro bezstavové a stavové služby, i když není relevantní pro bezstavové služby ztrátě kvora.

Přejděte zpět do předchozího příkladu a předpokládá, že cluster má teď osmi uzlů. S pěti doménami selhání a pěti upgradovacích domén, je stále probíhá konfigurace clusteru a **TargetReplicaSetSize** pět zůstává hodnota službě hostované na tomto clusteru. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Protože všechny nezbytné podmínky jsou splněny, Cluster Resource Manager použije logiku "na základě kvora" v distribuci služby. To umožňuje použití N6 N8. K jednomu distribučnímu možné služby v tomto případě může vypadat takto:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Pokud vaše služba **TargetReplicaSetSize** hodnota je omezená na čtyři (například), Cluster Resource Manageru se zobrazují tuto změnu. Bude pokračovat, protože pomocí logiky "maximální rozdíl" **TargetReplicaSetSize** už není dělitelné počet domén selhání a upgradovacích domén. V důsledku toho některé repliky pohybů plb typu dojde k distribuci zbývající čtyři repliky v uzlech N1 N5. Tímto způsobem "maximální rozdíl" verzi selhání logiku domény domény a upgrade není došlo k porušení. 

V předchozí rozložení Pokud **TargetReplicaSetSize** hodnota je pět a N1 se odebere z clusteru, počet domén upgradu změní rovna 4. Znovu spustí Cluster Resource Manageru pomocí logiky "maximální rozdíl", protože počet domén upgradu nebude rovnoměrně rozdělit služby **TargetReplicaSetSize** už hodnotu. V důsledku toho má objevil na N4 tak, aby se došlo k porušení omezení pro doménu chybových nebo upgradovacích replika R1, když znovu sestaven.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |neuvedeno |neuvedeno |neuvedeno |neuvedeno |neuvedeno |neuvedeno |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Konfigurace chybových nebo upgradovacích doménách
V nasazení aplikace Service Fabric hostovaný v Azure jsou doménami selhání a upgradovacích doménách definovány automaticky. Service Fabric vybere a použije informace o prostředí z Azure.

Pokud vytváříte vlastní cluster (nebo chcete spustit konkrétní topologie ve vývoji), můžete zadat doména selhání a informace o doméně upgradu sami. V tomto příkladu definujeme devět uzlu místní vývojový cluster, který zahrnuje tři datová centra (každý má tři stojany). Tento cluster má také tři upgradovacích domén, které jsou rozdělená mezi tyto tři datových centrech. Tady je příklad konfigurace v ClusterManifest.xml:

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

Tento příklad používá ClusterConfig.json pro samostatné nasazení:

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
> Během definování clustery prostřednictvím Azure Resource Manageru, Azure přiřadí domén selhání a upgradovacími doménami. Proto nastaví definici typy uzlů a VM scale v šablony Azure Resource Manageru neobsahuje informace o doméně selhání a upgradovací domény.
>

## <a name="node-properties-and-placement-constraints"></a>Vlastnosti uzlu a omezení umístění
V některých případech (ve skutečnosti ve většině případů) budete chtít zajistit, že určité úlohy spouštěny pouze v určitých typů uzlů v clusteru. Například některé úlohy mohou vyžadovat grafickými procesory nebo disky SSD a ostatními nemusí. 

Skvělé příkladem cílení na hardware pro konkrétní úlohy je téměř každé n vrstvou architekturu. Některé počítače jako části front end a straně aplikace slouží rozhraní API a jsou přístupné na klientech nebo na internet. Různých počítačích, často a obsahují různé hardwarové prostředky, zpracování pracovní vrstvy výpočetního výkonu a úložiště. Obvykle se jedná o _není_ přímo zveřejněné klientům nebo Internetu. 

Service Fabric očekává, že v některých případech se konkrétní úlohy může být nutné spouštět na konkrétní hardwarové konfigurace. Příklad:

* Existující n vrstvou aplikaci byla "zrušeno a posunuta" do prostředí Service Fabric.
* Úloha musí být spuštěný na konkrétní hardware z důvodu izolace výkonu, škálování a zabezpečení.
* Úloha by měla být izolované od jiných úloh důvodů využití zásad nebo prostředek.

Pro podporu těchto řadu konfigurací, Service Fabric obsahuje značky, které můžete provést u uzlů. Tyto značky se nazývají *vlastnosti uzlu*. *Omezení umístění* jsou příkazy připojených služeb, které vyberete pro jednu nebo více vlastností uzlu. Omezení umístění definovat, ve kterém by měly běžet služby. Je možné rozšířit sadu omezení. Můžete pracovat všechny dvojice klíč/hodnota. 

<center>

![Různé úlohy pro rozložení clusteru][Image5]
</center>

### <a name="built-in-node-properties"></a>Vlastnosti integrované uzlem
Service Fabric definuje některé výchozí uzel vlastnosti, které lze použít automaticky, takže není nutné definovat. Výchozí vlastnosti definované v každém uzlu jsou **NodeType** a **NodeName**. 

Můžete například napsat omezení umístění jako `"(NodeType == NodeType03)"`. **NodeType** je běžně používaných vlastností. To je užitečné, protože odpovídá 1:1 s typem počítače. Každý typ počítače odpovídá typu úlohy v tradiční n vrstvou aplikaci.

<center>

![Omezení umístění a vlastnosti uzlu][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>Omezení umístění a syntaxe vlastnosti uzlu 
Hodnota zadaná ve vlastnosti uzlu může být řetězec, logická hodnota, nebo long. Příkaz na službu se nazývá umístění *omezení* vzhledem k tomu, že se omezí, ve kterém se služba může běžet v clusteru. Omezení může být jakékoli logická příkaz, který pracuje na vlastnosti uzlu v clusteru. Platný selektory v těchto logická příkazy jsou:

* Podmíněné kontroluje vytváření konkrétní příkazy:

  | – Příkaz | Syntaxe |
  | --- |:---:|
  | "rovno" | "==" |
  | "nerovná se" | "!=" |
  | "větší než" | ">" |
  | "větší než nebo rovno" | ">=" |
  | "menší než" | "<" |
  | "menší než nebo rovno" | "<=" |

* Logická příkazů pro seskupení a logické operace:

  | – Příkaz | Syntaxe |
  | --- |:---:|
  | "a" | "&&" |
  | "nebo" | "&#124;&#124;" |
  | "not" | "!" |
  | "skupiny jako jeden příkaz" | "()" |

Tady je několik příkladů příkazů základní omezení:

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Služby umístěné v něm může mít pouze uzly, kde příkaz celkové omezení umístění vyhodnotí na hodnotu "True". Uzly, které nemusíte mít definovánu vlastnost neodpovídají žádné omezení umístění, které obsahuje vlastnost.

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

Následující příklad ukazuje, vlastnosti uzlu definované prostřednictvím ClusterConfig.json pro samostatné nasazení nebo Template.json pro clustery hostovaných v Azure. 

> [!NOTE]
> V šabloně Azure Resource Manageru je obvykle s parametry typu uzlu. To může vypadat třeba `"[parameters('vmNodeType1Name')]"` místo NodeType01.
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

Můžete vytvořit služby umístění *omezení* služby následujícím způsobem:

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

Pokud jsou všechny uzly NodeType01 platné, můžete také vybrat uzel typu s omezením `"(NodeType == NodeType01)"`.

Omezení umístění služby je možné aktualizovat dynamicky za běhu. Pokud je potřeba, můžete přesouvat služby v clusteru, přidat a odebrat požadavky a podobně. Service Fabric zajišťuje, že služba zůstane nahoru a k dispozici i v případě, že jsou provedeny těchto typů změn.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Omezení umístění pro všechny pojmenované instanci služby nejsou zadány. Aktualizace vždy provést místo (její přepsání) byl dříve zadán.

Definice clusteru definuje vlastnosti na uzlu. Změna vlastnosti uzlu vyžaduje upgrade v konfiguraci clusteru. Upgrade vlastnosti uzlu vyžaduje každý ovlivněné uzel restartovat, aby mohla jeho nové vlastnosti sestav. Service Fabric spravuje tyto postupné upgrady.

## <a name="describing-and-managing-cluster-resources"></a>Popis a Správa prostředků clusteru
Jedna z vašich nejdůležitějších úloh jakékoli orchestrator je při správě spotřeba prostředků v clusteru. Správa prostředků clusteru může znamenat, že několik různých věcí. 

Nejdřív se existuje se ujistíme, že počítače nejsou přetížené. To znamená, ujistěte se, že počítače neběží další služby, než dokáže zpracovat. 

Druhou je vyrovnávání a optimalizace, které jsou kritické pro spouštění služby efektivně. Některé uzly je aktivní, zatímco jiné jsou studenou nemůže povolit nabídky služeb nákladově efektivní nebo citlivé na výkon. Aktivní uzly vést ke kolizi prostředků a nízký výkon. Studená uzly představují prostoje prostředků a vyšší náklady. 

Service Fabric představuje prostředky jako *metriky*. Metriky jsou fyzické nebo logické prostředků, které chcete popsat do Service Fabric. Příkladem metriky jsou "WorkQueueDepth" nebo "MemoryInMb." Informace o fyzické prostředky, které může řídit Service Fabric na uzlech najdete v tématu [zásady správného řízení prostředků](service-fabric-resource-governance.md). Informace o konfiguraci vlastních metrik a jejich použití naleznete v tématu [v tomto článku](service-fabric-cluster-resource-manager-metrics.md).

Metriky se liší od omezení umístění a vlastnosti uzlu. Vlastnosti uzlu jsou statické popisovače samotných uzlech. Metriky popisují prostředky, které mají uzly a že služby využívat při spuštění na uzlu. Vlastnost uzlu může být **HasSSD** a může být nastaveno na hodnotu true nebo false. Množství volného místa na tomto SSD a kolik je používané služby by metriky, jako je "DriveSpaceInMb." 

Stejně jako omezení umístění a vlastnosti uzlu Service Fabric Cluster Resource Manager nerozumí názvy průměr metriky. Názvy metrik jsou pouze řetězce. Je vhodné deklarovat jednotky jako součást názvy metrik, které vytvoříte, když mohou být nejednoznačný.

## <a name="capacity"></a>Kapacita
Pokud jste měli vypnuté všechny prostředky *vyrovnávání*, Service Fabric Cluster Resource Manager stále zajistí, že žádný uzel překročí jeho kapacitu. Správa přetečení kapacitu je možné, pokud je plný cluster nebo je zatížení větší než libovolný uzel. Kapacita je jiný *omezení* , abyste pochopili, jak velká část prostředku se uzel má používá Cluster Resource Manageru. Zbývající kapacita je sledována také pro cluster jako celek. 

Metriky se vyjadřují kapacitě a spotřebě na úrovni služby. Například Metrika může být "ClientConnections" a uzel může mít kapacitu pro "ClientConnections" z 32 768. Ostatní uzly můžete mít další omezení. Služby spuštěné v tomto uzlu můžete říct, že ji aktuálně spotřebovává 32,256 metriky "ClientConnections."

Cluster Resource Manageru za běhu, sleduje zbývající kapacity v clusteru a na uzlech. Cluster Resource Manager ke sledování kapacity, odečte využití jednotlivých služeb z kapacity uzlu, kde je služba spuštěna. Pomocí těchto informací Cluster Resource Manageru můžete zjistit, kam chcete umístit nebo přesuňte repliky tak, aby uzly nenavazují přes kapacity.

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

Zobrazí se kapacity definovaný v manifestu clusteru. Tady je příklad pro ClusterManifest.xml:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Tady je příklad kapacit, které jsou definovány prostřednictvím ClusterConfig.json pro samostatné nasazení nebo Template.json pro clustery hostovaných v Azure: 

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

Služby dynamicky načíst často změny. Řekněme, že repliky zatížení "ClientConnections" změněn z 1 024 jednotek na 2 048. Uzel, který byl spuštěn na pak měl kapacitou pouze 512 zbývající pro tuto metriku. Nyní repliky nebo umístění instance je neplatný, protože na tomto uzlu není dostatek volného místa. Cluster Resource Manageru musí uzel zpátky pod kapacity. To snižuje zatížení na uzlu, který je překročena kapacita přesunutím jeden nebo více replik nebo instancí z daného uzlu do dalších uzlů. 

Cluster Resource Manageru se snaží minimalizovat náklady na přesunutí repliky. Další informace o [náklady na přesunutí](service-fabric-cluster-resource-manager-movement-cost.md) a asi [opětovné vyvážení strategie a pravidla](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Kapacita clusteru
Jak Service Fabric Cluster Resource Manager zachovat celkový clusteru z příliš plné? Pomocí dynamického zatížení není hodně, co můžete dělat. Služby může mít jejich zatížení ve špičce nezávisle na akce, které má Cluster Resource Manageru. V důsledku toho může být váš cluster s dostatek prostoru ještě dnes underpowered, pokud nedojde k prudkému zítra. 

Ovládací prvky v Cluster Resource Manageru pomohou zabránit problémům. První věc, co můžete dělat je zabránit vytvoření nové úlohy, které by mohly způsobit cluster tak, aby plná.

Řekněme, že vytvořte bezstavovou službu, a má nějaké zatížení s ním spojená. Služba dbá na metriku "DiskSpaceInMb". Služba bude využívat pěti jednotek "DiskSpaceInMb" pro každou instanci služby. Chcete vytvořit tři instance služby. To znamená, že potřebujete 15 jednotky "DiskSpaceInMb" nacházet v clusteru můžete dokonce vytvořit tyto instance služby.

Cluster Resource Manageru průběžně vypočítá kapacitě a spotřebě jednotlivé metriky tak může zjistit, zbývající kapacity v clusteru. Pokud není k dispozici dostatek místa, Cluster Resource Manager odmítne volání, pokud chcete vytvořit službu.

Vzhledem k tomu, že tento požadavek je pouze 15 jednotek bude k dispozici, můžete tento prostor přidělit mnoha různými způsoby. Například může existovat jednu jednotku zbývající kapacity na 15 různých uzlech, nebo tři zbývající jednotky kapacity na pět různých uzlech. Pokud Cluster Resource Manageru můžete uspořádat věcí, tak na tři uzly jsou k dispozici pět jednotky, umístí služby. Změna uspořádání clusteru je obvykle je to možné, pokud je téměř plná clusteru nebo z nějakého důvodu nelze konsolidovat stávající služby.

## <a name="buffered-capacity"></a>Kapacita ve vyrovnávací paměti
Kapacita ve vyrovnávací paměti je jiné funkce Cluster Resource Manageru. To umožňuje vyhrazení nějaká část celkové kapacity uzlu. Tato kapacita vyrovnávací paměti slouží pouze k umístění služby během upgradu a selhání uzlů. 

Kapacita ve vyrovnávací paměti je zadán globálně na metriku pro všechny uzly. Hodnota, která vyberete rezervované kapacity je funkce počtu chybových nebo upgradovacích doménách, které máte v clusteru. Více chybových nebo upgradovacích doménách znamená, že můžete vybrat nižší číslo pro kapacitu ve vyrovnávací paměti. Pokud máte více domén, můžete očekávat menší množství váš cluster bude během upgradu a selhání není k dispozici. Určení ve vyrovnávací paměti kapacity má smysl pouze v případě, že jste zadali také kapacity uzlů pro metriku.

Tady je příklad toho, jak určit kapacitu ve vyrovnávací paměti v ClusterManifest.xml:

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

Tady je příklad toho, jak zadat ve vyrovnávací paměti kapacitu prostřednictvím ClusterConfig.json pro samostatné nasazení nebo Template.json pro clustery hostovaných v Azure:

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

Vytvoření nové služby selže, pokud clusteru je mimo kapacitu ve vyrovnávací paměti pro metriku. Zabránění vytvoření nové služby, chcete-li zachovat vyrovnávací paměti zajišťuje upgrady a selhání nezpůsobí uzly si projít kapacity. Kapacita ve vyrovnávací paměti je volitelné, ale My ho doporučujeme do žádného clusteru, který definuje kapacity pro metriku.

Cluster Resource Manageru poskytuje tyto informace načíst. Pro každou metriku tyto informace zahrnují: 
- Nastavení kapacity ve vyrovnávací paměti.
- Celková kapacita.
- Aktuální využití.
- Určuje, zda je považován za jednotlivé metriky rovnoměrně, nebo ne.
- Statistika týkající se směrodatnou odchylku.
- Uzly, které mají nejvíce a nejnižší zatížení.  
  
Následující kód ukazuje příklad výstupu:

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

## <a name="next-steps"></a>Další postup
* Informace o architektuře a informačního toku v Cluster Resource Manageru najdete v tématu [přehled architektura Cluster Resource Manageru](service-fabric-cluster-resource-manager-architecture.md).
* Definování defragmentaci metrik je jeden způsob, jak konsolidovat zatížení na uzlech, místo aby rozložil ho navýšení kapacity. Další informace o konfiguraci defragmentace, najdete v článku [defragmentaci metrik a zatížení v Service Fabric](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Začít od začátku a [Úvod do Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md).
* Zjistěte, jak se spravuje Cluster Resource Manageru a vyrovnává zatížení v clusteru, najdete v článku [vyrovnávání vašeho clusteru Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png

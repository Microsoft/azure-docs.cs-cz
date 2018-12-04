---
title: Popis clusteru cluster Resource Manager | Dokumentace Microsoftu
description: Cluster Service Fabric popisující zadáním domén selhání, Upgrade domény, vlastnosti uzlu a kapacity uzlů pro Cluster Resource Manager.
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
ms.openlocfilehash: 64f02b1165d014a0eaa89dae64a7d9aa283cac32
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834583"
---
# <a name="describing-a-service-fabric-cluster"></a>Popis clusteru service fabric
Service Fabric Cluster Resource Manager poskytuje několik mechanismů pro popis clusteru. Cluster Resource Manageru za běhu, používá tyto informace k zajištění vysoké dostupnosti služby spuštěné v clusteru. Při vynucování tyto důležité pravidla, je taky automatický pokus o optimalizaci spotřeby prostředků v rámci clusteru.

## <a name="key-concepts"></a>Klíčové koncepty
Cluster Resource Manager podporuje několik funkcí, které popisují clusteru:

* Domény selhání
* Upgradovací domény
* Vlastnosti uzlu
* Uzel kapacity

## <a name="fault-domains"></a>Domény selhání
Doména selhání je všechny plochy koordinované selhání. Jeden počítač se doména selhání (vzhledem k tomu může selhat na své vlastní pro z různých důvodů se před výpadky napájení dodavatelského vůči selháním disku chybné firmwaru NIC). Počítače připojené ke stejnému přepínači sítě Ethernet jsou ve stejné doméně selhání, jako jsou počítače sdílení jednoho zdroje napájení nebo na jednom místě. Protože je přirozené pro hardwarových chyb překrytí, jsou ze své podstaty hierarchická domén selhání a jsou reprezentovány jako identifikátory URI v Service Fabric.

Je důležité, že domén selhání jsou správně nastavené protože Service Fabric pomocí těchto informací k bezpečné umístění služby. Service Fabric nechce k umístění služby tak, aby ke ztrátě doména selhání (způsobila selhání některé komponenty) způsobí, že služba přejdete. Prostředí Service Fabric v Azure používá doména selhání na základě informací poskytnutých prostředí správnou konfiguraci uzlů v clusteru vaším jménem. Pro samostatnou službu Service Fabric jsou definovány domén selhání v době, která je nastavená clusteru 

> [!WARNING]
> Je důležité, že doména selhání údaje do Service Fabric je přesné. Například Řekněme, že uzly clusteru Service Fabric běží uvnitř 10 virtuálních počítačů běžících na pět fyzických hostitelích. V takovém případě i když je 10 virtuálních počítačů, existují jenom 5 různých (nejvyšší úrovně) domén selhání. Sdílení na stejném fyzickém hostiteli způsobí, že virtuální počítače, které sdílejí stejnou kořenovou doménu selhání od virtuálních počítačů selhat koordinované Pokud se nezdaří jejich fyzickém hostiteli.  
>
> Service Fabric očekává, že doména selhání uzlu nebudou je moci měnit. Jiné mechanismy, jako třeba zajistit vysokou dostupnost virtuálních počítačů [vysokou dostupnost virtuálních počítačů](https://technet.microsoft.com/library/cc967323.aspx) může způsobit konflikty s využitím Service Fabric, které používají transparentní migrace virtuálních počítačů z jednoho hostitele do druhého. Tyto mechanismy nejsou opakovaně konfigurovat nebo oznámit spuštění kódu v tomto virtuálním počítači. V důsledku toho jsou **nepodporuje** jako prostředí pro Service Fabric s clustery. Service Fabric by měl být používané technologie pouze vysokou dostupnost. Mechanismy, jako je migrace za provozu virtuálního počítače, sítě SAN, nebo jiné nejsou nezbytné. Pokud se používá ve spojení s využitím Service Fabric, tyto mechanismy _snížit_ aplikace dostupnost a spolehlivost protože přinášejí další složitosti, přidejte centralizované zdroje selhání a využívat spolehlivost a strategie dostupnosti, které jsou v konfliktu s těmi v Service Fabric. 
>
>

Na následujícím obrázku jsme barva všech entit, které přispívají k domén selhání a seznam všech různých domén selhání, které vyplývají. V tomto příkladu máme datová centra ("řadiče domény"), stojany ("R") a v oknech ("B"). Případně pokud každé okno obsahuje více než jeden virtuální počítač, může dojít další vrstvu v hierarchii doména selhání.

<center>
![Uzly uspořádané prostřednictvím domén selhání][Image1]
</center>

Za běhu Service Fabric Cluster Resource Manager bere v úvahu domén selhání v clusteru a plány rozložení. Replik stavových nebo bezstavových instancí pro určitou službu se distribuují tak, aby byly v samostatných doménách selhání. Distribuce službu napříč doménami selhání zajistí, že dostupnost služby nedošlo k ohrožení bezpečnosti selhání doména selhání na libovolné úrovni hierarchie.

Service Fabric Cluster Resource Manageru není pro vás počet vrstev jsou v hierarchii doména selhání. Ale pokusí se ujistěte, že ztráta jakékoli jedné části hierarchie nebude mít vliv na služby spuštěné v ní. 

Je vhodné, pokud existují stejný počet uzlů na všech úrovních hloubka v hierarchii doména selhání. Je-li "stromu" domén selhání nevyváženou ve vašem clusteru, obtížnější pro Cluster Resource Manager zjistit nejlepší přidělení služeb. Rozložení imbalanced domén selhání znamenají, že ke ztrátě některých domén dopadu na dostupnost služeb víc než jiných domén. V důsledku toho se odpojí Cluster Resource Manageru se mezi dva cíle: chce použít na počítačích v této doméně "heavy" tak, že služby na nich a chce umístí služeb v jiných doménách, aby nezpůsobí ztrátu domény problémy. 

Jak imbalanced domén vypadat? Na obrázku níže ukážeme dvě rozložení jiného clusteru. V prvním příkladu se uzly rovnoměrně distribuovaných napříč doménami selhání. V druhém příkladu jednu doménu selhání má mnoho dalších uzlů, než v jiných doménách selhání. 

<center>
![Dvě různé clusteru rozložení][Image2]
</center>

V Azure volba, z nichž doména selhání obsahuje uzel spravuje za vás. Ale v závislosti na počtu uzlů, které zřizujete můžete stále skončit s doménami selhání s více uzly v nich než jiné. Řekněme například, máte 5 domén selhání v clusteru, ale zřízení sedm uzlů pro daný typ NodeType. V tomto případě první dvě domény selhání skončit s více uzly. Pokud budete pokračovat v nasazování další NodeTypes s pouze několika instancí, získá horší problém. Proto doporučujeme počet uzlů v každém uzlu je typ násobek počtu domén selhání.

## <a name="upgrade-domains"></a>Upgradovací domény
Upgradovací domény jsou další funkce, která pomáhá Service Fabric Cluster Resource Manager Principy rozložení clusteru. Upgradovací domény definovat sady uzlů, které budou upgradovány ve stejnou dobu. Upgradovací domény pomáhají Cluster Resource Manageru, pochopit a orchestrovat operace správy, jako je inovace.

Upgradovací domény jsou mnohem jako domén selhání, ale s několik klíčových rozdílů. Nejprve definujte oblasti selhání hardwaru koordinované domén selhání. Upgradovacích domén, na druhé straně se definice zásady. Dostanete se rozhodnout, kolik chcete, místo jeho diktování prostředím. Můžete mít libovolný počet upgradu domény jako uzly. Další rozdíl mezi doménami selhání a upgradu domény je, že nejsou hierarchické upgradu domény. Místo toho se více podobají jednoduchých značek. 

Následující diagram znázorňuje, že jsou tři domény upgradu rozdělené mezi tři domény selhání. Také ukazuje jeden možné umístění pro tří různých replik stavové služby, kde každý skončilo v různých chybových nebo upgradu domény. Toto umístění umožňuje ztráty doménu selhání při uprostřed upgrade služby a ještě jednu kopii kódu a data.  

<center>
![Umístění s chybových nebo Upgradovacích doménách][Image3]
</center>

Existují výhody a nevýhody mají velký počet domén upgradu. Další domény upgradu znamená, že každý krok upgradu je podrobnější a ovlivňuje menší počet uzlů nebo služby. V důsledku toho méně musí přesunout najednou, Představujeme menší změny do systému. To může zvýšit spolehlivost, protože menší služby má vliv jakýkoli problém zavedené během upgradu. Další domény upgradu také znamená, že vám míň dostupné vyrovnávací paměti na jiných uzlech pro zpracování dopad upgradu. Například pokud máte pět upgradu domén, uzly v každém zvládají zhruba 20 % z provozu. Pokud potřebujete vypnout tuto doménu Upgrade pro upgrade, obvykle potřeba přejít někde zátěž. Protože máte čtyři zbývající domén upgradu, každý musí mít místo pro přibližně 5 % celkového provozu. Další domény upgradu znamená, že potřebujete méně vyrovnávací paměti na uzlech v clusteru. Představte si třeba Pokud jste místo toho měli 10 upgradu domén. V takovém případě každý UD bude pouze zpracovávat přibližně 10 % z celkového provozu. Při upgradu prochází clusteru, třeba, aby měl místo pro o 1.1 % z celkového provozu pouze každou doménu. Další domény upgradu obecně umožňují provozovat uzly na vyšší využití, protože budete potřebovat méně záložní kapacitu. Totéž platí pro domén selhání.  

Nevýhodou s mnoha upgradu domény je, že upgrady obvykle trvá déle. Service Fabric počká krátké době po dokončení doméně upgradu a provádí kontroly před zahájením upgradu dalším objektem. Tato zpoždění povolit rozpoznání problémy spojené s uvedením upgradu před provedením upgradu. Kompromis představuje to je přijatelné, protože zabraňuje chybný změny výstrahy neovlivňovaly příliš velkou část služby najednou.

Moc malý počet domén upgradu má mnoho záporné vedlejší účinky – každé jednotlivé upgradu domény je mimo provoz a upgraduje velkou část celkové kapacity je k dispozici. Například pokud máte jenom tři domény upgradu přenášíte dolů přibližně 1/3 služby nebo kapacita clusteru současně. Tolik služby najednou s dolů není žádoucí, protože je třeba mít dostatečnou kapacitu ve zbývající části vašeho clusteru ke zpracování úlohy. Zachování vyrovnávací paměti znamená, že při normálním provozu tyto uzly jsou menší načten, než by se jinak. Tím se zvyšuje náklady na provozování vaší služby.

Neexistuje žádný skutečný omezení celkový počet selhání nebo upgradu domény v prostředí, nebo omezení na tom, jak se překrývají. Ale nutné dodat, existuje několik běžných vzorů:

- Domény selhání a upgradu domény namapovat 1:1
- Jedna doména upgradu na jeden uzel (fyzický nebo virtuální instance operačního systému)
- Modelu "prokládané" nebo "Přehled", kde domén selhání a upgradu domény formuláře matice s počítače, které obvykle běží dolů šikmé rozdělení

<center>
![Selhání a upgradovací doména rozložení][Image4]
</center>

Neexistuje že žádný nejlepší odpověď jaké rozložení byste měli zvolit, každá má některé výhody a nevýhody. Například 1FD:1UD modelu je snadné nastavení. 1 upgradovat doménu za uzel model je nejvhodnější jako osoby, které se používají k. Během upgradu se aktualizuje každý uzel nezávisle na sobě. To se podobá jak malý byly sady počítačů upgradovány ručně v minulosti. 

Matice FD/ud stejný, kde tabulku tvoří doménami selhání a aktualizačními doménami a uzly jsou umístěny spuštění po diagonální je nejběžnější model. Jedná se o model používá ve výchozím nastavení na clusterech Service Fabric v Azure. U clusterů s mnoha uzly všechno, co končí vypadá jako výše hustému matice modelu.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Omezení chyb a doména upgradu a výsledné chování
### <a name="default-approach"></a>*Výchozí přístup*
Ve výchozím nastavení Cluster Resource Manageru udržuje služby vyvažují mezi selhání a upgradu domény. To je modelovaná jako [omezení](service-fabric-cluster-resource-manager-management-integration.md). Stavy omezení chyb a doména upgradu: "pro daného oddílu by neměla existovat rozdíl větší než jedna v počet objektů služeb (Bezstavová služba instancí nebo replik pro stavové služby) mezi dvěma doménami na stejné úrovni hierarchie". Řekněme, že toto omezení poskytuje záruku "maximální rozdíl". Omezení chyb a upgradu domény brání určitých operací přesunutí nebo ujednání, které porušují pravidlo bylo uvedeno výše. 

Podívejme se na příklad. Řekněme, že máme cluster s uzly šesti, nakonfigurovaný s pěti doménami selhání a pěti doménami upgradovat.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2. | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

*Konfigurace 1*

Nyní Řekněme, že jsme pět vytvoření služby s TargetReplicaSetSize (nebo pro bezstavovou službu s InstanceCount). Repliky se objevil na N1 N5. Ve skutečnosti N6 se nikdy nepoužívá bez ohledu na to, kolik služby, jako jsou to, které vytvoříte. Ale proč? Podívejme se na rozdíl mezi aktuální rozložení a co by mohlo dojít, pokud je zvolená N6.

Tady je rozložení, které nám dává a celkového počtu replik na selhání a upgradu domény:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Rozložení 1*


Toto rozložení je balanced z hlediska uzlů na doména selhání a upgradu domény. To je také vyrovnávat z hlediska počtu replik na selhání a upgradu domény. Každá doména má stejný počet uzlů a stejný počet replik.

Teď se podívejme se na co by mohlo dojít, pokud jsme použili N6 místo N2. Jak by tyto repliky distribuovat pak?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

*Rozložení 2*


Toto rozložení v rozporu s naší definicí záruku "maximální rozdíl" omezení doména selhání. FD0 má dvě repliky, zatímco FD1 obsahuje nula, a tím rozdíl mezi FD0 a FD1 celkem 2, což je větší než maximální rozdíl jednoho. Protože je porušena omezení, Cluster Resource Manager neumožňuje toto uspořádání. Podobně pokud výběru N2 a N6 (namísto N1 a N2) dostali bychom:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Rozložení 3*


Toto rozložení je balanced z hlediska domén selhání. Ale teď ji porušuje omezení domény upgradu protože UD0 má nulovou repliky, zatímco UD1 dvě. Proto toto rozložení je neplatné a nebudou odebrány pomocí Cluster Resource Manager.

Tento přístup k distribuci replik stavových nebo bezstavových instancí poskytuje nejlepší možné odolnosti proti chybám. V situaci, při jedné domény ocitne mimo provoz, minimální počet replik nebo instancí se ztratí. 

Tento přístup na druhé straně může být příliš přísné a, aby mohli využívat všechny prostředky clusteru. Některé uzly nelze použít pro některé konfigurace clusteru. To může vést k Service Fabric není umístění služby, což vede k upozornění. V předchozím příkladu některé z uzlů clusteru nesmí být použita (N6 v daném příkladu). I v případě, že by přidání uzlů do tohoto clusteru (N7 – N10), replik a instancí pouze umístěné na N1 – N5 kvůli selhání a upgradu domény. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2. | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

*Konfigurace 2*


### <a name="alternative-approach"></a>*Alternativním přístupem*

Cluster Resource Manager podporuje jinou verzi omezení chyb a doména upgradu, které umožňuje umístění při zůstává zaručena minimální úroveň zabezpečení. Alternativní omezení chyb a doména upgradu můžete uvést následujícím způsobem: "Pro určitou službu oddíl, distribuce replik napříč doménami se ujistěte, že oddíl nezpůsobuje žádné ztrátě kvora". Řekněme, že toto omezení poskytuje záruku "bezpečné kvorum". 

> [!NOTE]
>Pro stavové služby definujeme *ztráty kvora* v situaci, když většinou replik oddílů jsou vypnuté ve stejnou dobu. Například pokud TargetReplicaSetSize je pět, představuje sadu všechny tři repliky kvora. Podobně pokud TargetReplicaSetSize 6, čtyři repliky jsou nezbytné pro kvorum. Více než dvě repliky můžou v obou případech být mimo provoz ve stejnou dobu, pokud chce oddílu dál normálně fungovat. Pro bezstavovou službu, je i *ztráty kvora* jako bezstavové služby dál normálně fungovat i v případě, že většina instancí, přestanou fungovat ve stejnou dobu. Proto se zaměříme na stavové služby ve zbývající části textu.
>

Přejděte zpět do předchozího příkladu. S verzí "kvora bezpečné" omezení by všechna tři dané rozložení platný. Je, že i v případě, že by existovat selhání FD0 v druhé rozložení nebo UD1 ve třetí rozložení, oddílu by stále kvora (většinou jeho repliky stále by šlo nahoru). V této verzi omezení N6 může téměř vždy je možné využít.

"Kvora bezpečné" přístup přináší více flexibility než přístup "maximální rozdíl" je snazší najít distribuce repliky, které jsou platné v téměř jakékoli Clusterová topologie. Však tento přístup nemůže zaručit nejlepší odolnost proti chybám charakteristiky vzhledem k tomu, že některé chyby jsou horší než jiné. V nejhorším případě případu by mohly být ztraceny kvůli chybě jednu doménu a jedna další replika většinou replik. Například místo nich vyžaduje ke ztrátě kvora s 5 replik nebo instancí 3 selhání, můžete nyní přijít o většinou s chybami jen dva. 

### <a name="adaptive-approach"></a>*Adaptivní přístup*
Vzhledem k tomu, že oba přístupy mít silné a slabé stránky, jsme naši nabídku doplnili adaptivní přístup, který kombinuje těchto dvou strategií.

> [!NOTE]
>Bude to výchozí chování, počínaje Service Fabric verze 6.2. 
>
Adaptivní přístup ve výchozím nastavení používá "maximální rozdíl" logika a přepne do logiky "kvora bezpečné" pouze v případě potřeby. Cluster Resource Manager se automaticky zjistí strategii, kterou je nutné zobrazením konfiguraci clusteru a služeb. Pro danou službu: *při rovnoměrně dělitelné podle počtu domén selhání a počet domén upgradu TargetReplicaSetSize **a** počet uzlů je menší než nebo rovno (počet domén selhání) * () počet domén upgradu), Cluster Resource Manager by měla využívat "na základě kvora" logiku pro danou službu.* Berte v úvahu, že Cluster Resource Manager bude tuto metodu použijte pro bezstavové a stavové služby, bez ohledu na ztráty kvora, které nejsou relevantní pro bezstavové služby.

Přejděte zpět do předchozího příkladu a předpokládá, že cluster má teď 8 uzlů (stále konfigurací clusteru s pěti doménami selhání a pěti doménami upgradovat a TargetReplicaSetSize službě hostované na tento cluster zůstane pět). 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2. | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

*Konfigurace 3*

Vzhledem k tomu, že všechny nezbytné podmínky jsou splněny, bude Cluster Resource Manager využít logiku "na základě kvora" v distribuci službu. To umožňuje použití N6 – N8. K jednomu distribučnímu možné služby v tomto případě by mohla vypadat:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

*Rozložení 4*

Pokud vaše služba TargetReplicaSetSize je omezená na čtyři (například), bude Cluster Resource Manageru Všimněte si, že tato změna a obnovit pomocí logiky "maximální rozdíl", protože už není dělitelné počet doménami selhání a aktualizačními doménami TargetReplicaSetSize. V důsledku toho některé repliky pohybů plb typu dojde k distribuci zbývající čtyři repliky v uzlech N1 N5 tak, aby se "maximální rozdíl" verzi logiku domény doména selhání a upgradu došlo k porušení. 

Hledání zpět na čtvrté rozložení a TargetReplicaSetSize pět. N1 se odebere z clusteru, stane se rovná čtyřem počet domén upgradu. Cluster Resource Manageru znovu spustí pomocí logiky "maximální rozdíl" jako počet aktualizačními doménami není rovnoměrně rozdělit TargetReplicaSetSize služby už. V důsledku toho má replika R1, když znovu sestaven objevil na N4 tak, aby selhání a upgradu domény omezení není došlo k porušení.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |neuvedeno |neuvedeno |neuvedeno |neuvedeno |neuvedeno |neuvedeno |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Rozložení 5*

## <a name="configuring-fault-and-upgrade-domains"></a>Konfigurace odolnosti a upgradu domény
Definice domén selhání a upgradu domény se provádí automaticky v nasazení Service Fabric hostované v Azure. Service Fabric vybere a použije informace o prostředí z Azure.

Pokud vytváříte vlastní cluster (nebo chcete spustit konkrétní topologie ve vývoji), je informace o doméně selhání a upgradu domény můžete zadat sami. V tomto příkladu definujeme místní vývojový cluster devíti uzlů, která zahrnuje tři "datová centra" (každý má tři stojany). Tento cluster má také tři domény upgradu rozdělená mezi tyto tři datových centrech. Níže je příklad konfigurace: 

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

prostřednictvím ClusterConfig.json pro samostatné nasazení

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
> Při definování clustery prostřednictvím Azure Resource Manageru, jsou přiřazeny doménami selhání a upgradu domény Azure. Typy uzlů a VM Scale Sets definice v šabloně Azure Resource Manageru proto nezahrnuje doménu selhání nebo informace o upgradu domény.
>

## <a name="node-properties-and-placement-constraints"></a>Vlastnosti uzlu a omezení umístění
V některých případech (ve skutečnosti ve většině případů), budete mít jistotu, že určité úlohy spouštěny pouze v určitých typů uzlů v clusteru. Pro některé úlohy může například vyžadovat grafickými procesory nebo disky SSD a jiné ne. Skvělé příkladem cílení na hardware pro konkrétní úlohy je spousta téměř každé n vrstvou architekturu. Některé počítače jako části front end a na straně aplikace slouží rozhraní API a jsou přístupné na klientech nebo na internet. Různých počítačích, často a obsahují různé hardwarové prostředky, zpracování pracovní vrstvy výpočetního výkonu a úložiště. Obvykle se jedná o _není_ přímo zveřejněné klientům nebo Internetu. Service Fabric očekává, že existují případy, kdy je potřeba spustit na konkrétní hardwarové konfigurace konkrétní úlohy. Příklad:

* existující n vrstvou aplikaci byla "zrušeno a posunuta" do prostředí Service Fabric
* Úloha chce běžet na konkrétním hardwaru pro výkon, škálování nebo z důvodu izolace zabezpečení
* Úloha by měla být izolované od jiných úloh důvodů využití zásad nebo prostředků

Service Fabric pro podporu těchto řadu konfigurací, má první třídy hodnoty značek, které se můžou uplatnit na uzly. Tyto značky se nazývají **vlastnosti uzlu**. **Omezení umístění** jsou příkazy připojených služeb, které vyberte jeden nebo více vlastností uzlu. Omezení umístění definovat, ve kterém by měly běžet služby. Je možné rozšířit sadu omezení – všechny dvojice klíč/hodnota může pracovat. 

<center>
![Cluster různými úlohami rozložení][Image5]
</center>

### <a name="built-in-node-properties"></a>Integrované vlastnosti uzlu
Service Fabric definuje některé výchozí uzel vlastnosti, které je možné automaticky bez nutnosti jejich definování uživatele. Výchozí vlastnosti definované v každém uzlu jsou **NodeType** a **NodeName**. Můžete tak třeba napsat omezení umístění jako `"(NodeType == NodeType03)"`. Obecně jsme našli NodeType být jeden z nejpoužívanějších běžně používané vlastnosti. To je užitečné, protože odpovídá 1:1 s typem počítače. Každý typ počítače odpovídá typu úlohy v tradiční n vrstvou aplikaci.

<center>
![Omezení umístění a vlastnosti uzlu][Image6]
</center>

## <a name="placement-constraint-and-node-property-syntax"></a>Omezení umístění a syntaxe vlastnosti uzlu 
Hodnota zadaná ve vlastnosti uzlu může být string, bool, nebo podepsaný dlouho. Příkaz na službu se nazývá umístění *omezení* vzhledem k tomu, že se omezí, ve kterém se služba může běžet v clusteru. Omezení může být jakékoli logická příkaz, který pracuje na vlastnosti jiného uzlu v clusteru. Platný selektory v těchto logická příkazy jsou:

1) Podmíněné kontroly pro vytváření konkrétní příkazy

| Výraz | Syntaxe |
| --- |:---:|
| "rovno" | "==" |
| "nerovná se" | "!=" |
| "větší než" | ">" |
| "větší než nebo rovno" | ">=" |
| "menší než" | "<" |
| "menší než nebo rovno" | "<=" |

2) Logická příkazů pro seskupení a logické operace

| Výraz | Syntaxe |
| --- |:---:|
| "a" | "&&" |
| "nebo" | "&#124;&#124;" |
| "not" | "!" |
| "skupiny jako jeden příkaz" | "()" |

Tady je několik příkladů příkazů základní omezení.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Služby umístěné v něm může mít pouze uzly, kde příkaz celkové omezení umístění vyhodnotí na hodnotu "True". Uzly, které nemají definované vlastnosti neodpovídají žádné omezení umístění obsahující tuto vlastnost.

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

prostřednictvím ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery. 

> [!NOTE]
> V šabloně Azure Resource Manageru je obvykle s parametry typu uzlu. Může vypadat třeba "[parameters('vmNodeType1Name')]" místo "NodeType01".
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

Můžete vytvořit služby umístění *omezení* službu, třeba takto:

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

Pokud jsou všechny uzly NodeType01 platné, můžete také vybrat uzel typu s omezením "(NodeType == NodeType01)".

Jedním z bezva věci o omezení umístění služby je, aby bylo možné aktualizovat dynamicky za běhu. Proto pokud je potřeba, můžete přesouvat služby v clusteru, přidat a odebrat požadavky atd. Service Fabric stará o to, že služba zůstane nahoru a k dispozici i v případě, že jsou provedeny těchto typů změn.

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

Omezení umístění jsou zadány pro každé jiné pojmenované instanci služby. Aktualizace vždy provést místo (její přepsání) byl dříve zadán.

Definice clusteru definuje vlastnosti na uzlu. Změna vlastnosti uzlu vyžaduje konfigurace upgradu clusteru. Upgrade vlastnosti uzlu vyžaduje každý ovlivněné uzel restartovat, aby mohla jeho nové vlastnosti sestav. Toto postupné upgrady spravuje nástroj Service Fabric.

## <a name="describing-and-managing-cluster-resources"></a>Popis a Správa prostředků clusteru
Jedna z vašich nejdůležitějších úloh jakékoli orchestrator je při správě spotřeba prostředků v clusteru. Správa prostředků clusteru může znamenat, že několik různých věcí. Nejdřív se existuje se ujistíme, že počítače nejsou přetížené. To znamená, ujistěte se, že počítače neběží další služby, než dokáže zpracovat. Druhou je vyrovnávání a optimalizace, což je důležité pro efektivní spuštění služby. Nákladově efektivní nebo výkonu nabídky služeb citlivé nemůže povolit některé uzly je aktivní, zatímco jiné jsou studenou. Aktivní uzly vést ke kolizi prostředků a slabým výkonem a úplné uzly představují prostoje prostředků a vyšší náklady. 

Service Fabric představuje prostředky jako `Metrics`. Metriky jsou fyzické nebo logické prostředků, které chcete popsat do Service Fabric. Příkladem metriky jsou věci, jako je "WorkQueueDepth" nebo "MemoryInMb". Informace o fyzické prostředky, které může řídit Service Fabric na uzlech najdete v tématu [zásady správného řízení prostředků](service-fabric-resource-governance.md). Informace o konfiguraci vlastních metrik a jejich použití naleznete v tématu [v tomto článku](service-fabric-cluster-resource-manager-metrics.md)

Metriky se liší od omezení umístění a vlastnosti uzlu. Vlastnosti uzlu jsou statické popisovače samotných uzlech. Metriky popisují prostředky, které mají uzly a že služby využívat při jejich spuštění na uzlu. Vlastnost uzlu může být "HasSSD" a může být nastavena na hodnotu true nebo false. Množství volného místa na tomto SSD a kolik je používané služby by metriky, jako je "DriveSpaceInMb". 

Je důležité si uvědomit, že stejně jako omezení umístění a vlastnosti uzlu Service Fabric Cluster Resource Manager nerozumí významu názvy metrik. Názvy metrik jsou pouze řetězce. Je vhodné deklarovat jednotky jako součást názvy metrik, které vytvoříte, když může být nejednoznačný.

## <a name="capacity"></a>Kapacita
Pokud jste měli vypnuté všechny prostředky *vyrovnávání*, Service Fabric Cluster Resource Manager stále zajistí, že žádný uzel skončila nad jeho kapacitu. Správa přetečení kapacitu je možné, pokud je plný cluster nebo je zatížení větší než libovolný uzel. Kapacita je jiný *omezení* , abyste pochopili, jak velká část prostředku se uzel má používá Cluster Resource Manager. Zbývající kapacita je sledována také pro cluster jako celek. Metriky se vyjadřují kapacitě a spotřebě na úrovni služby. Takže například metriku může být "ClientConnections" a daný uzel může mít kapacitu pro "ClientConnections" z 32768. Další omezení, některé služby a systémem, který uzel může Dejme tomu, že ji aktuálně spotřebovává 32256 metriky "ClientConnections" může mít jiné uzly.

Cluster Resource Manageru za běhu, sleduje zbývající kapacity v clusteru a na uzlech. Aby bylo možné sledovat kapacitu odečte Cluster Resource Manager využití jednotlivých služeb z kapacity uzlu, kde je služba spuštěna. Pomocí těchto informací Service Fabric Cluster Resource Manager můžete zjistit, kam chcete umístit nebo přesuňte repliky tak, aby uzly nenavazují přes kapacity.

<center>
![Uzly clusteru a kapacita][Image7]
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

Můžete zobrazit kapacity definovaný v manifestu clusteru:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

prostřednictvím ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery. 

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

Běžně služby změny dynamicky načíst. Řekněme, že repliky zatížení "ClientConnections" změnil z 1024 na hodnotu 2048, ale na uzel, který byl spuštěn na pak pouze měl 512 zbývající pro tuto metriku kapacity. Nyní repliky nebo umístění instance je neplatný, protože na tomto uzlu není dostatek volného místa. Cluster Resource Manager má rozjíždí a získat uzel zpátky pod kapacity. To snižuje zatížení na uzlu, který je překročena kapacita přesunutím jeden nebo více replik nebo instancí z daného uzlu do dalších uzlů. Při přesunu repliky, Cluster Resource Manager snaží minimalizovat náklady na tyto pohybů plb typu. Náklady na přesunutí je podrobněji popsána [v tomto článku](service-fabric-cluster-resource-manager-movement-cost.md) a informace o Cluster Resource Manager je nové vyvážení strategie a pravidla je popsaný [tady](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Kapacita clusteru
Jak Service Fabric Cluster Resource Manager zachovat celkový clusteru z příliš plné? Dobře s dynamického zatížení není k dispozici mnoho lze provádět. Služby může mít jejich zatížení ve špičce nezávisle na akce prováděné pomocí Cluster Resource Manager. Cluster s dostatek prostoru ještě dnes v důsledku toho může být underpowered, až vám budou zítra známý. Ale nutné dodat, existují některé ovládací prvky, které jsou těšte, aby se zabránilo problémům. První, co můžeme udělat je zabránit vytvoření nové úlohy, které by mohly způsobit cluster tak, aby plná.

Řekněme, že vytvoření bezstavové služby a má nějaké zatížení s ním spojená. Řekněme, že služba dbá na metriku "DiskSpaceInMb". Také Předpokládejme, že se bude používat pět jednotky "DiskSpaceInMb" pro každou instanci služby. Chcete vytvořit tři instance služby. Výborně! To znamená, že potřebujeme 15 jednotky "DiskSpaceInMb" nacházet v clusteru nám i tak možné k vytvoření těchto instancí služby. Cluster Resource Manager průběžně vypočítá kapacitě a spotřebě jednotlivé metriky tak může zjistit, zbývající kapacity v clusteru. Pokud není k dispozici dostatek místa, Cluster Resource Manager odmítne volání služby vytvořit.

Požadavek je pouze to, že není možné 15 jednotky, může toto místo přidělené mnoha různými způsoby. Například může existovat jednu jednotku zbývající kapacity na 15 různých uzlech, nebo tři zbývající jednotky kapacity na pět různých uzlech. Pokud Cluster Resource Manageru můžete uspořádat věci, tak na tři uzly je k dispozici pět jednotky, umístí služby. Změna uspořádání clusteru je obvykle je to možné, pokud je téměř plná clusteru nebo z nějakého důvodu nelze konsolidovat stávající služby.

## <a name="buffered-capacity"></a>Kapacita ve vyrovnávací paměti
Kapacita ve vyrovnávací paměti je jiné funkce Cluster Resource Manager. To umožňuje vyhrazení nějaká část celkové kapacity uzlu. Tato kapacita vyrovnávací paměti slouží pouze k umístění služby během upgradu a selhání uzlů. Kapacita ve vyrovnávací paměti je zadán globálně na metriku pro všechny uzly. Hodnota, kterou vyberete pro rezervované kapacity je funkce počtu selhání a upgradu domény, budete mít v clusteru. Další selhání a upgradu domény znamená, že, můžete si vybrat nižší číslo pro kapacitu ve vyrovnávací paměti. Pokud máte více domén, můžete očekávat menší množství váš cluster bude během upgradu a selhání není k dispozici. Určení kapacity ukládány do vyrovnávací paměti pouze dává smysl, pokud také nastavíte kapacity uzlů pro metriku.

Tady je příklad toho, jak určit kapacitu ve vyrovnávací paměti:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

prostřednictvím ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery:

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

Vytvoření nové služby selže, pokud clusteru je mimo kapacitu ve vyrovnávací paměti pro metriku. Zabránění vytvoření nové služby, chcete-li zachovat vyrovnávací paměti zajišťuje upgrady a selhání nezpůsobí uzly si projít kapacity. Kapacita ve vyrovnávací paměti je nepovinný, ale doporučuje se do libovolného clusteru, který definuje kapacity pro metriku.

Cluster Resource Manager poskytuje tyto informace načíst. Pro každou metriku tyto informace zahrnují: 
  - Nastavení kapacity ve vyrovnávací paměti
  - Celková kapacita
  - Aktuální využití
  - Určuje, zda je považován za jednotlivé metriky s vyrovnáváním nebo ne
  - statistické údaje o směrodatné odchylky
  - uzly, které mají nejvíce a nejnižší zatížení  
  
Níže jsme vidět příklad výstupu:

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
* Informace o architektuře a informačního toku v Cluster Resource Manager, projděte si [v tomto článku ](service-fabric-cluster-resource-manager-architecture.md)
* Definování defragmentaci metrik je jeden způsob, jak konsolidovat zatížení na uzlech, místo aby rozložil ho navýšení kapacity. Další informace o konfiguraci defragmentace, najdete v tématu [v tomto článku](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Začít od začátku a [Úvod do Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
* Přečtěte si o tom, jak Cluster Resource Manager spravuje a vyrovnává zatížení v clusteru, přečtěte si článek na [Vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png

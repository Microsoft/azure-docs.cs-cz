---
title: Popište cluster pomocí Správce prostředků clusteru
description: Popište Cluster Service Fabric zadáním domén selhání, upgradování domén, vlastností uzlů a kapacit uzlů pro Správce prostředků clusteru.
author: masnider
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 5d27a09f0ff38ec7422636ef0933552aa310c387
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92911762"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Popište Cluster Service Fabric pomocí Správce prostředků clusteru.

Funkce Správce prostředků clusteru v Azure Service Fabric poskytuje několik mechanismů pro popis clusteru:

* Domény selhání
* Upgradovat domény
* Vlastnosti uzlu
* Kapacity uzlů

Cluster Správce prostředků za běhu používá tyto informace k zajištění vysoké dostupnosti služeb spuštěných v clusteru. Při vynucování těchto důležitých pravidel se taky snaží optimalizovat spotřebu prostředků v rámci clusteru.

## <a name="fault-domains"></a>Domény selhání

Doména selhání je jakákoli oblast koordinovaného selhání. Jeden počítač je doména selhání. Může dojít k neúspěšnému selhání z různých důvodů od selhání napájení při nastavování chyb na špatný firmware síťových adaptérů.

Počítače připojené ke stejnému přepínači sítě Ethernet jsou ve stejné doméně selhání. Takže se jedná o počítače, které sdílejí jeden zdroj napájení nebo v jednom umístění.

Vzhledem k tomu, že je u hardwarových chyb překrytí, jsou domény selhání ve své podstatě hierarchické. Jsou reprezentovány jako identifikátory URI v Service Fabric.

Je důležité, aby se správně nastavily domény selhání, protože Service Fabric tyto informace používá k bezpečnému umístění služeb. Service Fabric nechtějí umístit služby tak, aby ztráta domény selhání (způsobená selháním některých součástí) způsobila, že se služba může dostat mimo provoz.

V prostředí Azure Service Fabric používá informace o doméně selhání poskytované prostředím ke správné konfiguraci uzlů v clusteru vaším jménem. Pro samostatné instance Service Fabric jsou domény selhání definované v době, kdy je cluster nastavený.

> [!WARNING]
> Je důležité, aby informace o doméně selhání uvedené Service Fabric byly přesné. Řekněme například, že uzly clusteru Service Fabric běží v 10 virtuálních počítačích, které běží na 5 fyzických hostitelích. V takovém případě i v případě, že existuje 10 virtuálních počítačů, existují pouze 5 různých (nejvyšší úroveň) domén selhání. Sdílení stejného fyzického hostitele způsobí, že virtuální počítače budou sdílet stejnou kořenovou doménu selhání, protože při selhání fyzického hostitele dojde k koordinované chybě virtuálních počítačů.  
>
> Service Fabric očekává, že se doména selhání uzlu nemění. Další mechanismy zajištění vysoké dostupnosti virtuálních počítačů, jako jsou [ha-VM](/previous-versions/system-center/virtual-machine-manager-2008-r2/cc967323(v=technet.10)), můžou způsobit konflikty s Service Fabric. Tyto mechanismy využívají transparentní migraci virtuálních počítačů z jednoho hostitele do druhého. Neprovádí překonfigurování nebo upozorňování běžícího kódu uvnitř virtuálního počítače. V takovém případě *nejsou podporované* jako prostředí pro spouštění Service Fabricch clusterů. 
>
> Service Fabric by měla být jediná technologie s vysokou dostupností, která je zaměstnaná. Mechanismy jako migrace živých virtuálních počítačů a San nejsou nutné. Pokud se tyto mechanismy používají ve spojení s Service Fabric, _omezují_ dostupnost a spolehlivost aplikace. Důvodem je, že zavádějí další složitost, dodávají centralizované zdroje selhání a využívají strategie spolehlivosti a dostupnosti, které jsou v konfliktu s nástroji Service Fabric.
>
>

Na následujícím obrázku jsou všechny entity, které přispívají k doménám selhání, a seznam všech různých domén selhání, které jsou výsledkem. V tomto příkladu máme datová centra (DC), racky (R) a Blade (B). Pokud každé okno obsahuje více než jeden virtuální počítač, může být v hierarchii domén selhání jiná vrstva.

![Uzly organizované prostřednictvím domén selhání][Image1]

Během doby běhu Service Fabric cluster Správce prostředků považuje domény selhání v clusteru a plánuje rozložení. Stavové repliky nebo bezstavové instance pro službu jsou distribuované, takže jsou v samostatných doménách selhání. Distribuce služby napříč doménami selhání zajišťuje, že dostupnost služby nebude ohrožena v případě, že doména selhání selže na jakékoli úrovni hierarchie.

Správce prostředků clusteru nezáleží na tom, kolik vrstev se nachází v hierarchii domén selhání. Pokusí se zajistit, aby ztráta některé části hierarchie neovlivnila spuštěné služby.

To je nejlepší, pokud je stejný počet uzlů v každé úrovni hloubky v hierarchii domén selhání. Pokud je strom domén selhání ve vašem clusteru nevyvážený, je Správce prostředků clusteru obtížnější zjistit, co nejlépe přidělující služby. Rozložení nevyvážených domén selhání znamenají, že ztráta některých domén má vliv na dostupnost služeb více než jiných domén. V důsledku toho se Správce prostředků clusteru roztrhané mezi dva cíle:

* Chce používat počítače v dané "těžké" doméně umístěním služeb. 
* Chce umístit služby do jiných domén tak, aby ztráta domény nezpůsobila problémy.

Co vypadají domény jako nevyrovnané? Následující diagram znázorňuje dvě různá rozložení clusterů. V prvním příkladu jsou uzly rovnoměrně rozloženy napříč doménami selhání. V druhém příkladu má jedna doména selhání mnoho dalších uzlů než jiné domény selhání.

![Dvě různá rozložení clusterů][Image2]

V Azure můžete zvolit, která doména selhání obsahuje uzel, se spravuje za vás. V závislosti na počtu uzlů, které jste zřídili, můžete i nadále končit doménami selhání, které mají více uzlů v nich než jiné.

Řekněme například, že máte pět domén selhání v clusteru, ale zřizujete sedm uzlů pro typ uzlu (**NodeType**). V takovém případě se první dvě domény selhání ukončí s více uzly. Pokud budete pokračovat v nasazování více instancí **NodeType** s pouze několika instancemi, bude problém horší. Z tohoto důvodu doporučujeme, aby počet uzlů v každém typu uzlu byl násobek počtu domén selhání.

## <a name="upgrade-domains"></a>Upgradovat domény

Domény upgradu jsou další funkce, která pomáhá Service Fabric clusteru Správce prostředků pochopení rozložení clusteru. Upgradovací domény definují sady uzlů, které jsou upgradovány současně. Upgrade domén Správce prostředků pochopení a orchestrace operací správy, jako jsou upgrady, v clusteru.

Domény upgradu představují velký počet domén, jako jsou například domény selhání, ale s několika klíčovými rozdíly. Nejdříve oblasti koordinovaných hardwarových selhání definují domény selhání. Upgradované domény jsou naopak definovány zásadami. Budete se muset rozhodnout, kolik potřebujete, a nechcete, aby prostředí nadiktujo toto číslo. Můžete mít tolik domén upgradu jako uzly. Dalším rozdílem mezi doménami selhání a upgradovacími doménami je, že domény upgradu nejsou hierarchické. Místo toho se podobají jednoduché značce.

Následující diagram znázorňuje tři domény upgradu rozložené ve třech doménách selhání. Také ukazuje jedno možné umístění pro tři různé repliky stavové služby, kde každá končí v různých doménách selhání a upgradu. Toto umístění umožňuje ztrátu domény selhání během upgradu služby a pořád má jednu kopii kódu a dat.  

![Umístění s doménami selhání a upgradu][Image3]

Existují specialisty a nevýhody pro velký počet domén upgradu. Více domén upgradu znamená, že každý krok upgradu je podrobněji podrobnější a má vliv na menší počet uzlů nebo služeb. Méně služeb se musí pohybovat v čase, což vede k menšímu množství změn v systému. To má za následek zlepšení spolehlivosti, protože k menšímu množství služeb dochází při potížích zavedených během upgradu. Další domény upgradu také znamenají, že potřebujete méně dostupnou vyrovnávací paměť na jiných uzlech, abyste mohli zpracovat dopad upgradu.

Pokud máte například pět domén upgradu, budou mít uzly v každé z nich zpracování přibližně 20 procent provozu. Pokud potřebujete vzít v úvahu upgrade upgradovací domény, musí se tato zátěže obvykle nacházet někde. Vzhledem k tomu, že máte čtyři zbývající domény pro upgrade, musí mít každý z nich místo 25 procent celkového provozu. Víc domén upgradu znamená, že pro uzly v clusteru potřebujete méně vyrovnávací paměti.

Vezměte v úvahu, jestli jste místo toho měli 10 upgradovacích domén. V takovém případě by každá upgradovací doména mohla zpracovávat pouze přibližně 10 procent celkového provozu. Když provedete kroky upgradu v rámci clusteru, musí mít každá doména prostor jenom pro přibližně 11 procent celkového provozu. Další domény upgradu obvykle umožňují spouštět uzly s vyšším využitím, protože potřebujete méně rezervovanou kapacitu. Totéž platí pro domény selhání.  

Nevýhodou s mnoha doménami upgradu je, že inovace mají za následek delší dobu. Service Fabric čeká krátkou dobu po dokončení upgradovací domény a provede kontrolu před zahájením upgradu dalšího. Tato zpoždění umožňují zjistit problémy zavedené upgradem před pokračováním upgradu. Kompromisy jsou přijatelné, protože zabraňují chybovým změnám v tom, aby ovlivnily příliš velkou část služby najednou.

Existence příliš málo domén upgradu má mnoho negativních vedlejších účinků. Zatímco každá upgradovací doména je mimo provoz a je upgradována, není k dispozici velká část celkové kapacity. Pokud máte například jenom tři domény upgradu, provedete si přibližně jednu třetinu celkové kapacity služby nebo clusteru. Nestačí, když máte dost služby v provozu, protože ve zbývající části clusteru potřebujete dostatek kapacity pro zpracování úloh. Zachování této vyrovnávací paměti znamená, že při normálním provozu jsou tyto uzly méně načtené, než by byly jinak. Tím se zvýší náklady na provoz vaší služby.

Neexistují žádné reálné omezení celkového počtu domén selhání nebo upgradu v prostředí nebo omezení jejich překrytí. Existují však běžné vzory:

* Domény selhání a upgradovací domény mapované 1:1
* Jedna upgradovací doména na uzel (instance fyzického nebo virtuálního operačního systému)
* Model "prokládaného" nebo "matice", kde domény selhání a upgradovací domény tvoří matici s počítači, obvykle běžící šikmé.

![Rozložení domén selhání a upgradu][Image4]

Neexistuje žádná nejlepší odpověď pro výběr rozložení. Každá z nich má specialisty a nevýhody. Například model 1FD: 1UD se snadno nastavuje. Model jedné upgradované domény na model Node je nejvíce podobný jako k čemu se lidé používají. Během upgradu se každý uzel aktualizuje nezávisle. To se podobá tomu, jak byly malé sady počítačů upgradovány ručně v minulosti.

Nejběžnějším modelem je matice FD/UD, kde se v doménách selhání a upgradovacích doménách nachází tabulka a uzly, které začínají kolem úhlopříčky. Toto je model používaný ve výchozím nastavení v Service Fabric clustery v Azure. U clusterů s mnoha uzly končí vše, co vypadá jako zhuštěný vzor matice.

> [!NOTE]
> Service Fabric clustery hostované v Azure nepodporují změnu výchozí strategie. Pouze samostatné clustery nabízejí toto přizpůsobení.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Omezení při selhání a upgradu domény a výsledné chování
### <a name="default-approach"></a>Výchozí přístup

Ve výchozím nastavení Clusterová Správce prostředků udržuje služby vyvážené mezi chybami a upgradovacími doménami. Toto je modelované jako [omezení](service-fabric-cluster-resource-manager-management-integration.md). Omezení pro stav domény selhání a upgrade: pro daný oddíl služby by nikdy neměl být rozdíl větší než jeden v počtu objektů služby (stavové instance služby nebo repliky stavové služby) mezi dvěma doménami na stejné úrovni hierarchie. "

Řekněme, že toto omezení poskytuje záruku "maximálního rozdílu". Omezení pro domény selhání a inovace zabraňuje určitým pohybům nebo uspořádáním, které porušují pravidlo.

Řekněme například, že máme cluster se šesti uzly konfigurovanými s pěti doménami selhání a pěti upgradovacími doménami.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Nyní řekněme, že vytvoříme službu s **TargetReplicaSetSize** (nebo pro bezstavovou službu, **InstanceCount**) s hodnotou 5. Repliky půdy na N1 – N5. Ve skutečnosti se N6 nikdy nepoužívá bez ohledu na to, kolik služeb jako to vytvoříte. Ale proč? Pojďme se podívat na rozdíl mezi aktuálním rozložením a co se stane, pokud je zvolená možnost N6.

Tady je rozložení, které jsme získali, a celkový počet replik na jednu chybu a upgradovací doménu:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Toto rozložení je vyvážené z pohledu uzlů na doménu selhání a upgradovací doménu. Vyrovnává se taky s ohledem na počet replik na selhání a upgradovací doménu. Každá doména má stejný počet uzlů a stejný počet replik.

Teď se podívejme na to, co se stane, když jsme použili N6 místo N2. Jak by se měly repliky distribuovat?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Toto rozložení porušuje naši definici záruky "maximálního rozdílu" pro omezení domény selhání. FD0 má dvě repliky, zatímco FD1 má nulovou hodnotu. Rozdíl mezi FD0 a FD1 je celkem 2, což je více než maximální rozdíl jedna. Vzhledem k tomu, že je porušení omezení, Správce prostředků clusteru toto uspořádání nepovoluje.

Podobně, pokud jsme vybrali N2 a N6 (místo N1 a N2), získáme:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Toto rozložení je vyvážené v souvislosti s doménami selhání. Ale teď je v rozporu s omezením domény upgradu, protože UD0 má nulové repliky a UD1 má dvě. Toto rozložení je také neplatné a nebude je vyzvednuto Správce prostředků clusteru.

Tento přístup k distribuci stavových replik nebo bezstavových instancí poskytuje nejlepší možnou odolnost proti chybám. Pokud dojde k výpadku jedné domény, dojde ke ztrátě minimálního počtu replik/instancí.

Na druhé straně může být tento přístup příliš striktní a neumožňuje, aby cluster využil všechny prostředky. V některých konfiguracích clusteru se některé uzly nedají použít. To může způsobit, že Service Fabric neumístí vaše služby, což bude mít za následek varovné zprávy. V předchozím příkladu nelze použít některé uzly clusteru (N6 v příkladu). I když jste přidali uzly do tohoto clusteru (N7-N10), repliky/instance budou umístěny pouze do N1 – N5 z důvodu omezení pro domény selhání a upgrady.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

### <a name="alternative-approach"></a>Alternativní přístup

Cluster Správce prostředků podporuje další verzi omezení pro domény selhání a inovace. Umožňuje umístění a zároveň zaručuje minimální úroveň bezpečnosti. Alternativní omezení lze určit následujícím způsobem: "pro daný oddíl služby by distribuce replik mezi doménami měla zajistit, aby nedošlo ke zhoršení kvora." Řekněme, že toto omezení poskytuje jistotu "bezpečné pro kvorum".

> [!NOTE]
> V případě stavové služby definujeme *ztrátu kvora* v situaci, kdy se většina replik oddílů nachází ve stejnou dobu. Například pokud je **TargetReplicaSetSize** pět, sada všech tří replik představuje kvorum. Podobně platí, že pokud je **TargetReplicaSetSize** šest, jsou pro kvorum nezbytné čtyři repliky. V obou případech může být nefunkční více než dvě repliky ve stejnou dobu, pokud oddíl chce pokračovat v běžném provozu.
>
> U bezstavových služeb neexistuje žádná taková věc jako *ztráta kvora*. Bezstavové služby fungují normálně i v případě, že většina instancí rozchází ve stejnou dobu. Proto se ve zbývající části tohoto článku zaměříme na stavové služby.
>

Pojďme se vrátit k předchozímu příkladu. V případě, že je verze tohoto omezení v bezpečí "kvorum" bezpečná, všechna tři rozložení budou platná. I v případě, že se FD0 nepovedlo ve druhém rozložení nebo UD1 selhalo ve třetím rozložení, oddíl by měl kvorum pořád. (Většina replik by se pořád nastavila.) V této verzi omezení může být N6 téměř vždy využíváno.

Přístup "kvorum Safe" poskytuje větší flexibilitu než přístup "maximální rozdíl". Důvodem je, že je snazší najít distribuce replik, které jsou platné ve skoro jakékoli topologii clusteru. Tento přístup ale nemůže zaručit nejlepší charakteristiky odolnosti proti chybám, protože některé chyby jsou horší než jiné.

V nejhorším případě může být většina replik ztracena při selhání jedné domény a jedné další repliky. Například namísto tří selhání nutných ke ztrátě kvora s pěti replikami nebo instancemi můžete nyní přijít o většinu jenom dvou selhání.

### <a name="adaptive-approach"></a>Adaptivní přístup

Vzhledem k tomu, že oba přístupy mají silné a slabé stránky, zavádíme adaptivní přístup, který kombinuje tyto dvě strategie.

> [!NOTE]
> Toto je výchozí chování počínaje verzí 6,2 Service Fabric.
>
> Adaptivní přístup ve výchozím nastavení používá logiku "maximální rozdíl" a v případě potřeby přepne na logiku "bezpečného kvora". Cluster Správce prostředků automaticky zjistí, jakou strategii je potřeba, a Prohlédněte si, jak jsou cluster a služby nakonfigurované.
>
> Cluster Správce prostředků by měl používat logiku "založená na kvoru" pro službu, která je v obou těchto podmínkách pravdivá:
>
> * **TargetReplicaSetSize** pro službu je rovnoměrně rozdělitelná počtem domén selhání a počtem domén upgradu.
> * Počet uzlů je menší nebo roven počtu domén selhání vynásobený počtem domén upgradu.
>
> Uvědomte si, že Clusterová Správce prostředků bude tento přístup používat pro bezstavové i stavové služby, i když ztráta kvora není relevantní pro bezstavové služby.

Pojďme přejít zpátky na předchozí příklad a předpokládat, že má cluster teď osm uzlů. Cluster je stále nakonfigurovaný s pěti doménami selhání a pěti upgradovacími doménami a hodnota **TargetReplicaSetSize** služby hostované v tomto clusteru zůstane pět.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Vzhledem k tomu, že všechny nezbytné podmínky jsou splněné, Clusterová Správce prostředků bude při distribuci služby používat logiku "založená na kvoru". To umožňuje použití N6-N8. Jedna možná distribuce služby v tomto případě může vypadat takto:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Pokud je hodnota **TargetReplicaSetSize** vaší služby zmenšená na čtyři (například), clusterová správce prostředků si všimněte, že se změní. Bude pokračovat pomocí logiky "maximálního rozdílu", protože **TargetReplicaSetSize** není rozdělit podle počtu domén selhání a již upgradovacích domén. V důsledku toho dojde k distribuci zbývajících čtyř replik na uzlech N1-N5 k některým přesunům replik. Tímto způsobem není porušená verze "maximální rozdíl" v doméně selhání a logika domény upgradu.

Pokud je v předchozím rozložení hodnota **TargetReplicaSetSize** 5 a N1 z clusteru, je počet domén upgradu stejný jako čtyři. Cluster Správce prostředků se znovu spouští pomocí logiky "maximálního rozdílu", protože počet domén upgradu už ještě nerozděluje hodnotu **TargetReplicaSetSize** služby. V důsledku toho se replika R1, když se znovu vytvoří, musí vystavit na N4, aby nedošlo k porušení omezení pro doménu selhání a upgrade.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/A |N/A |N/A |N/A |N/A |N/A |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Konfigurace domén selhání a upgradu

V nasazeních Service Fabric hostovaných v Azure se domény selhání a domény pro upgrade automaticky definují. Service Fabric vybere a použije informace o prostředí z Azure.

Pokud vytváříte vlastní cluster (nebo chcete spustit konkrétní topologii ve vývoji), můžete zadat doménu selhání a informace o upgradu domény sami. V tomto příkladu definujeme místní vývojový cluster s devíti uzly, který zahrnuje tři datová centra (každý se třemi racky). Tento cluster má také tři domény upgradu, které jsou rozloženy v těchto třech datových centrech. Tady je příklad konfigurace v ClusterManifest.xml:

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

Tento příklad používá ClusterConfig.jsv pro samostatná nasazení:

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
> Když definujete clustery prostřednictvím Azure Resource Manager, Azure přiřadí domény selhání a upgradovací domény. Definice typů uzlů a sady škálování virtuálních počítačů v šabloně Azure Resource Manager tak neobsahují informace o doméně selhání nebo upgradovací doméně.
>

## <a name="node-properties-and-placement-constraints"></a>Vlastnosti uzlu a omezení umístění

V některých případech (ve skutečnosti většinou) budete chtít zajistit, aby některé úlohy běžely jenom na určitých typech uzlů v clusteru. Některé úlohy můžou například vyžadovat GPU nebo SSD a jiné nemusí.

Skvělým příkladem cílení hardwaru na konkrétní úlohy je skoro každá architektura n-vrstvá. Některé počítače slouží jako front-end nebo rozhraní API pro poskytování aplikace a jsou vystavena klientům nebo Internetu. Různé počítače, často s různými hardwarovými prostředky, zpracovávají práci výpočetních nebo úložných vrstev. Obvykle _nejsou_ vystaveny přímo klientům nebo Internetu.

Service Fabric očekává, že v některých případech může být potřeba spustit konkrétní úlohy na určitých konfiguracích hardwaru. Například:

* Existující n-vrstvá aplikace byla "vyzdvižena a přesunuta" do Service Fabricho prostředí.
* Zatížení musí být spuštěno na konkrétním hardwaru pro účely výkonu, škálování nebo důvodů izolace zabezpečení.
* Úlohy by měly být izolované od jiných úloh pro účely zásad nebo spotřeby prostředků.

Pro podporu těchto řazení konfigurací Service Fabric obsahuje značky, které můžete použít na uzly. Tyto značky se nazývají *Vlastnosti uzlu*. *Omezení umístění* jsou příkazy připojené k jednotlivým službám, které vyberete pro jednu nebo více vlastností uzlu. Omezení umístění definují, kde by měly služby běžet. Sada omezení je rozšiřitelná. Může fungovat jakýkoli pár klíč/hodnota.

![Různé úlohy pro rozložení clusteru][Image5]

### <a name="built-in-node-properties"></a>Předdefinované vlastnosti uzlu

Service Fabric definuje některé výchozí vlastnosti uzlů, které se dají použít automaticky, takže je nemusíte definovat. Výchozí vlastnosti definované na jednotlivých uzlech jsou **NodeType** a **Node**.

Můžete například zapsat omezení umístění jako `"(NodeType == NodeType03)"` . **NodeType** je běžně používaná vlastnost. To je užitečné, protože odpovídá 1:1 s typem počítače. Každý typ počítače odpovídá typu úlohy v tradiční n-vrstvé aplikaci.

![Omezení umístění a vlastnosti uzlu][Image6]

## <a name="placement-constraints-and-node-property-syntax"></a>Omezení umístění a syntaxe vlastností uzlu

Hodnota zadaná ve vlastnosti node může být řetězec, logická hodnota nebo podepsaná Long. Příkaz ve službě se nazývá *omezení* umístění, protože omezuje, kde může služba běžet v clusteru. Omezení může být libovolný logický příkaz, který pracuje s vlastnostmi uzlu v clusteru. Platné selektory v těchto logických příkazech jsou:

* Podmíněné kontroly pro vytváření konkrétních příkazů:

  | Příkaz | Syntax |
  | --- |:---:|
  | rovná se | "==" |
  | "nerovná se" | "!=" |
  | "větší než" | ">" |
  | "je větší než nebo rovno" | ">=" |
  | "menší než" | "<" |
  | "je menší než nebo rovno" | "<=" |

* Logické příkazy pro seskupování a logické operace:

  | Příkaz | Syntax |
  | --- |:---:|
  | ani | "&&" |
  | ani | "&#124;&#124;" |
  | mění | "!" |
  | "seskupit jako jeden příkaz" | "()" |

Tady je několik příkladů základních příkazů omezení:

* `"Value >= 5"`
* `"NodeColor != green"`
* `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

V případě, že je vyhodnocena jako "true", může být služba umístěna pouze v uzlech, kde je uvedena celková hodnota omezení umístění. Uzly, které nemají definovanou vlastnost, neodpovídají žádnému omezení umístění, které obsahuje vlastnost.

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

Následující příklad ukazuje vlastnosti uzlu definované pomocí ClusterConfig.jsv pro samostatná nasazení nebo Template.jsv případě clusterů hostovaných v Azure.

> [!NOTE]
> V šabloně Azure Resource Manager je typ uzlu obvykle parametrizovaný. Vypadá to `"[parameters('vmNodeType1Name')]"` spíše než NodeType01.
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

*Omezení* umístění služby můžete pro službu vytvořit následujícím způsobem:

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

Pokud jsou všechny uzly NodeType01 platné, můžete také vybrat tento typ uzlu s omezením `"(NodeType == NodeType01)"` .

Omezení umístění služby je možné během běhu aktualizovat dynamicky. Pokud potřebujete, můžete přesunout službu v rámci clusteru, přidat a odebrat požadavky a tak dále. Service Fabric zajistí, že služba zůstane v provozu a dostupná i v případě, že jsou provedeny tyto typy změn.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Omezení umístění jsou určena pro každou pojmenovanou instanci služby. Aktualizace vždy přebírají místo (přepsat), co bylo dříve zadáno.

Definice clusteru definuje vlastnosti uzlu. Změna vlastností uzlu vyžaduje upgrade na konfiguraci clusteru. Upgrade vlastností uzlu vyžaduje, aby byl každý ovlivněný uzel restartován, aby nahlásil nové vlastnosti. Service Fabric tyto postupné inovace spravuje.

## <a name="describing-and-managing-cluster-resources"></a>Popis a Správa prostředků clusteru

Jednou z nejdůležitějších úloh nástroje Orchestrator je pomáhat při správě spotřeby prostředků v clusteru. Správa prostředků clusteru může znamenat několik různých věcí.

Nejprve je zajištěno, že nejsou počítače přetíženy. To znamená, že na počítačích nejsou spuštěné další služby, než můžou zpracovat.

Za druhé je vyrovnávání a optimalizace, které jsou důležité pro efektivní spouštění služeb. Cenově výhodné nebo nenáročné nabídky služeb neumožňují, aby některé uzly byly horké, zatímco jiné jsou studené. Aktivní uzly vedou k kolizí prostředků a k špatnému výkonu. Studené uzly reprezentují plýtvání prostředky a zvýšené náklady.

Service Fabric představuje prostředky jako *metriky*. Metriky jsou libovolný logický nebo fyzický prostředek, který chcete popsat Service Fabric. Příklady metrik jsou "WorkQueueDepth" nebo "MemoryInMb". Informace o fyzických prostředcích, které se Service Fabric můžou řídit na uzlech, najdete v tématu zásady [správného řízení prostředků](service-fabric-resource-governance.md). Informace o výchozích metrikách používaných Správce prostředků clusteru a o tom, jak nakonfigurovat vlastní metriky, najdete v [tomto článku](service-fabric-cluster-resource-manager-metrics.md).

Metriky se liší od omezení umístění a vlastností uzlů. Vlastnosti uzlu jsou statické popisovače uzlů samotné. Metriky popisují prostředky, které mají uzly a které služby spotřebovávají při jejich spuštění na uzlu. Vlastnost uzlu může být **HasSSD** a může být nastavena na hodnotu true nebo false. Množství místa, které je dostupné na disku SSD a kolik je spotřebované službami, by bylo metrika, jako je například "DriveSpaceInMb".

Stejně jako u omezení umístění a vlastností uzlů Service Fabric Správce prostředků clusteru nerozumí, jaké názvy metriky znamenají. Názvy metrik jsou pouze řetězce. Je dobrým zvykem deklarovat jednotky jako součást názvů metrik, které vytvoříte, když budou pravděpodobně dvojznačné.

## <a name="capacity"></a>Kapacita

Pokud jste vypnuli veškeré *Vyrovnávání* prostředků, Service Fabric správce prostředků clusteru by pořád zajistil, že žádný uzel nepřekračuje kapacitu. Je možné spravovat přetečení kapacity, pokud není cluster příliš úplný nebo je zatížení větší než u libovolného uzlu. Kapacita je jiné *omezení* , které clusterová správce prostředků používá k pochopení, kolik prostředků má uzel. Zbývající kapacita je také sledována pro cluster jako celek.

Kapacita i spotřeba na úrovni služby se vyjadřují v souvislosti s metrikami. Metrika může být například "ClientConnections" a uzel může mít kapacitu "ClientConnections" z 32 768. Ostatní uzly mohou mít další omezení. Služba spuštěná v tomto uzlu může říci, že aktuálně spotřebovává 32 256 metriky "ClientConnections".

Cluster Správce prostředků během doby běhu sledovat zbývající kapacitu v clusteru a na uzlech. Ke sledování kapacity cluster Správce prostředků odečte využití jednotlivých služeb od kapacity uzlu, kde je služba spuštěná. S těmito informacemi může cluster Správce prostředků zjistit, kde umístit nebo přesunout repliky, aby uzly nepřešly do kapacity.

![Kapacity a uzly clusteru][Image7]

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

Tady je příklad kapacit definovaných prostřednictvím ClusterConfig.jspro samostatné nasazení nebo Template.jsv případě clusterů hostovaných v Azure:

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

Načítání služby se často mění dynamicky. Řekněme, že zatížení repliky "ClientConnections" se změnilo z 1 024 na 2 048. Uzel, na kterém byl spuštěn, měl za tuto metriku kapacitu, která je pouze 512. Teď, když je umístění repliky nebo instance neplatné, protože v tomto uzlu není dost místa. Správce prostředků clusteru musí získat back-spodní kapacitu uzlu. Snižuje zatížení uzlu, který překračuje kapacitu, přesunutím jedné nebo více replik nebo instancí z daného uzlu do jiných uzlů.

Cluster Správce prostředků se snaží minimalizovat náklady na přesun replik. Můžete získat další informace o [nákladech na pohyb](service-fabric-cluster-resource-manager-movement-cost.md) a o [strategiích a pravidlech pro vyrovnávání](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Kapacita clusteru

Jak Cluster Service Fabric Správce prostředků udržet celkový cluster jako neúplný? S dynamickým zatížením není možné nic dělat. Služby mohou mít špičku zatížení nezávisle na akcích, které cluster Správce prostředků přijímá. V důsledku toho se může stát, že váš cluster s velmi úsporou je v současné době v případě špičky.

Ovládací prvky v clusteru Správce prostředků můžou zabránit problémům. První věc, kterou můžete udělat, je zabránit vytváření nových úloh, které by způsobily, že cluster bude plný.

Řekněme, že vytvoříte bezstavovou službu a k ní je přidruženo nějaké zatížení. Služba stojí o metrikě "DiskSpaceInMb". Služba zpracuje pět jednotek "DiskSpaceInMb" pro každou instanci služby. Chcete vytvořit tři instance služby. To znamená, že v clusteru budete potřebovat 15 jednotek "DiskSpaceInMb", abyste mohli dokonce vytvořit tyto instance služby.

Cluster Správce prostředků průběžně počítá kapacitu a spotřebu každé metriky tak, aby bylo možné určit zbývající kapacitu v clusteru. Pokud není dostatek místa, cluster Správce prostředků odmítne volání pro vytvoření služby.

Vzhledem k tomu, že požadavek je pouze k dispozici pro 15 jednotek, můžete toto místo přidělit mnoha různými způsoby. Například může existovat jedna zbývající kapacita jednotky na 15 různých uzlech nebo tři zbývající jednotky kapacity na pěti různých uzlech. Pokud cluster Správce prostředků může změnit uspořádání položek tak, aby bylo na třech uzlech dostupné pět jednotek, služba umístí. Změna uspořádání clusteru je obvykle možná, pokud není cluster téměř úplný, nebo z nějakého důvodu nelze konsolidovat stávající služby.

## <a name="node-buffer-and-overbooking-capacity"></a>Kapacita vyrovnávací paměti uzlu a přetečení

Pokud je zadána kapacita uzlu pro metriku, cluster Správce prostředků nikdy nebude umísťovat ani přesouvat repliky do uzlu, pokud by celková zátěž přešla nad určenou kapacitu uzlu. To může někdy zabránit umístění nových replik nebo nahrazení neúspěšných replik, pokud má cluster blízko celou kapacitu a replika s velkým zatížením musí být vložena, nahrazena nebo přesunuta.

Aby bylo možné zajistit větší flexibilitu, můžete zadat buď vyrovnávací paměť uzlu, nebo kapacitu pro přetečení. Když je v rámci metriky zadaná vyrovnávací paměť uzlu nebo kapacita přetečení, cluster Správce prostředků se pokusí umístit nebo přesunout repliky takovým způsobem, že vyrovnávací paměť nebo kapacita při přetečení zůstanou nevyužité, ale umožňuje použití vyrovnávací paměti nebo vymezené kapacity, pokud je to nutné pro akce, které zvyšují dostupnost služby, jako je například:

* Nové umístění repliky nebo nahrazení neúspěšných replik
* Umístění během upgradů
* Oprava porušení provizorního a pevného omezení
* Defragmentace

Kapacita vyrovnávací paměti uzlu představuje rezervovanou část kapacity níže zadané kapacity uzlu a kapacita při zastavování představuje část nadbytečné kapacity nad zadanou kapacitou uzlu. V obou případech se cluster Správce prostředků pokusí zachovat tuto kapacitu zdarma.

Například pokud má uzel zadanou kapacitu pro metriku *CpuUtilization* 100 a procento vyrovnávací paměti uzlů pro tuto metriku je nastavené na 20%, pak celkové a nebufferované kapacity budou 100 a 80, v uvedeném pořadí a správce prostředků clusteru nebudou v normálním případě v uzlu zavedeny více než 80 jednotky zatížení.

![Celková kapacita uzlu je rovna kapacitě uzlu (vyrovnávací paměť uzlu + neuložená do vyrovnávací paměti).](./media/service-fabric-cluster-resource-manager-cluster-description/node-capacity.png)

Vyrovnávací paměť uzlu by měla být použita, pokud chcete rezervovat část kapacity uzlu, která bude použita pouze pro akce, které zvyšují výše uvedenou dostupnost služby.

Na druhé straně platí, že pokud se používá procento přetečení uzlů a nastaví se na 20%, pak celkové a nebufferované kapacity budou 120 a 100.

![Celková kapacita se rovná kapacitě při přetečení a kapacitě uzlu (při přetečení + bez vyrovnávací paměti).](./media/service-fabric-cluster-resource-manager-cluster-description/node-capacity-with-overbooking.png)

Pokud chcete, aby Správce prostředků cluster mohl umístit repliky na uzel, i v případě, že celkové využití prostředků by překročilo kapacitu, měla by se použít kapacita pro překročení. Tato možnost slouží k zajištění vyšší dostupnosti služeb na úkor výkonu. Pokud se používá překládání, musí být logika uživatelských aplikací schopná pracovat s menšími fyzickými prostředky, než může vyžadovat.

Pokud jsou zadané vyrovnávací paměti uzlu nebo kapacity přetečení, Clusterová Správce prostředků nepřesunou ani neumísťuje repliky, pokud by celková zátěž na cílovém uzlu převzala celkovou kapacitu (kapacita uzlu v případě vyrovnávací paměti uzlu a kapacitu uzlu + přetížení v případě převzetí služeb při selhání).

Také je možné určit, že se má nekonečná kapacita při zakládání. V takovém případě se cluster Správce prostředků pokusí zachovat celkové zatížení uzlu pod zadanou kapacitou uzlu, ale může potenciálně umístit mnohem větší zátěž uzlu, což by mohlo vést k vážnému snížení výkonu.

Metrika nemůže mít současně určenou kapacitu pro vyrovnávací paměť uzlů i pro přetečení.

Tady je příklad, jak určit kapacitu pro vytvoření vyrovnávací paměti uzlu nebo pro přetečení v *ClusterManifest.xml*:

```xml
<Section Name="NodeBufferPercentage">
    <Parameter Name="SomeMetric" Value="0.15" />
</Section>
<Section Name="NodeOverbookingPercentage">
    <Parameter Name="SomeOtherMetric" Value="0.2" />
    <Parameter Name=”MetricWithInfiniteOverbooking” Value=”-1.0” />
</Section>
```

Tady je příklad, jak určit kapacitu pro vytvoření vyrovnávací paměti uzlu nebo při přetečení pomocí *ClusterConfig.jsv* pro samostatná nasazení nebo *Template.jsv* případě clusterů hostovaných v Azure:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      }
    ]
  },
  {
    "name": "NodeOverbookingPercentage",
    "parameters": [
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      },
      {
          "name": "MetricWithInfiniteOverbooking",
          "value": "-1.0"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Další kroky

* Informace o architektuře a toku informací v rámci clusteru Správce prostředků najdete v tématu [Přehled architektury clusterových správce prostředků](service-fabric-cluster-resource-manager-architecture.md).
* Definování metrik defragmentace je jedním ze způsobů, jak konsolidovat zatížení uzlů místo jejich rozprostření. Informace o tom, jak nakonfigurovat defragmentaci, najdete v tématu [Defragmentace metrik a načítání v Service Fabric](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Začněte od začátku a [Získejte Úvod do Service Fabric správce prostředků clusteru](service-fabric-cluster-resource-manager-introduction.md).
* Informace o tom, jak cluster Správce prostředků spravuje a vyrovnává zatížení v clusteru, najdete v tématu [Balancing the Service Fabric cluster](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png

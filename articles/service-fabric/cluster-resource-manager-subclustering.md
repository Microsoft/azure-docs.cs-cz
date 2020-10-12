---
title: Vyrovnávání metriky subclusterů
description: Vliv omezení umístění na vyvážení a způsob jejich zpracování
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 7f571a851e4da147240c524b742bcd652bc54181
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82183108"
---
# <a name="balancing-of-subclustered-metrics"></a>Vyrovnávání metriky subclusterů

## <a name="what-is-subclustering"></a>Co je subclustering

K subclusterům dochází, když služby s různým omezením umístění mají společnou metriku a jejich zatížení pro ně sestaví. Pokud se zatížení hlášené službami významně liší, celkové zatížení uzlů bude mít velkou směrodatnou odchylku a mělo by to vypadat jako v případě, že je cluster nevyvážený, a to i v případě, že má nejlepší možnou rovnováhu.

## <a name="how-subclustering-affects-load-balancing"></a>Vliv subclusterování na Vyrovnávání zatížení

Pokud se zatížení hlášené službami na různých uzlech liší významně, může to vypadat jako v případě vysoké nerovnováhy, kde žádná není. Pokud je nevyrovnaná nerovnováha způsobená subclustery větší než skutečná nerovnováha, může také Zaměňujte algoritmus vyrovnávání Správce prostředků a v clusteru tak vydávat optimální rovnováhu.

Řekněme například, že máme čtyři služby a všechny nahlásily zatížení metriky Metric1:

* Služba A – má omezení umístění "NodeType = = front-end", oznamuje zatížení 10
* Služba B – má omezení umístění "NodeType = = front-end", oznamuje zatížení 10
* Služba C – má omezení umístění "NodeType = = back-end", oznamuje zatížení 100
* Služba D – má omezení umístění "NodeType = = back-end", oznamuje zatížení 100
* A máme čtyři uzly. Dva z nich mají nastavení NodeType jako front-end a další dva jsou "back-end".

A máme následující umístění:

<center>

![Příklad umístění v subclusterech][Image1]
</center>

Cluster může vypadat nevyváženě, máme velké zatížení uzlů 3 a 4, ale toto umístění v této situaci vytvoří nejlepší možnou rovnováhu.

Správce prostředků může rozpoznat situace subclusterů a v téměř všech případech může vydávat optimální rovnováhu pro danou situaci.

U některých mimořádných situací, kdy Správce prostředků není možné optimálně vyrovnávat subclusterované metriky, se pořád detekuje subclustering a vygeneruje zprávu o stavu, která vám pomůže tento problém vyřešit.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Typy subclusterů a jejich zpracování

Situace v subclusterech je možné klasifikovat do tří různých kategorií. Kategorie konkrétního subclusterů určuje způsob, jakým se bude zpracovávat Správce prostředků.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>První kategorie – ploché subclustery s nesouvislými skupinami uzlů

Tato kategorie má nejjednodušší formu subclusterů, kde lze uzly rozdělit do různých skupin a každou službu lze umístit pouze do uzlů v jedné z těchto skupin. Každý uzel patří pouze do jedné skupiny a jedné skupiny. Výše popsaná situace patří do této kategorie, protože se jedná o většinu situací subclusterů. 

V situacích v této kategorii může Správce prostředků vydávat optimální rovnováhu a není potřeba provádět žádné další zásahy.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>Druhá kategorie – subclustering s hierarchickými skupinami uzlů

K této situaci dochází, když je skupina uzlů povolená pro jednu službu podmnožinou skupiny uzlů povolených pro jinou službu. Nejběžnějším příkladem této situace je, že některá služba má definované omezení umístění a jiná služba nemá žádné omezení umístění a dá se umístit na libovolný uzel.

Příklad:

* Služba A: žádné omezení umístění
* Služba B: omezení umístění "NodeType = = front-end"
* Služba C: omezení umístění "NodeType = = back-end"

Tato konfigurace vytvoří vztah nadmnožiny mezi skupinami uzlů pro různé služby.

<center>

![Podmnožiny nadmnožiny podclusterů][Image2]
</center>

V takové situaci je pravděpodobné, že se vyrovnává z optimálního zůstatku.

Správce prostředků rozpozná tuto situaci a vytvoří zprávu o stavu, která vám pomůže rozdělit službu A na dvě služby – Service a1, kterou lze umístit na uzly front-end a na službu a2, které lze umístit do back-endu uzlů. Tím se vrátíme k první situaci kategorie, kterou je možné optimálně vyvážit.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>Třetí kategorie – subclustering s částečným překrytím mezi sadami uzlů

K této situaci dochází, když dojde k částečnému překrytí mezi sadami uzlů, na které lze umístit některé služby.

Například pokud máme vlastnost Node nazvanou NodeColor a máme tři uzly:

* Uzel 1: NodeColor = Red
* Uzel 2: NodeColor = Blue
* Uzel 2: NodeColor = zelený

A máme dvě služby:

* Služba A: s omezením umístění "Color = = Red | | Color = = Blue
* Služba B: s omezením umístění "Color = = Blue | | | Color = = Zelená

Z tohoto důvodu může být služba A umístěna na uzlech 1 a 2 a služba B může být umístěna na uzlech 2 a 3.

V takové situaci je pravděpodobné, že se vyrovnává z optimálního zůstatku.

Správce prostředků rozpozná tuto situaci a vytvoří zprávu o stavu, která vám pomůže tyto služby rozdělit.

V této situaci Správce prostředků není schopen poskytnout návrh, jak rozdělit služby, protože je možné provést více rozdělení a neexistuje žádný způsob, jak odhadnout, který způsob by byl optimální pro rozdělení služeb.

## <a name="configuring-subclustering"></a>Konfigurace subclustering

Chování Správce prostředků o subclusterování se dá upravit úpravou následujících parametrů konfigurace:
* SubclusteringEnabled-parametr určuje, zda Správce prostředků při vyrovnávání zatížení převzít subclustery do účtu. Pokud je tento parametr vypnutý, Správce prostředků bude ignorovat subclustery a zkusit dosáhnout optimálního zůstatku na globální úrovni. Výchozí hodnota tohoto parametru je false.
* SubclusteringReportingPolicy – určuje, jak Správce prostředků budou generovat sestavy o stavu pro hierarchické a částečné překrytí subclustery. Hodnota nula znamená, že zprávy o stavu týkající se subclusterů jsou vypnuté, "1" znamená, že zprávy o stavu budou vytvořeny pro optimální situace subclusterů a hodnota "2" bude vytvářet sestavy o stavu "OK". Výchozí hodnota pro tento parametr je "1".

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

prostřednictvím ClusterConfig.jsv pro samostatná nasazení nebo Template.jsv případě hostovaných clusterů Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "SubclusteringEnabled",
          "value": "true"
      },
      {
          "name": "SubclusteringReportingPolicy",
          "value": "1"
      },
    ]
  }
]
```

## <a name="next-steps"></a>Další kroky
* Informace o tom, jak cluster Správce prostředků spravuje a vyrovnává zatížení v clusteru, najdete v článku o [Vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md) .
* Informace o tom, jak můžou být vaše služby omezené, aby se nastavily jenom na určitých uzlech, najdete v tématu [Vlastnosti uzlu a omezení umístění](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) .

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png

---
title: Vyvažování dílčích metrik
description: Vliv omezení umístění na vyvažování a způsob, jakým s ním nakládáte
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 23782a86d31251cb1a3474e0395df716a2e832df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430640"
---
# <a name="balancing-of-subclustered-metrics"></a>Vyvažování dílčích metrik

## <a name="what-is-subclustering"></a>Co je podclustering

Subclustering se stane, když služby s různými omezeními umístění mají společnou metriku a obě sestavy zatížení pro něj. Pokud zatížení hlášené službami výrazně liší, celkové zatížení uzlů bude mít velkou směrodatnou odchylku a bude vypadat, že cluster je nevyvážený, i když má nejlepší možnou rovnováhu.

## <a name="how-subclustering-affects-load-balancing"></a>Vliv podclusteringu na vyrovnávání zatížení

Pokud zatížení hlášené služby na různých uzlech výrazně liší, může to vypadat, že je velká nerovnováha, kde není žádný. Také pokud falešná nerovnováha způsobená podclustering je větší než skutečná nerovnováha, má potenciál zmást algoritmus vyrovnávání Správce prostředků a vytvořit neoptimální rovnováhu v clusteru.

Řekněme například, že máme čtyři služby a všechny hlásí zatížení pro metrickou metriku1:

* Služba A – má omezení umístění "NodeType==Type1", hlásí zatížení 10
* Služba B – má omezení umístění "NodeType==Type1", hlásí zatížení 10
* Služba C – má omezení umístění "NodeType==Type2", hlásí zatížení 100
* Služba D – má omezení umístění "NodeType==Type2", hlásí zatížení 100
* A máme čtyři uzly. Dva z nich mají NodeType nastavit jako "Type1" a další dva jsou "Type2"

A máme následující umístění:

<center>

![Příklad umístění podseskupených][Image1]
</center>

Cluster může vypadat nevyvážený, máme velké zatížení na uzly 3 a 4, ale toto umístění vytvoří nejlepší možnou rovnováhu v této situaci.

Správce prostředků dokáže rozpoznat situace podclusteringu a téměř ve všech případech může vytvořit optimální rovnováhu pro danou situaci.

V některých výjimečných situacích, kdy Správce prostředků není schopen optimálně vyvážit metriku podclusterované, bude stále detekovat podclustering a vygeneruje zprávu o stavu, která vám poradí s řešením problému.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Typy podskupin a způsob jejich zpracování

Situace podclusteringu lze rozdělit do tří různých kategorií. Kategorie konkrétní situace podclustering určuje, jak bude zpracována Správce prostředků.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>První kategorie – ploché podclusterování s nesouvislými skupinami uzlů

Tato kategorie má nejjednodušší formu podclusteringu, kde uzly mohou být rozděleny do různých skupin a každá služba může být umístěna pouze na uzly v jedné z těchto skupin. Každý uzel patří do jedné skupiny a pouze do jedné skupiny. Výše popsaná situace patří do této kategorie stejně jako většina situací podclustering. 

Pro situace v této kategorii správce prostředků může vytvořit optimální rovnováhu a není potřeba žádné další zásahy.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>Druhá kategorie – podclustering s hierarchickými skupinami uzlů

K této situaci dochází, když skupina uzlů povolených pro jednu službu je podmnožinou skupiny uzlů povolených pro jinou službu. Nejběžnějším příkladem této situace je, když má některá služba definované omezení umístění a jiná služba nemá žádné omezení umístění a může být umístěna na libovolném uzlu.

Příklad:

* Služba A: žádné omezení umístění
* Služba B: omezení umístění "NodeType==Type1"
* Služba C: omezení umístění "NodeType==Type2"

Tato konfigurace vytvoří vztah podmnožiny mezi skupinami uzlů pro různé služby.

<center>

![Podmnožina podmnožina][Image2]
</center>

V této situaci existuje možnost, že dojde k neoptimální rovnováze.

Resource Manager rozpozná tuto situaci a vytvoří zprávu o stavu, která vám poradí rozdělit službu A na dvě služby – službu A1, kterou lze umístit na uzly Type1 a službu A2, kterou lze umístit na uzly Type2. Tím se vrátíme do první kategorie situace, která může být vyvážena optimálně.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>Třetí kategorie – podskupování s částečným překrytím mezi sadami uzlů

K této situaci dochází, pokud dojde k částečnému překrytí mezi sadami uzlů, na které lze umístit některé služby.

Například pokud máme vlastnost uzlu s názvem NodeColor a máme tři uzly:

* Uzel 1: NodeColor=Červená
* Uzel 2: NodeColor=Modrá
* Uzel 2: NodeColor=Zelená

A máme dvě služby:

* Služba A: s omezením umístění "Color==Red || Barva==Modrá"
* Služba B: s omezením umístění "Color==Blue || Barva==Zelená"

Z tohoto důvodu lze službu A umístit na uzly 1 a 2 a službu B umístit na uzly 2 a 3.

V této situaci existuje možnost, že dojde k neoptimální rovnováze.

Správce prostředků tuto situaci rozpozná a vytvoří zprávu o stavu, která vám poradí rozdělit některé služby.

V takovém případě správce prostředků není schopen poskytnout návrh, jak rozdělit služby, protože více rozdělení lze provést a neexistuje žádný způsob, jak odhadnout, jakým způsobem by bylo optimální rozdělit služby.

## <a name="configuring-subclustering"></a>Konfigurace podclusteringu

Chování Správce prostředků ohledně podclusteringu lze upravit úpravou následujících parametrů konfigurace:
* SubclusteringEnabled - parametr určuje, zda Správce prostředků bude při vyrovnávání zatížení brát v úvahu podclustering. Pokud je tento parametr vypnutý, Správce prostředků bude ignorovat podclustering a pokusí se dosáhnout optimální rovnováhy na globální úrovni. Výchozí hodnota tohoto parametru je false.
* SubclusteringReportingPolicy - určuje, jak správce prostředků bude vyzařovat sestavy stavu pro hierarchické a částečné překrytí podclustering. Nulová hodnota znamená, že zprávy o stavu o podclustering jsou vypnuty, "1" znamená, že zprávy o stavu upozornění budou vytvořeny pro suboptimální subclustering situace a hodnota "2" bude produkovat "OK" sestavy stavu. Výchozí hodnota pro tento parametr je "1".

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

prostřednictvím souboru ClusterConfig.json pro samostatná nasazení nebo Template.json pro hostované clustery Azure:

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
* Informace o tom, jak Správce prostředků clusteru spravuje a vyrovnává zatížení clusteru, naleznete v článku o [vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md)
* Informace o tom, jak mohou být vaše služby omezeny tak, aby byly umístěny pouze na určitých uzlech, naleznete [v tématu Vlastnosti uzlů a omezení umístění](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png

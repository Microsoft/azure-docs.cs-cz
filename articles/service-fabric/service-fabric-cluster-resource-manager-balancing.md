---
title: Vyvážit cluster Azure Service Fabric | Microsoft Docs
description: Úvod do vyrovnávání cluster pomocí Service Fabric clusteru správce prostředků.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5d2f195c50750a5c7685f62c909f77b2960613e6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="balancing-your-service-fabric-cluster"></a>Vyrovnávání váš cluster service fabric
Správce prostředků clusteru Service Fabric podporuje dynamické zatížení změny reaktivní dodatky nebo odstraňování uzly nebo služeb. Také automaticky opraví narušení omezení a proaktivně znovu vytvoří rovnováhu clusteru. Ale jsou jak často tyto akce provést a co je aktivuje?

Existují tři různé kategorie práci, kterou provádí správce prostředků clusteru. Jsou to tyto:

1. Umístění – tato fáze se zabývá umístění žádné stavové repliky nebo bezstavové instancí, které chybí. Umístění zahrnuje nové služby a zpracování stavových repliky nebo bezstavové instancí, které selhaly. Tady jsou zpracovávány odstranění a vyřazení replik nebo instancí.
2. Omezení kontroluje – tato fáze kontroluje a opravuje porušení omezení jiné umístění (pravidla) v systému. Příklady pravidel jsou třeba zajistit, že uzly nejsou přes kapacity a že jsou splněny omezení umístění služby.
3. Vyrovnávání – tato fáze ověří, jestli nové vyrovnání je nezbytné podle nakonfigurované požadované úrovně vyrovnání pro jiné metriky. Pokud ano pokusí se najít uspořádání v clusteru, který je více vyrovnáváním.

## <a name="configuring-cluster-resource-manager-timers"></a>Konfigurace správce prostředků clusteru časovače
První sadu ovládacích prvků kolem vyrovnávání jsou sady časovače. Tyto časovače řídí, jak často správce prostředků clusteru prozkoumá clusteru a provede nápravné akce.

Každý z těchto různých typů opravy, které správce prostředků clusteru můžete provést řídí jiné časovače, která řídí její interval. Při každém časovače aktivuje, je naplánován. Ve výchozím nastavení správce prostředků:

* kontroluje stav a použije aktualizace (jako záznam, který je uzlem dolů) každých 1/10 sekund
* Nastaví příznak zkontrolujte umístění 
* Nastaví příznak kontrola omezení za sekundu
* Nastaví příznak vyrovnávání každých pět sekund.

Níže jsou příklady konfigurace řídících tyto časovače:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

pomocí souboru ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinConstraintCheckInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

Dnes clusteru provede správce prostředků pouze jednu z těchto akcí najednou, postupně. Z tohoto důvodu označujeme těchto časovačů jako "minimální intervaly" a akcí, které získat provedeny, když časovače přejděte jako "nastavení příznaky". Například správce prostředků clusteru má na starosti čekající požadavky na vytvoření služeb před vyrovnávání clusteru. Jak je vidět ve výchozí časové intervaly Zadaný správce prostředků clusteru hledá nic, co je potřeba udělat často. Obvykle znamená, že je sada změny provedené v průběhu každého kroku malé. Provedení malých změn často umožňuje správci prostředků clusteru jako odpovídající při problémech v clusteru. Výchozí časovače poskytují některé dávkování vzhledem k tomu, že mnoho stejné typy událostí, které jsou obvykle současných. 

Například, když nepovede uzly mohou provádět tak celý domén selhání najednou. Všechny tyto chyby jsou zaznamenána během další stav aktualizace po *PLBRefreshGap*. Opravy jsou určeny v průběhu následujících umístění, kontrola omezení a vyrovnávání spustí. Ve výchozím nastavení není správce prostředků clusteru kontrolu prostřednictvím čas změny v clusteru a zkusit vyřešit všechny změny najednou. Díky tomu by vedlo k shluky změn.

Správce prostředků clusteru musí také doplňující informace, které určí, zda imbalanced clusteru. Pro tento máme dva další požadované konfigurace: *BalancingThresholds* a *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Vyrovnávání prahové hodnoty
Prahovou hodnotu vyrovnávání je hlavní ovládací prvek pro spuštění vyrovnává. Prahová hodnota vyrovnávání pro metriku _poměr_. Pokud zatížení pro metriku na uzlu nejvíce načíst rozdělené podle množství zatížení na uzlu alespoň načíst překročí tuto metriku *BalancingThreshold*, pak je imbalanced clusteru. V důsledku vyrovnávání se aktivuje při příštím zkontroluje Správce prostředků clusteru. *MinLoadBalancingInterval* časovače definuje, jak často by měl správce prostředků clusteru zkontrolujte, zda vyrovnává je nezbytné. Kontrola neznamená, že se nic nestane. 

Vyrovnávání prahové hodnoty jsou definovány na základě-metrika jako součást definice clusteru. Další informace o metrikách, podívejte se na [v tomto článku](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

pomocí souboru ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery:

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

<center>
![Příklad vyrovnávání prahová hodnota][Image1]
</center>

V tomto příkladu je každá služba využívání jednu jednotku některé metriky. V horní příkladu maximální zatížení na uzlu je pět a minimum je dva. Řekněme, že vyrovnávání prahová hodnota pro tato metrika je tři. Vzhledem k tomu, že poměr v clusteru je 5/2 = 2.5 a který je menší než zadaný vyrovnávání tři prahovou hodnotu, jsou rovnoměrně clusteru. Žádné vyrovnávání se aktivuje, když kontroluje správce prostředků clusteru.

V příkladu dolní maximální zatížení na uzlu je 10, při minimálně dva týdny, výsledkem je poměr pět. Pět je větší než určené vyrovnávání prahová hodnota tří pro tuto metriku. V důsledku toho vyrovnává spustit bude naplánované příštím aktivuje vyrovnávání časovač. V situaci, jako je to je obvykle distribuován některé zatížení do Uzel3. Vzhledem k tomu, že správce prostředků clusteru Service Fabric nepoužívá typu greedy přístup, může na Uzel2 distribuován některé zatížení. 

<center>
![Akce příklad vyrovnávání prahová hodnota][Image2]
</center>

> [!NOTE]
> "Vyrovnávání" zpracovává dva různé strategie pro správu zatížení v clusteru. Výchozí strategii, která používá správce prostředků clusteru je rozdělovat mezi uzly v clusteru. Další strategie je [defragmentace](service-fabric-cluster-resource-manager-defragmentation-metrics.md). Defragmentace se provede během stejné vyrovnávání spustit. Strategie vyrovnávání a defragmentace lze použít pro jiné metriky ve stejném clusteru. Služba může mít vyrovnávání a defragmentace metriky. Defragmentace metrik poměr zatížení v clusteru spustí nové vyvážení v případě, že je _pod_ vyrovnávání prahovou hodnotu. 
>

Získání pod prahovou hodnotou vyrovnávání není explicitní cíle. Vyrovnávání prahové hodnoty jsou právě *aktivační událost*. Při vyrovnávání spustí, určuje správce prostředků clusteru jaká vylepšení mohl zajistit, pokud existuje. Právě, protože vyrovnávání vyhledávání je spuštěna neznamená, že nic přesune. Někdy je clusteru imbalanced ale příliš omezené opravit. Alternativně vylepšení vyžadují přesuny, které jsou příliš [nákladná](service-fabric-cluster-resource-manager-movement-cost.md)).

## <a name="activity-thresholds"></a>Aktivita prahové hodnoty
V některých případech, i když jsou relativně imbalanced uzly *celkový* množství zatížení v clusteru je nízká. Nedostatečná zatížení může být přechodný vyhrazené IP adresy, nebo protože clusteru je nový a právě získávání připravili. V obou případech nemusíte chtít věnovat času vyrovnávání clusteru, protože je málo užitečného. Pokud cluster podstoupila vyrovnávání by tráví síť a výpočetní prostředky pohyb věcí bez provedení všechny velké *absolutní* rozdíl. Aby se zabránilo zbytečným přesune, další ovládací prvek označuje jako aktivita prahové hodnoty. Prahové hodnoty aktivity umožňuje zadat některé absolutní dolní mez aktivity. Pokud žádný uzel nad touto prahovou hodnotou, vyrovnávání není aktivované i v případě dosáhla prahová hodnota vyrovnávání.

Řekněme, že jsme zachovat naše vyrovnávání prahovou hodnotu tři pro tuto metriku. Také se stát, že máme 1536 prahovou hodnotu aktivity. V prvním případě clusteru je imbalanced za vyrovnávání prahová hodnota je žádný uzel splňuje této prahové hodnoty aktivity, nedojde k žádné akci. V příkladu dolní Uzel1 je nad prahovou hodnotou aktivity. Vzhledem k tomu, že se překročí prahovou hodnotu vyrovnávání a aktivity prahovou hodnotu pro metriku, vyrovnávání naplánován. Jako příklad Podíváme se na následující diagram: 

<center>
![Příklad aktivity prahová hodnota][Image3]
</center>

Stejně jako vyrovnávání prahové hodnoty aktivity prahové hodnoty jsou definované za metriku prostřednictvím definice clusteru:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

pomocí souboru ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery:

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

Prahové hodnoty vyrovnávání a aktivity jsou obě vázaný na konkrétní metrika - vyrovnávání se spustí pouze v případě překročení vyrovnávání prahová hodnota i aktivity prahové hodnoty pro stejnou metriku.

> [!NOTE]
> Není-li zadána, vyrovnávání prahovou hodnotu pro metriku je 1 a aktivity prahová hodnota je 0. To znamená, že správce prostředků clusteru pokusí zachovat tuto metriku perfektně vyrovnáváním pro všechny daného zatížení. Pokud používáte vlastní metriky se doporučuje explicitně definovat vlastní prahové hodnoty pro vyrovnávání a aktivity pro vaše metriky. 
>

## <a name="balancing-services-together"></a>Společně vyrovnávání služby
Zda je cluster imbalanced nebo ne je platné pro celý cluster rozhodnutí. Způsob, jakým jsme přejděte o její opravu je však přesunutí repliky jednotlivé služby a instance kolem. To dává smysl, ne? Pokud paměti je skládaný na jednom uzlu, může být více replik nebo instancí přispívání do ho. Opravě nevyváženosti může vyžadovat přesunutí stavová repliky nebo bezstavové instancí, které použít imbalanced metriku.

Občas, když služba, která nebyla samotné imbalanced získá přesunout (Nezapomeňte diskuzi o místní a globální provede dříve). Proč by služby získat přesunuta, když všechno, co byly vyrovnáváním metriky služby? Podíváme se na příklad:

- Řekněme, že existují čtyři služby, Service1, Service2, Service3 a Service4. 
- Service1 sestavy metrik Metric1 a Metric2. 
- Service2 sestavy metrik Metric2 a Metric3. 
- Service3 sestavy metrik Metric3 a Metric4.
- Service4 hlásí metriky Metric99. 

Surely se zobrazí, kde vytvoříme zde: existuje řetězec! Nemáme skutečně čtyři nezávislé služby, máme tři služby, které se vztahují a ten, který je vypnutý o sobě.

<center>
![Společně vyrovnávání služby][Image4]
</center>

Z důvodu tento řetězec je možné, že byl zjištěn rozdíl v metriky 1 – 4 může způsobit repliky nebo instance, které patří ke službám 1 – 3 pohyb. Taky víme, že byl zjištěn rozdíl v metriky 1, 2 nebo 3 nelze způsobit pohybů v Service4. Od přesunutí repliky by být žádný bod, nebo instance, které patří k Service4 kolem provést absolutně nic mít dopad na rovnováhu mezi počtem metriky 1 – 3.

Správce prostředků clusteru automaticky hodnoty na jaké služby jsou související. Přidání, odebrání nebo změna metriky pro služby může ovlivnit jejich vztahů. Například mezi dvěma spustí vyrovnávání Service2 může mít aktualizovaná tak, aby odebrat Metric2. Tím se přeruší řetězu mezi Service1 a Service2. Teď místo dvě skupiny související služby, existují tři:

<center>
![Společně vyrovnávání služby][Image5]
</center>

## <a name="next-steps"></a>Další postup
* Metriky se, jak správce prostředků služby Fabric clusteru spravuje využívání a kapacity v clusteru. Další informace o metriky a způsob jejich konfigurace, podívejte se na [v tomto článku](service-fabric-cluster-resource-manager-metrics.md)
* Náklady na přesunutí je jeden ze způsobů signalizace správce prostředků clusteru, že jsou dražší než jiné přesunout určité služby. Další informace o náklady na přesunutí, najdete v části [v tomto článku](service-fabric-cluster-resource-manager-movement-cost.md)
* Správce prostředků clusteru má několik omezení, které můžete nakonfigurovat zpomalit změn v clusteru. Nejsou běžně potřebné, ale chcete-li se dozvíte je [sem](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png

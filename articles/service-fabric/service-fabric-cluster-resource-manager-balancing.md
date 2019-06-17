---
title: Vyrovnání clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Úvod do vyrovnávání clusteru pomocí Service Fabric Cluster Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 74fe4f7c4c231f80c7555f39f840a85baae310e9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60809454"
---
# <a name="balancing-your-service-fabric-cluster"></a>Vyrovnávání vašeho clusteru service fabric
Service Fabric Cluster Resource Manager podporuje změny dynamického zatížení reakce na přidání nebo odebrání uzlů nebo služby. Také automaticky opraví narušení omezení a proaktivně znovu vytvoří rovnováhu clusteru. Ale jak často pocházejí tyto akce a co je aktivace?

Existují tři různé druhy práce, která provede Cluster Resource Manager. Jsou to tyto:

1. Umístění – tato fáze se zabývá uvedení jakékoli replik stavových nebo bezstavových instancí, které nebyly nalezeny. Umístění zahrnuje nové služby a zpracování replik stavových nebo bezstavových instancí, které selhaly. Tady jsou zpracovány odstranění a umístěním replik nebo instancí.
2. Omezení kontroluje – tato fáze kontroluje a opravuje porušení omezení různých umístění (pravidla) v rámci systému. Příklady pravidel jsou třeba k zajištění, že uzly nejsou překročena kapacita a že jsou splněné omezení umístění služby.
3. Vyrovnávání – této fázi kontroluje, jestli opětovné vyvážení je nezbytné podle nakonfigurované požadované úrovni vyrovnání pro různé požadované metriky. V takovém případě se pokusí najít uspořádání v clusteru, který je více s vyrovnáváním.

## <a name="configuring-cluster-resource-manager-timers"></a>Konfigurace časovače Cluster Resource Manageru
První sada kontrolních mechanismů kolem vyrovnávání představují sadu časovače. Tyto časovače určují, jak často Cluster Resource Manager zkontroluje clusteru a provede nápravné akce.

Každý z těchto různých typů opravy, které můžete vytvořit Cluster Resource Manageru se řídí jiné časovače, které řídí jeho četnost. Když se aktivuje každou časovač je naplánován. Ve výchozím nastavení správce prostředků:

* kontrol stavu a aplikuje aktualizace (jako záznam, který je uzel dolů) každých 1/10 sekundy
* Nastaví příznak Kontrola umístění 
* Nastaví příznak kontroly omezení za sekundu
* Nastaví příznak vyrovnávání každých pět sekund.

Níže jsou příklady konfigurace, kterými se řídí těmito časovače:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

prostřednictvím ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery:

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

Dnes Cluster Resource Manager provádí pouze jednu z těchto akcí v době, postupně. To je důvod, proč označujeme jako "minimální intervaly" a akce, které získáte při časovače přejít jako "nastavení příznaků" provedeny tyto časovače. Například Cluster Resource Manageru se postará o čekající požadavky na vytvoření služeb před vyrovnávání clusteru. Jak je vidět ve výchozí časové intervaly zadaný Cluster Resource Manager prochází všechno, co je potřeba udělat často. Obvykle to znamená, sadu změn provedených během jednotlivých kroků, které je malý. Provádění změn malé často umožňuje Cluster Resource Manageru k buďte interaktivní, když nastane v clusteru. Výchozí časovače poskytují některé dávkové zpracování, protože mnoho stejné typy událostí, které jsou obvykle probíhají souběžně. 

Například když uzly se nepovede dělají tak celý domén selhání najednou. Všechny tyto chyby jsou zaznamenána během další stav aktualizace po *PLBRefreshGap*. Opravy jsou určeny v průběhu následujících umístění, kontrola omezení a rozložení zátěže spustí. Ve výchozím nastavení Cluster Resource Manageru není skenování prostřednictvím hodin změny v clusteru a pokusu o vyřešení všech změn najednou. To by mohlo dojít k nárůstům provozu.

Cluster Resource Manager potřebuje také některé další informace, které určí, zda imbalanced clusteru. K tomu máme dva kusy konfigurace: *BalancingThresholds* a *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Vyrovnávání prahové hodnoty
Vyrovnávání prahové hodnoty je hlavním ovládacím prvkem pro spuštění nové vyvážení. Je vyrovnávání prahové hodnoty pro metriku _poměr_. Pokud zatížení metriky na nejvíce načíst uzlu dělený objem zátěže na nejméně načíst uzlu překročí tuto metriku *BalancingThreshold*, dají se imbalanced clusteru. V důsledku vyrovnávání se aktivuje při příštím kontroluje Cluster Resource Manager. *MinLoadBalancingInterval* časovače definuje, jak často by měla Cluster Resource Manager zkontrolujte, zda opětovné vyvážení je nezbytné. Kontrola neznamená, že se nic nestane. 

Vyrovnávání prahové hodnoty jsou definovány na základě za metriku jako součást definice clusteru. Další informace týkající se metrik, projděte si [v tomto článku](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

prostřednictvím ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery:

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

V tomto příkladu každá služba využívá jednu jednotku některé metriky. V horním příkladu maximálního zatížení na uzlu je pět a minimum je dva. Řekněme, že je vyrovnávání prahová hodnota pro tuto metriku tři. Protože poměr v clusteru je 5/2 = 2.5, který je nižší než zadané vyrovnávání tři prahovou hodnotu, jsou rovnoměrně clusteru. Žádné služby Vyrovnávání se aktivuje, když kontroluje Cluster Resource Manager.

V příkladu dole maximálního zatížení na uzlu je 10, zatímco minimum je dvě, výsledkem je poměr pět. Pět zadefinujete, přesáhne určenou prahovou hodnotu vyrovnávání tří pro tuto metriku. V důsledku toho vyrovnávání spuštění bude naplánováno další vyrovnávání časovač vyvolá. V situaci, tímto způsobem je nějaké zatížení obvykle distribuován do Uzel3. Protože Service Fabric Cluster Resource Manager nepoužívá greedy přístup, může do Node2 distribuované nějaké zatížení. 

<center>

![Vyrovnávání prahovou hodnotu příklad akce][Image2]
</center>

> [!NOTE]
> "Vyrovnávání" zpracovává dvě různé strategie pro správu zatížení v clusteru. Výchozí strategie, která používá Cluster Resource Manager je můžete distribuovat zatížení napříč uzly v clusteru. Další možností je [defragmentace](service-fabric-cluster-resource-manager-defragmentation-metrics.md). Defragmentace se provádí během stejné vyrovnávání spustit. Strategie vyrovnávání a defragmentace lze použít pro různé požadované metriky ve stejném clusteru. Služba může mít vyrovnávání a defragmentaci metrik. Pro defragmentaci metrik, poměr zatížení v clusteru spustí nové vyvážení po _níže_ vyrovnávání prahovou hodnotu. 
>

Získání pod prahovou hodnotou vyrovnávání není explicitní cíle. Vyrovnávání prahové hodnoty jsou jenom *aktivační událost*. Při vyrovnávání zatížení běží, Cluster Resource Manager Určuje zdokonaleních, může být, pokud existuje. To, že je vydáno vyrovnávání vyhledávání neznamená, že se že nic přesouvá. Někdy je cluster imbalanced ale moc omezené, chcete-li opravit. Můžete také vylepšení vyžadují pohybů plb typu, které jsou příliš [nákladné](service-fabric-cluster-resource-manager-movement-cost.md)).

## <a name="activity-thresholds"></a>Prahových hodnot aktivit
Někdy, i když jsou poměrně imbalanced uzly *celkový* zatížením v clusteru je nízká. Chybějící zatížení může být přechodná vyhrazené IP adresy nebo polohy, protože clusteru je nový a teprve začíná. V obou případech nemusíte chtít ztrácet čas vyrovnávání clusteru, protože je malý být získány. Pokud cluster podstoupila vyrovnávání by věnovat síťové a výpočetní prostředky k přesunutí věcí bez provedení jakékoli velké *absolutní* rozdíl. Aby se zabránilo zbytečným přesune, existuje jiný ovládací prvek označované jako prahových hodnot aktivit. Prahových hodnot aktivit můžete zadat některé absolutní dolní mez pro aktivitu. Pokud žádný uzel nad touto prahovou hodnotou, neaktivuje vyrovnávání i v případě, že při dosažení prahové hodnoty vyrovnávání.

Řekněme, že můžeme uchovávat prahové hodnoty vyrovnávání tří pro tuto metriku. Předpokládejme také, že máme prahovou hodnotu aktivitu 1536. V prvním případě clusteru je imbalanced za vyrovnávání prahová hodnota neexistuje žádný uzel splňuje této prahové hodnoty aktivity, nic se nestane. V příkladu dole Node1 je nad prahovou hodnotou aktivity. Protože se překročila prahovou hodnotu vyrovnávání a aktivita prahové hodnoty pro metriku, vyrovnávání naplánován. Jako příklad Podívejme se na následující diagram: 

<center>

![Příklad aktivity prahová hodnota][Image3]
</center>

Stejně jako vyrovnávání prahové hodnoty prahových hodnot aktivit jsou definované na metrika prostřednictvím definice clusteru:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

prostřednictvím ClusterConfig.json pro samostatné nasazení nebo Template.json pro Azure hostované clustery:

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

Vyrovnávání a aktivita prahové hodnoty jsou obě váže na konkrétní metrika - vyrovnávání se aktivuje jenom v případě, že prahová hodnota vyrovnávání i aktivita prahová hodnota překročena stejné metriky.

> [!NOTE]
> Pokud není zadán, vyrovnávání prahové hodnoty pro metriku je 1, a aktivitu prahová hodnota je 0. To znamená, že Cluster Resource Manager se pokusí uchovávat tuto metriku dokonale vyrovnávání pro jakékoli dané zatížení. Pokud používáte vlastní metriky doporučujeme explicitně definovat vlastní rozložení zátěže a aktivita prahové hodnoty pro metriky. 
>

## <a name="balancing-services-together"></a>Služby Vyrovnávání dohromady
Určuje, zda je cluster imbalanced nebo ne je celoclusterový rozhodnutí. Jednotlivé služby replik a instancí kolem, tak, jak bychom postupovali při její opravu přesouvá. To dává smysl, ne? Pokud paměti je skládaný na jeden uzel, více replik nebo instancí může přispět k němu. Řešení míru nevyrovnaného může vyžadovat přesunete replik stavových nebo bezstavových instancí, které používají imbalanced metriku.

Čas od času, ale služba, která sama imbalanced nebyla přesunuta (Nezapomeňte diskuzi o místní a globální oceňuje dříve). Proč by služba získat přesouvány při všech funkcí, které byly metriky služby s vyrovnáváním? Podívejme se na příklad:

- Řekněme, že existují čtyři služby, Service1, Service2, Service3 a Service4. 
- Service1 sestavy metrik Metric1 a Metric2. 
- Service2 sestavy metrik Metric2 a Metric3. 
- Service3 sestavy metrik Metric3 a Metric4.
- Service4 sestavy metrik Metric99. 

Seděl můžete vidět, kde jsme teď tady: Existuje řetězec! Nemáme skutečně čtyř nezávislých služeb, máme tři služby, které se týkají a jednu, která sama o sobě je vypnuté.

<center>

![Služby Vyrovnávání dohromady][Image4]
</center>

Z důvodu tento řetězec je možné, že nevyrovnanosti metriky 1 – 4 může způsobit replik nebo instancí patřící do služby 1-3 uspořádat jinak. Uvědomujeme si také, že nevyrovnanosti metriky 1, 2 nebo 3 nemůže způsobit pohybů plb typu v Service4. Nebudou mít žádný bod od přesunutí replik nebo instancí, které patří do Service4 kolem můžete neprovedení naprosto dopad na rovnováhu mezi metriky 1-3.

Cluster Resource Manager automaticky přijde na to, jaké služby jsou související. Přidání, odebrání nebo změně metriky pro služby může ovlivnit jejich vztahy. Například mezi dvěma spuštěními vyrovnávání Service2 může byly aktualizovány Metric2 odebrat. Tím je prolomen řetězce mezi Service1 a Service2. Teď místo dvou skupin souvisejících služeb, jsou dostupné tři:

<center>

![Služby Vyrovnávání dohromady][Image5]
</center>

## <a name="next-steps"></a>Další postup
* Metriky se, jak spravuje správce prostředků Service Fabric Cluster využití a kapacitu v clusteru. Další informace o metrikách a způsob jejich konfigurace, podívejte se na [v tomto článku](service-fabric-cluster-resource-manager-metrics.md)
* Náklady na přesunutí je jeden způsob, jak signalizace pro Cluster Resource Manager, že určité služby jsou dražší než jiné přesunout. Další informace o náklady na přesunutí, najdete v tématu [v tomto článku](service-fabric-cluster-resource-manager-movement-cost.md)
* Cluster Resource Manager má několik omezení, které můžete nakonfigurovat zpomalovat četnosti změn v clusteru. Nejsou běžně potřebné, ale pokud je potřebujete informace o nich [zde](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png

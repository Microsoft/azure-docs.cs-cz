---
title: Vyvážení clusteru Azure Service Fabric
description: Úvod k vyvažování clusteru pomocí Správce prostředků clusteru Service Fabric.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b6df25b525975f2d4fe6a02064e81f359a804c58
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416272"
---
# <a name="balancing-your-service-fabric-cluster"></a>Vyvažování clusteru prostředků infrastruktury služby
Správce prostředků clusteru Service Fabric podporuje změny dynamického zatížení, reaguje na přidání nebo odebrání uzlů nebo služeb. Také automaticky opravuje porušení omezení a proaktivně vyvažuje cluster. Ale jak často jsou tyto akce prováděny a co je spouští?

Správce prostředků clusteru provádí tři různé kategorie práce. Jsou to tyto:

1. Umístění – tato fáze se zabývá umístěním stavových replik nebo bezstavových instancí, které chybí. Umístění zahrnuje nové služby a zpracování stavové repliky nebo bezstavové instance, které se nezdařily. Zde jsou zpracovány a přetažení mantinelu repliky nebo instance.
2. Kontroly omezení – tato fáze kontroluje a opravuje porušení různých omezení umístění (pravidel) v rámci systému. Příklady pravidel jsou věci, jako je zajištění, že uzly nejsou nad kapacitu a že jsou splněny omezení umístění služby.
3. Vyrovnávání – tato fáze kontroluje, zda je nutné vyvažování na základě nakonfigurované požadované úrovně vyvážení pro různé metriky. Pokud ano, pokusí se najít uspořádání v clusteru, který je vyváženější.

## <a name="configuring-cluster-resource-manager-timers"></a>Konfigurace časovačů Správce prostředků clusteru
První sada ovládacích prvků kolem vyvažování jsou sada časovačů. Tyto časovače určují, jak často Správce prostředků clusteru zkoumá cluster a provádí nápravná opatření.

Každý z těchto různých typů oprav, které může Správce prostředků clusteru provést, je řízen jiným časovačem, který řídí jeho frekvenci. Při každém časovači je naplánována úloha. Ve výchozím nastavení správce prostředků:

* skenuje jeho stav a aplikuje aktualizace (jako je záznam, že uzel je dole) každých 1/10 th sekundy
* nastaví příznak kontroly umístění každou sekundu
* Nastaví příznak kontroly omezení každou sekundu
* nastaví příznak vyvažování každých pět sekund

Příklady konfigurace, které řídí tyto časovače, jsou uvedeny níže:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

prostřednictvím souboru ClusterConfig.json pro samostatná nasazení nebo Template.json pro hostované clustery Azure:

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

Správce prostředků clusteru dnes provádí pouze jednu z těchto akcí najednou, postupně. To je důvod, proč jsme se odkazují na tyto časovače jako "minimální intervaly" a akce, které si vzít, když časovače zhasnou jako "nastavení příznaků". Správce prostředků clusteru se například postará o čekající požadavky na vytvoření služeb před vyvažováním clusteru. Jak můžete vidět ve výchozích zadaných časových intervalech, Správce prostředků clusteru prohledá vše, co potřebuje k tomu často. Obvykle to znamená, že sada změn provedených během každého kroku je malá. Provádění malých změn často umožňuje Správce prostředků clusteru reagovat, když se věci dějí v clusteru. Výchozí časovače poskytují některé dávkování, protože mnoho stejných typů událostí má tendenci vyskytovat současně. 

Například při selhání uzlů mohou tak učinit celé domény selhání najednou. Všechny tyto chyby jsou zachyceny během další aktualizace stavu po *PLBRefreshGap*. Opravy jsou určeny během následujícího umístění, kontroly omezení a vyvažování. Ve výchozím nastavení správce prostředků clusteru neprohledává hodiny změn v clusteru a nepokouší se vyřešit všechny změny najednou. To by vedlo k výbuchům konve.

Správce prostředků clusteru také potřebuje některé další informace k určení, zda cluster uvyváženosti. Pro které máme další dva kusy konfigurace: *BalancingThresholds* a *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Vyrovnávací prahy
Vyvažovací prahová hodnota je hlavní ovládací prvek pro aktivaci vyvažování. Prahová hodnota vyvažování pro metriku je _poměr_. Pokud zatížení metriky na nejvíce načteném uzlu vydělené množstvím zatížení na nejméně načteném uzlu překročí tuto *metriku BalancingThreshold*, pak je cluster nevyvážený. V důsledku toho je vyvažování spuštěno při příštím kontrole Správce prostředků clusteru. Časovač *MinLoadBalancingInterval* definuje, jak často by měl Správce prostředků clusteru kontrolovat, zda je nutné vyvažování. Kontrola neznamená, že se něco stane. 

Vyrovnávací prahové hodnoty jsou definovány na základě metriky jako součást definice clusteru. Další informace o metrikách najdete v [tomto článku](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

prostřednictvím souboru ClusterConfig.json pro samostatná nasazení nebo Template.json pro hostované clustery Azure:

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

![Příklad vyrovnávací prahové hodnoty][Image1]
</center>

V tomto příkladu každá služba spotřebovává jednu jednotku některé metriky. V horním příkladu je maximální zatížení uzlu pět a minimum je dvě. Řekněme, že práh vyvážení pro tuto metriku je tři. Vzhledem k tomu, že poměr v clusteru je 5/2 = 2,5 a to je menší než zadaná vyrovnávací prahová hodnota tří, je cluster vyvážený. Při kontrole Správce prostředků clusteru se neaktivuje žádné vyvažování.

V dolním příkladu je maximální zatížení uzlu 10, zatímco minimum je dvě, výsledkem je poměr pět. Pět je větší než určená vyrovnávací prahová hodnota tří pro tuto metriku. V důsledku toho bude naplánováno spuštění vyvážení při příštím spuštění časovače vyvažování. V situaci, jako je tato, je obvykle distribuováno do uzlu Node3. Vzhledem k tomu, že Správce prostředků clusteru Service Fabric nepoužívá chamtivý přístup, některé zatížení může být také distribuovánno do Node2. 

<center>

![Příklad vyrovnávací prahové hodnoty Akce][Image2]
</center>

> [!NOTE]
> "Vyrovnávání" zpracovává dvě různé strategie pro správu zatížení v clusteru. Výchozí strategie, kterou správce prostředků clusteru používá, je distribuce zatížení mezi uzly v clusteru. Druhou strategií je [defragmentace](service-fabric-cluster-resource-manager-defragmentation-metrics.md). Defragmentace se provádí během stejného vyvažování spustit. Strategie vyrovnávání a defragmentace lze použít pro různé metriky v rámci stejného clusteru. Služba může mít metriky vyrovnávání i defragmentace. Pro metriky defragmentace poměr zatížení v clusteru aktivuje vyvažování, když je _pod_ prahovou hodnotou vyvažování. 
>

Dostat se pod prahovou hodnotu vyvážení není explicitní cíl. Vyvažování prahových hodnot je pouze *aktivační událost*. Při vyvažování běží, Správce prostředků clusteru určuje, jaká zlepšení může provést, pokud existuje. Jen proto, že je vyvažování hledání odstartoval neznamená, že se něco pohybuje. Někdy clusteru je nevyvážená, ale příliš omezena na opravu. Alternativně, zlepšení vyžadují pohyby, které jsou příliš [nákladné](service-fabric-cluster-resource-manager-movement-cost.md)).

## <a name="activity-thresholds"></a>Prahové hodnoty aktivity
Někdy, i když uzly jsou relativně nevyvážené, *celkové* množství zatížení v clusteru je nízká. Nedostatek zatížení může být přechodný pokles, nebo proto, že cluster je nový a jen dostat bootstrapped. V obou případech možná nebudete chtít trávit čas vyrovnáváním clusteru, protože je málo získat. Pokud cluster prošel vyvažování, byste strávit sítě a výpočetní prostředky k pohybu věci kolem bez jakéhokoli velkého *absolutní* rozdíl. Chcete-li se vyhnout zbytečným přesunům, existuje další ovládací prvek známý jako prahové hodnoty aktivity. Prahové hodnoty aktivity umožňuje zadat některé absolutní dolní mez aktivity. Pokud žádný uzel je nad tuto prahovou hodnotu, vyrovnávání se neaktivuje i v případě, že je splněna prahová hodnota vyvažování.

Řekněme, že pro tuto metriku zachováme náš práh vyvažování tří. Řekněme také, že máme práh aktivity z roku 1536. V prvním případě, zatímco cluster je nevyvážený na vyrovnávací prahovou hodnotu neexistuje žádný uzel splňuje tuto prahovou hodnotu aktivity, takže se nic nestane. V dolním příkladu je Uzel1 nad prahovou hodnotou aktivity. Vzhledem k tomu, že jsou překročeny prahová hodnota vyvažování a prahová hodnota aktivity pro metriku, je naplánováno vyvažování. Jako příklad se podívejme na následující diagram: 

<center>

![Příklad prahové hodnoty aktivity][Image3]
</center>

Stejně jako vyvažování prahových hodnot jsou prahové hodnoty aktivity definovány podle metriky pomocí definice clusteru:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

prostřednictvím souboru ClusterConfig.json pro samostatná nasazení nebo Template.json pro hostované clustery Azure:

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

Prahové hodnoty vyvažování a aktivity jsou vázány na konkrétní metriku – vyrovnávání se aktivuje pouze v případě, že je pro stejnou metriku překročena prahová hodnota vyvažování i prahová hodnota aktivity.

> [!NOTE]
> Není-li zadána, je prahová hodnota vyvažování pro metriku 1 a prahová hodnota aktivity 0. To znamená, že Správce prostředků clusteru se bude snažit, aby tato metrika dokonale vyvážená pro dané zatížení. Pokud používáte vlastní metriky, doporučujeme explicitně definovat vlastní prahové hodnoty pro vyrovnávání a aktivitu pro metriky. 
>

## <a name="balancing-services-together"></a>Společné vyvažování služeb
Zda je cluster nevyvážený nebo ne, je celé rozhodnutí celého clusteru. Nicméně, způsob, jakým jdeme o opravu je přesunutí jednotlivých replik služby a instance kolem. To dává smysl, ne? Pokud je paměť naskládaná na jednom uzlu, může k ní přispívat více replik nebo instancí. Oprava nerovnováhy může vyžadovat přesunutí libovolné stavové repliky nebo bezstavové instance, které používají nevyváženou metriku.

Občas však služba, která nebyla sama o sobě nevyvážená, se přesune (vzpomeňte si na diskusi o místních a globálních vahách dříve). Proč by se služba přesunula, když byly všechny metriky této služby vyvážené? Podívejme se na příklad:

- Řekněme, že existují čtyři služby, Service1, Service2, Service3 a Service4. 
- Metriky Service1 jsou metriky Metrika1 a Metrika2. 
- Metriky Service2 jsou metriky Metrika2 a Metrika3. 
- Metriky Service3 jsou metriky Metrika3 a Metrika4.
- Service4 hlásí metriku Metric99. 

Jistě vidíte, kam jdeme: Je tam řetěz! Ve skutečnosti nemáme čtyři nezávislé služby, máme tři služby, které spolu souvisejí, a jednu, která je sama o sobě vypnuta.

<center>

![Vyvažování služeb společně][Image4]
</center>

Z důvodu tohoto řetězce je možné, že nerovnováha v metriky 1-4 může způsobit repliky nebo instance patřící do služeb 1-3 se pohybovat. Víme také, že nerovnováha v metrikách 1, 2 nebo 3 nemůže způsobit pohyby v Service4. Nemá smysl, protože přesunutí repliky nebo instance patřící do Service4 kolem může dělat absolutně nic ovlivnit rovnováhu metriky 1-3.

Správce prostředků clusteru automaticky zjistí, jaké služby spolu souvisejí. Přidání, odebrání nebo změna metrik pro služby může mít vliv na jejich vztahy. Například mezi dvěma spuštěnívy vyvažování Service2 může být aktualizován odebrat Metric2. Tím přerušíte řetěz mezi Service1 a Service2. Nyní namísto dvou skupin souvisejících služeb existují tři:

<center>

![Vyvažování služeb společně][Image5]
</center>

## <a name="next-steps"></a>Další kroky
* Metriky jsou způsob, jakým je manger prostředků clusteru Service Fabric spravuje spotřebu a kapacitu v clusteru. Další informace o metrikách a jejich konfiguraci najdete v [tomto článku](service-fabric-cluster-resource-manager-metrics.md)
* Náklady na přesun je jedním ze způsobů, jak správci prostředků clusteru signalizovat, že přesun některých služeb je nákladnější než jiné. Další informace o nákladech na přesun naleznete v [tomto článku](service-fabric-cluster-resource-manager-movement-cost.md)
* Správce prostředků clusteru má několik omezení, které můžete nakonfigurovat tak, aby zpomalily změny v clusteru. Obvykle nejsou nutné, ale pokud je potřebujete, můžete se o nich dozvědět [zde](service-fabric-cluster-resource-manager-advanced-throttling.md)
* Správce prostředků clusteru dokáže rozpoznat a zpracovat podclustering (situace, která někdy nastane při použití omezení umístění a vyvažování). Chcete-li se dozvědět, jak může podclustering ovlivnit vyvažování a jak jej můžete zvládnout, podívejte se [zde](cluster-resource-manager-subclustering.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png

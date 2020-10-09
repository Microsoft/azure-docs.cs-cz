---
title: Vyvážení clusteru Azure Service Fabric
description: Úvod k vyrovnávání clusteru s clusterem Service Fabric Správce prostředků.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b6df25b525975f2d4fe6a02064e81f359a804c58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81416272"
---
# <a name="balancing-your-service-fabric-cluster"></a>Vyvážení clusteru Service Fabric
Cluster Service Fabric Správce prostředků podporuje změny dynamického načtení, které fungují na přidávání nebo odebírání uzlů nebo služeb. Také automaticky opravuje porušení omezení a proaktivně znovu vyrovnává cluster. Ale jak často jsou tyto akce podniknuty a které je aktivují?

Existují tři různé kategorie práce, které cluster Správce prostředků provádí. Jsou to tyto:

1. Umístění – Tato fáze se zabývá umístěním všech stavových replik nebo nestavových instancí, které chybí. Umístění zahrnuje nové služby a zpracování stavových replik nebo nestavových instancí, u kterých došlo k chybě. Odstranění a odstranění replik nebo instancí se zpracovává tady.
2. Kontroly omezení – Tato fáze kontroluje a opravuje porušení různých omezení umístění (pravidel) v rámci systému. Příklady pravidel jsou věci, jako je například zajištění toho, že uzly nejsou nad kapacitou a že jsou splněné omezení umístění služby.
3. Vyrovnávání – Tato fáze zkontroluje, jestli je potřeba vyrovnávání zatížení na základě nakonfigurované požadované úrovně vyrovnání pro různé metriky. Pokud se tak pokusí najít uspořádání v clusteru, které je vyváženější.

## <a name="configuring-cluster-resource-manager-timers"></a>Konfigurace časovačů Správce prostředků clusteru
První sada ovládacích prvků kolem Vyrovnávání je sada časovačů. Tyto časovače určují, jak často cluster Správce prostředků prověřuje cluster a provede nápravné akce.

Každý z těchto různých typů oprav Správce prostředků clusteru je možné řídit jiným časovačem, který řídí jeho četnost. Když se každý časovač aktivuje, úloha se naplánuje. Ve výchozím nastavení Správce prostředků:

* Zkontroluje svůj stav a nainstaluje aktualizace (například nahrávání, že uzel je mimo provoz) každých 1/desátý druhý.
* každou sekundu nastaví příznak kontroly umístění.
* každou sekundu nastaví příznak kontroly omezení.
* každých pět sekund nastaví příznak vyrovnávání.

Příklady konfigurace upravující tyto časovače jsou následující:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

prostřednictvím ClusterConfig.jsv pro samostatná nasazení nebo Template.jsv případě hostovaných clusterů Azure:

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

V současné době cluster Správce prostředků provádí pouze jednu z těchto akcí postupně. Proto na tyto časovače odkazujeme jako na "minimální intervaly" a na akce, které se provedou, když se časovače najdou jako "nastavení příznaků". Například cluster Správce prostředků postará o nedokončené žádosti o vytvoření služeb před vyrovnáváním clusteru. Jak vidíte ve výchozích časových intervalech, Správce prostředků clusteru vyhledá cokoli, co potřebuje k tomu často. Obvykle to znamená, že sada změn provedených během každého kroku je malá. Provádění malých změn často umožňuje, aby cluster Správce prostředků reagovat, když se v clusteru vyskytují nějaké věci. Výchozí časovače poskytují určitou dávkování, protože mnohé ze stejných typů událostí se obvykle vyskytují současně. 

Například když uzly selžou, můžou to udělat po celou dobu selhání. Všechny tyto chyby jsou zachyceny během příští aktualizace stavu po *PLBRefreshGap*. Opravy se určují během následujícího umístění, kontroly omezení a vyrovnávání spuštění. Ve výchozím nastavení Správce prostředků clusteru nekontrolují hodiny změn v clusteru a snaží se vyřešit všechny změny najednou. To by vedlo k nárůstu počtu změn.

Cluster Správce prostředků taky potřebuje nějaké další informace, abyste zjistili, jestli je cluster nevyvážený. V případě, že máme dvě další části konfigurace: *BalancingThresholds* a *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Prahové hodnoty vyvážení
Prahová hodnota pro vyrovnávání je hlavním ovládacím prvkem pro aktivaci opětovného vyrovnávání. Prahová hodnota vyvážení metriky je _poměr_. Pokud zatížení metriky na největším načteném uzlu dělené množstvím zatížení u nejmenšího načteného uzlu přesáhne tuto metriku *BalancingThreshold*, cluster se vyrovnává. Při příští kontrole Správce prostředků clusteru se aktivuje vyvážení výsledku. Časovač *MinLoadBalancingInterval* definuje, jak často by měl správce prostředků cluster kontrolovat, jestli je potřeba vyrovnávání zatížení. Kontrola neznamená, že dojde k nějakému problému. 

Prahové hodnoty pro vyvážení jsou definovány na základě metriky jako součást definice clusteru. Další informace o metrikách najdete v [tomto článku](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

prostřednictvím ClusterConfig.jsv pro samostatná nasazení nebo Template.jsv případě hostovaných clusterů Azure:

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

![Příklad prahové hodnoty pro vyvážení][Image1]
</center>

V tomto příkladu každá služba spotřebovává jednu jednotku nějaké metriky. V horním příkladu je maximální zatížení uzlu pět a minimum je dva. Řekněme, že prahová hodnota pro vyvážení této metriky je tři. Vzhledem k tomu, že poměr v clusteru je 5/2 = 2,5 a je menší než zadaná prahová hodnota pro vyrovnávání zatížení tři, cluster se vyrovnává. Když cluster Správce prostředků kontroluje, neaktivuje se žádné vyrovnávání.

V dolním příkladu je maximální zatížení uzlu 10, zatímco minimum je dva, což vede k poměru pěti. Pět je větší než stanovená prahová hodnota vyvážení tři pro tuto metriku. V důsledku toho bude při příštím spuštění časovače vyrovnávání zatížení naplánováno spuštění opětovného vyrovnávání. V situaci, kdy je toto načtení obvykle distribuováno do Uzel3. Vzhledem k tomu, že Cluster Service Fabric Správce prostředků nepoužívá hladový přístup, může být některé zatížení distribuováno také do Uzel2. 

<center>

![Vyvážení akcí – příklad prahové hodnoty][Image2]
</center>

> [!NOTE]
> "Vyrovnává" zpracovává dvě různé strategie pro správu zatížení v clusteru. Výchozí strategií, kterou Správce prostředků cluster používá, je distribuce zatížení mezi uzly v clusteru. Další strategií je [Defragmentace](service-fabric-cluster-resource-manager-defragmentation-metrics.md). Defragmentace se provádí během stejného vyrovnávání běhu. Strategie vyrovnávání a defragmentace lze použít pro různé metriky v rámci stejného clusteru. Služba může mít metriky vyrovnávání i defragmentace. V případě metriky defragmentace se poměr zatížení v clusteru spustí znovu vyvážení, pokud je _pod_ prahovou hodnotou vyrovnávání. 
>

Získání pod prahovou hodnotou pro vyvážení není explicitní cíl. Prahové hodnoty vyvážení jsou pouze *triggerem*. Při vyrovnávání zatížení cluster Správce prostředků určuje, která vylepšení může dělat, pokud nějaké máte. Vzhledem k tomu, že se vypíná vyrovnávání vyhledávání, neznamená to, že se přesune. V některých případech je cluster nevyvážený, ale je moc omezený na správný. Další možností je, že vylepšení vyžadují příliš [nákladné](service-fabric-cluster-resource-manager-movement-cost.md)přesuny.

## <a name="activity-thresholds"></a>Prahové hodnoty aktivity
I když jsou uzly relativně nevyvážené, *celkové* množství zatížení v clusteru je nízké. Nedostatku zátěže může být přechodný DIP nebo to, že cluster je nový a jenom se načítá. V obou případech možná nebudete chtít strávit dobu vyrovnávání clusterů, protože je to málo. Pokud je vyrovnávání zatížení clusteru, strávíte tím síťové a výpočetní prostředky, které se budou pohybovat bez jakýchkoli velkých *absolutních* rozdílů. Aby nedocházelo k zbytečnému přesunutí, je další ovládací prvek známý jako prahové hodnoty aktivity. Prahové hodnoty aktivity umožňují zadat absolutní dolní mez pro aktivitu. Pokud žádný uzel není nad touto prahovou hodnotou, vyvážení se neaktivuje ani v případě, že je splněno prahová hodnota pro vyvážení.

Řekněme, že pro tuto metriku uchováváme prahovou hodnotu pro vyvážení tři. Řekněme také, že máme prahovou hodnotu aktivity 1536. V prvním případě je v případě, že cluster není vyrovnaný na prahovou hodnotu pro vyrovnávání zatížení, nesplňuje žádná prahová hodnota aktivity, takže nic nestane. V dolním příkladu je Uzel1 nad prahovou hodnotou aktivity. Vzhledem k tomu, že prahová hodnota pro vyvážení i prahová hodnota aktivity pro tuto metriku jsou, je vyrovnávání zatížení naplánováno. V příkladu se podívejme na následující diagram: 

<center>

![Příklad prahové hodnoty aktivity][Image3]
</center>

Stejně jako prahové hodnoty pro vyvážení jsou prahové hodnoty aktivity definovány na základě metriky prostřednictvím definice clusteru:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

prostřednictvím ClusterConfig.jsv pro samostatná nasazení nebo Template.jsv případě hostovaných clusterů Azure:

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

Prahové hodnoty vyrovnávání a aktivity jsou vázané na konkrétní vyrovnávání metrik se aktivují jenom v případě, že prahová hodnota pro vyrovnávání zatížení i prahová hodnota aktivity se pro stejnou metriku překročí.

> [!NOTE]
> Není-li parametr zadán, je prahová hodnota pro vyrovnávání metriky 1 a prahová hodnota aktivity je 0. To znamená, že Správce prostředků clusteru se pokusí zajistit, aby metrika byla dokonale vyvážená pro jakékoli dané zatížení. Pokud používáte vlastní metriky, doporučujeme, abyste explicitně definovali vlastní prahové hodnoty pro vyrovnávání a aktivity pro vaše metriky. 
>

## <a name="balancing-services-together"></a>Vyrovnávání služeb dohromady
Bez ohledu na to, jestli je cluster nevyvážený, nebo nejedná se o rozhodnutí v rámci clusteru. Nicméně způsob, jakým se chystáme opravit, přesouvá jednotlivé repliky služby a instance kolem. To je to smysl, který je správný? Pokud je paměť sestavená na jednom uzlu, může do ní přispívat více replik nebo instancí. Oprava nerovnováhy by mohla vyžadovat přesunutí jakékoli stavové repliky nebo bezstavových instancí, které používají nevyváženou metriku.

V některých případech je však přesun služby, která se sama vyrovnala (poznáte diskuzi o lokálních a globálních hmotnostech dříve). Proč by se služba mohla přesunout, když se vyrovnávají všechny metriky této služby? Pojďme se podívat na příklad:

- Řekněme, že jsou k dispozici čtyři služby, Service1, Jazyka2, Service3 a Service4. 
- Service1 hlásí metriky Metric1 a Metric2. 
- Jazyka2 hlásí metriky Metric2 a Metric3. 
- Service3 hlásí metriky Metric3 a Metric4.
- Service4 hlásí metriku Metric99. 

Surely vidíte, kde tady nacházím: existuje řetězec! Ve skutečnosti nepoužíváme čtyři nezávislé služby, máme tři služby, které jsou v relaci, a jednu z nich.

<center>

![Vyrovnávání služeb dohromady][Image4]
</center>

Z důvodu tohoto řetězce je možné, že nerovnováha v metrikách 1-4 může způsobit, že repliky nebo instance patřící ke službám 1-3 mají být přesunuty. Víme také, že nerovnováha v metrikách 1, 2 nebo 3 nemůže způsobit pohyb v Service4. Od přesunutí replik nebo instancí, které patří do Service4, by nedocházelo k žádnému okamžiku, který by ovlivnil rovnováhu metrik 1-3.

Cluster Správce prostředků automaticky vyhodnotí, které služby jsou v relaci. Přidávání, odebírání a změny metrik pro služby mohou ovlivnit jejich vztahy. Například mezi dvěma spuštěními vyrovnávání Jazyka2 může být aktualizováno odebrání Metric2. Tím se přeruší řetěz mezi Service1 a Jazyka2. Nyní se místo dvou skupin souvisejících služeb nacházejí tři:

<center>

![Vyrovnávání služeb dohromady][Image5]
</center>

## <a name="next-steps"></a>Další kroky
* Metriky představují způsob, jakým Správce prostředků clusteru Service Fabric spravuje spotřebu a kapacitu v clusteru. Další informace o metrikách a jejich konfiguraci najdete v [tomto článku](service-fabric-cluster-resource-manager-metrics.md) .
* Náklady na pohyb jsou jedním ze způsobů signalizace clusteru Správce prostředků, že některé služby jsou dražší než ostatní. Další informace o nákladech na pohyb najdete v [tomto článku](service-fabric-cluster-resource-manager-movement-cost.md) .
* Cluster Správce prostředků má několik omezení, která můžete nakonfigurovat pro zpomalení změn v clusteru. Nejsou obvykle nutné, ale pokud je potřebujete, můžete o nich získat informace [zde](service-fabric-cluster-resource-manager-advanced-throttling.md) .
* Cluster Správce prostředků může rozpoznat a zpracovat subclustery (situace, která někdy nastane při použití omezení umístění a vyrovnávání). Informace o tom, jak může subclustering ovlivnit vyrovnávání a jak se dá zpracovávat, najdete [tady](cluster-resource-manager-subclustering.md) .

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png

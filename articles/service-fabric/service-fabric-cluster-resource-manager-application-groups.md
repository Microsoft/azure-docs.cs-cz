---
title: Správce prostředků Service Fabric clusteru – skupiny aplikací
description: Přehled funkce skupiny aplikací v clusteru Service Fabric Správce prostředků
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 988c7ce52125800c16aa785d5b1458604a927ecd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75452156"
---
# <a name="introduction-to-application-groups"></a>Seznámení se skupinami aplikací
Cluster Service Fabric Správce prostředků obvykle spravuje prostředky clusteru rozšíří zatížení (reprezentované prostřednictvím [metrik](service-fabric-cluster-resource-manager-metrics.md)) rovnoměrně v celém clusteru. Service Fabric spravuje kapacitu uzlů v clusteru a clusteru jako celku přes [kapacitu](service-fabric-cluster-resource-manager-cluster-description.md). Metriky a kapacita fungují skvěle pro mnoho úloh, ale vzory, které výrazně využívají různé Service Fabric instance aplikace, někdy přinášejí další požadavky. Můžete například potřebovat:

- Vyhradit u uzlů v clusteru určitou kapacitu pro služby v rámci některé pojmenované instance aplikace
- Omezí celkový počet uzlů, na kterých běží služby v pojmenované instanci aplikace (místo jejich rozprostření přes celý cluster).
- Definujte kapacity na samotné instanci pojmenované aplikace, abyste omezili počet služeb nebo celkovou spotřebu prostředků služeb uvnitř ní.

Aby bylo možné tyto požadavky splnit, Cluster Service Fabric Správce prostředků podporuje funkci nazvanou skupiny aplikací.

## <a name="limiting-the-maximum-number-of-nodes"></a>Omezení maximálního počtu uzlů
Nejjednodušší případ použití pro kapacitu aplikace je v případě, že instance aplikace musí být omezena na určitý maximální počet uzlů. Tím se všechny služby v rámci této instance aplikace konsolidují do nastaveného počtu počítačů. Konsolidace je užitečná, když se pokoušíte odhadnout nebo Cap fyzické prostředky využívají služby v rámci této pojmenované instance aplikace. 

Následující obrázek ukazuje instanci aplikace s maximálním počtem definovaných uzlů a bez nich:

<center>

![Instance aplikace definující maximální počet uzlů][Image1]
</center>

V levém příkladu nemá aplikace definováno maximální počet uzlů a má tři služby. Správce prostředků clusteru rozšíří všechny repliky ve šesti dostupných uzlech, aby se dosáhlo nejlepšího zůstatku v clusteru (výchozí chování). V pravém příkladu uvidíme stejnou aplikaci, která je omezená na tři uzly.

Parametr, který řídí toto chování, se nazývá MaximumNodes. Tento parametr lze nastavit během vytváření aplikace nebo aktualizovaný pro instanci aplikace, která již byla spuštěna.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –ApplicationName fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
await fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
await fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

```

V rámci sady uzlů Správce prostředků clusteru nezaručuje, které objekty služby se mají umístit dohromady nebo které uzly se používají.

## <a name="application-metrics-load-and-capacity"></a>Metriky, zatížení a kapacita aplikace
Skupiny aplikací také umožňují definovat metriky přidružené k dané pojmenované instanci aplikace a kapacitu instance aplikace pro tyto metriky. Metriky aplikací umožňují sledovat, vyhradit a omezit spotřebu prostředků služeb uvnitř dané instance aplikace.

U každé metriky aplikace můžete nastavit dvě hodnoty:

- **Celková kapacita aplikace** – toto nastavení představuje celkovou kapacitu aplikace pro určitou metriku. Cluster Správce prostředků nepovoluje vytváření jakýchkoli nových služeb v rámci této instance aplikace, což by způsobilo, že celkové zatížení tuto hodnotu překročí. Řekněme například, že instance aplikace měla kapacitu 10 a již byla načtena pět. Vytváření služby s celkovým výchozím zatížením 10 se nepovoluje.
- **Maximální kapacita uzlu** – toto nastavení určuje maximální celkové zatížení aplikace na jednom uzlu. Pokud zatížení přechází z této kapacity, cluster Správce prostředků přesune repliky do jiných uzlů, aby se zátěž snižuje.


Prostředí

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -Metrics @("MetricName:Metric1,MaximumNodeCapacity:100,MaximumApplicationCapacity:1000")
```

C#:

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;
appMetric.MaximumNodeCapacity = 100;
ad.Metrics.Add(appMetric);
await fc.ApplicationManager.CreateApplicationAsync(ad);
```

## <a name="reserving-capacity"></a>Rezervace kapacity
Dalším běžným použitím pro skupiny aplikací je zajistit, aby prostředky v rámci clusteru byly rezervované pro danou instanci aplikace. Místo je vždy rezervované při vytvoření instance aplikace.

Rezervace místa v clusteru pro aplikaci proběhne okamžitě, i když:
- instance aplikace je vytvořená, ale ještě nemá žádné služby.
- počet služeb v instanci aplikace se mění pokaždé, když 
- služby existují, ale nespotřebovávají prostředky. 

Rezervace prostředků pro instanci aplikace vyžaduje zadání dvou dalších parametrů: *MinimumNodes* a *NodeReservationCapacity* .

- **MinimumNodes** – definuje minimální počet uzlů, ve kterých by instance aplikace měla běžet.  
- **NodeReservationCapacity** – toto nastavení je na metriku pro aplikaci. Hodnota je množství metriky rezervované pro aplikaci na jakémkoli uzlu, ve kterém je spuštěná služba v dané aplikaci.

Kombinování **MinimumNodes** a **NodeReservationCapacity** garantuje minimální rezervaci zatížení pro aplikaci v rámci clusteru. Pokud je v clusteru méně zbývající kapacita než celková požadovaná rezervace, vytvoření aplikace se nezdařilo. 

Pojďme se podívat na příklad rezervace kapacity:

<center>

![Instance aplikací definující rezervovanou kapacitu][Image2]
</center>

V levém příkladu nemají aplikace definovanou žádnou kapacitu aplikace. Cluster Správce prostředků vyrovnává všechny závislosti na běžných pravidlech.

V příkladu na pravé straně řekněme, že byl vytvořen Application1 s následujícím nastavením:

- MinimumNodes nastavené na dvě
- Metrika aplikace definovaná s
  - NodeReservationCapacity o 20

PowerShell

 ``` posh
 New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MinimumNodes 2 -Metrics @("MetricName:Metric1,NodeReservationCapacity:20")
 ```

C#

 ``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MinimumNodes = 2;

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.NodeReservationCapacity = 20;

ad.Metrics.Add(appMetric);

await fc.ApplicationManager.CreateApplicationAsync(ad);
```

Service Fabric rezervuje kapacitu na dvou uzlech pro Application1 a nepovoluje službám z Application2 využívání této kapacity, i když služby uvnitř Application1 v daném čase nespotřebovává žádné zatížení. Tato Rezervovaná kapacita aplikace se považuje za spotřebované a počítá se se zbývající kapacitou v tomto uzlu a v rámci clusteru.  Rezervace se od zbývající kapacity clusteru odečte okamžitě, ale rezervovaná spotřeba se odečte od kapacity konkrétního uzlu jenom v případě, že je na ni umístěný aspoň jeden objekt služby. Tato rezervace umožňuje flexibilitu a lepší využití prostředků, protože prostředky jsou v případě potřeby rezervovány pouze v uzlech.

## <a name="obtaining-the-application-load-information"></a>Získání informací o načtení aplikace
Pro každou aplikaci, která má kapacitu aplikace definovanou pro jednu nebo více metrik, můžete získat informace o agregovaném zatížení hlášené replikami svých služeb.

Prostředí

``` posh
Get-ServiceFabricApplicationLoadInformation –ApplicationName fabric:/MyApplication1
```

C#

``` csharp
var v = await fc.QueryManager.GetApplicationLoadInformationAsync("fabric:/MyApplication1");
var metrics = v.ApplicationLoadMetricInformation;
foreach (ApplicationLoadMetricInformation metric in metrics)
{
    Console.WriteLine(metric.ApplicationCapacity);  //total capacity for this metric in this application instance
    Console.WriteLine(metric.ReservationCapacity);  //reserved capacity for this metric in this application instance
    Console.WriteLine(metric.ApplicationLoad);  //current load for this metric in this application instance
}
```

Dotaz ApplicationLoad vrací základní informace o kapacitě aplikace, která byla zadána pro aplikaci. Tyto informace zahrnují minimální informace o uzlech a maximálních uzlech a číslo, které aktuálně aplikace zabírá. Obsahuje také informace o každé metrikě zatížení aplikace, včetně:

* Název metriky: název metriky.
* Kapacita rezervace: kapacita clusteru, která je v clusteru vyhrazena pro tuto aplikaci.
* Zatížení aplikace: celkové načtení podřízených replik této aplikace.
* Kapacita aplikace: maximální povolená hodnota zatížení aplikace.

## <a name="removing-application-capacity"></a>Odebírá se kapacita aplikace.
Jakmile jsou parametry kapacity aplikace nastavené pro aplikaci, dají se odebrat pomocí rozhraní Update API aplikace nebo rutin PowerShellu. Například:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Tento příkaz odebere všechny parametry správy kapacity aplikace z instance aplikace. To zahrnuje MinimumNodes, MaximumNodes a metriky aplikace, pokud existují. Účinek příkazu je okamžitý. Po dokončení tohoto příkazu cluster Správce prostředků použije výchozí chování pro správu aplikací. Parametry kapacity aplikace lze zadat znovu prostřednictvím `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()` .

### <a name="restrictions-on-application-capacity"></a>Omezení pro kapacitu aplikace
Existují několik omezení pro parametry kapacity aplikace, které musí být respektovány. Pokud dojde k chybám ověření, probíhají žádné změny.

- Všechny celočíselné parametry musí být nezáporná čísla.
- MinimumNodes nesmí být větší než MaximumNodes.
- Pokud jsou definované kapacity pro metriku zatížení, musí dodržovat tato pravidla:
  - Kapacita rezervovaného uzlu nesmí být větší než maximální kapacita uzlu. Například nemůžete omezit kapacitu metriky "CPU" na uzlu na dvě jednotky a zkusit na každém uzlu rezervovat tři jednotky.
  - Pokud zadáte MaximumNodes, pak produkt MaximumNodes a maximální kapacitu uzlu nesmí být větší než celková kapacita aplikace. Řekněme například, že maximální kapacita uzlu pro metriku zatížení CPU je nastavená na osm. Řekněme také, že jste nastavili maximum uzlů na hodnotu 10. V tomto případě musí být celková kapacita aplikace větší než 80 pro tuto metriku zatížení.

Tato omezení se vynutila během vytváření a aktualizace aplikací.

## <a name="how-not-to-use-application-capacity"></a>Jak používat kapacitu aplikace
- Nepokoušejte se používat funkce skupin aplikací k omezení aplikace na _určitou_ podmnožinu uzlů. Jinými slovy můžete určit, že aplikace běží na nejvíce pěti uzlech, ale ne na konkrétní pět uzlů v clusteru. Omezení aplikace na konkrétní uzly lze dosáhnout pomocí omezení umístění pro služby.
- Nepokoušejte se použít kapacitu aplikace, abyste se ujistili, že dvě služby ze stejné aplikace jsou umístěné na stejných uzlech. Místo toho použijte omezení [spřažení](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) nebo [umístění](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Další kroky
- Další informace o konfiguraci služeb najdete v informacích [o konfiguraci služeb](service-fabric-cluster-resource-manager-configure-services.md) .
- Informace o tom, jak cluster Správce prostředků spravuje a vyrovnává zatížení v clusteru, najdete v článku o [Vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md) .
- Začněte od začátku a [Získejte Úvod do clusteru Service Fabric správce prostředků](service-fabric-cluster-resource-manager-introduction.md)
- Další informace o tom, jak metriky fungují obecně, najdete v [Service Fabric metriky zatížení](service-fabric-cluster-resource-manager-metrics.md) .
- Správce prostředků clusteru má mnoho možností pro popis clusteru. Pokud se o nich chcete dozvědět víc, přečtěte si článek [popisující Service Fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md) .

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

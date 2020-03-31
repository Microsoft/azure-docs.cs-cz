---
title: Správce prostředků clusteru prostředků infrastruktury clusteru služby – skupiny aplikací
description: Přehled funkce Skupiny aplikací ve Správci prostředků clusteru Service Fabric
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 988c7ce52125800c16aa785d5b1458604a927ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452156"
---
# <a name="introduction-to-application-groups"></a>Úvod do skupin aplikací
Správce prostředků clusteru Service Fabric obvykle spravuje prostředky clusteru rozložením zatížení (reprezentované prostřednictvím [metrik](service-fabric-cluster-resource-manager-metrics.md)) rovnoměrně v celém clusteru. Service Fabric spravuje kapacitu uzlů v clusteru a clusteru jako celku prostřednictvím [kapacity](service-fabric-cluster-resource-manager-cluster-description.md). Metriky a kapacita fungují skvěle pro mnoho úloh, ale vzory, které velmi využívají různé instance aplikací Service Fabric, někdy přinášejí další požadavky. Například můžete chtít:

- Rezervovat určitou kapacitu na uzlech v clusteru pro služby v rámci některé instance pojmenované aplikace
- Omezte celkový počet uzlů, na kterých jsou spuštěny služby v rámci instance pojmenované aplikace (namísto jejich rozložení v celém clusteru)
- Definujte kapacity samotné instance pojmenované aplikace, abyste omezili počet služeb nebo celkovou spotřebu prostředků služeb uvnitř aplikace.

Ke splnění těchto požadavků podporuje Správce prostředků clusteru Service Fabric funkci nazvanou Skupiny aplikací.

## <a name="limiting-the-maximum-number-of-nodes"></a>Omezení maximálního počtu uzlů
Nejjednodušší případ použití pro kapacitu aplikace je, když instance aplikace musí být omezena na určitý maximální počet uzlů. Tím se konsoliduje všechny služby v rámci této instance aplikace na nastavený počet počítačů. Konsolidace je užitečná, když se pokoušíte předpovědět nebo zastropit využití fyzických prostředků službami v rámci této instance pojmenované aplikace. 

Následující obrázek znázorňuje instanci aplikace s a bez definovaného maximálního počtu uzlů:

<center>

![Instance aplikace definující maximální počet uzlů][Image1]
</center>

V levém příkladu aplikace nemá maximální počet uzlů definované a má tři služby. Správce prostředků clusteru rozložil všechny repliky do šesti dostupných uzlů, aby dosáhl nejlepší rovnováhy v clusteru (výchozí chování). V pravém příkladu vidíme stejnou aplikaci omezenou na tři uzly.

Parametr, který řídí toto chování se nazývá MaximumNodes. Tento parametr lze nastavit během vytváření aplikace nebo aktualizovat pro instanci aplikace, která již byla spuštěna.

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

V rámci sady uzlů Správce prostředků clusteru nezaručuje, které objekty služby jsou umístěny společně nebo které uzly se používají.

## <a name="application-metrics-load-and-capacity"></a>Metriky aplikace, zatížení a kapacita
Skupiny aplikací také umožňují definovat metriky přidružené k dané instance pojmenované aplikace a kapacitu instance aplikace pro tyto metriky. Metriky aplikace umožňují sledovat, rezervovat a omezit spotřebu prostředků služeb uvnitř této instance aplikace.

Pro každou metriku aplikace existují dvě hodnoty, které lze nastavit:

- **Celková kapacita aplikace** – toto nastavení představuje celkovou kapacitu aplikace pro konkrétní metriku. Správce prostředků clusteru zakazuje vytváření všech nových služeb v rámci této instance aplikace, které by způsobily, že celkové zatížení překročí tuto hodnotu. Řekněme například, že instance aplikace měla kapacitu 10 a již měla zatížení pět. Vytvoření služby s celkovým výchozím zatížením 10 by bylo zakázáno.
- **Maximální kapacita uzlu** – toto nastavení určuje maximální celkové zatížení aplikace v jednom uzlu. Pokud zatížení přejde přes tuto kapacitu, Správce prostředků clusteru přesune repliky do jiných uzlů tak, aby se zatížení snížilo.


Powershell:

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
Dalším běžným použitím pro skupiny aplikací je zajistit, aby prostředky v rámci clusteru byly vyhrazeny pro danou instanci aplikace. Místo je vždy vyhrazeno při vytvoření instance aplikace.

K rezervaci místa v clusteru pro aplikaci dojde okamžitě i v případě, že:
- instance aplikace je vytvořena, ale ještě v ní nemá žádné služby
- počet služeb v rámci instance aplikace se pokaždé změní 
- služby existují, ale nespotřebovávají zdroje 

Rezervace prostředků pro instanci aplikace vyžaduje zadání dvou dalších parametrů: *MinimumNodes* a *NodeReservationCapacity*

- **MinimumNodes** - Definuje minimální počet uzlů, které by měla být spuštěna instance aplikace.  
- **NodeReservationCapacity** - Toto nastavení je pro aplikaci pro metriku. Hodnota je částka této metriky vyhrazené pro aplikaci na libovolném uzlu, kde jsou spuštěny služby v této aplikaci.

Kombinace **MinimumNodes** a **NodeReservationCapacity** zaručuje minimální rezervaci zatížení pro aplikaci v rámci clusteru. Pokud je v clusteru méně zbývající kapacity, než je celková požadovaná rezervace, vytvoření aplikace se nezdaří. 

Podívejme se na příklad rezervace kapacity:

<center>

![Instance aplikací definující rezervovanou kapacitu][Image2]
</center>

V levém příkladu aplikace nemají definovány žádné kapacity aplikace. Správce prostředků clusteru vyvažuje vše podle běžných pravidel.

V příkladu na pravé straně řekněme, že Application1 byl vytvořen s následujícím nastavením:

- MinimumNodes nastavena na dvě
- Metrika aplikace definovaná
  - NodeReservationKapacita 20

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

Service Fabric rezervuje kapacitu na dvou uzlech pro Application1 a neumožňuje služby z Application2 využívat tuto kapacitu i v případě, že nejsou žádné zatížení spotřebovává služby uvnitř Application1 v té době. Tato kapacita rezervované aplikace je považována za spotřebovanou a počítá se podle zbývající kapacity v tomto uzlu a v rámci clusteru.  Rezervace je odečtena od zbývající kapacity clusteru okamžitě, ale rezervovaná spotřeba je odečtena z kapacity konkrétního uzlu pouze v případě, že je na něj umístěn alespoň jeden objekt služby. Tato pozdější rezervace umožňuje flexibilitu a lepší využití prostředků, protože prostředky jsou vyhrazeny pouze na uzlech v případě potřeby.

## <a name="obtaining-the-application-load-information"></a>Získání informací o zatížení aplikace
Pro každou aplikaci, která má kapacitu aplikace definovanou pro jednu nebo více metrik, můžete získat informace o agregovaném zatížení hlášeném replikami jejích služeb.

Powershell:

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

Dotaz ApplicationLoad vrátí základní informace o kapacitě aplikace, která byla určena pro aplikaci. Tyto informace zahrnují informace o minimálních uzlech a maximálních uzlech a číslo, které aplikace aktuálně zabírá. Obsahuje také informace o každé metriky zatížení aplikace, včetně:

* Název metriky: Název metriky.
* Kapacita rezervace: Kapacita clusteru, která je vyhrazena v clusteru pro tuto aplikaci.
* Zatížení aplikace: Celkové zatížení podřízených replik této aplikace.
* Kapacita aplikace: Maximální povolená hodnota zatížení aplikace.

## <a name="removing-application-capacity"></a>Odebrání kapacity aplikace
Jakmile jsou parametry kapacity aplikace nastaveny pro aplikaci, lze je odebrat pomocí rozhraní API pro aktualizaci aplikací nebo rutin prostředí PowerShell. Například:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Tento příkaz odebere všechny parametry správy kapacity aplikace z instance aplikace. To zahrnuje MinimumNodes, MaximumNodes a metriky aplikace, pokud existuje. Účinek příkazu je okamžitý. Po dokončení tohoto příkazu použije Správce prostředků clusteru výchozí chování pro správu aplikací. Parametry kapacity aplikace lze `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`zadat znovu pomocí aplikace .

### <a name="restrictions-on-application-capacity"></a>Omezení kapacity aplikací
Existuje několik omezení parametrů kapacity aplikace, které musí být respektovány. Pokud dojde k chybám ověření, nedojde k žádným změnám.

- Všechny celočíselné parametry musí být nezáporná čísla.
- MinimumNodes nesmí být nikdy větší než MaximumNodes.
- Pokud jsou definovány kapacity pro metriku zatížení, musí se řídit následujícími pravidly:
  - Kapacita rezervace uzlu nesmí být větší než maximální kapacita uzlu. Například nelze omezit kapacitu pro metriku "CPU" v uzlu na dvě jednotky a pokusit se rezervovat tři jednotky na každém uzlu.
  - Pokud maximumNodes je zadán, pak součin MaximumNodes a Maximální kapacita uzlu nesmí být větší než celková kapacita aplikace. Řekněme například, že maximální kapacita uzlu pro metriku zatížení "CPU" je nastavena na osm. Řekněme také, že nastavíte maximální uzly na 10. V tomto případě musí být celková kapacita aplikace větší než 80 pro tuto metriku zatížení.

Omezení jsou vynucena při vytváření a aktualizacích aplikace.

## <a name="how-not-to-use-application-capacity"></a>Jak nepoužívat kapacitu aplikace
- Nepokoušejte se použít funkce Skupiny aplikací k omezení aplikace na _určitou_ podmnožinu uzlů. Jinými slovy můžete určit, že aplikace běží na maximálně pět uzlů, ale ne, které konkrétní pět uzlů v clusteru. Omezení aplikace na konkrétní uzly lze dosáhnout pomocí omezení umístění pro služby.
- Nepokoušejte se použít kapacitu aplikace k zajištění, že dvě služby ze stejné aplikace jsou umístěny na stejné uzly. Místo toho použijte omezení [spřažení](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) nebo [umístění](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Další kroky
- Další informace o konfiguraci služeb [naleznete v informacích o konfiguraci služeb](service-fabric-cluster-resource-manager-configure-services.md)
- Informace o tom, jak Správce prostředků clusteru spravuje a vyrovnává zatížení clusteru, naleznete v článku o [vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md)
- Začněte od začátku a [získejte úvod do Správce prostředků clusteru Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- Další informace o tom, jak metriky fungují obecně, načtěte na [Service Fabric Load Metriky](service-fabric-cluster-resource-manager-metrics.md)
- Správce prostředků clusteru má mnoho možností pro popis clusteru. Další informace o nich naleznete v tomto článku [popisujícím cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

---
title: Service Fabric Cluster Resource Manager – skupin aplikací | Dokumentace Microsoftu
description: Přehled funkcí skupiny aplikací v Service Fabric Cluster Resource Manager
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 2c641703547c391618d75fabfa181dff0b98f74f
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918766"
---
# <a name="introduction-to-application-groups"></a>Úvod do skupiny aplikací
Service Fabric Cluster Resource Manageru obvykle spravuje prostředky clusteru rozložení zátěže (reprezentované prostřednictvím [metriky](service-fabric-cluster-resource-manager-metrics.md)) rovnoměrně v celém clusteru. Service Fabric spravuje kapacity uzlů v clusteru a clusteru jako celek prostřednictvím [kapacity](service-fabric-cluster-resource-manager-cluster-description.md). Metriky a kapacitu fungují velmi vhodné pro mnoho úloh, ale vzorce, které se hojně používají různé instance aplikace Service Fabric v některých případech umožňuje přinést si další požadavky. Například můžete chtít:

- Rezervovat pro služby v rámci některé instance s názvem aplikace nějakým na uzlech v clusteru
- Omezit celkový počet uzlů, které běží v rámci instance pojmenované aplikace na (místo aby rozložil je navýšení kapacity za celý cluster)
- Definovat kapacity na samotné lze omezit, služeb nebo celkový počet spotřebovaných služeb dovnitř instanci s názvem aplikace

Budou odpovídat vašim požadavkům, Service Fabric Cluster Resource Manager podporuje funkci s názvem skupiny aplikací.

## <a name="limiting-the-maximum-number-of-nodes"></a>Omezení maximální počet uzlů
Nejjednodušší případ použití pro kapacity aplikace při instance aplikace musí být omezené na určité maximální počet uzlů. To spojuje všechny služby v rámci této instance aplikace na stanovený počet počítačů. Konsolidace je užitečné, když se snažíte předpovídat nebo limit fyzický prostředek používá prostřednictvím služeb v rámci této instance s názvem aplikace. 

Instance aplikace a nemusíte maximální počet uzlů, které jsou definované na následujícím obrázku:

<center>
![Instance aplikace definuje maximální počet uzlů][Image1]
</center>

V levém příkladu aplikace nemá maximální počet uzlů, které jsou definovány a má tři služby. Cluster Resource Manager má rozprostřete všech replik napříč šesti dostupné uzly k dosažení nejlepší poměr v clusteru (výchozí chování). V pravém příkladu vidíme stejnou aplikaci omezené na tři uzly.

Parametr, který určuje toto chování je volat MaximumNodes. Tento parametr lze nastavit během vytváření aplikace nebo aktualizovat pro instanci aplikace, která je již spuštěna.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
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

V rámci sady uzlů Cluster Resource Manager nezaručuje objektů, které služba umístěny společně nebo získat uzlů, které používá.

## <a name="application-metrics-load-and-capacity"></a>Metriky aplikací, načítání a kapacita
Skupiny aplikací také umožňují definovat metriky, které jsou přidružené k dané aplikaci s názvem instance a kapacitu této instance aplikace pro tyto metriky. Metriky aplikací umožňují sledovat, rezervovat a omezit spotřebu prostředků služeb v této instanci aplikace.

Pro jednotlivé aplikace metriky jsou dvě hodnoty, které je možné nastavit:

- **Celková kapacita aplikace** – toto nastavení představuje celkové kapacity aplikace konkrétní metriky. Cluster Resource Manager nepovoluje vytvoření jakékoli nové služby v rámci této instance aplikace, která může způsobit celkové zatížení překročí tuto hodnotu. Řekněme například, instance aplikace měli kapacitou 10 a již má pět zatížení. Vytvoření služby s výchozí celkové zatížení 10 bude zakázáno.
- **Maximální kapacita uzlu** – toto nastavení určuje maximální celkové zatížení aplikace v jednom uzlu. Zatížení překročí tuto kapacitu, přesune Cluster Resource Manager repliky na jiné uzly tak, aby zatížení sníží.


Prostředí PowerShell:

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

## <a name="reserving-capacity"></a>Rezervaci kapacity
Dalším běžným způsobem použití u skupiny aplikací je zajistit, že prostředky v rámci clusteru jsou vyhrazené pro instance dané aplikace. Místo je vyhrazený vždy při vytvoření instance aplikace.

Rezervování prostoru v clusteru pro aplikace se okamžitě stane i v případě:
- instance aplikace se vytvoří, ale ještě nemá žádné služby v rámci něj
- počet služeb v rámci instance aplikace změní pokaždé, když 
- služby existuje, ale nejsou využívající tyto prostředky 

Rezervování prostředků pro instanci aplikace vyžaduje zadání dvou dalších parametrů: *MinimumNodes* a *NodeReservationCapacity*

- **MinimumNodes** – Určuje minimální počet uzlů, které by se spustit na instanci aplikace.  
- **NodeReservationCapacity** – toto nastavení je na metriku pro aplikaci. Hodnota je množství tuto metriku vyhrazena pro aplikaci ve všech uzlech kde spuštění služby v dané aplikaci.

Kombinování **MinimumNodes** a **NodeReservationCapacity** zaručuje rezervace minimální zatížení pro aplikaci v rámci clusteru. Pokud má menší zbývající kapacity v clusteru než celkový počet rezervací vyžaduje, vytvoření aplikace se nezdaří. 

Podívejme se na příklad rezervaci kapacity:

<center>
![Definování záložní kapacitu instancí aplikace][Image2]
</center>

V levém příklad aplikací není nutné žádné kapacity aplikace definované. Cluster Resource Manager vyrovnává všechno, co podle obvyklých pravidel.

V příkladu na pravé straně Řekněme, že Application1 byl vytvořen s následujícími nastaveními:

- MinimumNodes nastavena na dvě
- Metrika definovaná pomocí aplikace
  - NodeReservationCapacity 20

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

Service Fabric rezervuje kapacitu na dvou uzlech pro Application1 a neumožňuje z Application2 i v případě, že nejsou že žádné zatížení spotřebovává služby uvnitř Application1 v době využívat kapacitu této služby. Tato kapacita vyhrazená aplikace se považuje za spotřebované a počítat zbývající kapacity v tomto uzlu a v rámci clusteru.  Rezervace se odečte od zbývající kapacita clusteru okamžitě, ale využití rezervovaných je odečtena od objemu konkrétním uzlu pouze v případě, že objekt nejméně jedné služby se umístí na něm. Tato vyšší rezervace umožňuje flexibilitu a lepší využití prostředků od prostředků jsou vyhrazeny pouze na uzlech v případě potřeby.

## <a name="obtaining-the-application-load-information"></a>Získávání informací o zatížení aplikace
Pro každou aplikaci, která má kapacity aplikace definované pro jednu nebo víc metrik, které můžete získat informace o agregované zatížení hlášených repliky z jejích služeb.

Prostředí PowerShell:

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

Vrátí dotaz ApplicationLoad základní informace o kapacitě aplikace, který byl zadán pro aplikaci. Tyto informace zahrnují informace, které uzly minimální a maximální počet uzlů a číslo, které aplikace je aktuálně zabírá. Obsahuje také informace o jednotlivých metrika zatížení aplikace, včetně:

* Název metriky: Název metriky.
* Rezervované kapacity: Kapacita clusteru, který je vyhrazen v clusteru pro tuto aplikaci.
* Zatížení aplikace: Celkové zatížení replik podřízené této aplikace.
* Kapacity aplikace: Maximální povolená hodnota zatížení aplikace.

## <a name="removing-application-capacity"></a>Odebrání kapacity aplikace
Po parametrech kapacity aplikace nastavené pro aplikaci, lze je odstranit pomocí rutiny Powershellu ani rozhraní API aktualizace aplikace. Příklad:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Tento příkaz odebere všechny parametry správy kapacity aplikace z instance aplikace. To zahrnuje MinimumNodes MaximumNodes a metriky vaší aplikace, pokud existuje. Efekt příkazu se okamžitě. Po dokončení tohoto příkazu Cluster Resource Manager použije výchozí chování pro správu aplikací. Parametry kapacity aplikace se dá nastavit znovu prostřednictvím `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`.

### <a name="restrictions-on-application-capacity"></a>Omezení kapacity aplikace
Existuje několik omezení kapacity aplikace parametry, které musí být zachovány. Pokud došlo k chybám při ověření žádné změny provést.

- Všechny celočíselné parametry musí být záporná čísla.
- MinimumNodes musí být nikdy delší než MaximumNodes.
- Pokud jsou definovány pro zatížení metriku kapacity, se musí dodržovat tato pravidla:
  - Kapacita uzlu rezervace nesmí být větší než maximální kapacita uzlu. Nelze například omezení kapacity pro metriku "CPU" na uzel, který má dvě jednotky a zkuste to rezervovat tři jednotky na každém uzlu.
  - Je zadán MaximumNodes, produktu MaximumNodes a maximální kapacitu uzlu nesmí být větší než celkové kapacity aplikace. Řekněme například, maximální kapacitu uzlu "CPU" je nastavena na osm metriky zatížení. Vezměme si také třeba že nastavit maximální počet uzlů na 10. Celkové kapacity aplikace v tomto případě musí být větší než 80 pro tato metrika zatížení.

Omezení se vynucují, jak při vytváření aplikací a aktualizací.

## <a name="how-not-to-use-application-capacity"></a>Jak nepoužívat kapacity aplikace
- Nepokoušejte se použít funkce skupiny aplikací k omezení aplikace _konkrétní_ dílčí sadu uzlů. Jinými slovy, můžete určit, že aplikace bude spuštěna na nejvíce pět uzlů, ale ne konkrétní pět uzlů, které v clusteru. Omezení aplikace ke konkrétním uzlům lze dosáhnout pomocí omezení umístění služby.
- Nepokoušejte se použít k zajištění, že dvě služby ze stejné aplikace jsou umístěné na stejné uzly kapacity aplikace. Místo toho použijte [spřažení](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) nebo [omezení umístění](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Další postup
- Další informace o konfiguraci služby [informace o konfiguraci služby](service-fabric-cluster-resource-manager-configure-services.md)
- Přečtěte si o tom, jak Cluster Resource Manager spravuje a vyrovnává zatížení v clusteru, přečtěte si článek na [Vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md)
- Začít od začátku a [Úvod do Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Další informace o fungování metriky obecně, přečtěte si [metriky zatížení Service Fabric](service-fabric-cluster-resource-manager-metrics.md)
- Cluster Resource Manager má mnoho možností pro popis clusteru. Další informace o nich najdete v tomto článku na [popisující cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

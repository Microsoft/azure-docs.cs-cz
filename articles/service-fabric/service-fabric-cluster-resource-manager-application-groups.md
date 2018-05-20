---
title: Správce prostředků clusteru služby Fabric - skupin aplikací | Microsoft Docs
description: Přehled funkcí skupiny aplikací na portálu Service Fabric clusteru Resource Manager
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
ms.openlocfilehash: 215efc1f0597f5199dd37baf4b109d7e76040aae
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="introduction-to-application-groups"></a>Úvod do skupiny aplikací
Správce prostředků clusteru Service Fabric obvykle spravuje prostředky clusteru tak, že se zatížení (reprezentované prostřednictvím [metriky](service-fabric-cluster-resource-manager-metrics.md)) rovnoměrně v rámci clusteru. Service Fabric spravuje kapacitu uzly v clusteru a clusteru jako celek prostřednictvím [kapacity](service-fabric-cluster-resource-manager-cluster-description.md). Metriky a kapacity pracovní velká pro řadu úloh, ale vzorů, které hodně využívají různé instance aplikace Service Fabric někdy předány další požadavky. Například můžete chtít:

- Záložní některé kapacita v uzlech v clusteru pro služby v rámci některé instance s názvem aplikace
- Omezit celkový počet uzlů, které služby v rámci instance s názvem aplikace běží na (namísto šíří je po celý cluster)
- Definování kapacity v samotné omezit počet služeb nebo spotřeby celkový počet prostředků služeb je uvnitř instanci s názvem aplikace

Pro splnění těchto požadavků, správce prostředků clusteru Service Fabric podporuje funkci skupiny aplikací.

## <a name="limiting-the-maximum-number-of-nodes"></a>Omezení maximální počet uzlů
Nejjednodušší případ použití kapacity aplikace je při instanci aplikace musí být omezeno na určité maximální počet uzlů. Tím dojde ke konsolidaci všechny služby v rámci této instance aplikace na se stanoveným počtem počítačů. Konsolidace je užitečné, když se pokoušíte předpovědi nebo cap využití fyzických prostředků prostřednictvím služeb v rámci dané aplikace s názvem instance. 

Následující obrázek znázorňuje instance aplikace s i bez maximální počet uzlů, které jsou definované:

<center>
![Instance aplikace definování maximální počet uzlů][Image1]
</center>

V levém příkladu aplikace nemá maximální počet uzlů, které jsou definované a má tři služby. Správce prostředků clusteru má na všech replik rozloženy šesti dostupných uzlů k dosažení nejlepší vyrovnávání v clusteru (výchozí nastavení). V pravém příkladu vidíte stejnou aplikaci omezena na tři uzly.

Parametr, který určuje toto chování se nazývá MaximumNodes. Tento parametr můžete nastavit při vytváření aplikace, nebo aktualizovat pro instanci aplikace, která je již spuštěna.

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

V rámci sada uzlů správce prostředků clusteru není zárukou toho, jaké objekty služby získat umístit společně nebo získat uzlů, které používá.

## <a name="application-metrics-load-and-capacity"></a>Aplikace metriky zatížení a kapacity
Skupiny aplikací, které umožňují definovat metriky, které jsou přidružené k dané aplikaci s názvem instance a tuto instanci aplikace kapacity pro tyto metriky. Metriky aplikace vám umožňují sledovat, rezervace a omezit spotřeby prostředků služeb v této instanci aplikace.

Pro jednotlivé aplikace metriky existují dvě hodnoty, které lze nastavit:

- **Celková kapacita aplikace** – toto nastavení představuje celkovou kapacitu aplikace pro konkrétní metriky. Správce prostředků clusteru zakáže vytváření všech nových služeb v rámci této instance aplikace, které by způsobily celkové zatížení překročí tuto hodnotu. Řekněme například, instance aplikace měla kapacitou 10 a již obsahuje pět zatížení. Vytvoření služby se zatížením celkový výchozí 10 by povoleny.
- **Maximální kapacita uzlu** – toto nastavení určuje maximální celkové zatížení pro danou aplikaci na jednom uzlu. Pokud zatížení prochází přes tuto kapacitu, správce prostředků clusteru přesune repliky do dalších uzlů, tak, aby snížení zatížení.


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
Jiné běžně používá pro skupiny aplikací, které je potřeba zajistit, že prostředky v rámci clusteru jsou vyhrazené pro instanci dané aplikaci. Místo je vyhrazená vždy při vytvoření instance aplikace.

Pro aplikace, se stane okamžitě vyhrazením místa v clusteru i v případě:
- instance aplikace je vytvořena, ale nemá žádné služby, v něm ještě
- počet služeb v rámci instance aplikace na změní pokaždé, když 
- služby existuje, ale nejsou využívání prostředků 

Rezervování prostředků pro instanci aplikace vyžaduje zadání další dva parametry: *MinimumNodes* a *NodeReservationCapacity*

- **MinimumNodes** -Určuje minimální počet uzlů, které by neměl být spuštěný instanci aplikace.  
- **NodeReservationCapacity** – toto nastavení je za Metrika pro aplikaci. Hodnota je velikost této metrika vyhrazené pro aplikace v každém uzlu kde, spouštění služeb v této aplikaci.

Kombinování **MinimumNodes** a **NodeReservationCapacity** zaručuje rezervace minimální zatížení pro danou aplikaci v rámci clusteru. Pokud existuje méně zbývající kapacity v clusteru než celkový rezervace vyžaduje, vytvoření aplikace se nezdaří. 

Podívejme se na příklad rezervaci kapacity:

<center>
![Instance aplikace definování rezervované kapacity][Image2]
</center>

V levém příkladu aplikace nemají žádné aplikace kapacity definované. Správce prostředků clusteru vyrovnává všechno podle běžných pravidel.

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

Service Fabric rezervuje kapacitu na dva uzly pro Application1 a neumožňuje služby z Application2 využívat tuto kapacitu, i když nejsou že žádné zatížení spotřebovává služby uvnitř Application1 v době. Tato vyhrazená aplikace kapacita považuje za spotřebované a započítává zbývající kapacity v tomto uzlu a v rámci clusteru.  Rezervace bude odečtena z zbývající kapacity clusteru okamžitě, ale odečtením vyhrazené spotřeby z kapacity konkrétním uzlu jenom v případě, že je alespoň jedna služba objekt je umístěn na něm. Tento novější rezervace umožňuje flexibilitu a lepší využití prostředků vzhledem k tomu, že prostředky jsou vyhrazeny pouze na uzlech v případě potřeby.

## <a name="obtaining-the-application-load-information"></a>Získávání informací o zatížení aplikace
Pro každou aplikaci, která má kapacitu aplikace definovaná pro jeden nebo více metriky, které můžete získat informace o agregační zatížení hlášené repliky jeho služby.

Prostředí PowerShell:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1
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

ApplicationLoad dotaz vrátí základní informace o kapacitě aplikace, která byla zadaná pro aplikaci. Tyto informace zahrnují informace, které uzly minimální a maximální počet uzlů a číslo, který je aktuálně zabírá aplikace. Zahrnuje taky informace o jednotlivých metrika zatížení aplikací, včetně:

* Název metriky: Název metriky.
* Rezervaci kapacity: Kapacity clusteru, která je vyhrazena v clusteru pro tuto aplikaci.
* Zatížení aplikací: Celkový počet zatížení replik podřízené této aplikace.
* Aplikace kapacity: Maximální povolená hodnota zatížení aplikace.

## <a name="removing-application-capacity"></a>Odebrání aplikace kapacity
Jakmile aplikace kapacity parametry jsou nastavené pro aplikace, budou se dá odebrat pomocí rozhraní API pro aktualizaci aplikace nebo rutiny Powershellu. Příklad:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Tento příkaz odebere z instance aplikace na všechny aplikace kapacity správy parametry. To zahrnuje MinimumNodes, MaximumNodes a metriky aplikace, pokud existuje. Efekt příkazu se okamžitě. Po dokončení tohoto příkazu, správce prostředků clusteru použije výchozí chování pro správu aplikací. Parametry kapacity aplikace mohou být zadané znovu prostřednictvím `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`.

### <a name="restrictions-on-application-capacity"></a>Omezení kapacity aplikace
Existuje několik omezení kapacity aplikace parametry, které je nutné dodržovat. Pokud nejsou chyby ověření žádné změny proběhnout.

- Všechny parametry celé číslo musí být nezáporné číslo.
- MinimumNodes, nikdy musí být větší než MaximumNodes.
- Pokud jsou definovány kapacity pro metrika zatížení, musí se postupujte tato pravidla:
  - Uzel rezervaci kapacity nesmí být větší než maximální kapacita uzlu. Nelze například omezení kapacity pro metriku "Procesoru" v uzlu a dvě jednotky a akci tak, aby vyhradil tři jednotky na každém uzlu.
  - Pokud je zadán MaximumNodes, nesmí být větší než celková kapacita aplikace produktu MaximumNodes a maximální kapacita uzlu. Předpokládejme například že maximální kapacita uzlu pro metrika zatížení, "Procesor" je nastavena na 8. Také se stát, že nastavíte maximální počet uzlů na 10. V takovém případě celková kapacita aplikace musí být větší než 80 pro tato metrika zatížení.

Omezení se vynucují, jak při vytváření aplikací a aktualizací.

## <a name="how-not-to-use-application-capacity"></a>Jak nepoužívat kapacity aplikace
- Nepokoušejte se použít funkce skupiny aplikací k omezení aplikace _konkrétní_ dílčí sadu uzlů. Jinými slovy, můžete určit, že je aplikace spuštěná na nejvíce pět uzlů, ale není konkrétní pět uzlů, které v clusteru. Chovaly aplikaci ke konkrétní uzly lze dosáhnout pomocí omezení umístění pro služby.
- Nepokoušejte se použít aplikaci kapacitu k zajištění, že dvě služby z stejná aplikace budou umístěny v stejným uzlům. Místo toho použít [spřažení](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) nebo [omezení umístění](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Další postup
- Další informace o konfiguraci služby [Další informace o konfiguraci služby](service-fabric-cluster-resource-manager-configure-services.md)
- Chcete-li zjistit, o tom, jak správce prostředků clusteru spravuje a vyrovnává zatížení v clusteru, podívejte se na článek na [Vyrovnávání zatížení](service-fabric-cluster-resource-manager-balancing.md)
- Začít od začátku a [získejte Úvod do Service Fabric clusteru správce prostředků](service-fabric-cluster-resource-manager-introduction.md)
- Další informace o fungování metriky obecně, přečtěte si [metriky zatížení Service Fabric](service-fabric-cluster-resource-manager-metrics.md)
- Správce prostředků clusteru má mnoho možností pro popis clusteru. Další informace o nich, projděte si tento článek na [popisující cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

---
title: "Model hostování Azure Service Fabric | Microsoft Docs"
description: "Popisuje vztah mezi repliky (nebo instance) nasazené služby Statistika prostředků infrastruktury a hostitele služby procesu."
services: service-fabric
documentationcenter: .net
author: harahma
manager: timlt
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: 0206a9a486e3511834a23b3cc3f20f236a1cc261
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="service-fabric-hosting-model"></a>Model hostování Service Fabric
Tento článek obsahuje přehled hostování modely poskytované Service Fabric aplikace a popisuje rozdíly mezi **sdílený proces** a **výhradní proces** modelů. Popisuje, jak vypadá nasazené aplikace v Service Fabric uzlu a vztah mezi repliky (nebo instance) služby a proces hostitele služby.

Než budete pokračovat, ujistěte se, že jste obeznámeni s [Service Fabric aplikačního modelu] [ a1] a pochopit různé koncepty a vztah mezi nimi. 

> [!NOTE]
> V tomto článku, pro jednoduchost Pokud není výslovně uvedeno:
>
> - Všechny používá aplikace word *repliky* odkazuje na obou repliku stavové služby nebo instance bezstavové služby.
> - *CodePackage* je ošetřených ekvivalent *ServiceHost* proces, který registruje *ServiceType* a hostitele repliky služeb této *ServiceType*.
>

Model hostování pochopit, dejte nám provede příklad. Dejte nám vyslovení máme *ApplicationType* 'MyAppType', který má *ServiceType* 'MyServiceType'.  'MyServiceType' zajišťuje *ServicePackage* 'MyServicePackage', který má *CodePackage* 'MyCodePackage'. Zaregistruje 'MyCodePackage' *ServiceType* 'MyServiceType, když je spuštěna.

Řekněme, že máme tři uzly clusteru a vytvoříme *aplikace* **fabric: / App1** typu 'MyAppType'. Uvnitř to *aplikace* **fabric: / App1** vytvoříme služby **prostředků infrastruktury: / App1/ServiceA** typu 'MyServiceType', která má dva oddíly (vyslovení **P1**   &  **P2**) a tři repliky na jeden oddíl. Následující diagram znázorňuje zobrazení této aplikace, jak se bude nakonec nasazené na uzlu.

<center>
![Zobrazení uzlu nasazené aplikace][node-view-one]
</center>

Service Fabric aktivovat 'MyServicePackage', která spustila 'MyCodePackage', který je hostitelem repliky z obou oddílů.  Například **P1** & **P2**. Všechny uzly v clusteru budou mít stejné zobrazení, protože jsme zvolili počet replik na oddíl, který chcete být roven počtu uzlů v clusteru. Vytvoříme jiné služby **prostředků infrastruktury: / App1/ServiceB** v aplikaci **fabric: / App1**, který má jeden oddíl (vyslovení **P3**) a tři repliky na jeden oddíl. Následující diagram znázorňuje nové zobrazení na uzlu:

<center>
![Zobrazení uzlu nasazené aplikace][node-view-two]
</center>

Jak jsme najdete v části Service Fabric umístit nového serveru repliky pro oddíl **P3** služby **fabric: / App1/ServiceB** v existující aktivace 'MyServicePackage'. Teď umožňuje vytvořit jiné *aplikace* **fabric: / počítači App2** typu 'MyAppType'. Uvnitř **prostředků infrastruktury: / počítači App2**, vytvoření služby **fabric: / počítači App2/ServiceA** který má dva oddíly (vyslovení **P4** & **P5**) a tři repliky na jeden oddíl. Následující diagramy ukazuje nové zobrazení uzlu:

<center>
![Zobrazení uzlu nasazené aplikace][node-view-three]
</center>

Service Fabric aktivuje novou kopii 'MyServicePackage', který spustí novou kopii 'MyCodePackage'. Repliky z obou oddílů služby **fabric: / počítači App2/ServiceA** (například **P4** & **P5**) jsou umístěny v této nové kopie 'MyCodePackage'.

## <a name="shared-process-model"></a>Sdílené model procesu
Co jsme viděli výše je výchozí model poskytované Service Fabric hostování a se označuje jako **sdílený proces** modelu. V tomto modelu pro daný *aplikace*, jenom jeden zkopírovat z dané *ServicePackage* se aktivuje na *uzlu* (které spustí všechny *CodePackages* obsažené v ní) a všechny repliky všechny služby daný *ServiceType* jsou umístěny v *CodePackage* , který registruje *ServiceType*. Jinými slovy, všechny repliky všech služeb v uzlu danou *ServiceType* sdílet stejný postup.

## <a name="exclusive-process-model"></a>Model výhradní procesu
Je hostování model poskytované Service Fabric **výhradní proces** modelu. V tomto modelu na danou *uzlu*, k umístění každou repliku, Service Fabric aktivuje novou kopii *ServicePackage* (které spustí všechny *CodePackages* obsažené v ní) a repliky je umístěn v *CodePackage* které registrované *ServiceType* služby, do které replika patří. Jinými slovy žije každé repliky ve svém vlastním procesu vyhrazené. 

Tento model je podporováno od verze 5.6 Service Fabric. **Výhradní proces** modelu je možné vybrat v době vytváření služby (pomocí [prostředí PowerShell][p1], [REST][r1], nebo [FabricClient][c1]) tak, že zadáte **ServicePackageActivationMode** jako 'ExclusiveProcess'.

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Pokud máte výchozí služba v manifestu aplikace, můžete vybrat **výhradní proces** modelu zadáním **ServicePackageActivationMode** atributu, jak je uvedeno níže:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Budete pokračovat v předchozím příkladu, umožňuje vytvořit jinou službu **prostředků infrastruktury: / App1/ServiceC** v aplikaci **fabric: / App1** který má dva oddíly (vyslovení **P6**  &  **N7**) a tři repliky na oddíl s **ServicePackageActivationMode** nastavena na 'ExclusiveProcess'. Následující diagram znázorňuje nové zobrazení na uzlu:

<center>
![Zobrazení uzlu nasazené aplikace][node-view-four]
</center>

Jak vidíte, Service Fabric aktivovat dva nové kopie 'MyServicePackage' (jeden pro každou repliku z oddílu **P6** & **N7**) a umístit každou repliku v jeho vyhrazené kopie *CodePackage*. Další fakt, který je zde, při **výhradní proces** model se používá, pro daný *aplikace*, více zkopíruje z danou *ServicePackage* může být aktivní v  *Uzel*. Ve výše příkladu vidíme pro aktivní tři kopie 'MyServicePackage' **fabric: / App1**. Každá z těchto aktivní kopie 'MyServicePackage' má **ServicePackageActivationId** související s ji identifikuje tuto kopii v rámci *aplikace* **fabric: / App1**.

Při pouze **sdílený proces** model se používá pro *aplikace*, například **fabric: / počítači App2** v existuje pouze jeden aktivní kopie výše například *ServicePackage*  na *uzlu* a **ServicePackageActivationId** u této aktivace *ServicePackage* je "prázdný řetězec".

> [!NOTE]
>- **Sdílený proces** model hostování odpovídá **ServicePackageActivationMode** rovna **SharedProcess**. Toto je výchozí model hostování a **ServicePackageActivationMode** není nutné zadávat při vytváření služby.
>
>- **Výhradní proces** model hostování odpovídá **ServicePackageActivationMode** nastavena na **ExclusiveProcess** a je třeba explicitně zadat při vytváření služby. 
>
>- Model hostování služby může být známé pomocí dotazu [popisu služby] [ p2] a prohlížení hodnotu **ServicePackageActivationMode**.
>
>

## <a name="working-with-deployed-service-package"></a>Práce s nasazený balíček služby
Aktivní kopie *ServicePackage* na uzlu se označuje jako [nasazený balíček služby][p3]. Jak už jsme zmínili, když **výhradní proces** model se používá pro vytváření služeb, pro daný *aplikace*, může být víc balíčků nasazené služby pro stejné  *ServicePackage*. Při provádění operací, které jsou specifické pro službu nasazenou balíčku (například [vytváření sestav stavu balíčku nasazené služby] [ p4] nebo [restartování balíček kódu nasazené služby balíček][p5]), **ServicePackageActivationId** je třeba zadat identifikovat konkrétní nasazený balíček služby.

**ServicePackageActivationId** nasazené služby balíček lze získat pomocí dotazu na seznam [nasazené balíčky služeb] [ p3] na uzlu. Při dotazování [nasazené typů služeb][p6], [nasazení repliky][p7], a [nasadit balíčky kódu] [ p8] na uzlu, výsledek dotazu obsahuje taky **ServicePackageActivationId** balíčku služby nadřazená nasazená.

> [!NOTE]
>- V části **sdílený proces** model, hostování na daný *uzlu*, pro daný *aplikace*, jenom jeden zkopírovat z *ServicePackage* se aktivuje . Má **ServicePackageActivationId** rovna *prázdný řetězec* a není nutné zadávat při provádění operace související se balíček nasazené služby. 
>
> - V části **výhradní proces** model, hostování na dané *uzlu*, pro daného *aplikace*, jeden nebo více kopií *ServicePackage* může být aktivní. Každá aktivace, má *neprázdný* **ServicePackageActivationId**zadaný při provádění operace související se balíček nasazené služby. 
>
> - Pokud **ServicePackageActivationId** je vynechán, je výchozí hodnota je "prázdný řetězec". Pokud službu nasazenou balíčku, který byl aktivován pod **sdílený proces** modelu je k dispozici, pak se provede operaci na něm, jinak se operace nezdaří.
>
> - Dotazování jednou a mezipaměti **ServicePackageActivationId**, protože se dynamicky vygeneruje a můžete změnit z různých důvodů. Před provedením operace, která potřebuje **ServicePackageActivationId**, musí nejprve dotaz seznam [nasazené balíčky služeb] [ p3] na uzel a pak použít  **ServicePackageActivationId** z výsledku dotazu k provedení operace původní.
>
>

## <a name="guest-executable-and-container-applications"></a>Spustitelný soubor a kontejneru aplikace hosta
Service Fabric zpracovává [spustitelný soubor hosta] [ a2] a [kontejneru] [ a3] aplikace jako bezstavové služby, které jsou samostatné: neexistuje žádné modulu runtime Service Fabric v *ServiceHost* (procesu nebo kontejneru). Vzhledem k tomu, že tyto služby jsou samostatný, počet replik na *ServiceHost* není použitelná pro tyto služby. Nejběžnější konfigurace používají tyto služby je jedním oddílem s [InstanceCount] [ c2] rovno -1 (tj. jednu kopii kódu služby spuštěné v každém uzlu clusteru). 

Výchozí hodnota **ServicePackageActivationMode** pro tyto služby je **SharedProcess**, v takovém případě Service Fabric aktivuje pouze jedné kopie *ServicePackage* na  *Uzel* pro danou *aplikace*.  To znamená, že bude spuštěna pouze jedna kopie kódu služby *uzlu*. Pokud chcete, aby více kopií kódu služby ke spuštění na *uzlu* při vytváření více služeb (*Service1* k *ServiceN*) z *ServiceType* (zadaný v *ServiceManifest*) nebo pokud je vaše služba více oddíly, musíte zadat **ServicePackageActivationMode** jako **ExclusiveProcess**v době vytváření služby.

## <a name="changing-hosting-model-of-an-existing-service"></a>Změna existující službu hostování modelu
Změna hostování modelu existující službu z **sdílený proces** k **výhradní proces** a naopak prostřednictvím upgradovat nebo aktualizovat mechanismus (nebo ve výchozích služeb specifikace v aplikaci manifest) se aktuálně nepodporuje. Podpora pro tuto funkci bude pocházet v budoucích verzích.

## <a name="choosing-between-shared-process-and-exclusive-process-model"></a>Volba mezi sdílený proces a model výhradní procesu
Obě tyto hostování modely mají jeho výhody a nevýhody a musí uživatel k vyhodnocení, které z nich nejlépe vyhovuje jejich požadavky. **Sdílený proces** model umožňuje lepší využití prostředků operačního systému, protože jsou méně procesy vytvořený, víc replik v rámci jednoho procesu můžete sdílet porty atd. Ale pokud jeden z repliky přístupy k chybě, kde je nutné uvést dolů hostitele služby, bude to mít vliv všech replik ve stejném procesu.

 **Výhradní proces** model zajišťuje lepší izolaci s každé repliky ve svém vlastním procesu a identifikovala repliky nebude mít vliv na ostatní repliky. Se hodit pro případy, kdy sdílení portů nepodporuje komunikační protokol. Zařídí schopnosti uplatňovat zásady správného řízení prostředků na úrovni repliky. Ale **výhradní proces** budou spotřebovávat více prostředků operačního systému, jak se vytvoří jeden proces pro každou repliku na uzlu.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Model procesu výhradní a aplikací modelu aspekty
Doporučený způsob modelování aplikace v Service Fabric je udržovat jeden *ServiceType* za *ServicePackage* a tento model funguje dobře pro většinu aplikací. 

Určený pro určité případy použití, Service Fabric také umožňuje více než jeden *ServiceType* za *ServicePackage* (a jeden *CodePackage* můžete zaregistrovat více než jeden  *ServiceType*). Tady jsou některé scénáře, kde může být užitečné tyto konfigurace:

- Chcete optimalizovat využití prostředků operačního systému tím, že při vytváření kopie méně procesy, které mají vyšší hustotu repliky podle procesu.
- Repliky z různých ServiceTypes potřeba sdílet některé běžné data, která má vysokou inicializace nebo paměti náklady.
- Máte nabídku bezplatné služby a chcete umístit omezení využití prostředků umístěním všechny repliky služby ve stejném procesu.

**Výhradní proces** model hostování není souvislý s modelem aplikace s více *ServiceTypes* za *ServicePackage*. Důvodem je, že více *ServiceTypes* za *ServicePackage* slouží k dosažení vyšší sdílení mezi repliky a umožňuje vyšší hustotu repliky podle procesu prostředků. To bylo v rozporu s co **výhradní proces** model je určen k dosáhnout.

Podívejte se více *ServiceTypes* za *ServicePackage* jiné *CodePackage* registrace každý *ServiceType* . Řekněme, že máme *ServicePackage* 'MultiTypeServicePackge', která má dva *CodePackages*:

- 'MyCodePackageA', který registruje *ServiceType* 'MyServiceTypeA'.
- 'MyCodePackageB', který registruje *ServiceType* 'MyServiceTypeB'.

Teď umožňuje vyslovení, vytvoříme *aplikace* **prostředků infrastruktury: / SpecialApp** a uvnitř **fabric: / SpecialApp** vytvoříme následující dvě služby s **výhradní Proces** modelu:

- Služba **fabric: / SpecialApp/ServiceA** typu 'MyServiceTypeA' se dva oddíly (vyslovení **P1** a **P2**) a tři repliky na jeden oddíl.
- Služba **fabric: / SpecialApp/ServiceB** typu 'MyServiceTypeB' se dva oddíly (vyslovení **P3** a **P4**) a tři repliky na jeden oddíl.

V daném uzlu bude mít obě služby dvě repliky. Vzhledem k tomu, že jsme použili **výhradní proces** modelu vytvářet služby, Service Fabric se budou aktivovat novou kopii 'MyServicePackge' pro každou repliku. Každá aktivace, MultiTypeServicePackge"se spustí kopie 'MyCodePackageA' a 'MyCodePackageB'. Ale pouze jeden 'MyCodePackageA' nebo 'MyCodePackageB' bude hostovat repliku, pro který byla aktivována 'MultiTypeServicePackge'. Následující diagram znázorňuje zobrazení uzlu:

<center>
![Zobrazení uzlu nasazené aplikace][node-view-five]
</center>

Při aktivaci 'MultiTypeServicePackge' pro repliku oddílu **P1** služby **fabric: / SpecialApp/ServiceA**'MyCodePackageA' je hostitelem repliky a 'MyCodePackageB' je v provozu . Podobně při aktivaci 'MultiTypeServicePackge' pro repliku oddílu **P3** služby **fabric: / SpecialApp/ServiceB**'MyCodePackageB' je hostitelem repliky a 'MyCodePackageA' je právě nastavení a spuštění a tak dále. Proto více počet *CodePackages* (registraci různých *ServiceTypes*) za *ServicePackage*, vyšší bude využití redundantní prostředků. 
 
 Ale pokud vytvoříme služby **fabric: / SpecialApp/ServiceA** a **prostředků infrastruktury: / SpecialApp/ServiceB** s **sdílený proces** modelu, služba Fabric se aktivují jenom jeden kopii 'MultiTypeServicePackge' pro *aplikace* **fabric: / SpecialApp** (jak jsme viděli dříve). 'MyCodePackageA' bude hostitelem všechny repliky pro službu **fabric: / SpecialApp/ServiceA** (nebo všechny služby typu 'MyServiceTypeA' jako přesnější). 'MyCodePackageB' bude hostitelem všechny repliky pro službu **fabric: / SpecialApp/ServiceB** (nebo všechny služby typu 'MyServiceTypeB' jako přesnější). Následující diagram znázorňuje zobrazení uzlu na toto nastavení: 

<center>
![Zobrazení uzlu nasazené aplikace][node-view-six]
</center>

V předchozím příkladu může domníváte, že pokud zaregistruje 'MyCodePackageA', 'MyServiceTypeA' a 'MyServiceTypeB' a neexistuje žádný MyCodePackageB, pak budou existovat žádné redundantní *CodePackage* systémem. To je správná, ale, jak je uvedeno dříve. Tento model aplikace zarovnání neshoduje s **výhradní proces** model hostování. Pokud je cílem pro umístění každé repliky ve svém vlastním procesu vyhrazené, pak oba registrace *ServiceTypes* ze stejné *CodePackage* není potřeba. Vložení každý *ServiceType* ve svém vlastním *ServicePacakge* je více fyzických volbou.

## <a name="next-steps"></a>Další kroky
[Balíček aplikace] [ a4] a připravit nasazení.

[Nasazení a odebírat aplikace] [ a5] popisuje, jak pomocí prostředí PowerShell pro správu instancí aplikace.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-guest-executables-introduction.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-createservice

[c1]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/servicefabric/vlatest/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/servicefabric/vlatest/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedcodepackage

---
title: Model hostování Azure Service Fabric | Microsoft Docs
description: Popisuje vztah mezi repliky (nebo instance) nasazené služby Service Fabric a proces hostitele služby.
services: service-fabric
documentationcenter: .net
author: harahma
manager: timlt
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: d56bb10041e3baffddf6fd4121a6e1f7ba8e0632
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="azure-service-fabric-hosting-model"></a>Model hostování Azure Service Fabric
Tento článek obsahuje přehled hostování modely, které poskytuje Azure Service Fabric aplikace a popisuje rozdíly mezi **sdílený proces** a **výhradní proces** modelů. Popisuje, jak vypadá nasazené aplikace na uzlu Service Fabric a vztah mezi repliky (nebo instance) služby a proces hostitele služby.

Než budete pokračovat, ujistěte se, že různé základními pojmy a vztahy podrobně [Model aplikace v Service Fabric][a1]. 

> [!NOTE]
> V tomto článku Pokud není výslovně uveden jako znamená něco jiného:
>
> - *Repliky* odkazuje na obou repliku stavové služby a instance bezstavové služby.
> - *CodePackage* považuje jako ekvivalentní *ServiceHost* proces, který registruje *ServiceType*a hostitele repliky služeb této *ServiceType*.
>

Model hostování pochopit, projděme příklad. Řekněme, že máme *ApplicationType* 'MyAppType', který má *ServiceType* 'MyServiceType'. 'MyServiceType' zajišťuje *ServicePackage* 'MyServicePackage', který má *CodePackage* 'MyCodePackage'. Zaregistruje 'MyCodePackage' *ServiceType* 'MyServiceType, když je spuštěna.

Řekněme, že máme tři uzly clusteru a vytvoříme *aplikace* **fabric: / App1** typu 'MyAppType'. Uvnitř této aplikace **fabric: / App1**, vytvoříme služby **fabric: / App1/ServiceA** typu 'MyServiceType'. Tato služba má dva oddíly (například **P1** a **P2**) a tři repliky na jeden oddíl. Následující diagram znázorňuje zobrazení této aplikace, jak se bude nakonec nasazené na uzlu.


![Diagram uzlu zobrazení nasazené aplikace][node-view-one]


Service Fabric aktivovat 'MyServicePackage', která spustila 'MyCodePackage', který je hostitelem repliky z obou oddílů. Všechny uzly v clusteru mají stejnou zobrazení, protože jsme zvolili počet replik na oddíl, který chcete být roven počtu uzlů v clusteru. Vytvoříme jiné službě, **fabric: / App1/ServiceB**, v aplikaci **fabric: / App1**. Tato služba obsahuje jeden oddíl (například **P3**) a tři repliky na jeden oddíl. Následující diagram znázorňuje nové zobrazení na uzlu:


![Diagram uzlu zobrazení nasazené aplikace][node-view-two]


Service Fabric umístit nového serveru repliky pro oddíl **P3** služby **fabric: / App1/ServiceB** v existující aktivace 'MyServicePackage'. Nyní. Umožňuje vytvořit jinou aplikaci **fabric: / počítači App2** typu 'MyAppType'. Uvnitř **fabric: / počítači App2**, vytvoření služby **fabric: / počítači App2/ServiceA**. Tato služba má dva oddíly (**P4** a **P5**) a tři repliky na jeden oddíl. Následující diagram znázorňuje nové zobrazení uzlu:


![Diagram uzlu zobrazení nasazené aplikace][node-view-three]


Service Fabric aktivuje novou kopii 'MyServicePackage', který spustí novou kopii 'MyCodePackage'. Repliky z obou oddílů služby **fabric: / počítači App2/ServiceA** (**P4** a **P5**) jsou umístěny v této nové kopie 'MyCodePackage'.

## <a name="shared-process-model"></a>Sdílené model procesu
V předchozí části popisuje výchozí model poskytované Service Fabric, označuje jako modelu procesu sdílené hostování. V tomto modelu pro danou aplikaci, pouze jedna kopie danou *ServicePackage* se aktivuje na uzlu (které spustí všechny *CodePackages* obsažené v ní). Všechny repliky všechny služby daný *ServiceType* jsou umístěny v *CodePackage* , který registruje *ServiceType*. Jinými slovy, všechny repliky všech služeb v uzlu danou *ServiceType* sdílet stejný postup.

## <a name="exclusive-process-model"></a>Výhradní model procesu
Další hostování modelu poskytované Service Fabric je model výhradní procesu. V tomto modelu v daném uzlu žije každé repliky ve svém vlastním procesu vyhrazené. Service Fabric aktivuje novou kopii *ServicePackage* (které spustí všechny *CodePackages* obsažené v ní). Repliky jsou umístěny v *CodePackage* které registrované *ServiceType* služby, do které replika patří. 

Pokud používáte Service Fabric verze 5.6 nebo novější, můžete zvolit modelu výhradní procesu v době vytvoření služby (s použitím [prostředí PowerShell][p1], [REST] [ r1], nebo [FabricClient][c1]). Zadejte **ServicePackageActivationMode** jako 'ExclusiveProcess'.

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

Pokud máte výchozí služba v manifestu aplikace, můžete vybrat model výhradní procesu zadáním **ServicePackageActivationMode** atribut:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Nyní vytvoříme jiné službě, **fabric: / App1/ServiceC**, v aplikaci **fabric: / App1**. Tato služba má dva oddíly (například **P6** a **N7**) a tři repliky na jeden oddíl. Nastavíte **ServicePackageActivationMode** k 'ExclusiveProcess'. Následující diagram znázorňuje nové zobrazení na uzlu:


![Diagram uzlu zobrazení nasazené aplikace][node-view-four]


Jak vidíte, Service Fabric aktivovat dva nové kopie 'MyServicePackage' (jeden pro každou repliku z oddílu **P6** a **N7**). Service Fabric umístit každou repliku v jeho vyhrazené kopie *CodePackage*. Použijete-li model výhradní proces pro danou aplikaci, více kopií danou *ServicePackage* může být aktivní na uzlu. V předchozím příkladu jsou aktivní pro tři kopie 'MyServicePackage' **fabric: / App1**. Každá z těchto aktivní kopie 'MyServicePackage' má **ServicePackageActivationId** s ním spojená. Toto ID identifikuje tuto kopii v rámci aplikace **fabric: / App1**.

Pokud používáte pouze modelu procesu sdílené pro aplikaci, je pouze jeden aktivní kopie *ServicePackage* na uzlu. **ServicePackageActivationId** u této aktivace *ServicePackage* je prázdný řetězec. To je případ, například s **fabric: / počítači App2**.

> [!NOTE]
>- Proces sdíleného hostingu modelu odpovídá **ServicePackageActivationMode** rovná **SharedProcess**. Toto je výchozí model, hostování a **ServicePackageActivationMode** není nutné zadávat při vytváření služby.
>
>- Proces výhradní model hostování odpovídá **ServicePackageActivationMode** rovná **ExclusiveProcess**. Chcete-li toto nastavení použít, musíte zadat ji explicitně v době vytváření služby. 
>
>- Pokud chcete zobrazit model hostování služby, dotaz [popisu služby][p2]a podívejte se na hodnotu **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Práce s balíčkem nasazené službě
Aktivní kopie *ServicePackage* na uzlu se označuje jako [nasazený balíček služby][p3]. Při použití modelu výhradní procesu pro vytváření služeb pro danou aplikaci, může být víc balíčků nasazené služby pro stejné *ServicePackage*. Pokud provádíte operace, které jsou specifické pro službu nasazenou balíčku, měli byste jim poskytnout **ServicePackageActivationId** k identifikaci balíček konkrétní nasazené služby. Pokud jste například zadat ID [reporting stav balíčku nasazené služby] [ p4] nebo [restartování balíček kódu balíčku nasazené služby] [p5].

Můžete zjistit **ServicePackageActivationId** balíčku nasazené služby dotazováním seznam [nasazené balíčky služeb] [ p3] na uzlu. Když dotazujete pro [nasazené typů služeb][p6], [nasazení repliky][p7], a [nasadit balíčky kódu ] [ p8] na uzlu, výsledek dotazu obsahuje taky **ServicePackageActivationId** balíčku služby nadřazená nasazená.

> [!NOTE]
>- V rámci modelu hostování sdílených procesu v daném uzlu, pro danou aplikaci, pouze jedna kopie *ServicePackage* se aktivuje. Má **ServicePackageActivationId** rovna *prázdný řetězec*a není nutné zadávat při provádění operace týkající se balíček nasazené služby. 
>
> - V části výhradní proces hostování modelu v daném uzlu, pro danou aplikaci, jednu nebo více kopií *ServicePackage* může být aktivní. Každá aktivace, má *neprázdný* **ServicePackageActivationId**zadaný při provádění operace týkající se balíček nasazené služby. 
>
> - Pokud **ServicePackageActivationId** je tento parametr vynechán, bude výchozí *prázdný řetězec*. Pokud je nasazená služba balíček, který byl aktivován v rámci modelu sdílený proces, operace se provede na něm. Jinak se operace nezdaří.
>
> - Dotazování jednou a mezipaměti **ServicePackageActivationId**. ID se dynamicky vygeneruje a můžete změnit z různých důvodů. Před provedením operace, která potřebuje **ServicePackageActivationId**, musí nejprve dotaz seznam [nasazené balíčky služeb] [ p3] na uzlu. Potom pomocí **ServicePackageActivationId** od výsledku dotazu k provedení operace původní.
>
>

## <a name="guest-executable-and-container-applications"></a>Spustitelný soubor a kontejneru aplikace hosta
Service Fabric zpracovává [spustitelný soubor hosta] [ a2] a [kontejneru] [ a3] aplikace jako bezstavové služby, které jsou samostatné. Neexistuje žádné modulu runtime Service Fabric v *ServiceHost* (procesu nebo kontejneru). Protože tyto služby jsou samostatný, počet replik na *ServiceHost* není použitelná pro tyto služby. Nejběžnější konfigurace používají tyto služby je jedním oddílem, s [InstanceCount] [ c2] rovno -1 (jednu kopii kódu služby spuštěné v každém uzlu clusteru). 

Výchozí hodnota **ServicePackageActivationMode** pro tyto služby je **SharedProcess**, v takovém případě Service Fabric aktivuje pouze jedné kopie *ServicePackage* na uzlu pro danou aplikaci.  To znamená, že pouze jedna kopie kódu služby bude spustit uzlu. Pokud chcete více kopií kódu služby ke spuštění na uzlu, zadejte **ServicePackageActivationMode** jako **ExclusiveProcess** v době vytváření služby. Například musíte při vytváření více služeb (*Service1* k *ServiceN*) z *ServiceType* (zadaný v *ServiceManifest*), nebo pokud je vaše služba více oddíly. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Změňte model hostování existující službu
V současné době nelze změnit model hostování existující službu z procesu sdílené výhradní procesu (nebo naopak).

## <a name="choose-between-the-hosting-models"></a>Výběr mezi hostování modely
Byste měli zvážit hostování modelu nejlepší vyhovuje vašim požadavkům. Model zpracování sdílené prostředky operačního systému používá lépe, protože jsou vytvořený méně procesů a víc replik v rámci jednoho procesu můžete sdílet porty. Ale pokud jeden z repliky došlo k chybě, kde je nutné uvést dolů hostitele služby, ovlivňuje všechny repliky ve stejném procesu.

 Model výhradní procesu poskytuje lepší izolace, s každé repliky ve svém vlastním procesu. Pokud jeden z repliky došlo k chybě, neovlivňuje ostatní repliky. Tento model je užitečné v případě, kde sdílení portů nepodporuje komunikační protokol. Zařídí schopnosti uplatňovat zásady správného řízení prostředků na úrovni repliky. Proces výhradní však spotřebuje další prostředky operačního systému, jak ho vytvoří jeden proces pro každou repliku na uzlu.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Výhradní modelu procesu a aplikace modelu aspekty
Pro většinu aplikací, můžete tak jeden model aplikace v Service Fabric *ServiceType* za *ServicePackage*. 

Pro určité případy Service Fabric také umožňuje více než jeden *ServiceType* za *ServicePackage* (a jeden *CodePackage* můžete zaregistrovat více než jeden  *ServiceType*). Tady jsou některé scénáře, kde může být užitečné tyto konfigurace:

- Chcete optimalizovat využití prostředků při vytváření kopie méně procesy, které mají vyšší hustotu repliky podle procesu.
- Repliky z různých *ServiceTypes* potřebovat sdílet běžné data, která má vysokou inicializace nebo paměti náklady.
- Máte nabídky bezplatné služby a chcete umístit omezení využití prostředků umístěním všechny repliky služby ve stejném procesu.

Proces výhradní model hostování není souvislý s modelem aplikace s více *ServiceTypes* za *ServicePackage*. Důvodem je, že více *ServiceTypes* za *ServicePackage* jsou určeny k dosažení vyšší sdílení mezi repliky prostředků a umožňuje vyšší hustotu repliky podle procesu. Model výhradní procesu slouží k dosažení různých výsledků.

Podívejte se více *ServiceTypes* za *ServicePackage*, s jiným *CodePackage* registrace každý *ServiceType*. Řekněme, že máme *ServicePackage* 'MultiTypeServicePackge', která má dva *CodePackages*:

- 'MyCodePackageA', který registruje *ServiceType* 'MyServiceTypeA'.
- 'MyCodePackageB', který registruje *ServiceType* 'MyServiceTypeB'.

Nyní předpokládejme, že jsme vytvořit aplikaci, **fabric: / SpecialApp**. Uvnitř **fabric: / SpecialApp**, vytvoříme následující dvě služby s modelem výhradní procesu:

- Služba **fabric: / SpecialApp/ServiceA** typu 'MyServiceTypeA' se dva oddíly (například **P1** a **P2**) a tři repliky na jeden oddíl.
- Služba **fabric: / SpecialApp/ServiceB** typu 'MyServiceTypeB' se dva oddíly (**P3** a **P4**) a tři repliky na jeden oddíl.

V daném uzlu mají obě služby dvě repliky. Protože jsme použili modelu výhradní procesu k vytvoření služby, Service Fabric aktivuje novou kopii 'MyServicePackage' pro každou repliku. Každá aktivace, MultiTypeServicePackge, spustí se kopie 'MyCodePackageA' a 'MyCodePackageB'. Je však pouze jeden 'MyCodePackageA' nebo 'MyCodePackageB' hostitelem repliky, pro který byla aktivována 'MultiTypeServicePackge'. Následující diagram znázorňuje zobrazení uzlu:


![Diagram zobrazení uzlu nasazené aplikace][node-view-five]


Při aktivaci 'MultiTypeServicePackge' repliky oddílu **P1** služby **fabric: / SpecialApp/ServiceA**, 'MyCodePackageA' je hostitelem repliky. 'MyCodePackageB' je spuštěna. Podobně v aktivace 'MultiTypeServicePackge' pro repliku oddílu **P3** služby **fabric: / SpecialApp/ServiceB**, 'MyCodePackageB' je hostitelem repliky. 'MyCodePackageA' je spuštěna. Proto větší počet *CodePackages* (registraci různých *ServiceTypes*) za *ServicePackage*, tím vyšší využití prostředků redundantní. 
 
 Ale pokud vytvoříme služby **fabric: / SpecialApp/ServiceA** a **prostředků infrastruktury: / SpecialApp/ServiceB** s modelem sdílené proces Service Fabric aktivuje pouze jedné kopie. MultiTypeServicePackge' pro aplikaci **fabric: / SpecialApp**. Všechny repliky pro službu hostuje 'MyCodePackageA' **fabric: / SpecialApp/ServiceA**. Všechny repliky pro službu hostuje 'MyCodePackageB' **fabric: / SpecialApp/ServiceB**. Následující diagram znázorňuje zobrazení uzlu na toto nastavení: 


![Diagram zobrazení uzlu nasazené aplikace][node-view-six]


V předchozím příkladu domníváte se, pokud zaregistruje 'MyCodePackageA', 'MyServiceTypeA' a 'MyServiceTypeB' a neexistuje žádný MyCodePackageB, neexistuje žádná redundantní *CodePackage* systémem. I když je to v pořádku, tento model aplikace zarovnání neshoduje s proces výhradní model hostování. Pokud je cílem pro umístění každé repliky ve svém vlastním procesu vyhrazené, není potřeba zaregistrovat i *ServiceTypes* ze stejné *CodePackage*. Místo toho můžete jednoduše řečeno každý *ServiceType* ve svém vlastním *ServicePackage*.

## <a name="next-steps"></a>Další postup
[Balíček aplikace] [ a4] a připravit nasazení.

[Nasazení a odebírat aplikace][a5]. Tento článek popisuje, jak pomocí prostředí PowerShell pro správu instancí aplikace.

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

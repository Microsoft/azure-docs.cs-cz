---
title: Model hostování Azure Service Fabric
description: Popisuje vztah mezi replikami (nebo instancemi) nasazené služby Service Fabric a procesem hostitele služby.
author: harahma
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: 69c7edb08693937aad5a658e0b22b00cd2a81647
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282390"
---
# <a name="azure-service-fabric-hosting-model"></a>Model hostování Azure Service Fabric
Tento článek obsahuje přehled modelů hostování aplikací poskytovaných Azure Service Fabric a popisuje rozdíly mezi **sdíleného procesu** a **výhradní proces** modely. Popisuje, jak nadeštěná aplikace vypadá na uzlu Service Fabric a vztah mezi replikami (nebo instancemi) služby a procesem hostitele služby.

Než budete pokračovat dále, ujistěte se, že rozumíte různým konceptům a vztahům vysvětleným v [modelu aplikace v service fabric .][a1] 

> [!NOTE]
> V tomto článku, pokud není výslovně uvedeno jako význam něco jiného:
>
> - *Replika* odkazuje na repliku stavové služby a instanci bezstavové služby.
> - *CodePackage* je považován za ekvivalentní *Proces ServiceHost,* který registruje *ServiceType*a hostuje repliky služeb tohoto *ServiceType*.
>

Chcete-li pochopit model hostování, projdeme si příklad. Řekněme, že máme *ApplicationType* 'MyAppType', který má *ServiceType* 'MyServiceType'. 'MyServiceType' je *poskytována ServicePackage* 'MyServicePackage', který má *CodePackage* MyCodePackage'. 'MyCodePackage' registruje *ServiceType* MyServiceType při spuštění.

Řekněme, že máme cluster se třemi uzlovými aplikacemi a vytvoříme *aplikaci* **fabric:/App1** typu MyAppType. Uvnitř této aplikace **fabric:/App1**, vytvoříme service **fabric:/App1/ServiceA** typu MyServiceType. Tato služba má dva oddíly (například **P1** a **P2**) a tři repliky na oddíl. Následující diagram znázorňuje zobrazení této aplikace, jak skončí nasazená na uzlu.


![Diagram zobrazení uzlu nasazené aplikace][node-view-one]


Service Fabric aktivován 'MyServicePackage', který spustil 'MyCodePackage', který je hostování repliky z obou oddílů. Všechny uzly v clusteru mají stejné zobrazení, protože jsme zvolili počet replik na oddíl, aby se rovnal počtu uzlů v clusteru. Pojďme vytvořit další službu, **fabric:/App1/ServiceB**, v **aplikaci prostředků infrastruktury:/App1**. Tato služba má jeden oddíl (například **P3**) a tři repliky na oddíl. Následující diagram znázorňuje nové zobrazení na uzlu:


![Diagram zobrazení uzlu nasazené aplikace][node-view-two]


Service Fabric umístil novou repliku pro oddíl **P3** service **fabric:/App1/ServiceB** v existující aktivaci "MyServicePackage". Nwo. pojďme vytvořit další aplikace **fabric:/App2** typu 'MyAppType'. Uvnitř **prostředků infrastruktury:/App2**vytvořte **strukturu infrastruktury služeb:/App2/ServiceA**. Tato služba má dva oddíly (**P4** a **P5**) a tři repliky na oddíl. Následující diagram znázorňuje zobrazení nového uzlu:


![Diagram zobrazení uzlu nasazené aplikace][node-view-three]


Service Fabric aktivuje novou kopii "MyServicePackage", která spustí novou kopii "MyCodePackage". Repliky z obou oddílů **service fabric:/App2/ServiceA** (**P4** a **P5**) jsou umístěny v této nové kopii "MyCodePackage".

## <a name="shared-process-model"></a>Model sdíleného procesu
Předchozí část popisuje výchozí model hostování poskytované Service Fabric, označované jako model sdíleného procesu. V tomto modelu pro danou aplikaci je aktivována pouze jedna kopie daného *ServicePackage* na uzlu (který spustí všechny *CodePackages* obsažené v něm). Všechny repliky všech služeb daného *ServiceType* jsou umístěny v *CodePackage,* který registruje, že *ServiceType*. Jinými slovy všechny repliky všech služeb na uzlu daného *Typu ServiceType* sdílejí stejný proces.

## <a name="exclusive-process-model"></a>Exkluzivní procesní model
Další hostování modelu poskytované Service Fabric je výhradní proces modelu. V tomto modelu v daném uzlu každá replika žije ve vlastním vyhrazeném procesu. Service Fabric aktivuje novou kopii *ServicePackage* (který spustí všechny *CodePackages* obsažené v něm). Repliky jsou umístěny v *CodePackage,* který zaregistroval *ServiceType* služby, do které patří replika. 

Pokud používáte Service Fabric verze 5.6 nebo novější, můžete zvolit model výhradníproces v době vytvoření služby (pomocí [prostředí PowerShell][p1], [REST][r1]nebo [FabricClient][c1]). Zadejte **ServicePackageActivationMode** jako "ExclusiveProcess".

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

Pokud máte výchozí službu v manifestu aplikace, můžete zvolit model výhradního procesu zadáním atributu **ServicePackageActivationMode:**

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Nyní vytvoříme další službu, **fabric:/App1/ServiceC**, v **aplikaci fabric:/App1**. Tato služba má dva oddíly (například **P6** a **P7**) a tři repliky na oddíl. Nastavení **ServicePackageActivationMode** na 'ExclusiveProcess'. Následující diagram znázorňuje nové zobrazení na uzlu:


![Diagram zobrazení uzlu nasazené aplikace][node-view-four]


Jak můžete vidět, Service Fabric aktivován dvě nové kopie 'MyServicePackage' (jeden pro každou repliku z oddílu **P6** a **P7**). Service Fabric umístěny každou repliku v jeho vyhrazené kopie *CodePackage*. Při použití modelu výhradní proces pro danou aplikaci více kopií daného *ServicePackage* může být aktivní na uzlu. V předchozím příkladu tři kopie "MyServicePackage" jsou aktivní pro **fabric:/App1**. Každá z těchto aktivních kopií "MyServicePackage" má **ServicePackageActivationId** s ním spojené. Toto ID identifikuje tuto kopii v rámci **prostředků infrastruktury aplikace:/App1**.

Pokud používáte pouze model sdíleného procesu pro aplikaci, je pouze jedna aktivní kopie *ServicePackage* na uzlu. **ServicePackageActivationId** pro tuto aktivaci *ServicePackage* je prázdný řetězec. To je případ, například s **fabric:/App2**.

> [!NOTE]
>- Hostitelský model sdíleného procesu odpovídá **ServicePackageActivationMode** rovná **se SharedProcess**. Toto je výchozí hostitelský model a **ServicePackageActivationMode** nemusí být zadán v době vytváření služby.
>
>- Výhradní proces hostování modelu odpovídá **ServicePackageActivationMode** rovná **ExclusiveProcess**. Chcete-li použít toto nastavení, měli byste zadat explicitně v době vytváření služby. 
>
>- Chcete-li zobrazit model hostování služby, dotaz [na popis služby][p2]a podívejte se na hodnotu **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Práce s nasazeným balíčkem služeb
Aktivní kopie *ServicePackage* na uzlu se označuje jako [balíček nasazených služeb][p3]. Při použití modelu výhradní proces pro vytváření služeb pro danou aplikaci může existovat více nasazených balíčků služeb pro stejný *ServicePackage*. Pokud provádíte operace specifické pro nasazený balíček služeb, měli byste poskytnout **ServicePackageActivationId** k identifikaci konkrétního balíčku nasazených služeb. Například zadejte ID, pokud [hlásíte stav balíčku nasazených služeb][p4] nebo [restartování balíčku kódu balíčku nasazených služeb][p5].

Můžete zjistit **ServicePackageActivationId** nasazené služby balíčku dotazem na seznam [nasazených balíčků služeb][p3] na uzlu. Při dotazování na [typy nasazených služeb][p6], [nasazených replik][p7]a [nasazených balíčků kódu][p8] v uzlu obsahuje výsledek dotazu také **ServicePackageActivationId** nadřazeného balíčku nasazených služeb.

> [!NOTE]
>- V rámci modelu hostování sdíleného procesu je v daném uzlu pro danou aplikaci aktivována pouze jedna kopie *balíčku ServicePackage.* Má **ServicePackageActivationId** rovná *prázdný řetězec*a nemusí být zadán při provádění operací souvisejících s nasazené balíček služby. 
>
> - V rámci výhradní proces hostování modelu, na daném uzlu pro danou aplikaci, jeden nebo více kopií *ServicePackage* může být aktivní. Každá aktivace má *neprázdné* **ServicePackageActivationId**, určené při provádění operací souvisejících s nasazeným balíčkem služeb. 
>
> - Pokud **ServicePackageActivationId** je vynechán, je výchozí *prázdný řetězec*. Pokud je k dispozici balíček nasazených služeb, který byl aktivován v rámci modelu sdíleného procesu, operace bude provedena na něm. Jinak se operace nezdaří.
>
> - Nedotazovat jednou a cache **ServicePackageActivationId**. ID je dynamicky generována a může změnit z různých důvodů. Před provedením operace, která potřebuje **ServicePackageActivationId**, měli byste nejprve dotaz na seznam [nasazených balíčků služeb][p3] na uzlu. Potom použijte **ServicePackageActivationId** z výsledku dotazu k provedení původní operace.
>
>

## <a name="guest-executable-and-container-applications"></a>Spustitelné a kontejnerové aplikace hosta
Service Fabric považuje [spustitelné a][a2] kontejnerové aplikace [hosta][a3] za bezstavové služby, které jsou samostatné. Neexistuje žádný service fabric runtime v *ServiceHost* (proces nebo kontejner). Vzhledem k tomu, že tyto služby jsou samostatné, počet replik na *ServiceHost* není pro tyto služby použitelné. Nejběžnější konfigurace používaná u těchto služeb je jeden oddíl, s [InstanceCount][c2] rovná -1 (jedna kopie kódu služby spuštěné na každém uzlu clusteru). 

Výchozí **ServicePackageActivationMode** pro tyto služby je **SharedProcess**, v takovém případě Service Fabric aktivuje pouze jednu kopii *ServicePackage* na uzlu pro danou aplikaci.  To znamená, že pouze jedna kopie kódu služby spustí uzel. Pokud chcete, aby více kopií kódu služby spustit na uzlu, zadejte **ServicePackageActivationMode** jako **ExclusiveProcess** v době vytvoření služby. Například to můžete provést při vytváření více služeb *(Service1* to *ServiceN*) *ServiceType* (zadáno v *ServiceManifest),* nebo když je vaše služba více oddílů. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Změna modelu hostování existující služby
V současné době nelze změnit model hostování existující služby ze sdíleného procesu na výhradní proces (nebo naopak).

## <a name="choose-between-the-hosting-models"></a>Vyberte si mezi hostingovými modely
Měli byste vyhodnotit, který hostingový model nejlépe vyhovuje vašim požadavkům. Model sdíleného procesu používá prostředky operačního systému lépe, protože se vytváří méně procesů a porty může sdílet více replik ve stejném procesu. Pokud však jedna z replik obsahuje chybu, kde potřebuje snížit počet hostitelů služby, ovlivní všechny ostatní repliky ve stejném procesu.

 Model výhradního procesu poskytuje lepší izolaci s každou replikou v jeho vlastním procesu. Pokud jedna z replik obsahuje chybu, nemá vliv na jiné repliky. Tento model je užitečný v případech, kdy sdílení portů není komunikačním protokolem podporováno. Usnadňuje možnost použít zásadsprávné řízení prostředků na úrovni repliky. Výhradní proces však spotřebovává více prostředků operačního systému, protože spouští jeden proces pro každou repliku v uzlu.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Aspekty exkluzivního modelu procesu a aplikačního modelu
Pro většinu aplikací můžete modelovat aplikaci v Service Fabric udržováním jednoho *ServiceType* per *ServicePackage*. 

Pro některé případy Service Fabric také umožňuje více než jeden *ServiceType* na *ServicePackage* (a jeden *CodePackage* můžete zaregistrovat více než jeden *ServiceType).* Následují některé scénáře, kde mohou být užitečné tyto konfigurace:

- Chcete optimalizovat využití prostředků tím, že se vytváří méně procesů a má vyšší hustotu replik na proces.
- Repliky z různých *ServiceTypes* je třeba sdílet některá společná data, která má vysoké náklady na inicializaci nebo paměť.
- Máte bezplatnou nabídku služeb a chcete omezit využití prostředků tím, že všechny repliky služby ve stejném procesu.

Výhradní proces hostování modelu není v souladu s aplikační model s více *ServiceTypes* na *ServicePackage*. Důvodem je, že více *ServiceTypes* na *ServicePackage* jsou navrženy tak, aby dosažení vyšší sdílení prostředků mezi replikami a umožňuje vyšší hustotu replik na proces. Model exclusive process je navržen tak, aby bylo dosaženo různých výsledků.

Vezměme si případ více *ServiceTypes* na *ServicePackage*, s jiným *CodePackage* registraci každého *ServiceType*. Řekněme, že máme *ServicePackage* 'MultiTypeServicePackage', který má dva *CodePackages*:

- 'MyCodePackageA', který registruje *ServiceType* 'MyServiceTypeA'.
- 'MyCodePackageB', který registruje *ServiceType* 'MyServiceTypeB'.

Nyní řekněme, že vytvoříme aplikaci, **fabric:/SpecialApp**. Uvnitř **fabric:/SpecialApp**, vytvoříme následující dvě služby s modelem Exclusive Process:

- **Service fabric:/SpecialApp/ServiceTyp** 'MyServiceTypeA', se dvěma oddíly (například **P1** a **P2**) a tři repliky na oddíl.
- **Service fabric:/SpecialApp/ServiceB** typu "MyServiceTypeB", se dvěma oddíly (**P3** a **P4**) a třemi replikami na oddíl.

Na daném uzlu obě služby mají dvě repliky každý. Protože jsme použili výhradní proces modelu k vytvoření služeb, Service Fabric aktivuje novou kopii "MyServicePackage" pro každou repliku. Každá aktivace balíčku MultiTypeServicePackage spustí kopii mycodepackagea a mycodepackageb. Repliku, pro kterou byl aktivován balíček MultiTypeServicePackage, však hostuje pouze jeden z mycodepackagea nebo mycodepackageb. Následující diagram znázorňuje zobrazení uzlu:


![Diagram zobrazení uzlu nasazené aplikace][node-view-five]


Při aktivaci 'MultiTypeServicePackage' pro repliku oddílu **P1** service **fabric:/SpecialApp/ServiceA**, 'MyCodePackageA' je hostitelem repliky. 'MyCodePackageB' je spuštěn. Podobně při aktivaci 'MultiTypeServicePackage' pro repliku oddílu **P3** service **fabric:/SpecialApp/ServiceB**, 'MyCodePackageB' je hostitelem repliky. 'MyCodePackageA' je spuštěn. Proto větší počet *CodePackages* (registrace různých *ServiceTypes*) na *ServicePackage*, tím vyšší redundantní využití prostředků. 
 
 Pokud však vytvoříme **služby fabric:/SpecialApp/ServiceA** a **fabric:/SpecialApp/ServiceB** s modelem sdíleného procesu, service fabric aktivuje pouze jednu kopii "MultiTypeServicePackage" pro **strukturu aplikace:/SpecialApp**. 'MyCodePackageA' hostuje všechny repliky pro **service fabric:/SpecialApp/ServiceA**. 'MyCodePackageB' hostuje všechny repliky pro **service fabric:/SpecialApp/ServiceB**. Následující diagram znázorňuje zobrazení uzlu v tomto nastavení: 


![Diagram zobrazení uzlu nasazené aplikace][node-view-six]


V předchozím příkladu si můžete myslet, že pokud 'MyCodePackageA' registruje myServiceTypeA ' a "MyServiceTypeB' a není 'MyCodePackageB', pak není spuštěn žádný redundantní *CodePackage.* I když je to správné, tento model aplikace není zarovnán s modelhostingu výhradní proces. Pokud je cílem umístit každou repliku do vlastního vyhrazeného procesu, není nutné registrovat oba *ServiceTypes* ze *stejnécodepackage*. Místo toho jednoduše umístit každý *ServiceType* v jeho vlastní *ServicePackage*.

## <a name="next-steps"></a>Další kroky
[Zabalte aplikaci][a4] a připravte ji k nasazení.

[Nasazujte a odeberte aplikace][a5]. Tento článek popisuje, jak používat PowerShell ke správě instancí aplikací.

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

[p1]: https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/module/servicefabric/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/module/servicefabric/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage

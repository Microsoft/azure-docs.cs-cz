---
title: Model hostování Azure Service Fabric
description: Popisuje vztah mezi replikami (nebo instancemi) nasazené služby Service Fabric a procesem hostitele služeb.
author: harahma
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: harahma
ms.custom: devx-track-csharp
ms.openlocfilehash: 5f3f6238bb72704d13fef4a7171aeaebee5f9141
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708692"
---
# <a name="azure-service-fabric-hosting-model"></a>Model hostování Azure Service Fabric
Tento článek obsahuje přehled modelů hostování aplikací poskytovaných službou Azure Service Fabric a popisuje rozdíly mezi **sdíleným procesem** a modely **exkluzivních procesů** . Popisuje, jak nasazená aplikace vypadá na Service Fabric uzlu, a vztahu mezi replikami (nebo instancemi) služby a procesem služby-Host.

Než budete pokračovat, ujistěte se, že rozumíte různým konceptům a vztahům, které jsou vysvětleny v tématu [modelování aplikace v Service Fabric][a1]. 

> [!NOTE]
> V tomto článku, pokud výslovně nezmiňujete o něco jiného:
>
> - *Replika* odkazuje jak na repliku stavové služby, tak na instanci bezstavové služby.
> - *CodePackage* se považuje za ekvivalent procesu *ServiceHost* , který registruje *ServiceType*a hostuje repliky služeb tohoto *ServiceType*.
>

Abychom porozuměli hostujícímu modelu, Podívejme se na příklad. Řekněme, že máme *typu ApplicationType* ' MyAppType ', který má *ServiceType* ' MyServiceType '. ' MyServiceType ' je poskytována ' *MyServicePackage* ', který má *CodePackage* ' MyCodePackage '. Při spuštění *zaregistruje ' MyCodePackage* ' MyServiceType '.

Řekněme, že máme cluster se třemi uzly a vytvoříme *aplikační* **Fabric:/app1** typu MyAppType. V rámci této aplikace **Fabric:/app1**vytvoříme Service **Fabric:/app1/služba** typu MyServiceType. Tato služba má dva oddíly (například **P1** a **P2**) a tři repliky na oddíl. Následující diagram znázorňuje zobrazení této aplikace v případě, že skončí její nasazení na uzlu.


![Diagram, který zobrazuje zobrazení této aplikace v případě, že končí nasazení na uzlu.][node-view-one]


Service Fabric aktivoval ' MyServicePackage ', který spustil ' MyCodePackage ', který hostuje repliky z obou oddílů. Všechny uzly v clusteru mají stejné zobrazení, protože jsme zvolili počet replik na oddíl, který se rovná počtu uzlů v clusteru. Pojďme vytvořit další službu, **Fabric:/app1/ServiceB**v Application **Fabric:/app1**. Tato služba má jeden oddíl (například **P3**) a tři repliky na oddíl. Následující diagram znázorňuje nové zobrazení na uzlu:


![Diagram, který zobrazuje nové zobrazení na uzlu.][node-view-two]


Service Fabric umístit novou repliku pro oddíl **P3** Service **Fabric:/app1/ServiceB** v existující aktivaci ' MyServicePackage '. Současné. Pojďme vytvořit další aplikační **Fabric:/app2** typu MyAppType. V **prostředcích infrastruktury:/app2**vytvořte Service **Fabric:/app2/Service**. Tato služba má dva oddíly (**P4** a **P5**) a tři repliky na oddíl. Následující diagram znázorňuje zobrazení nového uzlu:


![Diagram, který zobrazuje nové zobrazení uzlu.][node-view-three]


Service Fabric aktivuje novou kopii ' MyServicePackage ', která spustí novou kopii ' MyCodePackage '. V této nové kopii MyCodePackage jsou umístěné repliky z obou oddílů Service **Fabric:/app2/Service-** (**P4** a **P5**).

## <a name="shared-process-model"></a>Model sdíleného procesu
Předchozí část popisuje výchozí model hostování poskytovaný Service Fabric, který je označován jako model sdíleného procesu. V tomto modelu se pro danou aplikaci aktivuje jenom jedna kopie určitého programu *ServicePack* na uzlu (tím se spustí všechny *CodePackages* v něm obsažené). Všechny repliky všech služeb daného *ServiceType* jsou umístěny do *CodePackage* , který registruje tento *ServiceType*. Jinými slovy všechny repliky všech služeb na uzlu daného *ServiceType* sdílejí stejný proces.

## <a name="exclusive-process-model"></a>Exkluzivní model procesu
Dalším modelem hostování, který poskytuje Service Fabric, je exkluzivní model procesu. V tomto modelu na daném uzlu každá replika bydlí ve vlastním vyhrazeném procesu. Service Fabric aktivuje novou kopii aplikace *ServicePack* (která spustí všechna *CodePackages* obsažená v ní). Repliky jsou umístěny v *CodePackage* , který zaregistroval *ServiceType* služby, ke které replika patří. 

Pokud používáte Service Fabric verze 5,6 nebo novější, můžete zvolit exkluzivní model procesu v době vytváření služby (pomocí [PowerShellu][p1], [REST][r1]nebo [FabricClient][c1]). Zadejte **ServicePackageActivationMode** jako ExclusiveProcess.

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

Pokud máte ve svém manifestu aplikace výchozí službu, můžete zvolit exkluzivní model procesu zadáním atributu **ServicePackageActivationMode** :

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Nyní vytvoříme další službu, **Fabric:/app1/ServiceC**v Application **Fabric:/app1**. Tato služba má dva oddíly (například **P6** a **P7**) a tři repliky na oddíl. Nastavte **ServicePackageActivationMode** na ExclusiveProcess. Následující diagram znázorňuje nové zobrazení na uzlu:


![Diagram zobrazení uzlů nasazené aplikace][node-view-four]


Jak vidíte, Service Fabric aktivovat dvě nové kopie ' MyServicePackage ' (jeden pro každou repliku z oddílu **P6** a **P7**). Service Fabric umístit každou repliku do vyhrazené kopie *CodePackage*. Použijete-li exkluzivní model procesu pro danou aplikaci, může být v uzlu aktivní více kopií této *ServicePack* . V předchozím příkladu jsou pro **Fabric:/app1**aktivní tři kopie ' MyServicePackage '. K jednotlivým aktivním kopiím ' MyServicePackage ' je přidružena **ServicePackageActivationId** . Toto ID identifikuje tuto kopii v rámci aplikace **Fabric:/app1**.

Použijete-li pro aplikaci pouze model sdíleného procesu, je na uzlu pouze jedna aktivní kopie aplikace *ServicePack* . **ServicePackageActivationId** pro tuto aktivaci služby *ServicePack* je prázdný řetězec. Jedná se například o případ s **prostředky Fabric:/app2**.

> [!NOTE]
>- Model hostování sdíleného procesu odpovídá **ServicePackageActivationMode** rovná se **SharedProcess**. Toto je výchozí hostující model a v době vytváření služby není potřeba zadávat **ServicePackageActivationMode** .
>
>- Model hostování exkluzivních procesů odpovídá **ServicePackageActivationMode** rovná se **ExclusiveProcess**. Chcete-li použít toto nastavení, je nutné je explicitně zadat v době vytváření služby. 
>
>- Chcete-li zobrazit model hostování služby, proveďte dotaz na [Popis služby][p2]a podívejte se na hodnotu **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Práce s nasazeným balíčkem služby
Aktivní kopie *ServicePack* na uzlu je označována jako [nasazený balíček služby][p3]. Když použijete exkluzivní model procesu pro vytváření služeb pro danou aplikaci, může existovat více nasazených balíčků služby pro stejnou *ServicePack*. Pokud provádíte operace specifické pro nasazený balíček služby, měli byste poskytnout **ServicePackageActivationId** k identifikaci konkrétního balíčku nasazené služby. Zadejte například ID, pokud [hlásíte stav nasazeného balíčku služby][p4] nebo [restartuje balíček kódu nasazeného balíčku služby][p5].

**ServicePackageActivationId** nasazeného balíčku služby můžete zjistit dotazem na seznam [nasazených balíčků služby][p3] na uzlu. Při dotazování na [nasazené typy služeb][p6], [nasazené repliky][p7]a [nasazené balíčky kódu][p8] na uzel, výsledek dotazu obsahuje také **ServicePackageActivationId** nadřazeného balíčku nasazené služby.

> [!NOTE]
>- V rámci modelu hostování sdíleného procesu se v daném uzlu pro danou aplikaci aktivuje jenom jedna kopie *ServicePack* . Má **ServicePackageActivationId** , který se rovná *prázdnému řetězci*a při provádění operací souvisejících s nasazeným balíčkem služby není nutné zadávat žádné. 
>
> - V rámci modelu hostování exkluzivního procesu, v daném uzlu pro danou aplikaci, může být jedna nebo více kopií služby *ServicePack* . Každá aktivace má *neprázdné* **ServicePackageActivationId**, která se zadala při provádění operací souvisejících s nasazeným balíčkem služby. 
>
> - Pokud je vynecháno **ServicePackageActivationId** , výchozí hodnota je *prázdný řetězec*. Pokud je k dispozici nasazený balíček služby, který byl aktivován pomocí modelu sdíleného procesu, bude provedena operace. Jinak se operace nezdaří.
>
> - Nedotazovat se jen jednou a **ServicePackageActivationId**do mezipaměti. ID je dynamicky generované a může se změnit z různých důvodů. Před provedením operace, která potřebuje **ServicePackageActivationId**, byste nejdřív měli zadat dotaz na seznam [nasazených balíčků služby][p3] na uzlu. Pak použijte **ServicePackageActivationId** z výsledku dotazu k provedení původní operace.
>
>

## <a name="guest-executable-and-container-applications"></a>Spustitelné aplikace a aplikace typu Host
Service Fabric považuje [spustitelné soubory hosta][a2] a [kontejnerové][a3] aplikace jako bezstavové služby, které jsou samostatně obsaženy. V *ServiceHost* není žádný modul runtime Service Fabric (proces nebo kontejner). Vzhledem k tomu, že tyto služby jsou samy obsažené, není možné pro tyto služby použít počet replik na *ServiceHost* . Nejběžnější konfigurace, která se používá pro tyto služby, je jeden oddíl, přičemž [InstanceCount][c2] se rovná-1 (jedna kopie kódu služby spuštěná na každém uzlu clusteru). 

Výchozí **ServicePackageActivationMode** pro tyto služby je **SharedProcess**. v takovém případě Service Fabric aktivovat pouze jednu kopii *ServicePack* na uzlu pro danou aplikaci.  To znamená, že se na jednom z kopií kódu služby spustí uzel. Pokud chcete, aby se na uzlu spouštělo více kopií kódu služby, zadejte v době vytváření služby **ServicePackageActivationMode** jako **ExclusiveProcess** . Můžete to například udělat, když vytvoříte více služeb (*Service1* *ServiceN*) pro *ServiceType* (určený v *ServiceManifest*) nebo když je vaše služba ve více oddílech. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Změna modelu hostování existující služby
V současné době nemůžete změnit model hostování existující služby ze sdíleného procesu na exkluzivní proces (nebo naopak).

## <a name="choose-between-the-hosting-models"></a>Výběr mezi modely hostování
Měli byste vyhodnotit, který model hostování nejlépe vyhovuje vašim požadavkům. Model sdíleného procesu používá prostředky operačního systému lépe, protože jsou vytvořeny méně procesů a více replik ve stejném procesu může sdílet porty. Pokud ale jedna z replik obsahuje chybu, kde je potřeba vyvolat hostitele služby, má dopad na všechny ostatní repliky ve stejném procesu.

 Exkluzivní model procesu poskytuje lepší izolaci se všemi replikami ve vlastním procesu. Pokud některá z replik obsahuje chybu, nemá vliv na jiné repliky. Tento model je vhodný pro případy, kdy komunikační protokol nepodporuje sdílení portů. Usnadňuje možnost uplatňovat zásady správného řízení prostředků na úrovni repliky. Exkluzivní proces ale spotřebovává více prostředků operačního systému, protože pro každou repliku na uzlu vytvoří jeden proces.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Exkluzivní model procesu a požadavky modelu aplikace
Pro většinu aplikací můžete modelovat aplikaci v Service Fabric tím, že zachováte jeden *ServiceType* za *ServicePack*. 

V některých případech Service Fabric také umožňuje více než jeden *ServiceType* za *ServicePack* (a jeden *CodePackage* může registrovat více než jeden *ServiceType*). Níže jsou uvedeny některé z scénářů, ve kterých mohou být tyto konfigurace užitečné:

- Chcete optimalizovat využití prostředků vytvořením menšího počtu procesů a vyšší hustoty replik na proces.
- Repliky z různých *tříd ServiceType* musí sdílet některá společná data s vysokou inicializací nebo pamětí.
- Máte bezplatnou nabídku služeb a chcete omezit využití prostředků tím, že do stejného procesu umístíte všechny repliky služby.

Exkluzivní model hostování procesu není soudržný s modelem aplikace, který má více *tříd ServiceType* za službu *ServicePack*. Důvodem je to, že více *servicetypeí* za *ServicePack* je navrženo tak, aby bylo dosaženo většího sdílení prostředků mezi replikami, a umožňuje vyšší hustotu repliky na proces. Exkluzivní model procesu je navržený tak, aby dosáhl různých výsledků.

Vezměte v úvahu případ vícenásobných *servicetypeí* za každou *ServicePack*, s různými *CodePackage* registrací každého *ServiceType*. Řekněme *, že máme "MultiTypeServicePackage* ", který má dvě *CodePackages*:

- ' MyCodePackageA ', který registruje *ServiceType* ' MyServiceTypeA '.
- ' MyCodePackageB ', který registruje *ServiceType* ' MyServiceTypeB '.

Teď řekněme, že vytvoříme aplikaci, **Fabric:/SpecialApp**. V **prostředcích infrastruktury:/SpecialApp**vytvoříme tyto dvě služby s výhradním modelem procesu:

- Service **Fabric:/SpecialApp/Service** ' typu ' MyServiceTypeA ', se dvěma oddíly (například **P1** a **P2**) a tři repliky na oddíl.
- Service **Fabric:/SpecialApp/ServiceB** typu MyServiceTypeB, se dvěma oddíly (**P3** a **P4**) a třemi replikami na oddíl.

V daném uzlu mají obě služby dvě repliky každý. Vzhledem k tomu, že jsme k vytváření služeb použili model exkluzivních procesů, Service Fabric pro každou repliku aktivovat novou kopii ' MyServicePackage '. Každá aktivace ' MultiTypeServicePackage ' spustí kopii ' MyCodePackageA ' a ' MyCodePackageB '. Pouze jedna z ' MyCodePackageA ' nebo ' MyCodePackageB ' hostuje repliku, pro kterou byla aktivována ' MultiTypeServicePackage '. Následující diagram znázorňuje zobrazení uzlu:


![Diagram znázorňující zobrazení uzlu][node-view-five]


Při aktivaci ' MultiTypeServicePackage ' pro repliku oddílu **P1** služby Service **Fabric:/SpecialApp/Service**', ' MyCodePackageA ' hostuje repliku. ' MyCodePackageB ' je spuštěn. Podobně v aktivaci MultiTypeServicePackage pro repliku oddílu **P3** služby Service **Fabric:/SpecialApp/ServiceB**, ' MyCodePackageB ' hostuje repliku. ' MyCodePackageA ' je spuštěn. Čím větší je počet *CodePackages* (registrování různých *servicetypeí*) na každou *ServicePack*, tím vyšší je využívání redundantních prostředků. 
 
 Pokud však vytvoříme služby **Fabric:/SpecialApp/Service-** a **Fabric:/SpecialApp/ServiceB** s modelem sdíleného procesu, Service Fabric pro aplikační **Fabric:/SpecialApp**aktivuje pouze jednu kopii ' MultiTypeServicePackage '. ' MyCodePackageA ' hostuje všechny repliky pro Service **Fabric:/SpecialApp/Service**. ' MyCodePackageB ' hostuje všechny repliky pro Service **Fabric:/SpecialApp/ServiceB**. Následující diagram znázorňuje zobrazení uzlu v tomto nastavení: 


![Diagram zobrazení uzlu nasazené aplikace][node-view-six]


V předchozím příkladu si myslíte, že pokud ' MyCodePackageA ' registruje ' MyServiceTypeA ' a ' MyServiceTypeB ' a neexistuje ' MyCodePackageB ', není spuštěn žádný redundantní *CodePackage* . I když je to správné, tento model aplikace se neshoduje s modelem hostování exkluzivních procesů. Pokud je cílem umístit každou repliku ve vlastním vyhrazeném procesu, nemusíte registrovat oba typy *ServiceType* ze stejného *CodePackage*. Místo toho je třeba jednotlivé *servicetypee* vkládat do své vlastní *ServicePack*.

### <a name="reliable-services-and-actor-forking-subprocesses"></a>Reliable Services a podprocesy pro rozvětvení objektu actor

Service Fabric nepodporuje spolehlivé služby a následně spolehlivé objekty actor rozvětvené podprocesy. Příklad, proč není podporován, je [CodePackageActivationContext](/dotnet/api/system.fabric.codepackageactivationcontext?view=azure-dotnet) nelze použít k registraci nepodporovaného podprocesu a tokeny zrušení jsou odesílány pouze registrovaným procesům. Výsledkem nejrůznějších problémů, jako například selhání upgradu, pokud se podprocesy nezavřou po přijetí nadřazeného procesu tokenu zrušení.

## <a name="next-steps"></a>Další kroky
[Zabalit aplikaci][a4] a připravit ji na nasazení.

[Nasaďte a odeberte aplikace][a5]. Tento článek popisuje, jak pomocí PowerShellu spravovat instance aplikací.

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

[r1]: /rest/api/servicefabric/sfclient-api-createservice

[c1]: /dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: /dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: /powershell/module/servicefabric/new-servicefabricservice
[p2]: /powershell/module/servicefabric/get-servicefabricservicedescription
[p3]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[p4]: /powershell/module/servicefabric/send-servicefabricdeployedservicepackagehealthreport
[p5]: /powershell/module/servicefabric/restart-servicefabricdeployedcodepackage
[p6]: /powershell/module/servicefabric/get-servicefabricdeployedservicetype
[p7]: /powershell/module/servicefabric/get-servicefabricdeployedreplica
[p8]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage

---
title: Model hostingu Azure Service Fabric | Dokumentace Microsoftu
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
ms.openlocfilehash: 80d9d447a86b58c8d6db5a62d3b0df997e42f673
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55172370"
---
# <a name="azure-service-fabric-hosting-model"></a>Model hostingu Azure Service Fabric
Tento článek poskytuje přehled modelů poskytované Azure Service Fabric pro hostování aplikací a popisuje rozdíly mezi **sdílený proces** a **exkluzivní procesu** modely. Popisuje, jak vypadá aplikace nasazené na uzlu Service Fabricu a vztah mezi repliky (nebo instance), služby a služby hostitelský proces.

Než budete pokračovat, ujistěte se, že rozumíte různé koncepty a relace je vysvětleno v [modelování aplikace v Service Fabric][a1]. 

> [!NOTE]
> V tomto článku pokud výslovně uvedené jako to znamená něco jiného:
>
> - *Repliky* odkazuje na obou repliku stavové služby a instance bezstavové služby.
> - *CodePackage* je zpracováván jako ekvivalentní *ServiceHost* proces, který se registruje *ServiceType*a repliky hostitelé služeb, který *ServiceType*.
>

Informace o tom model hostingu, projděme si příklad. Řekněme, že máme *ApplicationType* "MyAppType", který má *ServiceType* "MyServiceType". Je poskytována "MyServiceType" *ServicePackage* "MyServicePackage", který má *CodePackage* "MyCodePackage". Zaregistruje "MyCodePackage" *ServiceType* "MyServiceType' při spuštění.

Řekněme, že budeme mít cluster se třemi uzly a vytvoříme *aplikace* **fabric: / App1** typu "MyAppType". V této aplikaci **fabric: / App1**, můžeme vytvořit službu **fabric: / App1/ServiceA** typu "MyServiceType". Tato služba má dva oddíly (například **P1** a **P2**) a tři repliky na oddíl. Následující diagram znázorňuje zobrazení této aplikace, protože skončilo nasazené na uzlu.


![Diagram uzlu zobrazení nasazené aplikace][node-view-one]


Service Fabric aktivuje "MyServicePackage", který spouští "MyCodePackage", který je hostitelem repliky z obou oddílů. Všechny uzly v clusteru mít stejný zobrazení, protože jsme zvolili počet replik na oddíl musí rovnat počtu uzlů v clusteru. Pojďme vytvořit jiné služby, **fabric: / App1/ServiceB**, v aplikaci **fabric: / App1**. Tato služba obsahuje jeden oddíl (například **P3**) a tři repliky na oddíl. Následující diagram znázorňuje nové zobrazení na uzlu:


![Diagram uzlu zobrazení nasazené aplikace][node-view-two]


Service Fabric umístit novou repliku pro oddíl **P3** služby **fabric: / App1/ServiceB** v existující aktivace "MyServicePackage". Nyní. Pojďme vytvořit jinou aplikaci **fabric: / počítači App2** typu "MyAppType". Uvnitř **fabric: / počítači App2**, vytvoření služby **fabric: / počítači App2/ServiceA**. Tato služba má dva oddíly (**P4** a **P5**) a tři repliky na oddíl. Následující diagram znázorňuje nové zobrazení uzlu:


![Diagram uzlu zobrazení nasazené aplikace][node-view-three]


Service Fabric aktivuje novou kopii "MyServicePackage", který spouští novou kopii "MyCodePackage". Repliky z obou oddílů služby **fabric: / počítači App2/ServiceA** (**P4** a **P5**) jsou umístěné v této nové kopie "MyCodePackage".

## <a name="shared-process-model"></a>Sdílené model procesu
Předchozí část popisuje výchozí model poskytuje Service Fabric se označuje jako model procesu sdíleného hostingu. V tomto modelu pro danou aplikaci, pouze jedna kopie daného *ServicePackage* se aktivuje v uzlu (které spustí všechny *CodePackages* jsou v něm obsažené). Všechny repliky všech služeb danou *ServiceType* jsou umístěny v *CodePackage* , který zaregistruje *ServiceType*. Jinými slovy, všechny repliky všech služeb v uzlu danou *ServiceType* sdílet stejný postup.

## <a name="exclusive-process-model"></a>Výhradní model procesu
Další model hostingu poskytuje Service Fabric je model exkluzivní procesu. V tomto modelu, v daném uzlu každá replika nachází ve vlastním vyhrazeném procesu. Service Fabric aktivuje novou kopii *ServicePackage* (které spustí všechny *CodePackages* jsou v něm obsažené). Repliky jsou umístěny v *CodePackage* , které registrované *ServiceType* služby, do které replika patří. 

Pokud používáte Service Fabric verze 5.6 nebo novější, můžete použít model exkluzivní procesu v době vytvoření služby (s použitím [PowerShell][p1], [REST] [ r1], nebo [FabricClient][c1]). Zadejte **ServicePackageActivationMode** jako "ExclusiveProcess".

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

Pokud máte službu výchozí v manifestu aplikace, můžete použít model exkluzivní procesu tak, že zadáte **ServicePackageActivationMode** atribut:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Nyní Pojďme vytvořit jiné služby, **fabric: / App1/ServiceC**, v aplikaci **fabric: / App1**. Tato služba má dva oddíly (například **P6** a **N7**) a tři repliky na oddíl. Nastavíte **ServicePackageActivationMode** na "ExclusiveProcess". Následující diagram znázorňuje nové zobrazení na uzlu:


![Diagram uzlu zobrazení nasazené aplikace][node-view-four]


Jak je vidět, Service Fabric aktivuje dvě nové kopie "MyServicePackage" (jeden pro každou repliku z oddílu **P6** a **N7**). Service Fabric umístit každou repliku v jeho vyhrazená kopie *CodePackage*. Při použití modelu exkluzivní procesu pro danou aplikaci více kopií daný *ServicePackage* může být aktivní na uzlu. V předchozím příkladu jsou aktivní pro tři kopie "MyServicePackage" **fabric: / App1**. Každá z těchto aktivní kopií 'MyServicePackage' obsahuje **ServicePackageActivationId** s ním spojená. Toto ID identifikuje tuto kopii v rámci aplikace **fabric: / App1**.

Při použití pouze model procesu sdílet pro aplikace, je pouze jeden aktivní kopií *ServicePackage* na uzlu. **ServicePackageActivationId** u této aktivace *ServicePackage* je prázdný řetězec. To platí, například s **fabric: / počítači App2**.

> [!NOTE]
>- Sdílet proces odpovídá model hostingu **ServicePackageActivationMode** rovná **SharedProcess**. Toto je výchozí model hostingu a **ServicePackageActivationMode** není nutné zadávat v okamžiku vytváření služby.
>
>- Výhradní proces model hostingu odpovídá **ServicePackageActivationMode** rovná **ExclusiveProcess**. Pokud chcete použít toto nastavení, je třeba zadat explicitně v době vytváření služby. 
>
>- Chcete-li zobrazit model hostingu služby, dotaz [popis služby][p2]a podívejte se na hodnotu **ServicePackageActivationMode**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Práce s balíčkem nasazené službě
Aktivní kopií *ServicePackage* v uzlu se označuje jako [nasazený balíček služby][p3]. Při použití modelu exkluzivní proces pro vytvoření služby pro danou aplikaci, může být víc balíčků nasazené služby pro stejný *ServicePackage*. Pokud provádíte operace, které jsou specifické pro balíček nasazené služby, by měla poskytnout **ServicePackageActivationId** k identifikaci konkrétní nasazenou službu balíčku. Pokud jste třeba zadat ID [hlášení stavu balíčku nasazené služby] [ p4] nebo [restartování balíček kódu balíčku nasazené služby] [p5].

Můžete zjistit **ServicePackageActivationId** balíčku nasazené služby pomocí dotazu na seznam [nasazené balíčky služeb] [ p3] na uzlu. Při dotazování na [nasazené typy služeb][p6], [nasazení repliky][p7], a [nasazené balíčky kódu ] [ p8] na uzlu, výsledek dotazu obsahuje také **ServicePackageActivationId** balíčku nadřazená nasazená služba.

> [!NOTE]
>- V rámci modelu hostingu sdílený proces na daný uzel pro danou aplikaci, pouze jedna kopie *ServicePackage* aktivován. Má **ServicePackageActivationId** rovna *prázdný řetězec*a není nutné zadávat při provádění operace související se balíček nasazené služby. 
>
> - V části exkluzivní proces hostování model na daný uzel pro danou aplikaci, jeden nebo více kopií *ServicePackage* může být aktivní. Má každá aktivace *neprázdný* **ServicePackageActivationId**zadané při provádění operace související se balíček nasazené služby. 
>
> - Pokud **ServicePackageActivationId** je tento parametr vynechán, použije se výchozí *prázdný řetězec*. Pokud balíček nasazenou službu, která se aktivovala v rámci modelu sdílený proces je k dispozici, operace se provede na něj. Jinak se operace nezdaří.
>
> - Dotaz není jednou a mezipaměti **ServicePackageActivationId**. ID se dynamicky vygeneruje a můžete změnit z různých důvodů. Před provedením operace, která potřebuje **ServicePackageActivationId**, byste nejprve zadá dotaz na seznam [nasazené balíčky služeb] [ p3] na uzlu. Potom použijte **ServicePackageActivationId** z výsledku dotazu pro původní operaci.
>
>

## <a name="guest-executable-and-container-applications"></a>Aplikace hosta spustitelný soubor a kontejnerů
Service Fabric bude [spustitelný soubor typu Host] [ a2] a [kontejneru] [ a3] jako bezstavové služby, které jsou samostatné aplikace. Není žádný modul runtime Service Fabric v *ServiceHost* (procesu nebo kontejneru). Protože tyto služby jsou samostatný, počet replik na *ServiceHost* se nedá použít u těchto služeb. Nejběžnější konfigurace používají tyto služby je jedním oddílem, s [InstanceCount] [ c2] roven -1 (jedna kopie služby kód spuštěný na každém uzlu clusteru). 

Výchozí hodnota **ServicePackageActivationMode** pro tyto služby je **SharedProcess**, v takovém případě Service Fabric aktivuje pouze jednu kopii *ServicePackage* na uzlu pro danou aplikaci.  To znamená, že pouze jedna kopie kódu služby se spustí uzlu. Pokud chcete, aby několik kopií kódu služby provozovat na uzlu, zadejte **ServicePackageActivationMode** jako **ExclusiveProcess** v době vytváření služby. Například to můžete provést při vytváření více služeb (*Service1* k *ServiceN*) z *ServiceType* (zadané v poli *ServiceManifest*), nebo když je vaše služba s více oddílů. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Změňte model hostingu existující služby
V současné době nelze změnit model hostingu existující služby z sdílený proces na exkluzivní procesu (nebo naopak).

## <a name="choose-between-the-hosting-models"></a>Výběr mezi modelech hostování
By měla vyhodnotit, které model hostingu nejlepší vyhovuje vašim požadavkům. Prostředky operačního systému používá model sdílený proces lépe, vzhledem k tomu, že se vytvoří podřízený proces menší počet procesů a víc replik v rámci stejného procesu můžete sdílet porty. Nicméně pokud jeden z repliky došlo k chybě, kde jsou potřeba převést dolů hostitele služby, ovlivňuje všechny repliky ve stejném procesu.

 Model procesu exkluzivní poskytuje lepší izolace, se všemi replikami ve svém vlastním procesu. Pokud jeden z repliky došlo k chybě, neovlivní jiné repliky. Tento model je užitečný pro případy, kdy sdílení portů nepodporuje komunikační protokol. Usnadňuje to schopnosti uplatňovat zásady správného řízení prostředků na úrovni repliky. Výhradní proces však využívá více prostředků operačního systému, jak vytvoří jeden proces pro každou repliku na uzlu.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Důležité informace o modelu exkluzivní modelu procesu a aplikace
Pro většinu aplikací, můžete modelovat vaši aplikaci v Service Fabric udržováním jeden *ServiceType* za *ServicePackage*. 

V některých případech, Service Fabric také umožňuje více než jeden *ServiceType* za *ServicePackage* (a jeden *CodePackage* lze registrovat více než jeden  *ServiceType*). Tady jsou některé scénáře, kde může být užitečné tyto konfigurace:

- Chcete optimalizovat využití prostředků tak, že vytváření podřízeného procesu méně procesy a s vyšší hustotou replika jeden proces.
- Repliky z různých *ServiceType* potřebujete sdílet některým společným datům, která má vysokou inicializace nebo paměti nákladů.
- Máte nabídky bezplatné služby a chcete umístit omezení na využití prostředků vložením všech replik služby ve stejném procesu.

Výhradní proces model hostingu není souvislé s modelem aplikace s více *ServiceType* za *ServicePackage*. Důvodem je, že více *ServiceType* za *ServicePackage* jsou navržené tak, abyste dosáhli vyšší prostředků sdílení mezi repliky a umožňuje vyšší hustota replika jeden proces. Model exkluzivní procesu slouží k dosažení různé výsledky.

Podívejte se na několika *ServiceType* za *ServicePackage*, s jiným *CodePackage* registrace jednotlivých *ServiceType*. Řekněme, že máme *ServicePackage* "MultiTypeServicePackage", který má dva *CodePackages*:

- "MyCodePackageA", které je zaregistruje *ServiceType* "MyServiceTypeA".
- "MyCodePackageB", které je zaregistruje *ServiceType* "MyServiceTypeB".

Nyní Řekněme, že se nám vytvořit aplikaci, která **fabric: / SpecialApp**. Uvnitř **fabric: / SpecialApp**, můžeme vytvořit následující dvě služby s modelem exkluzivní procesu:

- Služba **fabric: / SpecialApp/ServiceA** typu "MyServiceTypeA", se dva oddíly (například **P1** a **P2**) a tři repliky na oddíl.
- Služba **fabric: / SpecialApp/ServiceB** typu "MyServiceTypeB", se dva oddíly (**P3** a **P4**) a tři repliky na oddíl.

V daném uzlu obě služby mají dvě repliky. Protože model exkluzivní procesu jsme použili k vytvoření služby, Service Fabric aktivuje novou kopii "MyServicePackage" pro každou repliku. Každá aktivace "MultiTypeServicePackage" spustí kopii "MyCodePackageA" a "MyCodePackageB". Je však pouze jeden 'MyCodePackageA' nebo "MyCodePackageB" hostitelem repliky, pro kterou byl aktivován "MultiTypeServicePackage". Následující diagram znázorňuje zobrazení uzlu:


![Diagram zobrazení uzlů nasazenou aplikaci.][node-view-five]


Při aktivaci "MultiTypeServicePackage" repliky oddílu **P1** služby **fabric: / SpecialApp/ServiceA**, "MyCodePackageA" je hostitelem repliky. 'MyCodePackageB' je spuštěná. Podobně při aktivaci "MultiTypeServicePackage" repliky oddílu **P3** služby **fabric: / SpecialApp/ServiceB**, "MyCodePackageB" je hostitelem repliky. 'MyCodePackageA' je spuštěná. Proto tím větší je počet *CodePackages* (registraci různých *ServiceType*) za *ServicePackage*, tím vyšší využití prostředků redundantní. 
 
 Ale když vytvoříme služby **fabric: / SpecialApp/ServiceA** a **fabric: / SpecialApp/ServiceB** s modelem sdílený proces Service Fabric aktivuje jenom jednu kopii " MultiTypeServicePackage "pro aplikaci **fabric: / SpecialApp**. Všechny repliky pro službu hostuje "MyCodePackageA" **fabric: / SpecialApp/ServiceA**. Všechny repliky pro službu hostuje "MyCodePackageB" **fabric: / SpecialApp/ServiceB**. Následující diagram znázorňuje zobrazení uzlů v tomto nastavení: 


![Diagram zobrazení uzlů nasazenou aplikaci.][node-view-six]


V předchozím příkladu si možná myslíte, pokud "MyCodePackageA" registruje "MyServiceTypeA" a "MyServiceTypeB" a neexistuje žádná MyCodePackageB, neexistuje žádné redundantní *CodePackage* systémem. I když je to v pořádku, tento aplikační model není sladěný s výhradním procesu model hostingu. Pokud chcete změnit každé repliky na svůj vlastní vyhrazený proces je cílem, není potřeba zaregistrovat oba *ServiceType* ze stejné *CodePackage*. Místo toho můžete jednoduše umisťovat všechny *ServiceType* ve vlastním *ServicePackage*.

## <a name="next-steps"></a>Další postup
[Balení aplikace] [ a4] a příprava na nasazení.

[Nasazení a odebírat aplikace][a5]. Tento článek popisuje použití Powershellu ke správě instance aplikace.

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

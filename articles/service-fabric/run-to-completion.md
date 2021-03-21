---
title: RunToCompletion sémantika v Service Fabric
description: Popisuje sémantiku RunToCompletion v Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: 6f2f6aa4380fcf6909957118bf682275350ce68c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000262"
---
# <a name="runtocompletion"></a>Spuštění až do konce

Počínaje verzí 7,1 podporuje Service Fabric sémantiku **RunToCompletion** pro [kontejnery][containers-introduction-link] a spustitelné aplikace [hosta][guest-executables-introduction-link] . Tyto sémantiky umožňují aplikace a služby, které dokončí úlohu a ukončí, na rozdíl od, vždy běžící aplikace a služby.

Než budete pokračovat v tomto článku, doporučujeme seznámit se s [Service Fabric aplikačním modelem][application-model-link] a [modelem Service Fabric hostování][hosting-model-link].

> [!NOTE]
> Sémantika RunToCompletion se v tuto chvíli nepodporuje u služeb napsaných pomocí programovacího modelu [Reliable Services][reliable-services-link] .
 
## <a name="runtocompletion-semantics-and-specification"></a>Sémantika a specifikace RunToCompletion
Sémantika RunToCompletion může být zadána jako **ExecutionPolicy** při [importu ServiceManifest][application-and-service-manifests-link]. Zadané zásady dědí všechny CodePackages, které tvoří ServiceManifest. Následující fragment kódu ApplicationManifest.xml poskytuje příklad.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy** umožňuje následující dva atributy:
* **Typ:** **RunToCompletion** je aktuálně jedinou povolenou hodnotou pro tento atribut.
* **Restartování:** Tento atribut určuje zásady restartování, které se vztahují na CodePackages obsahující ServicePack, při selhání. CodePackage ukončení s **nenulovým ukončovacím kódem** se považuje za neúspěšné. Povolené hodnoty pro tento atribut jsou **Chyba při selhání** a **nikdy** se **nezdařila** výchozí hodnota.

Pokud se v případě selhání nějaké CodePackage **(nenulový ukončovací kód)** spustí zásada restartování nastavená na **chybu**, bude restartována s zpětným voláním mezi opakovanými selháními. Když se zásada restartování nastaví na **nikdy**, pokud některé CodePackage selžou, stav nasazení DeployedServicePackage je označený jako **neúspěšný** , ale ostatní CodePackages můžou pokračovat v provádění. Pokud všechny CodePackages, které tvoří rutinu ServicePack k úspěšnému dokončení **(ukončovací kód 0)**, je stav nasazení DeployedServicePackage označený jako **RanToCompletion**. 

## <a name="complete-example-using-runtocompletion-semantics"></a>Kompletní příklad použití sémantiky RunToCompletion

Pojďme se podívat na kompletní příklad s využitím sémantiky RunToCompletion.

> [!IMPORTANT]
> Následující příklad předpokládá znalost vytváření [kontejnerových aplikací pro Windows pomocí Service Fabric a Docker][containers-getting-started-link].
>
> Tento příklad odkazuje na mcr.microsoft.com/windows/nanoserver:1809. Kontejnery Windows serveru nejsou kompatibilní napříč všemi verzemi hostitelského operačního systému. Další informace najdete v tématu [Kompatibilita verzí kontejnerů Windows](/virtualization/windowscontainers/deploy-containers/version-compatibility).

Následující ServiceManifest.xml popisuje ServicePack, skládající se ze dvou CodePackages, které reprezentují kontejnery. *RunToCompletionCodePackage1* pouze zaznamená zprávu do **stdout** a ukončí. *RunToCompletionCodePackage2* otestuje adresu zpětné smyčky za chvíli a pak ukončí s ukončovacím kódem buď **0**, **1** nebo **2**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

Následující ApplicationManifest.xml popisuje aplikaci na základě ServiceManifest.xml popsaných výše. Určuje **RunToCompletion** **ExecutionPolicy** pro *WindowsRunToCompletionServicePackage* se zásadami restartování při **selhání**. Po aktivaci *WindowsRunToCompletionServicePackage* se spustí jeho prvek CodePackages. *RunToCompletionCodePackage1* by se měl úspěšně ukončit při první aktivaci. *RunToCompletionCodePackage2* ale může selhat **(nenulový ukončovací kód)**. v takovém případě se restartuje, protože zásada restartování je **chybná**.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>Dotazování na stav nasazení DeployedServicePackage
Stav nasazení DeployedServicePackage se dá dotazovat z PowerShellu pomocí rutiny [Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] nebo z C# pomocí rozhraní [FabricClient][fabric-client-link] API [GetDeployedServicePackageListAsync (řetězec, URI, řetězec)][deployed-service-package-fabricclient-link] .

## <a name="considerations-when-using-runtocompletion-semantics"></a>Předpoklady při použití sémantiky RunToCompletion

Pro aktuální podporu RunToCompletion je třeba si uvědomit následující body.
* Tyto sémantiky se podporují jenom pro [kontejnery][containers-introduction-link] a [spustitelné aplikace hostované v hostovi][guest-executables-introduction-link] .
* Scénáře upgradu pro aplikace se sémantikou RunToCompletion nejsou povoleny. Uživatelé by v případě potřeby měli tyto aplikace odstranit a znovu vytvořit.
* Události převzetí služeb při selhání můžou způsobit, že se CodePackages znovu spustí po úspěšném dokončení, ve stejném uzlu nebo v jiných uzlech clusteru. Příklady událostí převzetí služeb při selhání jsou, restarty uzlu a Service Fabric upgrady modulu runtime v uzlu.

## <a name="next-steps"></a>Další kroky

Související informace najdete v následujících článcích.

* [Service Fabric a kontejnery.][containers-introduction-link]
* [Service Fabric a spustitelné soubory hosta.][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[fabric-client-link]: /dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: /dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync

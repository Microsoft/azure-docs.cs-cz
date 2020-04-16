---
title: Sémantiku RunToCompletion v service fabric
description: Popisuje sémantiku RunToCompletion v service fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: adf4b11412aa752144d4ed4fef06d2de1d76598d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431290"
---
# <a name="runtocompletion"></a>RunToCompletion

Počínaje verzí 7.1 service fabric podporuje sémantiku **RunToCompletion** pro [kontejnery][containers-introduction-link] a [spustitelné][guest-executables-introduction-link] aplikace hosta. Tato sémantika umožňuje aplikacím a službám, které dokončí úlohu a ukončí na rozdíl od vždy spuštěných aplikací a služeb.

Než budete pokračovat v tomto článku, doporučujeme seznámit se s [modelem aplikace Service Fabric][application-model-link] a [modelhostování service fabric][hosting-model-link].

> [!NOTE]
> Sémantiku RunToCompletion nejsou aktuálně podporovány pro služby napsané pomocí programovacího modelu [Spolehlivé služby.][reliable-services-link]
 
## <a name="runtocompletion-semantics-and-specification"></a>Sémantiku a specifikaci RunToCompletion
Sémantiku RunToCompletion lze zadat jako zásadu **spuštění** [při importu manifestu ServiceManifest][application-and-service-manifests-link]. Zadaná zásada je zděděna všemi CodePackages obsahující ServiceManifest. Následující fragment applicationManifest.xml poskytuje příklad.

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
* **Restartovat:** Tento atribut určuje zásadu restartování, která je použita pro CodePackages obsahující ServicePackage, při selhání. CodePackage ukončení s **nenulový ukončovací kód** se považuje za neúspěšné. Povolené hodnoty pro tento atribut jsou **OnFailure** a **Nikdy** s **OnFailure** je výchozí.

S zásady restartování nastavena **na OnFailure**, pokud některý CodePackage selže **(nenulový ukončovací kód)**, je restartován, s back-off mezi opakovanými selháními. S zásady restartování nastavena na **Nikdy**, pokud některý CodePackage selže, stav nasazení DeployedServicePackage je označen jako **neúspěšný,** ale ostatní CodePackages mohou pokračovat v provádění. Pokud všechny CodePackages obsahující ServicePackage spustit úspěšné dokončení **(ukončovací kód 0)**, stav nasazení DeployedServicePackage je označen jako **RanToCompletion**. 

## <a name="complete-example-using-runtocompletion-semantics"></a>Kompletní příklad pomocí sémantiky RunToCompletion

Podívejme se na úplný příklad pomocí Sémantiku RunToCompletion.

> [!IMPORTANT]
> Následující příklad předpokládá znalost vytváření [aplikací kontejnerů systému Windows pomocí Service Fabric a Docker][containers-getting-started-link].
>
> Tento příklad odkazuje mcr.microsoft.com/windows/nanoserver:1809. Kontejnery systému Windows Server nejsou kompatibilní ve všech verzích hostitelského operačního systému. Další informace naleznete v [tématu Kompatibilita verzí kontejneru systému Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

Následující ServiceManifest.xml popisuje ServicePackage skládající se ze dvou CodePackages, které představují kontejnery. *RunToCompletionCodePackage1* pouze protokoly zprávu **stdout** a ukončí. *RunToCompletionCodePackage2* příkazy ping adresu zpětné smyčky na chvíli a potom ukončí s ukončovací kód buď **0**, **1** nebo **2**.

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

Následující soubor ApplicationManifest.xml popisuje aplikaci založenou na výše popsaném souboru ServiceManifest.xml. Určuje zásadu **spuštění** **runtocompletion** pro *program WindowsRunToCompletionServicePackage* se zásadou restartování **programu OnFailure**. Po aktivaci *WindowsRunToCompletionServicePackage*, jeho základní CodePackages budou spuštěny. *RunToCompletionCodePackage1* by měl úspěšně ukončit při první aktivaci. *RunToCompletionCodePackage2* však může selhat **(nenulový ukončovací kód)**, v takovém případě bude restartován, protože zásada restartování je **OnFailure**.

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
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>Dotazování na stav nasazení balíčku DeployedServicePackage
Stav nasazení balíčku DeployedServicePackage může být dotazován z prostředí PowerShell pomocí [get-ServiceFabricDeployedServicePackage][deployed-service-package-link] nebo z jazyka C# pomocí rozhraní [FABRICClient][fabric-client-link] API [GetDeployedServicePackageListAsync(Řetězec, Uri, Řetězec)][deployed-service-package-fabricclient-link]

## <a name="considerations-when-using-runtocompletion-semantics"></a>Důležité informace při použití sémantiky RunToCompletion

Následující body by měly být uvedeny pro aktuální podporu RunToCompletion.
* Tato sémantika je podporována pouze pro [kontejnery][containers-introduction-link] a spustitelné aplikace [hosta.][guest-executables-introduction-link]
* Scénáře upgradu pro aplikace s sémantikou RunToCompletion nejsou povoleny. Uživatelé by měli v případě potřeby tyto aplikace odstranit a znovu vytvořit.
* Události převzetí služeb při selhání může způsobit CodePackages znovu spustit po úspěšném dokončení, na stejném uzlu nebo jiných uzlů clusteru. Příklady událostí převzetí služeb při selhání jsou restartování uzlu a inovace za běhu service fabric na uzlu.

## <a name="next-steps"></a>Další kroky

Související informace naleznete v následujících článcích.

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
[deployed-code-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricdeployedservicePackage
[fabric-client-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync


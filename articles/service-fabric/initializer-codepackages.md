---
title: Inicializátor codepackages v service fabric
description: Popisuje Initializer CodePackages v service fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: shsha
ms.openlocfilehash: 8483e00f55d0dd49ba57db58b99b237ce0a169e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430627"
---
# <a name="initializer-codepackages"></a>Balíčky kódu inicializátoru

Počínaje verzí 7.1 service fabric podporuje **Initializer CodePackages** pro [kontejnery][containers-introduction-link] a [host spustitelné][guest-executables-introduction-link] aplikace. Initializer CodePackages poskytují možnost provést inicializace v oboru ServicePackage před spuštěním spuštění jiných CodePackages. Jejich vztah k ServicePackage je obdobou what [SetupEntryPoint][setup-entry-point-link] je pro CodePackage.

Než budete pokračovat v tomto článku, doporučujeme seznámit se s [modelem aplikace Service Fabric][application-model-link] a [modelhostování service fabric][hosting-model-link].

> [!NOTE]
> Initializer CodePackages nejsou aktuálně podporovány pro služby napsané pomocí programovacího modelu [Spolehlivé služby.][reliable-services-link]
 
## <a name="semantics"></a>Sémantika

Očekává se, že balíček kódu inicializátoru bude spuštěn k **úspěšnému dokončení (ukončovací kód 0).** Neúspěšný balíček kódu inicializátoru je restartován, dokud nebude úspěšně dokončen. Více Initializer CodePackages jsou povoleny a jsou spouštěny k **úspěšnému dokončení**, **postupně** **, v zadaném pořadí** před jiné CodePackages v ServicePackage zahájení spuštění.

## <a name="specifying-initializer-codepackages"></a>Určení balíčků kódu inicializátoru
CodePackage můžete označit jako Initializer nastavením **Initializer** atribut **true** v ServiceManifest. Pokud existuje více Initializer CodePackages, jejich pořadí provádění lze zadat prostřednictvím **ExecOrder** atribut. **ExecOrder** musí být nezáporné celé číslo a je platný pouze pro Initializer CodePackages. Initializer CodePackages s nižšími hodnotami **ExecOrder** jsou provedeny jako první. Pokud **ExecOrder** není zadán pro Initializer CodePackage, předpokládá se výchozí hodnota 0. Relativní pořadí provádění Initializer CodePackages se stejnou hodnotou **ExecOrder** není zadán.

Následující serviceManifest úryvek popisuje tři CodePackages dva z nich jsou označeny jako Initializers. Při aktivaci tohoto ServicePackage *InitCodePackage0* je proveden jako první, protože má nejnižší hodnotu **ExecOrder**. Po úspěšném dokončení (ukončovací kód 0) *InitCodePackage0*, *InitCodePackage1* je spuštěn. Nakonec po úspěšném dokončení *InitCodePackage1*, *WorkloadCodePackage* je spuštěn.

```xml
<CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
  ...
</CodePackage>

<CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
  ...
</CodePackage>

<CodePackage Name="WorkloadCodePackage" Version="1.0">
  ...
</CodePackage>
```
## <a name="complete-example-using-initializer-codepackages"></a>Kompletní příklad pomocí Initializer CodePackages

Podívejme se na úplný příklad pomocí Initializer CodePackages.

> [!IMPORTANT]
> Následující příklad předpokládá znalost vytváření [aplikací kontejnerů systému Windows pomocí Service Fabric a Docker][containers-getting-started-link].
>
> Tento příklad odkazuje mcr.microsoft.com/windows/nanoserver:1809. Kontejnery systému Windows Server nejsou kompatibilní ve všech verzích hostitelského operačního systému. Další informace naleznete v [tématu Kompatibilita verzí kontejneru systému Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

Následující soubor ServiceManifest.xml vychází z výše popsaného úrychu ServiceManifest. *InitCodePackage0*, *InitCodePackage1* a *WorkloadCodePackage* jsou CodePackages, které představují kontejnery. Po aktivaci *InitCodePackage0* je proveden jako první. Zaznamená zprávu do souboru a ukončí. Dále *InitCodePackage1* je spuštěn, který také zaznamená zprávu do souboru a ukončí. Nakonec *WorkloadCodePackage* spustí spuštění. Také zaznamená zprávu do souboru, výstupy obsahu souboru **stdout** a pak ping navždy.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsInitCodePackageServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows Init CodePackage Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsInitCodePackageServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="InitCodePackage0" Version="1.0" Initializer="true" ExecOrder="0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage0. &gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="InitCodePackage1" Version="1.0" Initializer="true" ExecOrder="1">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from InitCodePackage1. &gt;&gt; C:\WorkspaceOnContainer\log.txt</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="WorkloadCodePackage" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from WorkloadCodePackage. &gt;&gt; C:\WorkspaceOnContainer\log.txt &amp;&amp; type C:\WorkspaceOnContainer\log.txt &amp;&amp; ping -t 127.0.0.1 &gt; nul</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

Následující soubor ApplicationManifest.xml popisuje aplikaci založenou na výše popsaném souboru ServiceManifest.xml. Všimněte si, že určuje stejné **připojení svazku** pro všechny kontejnery, **tj.** **C:\WorkspaceOnContainer** Čistý efekt je, že všechny kontejnery zapisovat do stejného souboru protokolu v pořadí, ve kterém jsou aktivovány.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsInitCodePackageApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows Init CodePackage Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsInitCodePackageServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ContainerHostPolicies CodePackageRef="InitCodePackage0" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

     <ContainerHostPolicies CodePackageRef="InitCodePackage1" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>

      <ContainerHostPolicies CodePackageRef="WorkloadCodePackage" ContainersRetentionCount="2" RunInteractive="true">
        <Volume Source="C:\WorkspaceOnHost" Destination="C:\WorkspaceOnContainer" IsReadOnly="false" />
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsInitCodePackageService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsInitCodePackageServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
Po úspěšné aktivaci servicepackage by měl být následující obsah **C:\WorkspaceOnHost\log.txt.**

```console
C:\Users\test>type C:\WorkspaceOnHost\log.txt
Hi from InitCodePackage0.
Hi from InitCodePackage1.
Hi from WorkloadCodePackage.
```

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
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md

